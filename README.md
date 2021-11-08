So this is a very basic Sinatra app...

```ruby
#sinatra/app.rb
class MakersBNB < Sinatra::Base

  get '/spaces' do
    @spaces = Space.all

    erb :'spaces/all'
  end

  get '/spaces/new' do
    erb :'spaces/new'
  end

  post '/new-space' do
    @space = Space.create(name: params[:name], desc: params[:desc], price: params[:price])

    redirect '/spaces'
  end

  run! if app_file == $PROGRAM_NAME
end
```

let's take the first route as an example.
```ruby
#sinatra/app.rb
...

get '/spaces' do
  @spaces = Space.all

  erb :'spaces/all'
end

...
```

In Rails the Model, Views and Controllers are connected by **naming convention**. So for `Space` we will have the following files:

`./app/controllers/spaces_controller.rb`

`./app/models/space.rb`

and then a folder for the views:

`.app/views/spaces/`

In Sinatra, we link the views with `erb :'spaces/all'` etc... but in Rails we create a file inside the spaces folder called `index.html.erb` (and the equivalent for _new_, etc).

```ruby
# sinatra/app.rb
...

get '/spaces/' do
  @spaces = Space.all

  erb :'spaces/all' # DONE
end

...
```

Next, for the routing... in Sinatra we use get `get '/spaces/' do` but in Rails, we go to another file: `./config/routes.rb`.

The file looks like this:
```ruby
# rails_app/config/routes.rb

Rails.application.routes.draw do
  resources :spaces
end
```

Now there aren't any strings of routes in here - what `resources` does is create a route for each method in `spaces_controller.rb` (will come to this in a second)

```ruby
# sinatra/app.rb
...

get '/spaces' do # DONE
  @spaces = Space.all

  erb :'spaces/all' # DONE
end

...
```

On to the controller...
```ruby
# rails_app/app/controllers/spaces_controller.rb

class SpacesController < ApplicationController

  def index
    @spaces = Space.all
  end

end
```

First point is that `index` is the default here - it is the equivalent of `/spaces`.

In `routes.rb`, a route is now established between our model and our view. If we then call `@spaces` in our view, it will take all of the Spaces from our database.

```ruby
# sinatra/app.rb
...

get '/spaces' do # DONE
  @spaces = Space.all # DONE

  erb :'spaces/all' # DONE
end

...
```

Now let's add the other methods that were routed in our Sinatra app.

```ruby
# rails_app/app/controllers/spaces_controller.rb

class SpacesController < ApplicationController

  def new
    @space = Space.new
  end

  # get '/spaces/new' do
  #   erb :'spaces/new'
  # end

  def create
    @space = Space.create(space_params)
    redirect_to spaces_url
  end

  # post '/new-space' do
  #   @space = Space.create(params)
  #   redirect '/spaces'
  # end

  def index
    @spaces = Space.all
  end

  # get '/spaces' do
  #   @spaces = Space.all
  #   erb :'spaces/all'
  # end

  private

  def space_params
    params.require(:space).permit(:message)
  end
end
```

So our Controller effectively defines which data the Model should feed into the Views, and then our `routes.rb` file actually creates the routes for these views.


