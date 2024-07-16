title: go 的 Context
author: Laiyong Wang
date: 2024-06-27 17:57:46
tags:
---
`context`包提供了一种方式来管理多个goroutine之间的截止时间、取消信号和请求范围值。`context`常用于在多个goroutine之间传递取消信号或截止时间，以便能够及时终止或超时处理操作。它的主要作用是控制并发操作的生命周期。

### 描述

`context`包提供了`Context`类型和与之相关的函数，用于传递截止日期、取消信号和其他请求范围值。`Context`类型是一个接口，它定义了四个方法：

- `Deadline() (deadline time.Time, ok bool)`：返回设置的截止时间和一个布尔值，表示是否设置了截止时间。
- `Done() <-chan struct{}`：返回一个通道，当`Context`被取消或超时时，通道会被关闭。
- `Err() error`：返回`Context`被取消的原因：`Canceled`或`DeadlineExceeded`。
- `Value(key interface{}) interface{}`：返回与`key`关联的值。

### 作用

- **取消信号传递**：允许在不同的goroutine之间传递取消信号，从而能够在需要时取消操作。
- **超时控制**：设置截止时间，自动在指定时间后取消操作。
- **传递请求范围的值**：在整个请求的生命周期中传递与请求相关的数据。

### 用法

通常，`context`会与`context.Background()`或`context.TODO()`一起使用来创建根`Context`，然后通过`context.WithCancel`、`context.WithDeadline`或`context.WithTimeout`派生出子`Context`。

### 实际例子

以下是一个实际例子，展示了如何在HTTP服务器中使用`context`来控制请求的超时和取消操作：

```go
package main

import (
	"context"
	"fmt"
	"net/http"
	"time"
)

func main() {
	http.HandleFunc("/hello", helloHandler)
	http.ListenAndServe(":8080", nil)
}

func helloHandler(w http.ResponseWriter, r *http.Request) {
	// 设置一个超时的 context
	ctx, cancel := context.WithTimeout(r.Context(), 2*time.Second)
	defer cancel()

	// 模拟一个需要时间的操作
	select {
	case <-time.After(3 * time.Second):
		fmt.Fprintln(w, "Hello, World!")
	case <-ctx.Done():
		// 如果操作超时或被取消
		http.Error(w, "Request canceled or timed out", http.StatusRequestTimeout)
	}
}
```

1. `helloHandler`接收一个HTTP请求，并创建一个带有2秒超时的`context`。
2. 使用`select`语句来等待模拟的操作完成或`context`超时。
3. 如果操作在2秒内完成，则返回“Hello, World!”。否则，返回一个超时错误。

通过这种方式，`context`帮助我们处理长时间运行的操作，并在需要时取消操作，避免资源浪费和响应延迟。