# go-ringbuf [V2]

![Go](https://github.com/hedzr/go-ringbuf/workflows/Go/badge.svg)
[![GitHub tag (latest SemVer)](https://img.shields.io/github/tag/hedzr/go-ringbuf.svg?label=release)](https://github.com/hedzr/go-ringbuf/releases)
[![go.dev](https://img.shields.io/badge/go-dev-green)](https://pkg.go.dev/github.com/hedzr/go-ringbuf)
[![GoDoc](https://img.shields.io/badge/godoc-reference-blue.svg?style=flat)](https://godoc.org/github.com/hedzr/go-ringbuf)
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fhedzr%2Fgo-ringbuf.svg?type=shield)](https://app.fossa.com/projects/git%2Bgithub.com%2Fhedzr%2Fgo-ringbuf?ref=badge_shield)
[![Go Report Card](https://goreportcard.com/badge/github.com/hedzr/go-ringbuf)](https://goreportcard.com/report/github.com/hedzr/go-ringbuf)
[![Coverage Status](https://coveralls.io/repos/github/hedzr/go-ringbuf/badge.svg?branch=master&.9)](https://coveralls.io/github/hedzr/go-ringbuf?branch=master)
<!--
[![Build Status](https://travis-ci.org/hedzr/go-ringbuf.svg?branch=master)](https://travis-ci.org/hedzr/go-ringbuf)
[![codecov](https://codecov.io/gh/hedzr/go-ringbuf/branch/master/graph/badge.svg)](https://codecov.io/gh/hedzr/go-ringbuf) 
-->

`go-ringbuf` provides a high-performance, lock-free circular queue (ring buffer) implementation in golang.

MPMC (multiple producers and multiple consumers) enabled.

## History

### v2.2.1

- security updates

### v2.2.0

- added new impl to support overlapped ringbuf
- security updates

### v2.1.0

- remove extras deps
  - replaced 'hedzr/log' with 'log/slog', 'hedzr/errors.v3' with 'errors'
- remove WithLogger()

### v2.0.+

security updates

### v2.0.0 @20220408 - go 1.18+

generic version for MPMC Ring Buffer.

- rewritten with go generics

### v1.0.+ [archived]

security updates

### v1.0.0 @20220408

Last release for classical version.

Next release (v2) will move to go 1.18+ with generic enabled.

### v0.9.1 @2022

- review all codes
- updated deps
- review and solve uncertain misreport failed licenses
  - ~~we have two deps: [hedzr/errors](https://github.com/hedzr/errors) and [hedzr/log](https://github.com/hedzr/log), and both them have no 3rd-party deps.~~
    - since 2.1.0, any deps removed.
  - we have no more 3rd-party deps.
  - we assumed a free license under MIT (unified).

## Getting Start

```bash
go get -v github.com/hedzr/go-ringbuf/v2
```

### Samples

```go
package main

import (
 "fmt"
 "log"

 "github.com/hedzr/go-ringbuf/v2"
)

func main() {
 testIntRB()
 testStringRB()
}

func testStringRB() {
 var err error
 var rb = ringbuf.New[string](80)
 err = rb.Enqueue("abcde")
 errChk(err)

 var item string
 item, err = rb.Dequeue()
 errChk(err)
 fmt.Printf("dequeue ok: %v\n", item)
}

func testIntRB() {
 var err error
 var rb = ringbuf.New[int](80)
 err = rb.Enqueue(3)
 errChk(err)

 var item int
 item, err = rb.Dequeue()
 errChk(err)
 fmt.Printf("dequeue ok: %v\n", item)
}

func errChk(err error) {
 if err != nil {
  log.Fatal(err)
 }
}
```

### Using Ring-Buffer as a fixed resource pool

The following codes is for v1, needed for rewriting

```go
func newRes() *Res{...}

var rb fast.RingBuffer

func initFunc() (err error) {
  const maxSize = 16
  
  if rb = fast.New(uint32(maxSize)); rb == nil {
  err = errors.New("cannot create fast.RingBuffer")
  return
 }

    // CapReal() will be available since v0.8.8, or replace it with Cap() - 1
 for i := uint32(0); i < rb.CapReal(); i++ {
  if err = rb.Enqueue(newRes()); err != nil {
   return
  }
 }
}

func loopFor() {
  var err error
  for {
    it, err := rb.Dequeue()
    checkErr(err)
    if res, ok := it.(*Res); ok {
      // do stuff with `res`, and put it back into ring-buffer
      err = rb.Enqueue(it)
    }
  }
}
```

### Using Overlapped Ring Buffer

Since v2.2.0, `NewOverlappedRingBuffer()` can initiate a different ring buffer, which
allows us to overwrite the head element if putting new element into a **full** ring buffer.

```go
func testStringRB() {
 var err error
 var rb = ringbuf.NewOverlappedRingBuffer[string](80)
 err = rb.Enqueue("abcde")
 errChk(err)

 var item string
 item, err = rb.Dequeue()
 errChk(err)
 fmt.Printf("dequeue ok: %v\n", item)
}
```

## Contrib

Welcome

## LICENSE

Apache 2.0

<!--
[![FOSSA Status](https://app.fossa.com/api/projects/git%2Bgithub.com%2Fhedzr%2Fgo-ringbuf.svg?type=large)](https://app.fossa.com/projects/git%2Bgithub.com%2Fhedzr%2Fgo-ringbuf?ref=badge_large)
-->