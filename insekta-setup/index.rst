
The idiot proof guide to setting up Insekta from scratch
========================================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:


This installation guide provides step-by-step instructions on how to setup Insekta from scratch.

Requirements
------------
-  A physical machine (called ``insekta host``) on which Insekta will be installed. Initial physical access is required for setting up the operating system. A brief guide on setting up SSH access is given below such that physical access is no longer necessary.
-  Another physical machine (called ``local machine``) for setting up Insekta from remote. Such a device, e.g., your laptop, is not necessary if you want to perform the setup in front of the ``insekta host``. Note that this guide assumes remote setup.
-  A working internet connection.
-  A USB flash drive with at least 1 GB.
-  Roughly 60 minutes of free time.


Installing Debian on the ``insekta host``
-----------------------------------------
#. Download the latest Debian image (preferably a netinst version) from `https://www.debian.org/CD/netinst/ <https://www.debian.org/CD/netinst/>`_. At the time of writing this guide you could fetch the ISO file by running ``wget -c https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-9.4.0-amd64-netinst.iso`` on your local machine.
#. Create a bootable USB flash drive using the ISO file from the previous step. To do so run ``sudo dd if=~/debian-9.4.0-amd64-netinst.iso of=/dev/sdb42`` on your local machine. Note that ``of`` must be assigned the location of your USB flash drive. You can lookup your already mounted devices via ``df`` and use ``sudo fdisk -l`` for unnmounted devices. **Double-check the parameters before running this command.**
#. Plugin the bootable USB flash drive to the ``insekta host``, power up the machine and boot from the USB flash drive. You might have to turn off ``Secure Boot`` in the
   BIOS before.
#. Perform a typical debian installation, but make sure to enable ``SSH server``. You can disable ``Print server``; a desktop manager is not required either.
#. Finally, reboot the machine, boot from the hard disk, and remove the USB flash drive.