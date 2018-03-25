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