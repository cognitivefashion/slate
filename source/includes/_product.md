# Catalog 

The APIs come in two flavors. Some APIs work on top of a simple user input and do not require access to the product catalog. However some APIs (like visual search) operate on top of a client catalog. The first step is to upload the product catalog. These are some end points for uploading, accessing, modifying and deleting a product catalog.

We use the term **catalog** to refer to a collection of product jsons. A product json consists of any available product meta-data and info about the product images.

## Product JSON format

> Sample product json document for catalog ingestion.

```json
{
 "id": "109FA16AWWWDR9D03Y10", 
 "gender": "female",  
 "name": "109f women brown & yellow printed dress", 
 "url": "http://www.abof.com/product/109FA16AWWWDR9D03Y10-109F-Women-Brown-&-Yellow-Printed-Dress", 
 "date": "2016-10-12", 
 "out_of_stock": "no",
 "category": "dress", 
 "taxonomy": "women;dress", 
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
 "currency": "INR",
 "visual_search_category": ["dress"],
 "visual_browse_category": ["dress"]
}
```

We refer to any **apparel**, **accessory**, or **footwear** as a **product** in the table below. A product in a catalog is represented as a json object with fields specified below. Ingesting the catalog means uploading these json files for all products in the catalog via an API call.

Try to populate as much information available for each product into these fields in the json document. Except for the fields in bold (**id**, **gender**, **name** (for natural language search), **images**(for visual search and visual browse)) all other fields are optional. However the performance of the natural language search is heavily dependent on the richness of these structured fields and is recommended that you provide as much information as possible.

