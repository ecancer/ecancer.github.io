## Accessing the API

A web interface is provided at the root URL of the API to define and 
demonstrate the API functionality. The endpoints are given with 
try-it-out functionality. The schema are documented.

After trying out an endpoint, the appropriate CURL request is shown.

~~~
Endpoint:           https://api.ecancer.org
Authentication:     basic authentication
~~~

## Using the API

Intended use:

1. Call each endpoint repeatedly to obtain a full index of all entity types.
2. Use the last-update filtering to keep indexes up-to-date for entities which have changed since the initial index was created.

## Downloading videos from Vimeo

Intended use:

1. Call the Video endpoint to index videos. Each video resource will have
a set of 'sources' which will generally consist of a single Vimeo item.
The Vimeo ID is under the `sources.serviceVideoId` key.
2. Call the Vimeo endpoint `/vimeo/{id}` for time-limited secure download
links. There is one link per video resolution. Expiry times are given.
3. Use the download link for the required resolution to download the video
file iteself (usually .mp4) from the Vimeo hosting service.

NB: Vimeo implements rate-limiting. The rate-limiting data is returned
in the headers of the API response e.g.

Response headers:
~~~
 x-ratelimit-limit: 1000 
 x-ratelimit-remaining: 996 
 x-ratelimit-request-date: 2022-08-30T08:55:06+00:00 
 x-ratelimit-reset: 2022-08-30T08:55:40+00:00 
~~~

Use the API web interface to try out the endpoints. The returned
headers are displayed below the returned body (JSON).
