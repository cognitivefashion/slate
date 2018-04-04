# Visual Attributes

Given a fashion image (containing apparel,accessories,or footwear) identify the main product **category** along with the different **attributes**. The category and attributes are grounded in a standardized fashion taxonomy. 

Some use cases for visual attributes include:

- Auto-attribution of catalog images.
- Catalog enrichment for a better text search.
- Post-filtering of visual search results.

<aside class="notice">
We differentiate between two kinds of images. Iconic images are mainly single product images where the image is loosely focused only on the main apparel, footwear or accessory. Non-Iconic are mainly multiple product images where the image contains multiple apparel, footwear and accessories. Non-Iconic images are typically full outfit images whereas iconic images are mainly focused on the main product.
</aside>


## Get visual attributes for iconic/non-iconic fashion images

Given a fashion image (containing apparel,accessories,or footwear) identify the detect differnt product **category** along with the different **attributes**. The attributes predicted shall be consistent with the predicted category, e.g. `sleeve length` won't be predicted for category `jeans`.

```python
#------------------------------------------------------------------------------
# Get visual attributes for a given iconic/non-iconic image.
# GET /v1/visualattributes
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
# Optional parameters.
params['threshold'] = 0.2
#params['image_type'] = 'iconic'
params['image_type'] = 'non-iconic'

api_endpoint = '/v1/visualattributes'

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
image_filename = 'test_image_1.jpeg'
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
    for attribute in entity['attributes']:
        print('[%1.2f][%s][%s]'%(entity['attributes'][attribute]['score'][entity['attributes'][attribute]['value']],
                                 attribute,
                                 entity['attributes'][attribute]['value']))
```


> Sample json response

