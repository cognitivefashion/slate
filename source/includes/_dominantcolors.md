# Dominant Colors

A collection of APIs for computing the dominant colors for an user uploaded image, the entire catalog, and any image in the catalog.

## Get dominant colors for an image

```python
#------------------------------------------------------------------------------
# Dominant Colors for an user uploaded image. 
# POST /v1/colors/dominant_colors
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
params['color_count'] = 5
params['quality'] = 1
#params['image_max_dimension'] = 256
params['ignore_background'] = 'true'
params['model'] = 'person_accurate'
#params['fraction_pixels_threshold'] = 0.1

api_endpoint = '/v1/colors/dominant_colors'

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
"""

"""
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

for color_info in results['dominant_colors']:
    print('[dominant colors] %s - %1.2f - %s - %s - %s - %s'%(color_info['hex'],
                                  color_info['fraction_pixels'],
                                  color_info['name'],
                                  color_info['entrylevel_name'],
                                  color_info['universal_name'],
                                  color_info['pantone_id']))
```

> Sample json response

```json
{
 "dominant_colors": [
  {
   "name": "tart orange",
   "pantone_rgb": [
    243,
    91,
    83
   ],
   "pantone_id": "pantone 17-1656 tcx hot coral",
   "hex": "#f5594b",
   "universal_name": "red",
   "rgb": [
    245,
    89,
    75
   ],
   "hsv": [
    4.941176470588232,
    0.6938775510204082,
    0.9607843137254902
   ],
   "pantone_code": "17-1656 tcx",
   "fraction_pixels": 0.5328356594179379,
   "pantone_name": "hot coral",
   "entrylevel_name": "tomato"
  },
  {
   "name": "pink lace",
   "pantone_rgb": [
    237,
    208,
    221
   ],
   "pantone_id": "pantone 12-2905 tcx cradle pink",
   "hex": "#fae0f3",
   "universal_name": "pink",
   "rgb": [
    250,
    224,
    243
   ],
   "hsv": [
    316.1538461538462,
    0.10399999999999998,
    0.9803921568627451
   ],
   "pantone_code": "12-2905 tcx",
   "fraction_pixels": 0.23604890187168667,
   "pantone_name": "cradle pink",
   "entrylevel_name": "pink"
  },
  {
   "name": "light hot pink",
   "pantone_rgb": [
    233,
    173,
    202
   ],
   "pantone_id": "pantone 14-2710 tcx lilac sachet",
   "hex": "#fbafe3",
   "universal_name": "pink",
   "rgb": [
    251,
    175,
    227
   ],
   "hsv": [
    318.94736842105266,
    0.3027888446215139,
    0.984313725490196
   ],
   "pantone_code": "14-2710 tcx",
   "fraction_pixels": 0.22574921562263334,
   "pantone_name": "lilac sachet",
   "entrylevel_name": "pink"
  },
  {
   "name": "bubblegum",
   "pantone_rgb": [
    230,
    121,
    142
   ],
   "pantone_id": "pantone 16-1723 tcx confetti",
   "hex": "#e37687",
   "universal_name": "pink",
   "rgb": [
    227,
    118,
    135
   ],
   "hsv": [
    350.64220183486236,
    0.48017621145374445,
    0.8901960784313725
   ],
   "pantone_code": "16-1723 tcx",
   "fraction_pixels": 0.0027913015254787408,
   "pantone_name": "confetti",
   "entrylevel_name": "pink"
  },
  {
   "name": "light salmon pink",
   "pantone_rgb": [
    231,
    139,
    144
   ],
   "pantone_id": "pantone 16-1720 tcx strawberry ice",
   "hex": "#e89694",
   "universal_name": "pink",
   "rgb": [
    232,
    150,
    148
   ],
   "hsv": [
    1.4285714285714448,
    0.3620689655172413,
    0.9098039215686274
   ],
   "pantone_code": "16-1720 tcx",
   "fraction_pixels": 0.0025749215622633344,
   "pantone_name": "strawberry ice",
   "entrylevel_name": "pink"
  }
 ],
 "time_ms": "13072.34",
 "bounding_box": {
  "image_location": "/v1/images/dc3a108d-c4fe-4d15-80fa-1f66518b21e6.jpe?top_left_x=165&top_left_y=24&width=260&height=842",
  "top_left_x": 165,
  "top_left_y": 24,
  "height": 842,
  "width": 260,
  "score": 0.841905,
  "model": "person_accurate"
 },
 "image_location": "/v1/images/dc3a108d-c4fe-4d15-80fa-1f66518b21e6.jpe"
}
```

