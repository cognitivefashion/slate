# Color Trends

Analyze and forecast temporal color trends.

## Build color trend report

```python
#------------------------------------------------------------------------------
# Start computing the color trend report.
# POST /v1/color_trends/{report_name}/trend_report
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

# Pass the catalog names as a comma separated list of names.
catalog_names = []
catalog_names.append('vogue-autumn-winter-2014')
catalog_names.append('vogue-autumn-winter-2015')
catalog_names.append('vogue-autumn-winter-2016')
catalog_names.append('vogue-autumn-winter-2017')
params['catalog_names'] = ','.join(catalog_names)

# Optional parameters.
params['colors'] = 10
params['max_colors_per_image'] = 2

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trend_report'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.post(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "dominant_colors": {
   "vogue-autumn-winter-2017": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "90",
    "num_of_images_processed": "90"
   },
   "vogue-autumn-winter-2016": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "80",
    "num_of_images_processed": "80"
   },
   "vogue-autumn-winter-2015": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "100",
    "num_of_images_processed": "100"
   },
   "vogue-autumn-winter-2014": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "85",
    "num_of_images_processed": "85"
   }
  },
  "status": "done",
  "start_time": "2017-11-27 10:07:01.804707",
  "finish_time": "2017-11-27 10:07:09.043789"
 },
 "time_ms": "6.24"
}
```

Start computing the color trend report.

This API does color trend analysis and forecasting. The input is a time ordered list of catalogs. This first loads all the dominant colors for every image in the catalog and then clusters the colors. Then for each cluster is computes a popularity score for the cluster for every catalog. Once we have the poularity scores for a color over multiple time points we then apply a regression model to forecast the popularity score for the next year/time point.

<aside class="notice">
Prior to calling this API, the catalog has to be ingested and the colors search index (which computes the dominant colors for all the images) has to be built for all the catalogs.
</aside>

<aside class="notice">
This is a one time long running operation. Depending on your catalog size you will have to wait till the analysis is completed to start querying the trend report. You can query the status of the index creation using the GET endpoint. The analysis is completed when the status changes to `done`
</aside>

### End point

`POST /v1/color_trends/{report_name}/trend_report`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
report_name | path | (**Required**) The report name. Every trend analysis is refered by a unique report name. |
catalog_names | query | (**Required**) A time ordered list of catalog names. Pass the catalog names as a comma separated list of names, for example, `catalog_names=vogue-autumn-winter-2014,vogue-autumn-winter-2015,vogue-autumn-winter-2016,vogue-autumn-winter-2017`. Note that these catalog have to be ingested and the color search index has to be computed for each of the catalog. It is important to pass the catalog names in the right time order.|
colors | query | The number of clusters to use in the analysis. The poularity sscore is computed for every cluster. | 10
max_colors_per_image | query | The maximum number of dominant colors to use per image. | all available colors
fraction_pixels_threshold | query | Include only those colors whos fraction of pixels is greater than this number. | 0.0

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status.
status | The current status of the trend analysis. The status can be one of `retrieving dominant colors`,`clustering`,`trend forecasting` or `done`. The analysis is when the status becomes `done`.
start_time | The time when the trend analysis started.
finish_time | The time when the trend analysis finished.
**dominant_colors** | Info about retrieving dominant colors.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_processed | The total number of images processed.

<aside class="warning">
The current version supports building only one trend report at the same time and does not allow you to build another report when a current report building process in running. Please wait till the current analysis is completed. 
</aside>

## Get status of color trend report

```python
#------------------------------------------------------------------------------
# Get the status of the color trend report.
# GET /v1/color_trends/{report_name}/trend_report
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

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trend_report'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "status": {
  "dominant_colors": {
   "vogue-autumn-winter-2017": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "90",
    "num_of_images_processed": "90"
   },
   "vogue-autumn-winter-2016": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "80",
    "num_of_images_processed": "80"
   },
   "vogue-autumn-winter-2015": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "100",
    "num_of_images_processed": "100"
   },
   "vogue-autumn-winter-2014": {
    "num_of_products": "2",
    "num_of_images_ignored": "0",
    "num_of_images_missing_colors": "0",
    "num_of_images": "85",
    "num_of_images_processed": "85"
   }
  },
  "status": "done",
  "start_time": "2017-11-27 10:07:01.804707",
  "finish_time": "2017-11-27 10:07:09.043789"
 },
 "time_ms": "6.24"
}
```

Get the status of the color trend report.

### End point

