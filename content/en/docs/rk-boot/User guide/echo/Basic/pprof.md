---
title: "PPROF"
linkTitle: "PPROF"
weight: 4
description: >
  Enable pprof UI。
---

## Install
```shell script
go get github.com/rookie-ninja/rk-boot/v2
go get github.com/rookie-ninja/rk-echo
```

## Options
| options            | description          | type    | default |
|--------------------|----------------------|---------|---------|
| echo.pprof.enabled | Enable pprof web UI  | boolean | false   |
| echo.pprof.path    | Path of pprof web UI | string  | pprof   |

## Quick start
### 1.Create boot.yaml

```yaml
---
echo:
  - name: greeter
    port: 8080
    enabled: true
    pprof:
      enabled: true
#      path: ""
```

### 2.Create main.go
```go
package main

import (
	"context"
	"fmt"
    "github.com/labstack/echo/v4"
    "github.com/rookie-ninja/rk-boot/v2"
    "github.com/rookie-ninja/rk-echo/boot"
    "net/http"
)

func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Register handler
    echoEntry := rkecho.GetEchoEntry("greeter")
    echoEntry.Echo.GET("/v1/greeter", Greeter)

	// Bootstrap
	boot.Bootstrap(context.TODO())

	boot.WaitForShutdownSig(context.TODO())
}

func Greeter(ctx echo.Context) error {
  return ctx.JSON(http.StatusOK, &GreeterResponse{
    Message: fmt.Sprintf("Hello %s!", ctx.QueryParam("name")),
  })
}

type GreeterResponse struct {
	Message string
}
```

### 3.Validate
> **PPROF:** [http://localhost:8080/pprof](http://localhost:8080/pprof)

![](/rk-boot/user-guide/gin/basic/gin-pprof.png)

### _**Cheers**_
![](/rk-boot/user-guide/cheers.png)
