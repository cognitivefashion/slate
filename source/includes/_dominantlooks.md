# Dominant Looks

A collection of APIs for computing the dominant looks for a catalog.

## Build dominant looks index

```python
#------------------------------------------------------------------------------
# Build the dominant looks index.
# POST /v1/catalog/{catalog_name}/dominant_looks_index
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

params = {}

# Optional parameters.
params['looks'] = 10

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_looks_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "status": "done",
  "dominant_looks": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "num_of_products": "8",
  "results_updated": "true",
  "start_time": "2018-03-29 11:56:41.883208",
  "pid": "2067",
  "finish_time": "2018-03-29 11:56:42.170910",
  "num_of_images": "38"
 },
 "time_ms": "1.16"
}
```

Build the dominant looks index.

Before you can start querying for the dominant looks you need to call this API to start the clustering procedure. 

<aside class="notice">
Prior to calling this API the looks search index has to be built.
</aside>

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the dominant looks APIs. You can query the status of the index creation using the GET endpoint. The clusteing is completed when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/dominant_looks_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
looks | query | The number of dominant looks to compute. | 10

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `clustering` or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**dominant_looks** | Info about dominant looks.
num_of_images_missing_features | The number of images for which there was no feature was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get dominant looks index status

```python
#------------------------------------------------------------------------------
# Get the status of the dominant looks index.
# GET /v1/catalog/{catalog_name}/dominant_looks_index
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

api_endpoint = '/v1/catalog/%s/dominant_looks_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "status": "done",
  "dominant_looks": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "num_of_products": "8",
  "results_updated": "true",
  "start_time": "2018-03-29 11:56:41.883208",
  "pid": "2067",
  "finish_time": "2018-03-29 11:56:42.170910",
  "num_of_images": "38"
 },
 "time_ms": "2.45"
}
```

Get the status of the dominant looks index.

### End point

`GET /v1/catalog/{catalog_name}/dominant_looks_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `clustering` or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**dominant_looks** | Info about dominant looks.
num_of_images_missing_features | The number of images for which there was no feature was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the dominant looks index 

```python
#------------------------------------------------------------------------------
# Delete the dominant looks index.
# DELETE /v1/catalog/{catalog_name}/dominant_looks_index
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

api_endpoint = '/v1/catalog/%s/dominant_looks_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "deleted": true,
 "time_ms": "8.88",
 "catalog_name": "sample_catalog"
}
```

Delete the dominant looks index.

### End point

`DELETE /v1/catalog/{catalog_name}/dominant_looks_index`

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


## Get dominant looks for a catalog

```python
#------------------------------------------------------------------------------
# Get the dominant looks for all images in the catalog.
# GET /v1/catalog/{catalog_name}/dominant_looks
# params - looks
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

params = {}
params['looks'] = 3

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_looks'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

```

> Sample json response

```json

 "computed_on": "2018-03-29 11:56:42.162979",
 "time_ms": "2.24",
 "dominant_looks": [
  {
   "image_location": "/v1/catalog/sample_catalog/images/LPJNA16AMDMTE91662_2.jpg?top_left_x=57&top_left_y=0&width=197&height=351",
   "popularity": 0.14285714285714285,
   "image_id": "2",
   "bounding_box": {
    "width": 197,
    "score": 0.530316,
    "top_left_x": 57,
    "top_left_y": 0,
    "height": 351
   },
   "n_instances": 4,
   "image_url": "http://images.abofcdn.com/catalog/images/2016/LPJNA16AMDMTE9166200/Right_Medium.jpg",
   "id": "LPJNA16AMDMTE91662",
   "image_filename": "LPJNA16AMDMTE91662_2.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_4.jpg?top_left_x=111&top_left_y=0&width=91&height=326",
   "popularity": 0.14285714285714285,
   "image_id": "4",
   "bounding_box": {
    "width": 91,
    "score": 0.538281,
    "top_left_x": 111,
    "top_left_y": 0,
    "height": 326
   },
   "n_instances": 4,
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/ABOFA15AMCWJG10449_4.jpg?top_left_x=119&top_left_y=0&width=114&height=360",
   "popularity": 0.14285714285714285,
   "image_id": "4",
   "bounding_box": {
    "width": 114,
    "score": 0.76403,
    "top_left_x": 119,
    "top_left_y": 0,
    "height": 360
   },
   "n_instances": 4,
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044901/Look_Medium.jpg",
   "id": "ABOFA15AMCWJG10449",
   "image_filename": "ABOFA15AMCWJG10449_4.jpg"
  }
 ]
}
```

Get the dominant looks for all images in the catalog.

The dominant looks is to be interpreted as a representative of the cluster of similar looks.

<aside class="notice">
Prior to calling this API the dominant looks index has to be built.
</aside>

### End point

`GET /v1/catalog/{catalog_name}/dominant_looks`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
looks | query | The number of dominant looks to return. | 10

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_url | The image url.
image_location | The image location.
**dominant_looks** |  A list of dominant looks for the catalog sorted in the decreasing order of popularity. Each field in the list has the following fieldnames.
id | The product id.
image_id | The image id.
image_url | The image url.
image_filename | The image filename.
image_location | The image location of the person in the image.
popularity | A score between 0 and 1 indicating the popularity of the print.
n_instances | The number of looks in the cluster.
**bounding_box** | The bounding box of the person.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.




