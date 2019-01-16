### vagrant best practices : prepare a salt environment with vagrant (multiple machines using same Vagrant file)

This is from: http://humankeyboard.com/saltstack/2014/saltstack-virtualbox-vagrant.html

You dont need separate projects to create multiple virtual machines. You can write everything in just one vagrant file.


### 1. Create Folder
Lets make only 1 folder in desktop where we want to make the environment
```bash
$ cd VagrantBoxes
$ mkdir salt
$ vagrant init ubuntu/trusty64
```
Open 3 separate __terminals__ for each master/minion1/minion2

### 2. Edit Vagrant file
in Vagrant file:
```
Vagrant.configure(2) do |config|
 
  config.vm.define "master" do |MS|
    MS.vm.box = "ubuntu/trusty64"
    MS.vm.host_name = "salt-master"
    MS.vm.network "private_network", ip: "192.168.33.10"
  end
  
  config.vm.define "minion1" do |mi1|    
    mi1.vm.box = "ubuntu/trusty64"
    mi1.vm.host_name = "salt-minion1"
    mi1.vm.network :private_network, ip: "192.168.56.11" 
  end  
  
  config.vm.define "minion2" do |mi2|    
    mi2.vm.box = "ubuntu/trusty64"
    mi2.vm.host_name = "salt-minion2"
    mi2.vm.network :private_network, ip: "192.168.56.12" 
  end
end
```


You can also write this file as:
```
Vagrant.configure(2) do |config|
  
  config.vm.define "master" do |machine|
    machine.vm.box = "ubuntu/trusty64"
    machine.vm.host_name = "salt-master"
    machine.vm.network "private_network", ip: "192.168.33.10"
  end
  
  config.vm.define "minion1" do |machine|    
    machine.vm.box = "ubuntu/trusty64"
    machine.vm.host_name = "salt-minion1"
    machine.vm.network :private_network, ip: "192.168.56.11" 
  end  
  
  config.vm.define "minion2" do |machine|    
    machine.vm.box = "ubuntu/trusty64"
    machine.vm.host_name = "salt-minion2"
    machine.vm.network :private_network, ip: "192.168.56.12" 
  end
  
end
```
Also we can use for loops: https://maci0.wordpress.com/2013/11/09/dynamic-multi-machine-vagrantfile/
__BE CAREFUL__: Avoid this bug for any loops: https://docs.vagrantup.com/v2/vagrantfile/tips.html

Final Vagrant file is something like this:
```
VAGRANTFILE_API_VERSION = "2"
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  #common property for any kind of machine
  config.vm.box = "ubuntu/trusty64"
  
  #propertry for master  
  config.vm.define "master" do |machine|
    #after entering machine u will see vagrant@salt-master
    machine.vm.host_name = "salt-master"
    machine.vm.network "private_network", ip: "192.168.33.10"
  end
  
  #propertry for minion
  NODE_COUNT = 2
  NODE_COUNT.times do |i|
    #see $ vagrant status to understand the use of machine_id 
    machine_id = "node#{i+1}"
    config.vm.define machine_id do |machine|
      machine.vm.host_name = "salt-minion#{i+1}"
      machine.vm.network :private_network, ip: "192.168.56.1#{i+1}" 
    end
  end
end
```


I found another way to create vagrant box 
1.  taking input from __YAML__ file :
http://blog.scottlowe.org/2014/10/22/multi-machine-vagrant-with-yaml/
2. taking input from __JSON__ file: 
https://programmaticponderings.wordpress.com/2014/02/27/multi-vm-creation-using-vagrant-and-json/

### 3. Status of VMs
in  __terminal__:
```bash
$ vagrant status
```
```
Current machine states:

master                    not created (virtualbox)
node1                     not created (virtualbox)
node2                     not created (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

### 4. Vagrant up and ssh
To run entire 3 machines
```bash
$ vagrant up
#this will "vagrant up" all 3 machines
#to "vagrant up" only a particular machine use
#vagrant up <machine_id>

#to enter minion1
$ vagrant ssh node1
```

To come out of a VM:
```bash
$ exit
```
### 5. Setup Rest of Salt

Setup salt yourself using previous reads
