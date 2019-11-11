---
title: Bureau Works API

language_tabs:
  - shell

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>

includes:
  - terminology
  - errors

search: true
---

# Introduction

This API implements methods for creation and management of translation projects with [Bureau Works](https://bureau.works).

## Getting Started

Before you use your API credentials, there are a few items that need to be checked first. Users in Works with a profile suitable 
to request projects are bound to Clients. Clients are organizations or persons, and they allow for multiple users to share the 
same workspace. Your `Client` can be your own company, a corporation you work for, or youself.

We need to make sure that a `Client` has the following information on file:

- Country
- Currency
- Timezone (for properly calculating key dates)

<!---




    AUTH




-->

# Authentication

> Auth example, uses a JSON model in the request body:

```json
{
   "accesskey":"eeecccff-9999-332f-333c-999999999999",
   "secretAccesskey": "aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa"
}
```

```shell
curl "https://bureau.works/api/pub/v1/login"
  -H "Content-Type: application/json"
  -X POST -d '<MODEL>'
```

> Response `200` `X-AUTH-TOKEN: <TOKEN>`

Use this method to get the credentials necessary to access the API endpoints. 

If authentication is successful, returns a header `X-AUTH-TOKEN` that must be used in all subsequent API calls:

`X-AUTH-TOKEN: {token}`

### HTTP Request

`POST https://bureau.works/api/pub/v1/login`

<aside class="notice">
Please note that the API credentials are separated from your Bureau Works login and password. You must get your API credentials under the <b>My Account</b> section. 
</aside>

<!---




    LOCALE




-->

# Locale

Methods that provide support data for `Project` requests.

<!---




    LANGS




-->

## Languages

```shell
curl "https://bureau.works/api/pub/v1/language"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
[
  {
      "id": 2,
      "name": "Abkhaz",
      "code": "ab"
  },
  {
      "id": 1,
      "name": "Afar",
      "code": "aa"
  },
  {
      "id": 3,
      "name": "Afrikaans",
      "code": "af"
  }
]
```

These are all available languages in Bureau Works. If your project demands a translation in a language that is not
listed by this API, please contact us to resolve the issue.

You must use the value in `code` fields to populate requests with source or target languages.

### HTTP Request

`GET https://bureau.works/api/pub/v1/language`

<!---




    TIMEZONES




-->

## Timezones

```shell
curl "https://bureau.works/api/pub/v1/timezone"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` with a JSON formatted like this:

```json
[
    {
        "key": "AET",
        "value": "(GMT+10:00) AET"
    },
    {
        "key": "Antarctica/DumontDUrville",
        "value": "(GMT+10:00) Antarctica/DumontDUrville"
    },
    {
        "key": "Asia/Ust-Nera",
        "value": "(GMT+10:00) Asia/Ust-Nera"
    },
    {
        "key": "Asia/Vladivostok",
        "value": "(GMT+10:00) Asia/Vladivostok"
    },
    {
        "key": "Australia/ACT",
        "value": "(GMT+10:00) Australia/ACT"
    }
]
```

Timezones are used according to a client's or person's location. When creating a project, you must specify a timezone in order to correctly calculate creation date, and also due and delivery dates.

In your requests, you may use the `key` to populate `timezone` fields.

### HTTP Request

`GET https://bureau.works/api/pub/v1/timezone`

<!---




    PROJECT




-->

# Project

Projects are the core of the workflows in Bureau Works. We need to create Projects to engage professionals and users alike, perform translations, reviews or publish deliverables to
our clients. With these APIs, you can create and manage your own projects, as well as check for deliveries, approvals and get cost information. With the Project API, you will be able
to push files for translation, and receive the deliverables programatically.

A `Project` creation must follow this workflow:

- Create a `Project` wireframe, with languages and services - `Project` is created with a `Preparing` status
- Upload files to the desired services - only `Translation` services may be analyzed and counted automatically
- Finish "preparing" your projects, once you finish uploading file,s by invoking the `ready` method - `Project` is transitioned to a `Pending` state
- While `Pending`, you may approve or cancel the `Project` - approving a project will send it to production

<!---




    PROJECT / SERVICES




-->

## Services

```shell
curl "https://bureau.works/api/pub/v1/service"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
[
    {
        "id": 1,
        "name": "Translation"
    },
    {
        "id": 2,
        "name": "Sworn Translation"
    },
    {
        "id": 3,
        "name": "Transcription"
    },
    {
        "id": 4,
        "name": "Subtitling"
    },
    {
        "id": 5,
        "name": "Formatting & Design"
    },
    {
        "id": 6,
        "name": "Voiceover"
    },
    {
        "id": 7,
        "name": "Interpretation"
    },
    {
        "id": 8,
        "name": "Product Testing"
    },
    {
        "id": 9,
        "name": "In-Country Review"
    },
    {
        "id": 10,
        "name": "Project Management"
    },
    {
        "id": 11,
        "name": "File Engineering"
    },
    {
        "id": 12,
        "name": "Video QA"
    }
]
```

This method returns all `Services` available in the API, the most common is `Translation`. You can create projects with one or more 
`Services`, by using the `id` attribute in the requests that require this specification.

### HTTP Request

`GET https://bureau.works/api/pub/v1/service`

<!---




    PROJECT / CREATE




-->

## Create Project

> Request with this `model` in the body of the request, where `sourceLanguage` and `targetLanguages` are codes from the Language API, and `services` are IDs from the Service API. You can provide a `projectType` to communicate the kind of project, for example, `AEM` for Adobe Experience Manager, or `Finance` to offer some background on the content at hand.

```json
{
    "reference" : "Test project 004",
    "sourceLanguage" : "en_us",
    "targetLanguages" : ["pt_br", "es"],
    "services" : [1],
    "notes" : "You can add some notes here",
    "desiredDeliveryDate" : 1500392096126,
    "projectType" : null
}
```

```shell
curl "https://bureau.works/api/pub/v1/project"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST -d '<MODEL>'
```

> Response `200` `application/json`

```json
{
    "id": 10228,
    "clientName": "Bureau Translations",
    "clientId": 120,
    "currency": "USD",
    "name": "B-1865-72739",
    "reference": "Test project AEM3",
    "sourceLanguage": "pt_br",
    "quoteDueDate": 1500390746451,
    "creationDate": 1520385139654,
    "status": "PREPARING",
    "grandTotal": null,
    "delivered": false,
    "targetLanguages": [
        "en_us",
        "es"
    ],
    "tags": [],
    "items": [
        {
            "id": 13769,
            "serviceId": 1,
            "serviceName": "Translation",
            "originalFiles": [],
            "filesDeliveredByManagers": [],
            "deliveries": [],
            "words": 0,
            "subtotal": 0,
            "savings": 0,
            "grandTotal": 0,
            "jobs": []
        }
    ]
}
```

This will create a Project "wireframe", with a `Preparing` state. This means that the newly created project is ready to receive
files associated to all service items selected. You don't need to upload files for all service items, but usually a `Translation`
requires some files. Services that not necessarily require files are Interpreation, Consulting, Project Management, etc.

The DTO (Data Transfer Object) returned by this request is a simple representation of the `Project`. You will need some of this information
in order to proceed. Some key attributes to note here:

- `id` is the unique ID of the newly created `Project`
- `name` is an internal identifier and will be pointed out in Bureau Works, some reports, etc.
- `quoteDueDate` will be the millisecond representation of the desired date you submitted. This date may change depending on the analysis and
word count to be performed afterwards. If a desired date will occur before the calculated due date, the project may incur rush fees.
- `items` is an array with the services you selected. A given item's `id` is also unique accross the system, and you will need this value to
submit files for these services.
- `originalFiles` will contain the files you submit.
- `filesDeliveredByManagers` our project managers can also deliver files, perhaps after review rounds or other services, and here's where they will appear.


### HTTP Request

`POST https://bureau.works/api/pub/v1/project`

<!---




    PROJECT / FILE UPLOAD




-->

## Upload Files to a Project

```shell
curl -i -X POST -v -F "file=@/home/user/Desktop/file.docx"
  "https://bureau.works/api/pub/v1/project/{id}/file/{serviceItemId}"
  -H "Content-Type: multipart/form-data"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
[
    {
        "id": 11,
        "serviceItemId": 13770,
        "sourceLang": "pt_br",
        "targetLang": "en_us",
        "fileName": "BT_JD_Template_.docx",
        "status": "NEW",
        "statusChangeTimestamp": 1520572747693
    },
    {
        "id": 12,
        "serviceItemId": 13770,
        "sourceLang": "pt_br",
        "targetLang": "es",
        "fileName": "BT_JD_Template_.docx",
        "status": "NEW",
        "statusChangeTimestamp": 1520572747693
    }
]
```

This method returns an `HTTP 200` response if the upload is successful. The response body contains information about the job, such as its ID and status. This method is synchronous, so depending on the file size, a typical upload can take 
a few seconds to a couple of minutes for larger files, like a 50MB Text Document. During the upload process, a job may be created in the cat tool, which further
delays a response.

Each request will upload one file. If you upload the same file more than one time, it will be overwritten.

You must upload all your files to finish the preparation of the project for quoting.

### HTTP Request

`POST https://bureau.works/api/pub/v1/project/{id}/file/{serviceItemId}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemId | YES | The service item ID, returned as part of the project structure

<!---




    PROJECT / READY




-->

## Submit Project for Quoting

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/ready"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

```json
{
    "projectId": 1919,
    "inputWordsCount": 6365,
    "words": 0,
    "subtotal": 1654.9,
    "savings": 43.758,
    "grandTotal": 1611.142,
    "langPairCosts": [
        {
            "sourceLang": "pt_br",
            "targetLang": "en_us",
            "serviceItemId": 2699,
            "words": 6365,
            "wordFullPrice": 0.13,
            "subtotal": 827.45,
            "savings": 21.879,
            "grandTotal": 805.571,
            "translationMatches": [
                {
                    "description": "Words Translation 50-74% Match",
                    "words": 0,
                    "costPerItem": 0.065,
                    "total": 0
                },
                {
                    "description": "Words Translation Repetitions",
                    "words": 187,
                    "costPerItem": 0.013,
                    "total": 2.431
                },
                {
                    "description": "Words Translation 0-49% Match",
                    "words": 6178,
                    "costPerItem": 0.13,
                    "total": 803.14
                },
                {
                    "description": "Words Translation 85-94% Match",
                    "words": 0,
                    "costPerItem": 0.039,
                    "total": 0
                },
                {
                    "description": "Words Translation 75-84% Match",
                    "words": 0,
                    "costPerItem": 0.052,
                    "total": 0
                },
                {
                    "description": "Words Translation 95-99% Match",
                    "words": 0,
                    "costPerItem": 0.026,
                    "total": 0
                },
                {
                    "description": "Words Translation Context TM",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                },
                {
                    "description": "Words Translation 100% Match",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                }
            ]
        },
        {
            "sourceLang": "pt_br",
            "targetLang": "es",
            "serviceItemId": 2699,
            "words": 6365,
            "wordFullPrice": 0.13,
            "subtotal": 827.45,
            "savings": 21.879,
            "grandTotal": 805.571,
            "translationMatches": [
                {
                    "description": "Words Translation 50-74% Match",
                    "words": 0,
                    "costPerItem": 0.065,
                    "total": 0
                },
                {
                    "description": "Words Translation Repetitions",
                    "words": 187,
                    "costPerItem": 0.013,
                    "total": 2.431
                },
                {
                    "description": "Words Translation 0-49% Match",
                    "words": 6178,
                    "costPerItem": 0.13,
                    "total": 803.14
                },
                {
                    "description": "Words Translation 85-94% Match",
                    "words": 0,
                    "costPerItem": 0.039,
                    "total": 0
                },
                {
                    "description": "Words Translation 75-84% Match",
                    "words": 0,
                    "costPerItem": 0.052,
                    "total": 0
                },
                {
                    "description": "Words Translation 95-99% Match",
                    "words": 0,
                    "costPerItem": 0.026,
                    "total": 0
                },
                {
                    "description": "Words Translation Context TM",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                },
                {
                    "description": "Words Translation 100% Match",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                }
            ]
        }
    ],
    "serviceItemCosts": [
        {
            "serviceId": 1,
            "serviceItemId": 2699,
            "serviceName": "Translation",
            "words": 12730,
            "subtotal": 1654.9,
            "savings": 43.758,
            "grandTotal": 1611.142
        }
    ]
}
```

When you're done uploading your files, invoke this method to signal to Bureau Works that your `Project` is ready for quoting. This will trigger an analysis
tool that will try to parse and analyze the data. If the files are suitable for analysis, this method will return the cost structure. Otherwise, the quote will
update by our team and you will have that information in the system within a couple of hours if the project is submitted in business hours.

The DTO returned in JSON format contains the cost structure per Service Item and per language. Here are some key attributes:

- `inputWordsCount` this is the total word count for your project.
- `subtotal` is the cost of the project, the gross cost, without leveraging of Translation Memory.
- `savings` represents the value amount of savings due to Translation Memory leveraging.
- `grandTotal` is the the actual value you owe if you approve your project.
- `langPairCosts` is an array with a detailed breakdown of costs per target language, with the Translation Matches included.

If the `Project` is not analyzed automatically, it will return a JSON but with blank values and zeroes. That means that our team will provide a manual update, 
and in a similar structure.


### HTTP Request

`POST https://bureau.works/api/pub/v1/project/{id}/ready`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID

<!---




    PROJECT / APPROVE




-->

## Approve a Project

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/approve"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

When you approve a project, this will trigger some actions in Bureau Works: Project Managers will be notified and your `Project` will be eligible for
accounting/receivables.


### HTTP Request

`POST https://bureau.works/api/pub/v1/project/{id}/approve`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID

<!---




    PROJECT / CANCEL




-->

## Cancel a Project

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/cancel"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

This method will cancel a project, with no penalties involved.


### HTTP Request

`POST https://bureau.works/api/pub/v1/project/{id}/cancel`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID

<!---




    PROJECT / GET BY STATUS




-->

## Get Projects by Status

```shell
curl "https://bureau.works/api/pub/v1/project?status=PENDING"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
[
    {
        "id": 1919,
        "clientId": 120,
        "clientName": "Bureau Translations",
        "currency": "USD",
        "name": "B-17199-56225",
        "reference": "Test project 005",
        "sourceLanguage": "pt_br",
        "quoteDueDate": 1500665954789,
        "creationDate": 1500406625160,
        "status": "PENDING",
        "grandTotal": 1611.142,
        "delivered": false,
        "targetLanguages": [
            "en_us",
            "es"
        ],
        "tags": [],
        "items": [
            {
                "id": 2699,
                "serviceId": 1,
                "serviceName": "Translation",
                "originalFiles": [
                    "test.docx",
                    "ohplaces.txt"
                ],
                "filesDeliveredByManagers": [],
                "deliveries": [],
                "words": 12730,
                "subtotal": 1654.9,
                "savings": 43.758,
                "grandTotal": 1611.142
            }
        ]
    }
]
```

Gets a list of projects, by passing at least one `status` as parameter. You can provide more than one status, but at least one.
An example request would be, which would list all pending AND approved projects for your organization in the system:

`GET /api/pub/v1/project?status=PENDING&status=APPROVED`

### HTTP Request
`GET /api/pub/v1/project`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
status | YES | A status to filter the request. Valid statuses are `PREPARING`, `PENDING`, `APPROVED`, `CANCELLED` and `INVOICED`

<!---




    PROJECT / GET ONE




-->

## Get Project

```shell
curl "https://bureau.works/api/pub/v1/project/{id}"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
{
    "id": 10227,
    "clientName": "Bureau Translations",
    "clientId": 120,
    "currency": "USD",
    "name": "B-1865-63079",
    "reference": "Test project AEM2",
    "sourceLanguage": "pt_br",
    "quoteDueDate": 1520462190263,
    "creationDate": 1520375479574,
    "status": "PENDING",
    "grandTotal": 60826.374,
    "delivered": false,
    "targetLanguages": [
        "en_us",
        "es"
    ],
    "tags": [],
    "items": [
        {
            "id": 13768,
            "serviceId": 1,
            "serviceName": "Translation",
            "originalFiles": [
                "ohplaces.txt"
            ],
            "filesDeliveredByManagers": [],
            "deliveries": [],
            "words": 1854,
            "subtotal": 61302.51,
            "savings": 476.136,
            "grandTotal": 60826.374,
            "jobs": [
                {
                    "id": 3,
                    "serviceItemId": 13768,
                    "sourceLang": "pt_br",
                    "targetLang": "en_us",
                    "fileName": "ohplaces.txt",
                    "status": "READY_FOR_TRANSLATION",
                    "statusChangeTimestamp": null
                },
                {
                    "id": 4,
                    "serviceItemId": 13768,
                    "sourceLang": "pt_br",
                    "targetLang": "es",
                    "fileName": "ohplaces.txt",
                    "status": "READY_FOR_TRANSLATION",
                    "statusChangeTimestamp": null
                }
            ]
        }
    ]
}
```

Retrieves a project by passing its unique `id` to this method. 

### HTTP Request
`GET /api/pub/v1/project/{id}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID

<!---




    PROJECT / GET COST




-->

## Get Project Costs

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/cost"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
{
    "projectId": 1919,
    "inputWordsCount": 6365,
    "words": 0,
    "subtotal": 1654.9,
    "savings": 43.758,
    "grandTotal": 1611.142,
    "langPairCosts": [
        {
            "sourceLang": "pt_br",
            "targetLang": "en_us",
            "serviceItemId": 2699,
            "words": 6365,
            "wordFullPrice": 0.13,
            "subtotal": 827.45,
            "savings": 21.879,
            "grandTotal": 805.571,
            "translationMatches": [
                {
                    "description": "Words Translation 50-74% Match",
                    "words": 0,
                    "costPerItem": 0.065,
                    "total": 0
                },
                {
                    "description": "Words Translation Repetitions",
                    "words": 187,
                    "costPerItem": 0.013,
                    "total": 2.431
                },
                {
                    "description": "Words Translation 0-49% Match",
                    "words": 6178,
                    "costPerItem": 0.13,
                    "total": 803.14
                },
                {
                    "description": "Words Translation 85-94% Match",
                    "words": 0,
                    "costPerItem": 0.039,
                    "total": 0
                },
                {
                    "description": "Words Translation 75-84% Match",
                    "words": 0,
                    "costPerItem": 0.052,
                    "total": 0
                },
                {
                    "description": "Words Translation 95-99% Match",
                    "words": 0,
                    "costPerItem": 0.026,
                    "total": 0
                },
                {
                    "description": "Words Translation Context TM",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                },
                {
                    "description": "Words Translation 100% Match",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                }
            ]
        },
        {
            "sourceLang": "pt_br",
            "targetLang": "es",
            "serviceItemId": 2699,
            "words": 6365,
            "wordFullPrice": 0.13,
            "subtotal": 827.45,
            "savings": 21.879,
            "grandTotal": 805.571,
            "translationMatches": [
                {
                    "description": "Words Translation 50-74% Match",
                    "words": 0,
                    "costPerItem": 0.065,
                    "total": 0
                },
                {
                    "description": "Words Translation Repetitions",
                    "words": 187,
                    "costPerItem": 0.013,
                    "total": 2.431
                },
                {
                    "description": "Words Translation 0-49% Match",
                    "words": 6178,
                    "costPerItem": 0.13,
                    "total": 803.14
                },
                {
                    "description": "Words Translation 85-94% Match",
                    "words": 0,
                    "costPerItem": 0.039,
                    "total": 0
                },
                {
                    "description": "Words Translation 75-84% Match",
                    "words": 0,
                    "costPerItem": 0.052,
                    "total": 0
                },
                {
                    "description": "Words Translation 95-99% Match",
                    "words": 0,
                    "costPerItem": 0.026,
                    "total": 0
                },
                {
                    "description": "Words Translation Context TM",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                },
                {
                    "description": "Words Translation 100% Match",
                    "words": 0,
                    "costPerItem": 0.013,
                    "total": 0
                }
            ]
        }
    ],
    "serviceItemCosts": [
        {
            "serviceId": 1,
            "serviceItemId": 2699,
            "serviceName": "Translation",
            "words": 12730,
            "subtotal": 1654.9,
            "savings": 43.758,
            "grandTotal": 1611.142
        }
    ]
}
```

This method returns all costs related to the project with `id`. Whether the costs will come from an automatic analysis or a manual input, these costs are grouped by language and by service items.

### HTTP Request
`GET /api/pub/v1/project/{id}/cost`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID


<!---




    PROJECT / UPDATE DUE DATE




-->

## Update Project Due Date

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/due_date/{newDueDate}"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

```json
{
    "id": 10228,
    "clientName": "Bureau Translations",
    "clientId": 120,
    "currency": "USD",
    "name": "B-1865-72739",
    "reference": "Test project AEM3",
    "sourceLanguage": "pt_br",
    "quoteDueDate": 1500390746451,
    "creationDate": 1520385139654,
    "status": "PREPARING",
    "grandTotal": null,
    "delivered": false,
    "targetLanguages": [
        "en_us",
        "es"
    ],
    "tags": [],
    "items": [
        {
            "id": 13769,
            "serviceId": 1,
            "serviceName": "Translation",
            "originalFiles": [],
            "filesDeliveredByManagers": [],
            "deliveries": [],
            "words": 0,
            "subtotal": 0,
            "savings": 0,
            "grandTotal": 0,
            "jobs": []
        }
    ]
}
```

Updates the Project with the desired new due date. If the date is prior to the date calculated after the `ready` invoke, the project may incur Project Management and "Rush" fees.

<aside class="warning">This endpoint may recalculate the quote costs.</aside>

### HTTP Request
`POST /api/pub/v1/project//{id}/due_date/{newDueDate}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
newDueDate | YES | The desired due date, in epoch long (number) format



<!---




    PROJECT / GET ITEMS




-->

## Get Project Items

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/items"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
[
    {
        "id": 13768,
        "serviceId": 1,
        "serviceName": "Translation",
        "originalFiles": [
            "ohplaces.txt"
        ],
        "filesDeliveredByManagers": [],
        "deliveries": [],
        "words": 1854,
        "subtotal": 61302.51,
        "savings": 476.136,
        "grandTotal": 60826.374,
        "jobs": [
            {
                "id": 3,
                "serviceItemId": 13768,
                "sourceLang": "pt_br",
                "targetLang": "en_us",
                "fileName": "ohplaces.txt",
                "status": "READY_FOR_TRANSLATION",
                "statusChangeTimestamp": null
            },
            {
                "id": 4,
                "serviceItemId": 13768,
                "sourceLang": "pt_br",
                "targetLang": "es",
                "fileName": "ohplaces.txt",
                "status": "READY_FOR_TRANSLATION",
                "statusChangeTimestamp": null
            }
        ]
    }
]
```

This method returns all items (service items and jobs) related to the project with `id`. The array `jobs` contains information about individual jobs, i.e.,
per language and unique file within that project and service scope. Possible statuses for `jobs` are: `NEW`, `READY_FOR_TRANSLATION`, `TRANSLATION_IN_PROGRESS`, `TRANSLATION_APPROVED`, `TRANSLATION_REJECTED`, `TRANSLATION_DELIVERED` and `PROJECT_CANCELLED`, where:

`NEW` - file has just been uploaded.

`READY_FOR_TRANSLATION` - project is pending, waiting for approval

`TRANSLATION_IN_PROGRESS` - project was approved, so jobs will transition to this

`TRANSLATION_APPROVED` - user calls the `approve-job` endpoint to mark a job as accepted

`TRANSLATION_REJECTED` - user calls the `reject-job` endpoint and the job is marked as rejected

`TRANSLATION_DELIVERED` - translation is delivered by Bureau Works and corresponding translated file is available in the `deliveries` array

`PROJECT_CANCELLED` - the project has been cancelled for some reason, rendering all jobs as invalid


### HTTP Request
`GET /api/pub/v1/project/{id}/items`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID

<!---




    PROJECT / APPROVE JOBS/ITEMS




-->

## Approve Project Jobs / Items

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/approve-job/{serviceItemFileId}"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

This endpoint is used in some cases where the client wants to "mark" a given job as approved. A common use-case is in API integrations where a polling task
will monitor this approval to effectively download the translated files.

### HTTP Request
`POST /api/pub/v1/project/{id}/approve-job/{serviceItemFileId}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemFileId | YES | The service item file `job` ID, returned as part of the jobs structure for `items`

<!---




    PROJECT / REJECT JOBS/ITEMS




-->

## Reject Project Jobs / Items

> You can send a rejection message in the request body

```shell
Rejection message as plain string format
```

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/reject-job/{serviceItemFileId}"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST -d '<MESSAGE>'
```

> Response `200` `application/json`

This endpoint is used in some cases where the client wants to "mark" a given job as rejected. A common use-case is in API integrations where a polling task
will monitor this reject to disregard or request a new translation job.

### HTTP Request
`POST /api/pub/v1/project/{id}/reject-job/{serviceItemFileId}`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemFileId | YES | The service item file `job` ID, returned as part of the jobs structure for `items`

### Request Body

Content | Required | Description
--------- | ------- | -----------
String | NO | A pure text string informed by the user about this rejection action


<!---




    DOWNLOAD FILES




-->

## Download Delivered Files (NO language provided)

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/{serviceItemId}/{filename}/"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
{
    "signed_request": "https://<direct_download_url>"
}
```

This endpoint will download a file that is listed in the `deliveries` array of the higher level object `item`, an instance of a Service Item. This endpoint does not need a language specified, because the file path may contain the language reference already, such as `<language>/<filename>`.

### HTTP Request
`GET /api/pub/v1/project/{id}/{serviceItemId}/{filename}/`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemId | YES | The Service Item ID
filename | YES | The filename as displayed in the `deliveries` array, including language directory if any


## Download Delivered Files (language provided)

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/{serviceItemId}/{lang}/{filename}/"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
{
    "signed_request": "https://<direct_download_url>"
}
```

This endpoint will download a file that is listed in the `deliveries` array of the higher level object `item`, an instance of a Service Item. This endpoint needs a language specified.

### HTTP Request
`GET /api/pub/v1/project/{id}/{serviceItemId}/{lang}/{filename}/`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemId | YES | The Service Item ID
lang | YES | The string language code, when the path does not include a call for the language
filename | YES | The filename as displayed in the `deliveries` array, including language directory if any


## Download Delivered Files by Job ID

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/delivered/{serviceItemFileId}"
  -H "X-AUTH-TOKEN: <TOKEN>"
```

> Response `200` `application/json`

```json
{
    "signed_request": "https://<direct_download_url>"
}
```

This endpoint will download a file that is listed in the `deliveredFile` attribute of the `job` instance. This endpoint should be invoked only when the job status is `TRANSLATION_DELIVERED`.

### HTTP Request
`GET /api/pub/v1/project/{id}/delivered/{serviceItemFileId}/`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID
serviceItemFileId | YES | The service item file (job) ID, returned as part of the jobs structure for `items`

<!---




    PROJECT / COMPLETE PROJECT




-->

## Complete a Project

```shell
curl "https://bureau.works/api/pub/v1/project/{id}/complete"
  -H "X-AUTH-TOKEN: <TOKEN>"
  -X POST
```

> Response `200` `application/json`

Updates the Project by adding a `completed` tag to it. This method does not modify any other metadata, and its sole purpose is to provide a communication channel between the API consumer and Bureau Works backoffice. 

### HTTP Request
`POST /api/pub/v1/project/{id}/complete`

### Query Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | YES | The project ID