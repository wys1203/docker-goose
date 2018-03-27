# Create a lightweight [goose](https://bitbucket.org/liamstask/goose) docker image

## Goose binary build
Since the goose dependency package go-sqlite3 is base on cgo, we cannot set `CGO_ENABLED=0`

HINT: `-ldflags "-extldflags -static"`

### [Use multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/)
```Dockerfile
FROM golang:alpine AS build-env

RUN set -ex \
        && apk add --no-cache --virtual build-dependencies \
            build-base \
            git \
        && go get -ldflags "-extldflags -static" bitbucket.org/liamstask/goose/cmd/goose \
        && apk del build-dependencies

FROM alpine

COPY --from=build-env /go/bin/goose /usr/local/bin/goose

ENTRYPOINT ["/usr/local/bin/goose"]
CMD ["--help"]

```
