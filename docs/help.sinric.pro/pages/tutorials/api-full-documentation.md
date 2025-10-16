# SinricPro API Documentation

## Overview
Welcome to the SinricPro API documentation. This document provides a comprehensive overview of all available API endpoints, their functionalities, and how to interact with them.

## Base Configuration
- **Base URL**: `https://api.sinric.pro`
- **Version**: `1.0.0`
- **Authentication**: All endpoints require a valid `Bearer Token` in the `Authorization` header.


## ActivityLog 


### Get a specific device logs

**Method**: GET  
**Path**: `/api/v1/activitylog/device/:id`  

**Description**: Get a specific device logs using  device <code>id</code>. 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Home object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/activitylog/device/5c5e726095bd1817e07ab3ac',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | home | The requested home. |

---

### Get account activity history

**Method**: GET  
**Path**: `/api/v1/activitylog`  

**Description**: Get latest 15 activities related to your account.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/activitylog/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | homes | List of homes |

---

## Authentication 


### Login with API Key

**Method**: POST  
**Path**: `/api/v1/auth`  

**Description**: Login with an API Key to get access to the subsequent calls to the API.  Set the x-sinric-api-key http header with your API Key.  

**Headers
| Header | Type | Description |
|---|---|---|
| x-sinric-api-key: | `String` | Your API Key |

**Example: NodeJS:**
```js
const request = require("request");
const username = "john";
const password = "1234";
const url = "https://api.sinric.pro/api/v1/auth";

const options = {
method: 'POST',
url: url,
headers:
{   'x-sinric-api-key' : 'xxxxxxxxxxxxxx',
'Content-Type'  : 'application/x-www-form-urlencoded'
},
form:
{
client_id: "api_interaction"
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `String` | message | Login result |
| `String` | accessToken | Your access token to the subsequent calls. (for <TOKEN HERE> in this guide) |
| `String` | refreshToken | Your refresh token. Use this to obtain a new <code>accessToken</code> |
| `Number` | expiresIn | When this token will expire (in seconds) |
| `Boolean` | subscriptionExpired | Whether account subcription has expired? |
| `Object` | account | User account details |

---

### Login with credentials

**Method**: POST  
**Path**: `/api/v1/auth`  

**Description**: Creates an access token that gives you access to the subsequent calls to the API.  This resource uses Basic Authorization. Follow these steps to create an access token:  1. Concatenate your email, a colon character “:”, and your password into a single string 2. Base64 encode the string from Step 1 3. Include the resulting encoded string from Step 2 in an Authorization header as follows   

**Headers
| Header | Type | Description |
|---|---|---|
| Authorization: | `String` | Basic [encoded string] |

**Example: NodeJS:**
```js
const request = require("request");
const username = "john";
const password = "1234";
const url = "https://api.sinric.pro/api/v1/auth";
const auth = "Basic " + new Buffer.from(username + ":" + password).toString("base64");

