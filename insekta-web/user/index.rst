
Insekta-Web User Management
===================================

Creating a superuser account
----------------------------

Navigate into your local insekta-web installation and spawn a virtual environment shell using the command depicted below: 

.. code-block:: bash

   source venv/bin/activate

Afterwards, invoke the ``settings.py`` file with the target ``createsuperuser``:

.. code-block:: bash

   ./manage.py createsuperuser

Subsequently, enter your credentials, i.e., username, email address, and password, to complete the account creation. Finally, leave the virtual environment shell using the following command:

.. code-block:: bash

   deactivate