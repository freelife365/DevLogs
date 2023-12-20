# Docx文件 结构分析

把一个.docx文件解压，可以看下类似下面的结构：

```
│——[Content_Types].xml
│
├─customXml
│  │  item1.xml
│  │  itemProps1.xml
│  │
│  └─_rels
│          item1.xml.rels
│
├─docProps
│      app.xml
│      core.xml
│
├─word
│  │  document.xml  //包含文档内容的xml文件，主要是解析它
│  │  endnotes.xml
│  │  fontTable.xml
│  │  footer1.xml
│  │  footnotes.xml
│  │  numbering.xml
│  │  settings.xml  //word文档的设置信息
│  │  styles.xml
│  │  webSettings.xml
│  │
│  ├─media  //存放docx文档里的图片，音频，视频等。
│  │      image1.wmf 
│  │
│  ├─theme //主题文件夹
│  │      theme1.xml
│  │
│  └─_rels
│          document.xml.rels //资源的引用关系
│
└─_rels
        .rels
```
可以看出，大多数都是 XML 文件。
我们主要关注其中三个文件：word/document.xml，word/styles.xml 和 word/fontTable.xml。

- document.xml： docx 文件的内容
- styles.xml：	    docx 文件的样式
- fontTable.xml：   docx 文件用到的的字体

docx 文件基本上做到了表现和内容分离。
document.xml 和 styles.xml 类似于 Web 中的 HTML 和 CSS。定制样式时，我们基本上不用修改内容，也就是 document.xml 文件。

## styles.xml 文件说明

styles.xml 文件的作用相当于 Web 中的 CSS，下面是一个样式示例：

```xml
<w:style w:styleId="BodyText" w:type="paragraph">
    <w:name w:val="Body Text"/>
    <w:basedOn w:val="Normal"/>
    <w:link w:val="BodyTextChar"/>
    <w:pPr>
        <w:spacing w:after="180" w:before="180"/>
    </w:pPr>
    <w:rPr>
        <w:rFonts w:ascii="Arial" w:cstheme="majorBidi" w:eastAsia="黑体" w:hAnsiTheme="majorHAnsi"/>
        <w:sz w:val="36"/>
    </w:rPr>
</w:style>
```

## fontTable.xml 文件说明
这个文件定义 docx 文件中用到的所有字体。下面是一个字体定义示例：
```xml
<w:font w:name="宋体">
    <w:altName w:val="SimSun"/>
    <w:panose1 w:val="02010600030101010101"/>
    <w:charset w:val="86"/>
    <w:family w:val="auto"/>
    <w:pitch w:val="variable"/>
    <w:sig w:csb0="00040001" w:csb1="00000000" w:usb0="00000003" w:usb1="080E0000" w:usb2="00000010" w:usb3="00000000"/>
</w:font>
```


## docx中的常用XML元素说明