`GET /v1/color_trends/{report_name}/trend_report`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
report_name | path | (**Required**) The report name. Every trend analysis is refered by a unique report name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
**status** | A json containing various info about the current status.
status | The current status of the trend analysis. The status can be one of `retrieving dominant colors`,`clustering`,`trend forecasting` or `done`. The analysis is when the status becomes `done`.
start_time | The time when the trend analysis started.
finish_time | The time when the trend analysis finished.
**dominant_colors** | Info about retrieving dominant colors.
num_of_products | The number of products in the catalog.
num_of_images | The total number of images in the catalog.
num_of_images_missing_colors | The number of images for which there was no dominant color was found.
num_of_images_ignored | The number of images that were ignored.
num_of_images_processed | The total number of images processed.

## Delete color trend report

```python
#------------------------------------------------------------------------------
# Delete the color trend report.
# DELETE /v1/color_trends/{report_name}/trend_report
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

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trend_report'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.delete(url,headers=headers)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "deleted": true,
 "time_ms": "134.87",
 "report_name": "vogue-autumn-winter"
}
```

Delete the color trend report.

### End point

`DELETE /v1/color_trends/{report_name}/trend_report`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
report_name | path | (**Required**) The report name. Every trend analysis is refered by a unique report name. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
report_name | The report name.
deleted |  Returns true is the report was successfully deleted.

## Forecast popular colors

```python
#------------------------------------------------------------------------------
# Get the popular colors.
# GET /v1/color_trends/{report_name}/popular_colors
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

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/popular_colors'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

for color in response.json()['colors']:
    print color['name']
```

> Sample json response

