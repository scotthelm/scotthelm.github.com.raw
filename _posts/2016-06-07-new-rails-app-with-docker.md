---
title: "New Rails App With Docker"
layout: base
show_title: true
tags: tech tools docker
---
There are a ton of posts out there on this topic. The purpose of this post is
to show the step-by-step creation of a new dockerized rails application, hooked
up to postgres. The next article in the series will show how to set up rspec and guard for your testing pleasure, and the final article in the series will highlight some tools that make development in docker an even more enjoyable experience.

## Prerequisites
In order to follow along, you will need to install docker on your system. For
Mac and Windows,
[this link will get you started](https://www.docker.com/products/docker-toolbox).
If you are in a linux environment, you will need:

* [Docker](https://docs.docker.com/linux/)
* [Docker Machine](https://docs.docker.com/machine/install-machine/)
* [Docker Compose](https://docs.docker.com/compose/install/)

Come back once you get it installed. I'll wait.

## Getting Started
Cool. The first thing we'll need is an empty directory. In order for mounted
volumes to play nicely, you'll need to create this directory somewhere in your
home folder:

    ~/development $ mkdir myapp
    ~/development $ cd myapp

##The Gemfile
The next thing we'll need is a Gemfile. This is where we'll start setting up our development environment.

    # frozen_string_literal: true
    source 'https://rubygems.org'

    gem 'rails'

    group :development, :test do
      gem 'rspec-rails'
      gem 'guard-rspec'
      gem 'rubocop'
    end

##The Dockerfile
Now that we have a Gemfile, we can start building our docker image. Docker works on the principal of linux containers, and uses a layered file system. A docker image is like a
photoshop image. You start with a base layer (in this case, it's `ruby:latest`) and then we start layering our changes on top of it. Every command you see in the `Dockerfile` changes the file system in some way. Docker responds to this by creating another layer on top of the base image. In our first iteration of the Dockerfile, we specify that we want Ruby 2.3.1, and that we want our code to live in `/usr/src/app`. We set our working directory to that location and copy our gemfile into it.

    FROM ruby:2.3.1

    WORKDIR /usr/src/app

    COPY Gemfile .

# The docker-compose.yml
This file describes the services in our application. This will be an iterative process to build our application step-by step. Doing it this way, we don't even have to have ruby installed on our system. The Docker image handles that for us.

    version: "2"
    services:
      app:
        context:
          build: .
          dockerfile: Dockerfile
        volumes:
          - .:/usr/src/app

This file lets us specify that we have a service called `app`. We want to build an image for `app` using the current directory as the contents to layer on top of the base image. We want to use the Dockerfile that we created earlier, and we want to mount our current directory (in this case: `~/development/myapp`) to the /user/src/app directory in the container. We'll get into the difference between an Docker image and a Docker container later.

## Building our app image
So now that we have the rough-in of our plumbing done, we should be able to build the Docker image for our app. We can do this by using docker-compose:

    ~/development/myapp $ docker-compose build

We should get some output like this (yours will probably need to pull the ruby base image down, so you will see some progress bars as this happens).

    Building app
    Step 1 : FROM ruby:2.3.1
     ---> 316ceaa79560
    Step 2 : WORKDIR /usr/src/app
     ---> Running in 64c2cf1a6324
     ---> f677d3669a90
    Removing intermediate container 64c2cf1a6324
    Step 3 : COPY Gemfile .
     ---> 64cd3ad6bf21
    Removing intermediate container 6b71f68d3fc7
    Successfully built 64cd3ad6bf21

We now have a Docker image of our app that has our Gemfile in it. Now we need to install the gems.

## Running our first container
You can think of the difference between an image and a container much like the difference between a class and an instance of a class. The image is the class. It is a full filesystem, and it has everthing needed for running your ruby process. However, it's static. It is an image of a running system. The running system is a container. The container uses the image to run a single process. When that process ends, so does the container. Poetic, really. The first thing we are going to do is run a bash session so we can bundle our gems. Because our current directory is mounted as the volume `/usr/src/app` in the container, changes to that directory in the container are reflected in our host system. The `--rm` indicates that we want to remove the `app` container when the bash process ends.

    ~/development/myapp $ docker-compose run --rm app /bin/bash

You should now be at a prompt as root in your new container:

    Creating network "myapp_default" with the default driver
    root@ff372d086cd0:/usr/src/app#

Here, we will run 

    root@ff372d086cd0:/usr/src/app# bundle install

This will install the gems from our `Gemfile` and create a new `Gemfile.lock` in the container AND on our host system. You should get the usual bundler output. type `exit` at the prompt and you should be back on the host system. If you `ls` you should now see a new file: `Gemfile.lock`

## Dockerfile Part Deux
Now that we have a Gemfile.lock, we need to get that into our image. Additionally, we need to install the gems into the image. To do this, we need to modify our `Dockerfile` so the image has everything our container needs in order to install our rails app. The new dockerfile should look like this:

    FROM ruby:2.3.1

    WORKDIR /usr/src/app

    COPY Gemfile .
    COPY Gemfile.lock .

    RUN bundle install

Now we need to run

    ~/development/myapp $ docker-compose build

again. This will install the gems into the image, and allow us to use them when we launch the image as a container

## docker-compose.yml Part Deux
We want to mimic our production environment, so we want to use Postgresql as our database. Turns out, Postgresql has an official image that we can use - so let's add it as a service and link it to our app in our `docker-compose.yml` file:

    version: "2"
    services:
      app:
        build:
          context: .
          dockerfile: Dockerfile
        volumes:
          - .:/usr/src/app
        links:
          - db
      db:
        image: postgres:9.5.3


## Installing Rails
Start a new container using

    ~/development/myapp $ docker-compose run --rm app /bin/bash

Now that we're back inside the container, we can install rails with Postgres as the database, and skipping tests (we'll set up rspec in a minute):

    root@5ff8a0519772:/usr/src/app# rails new -T --database=postgresql .

You'll be prompted to overwrite your Gemfile...type Y. You'll also be warned not to run bundler as root. Ignore that since this is a container, and the only user is root! Once that's done, you can `exit` out of the container, and you should have all the files for a new Rails application in your host directory.

There's a couple of other modifications we'll need to make. We'll need a javascript runtime, so we can uncomment `gem 'therubyracer'` in the Gemfile. Wel'll also need to set up our database connections in our `config/database.yml` file:

    development:
      <<: *default
      host: db
      database: <%= ENV['RAILS_DATABASE_NAME'] %>
      username: <%= ENV['RAILS_DATABASE_USER'] %>
      password: <%= ENV['RAILS_DATABASE_PASSWORD'] %>
    test:
    <<: *default
      host: db
      database: <%= ENV['RAILS_TEST_DATABASE_NAME'] %>
      username: <%= ENV['RAILS_DATABASE_USER'] %>
      password: <%= ENV['RAILS_DATABASE_PASSWORD'] %>

"Wait..." I hear you say. "Where do those environment variables come from?" I'm glad you asked:

## Environment Variables
The easiest way to accomplish this is with a `.env` file. This file can be git ignored so it never hits your remote repo, but still has your super secret username/password stuff. It has this format:

    RAILS_DATABASE_NAME=app
    RAILS_TEST_DATABASE_NAME=app_test
    RAILS_DATABASE_USER=postgres
    RAILS_DATABASE_PASSWORD=

To use it we need to modify our `docker-compose.yml` again

    version: "2"
    services:
      app:
        build:
          context: .
          dockerfile: Dockerfile
        volumes:
          - .:/usr/src/app
        links:
          - db
        env_file: .env
      db:
        image: postgres:9.5.3

## Safety Third
If you haven't already...Now is probably a good time to `git init` and make your initial commit.

## Dockerfile Part The-One-After-Deux
Installing rails modified our Gemfile and Gemfile.lock. It also added all the files for a rails app - so we need to build the new image. In order to do that, our new `Dockerfile` should look like this:

    FROM ruby:2.3.1

    WORKDIR /usr/src/app

    COPY Gemfile .
    COPY Gemfile.lock .

    RUN bundle install

    COPY . /usr/src/app

now build it again

    ~/development/myapp $ docker-compose build

## Starting Postgres
The first thing we need to do is create our database. In order to do that, we're going to start our postgresql container thusly:

    ~/development/myapp $ docker-compose up -d db

This command starts a postgres container in the background (-d) as defined by our `db:` service in our `docker-compose.yml`

## Creating The Database
Now that we have a running postgres instance, we can create our development and test databases

    ~/development/myapp $ docker-compose run --rm app rake db:create

If all goes well, it should simply return. On repeat invocations, you should get the following output:

    ~/development/myapp $ docker-compose run --rm app rake db:create
    app already exists
    app_test already exists

##Running The App
To do this, we're going to need to add a command and open some ports to our `app:` container. To do that, we're going to modify our docker-compose file again:
	
    version: "2"
    services:
      app:
        build:
          context: .
          dockerfile: Dockerfile
        volumes:
          - .:/usr/src/app
        links:
          - db
        env_file: .env
        command: rails s -p 3000 -b 0.0.0.0
        ports:
          - "3000:3000"
      db:
        image: postgres:9.5.3

This starts a server when we bring the container up, it also exposes port 3000 all the way to the host machine. To start the server, we run

    ~/development/myapp $ docker-compose up -d

To find out where the app is running, you can use

    ~/development/myapp $ docker-machine ls
    NAME      ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER    ERRORS
    default    *        virtualbox   Running   tcp://192.168.99.100:2376           v1.10.2

    ~/development/myapp $ docker-machine env default
    export DOCKER_TLS_VERIFY="1"
    export DOCKER_HOST="tcp://192.168.99.100:2376"
    export DOCKER_CERT_PATH="/Users/Scott/.docker/machine/machines/default"
    export DOCKER_MACHINE_NAME="default"

That docker host ip is what we're interested in. Now you should be able to open your browser to `http://192.168.99.100:300` and see that lovely new rails app page.

## Next Time...
The next article will be all about setting up rspec and guard 
