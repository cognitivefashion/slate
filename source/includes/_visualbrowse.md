## Visual Browse

```python
#------------------------------------------------------------------------------
# Visual Browse
#
# Get other visually similar products in the catalog.
# GET /v1/catalog/{catalog_name}/visual_search/{id}/{image_id}
# params - max_number_of_results
#          per_category_index
#          category
#          unique_products
#          sort_option
#          use_cache
#------------------------------------------------------------------------------

import os
import json
import requests
try:
    from urllib.parse import urljoin
except ImportError:
    from urlparse import urljoin
from pprint import pprint

from props import *

#------------------------------------------------------------------------------
# API URL
#------------------------------------------------------------------------------
api_gateway_url = props['api_gateway_url']

#------------------------------------------------------------------------------
# HEADERS
#------------------------------------------------------------------------------
headers = {}
# API key
headers['X-Api-Key'] = props['X-Api-Key']
# Data collection opt out.
headers['X-Data-Collection-Opt-Out'] = props['X-Data-Collection-Opt-Out']

#------------------------------------------------------------------------------
# OPTIONAL QUERY PARAMETERS
#------------------------------------------------------------------------------
params = {}

#------------------------------------------------------------------------------
# max_number_of_results : int, optional (default: 12)
#
# The maximum number of results to return. 
#------------------------------------------------------------------------------
params['max_number_of_results'] = 5

#------------------------------------------------------------------------------
# per_category_index : str, optional (default: 'false')
#
# If this is set to 'true' does visual browse only in the categories specifed 
# in the data['visual_search_category'] field. 
#------------------------------------------------------------------------------
params['per_category_index'] = 'true'

#------------------------------------------------------------------------------
# category : str, optional (default: None) 
#
# Optionally you can also specify a list of categories to search.
# This is specified as a comma separated string, for example, 
# `category=tops,blouses`. The categories have to be valid 
# categories specified in the data['visual_browse_category'] field.
#------------------------------------------------------------------------------
#params['category'] = 'shirt,dress'

#------------------------------------------------------------------------------
# unique_products : str, optional (default: 'false')
#
# By default visual browse uses all images available for a product.
# Hence, sometimes in the search results the same product id can 
# appear multiple times (with a different image_id). If this 
# parameter is set to 'true' then the results are post-filtered 
# to contain only unique products (the best matching image_id is
# retained).  
#------------------------------------------------------------------------------
#params['unique_products'] = 'true'

#------------------------------------------------------------------------------
# sort_option : str, optional (default: 'visual_similarity')
#
# 'visual_similarity'  : Results are sorted by visual similarity.
# 'apparel_similarity' : Re-sorts the results from using apparel similarity.
# 'color_similarity'   : Re-sorts the results from using color similarity.
#------------------------------------------------------------------------------
#params['sort_option'] = 'apparel_similarity'
#params['sort_option'] = 'color_similarity'

#------------------------------------------------------------------------------
# use_cache : str, optional (default: 'true')
#
# The first time visual browse is called it caches the results.
# On subsequent calls the API directly retrieves the results from the cache 
# unless use_cache='false'.  
#------------------------------------------------------------------------------
params['use_cache'] = 'false'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

# The product id.
id ='ABOFA15AMCWJG10449'

# The image id.
image_id = '1'

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/visual_browse/%s/%s'%(catalog_name,
                                                     id,
                                                     image_id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)

print(response.url)
print(response.status_code)
pprint(response.json())

#------------------------------------------------------------------------------
# FRIENDLY RESPONSE
#------------------------------------------------------------------------------
results = response.json()

for product in results['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    # Get the image_url and image_filename by querying the catalog
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    image_filename = response.json()['data']['images'][image_id]['image_filename']
    image_location = '/v1/catalog/%s/images/%s'%(catalog_name,image_filename)
    image_url_local = '%s?api_key=%s'%(urljoin(api_gateway_url,image_location),props['X-Api-Key'])
    print('%s %s %1.2f %s'%(id,image_id,score,image_url_local))
```

> Sample json response

