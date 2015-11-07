# CDN Purge API
Cached content can be purged either through the web dashboard or the HTTP API. This document deals with purging paths with the API.
The API receives requests as query strings and returns JSON responses.

## Requirements
* Username

* API Password 

* Distribution ID

* The path to be purged

## Satisfying requirements
### Username
The API username is the same as the username used to log into the dashboard. 

### API Password
The API password is unique to the API system. The password is generated on the [Account API Page](https://manage.c3edge.com/account/api).
This page also contains additional documentation of the APIs.

### Distribution ID
The distribution ID uniquely identifies a CDN distribution. This ID can be found on the dashboard. Alternatively, you may obtain a list of your distributions using an API call.

To get the list, make a call to https://api.c3edge.com/dists/list.json with the username, API password, and no arguments. The ID field is the distribution ID that the purge API requires.

```
ibusr@boxen~$ curl https://exemplum:2619e75b5d4297adceabd54c9c0bd7fb9ca2da0ab98fc99bc9e037c67a580962a8c4314f7b79d3edb50d40d1d10f2db737688700ea18fb9406437c87fc9c327d@api.c3edge.com/dists/list.json
{
    "status" : "ok",
    "dists" : [
    {
        "id" : 434,
        "origin" : "data.up.example.net",
        "upstream" : "us01.example.net",
        "cname_to" : "a382828210.a.uxengine.net",
        "verified" : 1441067142
    }
    ]
}
```
## Purging URLs
To purge a path make an API call to /dists/purge.json, passing dist_id for the CDN distribution and the path you would like to purge.
Only one path may be purged at a time. Depending on how your site is structured, you may have to purge the same object twice
(e.g. /example/ and /example/index.html).

```
ibusr@boxen~$ curl 'https://exemplum:2619e75b5d4297adceabd54c9c0bd7fb9ca2da0ab98fc99bc9e037c67a580962a8c4314f7b79d3edb50d40d1d10f2db737688700ea18fb9406437c87fc9c327d@api.c3edge.com/dists/purge.json?dist_id=434&path=/pics/'
{
    "status" : "ok",
    "message" : "360 Tasks Triggered",
    "errors" : 0,
    "rate_limit" : {
        "used" : 4,
        "hourly_quota" : 600,
        "hourly_remaining" : 596
    },
    "targets" : [
        "/pics/"
    ],
    "dist" : {
        "id" : 434,
        "origin" : "data.up.example.net"
    }
}
```
The edge nodes will begin removing the cached content immediately. The process typically takes between 2 and 5 minutes for all global
nodes to purge the content.

## Limitations
All API calls are subject to default limit of 600 API calls per hour. When using the APIs in an automated fashion, take care to not exceed this limit.
All purge calls include a section with the current rate limit information.

Please note that purging paths is not a substitute for setting appropriate cache control policies. While it is possible to increase the rate limit,
if you find yourself needing significantly more API calls, you should first carefully consider how your site is structured. Techniques such
as appending a query strings to create unique cache keys may be more appropriate for content that changes frequently.
