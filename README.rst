crawler
=======

crawler 致力于实现中文友好的网络抓取系统，项目基于PuerkitoBio的一个初级的并行的轻量级抓取库gocrawl.本项目目标：

    1. 实现分布式
    #. 增加机器学习算法
    #. 优化中文编码处理
    #. 完善文档


Features
========
*    Full control over the URLs to visit, inspect and query (using a pre-initialized [goquery][] document)
*    Crawl delays applied per host
*    Obedience to robots.txt rules (using the [robotstxt.go][robots] library)
*    Concurrent execution using goroutines
*    Configurable logging
*    Open, customizable design providing hooks into the execution logic

Installation and dependencies
=============================

crawl depends on the following userland libraries:

*    [goquery][]
*    [purell][]
*    [robotstxt.go][robots]

To install:

   *go get github.com/zchking/crawl*

To install a previous version, you have to `git clone https://github.com/zchking/crawl` into your `$GOPATH/src/github.com/zchking/gocrawl/` directory, and then run (for example) `git checkout v0.3.2` to checkout a specific version, and `go install` to build and install the Go package.

Changelog
=========

**2015.01.16** start from PuerkitoBio.Thanks. 


Example
=======

From `example_test.go`::

    package gocrawl

    import (
      "github.com/PuerkitoBio/goquery"
      "net/http"
      "regexp"
      "time"
    )

    // Only enqueue the root and paths beginning with an "a"
    var rxOk = regexp.MustCompile(`http://duckduckgo\.com(/a.*)?$`)

    // Create the Extender implementation, based on the gocrawl-provided DefaultExtender,
    // because we don't want/need to override all methods.
    type ExampleExtender struct {
      DefaultExtender // Will use the default implementation of all but Visit() and Filter()
    }

    // Override Visit for our need.
    func (this *ExampleExtender) Visit(ctx *URLContext, res *http.Response, doc *goquery.Document) (interface{}, bool) {
      // Use the goquery document or res.Body to manipulate the data
      // ...

      // Return nil and true - let gocrawl find the links
      return nil, true
    }

    // Override Filter for our need.
    func (this *ExampleExtender) Filter(ctx *URLContext, isVisited bool) bool {
      return !isVisited && rxOk.MatchString(ctx.NormalizedURL().String())
    }

    func ExampleCrawl() {
      // Set custom options
      opts := NewOptions(new(ExampleExtender))
      opts.CrawlDelay = 1 * time.Second
      opts.LogFlags = LogAll

      // Play nice with ddgo when running the test!
      opts.MaxVisits = 2

      // Create crawler and start at root of duckduckgo
      c := NewCrawlerWithOptions(opts)
      c.Run("https://duckduckgo.com/")

      // Remove "x" before Output: to activate the example (will run on go test)

      // xOutput: voluntarily fail to see log output
    }
    ```

Document
========
@TODO crawler Document

Refer to PuerkitoBio/gocrawl


Thanks
======
    
    - PuerkitoBio
    - Richard Penman
    - Dmitry Bondarenko
    - Markus Sonderegger

License
=======

The [BSD 3-Clause license][bsd].

[bsd]: http://opensource.org/licenses/BSD-3-Clause

[goquery]: https://github.com/PuerkitoBio/goquery

[robots]: https://github.com/temoto/robotstxt.go

[purell]: https://github.com/PuerkitoBio/purell

[robprot]: http://www.robotstxt.org/robotstxt.html

[robspec]: https://developers.google.com/webmasters/control-crawl-index/docs/robots_txt

