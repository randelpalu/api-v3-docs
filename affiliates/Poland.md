![Logo](../assets/img/logo.png?raw=true)

# Introduction 

Credy is a company that specializes on the quality lead generation. 
This document further describes how to integrate API to send leads to Credy.
Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Production URL: [https://api.credy.eu/v3/](https://api.credy.eu/v3/)

Staging URL: [http://api.staging.credy.eu/v3/](http://api.staging.credy.eu/v3/)

API supports post fields, XML and JSON.

To get response error messages in polish, "Accept-Language" header’s value must be set to “pl”.

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

| Field                           | Validations                                                                                                                                   | Description                                                |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------|
| first_name                      | Required                                                                                                                                      | First name of customer                                     |
| last_name                       | Required                                                                                                                                      | Last name of the customer                                  |
| email                           | Required <br /> valid email address                                                                                                                 | Email address                                              |
| personal_id                     | Required <br /> must be valid PESEL                                                                                                                 | PESEL                                                      |
| bank_account                    | Optional <br /> must be valid IBAN                                                                                                                  | Bank account                                               |
| phone                           | Required <br /> must be valid phone number                                                                                                          | Customer mobile phone number                               |
| id_card_number                  | Required <br /> must be valid ID card number                                                                                                        | ID card number                                             |
| occupation                      | Required <br /> for allowed values see [Addendum A](#addendum-a---enums)                                                                                                   | Occupation                                                 |
| housing_type                    | Required <br /> for allowed values see [Addendum A](#addendum-a---enums)                                                                                                   | Housing type                                               |
| marital_status                  | Required <br /> for allowed values see [Addendum A](#addendum-a---enums)                                                                                                   | Marital status                                             |
| education                       | Required <br /> for allowed values see [Addendum A](#addendum-a---enums)                                                                                                   | Education                                                  |
| neto_income                     | Required <br /> numeric                                                                                                                             | Net income                                                 |
| monthly_expenses                | Required <br /> numeric                                                                                                                             | Monthly expenses                                           |
| address                         | Required <br /> Object                                                                                                                              | Real address of the customer                               |
| address[city]                   | Required <br /> free text                                                                                                                           | City                                                       |
| address[street]                 | Required <br /> free text                                                                                                                           | Street                                                     |
| address[house_number]           | Required <br /> integer                                                                                                                             | House number                                               |
| address[flat_number]            | Optional <br /> must match regex: `/^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u`                                                                                        | Flat number                                                |
| address[postal_code]           | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| lives_at_registered_address     | Optional <br /> boolean; defaults to true                                                                                                           | Is customer’s real address the same as registered address? |
| secondary_address               | Required <br /> Object                                                                                                                              | Registered address of the customer                         |
| secondary_address[city]         | Required <br /> free text                                                                                                                           | City                                                       |
| secondary_address[street]       | Required <br /> free text                                                                                                                           | Street                                                     |
| secondary_address[house_number] | Required <br /> integer                                                                                                                             | House number                                               |
| secondary_address[flat_number]  | Optional <br /> must match regex: `/^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u`                                                                                        | Flat number                                                |
| secondary_address[postal_code] | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| employer_address                | Required when occupation in list: EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER                            | Employer’s address                                         |
| employer_address[city]          | Required <br /> free text                                                                                                                           | City                                                       |
| employer_address[street]        | Required <br /> free text                                                                                                                           | Street                                                     |
| employer_address[house_number]  | Required <br /> integer                                                                                                                             | House number                                               |
| employer_address[flat_number]   | Optional <br /> must match regex: `/^[0-9]+[A-ZĄĆĘŁŃÓŚŹŻ]?$/u`                                                                                        | Flat number                                                |
| employer_address[postal_code]  | Required <br /> format: #####                                                                                                                       | Postal index                                               |
| remuneration_deadline           | Required when occupation != UNEMPLOYED; format: YYYY-MM-DD                                                                                    | Remuneration deadline                                      |
| employed_since                  | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> format: YYYY-MM-DD         | Employed since date                                        |
| employer                        | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> free text                  | Employer                                                   |
| employer_phone                  | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> must be valid phone number | Employer’s phone number                                    |
| current_job_position            | Required when occupation in list EMPLOYED_INDEFINITE_PERIOD, EMPLOYED_SPECIFIED_PERIOD, WRITTEN_CONTRACT_OR_ORDER <br /> free text                  | Current job position                                       |
| tax_id_number                   | Required when occupation == ECONOMIC_ACTIVITY <br /> must be valid tax id number                                                                    | Tax ID number                                              |
| business_registration_date      | Required when occupation == ECONOMIC_ACTIVITY <br /> format: YYYY-MM-DD                                                                             | Business registration date                                 |
| university_name                 | Required when occupation == STUDENT <br /> free text                                                                                                | University name                                            |
| university_city                 | Required when occupation == STUDENT <br /> free text                                                                                                | university city                                            |
| car                             | Required <br /> for allowed values see [Addendum A](#addendum-a---enums) <br />                                                                                                          | Does customer own a car?                                   |
| bad_credit_history              | Required <br /> boolean <br /> defaults to false                                                                                                          | Does customer have bad credit history?                                  |
| salary_to_personal_account      | Required <br /> boolean <br /> defaults to false                                                                                                          | Does customer receive salary on their bank account?        |
| dependant_count                 | Required <br /> integer                                                                                                                   | Dependant count        |
| signature                       | Required <br /> Object                                                                                                                              | Request signature                                          |
| signature[timestamp]            | Required <br /> unix timestamp – must be UTC +/- 60 seconds                                                                                         | Timestamp when request is made                             |
| signature[api_key]              | Required                                                                                                                                      | Api key                                                    |
| signature[hash]                 | Required                                                                                                                                      | A sha1 concatenation of timestamp, api key and secret key  |
| agreements.terms_of_service     | Required <br /> Integer [1 - Yes, 0 - No]                                                                                                           | Does customer agree with terms of service?                 |
| agreements.data_proccessing_policy | Required <br /> Integer [1 - Yes, 0 - No]                                                                                                        | Does customer agree with data processing policy             |         


### JSON examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |

Body:
```json
{  
   "business_registration_date":"1994-11-25",
   "dependant_count":"1",
   "education":"MASTER",
   "employed_since":"2009-05-21",
   "housing_type":"RENTED_APARTMENT_OR_HOUSE",
   "id_card_number":"ATM052480",
   "lives_at_registered_address":"1",
   "marital_status":"MARRIED",
   "monthly_expenses":"901",
   "tax_id_number":"3309963272",
   "address": {
      "street":"Reymonta W\u0142adys\u0142awa Stanis\u0142awa",
      "city":"G\u0142og\u00f3w",
      "house_number":"94",
      "postal_code":"92139"
   },
   "bank_account":"PL08398211145244305277071135",
   "current_job_position":"Businessman",
   "email":"adamski.andrzej@mailinator.com",
   "employer":"Traffic Control",
   "employer_phone":"881001660",
   "first_name":"Rozalia",
   "last_name":"Pawlak",
   "neto_income":"1134",
   "occupation":"ECONOMIC_ACTIVITY",
   "personal_id":"73090303632",
   "phone":"881040331",
   "remuneration_deadline":"2017-11-19",
   "signature":{  
      "api_key":"poland",
      "timestamp":1510256918,
      "hash":"1f9158037d701039236cedc1b6efcd74bd0dc1cb"
   },
   "agreements.terms_of_service":"1",
   "agreements.data_proccessing_policy":"1"
}
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```json
{
    "uuid": "c35c40b7-7fba-4dad-bc40-e81652c46c7c",
    "first_name": "rozalia",
    "last_name": "pawlak",
    "email": "adamski.andrzej@mailinator.com",
    "product": "PAYDAY",
    "address_flat_number": "",
    "employer_flat_number": "",
    "secondaryAddress_flat_number": "",
    "personal_id": "73090303632",
    "phone": "+48881040331",
    "carBrand": null,
    "carModel": null,
    "carYear": null,
    "employer_street": "",
    "employer_postal_index": "",
    "employer_house_number": "",
    "phone2": null,
    "employer_phone": "+48881001660",
    "dependant_count": "1",
    "monthly_expenses": "901",
    "neto_income": "1134",
    "lives_at_registered_address": "1",
    "phone_confirmed": 0,
    "bad_credit_history": null,
    "salary_to_personal_account": null,
    "car": "NO",
    "ruian": null,
    "occupation": "ECONOMIC_ACTIVITY",
    "marital_status": "MARRIED",
    "housing_type": "RENTED_APARTMENT_OR_HOUSE",
    "gender": "MALE",
    "education": "MASTER",
    "industry": "",
    "remuneration_frequency": "",
    "bank_account": "PL08398211145244305277071135",
    "address_postal_index": "92139",
    "birth_date": "1973-09-03",
    "remuneration_deadline": "2017-11-19",
    "employed_since": "2009-05-21",
    "address_city": "Głogów",
    "address_address": "Reymonta Władysława Stanisława",
    "address_house_number": "94",
    "secondaryAddress_city": "",
    "secondaryAddress_address": "",
    "secondaryAddress_house_number": "",
    "secondaryAddress_postal_index": "",
    "employer": "Traffic Control",
    "current_job_position": "Businessman",
    "employer_work_city": "",
    "tax_id_number": "3309963272",
    "id_card_number": "ATM052480",
    "business_registration_date": "1994-11-25",
    "employment_sector": "",
    "address": {
        "street":"Reymonta W\u0142adys\u0142awa Stanis\u0142awa",
        "city":"G\u0142og\u00f3w",
        "house_number":"94",
        "postal_code":"92139"
    },
    "secondary_address": {
        "street":"",
        "city":"",
        "house_number":"",
        "postal_code":""
    },
    "employer_address": {
        "street":"",
        "city":"",
        "house_number":"",
        "postal_code":""
    }
}
```

**Failed response for /v3/customers**

Status code: 422

Response:
```json
[
   {
      "field":"email",
      "message":"Email is not valid."
   },
   {
      "field":"address.street",
      "message":"Street is not valid."
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
    <business_registration_date>1994-11-25</business_registration_date>
    <dependant_count>1</dependant_count>
    <education>MASTER</education>
    <employed_since>2009-05-21</employed_since>
    <housing_type>RENTED_APARTMENT_OR_HOUSE</housing_type>
    <id_card_number>ATM052480</id_card_number>
    <lives_at_registered_address>1</lives_at_registered_address>
    <marital_status>MARRIED</marital_status>
    <monthly_expenses>901</monthly_expenses>
    <tax_id_number>3309963272</tax_id_number>
    <address>
        <street>Reymonta Władysława Stanisława</street>
        <city>Głogów</city>
        <house_number>94</house_number>
        <postal_code>92139</postal_code>
    </address>
    <bank_account>PL08398211145244305277071135</bank_account>
    <current_job_position>Businessman</current_job_position>
    <email>adamski.andrzej@mailinator.com</email>
    <employer>Traffic Control</employer>
    <employer_phone>881001660</employer_phone>
    <first_name>Rozalia</first_name>
    <last_name>Pawlak</last_name>
    <neto_income>1134</neto_income>
    <occupation>ECONOMIC_ACTIVITY</occupation>
    <personal_id>73090303632</personal_id>
    <phone>881040331</phone>
    <remuneration_deadline>2017-11-19</remuneration_deadline>
    <signature>
        <api_key>poland</api_key>
        <timestamp>1510256918</timestamp>
        <hash>1f9158037d701039236cedc1b6efcd74bd0dc1cb</hash>
    </signature>
    <agreements.terms_of_service>1</agreements.terms_of_service>
    <agreements.data_proccessing_policy>1</agreements.data_proccessing_policy>
</request>
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<response>
	<uuid>c35c40b7-7fba-4dad-bc40-e81652c46c7c</uuid>
	<first_name>rozalia</first_name>
	<last_name>pawlak</last_name>
	<email>adamski.andrzej@mailinator.com</email>
	<product>PAYDAY</product>
	<address_flat_number></address_flat_number>
	<employer_flat_number></employer_flat_number>
	<secondaryAddress_flat_number></secondaryAddress_flat_number>
	<personal_id>73090303632</personal_id>
	<phone>+48881040331</phone>
	<carBrand />
	<carModel />
	<carYear />
	<employer_street></employer_street>
	<employer_postal_index></employer_postal_index>
	<employer_house_number></employer_house_number>
	<phone2 />
	<employer_phone>+48881001660</employer_phone>
	<dependant_count>1</dependant_count>
	<monthly_expenses>901</monthly_expenses>
	<neto_income>1134</neto_income>
	<lives_at_registered_address>1</lives_at_registered_address>
	<phone_confirmed>0</phone_confirmed>
	<bad_credit_history />
	<salary_to_personal_account />
	<car>NO</car>
	<ruian />
	<occupation>ECONOMIC_ACTIVITY</occupation>
	<marital_status>MARRIED</marital_status>
	<housing_type>RENTED_APARTMENT_OR_HOUSE</housing_type>
	<gender>MALE</gender>
	<education>MASTER</education>
	<industry></industry>
	<remuneration_frequency></remuneration_frequency>
	<bank_account>PL08398211145244305277071135</bank_account>
	<address_postal_index>92139</address_postal_index>
	<birth_date>1973-09-03</birth_date>
	<remuneration_deadline>2017-11-19</remuneration_deadline>
	<employed_since>2009-05-21</employed_since>
	<address_city>Głogów</address_city>
	<address_address>Reymonta Władysława Stanisława</address_address>
	<address_house_number>94</address_house_number>
	<secondaryAddress_city></secondaryAddress_city>
	<secondaryAddress_address></secondaryAddress_address>
	<secondaryAddress_house_number></secondaryAddress_house_number>
	<secondaryAddress_postal_index></secondaryAddress_postal_index>
	<employer>Traffic Control</employer>
	<current_job_position>Businessman</current_job_position>
	<employer_work_city></employer_work_city>
	<tax_id_number>3309963272</tax_id_number>
	<id_card_number>ATM052480</id_card_number>
	<business_registration_date>1994-11-25</business_registration_date>
	<employment_sector></employment_sector>
        <address>
            <street>Reymonta Władysława Stanisława</street>
            <city>Głogów</city>
            <house_number>94</house_number>
            <postal_code>9213</postal_code>
        </address>
        <secondary_address>
            <street></street>
            <city></city>
            <house_number></house_number>
            <postal_code></postal_code>
        </secondary_address>
        <employer_address>
            <street></street>
            <city></city>
            <house_number></house_number>
            <postal_code></postal_code>
        </employer_address>
</response>
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
| loan_sum             | Required <br /> Must be valid number       | Amount of credit requested by customer                  |
| loan_period          | Required <br /> Must be valid number       | Term for credit requested                               |
| ip_address           | Must be valid IP address                   | IP address of customer                                  |
| aff_lead_id          | Optional <br /> free text                  | Your lead ID                                            |
| signature            | Required <br /> object <br /> contains signature fields| request signature                                       |
| signature[timestamp] | Required                                   | unix timestamp, must be UTC +/- 60 seconds              |
| signature[api_key]   | Required                                   | api key                                                 |
| signature[hash]      | Required                                   | sha1 concatenation of timestamp, api key and secret key |


### JSON examples for /v3/leads

**POST /v3/leads?customer=c35c40b7-7fba-4dad-bc40-e81652c46c7c**

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


**POST /v3/leads?customer=c35c40b7-7fba-4dad-bc40-e81652c46c7c**

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

### marital_status

| value          | English      | Polish                    |
|----------------|--------------|---------------------------|
| single         | Single       | Wolna/wolny               |
| married        | Married      | Mężatka/żonaty            |
| divorced       | Divorced     | Rozwiedziony/rozwiedziona |
| with_partner   | With partner | W związku partnerskim     |
| widow          | Widow        | Wdowa/wdowiec             |

### education

| value             | English           | Polish              |
|-------------------|-------------------|---------------------|
| NO_EDUCATION      | No education      | Brak                |
| BASIC_SCHOOL      | Basic school      | Gimnazjalne         |
| HIGH_SCHOOL       | High school       | Średnie             |
| BACHELOR          | Bachelor’s degree | Wyższe licencjackie |
| MASTER            | Master            | Wyższe magisterskie |
| PHD               | Ph. D             | Wyższe doktorskie   |
| INDUSTRIAL_SCHOOL | Industrial school | Zawodowe            |

### housing_type

| value                     | English                | Polish                         |
|---------------------------|------------------------|--------------------------------|
| RENTED_ROOM               | Rented room            | Wynajęty pokój                 |
| RENTED_APARTMENT_OR_HOUSE | Rented apartment/house | Wynajęte mieszkanie lub dom    |
| OWN_HOUSE_OR_APARTMENT    | Own apartment/house    | Właściciel domu lub mieszkania |
| WITH_PARENTS              | Living with parents    | Mieszkający z rodzicami        |

### occupation

| value                      | English                       | Polish                             |
|----------------------------|-------------------------------|------------------------------------|
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
### car

| value | English | Polish |
|-------|---------|--------|
| YES   | Yes     | Tak    |
| NO    | No      | Nie    |

# Versions

- 1.0 (2017-11-09): first publish
- 1.0.1 (2017-12-22): fixed address fields
- 1.0.2 (2018-03-15): fix car field boolean -> enum