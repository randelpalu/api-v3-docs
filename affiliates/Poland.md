# Introduction 

Traffic Control is a company that specializes on the quality lead generation. 
This document further describes how to integrate API to send leads to Traffic Control.
Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Production URL: [https://api.credy.eu/v3/](https://api.credy.eu/v3/)

Staging URL: [http://api.staging.credy.eu/v3/](http://api.staging.credy.eu/v3/)

## Signing requests

Each request must be signed. Signature consists of api_key, timestamp and hash.

Hash is a SHA1 hash of the concatenation of timestamp, api_key and secret_key.

Allowed deviation for timestamp is UTC +/- 60 seconds.

*ex. sha1(timestamp + apiKey + secretKey)*

Api key and secret key will be assigned to you by Traffic Control.

API supports post fields, XML and JSON.

To get response error messages in polish, "Accept-Language" header’s value must be set to “pl”.

## Lead registration flow

1. Request is sent to /v3/customers

2. If request is successful, customer uuid must be stored (uuid is a field in response)

3. Request is sent to /v3/leads with previously stored customer uuid in query parameter "customer" *ex. /v3/leads?customer=a3ff7c20-e9e8-43a0-bfb7-71a5cf43b8d3*

# API methods

### POST /v3/customers

This method will create or update customer and return customer uuid.

| Field                           | Validations                                                                                                                                   | Description                                                |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| first_name                      | Required                                                                                                                                      | First name of customer                                     |
| last_name                       | Required                                                                                                                                      | Last name of the customer                                  |
| email                           | Required <br /> valid email address                                                                                                                 | Email address                                              |
| personal_id                     | Required <br /> must be valid PESEL                                                                                                                 | PESEL                                                      |
| bank_account                    | Optional <br /> must be valid IBAN                                                                                                                  | Bank account                                               |
| phone                           | Required <br /> must be valid phone number                                                                                                          | Customer mobile phone number                               |
| id_card_number                  | Required <br /> must be valid ID card number                                                                                                        | ID card number                                             |
| occupation                      | Required <br /> for allowed values see Addendum A                                                                                                   | Occupation                                                 |
| housing_type                    | Required <br /> for allowed values see Addendum A                                                                                                   | Housing type                                               |
| marital_status                  | Required <br /> for allowed values see Addendum A                                                                                                   | Marital status                                             |
| education                       | Required <br /> for allowed values see Addendum A                                                                                                   | Education                                                  |
| neto_income                     | Required <br /> numeric                                                                                                                             | Net income                                                 |
| monthly_expenses                | Required <br /> numeric                                                                                                                             | Monthly expenses                                           |
| address                         | Required <br /> Object                                                                                                                              | Real address of the customer                               |
| address[city]                   | Required <br /> free text                                                                                                                           | City                                                       |
| address[street]                 | Required <br /> free text                                                                                                                           | Street                                                     |
| address[house_number]           | Required <br /> integer                                                                                                                             | House number                                               |
| address[flat_number]            | Optional <br /> must match regex: /^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u                                                                                        | Flat number                                                |
| address[postal_index]           | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| lives_at_registered_address     | Optional <br /> boolean; defaults to true                                                                                                           | Is customer’s real address the same as registered address? |
| secondary_address               | Required <br /> Object                                                                                                                              | Registered address of the customer                         |
| secondary_address[city]         | Required <br /> free text                                                                                                                           | City                                                       |
| secondary_address[street]       | Required <br /> free text                                                                                                                           | Street                                                     |
| secondary_address[house_number] | Required <br /> integer                                                                                                                             | House number                                               |
| secondary_address[flat_number]  | Optional <br /> must match regex: /^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u                                                                                        | Flat number                                                |
| secondary_address[postal_index] | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| employer_address                | Required when occupation in list: EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER                            | Employer’s address                                         |
| employer_address[city]          | Required <br /> free text                                                                                                                           | City                                                       |
| employer_address[street]        | Required <br /> free text                                                                                                                           | Street                                                     |
| employer_address[house_number]  | Required <br /> integer                                                                                                                             | House number                                               |
| employer_address[flat_number]   | Optional <br /> must match regex: /^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u                                                                                        | Flat number                                                |
| employer_address[postal_index]  | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| remuneration_deadline           | Required when occupation != UNEMPLOYED; format: YYYY-MM-DD                                                                                    | Remuneration deadline                                      |
| employed_since                  | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> format: YYYY-MM-DD         | Employed since date                                        |
| employer                        | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> free text                  | Employer                                                   |
| employer_phone                  | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> must be valid phone number | Employer’s phone number                                    |
| current_job_position            | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> free text                  | Current job position                                       |
| tax_id_number                   | Required when occupation == ECONOMIC_ACTIVITY <br /> must be valid tax id number                                                                    | Tax ID number                                              |
| business_registration_date      | Required when occupation == ECONOMIC_ACTIVITY <br /> format: YYYY-MM-DD                                                                             | Business registration date                                 |
| university_name                 | Required when occupation == STUDENT <br /> free text                                                                                                | University name                                            |
| university_city                 | Required when occupation == STUDENT <br /> free text                                                                                                | university city                                            |
| car                             | Required <br /> boolean <br /> defaults to false                                                                                                          | Does customer own a car?                                   |
| salary_to_personal_account      | Required <br /> boolean <br /> defaults to false                                                                                                          | Does customer receive salary on their bank account?        |
| signature                       | Required <br /> Object                                                                                                                              | Request signature                                          |
| signature[timestamp]            | Required <br /> unix timestamp – must be UTC +/- 60 seconds                                                                                         | Timestamp when request is made                             |
| signature[api_key]              | Required                                                                                                                                      | Api key                                                    |
| signature[hash]                 | Required                                                                                                                                      | A sha1 concatenation of timestamp, api key and secret key  |

### JSON examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |

Body:
```json
# TODO
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```json
# TODO
```

**Failed response for /v3/customers**

Status code: 422

Response:
```json
[
   {
      "field":"email",
      "message":"Email no puede estar vacío."
   },
   {
      "field":"address.street",
      "message":"Dirección no puede estar vacío."
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
# TODO
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```xml
# TODO
```

**Failed response for /v3/customers**

Status code: 422

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <item>
        <field>personal_id</field>
        <message>Personal ID cannot be blank.</message>
    </item>
    <item>
        <field>address.postal_code</field>
        <message>Postal code cannot be blank.</message>
    </item>
</response>
```

### POST /v3/leads?customer={customer_uuid} *

Creates lead for existing customer.

* Replace customer_uuid placeholder with received uuid value from POST /v3/customers response.

| Field                | Validations                                | Description                                             |
|----------------------|--------------------------------------------|---------------------------------------------------------|
| loan_sum             | Required; Must be valid number             | Amount of credit requested by customer                  |
| loan_period          | Required; Must be valid number             | Term for credit requested                               |
| ip_address           | Must be valid IP address                   | IP address of customer                                  |
| aff_lead_id          |                                            | Your lead ID                                            |
| signature            | Required; object; contains signature fields| request signature                                       |
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
      "field":"loan_sum",
      "message":"Loan Sum cannot be blank."
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

# Addendum A - ENUMs

| marital_status |              |                           |
|----------------|--------------|---------------------------|
| value          | English      | Polish                    |
| single         | Single       | Wolna/wolny               |
| married        | Married      | Mężatka/żonaty            |
| divorced       | Divorced     | Rozwiedziony/rozwiedziona |
| with_partner   | With partner | W związku partnerskim     |
| widow          | Widow        | Wdowa/wdowiec             |

| education         |                   |                     |
|-------------------|-------------------|---------------------|
| value             | English           | Polish              |
| NO_EDUCATION      | No education      | Brak                |
| BASIC_SCHOOL      | Basic school      | Gimnazjalne         |
| HIGH_SCHOOL       | High school       | Średnie             |
| BACHELOR          | Bachelor’s degree | Wyższe licencjackie |
| MASTER            | Master            | Wyższe magisterskie |
| PHD               | Ph. D             | Wyższe doktorskie   |
| INDUSTRIAL_SCHOOL | Industrial school | Zawodowe            |

| housing_type              |                        |                                |
|---------------------------|------------------------|--------------------------------|
| value                     | English                | Polish                         |
| RENTED_ROOM               | Rented room            | Wynajęty pokój                 |
| RENTED_APARTMENT_OR_HOUSE | Rented apartment/house | Wynajęte mieszkanie lub dom    |
| OWN_HOUSE_OR_APARTMENT    | Own apartment/house    | Właściciel domu lub mieszkania |
| WITH_PARENTS              | Living with parents    | Mieszkający z rodzicami        |

| occupation                 |                               |                                    |
|----------------------------|-------------------------------|------------------------------------|
| value                      | English                       | Polish                             |
| employed_indefinite_period | Employed indefinitely         | Umowa o pracę na czas nieokreślony |
| employed_specified_period  | Employed for specified period | Umowa o pracę na czas określony    |
| written_contract_or_order  | Written contract or order     | Umowa zlecenie/Umowa o dzieło      |
| economic_activity          | Economic activity             | Działalność gospodarcza            |
| student                    | Student                       | Student                            |
| maternity_leave            | Materniry leave               | Gospodyni domowa                   |
| pensioner1                 | Pensioner                     | Emeryt/Rencista                    |
| benefits                   | Benefits                      | Świadczenie społeczne              |
| farmer                     | Farmer                        | Rolnik                             |
| unemployed                 | Unemployed                    | Bezrobotny                         |
| other                      | Other                         | Inna                               |