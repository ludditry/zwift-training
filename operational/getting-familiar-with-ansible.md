# Getting Familiar with Ansible #

Before doing anything interesting with Ansible, the first requirement
is to define the hosts we are going to be operating on.  In an
agent-based configuration management system, this would require
setting up a configuration management server and registering hosts
with the config management server.

Since Ansible is an agentless system with orchestration over ssh, we
merely need to specify the ip addresses and authentication info in an
inventory file.

For the clusters we will be spinning, this has already been done.

Copy the file named `~/local-labN` to your ansible inventory directory
(~/ansible-zwift/inventory).

## Ping check ##

First, let's walk through all the hosts and make sure we can
communicate with them.

~~~~
$ ansible labN -m ping
~~~~

This will walk through all the hosts in labN, and run a simple health
command.  The initial argument to ansible is a host list to run
against.  In this case, `labN`.  The -m argument takes a module name
(in this case, `ping`), and runs that module with the supplied
arguments (none) against the host list.

You can also specify host lists by wildcard:

~~~~
$ ansible 'labN-storage*' -m ping
~~~~

In the case of a hostlist of `labN`, ansible was matching a group
definition.  Look at the inventory file we copied into `inventory`.
This file has a group (ini-style section header) called `labN` that
includes multiple hosts.

In the second case, the host list was a file-type glob of hostnames.

We can do the same thing as the "globbed" command by using the ansible
group `storage`:

~~~~
$ ansible storage -m ping
~~~~

Or, we could use the intersection of two groups: `labN` and `storage`,
like so:

~~~~
$ ansible 'storage:&labN' -m ping
~~~~

So much for host lists.

## Vars ##

All hosts have a number of variables associated with them, as a series
of key-value pairs.  Any arbitrary key can be associated with a host,
but there are several well-known values associated with hosts that
bear mentioning.  Some you may have already observed in the inventory
file you copied.  A few of the most important well-known ansible
variables include:

* ansible_ssh_user - user to log into remote system as
* ansible_ssh_pass - password to use for that user
* ansible_ssh_host - ip to use as a ssh target

There are other well-known values that can be associated with a host
including sudo users and sudo passwords, managing servers over
double-ssh hops (ssh jump servers/bastions), etc.

Key based auth can also be used by omitting the ansible_ssh_pass.

These vars are use not only to control the behavior of ansible, but
also to used for the purposes of templating configuration files, or
controlling the behavior of ansible playbooks.  Consider the case of a
playbook to set up NTP.  A reasonable approach would be to do the
following:

* Install the system package corresponding to ntpd
* Drop a ntpd.conf pointing to pool.ntp.org
* Restart ntpd

This would be a trivial ansible playbook.  Slightly better would be to
specify the ntp server settings using a var.  Even better would be to
set default values of the var to be pool.ntp.org, so that that would
be the default server unless it was overriden by the person running
ansible.  This can be done with var inheritance.

In the example inventory files, we specified these variables on a
host-by-host basis, but it is possible to set variables on groups.
The most obvious group is the `all` group, the default group all
ansible-managed servers are a member of, but it might make sense to
apply other variables on a group-by-group basis rather than a
host-by-host basis.  If we grouped our physical machines by geographic
location, for example, we might assign different NTP hosts based on
our geographic group membership.

## Facts

Facts are vars that are applied to hosts based on physical
characteristics of the host.  Some examples might include the amount
of physical memory, or the number of procs, etc.  Facts automatically
get populated, and look no different than vars.

You can see the generated facts of a host by running the setup module
against it:

~~~~
$ ansible labN-management -m setup
~~~~

## Command module ##

