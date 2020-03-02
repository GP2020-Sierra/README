# API Documentation

The API exposed by the Azure Function is a simple HTTP REST API with a few GET endpoints which each return JSON.

This can be accessed at `https://gp2020-sierra.azurewebsites.net/` for our production version, or `https://[YOUR APP FUNCTION NAME].azurewebsites.net/` if you have followed the Azure documentation to copy this setup.

## Main Endpoints
- [`/api/locations`](https://gp2020-sierra.azurewebsites.net/api/locations)
- [`/api/summary`](https://gp2020-sierra.azurewebsites.net/api/summary)
- [`/api/data/[LOCATION ID]`](https://gp2020-sierra.azurewebsites.net/api/data/churchill-1c)

### Locations endpoint
`/api/locations` returns a JSON List containing Objects each containing `name` and `locationID` attributes:

```json
[

    {
        "name": "Churchill 1C",
        "locationID": "churchill-1c"
    },
    ...
]
```

### Data endpoint
`/api/data/[LOCATION ID]` (e.g. `/api/data/churchill-1c` for the above example) returns a JSON List containing Objects each containing the sensor data for a time period.

This endpoint supports options which can be supplied as URL parameters.

- `granularity` sets the time in minutes between data points (defaults to 5 minutes)
- `since` return data since a specific unix timestamp (also includes the last result before that timestamp)

e.g. `https://gp2020-sierra.azurewebsites.net/api/data/churchill-1c?granularity=60&since=1583020800`

```json
[
    {
        "timestamp": "2020-03-01T00:01:00.000Z",
        "temperature": 22.0,
        "pressure": 987.0,
        "humidity": 29,
        "co2": 405,
        "devices": 1,
        "_count": 300
    },
    {
        "timestamp": "2020-03-01T00:00:00.000Z",
        "temperature": 22.5,
        "pressure": 986.5,
        "humidity": 28,
        "co2": 402,
        "devices": 0,
        "_count": 258
    },
    ...
]
```

The fields inside each object store the `timestamp` of the group of readings, the average `temperature` within the window, the average `pressure`, the average `humidity`, the average `co2` and the average number of Wi-Fi connected `devices` nearby. The `_count` entry stores how many readings the averaged over. This can be used to check if more readings have been uploaded in the current timeslot since the last request.

### Summary endpoint
`/api/summary` is a hybrid of the location and data endpoint. It returns a JSON List containing Objects each containing `name` and `locationID` attributes like the location endpoint, but each one also has a `data` array which contains the last 15 minutes of data the sensor recorded in 1 minute intervals. This is in the same format as the readings for the data endpoint.

## Debug endpoints
Finally, there is a debug endpoint [`/api/debug/last-data`](https://gp2020-sierra.azurewebsites.net/api/debug/last-data) which can be used to monitor when each location last received data which can be useful to check the boards are working. This returns a JSON List, containing objects with the following attributes:
`location` (location name), `devOwner` (the device owner) and `lastData` (the timestamp of the last data received).