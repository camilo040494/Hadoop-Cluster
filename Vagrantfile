# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  N = 2 

  VAGRANT_VM_PROVIDER = "virtualbox"
  ANSIBLE_RAW_SSH_ARGS = []

  (1..N-1).each do |machine_id|
    ANSIBLE_RAW_SSH_ARGS << "-o IdentityFile=.vagrant/machines/machine#{machine_id}/#{VAGRANT_VM_PROVIDER}/private_key"
  end

  (1..N).each do |machine_id|
    config.vm.define "machine#{machine_id}" do |machine|
      machine.vm.box = "generic/rhel7"
      machine.vm.hostname = "machine#{machine_id}"
      machine.vm.network "private_network", ip: "172.17.177.#{20+machine_id}"
      
      machine.vm.provider :virtualbox do |vb|
        vb.customize [ 'modifyvm', :id, '--memory', '1024' ]
	vb.customize [ 'modifyvm', :id, '--cpus', '2' ]
	vb.customize [ 'modifyvm', :id, '--name', 'machine#{machine_id}' ]
      end
      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      if machine_id == N
        machine.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.playbook = "playbook.yml"
          #ansible.limit = "--limit playbook.retry"
          ansible.inventory_path = "inventory"
          ansible.verbose = "-vvv"
          ansible.raw_ssh_args = ANSIBLE_RAW_SSH_ARGS
        end
      end
    end
  end
end