```xml
<w:document> <!--文档标记，里面有一堆namespace的声明-->
<w:body> <!--标记文档的主体部分-->
<w:p> <!--段落标记(Paragraph)-->
<w:pPr> <!--段落属性，其中 w:spacing 元素用于设置段前和段后距离，单位是二十分之一英寸。-->
<w:r> <!--行标记(run), run也称为文本块(Text run container)-->
<w:rPr> <!--行属性，其中w:rFonts用于设置字体（可以分别设置中西文字体），w:sz元素用于设置字号，单位是二分之一点(pt)-->
<w:t><!--文本标记(text)-->
<w:t xml:space="preserve"> <!--保持空格，如果没有这内容的话，文本的前后空格将会被Word忽略--> 
<w:u><!--下划线样式-->
<w:i><!--斜体样式-->
<w:b w:val=”on”> <!--粗体样式-->
<w:bCs> <!--复合字体的加粗-->
<w:jc w:val="right"/> <!--对齐方式-->
<w:sz w:val="40"/> <!--字号大小-->
<w:szCs w:val="40"/> <!--	-->
<w:spacing  w:line="600" w:lineRule="auto"/> <!--设置行距，要进行运算，要用数字除以240，如此处为600/240=2.5倍行距-->  
<w:drawing > <!--图片-->
<wp:inline  > <!--内嵌绘图对象，dist(T,B,L，R)距离文本上下左右的距离-->
<wp:extent> <!--绘图对象大小-->
<wp:effectExtent > <!--嵌入图形的效果-->
<w:ind> <!--w:pPr元素的子元素，跟w:pStyle并列，ind代表缩进情况：有几个属性值：
①firstLine（首行缩进） ②left（左缩进）③left和firstLine ④hanging（悬挂）-->
<w:hint> <!--字体的类型，w:rFonts的子元素，属性值eastAsia表面上的意思是东亚"，指代"中日韩CJK"类型。-->
<w:styleId> <!--属性的值是内部ID，document.xml 文件中的结构需要什么样式，就引用这个ID的值，就像是 CSS 中的选择符一样。-->
<w:basedOn> <!--元素中 w:val 的值是这个样式继承的其他样式ID；这个元素很重要，用于实现块级元素的样式继承。-->
<w:link> <!--元素中 w:val 的值也是这个样式继承的其他样式 ID，不过这个元素继承的是行内元素的样式。-->
<w:bookmarkStart> <!--书签起始标记-->
<w:bookmarkEnd> <!--书签终止标记-->
 <w:hdr> <!--页眉-->
<w:ftr> <!--页脚-->
<w:secPr> <!--定义的页面尺寸-->
<w:pgSz> <!--页面大小标记，其中w:w表示宽度，w:h表示高度-->
<w:docPr> <!--表示文档属性-->
<w:type> <!--属性是样式的类型，一般只会用到 paragraph、character 和 table。-->
<w:name> <!--元素中 w:val 属性的值是这个样式在 GUI 应用（Word，Pages 或 OpenOffice 等）中显示的名称。-->
<w:noProof  > <!--不检查拼写和语法错误-->
<w:lastRenderedPageBreak > <!--页面进行分页的标记，是w:r的一个属性，表示此段字符串是一页中的最后一个字符串。-->
<w:smartTag > <!--智能标记-->
<w:attr  > <!--自定义XML属性-->
<w:rsidR> <!--指定一个标识符，用来跟踪编辑在修订时所做的修改，所有段落和段落中的内容都应该拥有相同的属性值，如果出现差异，那么表示这个段落在后面的编辑中被修改。-->

<!-- 设置了页的宽，高，和页的各边距。各项的值均是英寸乘1440得出 --> 
<w:body>
    <w:sectPr>  
        <w:pgSz w:w="12240" w:h="15840"/>
        <w:pgMar w:top="1440" w:right="1800" w:bottom="1440" w:left="1800" w:header="720" w:footer="720" w:gutter="0"/>
    </w:sectPr>  
</w:body> 

<!--页眉和页脚-->
<w:sectPr wsp:rsidR="002C452C">
    <w:hdr w:type="odd" >
        <w:p>
            <w:pPr>
                <w:pStyle w:val="Header"/>
            </w:pPr>
            <w:r>
                <w:t>这是页眉</w:t>
            </w:r>
        </w:p>
    </w:hdr>
    <w:ftr w:type="odd">
        <w:p>
            <w:pPr>
                <w:pStyle w:val="Footer"/>
            </w:pPr>
            <w:r>
                <w:t>这是页脚</w:t>
            </w:r>
        </w:p>
    </w:ftr>
</w:sectPr> 

<!--docPr(document property)，表示文档属性，这里表示文档的视图是print，视图比例为100%-->
<w:docPr>
    <w:view w:val="print"/>
	<w:zoom w:percent="100"/>
</w:docPr>

```

Word中一般是`文档Document - 段落Paragraph - 文字块Run`三级结构，这是最普遍的情况。

如果存在表格则有`Document - Table - Row/Column - Cell`四级结构

假如段落句子中有多种不同的样式，则会被划分成多个文字块。

## xpath的使用

```xml
指定字体为粗体
<w：style / w：rPr / w：b>  

表示字体颜色
<w：style / w：rPr / w：color>
```



##  DOCX支持两种图像：**内联图像** 和 **浮动图像**。

内联图像与其他字符一起出现在段落内, 使用`<w:drawing>`而不是使用`<w:t>`(文本)。

浮动图像是相对于段落放置的, 文字在它们周围流动。浮动图像使用`<wp:anchor>`而不是`<w:drawing>`

## rFonts意为RunFonts
目前rFonts支持四个语系( w:ascii, w:eastAsia,  w:hAnsi,  w:cs)，分别为：

```json
{
    'ascii':	'ASCII Font',	  
    'eastAsia':	'East Asian Font',
    'hAnsi':	'High ANSI Font',	
    'cs':		'Complex Script Font'
}
```

rFonts 使用`w:hint`说明此段文本具体使用哪种语系的字体。
例如，将一段汉字的中文字体与西文字体全部设置为宋体：

```xml
<w:r>
    <w:rPr>
        <w:rFonts w:ascii="宋体" w:eastAsia="宋体" w:hAnsi="宋体" w:cs="宋体" w:hint="eastAsia"/>
    </w:rPr>
    <w:t>这一段文字字体为宋体。</w:t>
</w:r>
```

## 字号`<w:sz>`、`<w:szCs>`
sz表示Non-Complex Script Font Size，简单理解的话，就是单字节字符（如ASCII编码字符等）的大小。

szCs表示Complex Script Font Size，可以简单理解为双字节字符（如中日韩文字、阿拉伯文等）的大小。

如果不显式指明字号，此段文字会使用上一级指明的字号。

sz的官方资料中这句话The font sizes specified by this element’s val attribute are expressed as half-point values 说明，

`<w:sz>`与`<w:szCs>`中的数值是实际磅值的两倍（原文翻译为 "此元素的数值代表的字体大小，表示半点值"）。

