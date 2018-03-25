# Colors

These are a few APIs for dealing with color terms. 

A **color term**, also known as a color name, is a word or phrase that refers to a specific color. For example *red* is the color term used for a color with RGB values [255,0,0]. While the number of color terms are quite huge we internally organize our color terms into three color families:

- **universal** Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
- **entrylevel** A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
- **full** The full set of colors available in our color taxonomy including more esoteric color terms.

Color similarities are computed using the [Delta E (CIE2000)](https://en.wikipedia.org/wiki/Color_difference) metric in the LAB space.

## Get color terms

```python
#------------------------------------------------------------------------------
# Get color terms. 
# GET /v1/colors/color_terms
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

api_endpoint = '/v1/colors/color_terms'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['r'] = 106
params['g'] = 103
params['b'] = 95

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "time_ms": "250.63",
 "color_terms": [
  {
   "name": "papaya",
   "hex": "#645e54",
   "universal_name": "gray",
   "rgb": [
    100,
    94,
    84
   ],
   "hsv": [
    37.5,
    0.16000000000000003,
    0.39215686274509803
   ],
   "entrylevel_name": "gray"
  },
  {
   "name": "granite gray",
   "hex": "#676767",
   "universal_name": "gray",
   "rgb": [
    103,
    103,
    103
   ],
   "hsv": [
    0.0,
    0.0,
    0.403921568627451
   ],
   "entrylevel_name": "gray"
  },
  {
   "name": "dim gray",
   "hex": "#696969",
   "universal_name": "gray",
   "rgb": [
    105,
    105,
    105
   ],
   "hsv": [
    0.0,
    0.0,
    0.4117647058823529
   ],
   "entrylevel_name": "gray"
  },
  {
   "name": "nickel",
   "hex": "#727472",
   "universal_name": "gray",
   "rgb": [
    114,
    116,
    114
   ],
   "hsv": [
    120.0,
    0.01724137931034475,
    0.4549019607843137
   ],
   "entrylevel_name": "gray"
  },
  {
   "name": "sonic silver",
   "hex": "#757575",
   "universal_name": "gray",
   "rgb": [
    117,
    117,
    117
   ],
   "hsv": [
    0.0,
    0.0,
    0.4588235294117647
   ],
   "entrylevel_name": "gray"
  }
 ]
}
```

Get the closest color name for a given color (specified by [RGB color model](https://en.wikipedia.org/wiki/RGB_color_model)).

### End point

`GET /v1/colors/color_terms`

### Request

Parameter | Type | Description 
--------- | ------- | ----------- 
r | query | (**Required**) The value for the red channel in the RGB color model (in the range 0 to 255).
g | query | (**Required**) The value for the green channel in the RGB color model (in the range 0 to 255).
b | query | (**Required**) The value for the blue channel in the RGB color model (in the range 0 to 255).

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
color_terms |  A list of five closest color terms. Each field has the following field names.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors available in our color taxonomy.

## Get similar colors

```python
#------------------------------------------------------------------------------
# Get similar colors. 
# GET /v1/colors/similar_colors
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

api_endpoint = '/v1/colors/similar_colors'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['color_term'] = 'purple'

# Optional parameters
#params['color_family'] = 'full'

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "similar_colors": [
  {
   "name": "purple html",
   "hex": "#800080",
   "universal_name": "purple",
   "rgb": [
    128,
    0,
    128
   ],
   "hsv": [
    300.0,
    1.0,
    0.5019607843137255
   ],
   "entrylevel_name": "purple"
  },
  {
   "name": "violet",
   "hex": "#8f00ff",
   "universal_name": "blue",
   "rgb": [
    143,
    0,
    255
   ],
   "hsv": [
    273.6470588235294,
    1.0,
    1.0
   ],
   "entrylevel_name": "violet"
  },
  {
   "name": "navy",
   "hex": "#000080",
   "universal_name": "blue",
   "rgb": [
    0,
    0,
    128
   ],
   "hsv": [
    240.0,
    1.0,
    0.5019607843137255
   ],
   "entrylevel_name": "navy blue"
  },
  {
   "name": "rich maroon",
   "hex": "#b03060",
   "universal_name": "purple",
   "rgb": [
    176,
    48,
    96
   ],
   "hsv": [
    337.5,
    0.7272727272727273,
    0.6901960784313725
   ],
   "entrylevel_name": "maroon"
  },
  {
   "name": "blue",
   "hex": "#0000ff",
   "universal_name": "blue",
   "rgb": [
    0,
    0,
    255
   ],
   "hsv": [
    240.0,
    1.0,
    1.0
   ],
   "entrylevel_name": "blue"
  }
 ],
 "time_ms": "0.17"
}
```

Get other color terms similar to a given color term.

### End point

`GET /v1/colors/similar_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
color_term  | query | (**Required**) The color term. |
color_family | query | The color family to use to retrieve the similar colors. Can take values `universal`, `entrylevel` or `full` | `entrylevel`

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
similar_colors |  A list of five closest color terms. Each field has the following field names.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors available in our color taxonomy.

## Get color similarity 

```python
#------------------------------------------------------------------------------
# Get similarity score between two color terms. 
# GET /v1/colors/color_terms_similarity
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

api_endpoint = '/v1/colors/color_terms_similarity'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['color_term_1'] = 'red'
params['color_term_2'] = 'pink'

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
  "similarity_score": 0.650632524978343,
  "time_ms": u'22.23'
}
```

Get similarity score between two color terms (eg. 'red' and 'pink'). 

Uses the [CIEDE2000](https://en.wikipedia.org/wiki/Color_difference) metric to measure similarity between two color terms. 

<aside class="notice">
We internally use our color taxonomy to get the RGB values for the color term. If the color terms are not in our color taxonomy the code defaults to the jaccard similarity between the color terms.
</aside>

### End point

`GET /v1/colors/color_terms_similarity`

### Request

Parameter | Type | Description 
--------- | ------- | ----------- 
color_term_1  | query | (**Required**) The first  color term.
color_term_2  | query | (**Required**) The second color term.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
similarity_score |  The similarity score (between 0 and 1) which indicates the similarity between the two color terms. Internally it uses the [CIEDE2000](https://en.wikipedia.org/wiki/Color_difference) metric to measure similarity between two color terms. 
