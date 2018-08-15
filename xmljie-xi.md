XML解析技术是JDK提供的基础技术之一，名叫JAXP，其JSR为JSR005，规范中规定了SAX和DOM两种XML解析方式。大量的Java项目配置文件的解析都使用到XML解析，不光JDK实现了JSR005标准，dom4j和jdom也可以解析XML，并提供了兼容转换JAXP的接口，但并不严格遵循JSR005标准。日常项目开发中我们一般更多的使用dom4j解析xml，但也有例外，为了尽量减少包依赖而使用JDK提供的JAXP解析，spring就是如此。本文也仅介绍JAXP解析方式。

注：严格说来，JAXP 是 API，更准确地说是抽象层。原来JDK的自带的解析器是SUN公司自己实现的，在1.5以后替换成了Apache Xerces解析器，当然，为了适应JDK对其目录之类的做了一些改动，就成了现在这个样子。

### DOM解析方式

DOM方式是一次性将整份XML导入内存，转换成DOM树，因此转换速度慢；但因为常驻内存，所以重复访问效率高，并且可修改节点内容。JAXP的DOM通过DocumentBuilderFactory工厂类来获取。

使用案例：

```
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();  
DocumentBuilder builder = factory.newDocumentBuilder();  
Document document = builder.parse("my.xml"); //w3c标准document
NodeList list = document.getElementsByTagName("name");
```

### SAX解析方式

SAX方式是顺序解析，无需一次性导入，转换速度快；不保持以访问过的数据，所以重复访问效率低（需要重新解析XML），且无法修改节点内容。JAXP的SAX通过SAXParserFactory工厂类来获取，基于事件驱动的方式读取节点。

```
SAXParserFactory factory = SAXParserFactory.newInstance();
SAXParser saxParser = factory.newSAXParser();
saxParser.parse("my.xml", new MyDefaultHandler());


public class MyHandler extends DefaultHandler{

    @Override
    public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
        // TODO Auto-generated method stub
        super.startElement(uri, localName, qName, attributes);
    }
    @Override
    public void characters(char[] ch, int start, int length) throws SAXException {
        // TODO Auto-generated method stub
        super.characters(ch, start, length);
    }
    @Override
    public void endElement(String uri, String localName, String qName) throws SAXException {
        // TODO Auto-generated method stub
        super.endElement(uri, localName, qName);
    }

}
```

### 使用xpath解析

```
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();  
DocumentBuilder builder = factory.newDocumentBuilder();  
Document document = builder.parse("my.xml"); //w3c标准document

XPathFactory factory = XPathFactory.newInstance();
XPath xpath = factory.newXPath();
XPathExpression expr = xpath.compile("//book[author='Neal Stephenson']/title/text()");
Object result = expr.evaluate(doc, XPathConstants.NODESET);
NodeList nodes = (NodeList) result;
for (int i = 0; i &lt; nodes.getLength(); i++) { 
    System.out.println(nodes.item(i).getNodeValue());
}
```



