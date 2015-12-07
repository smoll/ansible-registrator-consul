$number_of_machines = 3

Vagrant.configure("2") do |config|
  # always use Vagrant's insecure key
  config.ssh.insert_key = false

  config.vm.box = "phusion-open-ubuntu-14.04-amd64"
  config.vm.box_url = "https://oss-binaries.phusionpassenger.com/vagrant/boxes/latest/ubuntu-14.04-amd64-vbox.box"

  (1..$number_of_machines).each do |i|
    config.vm.define "host-#{i}" do |host|
      host.vm.hostname = "host-#{i}"
      # Hardcode IPs as 192.168.50.101, 192.168.50.102, etc. to make it easier to debug
      host.vm.network "private_network", ip: "192.168.50.#{100 + i}"

      # See http://docs.ansible.com/ansible/guide_vagrant.html#running-ansible-manually
      host.vm.network :forwarded_port, guest: 22, host: 2200 + i, id: 'ssh'
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.inventory_path = "hosts.sample"
    ansible.playbook = "test.yml"
    ansible.sudo = true
    ansible.verbose = "v"
  end
end
