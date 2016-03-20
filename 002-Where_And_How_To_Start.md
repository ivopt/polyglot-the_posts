## The Polyglot: Where and how to start

### Let me think about this...
So, I'm off to build myself a blog (yes, I'm reinventing the wheel, but why not?). Let me think about what I want and what would be the "mvp".

For my blog I'd like to have:
* A nice **homepage** where I would show a few of the **latest entries** and perhaps a **list of "pinned" entries** - having the **search** here would also make sense...
* An index of **blog entries** that would be searchable (in the same sense as a google search makes sense).
* A **list of tags** to tag **blog entries** with and to serve as a quick filter for the visitors of my blog.
* A **bio page** with an ugly picture of me and a few bits of info on me like stuff I've worked on, stuff I like, etc...
* A **view blog entry** page that would show the blog entry, that would have an area for people to add **comments** and that would have a small up/down vote button (just so I know that people liked or disliked my post)

It would also be nice to have a **backoffice** kind of tool that would allow me to:
* **manage** the blog entries.
* While managing blog entries, I'd like to be able to have non-published blog entries (those I'd be working on)
* **manage** tags.
* **manage** my bio.
* **manage** the homepage.

### The MVP
The big question now is: **do I need all of this to get the thing going?** most probably not. So, what would be the **minimum viable product**? 
* Well, **search** is useless when you have little content, so that's a feature to add later.
* The **homepage** is nice but I can live without it.
* **Tags** are nice, but I can live without them for now. Later I'll add them, meaning that I'll have to go back and tag the already existing entries, but that won't be a big problem.
* **Comments** can wait, after all, they are not essential to the whole idea of me publishing my thoughts.
* The whole **backoffice** can wait, for now I'll manage to get the entries into the blog in some weird manner, after all, this is something that only I will use, not the people visiting my blog, so I can afford to not tend to my own needs for a while...

Whats left?

* The **blog entries index**
* The **view blog entry page**
* And because of my big ego, my **bio page**.

Sounds like decent set of things to start. When you're building something, its important to have an idea of the general goal you're trying to achieve but don't let yourself be stuck on trying to understand every single detail of how and why you're building that something. If you try to do that, you're most likely to never actually build anything. 

Don't be afraid of writing code that you may end up throwing away, that its part of growing your app. I'd say that more than 50% of the code you write today will be replaced in the near future, so instead of trying to do it perfect on the first go, just try to make it flexible so it will be easy to change.

### So, time to code?.. not just yet.

Don't be eager to jump into code. I know writing code is a lot of fun but that fun can be easily spoiled if you jump straight into it without thinking on what you'll be building.
From an outside perspective, I'd say this blog needs:

* A page to list blog entries, ordered by date (from most recent to oldest)
* A page to show a particular blog entry
* A page to show my bio

From this I kind of extract 2 entities: **The blog entry** and **the bio page**.

Blog entries are things with a **title** and a **body** that can be a huge piece of text, contain links do images, fragments of code, etc..
My bio page is a huge piece of text and contains at least one picture of my ugly self... kinda like a blog entry!

Now I'm having a mixed feeling: If these 2 things are so similar, should they be represented by the same entity? I honestly don't think so. I may be wrong, if so somewhere in the future I may need to review this.
My bio kinda seems like "seeded data" - something that should exist from day 1 - and it will be accessed in a very unique way.
Also (and this is premature) I easily can imagine having other "fixed" pages like my bio (like a small portfolio kind of thing).. So I'd say there is a **Page** entity here.

So, lets summarize a bit:

* 3 endpoints: `/entries`, `/entries/:id` and `/bio`
* 2 entities: **Blog Entry** and **Page** 

Things are kind of thought out, time to act!
