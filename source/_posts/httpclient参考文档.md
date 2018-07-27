---
title: httpclient参考文档
date: 2018-07-27 14:13:33
categories: httpclient
tags: httpclient
---

<b style="font-size:18px">（注：该文档基于httpclient4.0+版本）</b>
# 基本知识

## Request execution
<b style="font-size:16px">HttpClient最重要的功能是执行HTTP方法。执行HTTP方法涉及一个或多个HTTP请求/ HTTP响应交换，通常由HttpClient内部处理。用户需要提供一个request对象，Httpclient发送该对象到目标服务器，并返回一个相应的reponse对象，如果不成功则跑出异常</b>
<br>
<b style="font-size:16px">HttpClient API的主要入口点是HttpClient接口,它实现了上述的规定</b>
<br>
<b style="font-size:16px">下面是最简的请求执行过程示例：</b>

````java
CloseableHttpClient httpclient = HttpClients.createDefault();
HttpGet httpget = new HttpGet("http://localhost/");
CloseableHttpResponse response = httpclient.execute(httpget);
try {
<...>
} finally {
response.close();
}
````

##  HTTP request
<b style="font-size:16px">所有HTTP请求都有一个请求行，包括方法名称，请求URI和HTTP协议版本。</b>
<br>
<b style="font-size:16px">HttpClient supports out of the box all HTTP methods defined in the HTTP/1.1 specification:  GET ,HEAD ,  POST ,  PUT ,  DELETE ,  TRACE and  OPTIONS . There is a specific class for each method type.:  HttpGet ,HttpHead ,  HttpPost ,  HttpPut ,  HttpDelete ,  HttpTrace , and  HttpOptions .</b>
<br>
<b style="font-size:16px">Request-URI是统一资源标识符，用于标识应用请求的资源。HTTP请求URI由协议方案，主机名，可选端口，资源路径，可选查询和可选片段组成。</b>

````java
HttpGet httpget = new HttpGet("http://www.google.com/search?hl=en&q=httpclient&btnG=Google+Search&aq=f&oq=");
````

<b style="font-size:16px">HttpClient提供了URIBuilder实体类，以简化请求URI的创建和修改。</b>

````java
URI uri = new URIBuilder()
.setScheme("http")
.setHost("www.google.com")
.setPath("/search")
.setParameter("q", "httpclient")
.setParameter("btnG", "Google Search")
.setParameter("aq", "f")
.setParameter("oq", "")
.build();
HttpGet httpget = new HttpGet(uri);
System.out.println(httpget.getURI());
````

<b style="font-size:16px">输出<b>

````
http://www.google.com/search?q=httpclient&btnG=Google+Search&aq=f&oq=
````


## HTTP response

<b style="font-size:16px">HTTP响应是服务器在接收并解释请求消息后发送回客户端的消息。 该消息的第一行包括协议版本，后跟数字状态代码及其相关的文本短语。</b>

````java
HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,
HttpStatus.SC_OK, "OK");
System.out.println(response.getProtocolVersion());
System.out.println(response.getStatusLine().getStatusCode());
System.out.println(response.getStatusLine().getReasonPhrase());
System.out.println(response.getStatusLine().toString());
````


<b style="font-size:16px">输出</b>
````
HTTP/1.1
200
OK
HTTP/1.1 200 OK
````


## 使用response首部

<b style="font-size:16px">HTTP消息可以包含许多描述消息属性的标题，例如内容长度，内容类型等。 HttpClient提供了检索，添加，删除和枚举首部的方法。</b>

````java
HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,
HttpStatus.SC_OK, "OK");
response.addHeader("Set-Cookie",
"c1=a; path=/; domain=localhost");
response.addHeader("Set-Cookie",
"c2=b; path=\"/\", c3=c; domain=\"localhost\"");
Header h1 = response.getFirstHeader("Set-Cookie");
System.out.println(h1);
Header h2 = response.getLastHeader("Set-Cookie");
System.out.println(h2);
Header[] hs = response.getHeaders("Set-Cookie");
System.out.println(hs.length);
````
<b style="font-size:16px">输出</b>

````
Set-Cookie: c1=a; path=/; domain=localhost
Set-Cookie: c2=b; path="/", c3=c; domain="localhost"
2
````

<b style="font-size:16px">获取给定类型的所有首部的最有效方法是使用HeaderIterator接口。</b>

````java
HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,
HttpStatus.SC_OK, "OK");
response.addHeader("Set-Cookie",
"c1=a; path=/; domain=localhost");
response.addHeader("Set-Cookie",
"c2=b; path=\"/\", c3=c; domain=\"localhost\"");
HeaderIterator it = response.headerIterator("Set-Cookie");
while (it.hasNext()) {
System.out.println(it.next());
}
````
<b style="font-size:16px">输出</b>

````
Set-Cookie: c1=a; path=/; domain=localhost
Set-Cookie: c2=b; path="/", c3=c; domain="localhost"
````

