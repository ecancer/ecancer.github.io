# The *e*cancer API

## Accessing the API

~~~
Base Endpoint:      https://api.ecancer.org
Authentication:     basic authentication
Example:            https://user:password@api.ecancer.org
~~~

A web dashboard is provided at [api.ecancer.org](https://api.ecancer.org).
The dashboard has functionality to explore and test the API.
Each endpoint is given with try-it-out functionality and test filtering.
The schema are documented.

After trying out an endpoint, the results and return headers are shown.
The corresponding CURL request is given which includes filtering and pagination.


## Using the API

Please note that the schema, including individual fields, are documented in the dashboard.

### The `id` field

Each of the *e*cancer resource types {Article, Editorial, News, Video} has an `id` field
and also an `articleNo` field.

The `id` should be considered the database identifier and used in the individual endpoints.   
The `articleNo` is what is used in URIs in the website.  

These will have the same values for News and Video i.e. `id == articleNo`.  
However, they are not necessarily, or generally, the same for Articles and Editorials.

Example: Article with `id=301` and `articleNo=300` is requested at `/articles/301`.

NB: for the **Vimeo** endpoint, the `id` is the Vimeo ID which is generally a long integer.
It is this longer ID which is required in the `vimeos/{id}` endpoint.

Example: Video with `id=10466` and `articleNo=10466` has Vimeo ID 719042458.


### Ordering of results

The resource collection endpoints are configured to return items in
reverse `publicationDate` and reverse `updateDate` order by default.

A different ordering can be specified. Use the dashboard and change the order
with the `order[publicationDate]` or `order[updateDate]` options to try this out.
The results will include the curl request for the selected ordering.

### Filtering of results by date

Each resource collection endpoint has four date filters on the `updateDate` and
`publicationDate` fields. The four date filters are: `before`, `strictly_before`,
`after`, `strictly_after`. 
The `strictly` filters do not include the specified date-time.

These can be combined to form date selection periods.

Date format example: `2020-01-01T12:01:02+00:00`

NB: dates stored in the database are UTC.

### Downloading videos from Vimeo

To download a video from the Vimeo hosting platform, the following procedure
may be used:

1. Call the **Video endpoint** `/videos` to index videos, 
or `/videos/{id}` for a specific video. Each video result will have
a set of 'sources' which will generally consist of a single Vimeo item.
The **Vimeo ID** is under the `sources.serviceVideoId` key.
2. Call the **Vimeo endpoint** `/vimeo/{id}` using the **Vimeo ID**.
The result should contain a set of time-limited secure download links. 
There is one link per video resolution. Expiry times are given.
(some will return 404 due to privacy settings - see NB below).
3. Use the download link for the required resolution to download the video
file iteself (usually .mp4) from the Vimeo hosting service.

NB: The `/videos` endpoint returns all Vimeo video sources for a given video resource.
However, some Vimeo videos have private visibility and are not publicly viewable.
These are not returned by the `/vimeo/{id}` endpoint - they return 404.
Generally, the first Vimeo video source returned will be the one visible to the public,
but if that gets a 404 from the `/vimeo/{id}` endpoint, then checkout other availabe
video sources for that resource.

NB: **Vimeo implements rate-limiting**.
The rate-limiting data from Vimeo is returned in the headers of the API response.  

Example:
~~~
 x-ratelimit-limit: 1000 
 x-ratelimit-remaining: 996 
 x-ratelimit-request-date: 2022-08-30T08:55:06+00:00 
 x-ratelimit-reset: 2022-08-30T08:55:40+00:00 
~~~

It has been our experience that Vimeo can impose rate-liming on incoming requests at any
time regardless of the limit data.

Use the API dashboard to try out the vimeo endpoint. The returned
headers, containing the `x-ratelimit` values are displayed below the returned body.

### To setup and update an index of *e*cancer content, the following procedure may be used:

1. Call each resource endpoint (`/articles`, `/editorials`, `/news`, `/videos`), 
cycling through the pages, to obtain all pages for each of the resource types.
2. Form your full index from these results.
3. Use the `updateDate` filters regularly (each day as a minimum) 
on each resource endpoint, to keep indexes up-to-date for new or updated resources
since the initial index was created.

It is important to keep indexes up to date as *e*cancer admin staff sometimes update 
or deactivate resources due to some client-based requirement or restriction.

