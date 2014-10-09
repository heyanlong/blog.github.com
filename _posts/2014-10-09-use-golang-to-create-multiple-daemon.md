---
layout: post
title: 使用golang创建守候进程
---
###背景
项目中要是用php后台守候进程处理大量的发短信任务，之前是启动起来就不管了，时间长了进程就休眠了或者异常退出了。然后想出一个这样的曲线救国的招数。
###流程
是用golang去创建守候进程，每一个进程对应一个golang协程，协程去监听进程。如果发生意外进程退出，则启动一个进程替代异常进程，这样来保证进程数量不会变少。。。真是曲线救国啊~~~
###使用

###代码
```go
package main
import (
	"flag"
	"fmt"
	"os"
	"os/exec"
)
```
[Fork me on Github](https://github.com/heyanlong/duorun, "Fork me on Github")