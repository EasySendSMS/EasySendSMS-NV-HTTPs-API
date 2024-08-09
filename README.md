
# Number Validation (NV) HTTP(s) API (Get, Post Method)

This page provides a reference for all features available via the HTTP interface for [number validation](https://easysendsms.com/number-validation) queries.

## Overview

The NV HTTP-API allows you to integrate your system with the [Number Validation service](https://easysendsms.com/number-validation) using the HTTP protocol to request network information. HTTPS is also supported for secure transactions using SSL encryption.

The client issues either a GET or POST request to the NV HTTP API, supplying a list of required parameters. Our system returns an HTTP response in JSON format, indicating the status of the number, including the country, MCC, MNC, and operator information.

This service:

- Validates if the phone number is in the correct format.
- Verifies the correct country code.
- Identifies the original network where the number was registered.

Unlike the HLR Lookup, the Number Validation service is offline and does not provide live data.

Our [Number Validation tool](https://easysendsms.com/number-validation) allows users to perform bulk checks of up to 30 numbers at once by separating them with commas. This feature is ideal for businesses and individuals who need to verify multiple phone numbers quickly. By integrating our NV API, you can seamlessly incorporate this functionality into your application, ensuring accurate and up-to-date information on number status.

## Important Notes

- If you have set a dedicated API Password different from the account password, make sure to use it.
- If you have set a Whitelisted IP for your API, make sure the request comes from that IP; otherwise, the request will be rejected.
- Invalid Number: If one or more provided numbers are invalid (less than 9 digits, have invalid characters, etc.), the process will continue for valid numbers, and invalid numbers will be ignored. No balance will be deducted for invalid numbers.

## API Base URL

```
https://api.easysendsms.app/nv
```

## API Parameters

| Parameter | Description                                                                 | Presence   |
|-----------|-----------------------------------------------------------------------------|------------|
| Username  | Your NV service username                                                    | Mandatory  |
| Password  | Your NV account password or your API password (if set in your account)      | Mandatory  |
| Number    | Comma-separated list of numbers to check.                                   | Mandatory  |

## HTTP Response

The HTTP response from our system contains the following:

### Response JSON Format

If the query has been sent successfully, the status code will return as below:

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

If the query is incorrect, it will return `ERROR: {Error description}`

**Example:**

```json
{"error":"Authentication failed"}
```

### Example Queries

**Single Number Query (GET Method):**

- **Username**: testuser
- **Password**: secret
- **Number**: 61111111111111

**Request URL:**

```
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

**Bulk Query (Max 30 numbers per request):**

**Request URL:**

```
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

## API Rate Limit

To maintain a high quality of service, the NV API applies rate limits. The default request rate limit is 30 requests per second per account per IP address. The API will reject all requests exceeding this rate limit with a `429 Too Many Requests` HTTP Status. You can retry the request after 1 second.

## API Number Status Codes

| Response | Description                                                                                      |
|----------|--------------------------------------------------------------------------------------------------|
| INVALID  | Invalid number. This indicates that the number is neither a valid mobile number nor a landline.  |
| VALID    | Valid number. This indicates that the number format and details are correct.                     |

## API Error Codes

| Error Text                     | Description                                                                                       |
|--------------------------------|---------------------------------------------------------------------------------------------------|
| Invalid Number Parameter       | Invalid number. This means the parameter was not provided or left blank, or the number format is incorrect. |
| Invalid Password Parameter     | Invalid password. This means the parameter was not provided or left blank.                       |
| Invalid Username Parameter     | Invalid username. This means the parameter was not provided or left blank.                       |
| Authentication failed          | Invalid username or password, or account not active or existing.                                |
| Insufficient credits           | The account has insufficient credits.                                                             |
| Http Error Code: 429           | API calls quota exceeded! Maximum allowed is 30 requests per second.                             |

## Call The API By Code

### .NET Example:

```csharp
var options = new RestClientOptions("")
{
    MaxTimeout = -1,
};
var client = new RestClient(options);
var request = new RestRequest("[https://api.easysendsms.app/nv](https://easysendsms.com)", Method.Post);
request.AddHeader("Content-Type", "application/x-www-form-urlencoded");
request.AddParameter("username", "username");
request.AddParameter("password", "password");
request.AddParameter("number", "12345678900");
RestResponse response = await client.ExecuteAsync(request);
Console.WriteLine(response.Content);
```

### PHP Example:

```php
$curl = curl_init();
curl_setopt_array($curl, array(
    CURLOPT_URL => '[https://api.easysendsms.app/nv](https://easysendsms.com)',
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

### Java Example:

```java
Unirest.setTimeouts(0, 0);
HttpResponse<String> response = Unirest.post("[https://api.easysendsms.app/nv](https://easysendsms.com)")
  .header("Content-Type", "application/x-www-form-urlencoded")
  .field("username", "username")
  .field("password", "password")
  .field("number", "12345678900")
  .asString();
```
