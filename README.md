Sakai-Scripts
=============

Some scripts that I use to make my life as a Sakai developer easier.

This is a set of scripts to set up a developer instance of Sakai on 
your system or set up a nighly build.  Sakai can run on a 2GB RAM system
but is a lot more comfortable with a 4GB or more app server.

Getting Started on Mac (as needed)
==================================

* Make sure you have Java-8 Installed

        java -version
        javac -version

* Create an account on github

* Go to https://github.com/sakaiproject/sakai and "fork" a copy into 
your github account

* Install git using the command

        xcode-select --install

* Set up git with the folowing commands

        git config --global user.name "John Doe"
        git config --global user.email johndoe@example.com

* Check out sakai-scripts into some folder.   I tend to make a folder
named "dev" in my home directory:

        mkdir /home/csev/dev  (if needed)
        cd /home/csev/dev
        git clone https://github.com/csev/sakai-scripts

* Read the instructions in profile.txt to update your login files.
Once you update your login files, close your terminal window and 
reopen a brand new window and type:

        echo $JAVA_OPTS

If you see the settings, you have edited the correct file, if not try
another of the files.  Keep repeating this process of editing the log in file,
opening a new terminal, and typing 'echo' until the above command shows
the java settings.

* Download the platform independent MYSQL JDBC Connector from:

        http://dev.mysql.com/downloads/connector/j/

    You will need to create or use your Oracle account.  Download the
    ZIP version of the file and unzip the file and move the unzipped 
    folder into:

        /home/csev/dev/mysql-connector-java-5.1.35

    (Or a similar name)

* Install MAMP if you have not already done so

Getting Started on ubuntu
=========================

* Create an account on github

* Go to https://github.com/sakaiproject/sakai and "fork" a copy into 
your github account

* Install git using the command

        sudo apt-get -y install git

* Check out sakai-scripts into some folder.   I tend to make a folder
named "dev" in my home directory:

        mkdir /home/csev/dev  (if needed)
        cd /home/csev/dev
        git clone https://github.com/csev/sakai-scripts

* Set up git with the folowing commands

        git config --global user.name "John Doe"
        git config --global user.email johndoe@example.com

* Read the instructions in sakai-scripts/profile.txt to update 
your login files.  Once you update your login files, close 
your terminal window and reopen your window and type:

        echo $JAVA_OPTS

If you see the settings, you have edited the correct file, if not try
another of the files.

* Set up the rest of the pre-requisites using the following command:

        cd /home/csev/dev/sakai-scripts
        sh ubuntu.sh
    
    You may need to edit this file if you are running ubuntu before 14.10

Common Steps
============

* Copy config-dist.sh to config.sh
* Edit config.sh 
    * Change "sakaiproject" in the GIT REPO  varuable to be your github account
    * If you are not running MAMP, edit the MySQL root password

* Run bash db.sh to create a database
* Run bash na.sh to set up the Tomcat
* Run bash co.sh to check things out
* Run bash qmv.sh to compile it all
* Run bash start.sh to start the Tomcat
* Run bash tail.sh to watch the logs (Press CTRL-C to stop the tail)
* Navigate to http://localhost:8888/portal or http://localhost/portal depending
on the port where your Tomcat server is located
* Run bash stop.sh to shutdown Tomcat

* Copy smv.sh into a folder in your PATH and set execute permission
so you can recompile any sub-folder in Sakai that has a pom.xml 
by typing "smv.sh"

Eventually you can just use the tomcat startup.sh and shutdown.sh
and run your own tail commands if that is what you like.

Description of the Scripts
==========================

Here are the scripts to be run in roughly the following order:

ubuntu.sh
---------

This runs a bunch of apt-get commands to load the pre-requisites for Sakai
for Ubuntu.  If you are running something other than Ubuntu, you can look 
at this and use whatever package manager that is on the system to 
install the necessary pre-requisites.

config-dist.sh
--------------

This is a configuration script that sets a few parameters.  If you want/
need to tweak the parameters simply copy this to config.sh so git does
not try to check it in.  If there is a config.sh, it will be used instead
of config-dist.sh.   

Generally you need to change the git repo to be checked out and the
root mysql password for non-MAMP MySQL connections.

But there is a lot of flexibility here if you are setting up nightly build
servers.   Changing things like the HTTP port, shutdown port, and location 
of the Tomcat logs in config.sh allows you check these scripts out 
into several directories and make different nightly builds.  If you get
really tricky, you can override sakai-dist.properties by copying it to 
sakai.properties (which git will ignore) and change lots of things like
switching from MySql to Oracle.  Also you could put the ojdbc jar file
in the jars folder so the db.sh copies that into common/lib each time 
you make a new Apache.  Note that db.sh does not understand oracle so 
your might want to change MYSQL\_COMMAND to be "cat" so it does not try
to run mysql to create an Oracle database.

db.sh
-----
This sets up a MySql database with an account and password based on 
the configuration

co.sh
-----

Checkout all of the Sakai source into the folder trunk.   This checks out
the main Sakai source and a few projects that Sakai depends on in case you
want to peruse the source of those projects.

db.sh
-----
Drop the database if it is there and re-create an empty database.

na.sh
-----

Download and create a fresh instance of Tomcat, patch configurations, 
and set up a sakai.properties

qmv.sh
------

Compile all of the Sakai source bypassing unit tests.   This saves a lot
of time in the initial compile.   The mv.sh will compile with unit test.
Once things work and you need to go out for an errand, you can run a mv.sh 
to compile with all unit tests.

start.sh
--------
Start tomcat.  Actually this script calls stop.sh at the begining to 
shut down any running tomcat on the configured port so it can aso be 
used as a "restart" to bounce Tomcat.

stop.sh
-------
Stop tomcat

tail.sh
-------
Tail the tomcat log.

Using Sakai
===========

And at this point, you should be able to navigate a browser to:

    http://localhost:8080/portal

If you need to recompile, the steps to shut down Sakai are to first
abort your tail command with a CTRL-C and then run stop.sh

Usually to keep things clean, I remove all the files in the Tomcat logs
folder before I restart Sakai.

Developer Compiles
==================

smv.sh
------

This is for developers working on one or more of the elements of 
Sakai.  This can be run deep in the code tree at any maven node
that can compile (i.e. has a pom.xml).   You can actually put this in your
~/bin path and then just type ./smv.sh to do a re-compile into 
your Tomcat.  

A surprising amount of Sakai development can be done without
rebooting your Sakai.  If you are working on tool code, generally you can 
recompile without a reboot.  Changes to APIs in shared or components/services
generally require a Tomcat bounce.


Setting up Nightly Builds
=========================

There is a lot of flexibility inherent in creating config.sh and/or
sakai.properties that lets you set up a bunch of nightlies running 
on the same server and then having crontab entries to run things 
at whatever period(s) you desire.  You can set up different databases, 
different TCP ports, log to some Apache web space, even use Oracle
instead of MySql with some effort.  The script nightly.sh is pretty 
much ready to handle the job once the configuration is in place.

You can test all the scripts separately as you are configuring things
and then nightly.sh knits them together.

nightly.sh
----------

This script just calls the other scripts in the right order.  The script
does a checkout, shuts down any tomcat (killing if nexeccary), creates a new 
empty database, creates a fresh tomcat, checks out the code you want to compile,
compiles the code and starts Tomcat.   Since the script stops and starts the
right Tomcat automatically, you can run this script over and over interactively
in a cron job. 






