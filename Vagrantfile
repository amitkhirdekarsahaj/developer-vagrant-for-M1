Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.box_version = "20210623.0.0"
  # comment below line if encountering disk size error
  # config.disksize.size = '50GB'
  config.vm.provider "virtualbox" do |v|
    # comment below line if using MacOS BigSur or later
    v.customize [ "modifyvm", :id, "--uartmode1", "disconnected" ]
    v.memory = 4096
    v.cpus = 2
  end
  config.vm.synced_folder "./..", "/home/vagrant/workspace"
  config.vm.synced_folder "./scripts", "/home/vagrant/scripts"
  config.vm.synced_folder "./.terraform.d", "/home/vagrant/.terraform.d"
  config.vm.synced_folder "~/.ssh", "/home/vagrant/.ssh-keys"
  config.vm.network "forwarded_port", guest: 8080, host: 18080
  config.vm.network "forwarded_port", guest: 8200, host: 18200
  config.vm.network "forwarded_port", guest: 8443, host: 18443
  config.vm.provision "ansible_local" do |ansible|
    ansible.verbose = true
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "playbook.yml"
    ansible.galaxy_role_file = "requirements.yml"
    ansible.galaxy_roles_path = "/etc/ansible/roles"
    ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"
  end

  # Custom configuration for docker
  config.vm.provider "docker" do |docker, override|
    # docker doesnt use boxes
    override.vm.box = nil

    # this is where your Dockerfile lives
    docker.build_dir = "."

    # Make sure it sets up ssh with the Dockerfile
    # Vagrant is pretty dependent on ssh
    override.ssh.insert_key = true
    docker.has_ssh = true

    # Configure Docker to allow access to more resources
    docker.privileged = true
  end
end
