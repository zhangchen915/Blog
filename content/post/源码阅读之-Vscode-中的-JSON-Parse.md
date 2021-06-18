png---
title: "源码阅读之 Vscode 中的 JSON Parse"
categories: ["前端"]
tags: []
draft: false
slug: "790"
date: "2020-04-19 18:37:00"
---

编译原理一共分为四个部分：词法分析，语法（文法）分析，语义分析，代码生成。

词法分析将代码中的关键字、变量名、字符串、直接量和大括号等等转换成标记（token）并归类。
语法分析程序判断源程序在结构上是否正确。
语义分析的任务是对结构上正确的源程序进行上下文有关性质的审查

**词法分析器（lexer，scanner或tokenizer）--> 记号 --> 语法分析器（parser）  -->  抽象语法树（AST）**

### 词法分析
我们要首先抽象出哪些是我们` tokens`，进行归类，然后一个个字符的进行`read()`。

```ts
export type NodeType = 'object' | 'array' | 'property' | 'string' | 'number' | 'boolean' | 'null';

export function getNodeType(value: any): NodeType {
	switch (typeof value) {
		case 'boolean': return 'boolean';
		case 'number': return 'number';
		case 'string': return 'string';
		case 'object': {
			if (!value) {
				return 'null';
			} else if (Array.isArray(value)) {
				return 'array';
			}
			return 'object';
		}
		default: return 'null';
	}
}
```
createScanner方法在给定的文本上创建一个JSON扫描器。如果设置了ignoreTrivia，则空格或注释将被忽略。
这个方法定义了返回了一个JSONScanner对象，结构如下：
```ts
return {
		setPosition: setPosition,
		getPosition: () => pos,
		scan: ignoreTrivia ? scanNextNonTrivia : scanNext,  // 调用该方法获得当前位置的下一个TOKEN
		getToken: () => token,
		getTokenValue: () => value,
		getTokenOffset: () => tokenOffset,
		getTokenLength: () => pos - tokenOffset,
		getTokenError: () => scanError
	};
```



### 语法分析
`parse` 实际上也就是遍历并构造一颗抽象语法树，树中的每个节点都是Node类型。
这里的`parse`是一个叫 `visit` 方法，该方法接受一个`JSONVisitor`对象，这个对象包含了遍历时遇到对应节点的开始和结束时的回调。所以后续的处理都是通过这个visit方法来完成的。
核心是 scanNext 以深度优先方式逐一返回
```ts
export interface Node {
	readonly type: NodeType;
	readonly value?: any;
	readonly offset: number;
	readonly length: number;
	readonly colonOffset?: number;
	readonly parent?: Node;
	readonly children?: Node[];
}
```

```ts
function parseValue(): boolean {
		switch (_scanner.getToken()) {
			case SyntaxKind.OpenBracketToken:
				return parseArray();
			case SyntaxKind.OpenBraceToken:
				return parseObject();
			case SyntaxKind.StringLiteral:
				return parseString(true);
			default:
				return parseLiteral();
		}
	}
```
这里实现了两个 `parse`，名为`parse`的方法直接解析为JS对象，`parseTree`则解析为抽象语法树。

#### 用工具生成Parser
PEG（Parsing Expression Grammar，解析表达式文法），是一种解析形式文法。这种文法用一个识别字符串的规则的集合来描述某种形式语言，以纯公式的形式的展现递归下降解析器的基础语法。

参考文章：
https://zhuanlan.zhihu.com/p/107344979
https://github.com/microsoft/vscode/blob/master/src/vs/base/common/json.ts
https://www.codesky.me/archives/compilation-principle-from-zero-to-one-write-a-json-parser.wind
https://xwjgo.github.io/2018/03/09/jison%20VS%20PEG.js/
https://www.codercto.com/a/45502.html
