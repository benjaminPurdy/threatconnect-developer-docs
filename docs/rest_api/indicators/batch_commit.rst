Batch Upload: Indicators
------------------------

The Batch API allows bulk Indicator creation and deletion via the HTTP
POST method. After creating a batch, an Indicator file is uploaded. The
content of the file must be valid JSON, with content and format
mimicking the data structure of the Bulk JSON file download. A file
upload instantly triggers a batch job to begin processing the data. The
Batch API is restricted to Indicators and will improve performance when
importing large amounts of data.

.. note:: Document Storage is required to use the Batch API.

The Batch Create resource creates a batch entry in the system. No batch processing is triggered until the batch input file is uploaded. The table below displays the fields required for the Batch Create message.

+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
| BatchConfig Message | Values          | Description                                                                                                                      |
+=====================+=================+==================================================================================================================================+
| haltOnError         | true            | Batch process stops processing entire batch first time it reaches an error during processing.                                    |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | false (default) | If field not provided, default behavior continues processing further entities in input file.                                      |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
| attributeWriteType  | Append          | Adds Attributes (allow duplicates)                                                                                                |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | Replace         | Deletes current Attributes; Adds/Validates new Attributes                                                                        |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
| action              | Create          | Creates Indicator                                                                                                                |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | Delete          | Deletes Indicator (Only the ‘summary’ and ‘type’ field are required.)                                                             |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
| fileMergeMode       | Distribute      | Rating, Confidence, Tags, Attributes, etc., from incoming file hashes are applied to all matching Indicators (up to 3 possible). |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | Merge           | Combines multiple Indicators into one                                                                                            |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
| hashCollisionMode   | Split           | Inhibits a merge and, instead, splits the incoming data out across any offending Indicators                                      |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | IgnoreIncoming  | Drops incoming Indicator from the import, leaving pre-existing data same as it was before importing offending Indicator      |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | IgnoreExisting  | If conflict exists between two or more existing Indicators, existing file Indicators that caused conflict are deleted.       |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | FavorIncoming   | Favors hashes in incoming data, overwriting hashes that conflicted within existing data                                          |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+
|                     | FavorExisting   | Favors hashes in existing data, ignoring hashes that conflicted from incoming data                                                |
+---------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------+

.. note:: If ``haltOnError`` is set to ‘true’ and an error occurs, then the status will be set to ‘Completed’, and ‘errorCount’ will be greater than zero. The ‘unprocessedCount’ field will be greater than zero, unless the uploaded file did not contain valid JSON.

.. note:: Occasionally, imported File Indicators may overlap one or more hashes with other File Indicators already present within the system. In the typical situation, either the incoming data or the existing data will contain additional hash type[s] that the other item did not have (e.g., Incoming data has both an md5 and sha1, while the existing data has only the md5, or vice versa). In this typical situation, the resulting File Indicator will end up with the "superset" of file hashes by either retaining the existing hash[es] or adding in the new hash[es]. However, certain non-typical situations may exist that require special processing when incoming file hash[es] cause conflicts with existing data (e.g., Incoming data has an md5 and sha1, while the existing data has the same md5 but a different sha1). The behavior in situations like these are controlled by the ``fileMergeMode`` and ``hashCollisionMode`` parameters defined in the above table.

Batch Indicator Input File Format
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code:: json

    [
      {
        "rating": 3,
        "confidence": 60,
        "description": "a malicious domain",
        "summary": "super-malicious.ru",
        "type": "Host",
        "associatedGroup": [12345, 54321],
        "attribute": [
          {
            "type": "AttributeName",
            "value": "MyAttribute"
          }
        ],
        "tag": [
          {
            "name": "MyTag"
          }
        ]
      }
    ]

The batch upload feature expects to ingest a JSON file consisting of a
list of dictionaries.

+----------------------+----------------------+-----------+
| Field                | Data type            | Required? |
+======================+======================+===========+
| ``rating``           | integer              | Required  |
+----------------------+----------------------+-----------+
| ``confidence``       | float                | Required  |
+----------------------+----------------------+-----------+
| ``description``      | string               | Required  |
+----------------------+----------------------+-----------+
| ``summary``          | string               | Required  |
+----------------------+----------------------+-----------+
| ``type``             | string               | Required  |
+----------------------+----------------------+-----------+
| ``tag``              | list of dictionaries | Optional  |
+----------------------+----------------------+-----------+
| ``attribute``        | list of dictionaries | Optional  |
+----------------------+----------------------+-----------+
| ``associatedGroup``  | list of integers     | Optional  |
+----------------------+----------------------+-----------+
| ``dateAdded``        | date/time            | Optional  |
+----------------------+----------------------+-----------+

