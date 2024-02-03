# fioepq9/errors

## Feature

1. 0 依赖
2. 干净且清晰的错误堆栈

## QuickStart

```bash
go get -u github.com/fioepq9/errors
```

```go
package main

import (
	"fmt"

	"github.com/fioepq9/errors"
)

func foo(method string) error {
	switch method {
	case "new":
		return errors.New("foo")
	case "wrap":
		return errors.Wrap(foo("new"), "this is wrapeed foo")
	default:
		panic("invalid method")
	}
}

func bar(method string) error {
	switch method {
	case "new":
		return foo("new")
	case "wrap":
		return errors.Wrap(foo("wrap"), "this is wrapeed bar")
	default:
		panic("invalid method")
	}
}

func baz(method string) error {
	switch method {
	case "new":
		return bar("new")
	case "wrap":
		return errors.Wrap(bar("wrap"), "this is wrapeed baz")
	default:
		panic("invalid method")
	}
}

func main() {
	errors.C.EnableCleanFileName()
	errors.C.Style = errors.StyleStack

	fmt.Println(baz("new"))
	fmt.Println(baz("wrap"))
}

/*
foo
  main.go:12 (0x658aba) main.foo()
  main.go:23 (0x658b7a) main.bar()
  main.go:34 (0x658c3a) main.baz()
  main.go:46 (0x658d24) main.main()
  D:/apps/scoop/apps/go/current/src/runtime/proc.go:267 (0x605411) runtime.main()

foo
  main.go:12 (0x658aba) main.foo()
this is wrapeed foo
  main.go:14 (0x658af5) main.foo()
this is wrapeed bar
  main.go:25 (0x658bb5) main.bar()
this is wrapeed baz
  main.go:36 (0x658c75) main.baz()
  main.go:47 (0x658d6e) main.main()
  D:/apps/scoop/apps/go/current/src/runtime/proc.go:267 (0x605411) runtime.main()

*/
```

## Examples

> see examples/stack/main.go

```bash
==== golang errors.New ====
foo
===========================

==== golang fmt.Errorf(%w) ====
this is wrapeed baz: this is wrapeed bar: this is wrapeed foo: foo
============================

==== pkg errors.New ====
foo
=========================

==== pkg errors.Wrap ====
this is wrapeed baz: this is wrapeed bar: this is wrapeed foo: foo
==========================

==== pkg errors.Wrap(stack trace) ====
main.baz
        D:/code/projects/errors/examples/stack/main.go:64
main.main
        D:/code/projects/errors/examples/stack/main.go:94
runtime.main
        D:/apps/scoop/apps/go/current/src/runtime/proc.go:267
runtime.goexit
        D:/apps/scoop/apps/go/current/src/runtime/asm_amd64.s:1650
=====================================

==== fioepq9 errors.New(normal style) ====
foo
===========================================

==== fioepq9 errors.Wrap(normal style) ====
this is wrapeed baz: this is wrapeed bar: this is wrapeed foo: foo
============================================

==== fioepq9 errors.New(stack style: default) ====
foo
  main.go:16 (0xfd1e36) main.foo()
  main.go:35 (0xfd20b6) main.bar()
  main.go:54 (0xfd22f6) main.baz()
  main.go:111 (0xfd2b05) main.main()
  D:/apps/scoop/apps/go/current/src/runtime/proc.go:267 (0xf664f1) runtime.main()

===================================================

==== fioepq9 errors.Wrap(stack style: default) ====
foo
  main.go:16 (0xfd1e36) main.foo()
this is wrapeed foo
  main.go:18 (0xfd1e75) main.foo()
this is wrapeed bar
  main.go:37 (0xfd20f5) main.bar()
this is wrapeed baz
  main.go:56 (0xfd2335) main.baz()
  main.go:115 (0xfd2bcd) main.main()
  D:/apps/scoop/apps/go/current/src/runtime/proc.go:267 (0xf664f1) runtime.main()

====================================================

==== fioepq9 errors.New(stack style: json) ====
[{"message":"foo","frames":[{"function":"main.foo","file":"main.go","line":16},{"function":"main.bar","file":"main.go","line":35},{"function":"main.baz","file":"main.go","line":54},{"function":"main.main","file":"main.go","line":120},{"function":"runtime.main","file":"D:/apps/scoop/apps/go/current/src/runtime/proc.go","line":267}]}]
================================================

==== fioepq9 errors.Wrap(stack style: json) ====
[{"message":"foo","frames":[{"function":"main.foo","file":"main.go","line":16}]},{"message":"this is wrapeed foo","frames":[{"function":"main.foo","file":"main.go","line":18}]},{"message":"this is wrapeed bar","frames":[{"function":"main.bar","file":"main.go","line":37}]},{"message":"this is wrapeed baz","frames":[{"function":"main.baz","file":"main.go","line":56},{"function":"main.main","file":"main.go","line":124},{"function":"runtime.main","file":"D:/apps/scoop/apps/go/current/src/runtime/proc.go","line":267}]}]
=================================================

```
