ansible-registrator-consul
=========

Ansible role for Consul + Registrator. Currently uses [`gliderlabs/consul:legacy`](https://hub.docker.com/r/gliderlabs/consul/).

Consul = service registry that can also be used for DNS lookup

Registrator = automatic registration+deregistration of Docker containers

Requirements
------------

Dependencies of the [docker module](http://docs.ansible.com/ansible/docker_module.html), which are presently:
* python >= 2.6
* docker-py >= 0.3.0
* The docker server >= 0.10.0

Role Variables
--------------

These variables are only needed for the initial bootstrap process. See the [README from gliderlabs/docker-consul](https://github.com/gliderlabs/docker-consul/blob/4dad2dd6c88af8c8c5680f5dcc392542e000c878/README.md#running-a-real-consul-cluster-in-a-production-environment) for more info.

`consul_cluster_size`: The total size of the Consul cluster before it attempts to bootstrap. Needs to be specified on the _leader_ host only.

`consul_leader_ip`: The private IP of the leader host. Needs to be specified on all _follower_ hosts.

Note that _once initial bootstrap is complete_, the leader is constantly reelected and even if one or more nodes go bad, they will [maintain a quorum](https://www.consul.io/docs/internals/consensus.html) as long as there are 3 or more nodes.

Dependencies
------------

* aeriscloud.docker ([GitHub](https://github.com/AerisCloud/ansible-docker) / [Ansible Galaxy](https://galaxy.ansible.com/detail#/role/3019))
* bobbyrenwick.pip ([GitHub](https://github.com/bobbyrenwick/ansible-pip) / [Ansible Galaxy](https://galaxy.ansible.com/detail#/role/393))

Example Playbook
----------------

Here's a sample setup for 5 Docker hosts in AWS EC2. First, write a playbook `service_discovery.yml`

```
- hosts: all
  roles:
  - { role: smoll.registrator-consul }
```

And a `requirements.yml`

```
- src: smoll.registrator-consul
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

Then run

```
$ ansible-galaxy install -r requirements.yml
$ ansible-playbook -i /etc/ansible/hosts service_discovery.yml
```

Note that `consul_leader_ip` is the private IP of the consul leader. This playbook also assumes that the private IP is the IP of the `eth1` interface (`{{ansible_default_ipv4.address}}`) -- I'm not sure if this is correct; feel free to submit a PR if it doesn't work for you.

Vagrant test
------------

(**NOTE:** if you clone this repo, the parent directory must be named `registrator-consul` -- not `ansible-registrator-consul` -- or Ansible won't be able to find this role for the Vagrant test!)

First, install playbook dependencies by running

```
sudo ansible-galaxy install -r test_requirements.yml # --force
```

Then bring up the vagrant hosts and run the plays against them

```
vagrant up

# separately:
vagrant up --no-provision

# then
vagrant provision

# or, a more realistic simulation:
ansible-playbook --private-key=~/.vagrant.d/insecure_private_key -u vagrant -i test_hosts -s test.yml
```

License
-------

MIT

Author Information
------------------

[Shujon Mollah](https://github.com/smoll) (@smoll)
