# Rails Performance

#### Optimization Strategies

1. Optimize Memory
2. Optimize Loops
3. Write Less Ruby (SQL queries or C)
4. Control Memory (externally and internally)
    - Most important piece is to gain perspective by fetching as much data as possible
    - Figure out where the biggest performance hits affect the most customers
    - **NewRelic** is an amazing app monitoring software that gives you so many insights (apex score, slowest db calls, customer satisfaction rating etc.) Helped me make the Reconciliation app 2-3x faster
    - Analyse database design and ensure it still works (something that worked well a year ago might not be effective anymore)
5. partition table separated by day/month/year
6. move table to another db
7. move to NoSQL (use lose aggregation in favour of fast writes)
8. Do we need all this data?
9. Throttle data somehow?
10. Expire old data
11. Archive and summarise archived data into summary table with the same but aggregate of archived rows (should include an identifier to figure out what makes up the data)
12. what will give you the biggest bang for our buck with minimum risk (might get us 80% of the way)
    - Expire old data (> 2 weeks old)
    - cache “#{some_key}”, Time.current.strftime(‘%Y-%m-%d’)
    - Rack mini profiler (work against realistic data on dev if possible)
    - Bullet (bye bye N+1 bugs)
    - Analyze DB locks
13. Figure out where the **dead locks** are coming from
14. Move long running logic to Delayed Job at least until you fix any performance related issues

#### Caching
    
###### Action Caching

- Action Caching works like Page Caching except the incoming web request hits the Rails stack so that before filters can be run on it before the cache is served. 
- This allows authentication and other restrictions to be run while still serving the result of the output from a cached copy.

###### Fragment Caching

```erbruby
<% @products.each do |product| %>
  <% **cache** product do %>
    <%= render product %>
  <% end %>
<% end %>
```

###### Optional caching

```erbruby
<% cache_if admin?, product do %>
  <%= render product %>
<% end %>
```

###### Collection Caching

````erbruby
<%= render partial: 'products/product', collection: @products, cached: true %>
````

###### key-based expiration

- 1-201505056193031061005000 => <product_id>-<record updated_at timestamp>
- bea67108094918eeba42cd4a6e786901 view fragment cache key

###### Russian Doll Caching
> With touch set to true, any action which changes updated_at for a game record will also change it for the associated product, thereby expiring the cache.

```ruby
class Product < ApplicationRecord
  has_many :games
end

class Game < ApplicationRecord
  belongs_to :product, touch: true
end
```

```erbruby
cache product do%>
 <%= render product.games %>
<%end%>

<% cache game do%>
 <%= render game %>
<%end%>
```

###### Low-Level Caching

```ruby
class Product < ApplicationRecord
  def competing_price
    Rails.cache.fetch("#{cache_key}/competing_price", expires_in: 12.hours) do
      Competitor::API.find_price(id)
    end
  end
end
```

###### SQL Caching
- works by default per request
- if same query is called twice it will used the cached query

###### Cache Stores

```erbruby
config.cache_store = :memory_store, { size: 64.megabytes }
```

###### Conditional GET support
> Rails will use the updated_at and cache_key methods for setting last_modified and etag:

```erbruby
class ProductsController < ApplicationController
  def show
    @product = Product.find(params[:id])

    if stale?(@product)
      respond_to do |wants|
        # ... normal response processing
      end
    end
  end
end
```

###### Eager Loading (N+1 fix)

```erbruby
@user = User.find(id).includes(:posts => :comments)
```

###### Serializers

```ruby
lass Smth < ActiveRecord::Base
  serialize :data, JSON
end
Smth.find(...).data
Smth.find(...).data = { ... }
# But convenience comes with 3x memory overhead. If you store 100M in data column, expect to allocate 300M just to read it from the database
class Smth < ActiveRecord::Base
  def data
    JSON.parse(read_attribute(:data))
  end
  def data=(value)
    write_attribute(:data, value.to_json)
  end
end
```

###### Bulk Update

```ruby
Book.where('title LIKE ?', '%Rails%').update_all(author: 'David')
```

###### Write Less Ruby
> Let the database do it job for sorting, ordering, unique etc.

> strong understanding of SQL and relational databases, capable of tracking down slowdowns to root causes in a complex system based on little initial information.

Questions:
- How do you measure performance metrics in production
  - new relic
  - datadog
  - log server (logstash & elastic search)
- How do you prioritize performance?
- Do you have a dedicated performance group?