Get the dominant colors for an image.

### End point

`POST /v1/colors/dominant_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
color_count | query | The maximum number of dominant colors to extract per image. | 3
quality | query | This parameter provides a trade off between the computation time and the quality of the dominant colors. Larger the number faster is the dominant color computation but greater the chance that the colors will be missed. 1 is the highest quality. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
ignore_background | query | By default we use the entire image to compute the dominant colors. If we set this parameter to `true` we internally use some algorithms to ignore the background and mainly focus on the person or apparel. | `false`
model | query | The model to use to ignore the background. Currently we use person detecors to focus only on the person. The following options are supported `person_fast` (faster but slightly less accurate model), `person_accurate` (slower but more accurate model) and `person_basic` (a basic person detector using standard body proportions). | `person_fast`   
fraction_pixels_threshold | query  | Include only those colors whos fraction of pixels is greater than this number. |  0.0

Instead of the posting the image data you can also pass an image url.

Parameter | Type | Description 
--------- | ------- | ----------- 
image_url | query | (**Required**) The image url.

You can also pass the image using multipart/form-data with the fieldname `image`.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The uploaded image location.
**dominant_colors** |  A list of dominant colors for the image sorted in the decreasing order of the fraction of pixels. Each field in the list has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors available in our color taxonomy.
fraction_pixels | The fraction of pixels in the bounding box that can be attributed to this color.
**bounding_box** | The region in the image used to compute the dominant colors and corresponds to the the bounding box for the central detected person. In case `ignore_background` is set to `false` this corresponds to the entire image.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.
model | The person detection model used.

<aside class="warning">
If the person detector is unable to detect any person in the image then we backoff to the person_basic model. 
</aside>

## Build dominant colors index

```python
#------------------------------------------------------------------------------
# Build the dominant colors index.
# POST /v1/catalog/{catalog_name}/dominant_colors_index
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
params['colors'] = 20
params['max_colors_per_image'] = 2
params['fraction_pixels_threshold'] = 0.1

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_colors_index'%(catalog_name)

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
  "num_of_products": "10",
  "results_updated": "true",
  "start_time": "2017-11-18 17:10:00.141319",
  "pid": "379",
  "dominant_colors": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  },
  "finish_time": "2017-11-18 17:10:05.956428",
  "num_of_images": "60"
 },
 "time_ms": "2.51"
}
```

Build the dominant colors index.

This loads all the dominant colors for every image in the catalog and then clusters the colors to compute the dominant colors for the entire catalog. Before you can start querying for the dominant colors you need to call this API to start the clustering procedure. 

<aside class="notice">
Prior to calling this API the colors search index has to be built, which computes the dominant colors for all the images. 
</aside>

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the index creation is completed to start using the dominant colors APIs. You can query the status of the index creation using the GET endpoint. The clusteing is completed when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/dominant_colors_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
colors | query | The number of dominant colors to compute. | 10
max_colors_per_image | query | The maximum number of dominant colors to use per image. | all available colors
fraction_pixels_threshold | query | Include only those colors whos fraction of pixels is greater than this number. | 0.0

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
**dominant_colors** | Info about dominant color.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get dominant colors index status

```python
#------------------------------------------------------------------------------
# Get the status of the dominant colors index.
# GET /v1/catalog/{catalog_name}/dominant_colors_index
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

api_endpoint = '/v1/catalog/%s/dominant_colors_index'%(catalog_name)

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
  "num_of_products": "10",
  "results_updated": "true",
  "start_time": "2017-11-18 17:10:00.141319",
  "pid": "379",
  "dominant_colors": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  },
  "finish_time": "2017-11-18 17:10:05.956428",
  "num_of_images": "60"
 },
 "time_ms": "2.51"
}
```

Get the status of the dominant colors index.

### End point

`GET /v1/catalog/{catalog_name}/dominant_colors_index`

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
**dominant_colors** | Info about dominant color.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the dominant colors index 

```python
#------------------------------------------------------------------------------
# Delete the dominant colors index.
# DELETE /v1/catalog/{catalog_name}/dominant_colors_index
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

