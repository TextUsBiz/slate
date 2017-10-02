---
title: TextUs API Reference

language_tabs:
  - shell

toc_footers:
  - <a href='https://textus.com/signup/'>Sign Up for a Developer Key</a>
---

# Overview

The TextUs API is a RESTful API that allows your company to integrate your
TextUs messages and contacts with your CRM or any other tool. The majority of
functionality within our web application is accessible via the API, so the
integration possibilities are endless.

## Current Version

By default, all requests receive the v2 version of the API. We encourage you to
explicitly request this version via the Accept header.

`Accept: application/vnd.textus-v2`

## Authentication

```shell
curl https://app.textus.com/api/messages \
  --user {username}:{api_key} \
  --verbose
```

The TextUs API supports HTTP Basic authentication. To authenticate, base-64
encode your username and api key (separated with a colon) and place the result
in an 'Authorization' header. You may authenticate with your username and
password, or with your username and api key.

`Authorization: {username}:{api_key}`

For curl commands, you can specify the user name and password to use for server
authentication with -u or --user.

`--user {username}:{api_key}`

## Parameters

Many API methods take optional parameters. For GET requests, any parameters not
specified as a segment in the path can be passed as an HTTP query string
parameter.

For POST, PATCH, PUT, and DELETE requests, parameters not included in the URL
should be encoded as JSON with a Content-Type of 'application/json'.

## Errors

> The JSON response body for an error is structured like this:

```json
{"error": "Validation failed: Phone has already been taken"}
```

TextUs uses conventional HTTP response codes to indicate the success or failure
of an API request. In general, codes in the 2xx range indicate success, codes
in the 4xx range indicate an error that failed given the information provided
(e.g., a required parameter was omitted, contact creation failed, etc.), and
codes in the 5xx range indicate an error with TextUs servers.

## Pagination

```shell
curl https://app.textus.com/api/contacts?page=1&per_page=25 \
  --user {username}:{api_key} \
  --verbose
```

> This will return the first 25 contacts and a JSON Response header structured like this:

```json
{
  "Date": null,
  "Via": null,
  "X-Total": null,
  "Connection": null,
  "X-Runtime": null,
  "Content-Length": null,
  "X-Request-Id": null,
  "X-Per-Page": null,
  "X-Offset": null,
  "Server": null,
  "Etag": null,
  "Strict-Transport-Security": null,
  "X-Next-Page": null,
  "Cache-Control": null,
  "X-Total-Pages": null,
  "Content-Type": null,
  "X-Page": null
}
```

Requests that return multiple items will be paginated to 25 items by default.
You can specify further pages with the ?page parameter. For some resources, you
can also set a custom page size up to 100 with the ?per_page parameter. Note
that for technical reasons not all endpoints respect the ?per_page parameter,
see events for example.

Note: the response headers for these requests include:

| Key | Value |
|:---:|:------|
| X-Offset | The page offset requested. |
| X-Page | The current page requested. |
| X-Per-Page | The number of resources requested per page. |
| X-Total | The total number of resources. |
| X-Total-Pages | The total number of pages for the requested resources. |

## Rate Limiting

For broadcast requests, you can make up to 1 request per minute. For all other
requests, you can make up to 1,000 requests per hour.

## CORS

The API currently **does not** support Cross Origin Resource Sharing (CORS) for
AJAX requests from any origin.

## Webhooks

Webhooks allow you to build or set up integrations which subscribe to messaging
events on TextUs. When a message is sent or received, we'll send a HTTP POST
payload to the webhook's configured URL. Webhooks can be used to log messages
for a contact in an external CRM or ATS or any other creative use.

Whenever a message is sent to or from a TextUs account we can POST that message
to your provided webhook URLs.

## Webhooks V1

> Webhook V1 Sample Payload

```json
{
  "body": null,
  "read": null,
  "broadcast_id": null,
  "status": null,
  "id": null,
  "created_at": null,
  "sender": null,
  "sender_type": null,
  "recipient": null,
  "recipient_type": null,
  "sender_phone": null,
  "recipient_phone": null
}
```

*Version 1 is deprecated. Please use V2 for new integration development*

V1 Webhooks deliver a payload that matches V1 of our messages API.

## Webhooks V2

