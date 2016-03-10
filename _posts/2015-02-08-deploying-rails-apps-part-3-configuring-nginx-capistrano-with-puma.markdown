---
layout: post
title: "Deploying rails apps, part 3. Configuring capistrano, nginx with puma"
date: "2015-02-08 15:07:54"
summary: "The third article of deployment series. Configuring capistrano, nginx  with puma"
categories: rails deployment
---

In the [previous](http://bigk.me/posts/deploying-rails-apps-part-2-setting-up-software/) article we've set up the necessary soft for our rails application. In this article we'll meet wit Capistrano, configuring Nginx and Puma server and logrotate.

## Capistrano

To the Gemfile of your application add this lines or neccessary gems, mentioned below:

~~~
  gem 'puma'
~~~
and

~~~
  group :development do

    gem 'capistrano'
    gem 'capistrano-rails'
    gem 'capistrano-bundler'
    gem 'capistrano-rvm'
    gem 'capistrano3-puma'

  end
~~~


I like puma in development because it's better than Webrick. But if you like something different (like [pow](http://pow.cx/)) in development add 'puma' gem to :production section.

After that:

~~~
  bundle install
  cap install
~~~

To the new Capfile add

~~~
  require 'capistrano/rvm'
  require 'capistrano/bundler'
  require 'capistrano/rails/assets'
  require 'capistrano/rails/migrations'
  require 'capistrano/puma'
~~~

To config/deploy.rb modify this strings:

~~~
  set :application, 'your_application_name'
  set :repo_url, 'your repository_url'

  set :deploy_user, 'deploy'

  set :deploy_to, "/home/#{fetch :deploy_user}/apps/#{fetch :application}"

  set :rvm_ruby_version,  '2.x.x@your_gemset --create'
~~~

And set linked files and dirs after that. Linked dirs or files - its files and directories that necessary for the project but not  designated to store in your repository. For example config files like database.yml, and folders like vendor/bundle, log, public/uploads (if you use something like CarrierWave to crop photos). This files have to be in a 'shared' folder and each release should contain only symlinks for that otherwise it won't appear in the next release (because that is not presented in the repository)

~~~
  set :linked_files, fetch(:linked_files, []).push('.env', 'config/you_first_config.yml', 'your_second_config')
  set :linked_dirs, fetch(:linked_dirs, []).push('log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'another_folder')
~~~

For the security reasons you should store your configuration values such 'secret_key_base', postgres username and password, different oauth client_id and client_secrets in environment variables. So, add to your Gemfile

~~~
  gem 'dotenv-rails'
  bundle install
~~~

add
~~~
.env
~~~

to your .gitignore file and create .env.example with example parameters in the root of the repository (you should store this file in the remote repository)

And another one - you should have file config/environments/production.rb. The rails default installation contains this file and I suppose that you know or can uderstand this options. If not you should met with [configuring](http://guides.rubyonrails.org/configuring.html) rails applications.

After that we should deploy the project the first time we will get the **error**. Don't worry, for now it's okey.


~~~
  cap production deploy
~~~
Add to config/deploy/production.rb

~~~
role :app, %w{deploy@your-host-machine-ip}
role :web, %w{deploy@your-host-machine-ip}
role :db,  %w{deploy@your-host-machine-ip}
~~~

Then ssh to your server as deploy user, go to ~/apps/your_app_folder/shared and create .env file with your very secret values(for syntax and more see [.env](https://github.com/bkeepers/dotenv)).
Don't forget to create in shared folder files linked before (yes, that files and folders mentioned in ~~~set :linked_files~~~). So, your config files for example like secrets.yml, sidekiq.yml and others should be in ~/apps/your_app_folder/shared/config


After you do it, run again

~~~
 cap production deploy
~~~

If your need to stop/start puma, use

~~~
  cap production puma:start
  cap production puma:stop
~~~

Okey, it's time to configure Nginx


Login as root and create file /etc/nginx/sites-available/your_app_name.conf and open it via your favorite editor. For example

~~~
vim /etc/nginx/sites-available/your_app_name.conf
~~~

add there this config options

~~~
upstream application {
  server unix:/home/deploy/apps/APPNAME/shared/tmp/sockets/puma.sock fail_timeout=0;
}

server {

  listen 80;

  server_name DOMAIN;
  root /home/deploy/apps/APPNAME/current/public;
  try_files $uri/index.html $uri @application;

  location @application {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;

    proxy_pass http://application;

    access_log /var/log/nginx/APPNAME.access.log;
    error_log /var/log/nginx/APPNAME.error.log;
  }

  location ~ ^/(assets|images|javascripts|stylesheets|swfs|system|uploads)/ {
    gzip_static on;
    expires max;
    add_header Cache-Control public;
    add_header Last-Modified "";
    add_header ETag "";

    open_file_cache max=1000 inactive=500s;
    open_file_cache_valid 600s;
    open_file_cache_errors on;
    break;
  }

  client_max_body_size 10M;
  keepalive_timeout 10;

  error_page 500 502 504 /500.html;
  error_page 503 @503;

  location = /50x.html {
    root html;
  }

  location = /404.html {
    root html;
  }
  location @503 {
    error_page 405 = /system/maintenance.html;
    if (-f $document_root/system/maintenance.html) {
      rewrite ^(.*)$ /system/maintenance.html break;
    }
    rewrite ^(.*)$ /503.html break;
  }
  if ($request_method !~ ^(GET|HEAD|PUT|POST|DELETE|OPTIONS)$ ){
    return 405;
  }

  if (-f $document_root/system/maintenance.html) {
    return 503;
  }

  location ~ \.(php|html)$ {
    return 405;
  }
}
~~~

where replace 'your_app_name' to your real app name. Tell about each line is out of this article because this articles is like a tutorial for people like me. When I stood in front of the problem deployment problem it was the last thing that I wanted to know what means each line of this config.

After that go to /etc/nginx/sites-enabled and create symlink to the file described below.

~~~
cd /etc/nginx/sites-enabled
ln -s ../sites-available/your_app_name.conf
~~~
And restart nginx

~~~
  service nginx restart
~~~

Set up logrotate

~~~
  # save as /etc/logrotate.d/APPNAME
  /home/deploy/apps/APPNAME/shared/log/*.log {
    su deploy deploy
    daily
    missingok
    rotate 30
    compress
    delaycompress
    notifempty
    copytruncate
  }

  sudo logrotate /etc/logrotate.d/APPNAME -f
~~~

That is all. If all was correct you should see your site in the browser at http://your-machine-ip

