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

<table>
  <tr>
    <td>Field</td>
    <td>Validations</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>lead</td>
    <td>Required, object</td>
    <td>Contains lead data</td>
  </tr>
  <tr>
    <td>loan_sum</td>
    <td>Required
float
</td>
    <td>loan amount</td>
  </tr>
  <tr>
    <td>loan_period</td>
    <td>Required
integer</td>
    <td>loan term</td>
  </tr>
  <tr>
    <td>ip_address</td>
    <td>Required</td>
    <td>IP address of customer</td>
  </tr>
  <tr>
    <td>uuid</td>
    <td>Required</td>
    <td>lead UUID in TC system</td>
  </tr>
  <tr>
    <td>customer</td>
    <td>Required, object</td>
    <td>Contains customer data</td>
  </tr>
  <tr>
    <td>customer[first_name]</td>
    <td>Required</td>
    <td>first name</td>
  </tr>
  <tr>
    <td>customer[last_name]</td>
    <td>Required</td>
    <td>last name</td>
  </tr>
  <tr>
    <td>customer[email]</td>
    <td>Required</td>
    <td>email</td>
  </tr>
  <tr>
    <td>customer[personal_id]</td>
    <td>Required</td>
    <td>PESEL</td>
  </tr>
  <tr>
    <td>customer[phone]</td>
    <td>Required
</td>
    <td>phone number</td>
  </tr>
  <tr>
    <td>customer[gender]</td>
    <td>Required
For possible values please see Addendum A</td>
    <td>gender</td>
  </tr>
  <tr>
    <td>customer[birth_date]</td>
    <td>Required
format: YYYY-MM-DD</td>
    <td>birth date</td>
  </tr>
  <tr>
    <td>customer[id_card_number]</td>
    <td>Required</td>
    <td>Id card number</td>
  </tr>
  <tr>
    <td>customer[address]</td>
    <td>Required, object</td>
    <td>Actual address of customer</td>
  </tr>
  <tr>
    <td>customer[address][city]</td>
    <td>Required</td>
    <td>city</td>
  </tr>
  <tr>
    <td>customer[address][street]</td>
    <td>Required</td>
    <td>street</td>
  </tr>
  <tr>
    <td>customer[address][house_number]</td>
    <td>Required</td>
    <td>house number with optional extension</td>
  </tr>
  <tr>
    <td>customer[address][flat_number]</td>
    <td>Optional</td>
    <td>flat number</td>
  </tr>
  <tr>
    <td>customer[address][postal_index]</td>
    <td>Required</td>
    <td>postal code</td>
  </tr>
  <tr>
    <td>customer[lives_at_registered_address]</td>
    <td>Optional

boolean
if lives_at_registered_address == false, secondary address fields are required

defaults to false</td>
    <td>indicates if customer lives on registered address or not</td>
  </tr>
  <tr>
    <td>customer[secondary_address]</td>
    <td>Object, required if lives_at_registered_address = false</td>
    <td>Legal address of customer</td>
  </tr>
  <tr>
    <td>customer[secondary_address][city]</td>
    <td>Required when lives_at_registered_address = false</td>
    <td>city of secondary address
</td>
  </tr>
  <tr>
    <td>customer[secondary_address][street]</td>
    <td>Required when lives_at_registered_address = false</td>
    <td>street of secondary address
</td>
  </tr>
  <tr>
    <td>customer[secondary_address][house_number]</td>
    <td>Required when lives_at_registered_address = false</td>
    <td>secondary address house number with optional extension
</td>
  </tr>
  <tr>
    <td>customer[secondary_address][flat_number]</td>
    <td>Optional</td>
    <td>secondary address flat number</td>
  </tr>
  <tr>
    <td>customer[secondary_address][postal_index]</td>
    <td>Required when lives_at_registered_address = false</td>
    <td>postal code of secondary address</td>
  </tr>
  <tr>
    <td>customer[occupation]</td>
    <td>Required
For possible values please see Addendum A</td>
    <td>occupation</td>
  </tr>
  <tr>
    <td>customer[neto_income]</td>
    <td>Required</td>
    <td>neto income in Zloty
</td>
  </tr>
  <tr>
    <td>customer[employer]</td>
    <td>Required when occupation in list: 

EMPLOYED_INDEFINITE_PERIOD
EMPLOYED_SPECIFIED_PERIOD
WRITTEN_CONTRACT_OR_ORDER</td>
    <td></td>
  </tr>
  <tr>
    <td>customer[password]</td>
    <td>Required</td>
    <td>generated random string</td>
  </tr>
  <tr>
    <td>signature</td>
    <td>Required, object

