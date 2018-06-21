![Logo](../assets/img/logo.png?raw=true)

# Introduction 

Credy is a company that specializes on the quality lead generation. 
This document further describes how to integrate API to send leads to Credy.
Should you have any questions, please do not hesitate to contact the IT manager at [it@credy.eu](mailto:it@credy.eu).

## General information

Production URL: [https://api.credy.eu/v3/](https://api.credy.eu/v3/)

Staging URL: [http://api.staging.credy.eu/v3/](http://api.staging.credy.eu/v3/)

API supports post fields, XML and JSON.

To get response error messages in spain, "Accept-Language" header’s value must be set to according ISO code. Ex: “es” or “es-MX“.

## Signing requests

Each request must be signed. Signature consists of api_key, timestamp and hash.

Hash is a SHA1 hash of the concatenation of timestamp, api_key and secret_key.

Allowed deviation for timestamp is UTC +/- 60 seconds.

*ex. sha1(timestamp + apiKey + secretKey)*

Api key and secret key will be assigned to you by Credy.

## Lead registration flow

1. Request is sent to /v3/customers

2. If request is successful, customer uuid must be stored

3. Request is sent to /v3/leads with previously stored customer uuid in query parameter "customer" *ex. /v3/leads?customer=a3ff7c20-e9e8-43a0-bfb7-71a5cf43b8d3*

# API methods

### POST /v3/customers

This method will create or update customer and return customer uuid.

| Field | Validations | Description |
| ----- | ----------- | ----------- |
| first_name | Required | First name of customer |
| last_name | Required | Faternal last name of customer |
| second_last_name | Required | Maternal last name of customer |
| personal_id | Required<br> Must match regex listed in: [Addendum B](#addendum-b---regexps) | Personal ID of customer |
| tax_id_number | Required<br> Must match regex listed in: [Addendum B](#addendum-b---regexps) | Customer RFC number |
| bank_account | Required<br> Must be valid CLABE | Bank account number of customer |
| email | Required | Email address |
| phone | Required<br>Must be valid phone number | Customer mobile phone number |
| phone2 | Required<br>Must be valid phone number | Customer landline phone number |
| address | Required<br> Object | Contains registered address data |
| address[postal_code] | Required<br> Must match regex: ` /^\d{5}$/ `  | Postal code |
| address[region] | Required | State |
| address[county] | Required | County/Municipality |
| address[city] | Required | City |
| address[district] | Required | District/Colony |
| address[street] | Required | Street |
| address[house_number] | Required | House number |
| address[flat_number] | Optional | Flat number |
| neto_income | Required | Neto income in MXN |
| occupation | Required | Occupation type <br> For allowed values see [Addendum A](#addendum-a---enums) |
| employer | Required when occupation is one of following:<br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER<br> | Employer |
| employer_phone |Must be valid phone number<br> Required when occupation is one of following: <br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER | Employers phone number |
| employed_since |Must be valid date<br> Required when occupation is one of following: <br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER | Employed since date |
| employment_place | Required when occupation is one of following: <br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER <br><br> For possible values see [Addendum A](#addendum-a---enums) | Employment place |
| current_job_position | Required when occupation is one of following:<br>EMPLOYED_INDEFINITE_PERIOD<br>EMPLOYED_SPECIFIED_PERIOD<br> WRITTEN_CONTRACT_OR_ORDER<br> | Current job position |
| credit_card_verification | Optional <br> Must match regex: ` /^\d{4}$/ ` | Last 4 digits of credit card number, if customer has credit card number |
| has_mortgage | Required<br>Boolean | Does customer have a mortgage loan |
| has_carloan | Required<br>Boolean | Does customer have a car loan |
| nationality | Required <br><br> For possible values see [Addendum A](#addendum-a---enums) | Customer nationality | 
| signature | Required <br> Object | request signature |
| signature[timestamp] | Required <br> unix timestamp<br> must be UTC +/- 60 seconds | unix timestamp, must be UTC +/- 60 seconds |
| signature[api_key] | Required | api key |
| signature[hash] | Required | sha1 concatenation of timestamp, api key and secret key |

### JSON examples for /v3/customers

**POST /v3/customers**

| Headers      |                  |
|--------------|------------------|
| Content-Type | application/json |
| Accept       | application/json |

Body:
```json
{
   "first_name":"Aureliano",
   "last_name":"Orozco",
   "second_last_name":"Pacana",
   "email":"miranda.doedoe@mailinator.com",
   "personal_id":"AEDK661027HNEGLLT9",
   "employed_since": "2017-01-08",
   "bank_account":"141807225454240832",
   "phone":"01 449 329 1020",
   "phone2":"01 449 581 5915",
   "neto_income":960,
   "address":{
      "city":"Doe Doestad",
      "street":"Escarlata Haven",
      "house_number":"5052",
      "flat_number":3,
      "postal_code":91648,
      "region":"Quintana Roo",
      "county":"test",
      "district":"test"
   },
   "occupation":"WRITTEN_CONTRACT_OR_ORDER",
   "employer_phone":"687 493 9638",
   "tax_id_number":"AAMC160222CNX",
   "has_carloan":true,
   "has_mortgage":true,
   "credit_card_verification":3368,
   "employer":"Doe Doe-Doe Doe",
   "current_job_position":"Gas Plant Operator",
   "employment_place":"HOME",
   "nationality":"Afganistán",
   "signature":{
      "timestamp":1491466495,
      "api_key":"mexico",
      "hash":"8a77fcd5f495b6d3a9c01fb11a4e9ce10078442e"
   }
}
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```json
{
   "uuid":"7b1be1e9-3c5f-578c-8e44-20af1a6de4b8",
   "product":"PAYDAY",
   "first_name":"Aureliano",
   "last_name":"Orozco",
   "email":"miranda.doedoe@mailinator.com",
   "personal_id":"AEDK661027HNEGLLT9",
   "phone":"+524493291020",
   "phone2":"+524493291020",
   "employer_phone":"+526874939638",
   "neto_income":"960",
   "lives_at_registered_address":1,
   "occupation":"WRITTEN_CONTRACT_OR_ORDER",
   "bank_account":"141807225454240832",
   "employed_since": "2017-01-08",
   "employer":"Doe Doe-Doe Doe",
   "current_job_position":"Gas Plant Operator",
   "second_last_name":"Pacana",
   "tax_id_number":"AAMC160222CNX",
   "has_carloan":"1",
   "has_mortgage":"1",
   "credit_card_verification":3368,
   "employment_place":"HOME",
   "nationality":"Afganistán",
   "address":{
      "region":"Quintana Roo",
      "county":"test",
      "district":"test",
      "city":"Doe Doestad",
      "street":"Escarlata Haven",
      "house_number":"5052",
      "flat_number":"3",
      "postal_code":"91648"
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
<?xml version="1.0" encoding="UTF-8" ?>
<request>
    <first_name>Aureliano</first_name>
    <second_first_name>Gandharva</second_first_name>
    <last_name>Orozco</last_name>
    <email>miranda.doedoe@mailinator.com</email>
    <personal_id>AEDK661027HNEGLLT9</personal_id>
    <bank_account>141807225454240832</bank_account>
    <phone>01 449 329 1020</phone>
    <phone2>01 449 581 5915</phone>
    <neto_income>60</neto_income>
    <employed_since>2017-01-08</employed_since>
    <address>
        <city>Doe Doestad</city>
        <street>Escarlata Haven</street>
        <house_number>5052</house_number>
        <flat_number>3</flat_number>
        <postal_code>16485</postal_code>
        <region>Quintana Roo</region>
        <county>test</county>
        <district>test</district>
    </address>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <employer_phone>687 493 9638</employer_phone>
    <tax_id_number>AAMC160222CNX</tax_id_number>
    <has_carloan>1</has_carloan>
    <has_mortgage>1</has_mortgage>
    <credit_card_verification>6368</credit_card_verification>
    <employer>Doe Doe-Doe Doe</employer>
    <current_job_position>Gas Plant Operator</current_job_position>
    <employment_place>HOME</employment_place>
    <nationality>Afganistán</nationality>
    <signature>
        <timestamp>491466495</timestamp>
        <api_key>mexico</api_key>
        <hash>8a77fcd5f495b6d3a9c01fb11a4e9ce10078442e</hash>
    </signature>
</request>
```

**Successful response for /v3/customers**

Status code: 2xx

Response:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<response>
    <uuid>7b1be1e9-3c5f-578c-8e44-20af1a6de4b8</uuid>
    <product>PAYDAY</product>
    <first_name>Aureliano</first_name>
    <last_name>Orozco</last_name>
    <email>miranda.doedoe@mailinator.com</email>
    <personal_id>AEDK661027HNEGLLT9</personal_id>
    <phone>+524493291020</phone>
    <phone2>+524493291020</phone2>
    <employer_phone>+526874939638</employer_phone>
    <neto_income>60</neto_income>
    <employed_since>2017-01-08</employed_since>
    <occupation>WRITTEN_CONTRACT_OR_ORDER</occupation>
    <bank_account>141807225454240832</bank_account>
    <employer>Doe Doe-Doe Doe</employer>
    <current_job_position>Gas Plant Operator</current_job_position>
    <second_last_name>Pacana</second_last_name>
    <tax_id_number>AAMC160222CNX</tax_id_number>
    <has_carloan>1</has_carloan>
    <has_mortgage>1</has_mortgage>
    <credit_card_verification>6368</credit_card_verification>
    <nationality>Afganistán</nationality>
    <address>
        <region>Quintana Roo</region>
        <county>test</county>
        <district>test</district>
        <city>Doe Doestad</city>
        <street>Escarlata Haven</street>
        <house_number>5052</house_number>
        <flat_number>3</flat_number>
        <postal_code>16485</postal_code>
    </address>
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
| loan_sum             | Required Must be valid number              | Amount of credit requested by customer                  |
| loan_period          | Required Must be valid number              | Term for credit requested                               |
| ip_address           | Must be valid IP address                   | IP address of customer                                  |
| notes                | Required, for possible values see [Addendum A](#addendum-a---enums)                   | Reason for the loan                                  |
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
    "notes":"SCHOOL_EXPENSES",
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
	"notes":"SCHOOL_EXPENSES",
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
	<notes>SCHOOL_EXPENSES</notes>
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

| occupation                 |                                   |                                 |
|----------------------------|-----------------------------------|---------------------------------|
| value                      | English                           | Mexican                     |
EMPLOYED_SPECIFIED_PERIOD | Employed: specified time | Empleado por periodo especifico |
EMPLOYED_INDEFINITE_PERIOD | Employed: indefinite time | Empleado por periodo indefinido |
WRITTEN_CONTRACT_OR_ORDER | Written contract or order | Contrato u orden escrita |
SELF_EMPLOYED | Self employed (does not own business) | Autoempleado |
PENSIONER1 | Pensioner | Pensionado |
STUDENT | Student | Estudiante |
UNEMPLOYED | Unemployed | Desempleado |
FREELANCER | Freelancer | Freelancer |
OWN_BUSINESS | Own business (economic activity) | Negocio propio |
MATERNITY_LEAVE | Maternity leave | Ama de casa |


| employment_place                 |                                   |                                 |
|----------------------------|-----------------------------------|---------------------------------|
| value                      | English                           | Mexican                     |
OFFICE | Office | Oficina |
FACTORY | Factory | Fábrica |
HOME | Home | Hogar |
STREET_STALL | Street stall | Puesto callejero |
DOOR_TO_DOOR | Door to door | Puerta a puerta |
ON_THE_STREET | On the street | En la calle |
FIXED_PREMISES | Fixed premises | Lugar fijo |


| notes                 |                                   |                                 |
|----------------------------|-----------------------------------|---------------------------------|
| value                      | English                           | Mexican                     |
PURCHASE_OF_FOOD | Food | Alimentos |
BUYING_ELECTRONICS | Electronics | Articulos Electronicos |
CREATE_CREDIT_HISTORY | To build credit history | Crear historial crediticio |
SCHOOL_EXPENSES | School | Escuela |
UNFORESEEN_EXPENSE | Unexpected spendings | Gastos inesperados |
MEDICAL_EXPENSES | Medical expenses | Gastos médicos |
MAINTENANCE_OR_REPAIR_OF_HOUSE | House repair | Reparaciones del hogar |
PAY_ANOTHER_LOAN | To pay another loan | Pagar otro préstamos |
PAYMENT_OF_SERVICES | To pay for services | Pagar servicios |
FOR_MY_BUSINESS | For private busness | Para Negocio personal |
TRAVEL | Travelling | Viajar |
OTHER | Other | Otro |

| nationality | | |
|----------------------------------|-----------------------------------|----------------------------------|
| value                            | English                           | Mexican                          |
| Afganistán                       | Afghanistan                       | Afganistán                       |
| Albania                          | Albania                           | Albania                          |
| Alemania                         | Germany                           | Alemania                         |
| Andorra                          | Andorra                           | Andorra                          |
| Angola                           | Angola                            | Angola                           |
| Anguila                          | Eel                               | Anguila                          |
| Antigua y Barbuda                | Old and bearded                   | Antigua y Barbuda                |
| Arabia Saudita                   | Saudi Arabia                      | Arabia Saudita                   |
| Argelia                          | Algeria                           | Argelia                          |
| Argentina                        | Argentina                         | Argentina                        |
| Armenia                          | Armenia                           | Armenia                          |
| Australia                        | Australia                         | Australia                        |
| Austria                          | Austria                           | Austria                          |
| Azerbaiyán                       | Azerbaijan                        | Azerbaiyán                       |
| Bahamas                          | Bahamas                           | Bahamas                          |
| Baréin                           | Bahrain                           | Baréin                           |
| Bangladés                        | Bangladesh                        | Bangladés                        |
| Barbados                         | Barbados                          | Barbados                         |
| Bélgica                          | Belgium                           | Bélgica                          |
| Belice                           | Belize                            | Belice                           |
| Benín                            | Benin                             | Benín                            |
| Bielorrusia                      | Belarus                           | Bielorrusia                      |
| Birmania                         | Burma                             | Birmania                         |
| Bolivia                          | Bolivia                           | Bolivia                          |
| Bosnia y Herzegovina             | Bosnia and Herzegovina            | Bosnia y Herzegovina             |
| Botsuana                         | Botswana                          | Botsuana                         |
| Brasil                           | Brazil                            | Brasil                           |
| Brunéi                           | Brunei                            | Brunéi                           |
| Bulgaria                         | Bulgaria                          | Bulgaria                         |
| Burkina Faso                     | Burkina Faso                      | Burkina Faso                     |
| Burundi                          | Burundi                           | Burundi                          |
| Bután                            | Bhutan                            | Bután                            |
| Cabo Verde                       | Cape Verde                        | Cabo Verde                       |
| Camboya                          | Cambodia                          | Camboya                          |
| Camerún                          | Cameroon                          | Camerún                          |
| Canadá                           | Glen                              | Canadá                           |
| Catar                            | Taste                             | Catar                            |
| Centroafricana, República        | Central African Republic          | Centroafricana, República        |
| Chad                             | Chad                              | Chad                             |
| Checa, República                 | Czech Republic                    | Checa, República                 |
| Chile                            | Chile                             | Chile                            |
| China                            | China                             | China                            |
| Chipre                           | Cyprus                            | Chipre                           |
| Colombia                         | Colombia                          | Colombia                         |
| Comoras                          | Comoros                           | Comoras                          |
| Congo, República del             | Congo, Republic of                | Congo, República del             |
| Congo, República Democrática del | Congo, Democratic Republic of the | Congo, República Democrática del |
| Corea del Norte                  | North Korea                       | Corea del Norte                  |
| Corea del Sur                    | South Korea                       | Corea del Sur                    |
| Costa de Marfil                  | Ivory Coast                       | Costa de Marfil                  |
| Costa Rica                       | Costa Rica                        | Costa Rica                       |
| Croacia                          | Croatia                           | Croacia                          |
| Cuba                             | Cuba                              | Cuba                             |
| Dinamarca                        | Denmark                           | Dinamarca                        |
| Dominica                         | Dominica                          | Dominica                         |
| Dominicana, República            | Dominican Republic                | Dominicana, República            |
| Ecuador                          | Ecuador                           | Ecuador                          |
| Egipto                           | Egypt                             | Egipto                           |
| El Salvador                      | The Savior                        | El Salvador                      |
| Emiratos Árabes Unidos           | United Arab Emirates              | Emiratos Árabes Unidos           |
| Eritrea                          | Eritrea                           | Eritrea                          |
| Eslovaquia                       | Slovakia                          | Eslovaquia                       |
| Eslovenia                        | Slovenia                          | Eslovenia                        |
| España                           | Spain                             | España                           |
| Estados Unidos                   | U.S                               | Estados Unidos                   |
| Estonia                          | Estonia                           | Estonia                          |
| Etiopía                          | Ethiopia                          | Etiopía                          |
| Filipinas                        | Philippines                       | Filipinas                        |
| Finlandia                        | Finland                           | Finlandia                        |
| Fiyi                             | Fiji                              | Fiyi                             |
| Francia                          | France                            | Francia                          |
| Gabón                            | Gabon                             | Gabón                            |
| Gambia                           | Gambia                            | Gambia                           |
| Georgia                          | Georgia                           | Georgia                          |
| Ghana                            | Ghana                             | Ghana                            |
| Granada                          | Pomegranate                       | Granada                          |
| Grecia                           | Greece                            | Grecia                           |
| Guatemala                        | Guatemala                         | Guatemala                        |
| Guinea                           | Guinea                            | Guinea                           |
| Guinea-Bisáu                     | Guinea-Bissau                     | Guinea-Bisáu                     |
| Guinea Ecuatorial                | Equatorial Guinea                 | Guinea Ecuatorial                |
| Guyana                           | Guyana                            | Guyana                           |
| Haití                            | Haiti                             | Haití                            |
| Honduras                         | Honduras                          | Honduras                         |
| Hungría                          | Hungary                           | Hungría                          |
| India                            | India                             | India                            |
| Indonesia                        | Indonesia                         | Indonesia                        |
| Irak                             | Iraq                              | Irak                             |
| Irán                             | Iran                              | Irán                             |
| Irlanda                          | Ireland                           | Irlanda                          |
| Islandia                         | Iceland                           | Islandia                         |
| Israel                           | Israel                            | Israel                           |
| Italia                           | Italy                             | Italia                           |
| Jamaica                          | Jamaica                           | Jamaica                          |
| Japón                            | Japan                             | Japón                            |
| Jordania                         | Jordan                            | Jordania                         |
| Kazajistán                       | Kazakhstan                        | Kazajistán                       |
| Kenia                            | Kenya                             | Kenia                            |
| Kirguistán                       | Kyrgyzstan                        | Kirguistán                       |
| Kiribati                         | Kiribati                          | Kiribati                         |
| Kuwait                           | Kuwait                            | Kuwait                           |
| Laos                             | Laos                              | Laos                             |
| Lesoto                           | Lesotho                           | Lesoto                           |
| Letonia                          | Latvia                            | Letonia                          |
| Líbano                           | Lebanon                           | Líbano                           |
| Liberia                          | Liberia                           | Liberia                          |
| Libia                            | Libya                             | Libia                            |
| Liechtenstein                    | Liechtenstein                     | Liechtenstein                    |
| Lituania                         | Lithuania                         | Lituania                         |
| Luxemburgo                       | Luxembourg                        | Luxemburgo                       |
| Macedonia                        | Macedonia                         | Macedonia                        |
| Madagascar                       | Madagascar                        | Madagascar                       |
| Malasia                          | Malaysia                          | Malasia                          |
| Malaui                           | Malawi                            | Malaui                           |
| Maldivas                         | Maldives                          | Maldivas                         |
| Malí                             | Mali                              | Malí                             |
| Malta                            | malt                              | Malta                            |
| Marruecos                        | Morocco                           | Marruecos                        |
| Marshall, Islas                  | Marshall, Islands                 | Marshall, Islas                  |
| Mauricio                         | Mauricio                          | Mauricio                         |
| Mauritania                       | Mauritania                        | Mauritania                       |
| México                           | Mexico                            | México                           |
| Micronesia                       | Micronesia                        | Micronesia                       |
| Moldavia                         | Moldova                           | Moldavia                         |
| Mónaco                           | Monaco                            | Mónaco                           |
| Mongolia                         | Mongolia                          | Mongolia                         |
| Montenegro                       | Montenegro                        | Montenegro                       |
| Mozambique                       | Mozambique                        | Mozambique                       |
| Namibia                          | Namibia                           | Namibia                          |
| Nauru                            | Nauru                             | Nauru                            |
| Nepal                            | Nepal                             | Nepal                            |
| Nicaragua                        | Nicaragua                         | Nicaragua                        |
| Níger                            | Niger                             | Níger                            |
| Nigeria                          | Nigeria                           | Nigeria                          |
| Noruega                          | Norway                            | Noruega                          |
| Nueva Zelanda                    | New Zealand                       | Nueva Zelanda                    |
| Omán                             | Oman                              | Omán                             |
| Países Bajos                     | Netherlands                       | Países Bajos                     |
| Pakistán                         | Pakistan                          | Pakistán                         |
| Palaos                           | Palau                             | Palaos                           |
| Palestina                        | Palestine                         | Palestina                        |
| Panamá                           | Panama                            | Panamá                           |
| Papúa Nueva Guinea               | Papua New Guinea                  | Papúa Nueva Guinea               |
| Paraguay                         | Paraguay                          | Paraguay                         |
| Perú                             | Peru                              | Perú                             |
| Polonia                          | Poland                            | Polonia                          |
| Portugal                         | Portugal                          | Portugal                         |
| Puerto Rico                      | Puerto Rico                       | Puerto Rico                      |
| Reino Unido                      | United Kingdom                    | Reino Unido                      |
| Ruanda                           | Rwanda                            | Ruanda                           |
| Rumania                          | Romania                           | Rumania                          |
| Rusia                            | Russia                            | Rusia                            |
| Salomón, Islas                   | Solomon, Islands                  | Salomón, Islas                   |
| Samoa                            | Samoa                             | Samoa                            |
| San Cristóbal y Nieves           | Saint Kitts and Nevis             | San Cristóbal y Nieves           |
| San Marino                       | San Marino                        | San Marino                       |
| San Vicente y las Granadinas     | St. Vincent and the Grenadines    | San Vicente y las Granadinas     |
| Santa Lucía                      | St. lucia                         | Santa Lucía                      |
| Santo Tomé y Príncipe            | Sao Tome and Principe             | Santo Tomé y Príncipe            |
| Senegal                          | Senegal                           | Senegal                          |
| Serbia                           | Serbia                            | Serbia                           |
| Seychelles                       | Seychelles                        | Seychelles                       |
| Sierra Leona                     | Sierra Leone                      | Sierra Leona                     |
| Singapur                         | Singapore                         | Singapur                         |
| Siria                            | Syria                             | Siria                            |
| Somalia                          | Somalia                           | Somalia                          |
| Sri Lanka                        | Sri Lanka                         | Sri Lanka                        |
| Suazilandia                      | Swaziland                         | Suazilandia                      |
| Sudáfrica                        | South Africa                      | Sudáfrica                        |
| Sudán                            | Sudan                             | Sudán                            |
| Sudán del Sur                    | South Sudan                       | Sudán del Sur                    |
| Suecia                           | Sweden                            | Suecia                           |
| Suiza                            | Switzerland                       | Suiza                            |
| Surinam                          | Surinam                           | Surinam                          |
| Tailandia                        | Thailand                          | Tailandia                        |
| Taiwán                           | Taiwan                            | Taiwán                           |
| Tanzania                         | Tanzania                          | Tanzania                         |
| Tayikistán                       | Tajikistan                        | Tayikistán                       |
| Timor Oriental                   | East Timor                        | Timor Oriental                   |
| Togo                             | Togo                              | Togo                             |
| Tonga                            | Tonga                             | Tonga                            |
| Trinidad y Tobago                | Trinidad and Tobago               | Trinidad y Tobago                |
| Túnez                            | Tunisia                           | Túnez                            |
| Turkmenistán                     | Turkmenistan                      | Turkmenistán                     |
| Turquía                          | Turkey                            | Turquía                          |
| Tuvalu                           | Tuvalu                            | Tuvalu                           |
| Ucrania                          | Ukraine                           | Ucrania                          |
| Uganda                           | Uganda                            | Uganda                           |
| Uruguay                          | Uruguay                           | Uruguay                          |
| Uzbekistán                       | Uzbekistan                        | Uzbekistán                       |
| Vanuatu                          | Vanuatu                           | Vanuatu                          |
| Vaticano, Ciudad del             | Vatican, City of                  | Vaticano, Ciudad del             |
| Venezuela                        | Venezuela                         | Venezuela                        |
| Vietnam                          | Vietnam                           | Vietnam                          |
| Yemen                            | Yemen                             | Yemen                            |
| Yibuti                           | Djibouti                          | Yibuti                           |
| Zambia                           | Zambia                            | Zambia                           |
| Zimbabue                         | Zimbabwe                          | Zimbabue                         |

# Addendum B - Regexps

Personal ID:

```regexp
`/^[A-Z]{1}[AEIOUX]{1}[A-Z]{2}[0-9]{2}(0[1-9]|1[0-2])(0[1-9]|1[0-9]|2[0-9]|3[0-1])[HM]{1}(AS|BC|BS|CC|CS|CH|CL|CM|DF|DG|GT|GR|HG|JC|MC|MN|MS|NT|NL|OC|PL|QT|QR|SP|SL|SR|TC|TS|TL|VZ|YN|ZS|NE)[B-DF-HJ-NP-TV-Z]{3}[0-9A-Z]{1}[0-9]{1}$/`
```

Tax ID number:

```regexp
/^[A-Z]{1}[AEIOUX]{1}[A-Z]{2}[0-9]{2}(?:0[1-9]|1[0-2])(?:0[1-9]|1[0-9]|2[0-9]|3[0-1])[A-Za-z0-9]{3}$/
```

# Versions

- 1.0 (2018-05-14): first publish
- 1.1 (2018-06-21): add `nationality` field