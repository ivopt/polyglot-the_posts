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

* factory\_girl
* minitest-rails
* minitest-rails-capybara
* pry-rails

I'll stick with the default sqlite database, no need for anything else right now.

### Let's get started

So, first thing is creating the app:

```
$ rails new the_polyglot
```

Then add in the gem dependencies:

```
group :development, :test do
  gem 'pry-byebug'
  gem 'factory_girl'
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