```json
{
 "time_ms": "2823.04",
 "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe",
 "entities": [
  {
   "category": {
    "score": {
     "palazzo": 0.22113326648841536,
     "salwar": 0.06767382153963641,
     "pant": 0.5747724771499634
    },
    "value": "pant"
   },
   "attributes": {
    "style": {
     "score": {
      "fusion": 0.1323334127664566,
      "maternity": 0.12990465760231018,
      "casual": 0.13776597380638123
     },
     "value": "casual"
    },
    "rise": {
     "score": {
      "mid rise": 0.9471921920776367,
      "high rise": 0.003534807590767741,
      "low rise": 0.04925554618239403
     },
     "value": "mid rise"
    },
    "fabric": {
     "score": {
      "polyester": 0.14030905067920685,
      "rayon": 0.10886745154857635,
      "cotton": 0.3303784728050232
     },
     "value": "cotton"
    },
    "fit": {
     "score": {
      "regular": 0.8877512812614441,
      "slim": 0.05478856340050697,
      "loose": 0.012176275253295898
     },
     "value": "regular"
    },
    "color": {
     "score": {
      "pink": 0.13715988397598267,
      "multicoloured": 0.18392638862133026,
      "red": 0.2667190730571747
     },
     "value": "red"
    },
    "pattern": {
     "score": {
      "solid": 0.8236303329467773,
      "embellished": 0.07058356702327728,
      "embroidered": 0.04244432970881462
     },
     "value": "solid"
    },
    "material": {
     "score": {
      "leather": 0.1788148432970047,
      "polyurethane": 0.8198845982551575,
      "cotton": 0.000506136566400528
     },
     "value": "polyurethane"
    },
    "age_gender": {
     "score": {
      "boys": 0.021265005692839622,
      "girls": 0.28132501244544983,
      "women": 0.6916653513908386
     },
     "value": "women"
    },
    "length": {
     "score": {
      "ankle length": 0.06449545174837112,
      "full length": 0.8689517974853516,
      "regular length": 0.05531088262796402
     },
     "value": "full length"
    }
   },
   "bounding_box": {
    "width": 292,
    "score": 0.5747724771499634,
    "top_left_x": 155,
    "top_left_y": 246,
    "height": 642
   },
   "id": "1",
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=155&top_left_y=246&width=292&height=642"
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
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=163&top_left_y=5&width=278&height=877",
   "bounding_box": {
    "width": 278,
    "score": 0.7402761578559875,
    "top_left_x": 163,
    "top_left_y": 5,
    "height": 877
   },
   "id": "2"
  },
  {
   "category": {
    "score": {
     "sandal": 0.4261542558670044,
     "sandals": 0.3518989313999086,
     "boots": 0.054356454120087605
    },
    "value": "sandal"
   },
   "attributes": {
    "heel height": {
     "score": {
      "medium: 2.5-3.5 inch": 1.0,
      "low: 1.5-2.5 inch": 2.6993671187192092e-11,
      "high: above 3.5 inch": 1.1122387733042416e-11
     },
     "value": "medium: 2.5-3.5 inch"
    }
   },
   "bounding_box": {
    "width": 111,
    "score": 0.4261542558670044,
    "top_left_x": 240,
    "top_left_y": 823,
    "height": 63
   },
   "id": "3",
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=240&top_left_y=823&width=111&height=63"
  },
  {
   "category": {
    "score": {
     "sandal": 0.2754097878932953,
     "pumps": 0.09048446886142614,
     "sandals": 0.13116798881638747
    },
    "value": "sandal"
   },
   "attributes": {
    "heel height": {
     "score": {
      "medium: 2.5-3.5 inch": 7.981193630257621e-05,
      "low: 1.5-2.5 inch": 5.3353110334396475e-15,
      "high: above 3.5 inch": 0.999920129776001
     },
     "value": "high: above 3.5 inch"
    }
   },
   "bounding_box": {
    "width": 115,
    "score": 0.2754097878932953,
    "top_left_x": 328,
    "top_left_y": 833,
    "height": 55
   },
   "id": "4",
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=328&top_left_y=833&width=115&height=55"
  },
  {
   "category": {
    "score": {
     "blouse": 0.06075274238587225,
     "sweater": 0.05060515634194118,
     "shirt": 0.31577786803245544
    },
    "value": "shirt"
   },
   "attributes": {
    "style": {
     "score": {
      "party": 0.007760034408420324,
      "casual": 0.9074917435646057,
      "formal": 0.07398669421672821
     },
     "value": "casual"
    },
    "rise": {
     "score": {
      "mid rise": 0.9999979734420776,
      "high rise": 1.1497794903669867e-11,
      "low rise": 1.994056219700724e-06
     },
     "value": "mid rise"
    },
    "fabric": {
     "score": {
      "polyester": 0.10793536901473999,
      "blend": 0.06727375090122223,
      "cotton": 0.4398740828037262
     },
     "value": "cotton"
    },
    "fit": {
     "score": {
      "relaxed": 0.011904584243893623,
      "regular": 0.8485410213470459,
      "slim": 0.11031126230955124
     },
     "value": "regular"
    },
    "color": {
     "score": {
      "pink": 0.2812502682209015,
      "green": 0.1360998898744583,
      "yellow": 0.07928851246833801
     },
     "value": "pink"
    },
    "pattern": {
     "score": {
      "solid": 0.881832480430603,
      "printed": 0.044556956738233566,
      "textured": 0.029497725889086723
     },
     "value": "solid"
    },
    "sleeve style": {
     "score": {
      "cap sleeves": 0.9173927307128906,
      "roll up sleeves": 0.08121675252914429,
      "puff sleeves": 0.0013715812237933278
     },
     "value": "cap sleeves"
    },
    "age_gender": {
     "score": {
      "men": 0.018686337396502495,
      "girls": 0.058831632137298584,
      "women": 0.9153407216072083
     },
     "value": "women"
    },
    "length": {
     "score": {
      "waist length": 0.09034832566976547,
      "thigh length": 0.020926641300320625,
      "hip length": 0.8840341567993164
     },
     "value": "hip length"
    },
    "sleeve length": {
     "score": {
      "short sleeves": 0.0034761312417685986,
      "3/4th sleeves": 0.0034854754339903593,
      "long sleeves": 0.9906822443008423
     },
     "value": "long sleeves"
    },
    "neck": {
     "score": {
      "v neck": 0.2768712639808655,
      "round neck": 0.11630632728338242,
      "polo neck": 0.21823753416538239
     },
     "value": "v neck"
    }
   },
   "bounding_box": {
    "width": 204,
    "score": 0.31577786803245544,
    "top_left_x": 183,
    "top_left_y": 138,
    "height": 233
   },
   "id": "5",
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=183&top_left_y=138&width=204&height=233"
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
   "image_location": "/v1/images/23e67de5-1ac0-487d-9e7f-619e91e685a7.jpe?top_left_x=217&top_left_y=30&width=97&height=104",
   "bounding_box": {
    "width": 97,
    "score": 0.8626712560653687,
    "top_left_x": 217,
    "top_left_y": 30,
    "height": 104
   },
   "id": "6"
  }
 ]
}
```

### End point

`GET /v1/visualattributes`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
threshold | query |  Threshold for detections, ignore entities whose score is below this threshold value. | 0.2
iamge_type | query |  Can be either `iconic` or `non-iconic`. | `iconic`

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response

Parameter |  Description
--------- |  -----------
time_ms |  The time taken for prediction in milliseconds.
**entities** |  A list of detected entities. Each element of the list contains the following fields.
**bounding_box** | The bounding box for the deteted entity containing the following fields.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score.
**category** |  The top-3 product categories predicted along with their respective confidence levels and the final predicted category.
**attributes** | The attributes and their top-3 values predicted along with the respective confidence levels. 
**image_location** | The image location of the detected entity

