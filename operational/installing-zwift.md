# Installing Zwift #

Installing zwift is a matter of only a few things:

* Generating an inventory file with groups the following minimum groups:
  * proxy
  * storage
  * management

* Overriding any default settings with custom ones in a vars file

* Running configure.yml against the specified hosts, with the vars file.

The inventory file has been pre-generated with the proper group
membership, passwords, etc.  A cluster configuration has been
generated as well.  This file should be in ~/cluster.yml.  Copy this
file to ~/ansible-zwift/local.

Edit the cluster.yml.

Change the `prefix` and `cluster_confirm` values to `labN`.  Change
the values marked "CHANGEME" with unique strings.

Once done, build the cluster:

~~~~
$ ansible-playbook configure.yml -l labN -e @local/cluster.yml
~~~~

This may take a while.  Once it is done, we can do the cluster prep tasks that need only be done once:

~~~~
$ ansible-playbook oneshot.yml -l labN -e @local/cluster.yml
~~~~

If all goes successfully, you can access the web UI at the https address of the proxy server.

