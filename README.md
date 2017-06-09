Forked from https://github.com/jwilder/nginx-proxy

A new env varaible `ENABLE_ACME` is added to use acme.sh to generate free ssl cert from letsencrypt.

All the other options are the same as the upstream project: https://github.com/jwilder/nginx-proxy

It's very easy to use:

### 1. Run nginx reverse proxy

```sh
docker run  \
-p 80:80 \
-p 443:443 \
-it  -d --rm  \
-v /var/run/docker.sock:/tmp/docker.sock:ro  \
-v $(pwd)/proxy/certs:/etc/nginx/certs \
-v $(pwd)/proxy/acme:/acmecerts \
-v $(pwd)/proxy/conf.d:/etc/nginx/conf.d \
--name proxy \
neilpang/nginx-proxy
```


### 2. Run an internal webserver

```sh
docker run -itd --rm \
-e VIRTUAL_HOST=foo.bar.com \
-e ENABLE_ACME=true \
httpd

```


The other options: https://github.com/Neilpang/nginx-proxy/blob/upstream/README.md


