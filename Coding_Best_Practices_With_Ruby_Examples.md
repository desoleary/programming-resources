# Ruby Best Practices

#### SOLID Design Principles

###### S - Single Responsibility

> The methods in a class should be cohesive around a single purpose
 

**Single Responsibility Pattern**

```ruby
class DealProcessor
  def initialize(deals)
    @deals = deals
  end

  def process
    @deals.each do |deal|
      mark_deal_processed
      CommissionCalculator.new.create_commission(deal)
    end
  end

  private

  def mark_deal_processed
    @deal.processed = true
    @deal.save!
  end
end
```

####### O - Open-Closed
> Objects should be open for extension, but closed for modification.

```ruby
class UsageFileParser
  def initialize(client, parser)
    @client = client
    @parser = parser
  end

  def parse(usage_file)
    parser.parse(usage_file)
    @client.last_parse = Time.now
    @client.save!
  end
end

class XmlParser
  def parse(usage_file)
    # parse xml
  end
end

class CsvParser
  def parse(usage_file)
    # parse csv
  end
end
```

###### L - Liskov Subsitution
> Subclasses should be substitable for their superclasses

###### I - Interface Segregation
> Objects should not be forced to depend on methods they don’t use

###### D - Dependency Inversion
> Depend on abstractions, not on concretions (also, promotes simple unit tests)

The Dependency Inversion Principle has to do with high-level (think business logic) objects not depending on low-level (think database querying and IO) implementation details.

#### Flocking Rueles

-   Select the things that are most alike.
-   Find the smallest differences between them.
-   Make the simpliest change that will remove that difference.

- Service Objects
- Concerns (reuse modules)
- DRY
- Defensive Programming
- Good naming

- use meaningful names
- functions:
    - small
    - do one thing and do it well
    - descriptive names
    - no side effects (should do what u say only)
- Objects should hide their data and expose operations
- formatting
    - programme should read like a book
- Data abstractions

#### Law of Demeter
> Object should not expose its internal structure through accessors because to do so is to expose, rather than to hide, its internal structure.

###### Class organization

- Classes should be small
- class or module should have only one reason to change

#### Code Smells & Heuristics

###### Comments

- Inappropriate Information: Only add technical notes about the code and design
- Obsolete comments remove
- Redundant comment
- poorly written comments
- commented out code

###### Environment
- Build requires more than one step
- tests require more than one step

###### Functions

function is doing more than one thing and should be split into separate methods IF:
  - Too many arguments
  - flag arguments

###### General

- Obvious Behaviour is Unimplemented
- Incorrect behaviour at the boundaries
Look for every boundary condition and write a test for it.
- Duplication
- Too much information
- Dead code
- Feature Envy

The methods of a class should be interested in the variables and functions of the class they belong to, and not the variables and functions of other classes
- Function Names Should Say What They Do
- Prefer Polymorphism to If/Else or Switch/Case
- Replace Magic Numbers with Named Constants

###### Refactoring Tips

####### Explaining Variable

```ruby
class Tipper
  TAX = 0.05
   def initialize(amount:, discount_percentage: 0, tip_percentage:)
    @amount = amount
    @discount_percentage = discount_percentage
    @tip_percentage = tip_percentage
  end
   def total
    tax       = amount * TAX
    discount  = amount * (discount_percentage / 100.0)
    tip       = amount * (tip_percentage / 100.0)
    amount + tax - discount + tip
  end
   private
   attr_reader :amount, :discount_percentage, :tip_percentage
end
```

###### Replace Variable with Query

```ruby
class Tipper
  TAX = 0.05
   def initialize(amount:, discount_percentage: 0, tip_percentage:)
    @amount = amount
    @discount_percentage = discount_percentage
    @tip_percentage = tip_percentage
  end
   def total
    amount + tax - discount + tip
  end
   def tip
    percentage_amount(tip_percentage)
  end
   def discount
    percentage_amount(discount_percentage)
  end
   def tax
    amount * TAX
  end
   private
   def percentage_amount(percentage)
    amount * (percentage / 100.0)
  end
   attr_reader :amount, :discount_percentage, :tip_percentage
end
```

###### Extract Method

```ruby
class ReceiptPrinter
  COST = {
    'meat' => 5,
    'milk' => 3,
    'candy' => 1,
  }
   TAX = 0.05
   def initialize(output: $stdout, items:)
    @output = output
    @items = items
  end
   def print
    output_items
    output_diver
    output_with_label(label: 'subtotal', cost: subtotal)
    output_with_label(label: 'tax', cost: tax)
    output_diver
    output_with_label(label: 'total', cost: total)
  end
   def total
    subtotal + tax
  end
   def tax
    subtotal * TAX
  end
   def subtotal
    @calculated_subtotal ||= items.reduce(0) do |sum, item|
      sum + COST[item].to_i
    end
  end
   private
   attr_reader :output, :items
   def divider
    '-' * 13
  end
   def output_items
    items.each do |item|
      output_with_label(label: item, cost: COST[item])
    end
  end
   def output_diver
    output.puts divider
  end
   def output_with_label(label:, cost:)
    output.puts "#{label}: #{sprintf('$%.2f', cost)}"
  end
end
```


