# Print Search

A collection of APIs for enabling catalog search based on prints.

## Build print search index

```python
#------------------------------------------------------------------------------
# Build the print search index.
# POST /v1/catalog/{catalog_name}/print_search_index
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin
from pprint import pprint

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header.
headers = {'X-Api-Key': props['X-Api-Key']}

# Query paarameters.
params = {}
# Optional parameters.
params['print_count'] = 3
#params['image_max_dimension'] = 512
params['ignore_background'] = 'true'
params['model'] = 'person_fast'
params['fraction_boxes_threshold'] = 0.1
#params['ignore_images_without_person'] = 'true'

# Path paarameters.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/print_search_index'%(catalog_name)

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
  "finish_time": "2018-03-25 17:21:50.924603",
  "start_time": "2018-03-25 17:20:05.733959",
  "pid": "139",
  "print_search_index": {
   "num_of_images_missing_prints": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "index_updated": "true",
  "print_computation": {
   "num_of_images_ignored": "10",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "28"
  },
  "num_of_products": "8",
  "num_of_images": "38"
 },
 "time_ms": "15.24"
}
```

Build the print search index.

Before you can start using the print search or dominant prints APIs the print search index has to be built. This API pre-computes the dominant prints for all the uploaded images in the catalog and then builds an index for fast nearest neighbor retrieval. A **dominant print** is a representative 64x64 print swatch that occurs most frequently in the image.

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the print search or dominant print APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/print_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
print_count | query | The number of dominant prints to extract per image. | 3
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant prints. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant prints. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`
fraction_boxes_threshold | query | Include only those prints whos fraction of boxes is greater than this number. | 0.0
ignore_images_without_person | query | If `true` ignore images where person is not detected. If `false` and person is not detected then defaults to 'person_basic' | `false`      

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_prints`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**print_computation** | Info about dominant print computation.
num_of_images_error | The number of images for which there was some error in dominant print computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant prints were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant prints were computed. 
**print_search_index** | Info about the index building.
num_of_images_missing_prints | The number of images for which there was no dominant print was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index by calling this API again. Note that the dominant prints for the old images will still be saved and we will be computing the dominant prints only for the newly added images.
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get print search index status

```python
#------------------------------------------------------------------------------
# Get the status of the print search index.
# GET /v1/catalog/{catalog_name}/print_search_index
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

api_endpoint = '/v1/catalog/%s/print_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "status": "computing_dominant_prints",
  "num_of_products": "8",
  "start_time": "2018-03-25 17:20:05.733959",
  "pid": "139",
  "print_search_index": {
   "num_of_images_missing_prints": "0",
   "num_of_images_indexed": "0",
   "num_of_images_ignored": "0"
  },
  "finish_time": "0",
  "print_computation": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "1",
   "num_of_images_existing": "0",
   "num_of_images_processed": "5"
  },
  "num_of_images": "38"
 },
 "time_ms": "8.30"
}
```

Get the status of the print search index.

### End point

`GET /v1/catalog/{catalog_name}/print_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_prints`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**print_computation** | Info about dominant print computation.
num_of_images_error | The number of images for which there was some error in dominant print computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant prints were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant prints were computed. 
**print_search_index** | Info about the index building.
num_of_images_missing_prints | The number of images for which there was no dominant print was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the print search index 

```python
#------------------------------------------------------------------------------
# Delete the print search index.
# DELETE /v1/catalog/{catalog_name}/print_search_index
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

api_endpoint = '/v1/catalog/%s/print_search_index'%(catalog_name)

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

Delete the print search index.

### End point

`DELETE /v1/catalog/{catalog_name}/print_search_index`

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

## Print Search by id

```shell
http://localhost:9080/v1/catalog/sample_catalog/print_search?max_number_of_results=3&image_id=1&id=SHRES16AWFSDR9346A&print_count=1&print_id=1&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------ 
# Get similar image/print swatches in the catalog for an existing image/print swatch.
# GET /v1/catalog/{catalog_name}/print_search
# params - id,image_id,print_id,print_count,max_number_of_results
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

