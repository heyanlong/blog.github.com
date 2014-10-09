---
layout: post
title: 使用golang创建守候进程
---
###背景
项目中要是用php后台守候进程处理大量的发短信任务，之前是启动起来就不管了，时间长了进程就休眠了或者异常退出了。然后想出一个这样的曲线救国的招数。
###流程
是用golang去创建守候进程，每一个进程对应一个golang协程，协程去监听进程。如果发生意外进程退出，则启动一个进程替代异常进程，这样来保证进程数量不会变少。。。真是曲线救国啊~~~
###使用
在shell输入

```shell
go run test.go -n 100 -f /root/test.php
```

###代码
~~~go
package main

import (
	"flag"
	"fmt"
	"os"
	"os/exec"
)

var (
	inputN = flag.Int("n", 100, "并发数")
	inputF = flag.String("f", "", "程序名称")
	php    string
)

func main() {
	path, pathErr := exec.LookPath("php")
	if pathErr != nil {
		panic(pathErr)
		os.Exit(1)
	}
	php = path
	flag.Parse()
	if len(*inputF) == 0 {
		fmt.Println("请输入程序名")
	}
	c := make(chan int)
	for i := 0; i < *inputN; i++ {
		go start(c)
	}
	for i := 0; i < *inputN; i++ {
		<-c
	}
}

//启动
func start(c chan int) {

	for {
		cmd := exec.Command(php, *inputF)
		startErr := cmd.Start()
		if startErr != nil {
			panic(startErr)
		} else {
			fmt.Printf("我启动了%d\n", cmd.Process.Pid)
			process := cmd.Process
			stat, _ := process.Wait()
			if stat.Exited() || !stat.Success() {
				fmt.Printf("%d退出了，启动一个替补\n", process.Pid)
			}
		}
	}
	c <- 0
}
~~~
[Fork me on Github](https://github.com/heyanlong/duorun, "Fork me on Github")