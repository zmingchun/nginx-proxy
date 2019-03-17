Forked from https://github.com/jwilder/nginx-proxy

A new env varaible `ENABLE_ACME` is added to use acme.sh to generate free ssl cert from letsencrypt.

By default, acme.sh will use Nginx mode to obtain a certificate. DNS APIs can also be used, triggered by setting a DNS_PROVIDER environment variable.

All the other options are the same as the upstream project: https://github.com/jwilder/nginx-proxy

It's very easy to use.

### 1. Run nginx reverse proxy

#### Nginx mode

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

It's recommended to run with `--net=host` option, like:

```sh
docker run  \
-it  -d --rm  \
-v /var/run/docker.sock:/tmp/docker.sock:ro  \
-v $(pwd)/proxy/certs:/etc/nginx/certs \
-v $(pwd)/proxy/acme:/acmecerts \
-v $(pwd)/proxy/conf.d:/etc/nginx/conf.d \
--name proxy \
--net=host \
neilpang/nginx-proxy
```

#### Automatic DNS API integration

Set DNS_PROVIDER to the provider the same value as the --dns option in acme.sh. Add the environment variables required by the specific provider.

Example with Cloudflare:

```sh
docker run  \
-p 80:80 \
-p 443:443 \
-it  -d --rm  \
-e DNS_PROVIDER=dns_cf \
-e CF_Key="<cloudflare key>" \
-e CF_Email="<cloudflare email>" \
-v /var/run/docker.sock:/tmp/docker.sock:ro  \
-v $(pwd)/proxy/certs:/etc/nginx/certs \
-v $(pwd)/proxy/acme:/acmecerts \
-v $(pwd)/proxy/conf.d:/etc/nginx/conf.d \
--name proxy \
neilpang/nginx-proxy
```

For all other providers, see https://github.com/Neilpang/acme.sh/tree/master/dnsapi.


#### Docker Compose

For a docker compose v2 or v3 project, every project has a dedicated network, so, you must use `--net=host` option,  so that it can proxy any projects on you machine.

```yaml
version: '2'

services:
  nginx-proxy:
    image: neilpang/nginx-proxy
    ports:
      - "80:80"
      volumes:
        - /var/run/docker.sock:/tmp/docker.sock:ro
        - ./proxy/certs:/etc/nginx/certs
        - ./proxy/acme:/acmecerts
        - ./proxy/conf.d:/etc/nginx/conf.d
      network_mode: "host"
```


### 2. Run an internal webserver

```sh
docker run -itd --rm \
-e VIRTUAL_HOST=foo.bar.com \
-e ENABLE_ACME=true \
httpd

```


The other options: https://github.com/Neilpang/nginx-proxy/blob/upstream/README.md


