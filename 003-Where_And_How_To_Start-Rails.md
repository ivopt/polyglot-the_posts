## The Polyglot: Where and how to start - Rails

### A note on architecture

I'm really not a fan of the way Rails forces that "MVC-like structure" down your throat, but the fact is that for simple apps it can be beneficial.
Because my objective is to compare multiple frameworks and languages, I'll keep by the rails "golden path", just as I'll do with the other tools I'll use to later re-implement this blog.

### A note on approach

I'm going for a BDD/TDD approach on this one, but I'm not resorting to Cucumber, which would be the default for BDD.
Cucumber tests have the tendency to run slow. Plus, I normally only resort to Cucumber when I need to test some JS behavior. For this project I won't need to test JS so..

As for method, I'm going top-to-bottom which means that I'll follow the flow:

1. Write an acceptance test
2. See it fail
3. Write a integration test
4. See them fail as well
5. Write unit tests
6. See them fail, make them green, refactor
7. See the integration tests go green, refactor
8. See the acceptance test go green, refactor
9. Feature done, start over with another feature.


**Acceptance tests** will cover all the happy paths form a end user perspective. They are the ones that by reading them, any developer will have an immediate and quite solid idea of what the app is supposed to do. These tests run on top of a fully running app (with server, services, databases, etc...)

**Integration tests** dig a little deeper than the acceptance tests by doing a few more interactions and dealing with error and edge cases. On these tests I'm usually not so worried with the readability of the tests themselves but rather on covering what has not been covered with the acceptance tests. Most times I feel comfortable running these with with everything underneath the controller layer mocked out, but it depends from case to case. This way these tests are super fast.

**Functional tests** Explore most permutations that can be invoked on a public API (and by API I don't mean what the "cool kids" think an API is - a set of JSON endpoints - I'm talking API of an object, of a layer, etc..) with the least knowledge possible for the implementation (preferably not knowing anything at all but then mocking the layers bellow gets kind of hard sometimes).

**Unit tests** Test every single thing under the sun on an object. It tries its best to walk all possible code branches for the implementation it is testing.


Maybe I'm abusing on the naming conventions for these tests. Most people would call "Acceptance tests" to cucumber tests but and I've said, cucumber tends to be slow and I actually don't see Gherkin as real communications tool between developers and analysts. I feel Acceptance Tests should be used for communication between developers, and this is why I'm ok doing them on something a bit more "code like".


### Setup

I'm using Ruby 2.3.0 on Rails 4.2.6.

As for extra dependencies:

* factory\_girl
* minitest-rails
* minitest-rails-capybara
* pry-rails

I'll stick with the default sqlite database for now, no need for anything else right now.

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


