# Create Groups

To create a Group, the most basic format is:

```javascript
POST /v2/groups/{groupType}
Content-type: application/json; charset=utf-8

{
  "name": "Test Group"
}
```

The `{groupType}` can be any one of the available group types:

```eval_rst
.. include:: ../_includes/group_types.rst
```

Some group types require additional fields when being created. Refer to the table below for the fields required to create each group type:

```eval_rst
.. include:: group_fields.rst
```

By way of example, the query below will create an incident in the default owner:

```json
POST /v2/groups/incidents
Content-type: application/json; charset=utf-8

{
  "name": "Test Incident",
  "eventDate": "2017-7-13T00:00:00-04:00",
  "status": "New"
}
```

JSON Response:

```json
{
  "status": "Success",
  "data": {
    "incident": {
      "id": "54321",
      "name": "Test Incident",
      "owner": {
        "id": 1,
        "name": "Example Organization",
        "type": "Organization"
      },
      "dateAdded": "2017-07-13T17:50:17",
      "webLink": "https://app.threatconnect.com/auth/incident/incident.xhtml?incident=54321",
      "eventDate": "2017-7-13T00:00:00-04:00"
    }
  }
}
```

## Uploading Document Contents

If you are creating a Document in ThreatConnect that is a malware sample, refer to the [next section](https://docs.threatconnect.com/en/latest/rest_api/groups/groups.html#creating-a-malware-document). If you are creating a benign Document and want to upload content into the Document via API, refer to the section on [uploading Document contents](https://docs.threatconnect.com/en/latest/rest_api/groups/groups.html#update-document-contents) for more details.

### Creating a Malware Document

To create a document that can be uploaded into [ThreatConnect's Malware Vault](http://kb.threatconnect.com/customer/en/portal/articles/2171402-uploading-malware) via API, include the `malware` and `password` fields when creating the document as shown below.

```json
POST /v2/groups/documents/
Content-type: application/json; charset=utf-8

{ 
  "fileName" : "malwaresample.zip",
  "name": "malwaresample.exe",
  "malware": true,
  "password": "TCinfected"
}
```

To upload a malware sample as a Document, the following steps must be taken:

-  Create a [password-protected zip file](https://askubuntu.com/questions/17641/create-encrypted-password-protected-zip-file) on your local machine containing the sample.
-  Create a new Document Group including the additional fields `malware` set to True and `password` set to the zip file's password.
- [Upload the Document contents](https://docs.threatconnect.com/en/latest/rest_api/groups/groups.html#update-document-contents).

```eval_rst
.. warning:: Uploading raw malware executables or weaponized documents is strictly forbidden.
```

For malware uploads, an `HTTP 400 - Bad Request` error will be returned if the document is not a password-protected zip file (as determined by file headers).

# Create Group Metadata

## Create Group Attributes

To add an attribute to a Group, use the following format:

```javascript
POST /v2/groups/{groupType}/{groupId}/attributes
Content-type: application/json; charset=utf-8

{
  "type" : {attributeType},
  "value" : "Test Attribute",
  "displayed" : true
}
```

For example, if you wanted to add a Description attribute to the threat with the ID `12345`, you would use the following query:

```javascript
POST /v2/groups/threats/12345/attributes
Content-type: application/json; charset=utf-8

{
  "type" : "Description",
  "value" : "Test Description",
  "displayed" : true
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "attribute": {
      "id": "54321",
      "type": "Description",
      "dateAdded": "2017-07-13T17:50:17",
      "lastModified": "2017-07-13T17:50:17",
      "displayed": true,
      "value": "Test Description"
    }
  }
}
```

To add a Security Label to an attribute, use the following format where `{securityLabel}` is replaced with the name of a Security Label that already exists in the owner:

```
POST /v2/groups/{groupType}/{groupId}/attributes/{attributeId}/securityLabels/{securityLabel}
```

For example, the query below will add a `TLP Amber` Security Label to the attribute on the Threat:

```
POST /v2/groups/threats/12345/attributes/54321/securityLabels/TLP%20Amber
```

```eval_rst
.. note:: In order to add a Security Label to an attribute, the Security Label must already exist. The query above will not create a new Security Label. If you specify a Security Label that does not exist, it will return an error.
```

## Create Group Security Labels

To add a Security Label to a Group, use the following format where `{securityLabel}` is replaced with the name of a Security Label that already exists in the owner:

```
POST /v2/groups/{groupType}/{groupId}/securityLabels/{securityLabel}
```

For example, the query below will add a `TLP Amber` Security Label to the Threat with ID `12345`:

```
POST /v2/groups/threats/12345/securityLabels/TLP%20Amber
```

JSON Response:

```javascript
{
  "apiCalls": 1,
  "resultCount": 0,
  "status": "Success"
}
```

```eval_rst
.. note:: In order to add a Security Label to a Group, the Security Label must already exist. The query above will not create a new Security Label. If you specify a Security Label that does not exist, it will return an error.
```

## Create Group Tags

To add a tag to a Group, use the following format where `{tagName}` is replaced with the name of the tag you wish to add to the Group:

```
POST /v2/groups/{groupType}/{groupId}/tags/{tagName}
```

For example, the query below will add the `Nation State` tag to the Threat with ID `12345`:

```
POST /v2/groups/threats/12345/tags/Nation%20State
```

JSON Response:

```javascript
{
  "apiCalls": 1,
  "resultCount": 0,
  "status": "Success"
}
```

```eval_rst
.. include:: ../_includes/tag_length.rst
```

## Create Adversary Assets

To create an Adversary Asset for an Adversary, use a query in the following format:

```
POST /v2/groups/adversaries/{adversaryId}/adversaryAssets/{assetType}
```

`{assetType}` can be replaced with the following asset types:

* handles
* phoneNumbers
* urls

For example, the query below will add `Pierre Despereaux` as a Handle to the Adversary with ID `12345`:

```javascript
POST /v2/groups/adversaries/12345/adversaryAssets/handles
Content-type: application/json; charset=utf-8

{
  "handle": "Pierre Despereaux"
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "adversaryHandle": {
      "id": 1,
      "type": "Handle",
      "webLink": "https://app.threatconnect.com/auth/adversary/adversary.xhtml?adversary=12345",
      "handle": "Pierre Despereaux"
    }
  }
}
```

# Create Group Associations

## Associate Group to Group

To associate one Group with another, use a query in the following format:

```
POST /v2/groups/{groupType}/{groupId}/groups/{associatedGroupType}/{associatedGroupId}
```

Replace `{associatedGroupType}` with one of the following Group types:

```eval_rst
.. include:: ../_includes/group_types.rst
```

For example, the query below will associate a Threat with the ID `12345` with an Incident with the ID `54321`:

```
POST /v2/groups/threats/12345/groups/incidents/54321
```

JSON Response:

```javascript
{
  "status": "Success"
}
```

## Associate Indicator to Group

To associate a Group with an Indicator, use a query in the following format:

```
POST /v2/groups/{groupType}/{groupId}/indicators/{associatedIndicatorType}/{associatedIndicator}
```

For example, the query below will associate the Threat with the ID 12345 with the IP Address `0.0.0.0`:

```
POST /v2/groups/threats/12345/indicators/addresses/0.0.0.0
```

JSON Response:

```javascript
{
  "status": "Success"
}
```

## Associate Victim to Group

To associate a Group with a Victim, use a query in the following format:

```
POST /v2/groups/{groupType}/{groupId}/victims/{victimId}
```

For example, the query below will associate the Threat with the ID `12345` with the Victim with ID `54321`:

```
POST /v2/groups/threats/12345/victims/54321
```

JSON Response:

```javascript
{
  "status": "Success"
}
```

```eval_rst
.. include:: ../_includes/victim_asset_required_warning.rst
```
