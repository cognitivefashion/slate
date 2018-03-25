# Visual Attributes

Given a fashion image (containing apparel,accessories,or footwear) identify the main product **category** along with the different **attributes**. The category and attributes are grounded in a standardized fashion taxonomy. 

Some use cases for visual attributes include:

- Auto-attribution of catalog images.
- Catalog enrichment for a better text search.
- Post-filtering of visual search results.

<aside class="notice">
We differentiate between two kinds of images. Iconic images are mainly single product images where the image is loosely focused only on the main apparel, footwear or accessory. Non-Iconic are mainly multiple product images where the image contains multiple apparel, footwear and accessories. Non-Iconic images are typically full outfit images whereas iconic images are mainly focused on the main product.
</aside>


## Get visual attributes for iconic images

Given a iconic fashion image (containing apparel,accessories,or footwear) identify the main product **category** along with the different **attributes**. The attributes predicted shall be consistent with the predicted category, e.g. `sleeve length` won't be predicted for category `jeans`.

```python
#------------------------------------------------------------------------------
# Get visual attributes for a given iconic image.
# GET /v1/visualattributes/iconic
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

api_endpoint = '/v1/visualattributes/iconic'

url = urljoin(api_gateway_url,api_endpoint)

# Three options to pass the image

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_1.jpeg','rb'))

"""            
# OPTION 2 : Pass the image url
params['image_url'] = 'http://vg-images.condecdn.net/image/oDXPOxw65EZ/crop/405'
response = requests.post(url,
                         headers=headers,
                         params=params)
"""

"""
# OPTION 3 : using multipart
image_filename = 'test_image_1.jpeg'
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
  "time_ms": "570.49",
 "category": {
  "score": {
   "stilettos": 0.7297826409339905,
   "sandals": 0.22344675660133362,
   "pumps": 0.02747255750000477
  },
  "value": "stilettos"
 },
 "attributes": {
  "fit": {
   "score": {
    "skinny": 0.011083225719630718,
    "regular": 0.947587251663208,
    "slim": 0.024282250553369522
   },
   "value": "regular"
  },
  "color": {
   "score": {
    "golden": 0.08500122278928757,
    "black": 0.5809345245361328,
    "silver": 0.10020259022712708
   },
   "value": "black"
  },
  "age_gender": {
   "score": {
    "men": 8.310367149988451e-08,
    "girls": 9.555650493098256e-09,
    "women": 0.9999998807907104
   },
   "value": "women"
  },
  "heel height": {
   "score": {
    "medium: 2.5-3.5 inch": 0.3852241635322571,
    "low: 1.5-2.5 inch": 0.008176162838935852,
    "high: above 3.5 inch": 0.6064814925193787
   },
   "value": "high: above 3.5 inch"
  },
  "heel shape": {
   "score": {
    "stiletto": 0.9994674324989319,
    "block": 0.00012550147948786616,
    "kitten": 0.0003877997223753482
   },
   "value": "stiletto"
  },
  "tip shape": {
   "score": {
    "pointed": 0.20216183364391327,
    "open": 0.6662291884422302,
    "round": 0.1315946727991104
   },
   "value": "open"
  },
  "ankle height": {
   "score": {
    "calf length": 0.014615225605666637,
    "low ankle": 0.003991052508354187,
    "ankle length": 0.9804659485816956
   },
   "value": "ankle length"
  },
  "closing": {
   "score": {
    "buckle": 0.26762887835502625,
    "slip on": 0.4985831677913666,
    "zip": 0.145882248878479
   },
   "value": "slip on"
  }
 }
}
```

### End point

`GET /v1/visualattributes/iconic`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response

Parameter |  Description
--------- |  -----------
time_ms |  The time taken for prediction in milliseconds.
category | The top-3 product categories predicted along with their respective confidence levels and the final predicted category.
attributes | The attributes and their top-3 values predicted along with the respective confidence levels. 