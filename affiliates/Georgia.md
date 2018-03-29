![Logo](../assets/img/logo.png?raw=true)

# Introduction

Credy is a company that specializes on the quality lead generation.
This document further describes how to integrate API to send leads to Credy.
Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Production URL: [https://api.credy.eu/v3/](https://api.credy.eu/v3/)

Staging URL: [http://api.staging.credy.eu/](http://api.staging.credy.eu/)

API supports post fields, XML and JSON.

To get response error messages in georgian, "Accept-Language" header’s value must be set to “ka”.

## Signing requests

Each request must be signed. Signature consists of api_key, timestamp and hash.

Hash is a SHA1 hash of the concatenation of timestamp, api_key and secret_key.

Allowed deviation for timestamp is UTC +/- 60 seconds.

*ex. sha1(timestamp + apiKey + secretKey)*

Api key and secret key will be assigned to you by Credy.

## Lead registration flow

1. Request is sent to /v3/customers

2. If request is successful, customer uuid must be stored (uuid is a field in response)

3. Request is sent to /v3/leads with previously stored customer uuid in query parameter "customer" *ex. /v3/leads?customer=a3ff7c20-e9e8-43a0-bfb7-71a5cf43b8d3*

# API methods

### POST /v3/customers

This method will create or update customer and return customer uuid.

| Field                           | Validations                                                                                                                                                                | Description                                                                                                                                                   |
|---------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| first_name                      | Required<br /> Must match regex: /^[\p{Georgian}[:space:]-]+$/u                                                                                                            | First name of customer                                                                                                                                        |
| last_name                       | Required<br /> Must match regex: /^[\p{Georgian}[:space:]-]+$/u                                                                                                            | Last name of customer                                                                                                                                         |
| email                           | Required<br /> Must be valid email                                                                                                                                         | Email address                                                                                                                                                 |
| personal_id                     | Required<br /> Must be valid                                                                                                                                               | Personal ID of customer                                                                                                                                       |
| phone                           | Required<br /> Must be valid georgian number                                                                                                                               | Customer phone number                                                                                                                                         |
| gender                          | Required<br /> For allowed values see Appendix A                                                                                                                           | Gender                                                                                                                                                        |
| birth_date                      | Required<br /> Must be valid date YYYY-mm-dd                                                                                                                               | Birth date of customer                                                                                                                                        |
| doc_type                        | Required<br /> For allowed values see Appendix A                                                                                                                           | Document type Indicates which value will be sent in id_card_number field                                                                                      |
| id_card_number                  | Required<br /> Must match regex In case of ID & passport: /^[0-9]{2}[A-Z]{2}[0-9]{5}$/ In case of old ID: /^[\p{Georgian}][0-9]{7}$/u                                      | Document number for document type sent in doc_type field                                                                                                      |
| occupation                      | Required<br /> For allowed values see Appendix A                                                                                                                           | Occupation type                                                                                                                                               |
| neto_income                     | Required<br /> Must be valid number                                                                                                                                        | Neto income in GEL                                                                                                                                            |
| address[city]                   | Required                                                                                                                                                                   | City                                                                                                                                                          |
| address[street]                 | Required<br /> Minimum length 3 characters                                                                                                                                 | Street                                                                                                                                                        |
| address[house_number]           | Required<br /> Must match regex: /^\d+[\p{Georgian}]{0,1}$/u                                                                                                               | House number                                                                                                                                                  |
| address[flat_number]            | Must be integer                                                                                                                                                            | Flat numver                                                                                                                                                   |
| address[postal_code]            | Required<br /> Must match regex: /^\d{4}$/                                                                                                                                 | Postal code                                                                                                                                                   |
| lives_at_registered_address     | Boolean                                                                                                                                                                    | Does customer live at his/her registered address                                                                                                              |
| secondary_address[city]         | Required when lives_at_registered_address is false                                                                                                                         | City                                                                                                                                                          |
| secondary_address[street]       | Required when lives_at_registered_address is false<br /> Minimum length 3 characters                                                                                       | Street                                                                                                                                                        |
| secondary_address[house_number] | Required when lives_at_registered_address is false<br /> Must match regex: /^\d+[\p{Georgian}]{0,1}$/u                                                                     | House number                                                                                                                                                  |
| secondary_address[flat_number]  | Must be integer                                                                                                                                                            | Flat numver                                                                                                                                                   |
| secondary_address[postal_code]  | Required when lives_at_registered_address is false<br /> Must match regex: /^\d{4}$/                                                                                       | Postal code                                                                                                                                                   |
| signature                       | Required, object contains signature fields                                                                                                                                 | request signature                                                                                                                                             |
| signature[timestamp]            | Required                                                                                                                                                                   | unix timestamp, must be UTC +/- 60 seconds                                                                                                                    |
| signature[api_key]              | Required                                                                                                                                                                   | api key                                                                                                                                                       |
| signature[hash]                 | Required                                                                                                                                                                   | sha1 concatenation of timestamp, api key and secret key                                                                                                       |


### JSON examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |


Body:
```json
{
    "first_name": "ლენა",
    "last_name": "ჯინჭარაძე",
    "personal_id": "09033964901",
    "phone": "+995 557 993 306",
    "email": "inga33@mailinator.com",
    "id_card_number": "ბ5115476",
    "doc_type": "ID_OLD",
    "birth_date": "1993-08-01",
    "address": {
        "city": "ქ. ფოთი",
        "street": "ხუციშვილი ჩიხი",
        "house_number": "22ნ",
        "flat_number":"18",
        "postal_code": "7185"
    },
    "secondary_address": {
        "city": "ქალაქი ცხინვალი",
        "street": "კუპატაძე გზატკეცილი",
        "house_number": "361",
        "postal_code": "5297"
    },
    "car": "YES",
    "gender": "MALE",
    "occupation": "WRITTEN_CONTRACT_OR_ORDER",
    "neto_income": 2363,
    "lives_at_registered_address": false,
    "signature": {
        "timestamp": 1483535066,
        "api_key": "georgia",
        "hash": "c0ee12c730fae90ecfe1d76af8a874d9667d143b"
    }
}
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```json
{
    "uuid": "f549599b-9ea0-402d-9524-a07707a7da17",
    "first_name": "ლენა",
    "last_name": "ჯინჭარაძე",
    "email": "inga33@mailinator.com",
    "product": "PAYDAY",
    "personal_id": "09033964901",
    "phone": "+995557993306",
    "neto_income": 2363,
    "lives_at_registered_address": false,
    "car": "YES",
    "occupation": "WRITTEN_CONTRACT_OR_ORDER",
    "gender": "MALE",
    "birth_date": "1993-08-01",
    "id_card_number": "ბ5115476",
    "doc_type": "ID_OLD",
    "address": {
        "city": "ქ. ფოთი",
        "street": "ხუციშვილი ჩიხი",
        "house_number": "22ნ",
        "flat_number": "18",
        "postal_code": "7185"
    },
    "secondary_address": {
        "city": "ქალაქი ცხინვალი",
        "street": "კუპატაძე გზატკეცილი",
        "house_number": "361",
        "flat_number": null,
        "postal_code": "5297"
    }
}
```

**Failed response for /v3/customers**

Status code: 422

Response:
```json
[
    {
        "field": "occupation",
        "message": "Occupation is invalid."
    },
    {
        "field": "address.city",
        "message": [
            "City cannot be blank."
        ]
    }
]
```
### XML examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/xml  |
| Accept       | application/xml  |


Body:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<request>
    <first_name>ლენა</first_name>
    <last_name>ჯინჭარაძე</last_name>
    <personal_id>09033964901</personal_id>
    <phone>+995 557 993 306</phone>
    <email>inga33@mailinator.com</email>
    <id_card_number>ბ5115476</id_card_number>
    <doc_type>ID_OLD</doc_type>
    <birth_date>1993-08-01</birth_date>
    <address>
        <city>ქ. ფოთი</city>
        <street>ხუციშვილი ჩიხი</street>
        <house_number>22ნ</house_number>
        <flat_number>18</flat_number>
        <postal_code>7185</postal_code>
    </address>
    <secondary_address>
        <city>ქალაქი ცხინვალი</city>
        <street>კუპატაძე გზატკეცილი</street>
        <house_number>361</house_number>
        <postal_code>5297</postal_code>
    </secondary_address>
    <car>YES</car>
    <gender>MALE</gender>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <neto_income>2363</neto_income>
    <lives_at_registered_address> 0</lives_at_registered_address>
    <signature>
        <timestamp>1483535066</timestamp>
        <api_key>georgia</api_key>
        <hash>c0ee12c730fae90ecfe1d76af8a874d9667d143</hash>
    </signature>
</request>
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <uuid>f549599b-9ea0-402d-9524-a07707a7da17</uuid>
    <first_name>ლენა</first_name>
    <last_name>ჯინჭარაძე</last_name>
    <email>inga33@mailinator.com</email>
    <product>PAYDAY</product>
    <personal_id>09033964901</personal_id>
    <phone>+995557993306</phone>
    <neto_income>2363</neto_income>
    <lives_at_registered_address>0</lives_at_registered_address>
    <car>YES</car>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <gender>MALE</gender>
    <birth_date>1993-08-01</birth_date>
    <id_card_number>ბ5115476</id_card_number>
    <doc_type>ID_OLD</doc_type>
    <address>
        <city>ქ. ფოთი</city>
        <street>ხუციშვილი ჩიხი</street>
        <house_number>22ნ</house_number>
        <flat_number>18</flat_number>
        <postal_code>7185</postal_code>
    </address>
    <secondary_address>
        <city>ქალაქი ცხინვალი</city>
        <street>კუპატაძე გზატკეცილი</street>
        <house_number>361</house_number>
        <flat_number></flat_number>
        <postal_code>5297</postal_code>
    </secondary_address>
</response>
```

**Failed response for /v3/customers**

Status code: 422

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <item>
        <field>first_name</field>
        <message>First Name cannot be blank.</message>
    </item>
    <item>
        <field>address.city</field>
        <message>
            <item>City cannot be blank.</item>
        </message>
    </item>
</response>
```

### POST /v3/leads?customer={customer_uuid} *

Creates lead for existing customer.

* Replace customer_uuid placeholder with received uuid value from POST /v3/customers response.

| Field                | Validations                                | Description                                             |
|----------------------|--------------------------------------------|---------------------------------------------------------|
| loan_sum             | Required Must be valid number              | Amount of credit requested by customer                  |
| loan_period          | Required Must be valid number              | Term for credit requested                               |
| ip_address           | Must be valid IP address                   | IP address of customer                                  |
| aff_lead_id          |                                            | Your lead ID                                            |
| signature            | Required, object contains signature fields | request signature                                       |
| signature[timestamp] | Required                                   | unix timestamp, must be UTC +/- 60 seconds              |
| signature[api_key]   | Required                                   | api key                                                 |
| signature[hash]      | Required                                   | sha1 concatenation of timestamp, api key and secret key |


### JSON examples for /v3/leads

**POST /v3/leads?customer=bbf18703-1f8d-5c8a-a83b-9433f003807f**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |


Body:

```json
{
    "loan_sum": "50",
    "loan_period": "1",
    "ip_address": "127.0.0.1",
    "signature": {
        "api_key": "6acc49b6-540c-512f-b1b8-7e1a9eb7d7a5",
        "timestamp": 1465310979,
        "hash": "051d1de34995444d44ab344e62e5775727f10f36"
    }
}
```

**Successful response for /v3/leads**

Status code: 2xx

Response:
```json
{
    "loan_sum": "250.00",
    "loan_period": "30",
    "uuid": "76171735-1c22-5564-8438-fb597523fdeb",
    "status": "UNDER_INVESTIGATION",
    "date_created": "2017-01-04 08:33:09",
    "product": "PAYDAY"
}
```

**Example failed response for /v3/leads**

Status code: 422

Response:
```json
[
    {
        "field": "loan_sum",
        "message": "Loan Sum cannot be blank."
    }
]
```

### XML examples for /v3/leads

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/xml  |
| Accept       | application/xml  |


**POST /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Body:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<request>
    <loan_sum>50</loan_sum>
    <loan_period>1</loan_period>
    <ip_address>127.0.0.1</ip_address>
    <signature>
        <api_key>6acc49b6-540c-512f-b1b8-7e1a9eb7d7a5</api_key>
        <timestamp>1465310979</timestamp>
        <hash>051d1de34995444d44ab344e62e5775727f10f36</hash>
    </signature>
</request>
```

**Successful response for /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <loan_sum>50.00</loan_sum>
    <loan_period>1</loan_period>
    <uuid>5badfe4e-a157-55a4-bcdb-f9f3754ea054</uuid>
    <status>UNDER_INVESTIGATION</status>
    <date_created>2017-01-04 17:25:00</date_created>
</response>
```

**Failed response for /v3/leads?customer=318d4a7b-b39e-58f2-a413-caefbe1615b7**

Status code: 422

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <item>
        <field>loan_sum</field>
        <message>Loan Sum cannot be blank.</message>
    </item>
</response>
```

# Appendix A - ENUMs

### gender

| value  | English | Georgian   |
|--------|---------|------------|
| MALE   | Male    | მამრობითი  |
| FEMALE | Female  | მდედრობითი |

### occupation

| value                      | English                           | Georgian                        |
|----------------------------|-----------------------------------|---------------------------------|
| EMPLOYED_INDEFINITE_PERIOD | Employed: indefinite time         | დასაქმებული განუსაზღვრელი ვადით |
| EMPLOYED_SPECIFIED_PERIOD  | Employed: specified time          | დასაქმებული განსაზღვრული ვადით  |
| WRITTEN_CONTRACT_OR_ORDER  | Written contract or order         | ხელშეკრულების საფუძველზე        |
| ECONOMIC_ACTIVITY          | Self employed (economic activity) | სამეწარმეო საქმიანობა           |
| UNEMPLOYED                 | Unemployed                        | უმუშევარი                       |
| STUDENT                    | Student                           | სტუდენტი                        |
| PENSIONER1                 | Pensioner                         | პენსიონერი                      |
| MATERNITY_LEAVE            | Maternity leave                   | დეკრეტული შვებულება             |
| BENEFITS                   | Benefits                          | სოციალური დახმარება             |
| OTHER                      | Other                             | სხვა                            |

### doc_type

| value    | English     | Georgian                |
|----------|-------------|-------------------------|
| ID       | ID card     | ID ბარათი               |
| PASSPORT | Passport    | პასპორტი                |
| ID_OLD   | Old ID card | ძველი პირადობის მოწმობა |

### housing_type

| value                     | English                   | Georgian                |
|---------------------------|---------------------------|-------------------------|
| RENTED_ROOM               | Rented room               | დაქირავებული ოთახი      |
| RENTED_APARTMENT_OR_HOUSE | Rented apartment or house | ქირით                   |
| OWN_HOUSE_OR_APARTMENT    | Own house or appartment   | საკუთარი სახლი ან ბინა  |
| WITH_PARENTS              | With parents              | მშობლებთან ერთად        |

### car

| value | English |
|-------|---------|
| YES   | Yes     |
| NO    | No      |
