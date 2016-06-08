[![GoDoc](https://godoc.org/github.com/didip/stopwatch?status.svg)](http://godoc.org/github.com/didip/stopwatch)
[![license](http://img.shields.io/badge/license-MIT-red.svg?style=flat)](https://raw.githubusercontent.com/didip/stopwatch/master/LICENSE)

## Stopwatch

A small library to measure latency of things.

It can measure:

1. Arbitrary closure's latency.

2. Request latency via middleware pattern.


## Five Minutes Tutorial
```
package main

import (
    "fmt"
    "github.com/didip/stopwatch"
    "net/http"
)

func HelloHandler(w http.ResponseWriter, req *http.Request) {
    w.Write([]byte("Hello, World!"))
}

func main() {
    // 1. Create a stopwatch.
    s := stopwatch.New()

    // 2. Pull latency result asynchronously.
    go func() {
        for {
            select {
            case latency := <-s.ResultChan:
                fmt.Printf("Latency in nanoseconds: %v\n", latency)
            }
        }
    }()

    fmt.Println("Starting HTTP server on :12345")
    http.Handle("/", stopwatch.LatencyFuncHandler(s, HelloHandler))
    http.ListenAndServe(":12345", nil)
}
```