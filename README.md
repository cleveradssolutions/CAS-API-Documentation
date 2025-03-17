# CAS.AI Reporting API
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

## Mediation report
```
https://b2b.cas.ai/api/mediation/data
```

### POST Request body: application/json
Request object structure:

| Field     | Type             | Description                                                                 |
| --------- | ---------------- | --------------------------------------------------------------------------- |
| filter    | array of objects | Mandatory filter options for report. See [table of filter](#filters) below. |
| columns   | array of objects | List of columns for report. See [table of columns](#columns) below.         |
| calcTotal | boolean          | Append total row for report. (`true` or `false`)                            |
| currency  | string           | Currency of revenue, `euro` or `usd` supported only.                        |

#### Filters
The filter options have the following structure:
```json
"filter": [
    { "type": "app", "value": 1 },
    { "type": "app", "value": 2 }
]
```

| Filter type | Description                                                                                                  | Value Example                                                     |
| ----------- | ------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------- |
| `date`      | (Required) The first and last days you want in the report, in `«YYYY»-«MM»-«DD»` format.                     | `"value": { "beginDate": "YYYY-MM-DD", "endDate": "YYYY-MM-DD" }` |
| `app`       | Report for selected application(s) id. Use id from [Applications Ids list](#applications-ids-list) response. | `"value": 1`                                                      |
| `country`   | Report for selected country(s) id. Use id from [Countries Ids list](#countries-ids-list) response.           | `"value": 4`                                                      |
| `format`    | Report for selected format(s) id. See [table of ad format ids](#ad-format-ids) below.                        | `"value": 2`                                                      |
| `ad_source` | Report for selected ad source(s) id. Use id from [Ad Sources Ids list](#ad-sources-Ids-list) response.       | `"value": 3`                                                      |

> [!WARNING]
> You must add `date` filter for each report request.

#### Columns
The filter options have the following structure:
```json
"columns": [
    { "id": "app" },
    { "type": "date" }
]
```

| Column id                   | Description                                                                                  |
| --------------------------- | -------------------------------------------------------------------------------------------- |
| `ad_source`                 | Ad source id column. Use id from [Ad Sources Ids list](#ad-sources-Ids-list) response.       |
| `app`                       | Application id column. Use id from [Applications Ids list](#applications-ids-list) response. |
| `date` or `week` or `month` | Date, Week or Month column                                                                   |
| `country`                   | Country id column. Use id from [Countries Ids list](#countries-ids-list) response.           |
| `format`                    | Ad Format id column. See [table of ad format ids](#ad-format-ids) below.                     |
| `platform`                  | Application name platform column                                                             |
| `impressions`               | Number of impressions shown                                                                  |
| `observed_ecpm`             | Observed ECPM column                                                                         |
| `est_earnings`              | Estimated earnings column                                                                    |
| `dau`                       | DAU column. Available only if `ad_source` and `format` columns not requested.                |
| `arpu`                      | ARPU column. Available only if `ad_source` and `format` columns not requested.               |

## Ad Format ids

| Id  | Name            |
| --- | --------------- |
| 1   | Banner Ad       |
| 2   | Interstitial Ad |
| 3   | Rewarded Ad     |
| 4   | Native Ad       |
| 5   | AppOpen Ad      |
| 6   | In Game Ad      |

## Ad Sources Ids list
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

Array of objects with structure

| Field         | Type    | Description           |
| ------------- | ------- | --------------------- |
| Id            | integer | Ad source database id |
| AdSource_Name | string  | Name of the Ad Source |

## Countries Ids list
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

Array of objects with structure

| Field        | Type    | Description         |
| ------------ | ------- | ------------------- |
| Id           | integer | Country database id |
| Country_Name | string  | Name of the Country |

## Applications Ids list
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

Array of objects with structure

| Field     | Type    | Description                              |
| --------- | ------- | ---------------------------------------- |
| App_ID    | integer | Database id                              |
| Bundle_ID | string  | Android bundle name or iTunes Id         |
| Name      | string  | Name of the application.                 |
| Status    | string  | Status: `active` or `test` or `inactive` |

## Mediation report POST Request example
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

## Mediation report response example
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
