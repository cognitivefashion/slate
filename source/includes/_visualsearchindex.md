# Visual Search

A collection of APIs for enabling a better search experience using the catalog images. Visual search helps a consumer discover exact match or show similar fashion products based on image search. These APIs support the following two variants:

1. **Visual Search** (*Search the look*) Consumer can upload any fashion image of his/her liking and visual search will show visually similar products that exist in the catalog. 

1. **Visual Browse** (*Show me more like this*) A user clicks on an existing apparel/accessory image in the catalog and visual browse will fetch and display visually similar looking products from the catalog. This is a more interactive way of browsing the catalog based on visual search. 

1. **Category based Visual Search/Browse** (*Visual Search and Visual Browse in a specific category*) The visual search and visual browse APIs also support searching in a specific category. For example, in visual search, the user can upload any image and specify the category to search in (for example, show me similar looks like this in the `dress` category). To enable this use `visual_search_category` (for visual search) and `visual_browse_category` (for visual browse) field in the product json. 

    `visual_search_category` - A list of categories(or buckets) this product belongs to (for example, `data['visual_search_category'] = ['dress']`). This field is used to support category based visual search. 

    `visual_browse_category` - A list of categories(or buckets) this product belongs to (for example, `data['visual_browse_category'] = ['sheath dress']`). This field is used to support category based visual browse. 

    A separate visual search index will be built with images from a particular category. While in most cases this will be same as `category` field in principle this field can be differnet. For example, `shirts` could be the `category` while the `visual_search_category` could be `women shirts`. 

    The field can also support multiple category values. For example, `data['visual_search_category'] = ['tops','blouses']`. This means that the images corresponding to this product will be included in the visual search index for both `tops` and `blouses`.

## Build visual search index

```python
#------------------------------------------------------------------------------
# Build the visual search index.
# POST /v1/catalog/{catalog_name}/visual_search_index
# params - per_category_index
#          full_index
#          group_by
#          group_by_k 
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
# Data collection opt out
headers['X-Data-Collection-Opt-Out'] = props['X-Data-Collection-Opt-Out']

#------------------------------------------------------------------------------
# OPTIONAL QUERY PARAMETERS
#------------------------------------------------------------------------------
params = {}

#------------------------------------------------------------------------------
# per_category_index : str, optional (default: 'false')
#
# If this is set to 'true' builds a separate visual search index for each 
# category as specified in the field data['visual_search_category'].
#------------------------------------------------------------------------------
params['per_category_index'] = 'true'

#------------------------------------------------------------------------------
# full_index : str, optional (default: 'true')   
#
# If this is set to 'true' builds the the full visual search index along with
# the separate visual search index for each visual search/browse category. 
# By default the full visual search index is build along with the separate 
# index for each category. Make this 'false' only if you do not see the need 
# for the full visual search index.
#------------------------------------------------------------------------------
#params['full_index'] = 'false'

#------------------------------------------------------------------------------
# group_by : boolean, optional(default:'false')
#
#    If this is set to True the group_by option is enabled. This
#    groups the results from visual search into distinct groups 
#    according to various criteria. Currently the group_by option 
#    works only when full_index is set to True. If full_index is 
#    False the group_by is disabled.
#
# group_by_k : int, optional(default:5).
#
#    The number of nearest neighbors to use for group_by.
#------------------------------------------------------------------------------
#params['group_by'] = 'true'
#params['group_by_k'] = 5

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,
                         headers=headers,
                         params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "started": true,
 "time_ms": "0.55",
 "catalog_name": "sample_catalog"
}
```

Build the visual search index.

Before you can start using the visual search and visual browse APIs the visual search index has to be built first. This API computes the feature representations for all the catalog images and then builds a visual search index(each each category) for fast nearest neighbor retrieval.

### End point

