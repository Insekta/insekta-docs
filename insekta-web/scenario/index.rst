
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

