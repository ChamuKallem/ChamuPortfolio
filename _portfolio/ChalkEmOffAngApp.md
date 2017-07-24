---
layout: post
title: ChalkEmOffApp
feature-img: "img/ChalkEmOffImage.jpg"
thumbnail-path: "img/ChalkEmOffImage.jpg"
short-description: Create Todos and ChalkEmOff as you go.

---
#### Summary

The app is intended for users who plan to build a Todo App with any of their preferred Front-end technology.
This Todo web app will help end users build Todo Lists (categories) and individual items under each Todo List (Todo items).
I have used AngularJS to develop a Front-end and built a simple app calling on the RESTful todo-api.

![](/img/ChalkEmOffApp.png)


#### Project Set-up

This a Front-end that is built on api-only Rails 5 app (ChalkEmOff) which uses the token based authentication devise_token_auth. The Front-end wrapper for AngularJS is the [ng-token-auth](http://github.com/lynndylanhurley/ng-token-auth) library. By configuring the authProvider and injecting the ng-token-auth into the App, SignUp and SignIn views/routes can be set up.

```ruby
$authProvider.configure({
  apiUrl: 'http://localhost:3000',
  handleRegistrationResponse: function(response) {
        alert("Registration response");
        console.log(response.data);
        return response.data;
    }
    .
    .
    .
    .
    angular
        .module('ChalkEmOffApp', ['ng-token-auth', 'ui.router', 'ui.bootstrap'])
        .config(config);
```
![](/img/ChalkEmOffSignin.png)

#### Views
Once the user is signed-up/signed-in, the todoCtrl retrieves all(if previously created) todos and their items into $scope for that particular user_id by making the api-call to the ChalkEmOff app.
```ruby
$http.get('http://localhost:3000/v1/todos').
then(function(responseList) {
    console.log(responseList);
    // $scope.users = response.data;
    angular.forEach(responseList.data.data, function(obj){
      if (obj.attributes["user-id"] == user_id){
        Todos.push(obj);
      }
    });
    // $scope.Todos = responseList.data.data;
    $scope.Todos = Todos;
```
As the todos.html view loads, the $scope.Todos are read into the view.
```
<div class="list-group todos-left-panel-section">
  <div ng-repeat="todo in Todos" >
        <div class="list-group-item todo-name todo-name-hover" ng-click="todolist.showTodoItems(todo.id, todo.attributes.title )">
            {{todo.attributes.title}}</br>
        </div>
  </div>
  <div class="list-group-item new-todo-button">
    <button type="button" name="new-todo-button" ng-click="todolist.createTodo()" ng-model="newTodo">New Todo</button>
  </div>
</div>
```
![](/img/ChalkEmOffTodoList.png)

#### Controllers

userAuthCtrl and todosCtrl are the two main controller functionality.

$auth.submitLogin, $auth.submitRegistration and $auth.signOut are managed by the userAuthCtrl.

On the other hand, todosCtrl manages the api-calls to ChalkEmOff api-only app to make the necessary calls to create/show/delete/update the todolist and its items.

Sample code for few basic operations:
```ruby
this.createTodoCat = function(){
  var catName = document.getElementById('newTodoName').value;
  console.log(catName);
  var data = {"title": catName, "complete": false, "user_id": user_id};
  $http.post('http://localhost:3000/v1/todos/', JSON.stringify(data)).
  then(function(responseTodos) {
      console.log(responseTodos);
      if (responseTodos.data.data){
        $scope.Todos.push(responseTodos.data.data);
        $scope.newTodo = false;
        $scope.Todos.Items = [];
        $scope.Todos.title = catName;
        $scope.Todos.todo_id = responseTodos.data.data.id;
      }
     });
  document.getElementById('newTodoName').value = "";
};
this.addTodoItem = function(){
  var itemName = document.getElementById('newItemName').value;
  console.log(itemName);
  var data = {"name": itemName, "done": false};
  var str = $scope.Todos.todo_id + '';
  $http.post('http://localhost:3000/v1/todos/' + str + '/items', JSON.stringify(data)).
    then(function(responseNewItem) {
      console.log(responseNewItem);
      if (responseNewItem.data.data){
        $scope.Todos.Items.push(responseNewItem.data.data);
      }
     });
   document.getElementById('newItemName').value = "";
};
this.deleteItem = function(item_id){
  var itemIndex = $scope.Todos.Items.map(function(e) { return e.id; }).indexOf(item_id);
  var str = item_id + '';
  $http.delete('http://localhost:3000/v1/items/' + str).
    then(function(delteResponse) {
      console.log(delteResponse);
      $scope.Todos.Items.splice(itemIndex, 1);
     });
  console.log("entered delete Item");
};
this.deleteTodo= function(todo_id){
  var todoIndex = $scope.Todos.map(function(e) { return e.id; }).indexOf(todo_id);
  var str = todo_id + '';
  $http.delete('http://localhost:3000/v1/todos/' + str).
    then(function(delteResponse) {
      console.log(delteResponse);
      $scope.Todos.splice(todoIndex, 1);
      if ($scope.Todos.length > 0){
        $scope.Todos.title = $scope.Todos[0].attributes.title;
        $scope.Todos.todo_id = $scope.Todos[0].todo_id;
        $state.reload();
      }
     });
  console.log("entered delete todo");
};
```
The JSON response from these api-calls has been manipulated to fit the controls on the views.
