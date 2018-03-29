# Looks Search

A collection of APIs for enabling catalog search based on looks.

## Build look search index

```python
#------------------------------------------------------------------------------
# Build the looks search index.
# POST /v1/catalog/{catalog_name}/looks_search_index
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

# Query parameters.
params = {}
# Optional parameters.
#params['image_max_dimension'] = 512
params['ignore_background'] = 'true'
params['model'] = 'person_fast'
#params['ignore_images_without_person'] = 'true'

# Path paarameters.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/looks_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "status": "computing_features",
  "looks_search_index": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "0",
   "num_of_images_ignored": "0"
  },
  "start_time": "2018-03-28 15:05:30.654530",
  "pid": "1229",
  "num_of_products": "8",
  "finish_time": "0",
  "num_of_images": "38",
  "feature_computation": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "1",
   "num_of_images_existing": "0",
   "num_of_images_processed": "4"
  }
 },
 "time_ms": "8.29"
}
```

Build the looks search index.

Before you can start using the looks search or dominant looks APIs the looks search index has to be built. This API computes the features for all the uploaded images in the catalog and then builds an index for fast nearest neighbor retrieval.

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the looks search or dominant looks APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/looks_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant prints. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant prints. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`
ignore_images_without_person | query | If `true` ignore images where person is not detected. If `false` and person is not detected then defaults to using the entire image. | `false`      

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
num_of_images_error | The number of images for which there was some error in features computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**looks_search_index** | Info about the index building.
num_of_images_missing_feature | The number of images for which there was no features.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index by calling this API again. Note that the features for the old images will still be saved and we will be computing the features only for the newly added images.
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get looks search index status

```python
#------------------------------------------------------------------------------
# Get the status of the looks search index.
# GET /v1/catalog/{catalog_name}/looks_search_index
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

api_endpoint = '/v1/catalog/%s/looks_search_index'%(catalog_name)

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
  "index_updated": "true",
  "looks_search_index": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "start_time": "2018-03-28 15:05:30.654530",
  "pid": "1229",
  "finish_time": "2018-03-28 15:06:37.109888",
  "num_of_products": "8",
  "num_of_images": "38",
  "feature_computation": {
   "num_of_images_error": "0",
   "num_of_images_ignored": "10",
   "num_of_images_existing": "0",
   "num_of_images_processed": "28"
  }
 },
 "time_ms": "8.54"
}
```

Get the status of the looks search index.

### End point

`GET /v1/catalog/{catalog_name}/looks_search_index`

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
num_of_images_error | The number of images for which there was some error in features computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**looks_search_index** | Info about the index building.
num_of_images_missing_feature | The number of images for which there was no features.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the looks search index 

```python
#------------------------------------------------------------------------------
# Delete the looks search index.
# DELETE /v1/catalog/{catalog_name}/looks_search_index
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

api_endpoint = '/v1/catalog/%s/looks_search_index'%(catalog_name)

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

Delete the looks search index.

### End point

`DELETE /v1/catalog/{catalog_name}/looks_search_index`

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

## Looks Search by id

```python
#------------------------------------------------------------------------------ 
# Get similar looks in the catalog for an existing image.
# GET /v1/catalog/{catalog_name}/looks_search
# params - id,image_id,max_number_of_results
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
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/looks_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)


print response.status_code
pprint(response.json())

# Human friendly repsonse.

results = response.json()

for info in results['products']:
    person_image = '%s&api_key=%s'%(urljoin(api_gateway_url,info['image_location']),
                                    props['X-Api-Key'])
    print('[%1.2f] %s'%(info['similarity'],person_image)) 
```

> Sample json response

```json
{
 "time_ms": "10.32",
 "products": [
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346A_1.jpg?top_left_x=68&top_left_y=0&width=119&height=323",
   "similarity": 1.0,
   "image_id": "1",
   "bounding_box": {
    "width": 119,
    "score": 0.701361,
    "top_left_x": 68,
    "top_left_y": 0,
    "height": 323
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_filename": "SHRES16AWFSDR9346A_1.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346A_4.jpg?top_left_x=51&top_left_y=0&width=163&height=347",
   "similarity": 0.6284968256950378,
   "image_id": "4",
   "bounding_box": {
    "width": 163,
    "score": 0.702937,
    "top_left_x": 51,
    "top_left_y": 0,
    "height": 347
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_filename": "SHRES16AWFSDR9346A_4.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_5.jpg?top_left_x=95&top_left_y=0&width=119&height=324",
   "similarity": 0.5597510933876038,
   "image_id": "5",
   "bounding_box": {
    "width": 119,
    "score": 0.629143,
    "top_left_x": 95,
    "top_left_y": 0,
    "height": 324
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_5.jpg"
  }
 ]
}
```

