---
layout: page
feature-img: "img/InfopediaListimage.png"
thumbnail-path: "img/InfopediaListimage.png"
short-description: Create Wikis and share information.

---
# Infopedia

#### Summary

This is an application that allows users to create wikis (public and private). It lets users upgrade to a premium membership in order to create private wikis and add other users as collaborators for the private wiki.

#### Project set-up
This application is developed with AngularJS as front-end and Rails backend. I have integrated [Stripe](https://stripe.com/) for users to pay (test-mode) using credit cards and upgrade their a/c in Infopedia to a premium membership.
I also used [Devise](https://github.com/plataformatec/devise) gem for user authentication. User model defined as
```ruby
class User < ActiveRecord::Base
  has_many :collaborators
  has_many :wikis, through: :collaborators

  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable
  enum role: [:admin, :standard, :premium]
  after_initialize :set_default_role, :if => :new_record?
  delegate :wikis, to: :collaborators

  def set_default_role
    self.role ||= :standard
  end
  def collaborators
    Collaborator.where(user_id: id)
  end
end
```
There are 3 user roles/authorization that I have defined. Admin, standard and premium with default role being 'standard'.
I used [Pundit](https://github.com/elabs/pundit) gem for scoping out authorization for these roles.

![](/img/InfopediaWelcome.png)

Once the user model is created using the Devise gem, I created the Wiki model adding the User reference.
```bash
$ rails g model Wiki title:string body:text private:boolean user:references:index
```
Defining all the routes as
```
Rails.application.routes.draw do
  # resources :wikis
  resources :wikis do
    resources :collaborators, only: [:create, :build, :destroy]
  end
  resources :charges, only: [:new, :create, :destroy]
  devise_for :users, controllers: {registrations: 'registrations'}
  get 'about' => 'welcome#about'
  root 'welcome#index'
  resources :users do
    member do
      get :confirm_email
    end
  end

end
```
![](/img/InfopediaListWikis.png)

![](/img/InfopediaEditWiki.png)

Adding and deleting collaborators was a very interesting part of developing this project. As shown below, there is a drop-down to pick a collaborators from the user_list. I had orginally created another drop down in the same form which consisted of already selected Collaborators. The challenge was to pick a collaborator from this second drop down and deleting it. a delete button
