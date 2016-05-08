## Bootstrap - Launching a new app

I don't know about you but this is the time I love the least... Strange, ain't it? I'm always looking to build something from scratch and when I'm given the chance, the first steps are always the hardest for me.
Why? Can't quite explain it.. Maybe its because setting up a new app isn't something I'm used to, or maybe because there are always TOO many choices..

One thing to learn while moving forward on your career as a software developer is that if you can limit the amount of choices you have to make then you're already winning.
This is one of the reasons I love Rails - so many things have already been chosen by someone else - The ORM, the view layer, javascript libraries, etc...
This is not optimal though.. but optimal is something you have time to learn on your way into mastering rails (or any other thing you choose to work with).

For the reasons above, I'm trying to stick with the most vanilla approach possible while still introducing a few not-so-traditional things when it comes to Rails apps.

### Setup

I'm using Ruby 2.3.0 on Rails 4.2.6.

As for extra dependencies:

* minitest-rails (because test unit is ugly)
* minitest-rails-capybara (to help me with acceptance/integration tests)
* pry-rails
* haml-rails (because ERB is just an awful HTML templating language)

I'll stick with the default sqlite database, no need for anything else right now.

### Let's get started

So, first thing is creating the app:

```
$ rails new the_polyglot
```

Then add in the gem dependencies (in the `Gemfile`):

```
gem 'haml-rails'

group :development, :test do
  gem 'pry-byebug'
  gem 'minitest-rails'
  # ...
end

group :development do
  gem 'pry-rails'
  gem 'thin'
  # ...
end

group :test do
  gem 'minitest-rails-capybara'
  # ...
end
```

Now run bundler: `$ bundle`

Because I choose to use `minitest`, I need to run the install generator: `$ rails generate minitest:install`
And because I'm going to use `minitest-rails-capybara`, we also need to add `require 'minitest/rails/capybara'` to our `test/test_helper.rb`

And run the server: `$ rails s`

Yay! My app is up and running - It does nothing but at least it doesn't crash! :D

### Starting the development process

So, the app is setup, whats next? Start testing!
Looking at what we have for this iteration, I'm going to pickup the `GET /entries`.. Why? There are a number of reasons:

0. read endpoints (GET, HEAD, ...) are simple, they simply state something like "ok, give me the stuff behind this url".
0. read endpoints are a good starting point because they usually need very basic stuff in place.
0. From an acceptance test point of view, by the time we are implementing the write endpoints (PUT, POST, ...), we can use the read endpoints to assert that the writes were successful.

To start this we will need to create an acceptance test for the `GET /entries` call. For now this will be a simple test and we will elaborate later.
Create a file named `tests/acceptance/entries_test.rb` - this will be the base for our entries acceptance tests - and within create a simple test like:

```ruby
require 'test_helper'

describe "Entries", :capybara do
  describe '/entries' do
    it 'responds HTTP 200 OK' do
      visit '/entries'
      page.status_code.must_equal 200
    end
  end
end
```

Then run it: `$ rake test test/acceptance/entries_test.rb`

This will yield something like:

```
  1) Error:
Entries::capybara::/entries#test_0001_responds HTTP 200 OK:
ActionController::RoutingError: No route matches [GET] "/entries"
    test/acceptance/entries_test.rb:6:in `block (3 levels) in <top (required)>'
```

What does this mean? Well, we are missing a route! Lets add it in (bear with me...):

```ruby
Rails.application.routes.draw do
  resources :entries
end
```

Now if we run out tests again what do we get?

```
Entries::capybara::/entries#test_0001_responds HTTP 200 OK:
ActionController::RoutingError: uninitialized constant EntriesController
    test/acceptance/entries_test.rb:6:in `block (3 levels) in <top (required)>'
```

Well, now we have the route but there is no matching controller... Lets generate it: `$ rails g controller Entries`
This will generate lots of stuff (including some test files) but more interestingly this will generate an empty Entries controller.

Now, running tests still fails, but we get a totally different error:

