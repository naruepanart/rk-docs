---
title: "Middleware gzip"
linkTitle: "Middleware gzip"
weight: 13
description: >
  Enable gzip interceptor/middleware for the server.
---

## Installation
```shell script
go get github.com/rookie-ninja/rk-boot/gin
```

## General options
> These are general options to start a gin server with rk-boot

| name | description | type | default value |
| ------ | ------ | ------ | ------ |
| gin.name | The name of gin server | string | N/A |
| gin.port | The port of gin server | integer | nil, server won't start |
| gin.enabled | Enable gin entry | bool | false |
| gin.description | Description of gin entry. | string | "" |

## Gzip options
| name | description | type | default value |
| ------ | ------ | ------ | ------ |
| gin.interceptors.gzip.enabled | Enable gzip interceptor | boolean | false |
| gin.interceptors.gzip.level | Provide level of compression, options are noCompression, bestSpeed, bestCompression, defaultCompression, huffmanOnly. | string | defaultCompression |

## Quick start
### 1.Create boot.yaml
```yaml
---
gin:
  - name: greeter                     # Required
    port: 8080                        # Required
    enabled: true                     # Required
    commonService:
      enabled: true                   # Optional, default: false
    interceptors:
      gzip:
        enabled: true                 # Optional, default: false
        level: bestSpeed              # Optional, options: [noCompression, bestSpeed， bestCompression, defaultCompression, huffmanOnly]
```

### 2.Create main.go
```go
// Copyright (c) 2021 rookie-ninja
//
// Use of this source code is governed by an Apache-style
// license that can be found in the LICENSE file.
package main

import (
	"context"
	"fmt"
	"github.com/gin-gonic/gin"
	"github.com/rookie-ninja/rk-boot"
    "github.com/rookie-ninja/rk-boot/gin"
	"io"
	"net/http"
	"strings"
)

// Application entrance.
func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Register handler
    ginEntry := rkbootgin.GetGinEntry("greeter")
	ginEntry.Router.POST("/v1/post", post)

	// Bootstrap
	boot.Bootstrap(context.Background())

	// Wait for shutdown sig
	boot.WaitForShutdownSig(context.Background())
}

// PostResponse Response of Greeter.
type PostResponse struct {
	ReceivedMessage string
}

// post Handler.
func post(ctx *gin.Context) {
	buf := new(strings.Builder)
	io.Copy(buf, ctx.Request.Body)

	ctx.JSON(http.StatusOK, &PostResponse{
		ReceivedMessage: fmt.Sprintf("Received %s!", buf.String()),
	})
}
```

### 3.Validate
> Send request

```shell script
$ echo 'this is message' | gzip | curl --compressed --data-binary @- -H "Content-Encoding: gzip" -H "Accept-Encoding: gzip" localhost:8080/v1/post
{"ReceivedMessage":"this is message\n"}
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)