> Webhook V2 Sample Payload

```json
{
  "timestamp":1500000000,
  "web_hook":{
    "id":1,
    "url":"http://httpbin.org/post",
    "account_id":1,
    "version":"2",
    "created_at":"2017-07-14T19:38:44.231Z",
    "updated_at":"2017-07-14T19:38:44.231Z"
  },
  "message":{
    "content":"Hello, Bart.",
    "read":false,
    "broadcast_id":null,
    "status":"created",
    "deliver_at":"2017-07-18T21:01:11.596Z",
    "from_autoresponse":null,
    "id":6,
    "created_at":"2017-07-18T21:01:11.596Z",
    "updated_at":"2017-07-18T21:01:11.596Z",
    "sender_id":6,
    "receiver_id":6,
    "sender_type":"User",
    "receiver_type":"Contact",
    "sender_phone":"+12230000028",
    "sender_name":"Sideshow Bob",
    "receiver_phone":"+12230000030",
    "receiver_name":"Bart Simpson",
    "image_url":null,
    "thumb_url":null
  }
}
```

V2 Webhooks contain a more advanced payload, which include the originating
webhook and the message payload from V2 of our [Messages API](#messages).


### Signature Verification (Optional)

Webhook V2 payloads include a cryptographic signature so you may verify that
the message is legitimate.  This verification is completely optional on your
part. The signature will be contained in an `X-TextUs-Signature` header.

To verify the signature:

1. Parse the request body to retrieve the top-level `timestamp` field.
2. Extract the signature from the header.
3. Prepare the signed payload. This is achieved by combining the timestamp, a
   `.` character, and the request body.
4. Determine the expected signature value. Compute a HMAC with the SHA256 hash
   function, using your webhook's signing secret as the key and the signed
   payload string as the message.
5. Compare your computed signature with the value included in the
   `X-TextUs-Signature` field. If they match then you can be sure the request
   is authentic. In other words,
   `OpenSSL::HMAC.digest(OpenSSL::Digest.new('sha256'), secret_key, timestamp +
   '.' + request_body) == X-TextUs-Signature`


## Testing the API

Below is a static list of all of the accessible TextUs resources with an
example request and response. To test out a live version of the API for your
account, please sign in or sign up and navigate to the [API
docs](https://app.textus.com/api/docs).



# Auto Replies


## List all auto replies for the current account

```sh
curl https://app.textus.com/api/auto_replies \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "account_id": 121,
    "message": "Reply 1",
    "is_active": false,
    "id": 42,
    "created_at": "2017-09-13T10:40:23Z",
    "updated_at": "2017-10-01T12:04:46Z"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |


## Create a new account auto reply

```sh
curl https://app.textus.com/api/auto_replies \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"message":null}'
```


> The above command returns JSON:

```json
{
  "account_id": 122,
  "message": "Reply 2",
  "is_active": false,
  "id": 42,
  "created_at": "2017-09-15T15:22:45Z",
  "updated_at": "2017-10-02T04:49:16Z"
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `message` | String | Auto Reply Message |
|  | `is_active` | String | Auto reply status (true or false) |


## Retrieve a single auto reply

```sh
curl https://app.textus.com/api/auto_replies/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "account_id": 123,
  "message": "Reply 3",
  "is_active": false,
  "id": 42,
  "created_at": "2017-09-27T03:39:53Z",
  "updated_at": "2017-10-01T20:21:46Z"
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single auto reply

```sh
curl https://app.textus.com/api/auto_replies/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "account_id": 124,
  "message": "Reply 4",
  "is_active": false,
  "id": 42,
  "created_at": "2017-09-12T15:19:29Z",
  "updated_at": "2017-09-30T22:41:37Z"
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `message` | String | Auto reply message |
|  | `is_active` | String | Auto reply status (true or false) |


## Delete a single auto reply

```sh
curl https://app.textus.com/api/auto_replies/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "account_id": 125,
  "message": "Reply 5",
  "is_active": false,
  "id": 42,
  "created_at": "2017-09-17T04:45:00Z",
  "updated_at": "2017-09-30T16:11:05Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Blocked Numbers


## List all blocked numbers for the current organization

```sh
curl https://app.textus.com/api/blocked_numbers \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "organization_id": 152,
    "id": 42,
    "created_at": "2017-09-19T08:02:24Z",
    "updated_at": "2017-10-01T01:52:32Z",
    "phone": "12230000006"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against phone) |


## Create a new blocked number

```sh
curl https://app.textus.com/api/blocked_numbers \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"phone":null}'
```


> The above command returns JSON:

```json
{
  "organization_id": 153,
  "id": 42,
  "created_at": "2017-09-05T20:14:04Z",
  "updated_at": "2017-10-01T05:39:42Z",
  "phone": "12230000007"
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `phone` | String | Phone number |
|  | `country` | String | Two-digit country code for blocked phone number |


## Delete a single blocked number

```sh
curl https://app.textus.com/api/blocked_numbers/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "organization_id": 154,
  "id": 42,
  "created_at": "2017-09-14T15:35:17Z",
  "updated_at": "2017-10-01T15:12:34Z",
  "phone": "12230000008"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Broadcasts


## List all broadcasts for the current account

```sh
curl https://app.textus.com/api/broadcasts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "user_id": 19,
    "group_id": 9,
    "message_body": "Test Broadcast",
    "status": null,
    "deliver_at": "2017-10-02 22:20:25 UTC",
    "id": 42,
    "created_at": "2017-09-11T21:19:54Z",
    "updated_at": "2017-10-01T18:22:23Z",
    "group_name": "group 1",
    "total_messages": 0
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `last_broadcast_to_each_group` | Virtus::Attribute::Boolean | Displays the last broadcast to each unique group |
|  | `date` | String | Displays the broadcasts delivered or to be delivered on the provided ISO 8601 formatted date string |


## Create and send a broadcast

```sh
curl https://app.textus.com/api/broadcasts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"sender":null,"group":null,"message_body":null}'
```


> The above command returns JSON:

```json
{
  "user_id": 20,
  "group_id": 10,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-02 22:20:25 UTC",
  "id": 42,
  "created_at": "2017-09-25T14:00:25Z",
  "updated_at": "2017-10-01T04:42:32Z",
  "group_name": "group 2",
  "total_messages": 0
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `sender` | Integer | The sending user's id |
| ✓ | `group` | Integer | The id of the group receiving the broadcast |
| ✓ | `message_body` | String | The message content of the broadcast |
|  | `deliver_at` | DateTime | Delivery Date and Time (to schedule messages) |
|  | `from_template` | Virtus::Attribute::Boolean | Whether or not a broadcast is from a template |


## Retrieve a single broadcast

```sh
curl https://app.textus.com/api/broadcasts/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "user_id": 21,
  "group_id": 11,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-02 22:20:25 UTC",
  "id": 42,
  "created_at": "2017-09-14T12:10:29Z",
  "updated_at": "2017-09-30T23:16:13Z",
  "group_name": "group 3",
  "total_messages": 0
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Delete a scheduled broadcast

```sh
curl https://app.textus.com/api/broadcasts/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "user_id": 22,
  "group_id": 12,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-02 22:20:25 UTC",
  "id": 42,
  "created_at": "2017-09-10T16:13:04Z",
  "updated_at": "2017-09-30T18:49:19Z",
  "group_name": "group 4",
  "total_messages": 0
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Contacts


## List all contacts for the current account

```sh
curl https://app.textus.com/api/contacts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "first_name": "Quickleft",
    "last_name": "Contact",
    "business_name": null,
    "account_id": 134,
    "flagged": false,
    "notes": null,
    "phone": "12230000018",
    "country": "US",
    "line_type": null,
    "id": 42,
    "created_at": "2017-09-08T03:52:47Z",
    "updated_at": "2017-10-01T10:39:16Z",
    "opted_out": false,
    "tags": [

    ]
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against phone, first_name, last_name, business_name) |
|  | `order_by_name_then_phone` | Virtus::Attribute::Boolean | Orders by last name, first name, then phone number (default order is created_at) |
|  | `scope` | String | Filter contacts by: 'landlines', 'textable', 'flagged', or 'with_notes' |


## Create a new account contact

```sh
curl https://app.textus.com/api/contacts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"phone":null,"first_name":null}'
```


> The above command returns JSON:

```json
{
  "first_name": "Quickleft",
  "last_name": "Contact",
  "business_name": null,
  "account_id": 135,
  "flagged": false,
  "notes": null,
  "phone": "12230000020",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-09-17T11:58:50Z",
  "updated_at": "2017-10-01T03:43:42Z",
  "opted_out": false,
  "tags": [

  ]
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `phone` | String | Contact phone number |
|  | `country` | String | Two-digit country code for contact phone number.  Defaults to the account's country code. |
| ✓ | `first_name` | String | Contact first name |
|  | `last_name` | String | Contact last name |
|  | `business_name` | String | Contact business name |
|  | `tags` | [String] | Contact tags |


## Retrieve a single contact by id

```sh
curl https://app.textus.com/api/contacts/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "first_name": "Quickleft",
  "last_name": "Contact",
  "business_name": null,
  "account_id": 136,
  "flagged": false,
  "notes": null,
  "phone": "12230000022",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-09-21T19:41:58Z",
  "updated_at": "2017-09-30T06:33:49Z",
  "opted_out": false,
  "tags": [

  ]
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single contact

```sh
curl https://app.textus.com/api/contacts/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "first_name": "Quickleft",
  "last_name": "Contact",
  "business_name": null,
  "account_id": 137,
  "flagged": false,
  "notes": null,
  "phone": "12230000024",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-09-18T08:56:06Z",
  "updated_at": "2017-09-30T14:02:02Z",
  "opted_out": false,
  "tags": [

  ]
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `phone` | String | Contact phone number |
|  | `country` | String | Two-digit country code for contact phone number |
|  | `first_name` | String | Contact first name |
|  | `last_name` | String | Contact last name |
|  | `business_name` | String | Contact business name |
|  | `flagged` | Virtus::Attribute::Boolean | Contact flagged tag |
|  | `notes` | String | Contact notes |
|  | `tags` | [String] | Contact tags |


## Delete a single contact

```sh
curl https://app.textus.com/api/contacts/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "first_name": "Quickleft",
  "last_name": "Contact",
  "business_name": null,
  "account_id": 138,
  "flagged": false,
  "notes": null,
  "phone": "12230000026",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-09-18T19:49:13Z",
  "updated_at": "2017-09-30T22:22:57Z",
  "opted_out": false,
  "tags": [

  ]
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Groups


## List all groups for the current account

```sh
curl https://app.textus.com/api/groups \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "name": "group 5",
    "account_id": 139,
    "locked": false,
    "id": 42,
    "created_at": "2017-09-13T17:28:32Z",
    "updated_at": "2017-09-30T19:48:10Z",
    "size": 0
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against group name) |
|  | `order_by` | String | Order Query (orders by attribute) |


## Create a new account group

```sh
curl https://app.textus.com/api/groups \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"name":null}'
```


> The above command returns JSON:

```json
{
  "name": "group 6",
  "account_id": 140,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-26T16:06:24Z",
  "updated_at": "2017-10-01T13:59:51Z",
  "size": 0
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `name` | String | Name of the group |


## Retrieve a single group

```sh
curl https://app.textus.com/api/groups/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "name": "group 7",
  "account_id": 141,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-09T14:16:52Z",
  "updated_at": "2017-10-01T17:56:02Z",
  "size": 0
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single group

```sh
curl https://app.textus.com/api/groups/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "name": "group 8",
  "account_id": 142,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-09T21:50:59Z",
  "updated_at": "2017-09-30T20:09:58Z",
  "size": 0
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `name` | String | Name of the group |


## Delete a single group

```sh
curl https://app.textus.com/api/groups/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "name": "group 9",
  "account_id": 143,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-19T02:04:16Z",
  "updated_at": "2017-10-01T15:28:12Z",
  "size": 0
}
```


### HTTP Method

`DELETE`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `delete_contacts` | Virtus::Attribute::Boolean | Whether or not to delete all contacts in the group |


## List broadcasts for the group

```sh
curl https://app.textus.com/api/groups/:id/broadcasts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "name": "group 10",
  "account_id": 144,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-20T13:01:06Z",
  "updated_at": "2017-10-01T05:18:05Z",
  "size": 0
}
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |


## List contacts for the group

```sh
curl https://app.textus.com/api/groups/:id/contacts \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "name": "group 11",
  "account_id": 145,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-04T19:26:10Z",
  "updated_at": "2017-10-01T17:11:14Z",
  "size": 0
}
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `order_by` | String | Order Query (orders by attribute) |


## Add a contact to a group

```sh
curl https://app.textus.com/api/groups/:id/contacts/:contact_id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "name": "group 12",
  "account_id": 146,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-20T00:31:53Z",
  "updated_at": "2017-09-30T16:54:28Z",
  "size": 0
}
```


### HTTP Method

`PUT`

### Properties

_N/A_


## Remove a contact from a group

```sh
curl https://app.textus.com/api/groups/:id/contacts/:contact_id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "name": "group 13",
  "account_id": 147,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-04T03:17:47Z",
  "updated_at": "2017-09-30T10:39:25Z",
  "size": 0
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Message Signatures


