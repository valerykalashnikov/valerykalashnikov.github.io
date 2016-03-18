---
title: The easiest way to localize SPA application using polyglot.js
layout: post
summary: 'The simplest way to localize your modern single page application'
categories: javascript
---


For a long time ago when I was a teenager with long black hair excited with new Flash page, there wasn't such thing like web page localization with JavaScript. Indeed, there weren't any JavaScript-intensive pages to localize.

But at the age where JavaScript got triumph and each kid makes their own framework you can be encountered with such question.

Firstly, your should google and find library to localize your application. I found [polyglot.js](http://airbnb.io/polyglot.js/) but your can find something similar.

Then create your_locale.json file in js/locale directory (or something elswhere)

*en.json*

~~~json
{
"sign_in": "Sign in"
}
~~~

Next include the code below somewhere you want

~~~javascript
  //Obtain locale from somewhere (cookie, localstorage, url ...)
  var locale = getLocale();
  // Gets the language file.
  $.getJSON('js/locales/' + locale + '.json', function(data) {
    // Instantiates polyglot with phrases.
    window.polyglot = new Polyglot({phrases: data});
    TextBack.init();
  });
~~~

That's great but we have to use it in our favourite template engine preferably with handlebar moustache logo:

~~~javascript
  Handlebars.registerHelper('t',
    function(str){
      return polyglot.t(str);
    }
  );
~~~

Now we can use it in our templates files like this

~~~html
  <a href="/sign_in" class="sign-in">{% raw %}{{t 'sign_in'}}{% endraw %}</a>
~~~

That's it!
