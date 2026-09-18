Vagrant.configure("2") do |config|
  config.vm.box = "gusztavvargadr/windows-10"
  config.vm.communicator = "winrm"

  config.vm.boot_timeout = 600

  config.winrm.username = "vagrant"
  config.winrm.password = "vagrant"
  config.winrm.transport = :plaintext
  config.winrm.basic_auth_only = true

  config.vm.synced_folder ".", "/vagrant"

  config.vm.provider "virtualbox" do |vb|
    vb.name = "windows-deserialization-lab"
    vb.cpus = 2
    vb.memory = 3048
    vb.linked_clone = true
    vb.gui = true
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.inventory_path = "inventory/hosts"
    ansible.limit = "windows"
    ansible.verbose = "vvv"

    ansible.extra_vars = {
      ansible_connection: "winrm",
      ansible_user: "vagrant",
      ansible_password: "vagrant",
      ansible_port: 55985,
      ansible_winrm_transport: "basic",
      ansible_winrm_scheme: "http",
      ansible_winrm_server_cert_validation: "ignore"
    }
  end
end
