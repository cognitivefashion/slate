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

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

params = {}

# Optional parameters.
params['count'] = 3
#params['image_max_dimension'] = 512
params['ignore_background'] = 'true'
params['model'] = 'person_fast'

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
```

> Sample json response

```json
{
 "dominant_prints": [
  {
   "bounding_box": {
    "width": 64,
    "top_left_x": 276,
    "top_left_y": 359,
    "height": 64
   },
   "fraction_boxes": 0.46
  },
  {
   "bounding_box": {
    "width": 64,
    "top_left_x": 285,
    "top_left_y": 244,
    "height": 64
   },
   "fraction_boxes": 0.278
  },
  {
   "bounding_box": {
    "width": 64,
    "top_left_x": 225,
    "top_left_y": 206,
    "height": 64
   },
   "fraction_boxes": 0.262
  }
 ],
 "time_ms": "4023.90",
 "bounding_box": {
  "top_left_x": 158,
  "top_left_y": 0,
  "height": 640,
  "width": 259,
  "score": 0.819546,
  "model": "person_fast"
 }
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

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**dominant_prints** |  A list of dominant prints for the image sorted in the decreasing order of the fraction of boxes. Each field in the list has the following fieldnames.
**bounding_box** | The bounding box of the print swatch.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
fraction_boxes | The fraction of swatches in the bounding box that can be attributed to this print.
**bounding_box** | The region in the image used to compute the dominant prints and corresponds to the the bounding box for the central detected person. In case `ignore_background` is set to `false` this corresponds to the entire image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
model | The person detection model used.

<aside class="warning">
If the person detector is unable to detect any person in the image then we backoff to the person_basic model. 
</aside>