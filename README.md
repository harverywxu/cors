# CORS gin's middleware

[![Run Tests](https://github.com/harverywxu/cors/actions/workflows/go.yml/badge.svg)](https://github.com/harverywxu/cors/actions/workflows/go.yml)
[![codecov](https://codecov.io/gh/gin-contrib/cors/branch/master/graph/badge.svg)](https://codecov.io/gh/gin-contrib/cors)
[![Go Report Card](https://goreportcard.com/badge/github.com/harverywxu/cors)](https://goreportcard.com/report/github.com/harverywxu/cors)
[![GoDoc](https://godoc.org/github.com/harverywxu/cors?status.svg)](https://godoc.org/github.com/harverywxu/cors)

Gin middleware/handler to enable CORS support.

## Usage

### Start using it

Download and install it:

```sh
go get github.com/harverywxu/cors
```

Import it in your code:

```go
import "github.com/harverywxu/cors"
```

### Canonical example

```go
package main

import (
  "time"

  "github.com/harverywxu/cors"
  "github.com/gin-gonic/gin"
)

func main() {
  router := gin.Default()
  // CORS for https://foo.com and https://github.com origins, allowing:
  // - PUT and PATCH methods
  // - Origin header
  // - Credentials share
  // - Preflight requests cached for 12 hours
  router.Use(cors.New(cors.Config{
    AllowOrigins:     []string{"https://foo.com"},
    AllowMethods:     []string{"PUT", "PATCH"},
    AllowHeaders:     []string{"Origin"},
    ExposeHeaders:    []string{"Content-Length"},
    AllowCredentials: true,
    AllowOriginFunc: func(origin string) bool {
      return origin == "https://github.com"
    },
    MaxAge: 12 * time.Hour,
  }))
  router.Run()
}
```

### Using DefaultConfig as start point

```go
func main() {
  router := gin.Default()
  // - No origin allowed by default
  // - GET,POST, PUT, HEAD methods
  // - Credentials share disabled
  // - Preflight requests cached for 12 hours
  config := cors.DefaultConfig()
  config.AllowOrigins = []string{"http://google.com"}
  // config.AllowOrigins = []string{"http://google.com", "http://facebook.com"}
  // config.AllowAllOrigins = true

  router.Use(cors.New(config))
  router.Run()
}
```

Note: while Default() allows all origins, DefaultConfig() does not and you will still have to use AllowAllOrigins.

### Default() allows all origins

```go
func main() {
  router := gin.Default()
  // same as
  // config := cors.DefaultConfig()
  // config.AllowAllOrigins = true
  // router.Use(cors.New(config))
  router.Use(cors.Default())
  router.Run()
}
```

Using all origins disables the ability for Gin to set cookies for clients. When dealing with credentials, don't allow all origins.