`POST /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
per_category_index | query | If this is set to `true` builds a separate visual search index for each category as specified in the field `data['visual_search_category']` and `data['visual_browse_category']`. | `false`
full_index | query | If this is set to `true` builds the the full visual search index along with the separate visual search index for each visual search category. By default the full visual search index is built. Make this `false` only if you do not see the need for the full visual search index. | `true`
group_by | query | If this is set to `true` the `group_by` option is enabled. This will allow the results from visual search to be grouped into distinct groups  according to various criteria. Currently the `group_by` option works only when `full_index` is set to `true`. If `full_index` is `false` the `group_by` option is disabled. | `false`
group_by_k | query | The number of nearest neighbors to use for group_by. | 5

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
started |  This is `true` if the index building was started. You can query the status of the index building with the GET endpoint.

<aside class="notice">
This is a one time long running operation. Currently it takes around 250-500 milliseconds to compute the features for one image on a CPU instance. Depending on your catalog size you will have to wait till the index creation is completed to start using the visual search and visual browse APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

<aside class="notice">
Any time you add new product or images to the catalog you will have to rebuild the index. The features for the old images will still be saved and we will be computing the features only for the newly added images. 
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get visual search index status

```python
#------------------------------------------------------------------------------
# Get the status of the visual search index. 
# GET  /v1/catalog/{catalog_name}/visual_search_index
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
api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

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
 "status": {
  "status": "done",
  "visual_search_index": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "num_of_products": "8",
  "start_time": "2018-10-26 09:15:33.013153",
  "index_updated": "true",
  "finish_time": "2018-10-26 09:15:40.012162",
  "visual_search_index_by_category": {
   "upper": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "12",
    "num_of_images_ignored": "26"
   },
   "lower": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "8",
    "num_of_images_ignored": "30"
   },
   "full": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "8",
    "num_of_images_ignored": "30"
   },
   "shirt": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "8",
    "num_of_images_ignored": "30"
   },
   "t-shirt": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "4",
    "num_of_images_ignored": "34"
   },
   "kurta": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "4",
    "num_of_images_ignored": "34"
   },
   "dress": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "4",
    "num_of_images_ignored": "34"
   },
   "joggers": {
    "num_of_images_missing_features": "0",
    "num_of_images_indexed": "8",
    "num_of_images_ignored": "30"
   }
  },
  "num_of_products_by_category": {
   "upper": "4",
   "lower": "2",
   "full": "2",
   "shirt": "2",
   "t-shirt": "2",
   "joggers": "2",
   "dress": "1",
   "kurta": "1"
  },
  "num_of_images": "38",
  "feature_computation": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "10",
   "num_of_images_existing": "0",
   "num_of_images_processed": "28"
  },
  "num_of_images_by_category": {
   "upper": "18",
   "lower": "10",
   "full": "10",
   "shirt": "10",
   "t-shirt": "8",
   "kurta": "5",
   "dress": "5",
   "joggers": "10"
  }
 },
 "time_ms": "32.12",
 "catalog_name": "sample_catalog"
}
```

Get the status of the visual search index.

### End point

`GET /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `starting`,`computing_features`,`building_index`,or `done`. The index is built when the status becomes `done`.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
num_of_products | The total number of products in the catalog.
num_of_products_by_category | The number of products in the catalog bucketed by `visual_search_category` and `visual_browse_category`.
num_of_images | The total number of images in the catalog.
num_of_images_by_category | The number of images in the catalog bucketed by `visual_search_category` and `visual_browse_category`.
**feature_computation** | Info about feature computation.
num_of_images_error | The number of images for which there was some error in feature computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products being added).
num_of_images_processed | The total number of images for which the features were computed. 
**visual_search_index** | Info about the index building.
num_of_images_missing_features | The number of images for which there was no feature found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_index | The total number of images finally indexed.
**visual_search_index_by_category** | Same info as **visual_search_index** but bucketed by `visual_search_category` and `visual_browse_category`.

<aside class="notice">
The bucketed fields (*_by_category) are present in the response only when the 'per_category_index' parameter is set to 'true' during the index building.
</aside>

## Delete visual search index 

```python
#------------------------------------------------------------------------------
# Delete the visual search index. 
# DELETE  /v1/catalog/{catalog_name}/visual_search_index
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
api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,
                           headers=headers,
                           params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "deleted": true,
 "time_ms": "39.63",
 "catalog_name": "sample_catalog"
}
```

Delete the visual search index.

### End point

`DELETE /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
deleted |  This is `true` if the index was successfully deleted.