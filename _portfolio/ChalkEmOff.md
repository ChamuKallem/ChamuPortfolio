---
layout: post
title: ChalkEmOff
feature-img: "img/ChalkEmOffImage.jpg"
thumbnail-path: "img/ChalkEmOffImage.jpg"
short-description: Get organized with Todo Lists!

---
#### Summary
Welcome to the organized world! ChalkEmOff is an api-only Rails 5 app which will help users build a Todo app with their preferred Front-end technology.

The app is intended for users who plan to build a Todo App with any of their preferred Front-end technology.
This Todo web app will help end users build Todo Lists (categories) and individual items under each Todo List (Todo items).
Since it is an api-only app, it skips generating view files.
I have made an attempt to test some basic user authentication routes and todo-api call routes using RSpec.

#### User stories
* A user should be able to sign-up into a free a/c
* A user should be able to login to an already registered a/c
* A user should be able to create/edit/delete todo-lists (categories)
* A user should be able to create/edit/delete items(tasks) under a particular todo-list
* A user should be able list (either in POSTMAN or in a rails-console) the list of Todo (categories) and Items(tasks under each todo-list).

#### Project Set-up

This project was done using 'rails', '~> 5.0.2'. I used 'devise_token_auth' for user authentication.


Since this is a api-only Rails app,  - -api option is used to generate the Project

$ rails new ChalkEmOff - -api

After this, the subsequent user->Todos->Items models were created and the routes have been set-up as follows:

```ruby
Rails.application.routes.draw do
  mount_devise_token_auth_for 'User', at: 'auth'
  scope module: 'api' do
    namespace :v1 do
      resources :users, only: [:index, :show]
      resources :todos do
        resources :items, only: [:index, :create]
      end
      resources :items, only: [:show, :destroy, :update]
    end
  end
end
```
With this in place routes established for user/session(auth), todos and todo-items models are as follows:

```bash

          Prefix Verb   URI Pattern                               Controller#Action
       new_user_session GET    /auth/sign_in(.:format)            devise_token_auth/sessions#new
           user_session POST   /auth/sign_in(.:format)            devise_token_auth/sessions#create
   destroy_user_session DELETE /auth/sign_out(.:format)           devise_token_auth/sessions#destroy
      new_user_password GET    /auth/password/new(.:format)       devise_token_auth/passwords#new
     edit_user_password GET    /auth/password/edit(.:format)      devise_token_auth/passwords#edit
          user_password PUT    /auth/password(.:format)           devise_token_auth/passwords#update
                        POST   /auth/password(.:format)           devise_token_auth/passwords#create
      user_registration DELETE /auth(.:format)                    devise_token_auth/registrations#destroy
                        POST   /auth(.:format)                    devise_token_auth/registrations#create
    auth_validate_token GET    /auth/validate_token(.:format)     devise_token_auth/token_validations#validate_token
               v1_users GET    /v1/users(.:format)                api/v1/users#index
                v1_user GET    /v1/users/:id(.:format)            api/v1/users#show
          v1_todo_items GET    /v1/todos/:todo_id/items(.:format) api/v1/items#index
                        POST   /v1/todos/:todo_id/items(.:format) api/v1/items#create
               v1_todos GET    /v1/todos(.:format)                api/v1/todos#index
                        POST   /v1/todos(.:format)                api/v1/todos#create
                v1_todo GET    /v1/todos/:id(.:format)            api/v1/todos#show
                        DELETE /v1/todos/:id(.:format)            api/v1/todos#destroy
                v1_item GET    /v1/items/:id(.:format)            api/v1/items#show
                        DELETE /v1/items/:id(.:format)            api/v1/items#destroy
```


#### Testing

I have used the RSpec testing framework to test some of the above routes. I have also used Faker and FactoryGirl to seed testing data.

Testing User authentication as follows:
```ruby
describe 'user unauthenticated ops' do
  it 'registration returns success for valid params' do
        post "/auth", params: {email: Faker::Internet.email, password: "12345678", password_confirmation: "12345678"}
        expect(response).to be_success
  end
  it 'a new user added' do
    expect{
      post "/auth", params: {email: Faker::Internet.email, password: "12345678", password_confirmation: "12345678"}
    }.to change(User, :count).by(1)
  end
end
```

Retrieving and creating todo_lists as follows:
```ruby
describe 'GET /todos/:id' do
  before { get "/v1/todos/#{todo_id}"}
  it "returns success" do
    expect(response).to be_success
  end
  it "returns the todo" do
    expect(json).not_to be_empty
    expect(json['data']['id']).to eq(todo_id.to_s)
  end
end
describe 'POST todos' do
  let(:todo_values) { {title: "Kitchen List", complete: false, user_id: @id}}
  before { post '/v1/todos', params: todo_values}
  it "returns success" do
    expect(response).to be_success
  end
  it "creates a todo" do
    expect(json['data']['attributes']['title']).to eq('Kitchen List')
  end
  it "increases todo count by 1" do
    expect{ post '/v1/todos', params: {title: "Costco List", complete: false, user_id: @id}}.to change(Todo, :count).by(1)
  end
end
```

Another example of testing 'deleting items under a todo_list':
```ruby
describe 'DELETE /v1/items/:id' do
  it "reduces items count by 1" do
    puts "before"
    puts Item.count
    expect{ delete "/v1/items/#{item_id}" }.to change(Item, :count).by(-1)
    puts "after 1st delete"
    puts Item.count
  end
  let(:item_id) { items.last.id }
  it "returns status code" do
    delete "/v1/items/#{item_id}"
    expect(response).to have_http_status(204)
    puts "after 2nd delete"
    puts Item.count
  end
end
```
#### Creating/Viewing the todo_lists

Using POSTMAN we can create/edit/view the users/todos/items

![](/img/postman-todos.png)

Or we can use the conventional rails console to create/edit/view the users/todos/items

![](/img/rails-console-todos.png)
