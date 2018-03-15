# Text Search

```python
#------------------------------------------------------------------------------
# Basic text search
# GET /v1/catalog/{catalog_name}/text_search
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin
from pprint import pprint

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/text_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'joggers'
params['max_number_of_results'] = 12

response = requests.get(url,headers=headers,params=params)

print response.url
print response.status_code
pprint(response.json())

# Get more info about the relevant results
for product in response.json()['products']:
    id = product['id']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    print('%s %s'%(id,response.json()['data']['name']))
```

> Sample json response

```json
{
 "time_ms": "41.15",
 "products": [
  {
   "id": "ABOFA15AMCWJG10448"
  },
  {
   "id": "ABOFA15AMCWJG10449"
  }
 ]
}
```

Search the product catalog using a simple text query (e.g. `joggers`).

### End point

`GET /v1/catalog/{catalog_name}/text_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
catalog_name | path | (**Required**) The catalog name. 
query_text | query | (**Required**) The text query. (e.g. `joggers`) |
max_number_of_results | query | The maximum number of results to return. | 12


### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list of products in the catalog relevant to the query.
id | The id of the product.
