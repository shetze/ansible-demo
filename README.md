# ansible-demo

The goal is to deploy SAP Hana to one or more RHEL hosts in a fully automated
manner.


This demo shows how to utilize ansible roles from the Galaxy achieve this goal
fast and easy.


## Use Case 1: Create A New Template

### nanos gigantum humeris insidentes

We do not want and usually do not need to invent the wheel ourselves time and
time again.  Instead, we expect someone has had this goal before and has
managed to automate that task using Ansible.

So our first step is to search for such pre-existing work. The most promising
place to start with is the Ansible Galaxy https://galaxy.ansible.com.

Searching for keyword SAP in the Ansible Galaxy reveals there is indeed a lot
of prior art available. I decide to use the saphana-deploy role for my purpose.

To simply use that role I need to install it on my management workstation via
```ansible-galaxy install mk-ansible-roles.saphana-deploy```

There is also a ReadMe attached to the role that describes what I need to do
in order to facilitate it.

Additional information may be found in the original source which is hosted on
GitHub.  There is a button that links directly into that
[saphana-deploy GitHub repo](https://github.com/mk-ansible-roles/saphana-deploy).

If I am not satisfied with the role as it is, I may simply clone that GitHub
repo and make my changes. I then may issue a pull request to the original
author and see if my changes are accepted upstream. If I have commit privileges
to that upstream project I may also create a branch to develop and test my
modified version. Later these changes can be merged into the master branch. Or
I create a fork to develop that existing role into something completely new...

But wait, in most cases, the application developers need to deploy their new
stuff frequently and they usually do that in a fully automated fashion. Chances
are, that there exist well tested and maintained Ansible playbooks stored
alongide with the original code for the application somewhere in the
development department. This is how Development and Operations grow closer,
whether or not you like to call it DevOps.

### Stitching the Pieces Together

Anyway, for the moment I am satisfied with the role as it is and just want to
use it for my deployment.

In fact, additionally to the saphana-deploy role I decide to use four more roles
to design a modular deployment workflow with a high degree of re-usability: [disk-init](https://github.com/mk-ansible-roles/disk-init), [subscribe-rhn](https://github.com/mk-ansible-roles/subscribe-rhn), [saphana-preconfigure](https://github.com/mk-ansible-roles/saphana-preconfigure) and the OS role timesync as provided by Red Hat.

So I create four YML files, one for the group_vars and the another for my playbooks
that make up my phased deployment process:

* First I want to check if all the targets are up and running using an liveliness test
* Second I need to tune the OS and create a file system suiting the application
* Finally I install the application with all its dependencies


After committing and pushing the new content I create a new Project in Ansible
Tower that references my GIT repo. I use the **Update Revision on Launch**
option to make shure I always have the most current version of my playbooks and
group_vars applied.

Next I create new Run type Job Templates in Ansible Tower to get access to my just created playbooks:

* **Liveliness Check** checks if the target is alive using 'check_alive.yml'
* **Prepare RHEL**  performs the basic OS level steps to prepare the HANA installation using 'prepare_system.yml'
  This Template needs to have Privilege Escalation enabled.
* **App Deployment** actually deploys the HANA DB using **deploy_hana.yml**
  This Template also need Privilege Escalation to be enabled.

Finally I create a Workflow Template **Roll That Thing** that combines the
above declared Job Templates into one Workflow to Rollout the HANA Database.

For that Workflow Template I add Survey to allow the end user to adjust certain parameters upon HANA creation:

* **HANA SID** is a 3 alnum text string to identify the instance (defaults to **HXE**)
* **HANA Instance Number** is a 2 digit text (defaults to **75**)

I enable Concurrent Jobs for that Workflow and start the App Deployment only
after successful RHEL preparation. The Liveliness Check is performed three
times in a row in parallel to the other tasks for demoing purposes.

## Use Case 2: End User Perspective

In order to demonstrate End User Perspective in Ansible Tower, I create a new Team **class0815** with permissions to execute the **Roll Out That Thing** Template.

A new user is created and assigned to that team as a member.

When logging in as that user, only the one template available to that Team is
visible. Execution starts the otherwise unvisible Templates and uses the hidden
credentials to perform the task.

