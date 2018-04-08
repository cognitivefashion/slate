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
# Optional
#params['catalog_name'] = 'vogue-autumn-winter-2017'

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/popular_colors'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

# Print the colors.
results = response.json()
years = ['2013','2014','2015','2016','2017']
for color_info in results['colors']:
    print('popularity [%s] [%+1.2f] trend [%s] [%+1.2f] [%s]'%(
                          ' '.join(['%1.2f'%(color_info['popularity_by_id']['%s-%s'%(report_name,year)]) for year in years]),
                          color_info['popularity_forecast']['mean'],
                          ' '.join(['%+1.2f'%(color_info['trend_by_id']['%s-%s'%(report_name,year)]) for year in years]),
                          color_info['trend_forecast']['mean'],
                          color_info['pantone_id']))
```

> Sample json response

```json
{
 "computed_on": "2018-04-08 08:23:29.263227",
 "time_ms": "3.98",
 "colors": [
  {
   "trend_forecast": {
    "mean": 0.11846037510176805
   },
   "pantone_name": "jet black",
   "name": "jet",
   "pantone_rgb": [
    45,
    44,
    47
   ],
   "pantone_id": "pantone 19-0303 tcx jet black",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.27372356393630976,
    "vogue-autumn-winter-2016": 0.08555227333057681,
    "vogue-autumn-winter-2015": 0.10808528033524438,
    "vogue-autumn-winter-2014": -0.045649819903448
   },
   "popularity": 0.12474437627811862,
   "hex": "#2e2d30",
   "universal_name": "black",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.10106382816649939,
    "vogue-autumn-winter-2017": 0.17857142615067614,
    "vogue-autumn-winter-2016": 0.12121212398756362,
    "vogue-autumn-winter-2015": 0.12999999648153948,
    "vogue-autumn-winter-2014": 0.09183673705739197
   },
   "popularity_percentile": 90.0,
   "rgb": [
    46,
    45,
    48
   ],
   "n_instances": 122,
   "lab": [
    18.67502318692506,
    1.1641448868809912,
    -1.7731775401694017
   ],
   "pantone_code": "19-0303 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    259.99999999999994,
    0.06249999999999989,
    0.18823529411764706
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 19,
    "vogue-autumn-winter-2017": 35,
    "vogue-autumn-winter-2016": 24,
    "vogue-autumn-winter-2015": 26,
    "vogue-autumn-winter-2014": 18
   },
   "entrylevel_name": "black",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.10106382978723404,
    "vogue-autumn-winter-2017": 0.17857142857142858,
    "vogue-autumn-winter-2016": 0.12121212121212122,
    "vogue-autumn-winter-2015": 0.13,
    "vogue-autumn-winter-2014": 0.09183673469387756
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": 0.030499034528666535,
    "confidence_interval_upper_limit": 0.3087030731262529,
    "mean": 0.1696010538274597
   }
  },
  {
   "trend_forecast": {
    "mean": 0.32502210212075666
   },
   "pantone_name": "deep well",
   "name": "raisin black",
   "pantone_rgb": [
    44,
    42,
    51
   ],
   "pantone_id": "pantone 19-3713 tcx deep well",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.015566962791144334,
    "vogue-autumn-winter-2016": -0.33375267885934234,
    "vogue-autumn-winter-2015": -0.26977967317365265,
    "vogue-autumn-winter-2014": -0.22423467190267088
   },
   "popularity": 0.1114519427402863,
   "hex": "#22202a",
   "universal_name": "black",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.21276595355640066,
    "vogue-autumn-winter-2017": 0.09693877266310735,
    "vogue-autumn-winter-2016": 0.060606066963965866,
    "vogue-autumn-winter-2015": 0.07499999163851878,
    "vogue-autumn-winter-2014": 0.11734694598084383
   },
   "popularity_percentile": 85.0,
   "rgb": [
    34,
    32,
    42
   ],
   "n_instances": 109,
   "lab": [
    12.86179904012861,
    3.5801169702007196,
    -6.343380699850043
   ],
   "pantone_code": "19-3713 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    252.0,
    0.23809523809523814,
    0.16470588235294117
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 40,
    "vogue-autumn-winter-2017": 19,
    "vogue-autumn-winter-2016": 12,
    "vogue-autumn-winter-2015": 15,
    "vogue-autumn-winter-2014": 23
   },
   "entrylevel_name": "black",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.2127659574468085,
    "vogue-autumn-winter-2017": 0.09693877551020408,
    "vogue-autumn-winter-2016": 0.06060606060606061,
    "vogue-autumn-winter-2015": 0.075,
    "vogue-autumn-winter-2014": 0.11734693877551021
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": 0.0614139235353279,
    "confidence_interval_upper_limit": 0.24248830328589702,
    "mean": 0.15195111341061246
   }
  },
  {
   "trend_forecast": {
    "mean": 0.27244798323564934
   },
   "pantone_name": "black ink",
   "name": "black olive",
   "pantone_rgb": [
    68,
    65,
    60
   ],
   "pantone_id": "pantone 19-0506 tcx black ink",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.34844147476010096,
    "vogue-autumn-winter-2016": 0.6817749099763932,
    "vogue-autumn-winter-2015": 0.8257142812078491,
    "vogue-autumn-winter-2014": 1.1785714478460592
   },
   "popularity": 0.065439672801636,
   "hex": "#444340",
   "universal_name": "black",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.02127659559146082,
    "vogue-autumn-winter-2017": 0.10714285332584271,
    "vogue-autumn-winter-2016": 0.04545454898588769,
    "vogue-autumn-winter-2015": 0.0799999966233297,
    "vogue-autumn-winter-2014": 0.07142857173438694
   },
   "popularity_percentile": 75.0,
   "rgb": [
    68,
    67,
    64
   ],
   "n_instances": 64,
   "lab": [
    28.415459788429516,
    -0.22870650807427229,
    1.9645499625216334
   ],
   "pantone_code": "19-0506 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    45.00000000000006,
    0.05882352941176472,
    0.26666666666666666
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 4,
    "vogue-autumn-winter-2017": 21,
    "vogue-autumn-winter-2016": 9,
    "vogue-autumn-winter-2015": 16,
    "vogue-autumn-winter-2014": 14
   },
   "entrylevel_name": "black",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.02127659574468085,
    "vogue-autumn-winter-2017": 0.10714285714285714,
    "vogue-autumn-winter-2016": 0.045454545454545456,
    "vogue-autumn-winter-2015": 0.08,
    "vogue-autumn-winter-2014": 0.07142857142857142
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.0005011718754029948,
    "confidence_interval_upper_limit": 0.19164819507100214,
    "mean": 0.09557351159779957
   }
  },
  {
   "trend_forecast": {
    "mean": 0.5309714246051445
   },
   "pantone_name": "heron",
   "name": "gray blue",
   "pantone_rgb": [
    98,
    97,
    126
   ],
   "pantone_id": "pantone 18-3817 tcx heron",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.25516862766198656,
    "vogue-autumn-winter-2016": 0.008004838367361827,
    "vogue-autumn-winter-2015": 0.004636694980282223,
    "vogue-autumn-winter-2014": 0.1394553051314291
   },
   "popularity": 0.019427402862985686,
   "hex": "#5d607d",
   "universal_name": "gray",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.015957453050017,
    "vogue-autumn-winter-2017": 0.030612222082882584,
    "vogue-autumn-winter-2016": 0.015151550242968215,
    "vogue-autumn-winter-2015": 0.01499998395269847,
    "vogue-autumn-winter-2014": 0.02040815601843815
   },
   "popularity_percentile": 35.0,
   "rgb": [
    93,
    96,
    125
   ],
   "n_instances": 19,
   "lab": [
    41.49038321061422,
    5.772538816200379,
    -16.61053151124028
   ],
   "pantone_code": "18-3817 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    234.375,
    0.256,
    0.49019607843137253
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 3,
    "vogue-autumn-winter-2017": 6,
    "vogue-autumn-winter-2016": 3,
    "vogue-autumn-winter-2015": 3,
    "vogue-autumn-winter-2014": 4
   },
   "entrylevel_name": "gray",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.015957446808510637,
    "vogue-autumn-winter-2017": 0.030612244897959183,
    "vogue-autumn-winter-2016": 0.015151515151515152,
    "vogue-autumn-winter-2015": 0.015,
    "vogue-autumn-winter-2014": 0.02040816326530612
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": 0.03869663665993158,
    "confidence_interval_upper_limit": 0.05696091646598237,
    "mean": 0.047828776562956976
   }
  },
  {
   "trend_forecast": {
    "mean": 0.0970617321776512
   },
   "pantone_name": "white sand",
   "name": "timberwolf",
   "pantone_rgb": [
    219,
    213,
    209
   ],
   "pantone_id": "pantone 13-0002 tcx white sand",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.20115535841989507,
    "vogue-autumn-winter-2016": 0.07030295267334408,
    "vogue-autumn-winter-2015": -0.16875410705247687,
    "vogue-autumn-winter-2014": -0.16146461244093568
   },
   "popularity": 0.07566462167689161,
   "hex": "#d7d1d0",
   "universal_name": "white",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.09042553119131298,
    "vogue-autumn-winter-2017": 0.09183673675117678,
    "vogue-autumn-winter-2016": 0.08585857644650922,
    "vogue-autumn-winter-2015": 0.050000010183979496,
    "vogue-autumn-winter-2014": 0.0612244844941708
   },
   "popularity_percentile": 80.0,
   "rgb": [
    215,
    209,
    208
   ],
   "n_instances": 74,
   "lab": [
    84.27837486243521,
    1.8817398220458137,
    1.2191511354760154
   ],
   "pantone_code": "13-0002 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    8.571428571428555,
    0.032558139534883734,
    0.8431372549019608
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 17,
    "vogue-autumn-winter-2017": 18,
    "vogue-autumn-winter-2016": 17,
    "vogue-autumn-winter-2015": 10,
    "vogue-autumn-winter-2014": 12
   },
   "entrylevel_name": "silver",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.09042553191489362,
    "vogue-autumn-winter-2017": 0.09183673469387756,
    "vogue-autumn-winter-2016": 0.08585858585858586,
    "vogue-autumn-winter-2015": 0.05,
    "vogue-autumn-winter-2014": 0.061224489795918366
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": 0.0027438964207623234,
    "confidence_interval_upper_limit": 0.08989843677661184,
    "mean": 0.04632116659868708
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
colors | query | The number of colors. | 10
catalog_name | query | You can optionally specify the catalog name if you want the results from the previous years.   

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
# Optional
#params['catalog_name'] = 'vogue-autumn-winter-2017'