例如，一个五号汉字，考虑如下中文字号与磅值的对应关系，其磅值为10.5，翻倍为21，所以应该有`<w:szCs w:val="21"/>`

```xml
<w:r>
    <w:rPr>
        <w:rFonts w:hint="eastAsia"/>
        <w:sz w:val="21"/>
        <w:szCs w:val="21"/>
    </w:rPr>
    <w:t>这一段文字是五号字</w:t>
</w:r>
```

中文字号与磅值对应关系

```json
SZ_ZH_PT = {	
    "八号": 5,
    "七号": 5.5,
    "小六": 6.5,
    "六号": 7.5,
    "小五": 9,
    "五号": 10.5,
    "小四": 12,
    "四号": 14,
    "小三": 15,
    "三号": 16,
    "小二": 18,
    "二号": 22,
    "小一": 24,
    "一号": 26,
    "小初": 36,
    "初号": 42,
}

```

## 看的见的文字Text：`<w:t>`
正常来说，每个run中都应该有`<w:t>`结点，此结点包含的内容就是真实显示在word中的文字。

python-docx中的paragraph.text和run.text函数，返回的就是`<w:t>`结点中的内容，例如：
```python
from docx import Document

doc = Document('春江花月夜.docx')
for paragraph in doc.paragraphs:
    print('本段内容长度为%d' % len(paragraph.text))
    for i, run in enumerate(paragraph.runs):
        print('第{}个run的内容：{}'.format(i, run.text))

```

### 字体说明

```
'SimSun' : '宋体',
'NSimSun' : '新宋体',
'FangSong_GB2312' : '仿宋_GB2312',
'KaiTi_GB2312' : '楷体_GB2312',
'SimHei' : '黑体',
'Microsoft YaHei' : '微软雅黑',
'Arial' : 'Arial',
'Arial Black' : 'Arial Black',
'Times New Roman' : 'Times New Roman',
'Courier New' : 'Courier New',
'Tahoma' : 'Tahoma',
'Verdana' : 'Verdana'

```



##  修订版本号 rsid

Word会记录每一次对于文件的修改，并对修订版本进行随机编号。在OpenXML中，修订版本号通过rsid表示，具体的有`w:rsidR`、`w:rsidRPr`等。

具体的版本更迭信息，保存在`<w:settings>`下的`<w:rsids>`中。

##  注音系统 Ruby，`<w:ruby>`
标注过拼音的字符，在word中表现为一块域（field）。在OpenXML中，可看作是在`<w:r>`中，用`<w:ruby>`结点替换了`<w:t>`结点。

一个典型的注音xml：
```xml
<w:ruby>
    <w:rubyPr>
        <w:rubyAlign w:val="center"/>
        <w:lid w:val="zh-CN"/>
    </w:rubyPr>
    <w:rt>
        <w:r>
            <w:rPr></w:rPr>
            <w:t>zhōng</w:t>
        </w:r>
    </w:rt>
    <w:rubyBase>
        <w:r>
            <w:rPr></w:rPr>
            <w:t>中</w:t>
        </w:r>
    </w:rubyBase>
</w:ruby>

```

段属性包含在`<w:pPr></w:pPr>`中，

```xml
　　<w:pPr>
　　　　<w:rPr>
　　　　　　<w:jc w:val="center"/> <!-- 这句话表示段落对齐方式 -->
　　　　　　<w:spacing w:line="600" w:lineRule="auto" /> <!-- 设置行距，要进行运算，line为行距值,240为单倍行距,用数字除以240，如此处为600/240=2.5倍行距 -->
　　　　</w:rPr>
　　</w:pPr>
```

文本格式包含在`<w:rPr></w:rPr>`中，这儿的Pr是property的意思

```xml
　　<w:r w:rsidRPr="00F2468A">
　　　　<w:rPr>
　　　　　　<w:b w:val="on"/> <!-- 粗体 -->
　　　　　　<w:sz w:val="40"/> <w:szCs w:val="40"/> <!-- 字体尺寸为是40除2等于20 -->
　　　　　　<w:rFronts w:ascii="宋体" w:hAnsi="宋体" w:hint="esatAsia"/>
　　　　</w:rPr>
　　　　<w:t>${wordtext1}</w:t> <!-- ${wordtext1}是freemarker变量，一般是字符串，用于输出，这个可以参考freemarker文档 -->
　　</w:r>
```

## 文本部分

```xml
<w:p>
 Paragraph
<w:r>
 Text run container
<w:rPr>
 Text run properties container
<w:t>
 Text container

<w:u>
 Underline property flag
<w:i>
 Italic property flag
<w:b>
 Bold property flag


```

## 上下标的表示

### 下标（水的化学公式H2O）

