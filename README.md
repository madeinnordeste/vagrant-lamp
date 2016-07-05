Vagrant LAMP
============
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/r8/vagrant-lamp?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

My default LAMP development stack configuration for Vagrant.

Requrements
-----------

* Virtualbox
* Vagrant >= 1.7.0
* vagrant-omnibus plugin
* vagrant-hostmanager plugin

Installation:
-------------

Download and install [VirtualBox](http://www.virtualbox.org/)

Download and install [vagrant](http://vagrantup.com/)

Install [vagrant-omnibus](https://github.com/chef/vagrant-omnibus) plugin

    $ vagrant plugin install vagrant-omnibus

Install [vagrant-hostmanager](https://github.com/devopsgroup-io/vagrant-hostmanager) plugin

    $ vagrant plugin install vagrant-hostmanager

Clone this repository

Go to the repository folder and launch the box

    $ cd [repo]
    $ vagrant up
    
To update **/etc/hosts** in host machine

	$ vagrant hostmanager
	
This machine URL:

	http://local.dev	

What's inside:
--------------

Installed software:

* Apache
* MySQL
* php
* phpMyAdmin
* Xdebug with Webgrind
* zsh
* git, subversion
* mc, vim, screen, tmux, curl
* [MailHog](http://github.com/mailhog/MailHog)
* [Composer](http://getcomposer.org/)
* Phing
* Wordpress utils:
    * [WP-Cli](http://wp-cli.org/)
    * [wp2github.py](http://github.com/r8/wp2github.py)
* Magento utils:
    * [n98-magerun](https://github.com/netz98/n98-magerun)
    * [modman](https://github.com/colinmollenhour/modman)
    * [modgit](https://github.com/jreinke/modgit)
* Node.js with following packages:
    * [CoffeeScript](http://coffeescript.org)
    * [Grunt](http://gruntjs.com/)
    * [Bower](http://bower.io)
    * [Yeoman](http://yeoman.io)
    * [LESS](http://lesscss.org)
    * [CSS Lint](http://csslint.net)

Notes
-----

### Apache virtual hosts

You can add virtual hosts to apache by adding a file to the `data_bags/sites`
directory. The docroot of the new virtual host will be a directory within the
`public/` folder matching the `host` you specified. Alternately you may specify
a docroot explicitly by adding a `docroot` key in the json file.

### MySQL

The guests local 3306 port is available on the host at port 33066. It is also available on every domain. Logging in can be done with username=root, password=vagrant.


##### MySQL Remote Access

The root account's localhost-only in the vast majority of default installations, are you certain you've allowed it to log in from the other system? From the MySQL reference manual:

it means that there is no row in the user table with a Host value that matches the client host
So, there's no % or 10.0.2.2 in the Host column at all. Check your current config:

	select user,host from mysql.user where user='root';

You likely want to create a new root entry with the same password as you have now.

	create user 'root'@'10.0.2.2' identified by 'yourpassword';
	grant all privileges on *.* to 'root'@'10.0.2.2' with grant option;
	flush privileges;


Source: [Access to mysql server via virtualbox](http://serverfault.com/questions/486710/access-to-mysql-server-via-virtualbox)

### phpMyAdmin

phpMyAdmin is available on every domain. For example:

    http://local.dev/phpmyadmin

### XDebug and webgrind



XDebug is configured to connect back to your host machine on port 9000 when
starting a debug session from a browser running on your host. A debug session is
started by appending GET variable XDEBUG_SESSION_START to the URL (if you use an
integrated debugger like Eclipse PDT, it will do this for you).

XDebug is also configured to generate cachegrind profile output on demand by
adding GET variable XDEBUG_PROFILE to your URL. For example:

    http://local.dev/index.php?XDEBUG_PROFILE

Webgrind is available on each domain. For example:

    http://local.dev/webgrind

It looks for cachegrind files in the `/tmp` directory, where xdebug leaves them.

**Note:** xdebug uses the default value for xdebug.profiler_output_name, which
means the output filename only includes the process ID as a unique part. This
was done to prevent a real need to clean out cachgrind files. If you wish to
configure xdebug to always generate profiler output
(`xdebug.profiler_enable = 1`), you *will* need to change this setting to
something like

    xdebug.profiler_output_name = cachegrind.out.%t.%p

so your call to webgrind will not overwrite the file for the process that
happens to serve webgrind.

### MailHog

ll emails sent via local mail transport are intercepted by [MailHog](http://github.com/mailhog/MailHog). So normally no email would be delivered outside of the virtual machine. Instead you can check messages using web frontend for MailHog, which is running on port 8025 and also available on every domain:

    http://local.dev:8025

### Composer

Composer binary is installed globally (to `/usr/local/bin`), so you can simply call `composer` from any directory.


### Host
Add **local.dev** in /etc/hosts

	sudo echo "192.168.33.10   local.dev" >> /etc/hosts
	

### PHPUnit

[Oficial Website](https://phpunit.de/manual/current/pt_br/installation.html)

[PHPUnit phar Versions](https://phar.phpunit.de/)


#### Colors to bash_profile terminal

Add below content to **~/.bash_profile** in **/home/vagrant/**

/home/vagrant/.bash_profile

	# terminal colors
	export GREP_OPTIONS="--color=auto"
	export GREP_COLOR="4;33"
	#export CLICOLOR="auto"
	export CLICOLOR=1
	export LSCOLORS=ExFxCxDxBxegedabagacad
	export PATH="/usr/local/bin:$PATH"
	export PS1='\n\033[0;37m\][\t]\[\033[0;32m\][\u]\[\033[31m\][\h]`git branch 2>/dev/null | grep \* | head -1 | sed "s/\* //g" | awk "{ print \"[ \"\\\$1 \" ]\" }"` \[\033[1;33m\]\w\a\[\033[0m\] \n\033[0;37m\]➥\[\033[0;32m\] '

	
