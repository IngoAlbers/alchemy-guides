Getting Started
---------------

This guide covers getting up and running with Alchemy CMS. After reading
it, you should be familiar with:

-   Installing and creating a new application with Alchemy CMS
-   Creating a user and logging in for the first time.

endprologue.

### Prerequisites

This guide is designed for beginners who want to get started with
Alchemy CMS from scratch. It does not assume that you have any prior
experience with Alchemy CMS. However, to get the most out of it, you
need to have some prerequisites installed:

-   The [Ruby](http://www.ruby-lang.org/en/downloads) programming
    language
-   At least one of these databases:
    [MySQL](http://www.mysql.com/downloads/mysql),
    [PostgreSQL](http://www.postgresql.org/download/) or
    [SQLite](http://www.sqlite.org/download.html)
-   The image processor
    [ImageMagick](http://www.imagemagick.org/script/install-source.php)

INFO: Alchemy CMS needs at least version 6.4 of ImageMagick

#### Installing dependencies

**It is highly recommended** to use a package manager to install
dependencies like ImageMagick and your database.

Common package managers are:

-   [Homebrew](http://brew.sh) on the Mac
-   Your Linux box already has a package manager installed and you
    likely are already familiar with it.

#### Installing Ruby

The most common ways to install Ruby on your computer nowadays are:

-   If you are on a Mac or Linux Box use [RVM](http://rvm.io)
-   If you are on Windows use
    [RailsInstaller](http://railsinstaller.org)

#### Learning Rails

It is highly recommended to familiarize yourself with Ruby on Rails
before using Alchemy CMS.\
You will understand much more and see things clearer if you know about
the basics of Ruby on Rails.

There are many excellent resources on the internet for learning Ruby on
Rails, including:

-   [Try Ruby](http://tryruby.org)
-   [Getting Started with
    Rails](http://guides.rubyonrails.org/getting_started.html)
-   [Rails for Zombies](http://railsforzombies.org)
-   [Railscasts](http://railscasts.com)

### Installating Alchemy

#### As a stand alone project

The installation of Alchemy CMS is very easy.\
You just need to run the gem command.

<shell>\
gem install alchemy\_cms\
</shell>

The gem provides an executable file that can automagically create new
rails applications with a pre-configured Alchemy CMS engine.\
While the installation process you will get asked about your local
development environment. Just follow the instructions.

<shell>\
alchemy new YOUR\_APP\_NAME\
</shell>

The following parameters are available for the installer:

-   <code>—database=sqlite3</code>
-   <code>—scm=git/svn</code>

INFO: The installer creates a database, migrates the tables and also
seeds them. After finishing, your application is ready to start!

#### Install into an existing Rails application

If you already have an existing Rails application, you can require the
Alchemy CMS gem in your app.

Just add this line to your Gemfile.

<ruby>\
gem ‘alchemy\_cms’, ‘\~\> 3.0.0’\
</ruby>

Since Alchemy CMS is a mountable engine, you need to define the
mountpoint in your <code>config/routes.rb</code> file.

<ruby>\
MyApp::Application.routes.draw do\
 mount Alchemy::Engine =\> ‘/’\
end\
</ruby>

INFO: You can mount Alchemy on every route you want. <code>pages</code>,
<code>cms</code>, what ever you want. Most of the time you go with the
root route.

NOTE: Please be aware that Alchemy has a very strong catch all route. It
is **highly recommended** that you mount Alchemy at last position in
your app, so your existing routes are still available.

Now you need to run bundler for installing the dependencies.\
<shell>\
bundle install\
</shell>

Then run the install task.

<shell>\
bundle exec rake alchemy:install\
</shell>

##### Authentication

If you already have your own user class in your application, you have to
tell Alchemy about it. Please follow [this
guide](custom_authentication.html) to learn how to achieve that.

If you don’t have a authentication solution, you can use the
alchemy-devise gem.

Just add it to your apps Gemfile

<ruby>

1.  Gemfile\
    gem ‘alchemy-devise’, github: ‘magiclabs/alchemy-devise’, branch:
    ‘2.0-stable’\
    </ruby>

<shell>\
bundle install\
</shell>

and run the installer

<shell>\
bin/rake alchemy\_devise:install:migrations db:migrate\
</shell>

### Running Alchemy CMS!

Now that you have Alchemy CMS successfully installed, let’s move on by
creating your first user with administrative privilegs.

You just need to start a local ruby server on your development machine.

<shell>\
cd YOUR\_APP\_NAME\
</shell>

<shell>\
bin/rails s\
</shell>

Open a browser window and navigate to <http://localhost:3000>.\
You will be greeted with a screen that is prompting you to create the
first user.

<b>Congratulations, you can now access the backend!</b>

### Next steps

As next you should to read about:

-   [Alchemy’s architecture](architecture.html)
-   [The Standard Set](standard_set.html)
-   Customizing [page layouts](create_page_layouts.html) and
    [elements](elements.html).

 
