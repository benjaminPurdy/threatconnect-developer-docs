Retrieve URLs
^^^^^^^^^^^^^

Retrieving a Single URL
"""""""""""""""""""""""

This example demonstrates how to retrieve a URL Indicator from the ThreatConnect platform. The ``add_indicator`` filter allows us to specify the specific Indicator we would like to retrieve.

.. code-block:: python
    :emphasize-lines: 13-16,19-20

    # replace the line below with the standard, TC script heading described here:
    # https://docs.threatconnect.com/en/latest/python/quick_start.html#standard-script-heading
    ...

    tc = ThreatConnect(api_access_id, api_secret_key, api_default_org, api_base_url)

    # instantiate Indicators object
    indicators = tc.indicators()

    owner = 'Example Community'
    indicator = 'http://example.com/test/clickme.html'

    # set a filter to retrieve a specific URL Indicator
    filter1 = indicators.add_filter()
    filter1.add_owner(owner)
    filter1.add_indicator(indicator)

    try:
        # retrieve the Indicators
        indicators.retrieve()
    except RuntimeError as e:
        print('Error: {0}'.format(e))
        sys.exit(1)

    try:
        # prove there is only one Indicator retrieved
        assert len(indicators) == 1
    except AssertionError as e:
        # if the indicator doesn't exist in the given owner, raise an error
        print('AssertionError: The indicator {0} was not found in the "{1}" owner. '.format(indicator, owner) +
              'Try changing the `owner` variable to the name of an owner in your instance of ThreatConnect ' +
              'or make sure that the {0} indicator specified by the `indicator` '.format(indicator) +
              'variable exists in that owner.')
        sys.exit(1)

    # if the URL was found, print some information about it
    for indicator in indicators:
        print(indicator.indicator)
        print(indicator.weblink)
        print('')

.. note:: If you get an ``AssertionError`` when running this code, you likely need to change the name of the ``owner`` variable so that it is the name of an owner in your instance of ThreatConnect and/or you need to change the ``indicators`` variable so that it is an Indicator that exists in the given owner.

Retrieving Multiple URLs
""""""""""""""""""""""""

This example demonstrates how to retrieve all URL Indicators in the default organization. The ``IndicatorType.URLS`` which is passed into the filter specifies which Indicator type we want to retrieve.

.. code-block:: python
    :emphasize-lines: 1-2,13-14,17-18

    # this import allows us to specify which Indicator type we want to retrieve
    from threatconnect.Config.IndicatorType import IndicatorType

    # replace the line below with the standard, TC script heading described here:
    # https://docs.threatconnect.com/en/latest/python/quick_start.html#standard-script-heading
    ...

    tc = ThreatConnect(api_access_id, api_secret_key, api_default_org, api_base_url)

    # instantiate Indicators object
    indicators = tc.indicators()

    # set a filter to retrieve URL Indicators
    filter1 = indicators.add_filter(IndicatorType.URLS)

    try:
        # retrieve the Indicators
        indicators.retrieve()
    except RuntimeError as e:
        print('Error: {0}'.format(e))
        sys.exit(1)

    # iterate through the retrieved URLs and print them
    for indicator in indicators:
        print(indicator)
