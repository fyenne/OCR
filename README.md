### 1.网店工商信息图片文字提取

图片内容如下所示，但每张图片中信息出现的位置不尽相同，题目要求所写的程序能够完成如下几个功能点。

+ 程序能够识别不同格式的图片，并能够提取所要求的信息。

- 从图片之中提取企业注册号和企业名称信息，并保存到Excel表格之中。

- 程序能够自动读取企业工商信息图片所在的文件夹路径。
- 识别速度保持在60秒识别50张图片，识别正确率保证在95%以上。

![网店工商信息图片文字提取01](http://p66yyzg4i.bkt.clouddn.com/%E7%BD%91%E5%BA%97%E5%B7%A5%E5%95%86%E4%BF%A1%E6%81%AF%E5%9B%BE%E7%89%87%E6%96%87%E5%AD%97%E6%8F%90%E5%8F%9601.png)

### 2.Tess4j

了解题目要求之后，我们便开始来解决问题。首先明确一点的是，肯定不能从头去写文字识别算法或者文字识别程序，OCR(Optical Character Recognition , 光学字符识别)发展这么多年来，开源的库肯定不少，只需找到适合中文识别的类库或者项目即可。

个人采用的是Tess4j开源库，其中Tess4j是由Tesseract扩展而来，Tesseract是HP实验室开发由Google维护的开源OCR引擎，Tess4j支持Tiff,jpeg,gif,png,pdf等多种格式识别。我们只需要在[https://sourceforge.net/projects/tess4j/](https://sourceforge.net/projects/tess4j/)下载类库，然后编写下述代码便可实现文字识别，使用方法很简单。如果你要使用的话，请注意package,imageFile,instance的位置。

```java
package net.sourceforge.tess4j.example;

import java.io.File;
import net.sourceforge.tess4j.*;

public class TesseractExample1 {
    public static void main(String[] args) {        
    	
        File imageFile = new File("/Users/zhenhai/Downloads/SoftwareCup/Tess4J/test/resources/tianmao/1.png");
        ITesseract instance = new Tesseract();  // JNA Interface Mapping
        // ITesseract instance = new Tesseract1(); // JNA Direct Mapping
        instance.setDatapath("/Users/zhenhai/Downloads/SoftwareCup/Tess4J");
		instance.setLanguage("chi_sim");

        try {
            String result = instance.doOCR(imageFile);
            System.out.println(result);
        } catch (TesseractException e) {
            System.err.println(e.getMessage());
        }
    }
}
```

### 3.网店工商信息图片文字提取

下载的tess4j项目自带英文字体库，而我们需要识别中文信息，所以需下载中文简体字体库。字体库下载完成之后，将题目提供给我们的图片进行识别，识别之后发现准确率很低，而且识别时间过长，所以需要对图片进行处理。

> 企业注册号 : 913302055612570鄄7 ′
>
> 企业名称: 宁麦皮中哲票广鲳I忏善 蓼鬓′墓示埔壹甬
>
> 类 型 霉嫣膘占辆 虫资) 趴辕~蓼唧 `
>
> 住惑7妻 踢「【庄北长兴路689弄22号11巾童A1壬蔚雀菅业^ 刁乏喔憩」壹雇
>
> 法 人: 杨禾口荣
>
> 成立时间:2010-08-26 甬 甬
>
> 注册资本 : 1000万人民币元 / /
>
> 营业I言【j目〖艮:2010-08洲:i墅o碾言壹 鹏 莹鬓、′墓示簪
>
> 经莒范戛蓼反逼卫 目艮饰日勺扎匕发、 零售、 薯批愤嵩爵签稻昊信息的咨询 ; 服装i氦十犹撕{
>
> 菖〈茵珥跨止\ 懦牌苣理 广告服务、 企业苣癫颧琨蓼 扩〈喔圃蓼′
>
> 登i 机关 : 浙江雀宁波市江北区工商『壬政苣王里局
>
> 核准时间 : 2015-12-24 __日q __日辄

#### 3.1去除水印

首先能够看到，提供的图片带有**天猫营业执照信息公示专用**水印，所以我们需要进行去水印处理。花了很长时间在网上找去水印的开源代码，但多数都需要先提供水印模版，然后才能进行去水印处理。水印模版不是问题，我们直接截取水印图片即可，但重点是去水印处理之后，水印去除效果并不是很理想。观察一番之后，发现所有的图片水印都是同一个颜色，那么我们是不是可以把水印的rgb值改为和背景色相同，实验之后发现果然可以，由于代码比较简单，此处就不再贴出代码，可以自行尝试一下。然后重新对图片进行识别，发现准确率还是很低，那是什么原因呢？

> 企业注册号 : 913302055612570鄄7 ′
>
> 企业名称: 宁麦皮中哲票广鲳I忏善 蓼鬓′墓示埔壹甬
>
> 类 型 霉裴章膘占辆 虫资) 趴辕~苜趴 `
>
> 住惑)妻 踢「【庄北长兴路689弄22号11巾童A1壬蔚雀菅业^ 刁乏喔憩」壹雇
>
> 法 人: 杨禾口荣
>
> 成立时间:2010-08-26 甬 甬
>
> 注册资本 : 1000万人民币元 / /
>
> 营业I言【j目〖艮:2010-08洲:i墅o蔺言壹 鹏 莹鬓、′墓示簪
>
> 经莒范戛蓼反逼卫 目艮饰日勺扎匕发、 零售、 薯批愤嵩爵垦稻昊信息的咨询 ; 眼装i氦十犹撕{
>
> 菖〈茵珥跨止\ 懦牌苣理 广告目艮务、 企业苣癫颧寰蓼 扩〈喔圃蓼′
>
> 登i 机关 : 浙江雀宁波市江北区工商『壬政苣王里局
>
> 核准时间 : 2015-12-24 __日q __日辄

#### 3.2图片格式转换

将图片放大之后，发现图片处于全黑的状态，完全看不到任何字。然后尝试将图片格式转换为其他格式，比如jpg，当然这里不是直接改后缀名，而是利用在线图片格式转换工具。当然你也可以转换成其他格式，看看效果如何，转换完成之后，再次进行图片文字识别，发现准确率有较大提升。

> 企业注丹舟号 : 9133020……612…70177
>
> 企业名称 : 宁波中哲慕尚电子商务有限公司
>
> 类 型 : 有限责任公司〈法人独资)
>
> 住 所 二 宁波市江才匕长兴路689弄22号11瞳A112室
>
> 法定代表人: 杨禾口荣
>
> 成立时间 : 2010-08-26
>
> 注册资本 : 1000万人民币元
>
> 营业期限 : 2010-08-26至2020-08-25
>
> 经菖范围 : 服装、 箱包、 鞋帽` 眼饰的批发` 零售、 网上批发` 零售及棺关信息的咨询 : 眼装i毓十
>
> 、 企业品牌营王里、 广 告眼务、 企业盲理咨询。
>
> 登记机关 : 浙江雀宁波市麦工才匕区工商肴壬政苣王里局
>
> 核准时间 : 2015-12-24

#### 3.3分区域识别

图片识别准确率有一定程度提升之后，但是时间还是很高，大概15s左右，所以需要进一步优化。由于题目只需要我们识别企业注册号和企业名称，所以没有必要识别整张图片。但我们又不知道企业名称和企业注册号处于图片的什么位置，所以只能扫描着进行文字图片识别。我这里采用的是每次识别图片高度的18%，例第一次识别区域为0-18%，如果没有识别到我们所需的文字信息，下次识别图片15%-32%，这样就不会遇到文字刚好被识别区域切割的问题。

那这样识别会识别到很多重复区域，时间怎么会提升呢？其实不然，观察题目所给的50张图片，其中有46张图片的信息都是在头部，那么第一次扫描便能得到所需的信息，综合来看时间有很大程度提升。另外如果长时间未能识别到某张图片信息，那么则自动放弃识别。更改程序后重新识别图片，时间有很大程度提升，每张图片的识别速度在3s左右。

> 企业注册号 : 913302055612570177
>
> 企业名称 : 宁波中哲票尚电子商务有眼公司

对于本张图片来说，企业注册号已经能够正确识别，但是企业名称还是有个别字错误，比如将**幕**识别成**票**，将**限**识别成**眼**，因此需要进一步优化。

#### 3.4 图片二值化

为进一步提高准确率，我们将图片二值化，然后再对图片放大10倍，其实放大倍数越高，识别准确率也应该越高。这里为了在时间和准确度之间做个平衡，对图片只放大10倍。

```java
BufferedImage textImage = ImageHelper.convertImageToGrayscale(ImageHelper.getSubImage(image, 0, startHeight, resetWidth, resetHeight));

