# Colors

These are a few APIs for dealing with color terms. 

A **color term**, also known as a color name, is a word or phrase that refers to a specific color. For example *red* is the color term used for a color with RGB values [255,0,0]. While the number of color terms are quite huge we internally organize our color terms into three color families:

- **universal** Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
- **entrylevel** A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
- **full** The full set of colors available in our color taxonomy including more esoteric color terms.
- **pantone** The pantone color terms for fashion.

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
from pprint import pprint

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

api_endpoint = '/v1/colors/color_terms'

url = urljoin(api_gateway_url,api_endpoint)

# Parameters - required
params = {}
params['r'] = 106
params['g'] = 103
params['b'] = 95

# Parameters - optional
params['color_count'] = 3
params['color_family'] = 'full'
#params['color_family'] = 'universal'
#params['color_family'] = 'entrylevel'
#params['color_family'] = 'pantone'
params['method'] = 'accurate'
#params['method'] = 'fast'

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "234.64",
 "color_terms": [
  {
   "name": "dim gray",
   "pantone_rgb": [
    104,
    103,
    103
   ],
   "pantone_id": "pantone 18-0503 tcx gargoyle",
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
   "pantone_code": "18-0503 tcx",
   "pantone_name": "gargoyle",
   "entrylevel_name": "gray"
  },
  {
   "name": "charcoal gray",
   "pantone_rgb": [
    108,
    104,
    104
   ],
   "pantone_id": "pantone 18-0601 tcx charcoal gray",
   "hex": "#6c6868",
   "universal_name": "gray",
   "rgb": [
    108,
    104,
    104
   ],
   "hsv": [
    0.0,
    0.03703703703703709,
    0.4235294117647059
   ],
   "pantone_code": "18-0601 tcx",
   "pantone_name": "charcoal gray",
   "entrylevel_name": "gray"
  },
  {
   "name": "gunmetal",
   "pantone_rgb": [
    92,
    93,
    91
   ],
   "pantone_id": "pantone 18-0306 tcx gunmetal",
   "hex": "#5c5d5b",
   "universal_name": "gray",
   "rgb": [
    92,
    93,
    91
   ],
   "hsv": [
    90.0,
    0.02150537634408589,
    0.36470588235294116
   ],
   "pantone_code": "18-0306 tcx",
   "pantone_name": "gunmetal",
   "entrylevel_name": "gray"
  }
 ]
}
```

Get the closest color term for a given color (specified by [RGB color model](https://en.wikipedia.org/wiki/RGB_color_model)).

### End point

`GET /v1/colors/color_terms`

### Request

Parameter | Type | Description | Default
--------- | ------- | -----------  | ----------- 
r | query | (**Required**) The value for the red channel in the RGB color model (in the range 0 to 255). |
g | query | (**Required**) The value for the green channel in the RGB color model (in the range 0 to 255). |
b | query | (**Required**) The value for the blue channel in the RGB color model (in the range 0 to 255). |
color_count | query | the number of similar color terms (maximum value of 10) | 5
color_family | query | the color family, can be either `universal`, `entrylevel`, `full` or `pantone` | `full`
method | query | The method to use to get the color names, can be either `accurate` (exact but slower) or `fast` (approximate but fast) | `accurate`
               
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
# Get other color terms similar to a given color term.
# GET /v1/colors/similar_colors
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

api_endpoint = '/v1/colors/similar_colors'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['color_term'] = 'purple'

# Optional parameters
params['color_count'] = 3
#params['color_family'] = 'universal'
#params['color_family'] = 'entrylevel'
#params['color_family'] = 'full'

# To serch within the pantone family
#params['color_term'] = 'pantone 19-3336 tcx sparkling grape'
#params['color_family'] = 'pantone'

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "similar_colors": [
  {
   "name": "patriarch",
   "pantone_rgb": [
    119,
    51,
    118
   ],
   "pantone_id": "pantone 19-3336 tcx sparkling grape",
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
   "pantone_code": "19-3336 tcx",
   "pantone_name": "sparkling grape",
   "entrylevel_name": "purple"
  },
  {
   "name": "violet",
   "pantone_rgb": [
    193,
    127,
    181
   ],
   "pantone_id": "pantone 16-3320 tcx violet",
   "hex": "#c17fb5",
   "universal_name": "pink",
   "rgb": [
    193,
    127,
    181
   ],
   "hsv": [
    310.9090909090909,
    0.34196891191709844,
    0.7568627450980392
   ],
   "pantone_code": "16-3320 tcx",
   "pantone_name": "violet",
   "entrylevel_name": "magenta"
  },
  {
   "name": "navy blue",
   "pantone_rgb": [
    64,
    63,
    111
   ],
   "pantone_id": "pantone 19-3832 tcx navy blue",
   "hex": "#403f6f",
   "universal_name": "purple",
   "rgb": [
    64,
    63,
    111
   ],
   "hsv": [
    241.25,
    0.43243243243243246,
    0.43529411764705883
   ],
   "pantone_code": "19-3832 tcx",
   "pantone_name": "navy blue",
   "entrylevel_name": "navy blue"
  }
 ],
 "time_ms": "0.11"
}
```

Get other color terms similar to a given color term.

### End point

`GET /v1/colors/similar_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
color_term  | query | (**Required**) The color term. |
color_count | query | the number of similar color terms (maximum value of 10) | 5
color_family | query | The color family to use to retrieve the similar colors. Can take values `universal`, `entrylevel` ,`full` or `pantone` | `entrylevel`

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
