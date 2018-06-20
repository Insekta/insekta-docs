Other
=====


Further TODOs
-------------
#. **TODO**: iptables needs to be set up in order to forward traffic from vpn to libvirt. An example configuration can be found in the examples directory. This can be placed in ``/etc/network/if-up.d``.
#. **TODO**: Additionally, the following command needs to be ran once: ``sysctl -w net.ipv4.ip_forward=1``.
#. **TODO**: Setting up a network bridge for ?.
#. **TODO**: Setup the routing information at openVPN and libvirt.


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
#. Connect to the guest VM via ``virsh console name-of-your-vm``.


Setting up routing information
------------------------------
#. **TODO**: This is still an open issue!
#. Debug note: Try to ``ping`` the target host from some other machine and run ``tcpdump -a icmp`` on the target host. Then keep on adjusting the routes until the ``ping`` is received.


Network Bridge Definition
-------------------------
#. **TODO**: This does not work yet!
#. Append the following block to the end of file ``/etc/network/interfaces`` on the insekta host machine:
    
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
