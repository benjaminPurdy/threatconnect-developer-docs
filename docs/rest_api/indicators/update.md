# Update Indicators

To update an Indicator, the basic format is:

```javascript
PUT /v2/indicators/{indicatorType}/{indicator}
{
  {updatedField}: {updatedValue}
}
```

When updating the fields on an Indicator itself, you can change any of the fields available for the type of Indicator you are updating except the field that sets the indicator itself. Below is a table of available fields for each Indicator type:

+----------------+--------------+----------+
| Indicator Type | Valid Fields | Required |
+================+==============+==========+
| addresses      | rating       | FALSE    |
+----------------+--------------+----------+
|                | confidence   | FALSE    |
+----------------+--------------+----------+
| emailAddresses | rating       | FALSE    |
+----------------+--------------+----------+
|                | confidence   | FALSE    |
+----------------+--------------+----------+
| files          | size         | FALSE    |
+----------------+--------------+----------+
|                | rating       | FALSE    |
+----------------+--------------+----------+
|                | confidence   | FALSE    |
+----------------+--------------+----------+
| hosts          | dnsActive    | FALSE    |
+----------------+--------------+----------+
|                | whoisActive  | FALSE    |
+----------------+--------------+----------+
|                | rating       | FALSE    |
+----------------+--------------+----------+
|                | confidence   | FALSE    |
+----------------+--------------+----------+
| urls           | rating       | FALSE    |
+----------------+--------------+----------+
|                | confidence   | FALSE    |
+----------------+--------------+----------+
  
By way of example, the query below will update the threat and confidence rating of the Email Address `bad@example.com`:

```javascript
PUT /v2/indicators/emailAddresses/bad@example.com
{
  "rating": 4,
  "confidence": 80
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "emailAddress": {
      "id": "54321",
      "owner": {
        "id": 5,
        "name": "Example Organization",
        "type": "Organization"
      },
      "dateAdded": "2017-07-13T17:50:17",
      "lastModified": "2017-07-19T20:34:23Z",
      "rating": 4,
      "confidence": 80,
      "threatAssessRating": 1.67,
      "threatAssessConfidence": 18.33,
      "webLink": "https://app.threatconnect.com/auth/indicators/details/emailaddress.xhtml?emailaddress=bad%40example.com&owner=Example+Organization",
      "address": "bad@example.com"
    }
  }
}
```

# Update Indicator Metadata

## Update Indicator Attributes

To update an Indicator's attributes, use the following format:

```javascript
PUT /v2/indicators/{indicatorType}/{indicator}/attributes/{attributeId}
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

For example, if you wanted to update the value of an attribute with ID 54321 on the Email Address `bad@example.com`, you would use the following query:

```javascript
PUT /v2/indicators/emailAddresses/bad@example.com/attributes/54321
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

## Updating File Occurrences

To update the File Occurrences on a File Indicator, use a query in the following format:

```javascript
PUT /v2/indicators/files/{fileHash}/fileOccurrences/{fileOccurrenceId}
{
  "fileName" : {fileName},
  "path" : {filePath},
  "date" : {date}
}
```

When updating a File Occurrence, the following fields are available:

+--------------+----------+
| Valid Fields | Required |
+==============+==========+
| fileName     | FALSE\*  |
+--------------+----------+
| path         | FALSE\*  |
+--------------+----------+
| date         | FALSE\*  |
+--------------+----------+

\* While none of the fields are required, at least one of them must be populated to update a File Occurrence.

For example, the query below will update the File Occurrence with an ID of 54321 on the File Indicator represented by the hash `aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa`:

```javascript
PUT /v2/indicators/files/aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa/fileOccurrences/54321
{
  "fileName": "newFileName.exe",
  "path": "C:\\\\Windows\\User32",
  "date": "2017-07-14T05:00:00Z"
}
```

JSON Response:

```javascript
{
  "status": "Success",
  "data": {
    "fileOccurrence": {
      "id": 87534,
      "fileName": "newFileName.exe",
      "path": "C:\\\\Windows\\User32",
      "date": "2017-07-14T05:00:00Z"
    }
  }
}
```
