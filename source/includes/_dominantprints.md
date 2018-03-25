# Dominant Prints

A collection of APIs for computing the dominant prints for an user uploaded image, the entire catalog, and any image in the catalog.

## Get dominant prints for an image

```python
#------------------------------------------------------------------------------
# Dominant prints for an image. 
# POST /v1/prints/dominant_prints
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

params = {}
# Optional parameters.
params['count'] = 3
#params['image_max_dimension'] = 512
params['ignore_background'] = 'true'
params['model'] = 'person_fast'
#params['fraction_boxes_threshold'] = 0.3

api_endpoint = '/v1/prints/dominant_prints'

url = urljoin(api_gateway_url,api_endpoint)

# Three options to pass the image

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_2.jpeg','rb'))

"""          
# OPTION 2 : Pass the image url
params['image_url'] = 'https://vg-images.condecdn.net/image/oDXPOxw65EZ/crop/405'
response = requests.post(url,
                         headers=headers,


# OPTION 3 : using multipart
image_filename = 'test_image_2.jpeg'
with open(image_filename,'rb') as images_file:
    response = requests.post(url,
                             headers=headers,
                             params=params,
                             files={'image': (image_filename,images_file,'image/jpeg')})   
""" 

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

for print_info in results['dominant_prints']:
    print_swatch = '%s&api_key=%s'%(urljoin(api_gateway_url,print_info['image_location']),
                                    props['X-Api-Key'])
    print('[dominant prints] %1.2f %s'%(print_info['fraction_boxes'],print_swatch)) 
```

> Sample json response

```json

 "dominant_prints": [
  {
   "image_location": "/v1/images/2568a1e8-1993-40e0-9d5f-78c33666e876.jpe?top_left_x=256&top_left_y=402&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 256,
    "top_left_y": 402,
    "height": 64
   },
   "fraction_boxes": 0.388,
   "print_id": "2"
  },
  {
   "image_location": "/v1/images/2568a1e8-1993-40e0-9d5f-78c33666e876.jpe?top_left_x=239&top_left_y=372&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 239,
    "top_left_y": 372,
    "height": 64
   },
   "fraction_boxes": 0.332,
   "print_id": "1"
  },
  {
   "image_location": "/v1/images/2568a1e8-1993-40e0-9d5f-78c33666e876.jpe?top_left_x=225&top_left_y=210&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 225,
    "top_left_y": 210,
    "height": 64
   },
   "fraction_boxes": 0.28,
   "print_id": "3"
  }
 ],
 "time_ms": "2136.32",
 "bounding_box": {
  "image_location": "/v1/images/2568a1e8-1993-40e0-9d5f-78c33666e876.jpe?top_left_x=158&top_left_y=0&width=259&height=640",
  "top_left_x": 158,
  "top_left_y": 0,
  "height": 640,
  "width": 259,
  "score": 0.819546,
  "model": "person_fast"
 },
 "image_location": "/v1/images/2568a1e8-1993-40e0-9d5f-78c33666e876.jpe"
}
```

Get the dominant prints for an image.

### End point

`POST /v1/prints/dominant_prints`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
count | query | The number of dominant prints to extract per image. | 3
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant prints. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant prints. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`   
fraction_boxes_threshold | query  | Include only those prints whos fraction of boxes is greater than this number. |  0.0

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_url | The image url.
image_location | The image location.
**dominant_prints** |  A list of dominant prints for the image sorted in the decreasing order of the fraction of boxes. Each field in the list has the following fieldnames.
**bounding_box** | The bounding box of the print swatch.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
fraction_boxes | The fraction of swatches in the bounding box that can be attributed to this print.
print_id | The print id.
image_location | The image location of the print swatch.
**bounding_box** | The region in the image used to compute the dominant prints and corresponds to the the bounding box for the central detected person. In case `ignore_background` is set to `false` this corresponds to the entire image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
model | The person detection model used.
image_location | The image location of the bounding box.

<aside class="warning">
If the person detector is unable to detect any person in the image then we backoff to the person_basic model. 
</aside>

## Build dominant prints index

```python
#------------------------------------------------------------------------------
# Build the dominant prints index.
# POST /v1/catalog/{catalog_name}/dominant_prints_index
# params - prints,max_prints_per_image,fraction_boxes_threshold
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
params['prints'] = 10
params['max_prints_per_image'] = 2
params['fraction_boxes_threshold'] = 0.1

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_prints_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "dominant_prints": {
   "num_of_images_missing_prints": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "status": "done",
  "num_of_products": "8",
  "results_updated": "true",
  "start_time": "2018-03-25 18:01:16.941760",
  "pid": "218",
  "finish_time": "2018-03-25 18:01:17.188224",
  "num_of_images": "38"
 },
 "time_ms": "2.31"
}
```

Build the dominant prints index.

This loads all the dominant prints for every image in the catalog and then clusters the prints to compute the dominant prints for the entire catalog. Before you can start querying for the dominant prints you need to call this API to start the clustering procedure. 

<aside class="notice">
Prior to calling this API the prints search index has to be built, which computes the dominant prints for all the images. 
</aside>

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the dominant prints APIs. You can query the status of the index creation using the GET endpoint. The clusteing is completed when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/dominant_prints_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
prints | query | The number of dominant prints to compute. | 10
max_prints_per_image | query | The maximum number of dominant prints to use per image. | all available prints
fraction_boxes_threshold | query | Include only those prints whos fraction of boxes is greater than this number. | 0.0

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
**dominant_prints** | Info about dominant color.
num_of_images_missing_prints | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get dominant prints index status

```python
#------------------------------------------------------------------------------
# Get the status of the dominant prints index.
# GET /v1/catalog/{catalog_name}/dominant_prints_index
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

