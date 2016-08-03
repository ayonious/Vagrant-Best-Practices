you should read the entire
https://docs.vagrantup.com/v2/getting-started/index.html
very nice tutorial for beginners




###1.1 Add boxes in vagrant:
Adding boxes in vagrant means adding a __base box__ in your local vagrant directory so that whenever you want to open a project you just clone this image and start working. So after adding when you want to __vagrant up__ this image will be copied to your project directory and you start working with it. FYI changing in the cloned image will not harm your original  __base box__.
So any `$ vagrant init` will clone from a __base box__.

To add a (base)box in vagrant:
```bash
$ vagrant box add  puphpet/ubuntu1404-x64
```
output:
```
vagrant box add  puphpet/ubuntu1404-x64
==> box: Loading metadata for box 'puphpet/ubuntu1404-x64'
		box: URL: https://atlas.hashicorp.com/puphpet/ubuntu1404-x64
This box can work with multiple providers! The providers that it
can work with are listed below. Please review the list and choose
the provider you will be working with.

1) parallels
2) virtualbox
3) vmware_desktop

Enter your choice: 2
==> box: Adding box 'puphpet/ubuntu1404-x64' (v1.2) for provider: virtualbox
		box: Downloading: https://atlas.hashicorp.com/puphpet/boxes/ubuntu1404-x64/versions/1.2/providers/virtualbox.box
==> box: Box download is resuming from prior download progress
==> box: Successfully added box 'puphpet/ubuntu1404-x64' (v1.2) for 'virtualbox'!
```

As you can see that you have option for downloading in multiple format.

This will only add the image in your local vagrant images collection. If you are wondering where this image is stored, its in __~/.vagrant.d/boxes__
http://stackoverflow.com/questions/10155708/vagrant-box-add-where-does-box-file-get-downloaded-to


###1.2 Add boxes in vagrant (in offline mode):
This is helpful if you dont have net/slow internet. Suppose you downloaded images in one pc and you want to put this image in another pc.
I just copied all the __base boxes__ from one pc's hard drive to another pc's hard drive this worked.
Base boxes are located in __~/.vagrant.d/boxes__

###2. How to Create a Vagrant Base Box from an Existing One
Taken from: https://scotch.io/tutorials/how-to-create-a-vagrant-base-box-from-an-existing-one
#####__NOTE:__ __This command is Applicable Only for VirtualBox__

```bash
$ vagrant init hashicorp/precise64
$ vagrant up
$ vagrant ssh
#now you have entered in the image lets make some changes
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install vim
$ sudo apt-get install apache2
#ok done with all changes
```
Now lets clean before exiting
```bash
$ sudo apt-get clean
#zero out only for ubuntu (next 2 lines)
$ sudo dd if=/dev/zero of=/EMPTY bs=1M
$ sudo rm -f /EMPTY
# clear bash history and exit
cat /dev/null > ~/.bash_history && history -c
```

>__NOTE__:
>There is another issue about clearing in this step:https://github.com/mitchellh/vagrant/issues/997
>Its also mentioned in the comment section of:
>  https://scotch.io/tutorials/how-to-create-a-vagrant-base-box-from-an-existing-one

According to them you should also:
```bash
# Kill us some udev crap.
$ sudo ln -sf /dev/null /lib/udev/rules.d/75-persistent-net-generator.rules
$ sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
```
Repackage the VM into a New Vagrant Box
```bash
$ vagrant package --output mynew.box
#this is not working in 1.7.1 you can also use vagrant package for default name
```
Add the Box into Your Vagrant Install
```bash
$ vagrant box add mynewbox mynew.box
```
output:
```bash
==> box: Adding box 'mynewbox' (v0) for provider:
    box: Downloading: file:///home/nahiyan/Desktop/VagrantBoxes/testbox1/package.box
==> box: Successfully added box 'mynewbox' (v0) for 'virtualbox'!

```
now you can use this as __base box__. Here is some test that I made
```bash
$ vagrant list box
mynewbox        (virtualbox, 0)
uidevbox        (virtualbox, 0)
ubuntu/trusty64 (virtualbox, 14.04)
$ vagrant destroy
$ cd ..
$ mkdir testfolder
$ vagrant init mynewbox
...
$ vagrant up
$ vagrant ssh
$ sudo apt-get install fish
$ exit
```
You can also find this newly created __base box__ in the __.vagrant.d/boxes__ folder.

###3. Teardown
Taken from:
https://docs.vagrantup.com/v2/getting-started/teardown.html

We now have a fully functional virtual machine we can use for basic web development. But now let's say it is time to switch gears, maybe work on another project, maybe go out to lunch, or maybe just time to go home. How do we clean up our development environment?

With Vagrant, you suspend, halt, or destroy the guest machine. Each of these options have pros and cons. Choose the method that works best for you.

Suspending the virtual machine by calling `vagrant suspend` will save the current running state of the machine and stop it. When you're ready to begin working again, just run `vagrant up`, and it will be resumed from where you left off. The main benefit of this method is that it is super fast, usually taking only 5 to 10 seconds to stop and start your work. The downside is that the virtual machine still eats up your disk space, and requires even more disk space to store all the state of the virtual machine RAM on disk.

Halting the virtual machine by calling `vagrant halt` will gracefully shut down the guest operating system and power down the guest machine. You can use `vagrant up` when you're ready to boot it again. The benefit of this method is that it will cleanly shut down your machine, preserving the contents of disk, and allowing it to be cleanly started again. The downside is that it'll take some extra time to start from a cold boot, and the guest machine still consumes disk space.

Destroying the virtual machine by calling `vagrant destroy` will remove all traces of the guest machine from your system. It'll stop the guest machine, power it down, and remove all of the guest hard disks. Again, when you're ready to work again, just issue a `vagrant up`. The benefit of this is that no cruft is left on your machine. The disk space and RAM consumed by the guest machine is reclaimed and your host machine is left clean. The downside is that vagrant up to get working again will take some extra time since it has to reimport the machine and reprovision it.


###4. SYNCED FOLDERS
https://docs.vagrantup.com/v2/getting-started/synced_folders.html


While it is cool to have a virtual machine so easily, not many people want to edit files using just plain terminal-based editors over SSH. Luckily with Vagrant you don't have to. By using synced folders, Vagrant will automatically sync your files to and from the guest machine.

By default, Vagrant shares your project directory (remember, that is the one with the Vagrantfile) to the /vagrant directory in your guest machine. Run vagrant up again and SSH into your machine to see:
```bash
$ vagrant up
...
$ vagrant ssh
...
vagrant@precise32:~$ ls /vagrant
Vagrantfile
```
Believe it or not, that Vagrantfile you see inside the virtual machine is actually the same Vagrantfile that is on your actual host machine. Go ahead and touch a file to prove it to yourself:
```bash
vagrant@precise32:~$ touch /vagrant/foo
vagrant@precise32:~$ exit
$ ls
foo Vagrantfile
```
Whoa! "foo" is now on your host machine. As you can see, Vagrant kept the folders in sync.

With synced folders, you can continue to use your own editor on your host machine and have the files sync into the guest machine.
