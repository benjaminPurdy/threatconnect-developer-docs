Update Victim Attributes
""""""""""""""""""""""""

The code snippet below demonstrates how to update a Victim's attribute. This example assumes there is a Victim with an ID of ``123456`` in the target owner. To test this code snippet, change the ``victim_id`` variable to the ID of a victim in your owner.

.. code-block:: python
    :emphasize-lines: 37,39-40

    # replace the line below with the standard, TC script heading described here:
    # https://docs.threatconnect.com/en/latest/python/quick_start.html#standard-script-heading
    ...

    tc = ThreatConnect(api_access_id, api_secret_key, api_default_org, api_base_url)

    # define the ID of the victim we would like to retrieve
    victim_id = 123456

    # create a victims object
    victims = tc.victims()

    # set a filter to retrieve the victim with the id: 123456
    filter1 = victims.add_filter()
    filter1.add_id(victim_id)

    try:
        # retrieve the Victims
        victims.retrieve()
    except RuntimeError as e:
        print('Error: {0}'.format(e))
        sys.exit(1)

    # iterate through the Victims
    for victim in victims:
        print(victim.name)

        # load the victim's attributes
        victim.load_attributes()

        # iterate through the victim's attributes
        for attribute in victim.attributes:
            print(attribute.id)

            # if the current attribute is a description attribute, update the value of the description
            if attribute.type == 'Description':
                victim.update_attribute(attribute.id, 'Updated Description')

        # commit the changes
        victim.commit()

.. note:: In the prior example, no API calls are made until the ``commit()`` method is invoked.
