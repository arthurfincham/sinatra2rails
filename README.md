So this is a very basic Sinatra app...

```ruby
# sinatra_repo/app.rb
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

#### The blue links below each of the following Sinatra lines go to their Rails equivalent!

```ruby
# sinatra_repo/app.rb
```
[Rails Routes](#Routing)
```ruby
get '/spaces' do
```
[Rails Controller](#Controller)
```ruby
  @spaces = Space.all
```
[Rails Views](#Views)
```ruby
  erb :'spaces/all'
```
```ruby
end
```

## Conventions for a model/class in Rails

In Rails the Model, Views and Controllers are connected by **naming convention**. So for `Space` we will have the following files:

`./app/controllers/spaces_controller.rb`

`./app/models/space.rb`

and then a folder for the views:

`.app/views/spaces/`

In Sinatra, we link the views with `erb :'spaces/all'` etc... but in Rails we create a file inside the spaces folder called `index.html.erb` (and the equivalent for _new_, etc).

## Routing

```ruby
# sinatra_repo/app.rb

get '/spaces/' do
```

For the routing... in Sinatra we use get `get '/spaces/' do` but in Rails, we go to another file: `./config/routes.rb`.

The file looks like this:
```ruby
# rails_repo/config/routes.rb

Rails.application.routes.draw do
  resources :spaces
end
```

Now there aren't any strings of routes in here - what `resources` does is create a route for each method in `spaces_controller.rb`.

## Controller

```ruby
# sinatra_repo/app.rb

  @spaces = Space.all
```

On to the controller...
```ruby
# rails_repo/app/controllers/spaces_controller.rb

class SpacesController < ApplicationController

  def index
    @spaces = Space.all
  end

end
```

First point is that `index` is the default here - it is the equivalent of `/spaces`.

So the index method above will set the variable `@spaces` to `Space.all` for the index page only (which is _https://example.com/spaces_).

Now let's add the other methods that were routed in our Sinatra app.

```ruby
# rails_repo/app/controllers/spaces_controller.rb

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


## Views

```ruby
# sinatra_repo/app.rb

  erb :'spaces/all'
```

In Rails we'll use erb like we did in Sinatra - only difference is that the file is located within a folder that relates to the controller name.

So if we want our URL to be _https://example.com/spaces_, we have a file `rails_repo/app/views/spaces/index.html.erb`.

For anything other than index, the file name should match the end of the url.

For example the view for _https://example.com/spaces/new_ can be found in `rails_repo/app/views/spaces/new.html.erb`



