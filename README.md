# tileserv

## Idea
Easily spin up a vector tile server that will syncronize with some source.

### Components
0. postgis (external).
1. pg_tileserv - serves up vector tiles from postgis server.
2. proxy server (varnish?) - caches responses.
3. sync cronjob - updates database periodically.
## Reference
https://blog.crunchydata.com/blog/production-postgis-vector-tiles-caching

## Assumptions
- You have the oc command line installed and you're logged in.
- You have docker installed locally.
- You have a postgres database in your target openshift environment that can be accessed by pg_tileserv
## Instructions

```bash
# we have docker limits, so pull the images local - then put them in openshift

# pull local
docker pull eeacms/varnish
docker pull pramsey/pg_tileserv

# tag for upload
docker tag eeacms/varnish image-registry.apps.silver.devops.gov.bc.ca/e1e498-tools/varnish:latest
docker tag pramsey/pg_tileserv image-registry.apps.silver.devops.gov.bc.ca/e1e498-tools/pg_tileserv:latest

# log in to openshift docker
docker login -u developer -p $(oc whoami -t) image-registry.apps.silver.devops.gov.bc.ca

# push it
docker push image-registry.apps.silver.devops.gov.bc.ca/e1e498-tools/varnish:latest
docker push image-registry.apps.silver.devops.gov.bc.ca/e1e498-tools/pg_tileserv:latest

# deploy pg_tileserv
oc -n e1e498-dev process -f tileserv.yaml | oc -n e1e498-dev apply -f -
```