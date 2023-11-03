# MeteALOlogia-API
API service of the "MeteALOlogia" project. The project's goal is to create a weather station along with a website to view live measurements. This repository aims at creating an HTTP REST API server managing the collected data.

## Installation
Use `poetry install` and `poetry run start` in order to run the project on `localhost:8080`. Endpoint docs are available on `/docs`

## Configuration file
The config file will contain all the static data of the weather stations:
- types of sensors
- station instances
- public keys

## Signatures
To minimise costs, we decided not to use an HTTPS certificate. Instead the `Authorization` header will contain:
 - station ID
 - a nonce to prevent resending the same payload
 - an encrypted signature of request's body and the two above, possible to be verified using a public key.

## Planned endpoints:

### GET /stations/
Success: *200 OK*
```json
[
  {
    "id": station id,
    "name": station display name,
    "sensors":
    [
      {
        "id": sensor id,
        "type": sensor type,
        "name": sensor display name,
        "lastReport": timestamp
      }
    ]
  }
]
```

### GET /stations/\<station id>
Invalid ID: *404 Not Found*

A corresponding station element from `GET /stations/`

### GET /stations/\<station id>/sensors/\<sensor id>
Invalid ID: *404 Not Found*

A corresponding sensor element from `GET /stations/`

### POST /stations/\<station id>/reports

#### Request structure
```json
[
  {
    "id": sensor id,
    "at": measurement timestamp,
    "data": { ... }
  }
]
```

#### Response
Success: *201 Created*

### GET /stations/\<station id>/reports
Doesn't require authorization.

#### Request parameters
- before=timestamp
- after=timestamp

These will provide the requested range of reports

#### Response
Success: *200 OK*
```json
[
  { ... as in POST /stations/<station id>/reports request}
]
```

### GET /stations/\<station id>/sensors/\<sensor id>/reports
Doesn't require authorization.

#### Request parameters
Same as in `GET /stations/<station id>/reports`

#### Response
```json
[
  {
    "at": measurement timestamp,
    "data": { ... }
  }
]
```


### GET /stations/\<station id>/listen
### GET /stations/\<station id>/sensors/\<sensor id>/listen
Similar to `/reports` counterparts, but maintains the SSE connection streaming ndjson each time a new `POST` is made to a corresponding endpoint.