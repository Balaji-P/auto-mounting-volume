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

#. Copy the public key to the server, eg. ``$ scp -P 7331 ~/.ssh/USERNAME_rsa.pub USERNAME@SERVER_ADDRESS:/home/USERNAME/``
#. Ensure the correct SELinux contexts are set: ``$ restorecon -Rv ~/.ssh``

On the CentOS server:
+++++++++++++++++++++

#. Append the public key to the profile's ``authorized_keys`` SSH file and set file permissions:

   ::

        $ cat USERNAME_rsa.pub >> ~/.ssh/authorized_keys
        $ chmod 700 ~/.ssh
        $ chmod 600 ~/.ssh/authorized_keys

#. Ensure the correct SELinux contexts are set: ``$ restorecon -Rv ~/.ssh``


Rename Server Host
------------------

#. Open ``/etc/sysconfig/network`` file and modify the ``HOSTNAME=`` value to match your FQDN host name: ``$ sudo vi /etc/sysconfig/network``.
#. Open ``/etc/hosts`` file and modify any line referencing the old HOSTNAME to point to the new HOSTNAME.
#. Run the ``hostname`` command to see the current/old HOSTNAME and then run again with the first argument to set the new HOSTNAME: ``$ sudo hostname`` and ``$ sudo hostname NEW_HOSTNAME``.
#. Restart the networking service: ``$ sudo /etc/init.d/network restart``.