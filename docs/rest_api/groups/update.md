# Update Groups

To update a Group, the basic format is:

```javascript
PUT /v2/groups/{groupType}/{groupId}
{
  {updatedField}: {updatedValue}
}
```

The `{groupType}` can be any one of the available group types:

- `adversaries`
- `campaigns`
- `documents`
- `emails`
- `incidents`
- `signatures`
- `threats`

When updating the fields on a Group itself, you can change any of the fields available for the type of Group you are updating. Below is a table of available fields for each Group type:

```eval_rst
.. include:: group_fields.rst
```

By way of example, the query below will update the name of an Incident with ID 12345:

```javascript
PUT /v2/groups/incidents/12345
{
  "name": "New Incident Name"
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "incident": {
      "id": "12345",
      "name": "New Incident Name",
      "owner": {
        "id": 1,
        "name": "Example Organization",
        "type": "Organization"
      },
      "dateAdded": "2017-07-13T17:50:17",
      "webLink": "https://app.threatconnect.com/auth/incident/incident.xhtml?incident=12345",
      "eventDate": "2017-7-13T00:00:00-04:00"
    }
  }
}
```

## Update Document Contents

To update the contents of a Document in ThreatConnect, use a query in the following format:

```
PUT /v2/groups/documents/{documentId}/upload
Content-Type: application/octet-stream

<raw document contents>
```

The `Content-Type` header must be set to `application/octet-stream` for this query to work properly. The query below demonstrates how to upload some text into the Document with ID 12345:

```
PUT /v2/groups/documents/12345/upload
Content-Type: application/octet-stream

New document contents here.
```

JSON Response:

```javascript
{
  "status": "Success",
}
```

# Update Group Metadata

## Update Group Attributes

To update a Group's attribute, use the following format:

```javascript
PUT /v2/groups/{groupType}/{groupId}/attributes/{attributeId}
{
  {updatedField}: {updatedValue}
}
```

When updating attributes, you can change the following fields:

+----------------------------+----------+
| Updatable Attribute Fields | Required |
+============================+==========+
| value                      | TRUE     |
+----------------------------+----------+
| displayed                  | FALSE    |
+----------------------------+----------+
| source                     | FALSE    |
+----------------------------+----------+

For example, if you wanted to update the value of an attribute with ID 54321 on the threat with ID 12345, you would use the following query:

```javascript
PUT /v2/groups/threats/12345/attributes/54321
{
  "value": "Bad... Very bad."
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
      "lastModified": "2017-07-19T15:54:12Z",
      "displayed": true,
      "value": "Bad... Very bad."
    }
  }
}
```

## Update Adversary Assets

To update an Adversary's Asset, use the following format:

```
PUT /v2/groups/adversaries/{adversaryId}/adversaryAssets/{assetType}/{assetId}
{
  {updatedField}: {updatedValue}
}
```

`{assetType}` can be replaced with the following asset types:

* handles
* phoneNumbers
* urls

When updating an Adversary Asset, you can change the following field:

+----------------------------+----------+
| Updatable Attribute Fields | Required |
+============================+==========+
| handle\*                   | FALSE    |
+----------------------------+----------+
| phoneNumber\*\*            | FALSE    |
+----------------------------+----------+
| url\*\*\*                  | FALSE    |
+----------------------------+----------+

\* The `handle` field can only be updated for Adversary Assets of the handles type

\*\* The `phoneNumber` field can only be updated for Adversary Assets of the phoneNumbers type

\*\*\* The `url` field can only be updated for Adversary Assets of the urls type

For example, if you wanted to update the URL of the Adversary Asset with ID 1 on the Adversary with ID 12345, you would use the following request:

```javascript
PUT /v2/groups/adversaries/12345/adversaryAssets/urls/1
{
  "url": "https://newsite.com/"
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "adversaryUrl": {
      "id": 1,
      "type": "URL",
      "webLink": "https://app.threatconnect.com/auth/adversary/adversary.xhtml?adversary=12345",
      "url": "https://newsite.com/"
    }
  }
}
```
