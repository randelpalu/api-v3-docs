![Logo](../assets/img/logo.png?raw=true)

# Introduction 

Credy is a company that specializes on the quality lead generation. 

This document further describes how to integrate API to receive leads from Credy.

Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Each api request is signed. Signature consists of api_key, timestamp and hash.

Hash is a SHA1 hash of the concatenation of timestamp, api_key and secret_key. 

*ex. sha1(timestamp + apiKey + secretKey)*

API key and secret key will be assigned to you by Credy. 

API supports post fields and JSON. 

## Lead registration flow

1. Request is sent to https://www.your-site.com/your-url/receiveLead

2. API expects instant response: 
* If response status is ‘pin_verify’ (optional), customer will be asked for pin code
* If response status is ‘accepted’, lead will be marked as accepted. Customer will be redirected to your provided url if ‘landing_page_uri’ field exists in response

![Flow](./assets/img/flow.png?raw=true)

# API methods

### POST /receiveLead

This method will send application data. It consists of lead and customer.

| Field                                                                                  | Validations                                             | Description                                             |
|----------------------------------------------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| **lead**                                                                                   | Required, object                                        | **Contains lead data**                                      |
| lead[loan_sum]                                                                               | **Required float**                                          | loan amount                                             |
| lead[loan_period]                                                                            | Required integer                                        | loan term                                               |
| lead[ip_address]                                                                             | Required                                                | IP address of customer                                  |
| lead[uuid]                                                                                   | Required                                                | lead UUID in TC system                                  |
| **customer**                                                                               | **Required, object**                                        | **Contains customer data**                                  |
| customer[first_name]                                                                   | Required                                                | first name                                              |
| customer[last_name]                                                                    | Required                                                | last name                                               |
| customer[email]                                                                        | Required                                                | email                                                   |
| customer[personal_id]                                                                  | Required                                                | PESEL                                                   |
| customer[phone]                                                                        | Required                                                | phone number                                            |
| customer[gender]                                                                       | Required For possible values please see Addendum A      | gender                                                  |
| customer[birth_date]                                                                   | Required format: YYYY-MM-DD                             | birth date                                              |
| customer[id_card_number]                                                               | Required                                                | Id card number                                          |
| **customer[address]**                                                                      | **Required, object**                                        | **Actual address of customer**                              |
| customer[address][city]                                                                | Required                                                | city                                                    |
| customer[address][street]                                                              | Required                                                | street                                                  |
| customer[address][house_number]                                                        | Required                                                | house number with optional extension                    |
| customer[address][flat_number]                                                         | Optional                                                | flat number                                             |
| customer[address][postal_index]                                                        | Required                                                | postal code                                             |
| customer[lives_at_registered_address]                                                  | Optional <br />  boolean if lives_at_registered_address == false, secondary address fields are required <br />defaults to false |                       indicates if customer lives on registered address or not   |
| **customer[secondary_address]**                                                            | **Object, required if lives_at_registered_address = false** | **Legal address of customer**                               |
| customer[secondary_address][city]                                                      | Required when lives_at_registered_address = false       | city of secondary address                               |
| customer[secondary_address][street]                                                    | Required when lives_at_registered_address = false       | street of secondary address                             |
| customer[secondary_address][house_number]                                              | Required when lives_at_registered_address = false       | secondary address house number with optional extension  |
| customer[secondary_address][flat_number]                                               | Optional                                                | secondary address flat number                           |
| customer[secondary_address][postal_index]                                              | Required when lives_at_registered_address = false       | postal code of secondary address                        |
| customer[occupation]                                                                   | Required For possible values please see Addendum A      | occupation                                              |
| customer[neto_income]                                                                  | Required                                                | neto income in Zloty                                    |
| customer[employer]                                                                     | Required when occupation in list: <br />EMPLOYED_INDEFINITE_PERIOD <br />EMPLOYED_SPECIFIED_PERIOD <br /> WRITTEN_CONTRACT_OR_ORDER                      |                                                         |

| customer[password]                                                                     | Required                                                | generated random string                                 |
| **signature**                                                                              | **Required, object**                                        |       **request signature**             |
| signature[timestamp]                                                                   | Required                                                | unix timestamp, must be UTC +/- 60 seconds              |
| signature[api_key]                                                                     | Required                                                | api key                                                 |
| signature[hash]                                                                        | Required                                                | sha1 concatenation of timestamp, api key and secret key |


### JSON examples for /receiveLead method

**POST https://your-domain.com/your-url/receiveLead**

Body:


```json
{
 "customer": {
   "first_name": "\u10d1\u10d4\u10da\u10d0",
   "last_name": "\u10ee\u10d0\u10e0\u10d0\u10d8\u10e8\u10d5\u10d8\u10da\u10d8",
   "email": "vgurgenidze@mailinator.com",
   "phone": "+995557509160",
   "birth_date": "1999-01-01",
   "gender": "FEMALE",
   "personal_id": "63273962439",
   "id_card_number": "34LS88632",
   "neto_income": "7861",
   "occupation": "BENEFITS",
   "doc_type": "ID",
   "current_employment_start": "2017-05-23",
   "address": {
     "city": "\u10e5. \u10d7\u10d4\u10e0\u10ef\u10dd\u10da\u10d0",
     "street": "\u10d2\u10d8\u10dd\u10e0\u10d2\u10d0\u10eb\u10d4 \u10e5.",
     "house_number": "01",
     "postal_index": "2373"
   },
   "lives_at_registered_address": 1,
   "employer_city": null,
   "password": "Qm2EWmOK"
 },
 "lead": {
   "loan_sum": "2706.00",
   "loan_period": "3",
   "ip_address": "10.46.194.171",
   "uuid": "e3059d69-ca20-4e0b-864c-72838488b473",
   "product": "INSTALMENT"
 },
 "signature": {
   "api_key": "f30b4720-0d97-4421-a867-904962ff93b4",
   "timestamp": 1518698268,
   "hash": "2433413474a94eae67de0b238a6b09e97c7cef39"
 }
}
```


