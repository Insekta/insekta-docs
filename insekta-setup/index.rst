
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
#. The next step is to setup a new storage pool called ``insekta``, which is used later on to store VM images. This can either be achieved via ``virt-manager`` or by running the following commands on the insekta host:

    #. ``virsh pool-define-as insekta dir - - - - "/var/lib/libvirt/images/insekta/"``.
    #. ``virsh pool-build insekta``.
    #. ``virsh pool-start insekta``.
    #. ``virsh pool-autostart insekta``.

#. Upon running ``virsh pool-list --details`` you should see an output similar to the one below:
    ::
      
         Name     State    Autostart  Persistent    Capacity  Allocation   Available
        -----------------------------------------------------------------------------
         default  running  yes        yes         436.67 GiB    1.90 GiB  434.77 GiB
         insekta  running  yes        yes         436.67 GiB    1.90 GiB  434.77 GiB




Setting up the insekta libvirt image
------------------------------------
#. Install ``virt-manager`` on your local machine, e.g., by running ``apt install virt-manager`` on Debian.
#. Download a Debian image as previously done for the insekta host machine and store it in ``/var/lib/libvirt/images`` on the insekta host.
#. Setup a new Debian virtual machine using the wizard provided by ``virt-manager``. Make sure to check ``customize configuration before install`` to enable UEFI firmware and the i440FX chipest. You most likely want to turn on ``start vm on host boot`` as well.
#. Perform a normal Debian installation as done before.
#. Generate a new SSH key pair on the insekta host using ``ssh-keygen`` and setup SSH remote access as shown before such that you can connect from insekta host to the new insekta libvirtd image.
#. Lookup the IP address of the insekta libvirtd image by running ``ifconfig`` or ``ip a`` on this machine and connect to it from insekta host via SSH.
#. **TODO**: I think we have to switch to Debian Unstable (sid) due to python3.


Setting up openvpn
^^^^^^^^^^^^^^^^^^
Note that the following steps must be performed on the insekta libvirt image.

#. Install the following dependencies via ``apt install build-essential libvirt-dev qemu-kvm git virtualenv python-libvirt python3-libvirt python3 python3-dev python-dev pkg-config openvpn iptables python3-pip``.
#. Clone the ``insekta-vm`` repository to ``/opt`` and enter it via ``cd /opt/; git clone https://github.com/Insekta/insekta-vm.git; cd insekta-vm``.
#. Copy the openvpn directory from ``insekta-vm/insektavm/examples/openvpn/`` to ``/etc/openvpn`` via ``cp -r /opt/insekta-vm/insektavm/examples/openvpn/* /etc/openvpn``.
#. **TODO:** fix in GIT repository: ``chmod +x insekta-vm/insektavm/examples/openvpn/learn-address.sh``.
#. Create a system account for openvpn via ``useradd --system openvpn``.
#. Change the file ownership of ``/etc/openvpn`` to ``openvpn`` via ``chown -R openvpn /etc/openvpn``.
#. Enable the systemd service for openvpn via ``systemctl enable openvpn-server@server``.


