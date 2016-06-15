---
title: Overview Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
---

# Overview

The TextUs API is a RESTful API that allows your company to integrate your TextUs messages and contacts with your CRM or any other tool. The majority of functionality within our web application is accessible via the API, so the integration possibilities are endless.

## Current Version

By default, all requests receive the v2 version of the API. We encourage you to explicitly request this version via the Accept header.

`Accept: application/vnd.textus-v2`

## Authentication

The TextUs API supports HTTP Basic authentication. To authenticate, base-64 encode your username and api key (separated with a colon) and place the result in an 'Authorization' header. You may authenticate with your username and password, or with your username and api key.

`Authorization: {username}:{api_key}`

## Parameters

Many API methods take optional parameters. For GET requests, any parameters not specified as a segment in the path can be passed as an HTTP query string parameter.

For POST, PATCH, PUT, and DELETE requests, parameters not included in the URL should be encoded as JSON with a Content-Type of 'application/json'.

## Errors

> The JSON response body for an error is structured like this:

```json
{"error": "Validation failed: Phone has already been taken"}
```

TextUs uses conventional HTTP response codes to indicate the success or failure of an API request. In general, codes in the 2xx range indicate success, codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, contact creation failed, etc.), and codes in the 5xx range indicate an error with TextUs servers.

## Pagination

```shell
curl /contacts?page=1&per_page=25
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

Requests that return multiple items will be paginated to 25 items by default. You can specify further pages with the ?page parameter. For some resources, you can also set a custom page size up to 100 with the ?per_page parameter. Note that for technical reasons not all endpoints respect the ?per_page parameter, see events for example.

Note: the response headers for these requests include:

| Key | Value |
|:---:|:------|
| X-Offset | The page offset requested. |
| X-Page | The current page requested. |
| X-Per-Page | The number of resources requested per page. |
| X-Total | The total number of resources. |
| X-Total-Pages | The total number of pages for the requested resources. |

## Rate Limiting

For broadcast requests, you can make up to 1 request per minute. For all other requests, you can make up to 1,000 requests per hour.

## Cross Origin Resource Sharing (CORS)

The API currently **does not** support Cross Origin Resource Sharing (CORS) for AJAX requests from any origin.

## Webhooks

> The JSON payload is structured like this:

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

Webhooks allow you to build or set up integrations which subscribe to messaging events on TextUs. When a message is sent or received, we'll send a HTTP POST payload to the webhook's configured URL. Webhooks can be used to log messages for a contact in an external CRM or ATS or any other creative use.


## Testing the API

Below is a static list of all of the accessible TextUs resources with an example request and response. To test out a live version of the API for your account, please sign in or sign up and navigate to the [API docs](https://app.textus.com/api/docs).



# Auto Replies



## List all auto replies for the current account

```shell
curl https://app.textus.com/api/auto_replies
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "account_id": null,
    "message": null,
    "is_active": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all auto replies for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |



## Create a new account auto reply

```shell
curl https://app.textus.com/api/auto_replies \
  --request POST \
  --data '{"id":988,"is_active":null,"message":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "message": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Create a new account auto reply

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | is_active |  String | Auto reply status (true or false) |
| ✓ | message |  String | Auto Reply Message |







## Retrieve a single auto reply

```shell
curl https://app.textus.com/api/auto_replies/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "message": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single auto reply

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single auto reply

```shell
curl https://app.textus.com/api/auto_replies/:id \
  --request PUT \
  --data '{"id":988,"is_active":null,"message":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "message": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Update a single auto reply

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |
|  | is_active |  String | Auto reply status (true or false) |
|  | message |  String | Auto reply message |



## Delete a single auto reply

```shell
curl https://app.textus.com/api/auto_replies/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "message": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Delete a single auto reply

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Broadcasts



## List all broadcasts for the current account

