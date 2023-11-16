---
title: VScode + 算法题😋
date: 2022-09-13 19:55:24
tags: 算法 VSCode
---

VSCode 拿来写算法题本身就已经非常方便了，不过调教一下之后可以更方便

<!--more-->

## ① 配置用户代码片段
每次写题不再需要一遍遍的写头文件和主函数一些宏定义之类的。
```
"Print to conaole": {
		"prefix": "C++", //在新建立的页面中输入C++就会有智能提示，Tab就自动生成好了
		"body": [
			"#include <iostream>",
			"#include <algorithm>",
			"#include <cmath>",
			"#include <string>",
			"#include <cstring>",
			"#include <vector>",
			"#include <queue>",
			"#include <map>",
			"#include <set>",
			"#include <list>",
			"", //空行
			"using namespace std;", //标准命名空间
			"", //空行
			"typedef long long ll;",
			"typedef pair<int,int> P;",
			"const int maxn = 1e5 + 5;",
			"",
			"int main(){", //main()函数
			"    cin.tie(0)->ios::sync_with_stdio(false);",
			"    $0", //最终光标会在这里等待输入
			// "   system(\"pause\");", //标准C++的等待用户动作
			"    return 0;", //结束
			"}",
			"",
		],
		"description": "A cpp file template." //用户输入后智能提示的内容（你可以用中文写“生成C++模板”）
	}
```

## ②Code Runner 省略命令行输入

> 这个功能非常的实用！

不需要自己输入测试样例，配合VScode多窗口界面更加高效
结合Code runner本身的工作原理，是帮我们输入命令行，那么我们只需要打开CodeRunner的配置文件修改一下自动输入的命令行即可实现。


## ③tabnine 代码提示插件

使用体验良好，优点是确实非常智能  
缺点也很明显，经常会和其它代码提示插件冲突，并且占用内存过大，导致我在教室写代码的时候电脑风扇火力全开，太尴尬了，无奈只能想办法降低内存。

## ④code format

自定义的Google风格代码格式化，不过Google风格默认的缩进是两个字符，稍微配置一下改成了4个字符。
```javascript
 { BasedOnStyle: Google, UseTab: Never, IndentWidth: 4, TabWidth: 4, BreakBeforeBraces: Attach, AllowShortIfStatementsOnASingleLine: false, IndentCaseLabels: false, ColumnLimit: 80, AccessModifierOffset: -4 }
```