field | type | description 
--------- | ------- | ---------- 
**id** | string | A unique identifier for the product for which there is a product page and a set of product images associated with it. 
**gender** | string | The gender for which the product is intended for, can be `male`, `female` or `unisex`. Use `UNK` if not known.
**name** | string | The name of the product. This is generally a free text brief description of the product.
url | string | The corresponding url of the product page.
date | string | The date when the item was entered into the catalog, in `YYYY-MM-DD` format. You can also include time, like, `2015-01-01T12:10:30Z`. 
out_of_stock | string | Can either be `yes` or `no`. This is the field that need to be changed to `yes` when a product goes out of stock. By default all search queries will not return out-of-stock products. Note that typically out of stock is at the SKU level. Ideally make this `yes`  only when all SKUs for this product are out of stock. You will typically use this when the product will be replenished. If you think the product will be discontinued and not available again you can delete that product entirely.
category | string | The product category.
taxonomy | string | The complete product taxonomy separated via `;`.
age_group | list string | The age group for which the product is intended for. Some suggested tags : `baby`, `kid`, `teenager`, `adult`.
**images** | list of dict | A list of images available for the product with `image_id` as the key and the following fields in the dict. 
|  | **image_id** | The image id (string).
|  | **image_url**  | The image url. The actual image will be automatically downloaded from this url. The image format can be in JPEG,PNG,BMP, or GIF. Note that this url should be publicly accessible for us to download the image.
|  | image_filename | You can optionally specify an image filename. The image will be downloaded to the specified image filename.
|  | image_type | Can be either `primary` or `secondary`. You can use this field also to categorize images.
|  | ignore | You can use this field to select what kind of images should be used for visual browse/search. If `yes` then the image will not be downloaded and excluded from visual browse/search. In the example on the right, we ignore all images which have close-up of some details in the product.
|  | model_worn  | If `yes` then the image corresponds to the model wearing the particular product. If `no` refers to image of the the product only. Use `UNK` if not known.
|  | pose | Refers to the pose/orientation in which the image is taken. Can take one of the following values: `front`, `back`, `left`, `right`, `top`, `UNK`.
|  | camera_focus | Which part of the model the camera is focused on. Can take one of the following values: `full` (complete view of the model, entire body), `top` (upper part of the model above the waist, used in shirts etc.), `bottom` (lower part of the model below the waist, used in trousers etc.), `portrait` (model face, normally up to the shoulder), `detail` (a close-up of some details in the product), `outfit` (complete view of the model, entire body along with other apparel and accessories), `UNK`.
| | | *The fields `image_filename`,`image_type`,`ignore`,`model_worn`, `pose`, and `camera_focus` are optional.*
color | list of string | A list of terms that describe the colors in the product.
pattern | list of string | A list of terms that describe the pattern on the fabric.
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
|  | |*Note that the natural language search by default will use the sale_price field for the queries that deal with price.*
sale_percentage | float | The sale percentage. This is useful for queries related to sale percentages.
currency | string | The currency used, specified as a 3 letter [ISO 4217](http://www.xe.com/iso4217.php) currency code.
|  | | *The following two fields below are for category based visual search and visual browse. For more details refer to the Visual Search API.*
visual_search_category | list of string |  A list of categories (or buckets) this product belongs to. This field is used to support category based visual search. 
visual_browse_category | list of string |  A list of categories (or buckets) this product belongs to. This field is used to support category based visual browse.  

## Add product to a catalog    

```python
#------------------------------------------------------------------------------
# Add product to a catalog.    
# POST /v1/catalog/{catalog_name}/products/{id}
# params - download_images
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
# download_images : str, optional (default: 'true') 
# By default all the images in the json will be downloaded.  
# Set this to 'false' if you do not want to download the images.  
#------------------------------------------------------------------------------
params['download_images'] = 'true'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

#------------------------------------------------------------------------------
# ADD A BUNCH OF PRODUCTS
#------------------------------------------------------------------------------

# Some sample data where each product in the catalog is in a json format.
catalog_folder = os.path.join(props['catalog_folder'],'jsons')
json_filenames = [f for f in os.listdir(catalog_folder) if not f.startswith('.')]

for filename in json_filenames:
    # Load the json file.
    with open(os.path.join(catalog_folder,filename),'r') as f:
        data = json.loads(f.read())
        
    api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,data['id'])

    url = urljoin(api_gateway_url,api_endpoint)
    
    response = requests.post(url,
                             headers=headers,
                             data=json.dumps(data),
                             params=params)
    
    print response.status_code
    pprint(response.json())
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
 "n_images_existing": 0,
 "images_error": []
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
download_images | query | By default all the images specified in the json will be downloaded. Set this to `false` if you do not want to download the images. This is mainly useful when we have a local copy of the images or are not using visual search. | `true`  

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
id | The product id.
version | The version number. Every update will be assigned a new version number.
n_images | The total number of images in the json file.
n_images_downloaded | The actual number of images successfully downloaded. 
n_images_ignored | The total number of images ignored (as specified by the field `ignore` in `images` in the product json.). 
n_images_error | The number of images for which there was some error in downloading. 
n_images_existing | The number of images which were already downloaded.
images_error | The is of the images for which there was some error in downloading. 

<aside class="notice">
Note that the image download happens only once. Even if you post the json once again the image will not be downloaded again unless there is a change in the image url. 
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
# params - delete_images
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
# delete_images : str, optional (default: 'false') 
#
# Set this to 'true' if you want to delete the images.  
#------------------------------------------------------------------------------
params['delete_images'] = 'false'

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
 "time_ms": "4.01",
 "version": 4,
 "id": "SKLTS16AMCWSH8SH20"
}
```

Delete the product from a catalog.

### End point

`DELETE /v1/catalog/{catalog_name}/products/{id}`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
catalog_name | path | (**Required**) The catalog name. |
id | path | (**Required**) The product id. |
delete_images | query | By default when you delete a product the images are not deleted. Set this to `true` if you want to delete the images. | `false`  

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
# params - download_images
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
# download_images : str, optional (default: 'true') 
# By default all the images in the json will be downloaded.  
# Set this to 'false' if you do not want to download the images.  
#------------------------------------------------------------------------------
params['download_images'] = 'true'

#------------------------------------------------------------------------------
# PATH PARAMETERS
#------------------------------------------------------------------------------
# Catalog name.
catalog_name = props['catalog_name']

# product id
id = 'SKLTS16AMCWSH8SH20'

#------------------------------------------------------------------------------
# Data
#------------------------------------------------------------------------------
data = {}
data['out_of_stock'] = 'yes'

#------------------------------------------------------------------------------
# API END POINT
#------------------------------------------------------------------------------
api_endpoint = '/v1/catalog/%s/products/%s'%(catalog_name,id)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.put(url,
                        headers=headers,
                        params=params,
                        json=data)

print response.status_code
pprint(response.json())
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
- The product image has changes and you can update the `image_url`.

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