.. note:: File Indicators may have any or all of MD5, SHA1, and/or SHA256 hash values. The hashes may be provided in either of two ways: (1) concatenated using 'space-colon-space' into the 'summary' field of the indicator, or; (2) presented as individual 'md5', 'sha1', and 'sha256' hash values. The presence of any hashes using this second method will cause the summary field to be ignored during import. For example, consider a File Indicator with the md5 hash ``905ad8176a569a36421bf54c04ba7f95``, sha1 hash ``a52b6986d68cdfac53aa740566cbeade4452124e`` and sha256 hash ``25bdabd23e349f5e5ea7890795b06d15d842bde1d43135c361e755f748ca05d0``, which could be imported in either of the two following ways:

   ``Option 1``
   
   {
   "summary": "905ad8176a569a36421bf54c04ba7f95: a52b6986d68cdfac53aa740566cbeade4452124e:
   25bdabd23e349f5e5ea7890795b06d15d842bde1d43135c361e755f748ca05d0",
   "type": "File",
   ...
   }
   
   ``Option 2``
   
   {
   "md5": "905ad8176a569a36421bf54c04ba7f95",
   "sha1": "a52b6986d68cdfac53aa740566cbeade4452124e",
   "sha256": "25bdabd23e349f5e5ea7890795b06d15d842bde1d43135c361e755f748ca05d0",
   "type": "File",
   ...
   }        

Supported ``type`` values for Indicators:

-  Host
-  Address
-  EmailAddress
-  URL
-  File

.. note:: Exporting indicators via the `JSON Bulk Reports <https://docs.threatconnect.com/en/latest/rest_api/indicators/indicators.html#json-bulk-reports>`__ endpoint will create a file in this format.

.. warning:: The maximum number of Indicators that can be created in one batch job is 25,000. If you need to create more Indicators, you will have to use multiple batch jobs.

**Sample Batch Create request**

.. code::

    POST /v2/batch/
    Content-type: application/json; charset=utf-8

    {
      "haltOnError": "false",
      "attributeWriteType": "Replace",
      "action": "Create",
      "owner": "Common Community"
    }

**Server Response on Success**

.. code::

    HTTP/1.1 201 Created
    {
      batchId: "123"
    }

**Server Response on Insufficient Privileges**

.. code::

    HTTP/1.1 403 Forbidden
    {
      status: "Not Authorized",
      description: "Organization not authorized for batch"
    }

**Server Response on Incorrect Settings**

.. code::

    HTTP/1.1 403 Forbidden
    {
      status: "Not Authorized",
      description: "Document storage not enabled for this instance"
    }

**Sample Batch Upload Input File request**

Batch files should be sent as HTTP POST data to a REST endpoint, including the relevant ``batchId``, as shown in the format below.

.. code::

    POST /v2/batch/{batchId}

For example:

.. code::

    POST /v2/batch/123

    Content-Type: application/octet-stream
    Body: The JSON payload goes here.

**Server Response on Success**

.. code::

    HTTP/1.1 202 Accepted
    {
      status: "Queued"
    }

**Server Response on Overlarge Input File**

.. code::

    HTTP/1.1 400 Bad Request
    {
      status: "Invalid",
      description: "File size greater than allowable limit of 2000000"
    }

**Sample Batch Status Check request**

Use this request to check the status of a running batch-upload job. Possible GET response statuses are:

-  Created
-  Queued
-  Running
-  Completed

.. code::

    GET /v2/batch/123

**Server Response on Success (job still running)**

.. code::

    HTTP/1.1 200 OK
    {
      status: "Running"
    }

**Server Response on Success (job finished)**

.. code::

    HTTP/1.1 200 OK
    {
      status: "Completed",
      errorCount: 3420,
      successCount: 405432,
      unprocessCount: 0
    }

**Sample Batch Error Message request**

.. code::

    GET /v2/batch/123/errors

**Server Response on Success (job still running)**

.. code::

    HTTP/1.1 400 Bad Request
    {
      status: "Invalid",
      description: "Batch still in Running state"
    }

**Server Response on Success (job finished)**

.. code::

    HTTP/1.1 200 OK
    Content-Type: application/octet-stream ; boundary=
    Content-Length:
    Content-Encoding: gzip

.. note:: Batch jobs that end in partial failures will have an error file with a response having a 'reason text', which includes Tag, Attribute, or Indicator errors (fail on first).
