# devops-r_d

## Lecture 4 - Vagrant

# Virtual Machine Setup Guide

### install Vagrant on mac 
brew install --cask vagrant

### Сreate a Vagrantfile that runs a number of virtual machines, with the following requirements:
- **VM1 (behind the scenes webserver):** 
* Operating system: of your choice, especially recommended bento/ubuntu-24.04 or
generic/debian12
* Measurement connection to a secretly accessible network with dynamic IP
* Shared folder between host and VM
* Provisioning: use the shell command to update and install packages 

- **VM2 (private server):** 
* Operating system: of your choice, especially recommended bento/ubuntu-24.04 or
generic/debian12
* Connection to a private network with a static IP
* Shared folder between host and VM
* Provisioning: Vycort the external bash script that installs the required packages

- **VM3 (background server with static IP):**
* Operating system: according to your choice, recommended bento/ubuntu-24.04 or
generic/debian12
* Connect to a secretly accessible network with a static IP - vikorist the network
Host interface for bridged connection (for example, Wi-Fi)
* Create a shared folder between the host and VM


### VM1 
```
Vagrant.configure("2") do |config|

  config.vm.define "vm1" do |vm1|
    
    vm1.vm.box = "bento/ubuntu-24.04"
    # Network: dynamic IP
    vm1.vm.network "public_network"
  
    # Shared folder beetwen host and VM
    vm1.vm.synced_folder "./shared", "/home/vagrant/shared"
  
    # Provisioning
    vm1.vm.provision "shell", inline: <<-SHELL
      sudo apt update -y
      sudo apt upgrade -y
      sudo apt install -y curl wget git
    SHELL
  
    # use Parallels provider 
    vm1.vm.provider "parallels" do |prl|
      prl.memory = 2048  # 2GB RAM
      prl.cpus = 2       # 2 CPU
    end
  end
```
### VM2 
```
  config.vm.define "vm2" do |vm2|
    vm2.vm.box = "bento/ubuntu-24.04"  # or "generic/debian12"

    # Network: static IP (via DHCP in private network)
    vm2.vm.network "private_network", type: "dhcp"

    # Shared folder between host and VM
    vm2.vm.synced_folder "./shared", "/home/vagrant/shared"

    # Provisioning with an external shell script
    vm2.vm.provision "shell", path: "setup.sh"

    # Parallels provider configuration for VM2
    vm2.vm.provider "parallels" do |prl|
      prl.memory = 2048  # 2GB RAM
      prl.cpus = 2       # 2 CPU
    end
  end
```
## aditional configurations: 
touch setup.sh 
chmod +x setup.sh

### VM2 
```
config.vm.define "vm3" do |vm3|
    vm3.vm.box = "bento/ubuntu-24.04"  # або "generic/debian12"

    # Network: public network with a static IP
    vm3.vm.network "public_network", 
                   bridge: "en0", # Use your host network interface (e.g., Wi-Fi or Ethernet)
                   ip: "192.168.1.100"  # Static IP address for the VM

    # Shared folder between host and VM (separate shared folder for VM3)
    vm3.vm.synced_folder "./shared_vm3", "/home/vagrant/shared"

    # Provisioning with an inline shell script
    vm3.vm.provision "shell", inline: <<-SHELL
      sudo apt update -y
      sudo apt upgrade -y
      sudo apt install -y curl wget git
    SHELL

    # Parallels provider configuration for VM3
    vm3.vm.provider "parallels" do |prl|
      prl.memory = 2048  # 2GB RAM
      prl.cpus = 2       # 2 CPUs
    end
  end
```

### Varrant main commands: 
```
# install parallels 
vagrant plugin install vagrant-parallels 

# restart  
vagrant halt 

# suspend 
vagrant suspend

# get status 
vagrant global-status
```

## Report on Virtual Machine Setup
![Screenshot Placeholder](./lecture_4/images/vagrant_report.png)