api_endpoint = '/v1/catalog/%s/dominant_colors_index'%(catalog_name)

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

Delete the dominant colors index.

### End point

`DELETE /v1/catalog/{catalog_name}/dominant_colors_index`

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

## Get dominant colors for a catalog

```python
#------------------------------------------------------------------------------
# Get the dominant colors for all images in the catalog.
# GET /v1/catalog/{catalog_name}/dominant_colors
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
params['colors'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/dominant_colors'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{u'computed_on': u'2018-03-25 13:08:05.714292',
 u'dominant_colors': [{u'entrylevel_name': u'black',
                       u'hex': u'#282629',
                       u'hsv': [279.99999999999994,
                                0.07317073170731714,
                                0.1607843137254902],
                       u'lab': [15.4738227287183,
                                1.643636143861621,
                                -1.6514882578694712],
                       u'n_instances': 4,
                       u'name': u'raisin black',
                       u'pantone_code': u'19-3911 tcx',
                       u'pantone_id': u'pantone 19-3911 tcx black beauty',
                       u'pantone_name': u'black beauty',
                       u'pantone_rgb': [38, 38, 42],
                       u'popularity': 0.10810810810810811,
                       u'popularity_percentile': 100.0,
                       u'rgb': [40, 38, 41],
                       u'universal_name': u'black'},
                      {u'entrylevel_name': u'tan',
                       u'hex': u'#cdb3a2',
                       u'hsv': [23.720930232558146,
                                0.20975609756097569,
                                0.803921568627451],
                       u'lab': [74.716930640067,
                                6.646359569893512,
                                12.110173948868752],
                       u'n_instances': 3,
                       u'name': u'rose dust',
                       u'pantone_code': u'14-1307 tcx',
                       u'pantone_id': u'pantone 14-1307 tcx rose dust',
                       u'pantone_name': u'rose dust',
                       u'pantone_rgb': [205, 178, 165],
                       u'popularity': 0.08108108108108109,
                       u'popularity_percentile': 87.5,
                       u'rgb': [205, 179, 162],
                       u'universal_name': u'pink'},
                      {u'entrylevel_name': u'silver',
                       u'hex': u'#cfcccc',
                       u'hsv': [0.0,
                                0.01449275362318836,
                                0.8117647058823529],
                       u'lab': [82.27695306528122,
                                1.0314644455792221,
                                0.3580088239849344],
                       u'n_instances': 3,
                       u'name': u'timberwolf',
                       u'pantone_code': u'13-4104 tcx',
                       u'pantone_id': u'pantone 13-4104 tcx antarctica',
                       u'pantone_name': u'antarctica',
                       u'pantone_rgb': [198, 197, 198],
                       u'popularity': 0.08108108108108109,
                       u'popularity_percentile': 87.5,
                       u'rgb': [207, 204, 204],
                       u'universal_name': u'white'},
                      {u'entrylevel_name': u'off white',
                       u'hex': u'#ebe5e5',
                       u'hsv': [0.0,
                                0.025531914893616947,
                                0.9215686274509803],
                       u'lab': [91.396105013505,
                                2.0227459343862275,
                                0.7108750672036868],
                       u'n_instances': 3,
                       u'name': u'platinum',
                       u'pantone_code': u'11-1001 tcx',
                       u'pantone_id': u'pantone 11-1001 tcx white alyssum',
                       u'pantone_name': u'white alyssum',
                       u'pantone_rgb': [239, 235, 231],
                       u'popularity': 0.08108108108108109,
                       u'popularity_percentile': 87.5,
                       u'rgb': [235, 229, 229],
                       u'universal_name': u'white'},
                      {u'entrylevel_name': u'peach',
                       u'hex': u'#ebceb5',
                       u'hsv': [27.77777777777777,
                                0.22978723404255308,
                                0.9215686274509803],
                       u'lab': [84.55704693472978,
                                6.337699403810959,
                                16.165955587964454],
                       u'n_instances': 3,
                       u'name': u'nude',
                       u'pantone_code': u'12-0807 tcx',
                       u'pantone_id': u'pantone 12-0807 tcx sun kiss',
                       u'pantone_name': u'sun kiss',
                       u'pantone_rgb': [235, 209, 187],
                       u'popularity': 0.08108108108108109,
                       u'popularity_percentile': 87.5,
                       u'rgb': [235, 206, 181],
                       u'universal_name': u'white'}],
 u'time_ms': u'4.42'}

In [52]: print json.dumps(response.json(),indent=1)
{
 "computed_on": "2018-03-25 13:08:05.714292",
 "time_ms": "4.42",
 "dominant_colors": [
  {
   "name": "raisin black",
   "pantone_rgb": [
    38,
    38,
    42
   ],
   "pantone_id": "pantone 19-3911 tcx black beauty",
   "popularity": 0.10810810810810811,
   "hex": "#282629",
   "universal_name": "black",
   "lab": [
    15.4738227287183,
    1.643636143861621,
    -1.6514882578694712
   ],
   "popularity_percentile": 100.0,
   "rgb": [
    40,
    38,
    41
   ],
   "n_instances": 4,
   "pantone_code": "19-3911 tcx",
   "hsv": [
    279.99999999999994,
    0.07317073170731714,
    0.1607843137254902
   ],
   "pantone_name": "black beauty",
   "entrylevel_name": "black"
  },
  {
   "name": "rose dust",
   "pantone_rgb": [
    205,
    178,
    165
   ],
   "pantone_id": "pantone 14-1307 tcx rose dust",
   "popularity": 0.08108108108108109,
   "hex": "#cdb3a2",
   "universal_name": "pink",
   "lab": [
    74.716930640067,
    6.646359569893512,
    12.110173948868752
   ],
   "popularity_percentile": 87.5,
   "rgb": [
    205,
    179,
    162
   ],
   "n_instances": 3,
   "pantone_code": "14-1307 tcx",
   "hsv": [
    23.720930232558146,
    0.20975609756097569,
    0.803921568627451
   ],
   "pantone_name": "rose dust",
   "entrylevel_name": "tan"
  },
  {
   "name": "timberwolf",
   "pantone_rgb": [
    198,
    197,
    198
   ],
   "pantone_id": "pantone 13-4104 tcx antarctica",
   "popularity": 0.08108108108108109,
   "hex": "#cfcccc",
   "universal_name": "white",
   "lab": [
    82.27695306528122,
    1.0314644455792221,
    0.3580088239849344
   ],
   "popularity_percentile": 87.5,
   "rgb": [
    207,
    204,
    204
   ],
   "n_instances": 3,
   "pantone_code": "13-4104 tcx",
   "hsv": [
    0.0,
    0.01449275362318836,
    0.8117647058823529
   ],
   "pantone_name": "antarctica",
   "entrylevel_name": "silver"
  },
  {
   "name": "platinum",
   "pantone_rgb": [
    239,
    235,
    231
   ],
   "pantone_id": "pantone 11-1001 tcx white alyssum",
   "popularity": 0.08108108108108109,
   "hex": "#ebe5e5",
   "universal_name": "white",
   "lab": [
    91.396105013505,
    2.0227459343862275,
    0.7108750672036868
   ],
   "popularity_percentile": 87.5,
   "rgb": [
    235,
    229,
    229
   ],
   "n_instances": 3,
   "pantone_code": "11-1001 tcx",
   "hsv": [
    0.0,
    0.025531914893616947,
    0.9215686274509803
   ],
   "pantone_name": "white alyssum",
   "entrylevel_name": "off white"
  },
  {
   "name": "nude",
   "pantone_rgb": [
    235,
    209,
    187
   ],
   "pantone_id": "pantone 12-0807 tcx sun kiss",
   "popularity": 0.08108108108108109,
   "hex": "#ebceb5",
   "universal_name": "white",
   "lab": [
    84.55704693472978,
    6.337699403810959,
    16.165955587964454
   ],
   "popularity_percentile": 87.5,
   "rgb": [
    235,
    206,
    181
   ],
   "n_instances": 3,
   "pantone_code": "12-0807 tcx",
   "hsv": [
    27.77777777777777,
    0.22978723404255308,
    0.9215686274509803
   ],
   "pantone_name": "sun kiss",
   "entrylevel_name": "peach"
  }
 ]
}
```

