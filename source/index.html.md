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
    "web_hookable_type": "[Account|Organization|LinkedAccount]",
    "web_hookable_id":1,
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

1. Extract the signature from the header.
2. Prepare the signed payload. The signed body is the request body.
3. Determine the expected signature value. Compute a HMAC with the SHA256 hash
   function, using your webhook's signing secret as the key and the request
   body string as the message.
4. Compare your computed signature with the value included in the
   `X-TextUs-Signature` field. If they match then you can be sure the request
   is authentic. In other words,
   `OpenSSL::HMAC.digest(OpenSSL::Digest.new('sha256'), secret_key, request_body) == X-TextUs-Signature`


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
    "account_id": 117,
    "message": "Reply 1",
    "is_active": false,
    "id": 42,
    "created_at": "2018-04-09T01:04:49Z",
    "updated_at": "2018-04-30T11:35:19Z"
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
  "account_id": 118,
  "message": "Reply 2",
  "is_active": false,
  "id": 42,
  "created_at": "2018-04-13T02:42:48Z",
  "updated_at": "2018-04-30T09:56:01Z"
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
  "account_id": 119,
  "message": "Reply 3",
  "is_active": false,
  "id": 42,
  "created_at": "2018-04-05T03:01:44Z",
  "updated_at": "2018-05-01T06:12:38Z"
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
  "account_id": 120,
  "message": "Reply 4",
  "is_active": false,
  "id": 42,
  "created_at": "2018-04-28T01:25:14Z",
  "updated_at": "2018-05-01T13:08:35Z"
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
  "account_id": 121,
  "message": "Reply 5",
  "is_active": false,
  "id": 42,
  "created_at": "2018-04-20T22:42:23Z",
  "updated_at": "2018-05-01T16:25:50Z"
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
    "organization_id": 145,
    "id": 42,
    "created_at": "2018-04-13T18:09:10Z",
    "updated_at": "2018-05-01T08:05:45Z",
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
  "organization_id": 146,
  "id": 42,
  "created_at": "2018-04-17T00:49:47Z",
  "updated_at": "2018-05-01T07:41:34Z",
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
  "organization_id": 147,
  "id": 42,
  "created_at": "2018-04-30T15:39:06Z",
  "updated_at": "2018-04-30T07:25:15Z",
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
    "user_id": 45,
    "group_id": 9,
    "message_body": "Test Broadcast",
    "status": null,
    "deliver_at": "2018-05-02 17:27:55 UTC",
    "id": 42,
    "created_at": "2018-04-12T14:34:00Z",
    "updated_at": "2018-05-01T21:45:35Z",
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
  "user_id": 46,
  "group_id": 10,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2018-05-02 17:27:55 UTC",
  "id": 42,
  "created_at": "2018-04-20T04:30:27Z",
  "updated_at": "2018-05-01T18:14:10Z",
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
  "user_id": 47,
  "group_id": 11,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2018-05-02 17:27:55 UTC",
  "id": 42,
  "created_at": "2018-04-28T06:05:11Z",
  "updated_at": "2018-04-30T13:19:26Z",
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
  "user_id": 48,
  "group_id": 12,
  "message_body": "Test Broadcast",
  "status": null,
  "deliver_at": "2018-05-02 17:27:55 UTC",
  "id": 42,
  "created_at": "2018-05-01T08:14:15Z",
  "updated_at": "2018-05-01T08:38:15Z",
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
    "business_name": null,
    "account_id": 130,
    "flagged": false,
    "notes": null,
    "phone": "12230000018",
    "country": "US",
    "line_type": null,
    "id": 42,
    "created_at": "2018-04-28T07:06:49Z",
    "updated_at": "2018-04-30T10:59:06Z",
    "first_name": "Quickleft",
    "last_name": "Contact",
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
     --data '{"phone":null}'
