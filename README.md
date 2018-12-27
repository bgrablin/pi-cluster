Created Development Branch 12/21/2018

##  HOW TO CHANGE GIT BRANCH
Use the checkout command to switch branch.

$ git checkout <branch>
Switch to the branch "issue1" by doing the following.

$ git checkout issue1
Switched to branch 'issue1'

# My Notes:

/* Ansible Folder Structure

production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1.yml             # here we assign variables to particular groups
   group2.yml
host_vars/
   hostname1.yml          # here we assign variables to particular systems
   hostname2.yml

library/                  # if any custom modules, put them here (optional)
module_utils/             # if any custom module_utils to support modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""


"msg": "to use the 'ssh' connection type with passwords, you must install the sshpass program"

sudo apt-get install sshpass -y

#################################################################################
#################################################################################

Hosts file

[pi-cluster]
192.168.1.13[4:7]

[master]
flagship ansible_host=192.168.1.134

[nodes]
ship1 ansible_host=192.168.1.135
ship2 ansible_host=192.168.1.136
ship3 ansible_host=192.168.1.137

[pi-cluster:vars]
ansible_ssh_user=pi
ansible_ssh_pass=raspberry
deploy_target=pi
kubernetes_apiserver_advertise_address=192.168.1.134
ansible_ssh_common_args='-o StrictHostKeyChecking=no'

#################################################################################
#################################################################################

ansible all -i hosts -m ping

pi@raspberrypi:~ $ ansible all -i hosts -m ping
flagship | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ship3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ship1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ship2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
pi@raspberrypi:~ $ ansible master -i hosts -m ping
flagship | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
#################################################################################
#################################################################################

ansible all -i hosts -a "apt-get update" -s

#################################################################################
#################################################################################

pi@raspberrypi:~ $ ansible-playbook -i hosts test-cluster.yml

PLAY [Test my raspberry cluster] ***********************************************

TASK [setup] *******************************************************************
ok: [192.168.1.137]
ok: [192.168.1.136]
ok: [192.168.1.135]
ok: [192.168.1.134]

TASK [Check Rasp model] ********************************************************
changed: [192.168.1.134]
changed: [192.168.1.136]
changed: [192.168.1.135]
changed: [192.168.1.137]

TASK [Debug each entry of my hosts] ********************************************
ok: [192.168.1.136] => {
    "msg": "System 192.168.1.136 is a Raspberry Pi 3 Model B Plus Rev 1.3\u0000"
}
ok: [192.168.1.137] => {
    "msg": "System 192.168.1.137 is a Raspberry Pi 3 Model B Plus Rev 1.3\u0000"
}
ok: [192.168.1.135] => {
    "msg": "System 192.168.1.135 is a Raspberry Pi 3 Model B Plus Rev 1.3\u0000"
}
ok: [192.168.1.134] => {
    "msg": "System 192.168.1.134 is a Raspberry Pi 3 Model B Plus Rev 1.3\u0000"
}

PLAY RECAP *********************************************************************
192.168.1.134              : ok=3    changed=1    unreachable=0    failed=0
192.168.1.135              : ok=3    changed=1    unreachable=0    failed=0
192.168.1.136              : ok=3    changed=1    unreachable=0    failed=0
192.168.1.137              : ok=3    changed=1    unreachable=0    failed=0

#################################################################################
#################################################################################

List All Hosts in the Inventory File
A quick way to get a list of all the servers Ansible is aware of:

ansible -i hosts all --list-hosts

#################################################################################
#################################################################################
#################################################################################
#################################################################################

TEST Bare Metal Deploy


Commands:

sudo apt-get install ansible git sshpass

ansible all -i hosts -m ping
	Error:   WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!   ### Error is native to running ansible commands on ansible host that has already run commands

export ANSIBLE_HOST_KEY_CHECKING=False

###  Still had Issues

Try ssh-keyscan 192.168.1.xxx >> ~/.ssh/known_hosts


#################################################################################
#################################################################################

Configuring Docker natively

pi@raspberrypi:~ $ sudo docker swarm init --advertise-addr 192.168.1.134
Swarm initialized: current node (tz6ek9atjcdlf0drkxzj8k7wj) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-466431yc975tujjkp5jqr6vqoqmad29q2dzpkmibg6qtces54f-0c8xy8qneb6oze9csya0mnbli 192.168.1.134:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

[vagrant@rhel7 pi-cluster]$ ansible nodes -i hosts -a "docker swarm join --token SWMTKN-1-466431yc975tujjkp5jqr6vqoqmad29q2dzpkmibg6qtces54f-0c8xy8qneb6oze9csya0mnbli 192.168.1.134:2377" -s
[DEPRECATION WARNING]: The sudo command line option has been deprecated in favor of the "become" command line arguments. This feature will be removed in
version 2.9. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
192.168.1.137 | CHANGED | rc=0 >>
This node joined a swarm as a worker.

192.168.1.136 | CHANGED | rc=0 >>
This node joined a swarm as a worker.

192.168.1.135 | CHANGED | rc=0 >>
This node joined a swarm as a worker.

#################################################################################
#################################################################################

Vagrant Up
Vagrant Putty