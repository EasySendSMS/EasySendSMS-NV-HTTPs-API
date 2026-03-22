# Number Validation (NV) HTTP(s) API

This page provides a comprehensive reference for all features available via the HTTP interface for number validation queries.

## Overview

The HTTP API allows you to integrate your system (client) with the Number Validation service using HTTP or HTTPS protocols to request network information securely with SSL encryption.

The client issues either a GET or POST request to the NV HTTP API, supplying the required parameters. Our system responds with a JSON-format HTTP response that indicates the status of the number, including the country, MCC, MNC, and operator information.

This service validates if the phone number is in the correct format, verifies the correct country code, and identifies the original network where the number was registered. Unlike the HLR Lookup, the Number Validation service is offline and does not provide live data.

Our Number Validation tool also allows users to perform bulk checks of up to 30 numbers at once by separating them with commas. This feature saves time and enhances efficiency, making it ideal for businesses and individuals who need to verify multiple phone numbers quickly. By integrating our NV API, you can seamlessly incorporate this functionality into your application, ensuring accurate and up-to-date information on number status.

> **Note:**
> - 🔐 **API Password:** If you have set a dedicated API password (different from your account password), make sure you are using it in your request.
> - 🌐 **IP Whitelisting:** If API IP whitelisting is enabled, ensure the request is sent from your authorized IP address. Otherwise, the request will be rejected.
> - ⚠️ **URL Encoding:** All API parameters must be URL-encoded before sending the request (especially `password`). Special characters such as `&`, `%`, `+`, or spaces may break the request and cause incorrect processing.
> - 📱 **Invalid Numbers:** If some recipient numbers are invalid (e.g., less than 9 digits or containing invalid characters), the system will send SMS to valid numbers only. Invalid numbers will be skipped, and no balance will be deducted for them.

## API Base URL

```http
https://api.easysendsms.app/nv
```

**Method:** `GET`, `POST`

## Required Parameters

The following parameters must be included in your API request:

| Parameter | Description | Required |
| :--- | :--- | :--- |
| **Username** | Your NV service username. | Yes |
| **Password** | Your NV account password or your API password (if set in your account settings). | Yes |
| **Number** | Comma-separated list of numbers to check. | Yes |

## HTTP Response

The HTTP response from our system will contain the following information in JSON format.

If the query is successful, the status code will be returned as shown below:

**Example:**
```json
{
    "results": [
        {
            "Status": "INVALID",
            "Country": "Australia",
            "ISO3166_2": "AU",
            "CC": "61",
            "NetName": "",
            "MCC": "505",
            "MNC": "",
            "OPERATOR": "",
            "Type": "SUPPLEMENTARY SERVICES",
            "NetType": "",
            "MSISDN": "61111111111111"
        }
    ]
}
```

If the query is incorrect, it will return **ERROR: {Error description}**.

**Example:**
```json
{"error":"Authentication failed"}
```

## Example GET Requests

Below is an example of a GET method request using the HTTP interface.  
*(Ensure all parameter values are URL-encoded)*

### Single Number Query Example

**Request URL:**
```http
https://api.easysendsms.app/nv?username=testuser&password=secret&number=61111111111111
```

**Output:**
```json
{
    "results": [
        {
            "Status": "INVALID",
            "Country": "Australia",
            "ISO3166_2": "AU",
            "CC": "61",
            "NetName": "",
            "MCC": "505",
            "MNC": "",
            "OPERATOR": "",
            "Type": "SUPPLEMENTARY SERVICES",
            "NetType": "",
            "MSISDN": "61111111111111"
        }
    ]
}
```

### Bulk Query Example (Max 30 numbers)

You can also perform a bulk number validation by supplying up to 30 numbers, separated by commas.

**Request URL:**
```http
https://api.easysendsms.app/nv?username=testuser&password=secret&number=123456789999,123456789000
```

