---
title: pdfbox解析PDF文件
date: 2019-05-14 10:11:34
description: pdfbox解析PDF文件
tags:
  - java
  - pdfbox
  - pdf
---

    摘要:
      最近需要使用到对PDF文件内容进行解析,然后对文件的部分内容进行索引查询.在解析的PDF的时候Java语言有2个
      开源的PDF工具:PDFbox和Itext.
    
PDFbox和Itext都能读取、解析pdf文件，并且可对文件进行修改.有小伙伴将2个工具对比总结出以下结论:  
**在读取和解析PDF的时候使用PDFBox，较为简单，示例较为详细;修改PDF的时候使用Itext，支持粒度较细，比如控制文字字体等**

### Itext

  iText是著名的开放项目，是用于生成PDF文档的一个java类库。通过iText不仅可以生成PDF或rtf的文档，而且可以将XML、Html文件转化为PDF文件等.目前只是用到对PDF文档的解析,所以对于Itext具体使用暂未查看,

    官网:https://itextpdf.com/
    插入文字可以自定义字体，使用字库文件(ttf)

### PDFBox

#### 引入PDFBox工具库jar

    <dependency>
        <groupId>org.apache.pdfbox</groupId>
        <artifactId>pdfbox</artifactId>
        <version>2.0.15</version> <!--当前使用2.0.15的版本-->
    </dependency>

#### 编写PDFUtils类

```java
import org.apache.pdfbox.cos.COSName;
import org.apache.pdfbox.io.RandomAccessBuffer;
import org.apache.pdfbox.pdfparser.PDFParser;
import org.apache.pdfbox.pdmodel.PDDocument;
import org.apache.pdfbox.pdmodel.PDPage;
import org.apache.pdfbox.pdmodel.graphics.image.PDImageXObject;
import org.apache.pdfbox.text.PDFTextStripper;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;

/**
 * pdf文档解析工具类
 *
 * @author bz
 */
public class PDFUtils {

    private static final Logger logger = LoggerFactory.getLogger(PDFUtils.class);

    /**
     * @param pdfPath pdf文件路径
     * @return
     */
    public static PDDocument initPDDocument(String pdfPath) throws Exception {
        File pdfFile = Paths.get(pdfPath).toFile();
        if (!pdfFile.exists()) {
            logger.error("pdf文件不存在");
            return null;
        }
        // 新建一个PDF解析器对象
        PDFParser pdfParser = new PDFParser(new RandomAccessBuffer(new FileInputStream(pdfFile)));
        // 对PDF文件进行解析
        pdfParser.parse();
        // 获取解析后得到的PDF文档对象
        PDDocument pdfdocument = pdfParser.getPDDocument();
        return pdfdocument;
    }

    /**
     * @param inputStream 输入流
     * @return
     */
    public static PDDocument initPDDocument(InputStream inputStream) throws Exception {
        // 新建一个PDF解析器对象
        PDFParser pdfParser = new PDFParser(new RandomAccessBuffer(inputStream));
        // 对PDF文件进行解析
        pdfParser.parse();
        // 获取解析后得到的PDF文档对象
        PDDocument pdfdocument = pdfParser.getPDDocument();
        return pdfdocument;
    }

    /**
     * 解析pdf文档中的字符内容
     *
     * @param pdDocument
     * @param startPage  开始页码
     * @param endPage    结束页码
     * @return
     */
    public static String getContent(PDDocument pdDocument, int startPage, int endPage) throws IOException {
        if (endPage <= startPage) {
            logger.error("页码参数不正确");
            return null;
        }
        // 新建一个PDF文本剥离器
        PDFTextStripper stripper = new PDFTextStripper();
        stripper.setStartPage(startPage); // 开始提取页数
        stripper.setEndPage(endPage); // 结束提取页数
        // 从PDF文档对象中剥离文本
        String result = stripper.getText(pdDocument);
        return result;
    }

    /**
     * 解析pdf文档中的所有图片列表
     *
     * @param pdDocument
     * @param startPage  开始页码
     * @param endPage    结束页码
     * @return
     */
    public static List<PDImageXObject> getImageList(PDDocument pdDocument, int startPage, int endPage) throws IOException {
        if (endPage <= startPage) {
            logger.error("页码参数不正确");
            return null;
        }
        List<PDImageXObject> imageList = new ArrayList<PDImageXObject>();
        for (int i = startPage; i < endPage; i++) {
            PDPage page = pdDocument.getPage(i);
            Iterable<COSName> objectNames = page.getResources().getXObjectNames();
            for (COSName imageObjectName : objectNames) {
                if (page.getResources().isImageXObject(imageObjectName)) {
                    PDImageXObject imageXObject = (PDImageXObject) page.getResources()
                            .getXObject(imageObjectName);
                    imageList.add(imageXObject);
                }
            }
        }
        return imageList;
    }

}
```

#### 调用PDFUtils类方法

```java
@Test
public void test4() throws Exception {
    PDDocument pdDocument = PDFUtils.initPDDocument("/home/bz/Desktop/1.pdf");
    if (pdDocument != null) {
        // 获取文档文本内容
        String result = PDFUtils.getContent(pdDocument, 0, pdDocument.getNumberOfPages());
        System.out.println("PDF文件的文本内容如下：");
        System.out.println(result);
        // 获取文档中的所有图片
        List<PDImageXObject> imageList = PDFUtils.getImageList(pdDocument, 0,pdDocument.getNumberOfPages());
        for (int i = 0; i < imageList.size(); i++) {
            PDImageXObject imageXObject = imageList.get(i);
            BufferedImage bufferedImage = imageXObject.getImage();
            ImageIO.write(bufferedImage, imageXObject.getSuffix(),
                    new FileOutputStream(Paths
                            .get("/home/bz/Desktop/" + i + "." + imageXObject.getSuffix()).toFile()));
        }
    }
}
```
