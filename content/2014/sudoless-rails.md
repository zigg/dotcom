# sudoless Rails

- date: 2014-08-03 19:36
- tags: rails, sudo, bundler, homebrew, brewdo

----

I'm working through [chapter
1](http://www.railstutorial.org/book/beginning#cha-beginning) of
["The Ruby on Rails Tutorial"](http://www.railstutorial.org/book)
and I'm already blanching at the number of invocations of `sudo`
that are present. I'm thinking to myself, "surely there must be a
way to do this while globally installing as little software as
possible?" After all,

> sudo install is like using global variables to manage state.
>
> — [@trenton42](https://twitter.com/trenton42), via
> https://twitter.com/zigg/status/496002096085209088

Avoiding the global installation of software that's for a specific
project is not just an obsession of mine; it's very good practice,
too—after all, if *you* can confine your project's dependencies
into a specific area and produce your environment with a few simple
commands immediately after a `git clone`, so can anyone else who
pulls down your code.  Always erect that wall to avoid pulling in
undeclared dependencies—it will do you good in the long run.

With that in mind, here we go:

Install Bundler
----

Okay, *one* thing is outside our project directory:
[Bundler](http://bundler.io).  But that's a system tool, and while
it's *also* a Rails project dependency, it doesn't rise to the level
of a web framework.  We'll be using it similarly to `virtualenv`
on Python, which I also never hesitate to install globally because
it's the same software *every* project needs.

~~~~
$ brew install brew-gem
$ brew gem bundler
~~~~

**Pro tip/shameless plug:** Use [brewdo](/code/brewdo)!  Then you'll
be sandboxing your Homebrew-invoked software installations as well.

Create the project directory
----

~~~~
$ mkdir first_app
$ cd first_app
$ vim Gemfile
~~~~

For the `Gemfile` contents, consult [figure
1.5](http://www.railstutorial.org/book/beginning#code-gemfile_sqlite_version)
in the tutorial.  Obviously, as I learn what the heck I'm doing with
Rails, I expect to know what goes here for my own projects, but I
imagine at a minimum it specifies something like

~~~~ruby
source 'https://rubygems.org'
ruby '2.0.0'
#ruby-gemset=railstutorial_rails_4_0

gem 'rails', '4.0.8'
~~~~

Install and `rails new`
----

~~~~
$ bundle install --path vendor/bundle
$ bundle exec rails new .
~~~~

When prompted, *don't* overwrite your Gemfile.

~~~~
$ bundle exec rails server
~~~~

And viola!  I got through the entire chapter, including the Heroku deploy,
with this.  I'll come back and update if I find later that I've made a
horrible mistake.  Of course, if you need to manage Ruby versions as well
(I'm using the system Ruby 2.0.0 on OS X Mavericks), you'll need some
solution for that—RVM, rbenv, chruby, something.  I'm not yet
qualified to give a recommendation there, but stay tuned, maybe?

