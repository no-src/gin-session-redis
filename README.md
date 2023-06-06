# gin-session-redis

[![Build](https://img.shields.io/github/actions/workflow/status/no-src/gin-session-redis/go.yml?branch=main)](https://github.com/no-src/gin-session-redis/actions)
[![License](https://img.shields.io/github/license/no-src/gin-session-redis)](https://github.com/no-src/gin-session-redis/blob/main/LICENSE)
[![Go Reference](https://pkg.go.dev/badge/github.com/no-src/gin-session-redis.svg)](https://pkg.go.dev/github.com/no-src/gin-session-redis)
[![Go Report Card](https://goreportcard.com/badge/github.com/no-src/gin-session-redis)](https://goreportcard.com/report/github.com/no-src/gin-session-redis)
[![codecov](https://codecov.io/gh/no-src/gin-session-redis/branch/main/graph/badge.svg?token=VnOF67iMER)](https://codecov.io/gh/no-src/gin-session-redis)
[![Release](https://img.shields.io/github/v/release/no-src/gin-session-redis)](https://github.com/no-src/gin-session-redis/releases)

Gin middleware for session management with redis store.

The gin-session-redis project is a fork
of [gin-contrib/sessions/redis](https://github.com/gin-contrib/sessions/tree/master/redis).
The purpose of this fork is to replace the [redigo](https://github.com/gomodule/redigo)
with [go-redis](https://github.com/redis/go-redis) as the driver of redis store.

## Usage

Download and install it:

```bash
go get -u github.com/no-src/gin-session-redis
```

## Example

```go
package main

import (
	"github.com/gin-contrib/sessions"
	"github.com/gin-gonic/gin"
	"github.com/no-src/gin-session-redis/redis"
)

func main() {
	r := gin.Default()
	store, _ := redis.NewStore(10, "tcp", "localhost:6379", "", []byte("secret"))
	r.Use(sessions.Sessions("mysession", store))

	r.GET("/incr", func(c *gin.Context) {
		session := sessions.Default(c)
		var count int
		v := session.Get("count")
		if v == nil {
			count = 0
		} else {
			count = v.(int)
			count++
		}
		session.Set("count", count)
		session.Save()
		c.JSON(200, gin.H{"count": count})
	})
	r.Run(":8000")
}
```