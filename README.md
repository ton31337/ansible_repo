# ansible_repo
ansible_repo for testing ansible using test kitchen and ansiblespec.

This demostrates using test-kitchen, ansible and busser-ansiblespec to build an verify a tomcat server.
Everything is done via ssh from the Ansible/Serverspec server so nothing is install on the tomcat server apart from java and Tomcat.
In this demonstration both servers are centos 7 running under virtual box on your workstation, however the Tomcat server
could be anywhere like Amazon EC2, or a Docker Container.
You can take an image of the server after it is build and no comfiguration software is install on the Tomcat Server.


```
     TEST KITCHEN              ANSIBLE AND SERVERSPEC                TOMCAT SERVER
     WORKSTATION               SERVER (built and destroyed      (created separately
     (or Jenkins CI)           automatically)                   could be docker container
                             +----------------------------+     or multiple servers and roles)
+-------------------+        |                            |      +-----------------------+
|   test kitchen    |        |                            |      |                       |
|   kitchen-ansible | create |                            |      |                       |
|                   | ser^er |                            |      |      +-----------+    |
|     CREATE    +------------>               +----------+ |      |      | tomcat    |    |
|                   |        |               |          | | install     |           |    |
|                   | install and run        | ansible  +--------------->           |    |
|     CONVERGE  +------------+--------------->          | | tomcat      +-----------+    |
|                   |        |               +----------+ |      |                       |
|                   | install|  +----------+  +---------+ |   test                       |
|     VERIFY    +--------------->busser-   |-->serverspec--------+---->                  |
|                   |and run |  |ansiblespec  |         | |      |                       |
|                   |        |  +----------+  +---------+ |      +-----------------------+
|     DESTROY   +------------>                            |
+-------------------+ delete +----------------------------+
                      server

                   * All connections over SSH

```

## FIRST STEP: Workstation Software Installation

The first thing you need to do is install the test-kitchen environment on your workstation.
A useful link is: http://misheska.com/blog/2013/12/26/set-up-a-sane-ruby-cookbook-authoring-environment-for-chef/

The follow instructions are is for Windows PC (it will be similar for Mac):

1. Download and install virtualbox from https://www.virtualbox.org/wiki/Downloads.
2. Download and install Vagrant from https://www.vagrantup.com/downloads.html
3. Download and install the Windows RubyInstaller for 32 bit Ruby 2.1 from http://rubyinstaller.org/downloads.
   * Check the option to add ruby to your path.
4. Download and install the Windows Ruby DevKit for use with Ruby 2.0 and above (32bits version only) from http://rubyinstaller.org/downloads.
5. Configure the Ruby DevKit
   * In the devkit directory run �ruby dk.rb init�.
   * Check the config.yml generated has added the the path of the ruby install, if not add it manaully.
   * run �ruby dk.rb install� to bind it to the ruby installation.
6. Then install the following gems
  * gem install librarian-ansible
  * gem install test-kitchen
  * gem install kitchen-ansible
  * gem install kitchen-vagrant
7. git clone this repository and in a command window in the ansible_repo directory run command
```
kitchen list
```
This will return a list if everyting is correctly installed.

## SECOND STEP: Create Servers in Virtual Box on your Workstation.

1. review the .kitchen.yml file, specifying IP address that are part of your workstation private address space or
use DHCP to let the network dynamically allocte IP addresses.

2. To bring servers up using DHCP on your workstation run
```
kitchen create ansible-centos-7 -l debug
kitchen create tomcat-centos-7 -l debug
```
2. So ansible can access the server get the �private_key� file of the tomcat servers from directory
  ansible_repo\.kitchen\kitchen-vagrant\kitchen-ansible_repo-tomcat-centos-7\.vagrant\machines\default\virtualbox\private_key
and copy to
  ansible_repo\spec\tomcat_private_key.pem
3. Update the hosts file with the  IP address of the tomcat server.

## THIRD STEP: Build the tomcat server.
```
kitchen converge ansible-centos-7 -l debug
```

## FOURTH STEP: Verify the tomcat server.
```
kitchen verify ansible-centos-7 -l debug
```

##