**Successful response**

Body: 

```json
{
 "external_lead_id": "12345",
 "status": "accepted"
}
```

**Duplicate response**

Body: 

```json
{
 "status": "duplicate"
}
```


**Invalid params response**

Body:

```json
{
 "status": "invalid_params",
 "reason": "housing type is invalid, phone number is invalid"
}
```


For all possible responses please see [Expected responses for receiveLead & checkPin methods](#expected-responses-for-receivelead--checkpin-methods)

### POST /checkPin

Optional method. This method is used to verify pin code.

| Field                     | Validations      | Description                                                                          |
|---------------------------|------------------|--------------------------------------------------------------------------------------|
| pin                       | Required         | pin code                                                                             |
| phone                     | Required         | phone number                                                                         |
| uuid                      | Required UUID    | unique identifier of loan application in TC system                                   |
| external_lead_id          | Optional         | if external_lead_id was provided in /receiveLead response, it will be sent back here |
| **signature**                 | **Required, object** |  **request signature**                                                                                    |
| 
| signature[timestamp]      | Required         | unix timestamp, must be UTC +/- 60 seconds                                           |
| signature[api_key]        | Required         | api key                                                                              |
| signature[hash]           | Required         | sha1 concatenation of timestamp, api key and secret key                              |


### JSON examples for /checkPin method

**POST https://your-domain.com/your-url/checkPin**

Body:

```json
{
 "pin": "1234",
 "phone": "+995557321318",
 "uuid": "94dd50f3-08ee-41c4-8dd2-3b83d0ebd3ef",
 "external_lead_id": "cb5c7696-1233-11e8-b217-8b15c630db00",
 "signature": {
   "api_key": "c1a0ed51f2a2e04bbf501bb787158b02",
   "timestamp": 1518687435,
   "hash": "6a2565d03aeb13554c16979d0a129edbfcc743ea"
 }
}
```


**Pin confirmed response**

Body: 

```json
{
 "status": "accepted"
}
```


**Invalid pin code response**

Body: 

```json
{
 "status": "pin_invalid"
}
```

**Successful response (with redirect)**

Body: 

```json
{
 "external_lead_id": "a7713e1a-1233-11e8-8faa-a1c29134f140",
 "landing_page_uri": "https://your-awesome-site.com/page123",
 "status": "accepted"
}
```

**Successful response (receiveLead + ask for Pin)**

Body:

```json
{
 "status": "pin_verify",
 "external_lead_id": "a7713e1a-1233-11e8-8faa-a1c29134f140",
 "password": "ywcdyvb9"
}
```

For all possible responses please see [Expected responses for receiveLead & checkPin methods](#expected-responses-for-receivelead--checkpin-methods)

### Expected responses for receiveLead & checkPin methods

Expected responses have following fields.

| Field            | Validations                                        | Description                                                                   |
|------------------|----------------------------------------------------|-------------------------------------------------------------------------------|
| status           | Required For possible values please see Addendum B | application status                                                            |
| reason           | Required when status == invalid_params Free text   | additional explanation to status field                                        |
| external_lead_id | Optional                                           | application identifier in your system                                         |
| landing_page_uri | Optional                                           | landing page url, should be filled when redirection to your page is necessary |
| password         | Optional                                           | if password is generated in your system and TC needs to show it to customer   |


# Addendum A - Request ENUMs

| gender |             |
|--------|-------------|
| value  | description |
| MALE   |             |
| FEMALE |             |


| occupation                 |              |
|----------------------------|--------------|
| value                      | description  |
| EMPLOYED_INDEFINITE_PERIOD |              |
| EMPLOYED_SPECIFIED_PERIOD  |              |
| WRITTEN_CONTRACT_OR_ORDER  |              |
| ECONOMIC_ACTIVITY          | own business |
| UNEMPLOYED                 |              |
| STUDENT                    |              |
| PENSIONER1                 | pensioner    |
| OTHER                      |              |
| MATERNITY_LEAVE            |              |
| BENEFITS                   |              |


# Addendum B - Response ENUMs

| status (used in responses)              |                                                                                 |
|-----------------------------------------|---------------------------------------------------------------------------------|
| value                                   | description                                                                     |
| accepted                                | loan application is accepted by api                                             |
| duplicate                               | customer already exists in the system                                           |
| invalid_params                          | request contains ivalid parameters                                              |
| rejected                                | application rejected                                                            |
| pin_verify                              | pin code is sent to customer, TC system should verify pin with /checkPin method |
| pin_invalid (only for /checkPin method) | indicates that pin code is invalid and customer must be asked to try again      |

# Versions

- 1.0 (2018-02-15): first publish