Get the dominant colors for all images in the catalog.

The dominant colors is to be interpreted as a representative of the cluster of similar colors.

<aside class="notice">
Prior to calling this API the dominant colors index has to be built.
</aside>

### End point

`GET /v1/catalog/{catalog_name}/dominant_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
colors | query | The number of dominant colors to return. | 10

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
computed_on | The time the dominant colors where computed.
**dominant_colors** |  A list of dominant colors for the catalog sorted in the decreasing order of popularity. Each field in the list has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors avaialable in our color taxonomy.
popularity | A score between 0 and 1 indicating the popularity of the color.
popularity_percentile | The corresponding percentile score.
n_instances | The number of colors in the cluster.

## Get dominant colors for a product

```python
#------------------------------------------------------------------------------
# Get the dominant colors for an image in the catalog.
# GET /v1/catalog/{catalog_name}/dominant_colors/{id}/{image_id}
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
#params['fraction_pixels_threshold'] = 0.1

# Path parameters
catalog_name = props['catalog_name'] 
id ='SHRES16AWFSDR9346B' 
image_id = '1'

api_endpoint = '/v1/catalog/%s/dominant_colors/%s/%s'%(catalog_name,id,image_id)

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

for color_info in results['dominant_colors']:
    print('[dominant colors] %s - %1.2f - %s - %s - %s - %s'%(color_info['hex'],
                                  color_info['fraction_pixels'],
                                  color_info['name'],
                                  color_info['entrylevel_name'],
                                  color_info['universal_name'],
                                  color_info['pantone_id']))