```xml
<w:p w:rsidR="00033018" w:rsidRDefault="002C629D" w:rsidP="008A5A5F">
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>H</w:t>
    </w:r>
    <w:r w:rsidRPr="002C629D">
        <w:rPr>
            <w:vertAlign w:val="subscript"/>
        </w:rPr>
        <w:t>2</w:t>
    </w:r>
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>O</w:t>
    </w:r>
</w:p>
```

### 上标（平方米）

```xml
<w:p w:rsidR="002C629D" w:rsidRPr="00033018" w:rsidRDefault="002C629D" w:rsidP="008A5A5F">
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>M</w:t>
    </w:r>
    <w:r w:rsidRPr="002C629D">
        <w:rPr>
            <w:vertAlign w:val="superscript"/>
        </w:rPr>
        <w:t>2</w:t>
    </w:r>
</w:p>
```

## 表格

一个`2*2`的表格，每个单元格中的内容分别为 `AB12`

```xml
 <w:tbl>
     <w:tblPr>
         <w:tblStyle w:val="aa"/>
         <w:tblW w:w="0" w:type="auto"/>
         <w:tblLook w:val="04A0" w:firstRow="1" w:lastRow="0" w:firstColumn="1" w:lastColumn="0" w:noHBand="0" w:noVBand="1"/>
     </w:tblPr>
     <w:tblGrid>
         <w:gridCol w:w="4264"/>
         <w:gridCol w:w="4264"/>
     </w:tblGrid>
     <w:tr w:rsidR="00490004" w:rsidTr="00490004">
         <w:tc>
             <w:tcPr>
                 <w:tcW w:w="4264" w:type="dxa"/>
             </w:tcPr>
             <w:p w:rsidR="00490004" w:rsidRDefault="00033018" w:rsidP="00033018">
                 <w:pPr>
                     <w:jc w:val="center"/>
                 </w:pPr>
                 <w:r>
                     <w:rPr>
                         <w:rFonts w:hint="eastAsia"/>
                     </w:rPr>
                     <w:t>A</w:t>
                 </w:r>
             </w:p>
         </w:tc>
         <w:tc>
             <w:tcPr>
                 <w:tcW w:w="4264" w:type="dxa"/>
             </w:tcPr>
             <w:p w:rsidR="00490004" w:rsidRDefault="00033018" w:rsidP="00033018">
                 <w:pPr>
                     <w:jc w:val="center"/>
                 </w:pPr>
                 <w:r>
                     <w:rPr>
                         <w:rFonts w:hint="eastAsia"/>
                     </w:rPr>
                     <w:t>B</w:t>
                 </w:r>
             </w:p>
         </w:tc>
     </w:tr>
     <w:tr w:rsidR="00490004" w:rsidTr="00490004">
         <w:tc>
             <w:tcPr>
                 <w:tcW w:w="4264" w:type="dxa"/>
             </w:tcPr>
             <w:p w:rsidR="00490004" w:rsidRDefault="00033018" w:rsidP="00033018">
                 <w:pPr>
                     <w:jc w:val="center"/>
                 </w:pPr>
                 <w:r>
                     <w:rPr>
                         <w:rFonts w:hint="eastAsia"/>
                     </w:rPr>
                     <w:t>1</w:t>
                 </w:r>
             </w:p>
         </w:tc>
         <w:tc>
             <w:tcPr>
                 <w:tcW w:w="4264" w:type="dxa"/>
             </w:tcPr>
             <w:p w:rsidR="00490004" w:rsidRDefault="00033018" w:rsidP="00033018">
                 <w:pPr>
                     <w:jc w:val="center"/>
                 </w:pPr>
                 <w:r>
                     <w:rPr>
                         <w:rFonts w:hint="eastAsia"/>
                     </w:rPr>
                     <w:t>2</w:t>
                 </w:r>
             </w:p>
         </w:tc>
     </w:tr>
 </w:tbl>
```

## 自动编号

```xml
<w:p w:rsidR="000C4EBA" w:rsidRDefault="000C4EBA" w:rsidP="000C4EBA">
    <w:pPr>
        <w:pStyle w:val="a9"/>
        <w:numPr>
            <w:ilvl w:val="0"/>
            <w:numId w:val="4"/>
        </w:numPr>
        <w:ind w:firstLineChars="0"/>
    </w:pPr>
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>A</w:t>
    </w:r>
</w:p>
<w:p w:rsidR="000C4EBA" w:rsidRDefault="000C4EBA" w:rsidP="000C4EBA">
    <w:pPr>
        <w:pStyle w:val="a9"/>
        <w:numPr>
            <w:ilvl w:val="0"/>
            <w:numId w:val="4"/>
        </w:numPr>
        <w:ind w:firstLineChars="0"/>
    </w:pPr>
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>B</w:t>
    </w:r>
</w:p>
<w:p w:rsidR="000C4EBA" w:rsidRPr="008A5A5F" w:rsidRDefault="000C4EBA" w:rsidP="000C4EBA">
    <w:pPr>
        <w:pStyle w:val="a9"/>
        <w:numPr>
            <w:ilvl w:val="0"/>
            <w:numId w:val="4"/>
        </w:numPr>
        <w:ind w:firstLineChars="0"/>
    </w:pPr>
    <w:r>
        <w:rPr>
            <w:rFonts w:hint="eastAsia"/>
        </w:rPr>
        <w:t>C</w:t>
    </w:r>
</w:p>	
```