api_endpoint = '/v1/catalog/%s/dominant_prints_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "dominant_prints": {
   "num_of_images_missing_prints": "0",
   "num_of_images_indexed": "28",
   "num_of_images_ignored": "10"
  },
  "status": "done",
  "num_of_products": "8",
  "results_updated": "true",
  "start_time": "2018-03-25 18:01:16.941760",
  "pid": "218",
  "finish_time": "2018-03-25 18:01:17.188224",
  "num_of_images": "38"
 },
 "time_ms": "2.31"
}
```

Get the status of the dominant prints index.

### End point

`GET /v1/catalog/{catalog_name}/dominant_prints_index`

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
**dominant_prints** | Info about dominant color.
num_of_images_missing_prints | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the dominant prints index 

```python
#------------------------------------------------------------------------------
# Delete the dominant prints index.
# DELETE /v1/catalog/{catalog_name}/dominant_prints_index
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

api_endpoint = '/v1/catalog/%s/dominant_prints_index'%(catalog_name)

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

Delete the dominant prints index.

### End point

`DELETE /v1/catalog/{catalog_name}/dominant_prints_index`

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

## Get dominant prints for a product

```python
#------------------------------------------------------------------------------
# Get the dominant prints for an image in the catalog.
# GET /v1/catalog/{catalog_name}/dominant_prints/{id}/{image_id}
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
#params['fraction_boxes_threshold'] = 0.1

# Path parameters
catalog_name = props['catalog_name']
id ='SHRES16AWFSDR9346B'
image_id = '1'

api_endpoint = '/v1/catalog/%s/dominant_prints/%s/%s'%(catalog_name,id,image_id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())

# Human friendly repsonse.

results = response.json()

print('[image url      ] %s'%(results['image_url']))

image_location = '%s?api_key=%s'%(urljoin(api_gateway_url,results['image_location']),
                                  props['X-Api-Key'])
print('[original image ] %s'%(image_location))

image_location = '%s&api_key=%s'%(urljoin(api_gateway_url,results['bounding_box']['image_location']),
                                  props['X-Api-Key'])
print('[bounding box   ] %s'%(image_location))

for print_info in results['dominant_prints']:
    print_swatch = '%s&api_key=%s'%(urljoin(api_gateway_url,print_info['image_location']),
                                    props['X-Api-Key'])
    print('[dominant prints] %1.2f %s'%(print_info['fraction_boxes'],print_swatch)) 
```

> Sample json response

```json
{
 "dominant_prints": [
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_1.jpg?top_left_x=104&top_left_y=136&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 104,
    "top_left_y": 136,
    "height": 64
   },
   "fraction_boxes": 0.528,
   "print_id": "3"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_1.jpg?top_left_x=104&top_left_y=194&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 104,
    "top_left_y": 194,
    "height": 64
   },
   "fraction_boxes": 0.26,
   "print_id": "1"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_1.jpg?top_left_x=104&top_left_y=243&width=64&height=64",
   "bounding_box": {
    "width": 64,
    "top_left_x": 104,
    "top_left_y": 243,
    "height": 64
   },
   "fraction_boxes": 0.212,
   "print_id": "2"
  }
 ],
 "time_ms": "2.71",
 "bounding_box": {
  "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_1.jpg?top_left_x=83&top_left_y=0&width=107&height=365",
  "top_left_x": 83,
  "top_left_y": 0,
  "height": 365,
  "width": 107,
  "score": 0.800896
 },
 "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
 "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_1.jpg"
}
```

Get the dominant prints for an image in the catalog.

<aside class="notice">
Prior to calling this API the dominant prints index has to be built.
</aside>

### End point

