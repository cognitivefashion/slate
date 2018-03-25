# Person

## Detect persons in an image

```python
#------------------------------------------------------------------------------
# Person detection.
# POST /v1/person
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
params['model'] = 'fast' # default
#params['model'] = 'accurate'

api_endpoint = '/v1/person'

url = urljoin(api_gateway_url,api_endpoint)

# Three options to pass the image

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_4.jpeg','rb'))

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
```

> Sample json response

```json
{
 "time_ms": "3606.45",
 "persons": [
  {
   "width": 184,
   "score": 0.737177,
   "top_left_x": 41,
   "top_left_y": 14,
   "height": 493
  },
  {
   "width": 170,
   "score": 0.889102,
   "top_left_x": 177,
   "top_left_y": 27,
   "height": 541
  }
 ]
}
```

Detect and find the bounding boxes for all persons in an image.

### End point

`POST /v1/person`

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
**persons** |  A list of bounding boxes for all detected persons. Each element of the list contains the following fields.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
