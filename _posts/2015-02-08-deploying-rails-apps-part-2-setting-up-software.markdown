---
layout: post
title: "Deploying rails apps, part 2. Setting up the necessary software"
date: "2015-02-08 15:02:54"
summary: "The second article of deployment series. Setting up the necessary software."
categories: rails deployment
---

In the [previous](http://bigk.me/posts/deploying-rails-apps-part-1-bootstrap-the-server/) part of my articles I talk about bootstraping the server to prepare it to install neccessary software. Now we'll install RVM, Node.js, Nginx, Ruby, Bundler and Postrgres.

## Setting up the necessary software

So, the next step is setting up the RVM - tool to manage the versions of gems simplier.

Switching to root and the first thing we uprading the versions of sofware in the repository

~~~bash
  apt-get update
  apt-get dist-upgrade
~~~

Install some useful software we will need later:

~~~bash
  apt-get -y install curl git
~~~


### Rvm, ruby, and bundler installation

Switch to deploy user:

~~~bash
  login deploy -f
~~~

And install rvm after that.

~~~bash
  gpg --keyserver hkp://keys.gnupg.net --recv-keys D39DC0E3
  \curl -sSL https://get.rvm.io | bash -s stable
~~~

Don't forget to reload your shell to use rvm command.

Install ruby

~~~bash
  rvm install 2.1.1 (or version that you prefer)
~~~

Install bundler

~~~bash
  gem install bundler --no-ri --no-rdoc
  [[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"
~~~

### Node.js installation.

Next install Node.js -  Rails uses it to compile static assets.
Don't forget to switch to root or use sudo.

~~~bash
  add-apt-repository ppa:chris-lea/node.js
  apt-get -y update
  apt-get -y install nodejs
~~~

### Nginx installation.

The next step is installing Nginx - it will serve our static requests.

~~~bash
  add-apt-repository ppa:nginx/stable
  apt-get -y update
  apt-get -y install nginx

~~~
And start nginx:

~~~bash
  service nginx start
~~~

### PostgreSQL installation

My choice is using Postgres - it's smart, clever and has very convenient console.

~~~bash
  apt-get install postgresql postgresql-contrib libpq-dev
~~~

During the installation PostgreSQL created 'postgres' user. So, let's start use database console as postgres user.

~~~bash
  sudo -u postgres psql
~~~

In the console:

~~~bash
  create role deploy superuser;
  alter role deploy login;
~~~
We've created new database user 'deploy', give him superuser rights and allow him to login. This user can login but only from the localhost.

The good way is not use postgresql default config postgresql.conf. If you are not Posgtges-guru you can setting up the special tool called 'pgtune' that configure you database based on your environment.

~~~bash
  apt-get install pgtune
~~~

after that we should find postgresql.conf file to feed it to pgtune. Login to the "psql" console mentioned before and type.

~~~bash
  SHOW config_file;
~~~

Use `\q` for logout, then

~~~
  pgtune -i path/to/postgresql.conf -o path/to/postgresql.conf
  service postgresql restart
~~~

## Summary

In this section we setting up the necessary software to serve rails application. In the next section I cover how to install and configure Capistrano with Puma server and how to configure Nginx.



