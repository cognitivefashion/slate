---
title: IBM Research AI for Fashion API Reference

language_tabs:
  - shell
  - python

toc_footers:
  - <a href='https://cognitivefashion.github.io/'>Project Website</a>
  - <a href='https://github.com/cognitivefashion/cf-sdk-python'>Python SDK</a>
  - <a href='mailto:viraykar@in.ibm.com;jaikhari@in.ibm.com?Subject=AI%20%20for%20Fashion'>Contact</a>
includes:
  - catalog
  - visualsearchindex
  - visualbrowse
  - visualsearch
  - visualsearchcategories
  - textsearch
  - nls
  - visualattributes
  - detectors
  - colors
  - colorsearch
  - dominantcolors
  - colortrends
  - printsearch
  - dominantprints
  - lookssearch
  - dominantlooks
  - ctl
  - errors
  
search: true
---

# IBM Research AI for Fashion 

The [IBM Research AI for fashion](https://cognitivefashion.github.io/) project aims to build a suite of APIs for the fashion industry primarily leveraging deep learning, natural language processing and computer vision. 

The usecases are targeted towards end consumers, online retailers, buyers, merchandisers and designers. Developers/in-house IT team can use these APIs to build their own applications. Central to this is a curated domain specific taxonomy/knowledge graph, fashion related content and text/image annotators tuned to the domain of fashion. 

[Python SDK](https://github.com/cognitivefashion/cf-sdk-python)

<aside class="notice">
Our <b>fashion taxonomy</b> is a semi-automatically curated comprehensive hierarchical fashion taxonomy for apparel, footwear, and accessories along with their attributes and relations. The taxonomy is curated from several fashion resources and then hand tuned by domain experts. All the APIs below leverage this fashion taxonomy. Fashion evolves constantly and hence IBM fashion taxonomy is dynamically updated at regular intervals to include the current fashion terms and trends. The user will always have access to the latest fashion taxonomy when using any of these APIs.
</aside>

# Authentication

```shell
# Pass the API key in the header.
curl 'api_endpoint_here' -H "X-Api-Key: your_api_key"

# If you are making a GET request, 
# you may also pass in the API key as a parameter.
curl "api_endpoint_here"?api_key=your_api_key
```

```python
# We will use the requests HTTP library for python for all the examples.
# http://docs.python-requests.org

import requests

url = 'api_endpoint_here'
headers = {'X-Api-Key': 'your_api_key'}
response = requests.get(url,headers=headers)

# If you are making a GET request, 
# you may also pass in the API key as a parameter.
url = 'api_endpoint_here'
payload = {'api_key': 'your_api_key'}
response = requests.get(url,params=payload)
```

> Make sure to replace `your_api_key` with your API key.

Cognitive Fashion uses API keys to allow access to the API. The API key has to be included in all API requests to the server. 

The preferred way is to include it in a header that looks like the following:

`curl "api_endpoint_here" -H "X-Api-Key: your_api_key"`

If you are making a `GET` request, you may also pass in the API key as a parameter:

`curl "api_endpoint_here"?api_key=your_api_key`

<aside class="notice">
You must replace <code>your_api_key</code> with your personal API key.
</aside>

# Data collection

```shell
# Set X-Data-Collection-Opt-Out to true in the header.
curl 'api_endpoint_here' -H  "X-Data-Collection-Opt-Out: true"

# If you are making a GET request, 
# you may also set the data_collection_opt_out parameter to true.
curl "api_endpoint_here"?data_collection_opt_out=true
```

```python
import requests

url = 'api_endpoint_here'

# Set X-Data-Collection-Opt-Out to true in the header.
headers = {}
headers['X-Api-Key'] = 'your_api_key'
headers['X-Data-Collection-Opt-Out'] = 'true'

response = requests.get(url,headers=headers)

url = 'api_endpoint_here'

# If you are making a GET request, you may also set the 
# data_collection_opt_out parameter to true.
params = {}
params['api_key'] = 'your_api_key'
params['data_collection_opt_out'] = 'true'

response = requests.get(url,params=params)
```

By default, most APIs log requests and their results. For example, the visual search API by default will save the user uploaded image. Logging is done only to improve the accuracy of the APIs. The logged data is not shared or made public. However, you have the option of opting out. To prevent the service from accessing your data for further API improvements, set the `X-Data-Collection-Opt-Out` header parameter to `true`. For example, for visual search API this will delete the user uploaded image from our servers as soon as the computations are done.

The preferred way is to include it in a header that looks like the following:

`curl 'api_endpoint_here' -H  "X-Data-Collection-Opt-Out: true"`

If you are making a GET request, you may also set the `data_collection_opt_out ` parameter to `true`.

`curl "api_endpoint_here"?data_collection_opt_out=true`

# Fashion quote

```python
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header.
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Define the API end point.
api_endpoint = '/v1/fashion_quote'

url = urljoin(api_gateway_url,api_endpoint)
    
response = requests.get(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "quote": "Simplicity is the keynote of all true elegance.",
 "author": "Coco Chanel"
}
```

This endpoint gets a random quote about fashion. Think of this as our `Hello, world!` albiet a more interesting one which you can actually use on your websites.

### End point

`GET /v1/fashion_quote`

