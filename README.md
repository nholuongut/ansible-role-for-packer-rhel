# Ansible Role: Packer RHEL/CentOS Configuration for Vagrant VirtualBox

![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>

This role configures RHEL/CentOS (either minimal or full install) in preparation for it to be packaged as part of a .box file for Vagrant/VirtualBox deployment using [Packer](http://www.packer.io/).

The role may be made more flexible in the future, so it could work with other Linux flavors and/or other Packer builders besides VirtualBox, but I'm currently only focused on VirtualBox, since the main use case right now is developer VMs.

## Requirements

Prior to running this role via Packer, you need to make sure Ansible is installed via a shell provisioner, and that preliminary VM configuration (like adding a vagrant user to the appropriate group and the sudoers file) is complete, generally by using a Kickstart installation file (e.g. `ks.cfg`) with Packer. An example array of provisioners for your Packer .json template would be something like:

    "provisioners": [
      {
        "type": "shell",
        "execute_command": "echo 'vagrant' | {{.Vars}} sudo -S -E bash '{{.Path}}'",
        "script": "scripts/ansible.sh"
      },
      {
        "type": "ansible-local",
        "playbook_file": "ansible/main.yml",
        "role_paths": [
          "/Users/jgeerling/Dropbox/VMs/roles/geerlingguy.packer_rhel",
        ]
      }
    ],

The files should contain, at a minimum:

**scripts/ansible.sh**:

    #!/bin/bash -eux
    # Add the EPEL repository, and install Ansible.
    rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
    yum -y install ansible python-setuptools

**ansible/main.yml**:

    ---
    - hosts: all
      sudo: yes
      gather_facts: yes
      roles:
        - nholuongy.packer_rhel

You might also want to add another shell provisioner to run cleanup, erasing free space using `dd`, but this is not required (it will just save a little disk space in the Packer-produced .box file).

If you'd like to add additional roles, make sure you add them to the `role_paths` array in the template .json file, and then you can include them in `main.yml` as you normally would. The Ansible configuration will be run over a local connection from within the Linux environment, so all relevant files need to be copied over to the VM; configuratin for this is in the template .json file. Read more: [Ansible Local Provisioner](http://www.packer.io/docs/provisioners/ansible-local.html).

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    packer_rhel_libselinux_package: libselinux-python

The libselinux python package to be installed. This is overridden for RHEL 8 to be `python3-libselinux`.

## Dependencies

None.

## Example Playbook

    - hosts: all
      roles:
        - { role: nholuong.packer_rhel }

# 🚀 I'm are always open to your feedback.  Please contact as bellow information:
### [Contact ]
* [Name: nho Luong]
* [Skype](luongutnho_skype)
* [Github](https://github.com/nholuongut/)
* [Linkedin](https://www.linkedin.com/in/nholuong/)
* [Email Address](luongutnho@hotmail.com)

![](https://i.imgur.com/waxVImv.png)
![](Donate.png)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nholuong)

# License
* Nho Luong (c). All Rights Reserved.🌟
