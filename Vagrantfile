
Vagrant.configure("2") do | config |
  N = 2
  ANSIBLE_INVENTORY_DIR = "provisioner"
  VAGRANT_VM_PROVIDER   = "virtualbox"
  # ensure directory exists & clean 
  Dir.mkdir(ANSIBLE_INVENTORY_DIR) unless Dir.exist?(ANSIBLE_INVENTORY_DIR)
  File.open("#{ANSIBLE_INVENTORY_DIR}/hosts", "w") {|file| file.truncate(0) }
  
  (1..N).each do | machine_id |
    config.vm.define "machine.#{machine_id}" do | server |
    
      server.vm.provider "libvirt" do |config|
        config.memory = 2048
        config.cpus = 2
      end

      server.vm.box      = "debian/buster64"
      server.vm.hostname = "machine.#{machine_id}"
      server.vm.network "private_network", ip:"192.168.50.#{50+machine_id-1}"

      # dynamically create inventory
      File.open("#{ANSIBLE_INVENTORY_DIR}/hosts" ,"a") do | file |
        file.write "[machine.#{machine_id}]\n"
        file.write "192.168.50.#{50+machine_id-1}\n"
        file.write "\n"
      end

      server.vm.provision :ansible do | ansible |
        # Disable default limit to connect to all the servers
        ansible.limit           = "192.168.50.#{50+machine_id-1}"
        ansible.playbook        = "provisioner/playbook.yml"
        ansible.inventory_path  = "#{ANSIBLE_INVENTORY_DIR}/hosts"
        # ansible.verbose         = "-v"
      end
    end
  end
end


