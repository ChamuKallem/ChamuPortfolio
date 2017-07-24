---
layout: page
title: ConfabbingApp
feature-img: "img/ConfabbAppImage2x.png"
thumbnail-path: "img/ConfabbAppImage2x.png"
short-description: Simple Chat app!!

---
# Confabbing

#### Summary

ConfabbingApp is a simple chat room app where participants get to create chat rooms and chat in those rooms. All rooms are available to all the users at any given time after they sign-up/sign-in.

#### Project set-up
This is a single page web application and I have used AngularJS for the front-end and Google Firebase for data storage.  
This was my first project using Firebase for authentication and storage.

With the help of the [Firebase docs](https://firebase.google.com/docs/web/setup) initializing firebase on index.html as follows:

```javascript
var config = {

  apiKey: "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "bloc-test-aebd8.firebaseapp.com",
  databaseURL: "https://bloc-test-aebd8.firebaseio.com",
  storageBucket: "bloc-test-aebd8.appspot.com",
  messagingSenderId: "****************"
};
firebase.initializeApp(config);


auth.signInWithEmailAndPassword
auth.createUserWithEmailAndPassword
firebase.auth().onAuthStateChanged

```

Using the above calls to firebase a user can either sign-up/sign-in or enter an already logged-in session.

![](/img/ConfabSign-inpage.png)

On success, a user can start chatting in any of the Confabbing rooms or Create a new Confab room.

roomCtrl (controller) and roomService (service) handle the messages sent accross the room for a user.

```javascript
service.addRoom
service.displayRoom
service.send
service.updateUserInFB
service.logout
```
The above calls to the roomService take care of all the operations of this Simple Chat room.

![](/img/ConfabChatRooms.png)

The user/room model in the Firebase is as shown below.

![](/img/confabDatabase.png)
