# ansible-demo
Ansible Playbook Repository

The goal is to deploy SAP Hana to one or more RHEL hosts in a fully automated
manner.


This demo shows how to utilize ansible roles from the Galaxy achieve this goal
fast and easy.


We do not want and usually do not need to invent the wheel ourselves time and
time again.  Instead, we expect someone has had this goal before and has
managed to automate that task using Ansible.

So our first step is to search for such pre-existing work. The most promising
place to start with is the Ansible Galaxy https://galaxy.ansible.com.

Searching for keyword SAP in the Ansible Galaxy reveals there is indeed a lot
of prior art available. I decide to use the saphana-deploy role for my purpose.

To simply use that role I need to install it on my management workstation via
'''ansible-galaxy install mk-ansible-roles.saphana-deploy'''


There is also a ReadMe attachted to the role that describes what I need to do
in order to facilitate it.

Additional information may be found in the original source which is hosted on
GitHub.  There is a button that links directly into that GitHub repo.


If I am not satisfied with the role as it is, I may simply clone that GitHub
repo and make my changes. I then may issue a pull request to the original
author and see if my changes are accepted upstream. If I have commit privileges
to that upstream project I may also create a branch to develop and test my
modified version. Later these changes can be merged into the master branch. Or
I create a fork to develop that existing role into something completely new...


Anyway, for the moment I am satisfied with the role as it is and just want to
use it for my deployment.


So I create two YML files, one for the host_vars and another for my playbook and proceed from there.



