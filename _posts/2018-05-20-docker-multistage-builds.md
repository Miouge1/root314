---
layout:     post
title:      Docker multi stage builds
author:     Maxime
date:       2018-05-20 18:00:00
header-img: "img/container-bg.jpg"
image: "img/container-sq.jpg"
tags: [docker]
category: docker
comments: true
---

With Docker 1.17 the long awaited multi stage builds are now available. It allows you to run several stages in your `Dockerfile` while reducing the size of the final container.

It is typically used for compiled languages like C, Java or Go, where you need a compiler environment to compile the code, but don't need it for runtime.

The `Dockerfile` below is an example showing this in action for a Go application, a single `docker build .` will:
1. First compile the source code with `golang:alpine` container (~250MB)
2. Copy the compiled artefact into a `scratch` container

The resulting runtime image is only 1.86MB (!) and should be the *absolute minimum* to run the application.

##### app.go
```
package main

import (
        "fmt"
)

func main() {
        fmt.Println("Hello Docker")
}
```
##### Dockerfile
```
# compiler container
FROM golang:alpine as build
ENV CGO_ENABLED=1
ADD . .
RUN go build -o /main

# runtime container
FROM scratch
COPY --from=build /main /main
ENTRYPOINT ["/main"]
```

You can find this example in [GitHub RootPi314/docker-examples/go-multistage](https://github.com/RootPi314/docker-examples/tree/master/go-multistage)

This can be easily adapted to cover any compiled language, more info available in the Docker documentation