# Parameters.
params = {}
params['id'] = 'SHRES16AWFSDR9346A' 
params['image_id'] = '1'
params['print_id'] = '1'
params['print_count'] = 1
params['max_number_of_results'] = 3

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/print_search'%(catalog_name)

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
 "time_ms": "17.92",
 "products": [
  {
   "distance": 0.0,
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346A_1.jpg?top_left_x=97&top_left_y=169&width=64&height=64",
   "image_id": "1",
   "bounding_box": {
    "width": 64,
    "top_left_x": 97,
    "top_left_y": 169,
    "height": 64
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Look_Medium.jpg",
   "print_id": "1",
   "id": "SHRES16AWFSDR9346A",
   "image_filename": "SHRES16AWFSDR9346A_1.jpg"
  },
  {
   "distance": 0.023897528648376465,
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_4.jpg?top_left_x=129&top_left_y=205&width=54&height=54",
   "image_id": "4",
   "bounding_box": {
    "width": 54,
    "top_left_x": 129,
    "top_left_y": 205,
    "height": 54
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "print_id": "2",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  },
  {
   "distance": 0.024179430678486824,
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_2.jpg?top_left_x=115&top_left_y=191&width=45&height=45",
   "image_id": "2",
   "bounding_box": {
    "width": 45,
    "top_left_x": 115,
    "top_left_y": 191,
    "height": 45
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "print_id": "2",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  }
 ]
}
```

Get similar image/print swatches in the catalog for an existing image/print swatch.

<aside class="notice">
The print search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/print_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | query | (**Required**) The product id. |
image_id | query | (**Required**) The image id. |
print_id | query | The print id. | Searches for `print_count` number of prints in the image.    
print_count | query | The maximum number of dominant prints to search for in an image. | 1
max_number_of_results | query | maximum number of results to return. | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar print images. Each item in the list has the following fields.
id | The product id.
image_id | The image id.
print_id | The print id.
image_filename | The image filename.
image_url | The image url.
distance | The euclidean distance between the print in the image to the print in the query.
image_location | The resource location of the dominant print swatch.
bounding_box | The bounding box of the dominant print in the image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.

## Print Search by image

```shell
```

```python
#------------------------------------------------------------------------------
# Get similar image/print swatches in the catalog for a user uploaded image.
# POST /v1/catalog/{catalog_name}/print_search
# params - print_count,image_max_dimension,max_number_of_results
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

# Parameters.
params = {}
params['print_count'] =  1
params['max_number_of_results'] = 3
#params['image_max_dimension'] = 512

headers['Content-Type'] = 'image/jpeg'

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/print_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_1.jpeg','rb'))

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "245.13",
 "products": [
  {
   "distance": 0.3300967216491699,
   "image_location": "/v1/images/94571cf2-f376-4325-9305-c4973df05e38.jpe?top_left_x=116&top_left_y=272&width=64&height=64",
   "image_id": "5",
   "bounding_box": {
    "width": 64,
    "top_left_x": 116,
    "top_left_y": 272,
    "height": 64
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044801/Front_Medium.jpg",
   "print_id": "3",
   "id": "ABOFA15AMCWJG10448",
   "image_filename": "ABOFA15AMCWJG10448_5.jpg"
  },
  {
   "distance": 0.33134496212005615,
   "image_location": "/v1/images/94571cf2-f376-4325-9305-c4973df05e38.jpe?top_left_x=118&top_left_y=264&width=64&height=64",
   "image_id": "2",
   "bounding_box": {
    "width": 64,
    "top_left_x": 118,
    "top_left_y": 264,
    "height": 64
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044901/Front_Medium.jpg",
   "print_id": "1",
   "id": "ABOFA15AMCWJG10449",
   "image_filename": "ABOFA15AMCWJG10449_2.jpg"
  },
  {
   "distance": 0.3329866826534271,
   "image_location": "/v1/images/94571cf2-f376-4325-9305-c4973df05e38.jpe?top_left_x=136&top_left_y=272&width=64&height=64",
   "image_id": "1",
   "bounding_box": {
    "width": 64,
    "top_left_x": 136,
    "top_left_y": 272,
    "height": 64
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044901/Right_Medium.jpg",
   "print_id": "2",
   "id": "ABOFA15AMCWJG10449",
   "image_filename": "ABOFA15AMCWJG10449_1.jpg"
  }
 ],
 "image_location": "/v1/images/94571cf2-f376-4325-9305-c4973df05e38.jpe"
}
```


Get similar image/print swatches in the catalog for a user uploaded image.

<aside class="notice">
The print search index has to be built first before you can use this API.  
</aside>

### End point

`POST /v1/catalog/{catalog_name}/print_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, 
max_number_of_results | query | maximum number of results to return | 12
print_count | path | The maximum number of dominant prints to search for in an image. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant prints. If your image is of high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing

                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The resource location of the uploaded image.
**products** | A sorted list similar print images. Each item in the list has the following fields.
id | The product id.
image_id | The image id.
print_id | The print id.
image_filename | The image filename.
image_url | The image url.
distance | The euclidean distance between the print in the image to the print in the query.
image_location | The resource location of the dominant print swatch.
bounding_box | The bounding box of the dominant print in the image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.