Get similar looks in the catalog for an existing image.

<aside class="notice">
The looks search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/looks_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | query | (**Required**) The product id. |
image_id | query | (**Required**) The image id. |
max_number_of_results | query | maximum number of results to return. | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar images. Each item in the list has the following fields.
id | The product id.
image_id | The image id.
image_filename | The image filename.
image_url | The image url.
similariy | The similarity score.
image_location | The resource location of the bounding box for the detected person.
bounding_box | The bounding box of the dominant person in the image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.

## Looks Search by image

```python
#------------------------------------------------------------------------------
# Get similar looks in the catalog for a user uploaded image.
# POST /v1/catalog/{catalog_name}/looks_search
# params - max_number_of_results,ignore_background,model,image_max_dimension
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
params['max_number_of_results'] = 5
#params['image_max_dimension'] = 512
params['ignore_background'] = 'true'
params['model'] = 'person_fast'

headers['Content-Type'] = 'image/jpeg'

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/looks_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_3.jpeg','rb'))

print response.status_code
pprint(response.json())

# Human friendly repsonse.

results = response.json()

image_location = '%s?api_key=%s'%(urljoin(api_gateway_url,results['image_location']),
                                  props['X-Api-Key'])
print('[original image ] %s'%(image_location))

image_location = '%s&api_key=%s'%(urljoin(api_gateway_url,results['bounding_box']['image_location']),
                                  props['X-Api-Key'])
print('[bounding box   ] %s'%(image_location))

for info in results['products']:
    person_image = '%s&api_key=%s'%(urljoin(api_gateway_url,info['image_location']),
                                    props['X-Api-Key'])
    print('[%1.2f] %s'%(info['similarity'],person_image)) 
```

> Sample json response

```json
{
 "time_ms": "2158.11",
 "bounding_box": {
  "image_location": "/v1/images/61e16e38-bb3e-4779-baf8-748491ca6162.jpe?top_left_x=177&top_left_y=27&width=170&height=541",
  "top_left_x": 177,
  "top_left_y": 27,
  "height": 541,
  "width": 170,
  "score": 0.889102
 },
 "products": [
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346A_5.jpg?top_left_x=54&top_left_y=0&width=154&height=343",
   "similarity": 0.41838812828063965,
   "image_id": "5",
   "bounding_box": {
    "width": 154,
    "score": 0.575364,
    "top_left_x": 54,
    "top_left_y": 0,
    "height": 343
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_filename": "SHRES16AWFSDR9346A_5.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_2.jpg?top_left_x=101&top_left_y=0&width=74&height=303",
   "similarity": 0.404349684715271,
   "image_id": "2",
   "bounding_box": {
    "width": 74,
    "score": 0.307322,
    "top_left_x": 101,
    "top_left_y": 0,
    "height": 303
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/ABOFA15AMCWJG10448_5.jpg?top_left_x=0&top_left_y=0&width=313&height=417",
   "similarity": 0.40286576747894287,
   "image_id": "5",
   "bounding_box": {
    "width": 313,
    "score": 1.0,
    "top_left_x": 0,
    "top_left_y": 0,
    "height": 417
   },
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044801/Front_Medium.jpg",
   "id": "ABOFA15AMCWJG10448",
   "image_filename": "ABOFA15AMCWJG10448_5.jpg"
  }
 ],
 "image_location": "/v1/images/61e16e38-bb3e-4779-baf8-748491ca6162.jpe"
}
```


Get similar looks in the catalog for a user uploaded image.

<aside class="notice">
The looks search index has to be built first before you can use this API.  
</aside>

### End point

`POST /v1/catalog/{catalog_name}/looks_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, 
max_number_of_results | query | maximum number of results to return | 12
print_count | path | The maximum number of dominant prints to search for in an image. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant prints. If your image is of high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant prints. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The image location.
**products** | A sorted list similar images. Each item in the list has the following fields.
id | The product id.
image_id | The image id.
image_filename | The image filename.
image_url | The image url.
similarity | The similarity score.
image_location | The resource location of the bounding box for the detected person.
bounding_box | The bounding box of the dominant person in the image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
**bounding_box** | The bounding box for the detected person. 
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
image_location | The image location of the bounding box.