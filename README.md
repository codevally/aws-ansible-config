# Configuration for Ansible

My AWS playbooks for [Ansible](http://www.ansible.com).

For a concise introduction to Ansible, [read this page](https://github.com/afroisalreadyinu/practical-ansible-intro) or [this screencast series](https://sysadmincasts.com/episodes/43-19-minutes-with-ansible-part-1-4).

## Set Up ##

To set up Ansible on a macOS workstation:

    make macos-controller
    cp ./ansible.cfg.example ./ansible.cfg

## Usage ##

Ansible provides three main commands:

* *ansible-playbook* - to execute all of an Ansible playbook on the specified systems
* *ansible* - to execute an individual shell command or Ansible module on the specified systems
* *ansible-vault* - to encrypt or decrypt any individual YAML file that Ansible uses.

Both *ansible-playbook* and *ansible* require you to specify the group of systems that the commands will run on, and use *-i* to specify the inventory file or directory that lists the specified systems. The *all* group is a built-in group that automatically includes all of the systems in the specified inventory.

    ansible GROUP OPTIONS

If a command fails on any system in the group, Ansible automatically create a *retry* file that lists all of the systems where the command failed.

For example, use the *-a* option to execute a shell command:

    ansible all -a /usr/bin/uptime

Use *-m* to execute an Ansible module:

    ansible all -m ping
    ansible all -m setup

The *ping* module checks that Ansible can connect to the remote system. The *setup* module returns information about the remote system.

To run a playbook:

    ansible-playbook my_playbook.yml

Add *--syntax-check* to test the Ansible playbook without running it:

    ansible-playbook --syntax-check my_playbook.yml

Add *--check* to simulate the effect without making changes to the target systems:

    ansible-playbook --check my_playbook.yml

If the playbook requires data from a file that has been encrypted with *ansible-vault*, add  *--ask-vault-pass*:

    ansible-playbook --ask-vault-pass my_playbook.yml

Enter the password for the encrypted files when prompted.

### Creating a New Server with Ansible ###

To create a new server on Amazon Web Services using Ansible and the EC2 API:

    make create-ec2

## Directory structure ##

For convenience, the Ansible playbooks are in the root of this project.

* ansible.cfg.example - Example configuration file for Ansible
* inventory/ - Dynamic inventory scripts
* roles/ - Custom roles used by the Ansible playbooks
* scripts/ - Utility scripts

## Passwords ##

You must specify the SHA512 hashed version of a user password when you set it through Ansible. By default, macOS does not generate the same hashes as Linux, so use this command to generate a valid hash:

    make password

Enter the password that you would like to use at the prompt.

Use the [Vault](http://docs.ansible.com/playbooks_vault.html) feature to encrypt any YAML file that stores password variables.

## Ubuntu 16.04 ##

Ubuntu 16.04 systems no longer include Python 2 by default. Ansible currently uses Python 2 to manage UNIX-based systems, and cannot use Python 3 instead.

The *bootstrap_ubuntu_ansible* playbook handles this by installing Python 2 using the *raw* method, so that it is available for you to run other Ansible commands and playbooks.

Alternatively, install the *python-minimal* package:

    apt update && apt install python-minimal

Regardless of how you install Python 2, you also need to add an *ansible_python_interpreter* setting to the entries for these systems in your inventory:

    ansible_python_interpreter=/usr/bin/python2.7


## Contact ##

Email:

<stuart@stuartellis.name>
