## Visual Search

```python
#------------------------------------------------------------------------------
# Visual Search
#
# Get visually similar products in the catalog for an uploaded image.
# POST /v1/catalog/{catalog_name}/visual_search
# params - max_number_of_results
#          per_category_index
#          category
#          visual_search_categories_threshold
#          sort_option
#------------------------------------------------------------------------------

import os
import json
import requests
from urlparse import urljoin
from pprint import pprint

from props import *

#------------------------------------------------------------------------------
# API URL
#------------------------------------------------------------------------------
api_gateway_url = props['api_gateway_url']

#------------------------------------------------------------------------------
# HEADERS
#------------------------------------------------------------------------------
headers = {}
# API key
headers['X-Api-Key'] = props['X-Api-Key']
# Data collection opt out.
headers['X-Data-Collection-Opt-Out'] = props['X-Data-Collection-Opt-Out']

#------------------------------------------------------------------------------
# OPTIONAL QUERY PARAMETERS
#------------------------------------------------------------------------------

params = {}
#------------------------------------------------------------------------------
# max_number_of_results : int, optional (default: 12)
#
# The maximum number of results to return. 
#------------------------------------------------------------------------------
params['max_number_of_results'] = 10

#------------------------------------------------------------------------------
# category : str, optional (default: None) 
#
# Optionally you can also specify a list of categories to search.
# This is specified as a comma separated string, for example, 
# `category=tops,blouses`. The categories have to be valid 
# categories specified in the data['visual_search_category'] field.
#------------------------------------------------------------------------------
#params['category'] = 'kurta,dress'

#------------------------------------------------------------------------------
# per_category_index : str, optional (default: 'false')
#
# If this is set to 'true' first predicts the visual search category for the
# uploaded image and then does visual search with the predicted categories.
#------------------------------------------------------------------------------
params['per_category_index'] = 'true'

#------------------------------------------------------------------------------
# visual_search_categories_threshold : float, optional (default: 0.0)
#
# Use the prdictions from the visual search categories classifier
# only if the confidence score is greater than this value. 
#------------------------------------------------------------------------------
params['visual_search_categories_threshold'] = 0.25

#------------------------------------------------------------------------------
# sort_option : str, optional (default: 'visual_similarity')
#
# 'visual_similarity'  : Results are sorted by visual similarity.
# 'apparel_similarity' : Re-sorts the results from visual browse using 
#                        apparel similarity.
#------------------------------------------------------------------------------
#params['sort_option'] = 'apparel_similarity'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/visual_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

#------------------------------------------------------------------------------
# Three options to pass the image
#------------------------------------------------------------------------------

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_3.jpeg','rb'))

"""       
# OPTION 2 : Pass the image url
params['image_url'] = 'http://images.abofcdn.com/catalog/images/2015/ABOFA15AMCWJG1044901/Right_Medium.jpg'
response = requests.post(url,
                         headers=headers,
                         params=params)
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

print response.url
print response.status_code
pprint(response.json())

#------------------------------------------------------------------------------
# FRIENDLY RESPONSE
#------------------------------------------------------------------------------
results = response.json()

image_location = '%s?api_key=%s'%(urljoin(api_gateway_url,results['image_location']),
                                  props['X-Api-Key'])
print('[user uploaded image] %s'%(image_location))

for product in results['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    # Get the image_url and image_filename by querying the catalog
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    image_filename = response.json()['data']['images'][image_id]['image_filename']
    image_location = '/v1/catalog/%s/images/%s'%(catalog_name,image_filename)
    image_url_local = '%s?api_key=%s'%(urljoin(api_gateway_url,image_location),props['X-Api-Key'])
    print('%s %s %1.2f %s'%(id,image_id,score,image_url_local))

```

> Sample json response

```json
{
 "image_location": "/v1/images/c06f93b0-ac6f-4abc-a01f-5a59b139e0fa.jpe",
 "sort_option": "visual_similarity",
 "time_ms": "563.05",
 "per_category_index": true,
 "visual_search_category": [
  "upper"
 ],
 "visual_search_category_predictions": {
  "upper": 0.9998906529487854,
  "pant": 7.608256959051509e-06,
  "dress": 8.080697318057176e-05,
  "jeans": 1.1917099982383661e-05,
  "shorts": 4.029329622568618e-06
 },
 "products": [
  {
   "image_id": "2",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.9808963257819414
  },
  {
   "image_id": "1",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.6712234914302826
  },
  {
   "image_id": "1",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91662",
   "similarity": 0.6435617506504059
  },
  {
   "image_id": "2",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91662",
   "similarity": 0.5532491207122803
  },
  {
   "image_id": "2",
   "visual_search_category": "upper",
   "id": "SKLTS16AMCWSH8SH20",
   "similarity": 0.534146636724472
  }
 ]
}
```

Find other similar images in the product catalog for a given uploaded image based on visual appearance.

<aside class="notice">
Note that the visual search index has to be built first before we can use this API.  
</aside>

### End point

`POST /v1/catalog/{catalog_name}/visual_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF. |
max_number_of_results | query | maximum number of results to return | 12
category | query | Optionally you can also specify a list of categories to search. This is specified as a comma separated string, for example, `category=tops,blouses`. The categories have to be valid categories specified in the `data['visual_search_category']`` field. |
per_category_index | query | If this is set to `true` first predicts the visual search category for the uploaded image and then does visual search with the predicted categories. | `false`
visual_search_categories_threshold | query | Use the predictions from the visual search categories classifier only if the confidence score is greater than this value. | 0.0

<aside class="notice">
The last two parameters (per_category_index  and visual_search_categories_threshold) are to be used when the visual search categories are predicted using the visual search categories classifier.
</aside>

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The path to the user uploaded image. This is not avaiable if you set the `X-Data-Collection-Opt-Out` header parameter to `true`.
**products** | A sorted list of other visually similar images. 
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1 (similar). 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
image_location | The path to the user uploaded image. This is not avaiable if you set the `X-Data-Collection-Opt-Out` header parameter to `true`.
per_category_index | Indicates whetehr category based visual search was used or not.
visual_search_category | The list of visual search categories used.
visual_search_category_predictions | The top-5 predictions from the visual search category classifier.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1(similar). 
visual_search_category | The visual search category for the product.