## List all message signatures for the current account

```sh
curl https://app.textus.com/api/message_signatures \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "account_id": 148,
    "content": "signature 1",
    "is_active": null,
    "id": 42,
    "created_at": "2017-10-02T04:22:58Z",
    "updated_at": "2017-10-02T03:22:45Z"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |


## Create a new message signature

```sh
curl https://app.textus.com/api/message_signatures \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"content":null}'
```


> The above command returns JSON:

```json
{
  "account_id": 149,
  "content": "signature 2",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-08T15:30:31Z",
  "updated_at": "2017-10-01T17:37:53Z"
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `content` | String | Message signature content |
|  | `is_active` | String | Message signature activation status (true or false) |


## Retrieve a single message signature

```sh
curl https://app.textus.com/api/message_signatures/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "account_id": 150,
  "content": "signature 3",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-04T21:15:32Z",
  "updated_at": "2017-10-01T11:28:25Z"
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single message signature

```sh
curl https://app.textus.com/api/message_signatures/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "account_id": 151,
  "content": "signature 4",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-18T10:36:16Z",
  "updated_at": "2017-09-30T17:53:20Z"
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `content` | String | Message signature content |
|  | `is_active` | String | Message signature activation status (true or false) |


## Delete a single message signature

```sh
curl https://app.textus.com/api/message_signatures/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "account_id": 152,
  "content": "signature 5",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-07T15:49:46Z",
  "updated_at": "2017-10-01T14:02:39Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Message Templates


## List all message templates for the current account

```sh
curl https://app.textus.com/api/message_templates \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "content": "Hello {{contact.first_name}}",
    "title": "Hello Template",
    "owner_id": 153,
    "owner_type": "Account",
    "id": 42,
    "created_at": "2017-09-03T20:44:12Z",
    "updated_at": "2017-10-01T13:21:34Z",
    "account_id": 42
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against title and content) |


