## Add metadata to a catalog    

```python
#------------------------------------------------------------------------------
# Add metadata to a catalog.    
# POST /v1/catalog/{catalog_name}
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
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# Metadata
# Post any metadata you want to track for the catalog. Suggested fields
#------------------------------------------------------------------------------
data={}
#A friendly name for display purposes.
data['friendly_name'] = 'sample' 
# The url of the hero image.
data['hero_image_url'] = 'https://cognitivefashion.github.io/img/portfolio/logo_cf.svg' 
# A brief description.
data['description'] = 'A sample catalog to test IBM Research AI for Fashion APIs.' 

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = 'v1/catalog/%s'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,
                         headers=headers,
                         params=params,
                         json=data)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "2.87",
 "catalog_name": "sample_catalog",
 "metadata": {
  "friendly_name": "sample",
  "description": "A sample catalog to test IBM Research AI for Fashion APIs.",
  "hero_image_url": "https://cognitivefashion.github.io/img/portfolio/logo_cf.svg"
 }
}
```

Add any extrat metadata to a catalog  

You can add any meta data you want to track. Suggested fields could include

- `friendly_name` - A friendly name for display purposes.
- `hero_image_url` - The url of the hero image.
- `description` - A brief description.

### End point

`POST /v1/catalog/{catalog_name}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
data | application/json | (**Required**) The json object file containing the metadata as key value pairs. |  

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name |  The catalog name.
metadata | The uploaded metadata.

## Get info about a catalog

```python
#------------------------------------------------------------------------------
# Get info about a product catalog.  
# GET /v1/catalog/{catalog_name}
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
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = 'v1/catalog/%s'%(catalog_name)

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
 "num_of_products": 7,
 "time_ms": "9.22",
 "num_of_images": 24,
 "catalog_name": "sample_catalog",
 "metadata": {
  "friendly_name": "sample",
  "description": "A sample catalog to test IBM Research AI for Fashion APIs.",
  "hero_image_url": "https://cognitivefashion.github.io/img/portfolio/logo_cf.svg"
 }
}
```

Get some info about the product catalog. Currently this returns the number of products, the number of images and the metadata. 

### End point

`GET /v1/catalog/{catalog_name}`

### Request

Parameter | Type | Description | 
--------- | ------- | ----------- 
catalog_name | path | (**Required**) The catalog name. 

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
num_of_products | Number of products in the catalog.
num_of_images | Number of images in the catalog.
metadata | The metadata.

## Delete a catalog     

```python
#------------------------------------------------------------------------------
# Delete a product catalog.
# DELETE /v1/catalog/{catalog_name}
# params : delete_images
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
# delete_images : str, optional(default: 'true')
# By default deletes all the catalog images unless this is set to 'false'.     
#------------------------------------------------------------------------------
params['delete_images'] = 'false'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,
                           headers=headers,
                           params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "deleted": true,
 "time_ms": "360.34",
 "catalog_name": "sample_catalog"
}
```

Delete a product catalog. Also deletes all the downloaded images.

### End point

`DELETE /v1/catalog/{catalog_name}`

### Request

Parameter | Type | Description | 
--------- | ------- | ----------- 
catalog_name | path | (**Required**) The catalog name. 


Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
delete_images | query | By default deletes all the catalog images unless this is set to `false`.  | `true`  

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
deleted | This returns true is the catalog was successfully deleted.

## Get all catalog names    

```python
#------------------------------------------------------------------------------
# Get all catalog names. 
# GET /v1/catalog_names
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
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = 'v1/catalog_names'

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
  "catalog_names": ["sample_catalog",
                    "sample_catalog_2"],
  "time_ms": "3.78"
}
```

Get all the catalog names.

### End point

`GET /v1/catalog_names`

### Request

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_names | The catalog names.

## Get catalog image   

```shell
http://api_gateway_url/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SH20_5.jpg

http://api_gateway_url/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SH20_5.jpg?top_left_x=100&top_left_y=100&width=64&height=64

```

```python
#-----------------------------------------------------------------------------
# Get product from a catalog.  
# GET /v1/catalog/{catalog_name}/products/{id}
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
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

# product id
id = 'SKLTS16AMCWSH8SH20'

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params)

print response.status_code
pprint(response.json())

#------------------------------------------------------------------------------
# ACCESSING IMAGES.
#------------------------------------------------------------------------------
results = response.json()

for image_id in results['data']['images']:
    image_url = results['data']['images'][image_id]['image_url']
    image_filename = results['data']['images'][image_id]['image_filename']
    image_location = '/v1/catalog/%s/images/%s'%(catalog_name,image_filename)
    image_url_local = '%s?api_key=%s'%(urljoin(api_gateway_url,image_location),props['X-Api-Key'])
    print('%s %s \n %s \n %s \n %s'%(id,image_id,
                                     image_filename,
                                     image_url,
                                     image_url_local)
    )
```

> Sample json response

```json
```

Get an image in the catalog.

You can also pass the bounding box in the parameters to return a crop.

### End point

`GET /v1/catalog/{catalog_name}/images/{image_name}`

### Request

Parameter | Type | Description | 
--------- | ------- | ----------- 
catalog_name | path | (**Required**) The catalog name. 
image_name | path | (**Required**) The image name.
top_left_x | The x co-ordinate of the top left corner of the bounding box.
top_left_y | The y co-ordinate of the top left corner of the bounding box.
width | The width of the bounding box.
height | The height of the bounding box.
 
### Response 

