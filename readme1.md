###0. What is Vagrant?
__Ans__: it is a good wrapper over VirtualBox APIs and Commands.
Its a useful api to work with VirtualBox.


###1. Install vagrant:


install virtualbox:
```bash
sudo apt-get install virtualbox dkms virtualbox-guest-additions-iso
```
install vagrant from https://www.vagrantup.com/downloads.html
or you can also use command line

```bash
wget https://dl.bintray.com/mitchellh/vagrant/vagrant_1.6.3_x86_64.deb
#try this before isntalling
#sha256sum vagrant_1.6.3_x86_64.deb | grep 0fc3259cf08b693e3383636256734513ee93bf258f8328efb64e1dde447aadbe
sudo dpkg -i vagrant_1.6.3_x86_64.deb
#since you dont need this you can do this
#rm vagrant_1.6.3_x86_64.deb
```

__NOTE__:
latest version maybe:  __vagrant_1.7.1_x86_64.deb__
I kept it in the __downloaded files__ folder

__NOTE:__ __vagrant_1.7.1_x86_64.deb__ package has some issues, it cant make package
`$ sudo vagrant package --name mypackage` this command will not run
So a more stable version is I think __vagrant_1.6.3_x86_64.deb__
I kept it in the __downloaded files__ folder


now start reading:
https://docs.vagrantup.com/v2/getting-started/


###2. Simple hello world project to start:


```bash
#Create a folder where you want to put your new virtual machine
$ sudo mkdir VagrantTestProj
$ cd VagrantTestProj
#Official Ubuntu Server 14.04 LTS (Trusty Tahr) builds ready
$ vagrant init ubuntu/trusty64
#using sudo vagrant init will not allow the virtualmachine GUI to see your running machine
$ vagrant up
#no sudo also
```

output:
```
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'ubuntu/trusty64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'ubuntu/trusty64'
    default: URL: https://atlas.hashicorp.com/ubuntu/trusty64
==> default: Adding box 'ubuntu/trusty64' (v14.04) for provider: virtualbox
    default: Downloading: https://atlas.hashicorp.com/ubuntu/boxes/trusty64/versions/14.04/providers/virtualbox.box
```
after downloading:
```bash
==> default: Successfully added box 'ubuntu/trusty64' (v14.04) for 'virtualbox'!
==> default: Importing base box 'ubuntu/trusty64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'ubuntu/trusty64' is up to date...
==> default: Setting the name of the VM: testbox1_default_1420016529285_35744
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 => 2222 (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
    default: Warning: Remote connection disconnect. Retrying...
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if its present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /home/nahiyan/Desktop/VagrantBoxes/testbox1
```

I you are thinking what is __atlas.hashicorp__ its a place where you get lots of images ready to use. In this link you can search for virtualbox/VM/AWS/Digital ocean etc of every format images
https://atlas.hashicorp.com/boxes/search?

In this what actually happened is you started a vagrant project inside the folder __Vagrantboxes__ then you added the __.vbox__ file in your vagrant box collections. Then you cloned that .vbox file in this project. and finally started the cloned .vbox.
Now if you open your __virtualbox__ program you can see the image running. Now if you want to do something in the gueast OS. You need to ssh in it:
```bash
$ cd VagrantTestProj
$ vagrant ssh
```
output:
```bash
Welcome to Ubuntu 14.04.1 LTS (GNU/Linux 3.13.0-43-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Dec 31 09:13:40 UTC 2014

  System load:  0.24              Processes:           96
  Usage of /:   2.8% of 39.34GB   Users logged in:     0
  Memory usage: 15%               IP address for eth0: 10.0.2.15
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud
0 packages can be updated.
0 updates are security updates.
vagrant@vagrant-ubuntu-trusty-64:~$
```
Now you can see that you are inside the terminal of gueast OS __vagrant@vagrant-ubuntu-trusty-64:~$__. You can start running any command here as you like.
```bash
vagrant@vagrant-ubuntu-trusty-64:~$ fish --version
#this will get error as its not installed in the gueast
# you can install it here if you want
vagrant@vagrant-ubuntu-trusty-64:~$ sudo apt-get install fish
#installation starts
```
 To come back to host OS:
>```bash
>vagrant@vagrant-ubuntu-trusty-64:~$ exit
>```
>output:
>```bash
>logout
>Connection to 127.0.0.1 closed.
>$
>```

Note that you guest machine is still running. Open Virtualbox UI to see this.

>__Question__:
>Is after all the changes you made will they be there if you `$ vagrant ssh` again?
>__Answer__: yes

To stop the running machine (of this project):
```bash
$ cd VagrantTestProj
$ vagrant halt
```
>__Question__ :
>Aafter halting will the changes be there if you `$ vagrant up $ vagrant ssh` >again?
>__Answer__: yes, Only __$ vagrant destroy__ will erase all history

###3. Some useful commands in vagrant:

#####3.1 Show list of all boxes added:
>```bash
>$ vagrant box list
>```
>output:
>```bash
>uidevbox        (virtualbox, 0)
>ubuntu/trusty64 (virtualbox, 14.04)
>```

#####3.2 Refresh vagrant file:
```bash
$ vagrant reload
```
If you make any changes to the configuration in vagrantfile which needs to take effect.
You may want to reload the VM. It re-runs the provisioning defined in the vagrantfile unless you ask it not to.

It does not destroy the VM you have created from a base box. That means all the changes you have made to your VM, like say created a folder in your user directory will be there after reload.

It is like reboot where it powers off your VM  and then applies certain configuration change which can be applied only when VM has been powered off. and then power it on.
Example: like attaching another SATA Virtual Disk.


#####3.3 Start vagrant in a project
```bash
$ vagrant up
```
This reads your configuration file - `vagrantfile` and then creates a VM from base box.
Base Box is like a Template. You can create many VMs from it.

Similarly, `vagrant destroy` destroys your VM. In this case all changes you made
when inside it will be lost. But thats the cool idea that you can start from a base
predefined state when you create a new VM.
