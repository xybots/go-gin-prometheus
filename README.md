# go-gin-prometheus
[![](https://godoc.org/github.com/zsais/go-gin-prometheus?status.svg)](https://godoc.org/github.com/zsais/go-gin-prometheus) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Gin Web Framework Prometheus metrics exporter

## Installation

`$ go get github.com/banzaicloud/go-gin-prometheus`

## Usage

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/banzaicloud/go-gin-prometheus"
)

func main() {
	r := gin.New()

	p := ginprometheus.NewPrometheus("gin", []string{})
	p.SetListenAddress(":9900")
	p.Use(r, "/metrics")

	r.GET("/", func(c *gin.Context) {
		c.JSON(200, "Hello world!")
	})

	r.Run(":29090")
}
```

## Preserving a low cardinality for the request counter

The request counter (`requests_total`) has a `url` label which,
although desirable, can become problematic in cases where your
application uses templated routes expecting a great number of
variations, as Prometheus explicitly recommends against metrics having
high cardinality dimensions:

https://prometheus.io/docs/practices/naming/#labels

If you have for instance a `/customer/:name` templated route and you
want to generate a time series for every possible customer name:

```go
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/banzaicloud/go-gin-prometheus"
)

func main() {
	r := gin.New()

	p := ginprometheus.NewPrometheus("gin", []string{"name"})
	p.SetListenAddress(":9900")
	p.Use(r, "/metrics")

	r.GET("/", func(c *gin.Context) {
		c.JSON(200, "Hello world!")
	})

	r.Run(":29090")
}
```
