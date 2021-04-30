### Table of Contents

  * [General API Information](#general-api-information)
  * [HTTP Return Codes](#http-return-codes)
  * [Limits](#limits)
  * [Check Server is Alive](#check-server-is-alive)
  * [Private API Endpoints](#private-api-endpoints)
     * [Examples for Private API](#examples-for-private-api)
     * [Find Similarities](#find-similarities)
  * [Public API Endpoints](#public-api-endpoints)
     * [Get Available Classes](#get-available-classes)
     * [Get Current Usage](#get-current-usage)

# Initium API Documentation

## General API Information
* The base API endpoint is https://api.initium.ai
* All endpoints return either a JSON object or a string.

## HTTP Return Codes
* HTTP `403 Forbidden` return code is used when the query parameters are missing or invalid.
* HTTP `500 Internal Server Error` return code is used when either there is an error in the server, or the server is overloaded.

## Limits
* For endpoints that contain parameter `q`, the length should not be larger than 1000 characters.
* There are limits on the number of requests being sent and the number of tokens being processed per month. Once the allotted amount is reached, requests from the user will be dropped until it is refreshed on the first day of next month. The actual number depends on the subscription tier. The subscription tier can be changed on the website. The following table is the current allocation:

    Subscription Tier | #Requests per month | #Tokens per month
    ----------------- | -------- | --------
    0 | 100 | 1,000
    1 | 1,000 | 10,000

## Check Server is Alive
Users can check if the server is alive by directly pinging the base API endpoint. A string of `server is alive!` will be shown.

## Private API Endpoints

### Examples for Private API
In order to access the private API endpoints, the following steps should be applied. A pair of api_key and secrect_key can be obtained from our website after login.
1. Generate `signature` of `q=<message_string>` with `secret_key` using SHA-256
2. Send `api_key` as part of the request header, and `signature` as part of the request body or query string

Here is a walk-through example of sending valid requests to the private API endpoints from the Linux command line using `echo`, `openssl`, and `curl`. The endpoint is to count the number of words in a passage.

Key | Value
----- | --------
api_key | Pd18fous3vMUUwqMq9PFrEhOxnIdmkTV82KBTMWDyO4PHVokED8inKjs0WSHaUlX
secret_key | CeozhAHBLmImq0V6R1kUnLyGiL1NLz2BZGtzCaFREbFjwS6amqRf5W7S95FfZUpb

1. Generate signature:
```
[linux]$ echo -n "q=hello world!" | openssl dgst -sha256 -hmac "CeozhAHBLmImq0V6R1kUnLyGiL1NLz2BZGtzCaFREbFjwS6amqRf5W7S95FfZUpb"
(stdin)= e69ea112dd870973062b04bb0c86603eb4026088c8d82359818dc61fcff6d28e
```
2. Send request:
```
[linux]$ curl -H "X-INITIUM-APIKEY: Pd18fous3vMUUwqMq9PFrEhOxnIdmkTV82KBTMWDyO4PHVokED8inKjs0WSHaUlX" -X GET "https://api.initium.ai/wordsCount" -d "q=hello world!&signature=e69ea112dd870973062b04bb0c86603eb4026088c8d82359818dc61fcff6d28e"
{"count":2}
```
### Find Similarities
```
GET /classSim
```
Find the similarities between a passage and a class.
* Parameters

Name | Type | Required | Description
----- | -------- | -- | --
q | string | Yes | 
class | string | Yes | name of a class, or `ALL` for all the classes
n | integer | Yes | n tokens in the passage that are most closest to the class vector

* Example Response

```
{
    "cos_sim": 0.7678184693855867,
    "top_N": [
        [ "doctor", 0.4620885588754613 ],
        [ "you", 0.7937009166077699 ],
        ...
    ]
}
```

## Public API Endpoints
All the public endpoints need to send the api_key as part of the request header, but no need to generate the signature.

### Get Available Classes
```
GET /class
```
Get all the classes that are currently supported.
* Example Response

```
{
    "class": ["money", "power", "leisure", "covid19", "health"]
}
```

### Get Current Usage
```
GET /usage
```
Get the current number of requests and tokens being processed, as well as the number of allotted total.
* Example Response

```
{
    "requst_usage": {"count":4,"limit":100},
    "token_usage": {"count":45,"limit":1000}
}
```

