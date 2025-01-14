---
title: "Middleware CSRF"
linkTitle: "Middleware CSRF"
weight: 17
description: >
  Enable CSRF Middleware
---

## Install
```shell script
go get github.com/rookie-ninja/rk-boot/v2
go get github.com/rookie-ninja/rk-fiber
```

## CSRF options
| options                             | description                        | type     | default |
|-------------------------------------|----------------------------------------------------|----------|-----------------------|
| fiber.middleware.csrf.enabled       | Enable CSRF middleware                             | boolean  | false                 |
| fiber.middleware.csrf.ignore        | Ignore by path                                     | []string | []                    |
| fiber.middleware.csrf.tokenLength   | Length of CSRF Token                               | int      | 32                    |
| fiber.middleware.csrf.tokenLookup   | Scheme of CSRF Token, See code comment for details | string   | "header:X-CSRF-Token" |
| fiber.middleware.csrf.cookieName    | Name of cookie                                     | string   | _csrf                 |
| fiber.middleware.csrf.cookieDomain  | Name of cookie domain                              | string   | ""                    |
| fiber.middleware.csrf.cookiePath        | Name of cookie path                                | string   | ""                    |
| fiber.middleware.csrf.cookieMaxAge   | Name of cookie max age                             | int      | 86400                 |
| fiber.middleware.csrf.cookieHttpOnly | Support http only                                  | bool     | false                 |
| fiber.middleware.csrf.cookieSameSite | SameSite mode, options: lax, strict, none, default | string   | default               |

## Quick start
### 1.Create boot.yaml
```yaml
---
fiber:
  - name: greeter
    port: 8080
    enabled: true
    middleware:
      csrf:
        enabled: true
#        ignore: [""]
#        tokenLength: 32
#        tokenLookup: "header:X-CSRF-Token"
#        cookieName: "_csrf"
#        cookieDomain: ""
#        cookiePath: ""
#        cookieMaxAge: 86400
#        cookieHttpOnly: false
#        cookieSameSite: "default"
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
  "github.com/gofiber/fiber/v2"
  "github.com/rookie-ninja/rk-boot/v2"
  "github.com/rookie-ninja/rk-fiber/boot"
  "net/http"
)

// @title Swagger Example API
// @version 1.0
// @description This is a sample rk-demo server.
// @termsOfService http://swagger.io/terms/

// @securityDefinitions.basic BasicAuth

// @contact.name API Support
// @contact.url http://www.swagger.io/support
// @contact.email support@swagger.io

// @license.name Apache 2.0
// @license.url http://www.apache.org/licenses/LICENSE-2.0.html
func main() {
  // Create a new boot instance.
  boot := rkboot.NewBoot()

  // Bootstrap
  boot.Bootstrap(context.TODO())

  // Register handler
  entry := rkfiber.GetFiberEntry("greeter")
  entry.App.Get("/v1/greeter", Greeter)
  // This is required!!!
  entry.RefreshFiberRoutes()

  boot.WaitForShutdownSig(context.TODO())
}

// Greeter handler
// @Summary Greeter
// @Id 1
// @Tags Hello
// @version 1.0
// @Param name query string true "name"
// @produce application/json
// @Success 200 {object} GreeterResponse
// @Router /v1/greeter [get]
func Greeter(ctx *fiber.Ctx) error {
  ctx.Response().SetStatusCode(http.StatusOK)
  return ctx.JSON(&GreeterResponse{
    Message: fmt.Sprintf("Hello %s!", ctx.Query("name")),
  })
}

type GreeterResponse struct {
  Message string
}
```

### 3.Validation
- GET request, a new cookie is expected

```shell script
$ curl -X GET -vs localhost:8080/v1/greeter
  ...
  < HTTP/1.1 200 OK
  < Content-Type: application/json; charset=UTF-8
  < Set-Cookie: _csrf=WyOJLwzhfUGAMDHglkuIRucdpalxolWg; Expires=Mon, 06 Dec 2021 18:38:45 GMT
  < Vary: Cookie
  < Date: Sun, 05 Dec 2021 18:38:45 GMT
  < Content-Length: 22
  <
  {"Message":"Hello !"}
```

- POST request

```shell script
$ curl -X POST -v --cookie "_csrf=my-test-csrf-token" -H "X-CSRF-Token:my-test-csrf-token" localhost:8080/v1/greeter
...
> Accept: */*
> Cookie: _csrf=my-test-csrf-token
> X-CSRF-Token:my-test-csrf-token
> 
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Set-Cookie: _csrf=my-test-csrf-token; Expires=Sun, 17 Apr 2022 12:41:46 GMT
< Vary: Cookie
< Date: Sat, 16 Apr 2022 12:41:46 GMT
< Content-Length: 27
< 
{"Message":"Hello rk-dev!"}
```

- POST request, invalid CSRF。

```shell script
$ curl -X POST -v -H "X-CSRF-Token:my-test-csrf-token" localhost:8080/v1/greeter
...
> X-CSRF-Token:my-test-csrf-token
>
< HTTP/1.1 403 Forbidden
< Content-Type: application/json; charset=UTF-8
< Date: Sat, 16 Apr 2022 12:42:27 GMT
< Content-Length: 114
<
{"error":{"code":403,"status":"Forbidden","message":"Invalid csrf token","details":[null]}}
```

### _**Cheers**_
![](/rk-boot/user-guide/cheers.png)