textImage = ImageHelper.convertImageToBinary(textImage);

textImage = ImageHelper.getScaledInstance(textImage, textImage.getWidth() * 10, textImage.getHeight() * 10);

```

图片放大10倍之后，我们再次对图片进行识别，发现企业注册号和企业名称完全正确。

> 企业注册号 : 913302055612570177
>
> 企业名称 : 宁波中哲幕尚电子商务有限公司

#### 3.5图片模糊寻找和结果导出

图片模糊寻找的意思也就是，给出图片文件夹的大致路径，然后程序能够找到正确的图片路径，并能够正确进行文字识别。比如给定/Users/zhenhai/Downloads/SoftwareCup/Tess4J路径，程序能够找到/Users/zhenhai/Downloads/SoftwareCup/Tess4J/test/resources/tianmao1/1.jpg路径。然后将识别到的结果导出到Excel表格，问题也很简单，这里也就不给出相应代码。

![网店工商信息图片文字提取02](http://p66yyzg4i.bkt.clouddn.com/%E7%BD%91%E5%BA%97%E5%B7%A5%E5%95%86%E4%BF%A1%E6%81%AF%E5%9B%BE%E7%89%87%E6%96%87%E5%AD%97%E6%8F%90%E5%8F%9602.png)

至此已经能够识别图片，而且准确率挺不错，准确率在95%左右，识别成功之后也能够正常导出至Excel表格。但时间依旧不理想，目前识别50张图片大概在2分钟30s左右。

### 4.待优化

图片文字识别方面只做了4天，之前也没有做过相关问题，所以还是有很大的优化空间。

+ 利用多线程，识别时间应该能够减少1分钟，达到1分钟30s识别50张(猜测)。
+ 由于我们直接利用网上的字库，没有对字库做任何训练。比如可以将出现频率较高的词设置更高的优先级，这样不仅能够提高准确率，而且能够进一步降低时间，比如上述的**限**不会再识别成**眼**。
+ 图片大小不一，可以将图片设置为平均宽度和高度，然后再进行分区域识别。而且每次识别时候不是识别企业注册号和企业名称的完整信息，而只是试探识别这几个字，如果识别成功之后，然后再扩大识别宽度，提取所需要的完整信息。