**Output:**
```json
{
    "results": [
        {
            "Status": "VALID",
            "Country": "United States of America",
            "ISO3166_2": "US",
            "CC": "1",
            "NetName": "",
            "MCC": "310-316",
            "MNC": "",
            "OPERATOR": "",
            "Type": "FIXED",
            "NetType": "",
            "MSISDN": "123456789999"
        },
        {
            "Status": "VALID",
            "Country": "United States of America",
            "ISO3166_2": "US",
            "CC": "1",
            "NetName": "",
            "MCC": "310-316",
            "MNC": "",
            "OPERATOR": "",
            "Type": "FIXED",
            "NetType": "",
            "MSISDN": "123456789000"
        }
    ]
}
```

## NV API Response Fields

| Field | Description |
| :--- | :--- |
| **Status** | **VALID**: Indicates that the number format and details are correct and belong to either a mobile or fixed type.<br>**INVALID**: Indicates that the number is neither a valid mobile number nor a landline (fixed) number. However, the API will still display the type of number in the response. |
| **Country** | The country where the number is registered (e.g., United States of America). |
| **ISO3166_2** | The two-letter ISO 3166-1 alpha-2 country code (e.g., US for the United States). |
| **CC** | The country calling code associated with the number (e.g., 1 for the United States). |
| **NetName** | The name of the network associated with the number, if available. |
| **MCC** | The Mobile Country Code (MCC) associated with the number (e.g., 310-316 for the United States). |
| **MNC** | The Mobile Network Code (MNC) associated with the number, if available. |
| **OPERATOR** | The operator or service provider associated with the number, if available. |
| **Type** | The type of number, such as "MOBILE", "FIXED", or "SUPPLEMENTARY SERVICES". |
| **NetType** | The network type associated with the number, if available. |
| **MSISDN** | The full phone number including the country code (e.g., 123456789000). |

## API Rate Limit

To maintain a high quality of service, the NV API enforces rate limits. The default request rate limit is 30 requests per second per account per IP address.

The API will reject requests exceeding this limit with a `429 Too Many Requests` HTTP status. You can retry the request after 1 second.

## API Error Codes

| Error Text | Description |
| :--- | :--- |
| **Invalid Number Parameter** | Invalid number. This means the parameter was not provided, left blank, or the number format is incorrect. |
| **Invalid Password Parameter** | Invalid password. This means the parameter was not provided or left blank. |
| **Invalid Username Parameter** | Invalid username. This means the parameter was not provided or left blank. |
| **Authentication failed** | Invalid username or password, or account not active or does not exist. |
| **Insufficient credits** | The account has insufficient credits. |
| **Http Error Code: 429** | API calls quota exceeded! Maximum allowed is 30 requests per second. |

## NV Code Examples

Below are examples of calling the NV API using various programming languages.

### 🟢 .NET
[Direct Link to Example](#net)

```csharp
var options = new RestClientOptions("")
{
    MaxTimeout = -1,
};

var client = new RestClient(options);
var request = new RestRequest("https://api.easysendsms.app/nv", Method.Post);
request.AddHeader("Content-Type", "application/x-www-form-urlencoded");
request.AddParameter("username", "username");
request.AddParameter("password", "password");
request.AddParameter("number", "12345678900");
RestResponse response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

### 🐘 PHP
[Direct Link to Example](#php)

```php
$curl = curl_init();
curl_setopt_array($curl, array(
    CURLOPT_URL => 'https://api.easysendsms.app/nv',
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_ENCODING => '',
    CURLOPT_MAXREDIRS => 10,
    CURLOPT_TIMEOUT => 0,
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
    CURLOPT_CUSTOMREQUEST => 'POST',
    CURLOPT_POSTFIELDS => 'username=username&password=password&number=12345678900',
    CURLOPT_HTTPHEADER => array(
        'Content-Type: application/x-www-form-urlencoded'
    ),
));
$response = curl_exec($curl);
curl_close($curl);
echo $response;
```

### ☕ Java
[Direct Link to Example](#java)

```java
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("https://api.easysendsms.app/nv")
    .header("Content-Type", "application/x-www-form-urlencoded")
    .field("username", "username")
    .field("password", "password")
    .field("number", "12345678900")
    .asString();
```

### 🐍 Python
[Direct Link to Example](#python)

```python
import http.client

conn = http.client.HTTPSConnection("api.easysendsms.app")
payload = 'username=username&password=password&number=12345678900'
headers = {
    'Content-Type': 'application/x-www-form-urlencoded'
}
conn.request("POST", "/nv", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))
```
