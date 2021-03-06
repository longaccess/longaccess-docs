API Documentation, v0.9

API is based on the yummy [Tastypie](http://django-tastypie.readthedocs.org/en/latest/).
This is version 0.9.

---

## REST API Endpoint

The API endpoint is `https://www.longaccess.com/api/v1/`

## Request format
*:resource* refers to the value of a resource. For example, *:id* will be substituted by the numeric ID of the resource when an API call is used.

A request to a resource can provide URL parameters (e.g. GET /capsules/?limit=10) and can provide a request body in JSON format.


## Response format
The return format is by default JSON: any request that doesn't request a format explicitly will produce a reply in the JSON format. Since other formats may be supported in the future a client can explicitly request a reply in JSON format by:

   * supplying an HTTP `Accept` header in the request with the MIME type type `application/json` as the value.
   * supplying the HTTP query parameter `format` with a value of `json`.

---

## Authentication

All requests must be authenticated. Authentication methods supported:

- HTTP Basic Auth

---

## Users
Users are registered using the web interface of the service at www.longaccess.com. A user can have zero or more *capsules* and each capsule can have zero or more *archives*.

### GET /account/
Returns the profile of the authenticated user:

**Parameters**: None.

**Returns**:

- `displayname` - the name that the user prefers to be called by
- `email` - the email on file for this user

---

## Capsules
Capsules are storage containers of fixed size and expiration date.

### GET /capsule/

Get a list of all capsules that belong to the authenticated user.

**Parameters**:

- `limit` - optional parameter to specify the number of resources to return when doing pagination (the limit is by default 20, set `limit=0` to disable pagination).

**Returns**:

A JSON object containing two attributes:

- `meta` - the following metadata about the result:
   * `limit` - the maximum number of results in this page
   * `next` - if another page of results is available this will contain the page's URI or, if not available, null.
   * `offset` - the number of results that appeared in previous pages
   * `previous` - if a previous page of results is available this will contain the page's URI or, if not available, null.
   * `total_count` - the total number of results (independent of pagination)
- `objects` - a JSON list of objects which briefly describe each capsule in the current result page. Each object contains the following attributes:
   * `created` - when the capsule was created, a timestamp in ISO format (e.g. `2013-06-07T10:45Z`)
   * `id` - a unique identifier for this capsule
   * `resource_uri` - the absolute URI of the capsule resource (e.g.`/api/v1/capsule/3/`)
   * `title` - the title given to this capsule
   * `user` - the URI of the User resource who owns this capsule.
   * `expires` - when the capsule expires, a timestamp in ISO format (e.g. `2043-06-07T10:45Z`)

Example:

```
{
  "meta": {
     "limit": 20,
     "next": null,
     "offset": 0,
     "previous": null,
     "total_count": 2
  },
  "objects": [
     {
         "created": "2013-06-07T10:45Z",
         "id": 3,
         "resource_uri": "/api/v1/capsule/3/",
         "title": "Photos",
         "user": "/api/v1/user/3/",
         "expires": "2043-06-07T10:45Z"
      },
      {
          "created": "2013-06-07T10:44Z",
          "id": 2,
          "resource_uri":
          "/api/v1/capsule/2/",
          "title": "Stuff",
          "user": "/api/v1/user/2/",
          "expires": "2043-06-07T10:45Z"
       }
    ]
}
```

### GET /capsule/:id/
Get details for capsule :id. The capsule must be owned by the authenticated user.

**Parameters**: None

**Returns**:

- `id` - a unique identifier of the capsule
- `created` - when the capsule was created, a timestamp in ISO format (e.g. `2013-06-07T10:45Z`)
- `expires` - when the capsule will expire, a timestamp in ISO format (e.g. `2043-06-07T10:45Z`)
- `size` - the capsule's size in bytes
- `remaining` - remaining size in this capsule, in bytes
- `resource_uri` - the absolute URI of the capsule resource (e.g./api/v1/capsule/3/)
- `archives` - a list of JSON objects, one for each archive associated with this capsule. Each object has the following data:
   * `id` - a unique identifier for this archive
   * `size` - the size of this archive, in bytes
   * `created` - when this archive was created, a timestamp in ISO format (e.g. `2013-06-07T10:45Z`)
   * `title` - the title of this archive

---

## Archives

### GET /archive/

Get a list of all archives that belong to the authenticated user.

**Parameters**:

- `limit` - optional parameter to specify the number of resources to return when doing pagination (the limit is by default 20, set `limit=0` to disable pagination).

**Returns**:

A JSON object containing two attributes:

- `meta` - the following metadata about the result:
   * `limit` - the maximum number of results in this page
   * `next` - if another page of results is available this will contain the page's URI or, if not available, null.
   * `offset` - the number of results that appeared in previous pages
   * `previous` - if a previous page of results is available this will contain the page's URI or, if not available, null.
   * `total_count` - the total number of results (independent of pagination)
- `objects` - a JSON list of objects which briefly describe each capsule in the current result page. Each object contains the following attributes:
   * `created` - when the archive was created, a timestamp in ISO format (e.g. `2043-06-07T10:45Z`)
   * `id` - a unique identifier for this archive.
   * `resource_uri` - the absolute URI of the archive resource (e.g.`/api/v1/archive/3/`).
   * `title` - the title given to this archive.
   * `capsule` - the URI of the capsule where this archive belongs.

### GET /archive/:id/

Get archive (with :id) details.

**Parameters**: None

**Returns**:

- `id` - a unique identifier of the capsule
- `created` - when the archive was created, a timestamp in ISO format (e.g. `2013-06-07T10:45Z`)
- `size` - the archive's size in bytes
- `title` - the title given to this archive.
- `description` - the description given to this archive.

---

## Uploads

An *upload* is a temporary object used to upload archives. When an archive is to be uploaded, a POST call is made to /upload/ (see example 2 at the end of this document). API will return an *upload_id*, a STS (Amazon AWS Secure Token Service) and the expiration datetime of the TST.

The upload client will use the credentials to upload the archive (in chunks) to Amazon S3.

When credentials are close to expiring (or if they expired), the client can do a GET call to /upload/:id/ and get a new set of credentials.

When the upload is complete, the client does a PATCH call to /upload/:id/ sending the archive checksum, the parts number and updating the status to 'uploaded'.

Periodically the client does a GET call to /upload/:id/ to check the status of the operation. When the status is 'completed' the archive has been verified and archived by the backend. The client can now issue the PaperKey.

The values that may be returned by the API for the `status` attribute are:

* `pending` - the upload is active but the client has not completed the transmission
* `error` - the upload is not active due to an error
* `uploaded` - the upload is active and the client has completed the transmission
* `completed` - the operation is complete. The archive ID is available to the client in a separate attribute. 

### POST /upload/

Initiates a new upload operation for the authenticated user. The POST body must be JSON encoded and the `Content-Type` header should be `application/json`.

**Parameters**: None

**Request body**: JSON mapping with the following keys:

- `capsule` - the uri of the capsule (for example: /api/v1/capsule/3/)
- `title` - the title of the archive
- `description` - the description of the archive

Example:

    {
        "title": "test",
        "description": "blah blah",
        "capsule": "/api/v1/capsule/1/"
    }

**Returns**:

- `id` - the upload operation id
- `resource_uri` - the API uri for the specific upload operation
- `token_access_key` - S3 Access key
- `token_secret_key` - S3 Secret key
- `token_session` - STS secure token
- `token_expiration` - STS expiration datetime
- `token_uid` - STS upload id
- `bucket` - the name of the S3 bucket to upload to
- `prefix` - the prefix to add to the key name when uploading
- `created` - the date and time the upload operation was created (ISO timestamp)
- `status` - the status of the upload operation. Value may be `pending` or `error`.

### GET /upload/:id/

Get upload operation (with :id) details.

**Parameters**: None

**Returns**:

- `id` - a unique identifier of upload operation.
- `capsule` - the API uri for the capsule that this upload belongs to.
- `title` - the title given to this archive.
- `description` - the description given to this archive.
- `resource_uri` - the API uri for the specific upload operation.
- `status` - the status of the upload operation. See above for the possible values. 
- `token_access_key` - S3 Access key
- `token_secret_key` - S3 Secret key
- `token_session` - STS secure token
- `token_expiration` - STS expiration datetime
- `token_uid` - STS upload id
- `bucket` - the name of the S3 bucket to upload to
- `prefix` - the prefix to add to the key name when uploading
- `created` - the date and time the upload operation was created (ISO timestamp)
- `archive_key` - the key of the archive (only present when `status` is `completed`)

### PATCH /upload/:id/

Update upload operation status. The PATCH body must be JSON encoded and the `Content-Type` header should be `application/json`.

**Parameters**: None

**Request body**: JSON mapping with the following keys:

- `status` - what the new status of the archive should be. The value may be `pending` or `uploaded`.
- `size` - the uploaded archive's size in bytes
- `checksums` - a dictionary with archive hashes as hex encoded bytes. Contains the following keys:
    * `md5` - the `md5` hash of the archive
    * `sha512` - the `sha512` hash of the archive (optional)
- `parts` - the number of parts that the client has uploaded so far.
- `keys` - the S3 keys of the individual parts, in order.

Example:

    {
        "status": "uploaded",
        "size": "4509283",
        "checksums" : {
            "md5": "acbd18db4cc2f85cedef654fccc4a4d8",
            "sha512": "f7fbba6e0636f890e56fbbf3283e524c6fa3204ae298382d624741d0dc6638326e282c41be5e4254d8820772c5518a2c5a8c0c7f7eda19594a7eb539453e1ed7"
        },
        "parts" : 3,
        "keys" [
            'upload/1523-42/temp-archive-0',
            'upload/1523-42/temp-archive-1',
            'upload/1523-42/temp-archive-2',
        ],
    }

**Returns**:

- `id` - a unique identifier of upload operation.
- `capsule` - the API uri for the capsule that this upload belongs to.
- `title` - the title given to this archive.
- `description` - the description given to this archive.
- `resource_uri` - the API uri for the specific upload operation.
- `status` - the status of the upload operation. See above for the possible values.
- `token_access_key` - S3 Access key
- `token_secret_key` - S3 Secret key
- `token_session` - STS secure token
- `token_expiration` - STS expiration datetime
- `token_uid` - STS upload id
- `bucket` - the name of the S3 bucket to upload to
- `prefix` - the prefix to add to the key name when uploading
- `created` - the date and time the upload operation was created (ISO timestamp)


## Testing

You can easily test API with [curl](http://curl.haxx.se/).

**Examples**:

1. Get all capsules of authenticated user

```
curl -u user1@longaccess.com:test123la --dump-header - \
-H "Accept: application/json" https://www.longaccess.com/api/v1/capsule/
```

2. Initiate a new upload operation

```
curl -u user1@longaccess.com:test123la --dump-header - \
-H "Content-Type: application/json" -X POST \
--data '{"title": "test", "description": "blah blah", "capsule": "/api/v1/capsule/1/", "status": "pending"}' \
https://www.longaccess.com/api/v1/upload/
```

3. Get information and a new set of credentials for an existing upload operation

```
curl -u user1@longaccess.com:test123la https://www.longaccess.com/api/v1/upload/1/
```