```json
{
 "computed_on": "2017-11-27 10:37:36.358990",
 "time_ms": "3.12",
 "colors": [
  {
   "trend_forecast": {
    "mean": -0.12728999876319122
   },
   "name": "papaya",
   "popularity": 0.21971830985915494,
   "hex": "#5e564e",
   "universal_name": "gray",
   "lab": [
    37.087884341146065,
    1.729542020793312,
    5.8311566365969565
   ],
   "popularity_percentile": 100.0,
   "rgb": [
    94,
    86,
    78
   ],
   "hsv": [
    30.0,
    0.17021276595744683,
    0.3686274509803922
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 38,
    "vogue-autumn-winter-2016": 26,
    "vogue-autumn-winter-2015": 61,
    "vogue-autumn-winter-2014": 31
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.21111111024727194,
    "vogue-autumn-winter-2016": 0.16250000081027,
    "vogue-autumn-winter-2015": 0.30499999874377953,
    "vogue-autumn-winter-2014": 0.18235294136206898
   },
   "n_instances": 156,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "gray",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.2111111111111111,
    "vogue-autumn-winter-2016": 0.1625,
    "vogue-autumn-winter-2015": 0.305,
    "vogue-autumn-winter-2014": 0.18235294117647058
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.12146459232037632,
    "confidence_interval_upper_limit": 0.453414784272708,
    "mean": 0.16597509597616583
   }
  },
  {
   "trend_forecast": {
    "mean": 1.4698675509883368
   },
   "name": "white gold",
   "popularity": 0.09295774647887324,
   "hex": "#c3a696",
   "universal_name": "pink",
   "lab": [
    70.23022967554536,
    8.083993928497623,
    12.209366351225558
   ],
   "popularity_percentile": 60.0,
   "rgb": [
    195,
    166,
    150
   ],
   "hsv": [
    21.333333333333314,
    0.23076923076923073,
    0.7647058823529411
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 19,
    "vogue-autumn-winter-2016": 3,
    "vogue-autumn-winter-2015": 13,
    "vogue-autumn-winter-2014": 31
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.10555555375433567,
    "vogue-autumn-winter-2016": 0.01875000188182624,
    "vogue-autumn-winter-2015": 0.06499999997553355,
    "vogue-autumn-winter-2014": 0.18235293984079062
   },
   "n_instances": 66,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "tan",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.10555555555555556,
    "vogue-autumn-winter-2016": 0.01875,
    "vogue-autumn-winter-2015": 0.065,
    "vogue-autumn-winter-2014": 0.18235294117647058
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": 0.047120763884222955,
    "confidence_interval_upper_limit": 0.26775401097536666,
    "mean": 0.1574373874297948
   }
  },
  {
   "trend_forecast": {
    "mean": 0.3023520749400484
   },
   "name": "dark lava",
   "popularity": 0.13380281690140844,
   "hex": "#4a423b",
   "universal_name": "black",
   "lab": [
    28.55926533070292,
    2.047899595026992,
    5.501238386707485
   ],
   "popularity_percentile": 70.0,
   "rgb": [
    74,
    66,
    59
   ],
   "hsv": [
    28.0,
    0.20270270270270274,
    0.2901960784313726
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 42,
    "vogue-autumn-winter-2016": 13,
    "vogue-autumn-winter-2015": 29,
    "vogue-autumn-winter-2014": 11
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.23333333198751538,
    "vogue-autumn-winter-2016": 0.08125000054292353,
    "vogue-autumn-winter-2015": 0.1449999990809743,
    "vogue-autumn-winter-2014": 0.06470588240193809
   },
   "n_instances": 95,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "black",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.23333333333333334,
    "vogue-autumn-winter-2016": 0.08125,
    "vogue-autumn-winter-2015": 0.145,
    "vogue-autumn-winter-2014": 0.06470588235294118
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.13864351451135104,
    "confidence_interval_upper_limit": 0.36027123838492425,
    "mean": 0.11081386193678662
   }
  },
  {
   "trend_forecast": {
    "mean": 10.612144903259598
   },
   "name": "dark vanilla",
   "popularity": 0.13943661971830987,
   "hex": "#c8bfab",
   "universal_name": "white",
   "lab": [
    77.567985885634,
    -0.2225972073570115,
    11.218179844582398
   ],
   "popularity_percentile": 80.0,
   "rgb": [
    200,
    191,
    171
   ],
   "hsv": [
    41.379310344827616,
    0.14500000000000002,
    0.7843137254901961
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 39,
    "vogue-autumn-winter-2016": 1,
    "vogue-autumn-winter-2015": 44,
    "vogue-autumn-winter-2014": 15
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.2166666657847035,
    "vogue-autumn-winter-2016": 0.006250000214277067,
    "vogue-autumn-winter-2015": 0.21999999913628526,
    "vogue-autumn-winter-2014": 0.0882352938883679
   },
   "n_instances": 99,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "silver",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.21666666666666667,
    "vogue-autumn-winter-2016": 0.00625,
    "vogue-autumn-winter-2015": 0.22,
    "vogue-autumn-winter-2014": 0.08823529411764706
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.2787645697974107,
    "confidence_interval_upper_limit": 0.3400206912085775,
    "mean": 0.0306280607055834
   }
  },
  {
   "trend_forecast": {
    "mean": 0.39087462409644025
   },
   "name": "grullo",
   "popularity": 0.03943661971830986,
   "hex": "#948776",
   "universal_name": "gray",
   "lab": [
    57.00933143397715,
    1.9748630692357771,
    10.91651260092894
   ],
   "popularity_percentile": 30.0,
   "rgb": [
    148,
    135,
    118
   ],
   "hsv": [
    34.0,
    0.20270270270270274,
    0.5803921568627451
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 11,
    "vogue-autumn-winter-2016": 3,
    "vogue-autumn-winter-2015": 9,
    "vogue-autumn-winter-2014": 5
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.061111106565130024,
    "vogue-autumn-winter-2016": 0.018750002197004373,
    "vogue-autumn-winter-2015": 0.044999996665530374,
    "vogue-autumn-winter-2014": 0.029411764252569035
   },
   "n_instances": 28,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "gray",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.06111111111111111,
    "vogue-autumn-winter-2016": 0.01875,
    "vogue-autumn-winter-2015": 0.045,
    "vogue-autumn-winter-2014": 0.029411764705882353
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.040234912730223824,
    "confidence_interval_upper_limit": 0.10094250156085967,
    "mean": 0.03035379441531792
   }
  }
 ]
}
```

Get the forecast for the popular colors for the next year.

<aside class="notice">
Prior to calling this API the trend report has to be built.
</aside>

### End point

`GET /v1/color_trends/{report_name}/popular_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
report_name | path | (**Required**) The report name. |
colors | query | The number of colors. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
computed_on | The time the trend analysis report where computed.
**colors** |  An ordered list of polular colors. Each field in the list has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
lab | The [LAB](https://en.wikipedia.org/wiki/Lab_color_space) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors avaialable in our color taxonomy.
popularity | A score between 0 and 1 indicating the popularity of the color.
popularity_percentile | The corresponding percentile score.
n_instances | The number of colors in the cluster.
n_instances_by_id | The number of colors in the cluster stratified by the catalog name.
total_instances_by_id | The total number of colors stratified by the catalog name.
popularity_by_id | The popularity score for the color stratified by the catalog name.
popularity_by_id_predicted | The predicted popularity score for the color stratified by the catalog name.
popularity_forecast | The popularity score forecast (mean and 95% confidence interval) for the color for the next season. The colors are sorted by this value. 
trend_forecast  | The trend score forecast (mean) for the color for the next season.

## Forecast trending colors

```python
#------------------------------------------------------------------------------
# Get the trending colors.
# GET /v1/color_trends/{report_name}/trending_colors
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

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trending_colors'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