## Create a new message template

```sh
curl https://app.textus.com/api/message_templates \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"title":null,"content":null}'
```


> The above command returns JSON:

```json
{
  "content": "Hello {{contact.first_name}}",
  "title": "Hello Template",
  "owner_id": 154,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-09-09T04:22:12Z",
  "updated_at": "2017-10-01T23:53:31Z",
  "account_id": 42
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `title` | String | Message template title |
| ✓ | `content` | String | Message template content |
|  | `owner_type` | String | Org Admins can define if a template is made available to all users in the organization or an account |


## Retrieve a single message template

```sh
curl https://app.textus.com/api/message_templates/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "content": "Hello {{contact.first_name}}",
  "title": "Hello Template",
  "owner_id": 155,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-09-02T10:39:52Z",
  "updated_at": "2017-10-02T05:34:22Z",
  "account_id": 42
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single message template

```sh
curl https://app.textus.com/api/message_templates/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "content": "Hello {{contact.first_name}}",
  "title": "Hello Template",
  "owner_id": 156,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-09-10T02:35:25Z",
  "updated_at": "2017-10-01T03:38:11Z",
  "account_id": 42
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `title` | String | Message template title |
|  | `content` | String | Message template content |
|  | `owner_type` | String | Org Admins can define if a template is made available to all users in the organization or an account |
|  | `is_active` | String | Message template activation status (true or false) |


## Delete a single message template

```sh
curl https://app.textus.com/api/message_templates/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "content": "Hello {{contact.first_name}}",
  "title": "Hello Template",
  "owner_id": 157,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-09-20T22:46:20Z",
  "updated_at": "2017-10-02T00:37:28Z",
  "account_id": 42
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Messages


