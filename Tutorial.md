# Lighthouse Forum

Welcome to our introduction to Rails!

We're going to build a simple open forum app that allows users to **C**reate, **R**ead, **U**pdate, and **D**estroy posts. Those words should sound familiar. [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) is a central tenet to building relational database-backed applications (the kind Rails is great at!), so this tutorial should provide some great building blocks to use in the web apps you'll be building for the next few weeks and months and years. Very exciting.

As you've probably noticed, this tutorial is divided into handy [commits](https://github.com/lighthouse-labs/lighthouse_forum/commits/master). Each section correlates with a commit, and you should commit at the completion of each section.

## 1. rails new [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/e1390feced2e0d965da1e86c80fe9add4a0ab051)

First, let's make sure you have the right version of rails installed. We are using Rails version 4.0.0.

```
gem install rails -v 4.0.0

# Then, let's check that the rails command has the right version.
rails _4.0.0_ --version
```

To create a Rails app, your job couldn't be any easier. From the console for this app, type `rails _4.0.0_ new lighthouse_forum`. `cd` into the directory, open it with your editor of choice, and behold the ~~magic of Rai~~ WHOA that's a ton of files.

[Here](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book#table-rails_directory_structure)'s a nice rundown of all of these files, but we'll walk through the ones you'll need for this tutorial as we go.

Before we commit, it's a good idea to find your `README.rdoc` that's auto-generated by Rails and replace it with the more common `README.md`. You know what this is for. Feel free to edit as you wish.

## 2. routing /posts to /posts/index.html.erb [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/e7165e9c0d49d8add2b177685e8f7126a0f00b16)

Believe it or not at this point, we can already display a page. In the console, type `bin/rails server`, and you'll see the line `Rails [X.X.X] application starting in development on http://0.0.0.0:3000` (among others). This means—you guessed it—you can visit 0.0.0.0:3000 or [localhost:3000](http://localhost:3000) in your browser and see the "Welcome aboard" default Rails page. Success!

For this app, we'll need a page that displays all posts. The typical URL structure for such a page is `[root]/posts`, so let's try to get [localhost:3000/posts](http://localhost:3000/posts) to display a list of posts. (We don't have them yet, but we will very soon.)

If you visit [localhost:3000/posts](http://localhost:3000/posts) now, you'll see an error saying "No route matches `[GET] "/posts"`". This implies we sent a GET request to "/posts", but Rails wasn't sure what we were trying to do. Let's help it.

Find your `config/routes.rb` and add the `get` line to it so it looks something like this:

    LighthouseForum::Application.routes.draw do

      get 'posts', to: 'posts#index'

      # [tons of helpful comments]
    end

As you can guess, this is where [Rails' routing](http://guides.rubyonrails.org/routing.html) is handled. Now when we visit [localhost:3000/posts](http://localhost:3000/posts), we see Rails has successfully routed the request. It recognized the request to `posts` as a GET request.

But now we have another error: "uninitialized constant PostsController". Let's fix this by putting said PostsController in the correct spot. From the console, `touch app/controllers/posts_controller.rb`, and write the following code:

    class PostsController < ApplicationController
      
    end

As you know from your experience with Ruby, we've now made a new class `PostsController` that inherits from `ApplicationController`. (If you check out `app/controllers/application_controller.rb`, you'll notice it inherits from `ActionController::Base` which is where the real [magic](http://guides.rubyonrails.org/action_controller_overview.html) happens.)

Visit [localhost:3000/posts](http://localhost:3000/posts) once more and it's found `PostsController`, but unfortunately it's letting us know the "action `index` could not be found for `PostsController`." Makes sense. We've written `get 'posts', to: 'posts#index'` but there's no `index` in our `PostsController`. Let's add it:

    class PostsController < ApplicationController

      def index

      end

    end

Okay almost there! Visit [localhost:3000/posts](http://localhost:3000/posts) again and we see that our "template is missing." Easy fix. Let's create that template in the right spot. `mkdir app/views/posts` and `touch app/views/posts/index.html.erb`. We can leave it blank for now. The important thing is: [localhost:3000/posts](http://localhost:3000/posts) is displaying correctly!

## 3. displaying posts on /posts/index.html.erb [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/1f53cb42a76624f7ccdfb12c17d032aa2dafbf3a)

Now that we've successfully routed "/posts" to `posts#index` to `app/views/posts/index.html`, let's create some posts and display them.

We can use instance variables in our controller methods (`posts#index`) for use in our views `app/views/posts/index.html`. So for now, let's create an array of hashes to represent posts. Your `app/controllers/posts_controller.rb` should look something like this:

    class PostsController < ApplicationController

      def index
        @posts = [
          {
            title: "Superstar",
            author: "Carly Rae Jepson",
            text: <<-eos.gsub(/\s+/, " ").strip
              Khurram Virani has been my music idol since I started writing
              songs back when I was 4. His voice is a revelation. His stage presence
              is unparalleled. And those costumes! He remains an inspiration to this
              day.
            eos
          },
          {
            title: "Basketball Idol",
            author: "Steve Nash",
            text: <<-eos.gsub(/\s+/, " ").strip
              I remember watching Khurram Virani (#14) play back when he just playing
              pickup games on the street. Dude had moves nobody had ever seen. Breaking
              ankles. Poppin' threes. Great all-around game.
            eos
          },
          {
            title: "Acting Legend",
            author: "Michael J. Fox",
            text: <<-eos.gsub(/\s+/, " ").strip
              Back when I was in university, Khurram Virani was my acting coach. He
              studied with the best and it shows. His acting chops were already legendary
              before his teaching career began. But it seems he's actually improved!
            eos
          },
          {
            title: "Who?",
            author: "Vurram Khirani",
            text: "Never heard of this guy Khurram Virani, but he sounds great."
          }
        ]
      end

    end

(The odd `<<-eos.gsub(/\s+/, " ").strip` syntax is just to allow for multiline strings.)

Now that we have `@posts` captured, let's use it in our view. You're all familiar with ERB so `app/views/posts/index.html.erb` should be no sweat:

    <h1>Lighthouse Forum</h1>
    <hr>
    <% @posts.each do |post| %>
      <h2><%= post[:title] %></h2>
      <p><%= post[:text] %></p>
      <small>- <%= post[:author] %></small>
    <% end %>

Let's gaze upon the wonder of [localhost:3000/posts](http://localhost:3000/posts). Booyah!

## 4. statically routing each post to its own page [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/7f5bdfca1a8c0581932ee7ef7edf4ff6001a1541)

Now that our index page is looking pretty nice, let's add pages for each individual post. How else are you going to link directly to your killer post?

First let's head to `config/routes.rb`:

    LighthouseForum::Application.routes.draw do
      
      get 'posts', to: 'posts#index'

      get 'posts/0', to: 'posts#post0'
      get 'posts/1', to: 'posts#post1'
      get 'posts/2', to: 'posts#post2'
      get 'posts/3', to: 'posts#post3'

    end

Now instead of only being able to handle one type of request, we can handle five. The ones we've added take GET requests to "/posts/0", "/posts/1", "/posts/2", and "/posts/3" and route them to (soon-to-be) controller actions `posts#post0`, `posts#post1`, `posts#post2`, and `posts#post3`. You should have an idea of what to do next: we need to add these methods in our `PostsController`. Let's do it.

    class PostsController < ApplicationController

      [...]

      def post0
        @post = {
          title: "Superstar",
          author: "Carly Rae Jepson",
          text: <<-eos.gsub(/\s+/, " ").strip
            Khurram Virani has been my music idol since I started writing
            songs back when I was 4. His voice is a revelation. His stage presence
            is unparalleled. And those costumes! He remains an inspiration to this
            day.
          eos
        }
      end

      def post1
        @post = {
          title: "Basketball Idol",
          author: "Steve Nash",
          text: <<-eos.gsub(/\s+/, " ").strip
            I remember watching Khurram Virani (#14) play back when he just playing
            pickup games on the street. Dude had moves nobody had ever seen. Breaking
            ankles. Poppin' threes. Great all-around game.
          eos
        }
      end

      def post2
        @post = {
          title: "Acting Legend",
          author: "Michael J. Fox",
          text: <<-eos.gsub(/\s+/, " ").strip
            Back when I was in university, Khurram Virani was my acting coach. He
            studied with the best and it shows. His acting chops were already legendary
            before his teaching career began. But it seems he's actually improved!
          eos
        }
      end

      def post3
        @post = {
          title: "Who?",
          author: "Vurram Khirani",
          text: "Never heard of this guy Khurram Virani, but he sounds great."
        }
      end

    end

Okay now that we've set these `@post` variables, let's create the views that correlate.
`touch app/views/posts/post0.html.erb`
`touch app/views/posts/post1.html.erb`
`touch app/views/posts/post2.html.erb`
`touch app/views/posts/post3.html.erb`

I guess we should actually display something on these pages. Add this to each file:

    <h2><%= @post[:title] %></h2>
    <p><%= @post[:text] %></p>
    <small>- <%= @post[:author] %></small>

Excellent. Now when we visit [localhost:3000/posts/0](http://localhost:3000/posts/0), we see the correct post's info. Startin' to feel like somethin'!

## 5. refactoring static individual post routes to dynamic posts#show [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/864971e3909cb665848faa67d3e78cf03753fdf7)

Okay, I hope your DRY-dey sense (© Alex Naser, 2013) tingled as you were typing "0," "1," "2," and "3" in your routes, controller, and views. Obviously it won't take long for our application to get completely unmaintainable if we have to manually add a route for every single post. Crazy talk. Let's start to fix it.

First, we're going to delete those four routes we added previously and add a single line in its place. `config/routes.rb` should now look like this:

    LighthouseForum::Application.routes.draw do
      
      get 'posts', to: 'posts#index'
      get 'posts/:id', to: 'posts#show'

    end

Notice our new line has something that looks suspiciously like a variable. `:id` simply means that if a request comes in with "/posts/192023675" or anything else after "/posts", it can handle it no problem.

As you've guessed, we're going to need to remove our silly `posts#post0` methods and add a `posts#show` in our `PostsController`. Here we go:

    class PostsController < ApplicationController

      def index
        [...]
      end

      def show
        posts = [
          {
            title: "Superstar",
            author: "Carly Rae Jepson",
            text: <<-eos.gsub(/\s+/, " ").strip
              Khurram Virani has been my music idol since I started writing
              songs back when I was 4. His voice is a revelation. His stage presence
              is unparalleled. And those costumes! He remains an inspiration to this
              day.
            eos
          },
          {
            title: "Basketball Idol",
            author: "Steve Nash",
            text: <<-eos.gsub(/\s+/, " ").strip
              I remember watching Khurram Virani (#14) play back when he just playing
              pickup games on the street. Dude had moves nobody had ever seen. Breaking
              ankles. Poppin' threes. Great all-around game.
            eos
          },
          {
            title: "Acting Legend",
            author: "Michael J. Fox",
            text: <<-eos.gsub(/\s+/, " ").strip
              Back when I was in university, Khurram Virani was my acting coach. He
              studied with the best and it shows. His acting chops were already legendary
              before his teaching career began. But it seems he's actually improved!
            eos
          },
          {
            title: "Who?",
            author: "Vurram Khirani",
            text: "Never heard of this guy Khurram Virani, but he sounds great."
          }
        ]
        @post = posts[params[:id].to_i]
      end

    end

Notice we're now setting `@post` based on something called `params[:id]`. This is where that `:id` gets set from our dynamic routing line `get 'posts/**:id**, to: 'posts#show'`. [params](http://guides.rubyonrails.org/action_controller_overview.html#parameters) is simply a hash that stores information dealing with requests. Since our routes file is looking for stuff after "/posts," it knows to capture that number in `params[:id]` and pass it through to the appropriate controller. Very cool.

All we have to do now is `touch app/views/posts/show.html.erb`, and add the markup we had in the individual files:

    <h2><%= @post[:title] %></h2>
    <p><%= @post[:text] %></p>
    <small>- <%= @post[:author] %></small>

Might as well get rid of those other lame files now too.
`rm app/views/posts/post0.html.erb`
`rm app/views/posts/post1.html.erb`
`rm app/views/posts/post2.html.erb`
`rm app/views/posts/post3.html.erb`

RIP files. You will not be missed.

## 6. creating Post model and posts db table [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/3eaa764dd2fbd803d916ad424d9ee75737aafc38)

Now's when we put the _database_ in database-backed web application. Sexy!

Instead of dealing with posts represented as hashes, let's create a database table and ActiveRecord model to represent our posts. From the command line, it's easy:

    bin/rails generate model Post title:string author:string text:text

You'll notice from the output that we've created several files. There are two important ones for now: `app/models/post.rb`, and `db/migrate/[timestamp]_create_posts.rb`.

In `app/models/post.rb`, we see the following bare-bones code:

    class Post < ActiveRecord::Base
    end

But as we know from our ActiveRecord study thus far, this gives us a ton of power. (We just won't use much of it for this tutorial.)

And in `db/migrate/[timestamp]_create_posts.rb`, we see the following:

    class CreatePosts < ActiveRecord::Migration
      def change
        create_table :posts do |t|
          t.string :title
          t.string :author
          t.text :text

          t.timestamps
        end
      end
    end

Notice how the `string`s and `text` from our `bin/rails generate` command translate magically to this migration. We'll dive deeper into this as we get more comfortable with databases. From the command line, type `bin/rake db:migrate` to run this migration and create the posts table with three columns.

## 7. moving posts into db via seeds.rb [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/6aeaa552ecffb5fc44f98aa84bd87f4e6da91289)

Now that we have a db table to hold our data, let's get rid of those crazy hashes in our `PostsController`.

First, head to `db/seeds.rb` and create some post records using the ActiveRecord syntax you know and love:

    # [comments]

    Post.create!(
      title: "Superstar",
      author: "Carly Rae Jepson",
      text: <<-eos.gsub(/\s+/, " ").strip
        Khurram Virani has been my music idol since I started writing
        songs back when I was 4. His voice is a revelation. His stage presence
        is unparalleled. And those costumes! He remains an inspiration to this
        day.
      eos
    )

    Post.create!(
      title: "Basketball Idol",
      author: "Steve Nash",
      text: <<-eos.gsub(/\s+/, " ").strip
        I remember watching Khurram Virani (#14) play back when he just playing
        pickup games on the street. Dude had moves nobody had ever seen. Breaking
        ankles. Poppin' threes. Great all-around game.
      eos
    )

    Post.create!(
      title: "Acting Legend",
      author: "Michael J. Fox",
      text: <<-eos.gsub(/\s+/, " ").strip
        Back when I was in university, Khurram Virani was my acting coach. He
        studied with the best and it shows. His acting chops were already legendary
        before his teaching career began. But it seems he's actually improved!
      eos
    )

    Post.create!(
      title: "Who?",
      author: "Vurram Khirani",
      text: "Never heard of this guy Khurram Virani, but he sounds great."
    )

Now to get these into the database (to _seed_ the database), run `bin/rake db:seed` from the command line. Great! We have four records in the database! Wait, how do we know?

Let's run `bin/rails console` from the command line. Here in the [console](http://guides.rubyonrails.org/command_line.html#rails-console) we now have access to our development environment. So if you type `Post.all`, you can confirm that our posts are stored safely in the db. `bin/rails console` will be your best friend as you continue to work with Rails. Learn to love it. For now, type `exit` and let's move on.

In `app/controllers/posts_controller.rb`, we can now simply fetch the post or posts we need with ActiveRecord. This is all we need:

    class PostsController < ApplicationController

      def index
        @posts = Post.all
      end

      def show
        @post = Post.find(params[:id])
      end

    end

[So fresh and so clean](http://www.youtube.com/watch?v=-JfEJq56IwI). Let's take care of one more thing. Now that @posts and @post are ActiveRecord objects and not vanilla hashes, let's edit our views to use more idiomatic syntax. For example, instead of `@post[:title]`, type `@post.title`.

`app/views/index.html.erb`:

    <h1>Lighthouse Forum</h1>
    <hr>
    <% @posts.each do |post| %>
      <h2><%= post.title %></h2>
      <p><%= post.text %></p>
      <small>- <%= post.author %></small>
    <% end %>

`app/views/show.html.erb`:

    <h2><%= @post.title %></h2>
    <p><%= @post.text %></p>
    <small>- <%= @post.author %></small>

This is really coming together nicely.

[Suggested edit: As Active Record starts auto-incrementing id from 1 onwards, trying to access `http://localhost:3000/posts/0` corresponding to earlier `app/views/posts/post0.html.erb` would throw a `ActiveRecord::RecordNotFound` error.]

## 8. adding new post functionality [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/5a35db29ca224a278fa349f66d2619c3c396e2cb)

I'm sure you guys have been wondering: "Sure, this is fine, but the whole point of this is actually _creating new posts_!" Let's get to it.

First, we'll need two new routes to help. In `config/routes.rb`:

    LighthouseForum::Application.routes.draw do
      
      get 'posts',     to: 'posts#index'
      get 'posts/new', to: 'posts#new'
      get 'posts/:id', to: 'posts#show'
      post 'posts',    to: 'posts#create'

    end

The route pointing to `posts#new` makes enough sense, but what about that `post 'posts'`? As you might have guessed, this simply means it will route all POST requests to "/posts" to the `create` action (method) in our `PostsController`. We'll talk about how to initiate that POST request in a bit.

For now, let's tackle `posts#new`.

You know the drill, we'll need a `new` action in `app/controllers/posts_controller.rb` like so:

    class PostsController < ApplicationController

      [...]

      def new

      end

    end

We'll leave it blank for a minute while we work on its view, `app/views/posts/new.html.erb`:

    <h2>Create a post!</h2>
    <%= form_for @post do |f| %>
      <div>
        <%= f.label "title" %><br>
        <%= f.text_field "title" %>
      </div>
      <div>
        <%= f.label "author" %><br>
        <%= f.text_field "author" %>
      </div>
      <div>
        <%= f.label "text" %><br>
        <%= f.text_area "text" %>
      </div>
      <div>
        <%= f.submit "Save" %> 
      </div>
    <% end %>

There's a lot going on here, I know. First, we're using a method called [form_for](http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_for). It's worth reading a bit now about the heavy lifting it helps with. But in short, it makes a POST request to "/posts".

If we visit [localhost:3000/posts/new](http://localhost:3000/posts/new), we hit an error though: "First argument in form cannot contain nil or be empty." You may have figured out that we haven't actually set that `@post` in the `form_for` in our `PostsController` `new` action. If we add the following:

    class PostsController < ApplicationController

      [...]

      def new
        @post = Post.new
      end

    end

Our `form_**for**` knows the type of object it's **for**. (Nailed it.)

Let's take this form for a test run. Input some text into each field and click "Submit." We hit another error: "action `create` could not be found for `PostsController`." This is a good thing! It means our form is making a POST request to "/posts", and our routing of `post 'posts', to: 'posts#create'` is working as expected. All we have to do now is add the `create` action in `app/controllers/posts_controller.rb`:

    class PostsController < ApplicationController

      [...]

      def create
        @post = Post.new(
          title:  params[:post][:title],
          author: params[:post][:author],
          text:   params[:post][:text]
        )

        if @post.save
          redirect_to posts_path
        else
          render :new
        end
      end

    end

First, we build a `Post` object with the three fields from our form via the `params` hash. Notice the fields are nested inside the hash like this:

    {params: {post: {title: [user_input], author: [user_input], text: [user_input]}}}

Then we attempt to save it. On success, we'll redirect to the `posts_path`, and on failure, we'll simply render the `new` page with our form again.

The `posts_path` may look foreign. From the console, you can type `bin/rake routes` to see your routes at any given time. Notice Rails generates names like `posts_path` to help us in our controllers and views. Here we're simply redirecting back to `index` page to see our new post with all the rest.

We have one more thing to take care of before moving on. Because of a relatively newfound security issue, Rails has adopted a standard called "strong parameters" for mass assignment. You can read more about it [here](https://github.com/rails/strong_parameters), and adjust your `PostsController` like so:

    class PostsController < ApplicationController

      [...]

      def create
        @post = Post.new(post_params)

        if @post.save
          redirect_to posts_path
        else
          render :new
        end
      end

      protected

      def post_params
        params.require(:post).permit(:title, :author, :text)
      end

    end

Note we can use protected or private methods in controllers just like any other Ruby class. Nice.

## 9. adding edit post functionality [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/d35360afd2a6825eab872ce32f655138dae28494)

To add the ability to edit a post, our code will look very similar to the code in the last commit. I'll trust you'll follow no problem. First `config/routes.rb`:

    LighthouseForum::Application.routes.draw do
      
      get 'posts',          to: 'posts#index'
      get 'posts/new',      to: 'posts#new'
      get 'posts/:id/edit', to: 'posts#edit'
      get 'posts/:id',      to: 'posts#show', as: 'post' # necessary for the update action!
      patch 'posts/:id',    to: 'posts#update'
      post 'posts',         to: 'posts#create'

    end

Make sure to add the `, as: 'post'` to your `posts#show` line!

Notice we have a new type of request: PATCH. You can [read up](http://net.tutsplus.com/tutorials/other/a-beginners-introduction-to-http-and-rest/) on the different request types to learn why PATCH is now the Rails default for `update` actions.

Now let's add our controller actions in `app/controllers/posts_controller.rb`:

    class PostsController < ApplicationController

      [...]

      def edit
        @post = Post.find(params[:id])
      end

      def update
        @post = Post.find(params[:id])
        
        if @post.update_attributes(post_params)
          redirect_to posts_path
        else
          render :edit
        end
      end

      protected

      def post_params
        params.require(:post).permit(:title, :author, :text)
      end

    end

One important difference is that we're setting the @post variable to an existing record in our `edit` action as opposed to an empty object in `new`. This means we'll have our attributes preloaded for us in the `form_for`. Pretty slick.

Go ahead and `touch app/views/posts/edit.html.erb` and add this markup:

    <h2>Edit this post</h2>
    <%= form_for @post do |f| %>
      <div>
        <%= f.label "title" %><br>
        <%= f.text_field "title" %>
      </div>
      <div>
        <%= f.label "author" %><br>
        <%= f.text_field "author" %>
      </div>
      <div>
        <%= f.label "text" %><br>
        <%= f.text_area "text" %>
      </div>
      <div>
        <%= f.submit "Save" %> 
      </div>
    <% end %>

Now when you visit [localhost:3000/posts/1/edit](http://localhost:3000/posts/1/edit), you'll see `Post.find(1)`'s data already in the form, ready for you to edit. Click "Save", see the changes on the `index` page, and let's move on!

## 10. refactoring routes to use resources [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/139739b2271744e5d7d6462aa17fd482987c883d)

Since the CRUD actions we've been working on are so common, Rails provides us with the very helpful [resources](http://api.rubyonrails.org/classes/ActionDispatch/Routing/Mapper/Resources.html) method available in our `config/routes.rb`. Refactor it to look like this:

    LighthouseForum::Application.routes.draw do
      
      resources :posts

    end

And voila! Everything _just works_. You can even `bin/rake routes` again to make sure I'm not pranking you.

## 11. adding navigation using helpers [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/39df840a9f4aca8eb1f693f57d523ef15f83b660)

I can't believe you haven't gotten mad at me for waiting this long to add some freaking hyperlinks. Oh you have? I'm sorry!

Rails gives us access to a helpful method in views called [link_to](http://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to) that makes navigation maintainable within your app. Let's start by adding links on `app/views/posts/index.html.erb` to create a post and to view each existing post:

    <h1>Lighthouse Forum</h1>
    <hr>
    <%= link_to "Create a post!", new_post_path %>
    <hr>
    <% @posts.each do |post| %>
      <h2><%= link_to post.title, post_path(post) %></h2>
      <p><%= post.text %></p>
      <small>- <%= post.author %></small>
    <% end %>

We've gotten our `new_post_path` and `post_path` from `resources`. Notice you pass `post_path` the object itself, so Rails knows which record you'd like to display.

Let's zip through these other views.

`app/views/posts/show.html.erb`:

    <%= link_to "Back to all posts", posts_path %>
    <h2><%= @post.title %> (<%= link_to "edit", edit_post_path(@post) %>)</h2>
    <p><%= @post.text %></p>
    <small>- <%= @post.author %></small>

`app/views/posts/new.html.erb` and `app/views/posts/edit.html.erb`

    <%= link_to "Back to this post", post_path(@post) %>
    [...]

Now we can navigate around the app with ease.

## 12. adding delete post functionality [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/a39aa380ad44f3c98e05c511a964201afa534ee8)

If we `bin/rake routes`, we can see our DELETE route is already taken care of for us:

    DELETE /posts/:id(.:format)      posts#destroy

So let's jump straight to `app/controllers/posts_controller.rb`:

    class PostsController < ApplicationController

      [...]

      def destroy
        @post = Post.find(params[:id])
        @post.destroy
        redirect_to posts_path
      end

      protected

      [...]

    end

Easy enough. But we need to actually trigger this DELETE request from our app. How about in `app/views/posts/show.html.erb`?

    <%= link_to "Back to all posts", posts_path %>
    <h2><%= @post.title %> (<%= link_to "edit", edit_post_path(@post) %>, <%= link_to "delete", post_path(@post), :method => :delete, :"data-confirm" => "You sure?" %>)</h2>
    <p><%= @post.text %></p>
    <small>- <%= @post.author %></small>

Notice we again need to pass in the `@post` itself so the correct post is deleted.

## 13. mapping root to posts#index [★](https://github.com/lighthouse-labs/lighthouse_forum/commit/5a012579d52860db02654d25871f7fa3ef112b46)

One more commit and we'll have a fully-functioning best-practices Rails app. It's a good day.

Currently, if you visit localhost:3000, you'll still see that silly Rails default page! I think it makes sense to route the root of our application to `posts#index`. `config/routes.rb` should now look like this:

    LighthouseForum::Application.routes.draw do
      
      resources :posts
      root to: 'posts#index'

    end

Now when you visit [localhost:3000](http://localhost:3000), all is right with the world.

***
A serious congrats on building your first Rails app. They'll only get cooler from here.