###### Extract Class

```ruby
# Despite our best intentions, this class has acquired too many
# responsibilities.
#
# Here's an incomplete list:
#   1. Verifying that the message is valid.
#   2. Splitting email addresses on several delimiters.
#   3. Delivering invitations.
class SurveyInviter
   def initialize(attributes = {})
    @survey       = attributes[:survey]
    @message      = attributes[:message]    || ''
    @recipients   = attributes[:recipients] || ''
    @sender       = attributes[:sender]
    @email_mapper = attributes[:mapper]     || RecipientEmails.new(@recipients)
  end
   attr_reader :message, :recipients, :survey
   def valid?
    valid_message? && valid_recipients?
  end
   def deliver
    recipient_list.each do |email|
      invitation = Invitation.create(
        survey: @survey,
        sender: @sender,
        recipient_email: email,
        status: 'pending'
      )
      Mailer.invitation_notification(invitation, @message)
    end
  end
   private
   def valid_message?
    @message.present?
  end
   def valid_recipients?
    @email_mapper.valid_recipients?
  end
   def recipient_list
    @email_mapper.to_a
  end
end
class RecipientEmails
  include Enumerable
   EMAIL_REGEX = /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/
  WHITESPACE  = /\s+/
  DELIMITERS  = /[\n,;]+/
   def initialize(recipients_str)
    @emails = split_emails(recipients_str)
  end
   def each
    @emails.each{|email| yield email}
  end
   def valid_recipients
    @valid_recipients ||= select { |email| email.match(EMAIL_REGEX) }
  end
   def invalid_recipients
    @invalid_recipients ||= reject { |email| email.match(EMAIL_REGEX) }
  end
   def valid_recipients?
    invalid_recipients.empty?
  end
   private
   def split_emails(recipients_str)
    recipients_str.gsub(WHITESPACE, '').split(DELIMITERS)
  end
end
```

###### Null Object

```ruby
class NullSubscription
  def has_mentoring?
    false
  end
   def price
    0
  end
   def plan_name
    User::NO_PLAN
  end
   def charge(*) end
end
require_relative 'null_subscription'
 class FreeTrailSubscription < NullSubscription
  def has_mentoring?
    true
  end
   def plan_name
    User::FREE_TRIAL
  end
end
class User
  include ActiveModel::Model
  attr_accessor :created_at, :credit_card, :subscription
   delegate :has_mentoring?, :price, :plan_name, to: :subscription
   FREE_TRIAL = 'Free Trial'
  NO_PLAN = 'No Plan'
   def charge
    subscription.charge(credit_card)
  end
   def subscription
    return FreeTrailSubscription.new if free_trial?
    @subscription || NullSubscription.new
  end
   private
   def free_trial?
    created_at >= 30.days.ago
  end
end
Introduce Paramater Object

class Parcel
  attr_reader :weight
   def initialize(height:, width:, length:, weight:)
    @height  = height
    @width   = width
    @length  = length
    @weight  = weight
  end
   def volume
    @height * @length * @width
  end
end
class ShippingCalculator
  EXPRESS_CONVERSION_FACTOR = 3.33
  EXPRESS_RATE = 4.25
  NORMAL_CONVERSION_FACTOR = 6.67
  NORMAL_RATE = 2.75
   attr_reader :parcel
   def calculate_cost(parcel:, express: nil)
    @parcel = parcel
     return express_shipping if express
    normal_shipping
  end
   private
   def express_shipping
    total_cost(EXPRESS_CONVERSION_FACTOR, EXPRESS_RATE)
  end
   def normal_shipping
    total_cost(NORMAL_CONVERSION_FACTOR, NORMAL_RATE)
  end
   def total_cost(conversion_factor, rate)
    cost = parcel.volume * (parcel.weight / conversion_factor) * rate
    cost.round(2)
  end
end
```

###### Extract Validator

```ruby
class PhoneNumberValidator < ActiveModel::EachValidator
  PHONE_REGEX = /^(?:(?:\+?1\s*(?:[.-]\s*)?)?(?:\(\s*([2-9]1[02-9]|[2-9][02-8]1|[2-9][02-8][02-9])\s*\)|([2-9]1[02-9]|[2-9][02-8]1|[2-9][02-8][02-9]))\s*(?:[.-]\s*)?)?([2-9]1[02-9]|[2-9][02-9]1|[2-9][02-9]{2})\s*(?:[.-]\s*)?([0-9]{4})(?:\s*(?:#|x\.?|ext\.?|extension)\s*(\d+))?$/
   def validate_each(record, attribute, value)
    unless value =~ PHONE_REGEX
      record.errors[attribute] << (options[:message] || 'invalid phone number formatting')
    end
  end
end
require 'phone_number_validator'
class Person
  include ActiveModel::Validations
   validates :phone_number, phone_number: true
   attr_accessor :phone_number
   def initialize(attributes = {})
    @phone_number = attributes[:phone_number]
  end
end
```


