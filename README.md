Welcome to  Jenkins4Rails
=========================

This is a guide on how to setup your jenkins CI server to build and test rails 3 applications

Objectives
----------

The main goal is to explain how to use jenkins to test rails 3 apps. 

The environment will make use of RVM, rspec and capybara for acceptance tests.

Requirements
------------

* Linux (debian based is preferred)

Getting Started
---------------

* Install Jenkins:

	* Add the jenkins key to apt  
		`$ wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -`

	* Add jenkins apt repo to your /etc/apt/sources.list  
		`$ deb http://pkg.jenkins-ci.org/debian binary/`

	* Update your local package index and install jenkins:  
		`$ sudo apt-get update`  
		`$ sudo apt-get install jenkins`

* Configure jenkins environment:  

	* Change to jenkins user  
		`$ sudo -Hiu jenkins`  
		and go to jenkins home  
		`$ cd`

	* Install RVM:  
		`$ bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)`
		`$ source ~/.bash_profile` 
		`$ type -t rvm # should return "function"`
		`$ rvm requirements # check and install all missing requirements/lib`
		`$ sudo apt-get install build-essential openssl libreadline6 libreadline6-dev curl git-core 
					zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0 libsqlite3-dev 
					sqlite3 libmysqlclient-dev libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake 
					libtool bison subversion libcurl4-openssl-dev`
		`$ rvm install ree # 1.9.2, head or whatever`  
		`$ rvm use --default ree@global`  
		`$ gem install bundler`  
		`$ rvm gemset create name-of-your-project-gemset`  

	* Create the jenkins keys to access github  
		`$ ssh-keygen -t dsa -C "Jenkins key"`  
		`$ git config --global user.email "jenkins@your-domain.com"`  
		`$ git config --global user.name "jenkins"`  

	* Go to github and add the new key to your github project
	
	* Copy .bashrc file to jenkins home:  
		`$ mkdir tmp && cd tmp`  
		`$ git clone git@github.com:huyttq/jenkins4rails.git`  
		`$ cp jenkins4rails/.bashrc .`  

* Configure jenkins

	* go to [http://localhost:8080][localhost]  
		* go to Manage Jenkins -> Manage Plugins -> Available ([http://localhost:8080/pluginManager/available](http://localhost:8080/pluginManager/available "Available Plugins"))
		* Install [Jenkins GIT Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin)
		* Install [Hudson Xvnc plugin] - this will run UI testing (Capybara & Cucumber)
		* Install vnc4server with you admin account
 			<pre>
			sudo apt-get install vnc4server
			sudo -Hiu jenkins
			vncserver
			Enter a password, and verify it
			vncserver -kill :1
			</pre>

			For more detail: http://www.rapaul.com/2011/06/05/zero-to-headless-browser-tests-jenkins/#going-headless
		* Create a new jenkins job:  
			* In the jenkins home click on "New Job"
			* Give it a name and choose "Build a free-style software project"
			* In the next page under "Source Code Management" choose GIT
			* Enter the URL of your github project (i.e. git://github.com/gmonfort/jenkins4rails.git)
   			* Check "Run Xvnc during build" if you wish to run UI test
			* Choose a trigger under "Build Triggers" (i.e. periodically, * 1 * * * stands for daily)
			* Under "Build" select "Execute Shell" and put the following:  
				<pre>
				#!/bin/bash -x  
				source ~/.bashrc
				cd .
				rm -rf jenkins && mkdir jenkins
				rvm use 1.9.2@name-of-your-project-gemset  
				bundle install  
				rake db:migrate RAILS_ENV=test  
				SPEC_OPTS="--format html" rake spec > jenkins/rspec.html
				</pre>

			* Save your job and schedule a new build (click on "Build now")
			* Under "Build History" you'll see your new build running or scheduled to run, click on it,  
			  then click on "Console Output" to see the log


[localhost]: http://localhost:8080 "localhost port 8080"
<!-- vim: set ai ts=4 sts=4: -->
