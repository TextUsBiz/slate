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
    "sender_email": "sideshow.bob@simpsons.com",
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
    "account_id": 1,
    "message": "Reply 1",
    "is_active": false,
    "id": 42,
    "created_at": "2017-10-08T00:40:17Z",
    "updated_at": "2017-10-23T22:31:14Z"
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
  "account_id": 2,
  "message": "Reply 2",
  "is_active": false,
  "id": 42,
  "created_at": "2017-10-04T15:35:18Z",
  "updated_at": "2017-10-23T05:31:38Z"
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
  "account_id": 3,
  "message": "Reply 3",
  "is_active": false,
  "id": 42,
  "created_at": "2017-10-22T09:23:49Z",
  "updated_at": "2017-10-23T10:49:52Z"
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
  "account_id": 4,
  "message": "Reply 4",
  "is_active": false,
  "id": 42,
  "created_at": "2017-10-16T14:00:01Z",
  "updated_at": "2017-10-23T17:38:52Z"
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
  "account_id": 5,
  "message": "Reply 5",
  "is_active": false,
  "id": 42,
  "created_at": "2017-10-06T13:05:24Z",
  "updated_at": "2017-10-23T04:02:20Z"
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
    "organization_id": 6,
    "id": 42,
    "created_at": "2017-10-09T11:51:26Z",
    "updated_at": "2017-10-22T15:54:23Z",
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
  "organization_id": 7,
  "id": 42,
  "created_at": "2017-10-02T04:14:45Z",
  "updated_at": "2017-10-24T04:46:05Z",
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
  "organization_id": 8,
  "id": 42,
  "created_at": "2017-10-05T22:25:00Z",
  "updated_at": "2017-10-23T08:43:10Z",
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
    "user_id": 1,
    "group_id": 1,
    "message_body": "Test Broadcast",
    "status": null,
    "deliver_at": "2017-10-24 17:13:09 UTC",
    "id": 42,
    "created_at": "2017-10-02T06:34:02Z",
    "updated_at": "2017-10-24T03:42:50Z",
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
  "user_id": 2,
  "group_id": 2,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-24 17:13:09 UTC",
  "id": 42,
  "created_at": "2017-10-23T05:59:08Z",
  "updated_at": "2017-10-22T18:31:03Z",
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
  "user_id": 3,
  "group_id": 3,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-24 17:13:09 UTC",
  "id": 42,
  "created_at": "2017-10-06T06:53:20Z",
  "updated_at": "2017-10-23T09:20:42Z",
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
  "user_id": 4,
  "group_id": 4,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2017-10-24 17:13:09 UTC",
  "id": 42,
  "created_at": "2017-10-16T08:04:58Z",
  "updated_at": "2017-10-24T01:26:53Z",
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
    "account_id": 14,
    "flagged": false,
    "notes": null,
    "phone": "12230000018",
    "country": "US",
    "line_type": null,
    "id": 42,
    "created_at": "2017-10-19T00:38:38Z",
    "updated_at": "2017-10-23T21:45:05Z",
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
  "account_id": 15,
  "flagged": false,
  "notes": null,
  "phone": "12230000020",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-10-21T04:43:45Z",
  "updated_at": "2017-10-23T02:37:59Z",
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
  "account_id": 16,
  "flagged": false,
  "notes": null,
  "phone": "12230000022",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-10-21T19:45:29Z",
  "updated_at": "2017-10-23T15:53:17Z",
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
  "account_id": 17,
  "flagged": false,
  "notes": null,
  "phone": "12230000024",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-10-15T21:25:14Z",
  "updated_at": "2017-10-24T03:16:05Z",
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
  "account_id": 18,
  "flagged": false,
  "notes": null,
  "phone": "12230000026",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2017-10-16T11:57:36Z",
  "updated_at": "2017-10-23T06:37:03Z",
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
  "name": "group 5",
  "account_id": 19,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-07T19:41:19Z",
  "updated_at": "2017-10-23T02:20:04Z",
  "size": 0
}
```


### HTTP Method

`POST`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | `name` | String | Name of the group |


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
    "name": "group 6",
    "account_id": 20,
    "locked": false,
    "id": 42,
    "created_at": "2017-10-07T03:09:45Z",
    "updated_at": "2017-10-24T04:01:17Z",
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
  "name": "group 7",
  "account_id": 21,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-11T09:35:50Z",
  "updated_at": "2017-10-22T08:19:50Z",
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
  "name": "group 8",
  "account_id": 22,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-21T21:33:40Z",
  "updated_at": "2017-10-22T20:35:49Z",
  "size": 0
}
```


