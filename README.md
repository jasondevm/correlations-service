# FT Labs rebuild of Slurp, surfacing correlations between people and orgs

Basic gist:

* read info from SAPI
* build in-memory indexes
* support six degrees -esque functionality
* see the root page for the full list of endpoints

# Environment Parameters

When building locally, specify them in a local file, .env (and NB, this must not be included in the git repo, hence has a specific line in .gitignore). When deploying to Heroku, they need to be specified in the app's settings, Config Variables.

## Header Params for all endpoints:

* TOKEN=...
	* Note: in the absence of token, the application will fall back to S3O. Otherwise, the token is passed to the endpoints as a header.

## Mandatory Environment params (the absence of which will kill the app on startup)

* CAPI_KEY=...

## Optional Environment params:

* ONTOLOGY, default value is 'people', but could also be 'organisations', 'topics'
* STARTUP_RANGE_SECS, default is 0, what pre-processing to do on startup (before listening), i.e. what interval to look for articles. Will delay the app on startup. Must not take longer than 25ish seconds. If the pre-processing fails, the app will not start.
* POST_STARTUP_RANGE_SECS, default is 0, what processing to do immediately after startup (after listening), i.e. what interval to look for articles.
   * the idea is to have the combined total range come to 7 days, as soon as poss, but ensuring the initial load is not so long that it kills the app on startup (when Heroku complains about it taking too long). There will be a period of a minute or so after the app has started listening when it won't have the full complement of data (which will be loading in as part of the post startup process).
* UPDATE_EVERY_SECS, default 0, to poll for the latest articles every N secs and incorporate them into the stats
* FACETS_CONCURRENCE, default 4, used to throttle the SAPI calls to this number of concurrent requests.
* CAPI_CONCURRENCE, default 4, used to throttle the CAPI calls to this number of concurrent requests.

## Environment params for local builds:

* SERVER_ROOT=TRANSPORT_AND_DOMAIN_OF_SERVICE
   * to use when constructing the podcast feed
   * e.g. when developing locally, http://localhost:8060
* DEBUG=correlations:\*,bin:lib:\*
