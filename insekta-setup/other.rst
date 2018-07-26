This & That
===========

Gain access to the virtual machine via virsh console
----------------------------------------------------
#. Invoke the following two commands within the guest VM:

  - ``systemctl enable serial-getty@ttyS0.service``
  - ``systemctl start serial-getty@ttyS0.service``


#. On the guest VM replace the following two lines in the file ``/etc/default/grub``

    ::

        GRUB_CMDLINE_LINUX_DEFAULT="quiet"
        #GRUB_TERMINAL=console  

    by

    ::

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0"
        GRUB_TERMINAL="serial console"

#. Then run ``update-grub`` in the guest VM.
#. Finally, connect to the guest VM using ``virsh console name-of-your-vm``.


Scenario VM Life Cycle Management
---------------------------------
While a scenario page is open, the web page repeatedly sends AJAX requests to the insekta-vm server to ensure that VMs are not killed while they are in use. After closing a scenario tab, insekta-vm sets the spawned scenario VM to ``expired`` after a certain number of minutes. To remove expired VMs manually, spawn a venv shell at ``insekta-vm/insektavm`` and run ``$ python manage.py destroy_expired_vms`` to remove all expired VMs. This task can and should be automated, e.g., using a a time-based job scheduler like ``cron``.


Network Bridge Definition
-------------------------
#. Warning: this section needs further testing and documentation.
#. Debug note: try to ``ping`` the target host from some other machine and run ``tcpdump -a icmp`` on the target host. Then keep on adjusting the routes until the ping is received.
#. To forward network traffic to the running insekta virtual machines, append the following block to the end of file ``/etc/network/interfaces`` on the insekta host machine:
    
    ::
      
        # Bridge for VMs
        auto br0
        iface br0 inet static
          hwaddress ether ff:ff:ff:ff:ff:ff # taken from ifconfig/ip a .. the one from the actual network device
          address 420.420.420.420 # the adress that this device got .. see ifconfig
          netmask 255.255.255.224 # network mask .. ask sysadmin (i.e., 27)
          gateway 420.420.420.420 # given by sysadmin or taken from ip r
          dns-nameservers 1.1.1.1 9.9.9.9 # cloudflare DNS & Quad9 DNS service
          bridge_ports mynetwadapter # taken from ifconfig or ip a
          bridge_stp on
          bridge_fd 0
