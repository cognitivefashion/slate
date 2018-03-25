# Color Search

A collection of APIs for enabling catalog search based on colors.

## Build color search index

```python
#------------------------------------------------------------------------------
# Build the color search index.
# POST /v1/catalog/{catalog_name}/color_search_index
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
params['color_count'] = 3
params['quality'] = 1
params['image_max_dimension'] = 256
params['ignore_background'] = 'true'
params['model'] = 'person_fast'
params['fraction_pixels_threshold'] = 0.1

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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
  "color_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  },
  "finish_time": "2017-11-18 04:35:03.112859",
  "start_time": "2017-11-18 04:34:51.450504",
  "pid": "224",
  "index_updated": "true",
  "num_of_products": "10",
  "num_of_images": "60",
  "color_search_index": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  }
 },
 "time_ms": "1.97"
}
```

Build the color search index.

Before you can start using the color search APIs the color search index has to be built. This API computes the dominant colors for all the uploaded images in the catalog and then builds an index for fast nearest neighbor retrieval.

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the color search APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/color_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
color_count | query | The maximum number of dominant colors to extract per image. | 3
quality | query | This parameter provides a trade off between the computation time and the quality of the dominant colors. Larger the number faster is the dominant color computation but greater the chance that the colors will be missed. 1 is the highest quality. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant colors. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`
fraction_pixels_threshold | query | Include only those colors whos fraction of pixels is greater than this number. | 0.0
ignore_images_without_person | query |If `true` ignore images where person is not detected. If `false` and person is not detected then defaults to 'person_basic' | `false`      

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_color`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**color_computation** | Info about dominant color computation.
num_of_images_error | The number of images for which there was some error in dominant color computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant colors were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant colors were computed. 
**color_search_index** | Info about the index building.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index by calling this API again. Note that the dominant colors for the old images will still be saved and we will be computing the dominant colors only for the newly added images.
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get color search index status

```python
#------------------------------------------------------------------------------
# Get the status of the color search index.
# GET /v1/catalog/{catalog_name}/color_search_index
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

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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
  "color_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  },
  "finish_time": "2017-11-18 04:35:03.112859",
  "start_time": "2017-11-18 04:34:51.450504",
  "pid": "224",
  "index_updated": "true",
  "num_of_products": "10",
  "num_of_images": "60",
  "color_search_index": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  }
 },
 "time_ms": "1.97"
}
```

Get the status of the color search index.

### End point

`GET /v1/catalog/{catalog_name}/color_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_color`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**color_computation** | Info about dominant color computation.
num_of_images_error | The number of images for which there was some error in dominant color computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant colors were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant colors were computed. 
**color_search_index** | Info about the index building.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the color search index 

```python
#------------------------------------------------------------------------------
# Delete the color search index.
# DELETE /v1/catalog/{catalog_name}/color_search_index
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

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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

Delete the color search index.

### End point

`DELETE /v1/catalog/{catalog_name}/color_search_index`

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

## Color Search by RGB

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?r=255&g=0&b=0m&ax_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a specified RGB value.
# GET /v1/catalog/{catalog_name}/color_search
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

# Parameters.
params['r'] = 255
params['g'] = 0
params['b'] = 0
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "6.13",
 "products": [
  {
   "distance": 7.383520126342773,
   "image_id": "5",
   "rgb": [
    197,
    99,
    71
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_5.jpg"
  },
  {
   "distance": 9.618025779724121,
   "image_id": "1",
   "rgb": [
    196,
    106,
    76
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_1.jpg"
  },
  {
   "distance": 12.06448745727539,
   "image_id": "4",
   "rgb": [
    154,
    46,
    35
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  },
  {
   "distance": 19.470752716064453,
   "image_id": "2",
   "rgb": [
    128,
    41,
    38
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  },
  {
   "distance": 20.736268997192383,
   "image_id": "4",
   "rgb": [
    192,
    126,
    96
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  }
 ]
}
```

Get similar color images in the catalog for a specified RGB value.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
r | query | (**Required**) The value for the red channel in the RGB color model (in the range 0 to 255).
g | query | (**Required**) The value for the green channel in the RGB color model (in the range 0 to 255).
b | query | (**Required**) The value for the blue channel in the RGB color model (in the range 0 to 255).
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.
rgb | The RGB value of the matching color.

