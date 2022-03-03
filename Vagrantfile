
Vagrant.configure("2") do | config |
  
  N = 2
  ANSIBLE_INVENTORY_DIR = "provisioner"
  VAGRANT_VM_PROVIDER   = "virtualbox"
  
  # ensure directory exists
  Dir.mkdir(ANSIBLE_INVENTORY_DIR) unless Dir.exist?(ANSIBLE_INVENTORY_DIR)

  (1..N).each do | machine_id |
    config.vm.define "machine.#{machine_id}" do | server |
      server.vm.box      = "debian/buster64"
      server.vm.hostname = "machine.#{machine_id}"
      server.vm.network "private_network", ip:"192.168.50.#{50+machine_id-1}"

      # only execute once the Ansible provisioner, when all the servers are up and ready.

      # dynamically create individual Ansible entries
      File.open("#{ANSIBLE_INVENTORY_DIR}/hosts" ,'a') do | f |
        f.write "[machine.#{machine_id}]\n"
        f.write "192.168.50.#{50+machine_id-1}\n"
        f.write "\n"
      end

      server.vm.provision :ansible do | ansible |
        # Disable default limit to connect to all the servers
        ansible.limit           = "all"
        ansible.playbook        = "provisioner/playbook.yml"
        ansible.inventory_path  = "#{ANSIBLE_INVENTORY_DIR}/hosts"
        ansible.verbose         = "-v"
      end
    end
  end
end


