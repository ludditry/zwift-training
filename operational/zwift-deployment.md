% Zwift Deployment
% William Kelly and Ron Pedde
% April 2014


# Agenda #

## Goals ##

At the end of the session, we're hoping that you will be able to:

* Use ansible proficiently

* Deploy a zwift cluster on Rackspace public cloud or physical
  infrastructure using the
  [ansible-zwift](http://www.github.com/ludditry/ansible-zwift)
  configuration tool.

* Operate a swift cluster effectively with a good understanding of
  avaiexercisele operational tools.


# Zwift #

* [ZeroVM](http://www.zerovm.org) allows users to run abitrary code
  safely using
  [SFI](http://research.google.com/pubs/archive/34913.pdf) from
  google's native client.

* [Swift](http://swift.openstack.org) is an object storage system
  used by Rackspace's cloud files and similar to Amazon's S3.

* Zwift combines Swift and Zerovm. Now we can run user provided
  arbitrary code directly on the object servers using a REST api!


# Ansible #

* [Ansible](http://www.ansible.com) is a configuration management
  system.

* Agentless (remote configuration via ssh)

* [YAML](http://www.yaml.org/) based configuration language with
  [jinja2](http://jinja.pocoo.org/docs/) templating.

* Servers are assigned composable roles along with role specific
  variables to describe their ideal configuration.


# Exercise 1: Installing Ansible

[Installing Ansible]($PLACEHOLDER/installing-ansible.md)

This exercise explains how to install and configure ansible.


# Ansible Terminology #

* _Modules_ provide the core functionality of ansible -- they perform
  actions based on yaml configuration.  The core
  [list of ansible modules](http://docs.ansible.com/list_of_all_modules.html)
  is avaiexercisele on the ansible website.

* _Roles_ are user created.  They are a collection of yaml files that
  describe how to configure a server to perform a particular function.

* _Variables_ can be specified in roles, through ansible's automatic
  discovery mechanism, in the inventory as global defaults or against
  a specific host, in playbooks, or at the command line at runtime,
  and the value of a variable specified in multiple places is resolved
  in the listed order, with the command line winning..

* _Playbooks_ contain specific descriptions of actions to take to
  accomplish a particular task (configure a zwift cluster, for
  example).


# Ansible directory layout #

* The _inventory_ directory contains information on the devices that
  ansible will be configuring.

* The aptly named _roles_ directory contains roles.

* The _library_ directory contains extra ansible modules to enable
  additional functionality.


# Exercise 2: Getting Familiar with Ansible #

[Getting Familiar with Ansible](getting-familiar-with-ansible.md)

In this exercise, we add an inventory file to ansible and run some simple
ansible commands against the servers in the inventory file.


# Exercise 3: Installing Zwift #

[Installing Zwift](installing-zwift.md)

In this exercise, we install zwift.


# Mechanisms for using Zwift #

* [API extensions](https://github.com/zerovm/zerocloud/blob/icehouse/doc/Requests.md) to the swift api

* The [zwift-ui](https://github.com/zerovm/zwift-ui) web user interface

* [Command line client](https://github.com/zerovm/python-zwiftclient)


# Methods of execution #

* Zerovm can run objects already uploaded to swift or be used as a
  standalone execution environment.

* [Servlet descriptors](https://github.com/zerovm/zerocloud/blob/icehouse/doc/Servlets.md)
  are used to indicate which objects should be invoked, which objects
  should be provided as input, and where the output of the job should
  go.

* Jobs can be initiated by posting a servlet description or a tarball
  that includes data as well as a servlet description.

* Applications can be registered for mime types to provide default
  "openers", at which point GET requests can be used against objects
  of that mime type, resulting in the execution of the provided
  handler.


# Exercise 4: Using zwift-ui #

[Using zwift-ui](using-zwift-ui.md)

In this exercise, we will log in to the zwift ui, load some sample data,
modify the manifests appropriately for your environment, and run some
things!