<b style="font-size:16px">提供了将HTTP消息解析为单个头元素的便捷方法。</b>

````java
HttpResponse response = new BasicHttpResponse(HttpVersion.HTTP_1_1,
HttpStatus.SC_OK, "OK");
response.addHeader("Set-Cookie",
"c1=a; path=/; domain=localhost");
response.addHeader("Set-Cookie",
"c2=b; path=\"/\", c3=c; domain=\"localhost\"");
HeaderElementIterator it = new BasicHeaderElementIterator(
response.headerIterator("Set-Cookie"));
while (it.hasNext()) {
HeaderElement elem = it.nextElement();
System.out.println(elem.getName() + " = " + elem.getValue());
NameValuePair[] params = elem.getParameters();
for (int i = 0; i < params.length; i++) {
System.out.println(" " + params[i]);
}
}
````

<b style="font-size:16px">输出</b>

````
c1 = a
path=/
domain=localhost
c2 = b
path=/
c3 = c
domain=localhost
````


## HTTP entity

<b style="font-size:16px">HTTP消息可以携带与请求或响应相关联的内容实体。 实体可以在请求和响应中找到。 使用实体的请求称为包含请求的实体。 HTTP规范定义了两个请求方法的实体：POST和PUT。 通常reponse包含内容实体。 此规则有例外，例如对HEAD方法的响应和204 No Content，304 Not Modified，205 Reset Content响应。</b>
<br>
<b style="font-size:16px">HttpClient区分三种实体，具体取决于其内容的来源：</b>
<br>
<b style="font-size:16px"><li> 流式传输：内容从流中接收，或在运行中生成。 特别地，该类别包括从HTTP响应接收的实体。 流实体通常不可重复。</li></b>
<br>
<b style="font-size:16px"><li>自包含：内容在内存中或者通过独立的连接或者或其他实体的方式获得。自包含实体通常是可重复的。 这种类型的实体主要用于封装HTTP请求的实体。</li></b>
<br>
<b style="font-size:16px"><li> 包装：内容从另一个实体获得。</li></b>
<br>

<b style="font-size:16px">当从HTTP响应流获得内容时，这种区别对于连接管理很重要。对于由应用程序创建且仅使用HttpClient发送的请求实体，流式和自包含之间的差异并不重要。在这种情况下，建议将不可重复的实体视为流式传输，将那些可重复的实体视为自包含的。在这种情况下，建议将不可重复的实体视为流式传输，将那些可重复的实体视为自包含的。</b>

### Repeatable entities

<b style="font-size:16px">实体可以是可重复的，这意味着其内容可以被多次读取。 这是唯一可行的自包含实体（如ByteArrayEntity或StringEntity）</b>

### 使用 HTTP entities

<b style="font-size:16px">由于实体可以表示二进制和字符内容，因此它支持字符编码（支持后者，即字符内容）。</b>
<br>
<b style="font-size:16px">实体在执行带有附加内容的请求时或者请求成功并且使用响应主体将结果发送回客户端时创建。</b>
<br>
<b style="font-size:16px">要从实体读取内容，可以通过HttpEntity＃getContent（）方法检索输入流，该方法返回java.io.InputStream，或者可以向HttpEntity＃writeTo（OutputStream）方法提供输出流， 一旦所有内容都写入给定流，它将返回。</b>
<b>
<b style="font-size:16px">当接收到具有传入消息的实体时，方法HttpEntity＃getContentType（）和HttpEntity＃getContentLength（）方法可用于读取公共元数据，例如Content-Type和Content-Length头（如果它们可用）。 由于Content-Type首部可以包含文本mime类型（如text / plain或text / html）的字符编码，因此HttpEntity＃getContentEncoding（）方法用于读取此信息。 如果首部不可用，则返回长度-1，内容类型为NULL。 如果Content-Type首部可用，则将返回Header对象。</b>

````java
StringEntity myEntity = new StringEntity("important message",
ContentType.create("text/plain", "UTF-8"));
System.out.println(myEntity.getContentType());
System.out.println(myEntity.getContentLength());
System.out.println(EntityUtils.toString(myEntity));
System.out.println(EntityUtils.toByteArray(myEntity).length);
````

<b style="font-size:16px">输出</b>

````
Content-Type: text/plain; charset=utf-8
17
important message
17
````

## 释放资源

<b style="font-size:16px">为了确保正确释放系统资源，必须关闭与实体关联的内容流或响应本身</b>
````java
CloseableHttpClient httpclient = HttpClients.createDefault();
HttpGet httpget = new HttpGet("http://localhost/");
CloseableHttpResponse response = httpclient.execute(httpget);
try {
HttpEntity entity = response.getEntity();
if (entity != null) {
InputStream instream = entity.getContent();
try {
// do something useful
} finally {
instream.close();
}
}
} finally {
response.close();
}
````