```json
{
 "sort_option": "visual_similarity",
 "time_ms": "6.81",
 "cache": false,
 "visual_browse_category": [
  "joggers"
 ],
 "per_category_index": true,
 "products": [
  {
   "image_id": "1",
   "visual_browse_category": "joggers",
   "id": "ABOFA15AMCWJG10449",
   "similarity": 0.9999680509281461
  },
  {
   "image_id": "3",
   "visual_browse_category": "joggers",
   "id": "ABOFA15AMCWJG10449",
   "similarity": 0.6284773349761963
  },
  {
   "image_id": "2",
   "visual_browse_category": "joggers",
   "id": "ABOFA15AMCWJG10449",
   "similarity": 0.6176716089248657
  },
  {
   "image_id": "4",
   "visual_browse_category": "joggers",
   "id": "ABOFA15AMCWJG10448",
   "similarity": 0.47411400079727173
  },
  {
   "image_id": "3",
   "visual_browse_category": "joggers",
   "id": "ABOFA15AMCWJG10448",
   "similarity": 0.45831894874572754
  }
 ]
}
```

Find other similar images in the product catalog for a given image based on visual appearance.

<aside class="notice">
Note that the visual search index has to be built first before we can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/visual_browse/{id}/{image_id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
max_number_of_results | query | The maximum number of results to return. | 12
per_category_index | query | If this is set to `true` does visual browse only in the categories specifed  in the `data['visual_browse_category']`` field. |  `false`
category | query | Optionally you can also spaecify a list of categories to search. This is specified as a comma separated string, for example, `category=tops,blouses`. The categories have to be valid categories specified in the data['visual_browse_category'] field. |
use_cache | query | The first time visual browse is called it caches the results. On subsequent requests we directly retrieve the results from the cache unless `use_cache=false`. | `true`
sort_option | query | The sort option to use. Can be either `visual_similarity` (Results are sorted by visual similarity.) or `apparel_similarity` (Re-sorts the results from visual browse using a combination of visual similarity and textual similarity based on the avaialble meta data.) | `visual_similarity`
unique_products | query | By default visual browse uses all images available for a product. Hence, sometimes in the search results the same product id can appear multiple times (with a different image_id). If this parameter is set to 'true' then the results are post-filtered to contain only unique products (the best matching image_id is retained). | `false`

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
cache | Indicates whether cache was used or not.
per_category_index | Indicates whetehr category based visual search was used or not.
visual_browse_category | The list of visual browse categories used.
sort_option | The sort_option used.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1(similar). 
visual_browse_category | The visual browse category for the product.

<aside class="notice">
The first time the API is called the nearest neighbors are retrieved and the results are cached. On subsequent calls the API directly retrieves the results from the cache unless specified not to use the cache. The cache automatically gets invalidated whenever the index gets updated.
</aside>

## Get visual browse categories

```python
#------------------------------------------------------------------------------
# Get all visual browse categories 
# GET /v1/catalog/{catalog_name}/visual_browse_categories
#------------------------------------------------------------------------------

import os
import json
import requests
try:
    from urllib.parse import urljoin
except ImportError:
    from urlparse import urljoin
from pprint import pprint

from props import *

#------------------------------------------------------------------------------
# API URL
#------------------------------------------------------------------------------
api_gateway_url = props['api_gateway_url']

#------------------------------------------------------------------------------
# HEADERS
#------------------------------------------------------------------------------
headers = {}
# API key
headers['X-Api-Key'] = props['X-Api-Key']
# Data collection opt out.
headers['X-Data-Collection-Opt-Out'] = props['X-Data-Collection-Opt-Out']

#------------------------------------------------------------------------------
# OPTIONAL QUERY PARAMETERS
#------------------------------------------------------------------------------
params = {}

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/visual_browse_categories'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "14.88",
 "counts": {
  "num_of_products": 8,
  "num_of_products_by_category": {
   "t-shirt": 2,
   "kurta": 1,
   "dress": 1,
   "shirt": 2,
   "joggers": 2
  },
  "num_of_images": 38,
  "num_of_images_by_category": {
   "t-shirt": 8,
   "kurta": 5,
   "dress": 5,
   "shirt": 10,
   "joggers": 10
  }
 },
 "categories": [
  "t-shirt",
  "joggers",
  "dress",
  "shirt",
  "kurta"
 ]
}
```

Get all visual browse categories 

### End point

`GET /v1/catalog/{catalog_name}/visual_browse_categories`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
categories | The visual browse categories.
counts | The counts of the visual browse categories.