Besides the `ping` module, There are many other ansible modules
available.  A complete list of the built-in modules can be found in
[the ansible documentation](http://docs.ansible.com/modules_by_category.html).

Let's try the [shell](http://docs.ansible.com/shell_module.html) module.

~~~~
$ ansible lab1 -m shell -a 'hostname'
~~~~

This uses the shell module to run the command `hostname` on the remote
machines.  If we put the command line in single quotes to escape bash
interpretation, we can run complex commands, as though with a shell:

~~~~
$ ansible lab1 -m shell -a 'ps auxw | grep python'
~~~~

## Doing an ansibly thing ##

We can run ansible modules one-by-one using the `ansible` command-line
program, but most sysadmin tasks require a series of steps, we'll get
more done faster by building a sequence of steps.  This sequence of
steps in ansible is called a playbook.

Let's make a playbook for setting the motd on an ubuntu machine.
Ubuntu uses some kind of crazy script generated motd system run out of
/etc/motd.d, but we can append things to the bottom of the generated
motd by editing /etc/motd.tail.  So let's make a playbook to drop a
motd.tail file.

We'll start with making a playbook file, which is just a yaml file.
Make a file named `motd.yml`:

~~~~
---
- name: make an awesome motd
  hosts: all
  tasks:
  - name: drop /etc/motd.tail
    template: src=motd.tail.j2 dest=/etc/motd.tail owner=root mode=0644
~~~~

Now, make a motd.tail.j2 file:

~~~~
###########################################################
# This is {{ansible_hostname}} ({{ansible_ssh_host}})
# Unauthorized access is prohibited.
###########################################################
~~~~

The way to read the yaml is probably something like this:

For all hosts in the built-in hostlist `all` (which includes all
ansible managed hosts), read the file `motd.tail.j2`, evaluate it with
the jinja2 templating system using the hosts collected vars, then copy
the resulting file to /etc/motd.tail, chown it to root, and chmod it
644.

Incidentally, we could have copied the file directly, using no
templating by using the `copy` module, rather than the `template`
module.

The double-brace format is the [jinja2](http://jinja.pocoo.org/docs/)
syntax for emitting a variable.  The `ansible_ssh_host` variable is a
variable we specified in the host inventory file, while the
`ansible_hostname` variable is a fact that was generated from the
remote machine configuration.  In both cases however, we access them
as a simple variable -- same namespace, no real difference.

Also, note that the full jinja syntax is available to files templated
using the `template` module, so the full array of jinja functions are
available, including loops, control structures, etc.

We can run this playbook using the ansible-playbook command:

~~~~
$ ansible-playbook motd.yml
~~~~

This will run across all the hosts and drop the /etc/motd.tail file.
SSH into one of the machines and verify that the /etc/motd.tail file
is present and that it alters the login motd.

Notice that ansible-playbook doesn't take an argument for what hosts
to run on.  This is because the hosts to run on is specified in the
.yml file on the `hosts:` line.  It is still possible to limit what
hosts it runs on however, using the `-l` command line argument:

~~~~
$ ansible-playbook -l storage motd.yml
~~~~

This will only run on the storage nodes.  This is the same thing as
specifying "hosts: all:&storage" in the playbook itself.

Another way to make the playbook limit itself might be to template the
hosts line itself:

~~~~
---
- name: make an awesome motd
  hosts: "{{motd_hosts}}"
  tasks:
  - name: drop /etc/motd.tail
    template: src=motd.tail.j2 dest=/etc/motd.tail owner=root mode=0644
~~~~

Now if you run it, it will run on the hostlist specified in the
variable "motd_hosts".  This might be specified as a group_variable
(in `inventory/group_vars/all.yml`, for example), or on the
command-line using the -e (extra vars) flag:

~~~~
$ ansible-playbook motd-yml -e 'motd_hosts=labN:&proxy'
~~~~

This will run only on the labN proxy hosts.

## Roles ##

Roles are a way to combine the tasks associated with doing something
with the static config files, template values, and default variables
all in one place.

In the case of our ansible config, we have many different roles, all
visible in the roles directory.  They include proxy server, object
server, haproxy, ntp server, ntp client, and others.  Each of these
roles are configuration instructions to configure a particular
software application and related configuration, with no dependancy on
any other role.  Each role is an atomic unit, and can be arbitrarily
composed on any host.

Given that these roles can be applied on any host, a top level
playbook assembles the roles onto hosts.  In the case of our ansible
setup, that top level playbook is called `configure.yml`.  It composes
the roles onto the hosts in a moderately opinionated manner.

