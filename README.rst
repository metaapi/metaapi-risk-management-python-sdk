MetaApi risk management API for Python (a member of `metaapi.cloud <https://metaapi.cloud>`_ project)
#####################################################################################################

MetaApi risk management API is a member of MetaApi project (`https://metaapi.cloud <https://metaapi.cloud>`_),
a powerful cloud forex trading API which supports both MetaTrader 4 and MetaTrader 5 platforms.

MetaApi is a paid service, however API access to one MetaTrader account is free of charge.

The `MetaApi pricing <https://metaapi.cloud/#pricing>`_ was developed with the intent to make your charges less or equal
to what you would have to pay for hosting your own infrastructure. This is possible because over time we managed to heavily
optimize our MetaTrader infrastructure. And with MetaApi you can save significantly on application development and
maintenance costs and time thanks to high-quality API, open-source SDKs and convenience of a cloud service.

This SDK requires a 3.8+ version of Python to run.

MetaApi risk management API features
=====================================

Features supported:

- tracking equity drawdown API
- manage arbitrary number of trackers with different periods
- retrieving drawdown events with REST API or streaming
- retrieving drawdown statistics
- retrieving equity charts

Please check Features section of the `https://metaapi.cloud/docs/risk-management/ <https://metaapi.cloud/docs/risk-management/>`_
documentation for detailed description of all settings you can make.

FAQ
===

Please check this page for FAQ: `https://metaapi.cloud/docs/risk-management/faq/ <https://metaapi.cloud/docs/risk-management/faq/>`_.

Code examples
=============

Please check examples folder for code examples.

Installation
============

.. code-block:: bash

    pip install metaapi-cloud-sdk

Retrieving API token
====================

Please visit `https://app.metaapi.cloud/token <https://app.metaapi.cloud/token>`_ web UI to obtain your API token.

Configuring equity tracking
===========================

In order to configure equity tracking you need to:

- add MetaApi MetaTrader accounts with `riskManagementApiEnabled` field set to true (see below)
- create equity trackers for the accounts with needed parameters

.. code-block:: python

    from metaapi_cloud_sdk import MetaApi, RiskManagement

    token = '...'
    metaapi = MetaApi(token=token)
    risk_management = RiskManagement(token=token)

    # retrieve MetaApi MetaTrader accounts with riskManagementApiEnabled field set to true
    account = await api.metatrader_account_api.get_account(account_id='accountId')
    if not account.risk_management_api_enabled:
        raise Exception('Please set riskManagementApiEnabled field to true in your MetaApi account in ' +
            'order to use it in RiskManagement API')

    risk_management_api = risk_management.risk_management_api

    # create a tracker
    tracker_id = await risk_management_api.create_drawdown_tracker('accountId', {
        'name': 'Test tracker',
        'period': 'day',
        'absoluteDrawdownThreshold': 100
    })

    # retrieve list of trackers
    print(await risk_management_api.get_drawdown_trackers('accountId'))

    # retrieve a tracker by name
    print(await risk_management_api.get_drawdown_tracker_by_name('accountId', 'Test tracker'))

    # update a tracker
    print(await risk_management_api.update_drawdown_tracker('accountId', tracker_id['id'], {'name': 'Updated name'}))

    # remove a tracker
    print(await risk_management_api.delete_drawdown_tracker('accountId', tracker_id['id']))

See in-code documentation for full definition of possible configuration options.

Retrieving equity tracking events and statistics
================================================

RiskManagement allows you to monitor equity drawdowns on trading accounts.

Retrieving drawdown events
--------------------------
.. code-block:: python

    # retrieve drawdown events, please note that this method supports filtering by broker time range, account_id, tracker_id
    # and limits number of records
    print(await risk_management_api.get_drawdown_events('2022-04-13 09:30:00.000', '2022-05-14 09:30:00.000'))

Streaming drawdown events
-------------------------

You can subscribe to a stream of drawdown events using the drawdown listener.

.. code-block:: python

    from metaapi_cloud_sdk import DrawdownListener

    # create a custom class based on the DrawdownListener
    class Listener(DrawdownListener):

        # specify the function called on events arrival
        async def on_drawdown(drawdown_event):
            print('Drawdown event', drawdown_event)

    # add listener
    listener = Listener()
    listener_id = risk_management_api.add_drawdown_listener(listener)

    # remove listener
    risk_management_api.remove_drawdown_listener(listener_id)

Retrieving drawdown statistics
------------------------------
.. code-block:: python

    # retrieve drawdown statistics, please note that this method can filter returned data and supports pagination
    print(await risk_management_api.get_drawdown_statistics('accountId', tracker_id['id']))

Retrieving equity chart
------------------------------
.. code-block:: python

    # retrieve equity chart, please note that this method supports loading within specified broker time
    print(await risk_management_api.get_equity_chart('accountId'))

Related projects:
=================

See our website for the full list of APIs and features supported `https://metaapi.cloud/#features <https://metaapi.cloud/#features>`_

Some of the APIs you might decide to use together with risk management API are:

1. MetaApi cloud forex trading API `https://metaapi.cloud/docs/client/ <https://metaapi.cloud/docs/client/>`_
2. MetaStats cloud forex trading statistics API `https://metaapi.cloud/docs/metastats/ <https://metaapi.cloud/docs/metastats/>`_
3. CopyFactory copy trading  API `https://metaapi.cloud/docs/copyfactory/ <https://metaapi.cloud/docs/copyfactory/>`_
4. MetaStats forex trading metrics API `https://metaapi.cloud/docs/metastats/ <https://metaapi.cloud/docs/metastats/>`_
5. MetaApi MT manager API `https://metaapi.cloud/docs/manager/ <https://metaapi.cloud/docs/manager/>`_