```shell
curl https://app.textus.com/api/broadcasts
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "user_id": null,
    "group_id": null,
    "message_body": null,
    "status": null,
    "deliver_at": null,
    "id": null,
    "created_at": null,
    "updated_at": null,
    "group_name": null,
    "total_messages": null,
    "unsent_messages": null,
    "sent_messages": null,
    "send_errors": null
  }
]
```
List all broadcasts for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | last_broadcast_to_each_group |  Virtus::Attribute::Boolean | Displays the last broadcast to each unique group |
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |



## Create and send a broadcast

```shell
curl https://app.textus.com/api/broadcasts \
  --request POST \
  --data '{"deliver_at":null,"from_template":null,"group":null,"message_body":null,"sender":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "user_id": null,
  "group_id": null,
  "message_body": null,
  "status": null,
  "deliver_at": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "group_name": null,
  "total_messages": null,
  "unsent_messages": null,
  "sent_messages": null,
  "send_errors": null
}
```
Create and send a broadcast

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | deliver_at |  DateTime | Delivery Date and Time (to schedule messages) |
|  | from_template |  Virtus::Attribute::Boolean | Whether or not a broadcast is from a template |
| ✓ | group |  Integer | The id of the group receiving the broadcast |
| ✓ | message_body |  String | The message content of the broadcast |
| ✓ | sender |  Integer | The sending user's id |







## Retrieve a single broadcast

```shell
curl https://app.textus.com/api/broadcasts/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "user_id": null,
  "group_id": null,
  "message_body": null,
  "status": null,
  "deliver_at": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "group_name": null,
  "total_messages": null,
  "unsent_messages": null,
  "sent_messages": null,
  "send_errors": null
}
```
Retrieve a single broadcast

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Contacts



## List all contacts for the current account

```shell
curl https://app.textus.com/api/contacts
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "first_name": null,
    "last_name": null,
    "business_name": null,
    "account_id": null,
    "flagged": null,
    "notes": null,
    "phone": null,
    "country": null,
    "line_type": null,
    "id": null,
    "created_at": null,
    "updated_at": null,
    "opted_out": null
  }
]
```
List all contacts for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | order_by_name_then_phone |  Virtus::Attribute::Boolean | Orders by last name, first name, then phone number (default order is created_at) |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |
|  | q |  String | Search Query (searches against phone, first_name, last_name, business_name) |



## Create a new account contact

```shell
curl https://app.textus.com/api/contacts \
  --request POST \
  --data '{"business_name":null,"country":null,"first_name":null,"flagged":null,"id":758,"last_name":null,"notes":null,"phone":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "first_name": null,
  "last_name": null,
  "business_name": null,
  "account_id": null,
  "flagged": null,
  "notes": null,
  "phone": null,
  "country": null,
  "line_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "opted_out": null
}
```
Create a new account contact

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | business_name |  String | Contact business name |
|  | country |  String | Two-digit country code for contact phone number.  Defaults to the account's country code. |
| ✓ | first_name |  String | Contact first name |
|  | last_name |  String | Contact last name |
| ✓ | phone |  String | Contact phone number |







## Retrieve a single contact by id

```shell
curl https://app.textus.com/api/contacts/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "first_name": null,
  "last_name": null,
  "business_name": null,
  "account_id": null,
  "flagged": null,
  "notes": null,
  "phone": null,
  "country": null,
  "line_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "opted_out": null
}
```
Retrieve a single contact by id

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single contact

```shell
curl https://app.textus.com/api/contacts/:id \
  --request PUT \
  --data '{"business_name":null,"country":null,"first_name":null,"flagged":null,"id":758,"last_name":null,"notes":null,"phone":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "first_name": null,
  "last_name": null,
  "business_name": null,
  "account_id": null,
  "flagged": null,
  "notes": null,
  "phone": null,
  "country": null,
  "line_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "opted_out": null
}
```
Update a single contact

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | business_name |  String | Contact business name |
|  | country |  String | Two-digit country code for contact phone number |
|  | first_name |  String | Contact first name |
|  | flagged |  Virtus::Attribute::Boolean | Contact flagged tag |
| ✓ | id |  uuid | the `id` of the `:id` |
|  | last_name |  String | Contact last name |
|  | notes |  String | Contact notes |
|  | phone |  String | Contact phone number |



