## Keep Marching On

With the first feature implemented, its time to think on the next one: Showing the details for a given Entry.
So what is the first thing to do? Write a failing acceptance test that would respond to this story:

```gherkin
Given an existing blog entry
When I see the entry detail page
Then I can see the entry's title, body and publish date.
```

which can roughly equate to something like this:

```ruby
  describe '/entries/:id' do
    it 'shows existing entry' do
      # Given an existing blog entry
      title = 'Just a title'
      body = 'A random Body'
      published_date = 1.day.ago.to_date

      entry = create(:entry, title: title, body: body, published_on: published_date)

      # When I visit the entry detail page
      visit "/entries/#{entry.id}"

      #Then I can see the entry's title, body and publish date.
      page.must_have_text(title)
      page.must_have_text(body)
      page.must_have_text(published_date.to_s(:long))
    end
  end
```

By running this we discover we have a missing route, lets add it in by white listing it on the already existing route:

```ruby
  resources :entries, only: [:index, :show]
```

Again, this will lead us to the point of having yet another error, the unexistence of the show view.
So, we would expect that when getting sow page for an existing entry, the controller should respond with a HTTP 200 and assign something to the @entry (that will be used by the view later on). Something like this:

```ruby
  describe '#show' do
    let(:existing_entry) { create(:entry) }

    it 'responds with HTTP success' do
      get :show, id: existing_entry.id
      assert_response :success
    end

    it 'assigns the @entry variable' do
      get :show, id: existing_entry.id
      assert_not_nil assigns(:entry)
    end
  end
```

Running this tells us that we are missing the show view page. Just as we did before, lets just do a touch for now:

```
$ touch app/views/entries/show.html.haml
```

And now we need to go to the controller and add that assignment:

```ruby
  def show
    @entry = 1
  end
```

Running the controller tests now will be green, but are we done? What if we try to show a non-existing entry?
This is a quite common pattern, you write your happy-path tests and forget your sad-paths.
To do this lets wrap the previous tests within a describe scope and add a new one, like this:

```ruby
  describe '#show' do
    describe 'with an existing entry' do # scope the happy path tests in its own describe
      let(:existing_entry) { create(:entry) }

      it 'responds with HTTP success' do
        get :show, id: existing_entry.id
        assert_response :success
      end

      it 'assigns the @entry variable' do
        get :show, id: existing_entry.id
        assert_not_nil assigns(:entry)
      end
    end

    describe 'with a non-existing entry' do
      it 'responds with not found error' do
        get :show, id: 12345
        assert_response :not_found
      end
    end
  end
```

But running this will tell us that we are getting a HTTP 200 (success) when we would be expecting an HTTP 404 (not_found).
This is due to the way we've implemented the show action, so maybe its time to actually implement the real thing.
On `app/controllers/entries_controller` lets make the show action do something like:

```ruby
  def show
    @entry = Entry.find(params[:id])
  end
```

But running the tests still fails because now ActiveRecord complains that there is no Entry with the non-existing id we sent in, and that is a good thing, only we now need to capture and handle the exception ActiveRecord is throwing. To do that we can use the `rescue_from` method that ActiveSupport provides. So, again in `app/controllers/entries_controller` add this:

```ruby
  rescue_from 'ActiveRecord::NotFound' do
    render nothing: true, status: 404
  end
```

Now the controller tests pass, but rendering nothing as a response to a 404 is rather unhelpful.. Maybe we should set a user story for us to later on add a nice error page detailing what happened.
For now, there is no user story to drive the development of that page so, there is no action to be made and this is a rather important thing. Just because you feel something should be done, it doesn't mean it must be done right away. When you pick a story to implement, implement it from start to end, don't go on side quests. Whenever you encounter a "side quest" write it down somewhere, in a Trello card, in a TODO list or whatever tool you use to keep track of these things, just don't do it right away if it isn't key to what you are doing.

Now, going back to the acceptance tests we see that the responded page doesn't have the content we are expecting on our test (and that is no surprise).

So, lets add the needed code to the view:

```haml
%h1 Entry

%h2= @entry.title

%p= @entry.published_on.to_s(:long)

%p= @entry.body
```

This will make all our tests green. WOW That was fast! You see, TDD does not have to be slow, actually it is the fastest way to develop.
When I started to do TDD, I felt it was slowing me down, that stuff would take so much time to do... The main reasons behind this was inexperience and fear I was not doing things properly. Don't let this feeling overwhelm you and it did to me. Keep insisting on TDD and eventually you'll get to the point where it actually improves your velocity.
