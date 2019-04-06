---
layout: post
title: "golang test if string is numeric"
date: 2019-04-06 09:13:55 +0700
tags: golang,numeric
---

How do you tell if a string is numeric in go lang? Here is a go lang example that shows how to determine if a string is a number:

### Source: (example.go)

```go
package main
 
import (
   "fmt"
   "strconv"
)
 
func main() {
   fmt.Println(IsNumeric("1"))
   fmt.Println(IsNumeric("12.345"))
   fmt.Println(IsNumeric("NOT"))
}
 
func IsNumeric(s string) bool {
   _, err := strconv.ParseFloat(s, 64)
   return err == nil
}
```

### Output

```
$ go run example.go
true
true
false
```

Link follow: [ispycode.com](https://ispycode.com/GO/String-Conversions/Test-if-a-string-is-numeric)
