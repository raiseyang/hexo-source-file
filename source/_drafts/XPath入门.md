title: XPath入门
author: raiseyang
tags: []
categories: []
date: 2018-01-04 15:58:00
---


对此文档的描述
```
<?xml version="1.0" encoding="ISO-8859-1"?>
<bookstore>
  <book>
     <title lang="en">Harry Potter</title>
     <author>J K. Rowling</author>
     <year>2005</year>
     <price>29.99</price>
   </book>
</bookstore> 
```
<bookstore> **根**节点
<year>2005</year> **元素**节点
2005 **文本**节点
lang="en" **属性**节点


|表达式|描述|
|--|--|
|nodename	|选取此节点的所有子节点。
|/	|从根节点选取。
|//	|从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。
|.	|选取当前节点。
|..	|选取当前节点的父节点。
|@	|选取属性。

//book 选取所有book元素

@//lang 选取包含lang属性的所有节点
//title[@lang]	选取所有拥有名为 lang 的属性的 title 元素。

https://www.w3cschool.cn/xpath/xpath-syntax.html


