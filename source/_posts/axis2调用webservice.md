---
title: axis2调用webservice
date: 2018-12-11 09:36:33
description: axis2调用webservice
tags:
  - axis2
  - WebService
  - java
---
<!-- TOC -->

- [WebService:一种跨编程语言和跨操作系统平台的远程调用技术.](#webservice一种跨编程语言和跨操作系统平台的远程调用技术)
- [WebService的实现包有很多,Java语言有jdk1.6后内置的jws.\*,Apache cxf,Apache Axis,Apache Axis2.](#webservice的实现包有很多java语言有jdk16后内置的jwsapache-cxfapache-axisapache-axis2)
  - [1.远程调用](#1远程调用)
    - [1.1 axis2+wsdl地址方式调用:](#11-axis2wsdl地址方式调用)
    - [1.2 axis2+endpoint地址方式调用:](#12-axis2endpoint地址方式调用)
  - [2.wsdl2java方式调用](#2wsdl2java方式调用)

<!-- /TOC -->
### WebService:一种跨编程语言和跨操作系统平台的远程调用技术.

    WebService都是基于http请求(POST请求,无GET请求)
    XML+XSD,SOAP和WSDL就是构成WebService平台的三大技术。
    XML+XSD：

    WebService采用HTTP协议传输数据，采用XML格式封装数据（即XML中说明调用远程服务对象的哪个方法，传递的参数是什么，
    以及服务对象的返回结果是什么）。XML是WebService平台中表示数据的格式。

    SOAP(Simple Object Access Protocol)：简单对象访问协议
    
    SOAP提供了标准的RPC方法来调用Web Service。SOAP协议 = HTTP协议 + XML数据格式SOAP协议定义了SOAP消息的格式，
    SOAP协议是基于HTTP协议的，SOAP也是基于XML和XSD的，XML是SOAP的数据编码方式。
    soap协议分1.1和1.2版本:
    soap1.1:
      请求方式:post
      content-type:text/xml;charset=utf-8
    soap1.2:
      请求方式:post
      content-type:application/soap+xml

    WSDL(Web Services Description Language):基于XML的语言，用于描述Web Service及其函数、参数和返回值。
    
    它是WebService客户端和服务器端都能理解的标准格式。WSDL文件保存在Web服务器上，通过一个url地址就可以访问到它。
    客户端要调用一个WebService服务之前，要知道该服务的WSDL文件的地址。

    注:wsdl文件阅读技巧:(从下往上读)
      找到wsdl:service节点->查看下面的wsdl:port节点->查看其binding属性->找到与bingding名称相同的wsdl:binding节点
      ->查看节点下的wsdl:operation节点(对应的调用method名称)->wsdl:input方法输入参数节点->对应message属性值
      ->找到同名的wsdl:message节点->wsdl:part对应的参数名称和类型.

      wsdl:output节点代表服务返回的数据.阅读方式和wsdl:input一致

WebService优点:跨平台跨语言.服务方和调用方不用关心各自的平台和语言

WebService缺点:在使用方式上，RPC和soap的使用在减少，Restful架构占到了主导地位。在数据格式上，XML格式繁琐,使用在减少，json等轻量级格式的使用在增多..另外性能上略低(也有专门对xml解析优化的cpu)

### WebService的实现包有很多,Java语言有jdk1.6后内置的jws.*,Apache cxf,Apache Axis,Apache Axis2.

本文采用axis2包,发布webservice服务可以采用jdk的@WebService注解实现或者cxf/axis/axis2来进行实现,网上教程很多,本文不涉及到这块.主要是发现在调用的时候会有一些坑,所以在此记录

WebService客户端调用主要有2类方式,一种是通过http远程调用,一种是通过wsdl2java产生Java代码调用

#### 1.远程调用

  引入axisjar包:
  ```xml
      <!-- Axis2 -->
        <dependency>
            <groupId>org.apache.axis2</groupId>
            <artifactId>axis2</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.ws.commons.axiom</groupId>
            <artifactId>axiom</artifactId>
            <version>1.2.20</version>
            <type>pom</type>
        </dependency>
        <dependency>
            <groupId>org.apache.ws.commons.axiom</groupId>
            <artifactId>axiom-api</artifactId>
            <version>1.2.20</version>
        </dependency>
        <dependency>
            <groupId>org.apache.ws.commons.axiom</groupId>
            <artifactId>axiom-impl</artifactId>
            <version>1.2.20</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.ws.xmlschema</groupId>
            <artifactId>xmlschema-core</artifactId>
            <version>2.2.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.neethi</groupId>
            <artifactId>neethi</artifactId>
            <!--<version>2.0.4</version> -->
            <version>3.1.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.axis2</groupId>
            <artifactId>axis2-transport-local</artifactId>
            <version>1.7.7</version>
        </dependency>
        <dependency>
            <groupId>org.apache.axis2</groupId>
            <artifactId>axis2-transport-http</artifactId>
            <version>1.7.7</version>
        </dependency>
        <dependency>
            <groupId>javax.mail</groupId>
            <artifactId>javax.mail-api</artifactId>
            <version>1.6.2</version>
        </dependency>
      <!-- Axis2 End -->
  ```

##### 1.1 axis2+wsdl地址方式调用:

```java
  String soapBindingAddress = "http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx?wsdl";
        EndpointReference endpointReference = new EndpointReference(soapBindingAddress);
        //创建一个OMFactory
        OMFactory factory = OMAbstractFactory.getOMFactory();
        //指定命名空间
        OMNamespace namespace = factory.createOMNamespace("http://WebXml.com.cn/", "web");
        //创建method对象，方法名 为getMobileCodeInfo
        OMElement method = factory.createOMElement("getMobileCodeInfo", namespace);
        OMElement mobileCode = factory.createOMElement("mobileCode", namespace);//方法参数
        OMElement userID = factory.createOMElement("userID", namespace);//方法参数
        //封装参数
        mobileCode.addChild(factory.createOMText(mobileCode, "18265963256"));//设定参数的值
        method.addChild(mobileCode);
        userID.addChild(factory.createOMText(userID, ""));//设定参数的值
        method.addChild(userID);
        //请求参数设置
        ServiceClient sender = new ServiceClient();
        Options options = new Options();
        // 设置soap1.2协议
        options.setSoapVersionURI(SOAP12Constants.SOAP_ENVELOPE_NAMESPACE_URI);
        options.setAction("http://WebXml.com.cn/getMobileCodeInfo");
        options.setTo(endpointReference);
        sender.setOptions(options);
        OMElement result = sender.sendReceive(method);
        System.out.println(result.getFirstElement().getText());
```

##### 1.2 axis2+endpoint地址方式调用:

```java
  ServiceClient client = new ServiceClient();
        // 创建服务地址WebService的URL,注意不是WSDL的URL
        String url = "http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx";
        EndpointReference targetEPR = new EndpointReference(url);
        Options options = client.getOptions();
        // 设置soap1.2协议
        options.setSoapVersionURI(SOAP12Constants.SOAP_ENVELOPE_NAMESPACE_URI);
        options.setTo(targetEPR);
        // 确定调用方法（wsdl 命名空间地址 (wsdl文档中的targetNamespace) 和 方法名称 的组合）
        options.setAction("http://WebXml.com.cn/getMobileCodeInfo");
        OMFactory fac = OMAbstractFactory.getOMFactory();
        /*
         * 指定命名空间，参数： uri--即为wsdl文档的targetNamespace，命名空间 perfix--可不填
         */
        OMNamespace omNs = fac.createOMNamespace("http://WebXml.com.cn/", "");
        /*
         * 身份验证
         * OMElement header = fac.createOMElement("AuthenticationToken", omNs);
         * OMElement ome_user = fac.createOMElement("Username", omNs); OMElement
         * ome_pass = fac.createOMElement("Password", omNs);
         *
         * ome_user.setText("user"); ome_pass.setText("pass");
         *
         * header.addChild(ome_user); header.addChild(ome_pass);
         *
         * client.addHeader(header);
         */
        // 指定方法
        OMElement method = fac.createOMElement("getMobileCodeInfo", omNs);
        // 指定方法的参数
        OMElement mobileCode = fac.createOMElement("mobileCode", omNs);
        mobileCode.setText("18265963256");
        OMElement userID = fac.createOMElement("userID", omNs);
        userID.setText("");
        method.addChild(mobileCode);
        method.addChild(userID);
        method.build();
        // 远程调用web服务
        OMElement result = client.sendReceive(method);
        System.out.println(result);
```

    注:返回的xml数据可能含有需要转义的字符,比如&lt;之类的.
    可以用org.apache.commons.lang3.StringEscapeUtils.unescapeXml方法处理,(已过时),
    可以用commons-text包下的StringEscapeUtils类进行反转义处理

#### 2.wsdl2java方式调用

  使用jdk命令行下的wsimport命令和wsdl文件生成生成客户端中间代码,
  
  注意:只能编译soap1.1的协议，不能编译soap1.2的协议的代码

    wsimport [options] <WSDL_URI>
    比较常用的[options]有：
    1. -d <directory>
      在指定的目录生成class文件
    2. -clientjar <jarfile>
      在当前目录生成jar文件，结合-d <directory>可以在指定的目录生成jar文件
    3. -s <directory>
      在指定的目录生成java源文件
    4. -p <pkg>
      指定生成文件的包结构
    5. -keep
      在生成class文件，或者jar包时，同时保留java源文件

例:在当前目录新建class文件夹和java文件夹,分别存放对应的class文件个java文件

    wsimport -keep -d ./class -s ./java -p com.test.demo -verbose http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx?wsdl  

```java
  //创建一个MobileCodeWS工厂  
  MobileCodeWS factory = new MobileCodeWS();  
  //根据工厂创建一个MobileCodeWSSoap对象  
  MobileCodeWSSoap mobileCodeWSSoap = factory.getMobileCodeWSSoap();  
  //调用WebService提供的getMobileCodeInfo方法查询手机号码的归属地  
  String searchResult = mobileCodeWSSoap.getMobileCodeInfo("18265963256", null);  
  System.out.println(searchResult);  
```

笔后摘要:

webservice既然是基于http的,那么也可使用最原始的URLconnect来进行访问请求处理,但是要注意对应的soap协议版本请求头,还需要组装符合soap协议xml文档信息等.完整的请求头应该和下例类似:

```xml
  请求头:
  POST /WebServices/MobileCodeWS.asmx HTTP/1.1
  Host: webservice.webxml.com.cn
  Content-Type: text/xml; charset=utf-8
  Content-Length: length
  SOAPAction: "http://WebXml.com.cn/getMobileCodeInfo"

消息体:
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
<soap:Body>
<getMobileCodeInfo xmlns="http://WebXml.com.cn/">
<mobileCode>18265963256</mobileCode>
<userID>string</userID>
</getMobileCodeInfo>
</soap:Body>
</soap:Envelope>
```