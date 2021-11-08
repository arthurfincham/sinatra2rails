### [:floppy_disk: DatabaseConfig](#DatabaseConfig)

### [:gem: Favourite Gems](https://github.com/arthurfincham/sinatra2rails/blob/main/gems.md)

#### So this is a very basic Sinatra app...

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

and then a directory for the views:

`.app/views/spaces/`

In Sinatra, we link the views with `erb :'spaces/all'` etc... but in Rails we create a file inside the spaces directory called `index.html.erb` (and the equivalent for _new_, etc).

## Routing

In Sinatra, we set the route for a page with...
```ruby
# sinatra_repo/app.rb

get '/spaces/' do
```

...but in Rails, we define our routes in a seperate file:
```ruby
# rails_repo/config/routes.rb

Rails.application.routes.draw do
  resources :spaces
end
```

Now there aren't any strings of routes in here - what `resources` does is create a route for each method in `spaces_controller.rb`.

One other that is worth noting is how we set our _root route_.

In Sinatra we do:
```ruby
get '/' do
```
In Rails the equivalent is
```ruby
root to: 'home#index'
```
`'home#index'` will look inside our **views** directory for a sub-directory called **home**, and then render the file `index.html.erb`.

#### To see all the routes for our repo, run `rails routes` from the terminal!

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

First point is that `index` is the default here - it is the equivalent of `/spaces` (assuming that we have not set `'space#index'` as our **root** route)

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

In Rails we'll use erb like we did in Sinatra - only difference is that the file is located within a directory that relates to the controller name.

So if we want our URL to be _https://example.com/spaces_, we have a file `rails_repo/app/views/spaces/index.html.erb`.

For anything other than index, the file name should match the end of the url.

For example the view for _https://example.com/spaces/new_ can be found in `rails_repo/app/views/spaces/new.html.erb`

## DatabaseConfig

So with our Sinatra apps, we created tables in the database with SQL queries. Going along with the example of Spaces, it was something like...

```bash
% psql
```
```bash
> \c makersbnb
```
```sql
CREATE TABLE spaces (
    id SERIAL PRIMARY KEY, 
    name VARCHAR(40) NOT NULL,
    description VARCHAR(255) NOT NULL,
    available_from DATE NOT NULL,
    available_to DATE NOT NULL,
    price INT NOT NULL, 
    url VARCHAR, 
    user_id INTEGER REFERENCES users(id)
    );
```


I actually think that this is **way** easier in Rails.

Normally I do it like this.
```bash
% rails g migration CreateSpacesTable
```

This will create a migration file:
`rails_repo/db/migrations/12341asdfg7adfg7_Create_Spaces_Table.rb`
(the files start with a long key for some reason).

Then we open the file and there will be some boiler plate stuff there.
```ruby
# rails_repo/db/migrations/12341asdfg7adfg7_Create_Spaces_Table.rb

class CreateSpaces < ActiveRecord::Migration[5.1]
  def change
    create_table :spaces do |t|
      t.string :name

      t.timestamps
    end
  end
end
```
now we can transpose our SQL queries into their Rails equivalents.
```ruby
# rails_repo/db/migrations/12341asdfg7adfg7_Create_Spaces_Table.rb

class CreateSpaces < ActiveRecord::Migration[5.1]
  def change
    create_table :spaces do |t|
      # name VARCHAR(40) NOT NULL 
      t.string :name, null: false, limit: 40
      # description VARCHAR(255) NOT NULL
      t.text :description, null: false, limit: 255
      # available_from DATE NOT NULL,
      t.date :available_from, null: false
      # available_to DATE NOT NULL,
      t.date :available_to, null: false
      # price INT NOT NULL,
      t.integer :price, null: false
      t.timestamps
    end
  end
end
```

This should create a SQL table as if we have run the commands in SQL itself, with one difference:

We are missing ...
```sql
user_id INTEGER REFERENCES users(id)
```
... which associates each Space with a User.

In Rails, we do this through the **Model**.

We create a new file for the Model:

`rails_app/app/models/space.rb`

Here, we can set up things like associations.

```ruby
class Space < ApplicationRecord
  belongs_to :user
end
```


