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

    it 'respondes with HTTP success' do
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

Running the controller tests now will be green, but going back to the acceptance tests we see that the responded page doesn't have the content we are expecting on our test (and that is no surprise).

So, lets add the needed code to the view:

```haml
%h1 Entry

%h2= @entry.title

%p= @entry.published_on.to_s(:long)

%p= @entry.body
```

But the tests will now explode, and thats because we are setting `@entry` to `1` when it should be an `Entry` object. Lets fix that:

```ruby
  def show
    @entry = Entry.find(params[:id])
  end
```

This will make all our tests green. WOW That was fast! You see, TDD does not have to be slow, actually it is the fastest way to develop.
When I started to do TDD, I felt it was slowing me down, that stuff would take so much time to do... The main reasons behind this was inexperience and fear I was not doing things properly. Don't let this feeling overwhelm you and it did to me. Keep insisting on TDD and eventually you'll get to the point where it actually improves your velocity.