`GET /v1/catalog/{catalog_name}/dominant_prints/{id}/{image_id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
fraction_boxes_threshold | query  | Include only those prints whos fraction of boxes is greater than this number. |  0.0

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_url | The image url.
image_location | The image location.
**dominant_prints** |  A list of dominant prints for the image sorted in the decreasing order of the fraction of boxes. Each field in the list has the following fieldnames.
**bounding_box** | The bounding box of the print swatch.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
fraction_boxes | The fraction of swatches in the bounding box that can be attributed to this print.
print_id | The print id.
image_location | The image location of the print swatch.
**bounding_box** | The region in the image used to compute the dominant prints and corresponds to the the bounding box for the central detected person. In case `ignore_background` is set to `false` this corresponds to the entire image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
model | The person detection model used.
image_location | The image location of the bounding box.

## Get dominant prints for a catalog

```python
#------------------------------------------------------------------------------
# Get the dominant prints for all images in the catalog.
# GET /v1/catalog/{catalog_name}/dominant_prints
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
params['prints'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_prints'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

# Here is how you can serve the print swatches
results = response.json()
for print_info in results['dominant_prints']:
    print_swatch = '%s&api_key=%s'%(urljoin(api_gateway_url,print_info['image_location']),
                                    props['X-Api-Key'])
    print('[print swatch] %s'%(print_swatch)) 
```

> Sample json response

```json
{
 "computed_on": "2018-03-25 18:01:17.184551",
 "time_ms": "2.40",
 "dominant_prints": [
  {
   "image_location": "/v1/catalog/sample_catalog/images/ABOFA15AMCWJG10448_3.jpg?top_left_x=128&top_left_y=147&width=64&height=64",
   "popularity": 0.25,
   "image_id": "3",
   "bounding_box": {
    "width": 64,
    "top_left_x": 128,
    "top_left_y": 147,
    "height": 64
   },
   "n_instances": 14,
   "image_url": "http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044801/Left_Medium.jpg",
   "image_filename": "ABOFA15AMCWJG10448_3.jpg",
   "id": "ABOFA15AMCWJG10448",
   "print_id": "1"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SP01_3.jpg?top_left_x=104&top_left_y=133&width=64&height=64",
   "popularity": 0.14285714285714285,
   "image_id": "3",
   "bounding_box": {
    "width": 64,
    "top_left_x": 104,
    "top_left_y": 133,
    "height": 64
   },
   "n_instances": 8,
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Left_Medium.jpg",
   "image_filename": "SKLTS16AMCWSH8SP01_3.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "print_id": "2"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SP01_5.jpg?top_left_x=136&top_left_y=198&width=64&height=64",
   "popularity": 0.10714285714285714,
   "image_id": "5",
   "bounding_box": {
    "width": 64,
    "top_left_x": 136,
    "top_left_y": 198,
    "height": 64
   },
   "n_instances": 6,
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SP0150/Right_Medium.jpg",
   "image_filename": "SKLTS16AMCWSH8SP01_5.jpg",
   "id": "SKLTS16AMCWSH8SP01",
   "print_id": "1"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SHRES16AWFSDR9346B_5.jpg?top_left_x=121&top_left_y=149&width=64&height=64",
   "popularity": 0.10714285714285714,
   "image_id": "5",
   "bounding_box": {
    "width": 64,
    "top_left_x": 121,
    "top_left_y": 149,
    "height": 64
   },
   "n_instances": 6,
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "id": "SHRES16AWFSDR9346B",
   "print_id": "3"
  },
  {
   "image_location": "/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SH20_1.jpg?top_left_x=117&top_left_y=186&width=64&height=64",
   "popularity": 0.08928571428571429,
   "image_id": "1",
   "bounding_box": {
    "width": 64,
    "top_left_x": 117,
    "top_left_y": 186,
    "height": 64
   },
   "n_instances": 5,
   "image_url": "http://images.abofcdn.com/catalog/images/2016/SKLTS16AMCWSH8SH2005/Right_Medium.jpg",
   "image_filename": "SKLTS16AMCWSH8SH20_1.jpg",
   "id": "SKLTS16AMCWSH8SH20",
   "print_id": "1"
  }
 ]
}
```

Get the dominant prints for all images in the catalog.

The dominant prints is to be interpreted as a representative of the cluster of similar prints.

<aside class="notice">
Prior to calling this API the dominant prints index has to be built.
</aside>

### End point

`GET /v1/catalog/{catalog_name}/dominant_prints`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
prints | query | The number of dominant prints to return. | 10

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_url | The image url.
image_location | The image location.
**dominant_prints** |  A list of dominant prints for the catalog sorted in the decreasing order of popularity. Each field in the list has the following fieldnames.
id | The product id.
image_id | The image id.
print_id | The print id.
image_url | The image url.
image_filename | The image filename.
image_location | The image location of the print swatch.
popularity | A score between 0 and 1 indicating the popularity of the print.
n_instances | The number of prints in the cluster.
**bounding_box** | The bounding box of the print swatch.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.




