# Ongair

[Ongair](https://ongair.im) is a Customer Support platform for businesses to chat with their customers on WhatsApp, WeChat,
Telegram, SMS and many more. This API allows you to send and receive messages from connected channels to and from your
application.

## Getting Started

To begin, you first need to sign up on [Ongair](https://ongair.im/users/sign_up) and connect at least one channel. Once logged in, go to Settings and Select your channel.

![](/_media/settings.png)

#### 1. Setting your API Endpoint

In order to receive incoming messages from your channel you need to set an API Endpoint url and click the enabled API Endpoint checkbox. It needs to be:

- publicly accessible
- support https

#### 2. API Token

Access to the api is via API tokens. You can view your current token on the settings page.


## Receiving Messages

If you have configured your channel to receive incoming messages every time a message is sent to your channel you will receive a webhook notification at your specified API Endpoint as an HTTP post.

### Parameters

| Name | Type | Purpose | Example |
|--|--|--|--|
notification_type | String | The type of notification being sent. Could be either "MessageSent" or "MessageReceived" or "ImageReceived" or "TicketStatusUpdated" | "MessageReceived"
id | Integer | The id of the message | 3443
name | String | The name of the contact if available | "John"
text | String | The text sent or received | "Hello world"
external_contact_id | String | The id of the contact on the channel | "+254722200200"
conversation_id | Integer | The id of the conversation | 1
status | String | The status of the conversation. Could be either "Open" or "Pending" or "Closed" | "Closed"
account_type | String | The type of the channel | "WhatsApp"
image | String | The url of the image sent or received | "https://cdn.ongair.im/123.jpg"

The post uses the `application/x-www-form-urlencoded` content type therefore to read these values you need to use the names of the parameters as keys to get the values.

### Example

PHP
```php

  #in your handler
  $notification_type = $_POST["notification_type"];
  $text = $_POST["text"];
```

RUBY
```ruby

  #in your controller
  notification_type = params[:notification_type]
  text = params[:text]
```

### Response

Acknowledge receipt of the notification by responding with a HTTP 200 status code


## Sending Messages

In order to send a text message to a contact you need to send a post to the send message url `https://ongair.im/api/v1/base/send`

### Parameters

| Name | Type | Mandatory | Purpose | Example |
|--|--|--|--|--|
external_id | String | yes | The external id of the contact on the channel | "+254722200200"
text | String | yes | The text to be sent| "Hello world"
external_contact_id | String | yes | The id of the contact on the channel | "+254722200200"
thread | Boolean | no | Whether or not to attach the message to the current open conversation on the dashboard | true

### Example

```shell
curl -d {"external_id":"+254722200200", "text":"Hello world", "thread": "true" }
  -H "Content-Type: application/json"
  -H "Authorization: Token token:meowmeowmeow"
  -X POST https://ongair.im/api/v1/base/send
```

### Response

The response is a json object

```shell
  {  "sent": "true", "id": "35345432", "conversation_id": "22" }
```


## Sending Images

In order to send an image message to a contact you need to send a post to the send message url `https://ongair.im/api/v1/base/send_image`

### Parameters

| Name | Type | Mandatory | Purpose | Example |
|--|--|--|--|--|
external_id | String | yes | The external id of the contact on the channel | "+254722200200"
image | String | yes | The url to the image file to be send| "https://myserver.com/1.jpg"
content_type | String | yes | The mime type of the image. Should be either `image/jpeg` or `image/png`| "image/jpeg"
text | String | no | The caption to be sent| "Hello world"
external_contact_id | String | yes | The id of the contact on the channel | "+254722200200"
thread | Boolean | no | Whether or not to attach the message to the current open conversation on the dashboard | true

### Example

```shell
curl -d {"external_id":"+254722200200", "text":"Hello world", "image": "https://myserver.com/1.jpg", "content_type": "image/jpeg", "thread": "true" }
  -H "Content-Type: multipart/form-data"
  -H "Authorization: Token token:meowmeowmeow"
  -X POST https://ongair.im/api/v1/base/send_image
```

### Response

The response is a json object

```shell
  {  "sent": "true", "id": "35345435", "conversation_id": "22" }
```


## Sending Documents

A document type message is sent with a file url. In order to send a file to a contact you need to post to the send document url `https://ongair.im/api/v1/base/send_document`

### Parameters

| Name | Type | Mandatory | Purpose | Example |
|--|--|--|--|--|
external_id | String | yes | The external id of the contact on the channel | "+254722200200"
file_url | String | yes | The url of the document file to be sent | "https://myserver.com/api_docs.pdf"
content_type | String | yes | The mime type of the document. Default type is `application/pdf`
caption | String | no | The caption to be sent | "Hello world"
thread | Boolean | no | Whether or not to attach the message to the current open conversation on the dashboard | true

### Example

```shell
curl -d {"external_id":"+254722200200", "caption":"Hello world", "file_url": "https://myserver.com/api_docs.pdf", "content_type": "application/pdf", "thread": "true" }
  -H "Content-Type: multipart/form-data"
  -H "Authorization: Token token:meowmeowmeow"
  -X POST https://ongair.im/api/v1/base/send_document
```

### Response

The response is a json object

```shell
  {  "sent": "true", "id": "35345435", "conversation_id": "22" }
```




## Creating Tickets
You can create tickets on the Ongair platform for Agents to respond via the API. `https://ongair.im/api/v1/base/create_conversation`

### Parameters

| Name | Type | Mandatory | Purpose | Example |
|--|--|--|--|--|
external_id | String | yes | The external id of the contact on the channel | "+254722200200"
name | String | no | The name of the contact| "John Snow"
message_id | String | yes | The unique id of the message on your platform | "364326432432"
direction | String | no | Whether the message is incoming "IN" or outgoing "OUT". If not provided it defaults to 'IN' | 'IN'
text | String | no | The message that was sent by the contact| "Hello wall"
external_contact_id | String | yes | The id of the contact on the channel | "+254722200200"
profile_picture_url | String | no | The profile picture of the contact | "https://google.com/1.jpg"
email | String | no | The email of the contact | "john@thewall.org"
phone_number | String | no | The phone number of the contact | "254722200200"

### Example

```shell
curl -d {"external_id":"+254722200200", "text":"Hello world", "name": "John Snow", "message_id" : "364326432432" }
  -H "Content-Type: application/json"
  -H "Authorization: Token token:meowmeowmeow"
  -X POST https://ongair.im/api/v1/base/create_conversation
```

### Response

The response is a json object where `id` is the id of the conversation.

```shell
  {  "success": "true", "id": "3" }
```