```
Entries::capybara::/entries#test_0001_responds HTTP 200 OK:
AbstractController::ActionNotFound: The action 'index' could not be found for EntriesController
    test/acceptance/entries_test.rb:6:in `block (3 levels) in <top (required)>'
```

This basically says that we have the route and the controller in place, but we are still lacking the corresponding view..
Because we are not asserting anything on the content of that particular view, we could just do something like:

```
$ touch app/views/entries/index.html.haml
```

This will create an empty view file, which will be enough.
Now, running the tests again we see all passes! YAY! We've seen it Red, we've made it Green. Now we only need to refactor while keeping green.

The only refactor I can think of, right now, is that we've exceeded a bit with the routes, maybe something a bit more like `resources :entries, only: :index` would suffice.
Running the tests still yields green, so, BAM! The first version of our app is DONE!
Lets recap what we've done:

- Created an acceptance test for the "User visits blog" story
- Created an entry on routes to support the running user story
- Created a controller that will be handed the call
- Stubbed the template response (empty for now)

That was a nice set of achievements!

### What now?

We have an extremely basic functionality on our `GET /entries` call, what else do we need? Well, we need it to actually list existing entries..
For that I would like to write the following test:

```ruby
  it 'lists existing entries' do
    # Given a set of entries
    entry1 = create(:entry, title: 'entry one')
    entry2 = create(:entry, title: 'entry two')
    entry3 = create(:entry, title: 'entry three')

    # When I visit the /entries page
    visit '/entries'

    # Then I see the existing entries
    page.must_have_text('entry one')
    page.must_have_text('entry two')
    page.must_have_text('entry three')
  end
```

But if we run this test we will get something like:

```
1) Error:
  Entries::capybara::/entries#test_0002_lists existing entries:
  ArgumentError: Factory not registered: entry
```

This is because those `create(:entry)` expressions are FactoryGirl expressions that require us to have a "entry" factory that would build "entry" model instances.
We don't have this factory yet... We don't even have the model, nor the migration that will generate the corresponding table on the database, so lets use one of the many 
rails generators to help us out:

```
$ rails g model Entry title:string body:text published_on:date
```

This will generate a few files: the Entry model, a migration, a Factory, and a unit test, check them out.
Because we have a new migration we need to run `$ rake db:migrate` before re-running the tests, else we will get another error message saying that we forgot to run the migrations.

As this entity comes to life we need to think a bit about it. I think that there should be no entry without title - lets write a test for it.
This is a unit test, so lets open up the generated unit test and add a validation test in it.
Note: the file rails generated will probably have some testunit generated code in, so just delete that - we are using minitest/spec.

```ruby
require "test_helper"

describe Entry, :model do
  it 'is not valid without title' do
    entry = build(:entry, title: nil)
    entry.wont_be :valid?
  end
end
```

Run this, it will fail. Now we need to add a validation on the model (app/model/entry.rb):

```ruby
  validates :title, presence: true
```

Are we done with this? Not really. We test that our model is invalid without title, but do we test if is valid with title?
The fact is, if you know a bit of rails you know how presence validation works and you know that the `validates` I wrote will mark each instance valid as long as it has any title, but
what if in some later time, someone was to come and change that `validates` statement into something else? Shouldn't the any of the tests blow up if that other implementation did not met
the exact same contract as the `validates` statement?
I can easily supply another implementation that would make the current tests pass and that would not behave the same, all it would take is to remove the `validates` statement and add this in:

```ruby
  def valid?
    false
  end
```

Tests would still pass but this wouldn't be what I want, would it?
The lesson here is to test multiple scenarios and never forget to test both happy and sad paths.
To do so, just add a simple 'is valid with title' test:

```ruby
  it 'is valid with title' do
    entry = build(:entry, title: 'Random Title')
    entry.must_be :valid?
  end
```

So.. are we done? The Entry model sure seems so (for now), but if we run the integration test it still fails, why? Nothing gets rendered. Lets get to that.
What we need is some way of getting stuff from the database into the view. On a typical Rails app this coordination is done on the controller - we need to fetch entries and pass them on to
the views, lets dive that.

