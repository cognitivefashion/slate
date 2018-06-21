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
 "visual_search_category": ["dress"],
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
   "model_worn": "yes",
   "image_type": "primary"
  }, 
  "3": {
   "camera_focus": "detail", 
   "pose": "UNK", 
   "ignore": "yes",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Detail_Large.jpg", 
   "model_worn": "no",
   "image_type": "secondary"
  }, 
  "2": {
   "camera_focus": "full", 
   "pose": "right", 
   "ignore": "no",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Right_Large.jpg", 
   "model_worn": "yes",
   "image_type": "secondary"
  }, 
  "4": {
   "camera_focus": "full", 
   "pose": "back", 
   "ignore": "no",
   "image_url": "http://images.abofcdn.com/catalog/images/2016/109FA16AWWWDR9D03Y10/Back_Large.jpg", 
   "model_worn": "yes",
   "image_type": "secondary"
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
visual_search_category | list string |  A list of categories(or buckets) this product belongs to. This field is used to support category based visual search. A separate visual search index will be built with images from a particular category. While in most cases this will be same as `category` field in principle this field can be differnet. For example, `shirts` could be the `category` while the `visual_search_category` could be `women shirts`. The field can also support multiple category values. For example, `data['visual_search_category'] = ['tops','blouses']`. This means that the images corresponding to this product will be included in the visual search index for both `tops` and `blouses`.
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
| | image_type | Can be either `primary` or `secondary`. You can use this field also to categorize images.
| | | *The fields `ignore`,`model_worn`, `pose`, `camera_focus`, and `image_type` are optional.*
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

## Get catalog image   

```shell
http://api_gateway_url/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SH20_5.jpg

http://api_gateway_url/v1/catalog/sample_catalog/images/SKLTS16AMCWSH8SH20_5.jpg?top_left_x=100&top_left_y=100&width=64&height=64

```

```python
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

