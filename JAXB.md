Java Architecture for XML Binding (JAXB)

java中的XML数据绑定（JAXB）

作者：[Ed Ort and Bhakti Mehta](http://www.oracle.com/technetwork/articles/javase/index-140168.html#author)


本文提纲

- [什么是JAXB](1)
- 例子
	- 结构绑定
	- Unmarshal 文档
- 构建XML文档
	- 绑定文档schema
	- 创建结构树
	- Marshal结构树
- 最后一个例子：更新XML文档
- 自定义的数据绑定
- 优势
- 运行例子程序

<h2 id= "1">什么是JAXB</h2>

可扩展标记语言(XML)和java技术是一直以来的搭档在帮助程序员传递数据和代码通过网络。这是因为XML是一种平台无关的数据交换方式和java技术提供了平台的可移植性应用。这种伙伴关系对web Services 显得格外重要，webservice 保证用户和应用程序的开发者开发的功能需求在任何地方在web上。XML和java技术被认为是开发web services 和网络服务应用的理想构建工具。
	
	XML和Java技术被认为是开发Web Services和与web Services进行通信的应用程序。一个新的java API被称为 Java Architecture for XML Binding（JAXB）能够使java编写的应用程序访问XML数据更简单。

但是怎样在实践中将两者进行结合呢？更明确的说，你怎样访问和使用XML文档（包含XML标签包裹的数据的文件）通过java程序开发语言？一种方法，或许是最传统的一种方法是，通过解析符合XML简单API（SAX）或者是文档对象模型（DOM）。两种解析方法都是有XML处理Java API提供的。java开发者可以调用SAX或者DOM解析程序通过JAXP API去解析XML文档——过程是，浏览整改文档然后按照逻辑关系分解成独立的片段。内容被解析为程序可以理解的。在SAX