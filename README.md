ansible-docker-consul
=========

Ansible role for bootstrapping a Docker-based Consul cluster. Currently uses [`gliderlabs/consul:legacy`](https://hub.docker.com/r/gliderlabs/consul/) (might extend this to `gliderlabs/consul:latest` in the future.)

Requirements
------------

Dependencies of the [docker module](http://docs.ansible.com/ansible/docker_module.html), which are presently:
* python >= 2.6
* docker-py >= 0.3.0
* The docker server >= 0.10.0

Role Variables
--------------

TODO: A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

Dependencies
------------

* aeriscloud.docker ([GitHub](https://github.com/AerisCloud/ansible-docker) / [Ansible Galaxy](https://galaxy.ansible.com/detail#/role/3019))
* bobbyrenwick.pip ([GitHub](https://github.com/bobbyrenwick/ansible-pip) / [Ansible Galaxy](https://galaxy.ansible.com/detail#/role/393))

See [test_requirements.yml](./test_requirements.yml) and [test.yml](./test.yml) to see how to ensure dependencies are run ahead of this role.

Example Playbook
----------------

Write a playbook `consul.yml`

```
- hosts: all
  roles:
  - { role: aeriscloud.docker }
  - { role: bobbyrenwick.pip }
  tasks:
  - include: "tasks/main.yml"
```

And a inventory file `/etc/ansible/hosts` that looks like

```
[consul_leader]
ec2-54-204-214-172.compute-1.amazonaws.com consul_cluster_size=5

[consul_followers]
ec2-54-235-59-210.compute-1.amazonaws.com consul_leader_ip=169.254.169.254
ec2-54-83-161-83.compute-1.amazonaws.com consul_leader_ip=169.254.169.254
ec2-54-91-78-105.compute-1.amazonaws.com consul_leader_ip=169.254.169.254
ec2-54-82-227-223.compute-1.amazonaws.com consul_leader_ip=169.254.169.254
```

Note that `consul_leader_ip` is the private IP of the consul leader. This playbook also assumes that the private IP is the IP of the `eth1` interface (`{{ansible_eth1.ipv4.address}}`) -- I'm not sure if this is correct; feel free to submit a PR if it doesn't work for you.

Vagrant test
------------

First, install playbook dependencies by running

```
sudo ansible-galaxy install -r test_requirements.yml # --force
```

Then bring up the vagrant hosts and run the plays against them

```
vagrant up

# or separate steps
vagrant up --no-provision
vagrant provision
```

License
-------

BSD

Author Information
------------------

[Shujon Mollah](https://github.com/smoll) (@smoll)
