Welcome to  Jenkins4Rails
=========================

This is a guide on how to run headless tests in jenkins server, including cucumber, jasmine/jasminerice test 

Objectives
----------

The main goal is to explain how to use jenkins to running headless tests for rails 3 apps. 

The environment will make use of Xvfb, Xvnc, nodejs, phantomjs.

Requirements
------------

* Linux (debian based is preferred)
* The following examples run on Ubuntu server 11.10 (64 bit)

Getting Started
---------------

* Install nodejs:

	`$ sudo apt-get install nodejs`

* Install phantomjs:

	* Follow this step to install: http://code.google.com/p/phantomjs/wiki/Installation

	* If the repo is outdated (ubuntu 11.10 for ex), follow steps here : http://yoodey.com/how-install-and-run-phantomjs-ubuntu-oneiric-1110

* Configure jenkins environment: 
	
	* Ensure you installed "Jenkins Xvfb plugin" and "Hudson Xvnc plugin"

	* After restart jenkins, go to "Configure System" and add a "Xvfb installation", namely "default"


* Configure jenkins job:
	
	* If the job requires cucumber or jasmine:ci tests, then check "Run Xvnc during build"
	
	* To run javascript tests that use "jasminerice" tests :

		* Check "Start Xvfb before the build, and shut it down after."

		* You need to install gem "guard-jasmine" (this gem use "phantomjs" headless webkit)
		
		* Remember add "jasminerice" gem to test environment

		* Add "guard-jasmine spec" to "Execute shell" script
