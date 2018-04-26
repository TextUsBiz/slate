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
    "account_id": 1,
    "message": "Reply 1",
    "is_active": false,
    "id": 42,
    "created_at": "2018-04-20T19:04:48Z",
    "updated_at": "2018-04-25T13:00:49Z"
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
  "created_at": "2018-04-10T20:06:40Z",
  "updated_at": "2018-04-25T20:08:46Z"
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
  "created_at": "2018-04-19T01:42:59Z",
  "updated_at": "2018-04-25T21:01:24Z"
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
  "created_at": "2018-04-24T07:37:49Z",
  "updated_at": "2018-04-25T09:02:26Z"
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
  "created_at": "2018-04-26T02:09:33Z",
  "updated_at": "2018-04-24T13:16:44Z"
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
    "created_at": "2018-03-28T20:26:23Z",
    "updated_at": "2018-04-26T02:01:50Z",
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
  "created_at": "2018-04-05T10:40:52Z",
  "updated_at": "2018-04-25T03:40:26Z",
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
  "created_at": "2018-04-06T02:33:34Z",
  "updated_at": "2018-04-25T05:13:06Z",
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
    "deliver_at": "2018-04-26 17:38:03 UTC",
    "id": 42,
    "created_at": "2018-04-12T17:13:55Z",
    "updated_at": "2018-04-25T20:44:17Z",
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
  "deliver_at": "2018-04-26 17:38:03 UTC",
  "id": 42,
  "created_at": "2018-04-14T00:27:04Z",
  "updated_at": "2018-04-24T09:27:39Z",
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
  "deliver_at": "2018-04-26 17:38:03 UTC",
  "id": 42,
  "created_at": "2018-04-02T09:51:17Z",
  "updated_at": "2018-04-25T16:37:52Z",
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
  "deliver_at": "2018-04-26 17:38:03 UTC",
  "id": 42,
  "created_at": "2018-04-18T12:23:03Z",
  "updated_at": "2018-04-25T16:00:22Z",
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
    "account_id": 14,
    "flagged": false,
    "notes": null,
    "phone": "12230000018",
    "country": "US",
    "line_type": null,
    "id": 42,
    "created_at": "2018-04-16T23:31:29Z",
    "updated_at": "2018-04-24T07:34:54Z",
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
  "account_id": 15,
  "flagged": false,
  "notes": null,
  "phone": "12230000020",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-14T01:16:51Z",
  "updated_at": "2018-04-24T17:05:45Z",
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
  "account_id": 16,
  "flagged": false,
  "notes": null,
  "phone": "12230000022",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-03-30T09:53:38Z",
  "updated_at": "2018-04-24T13:43:51Z",
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
  "account_id": 17,
  "flagged": false,
  "notes": null,
  "phone": "12230000024",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-11T07:40:17Z",
  "updated_at": "2018-04-25T19:45:01Z",
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
  "account_id": 18,
  "flagged": false,
  "notes": null,
  "phone": "12230000026",
  "country": "US",
  "line_type": null,
  "id": 42,
  "created_at": "2018-04-25T03:33:20Z",
  "updated_at": "2018-04-25T13:20:46Z",
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
  "account_id": 19,
  "locked": false,
  "id": 42,
  "created_at": "2018-03-28T06:16:42Z",
  "updated_at": "2018-04-24T22:19:45Z",
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
    "created_at": "2018-04-18T16:15:09Z",
    "updated_at": "2018-04-24T06:23:56Z",
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
  "created_at": "2018-03-31T02:05:25Z",
  "updated_at": "2018-04-24T11:44:13Z",
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
  "created_at": "2018-04-06T21:02:50Z",
  "updated_at": "2018-04-25T20:07:12Z",
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
  "created_at": "2018-04-01T18:38:03Z",
  "updated_at": "2018-04-24T19:28:15Z",
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
  "created_at": "2018-04-02T04:51:13Z",
  "updated_at": "2018-04-25T11:47:00Z",
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
  "created_at": "2018-04-22T17:57:35Z",
  "updated_at": "2018-04-26T02:23:00Z",
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
  "created_at": "2018-04-26T03:24:15Z",
  "updated_at": "2018-04-25T03:22:44Z",
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
  "created_at": "2018-04-23T02:42:26Z",
  "updated_at": "2018-04-24T23:59:51Z",
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
    "account_id": 28,
    "id": 42,
    "created_at": "2018-04-19T08:02:36Z",
    "updated_at": "2018-04-25T04:47:01Z"
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
  "account_id": 29,
  "id": 42,
  "created_at": "2018-03-29T06:06:29Z",
  "updated_at": "2018-04-26T04:00:09Z"
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
  "account_id": 30,
  "id": 42,
  "created_at": "2018-04-07T15:28:54Z",
  "updated_at": "2018-04-25T14:08:05Z"
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
  "account_id": 31,
  "id": 42,
  "created_at": "2018-04-22T01:54:02Z",
  "updated_at": "2018-04-24T18:35:50Z"
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
  "account_id": 32,
  "id": 42,
  "created_at": "2018-04-10T06:40:19Z",
  "updated_at": "2018-04-25T18:33:29Z"
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
    "created_at": "2018-04-04T07:39:55Z",
    "updated_at": "2018-04-24T20:19:55Z",
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
  "created_at": "2018-03-29T10:23:50Z",
  "updated_at": "2018-04-24T11:52:32Z",
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
  "created_at": "2018-04-05T17:17:23Z",
  "updated_at": "2018-04-25T19:28:15Z",
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
  "created_at": "2018-04-09T09:39:48Z",
  "updated_at": "2018-04-24T14:54:35Z",
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
  "created_at": "2018-04-04T16:48:34Z",
  "updated_at": "2018-04-25T02:56:02Z",
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
    "created_at": "2018-03-29T07:43:10Z",
    "updated_at": "2018-04-24T17:34:38Z",
    "sender_id": 5,
    "receiver_id": 1,
    "sender_type": "User",
    "receiver_type": "Contact",
    "content": "hello",
    "sender_phone": "+12230000051",
    "sender_name": "Quickleft User",
    "sender_email": "thaddeus.bahringer@botsford.net",
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
  "created_at": "2018-04-14T08:19:31Z",
  "updated_at": "2018-04-25T09:59:57Z",
  "sender_id": 6,
  "receiver_id": 2,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000055",
  "sender_name": "Quickleft User",
  "sender_email": "aaron_pagac@leuschkewilkinson.name",
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
| ✓ | `receiver` | String |         The receiving contact's id or phone number
        (international format -- must begin with a + sign)
        If no contact exists with that phone number, one will be created automatically
        with no name.
 |
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
    "created_at": "2018-04-05T01:12:43Z",
    "updated_at": "2018-04-24T23:45:24Z",
    "sender_id": 7,
    "receiver_id": 3,
    "sender_type": "User",
    "receiver_type": "Contact",
    "content": "hello",
    "sender_phone": "+12230000059",
    "sender_name": "Quickleft User",
    "sender_email": "gabe.lockman@veumokuneva.biz",
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
  "created_at": "2018-04-22T21:02:30Z",
  "updated_at": "2018-04-25T01:03:05Z",
  "sender_id": 8,
  "receiver_id": 4,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000063",
  "sender_name": "Quickleft User",
  "sender_email": "sebastian.hauck@roobbreitenberg.net",
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
  "created_at": "2018-03-31T15:56:16Z",
  "updated_at": "2018-04-24T06:15:08Z",
  "sender_id": 9,
  "receiver_id": 5,
  "sender_type": "User",
  "receiver_type": "Contact",
  "content": "hello",
  "sender_phone": "+12230000067",
  "sender_name": "Quickleft User",
  "sender_email": "haylee.parisian@lehner.io",
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
    "organization_id": 56,
    "opt_in": false,
    "id": 42,
    "created_at": "2018-04-22T10:11:26Z",
    "updated_at": "2018-04-24T09:24:56Z",
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
  "organization_id": 57,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-13T05:00:20Z",
  "updated_at": "2018-04-25T14:38:36Z",
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
  "organization_id": 58,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-05T16:51:04Z",
  "updated_at": "2018-04-25T01:43:17Z",
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
  "organization_id": 59,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-20T00:46:50Z",
  "updated_at": "2018-04-24T10:58:34Z",
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
  "organization_id": 60,
  "opt_in": false,
  "id": 42,
  "created_at": "2018-04-20T11:26:42Z",
  "updated_at": "2018-04-25T09:57:43Z",
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
  "et",
  "fugit",
  "odio",
  "reiciendis"
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
    "email": "lafayette@beatty.name",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 48,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2018-03-27T20:57:32Z",
    "updated_at": "2018-04-24T08:15:44Z",
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
  "email": "savannah_emmerich@deckow.info",
  "first_name": "Quickleft",
  "last_name": "User",
  "account_id": 49,
  "role": "standard_user",
  "archived": false,
  "last_sign_in_at": null,
  "should_play_audio": true,
  "forward_messages": "never",
  "terms_accepted": false,
  "id": 42,
  "created_at": "2018-04-08T19:41:32Z",
  "updated_at": "2018-04-25T14:29:14Z",
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
    "email": "unique@howecain.name",
    "first_name": "Quickleft",
    "last_name": "User",
    "account_id": 50,
    "role": "standard_user",
    "archived": false,
    "last_sign_in_at": null,
    "should_play_audio": true,
    "forward_messages": "never",
    "terms_accepted": false,
    "id": 42,
    "created_at": "2018-03-27T12:46:13Z",
    "updated_at": "2018-04-24T13:23:53Z",
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
    "web_hookable_id": 51,
    "version": "2",
    "secret": "notasecret",
    "id": 42,
    "created_at": "2018-04-20T20:42:35Z",
    "updated_at": "2018-04-26T03:36:42Z"
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
     --data '{"url":"http://example.com/leta"}'
```


> The above command returns JSON:

```json
{
  "url": "http://httpbin.org/post",
  "web_hookable_type": "Account",
  "web_hookable_id": 52,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-14T03:40:16Z",
  "updated_at": "2018-04-24T17:56:21Z"
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
  "web_hookable_id": 53,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-13T09:18:25Z",
  "updated_at": "2018-04-24T06:07:25Z"
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
  "web_hookable_id": 54,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-14T19:53:00Z",
  "updated_at": "2018-04-25T08:58:01Z"
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
  "web_hookable_id": 55,
  "version": "2",
  "secret": "notasecret",
  "id": 42,
  "created_at": "2018-04-04T00:29:30Z",
  "updated_at": "2018-04-26T02:36:39Z"
}
```


### HTTP Method

`DELETE`

### Properties

_N/A_





