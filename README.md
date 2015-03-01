CodersTV Chat
================

Smart package for CodersTV' chat. It does not persist messages on Mongo.

## Install

To install in a new project:
```bash
> meteor add coderstv:chat
```

## Quick Start

You need to configure at least one kind of account. Supported social plataforms now are:
* Facebook
* Google

For example, with Google:

```bash
meteor add accounts-google
```

```html
<template name="index">
  {{> chatroom}}
  {{> loginButtons}}
</template>
```

## Required Configuration

### Host (Room)

Host is the same thing as room. To set a host, just do on your code:

```javascript
Session.set('chatHost', 'room1');
```

If that session var is empty, Chat will try to get the current `Path` (See `Path` definition above).

### Path

If you don't want to have a global chat for entire website, you can set a Path so you can have a different chat box for each Path you set.
What's needed to do on javascript part is set a Path. Path is a reactive source of current page path (like window.location.pathname).
So, whenever it changes, it must be updated calling ```Path.set(path)```

### Users information

Users info will be at the `Meteor.users` document.

To get users info, first, you need to manually expose users with the superchat attribute. For example, on your code, publish the users:

```javascript
// server
Meteor.publish('chatUsers', function () {
  return Meteor.users.find({/* add your filter*/}, {fields: {superchat: 1}});
});
```

Then, subscribe to the publication and turn on the subscripitions ready flag (also on your code):

```javascript
Meteor.subscribe('chatUsers', function () {
  Session.set('chatSubsReady', true);
});
```

Now you are going to have users information on chat.

## Other config and example

### Example with iron-router
```javascript
Router.map(function () {
  this.route('index', {
    path: '/',
    before: function () {
      Path.set(Router.current().location.get().path);
    }
  });
});
```

### Making height responsive
```
Template.parentTemplate.rendered = function() {
	$(window).resize(function () {
		var height = $(this).height(); // you can set a value you want here
		$('#chat-wrapper').height(height);
	});
	$(window).resize(); // trigger the resize
}
```

### Tracking users online

You will have to set up the Meteor-presence publication and
subscription:

```javascript
/* On Server */
Meteor.publish('superChatUserPresence', function (whereAt) {
  var filter = whereAt ? {'state.whereAt': whereAt} : {};

  return Meteor.presences.find(filter, {fields: {state: true, userId: true}});
});

/* On Client */
Meteor.Presence.state = function() {
  return {
    online: true,
    whereAt: Path()
  };
}

Deps.autorun(function () {
  Meteor.subscribe('superChatUserPresence', Path());
});
```

### Number of messages on chat area and default profile image

Just put on the client, on startup (if you override the default object,
make sure you put all those params):

```javascript
Superchat = {
  messageLimitOnScreen: 50,
  defaultProfilePicture: 'http://i.imgur.com/HKSh9X9.png'
};
```

## Dependencies

* [Meteor Streams](https://github.com/arunoda/meteor-streams).
* [Meteor Presence](https://github.com/tmeasday/meteor-presence)