## Color Search by name

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?color_term=red&ax_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a specified color name..
# GET /v1/catalog/{catalog_name}/color_search
# params - color_term,max_number_of_results
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
params['color_term'] = 'red' 
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "11.09",
 "products": [
  {
   "distance": 51.287628173828125,
   "image_id": "4",
   "rgb": [
    154,
    46,
    35
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  },
  {
   "distance": 53.020751953125,
   "image_id": "5",
   "rgb": [
    197,
    99,
    71
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_5.jpg"
  },
  {
   "distance": 56.67232894897461,
   "image_id": "1",
   "rgb": [
    196,
    106,
    76
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_1.jpg"
  },
  {
   "distance": 64.99003601074219,
   "image_id": "2",
   "rgb": [
    128,
    41,
    38
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  },
  {
   "distance": 67.47423553466797,
   "image_id": "2",
   "rgb": [
    210,
    138,
    106
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  }
 ]
}
```

Get similar color images in the catalog for a specified color name.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
color_term  | query | (**Required**) The color term (e.g. red).
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.
rgb | The RGB value of the matching color.

## Color Search by id

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?id=SHRES16AWFSDR9346B&image_id=1&color_count=1&max_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for an existing image.
# GET /v1/catalog/{catalog_name}/color_search
# params - id,image_id,color_count,max_number_of_results
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
params['id'] = 'SHRES16AWFSDR9346B' 
params['image_id'] = '1'
params['color_count'] =  1
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "12.31",
 "products": [
  {
   "distance": 0.0,
   "image_id": "1",
   "rgb": [
    196,
    106,
    76
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_1.jpg"
  },
  {
   "distance": 4.095544338226318,
   "image_id": "5",
   "rgb": [
    197,
    99,
    71
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_5.jpg"
  },
  {
   "distance": 12.966226577758789,
   "image_id": "4",
   "rgb": [
    192,
    126,
    96
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  },
  {
   "distance": 13.747275352478027,
   "image_id": "2",
   "rgb": [
    210,
    138,
    106
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_2.jpg"
  },
  {
   "distance": 21.508577346801758,
   "image_id": "4",
   "rgb": [
    154,
    46,
    35
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_filename": "SHRES16AWFSDR9346B_4.jpg"
  }
 ]
}
```

Get similar color images in the catalog for an existing image.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
color_count | path | The maximum number of dominant colors to search for in an image. | 1
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.
rgb | The RGB value of the matching color.

## Color Search by image

```shell
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a given uploaded image.
# POST /v1/catalog/{catalog_name}/color_search
# params - color_count,image_max_dimension,max_number_of_results
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
params['color_count'] =  2
params['max_number_of_results'] = 5

headers['Content-Type'] = 'image/jpeg'

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_2.jpeg','rb'))

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "689.53",
 "products": [
  {
   "distance": 1.9539705514907837,
   "image_id": "4",
   "rgb": [
    48,
    48,
    48
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SH2005/Look_Medium.jpg",
   "id": "SKLTS16AMCWSH8SH20",
   "image_filename": "SKLTS16AMCWSH8SH20_4.jpg"
  },
  {
   "distance": 4.879070281982422,
   "image_id": "5",
   "rgb": [
    45,
    42,
    45
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Right_Medium.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "image_filename": "SKLTS16AMCWSH8SP01_5.jpg"
  },
  {
   "distance": 6.080552577972412,
   "image_id": "2",
   "rgb": [
    42,
    40,
    44
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Front_Medium.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "image_filename": "SKLTS16AMCWSH8SP01_2.jpg"
  },
  {
   "distance": 6.821847438812256,
   "image_id": "3",
   "rgb": [
    40,
    38,
    42
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Left_Medium.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "image_filename": "SKLTS16AMCWSH8SP01_3.jpg"
  },
  {
   "distance": 7.602819442749023,
   "image_id": "4",
   "rgb": [
    36,
    34,
    34
   ],
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Look_Medium.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "image_filename": "SKLTS16AMCWSH8SP01_4.jpg"
  }
 ]
}
```


Get similar color images in the catalog for a given uploaded image.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`POST /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, 
max_number_of_results | query | maximum number of results to return | 12
color_count | path | The maximum number of dominant colors to search for in an image. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your image is of high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing

                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.
rgb | The RGB value of the matching color.