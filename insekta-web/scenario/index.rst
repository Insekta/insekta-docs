
Insekta-Web Scenario Management
===================================

A scenario is basically a directory containing the description of the scenario, some metadata and static files,
like images.

Scenarios in detail
------------------------- 

The directory structure will look like:

* example

  * scenario.html
  * metadata.json
  * static

    * insekta.png

scenario.html
^^^^^^^^^^^^^^

This file contains the description for the scenario. It describes what the
hacker needs to do to solve the scenario. Additionally it contains explanations of 
what's going on. The description is written in html. Therefore, you can use html tags like:

``headings``

.. code-block:: html

    <h3>Allgemeines</h3>

``paragraphs``

.. code-block:: html

    <p data-comment-id="hncopwog">
        Ein Thema wird in Insekta intern als "Szenario" bezeichnet. Dies ermöglicht 
        die Abgrenzung zwischem dem Thema, das inhaltlich behandelt wird (das "Thema") 
        und dem Thema, das die technische Einheit (als Bündel von mehreren Dateien) 
        darstellt (das "Szenario").
    </p>

``images``

.. code-block:: html

    <p><img src="{{ media('insekta.png') }}" alt="Insekta-Logo" title="Insekta-Logo"></p>

You can also set up challenges in form of multiple/single choice or fill-in-the-blank questions:

``single-choice``

.. code-block:: html

    {% call task(identifier='die_erde', type='single_choice', title='Astronomie') %}
        <p>Die Erde ist ...</p>
        {% call choice(name='flat') %}Eine Scheibe{% endcall %}
        {% call choice(name='sphere', correct=True) %}Eine Kugel{% endcall %}
        {% call choice(name='other') %}Etwas anderes{% endcall %}
    {% endcall %}

You have to mark the correct answer with a flag ('correct=True').

``multiple-choice``

.. code-block:: html

    {% call task(identifier='cookiemonster', type='multiple_choice') %}
        <p>Welche Aussagen treffen auf das Cookiemonster zu?</p>
        {% call choice(name='cookies', correct=True) %}Er mag Kekse{% endcall %}
        {% call choice(name='nocookies', correct=False) %}Er mag keine Kekse{% endcall %}
        {% call choice(name='morecookies', correct=True) %}Er möchte mehr Kekse{% endcall %}
    {% endcall %}

``fill-in-the-blank``

.. code-block:: html

    {% call task(identifier='hitchhiker', type='question') %}
        <p>Was gibt der Supercomputer "Deep Thought" auf <em>die</em> Frage als Antwort?</p>
        {{ answer(expected='42') }}
    {% endcall %}

Other functionalities which can be used:

``source code highlighting``

.. code-block:: html

    {% call code(language='python') %}
    def fib(n):
        a, b = 0, 1
        while n > 0:
            a, b = b, a + b
            n -= 1
        return a
    {% endcall %}

``integrate JavaScript and CSS``

.. code-block:: html

    {% call code(language='json') %}
    {
        "static": {
            "css": ["example.css"],
            "js": [["blockcipher", "prp.js"], "paddingoracle.js"]
        },
    }
    {% endcall %}

``virtual machine``

IP-address of the started virtual machine:

.. code-block:: html

    {% call code(language='html+jinja') %}
        <a href="http://{{ vm_ip('main') }}">Webseite auf der VM</a>
    {% endcall %}

If the machine is not started, then a placeholder is used.

You own IP-address:

.. code-block:: html

    {% call code(language='html+jinja') %}
        <p>Deine IP im VPN ist {{ vpn_ip }}</p>
    {% endcall %}

Check if virtual machine is started:

.. code-block:: html

    {% call code(language='html+jinja') %}
        {% if vms %}
            <p>Deine virtuelle Maschine wurde gestartet, durch erreichst sie unter {{ vm_ip('main') }}.</p>
        {% else %}
            <p>Bitte starte die virtuellen Maschinen, um die folgende Aufgabe zu lösen.</p>
        {% endif %}
    {% endcall %}

A whole example of a 'scenario.html' is available :download:`here <example.html>`.

metadata.json
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This file contains some metadata about the scenario. It is valid json and
looks like:

.. literalinclude:: metadata.json
   :language: JSON

``title``
   The title of the scenario.

``required_components``
      

``vm_resource``
   The name of the vm resource which is started (configured in insekta-vm).


static
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Put any files in this directory and you can reference them in your ``scenario.html``. For example, see above
how to reference images within the ``scenario.html``.

Loading Scenarios
------------------------------

Registering is easy:
   
.. code-block:: bash

   ./manage.py loadscenario <name of your scenario>

If you want to update the image, just call it again. The name of your scenario is the name of the folder.
You also have to use this name as key in process of setting up the scenario within the admin pages.

.. warning::
   Updating a scenario destroys all existing domains that belong to this
   scenario. However, submitted secrets are not lost.


Create Scenario
------------------------------

First of all, you have to create a Scenario. That's can be done after logging in as administrator on insekta-web site. Then, you have to choose
'Admin' tab, scroll down to the group of functions called 'Scenarios' and click on option 'Scenarios'. You suppose to see the view which is 
presented on picture below.

.. image:: /_static/scenarios_1.JPG
  :width: 400
  :alt: Add scenario

After clicking on 'ADD SCENARIO' button, you will get next view:

.. image:: /_static/scenarios_2.JPG
  :width: 400
  :alt: Fill mandatory fields

You have to fill mandatory fields in the following way:

.. code-block:: bash

   Key: <name of your scenario>
   Title: Beispielszenario <for example>
   Num tasks: <arbitarily>
   Enabled: <checked>

After that, by clicking on the 'Save' button 'Scenario' is created and you will get next view:

.. image:: /_static/scenarios_3.JPG
  :width: 400
  :alt: List of scenarios


  Create Course
------------------------------

If you want to be able to create a 'Scenario Group', first of all, you have to create a Course. Option for this action is in the same fild as 'Scenarios' option. After you click on th e 'Courses' option, you suppose to see the view which is presented on picture below.

.. image:: /_static/course_1.JPG
  :width: 400
  :alt: Add course

After clicking on 'ADD SCENARIO' button, you will get next view: In that view you have to fill mandatory(red) fields and don't forget to check enable checkbox.

.. image:: /_static/course_2.JPG
  :width: 400
  :alt: Fill mandatory fields

After that, by clicking on the 'Save' button 'Course' is created and you will get next view:

.. image:: /_static/course_3.JPG
  :width: 400
  :alt: List of courses

Create Scenario Group
------------------------------

Finally, after 'Scenario' and 'Course' has been made, you are able to create a 'Scenario group'. Option for this action is in the same fild as 'Scenarios' and 'Courses'. After you click on the 'Scenario groups', you suppose to see the view which is presented on picture below.

.. image:: /_static/groups_1.JPG
  :width: 400
  :alt: Add Scenario group

After clicking on 'ADD SCENARIO GROUP' button, you will get next view. In that view you have to fill mandatory(red) fields:

.. image:: /_static/groups_2.JPG
  :width: 400
  :alt: Fill mandatory fields

  As you can see, two mandatory fields requires selection of the 'Course' and 'Scenario'. That's the reason because this two entities suppose to be created before 'Scenario group'. After that, by clicking on the 'Save' button 'Scenario group' is created and you will get next view:

  .. image:: /_static/groups_3.JPG
  :width: 400
  :alt: List of Scenario groups