##

```shell
curl https://app.textus.com/api/contacts/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "first_name": null,
  "last_name": null,
  "business_name": null,
  "account_id": null,
  "flagged": null,
  "notes": null,
  "phone": null,
  "country": null,
  "line_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "opted_out": null
}
```


### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Groups



## List all groups for the current account

```shell
curl https://app.textus.com/api/groups
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "name": null,
    "account_id": null,
    "locked": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all groups for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | order_by |  String | Order Query (orders by attribute) |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |
|  | q |  String | Search Query (searches against group name) |



## Create a new account group

```shell
curl https://app.textus.com/api/groups \
  --request POST \
  --data '{"name":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Create a new account group

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | name |  String | Name of the group |







## Retrieve a single group

```shell
curl https://app.textus.com/api/groups/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single group

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single group

```shell
curl https://app.textus.com/api/groups/:id \
  --request PUT \
  --data '{"name":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Update a single group

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |
|  | name |  String | Name of the group |



## Delete a single group

```shell
curl https://app.textus.com/api/groups/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Delete a single group

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |







## List broadcasts for the group

```shell
curl https://app.textus.com/api/groups/:id/broadcasts
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
List broadcasts for the group

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `broadcasts` |
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |







## List contacts for the group

```shell
curl https://app.textus.com/api/groups/:id/contacts
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
List contacts for the group

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `contacts` |
|  | offset |  Integer | Pad a number of results. |
|  | order_by |  String | Order Query (orders by attribute) |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |







## Add a contact to a group

```shell
curl https://app.textus.com/api/groups/:id/contacts/:contact_id \
  --request PUT \
  --data '{"name":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Add a contact to a group

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | contact_id |  uuid | the `id` of the `contact` |
| ✓ | id |  uuid | the `id` of the `:contact_id` |



## Remove a contact from a group

```shell
curl https://app.textus.com/api/groups/:id/contacts/:contact_id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "name": null,
  "account_id": null,
  "locked": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Remove a contact from a group

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | contact_id |  uuid | the `id` of the `contact` |
| ✓ | id |  uuid | the `id` of the `:contact_id` |






# Message Signatures



## List all message signatures for the current account

```shell
curl https://app.textus.com/api/message_signatures
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "account_id": null,
    "content": null,
    "is_active": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all message signatures for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |



## Create a new message signature

```shell
curl https://app.textus.com/api/message_signatures \
  --request POST \
  --data '{"content":null,"is_active":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Create a new message signature

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | content |  String | Message signature content |
|  | is_active |  String | Message signature activation status (true or false) |







## Retrieve a single message signature

```shell
curl https://app.textus.com/api/message_signatures/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single message signature

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single message signature

```shell
curl https://app.textus.com/api/message_signatures/:id \
  --request PUT \
  --data '{"content":null,"is_active":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Update a single message signature

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | content |  String | Message signature content |
| ✓ | id |  uuid | the `id` of the `:id` |
|  | is_active |  String | Message signature activation status (true or false) |



## Delete a single message signature

```shell
curl https://app.textus.com/api/message_signatures/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "is_active": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Delete a single message signature

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Message Templates



## List all message templates for the current account

```shell
curl https://app.textus.com/api/message_templates
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "account_id": null,
    "content": null,
    "title": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all message templates for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |



## Create a new message template

```shell
curl https://app.textus.com/api/message_templates \
  --request POST \
  --data '{"content":null,"title":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "title": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Create a new message template

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | content |  String | Message template content |
| ✓ | title |  String | Message template title |







## Retrieve a single message template

