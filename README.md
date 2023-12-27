# Overview
This API allows you to search properties from our vast range of footprints data.

## Endpoint URL
The base URL for the API is
```bash
https://service.geox-ai.com
```

# Autorization
The API requires to be signed with Version 4 signing process of AWS. Here is how we can do it in different ways:

## Postman
In the Authorization tab of Postman app, do the following:
- For Type, choose AWS Signature.
- For AccessKey and SecretKey, enter your IAM access key ID and secret access key.

## Python
We can install and use the AWS Requests Auth library from [here](https://pypi.org/project/aws-requests-auth/)

## Manual
To manually sign your requests using another tool or environment,
use the [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html).
For more information, see [Signing requests](https://docs.aws.amazon.com/apigateway/api-reference/signing-requests/).


# HTTP response codes
- 200 — Success.
- 400 — Bad Request There was a validation error with the input. Most probably you missed to provide lat/lng or correlationId in the request body.
- 401 — You Signging process was failed. Probably due to incorrect AWS Access Key/Secret Key.
- 403 — Forbidden.
- 404 — Not Found One or more of the required resources was not found, please make sure you entered correct endpoint URL.
- 500 — Internal Server Error This is an issue with our servers processing your request. In most cases we are notified so that we can investigate the issue.

# Getting Started

## Postman
Postman is an amazing application. It's a tool that allows developers to make HTTP requests extremely easy, save examples, work under different environments, we LOVE it!

Postman is downloadable for free use from here and supported with lots of developer docs here.We are providing you the full collection of this API for your usage with Postman, this collection includes examples of different requests & responses, the endpoints, documentation to immensely ease your interaction with our API.

After downloading Postman follow the instructions below to get started.

=> Single Location API with GET method
1. Create a new request.
2. In the Request URL field, paste your API's invoke URL which is https://service.geox-ai.com/api/v10g/parcels
3. Select the GET HTTP method
4. Setup authorization as mentioned above.
5. Now put your lat/lng or address in the Params section with `lat` and `lng`  and `address` keys.
6. Finally, execute the API.

## Access API with Python
1. We will need following libraries to be installed
   - requests
   - aws_requests_auth

### Python code for both single and batch requests
```python
import json
import requests
from aws_requests_auth.aws_auth import AWSRequestsAuth


def m2m_request_single_location(access_key, secret_key, lat=None, lng=None, address=None):
    """
    This function will execute the API with single location and return the response.
    :param access_key: AWS Access Key
    :param secret_key: AWS Secret Key
    :param lat: Latitude of the location
    :param lng: Longitude of the location
    :param address: Address of the location
    :return: Response of the API
    """
    assert (lat and lng) or address, "Either lat/lng or address is required."
    api_url = "https://service.geox-ai.com/api/v10g/parcels"
    aws_details = {
        'aws_access_key': access_key,
        'aws_secret_access_key': secret_key,
        'aws_host': "service.geox-ai.com",
        'aws_region': "us-east-1",
        'aws_service': "execute-api"
    }
    auth = AWSRequestsAuth(**aws_details)
    if (lat and lng):
        params = {
            "lat": lat,
            "lng": lng
        }
    else:
        params = {
            "address": address
        }
    res = requests.get(api_url, auth=auth, params=request_params)
    assert res.status_code == 200, f"Request failed with status: {res.status_code}"
    res_data = res.json()
    return res_data


if __name__ == '__main__':
    # Calling single location API with lat/lng
    m2m_response = m2m_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               lat=45.6696163800542, lng=-122.5038830583887)
    print(json.dumps(m2m_response, indent=2))

    # Calling single location API with address
    m2m_response = m2m_request_single_location("YOUR_API_KEY", "YOUR_API_SECRET",
                               address="123 Main St, Portland, OR 97204")
    print(json.dumps(m2m_response, indent=2))

```

## Access API with cURL
The API request needs to be signed with AWS Signature Version 4. Please follow this [link](https://docs.aws.amazon.com/general/latest/gr/sigv4-signed-request-examples.html) for more details.

=> Single Location API with GET method
```shell
curl --location 'https://service.geox-ai.com/api/v10g/parcels?address=2306%20River%20Oaks%20Boulevard%2C%20Jackson%2C%20MS%20United%20States' \
--header 'X-Amz-Date: 20231227T075033Z' \
--header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIXXXXXXXXXXXXXXXX/20231227/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
```

## Access API with wget
=> Single Location API with GET method
```shell
wget --no-check-certificate --quiet \
  --method GET \
  --timeout=0 \
  --header 'X-Amz-Date: 20231227T075709Z' \
  --header 'Authorization: AWS4-HMAC-SHA256 Credential=AKIXXXXXXXXXXXXXXXX/20231227/us-east-1/execute-api/aws4_request, SignedHeaders=host;x-amz-date, Signature=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX' \
   'https://service.geox-ai.com/api/v10g/parcels?address=2306 River Oaks Boulevard, Jackson, MS United States'
```

# Request and Response Samples
Here are the sample request and response

## Request URL
```shell
https://service.geox-ai.com/api/v10g/parcels
```

## Request Query params Sample (Single Location API)

=> With lat/lng
```shell
lat=32.38883737450578&lng=-90.10806699133454
```
=> With address
```shell
address=2306 River Oaks Boulevard, Jackson, MS United States
```

## Response Sample

=> Single Loation API
```json
{
    "footprints": [
        {
            "roof_type": "Gable",
            "roof_material": "Shingle",
            "center_coord": "32.38883737450578, -90.10806699133454",
            "footprint_area": 176.88449189972076
        }
    ],
    "address": "2306 River Oaks Blvd, Jackson, Mississippi, USA",
    "coord": "32.3888218, -90.1081171",
    "detail": "successful"
}
```