```


> The above command returns JSON:

```json
{
  "business_name": null,
  "account_id": 131,
  "flagged": false,
  "notes": null,
  "phone": "12230000020",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-18T16:40:48Z",
  "updated_at": "2018-05-01T12:40:34Z",
  "first_name": "Quickleft",
  "last_name": "Contact",
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
|  | `first_name` | String | Contact first name |
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
  "business_name": null,
  "account_id": 132,
  "flagged": false,
  "notes": null,
  "phone": "12230000022",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-02T20:21:17Z",
  "updated_at": "2018-05-02T04:42:38Z",
  "first_name": "Quickleft",
  "last_name": "Contact",
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
  "business_name": null,
  "account_id": 133,
  "flagged": false,
  "notes": null,
  "phone": "12230000024",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-21T08:42:58Z",
  "updated_at": "2018-05-01T02:43:58Z",
  "first_name": "Quickleft",
  "last_name": "Contact",
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
  "business_name": null,
  "account_id": 134,
  "flagged": false,
  "notes": null,
  "phone": "12230000026",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-10T03:31:05Z",
  "updated_at": "2018-04-30T10:50:59Z",
  "first_name": "Quickleft",
  "last_name": "Contact",
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
  "account_id": 135,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-30T15:52:01Z",
  "updated_at": "2018-04-30T21:45:33Z",
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
    "account_id": 136,
    "locked": false,
    "id": 42,
    "created_at": "2018-04-24T17:24:06Z",
    "updated_at": "2018-04-30T06:12:58Z",
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
  "account_id": 137,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-05T07:59:32Z",
  "updated_at": "2018-04-30T16:22:26Z",
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
  "account_id": 138,
  "locked": false,
  "id": 42,
  "created_at": "2018-05-01T02:23:24Z",
  "updated_at": "2018-05-01T04:56:13Z",
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
  "account_id": 139,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-21T16:59:54Z",
  "updated_at": "2018-05-01T19:14:21Z",
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
  "account_id": 140,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-26T05:43:37Z",
  "updated_at": "2018-04-30T22:17:58Z",
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
  "account_id": 141,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-06T22:12:59Z",
  "updated_at": "2018-05-01T11:17:00Z",
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
  "account_id": 142,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-24T07:42:22Z",
  "updated_at": "2018-05-01T08:50:20Z",
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
  "account_id": 143,
  "locked": false,
  "id": 42,
  "created_at": "2018-04-26T19:17:48Z",
  "updated_at": "2018-05-02T00:35:49Z",
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
    "content": "signature 1",
    "is_active": false,
    "account_id": 144,
    "id": 42,
    "created_at": "2018-04-27T23:01:19Z",
    "updated_at": "2018-05-01T22:58:00Z"
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
  "content": "signature 2",
  "is_active": false,
  "account_id": 145,
  "id": 42,
  "created_at": "2018-04-19T22:08:09Z",
  "updated_at": "2018-04-30T13:23:29Z"
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
  "content": "signature 3",
  "is_active": false,
  "account_id": 146,
  "id": 42,
  "created_at": "2018-04-12T22:51:52Z",
  "updated_at": "2018-05-01T09:59:50Z"
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
  "content": "signature 4",
  "is_active": false,
  "account_id": 147,
  "id": 42,
  "created_at": "2018-04-03T14:15:07Z",
  "updated_at": "2018-04-30T10:18:18Z"
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
  "content": "signature 5",
  "is_active": false,
  "account_id": 148,
  "id": 42,
  "created_at": "2018-04-04T01:10:01Z",
  "updated_at": "2018-04-30T19:05:46Z"
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
    "owner_id": 149,
    "owner_type": "Account",
    "id": 42,
    "created_at": "2018-04-28T17:33:07Z",
    "updated_at": "2018-05-01T20:47:33Z",
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
  "owner_id": 150,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2018-04-26T11:38:00Z",
  "updated_at": "2018-05-02T00:06:25Z",
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
  "owner_id": 151,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2018-04-13T11:19:27Z",
  "updated_at": "2018-04-30T17:19:23Z",
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
  "owner_id": 152,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2018-04-11T00:31:53Z",
  "updated_at": "2018-04-30T20:16:49Z",
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
  "owner_id": 153,
  "owner_type": "Account",
  "id": 42,
  "created_at": "2018-04-16T04:50:02Z",
  "updated_at": "2018-05-01T03:29:41Z",
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
    "read": false,
    "broadcast_id": null,
    "status": "created",
    "deliver_at": null,
    "from_autoresponse": null,
    "id": 42,
    "created_at": "2018-04-12T21:56:06Z",
    "updated_at": "2018-05-01T21:35:50Z",
    "sender_id": 49,
    "receiver_id": 111,
    "sender_type": "User",
    "receiver_type": "Contact",
    "content": "hello",
    "sender_phone": "+12230000051",
    "sender_name": "Quickleft User",
    "sender_email": "hailee@beahan.biz",
    "receiver_phone": "+12230000052",
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
     --data '{"sender":null,"receiver":null}'
```


> The above command returns JSON:

```json
{
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2018-04-20T13:38:41Z",
  "updated_at": "2018-04-30T12:31:10Z",
  "sender_id": 50,
  "receiver_id": 112,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000055",
  "sender_name": "Quickleft User",
  "sender_email": "anabelle@rutherfordmacgyver.biz",
  "receiver_phone": "+12230000056",
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
| ✓ | `sender` | String | The sending user's id or email address |
| ✓ | `receiver` | String | The receiving contact's id or phone number(international format e.g. must begin with a "+" sign)If no contact exists with that phone number, one will be created automaticallywith no name. |
|  | `content` | String | The message content |
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
    "read": false,
    "broadcast_id": null,
    "status": "created",
    "deliver_at": null,
    "from_autoresponse": null,
    "id": 42,
    "created_at": "2018-04-23T17:29:06Z",
    "updated_at": "2018-05-01T21:32:03Z",
    "sender_id": 51,
    "receiver_id": 113,
    "sender_type": "User",
    "receiver_type": "Contact",
    "content": "hello",
    "sender_phone": "+12230000059",
    "sender_name": "Quickleft User",
    "sender_email": "waylon_hodkiewicz@gulgowski.com",
    "receiver_phone": "+12230000060",
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
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2018-04-25T10:44:18Z",
  "updated_at": "2018-05-01T06:58:07Z",
  "sender_id": 52,
  "receiver_id": 114,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000063",
  "sender_name": "Quickleft User",
  "sender_email": "shaun@bergnaum.biz",
  "receiver_phone": "+12230000064",
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
  "read": false,
  "broadcast_id": null,
  "status": "created",
  "deliver_at": null,
  "from_autoresponse": null,
  "id": 42,
  "created_at": "2018-04-28T18:41:28Z",
  "updated_at": "2018-04-30T21:25:07Z",
  "sender_id": 53,
  "receiver_id": 115,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000067",
  "sender_name": "Quickleft User",
  "sender_email": "cheyanne_kshlerin@bradtke.co",
  "receiver_phone": "+12230000068",
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
    "organization_id": 195,
    "opt_in": false,
    "id": 42,
    "created_at": "2018-04-03T20:06:25Z",
    "updated_at": "2018-05-01T17:06:47Z",
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
  "organization_id": 196,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-06T09:59:56Z",
  "updated_at": "2018-05-01T09:02:46Z",
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
  "organization_id": 197,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-15T12:41:23Z",
  "updated_at": "2018-05-01T14:46:45Z",
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
  "organization_id": 198,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-02T20:52:08Z",
  "updated_at": "2018-04-30T21:57:51Z",
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
  "organization_id": 199,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-25T21:02:53Z",
  "updated_at": "2018-05-01T05:20:52Z",
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
  "expedita",
  "deleniti",
  "et",
  "quod"
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
    "email": "scottie@cruickshank.org",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 164,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2018-04-15T17:58:32Z",
    "updated_at": "2018-05-01T15:30:09Z",
    "web_push_enabled": false,
    "v3_enabled": true
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
  "email": "kenneth.murphy@schmittwisoky.net",
  "first_name": "Quickleft",
  "last_name": "User",
  "account_id": 165,
  "role": "standard_user",
  "archived": false,
  "last_sign_in_at": null,
  "should_play_audio": true,
  "forward_messages": "never",
  "terms_accepted": false,
  "id": 42,
  "created_at": "2018-04-12T18:54:27Z",
  "updated_at": "2018-04-30T12:26:20Z",
  "web_push_enabled": false,
  "v3_enabled": true
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
    "email": "daisy_trantow@ferrygorczany.co",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 166,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2018-04-16T07:13:24Z",
    "updated_at": "2018-04-30T08:36:14Z",
    "web_push_enabled": false,
    "v3_enabled": true
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
    "web_hookable_type": "Account",
    "web_hookable_id": 167,
    "version": "2",
    "secret": "notasecret",
    "id": 42,
    "created_at": "2018-04-22T11:17:14Z",
    "updated_at": "2018-05-01T06:45:53Z"
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
     --data '{"url":"http://example.com/teie"}'
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "web_hookable_type": "Account",
  "web_hookable_id": 168,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-06T16:22:39Z",
  "updated_at": "2018-04-30T11:48:01Z"
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
  "web_hookable_type": "Account",
  "web_hookable_id": 169,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-06T22:41:06Z",
  "updated_at": "2018-05-02T00:43:40Z"
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
  "web_hookable_type": "Account",
  "web_hookable_id": 170,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-27T20:17:32Z",
  "updated_at": "2018-05-01T06:40:21Z"
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
  "web_hookable_type": "Account",
  "web_hookable_id": 171,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-26T21:48:54Z",
  "updated_at": "2018-05-01T04:12:42Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_