## Office Open XML (OOXML) 的测量单位
对于 WordProcessingML 文件，其的DPI是 72。

在 Office Open XML 默认单位是 dxa ，也叫二十分之一点(twips)。
它用于指定页面尺寸，边距，制表符等等，另外这里说的像素点是 Point 而不是像素 Pixel 哦.

国际纸长标准` ISO 216 A4 （210 *297mm，8.3* 11.7in)`，在word中档中的表示如下:

原文：https://startbigthinksmall.wordpress.com/2010/01/04/points-inches-and-emus-measuring-units-in-office-open-xml/

```xml
// 在页面大小 Page width, Page height 和 边距 margin 和 缩进 tabs 使用
// pageSize: with and height in 20th of a point
<w:pgSz w:w="11906" w:h="16838"/>
```
为什么会是这么巨大的值呢？因为其使用的单位是不同的。

A4 表示如下：
| 宽，高 | Twip(1/20Pt) | Point(1/72 Inch) | Inch  | Cm（2.54*Inch） |
| :----- | :----------- | :--------------- | :---- | :-------------- |
|        | 缇           | 点               | 英寸  | 厘米            |
| 宽度   | 11906        | 595.3            | 8.27  | 21.0            |
| 高度   | 16838        | 841.9            | 11.69 | 29.7            |

单位计算可以使用下面公式

```
以 A4 为例
A4 Width 
= 11906 dxa 
= 595.3 point // dxa/20 
= 8.27 in     // points/72
= 21 cm       // Inches*2.54
```

缩写如下

- 像素点 `Points: pt`
- 英寸 `Inches: in`
- 厘米 `Centimeters: cm`

## Half-points

用来表示字体大小的半点，一个点等于两个半点，*12pt* 的字体大小，等于*24 half points* ，如表示 12pt 可以这样写

```
// run properties
<w:rPr>
  // 24 = 12pt
  <w:sz w:val="24"/>
</w:rPr>
```

## Fiftieths of a Percent（1/50百分点）

表示百分比相对值，用于表示表格的宽度和相对宽度，他的值和百分比换算如下

```
n/100 * 5000
```

如百分之50可以表示为 `(50/100) * 5000 pct` 的大小，如表格的宽度是百分之50宽度

```
<w:tbl>
    <w:tblPr>
      <!-- 表格宽度是百分之50宽度 -->
      <w:tblW w:w="2500" w:type="pct"/>
    </w:tblPr>
    <w:tblGrid/>
    <w:tr>
        <w:tc>
            <w:p>
                <w:r>
                    <w:t>Hello, World!</w:t>
                </w:r>
            </w:p>
        </w:tc>
    </w:tr>
</w:tbl>
```

上面的表格将会占据可用宽度的 *50%*.如果想用 1/20点，也就是 dxa（缇）来表示，要设置 `w:type=dxa`。

## English Metric Unit

EMU(English Metric Unit)， 在基于矢量的绘制和图片的时候，EMU常用来进行描述坐标。EMU是 厘米 和 英寸的桥梁。

换算如下

```
1 inch = 914400 EMUs
1 cm = 360000 EMUs
```

如用于 `w:drawing` 绘制，表示绘制画布的宽度
```
<!-- drawing canvas size -->
<wp:extent cx="1530350" cy="2142490"/> <!-- 用这么大的数是可以提高精度和性能，不需要通过浮点计算 -->
```

原始图片是 `295x413px 72dpi`，使用DrawingML嵌入OOXML。文档中图片大小需要指定两次，一次是指定画板（extent），另一次是嵌入的图片自己。

绘制这个图片时，很难将其与单元格的实际尺寸匹配。下面是通过手动计算目标图片大小进行设置：
```
<w:drawing>
    <wp:anchor ...>
        ...
        <!-- drawing canvas size -->
        <wp:extent cx="1530350" cy="2142490"/>
        ...
        <a:graphic>
            <a:graphicData>
                <pic:pic>
                    ...
                    <!-- shape properties -->
                    <pic:spPr bwMode="auto">
                        <!-- 2D transform -->
                        <a:xfrm>
                            ...
                            <!-- picture size -->
                            <a:ext cx="1530350" cy="2142490"/>
                        </a:xfrm>
                        ...
                    </pic:spPr>
                </pic:pic>
            </a:graphicData>
        </a:graphic>
    </wp:anchor>
</w:drawing>
```

假如我们要在一个表格内插入图片：
```
<w:tcW w:w="2410" w:type="dxa"/>
```

可以使用一个简单的方式来避免浮点数： `2410*914400/20/72 = 1530350` 可以写成 `2140*635=1530350`

