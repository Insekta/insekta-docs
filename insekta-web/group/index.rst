
Insekta-Web Group Management
===================================

Creating Groups
-------------------------

Log into the Django admin backend located at https://your-insekta-web.host/admin using a previously created superuser account. You can access the rights management for groups' using the item ``Groups`` in section ``Authentication and Authorization``. There, you can specify new groups and grant them certain permissions. Make sure to save your group configuration in the end.

Using the item ``Users`` in section ``Account`` you can specify which group a user belongs to. Select the ``Staff status`` checkbox to allow a user to log into the admin site.

For example, you can establish a new group for users that shall be able to create and update scenarios. Hint: you can filter the rights with ``scenario`` to find all scenario relevant permissions. Afterwards, you can add each user to this group as described above.
