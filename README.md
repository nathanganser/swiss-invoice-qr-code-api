
# Magic Heidi API Documentation

At Magic Heidi, we've been developing a cross platform invoicing solution for Switzerland. Our aim is to enable all Swiss IT providers and make it as easy as possible for everyone to generate invoices. 

We're opening up our API to allow anyone to generate valid Swiss invoices with QR codes. This document describes our API and provides some examples to help you get started. 

For any queries, please feel free to email us at hello@magicheidi.ch.

## API Quickstart

To use our API, send a POST request to the following URL:

```
https://europe-west6-magic-heidi.cloudfunctions.net/createAbstractInvoice
```

and pass a JSON with the following parameters:

```json
{
  "language": "en",
  "logo_url": "https://...",
  "file_name": "test.pdf",
  "invoice_number": 11,
  "invoice_date": "30/06/2022",
  "vat_enabled": true,
  "vat_percentage": 0.025,
  "vat_number": "CHE-kjkj",
  "user_details": {
    "name": "Nathan",
    "address": "Rue de la gare 1",
    "zip": "1000",
    "city": "Lausanne",
    "iban": "CH12 1234 1234 1234 1234 1"
  },
  "customer_details": {
    "name": "Nathan",
    "address": "Rue de la gare 1",
    "zip": "1000",
    "city": "Lausanne"
  },
  "invoice_items": [
    {
      "description": "Test",
      "quantity": 1.5,
      "unit_price": 100.50
    }
  ]
}
```

The response will be a JSON with a `result` value that contains the URL:

```json
{ "result": "https://.../file.pdf" }
```

### Generate an invoice without VAT

You can choose to ignore all VAT related parameters to generate an invoice without VAT. Here is an example:

```json
{
  "data": {
    "language": "fr",
    "file_name": "invoice.pdf",
    "invoice_number": 1,
    "invoice_date": "30/06/2022",
    "user_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne",
      "iban": "CH0700700112900411647"
    },
    "customer_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne"
    },
    "invoice_items": [
      {
        "description": "Test",
        "quantity": 1.5,
        "unit_price": 100.50
      }
    ]
  }
}
```

### Parameters Description

#### Mandatory Parameters

- `file_name` (string): The name of the file.
- `invoice_number` (integer): This is mentioned in the invoice.
- `invoice_date` (string): A date for the invoice in the format: 30/06/2022.
- `user_details` (string): Contains all the details of the user who is issuing the invoice; name, address, zip, city, iban.
- `customer_details` (string): Similar to user_details but without the iban. Contains all the information of the invoice recipient, the

 one who's paying the invoice: name, address, zip, city.
- `invoice_items` (array): A list of the invoice items, each invoice item is composed of a description (string), quantity (float), and unit_price (float).

#### Optional Parameters

- `language` (string): The language of the invoice. Can be fr, de, it, or en. Defaults to en if not specified.
- `logo_url` (string): A URL to the logo of the business who issued the invoice. Defaults to the Magic Heidi logo if not specified.

#### Optional VAT Parameters

- `vat_enabled` (boolean): True or false. If true, then the following two parameters are required.
- `vat_percentage` (float): Usually 0.077 (for most invoices) or 0.025 (for food products).
- `vat_number` (string): The VAT number of the user, "CHE-kjkj..." for example.

## Code Samples

### Python example without VAT

```python
import requests
import json

url = "https://europe-west6-magic-heidi.cloudfunctions.net/createAbstractInvoice"

payload = json.dumps({
  "data": {
    "uuid": "45345",
    "language": "fr",
    "file_name": "invoice.pdf",
    "invoice_date": "30/06/2022",
    "user_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne",
      "iban": "CH0700700112900411647"
    },
    "customer_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne"
    },
    "invoice_items": [
      {
        "description": "Test",
        "quantity": 1.5,
        "unit_price": 100.5
      }
    ]
  }
})
headers = {
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```

### cURL Example

```bash
curl --location --request POST 'https://europe-west6-magic-heidi.cloudfunctions.net/createAbstractInvoice' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "language": "fr",
        "file_name": "test.pdf",
        "invoice_number": 11,
        "invoice_date": "30/06/2022",
        "vat_enabled": true,
        "vat_percentage": 2.5,
        "vat_number": "CHE-kjkj",
        "user_details": {
            "name": "Nathan",
            "address": "Rue de la gare 1",
            "zip": "1000",
            "city": "Lausanne",
            "iban": "CH0700700112900411647"
        },
        "customer_details": {
            "name": "Nathan",
            "address": "Rue de la gare 1",
            "zip": "1000",
            "city": "Lausanne"
        },
        "invoice_items": [
            {
                "description": "Test",
                "quantity": 1.5,
                "unit_price": 100.50
            }
        ]
    }
} '
```

### NodeJS Axios

```javascript
var axios = require('axios');
var data = JSON.stringify({
  "data": {
    "language":

 "fr",
    "file_name": "test.pdf",
    "invoice_number": 11,
    "invoice_date": "30/06/2022",
    "vat_enabled": true,
    "vat_percentage": 2.5,
    "vat_number": "CHE-kjkj",
    "user_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne",
      "iban": "CH0700700112900411647"
    },
    "customer_details": {
      "name": "Nathan",
      "address": "Rue de la gare 1",
      "zip": "1000",
      "city": "Lausanne"
    },
    "invoice_items": [
      {
        "description": "Test",
        "quantity": 1.5,
        "unit_price": 100.5
      }
    ]
  }
});

var config = {
  method: 'post',
  url: 'https://europe-west6-magic-heidi.cloudfunctions.net/createAbstractInvoice',
  headers: { 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});
```

### Java OkRest

```OkHttpClient client = new OkHttpClient().newBuilder()
  .build();
MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{\n    \"data\": {\n        \"language\": \"fr\",\n        \"file_name\": \"test.pdf\",\n        \"invoice_number\": 11,\n        \"invoice_date\": \"30/06/2022\",\n        \"vat_enabled\": true,\n        \"vat_percentage\": 2.5,\n        \"vat_number\": \"CHE-kjkj\",\n        \"user_details\": {\n            \"name\": \"Nathan\",\n            \"address\": \"Rue de la gare 1\",\n            \"zip\": \"1000\",\n            \"city\": \"Lausanne\",\n            \"iban\": \"CH0700700112900411647\"\n        },\n        \"customer_details\": {\n            \"name\": \"Nathan\",\n            \"address\": \"Rue de la gare 1\",\n            \"zip\": \"1000\",\n            \"city\": \"Lausanne\"\n        },\n        \"invoice_items\": [\n            {\n                \"description\": \"Test\",\n                \"quantity\": 1.5,\n                \"unit_price\": 100.50\n            }\n        ]\n    }\n} ");
Request request = new Request.Builder()
  .url("https://europe-west6-magic-heidi.cloudfunctions.net/createAbstractInvoice")
  .method("POST", body)
  .addHeader("Content-Type", "application/json")
  .build();
Response response = client.newCall(request).execute();
```


Thank you and if you have any questions! Let us know! 
