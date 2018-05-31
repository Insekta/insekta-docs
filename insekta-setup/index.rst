
The idiot proof guide to setting up Insekta from scratch
========================================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:


This installation guide provides step-by-step instructions on how to setup Insekta from scratch.

Requirements
------------
-  A physical machine (called ``insekta host``) on which Insekta will be installed. Initial physical access is required for setting up the operating system. A brief guide on setting up SSH access is given below such that physical access is no longer necessary.
-  Another physical machine (called ``local machine``) for setting up Insekta from remote. Such a device, e.g., your laptop, is not necessary if you want to perform the setup in front of the insekta host. Note that this guide assumes remote setup.
-  A working internet connection.
-  A USB flash drive with at least 1 GB.
-  Roughly 60 minutes of free time.


Installing Debian on the insekta host
-----------------------------------------
#. Download the latest Debian image (preferably a netinst version) from `https://www.debian.org/CD/netinst/ <https://www.debian.org/CD/netinst/>`_. At the time of writing this guide you could fetch the ISO file by running ``wget -c https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-9.4.0-amd64-netinst.iso`` on your local machine.
#. Create a bootable USB flash drive using the ISO file from the previous step. To do so run ``sudo dd if=~/debian-9.4.0-amd64-netinst.iso of=/dev/sdb42`` on your local machine. Note that ``of`` must be assigned the location of your USB flash drive. You can lookup your already mounted devices via ``df`` and use ``sudo fdisk -l`` for unnmounted devices. **Double-check the parameters for correctness before running this command.**
#. Plugin the bootable USB flash drive to the insekta host, power up the machine and boot from the USB flash drive. You might have to turn off ``Secure Boot`` in the
   BIOS before.
#. Perform a typical debian installation, but make sure to enable ``SSH server``. You can disable ``Print server``; a desktop manager is not required either.
#. Finally, reboot the machine, boot from the hard disk, and remove the USB flash drive.


Setting up remote SSH access
----------------------------
#. Login to the insekta host as ``root`` and create the ``.ssh`` directory via ``mkdir /root/.ssh``.
#. Create an empty ``authorized_keys`` file via ``touch /root/.ssh/authorized_keys``.
#. Adjust the file mode via ``chmod 600 /root/.ssh/``.
#. Append your public key to ``/root/.ssh/authorized_keys``, e.g., with your editor of choice, i.e., ``vim``. Hence, install ``vim`` via ``apt install vim``. You can read out the public key of your ``local machine`` via ``cat ~/.ssh/id_rsa.pub`` (at least this is the default location). In case that this file does not exist you can create a key pair by running ``ssh-keygen``.
#. Hint: instead of manually writing down your public key into the ``authorized_keys`` file, you can copy your public key on a USB flash drive, mount it on the insekta host and copy your key to the ``authorized_keys`` file. Note that you might have to manually mount the USB flash drive using ``fdisk -l`` to list the partition table and the ``mount`` command to actually mount the USB flash drive to a folder. For copying the public key to the ``authorized_keys`` file you can run ``vim /the/path/to/id_rsa.pub /root/.ssh/authorized_keys -O`` to display both files next to each other. Then, enter visual mode vie ``v``, highlight your public key and press ``y`` to copy/yank, switch panes via ``CTRL+w`` + ``w``, and paste via ``p``, then quit and save both files via ``:wq``.
#. To get the IP of the insekta host run ``ip a`` or ``ifconfig`` (you might have to run ``apt install net-tools`` before).
#. On your local machine connect to the insekta host via ``ssh root@420.420.420.420`` (replace ``420.420.420.420`` with the IP obtained in the previous step).
#. In case that you do not use the default SSH key location, make sure to adapt your SSH config within file ``~/.ssh/config``.


Setting up libvirt on the insekta host
-------------------------------------

#. Install libvirt dependencies via ``apt install libvirt-daemon-system``.
#. Install OVMF for UEFI image support via ``apt install ovmf``.
#. **TODO:** setup a network bridge by modifying the file ``/etc/network/interfaces``.
#. Reload the network configuration via ``ifdown yourinterface && ifup yourinterface``.
#. TODO: storage pool.


Setting up the insekta libvirt image
------------------------------------
#. Install ``virt-manager`` on your local machine, e.g., by running ``apt install virt-manager`` on Debian.
#. Download a Debian image as previously done for the insekta host machine and store it in ``/var/lib/libvirt/images``.
#. Setup a new Debian virtual machine using the ``virt-manager`` wizard. Make sure to check ``customize configuration before install`` to enable UEFI firmware and i440FX chipest. You most likely want to turn on ``start vm on host boot`` as well.
#. Perform a normal Debian installation as done before and generate a new SSH key pair using ``ssh-keygen``.
#. Setup SSH remote access as shown before such that you can connect from insekta host to the new insekta libvirtd image.
#. Lookup the IP address of the insekta libvirtd image by running ``ifconfig`` or ``ip a`` and connect to it via SSH.


Setting up openvpn
^^^^^^^^^^^^^^^^^^
**TODO**


Setting up the insekta-vm component
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
**TODO**


Setting up the insekta-web component
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
**TODO**




