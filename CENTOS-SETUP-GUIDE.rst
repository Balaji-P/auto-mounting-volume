CentOS Server Setup
===================

Initial Setup
-------------

#. Copy ``firewall`` shell script and modify it accordingly. Run it.
#. Run ``$ su -c 'visudo'`` and add ``USERNAME ALL=(ALL) ALL`` for each allowed USERNAME.
#. Update the system: ``$ sudo yum upgrade``.
#. Add the RHEL EPEL Repo:
   
   ::
   
        $ wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        $ sudo rpm -Uvh epel-release-6*.rpm

#. Setup SSH by editing ``/etc/ssh/sshd_config`` and adding/editing the following lines. Note each allowed SSH user must replace USERNAME1, USERNAME2, etc. below.

   ::

        Port 7331
        PermitRootLogin no
        UseDNS no
        AllowUsers USERNAME1 USERNAME2

#. Now restart the SSH service: ``$ sudo service sshd restart``.
#. Enable the SSH service: ``$ sudo chkconfig sshd on``.


SSH Key Setup
-------------

On your local PC:
+++++++++++++++++

#. Create a key:

   ::
        
        $ cd ~/.ssh
        $ ssh-keygen -t rsa -f USERNAME_rsa
        $ chmod 700 ~/.ssh
        $ chmod 600 ~/.ssh/USERNAME_rsa

#. Copy the public key to the server, eg. ``$ scp -P 7331 ~/.ssh/USERNAME_rsa.pub USERNAME@SERVER_ADDRESS:/home/USERNAME/``.
#. Ensure the correct SELinux contexts are set: ``$ restorecon -Rv ~/.ssh``.

On the CentOS server:
+++++++++++++++++++++

#. Append the public key to the profile's ``authorized_keys`` SSH file and set file permissions:

   ::

        $ cat USERNAME_rsa.pub >> ~/.ssh/authorized_keys
        $ chmod 700 ~/.ssh
        $ chmod 600 ~/.ssh/authorized_keys

#. Ensure the correct SELinux contexts are set: ``$ restorecon -Rv ~/.ssh``.


Rename Server Host
------------------

#. Open ``/etc/sysconfig/network`` file and modify the ``HOSTNAME=`` value to match your FQDN host name: ``$ sudo vi /etc/sysconfig/network``.
#. Open ``/etc/hosts`` file and modify any line referencing the old HOSTNAME to point to the new HOSTNAME.
#. Run the ``hostname`` command to see the current/old HOSTNAME and then run again with the first argument to set the new HOSTNAME: ``$ sudo hostname`` and ``$ sudo hostname NEW_HOSTNAME``.
#. Restart the networking service: ``$ sudo /etc/init.d/network restart``.

Auto-mounting External Drive
----------------------------
#. Find the device by its mount point: ``$ sudo fstab -l``.
#. Get the devices associated UUID: ``$ ls -l /dev/disk/by-uuid`` (this will allow you to plugin the device into any other port on the computer).
#. Create the mount directory: ``$ sudo mkdir /mnt/rb1tb``.
#. Edit the fstab file: ``$ sudo vi /etc/fstab``.
#. Add the following line to the file: ``UUID=43d65df3-ad4e-447e-ac97-a992c1dbe427  /mnt/rb1tb  ext4  defaults  1  1``.


Configuration of Python
-----------------------
#. Install easy_install: ``$ sudo yum install python-setuptools``.
#. Install pip: ``$ sudo easy_install pip``.

Adding a Script to Cron
-----------------------
#. Set your script to be executable: ``$ chmod 755 script.py``.
#. Create a symbolic link to the file in cron: ``$ ln -s /PATH/TO/script.py /etc/cron.hourly/``.

Installing Git-Annex
-----------------

**NOTE:** If you are using the steps below for your client computer with Fedora/CentOS/RHEL, it is recommended to replace the references to ``/usr/local/bin`` with ``$HOME/bin``. Note also that if you do this, you do not need to run the associated command with ``sudo``.

#. Install dependencies via YUM: ``$ sudo yum install haskell-platform gnutls-devel libgsasl-devel libxml2-devel zlib-devel ghc-zlib-devel libidn-devel``.
#. Update cabal: ``$ cabal update``.
#. If needed, upgrade cabal: ``$ cabal install cabal-install``.
#. Install c2hs: ``$ sudo cabal install c2hs --bindir=/usr/local/bin/``.
#. Create symbolic link for ``c2hs`` within ``/usr/sbin`` so you can run it as sudo or root: ``$ sudo ln -s /usr/local/bin/c2hs /usr/sbin/``.
#. Finally, install git-annex: ``$ sudo cabal install git-annex --bindir=/usr/local/bin/``.
#. If you encounter any errors with the installation, with regards to the Glasgow Haskell Compiler (ghc) see below.

Error with ghc While Installing Git-Annex
++++++++++++++++++++++++++++++++++++++++++
You will have to compile the newest version by going to the website http://justhub.org/download (as recommended on http://www.haskell.org/platform/linux.html).

#. Download the rpm for CentOS 6: ``$ wget http://sherkin.justhub.org/el6/RPMS/x86_64/justhub-release-2.0-4.0.el6.x86_64.rpm``.
#. Add the rpm to Yum: ``$ sudo -ivh justhub-release-2.0-4.0.el6.x86_64.rpm``.
#. Now install Haskell: ``$ sudo yum install haskell``.
#. You might have received an error about the existing compiler, remove it using: ``$ sudo yum remove [package(s)]``.