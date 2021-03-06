Upgrading Alchemy CMS
---------------------

This guide describes how to upgrade Alchemy CMS to version 3.0.0 and
assumes that your current Alchemy version is v2.8.x.

-   You should always upgrade from the next prior Alchemy release (based
    on your current version).
-   This Alchemy CMS upgrade calls for upgrading Rails from v3.2 to
    v4.0.

endprologue.

Depending on your App, upgrading Rails to v4.0 might be a bigger
challenge. This guide covers the basics for upgrading Alchemy driven
Rails apps without any individual routes, controllers and models.

There is a very good
<a href="http://railscasts.com/episodes/415-upgrading-to-rails-4" target="_blank">screencast
by Ryan Bates that covers the Rails upgrade</a> a bit more detailled.
Thank you, Ryan!

Railsdiff shows diffs of Rails versions. There you can look up what
exactly changed.
<a href="http://railsdiff.org/html/v3.2.16-v4.0.2.html" target="_blank">(Diff
of v3.2.16 and 4.0.2.)</a>

### Update the dependencies

In order to update the dependencies, the <code>Gemfile</code> needs to
be changed first.

-   The grouping for the asset related gems is not necessary anymore.
    That means all the gems in the <code>assets</code> group can be
    moved to the global area now.
-   Versions of <code>rails</code>, <code>sass-rails</code>,
    <code>coffee-rails</code> and <code>uglifier</code> needs to be
    raised.
-   The complete authentication part is extracted from the Alchemy core,
    so the <code>alchemy-devise</code> gem is necessary now.

That part of your updated Gemfile should look like this:

<ruby>\
…\
gem ‘rails’, ‘\~\> 4.0.2’\
gem ‘sass-rails’, ‘\~\> 4.0.0’\
gem ‘coffee-rails’, ‘\~\> 4.0.0’\
gem ‘uglifier’, ‘\>= 1.3.0’

gem ‘alchemy\_cms’, github: ‘magiclabs/alchemy\_cms’, branch:
‘3.0-stable’\
gem ‘alchemy-devise’, github: ‘magiclabs/alchemy-devise’, branch:
‘2.0-stable’\
…\
</ruby>

You should now update the dependencies via bundler.

<shell>\
bundle update\
</shell>

### Rails 4 Update

<code>bundle exec rake rails:update</code>

This task guides you through all changes and asks what you want to do.

NOTE: You should not change <code>config/routes.rb</code> by the task.
Also your locale yml files in <code>config/locales/</code> should not be
overwritten if you have individual content in there.

The other files can mostly be used as suggested by the update task.

But please **always double** check that you dont overwrite your
individual adjustments in any of the files.

#### Secret token handling

In <code>config/initializers/secret\_token.rb</code> the secret token of
your application is stored.

With Rails 4 the <code>secret\_token</code> is now
<code>secret\_key\_base</code>. It now encrypts all the contents of
cookie based sessions.

Both configurations can exists together, so the
<code>secret\_key\_base</code> can detach the secret\_token gradually.\
Depending on your App, you can also remove the
<code>secret\_token</code> immediately if the existing cookies of your
users are unimportant.

More information here:
<a href="http://edgeguides.rubyonrails.org/upgrading_ruby_on_rails.html#action-pack" target="_blank">http://edgeguides.rubyonrails.org/upgrading\_ruby\_on\_rails.html\#action-pack</a>

<ruby>\
Myapp::Application.config.secret\_token = ‘existing secret token’\
Myapp::Application.config.secret\_key\_base = ‘new secret key base’\
</ruby>

### Installing alchemy-devise migrations

In order to update the user model, the migrations of the alchemy-devise
gem need to be copied to the App.

<shell>\
bin/rake alchemy\_devise:install:migrations\
</shell>

### Run the Alchemy Upgrader

Now you can run the upgrade task.\
While upgrading, you will get informations about the process on your
screen.

<shell>\
bin/rake alchemy:upgrade\
</shell>

### Changes for the Alchemy configuration file

The Upgrade task added an up-to-date <code>config.yml.defaults</code> to
the <code>config/alchemy</code> folder.

Alchemy v3.0 comes with some config changes that need to be slipped in
to the <code>config/alchemy/config.yml</code> file.

#### Remove the Devise module configuration

<yaml>

1.  === Devise modules\
    \#
2.  List of Devise modules that should be activated on the user model.\
    \#\
    devise\_modules:\
     - :database\_authenticatable\
     - :trackable\
     - :validatable\
     - :timeoutable\
     - :recoverable\
     \# - :encryptable \# Uncomment this if you are upgrading from an
    Alchemy version \< 2.5.0\
    </yaml>

#### Adjust the user roles

The old role <code>registered</code> is now <code>member</code>

<yaml>\
user\_roles: [member, author, editor, admin]\
</yaml>

#### Add the regular expression for verifying email addresses and urls

<yaml>\
format\_matchers:\
 email: !ruby/regexp ‘/\\A[^`\s]+`([^`\s]+\.)+[^`\\s]+\\z/’\
 url: !ruby/regexp
‘/\\A[a-z0-9]+([\\-\\.]{1}[a-z0-9]+)**\\.[a-z]{2,5}(:[0-9]{1,5})?(\\/.**)?\\z/ix’\
 link\_url: !ruby/regexp ‘/\^(mailto:|\\/|[a-z]+:\\/\\/)/’\
</yaml>

### Changes in Alchemy you should know about

Some parts in Alchemy have changed. You might need to update your code,
so please have a look at the following points.

#### Ferret has been extracted

If you are using the ferret search feature with your Alchemy driven
application, you now need to require it as a gem in your
<code>Gemfile</code>.

<code>gem ‘alchemy-ferret’, github: ‘magiclabs/alchemy-ferret’, branch:
‘master’</code>

#### TinyMCE 4 Upgrade

The TinyMCE configuration syntax has changed.

If you have custom TinyMCE confugurations, like a customized toolbar
then you have to upgrade the syntax to a TinyMCE 4 compatible one.\
Please have a look in the default TinyMCE configuration from Alchemy and
also read the official TinyMCE documentation in how to upgrade.

-   Alchemy default TinyMCE config:
    <a href="https://github.com/magiclabs/alchemy_cms/blob/master/lib/alchemy/tinymce.rb#L5-L19" target="_blank">https://github.com/magiclabs/…/tinymce.rb\#L5-L19</a>

<!-- -->

-   Offical TinyMCE documentation:
    <a href="http://www.tinymce.com/wiki.php/Configuration" target="_blank">http://www.tinymce.com/wiki.php/Configuration</a>

#### Rendering Alchemy’s menubar

The way of rendering Alchemy’s menubar has changed (on your application
layout). Please replace the old helper call
<code>alchemy\_menu\_bar</code> with: <code>render
‘alchemy/menubar’</code>.

### Fix your capistrano deployment setup

Alchemy’s build in Capistrano tasks are compatible with Capistrano 2.0
only at the moment.

**Please update your Gemfile**

<ruby>\
 \# Gemfile\
 …\
 gem ‘capistrano’, ‘\~\> 2.15.5’\
 …\
</ruby>

If you use the capistrano tasks from Alchemy, you need to run
<code>bundle exec cap deploy:setup</code> to create
<code>cache/assets</code> in your shared folder, because Alchemy 3.0 now
symlinks <code>cache/assets</code> from the current release to the
shared folder.

Finished, yay!
