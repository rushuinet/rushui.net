---
title: "golang的json标签"
date: 2023-04-04T22:50:17+08:00
keywords: ["golang","json"]
description: ""
categories: ["技术文档"]
tags: ["golang","json"]
thumbnail: ""
banner: ""
---

json:"-" // 表示不进行序列化,忽略

json:"name,omitempty"//加上omitempty，可以在序列化的时候忽略0值或者空值；若要在被嵌套结构体整体为空时使其在序列化结果中被忽略，不仅要在被嵌套结构体字段后加json:“name,omitempty”，还要将其改为结构体指针

json:",inline"通常作用于内嵌的结构体类型type

有些时候，我们在序列化或者反序列化的时候，可能结构体类型和需要的类型不一致，这个时候可以指定,支持string,number和boolean；如：

ProductID int64 json:"product_id,string"

示例：
```go
package main

import (
	"encoding/json"
	"fmt"
)
type T1 struct {
	FieldInt     int    `json:"field_int"`
	FieldIgnore  int    `json:"-"`                       //忽略
	FieldBooleab bool   `json:"field_boolean,string"`    //不同类型
	FieldString1 string `json:"field_string1,omitempty"` //忽略空值，当时复合结构时为要为指针类型
	FieldString2 string `json:"field_string2,omitempty"`
}
type T2 struct {
	T1 `json:",inline"`  //表示内嵌与T1输出一致
}
type T3 struct {
	T1 `json:"t1"` //表示用t1包一层
}
func main() {
	val1 := T1{
		FieldInt:     11,
		FieldIgnore:  11,
		FieldBooleab: true,
		FieldString2: "no empty",
	}
	bty1, _ := json.Marshal(val1)
	fmt.Printf("%v\r\n", string(bty1))

	val2 := T2{
		val1,
	}
	bty2, _ := json.Marshal(val2)
	fmt.Printf("%v\r\n", string(bty2))

	val3 := T3{
		val1,
	}
	bty3, _ := json.Marshal(val3)
	fmt.Printf("%v\r\n", string(bty3))
}
//程序输出
{"field_int":11,"field_boolean":"true","field_string2":"no empty"}
{"field_int":11,"field_boolean":"true","field_string2":"no empty"}
{"t1":{"field_int":11,"field_boolean":"true","field_string2":"no empty"}}
```