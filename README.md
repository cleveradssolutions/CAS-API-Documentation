# CAS.AI API Documentation
This guide is for developers who want to use the CAS.AI API to programmatically get information about their CAS.AI account.

The CAS.AI API is built on HTTP and JSON, so any standard HTTP client can send requests to it and parse the responses.

## Authorization
Every request your application sends to the CAS.AI API must include an authorization token. 
```
https://b2b.cas.ai/api/login
```

### POST Request: multipart/form-data
```json
{ "email": "examapl@host.com", "password": "account password" }
```

### Response: JSON
```json
{ "access_token": "Authorization token" }
```

Use token for Authorization every API request using the Bearer scheme. For example in request header:
```
Authorization: Bearer <access_token>
```
> [!NOTE]  
> Token lifetime: 15 minutes.

## Ad Format id
- 1 - Banner
- 2 - Interstitial
- 3 - Rewarded

## Ad Sources list
```
https://b2b.cas.ai/api/mediation/adsources
```

### GET Request: 
NO Parameters  
### Response: JSON
```json
[
    {
        "Id ": 2,
        "AdSource_Name": "AdMob"
    },
    {
        "Id ": 3,
        "AdSource_Name": "Mintegral"
    }
]
```

#### Array of objects with structure
| Field | Type | Description |
|---|---|---|
| Id | integer | Ad source id |
| AdSource_Name | string | Ad source name |


## Countries list
```
https://b2b.cas.ai/api/mediation/country
```

### GET Request: 
NO Parameters  
### Response: JSON

```json
[
    {
        "Id": 4,
        "Country_Name": "Azerbaijan"
    },
    {
        "Id": 5,
        "Country_Name": "Bangladesh"
    },
    {
        "Id": 6,
        "Country_Name": "Belgium"
    }
]
```

#### Array of objects with structure
| Field | Type | Description |
|---|---|---|
| Id | integer | Country database id |
| Country_Name | string | Country bundle id |


## Applications list
```
https://b2b.cas.ai/api/mediation/apps
```

### GET Request: 
NO Parameters  
### Response: JSON
```json
[
    {
        "App_ID": 998,
        "Bundle_ID": "com.psv.ladybug.color_by_number",
        "Name": "Miraculous Ladybug: Coloring",
        "Status": "active"
    },
    {
        "App_ID": 999,
        "Bundle_ID": "com.hyppo.goodnight",
        "Name": "Good Night Hippo",
        "Status": "active"
    }
]
```

#### Array of objects with structure
| Field | Type | Description |
|---|---|---|
| App_ID | integer | App database id |
| Bundle_ID | string | App bundle id |
| Name | string | App name |
| Status | string | App status can be: `active` or `test` or `inactive` |

# Mediation report
```
https://b2b.cas.ai/api/mediation/data
```

### POST Request body: application/json
Request object structure:
| Field | Type | Description |
|---|---|---|
| filter | array of objects | Mandatory filter options for report. |
| columns | array of objects | List of columns for report. |
| calcTotal | boolean | Append total row for report. |
| currency | string | Currency of revenue. Supported only: `euro` or `usd` |

#### Filter array options:
- Start/End date for report

> [!WARNING]  
> Required date filter for each request.

```json
{
    "type": "date",
    "value": {
        "beginDate": "YYYY-MM-DD",
        "endDate": "YYYY-MM-DD"
    }
}
```
- Report for selected application(s) id. Append to filter separated object for each applications.
```json
{ "type": "app", "value": 1 }
```
- Report for selected country(s) id. Append to filter separated object for each country.
```json
{ "type": "country", "value": 4 }
```
- Report for selected format(s) id. Append to filter separated object for each format.
```json
{ "type": "format", "value": 2 }
```
- Report for selected ad source(s) id. Append to filter separated object for each ad source.
```json
{ "type": "ad_source", "value": 3 }
```
#### Column array options:
- Add Ad source column to response
```json
{ "id": "ad_source" }
```
- Add Application column to response
```json
{ "id": "app" }
```
- Add Date, Week or Month column to response
```json
{ "id": "date | week | month" }
```
- Add Country column to response
```json
{ "id": "country" }
```
- Add Format column to response
```json
{ "id": "format" }
```
- Add Application platform column to response
```json
{ "id": "platform" }
```
- Add DAU column to response
```json
{ "id": "dau" }
```
> [!WARNING]  
> Available only without "ad_source" and "format" selection.

- Add ARPU column to response
```json
{ "id": "arpu" }
```
> [!WARNING]  
> Available only without "ad_source" and "format" selection.

- Add Impressions column to response
```json
{ "id": "impressions" }
```
- Add Observed ECPM column to response
```json
{ "id": "observed_ecpm" }
```
- Add Estimated earnings column to response
```json
{ "id": "est_earnings" }
```

#### POST Request example:
```json
{
    "filter": [
        {
            "type": "date",
            "value": {
                "beginDate": "2021-12-09",
                "endDate": "2021-12-15"
            }
        },
        { "type": "app", "value": 1 },
        { "type": "country", "value": 4 }
    ],
    "columns": [
        { "id": "ad_source" },
        { "id": "week" },
        { "id": "format" },
        { "id": "impressions" },
        { "id": "observed_ecpm" },
        { "id": "est_earnings" }
    ],
    "calcTotal": true
}
```

### Response: JSON
```json
{
    "list": [
        {
            "impressions": "219",
            "observed_ecpm": 0.5342465693547845,
            "est_earnings": 0.11699999868869781,
            "week": "2021-0",
            "ad_source": 2,
            "format": 2
        },
        {
            "impressions": "141",
            "observed_ecpm": 0.19148935345893212,
            "est_earnings": 0.026999998837709427,
            "week": "2021-0",
            "ad_source": 2,
            "format": 3
        }
    ],
    "totals": {
        "impressions": "6820",
        "observed_ecpm": 0.31428592090250496,
        "est_earnings": 2.1434299805550836
    }
}
````
