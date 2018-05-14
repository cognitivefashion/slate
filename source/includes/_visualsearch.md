# Visual Search

A collection of APIs for enabling a better search experience using the catalog images. Visual search helps a consumer discover exact match or show similar fashion products based on image search. 

These APIs support the following two variants:

1. **Visual Browse:  Show me more like this** A user clicks on an existing apparel/accessory image in the catalog and visual browse will fetch and display similar looking products from the catalog. This is a more interactive way of browsing the catalog based on visual search. It essentially recreates the experience of asking a shopkeeper “Show me more dresses like this.” and encourages serendipitous buying. This can also be used for finding other similar looks for any product in the catalog.

1. **Visual Search: Search the look** Consumer can upload any fashion image of his/her liking and visual search will show visually similar products that exist in the catalog. 

## Build visual search index

```python
#------------------------------------------------------------------------------
# Build the visual search index.  
# GET /v1/catalog/{catalog_name}/visual_search_index
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "status": {
  "status": "done",
  "visual_search_index": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  },
  "num_of_products": "10",
  "start_time": "2017-01-21 03:46:32.043609",
  "finish_time": "2017-01-21 03:47:24.785563",
  "num_of_images": "60",
  "feature_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  }
 },
 "time_ms": "52746.07"
}
```

Build the visual search index.

Before you can start using the visual search and visual browse APIs the visual search index has to be built first. This API computes the feature representations for all the uploaded images and then builds an index for fast nearest neighbor retrieval.

<aside class="notice">
This is a one time long running operation. Currently it takes around 250-500 milliseconds to compute the features for one image on a bluemix instance (until bluemix supports GPU instances). Depending on your catalog size you will have to wait till the index creation is completed to start using the visual search APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status becomes `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_features`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**feature_computation** | Info about feature computation.
num_of_images_error | The number of images for which there was some error in feature computation.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**visual_search_index** | Info about the index building.
num_of_images_missing_features | The number of images for which there was no feature found.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_index | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index. Note that the features for the old images will still be saved and we will be computing the features only for the newly added images.
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
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "status": "computing_features",
 "visual_search_index": {
  "num_of_images_missing_features": "0",
  "num_of_images_indexed": "0",
  "num_of_images_ignored": "0"
 },
 "num_of_products": "10",
 "time_ms": "3.72",
 "start_time": "2017-01-21 04:05:50.445071",
 "finish_time": "0",
 "num_of_images": "60",
 "feature_computation": {
  "num_of_images_ignored": "6",
  "num_of_images_existing": "0",
  "num_of_images_error": "0",
  "num_of_images_processed": "19"
 }
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
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_features`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**feature_computation** | Info about feature computation.
num_of_images_error | The number of images for which there was some error in feature computation.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**visual_search_index** | Info about the index building.
num_of_images_missing_features | The number of images for which there was no feature found.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_index | The total number of images finally indexed.

## Delete visual search index 

```python
#------------------------------------------------------------------------------
# Delete the visual search index. 
# DELETE  /v1/catalog/{catalog_name}/visual_search_index
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)

print response.status_code
print response.json()
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

## Visual Browse

```python
#------------------------------------------------------------------------------
# Visual Browse
#
# Get other visually similar products in the catalog.
# GET /v1/catalog/{catalog_name}/visual_search/{id}/{image_id}
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

id ='LPJNA16AMDMTE91662'
image_id = '2'

api_endpoint = '/v1/catalog/%s/visual_browse/%s/%s'%(catalog_name,id,image_id)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
# Optional parameters.
params['max_number_of_results'] = 5
#params['use_cache'] = 'false'
#params['sort_option'] = 'apparel_similarity'

response = requests.get(url,headers=headers,params=params)

print response.url
print response.status_code
pprint(response.json())

# List the relevant images
for product in response.json()['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    print('%s %s %1.2f %s'%(id,image_id,score,image_url))
```

> Sample json response

```json
{
 "time_ms": "1.67",
 "cache": true,
 "sort_option": "visual_similarity",
 "products": [
  {
   "image_id": "2",
   "id": "LPJNA16AMDMTE91662",
   "similarity": 1.0
  },
  {
   "image_id": "1",
   "id": "LPJNA16AMDMTE91662",
   "similarity": 0.6761164963245392
  },
  {
   "image_id": "1",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.5998596549034119
  },
  {
   "image_id": "2",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.5588061809539795
  },
  {
   "image_id": "2",
   "id": "SKLTS16AMCWSH8SP01",
   "similarity": 0.4556262493133545
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
use_cache | query | The first time visual browse is called it caches the results. On subsequent requests we directly retrieve the results from the cache unless `use_cache=false`. | `true`
sort_option | query | The sort option to use. Can be either `visual_similarity` (Results are sorted by visual similarity.) or `apparel_similarity` (Re-sorts the results from visual browse using a combination of visual similarity and textual similarity based on the avaialble meta data.) | `visual_similarity`

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
cache | Indicates whether cache was used or not.
sort_option | The sort_option used.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1(similar). 

<aside class="notice">
The first time the API is called the nearest neighbors are retrieved and the results are cached. On subsequent calls the API directly retrieves the results from the cache unless specified not to use the cache. The cache automatically gets invalidated whenever the index gets updated.
</aside>

## Visual Search

```python
#------------------------------------------------------------------------------
# Visual Search
#
# Get visually similar products in the catalog for an uploaded image.
# POST /v1/catalog/{catalog_name}/visual_search
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
# Optional parameters.
params['max_number_of_results'] = 5

headers['Content-Type'] = 'image/jpeg'

response = requests.post(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_1.jpeg','rb'))

print response.status_code
print response.json()

# List the relevant images
for product in response.json()['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    print('%s %s %1.2f %s'%(id,image_id,score,image_url))
```

> Sample json response

```json
{
 "time_ms": "1333.02",
 "products": [
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.9999205280983006
  },
  {
   "image_id": "2",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.47892576456069946
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWPP9014709",
   "similarity": 0.4464869499206543
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9015309",
   "similarity": 0.429345965385437
  },
  {
   "image_id": "6",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.39676886796951294
  }
 ]
}
```

Find other similar images in the product catalog for a given uploaded image based on visual appearance.

<aside class="notice">
Note that the visual search index has to be built first before we can use this API.  
</aside>

### End point

`POST /v1/catalog/{catalog_name}/visual_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1 (similar). 