for color in response.json()['colors']:
    print color['name']

```

> Sample json response

```json
{
 "computed_on": "2017-11-27 10:37:36.358990",
 "time_ms": "5.53",
 "colors": [
  {
   "trend_forecast": {
    "mean": 497752322.2741051
   },
   "name": "dark sienna",
   "popularity": 0.056338028169014086,
   "hex": "#402122",
   "universal_name": "black",
   "lab": [
    16.957248576431418,
    15.021140508932994,
    5.866954508283007
   ],
   "popularity_percentile": 40.0,
   "rgb": [
    64,
    33,
    34
   ],
   "hsv": [
    358.06451612903226,
    0.484375,
    0.25098039215686274
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 1,
    "vogue-autumn-winter-2016": 1,
    "vogue-autumn-winter-2015": 0,
    "vogue-autumn-winter-2014": 38
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.005555555511256352,
    "vogue-autumn-winter-2016": 0.006249999950126951,
    "vogue-autumn-winter-2015": 4.1854818562143316e-12,
    "vogue-autumn-winter-2014": 0.22352940997773238
   },
   "n_instances": 40,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "black",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.005555555555555556,
    "vogue-autumn-winter-2016": 0.00625,
    "vogue-autumn-winter-2015": 0.0,
    "vogue-autumn-winter-2014": 0.2235294117647059
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.21919940064855728,
    "confidence_interval_upper_limit": 0.21922465390486276,
    "mean": 1.2626628152741416e-05
   }
  },
  {
   "trend_forecast": {
    "mean": 18452119.817761216
   },
   "name": "indian red",
   "popularity": 0.01971830985915493,
   "hex": "#c46463",
   "universal_name": "red",
   "lab": [
    53.76430408872871,
    38.108463259306944,
    18.279605679684895
   ],
   "popularity_percentile": 10.0,
   "rgb": [
    196,
    100,
    99
   ],
   "hsv": [
    0.6185567010309114,
    0.4948979591836735,
    0.7686274509803922
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 5,
    "vogue-autumn-winter-2016": 0,
    "vogue-autumn-winter-2015": 8,
    "vogue-autumn-winter-2014": 1
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.02777777496324953,
    "vogue-autumn-winter-2016": 5.017991386996323e-10,
    "vogue-autumn-winter-2015": 0.03999999598475976,
    "vogue-autumn-winter-2014": 0.005882352647930724
   },
   "n_instances": 14,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "tomato",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.027777777777777776,
    "vogue-autumn-winter-2016": 0.0,
    "vogue-autumn-winter-2015": 0.04,
    "vogue-autumn-winter-2014": 0.0058823529411764705
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.05974450566166774,
    "confidence_interval_upper_limit": 0.06387994327947344,
    "mean": 0.0020677188089028493
   }
  },
  {
   "trend_forecast": {
    "mean": 5079144.232882881
   },
   "name": "cool grey",
   "popularity": 0.022535211267605635,
   "hex": "#8391b6",
   "universal_name": "gray",
   "lab": [
    60.29571831507566,
    3.5433538937366116,
    -20.959539984083687
   ],
   "popularity_percentile": 20.0,
   "rgb": [
    131,
    145,
    182
   ],
   "hsv": [
    223.52941176470588,
    0.28021978021978033,
    0.7137254901960784
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 1,
    "vogue-autumn-winter-2016": 0,
    "vogue-autumn-winter-2015": 14,
    "vogue-autumn-winter-2014": 1
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.005555555085396079,
    "vogue-autumn-winter-2016": 3.6459908464743507e-10,
    "vogue-autumn-winter-2015": 0.06999999433464991,
    "vogue-autumn-winter-2014": 0.005882352802880201
   },
   "n_instances": 16,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "gray",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.005555555555555556,
    "vogue-autumn-winter-2016": 0.0,
    "vogue-autumn-winter-2015": 0.07,
    "vogue-autumn-winter-2014": 0.0058823529411764705
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.06850921461312398,
    "confidence_interval_upper_limit": 0.06920115765005821,
    "mean": 0.00034597151846711444
   }
  },
  {
   "trend_forecast": {
    "mean": 10.612144903259598
   },
   "name": "dark vanilla",
   "popularity": 0.13943661971830987,
   "hex": "#c8bfab",
   "universal_name": "white",
   "lab": [
    77.567985885634,
    -0.2225972073570115,
    11.218179844582398
   ],
   "popularity_percentile": 80.0,
   "rgb": [
    200,
    191,
    171
   ],
   "hsv": [
    41.379310344827616,
    0.14500000000000002,
    0.7843137254901961
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 39,
    "vogue-autumn-winter-2016": 1,
    "vogue-autumn-winter-2015": 44,
    "vogue-autumn-winter-2014": 15
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.2166666657847035,
    "vogue-autumn-winter-2016": 0.006250000214277067,
    "vogue-autumn-winter-2015": 0.21999999913628526,
    "vogue-autumn-winter-2014": 0.0882352938883679
   },
   "n_instances": 99,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "silver",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.21666666666666667,
    "vogue-autumn-winter-2016": 0.00625,
    "vogue-autumn-winter-2015": 0.22,
    "vogue-autumn-winter-2014": 0.08823529411764706
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.2787645697974107,
    "confidence_interval_upper_limit": 0.3400206912085775,
    "mean": 0.0306280607055834
   }
  },
  {
   "trend_forecast": {
    "mean": 1.6044313381841329
   },
   "name": "pale silver",
   "popularity": 0.18732394366197183,
   "hex": "#ccc5c0",
   "universal_name": "white",
   "lab": [
    79.9404763594862,
    1.5506503538516014,
    3.361606653267346
   ],
   "popularity_percentile": 90.0,
   "rgb": [
    204,
    197,
    192
   ],
   "hsv": [
    25.0,
    0.05882352941176472,
    0.8
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2017": 16,
    "vogue-autumn-winter-2016": 73,
    "vogue-autumn-winter-2015": 12,
    "vogue-autumn-winter-2014": 32
   },
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2017": 0.08888888888209355,
    "vogue-autumn-winter-2016": 0.45624999924450865,
    "vogue-autumn-winter-2015": 0.060000000098657365,
    "vogue-autumn-winter-2014": 0.18823529379558995
   },
   "n_instances": 133,
   "total_instances_by_id": {
    "vogue-autumn-winter-2017": 180,
    "vogue-autumn-winter-2016": 160,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 170
   },
   "entrylevel_name": "silver",
   "popularity_by_id": {
    "vogue-autumn-winter-2017": 0.08888888888888889,
    "vogue-autumn-winter-2016": 0.45625,
    "vogue-autumn-winter-2015": 0.06,
    "vogue-autumn-winter-2014": 0.18823529411764706
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.4773062410333882,
    "confidence_interval_upper_limit": 0.47984889182870377,
    "mean": 0.0012713253976577997
   }
  }
 ]
}
```

Get the forecast for the trending colors for the next year.

This reflects the color which had the maximum increase in popularity over the past three years. These are the colors which may not be that popular but there is sharp increase in its popularity compared to the last few years.

<aside class="notice">
Prior to calling this API the trend report has to be built.
</aside>

### End point

`GET /v1/color_trends/{report_name}/trending_colors`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
report_name | path | (**Required**) The report name. |
colors | query | The number of colors. |

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
computed_on | The time the trend analysis report where computed.
**colors** |  An ordered list of polular colors. Each field in the list has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
lab | The [LAB](https://en.wikipedia.org/wiki/Lab_color_space) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors avaialable in our color taxonomy.
popularity | A score between 0 and 1 indicating the popularity of the color.
popularity_percentile | The corresponding percentile score.
n_instances | The number of colors in the cluster.
n_instances_by_id | The number of colors in the cluster stratified by the catalog name.
total_instances_by_id | The total number of colors stratified by the catalog name.
popularity_by_id | The popularity score for the color stratified by the catalog name.
popularity_by_id_predicted | The predicted popularity score for the color stratified by the catalog name.
popularity_forecast | The popularity score forecast (mean and 95% confidence interval) for the color for the next season. 
trend_forecast  | The trend score forecast (mean) for the color for the next season. The colors are sorted by this value. 

## Get trend report by RGB

```python
#------------------------------------------------------------------------------
# Get the color trend graph by RGB value.
# GET /v1/color_trends/{report_name}/trend_graph
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
params['r'] = 188
params['g'] = 89
params['b'] = 84

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trend_graph'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())
```

> Sample json response

```json
{
 "computed_on": "2017-11-27 10:37:36.358990",
 "time_ms": "36.29",
 "distance": 4.14258845017703,
 "trend_graph": {
  "trend_forecast": {
   "mean": 18452119.817761216
  },
  "name": "indian red",
  "popularity": 0.01971830985915493,
  "hex": "#c46463",
  "universal_name": "red",
  "lab": [
   53.76430408872871,
   38.108463259306944,
   18.279605679684895
  ],
  "popularity_percentile": 10.0,
  "rgb": [
   196,
   100,
   99
  ],
  "hsv": [
   0.6185567010309114,
   0.4948979591836735,
   0.7686274509803922
  ],
  "n_instances_by_id": {
   "vogue-autumn-winter-2017": 5,
   "vogue-autumn-winter-2016": 0,
   "vogue-autumn-winter-2015": 8,
   "vogue-autumn-winter-2014": 1
  },
  "popularity_by_id_predicted": {
   "vogue-autumn-winter-2017": 0.02777777496324953,
   "vogue-autumn-winter-2016": 5.017991386996323e-10,
   "vogue-autumn-winter-2015": 0.03999999598475976,
   "vogue-autumn-winter-2014": 0.005882352647930724
  },
  "n_instances": 14,
  "total_instances_by_id": {
   "vogue-autumn-winter-2017": 180,
   "vogue-autumn-winter-2016": 160,
   "vogue-autumn-winter-2015": 200,
   "vogue-autumn-winter-2014": 170
  },
  "entrylevel_name": "tomato",
  "popularity_by_id": {
   "vogue-autumn-winter-2017": 0.027777777777777776,
   "vogue-autumn-winter-2016": 0.0,
   "vogue-autumn-winter-2015": 0.04,
   "vogue-autumn-winter-2014": 0.0058823529411764705
  },
  "popularity_forecast": {
   "confidence_interval_lower_limit": -0.05974450566166774,
   "confidence_interval_upper_limit": 0.06387994327947344,
   "mean": 0.0020677188089028493
  }
 }
}
```

Get the color trend graph by RGB value.

This retrieves the trend report for a particular color by RGB value. 

<aside class="notice">
Prior to calling this API the trend report has to be built.
</aside>

### End point

`GET /v1/color_trends/{report_name}/trend_graph`

### Request

Parameter | Type | Description | Default
--------- | ------- | ----------- | ----------- 
r | query | (**Required**) The value for the red channel in the RGB color model (in the range 0 to 255).
g | query | (**Required**) The value for the green channel in the RGB color model (in the range 0 to 255).
b | query | (**Required**) The value for the blue channel in the RGB color model (in the range 0 to 255).

### Response 

Parameter |  Description
--------- |  -----------
time_ms |  The time taken in milliseconds.
computed_on | The time the trend analysis report where computed.
distance | The distance between the RGB value and the closest cluster center in the trend analysis.
**trend_graph** | Each field has the following fieldnames.
rgb | The [RGB](https://en.wikipedia.org/wiki/RGB_color_model) values for the color.
hsv | The [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV) values for the color.
lab | The [LAB](https://en.wikipedia.org/wiki/Lab_color_space) values for the color.
hex | The hex code for the color.
universal_name | The best matching universal color name. Berlin and Kay, in a classic [study](https://en.wikipedia.org/wiki/Basic_Color_Terms:_Their_Universality_and_Evolution) of worldwide color naming, postulated the existence of *11 universal basic color terms* across languages. We have included two more colors(*olive* and *yellow green*) as per the [ISCC-NBS system](https://en.wikipedia.org/wiki/ISCC%E2%80%93NBS_system).
entrylevel_name | The best matching entry level color name. A slightly larger curated set of color names which we call *entry level*. A random person should be able to recognize these color names.
name | The best guess for the color name based on all the colors avaialable in our color taxonomy.
popularity | A score between 0 and 1 indicating the popularity of the color.
popularity_percentile | The corresponding percentile score.
n_instances | The number of colors in the cluster.
n_instances_by_id | The number of colors in the cluster stratified by the catalog name.
total_instances_by_id | The total number of colors stratified by the catalog name.
popularity_by_id | The popularity score for the color stratified by the catalog name.
popularity_by_id_predicted | The predicted popularity score for the color stratified by the catalog name.
popularity_forecast | The popularity score forecast (mean and 95% confidence interval) for the color for the next season. 
trend_forecast  | The trend score forecast (mean) for the color for the next season. The colors are sorted by this value. 