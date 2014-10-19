CodersTV Chat
================

Smart package for CodersTV' chat. It uses Arunoda's Meteor Streams.

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
  {{loginButtons}}
</template>
```

## Configuration

If you don't want to have a global chat for entire website, you can set a Path so you can have a different chat box for each Path you set.
What's needed to do on javascript part is set a Path. Path is a reactive source of current page path (like window.location.pathname).
So, whenever it changes, it must be updated calling ```Path.set(path)```

I've removed loginButtons from it and the popover that shows up to tell
people to login, because loginButtons frag has an id on its div. And if
you have another button on your site, it blows up your code. **So add them
to your site!**

### Example with iron-router
```javascript
Router.map(function () {
    this.route('index', {
        path: '/',
        before: function () {
            Path.set(Router.current().path);
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

### Number of messages on chat area

Just put on the client, on startup (if you override the default object,
make sure you put all those params):

```javascript
Superchat = {
    messageLimitOnScreen: 50,
    defaultProfilePicture: 'http://i.imgur.com/HKSh9X9.png'
};
```

## Dependencies

Thanks @arunoda for his great Meteor project called [Meteor
Streams](https://github.com/arunoda/meteor-streams).

Thanks @tmeasday for this awesome package [Meteor
Presence](https://github.com/tmeasday/meteor-presence)
