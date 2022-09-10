---
aliases: null
date: 2022-09-07 16:50:00 +02:00
tags: [2022-09]
title: WebService-protocols (SOAP,WSDL,UDDI)
TOC: true
---

# XML
https://www.runoob.com/xml/xml-intro.html

-   XML 的设计宗旨是传输和存储数据，而不是显示数据。
-   XML 标签没有被预定义。您需要自行定义标签。
-   XML 被设计为具有自我描述性。
-   XML 是 W3C 的推荐标准。
-   XML 被设计用来传输和存储数据，其焦点是数据的内容。
-   HTML 被设计用来显示数据，其焦点是数据的外观。
-   XML 是独立于软件和硬件的信息传输工具。
- XML 把数据从 HTML 分离，
- XML 简化平台变更
    升级到新的系统（硬件或软件平台），总是非常费时的。必须转换大量的数据，不兼容的数据经常会丢失。
    
    XML 数据以文本格式存储。这使得 XML 在不损失数据的情况下，更容易扩展或升级到新的操作系统、新的应用程序或新的浏览器。

## XML 声明

XML 声明文件的可选部分，如果存在需要放在文档的第一行，如下所示：
<?xml version="1.0" encoding="utf-8"?>

## XML 标签对大小写敏感
## XML 属性值必须加引号

与 HTML 类似，XML 元素也可拥有属性（名称/值的对）。

在 XML 中，XML 的属性值必须加引号。

## 实体引用

在 XML 中，一些字符拥有特殊的意义。

如果您把字符 "<" 放在 XML 元素中，会发生错误，这是因为解析器会把它当作新元素的开始。

这样会产生 XML 错误：
```plain-text
<message>if salary < 1000 then</message>
```

为了避免这个错误，请用**实体引用**来代替`<`

---

## XML 中的注释

在 XML 中编写注释的语法与 HTML 的语法很相似。

<!-- This is a comment -->

## XML 命名规则

XML 元素必须遵循以下命名规则：

-   名称可以包含字母、数字以及其他的字符
-   名称不能以数字或者标点符号开始
-   名称不能以字母 xml（或者 XML、Xml 等等）开始
-   名称不能包含空格

可使用任何名称，没有保留的字词。

## XML 元素是可扩展的

XML 元素是可扩展，以携带更多的信息。

请看下面的 XML 实例：

<note> <to>Tove</to> <from>Jani</from> <body>Don't forget me this weekend!</body> </note>

让我们设想一下，我们创建了一个应用程序，可将 `<to>`、`<from>` 以及 `<body>` 元素从 XML 文档中提取出来，并产生以下的输出：

**MESSAGE**

**To:** Tove  
**From:** Jani

Don't forget me this weekend!

想象一下，XML 文档的作者添加的一些额外信息：

```xml
<note> 
<date>2008-01-10</date> 
<to>Tove</to> 
<from>Jani</from> 
<heading>Reminder</heading> 
<body>Don't forget me this weekend!</body> 
</note>
```

那么这个应用程序会中断或崩溃吗？

不会。这个应用程序仍然可以找到 XML 文档中的 `<to>`、`<from>` 以及 `<body>`元素，并产生同样的输出。

XML 的优势之一，就是可以在不中断应用程序的情况下进行扩展。

## XML 元素 vs. 属性

请看这些实例：

```xml
<person sex="female">  
<firstname>Anna</firstname>  
<lastname>Smith</lastname>  
</person>
```

```xml
<person>  
<sex>female</sex>  
<firstname>Anna</firstname>  
<lastname>Smith</lastname>  
</person>
```

在第一个实例中，sex 是一个属性。在第二个实例中，sex 是一个元素。这两个实例都提供相同的信息。

没有什么规矩可以告诉我们什么时候该使用属性，而什么时候该使用元素。我的经验是在 HTML 中，属性用起来很便利，但是在 XML 中，您应该尽量避免使用属性。如果信息感觉起来很像数据，那么请使用元素吧。

## XML 验证

拥有正确语法的 XML 被称为"形式良好"的 XML。
通过 DTD 验证的XML是"合法"的 XML。

## 跨域访问

出于安全方面的原因，现代的浏览器不允许跨域的访问。
这意味着，网页以及它试图加载的 XML 文件，都必须位于相同的服务器上。


## XMLHttpRequest 对象

