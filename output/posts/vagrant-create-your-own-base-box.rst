.. title: Vagrant create your own base box
.. slug: vagrant-create-your-own-base-box
.. date: 2014/06/12 11:18:05
.. tags: draft
.. link: 
.. description: 
.. type: text

apt-get install vim
sudo update-alternatives --config editor
ensure you have sudo installed

apt-get install sudo 

add vagrant to sudoers file and passwordless login
vagrant ALL=(ALL) NOPASSWD:ALL


mkdir -p /home/vagrant/.ssh
wget --no-check-certificate https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub -O /home/vagrant/.ssh/authorized_keys
Ensure we have the correct permissions set
chmod 0700 /home/vagrant/.ssh
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant /home/vagrant/.ssh



apt-get install -y openssh-server



$ sudo apt-get install -y build-essential linux-headers-server
	# Mount guest additions ISO via virtualbox window then run...
	$ sudo mount /dev/cdrom /media/cdrom
	$ sudo /media/cdrom/VBoxLinuxAdditions.run
	$ sudo umount /media/cdrom
	$ sudo apt-get clean

Make sure that GRUB_TIMEOUT is set to “1”, GRUB_HIDDEN_TIMEOUT_QUIET is set to “true”, and GRUB_CMDLINE_LINUX_DEFAULT is set tp “quiet.” Save & close the file then update GRUB.
sudo vi /etc/default/grub
$ sudo update-grub


sudo dd if=/dev/zero of=/EMPTY bs=1M
    $ sudo rm -f /EMPTY
    # Shutdown the machine
    $ sudo shutdown -h now
    
The next command will actually create our box. The directory where you run this command is where the box file will be created.

    
    $ vagrant package --base vagrant-{distro}-{version}
    