```shell
curl https://app.textus.com/api/message_templates/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "title": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single message template

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single message template

```shell
curl https://app.textus.com/api/message_templates/:id \
  --request PUT \
  --data '{"content":null,"title":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "title": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Update a single message template

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | content |  String | Message template content |
| ✓ | id |  uuid | the `id` of the `:id` |
|  | is_active |  String | Message template activation status (true or false) |



## Delete a single message template

```shell
curl https://app.textus.com/api/message_templates/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "account_id": null,
  "content": null,
  "title": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Delete a single message template

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Messages



## List all messages for the current account

```shell
curl https://app.textus.com/api/messages
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "content": null,
    "read": null,
    "broadcast_id": null,
    "status": null,
    "deliver_at": null,
    "from_autoresponse": null,
    "sender_id": null,
    "sender_type": null,
    "receiver_id": null,
    "receiver_type": null,
    "id": null,
    "created_at": null,
    "updated_at": null,
    "sender_phone": null,
    "sender_name": null,
    "receiver_phone": null,
    "receiver_name": null
  }
]
```
List all messages for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | last_message_to_each_contact |  Virtus::Attribute::Boolean | Displays the last message to each unique contact |
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |
|  | q |  String | Search Query (searches against message body) |
|  | scope |  String | Filter messages by: 'flagged', 'unread', or 'with_notes' |



## Create and send a message

```shell
curl https://app.textus.com/api/messages \
  --request POST \
  --data '{"id":706,"read":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "content": null,
  "read": null,
  "broadcast_id": null,
  "status": null,
  "deliver_at": null,
  "from_autoresponse": null,
  "sender_id": null,
  "sender_type": null,
  "receiver_id": null,
  "receiver_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "sender_phone": null,
  "sender_name": null,
  "receiver_phone": null,
  "receiver_name": null
}
```
Create and send a message

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | content |  String | The message content |
|  | deliver_at |  DateTime | Delivery Date and Time (to schedule messages) |
| ✓ | receiver |  String |         The receiving contact's id or phone number
        (international format -- must begin with a + sign)
        If no contact exists with that phone number, one will be created automatically
        with first name 'New' and last name 'Contact.'
 |
| ✓ | sender |  String | The sending user's id or email address |







## List all messages to and from the specified contact

```shell
curl https://app.textus.com/api/messages/:contact_id
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "content": null,
    "read": null,
    "broadcast_id": null,
    "status": null,
    "deliver_at": null,
    "from_autoresponse": null,
    "sender_id": null,
    "sender_type": null,
    "receiver_id": null,
    "receiver_type": null,
    "id": null,
    "created_at": null,
    "updated_at": null,
    "sender_phone": null,
    "sender_name": null,
    "receiver_phone": null,
    "receiver_name": null
  }
]
```
List all messages to and from the specified contact

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | contact_id |  uuid | the `id` of the `contact` |
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |







## Update a message's read status

```shell
curl https://app.textus.com/api/messages/:id \
  --request PUT \
  --data '{"id":706,"read":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "content": null,
  "read": null,
  "broadcast_id": null,
  "status": null,
  "deliver_at": null,
  "from_autoresponse": null,
  "sender_id": null,
  "sender_type": null,
  "receiver_id": null,
  "receiver_type": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "sender_phone": null,
  "sender_name": null,
  "receiver_phone": null,
  "receiver_name": null
}
```
Update a message's read status

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |
| ✓ | read |  Virtus::Attribute::Boolean | Message read status (true or false) |






# Opt Outs



## List all opt outs for the current organization

```shell
curl https://app.textus.com/api/opt_outs
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "organization_id": null,
    "id": null,
    "created_at": null,
    "updated_at": null,
    "phone": null
  }
]
```
List all opt outs for the current organization

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |
|  | q |  String | Search Query (searches against phone) |



## Create a new opt out

```shell
curl https://app.textus.com/api/opt_outs \
  --request POST \
  --data '{"country":null,"phone":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "organization_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "phone": null
}
```
Create a new opt out

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | country |  String | Two-digit country code for opt out phone number |
| ✓ | phone |  String | Opt out phone number |







## Retrieve a single opt out

```shell
curl https://app.textus.com/api/opt_outs/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "organization_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "phone": null
}
```
Retrieve a single opt out

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Delete a single opt out

```shell
curl https://app.textus.com/api/opt_outs/:id \
  --request DELETE \
  --verbose