我们想要保持图片原始比例295:413，就需要计算单元格的dxa高度，并换算图片的EMU高度

图片的宽高比为1.4，所以单元格的高度就是 `2410*1.4=3374 dxa`，也就是 `3374*635=2142490 emu`

| 宽   | Twip(1/20Pt) | Point(1/72 Inch) | Inch    | Cm（2.54*Inch） | EMU         |
| :--- | :----------- | :--------------- | :------ | :-------------- | :---------- |
|      | 缇           | 点               | 英寸    | 厘米            | 英寸*914400 |
| 宽度 | 2410         | 120.5            | 1.57361 |                 | 1530350     |

计算方法：

```
2410 * 914400 / 20 /72 = 1530350
```

或者 直接：

```
2140 * 635 = 1530350
```

就是说 

```c++
1 dxa = 635 EMU
1 Inch = 914400EMU = 2.54cm = 72pt 
1 pt = (96/72)px = (4/3)px;
1 px = 9525 EMU

图片信息模型如下:
public class WordImagePropertyObject 
{
   static int emuWeight = 9525; // EMU = 像素*weight;
   string imgId; // ID,如20,21,22
   string relationshipId;
   int width=0; // 图片的宽度
   int height=0;// 图片的高度
   int imgHEMU; // EMU = 像素*weight;
   int imgWEMU;
   string imgSrc;
   char[] imgByte;
    ...
}
```

## A4纸的像素和分辨率

Pixel要换成物理长度需要指定DPI(Dots Per Inch, 每英寸像素数).
Windows默认是96dpi, Apple默认是72dpi.

A4纸的尺寸是 210mm * 297 mm， 1 英寸 = 2.54 cm。

根据分辨率来得出常用的尺寸：

当分辨率是72像素/英寸时，A4纸像素长宽分别是842×595；
当分辨率是120像素/英寸时，A4纸像素长宽分别是2105×1487；
当分辨率是150像素/英寸时，A4纸像素长宽分别是1754×1240；
当分辨率是300像素/英寸时，A4纸像素长宽分别是3508×2479；

现在我们一般 用的是 300dpi的这样。

###  POI内的实现

```java
- public static final int EMU_PER_PIXEL = 9525;
- public static final int EMU_PER_POINT = 12700;
- public static final int EMU_PER_CENTIMETER = 360000;
- public static final int MASTER_DPI = 576;
- public static final int PIXEL_DPI = 96;
- public static final int POINT_DPI = 72;
- public static final float DEFAULT_CHARACTER_WIDTH = 7.0017F;
- public static final int EMU_PER_CHARACTER = 66691;
```

## 文档坐标系统

为了在文档内指定一个形状，我们必须要先了解文档坐标系统。
这个系统也拥有 **x, y**组件，而且从 (0,0)开始，位于 左上角。这个坐标向右，向下增长。
文档的坐标的单位是 **EMU（ 91440 EMUs /英寸，36000 EMUs/cm）**。
还有，为了给形状指定一个位置，还要指定形状的宽高，这也叫做形状的 `extent`。
这个值的单位也是 EMU。为了指定这两个值，可以如下面这样：

```xml
<p:sp>
	<p:spPr>
		<a:xfrm>
			<a:off x="3200400" y="1600200"/>
			<a:ext cx="1200000" cy="1000000"/>
		</a:xfrm>
	</p:spPr>
</p:sp>
```

这里我们会看到，新形状被放在 文档中`(3200400, 1600200)`位置。其大小为 `1200000 EMUs，1000000 EMUs`。

宽高就设置了形状包含的边界盒子。

## 使用预定义形状

指定一个预定义形状非常的简单，下面我们来指定一个星形：

```xml
<p:sp>
    <p:spPr>
	  <a:xfrm>
		<a:off x="1981200" y="533400"/>
         <a:ext cx="1143000" cy="1066800"/>
      </a:xfrm>
      <a:prstGeom prst="heart">
      </a:prstGeom>
    </p:spPr>
</p:sp>
```

## px,pt,em换算表
pt (point，磅)：是一个物理长度单位，指的是72分之一英寸。
px (pixel，像素)：是一个虚拟长度单位，是计算机系统的数字化图像长度单位，如果px要换算成物理长度，需要指定精度DPI(Dots Per Inch，每英寸像素数)，在扫描打印时一般都有DPI可选。Windows系统默认是96dpi，Apple系统默认是72dpi。

em(相对长度单位，相对于当前对象内文本的字体尺寸)：是一个相对长度单位，最初是指字母M的宽度，故名em。现指的是字符宽度的倍数，用法类似百分比，如：`0.8em`, `1.2em`,`2em`等。通常`1em=16px`。

字号：是中文字库中特有的一种单位，以中文代号表示特定的磅值pt，便于记忆、表述。

pt和px的换算公式可以根据pt的定义得出:
pt = 1/72(英寸)
px = 1/dpi(英寸)

因此 pt = px * dpi / 72

