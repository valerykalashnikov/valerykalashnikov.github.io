---
layout:     post
title: "Deploying rails apps, part 1. Bootstrap the server"
date: "2015-02-07 22:05:54"
summary: "The first article of deployment series. Bootstrap the server."
categories: rails deployment
---


So, deploy time is coming. If the development with rails is funny, the deployment is like a pain - you have a day by day reading articles and googling. In this series of articles I'd like to show how to deploy simple rails application using Nginx, Puma server and Capistrano.

The problem standing in front of me is when you setting up the server following a lot of tutorials, you do it with a necessity to type(and store) a lot of passwords - and when you do it, it's a little bit uncomfortable.


## Bootstraping

Let's setting up the server. My choice is DigitalOcean server but you can use any different provider. Therefore I want to deploy my application to the smallest 521MB RAM machine, my choice is 32-bit Ubuntu because it uses less memory than 64-bit version

During setup process I've added my public key from `~/.ssh/id_rsa.pub` via DigitalOcean GUI. Of cource you could implement the same via adding you public key to `/root/.ssh/authorized_keys`

After machine created you'll be given your new machine ip. Let's loging in to the new server

## Setting up

~~~bash
  ssh root@<your new ip>
~~~

After that we should create a new user that will in charge of deployment process

~~~ bash
  adduser deploy
~~~

The systems asks you the new user password and you have to type there really strong (don't worry in the next steps we'll pre
vent us to type it on every sneeze via adding your public key to authorized_keys)

Open sudoers

~~~bash
  vim /etc/sudoers
~~~

and add there

~~~bash
  deploy  ALL=(ALL) NOPASSWD: ALL
~~~

If you use Digital Ocean and machine with minimal configuration, don't forget to use swap.

~~~bash
  dd if=/dev/zero of=/swapfile bs=1M count=1k
  mkswap /swapfile
  swapon /swapfile
  echo '/swapfile       none    swap    sw      0       0 ' >> /etc/fstab
  echo 10 | tee /proc/sys/vm/swappiness
  echo vm.swappiness = 10 | tee -a /etc/sysctl.conf
  chown root:root /swapfile
  chmod 0600 /swapfile

~~~

After that

~~~bash
  login deploy -f
~~~


On deploy user:

~~~bash
  ssh-copy-id deploy@<your-machine-ip>
~~~

or

~~~bash
  mkdir -p ~/.ssh
  touch authorized_keys
  vim authorized_keys
~~~

and copy there public key from your machine and save. After that

~~~bash
  chmod 600 authorized_key
~~~

## Summary

We created the server, created deploy user and give them an ability to simple loging in via ssh.

In the next chapter we'll set up the neccessary software to our new server.
