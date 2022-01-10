---
title: "RK TV"
linkTitle: "RK TV"
weight: 4
description: >
  启动 RK TV。
---

## 概述
RK TV 是一个 Web 界面，用户可以通过 RK TV 获取服务以及进程的信息，API 信息，请求监控等。

## 安装
```shell script
go get github.com/rookie-ninja/rk-boot
go get github.com/rookie-ninja/rk-gf
```

## 通用选项
> 启动器包含了如下通用选项，这些选项是启动 GoFrame 服务的必要选项。

| 名字 | 描述 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| gf.name | GoFrame 服务名称 | string | N/A |
| gf.port | GoFrame 服务端口 | integer | nil, 服务不会启动 |
| gf.enabled | GoFrame 服务启动开关 | bool | false |
| gf.description | GoFrame 服务的描述 | string | "" |

## TV 选项
| 名字 | 描述 | 类型 | 默认值 |
| ------ | ------ | ------ | ------ |
| gf.tv.enabled | 启动 RK TV | boolean | false |

## 快速开始
### 1.创建 boot.yaml
```yaml
---
gf:
  - name: greeter
    port: 8080
    enabled: true
    tv:
      enabled: true     # Enable TV
```

### 2.创建 main.go
```go
package main

import (
	"context"
	"github.com/rookie-ninja/rk-boot"
    _ "github.com/rookie-ninja/rk-gf/boot"
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
> 验证
>
> [http://localhost:8080/rk/v1/tv](http://localhost:8080/rk/v1/tv)


![tv](/bootstrapper/getting-started/gf-golang/gf-tv.png)

### _**Cheers**_
![](/bootstrapper/user-guide/cheers.png)