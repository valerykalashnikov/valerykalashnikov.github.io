---
title: Presenters in javascript
layout: post
summary: "An article describing using presenters in your javascript code"
categories: javascript
---

If you find that you have a lot of complex logic in your views and helpers, presenters can help you to clean up your code by using object-oriented approach.

Presenter is a component containing user interface logic for a view. Presenters decoples logic from user interface, thus you are able to test your view logic via good old unit tests.

Imagine we have and object representing user's profile. A first common task is to display user's full name or username if full name doesn't exist. A second common task is to display user's avatar of a particular size or return a fallback.

Implement all this logic in views would be like a noodles, so it will be better to create a presenter for this purposes.

~~~javascript
  Application.Presenters = Aplication.Presenters || {};
  (function () {
      'use strict';

      Application.Presenters.Profile = function(data) {
          this.data = data;

          return {
              avatarUrl: this.avatarUrl(),
              fullName: this.findChannel('sms')
          }

      }

      Application.Presenters.Profile.prototype = {

          avatarUrl: function(size) {
              var avatarUrl = this.data.avatar
              if (!!avatarUrl) {
                return [avatarUrl, 'size='+ size].join('&')
              }
              else{
                return 'fallback/user/default.png&size=' + size
              }
          },

          fullName: function(){
            var fullName = this.data.name
            if (!!fullName) {
              return fullName;
            }
            else {
              return this.data.username
            }
          }

      }

  })();
~~~

The next step is in initialization of the presenter object and passing it to your compiled template

~~~javascript
  var presenter = new Application.Presenters.Profile(profileObject);
  renderTemplate(
      yourCompiledTemplate(presenter)
  )
~~~

Here we go!