Setting up the CA
"""""""""""""""""
#. Enter ``/usr/share/easy-rsa``, invoke ``make-cadir cadir`` and enter the created ``cadir`` directory via ``cd /usr/share/easy-rsa; make-cadir cadir; cd cadir``.
#. Adjust the certificate fields in file ``vars``, e.g., via ``vim /usr/share/easy-rsa/cadir/vars``. The fields that need to be adjusted can be found at the bottom of the file and a sample configuration might look as follows:
    ::
      
        # These are the default values for fields
        # which will be placed in the certificate.
        # Don't leave any of these fields blank.
        export KEY_COUNTRY="DE"
        export KEY_PROVINCE="Bayern"
        export KEY_CITY="Bamberg"
        export KEY_ORG="PSI"
        export KEY_EMAIL="services.psi@uni-bamberg.de"
        export KEY_OU="Insekta"

#. Create a symlink to openssl via ``ln -s openssl-1.0.0.cnf openssl.cnf``.
#. Source the ``vars`` file via ``source ./vars``.
#. Run ``./clean-all``.
#. Run ``./build-ca``. Note that this will ask you to confirm the previously configured certificate fields by pressing enter.
#. Run ``./build-key-server server``. This might take some time for generating the key pair. It will also ask you again to confirm the previously configured certificate fields. In addition, enter ``y`` for signing, ``y`` for committing, and ``n`` for not challenging.
#. Copy the generated certificate files to ``/etc/openvpn/server/`` via:
    ::

      cp keys/ca.crt /etc/openvpn/server/
      cp keys/ca.key /etc/openvpn/server/
      cp keys/server.crt /etc/openvpn/server/
      cp keys/server.key /etc/openvpn/server/

#. **TODO:** we need this for setting up insekta-web later on ``cp /etc/openvpn/server/ca.* /path/to/insekta-web/insekta/testenv/vpn/``
#. Finally, start the systemd service for openvpn via ``systemctl start openvpn-server@server``.
#. **TODO:** Not sure whether the previous command will run without errors so far.


Setting up NGINX as a reverse proxy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
NGINX is used as a reverse proxy to map the requests to the respective services. Note that the following steps must be performed on the insekta libvirt image.

#. Install dependencies via ``apt install nginx``.
#. Start the service via ``service nginx start``.
#. Copy the example nginx configuration file from the repository to ``/etc/nginx/sites-available`` and adapt  if necessary. You will most likely have to adjust the ``server_name`` field.
#. Create a symlink in ``/etc/nginx/sites-enabled`` that points to the file in ``/etc/nginx/sites-available`` via ``ln -s /etc/nginx/sites-available/filename /etc/nginx/sites-enabled/filename``.
#. You might have to remove the ``default`` symlink in ``/etc/nginx/sites-enabled``.
#. Restart nginx via ``service nginx restart`` such that the modified configuration is applied.


Setting up the insekta-vm component
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Note that the following steps must be performed on the insekta libvirt image.

#. Install pipenv via ``pip3 install pipenv``.
#. Use the provided example settings file: ``cp insektavm/insektavm/settings.py.example insektavm/insektavm/settings.py``
#. Run ``apt install python3-venv``.
#. Setup the virtualenv environment:

    #. Create a new virtual environment ``python3 -m venv /opt/insekta-vm/insektavm``.
    #. Spawn the virtualenv shell via ``source /opt/insekta-vm/insektavm/venv/bin/activate``.
    #. Install dependencies via ``pip install -r /opt/insekta-vm/requirements.txt``.
    #. We also need ``gunicorn`` for serving this application. To install run ``pip install gunicorn``.
    #. Invoke ``deactivate`` to leave the virtualenv shell.

#. Copy the systemd service file and adapt the paths if necessary via ``cp /opt/insekta-vm/insektavm/examples/systemd/insekta-vm.service /etc/systemd/system/``.
#. Create a system account for insekta via ``useradd --system insekta``.
#. Adapt the rights via ``chown -c insekta /opt/insekta-vm``.
#. Enable the service via ``systemctl enable insekta-vm.service`` and start it via ``systemctl start insekta-vm.service``.
#. Setup ``nginx`` as a reverse proxy (see the instructions above).
#. **TODO**: insekta libvirt image must have access to the host machine for the following step.
#. **TODO**: adapt the settings -> libvirtd_nodes (``qemu+ssh ..`` ) & ``VM_IMAGE_DIR``.
#. **TODO**: also allowed_hosts in ``settings.py``.
#. **TODO**: ``insekta-vm/insektavm/insektavm/settings.py:VM_IMAGE_DIR = '/opt/vm-import'`` .. ``mkdir /opt/vm-import`` .. probably also ``chown``.


Setting up the insekta-web component
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Note that the following steps must be performed on the insekta libvirt image.

#. Install dependencies via ``apt install git make wget python3 python3-pip unzip gettext curl sudo python3-venv``.
#. Install nodejs as npm via ``curl -sL https://deb.nodesource.com/setup_9.x | sudo -E bash -`` and ``apt install -y nodejs``.
#. If not already done before, install pipenv via ``pip3 install pipenv``.
#. Clone the repository via ``cd /opt/; git clone https://github.com/Insekta/insekta-web.git; cd insekta-web``.
#. Use the provided example configuration file via ``cp /opt/insekta-web/insekta/insekta/settings.py.example /opt/insekta-web/insekta/insekta/settings.py`` and adapt it.
    ::
      
        # domain names or IP address allowed to access this service. Use the machine's IP or the respective domain name.
        ALLOWED_HOSTS = ['420.420.420.420']
        # location where static files are located
        STATIC_ROOT = "/opt/insekta-static/insekta-web/static-root"
        # location where media files are located
        MEDIA_ROOT = "/opt/insekta-static/insekta-web/media-root" 
        # the IP and port of the VPN server
        VPN_SERVER = {'host': 'localhost', 'port': 1194}
        # the URI where the insekta-vm API can be found
        REMOTE_API_URL = 'http://localhost:8001/api/'
        # authentication for the insekta-vm API
        REMOTE_API_AUTH = ('api', 'mypassword')
        # code required to register a new insekta account at insekta-web
        INVITATION_CODE = 'supergeheim'

#. Setup the virtualenv environment and generate static files:

    #. Create a new virtual environment ``python3 -m venv /opt/insekta-web/insekta/venv``.
    #. Spawn the virtualenv shell via ``source /opt/insekta-web/insekta/venv/bin/activate``.
    #. Install dependencies via ``pipenv install``.
    #. We also need ``gunicorn`` for serving this application. To install run ``pip install gunicorn``.
    #. Generate the static files by invoking the Makefile via ``cd /opt/insekta-web/insekta; make all``.
    #. Collect and copy the static files to the previously defined location via ``cd /opt/insekta-web/insekta; python manage.py collectstatic``.
    #. Invoke ``deactivate`` to leave the virtualenv shell.
    
#. Setup and configure NGINX as a reverse proxy:

    #. Install dependencies via ``apt install nginx``.
    #. Start the service via ``service nginx start``.
    #. Copy the example nginx configuration file from the repository to ``/etc/nginx/sites-available/insekta-web`` and adapt  if necessary. You will most likely have to adjust the ``server_name`` field.
    #. Create a symlink ``insekta-web`` in ``/etc/nginx/sites-enabled`` that points to the ``/etc/nginx/sites-available/insekta-web`` via ``ln -s /etc/nginx/sites-available/insekta-web /etc/nginx/sites-enabled/insekta-web``.
    #. You might have to remove the ``default`` symlink in ``/etc/nginx/sites-enabled``.
    #. Restart nginx via ``service nginx restart`` such that the modified configuration is applied.

#. If not already done before, create a system account for insekta via ``useradd --system insekta``.
#. Adapt the rights via ``chown -c insekta /opt/insekta-web``.
#. Use the provided systemd service file and adapt if necessary.
#. Enable the service via ``systemctl enable insekta-web.service`` and start it via ``systemctl start insekta-web.service``.



