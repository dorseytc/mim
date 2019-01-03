Vagrant.configure("2") do |config|
# -*- mode: ruby -*-
# vi: set ft=ruby :
  config.vm.define "roth" do |roth|
     roth.vm.box = "centos/7"
     roth.vm.network :private_network, ip: "10.0.0.101"
     roth.vm.provision "shell", inline: <<-SHELL
          echo =======  install git
          yum -y -q install git
          echo ======= install bunches
          yum -y -q install gcc kernel-devel kernel-headers dkms make bzip2 perl wget rsync 
          echo =======  check for Downloads directory
          if ! [ -d /home/vagrant/Downloads ]; then
             echo ======== create Downloads directory
             mkdir Downloads
          else 
             echo ======== Downloads directory already exists
          fi
          echo ======= check for epel rpm
          if ! [ -f /home/vagrant/Downloads/epel-release_latest-7.noarch.rpm ]; then
             echo ======= wget epel rpm
             wget -nv -O /home/vagrant/Downloads/epel-release-release-7.noarch.rpm https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
          else
             echo ======= epel rpm already exists
          fi 
          echo ======= install rpm epel
          yum -y -q install epel-release-latest-7.noarch.rpm
          echo ======= install additional
          yum -y -q install ansible ansible-galaxy
          echo ======= check for vboxguest vboxsf
          INSTALLED=$(lsmod | grep vboxguest | awk '{ print $4 }')
          echo ======= INSTALLED=$INSTALLED
          if [ "$INSTALLED." = "vboxsf." ]; then
               echo ======= VBoxAdditions is already installed
          else
               echo ======= check for VBox Additions iso 
               if ! [ -f /home/vagrant/Downloads/VBoxGuestAdditions_5.2.12.iso ]; then
                  echo =======  wget VBox Additions iso
                  wget -nv -O /home/vagrant/Downloads/VBoxGuestAdditions_5.2.12.iso  http://download.virtualbox.org/virtualbox/5.2.12/VBoxGuestAdditions_5.2.12.iso
               else
                  echo ======= iso already exists
               fi 
               echo =======  set KERN_DIR
               KERN_DIR=/usr/src/kernels/$(uname -r)/build 
               echo =======  KERN_DIR = $KERN_DIR
               echo =======  check for VBox Additions mount point 
               if ! [ -d /media/VirtualBoxGuestAdditions ]; then
                    echo =======  create directory for VBox Additions mount
                    mkdir /media/VirtualBoxGuestAdditions
               fi
               echo =======  mount VBox Additions
               mount -t iso9660 -o loop /home/vagrant/Downloads/VBoxGuestAdditions_5.2.12.iso /media/VirtualBoxGuestAdditions
               echo =======  run VBox Additions
               /media/VirtualBoxGuestAdditions/VBoxLinuxAdditions.run
               echo =======  usermod 
               sudo usermod -a -G vboxsf vagrant
               echo ======= checking install
               INSTALLED=$(lsmod | grep vboxguest | awk '{ print $4 }') 
               echo ======= installed file system is $INSTALLED
               
               if [ "$INSTALLED." = "vboxsf." ]; then
                   echo ===== SUCCESS:  vboxsf is installed
               else
                   echo ===== FAILURE:  vboxsf is not installed
               fi
          fi
          echo ======= add vagrant to vboxsf group
          sudo usermod -a -G vboxsf vagrant
          echo ======= modprobe
          modprobe -a vboxguest vboxsf 
          echo ======= vagrant mount
          MOUNT=$(sudo mount | grep vagrant | awk '{ print $1 "-" $3 "-" $5 }' | sort -u )
          echo MOUNT = $MOUNT
          if [ "$MOUNT." = "vagrant-/vagrant-vboxsf." ]; then
             echo ======= vagrant mounted successfully
          else
             echo ======= mount
             sudo mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant
             MOUNT=$(sudo mount | grep vagrant | awk '{ print $1 "-" $3 "-" $5 }' | sort -u )
             echo MOUNT = $MOUNT
          fi
          if [ "$MOUNT." = "vagrant-/vagrant-vboxsf." ]; then
             echo ======= yes vagrant mounted successfully
          else
             echo vagrant not mounted correctly
             echo ensure that a vagrant shared folder is defined in vbox for this vm
             echo create one manually if one was not created by vbox
             echo other commands to try:  
             echo \$ vagrant reload  
             echo still further commands to try:
             echo \$ vagrant halt 
             echo \$ vagrant up --provision
          fi 
          echo ======= end shell
          SHELL
  config.vm.synced_folder ".", "/vagrant", owner: "vagrant", group: "vagrant"
  end
end
  
# vagrant plugin install vagrant-vbguest
# vagrant vbguest
# modprobe -a vboxguest vboxsf # vagrant plugin install vagrant-vbguest
# vagrant vbguest