### HTTP Method

`DELETE`

### Properties

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | `delete_contacts` | Virtus::Attribute::Boolean | Whether or not to delete all contacts in the group |


## Retrieve a single group

```sh
curl https://app.textus.com/api/groups/:id \ 
     --user {username}:{api_key} \ 
     --header "Content-Type": "application/json
```


> The above command returns JSON:

```json
{
  "name": "group 9",
  "account_id": 23,
  "locked": false,
  "id": 42,
  "created_at": "2017-09-27T10:24:49Z",
  "updated_at": "2017-10-22T12:14:22Z",
  "size": 0
}
```


### HTTP Method

`GET`

### Properties

_N/A_


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
  "account_id": 24,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-03T01:42:21Z",
  "updated_at": "2017-10-23T19:49:10Z",
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
  "account_id": 25,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-01T11:13:03Z",
  "updated_at": "2017-10-22T17:44:11Z",
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
  "account_id": 26,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-12T08:53:45Z",
  "updated_at": "2017-10-22T20:29:34Z",
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
  "account_id": 27,
  "locked": false,
  "id": 42,
  "created_at": "2017-10-23T16:48:27Z",
  "updated_at": "2017-10-23T21:35:43Z",
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
    "account_id": 28,
    "content": "signature 1",
    "is_active": null,
    "id": 42,
    "created_at": "2017-09-25T20:14:37Z",
    "updated_at": "2017-10-24T03:57:26Z"
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
  "account_id": 29,
  "content": "signature 2",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-30T02:45:28Z",
  "updated_at": "2017-10-23T14:55:52Z"
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
  "account_id": 30,
  "content": "signature 3",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-24T15:33:43Z",
  "updated_at": "2017-10-23T00:05:06Z"
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
  "account_id": 31,
  "content": "signature 4",
  "is_active": null,
  "id": 42,
  "created_at": "2017-09-29T01:41:32Z",
  "updated_at": "2017-10-23T20:01:25Z"
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
  "account_id": 32,
  "content": "signature 5",
  "is_active": null,
  "id": 42,
  "created_at": "2017-10-20T09:08:58Z",
  "updated_at": "2017-10-23T00:42:21Z"
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
    "owner_id": 33,
    "owner_type": "Account",
    "id": 42,
    "created_at": "2017-10-09T03:18:41Z",
    "updated_at": "2017-10-22T18:45:20Z",
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
  "owner_id": 34,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-10-02T22:21:38Z",
  "updated_at": "2017-10-24T03:37:26Z",
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
  "owner_id": 35,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-10-15T10:49:01Z",
  "updated_at": "2017-10-24T03:30:51Z",
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
  "owner_id": 36,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-10-23T00:03:50Z",
  "updated_at": "2017-10-22T08:32:00Z",
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
  "owner_id": 37,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2017-09-25T05:46:48Z",
  "updated_at": "2017-10-22T23:49:02Z",
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
    "created_at": "2017-09-29T09:46:51Z",
    "updated_at": "2017-10-24T03:17:56Z",
    "sender_id": 5,
    "receiver_id": 1,
    "sender_type": "User",
    "receiver_type": "Contact",
    "sender_phone": "+12230000052",
    "sender_name": "Quickleft User",
    "sender_email": "mayra.kuphal@stokeshand.net",
    "receiver_phone": "+12230000054",
    "receiver_name": "Quickleft Contact",
    "image_url": null,
    "thumb_url": null,
    "media_urls": null
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
  "created_at": "2017-10-11T18:52:28Z",
  "updated_at": "2017-10-22T13:54:24Z",
  "sender_id": 6,
  "receiver_id": 2,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000056",
  "sender_name": "Quickleft User",
  "sender_email": "rupert_buckridge@roberts.name",
  "receiver_phone": "+12230000058",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null,
  "media_urls": null
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
    "created_at": "2017-10-07T19:09:07Z",
    "updated_at": "2017-10-23T17:34:11Z",
    "sender_id": 7,
    "receiver_id": 3,
    "sender_type": "User",
    "receiver_type": "Contact",
    "sender_phone": "+12230000060",
    "sender_name": "Quickleft User",
    "sender_email": "crawford@labadiebernier.co",
    "receiver_phone": "+12230000062",
    "receiver_name": "Quickleft Contact",
    "image_url": null,
    "thumb_url": null,
    "media_urls": null
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
  "created_at": "2017-10-01T06:56:26Z",
  "updated_at": "2017-10-22T07:03:10Z",
  "sender_id": 8,
  "receiver_id": 4,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000064",
  "sender_name": "Quickleft User",
  "sender_email": "elsie@wisoky.net",
  "receiver_phone": "+12230000066",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null,
  "media_urls": null
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
  "created_at": "2017-10-08T01:10:30Z",
  "updated_at": "2017-10-23T06:53:40Z",
  "sender_id": 9,
  "receiver_id": 5,
  "sender_type": "User",
  "receiver_type": "Contact",
  "sender_phone": "+12230000068",
  "sender_name": "Quickleft User",
  "sender_email": "liana.ruecker@runolfon.net",
  "receiver_phone": "+12230000070",
  "receiver_name": "Quickleft Contact",
  "image_url": null,
  "thumb_url": null,
  "media_urls": null
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
    "organization_id": 61,
    "opt_in": false,
    "id": 42,
    "created_at": "2017-10-20T09:56:27Z",
    "updated_at": "2017-10-22T12:01:19Z",
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
  "organization_id": 62,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-10-18T18:15:38Z",
  "updated_at": "2017-10-22T21:00:37Z",
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
  "organization_id": 63,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-27T05:05:57Z",
  "updated_at": "2017-10-22T14:24:48Z",
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
  "organization_id": 64,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-09-30T12:02:38Z",
  "updated_at": "2017-10-22T18:38:27Z",
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
  "organization_id": 65,
  "opt_in": false,
  "id": 42,
  "created_at": "2017-10-18T01:10:06Z",
  "updated_at": "2017-10-24T02:12:23Z",
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
  "nostrum",
  "repellendus",
  "incidunt",
  "deleniti"
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
    "email": "matteo.lemke@hauck.info",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 53,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2017-10-08T17:42:53Z",
    "updated_at": "2017-10-22T09:33:22Z"
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
  "email": "jamison@dietrich.biz",
  "first_name": "Quickleft",
  "last_name": "User",
  "account_id": 54,
  "role": "standard_user",
  "archived": false,
  "last_sign_in_at": null,
  "should_play_audio": true,
  "forward_messages": "never",
  "terms_accepted": false,
  "id": 42,
  "created_at": "2017-10-23T04:45:25Z",
  "updated_at": "2017-10-22T12:33:42Z"
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
    "email": "baby@schulist.net",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 55,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2017-10-21T23:51:22Z",
    "updated_at": "2017-10-23T07:41:01Z"
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
    "account_id": 56,
    "version": "2",
    "secret": "notasecret",
    "id": 42,
    "created_at": "2017-10-19T01:57:29Z",
    "updated_at": "2017-10-22T06:08:02Z"
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
     --data '{"url":"http://example.com/pedro"}'
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "account_id": 57,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-10-12T15:57:55Z",
  "updated_at": "2017-10-22T18:36:55Z"
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
  "account_id": 58,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-10-02T03:35:25Z",
  "updated_at": "2017-10-22T19:28:13Z"
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
  "account_id": 59,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-10-15T23:14:40Z",
  "updated_at": "2017-10-24T03:33:14Z"
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
  "account_id": 60,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2017-10-06T21:14:41Z",
  "updated_at": "2017-10-24T01:51:41Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_





