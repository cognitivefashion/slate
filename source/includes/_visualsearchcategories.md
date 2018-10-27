# (Category) Visual Search ++

Visual search supports a field called `visual_search_category` which allows us to build a separate visual search for each category. The earlier set of APIs allows the user to specify this field manually and duing query the user has to specify the category interested in along with the image.

The APIs below use a pre-trained classifier to automatically predict the visual search category based on the product images. During search when the user uploads an image we use the category classifier to automatically predict the category and then do visual search using the appropriate index.

## Predict categories

```python
#------------------------------------------------------------------------------
# Predict the visual search categories based on product images
# POST /v1/catalog/{catalog_name}/predict/visual_search_categories
# params - clear_cache
#          ignore_non_primary_images
#          visual_search_categories_threshold
#------------------------------------------------------------------------------

import os
import json
import requests
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
# clear_cache : str, optional (default: false)
#
# If 'visual_search_category' is already present in the product then
# we do not change it unless clear_cache=ture.
#------------------------------------------------------------------------------
params['clear_cache'] = 'true'

#------------------------------------------------------------------------------
# ignore_non_primary_images : str, optional (default: false)
#
# If true uses only the primary images (specified in the field 
# data['images'][image_id]['image_type'] = 'primary') and 
# ignores all other images.
# If false uses all the available images for the product.
# All images with data['images'][image_id]['ignore'] = 'yes' 
# are ignored.
#------------------------------------------------------------------------------
#params['ignore_non_primary_images'] = 'false'

#------------------------------------------------------------------------------
# visual_search_categories_threshold : float, optional (default: 0.0)
#
# Use the predictions from the visual search categories classifier
# only if the confidence score is greate than this value. 
#------------------------------------------------------------------------------
#params['visual_search_categories_threshold'] = 0.15

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/predict/visual_search_categories'%(catalog_name)

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
 "status": {
  "status": "starting",
  "num_of_products": "8",
  "start_time": "2018-06-21 14:52:17.252757",
  "prediction": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "10",
   "num_of_images_existing": "0",
   "num_of_images_processed": "0"
  },
  "finish_time": "UNK",
  "num_of_images": "38"
 },
 "time_ms": "0.68",
 "catalog_name": "sample_catalog"
}
```

Predict the visual search categories based on product images

This API uses a pre-trained classifier to automatically predict the visual search category based on the product images. The predicted visual search catgories are populated in the field `data['visual_search_category']`. Once you ingest the catalog you will call this API to populate the 'visual_search_category' field and then proceed with building the visual search index.

### End point

`POST /v1/catalog/{catalog_name}/predict/visual_search_categories`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
clear_cache | query | If `visual_search_category` is already present in the product then we do not change it unless `clear_cache=true`. | `false`
ignore_non_primary_images | query | If `true` uses only the primary images (specified in the field `data['images'][image_id]['image_type'] = 'primary'`) and ignores all other images. If `false` uses all the available images for the product. All images with `data['images'][image_id]['ignore'] = 'yes'`  are ignored. | `false`
visual_search_categories_threshold | query | Use the predictions from the visual search categories classifier only if the confidence score is greate than this value.  | 0.0

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
**status** | A json containing various info about the current status of the prediction.
status | The current status of the prediction. The status can be one of `starting`,`'predicting_visual_search_categories'`,or `done`. The prediction is done when the status becomes `done`.
start_time | The time when started.
finish_time | The time when finished.
num_of_products | The total number of products in the catalog.
num_of_images | The total number of images in the catalog.
**prediction** | Info about prediction.
num_of_images_error | The number of images for which there was some error.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the predictions were already computed (in case of building a new index with new products being added).
num_of_images_processed | The total number of images for which the predictions were computed. 

<aside class="notice">
This is a one time long running operation. Currently it takes around 250-500 milliseconds for one image on a CPU instance. Depending on your catalog size you will have to wait till prediction is completed to start building the visual search index. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

<aside class="notice">
Any time you add new product or images to the catalog you will have to call this API again to add the predictions for the new products.
</aside>

## Get status

```python
#------------------------------------------------------------------------------
# Get the status of the visual search categories prediction
# GET /v1/catalog/{catalog_name}/predict/visual_search_categories
#------------------------------------------------------------------------------
import os
import json
import requests
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
api_endpoint = '/v1/catalog/%s/predict/visual_search_categories'%(catalog_name)

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
  "num_of_products": "8",
  "start_time": "2018-06-21 14:52:17.252757",
  "prediction": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "10",
   "num_of_images_existing": "0",
   "num_of_images_processed": "28"
  },
  "finish_time": "2018-06-21 14:52:28.204947",
  "num_of_images": "38"
 },
 "time_ms": "1.20",
 "catalog_name": "sample_catalog"
}
```

Get the status of the visual search categories prediction

### End point

`GET /v1/catalog/{catalog_name}/predict/visual_search_categories`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
**status** | A json containing various info about the current status of the prediction.
status | The current status of the prediction. The status can be one of `starting`,`'predicting_visual_search_categories'`,or `done`. The prediction is done when the status becomes `done`.
start_time | The time when started.
finish_time | The time when finished.
num_of_products | The total number of products in the catalog.
num_of_images | The total number of images in the catalog.
**prediction** | Info about prediction.
num_of_images_error | The number of images for which there was some error.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the predictions were already computed (in case of building a new index with new products being added).
num_of_images_processed | The total number of images for which the predictions were computed. 

