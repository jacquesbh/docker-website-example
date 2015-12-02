# -*- mode: ruby -*-
# vi: set ft=ruby :

# CONFIGURATION
# ¯¯¯¯¯¯¯¯¯¯¯¯¯

# Machine hostname for local development
$hostname      = "demo.dev"
$virtualbox_ip = "10.0.1.42"
$project_name  = "demo"

# PLEASE DON'T TOUCH BELOW
# ¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯¯

# Dependencies
dependencies_installed = false
unless Vagrant.has_plugin?("vagrant-docker-compose")
  system("vagrant plugin install vagrant-docker-compose")
  dependencies_installed = true
end
unless Vagrant.has_plugin?("vagrant-hostmanager")
  system("vagrant plugin install vagrant-hostmanager")
  dependencies_installed = true
end
if dependencies_installed
  puts "Dependency installed, please try the command again."
  exit
end

Vagrant.configure(2) do |config|

  # Manage host
  config.hostmanager.enabled            = true
  config.hostmanager.manage_host        = true
  config.hostmanager.ignore_private_ip  = false
  config.hostmanager.include_offline    = true
  config.hostmanager.aliases            = ["www." + $hostname]

  config.vm.define $hostname do |server|

    # Which box?
    server.vm.box     = "puphpet/debian75-x64"
    server.vm.box_url = "puphpet/debian75-x64"
    #server.vm.box = "hashicorp/precise64"

    # VMWare Fusion customization
    server.vm.provider :vmware_fusion do |vmware, override|

        # Customize VM
        vmware.vmx["memsize"] = "2048"
        vmware.vmx["numvcpus"] = "2"

    end

    # Virtualbox customization
    server.vm.provider :virtualbox do |virtualbox, override|

        # Customize VM
        virtualbox.customize ["modifyvm", :id, "--memory", "2048", "--cpus", "2", "--pae", "on", "--hwvirtex", "on", "--ioapic", "on"]

        # Network
        override.vm.network :private_network, ip: $virtualbox_ip

    end

    # Network
    server.vm.hostname = $hostname

    # Synced folders
    # On OSX we use some tips to boost the nfs ;)
    if (/darwin/ =~ RUBY_PLATFORM)
        server.vm.synced_folder "", "/vagrant", nfs: true,
            mount_options: ["nolock", "async"],
            bsd__nfs_options: ["alldirs","async","nolock"]
    else
        server.vm.synced_folder "", "/vagrant"
    end

    # Manage the host
    server.vm.provision :hostmanager

    # Install required packages
    config.vm.provision :shell,
                        inline: "sudo apt-get update && apt-get install -y apt-transport-https"

    # Provision with Docker
    server.vm.provision :docker
    server.vm.provision :docker_compose,
                        yml: "/vagrant/infra/docker-compose.yml",
                        project_name: $project_name,
                        command_options: { up: "-d --timeout 20"},
                        run: "always"
  end

end