以 Windows 下的 96dpi 来计算，
1 pt = px * 96/72 = px * 4/3

convert-pixels-to-points

There is a way to get the configured pixels per inch of your display in Windows using GetDeviceCaps. 
Microsoft has a guide called "Developing DPI-Aware Applications", look for the section "Creating DPI-Aware Fonts".

There are 72 points in an inch (that is what a point is, 1/72 of an inch)
on a system set for 96dpi, there are 96 pixels per inch.
1 in = 72pt = 96px (for 96dpi setting)

1pt = (96/72)px = (4/3)px;

## `o:gfxdata`数据

Word XML中的`o:gfxdata`的数据,  是用来兼容之前的绘图数据。

将`o:gfxdata`数据中的`&#xA;`去掉，然后Base64 to File，得到一个bin文件，比如，`application.bin`， 然后将`.bin`重命名为`.zip`, 解压缩得到：
(执行 `tree . /f `)

```
│  [Content_Types].xml
│
├─drs
│      downrev.xml
│      e2oDoc.xml
│
└─_rels
        .rels
```

## DrawingML

DrawingML 是office 应用程序新增的一种功能强大的矢量图形处理工具。
Custom XML 可以让开发人员定义XML， 这就大大扩展了office2007的功能。

```
Bibliography
Metadata
Equations

WordprocessingML -.docx 
SpreadsheetML -.xslx
PresentationML -.pptx

Relationships
Content Types
Digital Signatures
```


## Word to HTML

### 数学公式： 

`Office MathML(OMML)-->MathML --> Latex`

Office在安装目录提供了OMML转MathML的XSL工具，`MML2OMML.XSL`

MathML转Latex用另外一个工具，`mmltex.xsl`

MathJax让前端页面支持Latex

### 图形：

`VML-->SVG`

` Drawing ML --> SVG` 

## 总结

如果要搞定一个word文档的ftl导出（模版部分），有一个很简单却很高效的方法：首
先用word编辑好文档，然后另存为的时候，改一下保存类型，改为xml文件，这个xml文件就是ftl模版了，
给这个xml文件重命名为ftl文件，模版就制作完成了，需要更改的只是模版中的变量而已

## HTML+CSS 导出 Word

