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
  available operational tools.

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

* [Ansible](http://www.ansible.com) is an agentless configuration
  management system focused on simple remote configuration specified
  in yaml configuration files.

* [Installing Ansible]($PLACEHOLDER/installing-ansible.md
