# Create PDF Report for Groups

As of ThreatConnect version 5.4, it is possible to create a report PDF for a Group in ThreatConnect from the API. The general format for this request is as follows:

```
GET /v2/groups/{type}/{id}/pdf
```

This endpoint is available for all of the following Group types:

- Adversaries
- Campaigns
- Emails
- Incidents
- Signatures
- Threats

```eval_rst
.. note:: This endpoint applies to all Groups except for Documents.
```