```

<<<<<<< HEAD
```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.
=======
>>>>>>> 2fceb1c... initial index.html.md creation

> The above command returns JSON structured like this:

```json
{
  "organization_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null,
  "phone": null
}
```
Delete a single opt out

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Users



## List all users for the current account

```shell
curl https://app.textus.com/api/users
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "email": null,
    "first_name": null,
    "last_name": null,
    "account_id": null,
    "role": null,
    "archived": null,
    "last_sign_in_at": null,
    "should_play_audio": null,
    "forward_messages": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all users for the current account

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | include_archived |  Virtus::Attribute::Boolean | Include archived users |
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |





## Retrieve the current user

```shell
curl https://app.textus.com/api/users/current
  --verbose
```

<<<<<<< HEAD
```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```
=======
>>>>>>> 2fceb1c... initial index.html.md creation

> The above command returns JSON structured like this:

```json
[
  {
    "email": null,
    "first_name": null,
    "last_name": null,
    "account_id": null,
    "role": null,
    "archived": null,
    "last_sign_in_at": null,
    "should_play_audio": null,
    "forward_messages": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
Retrieve the current user

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|







## Retrieve a single user

```shell
curl https://app.textus.com/api/users/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "email": null,
  "first_name": null,
  "last_name": null,
  "account_id": null,
  "role": null,
  "archived": null,
  "last_sign_in_at": null,
  "should_play_audio": null,
  "forward_messages": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single user

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






# Web Hooks



## List all web hooks accessible by the current user

```shell
curl https://app.textus.com/api/web_hooks
  --verbose
```


> The above command returns JSON structured like this:

```json
[
  {
    "url": null,
    "account_id": null,
    "id": null,
    "created_at": null,
    "updated_at": null
  }
]
```
List all web hooks accessible by the current user

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
|  | offset |  Integer | Pad a number of results. |
|  | page |  Integer | Page offset to fetch. |
|  | per_page |  Integer | Number of results to return per page. |



## Create a new web hook

```shell
curl https://app.textus.com/api/web_hooks \
  --request POST \
  --data '{"url":null}' \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "url": null,
  "account_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Create a new web hook

### HTTP Method

`POST`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | url |  String | URL where web hook payload will be delivered |







## Retrieve a single web hook

```shell
curl https://app.textus.com/api/web_hooks/:id
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "url": null,
  "account_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Retrieve a single web hook

### HTTP Method

`GET`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |



## Update a single web hook

```shell
curl https://app.textus.com/api/web_hooks/:id \
  --request PUT \
  --data '{"url":null}' \
  --verbose
```

<<<<<<< HEAD
```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```
=======
>>>>>>> 2fceb1c... initial index.html.md creation

> The above command returns JSON structured like this:

```json
{
  "url": null,
  "account_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Update a single web hook

### HTTP Method

`PUT`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |
|  | url |  String | Web hook URL (must be a valid URL) |



## Delete a single web hook

```shell
curl https://app.textus.com/api/web_hooks/:id \
  --request DELETE \
  --verbose
```


> The above command returns JSON structured like this:

```json
{
  "url": null,
  "account_id": null,
  "id": null,
  "created_at": null,
  "updated_at": null
}
```
Delete a single web hook

### HTTP Method

`DELETE`

### URL Parameters

| Required | Name | Type | Description |
|:--------:|:-----|:-----|:------------|
| ✓ | id |  uuid | the `id` of the `:id` |






## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint retrieves a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

