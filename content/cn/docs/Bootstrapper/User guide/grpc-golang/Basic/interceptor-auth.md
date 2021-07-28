---
title: "权限拦截器"
linkTitle: "权限拦截器"
weight: 11
description: >
  启动权限拦截器。
---

## 安装
```shell script
go get github.com/rookie-ninja/rk-boot
```

## 通用选项
> 启动器包含了如下通用选项，这些选项是启动 GRPC 服务的必要选项。

| 名字 | 描述 | 类型 | 默认值 | 必要与否
| ------ | ------ | ------ | ------ | ------ |
| grpc.name | GRPC 服务名称 | string | "", server won't start | Required |
| grpc.port | GRPC 服务端口 | integer | 0, server won't start | Required |
| grpc.description | GRPC 服务的描述 | string | "" | Optional |
| grpc.reflection | 启动 GRPC 反射功能 | boolean | false |

## 权限选项
| 名字 | 描述 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| grpc.interceptors.auth.enabled | 启动权限拦截器 | boolean | false |
| grpc.interceptors.auth.basic | Basic Auth 信息，格式：<user:pass> | []string | [] |
| grpc.interceptors.auth.apiKey | X-API-Key 信息 | []string | [] |
| grpc.interceptors.auth.ignorePrefix | 提供字符串前缀，拦截器会忽略包含这些字符串的请求路径 | []string | [] |

## 快速开始
### 1.创建 boot.yaml
```yaml
---
grpc:
  - name: greeter
    port: 8080
    rkServerOption: true
    commonService:
      enabled: true          # Enable common service for testing
    gw:
      enabled: true
      port: 8080
    interceptors:
      auth:
        enabled: true        # Enable auth interceptor/middleware
        basic: ["user:pass"] # Enable basic auth
```

### 2.创建 main.go
```go
package main

import (
	"context"
	"github.com/rookie-ninja/rk-boot"
)

// Application entrance.
func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Bootstrap
	boot.Bootstrap(context.Background())

	// Wait for shutdown sig
	boot.WaitForShutdownSig(context.Background())
}
```

### 3.验证
```shell script
$ curl  -X GET localhost:8080/rk/v1/healthy
# This is RK style error code if unauthorized
{
    "error":{
        "code":401,
        "status":"Unauthorized",
        "message":"Missing authorization, provide one of bellow auth header:[Basic Auth]",
        "details":[
            {
                "code":16,
                "status":"Unauthenticated",
                "message":"[from-grpc] Missing authorization, provide one of bellow auth header:[Basic Auth]"
            }
        ]
    }
}
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)

### 4.X-API-Key 类型授权
```yaml
---
grpc:
  - name: greeter
    ...
    interceptors:
      auth:
        enabled: true        # Enable auth interceptor/middleware
        apiKey: ["token"]    # Enable X-API-Key auth
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)

### 5.忽略请求路径
```yaml
---
grpc:
  - name: greeter
    ...
    interceptors:
      auth:
        enabled: true                                         # Enable auth interceptor/middleware
        basic: ["user:pass"]                                  # Enable basic auth
        ignorePrefix: ["/rk.api.v1.RkCommonService/Healthy"]  # Ignoring path with prefix
```

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)