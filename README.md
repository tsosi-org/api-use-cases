# TSOSI API use cases

```
author: Maxence Larrieu, PhD
author_orcid: https://orcid.org/0000-0002-1834-3007
date: 2025-06-05
```

<br />
<br />

For a short introduction, scroll down to see the two mains endpoints _entities_ and _transfers_—along with their associated metadata. After that, I have included notebooks that answer more specitic questions, such as "[What is the coverage or the ROR and Wikidata identifiers?](#what-is-the-coverage-or-the-ror-and-wikidata-identifiers)".


<br />
<br />

## API's endpoints

https://tsosi.org/api/


**/!\ TSOSI is in beta version**: see the the implication [tsosi.org/pages/faq#beta-version](https://tsosi.org/pages/faq#beta-version)


<br />
<br />

## API's structure

* **entities**: It includes all the infrastructures and organizations that have contributed to them

`https://tsosi.org/api/entities/all?format=json`

* **transfers**: It contains all the financial transfers that have taken place between entities

`https://tsosi.org/api/transfers/?format=json`


* **analytics**: It contains aggregated transfers by country and by year

`https://tsosi.org/api/analytics?format=json`


<br />
<br />

## Using the `entities` endpoint

* Get all the entitites

```python
import requests, json
raw_entities = requests.get("https://tsosi.org/api/entities/all?format=json").json()
len(raw_entities)
# 1048

```


* Find your organization with the ROR identifier

```py
for item in raw_entities : 
    for item_id in item["identifiers"] : 
        # if entity has an identifier
        if item_id.get("registry") : 
        	# if entity has a ROR identifier
            if item_id["registry"] == "ror" :
            	# if the ROR id correspond to my organization
                if item_id["value"] == "02rx3b187" : 
                    print(json.dumps(item, indent=2))

```

* Metadata structure of one entity

```json
{
  "id": "d17c5801-56f7-495b-a634-46df6c11b5f2",
  "name": "Universit\u00e9 Grenoble Alpes",
  "short_name": null,
  "country": "FR",
  "identifiers": [
    {
      "registry": "wikidata",
      "value": "Q945876"
    },
    {
      "registry": "ror",
      "value": "02rx3b187"
    }
  ],
  "coordinates": "POINT(5.76281 45.1787)",
  "logo": "https://tsosi.org/media/d17c5801-56f7-495b-a634-46df6c11b5f2/logo/Logo_Universit%C3%A9_Grenoble-Alpes_2020.jpg",
  "icon": null,
  "is_recipient": false,
  "is_partner": false
}

```


<br />
<br />

## Using the `transfers` endpoint

* Get all the transfers

```python
import requests, json
raw_transfers = requests.get("https://tsosi.org/api/transfers/all?format=json").json()
len(raw_transfers)

```

* Metadata structure of one transfer


```json
{
    "id": "c440231b-9ed6-4eaa-9b0c-351883d4ddad",
    "emitter_id": "9195f172-792d-465b-8d9a-639749ca24d2",
    "recipient_id": "127f60a0-bf97-4bef-8042-5f765c7ed86d",
    "agent_id": null,
    "amount": 5000.0,
    "currency": "EUR",
    "date_clc": {
        "value": "2017-01-01",
        "precision": "year"
    },
    "description": "",
    "amounts_clc": {
        "CAD": 7318,
        "DKK": 37192,
        "EUR": 5000,
        "GBP": 4378,
        "USD": 5637
    }
}

```

* Retrieve all transfers made by your organization


```python
import requests, json, pandas
raw_transfers = requests.get("https://tsosi.org/api/transfers/all?format=json").json()len(raw_transfers)
transfers = pd.json_normalize(raw_transfers)
## get the transfers related to your entity identifier
my_transfers = transfers[transfers["emitter_id"] == "d17c5801-56f7-495b-a634-46df6c11b5f2" ]
print(json.dumps(json.loads(my_transfers[["recipient_id", "amount", "currency", "date_clc.value"]].to_json()), indent=2))

```


```json
{
  "recipient_id": {
    "583": "127f60a0-bf97-4bef-8042-5f765c7ed86d",
    "1510": "127f60a0-bf97-4bef-8042-5f765c7ed86d",
    "2071": "127f60a0-bf97-4bef-8042-5f765c7ed86d",
    "2167": "c584df0e-75ea-44ee-a0b9-3a4b5c0706be",
    "2657": "239891e9-4ffe-4735-99b3-ea5b21ff3e00",
    "2713": "c584df0e-75ea-44ee-a0b9-3a4b5c0706be",
    "2899": "239891e9-4ffe-4735-99b3-ea5b21ff3e00"
  },
  "amount": {
    "583": 2000.0,
    "1510": 2000.0,
    "2071": 2000.0,
    "2167": null,
    "2657": 2500.0,
    "2713": null,
    "2899": 2500.0
  },
  "currency": {
    "583": "EUR",
    "1510": "EUR",
    "2071": "EUR",
    "2167": null,
    "2657": "EUR",
    "2713": null,
    "2899": "EUR"
  },
  "date_clc.value": {
    "583": "2022-01-01",
    "1510": "2023-01-01",
    "2071": "2024-01-01",
    "2167": "2024-01-01",
    "2657": "2024-09-13",
    "2713": "2025-01-01",
    "2899": "2025-01-17"
  }
}

```

<br />
<br />

## What is the coverage of ROR and Wikidata identifiers for TSOSI entities?

In short, Wikidata covers approximately 90 % while ROR covers about 80%. See [coverage-entities-identifiers.ipynb](coverage-entities-identifiers.ipynb)