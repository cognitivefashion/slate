
# Complete the Look

Given a fashion apparel suggest another apparel/accessory that goes well with it. The API also returns a natural language style tip along with the products.

Some use cases for visual attributes include:
- recommendation of apparels based on style rules
- aids in curating fashion outfits by bloggers, designers

<aside class="notice">
We differentiate between two kinds of usecase. Complete the look using the meta data of the product of interest. Complete the look using the image of the product of interest.
</aside>

## Complete the Look using meta data of the product of interest

Given a fashion apparel suggest another apparel/accessory that goes well with it using the meta data of the product

```python

import os
import json
import requests
from urlparse import urljoin
from pprint import pprint
from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
headers = {'X-Api-Key': props['X-Api-Key']}

# Parameters.
params = {}
params['query_text'] = "red floral dress"
params['gender'] = "female"

api_endpoint = '/v1/complete_the_look/text/'
url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)
print response.status_code
pprint(response.json())

```

> Sample json response

```json

{u'recommendation': [u'heels', u'sling bag', u'earrings'],
 u'style_tip': u'style this dress by teaming it with a pair of black heels and a sling bag and a pair of earrings and a sling bag. ',
 u'time_ms': 0.45203208923339844}

```

### End point

`GET /v1/complete_the_look/text/

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
query_text | query | (**Required**) The query text
gender | query | gender for the query text 


### Response

Parameter |  Description
--------- |  -----------
time_ms |  The time taken for generating the response in milliseconds.
**style_tip** |  The style tip for the query fashion item
**recommendation** | List of fashion items which would go well with the query fashion item


## Complete the Look using the image of the product of interest

Coming soon ...