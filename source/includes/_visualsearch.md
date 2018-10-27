## Visual Search

```python
#------------------------------------------------------------------------------
# Get visually similar products in the catalog for an uploaded image.
# POST /v1/catalog/{catalog_name}/visual_search
# params - max_number_of_results
#          per_category_index
#          categories
#          sort_option    
#          visual_search_categories_threshold
#          unique_products
#          group_by
#------------------------------------------------------------------------------

import os
import json
import requests
try:
    from urllib.parse import urljoin
except ImportError:
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
params['max_number_of_results'] = 5

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
# reweight_similarity_scores : str, optional (default: 'false')
#
# If true multiplies the visual similarity scores by the (normalized) category 
# classifier prediction scores.    
#------------------------------------------------------------------------------
#params['reweight_similarity_scores'] = 'false'

#------------------------------------------------------------------------------
# sort_option : str, optional (default: 'visual_similarity')
#
# 'visual_similarity'  : Results are sorted by visual similarity.
# 'apparel_similarity' : Re-sorts the results from using apparel similarity.
# 'color_similarity'   : Re-sorts the results from using color similarity.
#------------------------------------------------------------------------------
#params['sort_option'] = 'apparel_similarity'
#params['sort_option'] = 'color_similarity'

#------------------------------------------------------------------------------
# unique_products : str, optional (default: 'false')
#
# By default visual browse uses all images available for a product.
# Hence, sometimes in the search results the same product id can 
# appear multiple times (with a different image_id). If this 
# parameter is set to 'true' then the results are post-filtered 
# to contain only unique products (the best matching image_id is
# retained).  
#------------------------------------------------------------------------------
#params['unique_products'] = 'true'

#------------------------------------------------------------------------------
# - group_by : str, optional (default: None)  
#    This field can be used to group the results from visual search 
#    into distinct groups according to various criteria. 
#------------------------------------------------------------------------------
#params['group_by'] = 'color'
#params['group_by'] = 'visual'
#params['group_by'] = 'visual,color'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = 'v1/catalog/%s/visual_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

#------------------------------------------------------------------------------
# Three options to pass the image
#------------------------------------------------------------------------------

# OPTION 1 : Directly post the image
headers['Content-Type'] = 'image/jpeg'
response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_4.jpeg','rb'))

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
 "image_location": "/v1/images/ed802be3-afaf-4e84-bc0a-95a4cdee9268.jpe",
 "sort_option": "visual_similarity",
 "time_ms": "572.09",
 "per_category_index": true,
 "visual_search_category": [
  "upper"
 ],
 "visual_search_category_predictions": {
  "upper": 0.5216091680340469,
  "outer": 0.028820261858555796,
  "pant": 0.007078210662257334,
  "dress": 0.39006669973059616,
  "shorts": 0.0016162813953428667
 },
 "products": [
  {
   "image_id": "1",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.47364169359207153
  },
  {
   "image_id": "2",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91642",
   "similarity": 0.449137806892395
  },
  {
   "image_id": "1",
   "visual_search_category": "upper",
   "id": "LPJNA16AMDMTE91662",
   "similarity": 0.424848735332489
  },
  {
   "image_id": "2",
   "visual_search_category": "upper",
   "id": "SKLTS16AMCWSH8SP01",
   "similarity": 0.42225950956344604
  },
  {
   "image_id": "3",
   "visual_search_category": "upper",
   "id": "SKLTS16AMCWSH8SP01",
   "similarity": 0.42015230655670166
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
reweight_similarity_scores | query | If `true` multiplies the visual similarity scores by the (normalized) category classifier prediction scores.  | `false`     
sort_option | query | The sort option to use. Can be either `visual_similarity` (Results are sorted by visual similarity.) or `apparel_similarity` (Re-sorts the results using a combination of visual similarity and textual similarity based on the avaialble meta data.) or `color_similarity` (Re-sorts the results using a combination of visual similarity and color similarity based on the avaialble meta data.). | `visual_similarity`
unique_products | query | By default visual browse uses all images available for a product. Hence, sometimes in the search results the same product id can appear multiple times (with a different image_id). If this parameter is set to 'true' then the results are post-filtered to contain only unique products (the best matching image_id is retained). | `false`
group_by |query | This field can be used to group the results from visual search into distinct groups according to various criteria. This is specfied as a comma separated string, for example, `group_by=color,pattern`. This would group results with similar color and pattern into a distinct group. The group is accessed by the field `group_id` in the results. Note that the field has to be a valid field avaialble in the product json. There is also a special field called `visual` which uses the total visual apprarance to group the reuslts rather than one specific attribute, for example, `group_by=visual,color`. In order to use this feature `group_by` should also be set to `true` when building the visual search index. The final set of returned results are sorted by the group size. This is currently an experimental feature and following are the suggested values to try are `group_by=visual`,`group_by=color` and `group_by=visual,color`. | 

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

## Get visual search categories

```python
#------------------------------------------------------------------------------
# Get all visual search categories 
# GET /v1/catalog/{catalog_name}/visual_search_categories
#------------------------------------------------------------------------------

import os
import json
import requests
try:
    from urllib.parse import urljoin
except ImportError:
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
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/visual_search_categories'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "4.78",
 "counts": {
  "num_of_products": 8,
  "num_of_products_by_category": {
   "upper": 4,
   "lower": 2,
   "dress": 2
  },
  "num_of_images": 38,
  "num_of_images_by_category": {
   "upper": 18,
   "lower": 10,
   "dress": 10
  }
 },
 "categories": [
  "upper",
  "lower",
  "dress"
 ]
}
```

Get all visual search categories 

### End point

`GET /v1/catalog/{catalog_name}/visual_search_categories`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
categories | The visual search categories.
counts | The counts of the visual search categories.