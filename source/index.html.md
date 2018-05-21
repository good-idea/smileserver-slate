---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

search: true
---

# Overview

Smileserver handles all of Smilebooth's cloud functions. It is written in F# and uses [Suave](https://suave.io/). These douments are incomplete, for a full list of API routes, visit the routes file in `src/app/Api/Routes.fs`.

## Branches

The current working branch is V4.

# Authentication

Authentication is built out loosely according to the Client Credientials flow. You send your username and password to the server and receive a token which is to be included in subsequent authenticated requests.

## Get token

```shell
curl -X POST \
  https://v4-api.smilebooth.com/api/v4/user/get-by-login-info \
  -H 'Content-Type: application/json' \
  -d '{
  "username": "generally an email",
  "password": "secret"
}'
```

> The above request returns JSON structured like this:

```JSON
{
  "isAdmin": true,
  "isUnrestricted": true,
  "homePortalId": 2,
  "isSuperuser": false,
  "username": "username",
  "token": "some-crazy-long-token",
  "id": 27
}
```

Send credentials via POST to `https://v4-api.smilebooth.com/api/v4/user/get-by-login-info` and a token is returned. To access authenticated endpoints, include this token in the header under the name: `x-user-token`

## Log out

```shell
curl -X POST \
  https://v4-api.smilebooth.com/api/v4/user/logout \
  -H 'Content-Type: application/json'
```

> The above request returns a blank response, status 200

# Folders

## Get All Folders

This endpoint returns an array of objects which include folder names and ids. Currently there is a bug which returns repeat folder names, so results must be filtered on the receiving end.

```shell
curl -X POST \
  https://test-api.smilebooth.com/api/v4/folders/list-names-noauth \
  -H 'Content-Type: application/json'
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 528,
    "name": "addidas"
  },
  {
    "id": 529,
    "name": "tesla-dance-party"
  },
  {
    "id": 530,
    "name": "dave"
  }
]
```

### HTTP Request

`POST https://test-api.smilebooth.com/api/v4/folders/list-names-noauth`

## Get a Specific Folder

This command gets the folder details and gallery objects contained within it.

```shell
curl -X POST \
  https://test-api.smilebooth.com/api/v4/folders/get-sharing-details-noauth \
  -H 'Content-Type: application/json' \
  -d '{
  "id": 30
}'
```

> The above command returns JSON structured like this:

```json
{
  "folder": {
    "id": 30,
    "name": "downtest",
    "description": "downtest",
    "ownerId": 2,
    "portalId": 1,
    "uploadTypes": [],
    "autoPrint": false,
    "autoPrintTypes": [],
    "webhooks": [
      ""
    ],
    "date": "1970-01-01T00:00:00",
    "permanent": false,
    "test": false
  },
  "galleries": [
    {
      "id": 30,
      "folderId": 30,
      "name": "Default",
      "web": {
        "enableEmail": true,
        "enableTwitter": true,
        "enableFacebook": true,
        "enablePinterest": true,
        "enableGooglePlus": true
      },
      "maps": [
        {
          "mode": "video",
          "subtype": "original"
        },
        {
          "mode": "gif",
          "subtype": "gif"
        },
        {
          "mode": "print",
          "subtype": "print"
        },
        {
          "mode": "photo",
          "subtype": "original"
        }
      ]
    }
  ]
}
```

### HTTP Request

`POST https://test-api.smilebooth.com/api/v4/folders/get-sharing-details-noauth`

### Request Body JSON

Parameter | Description
--------- | -----------
ID | The ID of the folder to retrieve

# Images

## Get All Images in a Gallery

This endoint returns an array of image objects given a specific gallery ID.

```shell
curl -X POST \
  https://test-api.smilebooth.com/api/v4/images/list-by-gallery-noauth \
  -H 'Content-Type: application/json' \
  -d '{
  "galleryId": 3
}'
```

> The above command returns JSON structured like this:

```json
[
  {
    "id": 333,
    "sequenceId": 333,
    "subtype": 0,
    "position": 0,
    "url": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/1bcxd00035",
    "cardUrl": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/1bcxd00035",
    "thumbUrl": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/thumb/1bcxd00035",
    "width": 1024,
    "height": 768,
    "mode": 0,
    "source": null,
    "hidden": false,
    "created": "2016-09-10T14:50:00.785223-04:00"
  },
  {
    "id": 332,
    "sequenceId": 332,
    "subtype": 0,
    "position": 0,
    "url": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/1bcxd00034",
    "cardUrl": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/1bcxd00034",
    "thumbUrl": "https://s3.amazonaws.com/smilebooth_images/boost/tablet/thumb/1bcxd00034",
    "width": 1024,
    "height": 768,
    "mode": 0,
    "source": null,
    "hidden": false,
    "created": "2016-09-10T14:50:00.781464-04:00"
  }
]
```

### HTTP Request

`POST https://test-api.smilebooth.com/api/v4/images/list-by-gallery-noauth`

### Request Body JSON

Parameter | Description
--------- | -----------
ID | The ID of the gallery

# Sharing

These are enpoints which will deliver an image to a recipient.

## Send to Email

Send image to recipient via email.

```curl
curl -X POST \
  https://v4-api.smilebooth.com/api/v4/sharers/email/share \
  -H 'Content-Type: application/json'
  -d '{
  "email": "email@example.com",
  "uploadId": "17940",
  "galleryId": "3"
}'
```

> The above command will return a number (not sure what it is :))

```json
12122
```

### HTTP Request

`POST https://v4-api.smilebooth.com/api/v4/sharers/email/share`

### Request Body JSON

Parameter | Description
--------- | -----------
email | The email to send to
uploadId | The image id to send
galleryId | The id of the gallery the image is in

## Send via MMS

Send image to recipient via multimedia message.

```curl
curl -X POST \
  https://v4-api.smilebooth.com/api/v4/sharers/mms/share \
  -H 'Content-Type: application/json'
  -d '{
  "phone": "7192934678",
  "uploadId": "15610",
  "galleryId": "3"
}'
```

> The above command will return a number (not sure what it is :))

```json
1234
```

### HTTP Request

`POST https://v4-api.smilebooth.com/api/v4/sharers/mms/share`

### Request Body JSON

Parameter | Description
--------- | -----------
phone | The phone number to send it to
uploadId | The image id to send
galleryId | The id of the gallery the image is in
