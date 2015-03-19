# API Access
API server location is given only to our clients.
The API is responding only to HTTPS requests.

## API Keys
Your keys can be generated in the merchant dashboard and **should be kept private**

API keys are linked to sites, one site is usually a single store.

You can use multiple keys with one website, for example to distinguish payments made in different national currencies. And you should avoid reusing the same API key for multiple sites (stores).

Recomendation on using multiple API keys:  
Where possible or required on the merchant side to somehow distinguish different transactions it is recommended to use different API keys.  
* Example 1: A merchant having online stores in following countries: France, Germany, Spain (each country uses EUR as their official currency) might want to track transactions completely separately, for example due to tax, accounting, law requirements, technical problems or other reasons.
* Example 2: A merchant has single store but accepts multiple currencies (USD, GBP, EUR). To simplify accounting this merchant will request different national currency payments using different API keys.

Generally in order for different payments to be transferred to different bank accounts you have to use separate API keys.

## Request Limits
At the moment we don't do any rate-limiting of incoming API calls.

## API Authentication
The API relies on HTTP Basic Authentication mechanism.

You should set the username as your API key and leave the password field blank.
