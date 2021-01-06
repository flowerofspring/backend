## net/http

### example

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func IndexHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Hello, World!")
}

func main() {
	http.HandleFunc("/", IndexHandler)
	err := http.ListenAndServe(":9090", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

### 源码解读-路由和路由注册

```go
// http.ListenAndServe
func ListenAndServe(addr string, hander Handler) error {
  server := &Server{Addr: addr, Handler: handler}
  return server.ListenAndServe()
}

// Server Struct
type Server struct {
  Addr string       // TCP address to listen on, ":http" if empty
  Handler Handler   // Handler是一个interface
}
```

**Multiplexer路由和路由注册**

golang的http服务暴露给开发者的核心入口就是Handler interface，开发者可以定义一个struct，实现这个interface。很多web框架都是实现了http的这个interface，然后在服务启动的时候，将struct实例传给handler参数，Gin框架就是如此。

```go
// Handler interfade
type Handler interface {
  ServeHTTP(ResponseWriter, *Request)
}
```

实现了Handler interface的结构体通常至少要具备两个功能：路由注册和路由查找并执行handler函数，这个是http框架的核心功能。

**ServeMux**结构体如下所示，ServeMux结构中最重要的字段为m，这是一个map，key是一些url模式，value是一个muxEntry结构，后者里定义存储了具体的url模式和handler函数。

所有跟业务相关的path和handler映射信息都存储在ServeMux中。

```go
type ServeMux struct {
  mu sync.RWMutex
  m map[string]muxEntry
  hosts bool
}

type muxEntry struct {
  explicti bool
  h Handler
  pattern string
}
```

Handler函数的第二个参数使用HandlerFunc对handler进行了类型转换。

```go
// HandleFunc registers the handler function for the given pattern
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request)) {
  mux.Handle(pattern, HandlerFunc(handler))
}
```

### 源码解读-处理client请求

http的ListenAndServe方法中创建了一个Server对象，并调用了Server对象的同名方法。Server的ListenAndServe方法中，会调用net.Listen方法对网络连接进行监听，然后将返回的TCP对象传入Serve方法。

Serve方法的主要职能是，调用Listener的Accept方法来获取连接，然后使用newConn创建新连接对象，最后创建一个goroutine处理新的连接请求。每一个连接都开启了一个协和，请求的上下文都不同，同时又保证了go的高并发。

newConn创建的实例有serve方法，调用此方法完成后面的逻辑处理。serve方法也很长，但里面的结构和逻辑还是很清晰的。

1. 使用defer定义了函数退出时，连接关闭相关的处理。
2. 读取网络连接的数据。
3. 调用serverHandler{c.server}.ServeHTTP(w, w.req) 处理请求。

serverHandler是一个重要的结构，它只有一个字段Server结构。它调用ServeHTTP方法，在该方法中做了一件重要的事情，初始化multiplexer路由多路复用器。如果没指定Handler，则使用默认的DefaultServeMux作为multiplexer，然后调用Handler的ServeHTTP方法。

```go
type serverHandler struct {
  srv *Server
}

func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
  handler :=  sh.srv.Handler
  if handler == nil {
    handler = DefaultServeMux
  }
  if req.RequestURI == "*" && req.Method == "OPTIONS" {
		handler = globalOptionsHandler{}
	}
  handler.ServeHTTP(rw, req)
}
```

handler实现了Handler interface的结构，也就是http包暴露给开发者的入口，实现查找路由并执行具体业务函数。

ServeMux的ServeHTTP方法内部调用mux.Handler路由到对应业务函数，最后执行h.ServeHTTP方法。至此，一个client的请求就处理结束了。

```go
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request) {
  if r.RequestURI == "*" {
    if r.ProtoAtLeast(1, 1) {
      w.Header().Set("Connection", "close")
    }
    w.WriteHeader(StatusBadRequest)
    return
  }
  h, _ := mux.Handler(r)
  h.ServeHTTP(w, r)
}
```



上面代码最后又调用了一个ServeHTTP，这个ServeHTTP和Hander接口中的ServeHTTP有啥区别？

参见下面的代码，HanderFunc是一个函数类型，函数签名是func(ResponseWriter, *Request)，该类型有一个方法叫ServeHTTP（在内部执行handler函数），所以在经过HandlerFunc转换的业务函数中找到handler后，可以调用ServeHTTP方法。

```go
type HandlerFunc func(ResponseWriter, *Request)

// ServeHTTP calls f(w, r)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
  f(w, r)
}
```

