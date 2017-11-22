---
title: IBM Research Cognitive Fashion API Reference

language_tabs:
  - shell
  - python

toc_footers:
  - <a href='https://www.ibm.com/us-en/marketplace/8075'>Sign up for an API key</a>
  - <a href='https://github.com/cognitivefashion/cf-sdk-python'>Python SDK</a>
  - <a href='https://www.ibm.com/us-en/marketplace/8075'>Cognitive Fashion site</a>

includes:
  - errors
  
search: true
---

# IBM Research Cognitive Fashion 

The [IBM Research Cognitive Fashion](https://www.ibm.com/us-en/marketplace/8075) project aims to build a suite of APIs for the fashion industry in order to enable an engaging shopping experience, primarily leveraging natural language and image understanding technologies together with other cognitive capabilities. Developers/in-house IT team can use these APIs to build their own applications. Central to this is a curated domain specific taxonomy/knowledge graph, fashion related content and text/image annotators tuned to the domain of fashion. 

[Python SDK](https://github.com/cognitivefashion/cf-sdk-python)

<aside class="notice">
<b>IBM fashion taxonomy</b> is a semi-automatically curated comprehensive hierarchical fashion taxonomy for apparel, footwear, and accessories along with their attributes and relations. The taxonomy is curated from several fashion resources and then hand tuned by domain experts. All the APIs below leverage this fashion taxonomy. Fashion evolves constantly and hence IBM fashion taxonomy is dynamically updated at regular intervals to include the current fashion terms and trends. The user will always have access to the latest fashion taxonomy when using any of these APIs.
</aside>

# Authentication

```shell
# Pass the API key in the header.
curl 'api_endpoint_here' -H "X-Api-Key: your_api_key"

# If you are making a GET request, 
# you may also pass in the API key as a parameter.
curl "api_endpoint_here"?api_key=your_api_key
```

```python
# We will use the requests HTTP library for python for all the examples.
# http://docs.python-requests.org

import requests

url = 'api_endpoint_here'
headers = {'X-Api-Key': 'your_api_key'}
response = requests.get(url,headers=headers)

# If you are making a GET request, 
# you may also pass in the API key as a parameter.
url = 'api_endpoint_here'
payload = {'api_key': 'your_api_key'}
response = requests.get(url,params=payload)
```

> Make sure to replace `your_api_key` with your API key.

Cognitive Fashion uses API keys to allow access to the API. You can request for a API key here at our [portal](https://www.ibm.com/us-en/marketplace/8075). Once you sign up you will get a custom url and an API key. The API key has to be included in all API requests to the server. 

The preferred way is to include it in a header that looks like the following:

`curl "api_endpoint_here" -H "X-Api-Key: your_api_key"`

If you are making a `GET` request, you may also pass in the API key as a parameter:

`curl "api_endpoint_here"?api_key=your_api_key`

<aside class="notice">
You must replace <code>your_api_key</code> with your personal API key.
</aside>

# Fashion quote

```python
import requests
from urlparse import urljoin

from props import *

# Replace this with the custom url generated for you.
api_gateway_url = props['api_gateway_url']

# Pass the api key into the header.
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Define the API end point.
api_endpoint = '/v1/fashion_quote'

url = urljoin(api_gateway_url,api_endpoint)
    
response = requests.get(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "quote": "Simplicity is the keynote of all true elegance.",
 "author": "Coco Chanel"
}
```

This endpoint gets a random quote about fashion. Think of this as our `Hello, world!` albiet a more interesting one which you can actually use on your websites.

### End point

`GET /v1/fashion_quote`

# Catalog 

The APIs come in two flavors. Some APIs are simple and do not require access to the product catalog. However we have another bunch of powerful APIs which operate on top of the client catalog. The first step is to upload the product catalog. These are some end points for uploading, accessing, modifying and deleting  a product catalog.

## Product JSON Format

> Sample json document for catalog ingestion corresponding to this [product](http://www.abof.com/product/109FA16AWWWDR9D03Y10-109F-Women-Brown-&-Yellow-Printed-Dress).

```json
{
 "id": "109FA16AWWWDR9D03Y10", 
 "url": "http://www.abof.com/product/109FA16AWWWDR9D03Y10-109F-Women-Brown-&-Yellow-Printed-Dress", 
 "date": "2016-10-12", 
 "out_of_stock": "no",
 "name": "109f women brown & yellow printed dress", 
 "category": "dress", 
 "taxonomy": "women;dress", 
 "gender": "female",  
 "age_group": [
  "teenager", 
  "adult"
 ], 
 "images": {
  "1": {
   "camera_focus": "full", 
   "pose": "front", 
   "ignore": "no",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Front_Large.jpg", 
   "model_worn": "yes"
  }, 
  "3": {
   "camera_focus": "detail", 
   "pose": "UNK", 
   "ignore": "yes",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Detail_Large.jpg", 
   "model_worn": "no"
  }, 
  "2": {
   "camera_focus": "full", 
   "pose": "right", 
   "ignore": "no",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Right_Large.jpg", 
   "model_worn": "yes"
  }, 
  "4": {
   "camera_focus": "full", 
   "pose": "back", 
   "ignore": "no",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Back_Large.jpg", 
   "model_worn": "yes"
  }
 }, 
 "color": [
  "copper",
  "brown",
  "yellow"
 ], 
 "pattern": [
  "printed"
 ], 
 "fabric": [
  "polyester"
 ], 
 "brand": [
  "109f"
 ], 
 "occasion": [
  "brunch"
 ],  
 "season": [
  "summer"
 ], 
 "attributes": [
  "round neck",
  "sleeveless"
 ], 
 "description": "Brown and yellow printed dress, has a round neck with tie-up detail on one shoulder, sleeveless, elasticized detail along the waistline, side slits", 
 "style_tip": "Club this dress with a pair of sandals. Add on a statement neckpiece for a stylish allure.", 
 "available_sizes": [
  "XS", 
  "S", 
  "M", 
  "L", 
  "XL"
 ], 
 "price": 2199.0, 
 "sale_price": 1100.0, 
 "sale_percentage": 50.0,
 "currency": "INR"
}
```


We refer to any **apparel**, **accessory**, or **shoes** as a **product** in the table below. A product in a catalog is represented as a json object with fields specified below. Ingesting the catalog means uploading these json files for all products in the catalog via an API call.

Try to populate as much information available for each product into these fields in the json document. Except for the fields in bold (**id**, **gender**, **name** (for natural language search), **images**(for visual search and visual browse)) all of these are optional. However the the performance of the natural language search is heavily dependent on the richness of these structured fields and is recommended that you provide as much information as possible.

field | type | description 
--------- | ------- | ---------- 
**id** | string | A unique identifier for the product for which there is a product page and a set of product images associated with it. 
url | string | The corresponding url of the product page.
date | string | The date when the item was entered into the catalog, in `YYYY-MM-DD` format.
out_of_stock | string | Can either be `yes` or `no`. This is the field that need to be changed to `yes` when a product goes out of stock. By default all search queries will not return out-of-stock products. Note that typically out of stock is at the SKU level. Ideally make this `yes`  only when all SKUs for this product are out of stock.
| | | *You will typically use this when the product will be replenished. If you think the product will be discontinued and not available again you can delete that product entirely.*
**name** | string | The name of the product. This is generally a free text brief description of the product.
category | string | The product category.
taxonomy | string | The complete product taxonomy if known, separated via `;`.
**gender** | string | The gender for which the product is intended for, can be `male`, `female` or `unisex`.
age_group | list string | The age group for which the product is intended for. Some suggested tags : `baby`, `kid`, `teenager`, `adult`.
**images** | list of dict | A list of images available for the product with `image_id` as the key and the following fields in the dict. 
| | image_id | The image id (string).
| | image_url | The image url. The actual image will be downloaded from this url to build the visual search index. The image format can be in JPEG,PNG,BMP, or GIF.
| | ignore | You can use this field to select what kind of images should be used for visual browse/search.  If `yes` then the image will not be downloaded and excluded from visual browse/search. In the example on the right, we ignore all images which have close-up of some details in the product.
| | model_worn | If `yes` then the image corresponds to the model wearing the particular product. If `no` refers to image of the the product only. Use `UNK` if not known.
| | pose | Refers to the pose/orientation in which the image is taken. Can take one of the following values: `front`, `back`, `left`, `right`, `top`, `UNK`.
| | camera_focus | Which part of the model the camera is focussed on. Can take one of the following values: `full` (complete view of the model, entire body), `top` (upper part of the model above the waist, used in shirts etc.), `bottom` (lower part of the model below the waist, used in trousers etc.), `portrait` (model face, normally up to the shoulder), `detail` (a close-up of some details in the product), `outfit` (complete view of the model, entire body along with other apparel and accessories), `UNK`.
| | | *The fields `ignore`,`model_worn`, `pose` and `camera_focus` are optional.*
color | list of string | A list of colors terms that describe the colors in the product.
pattern | list of string | A list of pattern terms that describe the pattern on the fabric.
fabric | list of string | A list of terms that describe the fabric used in the product.
brand | list of string | A list of terms that describe the brand name of the product.
occasion | list of string | A list of terms that describe occasion for which this product can be worn.
season | list of string | A list of terms that describe the season for which this product can be worn.
attributes | list of string | A list of terms that describe other attributes like sleeve length, collar etc. of the product.
description | string | A detailed free text description of the product if available.
style_tip | string | A detailed free text style tip if available. Typically this suggests what to pair this with.
available_sizes | list of string | A list of available sizes for the product.
price | float | The regular list price of the product.
sale_price | float | The sale price of the product if the product is on sale. If the product is not on sale make this equal to the regular price.
|  | |*Note that the natural language search by default will use the sale_price field  for the queries that deal with price.*
sale_percentage | float | The sale percentage. This is useful for queries related to sale percentages.
currency | string | The currency used, specified as a 3 letter [ISO 4217](http://www.xe.com/iso4217.php) currency code.
|  | |*The catalog can have products in different currencies, and we will normalize the price internally for serving the query results.* (Not yet available)

## Add product to a catalog    

```python
#------------------------------------------------------------------------------
# Add product to a catalog.    
# POST /v1/catalog/{catalog_name}/products/{id}
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

# You need to give a name to your catalog.
catalog_name = 'sample_catalog'

# Some sample data where each product in the catalog is in a json format
# described earlier.
catalog_folder = 'sample_catalog'
json_filenames = [f for f in os.listdir(catalog_folder) if not f.startswith('.')]

params = {}
# Optional parameters.
params['download_images'] = 'true'

for filename in json_filenames:
    # load the json file
    with open(os.path.join(catalog_folder,filename),'r') as f:
        data = json.loads(f.read())

    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,data['id'])
    url = urljoin(api_gateway_url,api_endpoint)
    
    response = requests.post(url,
                             headers=headers,
                             json=data,
                             params=params)
    
    print response.status_code
    print response.json()
```

> Sample json response

```json
{
 "time_ms": "560.34",
 "version": 1,
 "id": "ABOFA15AWWWTP1101602",
 "n_images": 7,
 "n_images_downloaded": 5,
 "n_images_ignored": 2,
 "n_images_error": 0,
 "n_images_existing": 0
}
```

Add a product json to a catalog and download the images. 

Anytime you add a json to a catalog for the first time a new catalog will be automatically created with the name specified in the parameters `catalog_name`. The same `catalog_name` will have to be used for other end points.

### End point

`POST /v1/catalog/{catalog_name}/products/{id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
data | application/json | (**Required**) The json object file in the the format specified [earlier](#product-json-format). |  
download_images | query | By default all the images specified in the json will be downloaded. Set this to `false` if you do not want to download the images.  | `true`  


### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
id | The product id.
version | The version number. Every update will be assigned a new version number.
n_images | The total number of images in the json file.
n_images_downloaded | The actual number of images successfully downloaded. 
n_images_ignored | The total number of images ignored (specified by the field `ignore` in `images`). 
n_images_error | The number of images for which there was some error in downloading. 
n_images_existing | The number of images which were already downloaded.

<aside class="notice">
Note that the image download happens only once. If you post the json again the image will not be downloaded again unless there is a change in the image url. 
</aside>

## Get product from a catalog

```python
#-----------------------------------------------------------------------------
# Get product from a catalog.  
# GET /v1/catalog/{catalog_name}/products/{id}
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

# catalog name
catalog_name = 'sample_catalog'

# product id
id = '20DRA16FWCWFT9010970'

# API end point
api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)
print response.status_code
print response.json()
```

> Sample json response

```json
{
 "time_ms": "1.99",
 "id": "20DRA16FWCWFT9010970",
 "version": 2,
 "found": true,
 "data": {
  "color": [
   "copper"
  ],
  "taxonomy": "women;footwear;sandals-heels",
  "age_group": [
   "teenager",
   "adult"
  ],
  "currency": "INR",
  "images": {
   "1": {
    "camera_focus": "UNK",
    "pose": "front",
    "ignore": "no",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Front_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_1_front_UNK.jpg"
   },
   "3": {
    "camera_focus": "UNK",
    "pose": "right",
    "ignore": "no",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Right_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_3_right_UNK.jpg"
   },
   "2": {
    "camera_focus": "UNK",
    "pose": "left",
    "ignore": "no",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Left_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_2_left_UNK.jpg"
   },
   "5": {
    "camera_focus": "UNK",
    "pose": "top",
    "ignore": "no",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Top_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_5_top_UNK.jpg"
   },
   "4": {
    "camera_focus": "detail",
    "pose": "UNK",
    "ignore": "yes",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Detail_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_4_UNK_detail.jpg"
   },
   "6": {
    "camera_focus": "full",
    "pose": "front",
    "ignore": "yes",
    "image_url": "http://images.abofcdn.com/catalog/images/2015/20DRA16FWCWFT9010970/Look_Large.jpg",
    "model_worn": "yes",
    "image_filename": "20DRA16FWCWFT9010970_6_front_full.jpg"
   }
  },
  "available_sizes": [
   "36",
   "37",
   "38",
   "39",
   "40",
   "41"
  ],
  "style_tip": "For a comfortable and stylish look, team these sandals with a pair of shorts and a T-shirt. Add on a watch to complete the look.",
  "id": "20DRA16FWCWFT9010970",
  "category": "flats",
  "fabric": [
   "upper: faux suede, sole: tpr"
  ],
  "pattern": "UNK",
  "sale_percentage": 9.966555183946488,
  "sale_price": 1346.0,
  "brand": "20dresses",
  "description": "Pointy-toed brown sandals with low top styling, has a faux suede upper, stylized lace-ups across the mid-foot that connects to form an ankle loop, patterned outsole",
  "season": "UNK",
  "price": 1495.0,
  "occasion": "UNK",
  "date": "2016-10-12",
  "name": "20dresses women brown sandals",
  "url": "http://www.abof.com/product/20DRA16FWCWFT9010970-20Dresses-Women-Brown-Sandals",
  "gender": "female",
  "attributes": "UNK",
  "out_of_stock": "no"
 }
}
```

Get the product json from a catalog.

### End point

`GET /v1/catalog/{catalog_name}/products/{id}`

### Request

Parameter | Type | Description | 
--------- | ------- | ----------- 
catalog_name | path | (**Required**) The catalog name. 
id | path | (**Required**) The product id. 

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
id | The product id.
version | The version number. Every update will be assigned a new version number.
data | The product json.

## Delete product from a catalog

```python
#------------------------------------------------------------------------------
# Delete product from a catalog.
# DELETE /v1/catalog/{catalog_name}/products/{id}
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

# Catalog name.
catalog_name = 'sample_catalog'

# Product id.
id = '20DRA16FWCWHL9012909'

# API end point.
api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)

print response.status_code
print response.json()
```


> Sample json response

```json
{
 "deleted": true,
 "time_ms": "4.01",
 "version": 4,
 "id": "20DRA16FWCWHL9012909"
}
```

Delete the product from a catalog.

### End point

`DELETE /v1/catalog/{catalog_name}/products/{id}`

### Request

Parameter | Type | Description | 
--------- | ------- | ----------- 
catalog_name | path | (**Required**) The catalog name. 
id | path | (**Required**) The product id. 

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
id | The product id.
version | The version number. 
deleted | This will be true if the product was successfully deleted.

## Update product in a catalog

```python
#------------------------------------------------------------------------------
# Update product in a catalog.  
# PUT  /v1/catalog/{catalog_name}/products/{id}
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

# Catalog name.
catalog_name = 'sample_catalog'

# Product id.
id = '20DRA16FWCWFT9010970'

# API end point,
api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)

url = urljoin(api_gateway_url,api_endpoint)

data = {}
data['out_of_stock'] = 'yes'

params = {}
# Optional parameters.
params['download_images'] = 'true'

response = requests.put(url,
                        headers=headers,
                        json=data,
                        params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "time_ms": "12.83",
 "version": 6,
 "id": "20DRA16FWCWFT9010970"
}
```

Update the product in a catalog. This is useful to make partial/full updates to a product in the catalog. For example

- A product goes out-of-stock and you can just change the `out_of_stock` field in the product json to `yes` to reflect this change. 
- There is a sale for the product and and you can update the `sale_price` and `sale_percentage` fields.

### End point

`PUT /v1/catalog/{catalog_name}/products/{id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
data | application/json | (**Required**) The json object file in the the format specified [earlier](#product-json-format) containing only the modifications. |  
download_images | query | By default all the images specified in the json will be downloaded. Set this to `false` if you do not want to download the images.  | `true`  

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
id | The product id.
version | The version number. Every new update creates a new version number.

## Get info about a product catalog

```python
#------------------------------------------------------------------------------
# Get info about a product catalog.  
# GET /v1/catalog/{catalog_name}
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

# Catalog name.
catalog_name = 'sample_catalog'

# API end point.
api_endpoint = '/v1/catalog/%s'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "num_of_products": 9,
 "time_ms": "39.59",
 "num_of_images": 54,
 "catalog_name": "sample_catalog"
}
```

Get some info about the product catalog. Currently this just returns the number of products and the number of images. 

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

## Delete a product catalog     

```python
import os
import json
import requests
from urlparse import urljoin

api_gateway_url = 'http://localhost:9080'

# Pass the api key into the header
headers = {'X-Api-Key': 'your_api_key'}

# You need to give a name to your catalog.
catalog_name = 'sample_catalog'

#------------------------------------------------------------------------------
# Delete a product catalog.
# DELETE /v1/catalog/{catalog_name}
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s'%(catalog_name)
url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)
print response.status_code, response.json()
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

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
catalog_name | The catalog name.
deleted | This returns true is the catalog was successfully deleted.

## Get all catalog names    

```python
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

# API end point.
api_endpoint = '/v1/catalog_names'

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

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

# Natural Language Search

The Natural Language Search (NLS) for fashion allows the user to search a fashion e-commerce catalog using complex **natural language queries**. Below are a few sample queries that NLS can handle:

- *Show me some black graphic print cotton tees under 1k in small size on sale.*
- *Can you show me some red dresses for my girlfriend for around 100 dollars ?*
- *Show me some red mandarin collar shirts.*
- *Show me some edgy longline tees for a party tonight.*

NLS enables the user to query the product catalog in a more natural language as you would have a conversation with seasoned sales assistant. This functionality enables a user to ask his query (requirements) in a more natural manner than the conventional keywords and filter based search. NLS can be seen as replacement for conventional search based on filters and a precursor to a full conversation system. NLS will work on existing product text attributes in product catalog. 

Following is a list of some of the features that NLS supports:

1. **Leverages the underlying IBM Fashion Taxonomy** : NLS is heavily tuned for the fashion domain and leverages the underlying IBM fashion taxonomy. IBM fashion taxonomy is a semi-automatically curated comprehensive hierarchical fashion taxonomy for apparel,footwear, and accessories along with their attributes and relations. The taxonomy is curated from several fashion resources and then hand tuned by domain experts. This enables NLS to a very nuanced understanding of user requirements in the domain of fashion.

1. **Understands various fashion attributes and user constraints** : NLS is grounded in the fashion taxonomy and can understand various attributes in the query such as gender, category name (apparel,shoes,accessories), color, pattern/print, fabric/material, brand, price, size, occasion, sale/discount other category specific attributes (like sleeves, collar, etc.). NLS  can understand all the constraints on user requirements even when they are expressed in a very natural manner in the form of long complex natural language queries (e.g. ‘Show me red floral dresses for my niece under 1k on sale.’).

1. **Internally handles query synonyms** : IBM Fashion taxonomy is used to map segments of user utterances to nodes in the Fashion taxonomy. This mapping enables understanding of fashion concepts even when they are expressed in many different ways. For example, the user may search for a ‘tee’ whereas the item may be tagged as a ‘t-shirt’ in the catalog. NLS understands various synonyms and also common query words for different apparel and accessories.

1. **Appropriate semantic query back off** : The NLS capability allows showing relevant results to a user query even when there are no enough products that exactly matches with the user requirements. For example, if there are no ‘red floral dresses’ in the catalog then NLS has a built in back-off strategy that removes attributes (along with color expansion and similar brands substitution) till it finds sufficient number of products. For example, in this case it would first back off to ‘orange/tomato/red floral dresses’ and then to ‘red dresses’ and eventually to ‘dresses’.

1. **Color Substitution** : If the user searches for a specific color (say 'golden colored dresses') and that particular color is not available then we search for other color that look very similar (for example, yellow, yellow green, khaki etc.). The IBM Fashion Taxonomy also includes a detailed taxonomy specific to colors and their corresponding names. Is also provides a notion of visual semantic distance between two colors.

1. **Brand Substitution** : A similar back-off strategy is incorporated for a limited set of brands. If the user searches for a specific brand (say ‘adidas shoes’) and that particular brand is not available then we search for other similar brands (for example, ‘puma shoes’). The notion is brand similarity is based on the visual appearance of products and is computed using our visual similarity asset.

1. **Spelling and phonetic corrections tuned to fashion taxonomy** : Users usually make mistakes when searching with natural language queries. NLS can handles common spelling mistakes in a user query. The spelling correction module is aware of all the fashion related concepts and brands.

1. **Themes and collections capsules** : NLS offers limited support for search related to themes and collections (e.g. athleisure, monochrome, indigo, ethnic etc. ). We automatically curate the set of apparel, accessories and their attributes that define a fashion theme from various fashion blogs and when the user searches for a theme (say athleisure) then we show items from the catalog that are relevant to that theme.

1. **Dynamically evolving fashion taxonomy** : Fashion evolves constantly and hence IBM fashion taxonomy is dynamically updated at regular intervals to include the current fashion terms and trends. Since NLS is delivered as a REST API the user will always have access to the latest fashion taxonomy.

## Natural Language Search

```python
#------------------------------------------------------------------------------
# Natural Language Search 
# GET /v1/catalog/{catalog_name}/natural_language_search
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

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/natural_language_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'Watson show me some shrigs for my neice'
# Optional parameters.
params['max_number_of_results'] = 12
params['max_number_of_backoffs'] = 5

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()

# Get more info about the relevant results
for product in response.json()['products']:
    id = product['id']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    print('%s %s'%(id,response.json()['data']['name']))
```

> Sample json response

```json
{
 "time_ms": "18.14",
 "products": [
  {
   "id": "20DRA16FWCWHL9015309",
   "backoff_number": 0
  },
  {
   "id": "20DRA16FWCWPP9014709",
   "backoff_number": 0
  }
 ]
}
```

Search the product catalog using a natural language query (e.g. `Show me some black graphic print tees on sale.`).

### End point

`GET /v1/catalog/{catalog_name}/natural_language_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
catalog_name | path | (**Required**) The catalog name. 
query_text | query | (**Required**) The natural language search query. (e.g. `Show me some red graphic print tees under 1k.``) |
max_number_of_results | query | The maximum number of results to return. | 12
max_number_of_backoffs | query | The maximum number of backoffs to use if there are no exact matches. Set this to 0 if you do not want any backoffs. | 5

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list of products in the catalog relevant to the query.
id | The id of the product.
backoff_number | An integer specifying if backoff was used for retrieving this result. (0 corresponds to no backoff, 1 corresponds to backoff once and so on)

## Get elasticsearch queries

```python
#------------------------------------------------------------------------------
# elasticsearch queries 
# GET /v1/natural_language_search/elasticsearch_queries
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

api_endpoint = '/v1/natural_language_search/elasticsearch_queries'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'Show me some peach graphic print tees for my neice under 1k on sale.'
# Optional parameters.
params['max_number_of_results'] = 12
params['max_number_of_backoffs'] = 5

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
  "time_ms": "6.35", 
  "elasticsearch_queries": [
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"minimum_should_match": 1, "filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "should": [{"multi_match": {"query": "t-shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "tee", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}], "must": [{"multi_match": {"query": "red", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}, {"bool": {"minimum_should_match": 1, "should": [{"multi_match": {"query": "graphic", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "printed", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "print", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}]}}]}}, "from": 0, "size": 12}, 
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"minimum_should_match": 1, "filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "should": [{"multi_match": {"query": "t-shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "tee", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}], "must": [{"bool": {"minimum_should_match": 1, "should": [{"multi_match": {"query": "tomato", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}, {"multi_match": {"query": "orange", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}, {"multi_match": {"query": "red", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}]}}, {"bool": {"minimum_should_match": 1, "should": [{"multi_match": {"query": "graphic", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "printed", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "print", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}]}}]}}, "from": 0, "size": 12}, 
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"minimum_should_match": 1, "filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "must": [{"multi_match": {"query": "red", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}], "should": [{"multi_match": {"query": "t-shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "tee", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}]}}, "from": 0, "size": 12}, 
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "minimum_should_match": 1, "should": [{"multi_match": {"query": "t-shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "tee", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}]}}, "from": 0, "size": 12}, 
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"minimum_should_match": 1, "filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "should": [{"multi_match": {"query": "graphic", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "printed", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}, {"multi_match": {"query": "print", "type": "phrase", "fields": ["pattern^5.0", "name^2.0", "description^1.0"]}}], "must": [{"multi_match": {"query": "knit shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "red", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}]}}, "from": 0, "size": 12}, 
  {"sort": [{"sale_price": {"order": "desc"}}], "query": {"bool": {"filter": [{"bool": {"must": [{"range": {"sale_price": {"lte": 1000}}}, {"range": {"sale_price": {"gte": 1}}}, {"terms": {"gender": ["female", "women"]}}, {"term": {"out_of_stock": "no"}}]}}], "must": [{"multi_match": {"query": "knit shirt", "type": "phrase", "fields": ["category^2.0", "name^1.0"]}}, {"multi_match": {"query": "red", "type": "phrase", "fields": ["color^5.0", "name^1.0"]}}]}}, "from": 0, "size": 12}
  ]
}
```

<aside class="notice">
The next set of natural language search APIs are genric APIs in that they do not depend on the product catalog.
</aside>

Parse any fashion related query (e.g. `Show me some red graphic print tees for my neice under 1k on sale.`) in natural language and return a list of elasticsearch queries in the [query DSL format](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html).  The queries are ordered from specific to general queries. The suggested usage is to first fire the first query. If there are no sufficient results then fire the next query and so on. The queries use the field names as specified in the [product JSON format](#product-json-format).
                
### End point

`GET /v1/natural_language_search/elasticsearch_queries`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
query_text | query | (**Required**) The natural language search query. (e.g. `Show me some red graphic print tees for my neice under 1k on sale.`) |
max_number_of_results | query | The maximum number of results to return. | 12
max_number_of_backoffs | query | The maximum number of backoffs to use if there are no exact matches. Set this to 0 if you do not want any backoffs. | 5

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
elasticsearch_queries | A list of elasticsearch queries (ordered from specific to general) in the [query DSL format]((https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)). 

## Parse fashion query/text

```python
#------------------------------------------------------------------------------
# Parse fashion query/text. 
# GET /v1/natural_language_search/parse
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

api_endpoint = '/v1/natural_language_search/parse'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'Show me some red graphic print tees for my neice under 1k on sale.'

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "parse": [
  {
   "apparel": {
    "synonyms": [
     "t-shirt",
     "tee"
    ],
    "hypernyms": [
     "knit shirt"
    ],
    "name": "tee",
    "hyponyms": []
   },
   "attributes": {
    "color": [
     {
      "similar_colors": [
       "red",
       "tomato",
       "orange"
      ],
      "synonyms": [
       "red"
      ],
      "name": "red"
     }
    ],
    "pattern": [
     {
      "synonyms": [
       "graphic"
      ],
      "name": "graphic"
     },
     {
      "synonyms": [
       "printed",
       "print"
      ],
      "name": "print"
     }
    ],
    "price": {
     "currency": "rupee",
     "upper_limit": 1000,
     "lower_limit": 0,
     "sale_percentage_lower_limit": 1
    }
   },
   "remaining_tokens": [],
   "gender": "female"
  }
 ],
 "time_ms": "2.85"
}
```

Given any natural language fashion query this endpoint parses the query and returns an intermediate structured parse of the query.

<aside class="notice">
The structured parse is grounded in the fashion taxonomy and can understand various apparel/accessory names and their corresponding attributes like gender, category name (apparel,shoes,accessories), color, pattern/print, fabric/material, brand, price (can handle under, above, around and currency denotations.), size, occasion, sale/discount and other category specific attributes defined by the fashion taxonomy. The structured parse is also enriched using the fashion taxonomy to include synonyms for apparel terms, hyponyms (any its synonyms) for apparel terms, synonyms for attribute terms, hypernyms for query-backoff and other similar color and brand names.
</aside>

### End point

`GET /v1/natural_language_search/parse`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
query_text | query | (**Required**) The natural language search query. (e.g. `Show me some red graphic print tees for my neice under 1k on sale.`) |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
parse |  A list of structured parse of the natural language query grounded in the fashion taxonomy (see the sample json).  For gender agnostic queries returns two parses one for each gender. If the text contains multiple apparel returns one parse for each apparel.

## Get search terms

```python
#------------------------------------------------------------------------------
# Get search terms. 
# GET /v1/natural_language_search/search_terms
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

api_endpoint = '/v1/natural_language_search/search_terms'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'Show me some red graphic print tees for my neice under 1k on sale.'

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "time_ms": "3.13",
 "search_terms": [
  {
   "apparel": [
    "t-shirt",
    "tee"
   ],
   "color": [
    "red"
   ],
   "pattern": [
    "graphic",
    "printed",
    "print"
   ],
   "apparel_hypernyms": [
    "knit shirt"
   ],
   "similar_colors": [
    "tomato",
    "orange",
    "red"
   ]
  }
 ]
}
```

Parse a fashion related natural language query and generate search terms for its constituent apparel and various attributes. These search terms are used to compose the final elasticsearch/solr query and includes synonyms and hyponyms for the apparel and attributes.

### End point

`GET /v1/natural_language_search/search_terms`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
query_text | query | (**Required**) The natural language search query. (e.g. `Show me some red graphic print tees for my neice under 1k on sale.`) |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
search_terms |  A list of search terms corresponding to each parse.

## Spelling Correction

```python
#------------------------------------------------------------------------------
# Spelling correction. 
# GET /v1/natural_language_search/spell_correct
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

api_endpoint = '/v1/natural_language_search/spell_correct'

url = urljoin(api_gateway_url,api_endpoint)

params = {}
params['query_text'] = 'show me some purlep floaral dreses for my wife'

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "time_ms": "0.24",
 "corrected_text": "show me some purple floral dress for my wife"
}
```

Correct any spelling mistakes in the query. This is a lightweight spelling correction module that is tuned to the fasion taxonomy.

### End point

`GET /v1/natural_language_search/spell_correct`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | -----------
query_text | query | (**Required**) The natural language search query. (e.g. `show me some purlep floaral dreses for my wife.`) |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
corrected_text |  The text with the spelling corrected.

# Visual Search

A collection of APIs for enabling a better search experience using the catalog images. Visual search helps a consumer discover exact match or show similar fashion products based on image search. 

These APIs support the following two use cases:

1. **Visual Browse:  Show me more like this** A user clicks on an existing apparel/accessory image in the catalog and visual browse will fetch and display similar looking products from the catalog. This is a more interactive way of browsing the catalog based on visual search. It essentially recreates the experience of asking a shopkeeper “Show me more dresses like this.” and encourages serendipitous buying. This can also be used for finding other similar looks for any product in the catalog.

1. **Visual Search: Search the look** Consumer can upload any fashion image of his/her liking and visual search will show visually similar products that exist in the catalog. 

## Build visual search index

```python
#------------------------------------------------------------------------------
# Build the visual search index.  
# GET /v1/catalog/{catalog_name}/visual_search_index
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

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "status": {
  "status": "done",
  "visual_search_index": {
   "num_of_images_missing_features": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  },
  "num_of_products": "10",
  "start_time": "2017-01-21 03:46:32.043609",
  "finish_time": "2017-01-21 03:47:24.785563",
  "num_of_images": "60",
  "feature_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  }
 },
 "time_ms": "52746.07"
}
```

Build the visual search index.

Before you can start using the visual search and visual browse APIs the visual search index has to be built first. This API computes the feature representations for all the uploaded images and then builds an index for fast nearest neighbor retrieval.

<aside class="notice">
This is a one time long running operation. Currently it takes around 250-500 milliseconds to compute the features for one image on a bluemix instance (until bluemix supports GPU instances). Depending on your catalog size you will have to wait till the index creation is completed to start using the visual search APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status becomes `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_features`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**feature_computation** | Info about feature computation.
num_of_images_error | The number of images for which there was some error in feature computation.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**visual_search_index** | Info about the index building.
num_of_images_missing_features | The number of images for which there was no feature found.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_index | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index. Note that the features for the old images will still be saved and we will be computing the features only for the newly added images.
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get visual search index status

```python
#------------------------------------------------------------------------------
# Get the status of the visual search index. 
# GET  /v1/catalog/{catalog_name}/visual_search_index
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

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "status": "computing_features",
 "visual_search_index": {
  "num_of_images_missing_features": "0",
  "num_of_images_indexed": "0",
  "num_of_images_ignored": "0"
 },
 "num_of_products": "10",
 "time_ms": "3.72",
 "start_time": "2017-01-21 04:05:50.445071",
 "finish_time": "0",
 "num_of_images": "60",
 "feature_computation": {
  "num_of_images_ignored": "6",
  "num_of_images_existing": "0",
  "num_of_images_error": "0",
  "num_of_images_processed": "19"
 }
}
```

Get the status of the visual search index.

### End point

`GET /v1/catalog/{catalog_name}/visual_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_features`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**feature_computation** | Info about feature computation.
num_of_images_error | The number of images for which there was some error in feature computation.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_existing | The number of images for which the features were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the features were computed. 
**visual_search_index** | Info about the index building.
num_of_images_missing_features | The number of images for which there was no feature found.
num_of_images_ignored | The number of images that were ignored (for example the images of type `detail`).
num_of_images_index | The total number of images finally indexed.

## Delete visual search index 

```python
#------------------------------------------------------------------------------
# Delete the visual search index. 
# DELETE  /v1/catalog/{catalog_name}/visual_search_index
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

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search_index'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "deleted": true,
 "time_ms": "39.63",
 "catalog_name": "sample_catalog"
}
```

Delete the visual search index.

### End point

`DELETE /v1/catalog/{catalog_name}/visual_search_index`

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

## Visual Browse

```python
#------------------------------------------------------------------------------
# Visual Browse
#
# Get other visually similar products in the catalog.
# GET /v1/catalog/{catalog_name}/visual_search/{id}/{image_id}
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

# Catalog name.
catalog_name = 'sample_catalog'

id ='109FA16AWWWSG9G06Y05'
image_id = '1'

api_endpoint = '/v1/catalog/%s/visual_browse/%s/%s'%(catalog_name,id,image_id)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
# Optional parameters.
params['max_number_of_results'] = 5

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()

# List the relevant images
for product in response.json()['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    print('%s %s %1.2f %s'%(id,image_id,score,image_url))

```

> Sample json response

```json
{
 "time_ms": "0.96",
 "products": [
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.9999205280983006
  },
  {
   "image_id": "2",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.47892576456069946
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWPP9014709",
   "similarity": 0.4464869499206543
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9015309",
   "similarity": 0.429345965385437
  },
  {
   "image_id": "6",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.39676886796951294
  }
 ]
}
```

Find other similar images in the product catalog for a given image based on visual appearance.

<aside class="notice">
Note that the visual search index has to be built first before we can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/visual_browse/{id}/{image_id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1 (similar). 

## Visual Search

```python
#------------------------------------------------------------------------------
# Visual Search
#
# Get visually similar products in the catalog for an uploaded image.
# POST /v1/catalog/{catalog_name}/visual_search
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

# Catalog name.
catalog_name = 'sample_catalog'

api_endpoint = '/v1/catalog/%s/visual_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

params = {}
# Optional parameters.
params['max_number_of_results'] = 5

headers['Content-Type'] = 'image/jpeg'

response = requests.post(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_1.jpeg','rb'))

print response.status_code
print response.json()

# List the relevant images
for product in response.json()['products']:
    id = product['id']
    image_id = product['image_id']
    score = product['similarity']
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)
    url = urljoin(api_gateway_url,api_endpoint)
    response = requests.get(url,headers=headers)
    image_url = response.json()['data']['images'][image_id]['image_url']
    print('%s %s %1.2f %s'%(id,image_id,score,image_url))
```

> Sample json response

```json
{
 "time_ms": "1333.02",
 "products": [
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.9999205280983006
  },
  {
   "image_id": "2",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.47892576456069946
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWPP9014709",
   "similarity": 0.4464869499206543
  },
  {
   "image_id": "3",
   "id": "20DRA16FWCWHL9015309",
   "similarity": 0.429345965385437
  },
  {
   "image_id": "6",
   "id": "20DRA16FWCWHL9012909",
   "similarity": 0.39676886796951294
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
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list of other visually similar images. The first item in the list is the specified image.
image_id | The image id.
id | The product id.
similarity | The similarity score in the range from 0(dissimilar)  to 1 (similar). 

# Colors

These are a few APIs for dealing with color terms. A **color term**, also known as a color name, is a word or phrase that refers to a specific color. For example *red* is the color term used for a color with RGB values [255,0,0]. While the number of color terms are quite huge we restrict ourselves a set of color terms that are broadly understood by a layperson and skip the more esoteric color terms. Color similarities are computed using the [Delta E (CIE2000)](https://en.wikipedia.org/wiki/Color_difference) metric in the LAB space.

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
 "time_ms": "6.37",
 "color_terms": [
  "gray",
  "grey",
  "teal",
  "brown",
  "olive"
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
color_terms |  A list of five closest color terms.

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

response = requests.get(url,headers=headers,params=params)

print response.status_code
print response.json()
```

> Sample json response

```json
{
 "similar_colors": [
  "purple",
  "violet",
  "navy blue",
  "maroon",
  "blue"
 ],
 "time_ms": "0.01"
}
```

Get other color terms similar to a given color term.

### End point

`GET /v1/colors/similar_colors`

### Request

Parameter | Type | Description 
--------- | ------- | ----------- 
color_term  | query | (**Required**) The color term.

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
similar_colors |  A list of five closest color terms.

## Get color similairty 

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
similarity_score |  The similarity score (between 0 and 1) which indicates the similarity between the two color terms. Interanlly it uses the [CIEDE2000](https://en.wikipedia.org/wiki/Color_difference) metric to measure similarity between two color terms. 



# Color Search

A collection of APIs for enabling catalog search based on colors.

## Build color search index

```python
#------------------------------------------------------------------------------
# Build the color search index.
# POST /v1/catalog/{catalog_name}/color_search_index
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
params['color_count'] = 3
params['quality'] = 1
params['image_max_dimension'] = 256

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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
  "color_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  },
  "finish_time": "2017-11-18 04:35:03.112859",
  "start_time": "2017-11-18 04:34:51.450504",
  "pid": "224",
  "index_updated": "true",
  "num_of_products": "10",
  "num_of_images": "60",
  "color_search_index": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  }
 },
 "time_ms": "1.97"
}
```

Build the color search index.

Before you can start using the color search APIs the color search index has to be built. This API computes the dominant colors for all the uploaded images in the catalog and then builds an index for fast nearest neighbor retrieval.

<aside class="notice">
This is a one time long running operation. Currently it takes around 200-500 milliseconds to compute the dominant colors for one image. Depending on your catalog size you will have to wait till the index creation is completed to start using the color search APIs. You can query the status of the index creation using the GET endpoint. The index is built when the status changes to `done`
</aside>

### End point

`POST /v1/catalog/{catalog_name}/color_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
color_count | query | The maximum number of dominant colors to extract per image. | 3
quality | query | This parameter provides a trade off between the computation time and the quality of the dominant colors. Larger the number faster is the dominant color computation but greater the chance that the colors will be missed. 1 is the highest quality. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_color`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**color_computation** | Info about dominant color computation.
num_of_images_error | The number of images for which there was some error in dominant color computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant colors were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant colors were computed. 
**color_search_index** | Info about the index building.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

<aside class="notice">
Any time you add new images to the catalog you will have to rebuild the index by calling this API again. Note that the dominant colors for the old images will still be saved and we will be computing the dominant colors only for the newly added images.
</aside>

<aside class="warning">
The current version supports building only one index at the same time and does not allow you to build another index when a current index building process in running. Please wait till the current index creation is completed. 
</aside>

## Get color search index status

```python
#------------------------------------------------------------------------------
# Get the status of the color search index.
# GET /v1/catalog/{catalog_name}/color_search_index
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

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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
  "color_computation": {
   "num_of_images_ignored": "14",
   "num_of_images_existing": "0",
   "num_of_images_error": "0",
   "num_of_images_processed": "46"
  },
  "finish_time": "2017-11-18 04:35:03.112859",
  "start_time": "2017-11-18 04:34:51.450504",
  "pid": "224",
  "index_updated": "true",
  "num_of_products": "10",
  "num_of_images": "60",
  "color_search_index": {
   "num_of_images_missing_colors": "0",
   "num_of_images_indexed": "46",
   "num_of_images_ignored": "14"
  }
 },
 "time_ms": "1.97"
}
```

Get the status of the color search index.

### End point

`GET /v1/catalog/{catalog_name}/color_search_index`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status of the index building process.
status | The current status of the index building process. The status can be one of `computing_dominant_color`,`building_index`,or `done`. The index is built when the status becomes `done`.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
start_time | The time when the index building started.
finish_time | The time when the index building finished.
**color_computation** | Info about dominant color computation.
num_of_images_error | The number of images for which there was some error in dominant color computation.
num_of_images_ignored | The number of images that were ignored.
num_of_images_existing | The number of images for which the dominant colors were already computed (in case of building a new index with new products added).
num_of_images_processed | The total number of images for which the dominant colors were computed. 
**color_search_index** | Info about the index building.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_indexed | The total number of images finally indexed.

## Delete the color search index 

```python
#------------------------------------------------------------------------------
# Delete the color search index.
# DELETE /v1/catalog/{catalog_name}/color_search_index
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

api_endpoint = '/v1/catalog/%s/color_search_index'%(catalog_name)

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

Delete the color search index.

### End point

`DELETE /v1/catalog/{catalog_name}/color_search_index`

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

## Color Search by RGB

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?r=255&g=0&b=0m&ax_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a specified RGB value.
# GET /v1/catalog/{catalog_name}/color_search
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

# Parameters.
params['r'] = 255
params['g'] = 0
params['b'] = 0
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "98.54",
 "products": [
  {
   "distance": 51.557655334472656,
   "image_filename": "SHRES16AWFSDR9346B_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "1"
  },
  {
   "distance": 53.42538833618164,
   "image_filename": "SHRES16AWFSDR9346B_4.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "4"
  },
  {
   "distance": 59.01533126831055,
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "5"
  },
  {
   "distance": 67.46210479736328,
   "image_filename": "SHRES16AWFSDR9346A_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_id": "1"
  },
  {
   "distance": 67.5510482788086,
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "5"
  }
 ]
}
```

Get similar color images in the catalog for a specified RGB value.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
r | query | (**Required**) The value for the red channel in the RGB color model (in the range 0 to 255).
g | query | (**Required**) The value for the green channel in the RGB color model (in the range 0 to 255).
b | query | (**Required**) The value for the blue channel in the RGB color model (in the range 0 to 255).
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.

## Color Search by name

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?color_term=red&ax_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a specified color name..
# GET /v1/catalog/{catalog_name}/color_search
# params - color_term,max_number_of_results
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
params['color_term'] = 'red' 
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "214.84",
 "products": [
  {
   "distance": 51.557655334472656,
   "image_filename": "SHRES16AWFSDR9346B_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "1"
  },
  {
   "distance": 53.42538833618164,
   "image_filename": "SHRES16AWFSDR9346B_4.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "4"
  },
  {
   "distance": 59.01533126831055,
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "5"
  },
  {
   "distance": 67.46210479736328,
   "image_filename": "SHRES16AWFSDR9346A_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_id": "1"
  },
  {
   "distance": 67.5510482788086,
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "5"
  }
 ]
}
```

Get similar color images in the catalog for a specified color name.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
color_term  | query | (**Required**) The color term (e.g. red).
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.

## Color Search by id

```shell
http://api_gateway_url/v1/catalog/sample_catalog/color_search?id=SHRES16AWFSDR9346B&image_id=1&color_count=1&max_number_of_results=5&api_key=your_api_key
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for an existing image.
# GET /v1/catalog/{catalog_name}/color_search
# params - id,image_id,color_count,max_number_of_results
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
params['id'] = 'SHRES16AWFSDR9346B' 
params['image_id'] = '1'
params['color_count'] =  1
params['max_number_of_results'] = 5

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

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
 "time_ms": "25.82",
 "products": [
  {
   "distance": 0.0,
   "image_filename": "SHRES16AWFSDR9346B_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "1"
  },
  {
   "distance": 0.0,
   "image_filename": "SHRES16AWFSDR9346B_2.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "2"
  },
  {
   "distance": 0.35167253017425537,
   "image_filename": "LPJNA16AMDMTE91662_2.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/LPJNA16AMDMTE9166200/Right_Medium.jpg",
   "id": "LPJNA16AMDMTE91662",
   "image_id": "2"
  },
  {
   "distance": 0.629985511302948,
   "image_filename": "SHRES16AWFSDR9346B_4.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "4"
  },
  {
   "distance": 0.6313580870628357,
   "image_filename": "LPJNA16AMDMTE91662_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/LPJNA16AMDMTE9166200/Front_Medium.jpg",
   "id": "LPJNA16AMDMTE91662",
   "image_id": "1"
  }
 ]
}
```

Get similar color images in the catalog for an existing image.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
image_id | path | (**Required**) The image id. |
color_count | path | The maximum number of dominant colors to search for in an image. | 1
max_number_of_results | query | maximum number of results to return | 12
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.

## Color Search by image

```shell
```

```python
#------------------------------------------------------------------------------
# Get similar color images in the catalog for a given uploaded image.
# GET /v1/catalog/{catalog_name}/color_search
# params - color_count,image_max_dimension,max_number_of_results
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
params['color_count'] =  2
params['max_number_of_results'] = 5

headers['Content-Type'] = 'image/jpeg'

# Catalog name.
catalog_name = props['catalog_name']

api_endpoint = '/v1/catalog/%s/color_search'%(catalog_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,
                        headers=headers,
                        params=params,
                        data=open('test_image_2.jpeg','rb'))

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "time_ms": "1069.26",
 "products": [
  {
   "distance": 21.60784339904785,
   "image_filename": "SHRES16AWFSDR9346B_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "1"
  },
  {
   "distance": 22.59739112854004,
   "image_filename": "SHRES16AWFSDR9346B_4.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Right_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "4"
  },
  {
   "distance": 28.862674713134766,
   "image_filename": "SHRES16AWFSDR9346B_5.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Front_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "5"
  },
  {
   "distance": 36.39692306518555,
   "image_filename": "SHRES16AWFSDR9346A_1.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346A54/Look_Medium.jpg",
   "id": "SHRES16AWFSDR9346A",
   "image_id": "1"
  },
  {
   "distance": 37.1317138671875,
   "image_filename": "SHRES16AWFSDR9346B_2.jpg",
   "image_url": "http://images.abofcdn.com/catalog/images/2015/SHRES16AWFSDR9346B18/Left_Medium.jpg",
   "id": "SHRES16AWFSDR9346B",
   "image_id": "2"
  }
 ]
}
```


Get similar color images in the catalog for a given uploaded image.

<aside class="notice">
The color search index has to be built first before you can use this API.  
</aside>

### End point

`GET /v1/catalog/{catalog_name}/color_search`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, 
max_number_of_results | query | maximum number of results to return | 12
color_count | path | The maximum number of dominant colors to search for in an image. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your image is of high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing
                
### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**products** | A sorted list similar color images. Each item in the list has the following fields.
image_id | The image id.
id | The product id.
image_filename | The image filename.
image_url | The image url
distance | The euclidean distance (in LAB space)  between the color in the image to the color in the query.

# Dominant Colors

A collection of APIs for computing the dominant colors for the catalog.

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
{
 "computed_on": "2017-11-18 17:38:03.146494",
 "time_ms": "2.69",
 "dominant_colors": [
  {
   "name": "platinum",
   "popularity": 0.20652173913043478,
   "hex": "#e6e3e1",
   "universal_name": "white",
   "popularity_percentile": 100.0,
   "rgb": [
    230,
    227,
    225
   ],
   "hsv": [
    23.999999999999886,
    0.021739130434782705,
    0.9019607843137255
   ],
   "n_instances": 19,
   "entrylevel_name": "white"
  },
  {
   "name": "platinum",
   "popularity": 0.17391304347826086,
   "hex": "#e9e8e7",
   "universal_name": "white",
   "popularity_percentile": 95.0,
   "rgb": [
    233,
    232,
    231
   ],
   "hsv": [
    30.0,
    0.008583690987124415,
    0.9137254901960784
   ],
   "n_instances": 16,
   "entrylevel_name": "white"
  },
  {
   "name": "timberwolf",
   "popularity": 0.08695652173913043,
   "hex": "#dfdad6",
   "universal_name": "white",
   "popularity_percentile": 90.0,
   "rgb": [
    223,
    218,
    214
   ],
   "hsv": [
    26.666666666666572,
    0.04035874439461884,
    0.8745098039215686
   ],
   "n_instances": 8,
   "entrylevel_name": "silver"
  },
  {
   "name": "jet",
   "popularity": 0.07608695652173914,
   "hex": "#36322f",
   "universal_name": "black",
   "popularity_percentile": 85.0,
   "rgb": [
    54,
    50,
    47
   ],
   "hsv": [
    25.714285714285722,
    0.12962962962962954,
    0.21176470588235294
   ],
   "n_instances": 7,
   "entrylevel_name": "black"
  },
  {
   "name": "raisin black",
   "popularity": 0.05434782608695652,
   "hex": "#24222c",
   "universal_name": "black",
   "popularity_percentile": 77.5,
   "rgb": [
    36,
    34,
    44
   ],
   "hsv": [
    252.0,
    0.2272727272727273,
    0.17254901960784313
   ],
   "n_instances": 5,
   "entrylevel_name": "black"
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

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

# Catalog name.
catalog_name = props['catalog_name']

id ='109FA16AWWWTN9C36M18'
image_id = '1'

api_endpoint = '/v1/catalog/%s/dominant_colors/%s/%s'%(catalog_name,id,image_id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "dominant_colors": [
  {
   "name": "platinum",
   "hex": "#eae5e1",
   "universal_name": "white",
   "lab": [
    91.21959480157497,
    0.9891444931204307,
    2.535346529827587
   ],
   "rgb": [
    234,
    229,
    225
   ],
   "hsv": [
    26.666666666666742,
    0.038461538461538436,
    0.9176470588235294
   ],
   "entrylevel_name": "white"
  },
  {
   "name": "carrot orange",
   "hex": "#f49023",
   "universal_name": "orange",
   "lab": [
    68.97150523958666,
    30.7687372058737,
    67.65927525225112
   ],
   "rgb": [
    244,
    144,
    35
   ],
   "hsv": [
    31.291866028708114,
    0.8565573770491803,
    0.9568627450980393
   ],
   "entrylevel_name": "orange"
  },
  {
   "name": "cinnamon",
   "hex": "#d16718",
   "universal_name": "orange",
   "lab": [
    55.405269947627616,
    37.53890699256518,
    58.230572519589465
   ],
   "rgb": [
    209,
    103,
    24
   ],
   "hsv": [
    25.621621621621614,
    0.8851674641148325,
    0.8196078431372549
   ],
   "entrylevel_name": "copper"
  }
 ],
 "time_ms": "1.90"
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
popularity | A score between 0 and 1 indicating the popularity of the color.
popularity_percentile | The corresponding percentile score.
n_instances | The number of colors in the cluster.

## Get dominant colors for an image

```python
#------------------------------------------------------------------------------
# Dominant color palette. 
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

# Pass the api key into the header
# Replace 'your_api_key' with your API key.
headers = {'X-Api-Key': props['X-Api-Key']}

params = {}

# Optional parameters.
params['color_count'] = 3
params['quality'] = 1
params['image_max_dimension'] = 256

api_endpoint = '/v1/colors/dominant_colors'

url = urljoin(api_gateway_url,api_endpoint)

headers['Content-Type'] = 'image/jpeg'

response = requests.post(url,
                         headers=headers,
                         params=params,
                         data=open('test_image_2.jpeg','rb'))

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "dominant_colors": [
  {
   "name": "lavender blush",
   "hex": "#f7e8f2",
   "universal_name": "white",
   "lab": [
    93.40649747006204,
    6.792207586509836,
    -3.019243619029366
   ],
   "rgb": [
    247,
    232,
    242
   ],
   "hsv": [
    320.00000000000006,
    0.06072874493927127,
    0.9686274509803922
   ],
   "entrylevel_name": "off white"
  },
  {
   "name": "carmine pink",
   "hex": "#ed574b",
   "universal_name": "red",
   "lab": [
    57.398596108439904,
    57.08313749589916,
    37.966663400202684
   ],
   "rgb": [
    237,
    87,
    75
   ],
   "hsv": [
    4.444444444444457,
    0.6835443037974683,
    0.9294117647058824
   ],
   "entrylevel_name": "tomato"
  },
  {
   "name": "old burgundy",
   "hex": "#483431",
   "universal_name": "black",
   "lab": [
    23.854492807791047,
    8.487853036308863,
    5.404592036438394
   ],
   "rgb": [
    72,
    52,
    49
   ],
   "hsv": [
    7.826086956521749,
    0.3194444444444444,
    0.2823529411764706
   ],
   "entrylevel_name": "black"
  }
 ],
 "time_ms": "1226.92"
}
```

Get the dominant color palette for an image.

### End point

`POST /v1/colors/dominant_colors`

### Request

Parameter | Type | Description 
--------- | ------- | ----------- 
data | image/jpeg | (**Required**) The image. The image can be in PNG, JPEG, BMP, or GIF.
color_count | query | The maximum number of dominant colors to extract per image. | 3
quality | query | This parameter provides a trade off between the computation time and the quality of the dominant colors. Larger the number faster is the dominant color computation but greater the chance that the colors will be missed. 1 is the highest quality. | 1
image_max_dimension | query | You can set this parameters to resize the image for faster computation of dominant colors. If your images are of very high resolution you can set this parameter to a smaller value (suggested values 256-512) for faster computation. For any image if max(image width,image height) > image_max_dimension resizes the image such that max(image width,image height) = image_max_dimension. | no image resizing

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

# Zietgiest

Analyze and forecast fashion trends.

## Color trends

# Complete the Look

Given an fashion apparel suggest another apparel/accessory that goes well with it.



