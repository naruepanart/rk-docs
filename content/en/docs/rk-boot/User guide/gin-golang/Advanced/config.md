---
title: "Config"
linkTitle: "Config"
weight: 5
description: >
  How to read configs in local file system.
---

## Overview
Bootstrapper will try to read config files described in [config] section in boot.yaml file and load the file content with [viper](https://github.com/spf13/viper).

As a result, bellow file type can be supported.
- JSON
- TOML
- YAML
- HCL
- envfile
- Java propertie

## Architecture
![](/bootstrapper/user-guide/gin-golang/advanced/config-arch.png)

## Locale
> ConfigEntry support concept of [locale](/docs/bootstrapper/user-guide/gin-golang/advanced/locale/).

## Quick start
- Install

```shell script
$ go get github.com/rookie-ninja/rk-boot/gin
```

### 1.Create config
> Create config file and specify in boot.yaml file

**config/default.yaml**
```yaml
region: default
```
**boot.yaml**
```yaml
config:
  - name: my-config
    locale: "*::*::*::*"
    path: config/default.yaml
gin:
  - name: greeter
    port: 8080
    enabled: true
```

### 2.Refer config path
**main.go**
```go
package main

import (
	"context"
	"fmt"
	"github.com/rookie-ninja/rk-boot"
	_ "github.com/rookie-ninja/rk-boot/gin"
	"github.com/rookie-ninja/rk-entry/entry"
)

// Application entrance.
func main() {
	// Create a new boot instance.
	boot := rkboot.NewBoot()

	// Get ConfigEntry and get viper instance
	fmt.Println(rkentry.GlobalAppCtx.GetConfigEntry("my-config").GetViper().GetString("region"))

	// Bootstrap
	boot.Bootstrap(context.Background())

	// Wait for shutdown sig
	boot.WaitForShutdownSig(context.Background())
}
```

### 3.Access ConfigEntry
User can access ConfigEntry by calling **rkentry.GlobalAppCtx.GetConfigEntry()**.

