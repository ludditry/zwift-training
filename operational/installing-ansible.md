# Installing Ansible #

Ansible is a reasonably new project that is still moving fairly
quickly.  As a consequence, ansible playbooks are sometimes
incompatible with newest versions of Ansible.

Our playbooks are compatible with Ansible 1.5 - 1.5.3.  The recently
released 1.5.4 doesn't work with our playbooks, and will require some
re-writing to make them compatible.

In addition, we require the ipaddr module to do IP address lookups,
and the pyrax module to allow for spinning up test clusters on the
rackspace cloud.

In terms of pip requirements, they would be as follows:

~~~~
ansible==1.5.3
ipaddr
pyrax
~~~~

Since the requirements are fairly specific, and since most
distributions don't have ansible packages (or at least not packages at
the versions we require), it makes sense to install ansible in a
python virtual environment so as to better control specific versions.

In a production cluster, it might make sense to have a single admin
workstation that is used to do ansible management, particularly since
sensitive information (passwords, hash-prefixes, etc) must be stored
somewhere private and will have to be synchronized between all ansible
hosts.  Having only one host that runs ansible makes the
synchronization problem reasonably trivial.

## Downloading the Ansible playbooks ##

The ansible playbooks are in the `ludditry` github organization, in
the repo named `ansible-zwift`.  This repo is a public repo, and is
open to pull requests.  Install the git program, then clone the
ansible project from the ludditry organization:

~~~~
$ apt-get install git -y
$ git clone https://github.com/ludditry/ansible-zwift
~~~~

Once the ansible repository is created, we will make a virtual
environment in the checked out repository with the necessary python
prerequisites to run ansible.

## Creating a python virtual environment ##

Python virtual environments are self-contained python distributions
and dependancies necessary to run a single application with custom
python and python module versions.  For python applications that
require specific versions of packages that are not available as system
packages, this is a reasonably clean and self-contained way to install
a run-time environment for a python application.

This will walk through creating the necessary ansible virtual
environment.  This will be ubuntu/debian specific, though the package
installation can be generalized for other distros.

First, install the necessary system packages:

~~~~
$ apt-get install python-virtualenv python-pip -y
~~~~

Once these packages are installed, then a virtual environment can be
created:

~~~~
$ cd ~/ansible-zwift
$ mkdir -p local
$ virtualenv ~/local/.venv
~~~~

This will create a directory in `local/.venv` with a self-contained
python distribution.  To set the necessary environment settings and
path settings to ensure that python programs run using the python
version and modules in that virtual environment, the virtual
environment must be activated.

## Activating the virtual environment ##

You can activate the virtual environment by sourcing the `activate`
script in the bin directory of the virtual environment:

~~~~
$ . ~/ansible-zwift/local/.venv/bin/activate
~~~~


## Installing ansible ##

Once activated, you are using the python executable in the virtual
environment, and any python packages installed using the `pip` tool
are installed into the venv, rather than global to the local system.
We will use this characteristic to install ansible and the ansible
requirements into the virtual environment.  The prereqs for the
ansible cookbooks have been specified in `requirements.txt`, and pip
can install all the prereqs from that file:

~~~~
$ pip install -r requirement.txt
~~~~

Once pip stops grinding away, the virtual environment is ready for
use.  All binaries for the files have been added to the venv /bin
directory, and are on the path, so typing `ansible` from the command
line should generate an ansible usage description.

~~~~
$ ansible

Usage: ansible <host-pattern> [options]

Options:
  -a MODULE_ARGS, --args=MODULE_ARGS
                        module arguments
<snip>
~~~~