## List all messages for the current account

```sh
curl https://app.textus.com/api/messages \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "content": "hello",
    "read": false,
    "broadcast_id": null,
    "status": "created",
    "deliver_at": null,
    "from_autoresponse": null,
    "id": 42,
    "created_at": "2017-09-19T09:39:38Z",
    "updated_at": "2017-10-01T12:45:56Z",
    "sender_id": 23,
    "receiver_id": 11,
    "sender_type": "User",
    "receiver_type": "Contact",
    "sender_phone": "+12230000052",
    "sender_name": "Quickleft User",
    "receiver_phone": "+12230000054",
    "receiver_name": "Quickleft Contact",
    "image_url": null,
    "thumb_url": null
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against message body) |
|  | `last_message_to_each_contact` | Virtus::Attribute::Boolean | Displays the last message to each unique contact |
|  | `scope` | String | Filter messages by: 'flagged', 'unread', 'with_notes', or 'scheduled' |


## Create and send a message

```sh
curl https://app.textus.com/api/messages \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"content":null,"sender":null,"receiver":null}'
```


> The above command returns JSON:

```json
{
  "content": "hello",
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2017-10-01T06:57:28Z",
  "updated_at": "2017-10-01T23:24:53Z",
  "sender_id": 24,
  "receiver_id": 12,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000056",
  "sender_name": "Quickleft User",
  "receiver_phone": "+12230000058",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `content` | String | The message content |
| ✓ | `sender` | String | The sending user's id or email address |
| ✓ | `receiver` | String |         The receiving contact's id or phone number
        (international format -- must begin with a + sign)
        If no contact exists with that phone number, one will be created automatically
        with first name 'New' and last name 'Contact.'
 |
|  | `deliver_at` | DateTime | Delivery Date and Time (to schedule messages) |
|  | `file` | File | Image attachment to be sent with message |


## List all messages to and from the specified contact

```sh
curl https://app.textus.com/api/messages/:contact_id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "content": "hello",
    "read": false,
    "broadcast_id": null,
    "status": "created",
    "deliver_at": null,
    "from_autoresponse": null,
    "id": 42,
    "created_at": "2017-09-04T13:15:12Z",
    "updated_at": "2017-09-30T15:37:23Z",
    "sender_id": 25,
    "receiver_id": 13,
    "sender_type": "User",
    "receiver_type": "Contact",
    "sender_phone": "+12230000060",
    "sender_name": "Quickleft User",
    "receiver_phone": "+12230000062",
    "receiver_name": "Quickleft Contact",
    "image_url": null,
    "thumb_url": null
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |


## Update a message's read status

```sh
curl https://app.textus.com/api/messages/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{"read":null}'
```


> The above command returns JSON:

```json
{
  "content": "hello",
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2017-09-12T01:04:11Z",
  "updated_at": "2017-10-01T17:20:34Z",
  "sender_id": 26,
  "receiver_id": 14,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000064",
  "sender_name": "Quickleft User",
  "receiver_phone": "+12230000066",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `read` | Virtus::Attribute::Boolean | Message read status (true or false) |


## Delete a scheduled message

```sh
curl https://app.textus.com/api/messages/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "content": "hello",
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2017-09-05T09:06:52Z",
  "updated_at": "2017-10-01T02:30:56Z",
  "sender_id": 27,
  "receiver_id": 15,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000068",
  "sender_name": "Quickleft User",
  "receiver_phone": "+12230000070",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_




# Opt Outs


## List all opt outs for the current organization

```sh
curl https://app.textus.com/api/opt_outs \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "organization_id": 207,
    "opt_in": false,
    "id": 42,
    "created_at": "2017-09-18T00:41:32Z",
    "updated_at": "2017-09-30T08:25:43Z",
    "phone": "12230000071"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `q` | String | Search Query (searches against phone) |


## Create a new opt out

```sh
curl https://app.textus.com/api/opt_outs \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"phone":null}'
```


> The above command returns JSON:

```json
{
  "organization_id": 208,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-12T15:37:23Z",
  "updated_at": "2017-10-01T21:37:53Z",
  "phone": "12230000072"
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `phone` | String | Opt out phone number |
|  | `country` | String | Two-digit country code for opt out phone number |


## Retrieve a single opt out

```sh
curl https://app.textus.com/api/opt_outs/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "organization_id": 209,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-21T18:38:21Z",
  "updated_at": "2017-09-30T18:46:43Z",
  "phone": "12230000073"
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Delete a single opt out

```sh
curl https://app.textus.com/api/opt_outs/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "organization_id": 210,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-29T01:47:30Z",
  "updated_at": "2017-10-01T13:41:35Z",
  "phone": "12230000074"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_


## Update a single opt out

```sh
curl https://app.textus.com/api/opt_outs/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "organization_id": 211,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-11T19:21:53Z",
  "updated_at": "2017-10-01T21:24:49Z",
  "phone": "12230000075"
}
```


### HTTP Method

`PUT`

### Properties

_N/A_




# Tags


## List all tags for an account

```sh
curl https://app.textus.com/api/tags \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  "deleniti",
  "et",
  "qui",
  "ex"
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |




# Users


## List all users for the current account

```sh
curl https://app.textus.com/api/users \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "email": "sheldon@trompschowalter.biz",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 173,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2017-09-02T18:33:48Z",
    "updated_at": "2017-10-01T06:16:18Z"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |
|  | `include_archived` | Virtus::Attribute::Boolean | Include archived users |


## Retrieve a single user

```sh
curl https://app.textus.com/api/users/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "email": "dalton@farrellondricka.co",
  "first_name": "Quickleft",
  "last_name": "User",
  "account_id": 174,
  "role": "standard_user",
  "archived": false,
  "last_sign_in_at": null,
  "should_play_audio": true,
  "forward_messages": "never",
  "terms_accepted": false,
  "id": 42,
  "created_at": "2017-09-19T11:48:42Z",
  "updated_at": "2017-09-30T17:04:44Z"
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Retrieve the current user

```sh
curl https://app.textus.com/api/users/current \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "email": "ruthie_funk@boehm.io",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 175,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2017-09-13T06:57:31Z",
    "updated_at": "2017-10-01T16:54:33Z"
  }
]
```


### HTTP Method

`GET`

### Properties

_N/A_




# Web Hooks


## List all web hooks accessible by the current user

```sh
curl https://app.textus.com/api/web_hooks \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
[
  {
    "url": "http://httpbin.org/post",
    "account_id": 176,
    "version": "2",
    "secret": "notasecret",
    "id": 42,
    "created_at": "2017-09-03T09:32:52Z",
    "updated_at": "2017-09-30T20:36:14Z"
  }
]
```


### HTTP Method

`GET`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `page` | Integer | Page offset to fetch. |
|  | `per_page` | Integer | Number of results to return per page. |
|  | `offset` | Integer | Pad a number of results. |


## Create a new web hook

```sh
curl https://app.textus.com/api/web_hooks \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request POST \ 
     --data '{"url":"http://example.com/aliza"}'
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "account_id": 177,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-09-18T14:01:35Z",
  "updated_at": "2017-10-01T03:11:55Z"
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `url` | String | URL where web hook payload will be delivered |
|  | `version` | String | Version of the web hook payload to deliver |
|  | `account_id` | Integer | ID of the account where webhook is intended to be implemented |


## Retrieve a single web hook

```sh
curl https://app.textus.com/api/web_hooks/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "account_id": 178,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-09-05T04:35:04Z",
  "updated_at": "2017-09-30T09:24:03Z"
}
```


### HTTP Method

`GET`

### Properties

_N/A_


## Update a single web hook

```sh
curl https://app.textus.com/api/web_hooks/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request PUT \ 
     --data '{}'
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "account_id": 179,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-09-18T18:39:37Z",
  "updated_at": "2017-09-30T18:40:42Z"
}
```


### HTTP Method

`PUT`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `url` | String | Web hook URL (must be a valid URL) |


## Delete a single web hook

```sh
curl https://app.textus.com/api/web_hooks/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json \ 
     --request DELETE
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "account_id": 180,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-09-03T06:00:38Z",
  "updated_at": "2017-10-01T16:39:13Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_





