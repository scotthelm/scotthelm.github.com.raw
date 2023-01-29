---
layout: post
title: "Fencepost"
show_title: true
tags: tech ruby
---

When it comes to writing and maintianing software, I'm lazy.
I don't like doing mindless work. If I find myself doing the same thing
more than twice, I will find a way to automate that thing in some way so I don't
have to keep repeating it.

## The Problem
Ruby on Rails changed the way it handles input from a web request from version
3.2 to 4.0. It used to be that you defined what you allowed to be set via
update_attributes in the model itself by using `attr_accessible`. This was all
well and good until the new version of Rails removed that mechanism for
model security in favor of something called `strong_parameters`.

Strong Parameters are a good thing. They increase the flexibility of the application
by letting you define which parameters are allowed to be passed to your models
outside the models themselves.

The Problem is that I have more than a few applications I need to upgrade to
Rails 4, and that means I have to go into every controller in each of those
applications and create my strong parameters declarations. Not only that: I'm
lazy. And that means I need to automate this in some way or I will go insane.

## Enter Fencepost

I solved this problem by creating a gem called [Fencepost](http://rubygems.org/gems/fencepost).
This gem solves the strong parameters problem in an elegant way, freeing you
from needless declarations in your controllers, and giving you access to those
declarations in an intuitive way. The following is from the README for the gem:
[on Github](https://github.com/scotthelm/fencepost)

## Installation

Add `fencepost` to your `Gemfile`

    gem 'fencepost'


## Configuration


    rails g fencepost_config

This creates a yaml map of your models in `config/fencepost.yml`.

You can re-run the initializer at any time. You will be asked if you want to
overwrite the existing config. "Y" will force an overwrite of the file, and you
will need to re-comment out any attributes you want to remove by default.

### Default Configuration

The yaml map is where you can edit the allowable attributes for your models. In
the 80/20 rule, this would be the %80. Removing attributes in the configuration
yaml lets you set reasonable defaults for strong parameter behavior.
This map is read one time during intialization and stored in the Fencepost
model graph (a class-level variable)

### Dev Mode

During the early stages of development where your code is in flux, you can set
`dev_mode=true` in `config/initializers/fencepost.rb`. dev mode will eager load
and read in all your models dynamically every time the class is instantiated.
(Ignoring the yaml in the initializer)

## Usage

The gem creates a `fencepost` method in your contollers. This returns a
`Fencepost` object that has read your models and given you access to strong
params for any ActiveRecord model in your application.

### Simplest Case

    # app/controllers/people_controller
    def create
      @person = Person.create(fencepost.person_params)
    end

### Simple allow / deny for top level model

In this example, the Person model allows height and weight by default, but does
NOT allow dob (date of birth). In this example we want to allow date of birth but
deny weight.

    # app/controllers/people_controller
    def create
      @person = Person.create(fencepost.allow(:dob).deny(:weight).person_params)
    end

### More complex allow / deny for nested models

In this example, the Person model has a collection of addresses. We want to
deny latitude and longitude from the acceptable attributes.

    # app/controllers/people_controller
    def create
      @person = Person.create(fencepost.deny(addresses_attributes: [:latitude, :longitude]).person_params)
    end

## TLDR

This gem creates strong parameter declarations based on you ActiveRecord models.
It makes this configuration available in all your controllers, and gives you
the flexibility to allow or deny attributes on the fly.

Please have a look at the gem and feel free to submit a pull request if you 
can make it better.

