---
title: "Configuring Data"
description: "The api.json and api-*.json files allow us to configure the accessible paths for each data service."
headerTitle: "Data"
layout: "guide"
weight: 2
---

### {$page.title}

###### {$page.description}

<aside>

If you are unfamiliar with our API, please visit the [API Clients](/docs/intro/api-clients/) page first to learn how to install and configure it.

</aside>

<article id="1">

## Understanding configuration files

By default WeDeploy Data service is going to use all the JSON files starting with `api-*` and also the file `api.json`.

These files are used to help you manage features such as path validation, authentication, and params validation.

The api JSON files are located at the same path of the `./wedeploy.json` and are used following the ordering filesystem.

</article>

<article id="2">

## JSON attributes

After understanding how the api configuration files work, it's time to learn what are the supported attributes:

<div class="table-container">

| Field | Description |
| - | - |
| path | The path that represents the collection used to handle the request data. |
| data | Tells the service if the request to a collection should be stored or not. |
| description | Used to describe the behavior of an endpoint. |
| auth | Used to define authentication rules for the endpoint. |
| method | HTTP method allowed for the request. |
| parameters | Parameters and validation rules for the collection. |

</div>

##### path

A path represents the resource used to store your project data.

```application/json
[
	{
		"path": "/movies/:movieId"
	},
	{
		"path": "/fruits/*"
	}
]
```

##### data

You can create endpoints just for validation, in this case, data is used to finish the request in case you just need a validation or want to store the request in the collection.

```application/json
[
	{
		"path": "/fruits/*",
		"data": true
	}
]
```

##### description

Used to describe the behavior of an endpoint.

```application/json
[
	{
		"description": "Returns actors of a movie",
		"path": "/movies/:movieId/actors",
		"method": "GET"
	}
]
```

##### auth

You can prevent unauthorized applications and users from accessing certain endpoints by using the auth field. The example below verifies if the application is authenticated in order to perform the request:

```application/json
[
	{
		"path": "/movies/*",
		"auth": {
			"validator": "$auth != null"
		}
	}
]
```

##### method

Specifies the HTTP method used for the request. In the example bellow, it allows a GET request and if you try to do a PUT or DELETE the route will not be recognized and will fail.

```application/json
[
	{
		"path": "/movies/:movieId",
		"data": true,
		"method": "GET"
	}
]
```

##### parameters

You generally would use `parameters` to force validation in order to make sure that the params sent to a collection follow predefined rules.

```application/json
[
	{
		"description": "Creates a new movie",
		"path": "/movies",
		"method": "POST",
		"parameters": {
			"title": {
				"type": "string",
				"required": true
			}
		},
		"data": true
	}
]
```

</article>

<article id="3">

## Allowing usage of all the collections

In order to freely use any collection with any kind of operation, you just need to add the following content in your `api.json`.

```application/json
[
	{
		"path": "/*",
		"data": true
	}
]
```

The path `/\*` tells the data service to allow any request to the base path of the data service.

</article>

<article id="4">

## Validating resources

The Validator script will be executed in an environment where several request and server data will be available. In this environment, there are several global variables available to you that can be used to validate the request parameter, body, or even to authorize the request.

The validator can be used as an integration with the Auth service:

```application/json
{
	"path": "/movies/*",
	"auth": {
		"validator": "$auth != null"
	}
}
```

The global variables are:

<div class="table-container">

| Variable | Description |
| - | - |
| $auth | The authenticated user of this request. If the request was not authenticated, it will be null. |
| $config | The raw JSON data stored in the service's config.json file. |
| $session | All stored session data. If the request had no session cookie, it will be an empty map for the new session created for this request. |
| $params | The request params as they were loaded from url query and request body. All query and form parameters will be strings here. |
| $values | The parsed request params, as they are used for parameter validation. All query and form parameters will be parsed to JSON values. |
| $body | The parsed request body, according to the request Content-Type. |
| $data | The data view for this request, if a data path is mounted in the API path, and the request path represents a key to access any data resource (collection, document or inner field from a document). It will be null otherwise. |

</div>

Some common validators are:

<ol>

<li>Authenticated users only:</li>

```xml
$auth !== null
```

<li>Mixed with dynamic values:</li>

```xml
$auth.id === $params.id
```

<li>Validate new data value agains old one:</li>

```xml
$body.timestamp > $data.timestamp
```

<li>Multiple contitional validation:</li>

```xml
$auth !== null && $auth.id === $params.id
```

</ol>

</article>

## What's next?

Now that you have configured, you can start [saving data](/docs/data/saving-data/).
