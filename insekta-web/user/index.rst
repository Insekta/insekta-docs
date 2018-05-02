
Insekta-Web User Management
===================================

Creating Admins
-------------------------

To create an admin (superuser) you need a Python environment. You can create a test environment with the 
Makefile or you create a virtual environment with the module 'virtualenv'. Afterwards, you have to execute 
the following command to create an admin (be sure that you have a Python environment and you installed the 
required packages within the Pipfile):

.. code-block:: bash

   ./manage.py createsuperuser

Then you have to enter your credentials, like username, password and email address. Finally, the admin 
account is created.
