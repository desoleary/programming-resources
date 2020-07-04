Use brakeman

#### Command Injection

> Do not use in web app or ensure 100% its not injectable if completely necessary
```ruby
eval("ruby code here")
System("os command here")
`ls -al /`   (backticks contain os command)
Kernel.exec("os command here")
open("| os command here")
```

#### SQL Injection

```ruby
username = "admin' OR 1); -- "
password = ""
user = User.find_by("username = '#{username}' AND password = '#{password}'")
```

```sql
SELECT  "users".* FROM "users" WHERE (username = 'admin' OR 1); -- ' AND password = '') LIMIT 1 
```

**Solution**
```ruby
user = User.find_by('username = ? AND password = ?', params[:username], params[:password])

user.find_by(username: params[:username], password: params[:password]).first
```

> notice `?` it escapes to ensure it cannot be injectable
```sql
SELECT  "users".* FROM "users" WHERE (username = 'admin'' OR 1); -- ' AND password = '') LIMIT 1
```

#### Cross-site Scripting (XSS)

> Use caution when using these

```erb
<!--This method outputs without escaping a string-->
<%= raw "<script>alert('raw does not escape input!!')</script>" %>

<!--equivalent to raw method-->
<%= "<script>alert('html_safe does not escape input!!')</script>".html_safe %>

<!--content_tag does not escape-->
<%= content_tag "<script>alert('content_tag does not escape input!!')</script>" %>

```
**Ensure that links do not take user inputted data**
```erb
<%= link_to 'Personal Website', "javascript:alert('links not so safe either :(')" %>
```
**link returns**
```html
<a href="javascript:alert('links not so safe either :(')">Personal Website</a>
```

**Sessions**

```ruby
Project::Application.config.session_store :active_record_store
```

**Authentication**
- use Devise or AuthLogic

```ruby
class ProjectController < ApplicationController
       before_filter :authenticate_user
```

```ruby
config.password_length = 8..128
```

**Password Complexity**
```ruby
validate :password_complexity
   def password_complexity
      if password.present? and not password.match(/\A(?=.*[a-z])(?=.*[A-Z])(?=.*\d).+\z/)
          errors.add :password, "must include at least one lowercase letter, one uppercase letter, and one digit"
      end
   end
```

**Insecure Direct Object Reference or Forceful Browsing**
> introduce Authorization (admin-only)

**CSRF (Cross Site Request Forgery)**
```ruby
class ApplicationController < ActionController::Base
       protect_from_forgery
```

**Mass Assignment and Strong Parameters**
```ruby
 class Project < ActiveRecord::Base
       attr_accessible :name, :admin
   end
```

[Strong Params](https://github.com/rails/strong_parameters)
```ruby
class PeopleController < ActionController::Base
  # This will raise an ActiveModel::ForbiddenAttributes exception because it's using mass assignment
  # without an explicit permit step.
  def create
    Person.create(params[:person])
  end

  # This will pass with flying colors as long as there's a person key in the parameters, otherwise
  # it'll raise an ActionController::ParameterMissing exception, which will get caught by
  # ActionController::Base and turned into that 400 Bad Request reply.
  def update
    person = current_account.people.find(params[:id])
    person.update_attributes!(person_params)
    redirect_to person
  end

  private
    # Using a private method to encapsulate the permissible parameters is just a good pattern
    # since you'll be able to reuse the same permit list between create and update. Also, you
    # can specialize this method with per-user checking of permissible attributes.
    def person_params
      params.require(:person).permit(:name, :age)
    end
end
```

**Cross Origin Resource Sharing**

```ruby
gem 'rack-cors', :require => 'rack/cors'
```
**config/application.rb**

```ruby
module Sample
       class Application < Rails::Application
           config.middleware.use Rack::Cors do
               allow do
                   origins 'someserver.example.com'
                   resource %r{/users/\d+.json},
                       :headers => ['Origin', 'Accept', 'Content-Type'],
                       :methods => [:post, :get]
               end
           end
       end
   end
```

**Encryption**

```ruby
config.stretches = Rails.env.test? ? 1 : 10
```
#### Session Guidelines
 - Do not store large objects in a session.
 - Use object references only preferably GUIDs
 - Critical data should not be stored in session. to allow us to clear sessions without issue

**Force SSL**
 ```ruby
config.force_ssl = true
```

**Use database session management**
```ruby
Project::Application.config.session_store :active_record_store
```

**Encrypted Cookie Store**
- store keys in a password vault and not in code

```yaml
development:
  secret_key_base: a75d...
 
test:
  secret_key_base: 492f...
 
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

**Session Fixation - Countermeasures**

`reset_session` or Devise gem

**Session Expiry**
```ruby
class Session < ApplicationRecord
  def self.sweep(time = 1.hour)
    if time.is_a?(String)
      time = time.split.inject { |count, unit| count.to_i.send(unit) }
    end
 
    delete_all "updated_at < '#{time.ago.to_s(:db)}' OR
  created_at < '#{2.days.ago.to_s(:db)}'"
  end
end
```

**Cross-Site Request Forgery (CSRF)**

```ruby
protect_from_forgery with: :exception
```

Use `GET` if:
> The interaction is more like a question (i.e., it is a safe operation such as a query, read operation, or lookup).


otherwise use `POST`

**Redirection**
> just do not redirect user inputted/changeable links

```ruby
def legacy
  redirect_to(params.update(action:'main'))
end
```

**File Uploads**
- sanitize filename
- ensure OS is using appropriate permissions
- async uploads to prevent DOS

```ruby
def sanitize_filename(filename)
  filename.strip.tap do |name|
    # NOTE: File.basename doesn't work right with Windows paths on Unix
    # get only the filename, not the whole path
    name.sub! /\A.*(\\|\/)/, ''
    # Finally, replace all non alphanumeric, underscore
    # or periods with underscore
    name.gsub! /[^\w\.\-]/, '_'
  end
end
```

**File Downloads**

```ruby
send_file('/var/www/uploads/' + params[:filename])
```
> ensures requested file is in the expect directory

```ruby
basename = File.expand_path(File.join(File.dirname(__FILE__), '../../files'))
filename = File.expand_path(File.join(basename, @file.public_filename))
raise if basename !=
     File.expand_path(File.join(File.dirname(filename), '../../../'))
send_file filename, disposition: 'inline'
```

**Brute-Forcing Accounts**

- display a generic error message on forgot-password pages
- CAPTCHA after a number of failed logins from a certain IP address

- SALT passwords

**Logging**
- remove any sensitive data
- audit

`config.filter_parameters << :password`