XMLHttpRequest 对象用于在后台与服务器交换数据。
XMLHttpRequest 对象是**开发者的梦想**，因为您能够：
-   在不重新加载页面的情况下更新网页
-   在页面已加载后从服务器请求数据
-   在页面已加载后从服务器接收数据
-   在后台向服务器发送数据

## XML 命名空间 - xmlns 属性

当在 XML 中使用前缀时，一个所谓的用于前缀的**命名空间**必须被定义。
命名空间是在元素的开始标签的 **xmlns 属性**中定义的。
命名空间声明的语法如下。xmlns:_前缀_="_URI_"。

## XML CDATA

XML 文档中的所有文本均会被解析器解析。
只有 CDATA 区段中的文本会被解析器忽略。

-----------
## XML 相关技术
下面是一个 XML 技术的列表。

[XHTML (可扩展 HTML)](https://www.runoob.com/html/html-xhtml.html)  
更严格更纯净的基于 XML 的 HTML 版本。

[XML DOM (XML 文档对象模型)](https://www.runoob.com/dom/dom-tutorial.html)  
访问和操作 XML 的标准文档模型。

[XSL (可扩展样式表语言)](https://www.runoob.com/xsl/xsl-tutorial.html) XSL 包含三个部分：

-   [XSLT](https://www.runoob.com/xsl/xsl-tutorial.html) (XSL 转换) - 把 XML 转换为其他格式，比如 HTML
-   [XSL-FO](https://www.runoob.com/xslfo/xslfo-tutorial.html) (XSL 格式化对象)- 用于格式化 XML 文档的语言
-   [XPath](https://www.runoob.com/xpath/xpath-tutorial.html) - 用于导航 XML 文档的语言

[XQuery (XML 查询语言)](https://www.runoob.com/xquery/xquery-tutorial.html)  
基于 XML 的用于查询 XML 数据的语言。

[DTD (文档类型定义)](https://www.runoob.com/dtd/dtd-tutorial.html)  
用于定义 XML 文档中的合法元素的标准。

[XSD (XML 架构)](https://www.runoob.com/schema/schema-tutorial.html)  
基于 XML 的 DTD 替代物。

[XLink (XML 链接语言)](https://www.runoob.com/xlink/xlink-tutorial.html)  
在 XML 文档中创建超级链接的语言。

[XPointer (XML 指针语言)](https://www.runoob.com/xlink/xlink-tutorial.html)  
允许 XLink 超级链接指向 XML 文档中更多具体的部分。

[SOAP (简单对象访问协议)](https://www.runoob.com/soap/soap-tutorial.html)  
允许应用程序在 HTTP 之上交换信息的基于 XML 的协议。

[WSDL (Web 服务描述语言)](https://www.runoob.com/wsdl/wsdl-tutorial.html)  
用于描述网络服务的基于 XML 的语言。

[RDF (资源描述框架)](https://www.runoob.com/rdf/rdf-tutorial.html)  
用于描述网络资源的基于 XML 的语言。

[RSS (真正简易聚合)](https://www.runoob.com/rss/rss-tutorial.html)  
聚合新闻以及类新闻站点内容的格式。

[SVG (可伸缩矢量图形)](https://www.runoob.com/svg/svg-tutorial.html)  
定义 XML 格式的图形。

-------------

# Web Services 
https://www.runoob.com/webservices/ws-intro.html

Web Services 可使您的应用程序成为 Web 应用程序。
Web Services 通过 Web 进行发布、查找和使用。

## 什么是Web Services？

-   Web Services 是应用程序组件
-   Web Services 使用开放协议进行通信
-   Web Services 是独立的（self-contained）并可自我描述
-   Web Services 可通过使用UDDI来发现
-   Web Services 可被其他应用程序使用
-   XML 是 Web Services 的基础

## 它如何工作？

基础的 Web Services 平台是 XML + HTTP。
HTTP 协议是最常用的因特网协议。
XML 提供了一种可用于不同的平台和编程语言之间的语言。

### Web services 平台的元素：

-   SOAP (简易对象访问协议)
-   UDDI (通用描述、发现及整合)
-   WSDL (Web services 描述语言)

### 为什么使用 Web Services?
#### 最重要的事情是协同工作

由于所有主要的平台均可通过 Web 浏览器来访问 Web，不同的平台可以借此进行交互。为了让这些平台协同工作，Web 应用程序被开发了出来。

Web 应用程序是运行在 Web 上的简易应用程序。它们围绕 Web 浏览器标准被进行构建，几乎可被任何平台之上的任何浏览器来使用。

#### Web services 把 Web 应用程序提升到了另外一个层面

通过使用 Web services，您的应用程序可向全世界发布功能或消息。
Web services 使用 XML 来编解码数据，并使用 SOAP 借由开==放的协议来传输数据==。
通过 Web services，您的会计部门的 Win 2k 服务器可与 IT 供应商的 UNIX 服务器进行连接。

#### Web services 有两种类型的应用

##### 可重复使用的应用程序组件
有一些功能是不同的应用程序常常会用到的。那么为什么要周而复始地开发它们呢？
Web services 可以把应用程序组件作为服务来提供，比如汇率转换、天气预报或者甚至是语言翻译等等。
比较理想的情况是，每种应用程序组件==只有一个最优秀的版本==，这样任何人都可以在其应用程序中使用它。

##### 连接现有的软件
通过为不同的应用程序提供一种==链接其数据==的途径，Web services有助于解决==协同工作==的问题。
通过使用 Web services，您可以在不同的应用程序与平台之间来交换数据。

---
#程序员 
# SOAP
## 什么是 SOAP？
**Simple Object Access Protocol**
-   SOAP 指_简易对象访问协议_
-   SOAP 是一种_通信协议_
-   SOAP 用于_应用程序之间_的通信
-   SOAP 是一种用于_发送消息_的格式
-   SOAP 被设计用来_通过因特网_进行通信
-   SOAP _独立于平台_
-   SOAP _独立于语言_
-   SOAP _基于 XML_
-   SOAP _很简单并可扩展_
-   SOAP 允许您_绕过防火墙_
-   SOAP 将被作为 _W3C 标准_来发展


## 为什么使用 SOAP？

对于应用程序开发来说，使程序之间进行因特网通信是很重要的。

目前的应用程序通过使用远程过程调用（RPC）在诸如 DCOM 与 CORBA 等对象之间进行通信，但是 HTTP 不是为此设计的。RPC 会产生兼容性以及安全问题；防火墙和代理服务器通常会阻止此类流量。

通过 HTTP 在应用程序间通信是更好的方法，因为 HTTP 得到了所有的因特网浏览器及服务器的支持。SOAP 就是被创造出来完成这个任务的。

SOAP 提供了一种标准的方法，使得运行在不同的操作系统并使用不同的技术和编程语言的应用程序可以互相进行通信。


## SOAP 语法规则

这里是一些重要的语法规则：
-   SOAP 消息必须用 XML 来编码
-   SOAP 消息必须使用 SOAP Envelope 命名空间
-   SOAP 消息必须使用 SOAP Encoding 命名空间
-   SOAP 消息不能包含 DTD 引用
-   SOAP 消息不能包含 XML 处理指令


## SOAP 消息的基本结构

```xml
<?xml version="1.0"?>  
<soap:Envelope  
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"  
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">  
  
<soap:Header>  
...  
</soap:Header>  
  
<soap:Body>  
...  
  <soap:Fault>  
  ...  
  </soap:Fault>  
</soap:Body>  
  
</soap:Envelope>
```

## SOAP Header 元素

可选的 SOAP Header 元素可包含有关 SOAP 消息的应用程序专用信息（比如认证、支付等）。
如果 Header 元素被提供，则它必须是 Envelope 元素的第一个子元素。
**注意：** 所有 Header 元素的直接子元素**必须是**合格的命名空间

- mustUnderstand 属性
- actor 属性

## SOAP Body 元素
必需的 SOAP Body 元素可包含打算传送到消息最终端点的实际 SOAP 消息。
SOAP Body 元素的直接子元素**可以是**合格的命名空间，也可以是自定义的元素。

```xml
<?xml version="1.0"?>  
<soap:Envelope  
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"  
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">  
  
<soap:Body>  
  <m:GetPrice xmlns:m="http://www.w3schools.com/prices">  
    <m:Item>Apples</m:Item>  
  </m:GetPrice>  
</soap:Body>  
  
</soap:Envelope>
```

上面的例子请求苹果的价格。请注意，上面的 m:GetPrice 和 Item 元素是应用程序专用的元素。它们并不是 SOAP 标准的一部分。

而一个 SOAP 响应应该类似这样：

```xml
<?xml version="1.0"?>  
<soap:Envelope  
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"  
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">  
  
<soap:Body>  
  <m:GetPriceResponse xmlns:m="http://www.w3schools.com/prices">  
    <m:Price>1.90</m:Price>  
  </m:GetPriceResponse>  
</soap:Body>  
  
</soap:Envelope>
```

## SOAP Fault 元素

可选的 SOAP Fault 元素用于指示错误消息。
如果已提供了 Fault 元素，则它必须是 Body 元素的子元素。在一条 SOAP 消息中，Fault 元素只能出现一次。

## SOAP HTTP Binding

SOAP 方法指的是遵守 SOAP 编码规则的 HTTP 请求/响应。
`HTTP + XML = SOAP`
SOAP 请求可能是 HTTP POST 或 HTTP GET 请求。
HTTP POST 请求规定至少两个 HTTP 头：Content-Type 和 Content-Length。

## 一个 SOAP 实例

在下面的例子中，一个 GetStockPrice 请求被发送到了服务器。此请求有一个 StockName 参数，而在响应中则会返回一个 Price 参数。此功能的命名空间被定义在此地址中： "http://www.example.org/stock"

### SOAP 请求：
```xml
POST /InStock HTTP/1.1  
Host: www.example.org  
Content-Type: application/soap+xml; charset=utf-8  
Content-Length: nnn  
  
<?xml version="1.0"?>  
<soap:Envelope  
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"  
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">  
  
<soap:Body xmlns:m="http://www.example.org/stock">  
  <m:GetStockPrice>  
    <m:StockName>IBM</m:StockName>  
  </m:GetStockPrice>  
</soap:Body>  
  
</soap:Envelope>
```

### SOAP 响应：

```xml
HTTP/1.1 200 OK  
Content-Type: application/soap+xml; charset=utf-8  
Content-Length: nnn  
  
<?xml version="1.0"?>  
<soap:Envelope  
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"  
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">  
  
<soap:Body xmlns:m="http://www.example.org/stock">  
  <m:GetStockPriceResponse>  
    <m:Price>34.5</m:Price>  
  </m:GetStockPriceResponse>  
</soap:Body>  
  
</soap:Envelope>
```

此教程已向您讲解了如何透过 HTTP 使用 SOAP 在应用程序之间交换信息。
您已经学习了有关 SOAP 消息中不同元素和属性的知识。
您也学习了如何把 SOAP 作为一种协议来使用以访问 web service。


# WSDL
https://www.w3.org/TR/wsdl.html
## 什么是 WSDL?
WSDL 指网络服务描述语言 (Web Services Description Language)。
WSDL 是一种使用 XML 编写的文档。这种文档可描述某个 Web service。它可规定服务的位置，以及此服务提供的操作（或方法）。

-   WSDL 指网络服务描述语言
-   WSDL 使用 XML 编写
-   WSDL 是一种 XML 文档
-   WSDL 用于描述网络服务
-   WSDL 也可用于定位网络服务
-   WSDL 还不是 W3C 标准

## WSDL 文档实例

```xml
<definitions>
    <types> data type definitions, ws使用的数据类型 </types>
    <message> definition of the data being communicated, ws使用的消息 </message>
    <portType> set of operations, ws执行的操作 </portType>
    <binding> protocol and data format specification, ws使用的通信协议 </binding>
</definitions>
```

WSDL 文档可包含其它的元素，比如 extension 元素，以及一个 service 元素，此元素可把若干个 web services 的定义组合在一个单一的 WSDL 文档中。

## WSDL 端口

`<portType>`元素是==最重要==的 WSDL 元素。
它可描述一个 web service、可被执行的操作，以及相关的消息。
可以把` <portType>` 元素比作传统编程语言中的一个函数库（或一个模块、或一个类）。

- One-way, 此操作可接受消息，但不会返回响应。
- Request-response, 此操作可接受一个请求并会返回一个响应。
- Solicit-response, 此操作可发送一个请求，并会等待一个响应。
- Notification, 此操作可发送一条消息，但不会等待响应。

One-way case:
```xml
<message name="newTermValues">
    <part name="term" type="xs:string"/>
    <part name="value" type="xs:string"/>
</message>

<portType name="glossaryTerms">
    <operation name="setTerm">
        <input name="newTerm" message="newTermValues"/>
    </operation>
</portType>
```


## WSDL 消息

`<message>` 元素定义一个操作的数据元素。
每个消息均由一个或多个部件组成。可以把这些部件比作传统编程语言中一个函数调用的参数。

## WSDL types

`<types>` 元素定义 web service 使用的数据类型。
为了最大程度的平台中立性，WSDL 使用 XML Schema 语法来定义数据类型。

## WSDL Bindings

`<binding>` 元素为每个端口定义消息格式和协议细节。

绑定到 SOAP，一个“请求 - 响应”操作的例子：
```xml
<message name="getTermRequest">
    <part name="term" type="xs:string"/>
</message>

<message name="getTermResponse">
    <part name="value" type="xs:string"/>
</message>

<portType name="glossaryTerms">
    <operation name="getTerm">
        <input message="getTermRequest"/>
        <output message="getTermResponse"/>
    </operation>
</portType>

<binding type="glossaryTerms" name="b1">
    <soap:binding style="document" 
        transport="http://schemas.xmlsoap.org/soap/http" />
    <operation>
        <soap:operation soapAction="http://example.com/getTerm"/>
        <input>
            <soap:body use="literal"/>
        </input>
        <output>
            <soap:body use="literal"/>
        </output>
    </operation>
</binding>
```

_binding_ 元素有两个属性 - name 属性和 type 属性。

name 属性定义 binding 的名称，而 type 属性指向用于 binding 的端口，在这个例子中是 "glossaryTerms" 端口。

_soap:binding_ 元素有两个属性 - style 属性和 transport 属性。

style 属性可取值 "rpc" 或 "document"。在这个例子中我们使用 document。transport 属性定义了要使用的 SOAP 协议。在这个例子中我们使用 HTTP。

_operation_ 元素定义了每个端口提供的操作符。

对于每个操作，相应的 SOAP 行为都需要被定义。同时您必须如何对输入和输出进行编码。在这个例子中我们使用了 "literal"。


## WSDL 实例

这是某个 WSDL 文档的简化的片段：

## 实例

```xml
<message name="getTermRequest">
    <part name="term" type="xs:string"/>
</message>

<message name="getTermResponse">
    <part name="value" type="xs:string"/>
</message>

<portType name="glossaryTerms">
    <operation name="getTerm">
        <input message="getTermRequest"/>
        <output message="getTermResponse"/>
    </operation>
</portType>
```

在这个例子中，`<portType>` 元素把 "glossaryTerms" 定义为某个_端口_的名称，把 "getTerm" 定义为某个_操作_的名称。

操作 "getTerm" 拥有一个名为 "getTermRequest" 的_输入消息_，以及一个名为 "getTermResponse" 的_输出消息_。

`<message>` 元素可定义每个消息的部件，以及相关联的数据类型。

对比传统的编程，glossaryTerms 是一个==函数库==，而 "getTerm" 是带有输入参数 "getTermRequest" 和返回参数 getTermResponse 的==一个函数==。

## WSDL UDDI

UDDI 是一种==目录服务==，企业可以使用它对 Web services 进行==注册和搜索==。  
UDDI，英文为 "Universal Description, Discovery and Integration"，可译为"通用描述、发现与集成服务"。

它是一个基于 XML 的跨平台的描述规范，可以使世界范围内的企业在互联网上发布自己所提供的服务。
-   UDDI 指的是通用==描述、发现与集成==服务
-   UDDI 是一种用于存储有关 web services 的信息的目录。
-   UDDI 是一种由 WSDL 描述的 web services 界面的目录。
-   UDDI 经由 SOAP 进行通信
-   ==UDDI 被构建入了微软的 .NET 平台==。

### UDDI 基于什么？

UDDI 使用 W3C 和 IETF* 的因特网标准，比如 XML、HTTP 和 DNS 协议。
UDDI 使用 WSDL 来描述到达 web services 的界面。
此外，通过采用 SOAP，还可以实现跨平台的编程特性，大家知道，SOAP 是 XML 的协议通信规范，可在 W3C 的网站找到相关的信息。

*注释：IETF - Internet Engineering Task Force

# BPEL
![Mapping BPEL to UDDI](https://www.oasis-open.org/committees/uddi-spec/doc/tn/uddi-spec-tc-tn-bpel-20040725_files/image003.jpg)