###### Analyze SQL Performance

```postgresql
select count(*) from users; -- run slow query
EXPLAIN  SELECT * FROM users ;
create index test_index on logs (model_id)

EXPLAIN SHOW INDEX FROM grade;

ALTER TABLE grade DROP INDEX class;

SHOW INDEX FROM instructor;

ALTER TABLE instructor DROP INDEX name, DROP INDEX class;

SET profiling = 1;

SELECT *
FROM users;

SHOW PROFILES;
```

```sql
mysql> SET profiling = 1;
mysql> SELECT * FROM users WHERE name = 'Jesse';
mysql> SHOW PROFILES;
mysql> select count(*) from logs;
 +----------+
 | count(*) |
 +----------+
 | 24060924 |
 +----------+
 1 row in set (40.86 sec)
 
 mysql> show profiles;
 +----------+-------------+-----------------------------------+
 | Query_ID | Duration    | Query                             |
 +----------+-------------+-----------------------------------+
 |        1 | 40.85868250 | select count(*) from logs |
 +----------+-------------+-----------------------------------+
 1 row in set, 1 warning (0.00 sec)
 
 mysql> create index test_index on logs (model_id);
 Query OK, 0 rows affected (2 min 47.93 sec)
 Records: 0  Duplicates: 0  Warnings: 0
 
 mysql> show profiles;
 +----------+--------------+-------------------------------------------------------+
 | Query_ID | Duration     | Query                                                 |
 +----------+--------------+-------------------------------------------------------+
 |        1 |  40.85868250 | select count(*) from logs                     |
 |        2 | 167.94793775 | create index test_index on logs (model_id) |
 +----------+--------------+-------------------------------------------------------+
 2 rows in set, 1 warning (0.00 sec)
 
 mysql> create index test_index2 on logs (event_created_at);
 Query OK, 0 rows affected (1 min 7.28 sec)
 Records: 0  Duplicates: 0  Warnings: 0
 
 mysql> show profiles;
 +----------+--------------+-------------------------------------------------------------+
 | Query_ID | Duration     | Query                                                       |
 +----------+--------------+-------------------------------------------------------------+
 |        1 |  40.85868250 | select count(*) from logs                           |
 |        2 | 167.94793775 | create index test_index on logs (model_id)       |
 |        3 |  67.29140300 | create index test_index2 on logs (event_created_at) |
 +----------+--------------+-------------------------------------------------------------+
 3 rows in set, 1 warning (0.00 sec)
 
 mysql> show profile for query 2;
 +--------------------------------+------------+
 | Status                         | Duration   |
 +--------------------------------+------------+
 | starting                       |   0.000098 |
 | checking permissions           |   0.000009 |
 | init                           |   0.000002 |
 | Opening tables                 |   0.000058 |
 | setup                          |   0.000057 |
 | creating table                 |   0.018921 |
 | After create                   |   0.000126 |
 | System lock                    |   0.000011 |
 | preparing for alter table      |   0.023426 |
 | altering table                 | 167.862626 |
 | committing alter table to stor |   0.041056 |
 | end                            |   0.000047 |
 | query end                      |   0.000053 |
 | closing tables                 |   0.000007 |
 | freeing items                  |   0.001240 |
 | cleaning up                    |   0.000201 |
 +--------------------------------+------------+
 16 rows in set, 1 warning (0.00 sec)
 
 mysql> show profile for query 3;
 +--------------------------------+-----------+
 | Status                         | Duration  |
 +--------------------------------+-----------+
 | starting                       |  0.000057 |
 | checking permissions           |  0.000006 |
 | init                           |  0.000003 |
 | Opening tables                 |  0.000036 |
 | setup                          |  0.000038 |
 | creating table                 |  0.023687 |
 | After create                   |  0.000127 |
 | System lock                    |  0.000013 |
 | preparing for alter table      |  0.972869 |
 | altering table                 | 66.221796 |
 | committing alter table to stor |  0.072015 |
 | end                            |  0.000018 |
 | query end                      |  0.000044 |
 | closing tables                 |  0.000009 |
 | freeing items                  |  0.000150 |
 | cleaning up                    |  0.000537 |
 +--------------------------------+-----------+
 16 rows in set, 1 warning (0.00 sec)

mysql> set profiling=0;

Query OK, 0 rows affected, 1 warning (0.00 sec)




Looks like model's join needs work (293864 rows)

actual model rows under item: 165764
total model rows: 7641750
```
