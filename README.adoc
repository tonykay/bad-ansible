== Overview

Bad Ansible is a deliberately poorly constructed ansible project designed to give students a basic project to cleanup, refactor, and improve. It represents a newcomer's, aka _newbie_, approach to hacking together a functional playbook to deploy a three tier app.

The entry point, `main.yml` contains multiple _good enough_ plays, and some redundant ones.

=== Goal of *Bad Ansible*

Set up an application that:

* Sets up a `frontend1` haproxy server which load balances requests to http://frontend1.<GUID>.example.opentlc.com.
** Requests to `frontend1` come in on port 80
** Load balance requests to servers in the group `apps`, listening on port 8080
* Set up one or more tomcat servers, the group `apps`, to:
** Serve the included HTML content from ` /usr/share/tomcat/webapps/ROOT/index.html`
* Install postgres on all servers in the group appdbs

*Nothing else matters* i.e. you can get rid of it!

=== Tips

* Make sure you have read the *Goals* section above and understand the desired architecture
* There are redundant plays within `main.yml`
* You should be able to run your solution multiple times, not just once
* You are free to restructure your solution in any way


NOTE: Any plays or tasks not related to the above are therefore redundant and can be removed.

TIP: Don't rename any of the ansible groups: `frontends`, `apps`, `appdbs` - later labs require these!



=== Usage

. Set up the environmental variable GUID to your GUID
+
[source,bash]
----
export GUID=abcd
----
. Edited your `ansible.cfg` depending if you are executing on `bastion` or remotely
* Pay attention to the value of `ssh_args`
. Configure your _chosen_ `ssh` config file
* Set the correct user
* Set the correct key(s)
* If necessary, e.g. executing from a remote *control node*, install the right key(s)
+
NOTE: You can easily test your chosen `ssh` config file e.g. `ssh -F ssh.cfg app1` etc
. Execute your main wrapping playbook
[source,bash]
----
 ansible-playbook main.yml
----

=== Structure

[source,bash]
----
.
├── README.adoc                       <1>
├── ansible.cfg                       <2>
├── cleanup.yml                       <3>
├── haproxy.cfg.j2                    <4>
├── hosts                             <5>
├── index.html.app1                   <6>
├── index.html.app2                   <6>
├── index.html.j2                     <6>
├── main.yml                          <7>
├── ssh-bastion.cfg                   <8>
└── ssh-laptop.cfg                    <9>
----

. This README file
. Simple ansible.cfg
. Convenience playbook that removes the 3tier app - use to retest your work
. A badly written jinja2 template for HAProxy - *HINTS*
** This should loop over the apps group
** Perhaps the Tomcat port should be a variable?
. inventory file - referenced from `ansible.cfg`
** Note the use of a lookup in line 2, if you prefer consider:
** Hard coding it `GUID=abcd` or passing it in via `-e GUID=abcd`.
. Too many index.html files?
. Main entry point
. `ssh` configuration file for working off a `bastion` host
. `ssh` configuration file for working off a laptop or remote *control node*


=== Potential strategies

* Divide and Conquer
. Break `main.yml` into individual plays
. Discard the redundant ones
. Wrap in a `main.yml` or similar using `import_playbooks:`

* Role Play
. Identify the key roles e.g. common, frontends, apps, database etc
. Convert the relevant plays into roles
. Create wrapping playbook with multiple *plays* invoking *roles* on *groups*
