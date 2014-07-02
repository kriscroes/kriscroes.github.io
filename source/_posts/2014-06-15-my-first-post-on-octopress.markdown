---
layout: post
title: "'Hello world' MVC app powered by ERB, Sinatra and Active Record"
date: 2014-07-02 14:32:57 -0400
comments: true
categories: Flatiron School
---

This blog post is intended as a guideline on how to set up a basic MVC (Model-View-Controller) structure in ruby using ERB, Sinatra and Active Record.

MVC is a best-practice pattern used in software systems. One of the benefits is that it can avoid spaghetti code structures and help ease maintenance (or avoid coders such as Irv from the strip below)

->{% img /images/blog1/dilbert.png 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

You can apply MVC to a basic 'Hello World' app by following the next steps (and according to a logical folder structure!):

1. **Model**: Represent data and logic by creating a ruby Class which interacts with an SQLite database via Active Record for storing and retrieving the “Hello Message”.
2. **View**: Use ERB to render the “Hello Message” to be displayed to the user.
3. **Controller**: Use Sinatra routes to ask the Model for the “Hello Message” and to send this back to the browser for user display.



->{% img /images/blog1/mvc.png 500 700 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

##Build initial folder structure

Start by creating the following basic folder structure. The Models, Views and Controllers will be grouped into a folder named 'app'. The 'config' folder will contain the environment configurations and the 'db' folder will contain the database (Active Record) migrations (i.e. create 'Hello World' table).

->{% img /images/blog1/init_folder_structure.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

##Setup environment and gems

1. Add the files 'Gemfile' and 'config.ru' to the root of the project folder.
2. Add the file 'environment.rb' to the config folder.
3. Add the file 'Rakefile' to the root of the project folder.

->{% img /images/blog1/folder_structure_2.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

Add the following code to these files. Make sure to run command 'bundle' at the end.

```ruby Gemfile
source 'http://rubygems.org'

gem 'sinatra'
gem 'activerecord', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
gem 'rake'
gem 'require_all'
gem 'sqlite3'
```

```ruby config.ru
require './config/environment'

run HelloController
```

```ruby environment.rb
require 'bundler/setup'
Bundler.require(:default)

ActiveRecord::Base.establish_connection(
  :adapter => 'sqlite3',
  :database => 'db/hello.sqlite3'
)

require_all "app"
```

```ruby Rakefile
require_relative './config/environment'
require 'sinatra/activerecord/rake'  
```

##Create Model

1. Add the file 'hello.rb' to the models folder: contains a Hello class which inherits from ActiveRecord Base.
2. Add the file 'create_hello.rb' to the migrate folder: contains the code to create the database schema. For this example, a table named 'hello' will be created with a column 'message' to hold the "Hello World!" message. Make sure to run the rake task db:migrate to create the table.

->{% img /images/blog1/folder_structure_3.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

```ruby hello.rb
class Hello < ActiveRecord::Base

end 
```

```ruby create_hello.rb
class CreateHello < ActiveRecord::Migration
  def change
    create_table :hello do |t|
      t.string :message
      end
  end
end
```

##Create Controller

1. Add the file 'hello_controller.rb' to the controllers folder: code in this file ensures that a new instance of Hello is created and persisted with a message "Hello World!". This message is then passed to ERB (Embedded Ruby) for rendering in the index template.

->{% img /images/blog1/folder_structure_4.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

```ruby hello_controller.rb
class HelloController < Sinatra::Base
  register Sinatra::ActiveRecordExtension
  set :views, Proc.new { File.join(root, "../views/") }
  
  get "/" do 
    @hello = Hello.new
    @hello.message = "Hello World!"
    @hello.save
    erb :"index"
  end 
end
```

###Create View

1. Add the file 'index.erb' to the views folder: the end result will be rendered by ERB based on the contents in this file.

->{% img /images/blog1/folder_structure_5.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-

```ruby index.erb
<h1><%=@hello.message%></h1>
```

###Voilà, the end result!

Run 'rackup' command in terminal to view end result in browser.

->{% img /images/blog1/end_result.png 300 500 'Hooray for named parameters and bootlegging, in no particular order!' %}<-