contains signature fields</td>
    <td>request signature</td>
  </tr>
  <tr>
    <td>signature[timestamp]</td>
    <td>Required</td>
    <td>unix timestamp, must be UTC +/- 60 seconds </td>
  </tr>
  <tr>
    <td>signature[api_key]</td>
    <td>Required</td>
    <td>api key</td>
  </tr>
  <tr>
    <td>signature[hash]</td>
    <td>Required</td>
    <td>sha1 concatenation of timestamp, api key and secret key</td>
  </tr>
</table>


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

<table>
  <tr>
    <td>Field</td>
    <td>Validations</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>pin</td>
    <td>Required</td>
    <td>pin code</td>
  </tr>
  <tr>
    <td>phone</td>
    <td>Required</td>
    <td>phone number</td>
  </tr>
  <tr>
    <td>uuid</td>
    <td>Required
UUID</td>
    <td>unique identifier of loan application in TC system</td>
  </tr>
  <tr>
    <td>external_lead_id</td>
    <td>Optional</td>
    <td>if external_lead_id was provided in /receiveLead response, it will be sent back here</td>
  </tr>
  <tr>
    <td>signature</td>
    <td>Required, object

contains signature fields</td>
    <td>request signature</td>
  </tr>
  <tr>
    <td>signature[timestamp]</td>
    <td>Required</td>
    <td>unix timestamp, must be UTC +/- 60 seconds </td>
  </tr>
  <tr>
    <td>signature[api_key]</td>
    <td>Required</td>
    <td>api key</td>
  </tr>
  <tr>
    <td>signature[hash]</td>
    <td>Required</td>
    <td>sha1 concatenation of timestamp, api key and secret key</td>
  </tr>
</table>


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

<table>
  <tr>
    <td>Field</td>
    <td>Validations</td>
    <td>Description</td>
  </tr>
  <tr>
    <td>status</td>
    <td>Required
For possible values please see Addendum B</td>
    <td>application status</td>
  </tr>
  <tr>
    <td>reason</td>
    <td>Required when status == invalid_params
Free text</td>
    <td>additional explanation to status field</td>
  </tr>
  <tr>
    <td>external_lead_id</td>
    <td>Optional</td>
    <td>application identifier in your system</td>
  </tr>
  <tr>
    <td>landing_page_uri</td>
    <td>Optional</td>
    <td>landing page url, should be filled when redirection to your page is necessary</td>
  </tr>
  <tr>
    <td>password</td>
    <td>Optional</td>
    <td>if password is generated in your system and TC needs to show it to customer</td>
  </tr>
</table>


# Addendum A - Request ENUMs

<table>
  <tr>
    <td>gender</td>
    <td></td>
  </tr>
  <tr>
    <td>value</td>
    <td>description</td>
  </tr>
  <tr>
    <td>MALE</td>
    <td></td>
  </tr>
  <tr>
    <td>FEMALE</td>
    <td></td>
  </tr>
</table>


<table>
  <tr>
    <td>occupation</td>
    <td></td>
  </tr>
  <tr>
    <td>value</td>
    <td>description</td>
  </tr>
  <tr>
    <td>EMPLOYED_INDEFINITE_PERIOD</td>
    <td></td>
  </tr>
  <tr>
    <td>EMPLOYED_SPECIFIED_PERIOD</td>
    <td></td>
  </tr>
  <tr>
    <td>WRITTEN_CONTRACT_OR_ORDER</td>
    <td></td>
  </tr>
  <tr>
    <td>ECONOMIC_ACTIVITY</td>
    <td>own business</td>
  </tr>
  <tr>
    <td>UNEMPLOYED</td>
    <td></td>
  </tr>
  <tr>
    <td>STUDENT</td>
    <td></td>
  </tr>
  <tr>
    <td>PENSIONER1</td>
    <td>pensioner</td>
  </tr>
  <tr>
    <td>OTHER</td>
    <td></td>
  </tr>
  <tr>
    <td>MATERNITY_LEAVE</td>
    <td></td>
  </tr>
  <tr>
    <td>BENEFITS</td>
    <td></td>
  </tr>
</table>


# Addendum B - Response ENUMs

<table>
  <tr>
    <td>status (used in responses)</td>
    <td></td>
  </tr>
  <tr>
    <td>value</td>
    <td>description</td>
  </tr>
  <tr>
    <td>accepted</td>
    <td>loan application is accepted by api</td>
  </tr>
  <tr>
    <td>duplicate</td>
    <td>customer already exists in the system</td>
  </tr>
  <tr>
    <td>invalid_params</td>
    <td>request contains ivalid parameters</td>
  </tr>
  <tr>
    <td>rejected</td>
    <td>application rejected</td>
  </tr>
  <tr>
    <td>pin_verify</td>
    <td>pin code is sent to customer, TC system should verify pin with /checkPin method</td>
  </tr>
  <tr>
    <td>pin_invalid (only for /checkPin method)</td>
    <td>indicates that pin code is invalid and customer must be asked to try again</td>
  </tr>
</table>

# Versions

- 1.0 (2018-02-15): first publish
