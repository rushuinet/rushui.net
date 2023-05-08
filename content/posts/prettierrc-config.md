---
title: ".prettierrc文件常见配置"
date: 2023-04-12T22:59:06+08:00
keywords: ["prettier"]
description: "由于团队开发人员的编码习惯较难统一，要做到代码风格的一致性相对困难，尽管可以讨论制定代码规约，但可能会给开发带来额外的烦恼。Prettier 的出现，很好地解决了这个问题，还可以搭配 Git 的 hooks 规避不合规范的提交"
categories: ["技术文档"]
tags: ["prettier"]
thumbnail: ""
banner: ""
---

## 为何使用 prettier

由于团队开发人员的编码习惯较难统一，要做到代码风格的一致性相对困难，尽管可以讨论制定代码规约，但可能会给开发带来额外的烦恼。Prettier 的出现，很好地解决了这个问题，还可以搭配 Git 的 hooks 规避不合规范的提交

## Prettier 的配置项

说到自定义配置项，我们需要在项目根目录新建一个.prettierrc 的文件，格式为 json，结构如下：

```json
{
  "配置项名称": "配置项的值"
}
```

.prettierrc 文件常见配置

```json
{
  "semi": false,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "none"
}
```

常见配置解释

```json
{
  // tab缩进大小,默认为2
  "tabWidth": 2,
  // 使用tab缩进，默认false
  "useTabs": false,
  // 使用分号, 默认true
  "semi": false,
  // 使用单引号, 默认false(在jsx中配置无效, 默认都是双引号)
  "singleQuote": false,
  // 行尾逗号,默认none,可选 none|es5|all
  // es5 包括es5中的数组、对象
  // all 包括函数对象等所有可选
  "TrailingComma": "all",
  // 对象中的空格 默认true
  // true: { foo: bar }
  // false: {foo: bar}
  "bracketSpacing": true,
  // JSX标签闭合位置 默认false
  // false: <div
  //          className=""
  //          style={{}}
  //       >
  // true: <div
  //          className=""
  //          style={{}} >
  "jsxBracketSameLine": false,
  // 箭头函数参数括号 默认avoid 可选 avoid| always
  // avoid 能省略括号的时候就省略 例如x => x
  // always 总是有括号
  "arrowParens": "avoid"
}
```

其他配置项解释

```json
/*  prettier的配置 */
"prettier.printWidth": 120, // 超过最大值换行
"prettier.tabWidth": 2, // 缩进字节数
"prettier.useTabs": false, // 缩进不使用tab，使用空格
"prettier.semi": true, // 句尾添加分号
"prettier.singleQuote": true, // 使用单引号代替双引号
"prettier.proseWrap": "preserve", // 默认值。因为使用了一些折行敏感型的渲染器（如GitHub comment）而按照markdown文本样式进行折行
"prettier.arrowParens": "avoid", //  (x) => {} 箭头函数参数只有一个时是否要有小括号。avoid：省略括号
"prettier.bracketSpacing": true, // 在对象，数组括号与文字之间加空格 "{ foo: bar }"
"prettier.disableLanguages": ["vue"], // 不格式化vue文件，vue文件的格式化单独设置
"prettier.endOfLine": "auto", // 结尾是 \n \r \n\r auto
"prettier.eslintIntegration": false, //不让prettier使用eslint的代码格式进行校验
"prettier.htmlWhitespaceSensitivity": "ignore",
"prettier.ignorePath": ".prettierignore", // 不使用prettier格式化的文件填写在项目的.prettierignore文件中
"prettier.jsxBracketSameLine": false, // 在jsx中把'>' 是否单独放一行
"prettier.jsxSingleQuote": false, // 在jsx中使用单引号代替双引号
"prettier.parser": "babylon", // 格式化的解析器，默认是babylon
"prettier.requireConfig": false, // Require a 'prettierconfig' to format prettier
"prettier.stylelintIntegration": false, //不让prettier使用stylelint的代码格式进行校验
"prettier.trailingComma": "es5", // 在对象或数组最后一个元素后面是否加逗号（在ES5中加尾逗号）
"prettier.tslintIntegration": false // 不让prettier使用tslint的代码格式进行校验
```