```

> Sample json response

```json
{
 "dominant_colors": [
  {
   "name": "dark coral",
   "pantone_rgb": [
    194,
    90,
    60
   ],
   "pantone_id": "pantone 18-1447 tcx orange rust",
   "hex": "#c56143",
   "universal_name": "red",
   "lab": [
    52.760181298097564,
    37.55416477816376,
    35.351008096609306
   ],
   "rgb": [
    197,
    97,
    67
   ],
   "hsv": [
    13.846153846153868,
    0.6598984771573604,
    0.7725490196078432
   ],
   "pantone_code": "18-1447 tcx",
   "fraction_pixels": 0.7691573737253264,
   "pantone_name": "orange rust",
   "entrylevel_name": "tomato"
  },
  {
   "name": "nude",
   "pantone_rgb": [
    241,
    206,
    179
   ],
   "pantone_id": "pantone 12-0913 tcx alesan",
   "hex": "#eecdb4",
   "universal_name": "white",
   "lab": [
    84.55409785378009,
    7.834123124968007,
    16.72944045045519
   ],
   "rgb": [
    238,
    205,
    180
   ],
   "hsv": [
    25.86206896551721,
    0.24369747899159655,
    0.9333333333333333
   ],
   "pantone_code": "12-0913 tcx",
   "fraction_pixels": 0.14824974655614526,
   "pantone_name": "alesan",
   "entrylevel_name": "peach"
  },
  {
   "name": "seal brown",
   "pantone_rgb": [
    67,
    57,
    55
   ],
   "pantone_id": "pantone 19-0910 tcx mulch",
   "hex": "#453a39",
   "universal_name": "black",
   "lab": [
    25.523637518334688,
    4.61600764818082,
    2.4026609351798234
   ],
   "rgb": [
    69,
    58,
    57
   ],
   "hsv": [
    5.0,
    0.17391304347826075,
    0.27058823529411763
   ],
   "pantone_code": "19-0910 tcx",
   "fraction_pixels": 0.08259287971852823,
   "pantone_name": "mulch",
   "entrylevel_name": "black"
  }
 ],
 "time_ms": "60.52",
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

Get the dominant colors for an image in the catalog.

<aside class="notice">
Prior to calling this API the dominant colors index has to be built.
</aside>

### End point

`GET /v1/catalog/{catalog_name}/dominant_colors/{id}/{image_id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
fraction_pixels_threshold | query  | Include only those colors whos fraction of pixels is greater than this number. |  0.0

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
computed_on | The time the dominant colors where computed.
**dominant_colors** |  A list of dominant colors. Each field in the list has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors avaialable in our color taxonomy.
**bounding_box** | The region in the image used to compute the dominant colors and corresponds to the the bounding box for the central detected person.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
score | The confidence score (between 0 and 1) that the bounding box contains a person.