[html2openxml](https://github.com/onizet/html2openxml)

word 才是转 word 的最佳工具
用 word 打开一个带 html+css 的文件后，再另存为 word，不仅格式得到了保留，图片也在，而且兼容性也还不错！



| HTML                                   | Word                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| `<p>,<h1>-<h6>`                        | `<w:p>`                                                      |
| `<i>,<em>`                             | `<w:i>`                                                      |
| `<b>,<strong>`                         | `<w:b>`                                                      |
| `<strike>`                             | `<w:strike>`                                                 |
| `<u>`                                  | `<w:u w:val="single">`                                       |
| `<font face='couries new' size='8pt'>` | `<w:rFonts w:ascii="couries new" w:eastAsia="宋体" w:hAnsi="couries new" w:cs="couries new"/>` |
| `color`                                | `<w:color>`                                                  |
| `P的line_height属性`                   | `<w:spacing w:line=""`                                       |
| `P的align属性`                         | `<w:jc align="">`                                            |



## Word XML格式解析
我们动态生成网页一样，要动态生成的word文件也有相当多部分是固定不变的，并且复杂的版面、格式一般都在这些固定不变的部分中。
所以借用生成网页的思路，我们可以在word中先根据需要的版面和格式，结合一些虚拟的数据做出模板文件，
然后将这个模板文件保存为xml格式，再在xml文件中找到那些虚拟的数据对应的文本，将它们替换为将采用的后台处理技术的数据处理指令，
这样我们就可以在服务器端结合模板文件和数据库查询，生成word文件了。



## Word对象模型（Word Object Model）

Word 对象是按层次顺序排列的，层次结构顶端的两个主类是 [Application](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.application.aspx) 和 [Document](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.document.aspx) 类。

**Application** 对象表示整个应用程序，每个 **Document** 对象表示单个 Word 文档，[Paragraph](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.paragraph.aspx) 对象对应于单个段，以此类推。这些对象各自都有很多方法和属性，您可以使用这些方法和属性操作对象或与对象交互。

Word 对象模型中有许多重叠。例如，**Document** 和 [Selection](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.selection.aspx) 对象都是 **Application** 对象的成员，但是 **Document** 对象还是 **Selection** 对象的成员。**Document** 和 **Selection** 对象都包含 **Bookmark** 和 [Range](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.range.aspx) 对象。存在重叠是因为您可以通过多种方式来访问相同类型的对象。例如，您向一个 Range 对象应用格式化，但是您可能想要访问当前选择的范围、特定的段落、小节或整个文档。

**Application** 对象包含 **Document**、**Selection**、**Bookmark** 和 **Range** 对象。

Word 提供了数百个您可与之交互的对象。以下各部分简要描述顶级对象以及它们彼此之间如何进行交互。这些对象包括：

- Application 对象
- Document 对象
- Selection 对象
- Range 对象
- Bookmark 对象

### Application 对象

**Application** 对象表示 Word 应用程序，是其他所有对象的父级。它的所有成员通常作为一个整体应用于 Word。可以使用该对象的属性和方法来控制 Word 环境。

### Document 对象

**Microsoft.Office.Interop.Word.Document** 对象是 Word 编程的中枢。当您打开文档或创建新文档时，就创建了新的 **Microsoft.Office.Interop.Word.Document** 对象，该对象被添加到 Word 的 [Documents](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.documents.aspx) 集合中。焦点所在的文档叫做活动文档，由 **Application** 对象的 [ActiveDocument](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word._application.activedocument.aspx) 属性表示。

### Selection 对象

**Selection** 对象表示当前选择的区域。在 Word 用户界面中执行某项操作（例如，对文本进行加粗）时，应首先选择或突出显示文本，然后应用格式设置。**Selection** 对象始终存在于文档中。如果未选中任何对象，它表示插入点。此外，它也可以是不连续的多个文本块。

### Range 对象

**Range** 对象表示文档中的一个连续的区域，由一个起始字符位置和一个结束字符位置定义。**Range** 对象的数量并不局限于一个。您可以在同一文档中定义多个 **Range** 对象。**Range** 对象具有下面的特性：

- 它的组成成分可以是单独的插入点，也可以是一个文本范围或整个文档。
- 它包含非打印字符，例如空格、制表符和段落标记。
- 它可以是当前选择所表示的区域，也可以表示当前选择之外的区域。
- 与所选内容总是可见不同，它在文档中是不可见的。
- 它不随文档保存，仅存在于代码运行期间。

在向一个范围的末尾插入文本时，Word 会自动扩展该范围以包含插入的文本。

### Bookmark 对象

文档中的 **Microsoft.Office.Interop.Word.Bookmark** 是控制文档中的文本的最容易的方法，在这一点上它类似于 Windows 窗体上的文本框控件。**Microsoft.Office.Interop.Word.Bookmark** 对象表示文档中同时具有起始位置和结束位置的连续区域。书签用于在文档中标记一个位置，或者用作文档中的文本容器。**Microsoft.Office.Interop.Word.Bookmark** 对象可以小到只有一个插入点，也可以大到整篇文档。**Microsoft.Office.Interop.Word.Bookmark** 与 **Range** 对象的不同之处在于它具有以下特点：

- 您可以在设计时命名书签。
- **Microsoft.Office.Interop.Word.Bookmark** 对象随文档一起保存，因此当代码停止运行或文档关闭时，它不会被删除。
- 书签可以隐藏或变得可见，方法是将 [View](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.view.aspx) 对象的 [ShowBookmarks](http://msdn.microsoft.com/zh-cn/library/microsoft.office.interop.word.view.showbookmarks.aspx) 属性设置为 **True** 或 **False**。

Visual Studio Tools for Office 将 **Bookmark** 对象扩展为一个宿主控件。**Microsoft.Office.Tools.Word.Bookmark** 控件与本机 **Microsoft.Office.Interop.Word.Bookmark** 的行为相似，但是前者具有附加事件和数据绑定功能。您现在可以将数据绑定到文档中的书签控件，与将数据绑定到 Windows 窗体上的文本框控件的方法相同。

注意： 以编程方式在运行时添加到文档的 Microsoft.Office.Tools.Word.Bookmark 控件不会随文档一起得到保留。只有基础的 **Microsoft.Office.Interop.Word.Bookmark** 对象被保存下来。

### 如何实现某功能

1. 使用 word 的视图选项卡中的宏 -> 录制宏，把想实现的步骤手动操作录制成宏后 -> 停止录制，在宏编辑器里查看 VBA 代码，从而了解大概使用什么方法。

2. 如果不知道从哪获得实现该功能的对象，则可以使用 word 宏编辑器的对象浏览器（Alt+F8 -> 点击编辑 -> 按下 F2）

3. [使用在线的 .NET API 浏览器](https://docs.microsoft.com/en-us/dotnet/api/microsoft.office.interop.word?view=word-pia) API，从而了解详细的语法

4. 使用 Python 的 IDLE 进行实时交互并快速调整, 然后输入自己想尝试的对象属性或方法。

   > 有些新功能宏可能不支持，但通过.NET API 的文档还是能解决



## 文档

[Open-XML-SDK](https://github.com/OfficeDev/Open-XML-SDK)
[Open-Xml-PowerTools](https://github.com/EricWhiteDev/Open-Xml-PowerTools)

[Word-OOXML中的尺寸单位 | 退思园 (gowa.club)](https://gowa.club/Java/Word-OOXML中的尺寸单位.html)



## 开源

[LatexPP](https://github.com/goldsborough/latexpp.git)

[MML2TEX](https://github.com/transpect/mml2tex.git)