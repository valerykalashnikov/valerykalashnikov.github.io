---
title: Active Admin custom JavaScript for diverse pages
layout: post
summary: 'Simple hack to add per page JS to active admin'
categories: javascript rails active_admin
---


Active Admin provides fancy language to implement admin interface fast. But sometimes it would be very helpful to have an ability to add some custom JavaScript to diverse pages and actions.

For example, we have file ```articles.rb``` in ```app/admin``` folder.
And we want to add some custom JS only to edit action, for example.
Of course, we can manipulate JavaScript adding one in ```config/active_admin.rb```:

~~~ruby
  config.register_javascript 'custom_javascript.js'
~~~

But it would be awesome to add per page javascript for separate action ('edit' as you remember). During my research and googling I found one hack to implement such functionality:

Put ```layout.rb``` into ```app/admin``` folder with this content:

~~~ruby
  module ActiveAdmin::Views::Pages::BaseExtension
    def add_classes_to_body
      super
      @body.set_attribute "data-controller", params[:controller].gsub(/^admin\//, '')
      @body.set_attribute "data-action",     params[:action]
    end
  end
  class ActiveAdmin::Views::Pages::Base
    # mixes in the module directly below the class
    prepend ActiveAdmin::Views::Pages::BaseExtension
  end
~~~

Add to ```app/assets/javascripts/active_admin.js.coffee```

~~~coffeescript
  ActiveAdmin = {
    dashboard: {},
    articles: {}
  }

  ActiveAdmin.articles.edit = () ->
    console.log('articles#edit')

  $(() ->
    controller = $('body').data('controller')
    action = $('body').data('action')
    ActiveAdmin[controller][action]() if ActiveAdmin[controller]? && ActiveAdmin[controller][action]?
  )
~~~

Well, that's it.