const options = {
method: 'POST',
url: url,
headers:
{   'Authorization' : auth,
'Content-Type'  : 'application/x-www-form-urlencoded'
},
form:
{
client_id: "android-app"
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `String` | message | Login result |
| `String` | accessToken | Your access token to the subsequent calls. (for <TOKEN HERE> in this guide) |
| `String` | refreshToken | Your refresh token. Use this to obtain a new <code>accessToken</code> |
| `Number` | expiresIn | When this token will expire (in seconds) |
| `Boolean` | subscriptionExpired | Whether account subcription has expired? |
| `Object` | account | User account details |

---

### Logout

**Method**: POST  
**Path**: `/api/v1/logout`  

**Description**: Logout from the current session. This will clear the issued token for this session as well.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/logout',
headers:
{
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | Login result |

---

### Refresh token

**Method**: GET  
**Path**: `/api/v1/refresh_token`  

**Description**: Get a new <code>accessToken</code> using refresh_token.  If you login from multiple apps or devices (app, web) using the same account you will see multiple tokens in the output json.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/auth/refresh_token',
headers:
{
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{   refreshToken: 'c3pie156k86e...'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | Login result |
| `String` | accessToken | Your new access token |
| `String` | refreshToken | Your new refresh token |

---

### Reject a token

**Method**: POST  
**Path**: `/api/v1/reject_token`  

**Description**: Blacklist <code>refreshToken</code> from active use.   

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/auth/reject_token',
headers:
{
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{   refreshToken: 'c3pie156k86e...'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | Login result |
| `String` | accessToken | Your new access token |
| `String` | refreshToken | Your new refresh token |

---

## Credentials 


### Create a new app key and a secret credential

**Method**: POST  
**Path**: `/api/v1/credentials/appkeyandsecret`  

**Description**: Create a new app key and a secret credential  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/credentials/appkeyandsecret',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
name: 'tv device',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | credential | Credential details |

---

### Create an API credential

**Method**: POST  
**Path**: `/api/v1/credentials/api`  

**Description**: Create a new API credential  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/credentials/api',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
name: 'Door API KEY',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | credential | Credential details |

---

### Delete a app key and secret credentials

**Method**: DELETE  
**Path**: `/api/v1/credentials/appkeyandsecret/:id`  

**Description**: Delete a app key and secret credential using <code>id</code>.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/credentials/appkeyandsecret/5c5e4578beafb23e08e15b8d',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

### Delete a credential

**Method**: DELETE  
**Path**: `/api/v1/credentials/api/:id`  

**Description**: Delete a specific credential using <code>id</code>.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/credentials/api/5c5e4578beafb23e08e15b8d',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

### Get all API key credentials

**Method**: GET  
**Path**: `/api/v1/credentials/api`  

**Description**: Get all credentials  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/credentials/api',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | devices | List of devices |

---

### Get all app key and secret credentials

**Method**: GET  
**Path**: `/api/v1/credentials/appkeyandsecret`  

**Description**: Get all app key and secret credentials  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/credentials/appkeyandsecret',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | devices | List of devices |

---

## Dashboard 


### Get user's dashboard

**Method**: GET  
**Path**: `/api/v1/dashboard`  

**Description**: Get user dashboard details  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/dashboard/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | dashboard | dashboard items |

---

## Device 


### Clear all power usage data

**Method**: DELETE  
**Path**: `/api/v1/devices/:id/powerusage`  

**Description**: Clear all power usage data for a specific device  

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices/5c5e4578beafb23e08e15b8d/powerusage',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `String` | [message] | Success or error message. |

---

### Clear all temperature data

**Method**: DELETE  
**Path**: `/api/v1/devices/:id/temperatureusage`  

**Description**: Clear all temperature data for a specific device  

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices/5c5e4578beafb23e08e15b8d/temperatureusage',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `String` | [message] | Success or error message. |

---

### Clear device usage estimation data

**Method**: DELETE  
**Path**: `/api/v1/devices/:id/deviceusageestimation`  

**Description**: Clear all device usage estimation data for a specific device  

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices/5c5e4578beafb23e08e15b8d/deviceusageestimation',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `String` | [message] | Success or error message. |

---

### Clear energy sensor usage data

**Method**: DELETE  
**Path**: `/api/v1/devices/:id/energysensorusage`  

**Description**: Clear all energy sensor usage data for a specific device  

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices/5c5e4578beafb23e08e15b8d/energysensorusage',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `String` | [message] | Success or error message. |

---

### Create a device

**Method**: POST  
**Path**: `/api/v1/devices`  

**Description**: Create a new device  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/devices',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
name: 'IRDEVKIT PRO3',
description: 'IRDEVKIT PRO module in the living room',
macAddress: 'xx:xx:xx:xx',
productId: '5c5f72915ef9c024b423a046',
roomId: '5c5f796140a15340803141c8',
hwVersion: '1.0.0',
swVersion: '1.0.0',
'attributes[0]': '{ "name" : "gpio_pin", "values" :  4  }',
serialNumber: 'xxxxxxxx-xxx-xxx-xx',
lastIpAddress: '192.168.1.1',
customData: 'text',
accessKeyId: '5c5f796140a15340803141c8'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | device | Device details |

---

### Delete a device

**Method**: DELETE  
**Path**: `/api/v1/devices/:id`  

**Description**: Delete a specific device using <code>id</code>.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices/5c5e4578beafb23e08e15b8d',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

### Delete device by MAC address

**Method**: DELETE  
**Path**: `/api/v1/devices`  

**Description**: Delete a device using MAC address  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/devices?macAddress=xx:xx:xx:xx',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `String` | [message] | Success or error message. |

---

### Find devices

**Method**: POST  
**Path**: `/api/v1/devices/find`  

**Description**: Find devices by name, MAC address, or serial number  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/devices/find',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
macAddress: 'xx:xx:xx:xx',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Array` | devices | Array of matching devices |

---

### Get a specific device

**Method**: GET  
**Path**: `/api/v1/devices/:id`  

**Description**: Get a specific device using <code>id</code>. 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | device | Requested device details. |

---

### Get all devices

**Method**: GET  
**Path**: `/api/v1/devices`  

**Description**: Get all devices for the authenticated user  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Array` | devices | List of devices |

---

### Get device air quality data

**Method**: GET  
**Path**: `/api/v1/devices/:id/airquality/todayAvg`  

**Description**: Get motion history 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/airquality/todayAvg',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | device | Requested device details. |

---

### Get device air quality data

**Method**: GET  
**Path**: `/api/v1/devices/:id/airquality`  

**Description**: Get air quality history 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Device object's id |
| from | `from` | date in unix-time |
| to | `to` | date in unix-time |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/airquality',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | device | Requested device details. |

---

### Get device contact sensor data

**Method**: GET  
**Path**: `/api/v1/devices/:id/events/contact`  

**Description**: Get contact sensor events history for a specific device 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/events/contact?from=1577836800&to=1577923200',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Array` | contactEvents | Array of contact sensor events with timestamps. |

---

### Get device motion data

**Method**: GET  
**Path**: `/api/v1/devices/:id/motion`  

**Description**: Get motion history 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/motion',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | device | Requested device details. |

---

### Get device temperature data

**Method**: GET  
**Path**: `/api/v1/devices/:id/temperature`  

**Description**: Get temperature history for a specific device 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/temperature?from=1577836800&to=1577923200',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Array` | temperatures | Array of temperature readings with timestamps. |

---

### Get power usage data

**Method**: GET  
**Path**: `/api/v1/devices/:id/powerusage`  

**Description**: Get power usage history for a specific device 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `String` | Device object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/devices/5c5f50b31c28610d24156e2f/powerusage?from=1577836800&to=1577923200',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Array` | powerUsages | Array of power usage readings with timestamps. |

---

### Provision device automatically

**Method**: POST  
**Path**: `/api/v1/devices/prov/autoconfigure`  

**Description**: Provision a new device automatically using HMAC verification (no authentication token required)  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/devices/prov/autoconfigure',
headers:
{
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
email: 'user@example.com',
macAddress: 'xx:xx:xx:xx:xx:xx',
productCode: 'PROD123',
manufacturerId: 'MFG456',
password: 'deviceSecret',
sign: 'hmacSignature'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | [device] | Created device details (if successful) |
| `String` | [message] | Success or error message. |

---

### Update an existing device

**Method**: PUT  
**Path**: `/api/v1/devices`  

**Description**: Update an existing device  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'PUT',
url: 'https://api.sinric.pro/api/v1/devices',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
id: "5c5f796c40a15340803141c9",
name: 'IRDEVKIT PRO3',
description: 'IRDEVKIT PRO module in the living room',
macAddress: 'xx:xx:xx:xx',
productId: '5c5f72915ef9c024b423a046',
roomId: '5c5f796140a15340803141c8',
hwVersion: '1.0.0',
swVersion: '1.0.0',
'attributes[0]': '{ "name" : "gpio_pin", "values" :  4  }',
serialNumber: 'xxxxxxxx-xxx-xxx-xx',
lastIpAddress: '192.168.1.1',
customData: 'text'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

## Home 


### Create a new home

**Method**: POST  
**Path**: `/api/v1/homes`  

**Description**: Creates a user residence. The user can have multiple residences  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/homes/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{   name: 'Home',
imageUrl: 'http://192.168.1.100:3003/images/320x320/2019-02-09-512-1549677267718.png',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | home | Home details |

---

### Get a specific home

**Method**: GET  
**Path**: `/api/v1/homes/:id`  

**Description**: Get a specific home using <code>id</code>. 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Home object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/homes/5c5e726095bd1817e07ab3ac',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | home | The requested home. |

---

### Get all homes

**Method**: GET  
**Path**: `/api/v1/homes`  

**Description**: Get all homes this user has created.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/homes/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | homes | List of homes |

---

### Update an existing home

**Method**: PUT  
**Path**: `/api/v1/homes`  

**Description**: Updates a home  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'PUT',
url: 'https://api.sinric.pro/api/v1/homes/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
id: "5c5e726095bd1817e07ab3ac",
name: "Home",
imageUrl: 'http://192.168.1.100:3003/images/320x320/2019-02-09-512-1549677267718.png',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

## Product 


### Get a specific product

**Method**: GET  
**Path**: `/api/v1/products/:id`  

**Description**: Get a specific product using <code>id</code>. 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Product object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/products/5c5f50b31c28610d24156e2f',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | product | Requested product details. |

---

### Get all products

**Method**: GET  
**Path**: `/api/v1/products`  

**Description**: Get all products  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/products/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | products | List of products |

---

## Room 


### Create a new room

**Method**: POST  
**Path**: `/api/v1/rooms`  

**Description**: Creates a room inside home  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/rooms/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{   name: 'My bedroom',
homeId: '5c5e4578beafb23e08e15b8d'
description: 'test'
imageUrl: 'http://192.168.1.100:3003/images/320x320/2019-02-09-512-1549677267718.png',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | room | Room details |

---

### Delete a room

**Method**: DELETE  
**Path**: `/api/v1/rooms/:id`  

**Description**: Delete a specific room using <code>id</code>.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'DELETE',
url: 'https://api.sinric.pro/api/v1/rooms/5c5e4578beafb23e08e15b8d',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

### Get a specific room

**Method**: GET  
**Path**: `/api/v1/rooms/:id`  

**Description**: Get a specific room using <code>id</code>. 

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| id | `id` | Room object's id |

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/rooms/5c5e4578beafb23e08e15b8d',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `Object` | room | The requested room. |

---

### Get all rooms

**Method**: GET  
**Path**: `/api/v1/rooms`  

**Description**: Get all rooms this user has created.  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/rooms/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | rooms | List of rooms |

---

### Get default list of room names

**Method**: GET  
**Path**: `/api/v1/defaultlist`  

**Description**: List of common room names  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'POST',
url: 'https://api.sinric.pro/api/v1/rooms/defaultlist',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |
| `[Object]` | rooms | List of room names |

---

### Update an existing room

**Method**: PUT  
**Path**: `/api/v1/rooms`  

**Description**: Updates a room  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'PUT',
url: 'https://api.sinric.pro/api/v1/rooms/',
headers:
{   'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
id: "5c5e726095bd1817e07ab3ac",
name: 'My room',
homeId: '5c5e4578beafb23e08e15b8d'
imageUrl: 'http://192.168.1.100:3003/images/320x320/2019-02-09-512-1549677267718.png',
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---

## Upload 


### Get an uploaded image

**Method**: GET  
**Path**: `/api/v1/images/:key/:name`  

**Description**: Get an uploaded image from server  

**Path Parameters
| Parameter | Type | Description |
|---|---|---|
| key | `key` | Output image size. eg: 320x320 |
| name | `name` | Image name. |

**Example: NodeJS:**
```js
var request = require("request");

var options = { method: 'GET',
url: 'http://192.168.1.100:3003/api/v1/images/320x320/2019-02-15-512-1550266151540.png',
headers:
{ }
};

request(options, function (error, response, body) {
if (error) throw new Error(error);

console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Image` | Image | * |

---

### Uploading files

**Method**: POST  
**Path**: `/api/v1/upload`  

**Description**: Upload a file to the server  

**Example: NodeJS:**
```js
var fs = require("fs");
var request = require("request");

var options = { method: 'POST',
url: 'http://http://192.168.1.100:3003/api/v1/upload',
headers:
{
'Content-Type': 'application/x-www-form-urlencoded',
'content-type': 'multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW' },
formData:
{ file:
{ value: 'fs.createReadStream("C:\\Users\\Aruna\\Desktop\\512.png")',
options:
{ filename: 'C:\\Users\\Aruna\\Desktop\\512.png',
contentType: null } } } };

request(options, function (error, response, body) {
if (error) throw new Error(error);

console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | Upload result |
| `String` | message | status |
| `String` | imageUrl | Image accessible url |

---

## Users 


### Get your account details

**Method**: GET  
**Path**: `/api/v1/users/myself`  

**Description**: Get your account details  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'GET',
url: 'https://api.sinric.pro/api/v1/users/myself',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

---

### Update your account details

**Method**: PATCH  
**Path**: `/api/v1/myself`  

**Description**: Update your user account details  

**Example: NodeJS:**
```js
var request = require("request");
var options = {
method: 'PATCH',
url: 'https://api.sinric.pro/api/v1/users/myself',
headers:
{
'Authorization': 'Bearer <TOKEN HERE>',
'Content-Type': 'application/x-www-form-urlencoded'
},
form:
{
name: 'Your Name'
}
};

request(options, function (error, response, body) {
if (error) throw new Error(error);
console.log(body);
});

```

**Success Response (200 OK)**
| Type | Name | Description |
|---|---|---|
| `Boolean` | success | The request has succeeded or failed. |

---
## Glossary
| Term | Definition |
|---|---|
| `API` | Application Programming Interface |
| `JWT` | JSON Web Token |
| `RAG` | Retrieval-Augmented Generation |

## Change Log
| Version | Date | Changes |
|---|---|---|
| 1.0.0 | 2025-08-26 | Initial documentation generation. |
