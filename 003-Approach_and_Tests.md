## The Polyglot: Where and how to start - Rails

### A note on architecture

I'm really not a fan of the way Rails forces that "MVC-like structure" down your throat, but the fact is that for simple apps it can be beneficial.
Because my objective is to compare multiple frameworks and languages, I'll keep by the rails "golden path", just as I'll do with the other tools I'll use to later re-implement this blog.

### A note on approach

I'm going for a BDD/TDD approach on this one, but I'm not resorting to Cucumber, which would be the default for BDD.
Cucumber tests have the tendency to run slow. Plus, I normally only resort to Cucumber when I need to test some JS behavior. For this project I won't need to test JS so..

First and foremost let me tell you what layers of tests I usually consider and what they mean to me:

**Acceptance tests** will cover all the happy paths form a end user perspective. They are the ones that by reading them, any developer will have an immediate and quite solid idea of what the app is supposed to do. These tests run on top of a fully running app (with server, services, databases, etc...)

**Integration tests** dig a little deeper than the acceptance tests by doing a few more interactions and dealing with error and edge cases. On these tests I'm usually not so worried with the readability of the tests themselves but rather on covering what has not been covered with the acceptance tests. Most times I feel comfortable running these with with everything underneath the controller layer mocked out, but it depends from case to case. This way these tests are super fast.

**Functional tests** Explore most permutations that can be invoked on a public API (and by API I don't mean what the "cool kids" think an API is - a set of JSON endpoints - I'm talking API of an object, of a layer, etc..) with the least knowledge possible for the implementation (preferably not knowing anything at all but then mocking the layers bellow gets kind of hard sometimes).

**Unit tests** Test every single thing under the sun on an object. It tries its best to walk all possible code branches for the implementation it is testing.


Maybe I'm abusing on the naming conventions for these tests. Most people would call "Acceptance tests" to cucumber tests but and I've said, cucumber tends to be slow and I actually don't see Gherkin as real communications tool between developers and analysts. I feel Acceptance Tests should be used for communication between developers, and this is why I'm ok doing them on something a bit more "code like".


As for method, I'm going top-to-bottom which means that I'll somewhat follow the flow:

1. Write a high level test (usually an acceptance test)
2. See it fail and discover I am lacking a controller, an action or some unimplemented behavior.
3. Write a few functional tests
4. See them fail as well
5. Write unit tests
6. See them fail, make them green, refactor
7. See the integration tests go green, refactor
8. See the acceptance test go green, refactor
9. Feature done, start over with another feature.


The drill doesn't always need to go this way. Most times, the highest level tests should only describe the happy paths (those that describe normal usage of the app) and leave the "not so happy paths" (those that end up in some sort of error) to lower layers where multiple permutations are cheaper (usually unit tests are super fast while acceptance tests are slower).
For example, assume that on the BlogPosts index page I would have a few options to allow the users to choose how many posts per page should be shown (say 10, 15 or 20). I would have a high level acceptance test to drive the development of the Blog Posts index itself, but as for the paging and the number of items shown, that would be something that I would relegate to a functional test done on the controller.


#### To TDD or not to TDD

Don't feel too guilty for not following a strict TDD approach, for years I've felt that guilt and the only place it led me was to hate TDD. Full TDD is a thing that grows on you, don't expect to have a "eureka" moment and if you push yourself too hard you'll end up trying your best not to do TDD.

When I started out, _black box_ tests seemed quite an easy and useful thing, so I would develop something and then cover it with _black box_ tests that would make every single possible interaction with the public API of the object I had just created. I call these "black box" tests and not unit tests because I would not mock out the dependencies.
Soon I realized I had problems. I ended up with methods on my objects API that were not used at all (except from the tests), all tests depended on having a complete environment running (the DB had to be up, some external REST API had to be up, etc...), tests were slow, etc...

Then I learned that to do real **Unit Tests** I would need to mock out all dependencies (and with that I learned to do Dependency Injection). This made tests more comfortable and quicker to run - no longer would I need to have everything wired up - but still I would some times end up with APIs larger than they should... And only then I learned the value of TDD. By writing the tests first, an objects API would grow naturally.

For quite some time I only did **Unit Tests**. Why? Because I didn't quite know how other tests should be done. Was this a bad thing? No, it gave me time to let TDD settle in.
When I was comfortable enough with Unit Tests, I ventured into other kinds of tests. Tests that would check if my controllers would behave as expected - checking status codes, checking if they would delegate work to the proper core objects, checking if they would setup the view environment correctly. These where **Functional Tests**, they don't differ that much from **Unit tests** but they tend to cover a few objects rather than only 1 (as Unit Tests usually do).

Still there was something missing... I had most things covered but still there would be times where clients would complain that something on my HTML was off, or some property on my REST JSON API responses was missing... I was not testing my apps from a client perspective! Thats when **Acceptance tests** and **Integration tests** came in.


So, to sum this up, don't feel too guilty for not doing TDD/BDD the right way.. The fact is: Most people don't do it "the right way" - I certainly don't. You should do what yields the best results for you and give things time to sink in.


So.. Lets get to work??