report_name = 'vogue-autumn-winter'

api_endpoint = '/v1/color_trends/%s/trending_colors'%(report_name)

url = urljoin(api_gateway_url,api_endpoint)

response = requests.get(url,headers=headers,params=params)

print response.status_code
pprint(response.json())

# Print the colors.
results = response.json()
years = ['2013','2014','2015','2016','2017']
for color_info in results['colors']:
    print('popularity [%s] [%+1.2f] trend [%s] [%+1.2f] [%s]'%(
                          ' '.join(['%1.2f'%(color_info['popularity_by_id']['%s-%s'%(report_name,year)]) for year in years]),
                          color_info['popularity_forecast']['mean'],
                          ' '.join(['%+1.2f'%(color_info['trend_by_id']['%s-%s'%(report_name,year)]) for year in years]),
                          color_info['trend_forecast']['mean'],
                          color_info['pantone_id']))
```

> Sample json response

```json
{
 "computed_on": "2018-04-08 08:23:29.263227",
 "time_ms": "3.45",
 "colors": [
  {
   "trend_forecast": {
    "mean": 8.235715230566532
   },
   "pantone_name": "olivenite",
   "name": "aztec gold",
   "pantone_rgb": [
    193,
    166,
    92
   ],
   "pantone_id": "pantone 15-0732 tcx olivenite",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 7.976730869261527,
    "vogue-autumn-winter-2016": 0.25904713834398163,
    "vogue-autumn-winter-2015": 0.5923803456031483,
    "vogue-autumn-winter-2014": 1.1785704702543323
   },
   "popularity": 0.017382413087934562,
   "hex": "#bc9f52",
   "universal_name": "olive",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.010638301716580001,
    "vogue-autumn-winter-2017": 0.02551019780659891,
    "vogue-autumn-winter-2016": 5.673337612199791e-09,
    "vogue-autumn-winter-2015": 0.01500000029600288,
    "vogue-autumn-winter-2014": 0.03571427823021432
   },
   "popularity_percentile": 27.500000000000004,
   "rgb": [
    188,
    159,
    82
   ],
   "n_instances": 17,
   "lab": [
    66.59479318367542,
    1.2071716907996466,
    43.61008842556798
   ],
   "pantone_code": "15-0732 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    43.58490566037733,
    0.5638297872340425,
    0.7372549019607844
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 2,
    "vogue-autumn-winter-2017": 5,
    "vogue-autumn-winter-2016": 0,
    "vogue-autumn-winter-2015": 3,
    "vogue-autumn-winter-2014": 7
   },
   "entrylevel_name": "tan",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.010638297872340425,
    "vogue-autumn-winter-2017": 0.025510204081632654,
    "vogue-autumn-winter-2016": 0.0,
    "vogue-autumn-winter-2015": 0.015,
    "vogue-autumn-winter-2014": 0.03571428571428571
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.0054111940580404486,
    "confidence_interval_upper_limit": 0.06648021884312591,
    "mean": 0.030534512392542727
   }
  },
  {
   "trend_forecast": {
    "mean": 7.571549001664395
   },
   "pantone_name": "carmine",
   "name": "carmine",
   "pantone_rgb": [
    188,
    72,
    105
   ],
   "pantone_id": "pantone 17-1831 tcx carmine",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 8.218867318426964,
    "vogue-autumn-winter-2016": 8.084173058429286,
    "vogue-autumn-winter-2015": -0.33332935240964134,
    "vogue-autumn-winter-2014": -0.5000004190614799
   },
   "popularity": 0.0081799591002045,
   "hex": "#b94668",
   "universal_name": "purple",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.005319150011365242,
    "vogue-autumn-winter-2017": 0.010204085522693439,
    "vogue-autumn-winter-2016": 0.02525251555530907,
    "vogue-autumn-winter-2015": 8.322792285214708e-09,
    "vogue-autumn-winter-2014": -4.458101750498371e-09
   },
   "popularity_percentile": 10.0,
   "rgb": [
    185,
    70,
    104
   ],
   "n_instances": 8,
   "lab": [
    46.573551824894544,
    49.345973558034494,
    4.994866094189598
   ],
   "pantone_code": "17-1831 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    342.2608695652174,
    0.6216216216216216,
    0.7254901960784313
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 1,
    "vogue-autumn-winter-2017": 2,
    "vogue-autumn-winter-2016": 5,
    "vogue-autumn-winter-2015": 0,
    "vogue-autumn-winter-2014": 0
   },
   "entrylevel_name": "maroon",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.005319148936170213,
    "vogue-autumn-winter-2017": 0.01020408163265306,
    "vogue-autumn-winter-2016": 0.025252525252525252,
    "vogue-autumn-winter-2015": 0.0,
    "vogue-autumn-winter-2014": 0.0
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.049635410157543226,
    "confidence_interval_upper_limit": 0.030412093249694426,
    "mean": -0.0096116584539244
   }
  },
  {
   "trend_forecast": {
    "mean": 2.9002808043760666
   },
   "pantone_name": "burgundy",
   "name": "burgundy",
   "pantone_rgb": [
    100,
    49,
    62
   ],
   "pantone_id": "pantone 19-1617 tcx burgundy",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": -0.49456702511890355,
    "vogue-autumn-winter-2016": 1.1040734357100044,
    "vogue-autumn-winter-2015": 1.3893066120901132,
    "vogue-autumn-winter-2014": 1.8979602872913048
   },
   "popularity": 0.014314928425357873,
   "hex": "#5f323d",
   "universal_name": "purple",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.005319147493313,
    "vogue-autumn-winter-2017": -4.080331614253474e-09,
    "vogue-autumn-winter-2016": 0.005050514716681838,
    "vogue-autumn-winter-2015": 0.03499998777961907,
    "vogue-autumn-winter-2014": 0.025510208902419326
   },
   "popularity_percentile": 17.5,
   "rgb": [
    95,
    50,
    61
   ],
   "n_instances": 14,
   "lab": [
    26.880879480444015,
    21.737230131132435,
    2.138083288990311
   ],
   "pantone_code": "19-1617 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    345.3333333333333,
    0.4736842105263158,
    0.37254901960784315
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 1,
    "vogue-autumn-winter-2017": 0,
    "vogue-autumn-winter-2016": 1,
    "vogue-autumn-winter-2015": 7,
    "vogue-autumn-winter-2014": 5
   },
   "entrylevel_name": "maroon",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.005319148936170213,
    "vogue-autumn-winter-2017": 0.0,
    "vogue-autumn-winter-2016": 0.005050505050505051,
    "vogue-autumn-winter-2015": 0.035,
    "vogue-autumn-winter-2014": 0.025510204081632654
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.025658486147475734,
    "confidence_interval_upper_limit": 0.04677156348715779,
    "mean": 0.010556538669841027
   }
  },
  {
   "trend_forecast": {
    "mean": 1.8642376911307246
   },
   "pantone_name": "valiant poppy",
   "name": "firebrick",
   "pantone_rgb": [
    188,
    50,
    44
   ],
   "pantone_id": "pantone 18-1549 tcx valiant poppy",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 3.693773482978249,
    "vogue-autumn-winter-2016": 2.010100475412465,
    "vogue-autumn-winter-2015": 1.3333322551535562,
    "vogue-autumn-winter-2014": -0.4999999533767581
   },
   "popularity": 0.022494887525562373,
   "hex": "#be1f24",
   "universal_name": "red",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.015957445073912335,
    "vogue-autumn-winter-2017": 0.07653060568321701,
    "vogue-autumn-winter-2016": 0.015151517728698292,
    "vogue-autumn-winter-2015": 0.004999998160189827,
    "vogue-autumn-winter-2014": 1.4879756417621442e-09
   },
   "popularity_percentile": 40.0,
   "rgb": [
    190,
    31,
    36
   ],
   "n_instances": 22,
   "lab": [
    41.30704144641319,
    60.19009061985351,
    39.52332046616106
   ],
   "pantone_code": "18-1549 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    358.1132075471698,
    0.8368421052631578,
    0.7450980392156863
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 3,
    "vogue-autumn-winter-2017": 15,
    "vogue-autumn-winter-2016": 3,
    "vogue-autumn-winter-2015": 1,
    "vogue-autumn-winter-2014": 0
   },
   "entrylevel_name": "red",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.015957446808510637,
    "vogue-autumn-winter-2017": 0.07653061224489796,
    "vogue-autumn-winter-2016": 0.015151515151515152,
    "vogue-autumn-winter-2015": 0.005,
    "vogue-autumn-winter-2014": 0.0
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.024902090520356926,
    "confidence_interval_upper_limit": 0.10317595574874246,
    "mean": 0.039136932614192765
   }
  },
  {
   "trend_forecast": {
    "mean": 0.7906371620551244
   },
   "pantone_name": "astral aura",
   "name": "grape",
   "pantone_rgb": [
    54,
    49,
    81
   ],
   "pantone_id": "pantone 19-3830 tcx astral aura",
   "trend_by_id": {
    "vogue-autumn-winter-2013": 0.0,
    "vogue-autumn-winter-2017": 0.668688540385415,
    "vogue-autumn-winter-2016": 0.20100251734269106,
    "vogue-autumn-winter-2015": 0.45016069962896715,
    "vogue-autumn-winter-2014": 0.818877437721917
   },
   "popularity": 0.03374233128834356,
   "hex": "#322e4b",
   "universal_name": "black",
   "popularity_by_id_predicted": {
    "vogue-autumn-winter-2013": 0.02127659660718742,
    "vogue-autumn-winter-2017": 0.0408163201213404,
    "vogue-autumn-winter-2016": 0.010101016968916778,
    "vogue-autumn-winter-2015": 0.03999999541935249,
    "vogue-autumn-winter-2014": 0.05612244643346036
   },
   "popularity_percentile": 55.00000000000001,
   "rgb": [
    50,
    46,
    75
   ],
   "n_instances": 33,
   "lab": [
    20.596319430718573,
    9.582459495460604,
    -17.306983424884383
   ],
   "pantone_code": "19-3830 tcx",
   "total_instances_by_id": {
    "vogue-autumn-winter-2013": 188,
    "vogue-autumn-winter-2017": 196,
    "vogue-autumn-winter-2016": 198,
    "vogue-autumn-winter-2015": 200,
    "vogue-autumn-winter-2014": 196
   },
   "hsv": [
    248.27586206896552,
    0.3866666666666667,
    0.29411764705882354
   ],
   "n_instances_by_id": {
    "vogue-autumn-winter-2013": 4,
    "vogue-autumn-winter-2017": 8,
    "vogue-autumn-winter-2016": 2,
    "vogue-autumn-winter-2015": 8,
    "vogue-autumn-winter-2014": 11
   },
   "entrylevel_name": "navy blue",
   "popularity_by_id": {
    "vogue-autumn-winter-2013": 0.02127659574468085,
    "vogue-autumn-winter-2017": 0.04081632653061224,
    "vogue-autumn-winter-2016": 0.010101010101010102,
    "vogue-autumn-winter-2015": 0.04,
    "vogue-autumn-winter-2014": 0.05612244897959184
   },
   "popularity_forecast": {
    "confidence_interval_lower_limit": -0.004410300375401743,
    "confidence_interval_upper_limit": 0.09245706928045698,
    "mean": 0.04402338445252762
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
colors | query | The number of colors. | 10
catalog_name | query | You can optionally specify the catalog name if you want the results from the previous years. 

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