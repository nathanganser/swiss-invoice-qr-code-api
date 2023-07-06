
# Swiss Invoice with QR Code Generator: Magic Heidi API Documentation
![example of an invoice](https://files.umso.co/lib_rsaIjJoefXOkuYaf/ufg3jsia9jjb11px.png)

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
    "data": {
        "uuid": "866e9906-ef01-49e2-b09f-fc6a07c1b884",
        "logo_url": "https://www.google.com/images/branding/googleg/1x/googleg_standard_color_128dp.png",
        "language": "fr",
        "file_name": "test",
        "invoice_number": 11,
        "invoice_date": "2020-07-19T18:00:00.000Z",
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
}
```

### API Response

#### Success: Link to the invoice in PDF format

The response will be a JSON with a `uid` value, a `url` that contains the link to the file and an `expires` value that gives you the time at which the file will expire (24 hours after creation):

```json
{
  "uid": "866e9906-ef01-49e2-b09f-fc6a07c1b884",
  "url": "https://storage.googleapis.com/magic-heidi.appspot.com/{uid}/{file_name}.pdf",
  "expires": 1688139484461
}
```

#### Errors
##### 422 Somewthing wrong with your data
In case something is wrong with the data you pass to the api, you'll get a 422 error response that will look like that.
```json
{
    "result": {
        "error": "Provided IBAN is invalid"
    }
}
```

or 
```json
{
    "result": {
        "error": "user_details is missing key: zip"
    }
}
```

##### 500 Internal server error
If something breaks on our end, we'll return a 500 error. Those are monitored internally and we will fix them proactively. But you can of course reach out to us at hello@magicheidi.ch with the timestamp at which the error occured and we'll be able to fix it for you.

##### 400 Bad request
If the request you sent is missing a json in the body, or if the json is in an invalid format, we'll return a default error like this.
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Error</title>
</head>
<body>
<pre>Bad Request</pre>
</body>
</html>
```


### Generate a minimal invoice

You can choose to ignore all VAT related parameters, logo parameters and some more to generate a basic invoice. Here is an example with only the mandatory parameters:

```json
{
    "data": {
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
Everything you need to know about the parameters and options. The parameters below need to be within the `data` json dict. See examples above. 

#### Mandatory Parameters
- `user_details` (string): Contains all the details of the user who is issuing the invoice; name, address, zip, city, iban.
- `customer_details` (string): Similar to user_details but without the iban. Contains all the information of the invoice recipient, the
 one who's paying the invoice: name, address, zip, city.
- `invoice_items` (array): A list of the invoice items, each invoice item is composed of a description (string), quantity (float), and unit_price (float).

#### Optional Parameters
- `invoice_number` (integer): The number of the invoice. Will default to 1.
- `invoice_date` (string): A date for the invoice in ISO format: `2020-07-19T18:00:00.000Z` for example. Will default to the current time in Switzerland.
- `language` (string): The language of the invoice. Can be fr, de, it, or en. Will default to en.
- `uuid` (string): An optional id of the file. Will be generated randomly if not specified.
- `file_name` (string): The name of the file. Defaults to invoice.pdf.
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

```
OkHttpClient client = new OkHttpClient().newBuilder()
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
