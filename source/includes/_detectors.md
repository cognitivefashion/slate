# Detectors

A collection of APIs for detecting person, apparel, accessories and footwear in an image.

## Detect persons in an image

```python
#------------------------------------------------------------------------------
# Detect persons in a fashion image.
# POST /v1/detect_person
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
params['model'] = 'fast' # default
#params['model'] = 'accurate'

api_endpoint = '/v1/detect_person'

url = urljoin(api_gateway_url,api_endpoint)

# Three options to pass the image

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_3.jpeg','rb'))

"""
# OPTION 2 : Pass the image url
params['image_url'] = 'http://vg-images.condecdn.net/image/oDXPOxw65EZ/crop/405'
response = requests.post(url,
                         headers=headers,
                         params=params)
"""

"""
# OPTION 3 : using multipart
image_filename = 'test_image_3.jpeg'
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
print('[original image] %s'%(image_location))

for entity in results['persons']:
    entity_swatch = '%s&api_key=%s'%(urljoin(api_gateway_url,entity['image_location']),
                                    props['X-Api-Key'])
    print('[%1.2f][%s]'%(entity['score'],entity_swatch)) 
```

> Sample json response

```json
{
 "time_ms": "1934.55",
 "image_location": "/v1/images/4f394ff2-50fe-482a-a381-8e5f38136b3f.jpe",
 "persons": [
  {
   "image_location": "/v1/images/4f394ff2-50fe-482a-a381-8e5f38136b3f.jpe?top_left_x=41&top_left_y=14&width=184&height=493",
   "top_left_x": 41,
   "top_left_y": 14,
   "height": 493,
   "width": 184,
   "score": 0.737177
  },
  {
   "image_location": "/v1/images/4f394ff2-50fe-482a-a381-8e5f38136b3f.jpe?top_left_x=177&top_left_y=27&width=170&height=541",
   "top_left_x": 177,
   "top_left_y": 27,
   "height": 541,
   "width": 170,
   "score": 0.889102
  }
 ]
}
```

Detect and find the bounding boxes for all persons in an image.

### End point

`POST /v1/detect_person`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
model | query | Model can either be `fast` (faster but slightly less accurate model) or `accurate` (slower but more accurate model). | `fast`

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The image location of the uploaded image.
**persons** |  A list of bounding boxes for all detected persons. Each element of the list contains the following fields.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
image_location | The image location of the detected person.

## Detect apparel in an image

```python
#------------------------------------------------------------------------------
# Detect apparel,accessories and footwear in a fashion image.
# POST /v1/detect_apparel
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
params['threshold'] = 0.2

api_endpoint = '/v1/detect_apparel'

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
params['image_url'] = 'http://vg-images.condecdn.net/image/oDXPOxw65EZ/crop/405'
response = requests.post(url,
                         headers=headers,
                         params=params)
"""

"""
# OPTION 3 : using multipart
image_filename = 'test_image_3.jpeg'
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
print('[original image] %s'%(image_location))

for entity in results['entities']:
    entity_swatch = '%s&api_key=%s'%(urljoin(api_gateway_url,entity['image_location']),
                                    props['X-Api-Key'])
    print('[%1.2f][%s][%s]'%(entity['category']['score'][entity['category']['value']],
                                     entity['category']['value'],
                                     entity_swatch)) 
```

> Sample json response

```json
{
 "time_ms": "259.57",
 "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe",
 "entities": [
  {
   "category": {
    "layer_type": "layer_2_lower_body",
    "score": {
     "pant": 0.5747724771499634
    },
    "product_type": "apparel",
    "value": "pant"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=168&top_left_y=276&width=266&height=609",
   "bounding_box": {
    "width": 266,
    "score": 0.5747724771499634,
    "top_left_x": 168,
    "top_left_y": 276,
    "height": 609
   },
   "id": "1"
  },
  {
   "category": {
    "layer_type": "UNK",
    "score": {
     "person": 0.7402761578559875
    },
    "product_type": "UNK",
    "value": "person"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=175&top_left_y=44&width=254&height=799",
   "bounding_box": {
    "width": 254,
    "score": 0.7402761578559875,
    "top_left_x": 175,
    "top_left_y": 44,
    "height": 799
   },
   "id": "2"
  },
  {
   "category": {
    "layer_type": "footwear",
    "score": {
     "sandal": 0.4261542558670044
    },
    "product_type": "footwear",
    "value": "sandal"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=245&top_left_y=825&width=101&height=59",
   "bounding_box": {
    "width": 101,
    "score": 0.4261542558670044,
    "top_left_x": 245,
    "top_left_y": 825,
    "height": 59
   },
   "id": "3"
  },
  {
   "category": {
    "layer_type": "footwear",
    "score": {
     "sandal": 0.2754097878932953
    },
    "product_type": "footwear",
    "value": "sandal"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=333&top_left_y=835&width=105&height=51",
   "bounding_box": {
    "width": 105,
    "score": 0.2754097878932953,
    "top_left_x": 333,
    "top_left_y": 835,
    "height": 51
   },
   "id": "4"
  },
  {
   "category": {
    "layer_type": "layer_2_upper_body",
    "score": {
     "shirt": 0.31577786803245544
    },
    "product_type": "apparel",
    "value": "shirt"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=192&top_left_y=148&width=186&height=213",
   "bounding_box": {
    "width": 186,
    "score": 0.31577786803245544,
    "top_left_x": 192,
    "top_left_y": 148,
    "height": 213
   },
   "id": "5"
  },
  {
   "category": {
    "layer_type": "UNK",
    "score": {
     "face": 0.8626712560653687
    },
    "product_type": "UNK",
    "value": "face"
   },
   "image_location": "/v1/images/2e4e7b35-b8de-493f-8e90-e59080c65d73.jpe?top_left_x=221&top_left_y=34&width=89&height=96",
   "bounding_box": {
    "width": 89,
    "score": 0.8626712560653687,
    "top_left_x": 221,
    "top_left_y": 34,
    "height": 96
   },
   "id": "6"
  }
 ]
}
```

Detect and find the bounding boxes for all apparel, accessories and footwear in an image.

### End point

`POST /v1/detect_apparel`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
threhold | query |  Threshold for detections, ignore entities whose score is below this threshold value. | 0.2

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The image location of the uploaded image.
**entities** |  A list of detected entities. Each element of the list contains the following fields.
**bounding_box** | The bounding box for the deteted entity containing the following fields.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
**category** | The category of deteted entity containing the following fields.
value | The category label.
score | The confidence score for the category label.
product_type | Can be either `apparel`,`accessories`,`footwear` or `UNK`.
layer_type | The layer type.
**image_location** | The image location of the detected entity.
