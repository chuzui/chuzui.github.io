---
layout: post
title: "用 coroutine/goroutine 做 lexer"
data: 2017-12-19 13:24:02
description: 
category: coroutine goroutine lexer design
tags: [design coroutine]
---

某天在读 dgraph 代码中解析 rdf 的 [lexer][3] 时，无意间发现了如下一段代码结构

```
type StateFn func(*Lexer) StateFn

func (l *Lexer) Run(f StateFn) *Lexer {
	for state := f; state != nil; {
		state = state(l)
	}
	return l
}
```

搜索了下，发现了2011年题为 "Lexical Scanning with Go" 的[video][1], [slide][2] 阐述了这种 lexer 的形式

相对于传统的状态机函数在不同的 state 之间跳转，并采取对应的 action, stateFn
将两者组合了起来: 执行 action, 返回下一个 state, 从而使得代码更加清晰.

slide 中有一句话说得挺好

> Concurrency is not about parallelism.
> Concurrency is a design approach


[1]: http://www.youtube.com/watch?v=HxaD_trXwRE "Lexical Scanning with Go [video]"
[2]: https://talks.golang.org/2011/lex.slide#1 "Lexical Scanning with Go [slide]"
[3]: https://github.com/dgraph-io/dgraph/blob/master/lex/lexer.go "dgraph lexer"
