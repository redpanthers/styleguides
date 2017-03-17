
# Ruby on Rails


## Skip generators

Rails by default generates a lot of unnecessary files
when we run `rails g controller`.
To avoid polluting our codebase
with unnecessary files,
place this code in the `application.rb`
of your Rails project.

```rb
config.generators do |g|
  g.helper false
  g.stylesheets false
  g.javascripts false
  g.view_specs false
end
```

You can read more about from our
[blog](http://blog.redpanthers.co/customize-rails-auto-generation/).


## Development Gems

Listed below are the list of development/test Gems that we commonly use.

```ruby
group :development do
  gem 'better_errors'
  gem 'byebug'
  gem 'quiet_assets'
  gem 'letter_opener'
  gem 'bullet'
end
```


## Migrations

Don't edit or remove existing migrations files.
It is all right to edit migrations
if it's on a branch that you're working on,
but once a migration has reached staging or production,
it should not be changed.

- [Rails Guides: Changing Existing Migrations](http://edgeguides.rubyonrails.org/active_record_migrations.html#changing-existing-migrations)


## Models

- Don't use default scope -
  it makes it harder to understand
  where a certain scope is being added.

```ruby
# Bad
default_scope { order(created_at: :desc) }

# Good
scope :feed, -> { order(created_at: :desc) }
```

Now, instead of `Post.all`,
you would use `Post.feed`,
which makes it clear
what scope is being added.

## `lib/` folder

- There should be no direct communication
  between code written here and Rails models.
- The model/controller should call this
  class under lib folder get the result
  and save it to the database,
  rather than the lib class saving to database directly.

## API Wrappers

Code interacting with 3rd party API
or scraping 3rd party websites
should be placed in the `lib` folder.

How the contents of file should be arranged:

```rb
# A scraper to scrpe Alexa ranks

require 'nokogiri'
require 'open-uri'
module Alexa
  # All the constants should be declared on top
  URL = 'http://alexa.com/siteinfo/'
  API_KEY = ENV['API_KEY'] # Place any key if required

  #Alexa.fetch_rank(domain: 'redpanthers.co')
  def self.fetch_rank domain:
    # write the code to fetch results
  end
end
```

Note:

- Always pass the arguments as keyword arguments.
- There should be no direct communication to the DB from the lib folder
- Always return the contents
  in the format required to be inserted into DB (as a hash).
- Do not include and use the module directly,
  all the module through self as it would be more redable


## Handling exceptions

No matter how much we plan,
there will always occur cases which we haven't expected.
It mostly happen stuff that is not under our control like:

1. User Input
2. 3rd Party API
3. Network

So while working with the above three it is always better to lead with caution.

Always use `try` if you are doubtful about an object.

```ruby
@person = Person.where(name: 'Panther').first
@person.try?(:name)
```

You can also pass in code blocks in try:

```ruby
report = params[:search_param]
.try { |term| build_search_query(term) }
.try { |formated_term| run_search(formated_term) }
```

## Timezone management

**DON’T USE**

```ruby
Time.now
Date.today
Date.today.to_time
Time.parse("2015-07-04 17:05:37")
Time.strptime(string, "%Y-%m-%dT%H:%M:%S%z")
```

**USE**

```ruby
Time.current
2.hours.ago
Time.zone.today
Date.current
1.day.from_now
Time.zone.parse("2015-07-04 17:05:37")
Time.strptime(string, "%Y-%m-%dT%H:%M:%S%z").in_time_zone
```

Read more about it from
[our blog](http://blog.redpanthers.co/working-timezones-rails/).

## Counter cache

Place counter cache where ever necessary.
Read more about on
[our blog](http://blog.redpanthers.co/counter-cache-how-to-get-started/).


## References

- [Rails Best Practices](https://rails-bestpractices.com)

