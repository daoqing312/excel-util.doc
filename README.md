### Excel导入导出插件使用文档

> 面向开发人员

#### 简介

excel-util.jar包是基于EasyExcel的二次封装，满足公司各系统常用的excel导入导出的简化操作和自定义配置。更多底层api功能说明请查看，[EasyExcel官方文档](https://alibaba-easyexcel.github.io/index.html "官方文档")

#### 基础环境

##### Java

使用Jdk1.8作为开发SDK。

##### Maven

工具类包

```xml
  <dependency>
        <groupId>com.zejia.ms.bps</groupId>
        <artifactId>excel-util</artifactId>
        <version>1.0.0-SNAPSHOT</version>
  </dependency>
```

第三方依赖包

```xml

  <dependency>
      <groupId>cn.hutool</groupId>
      <artifactId>hutool-all</artifactId>
      <version>5.7.14</version>
      <scope>compile</scope>
  </dependency>
  <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>6.1.6.Final</version>
      <scope>compile</scope>
  </dependency>
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.16</version>
  </dependency>
```

##### 配置文件

导出分为两种方式，一种直接以流的方式输出页面下载，一种是写入到磁盘目录下。写入磁盘目录需要在引用此jar包的工程目录src路径下创建excel.conf文件，配置excel导出磁盘路径。

#### 使用说明

##### 包结构说明

excel-uitl目录结构描述

|----Help.md  使用说明文档

|----src/main/java

|----|----com.zejia.excel

|----|----|----converter 转换类

|----|----|----listener 读监听类

|----|----|----handler 写处理类

|----|----|----support 支持类

|----|----|----util 工具类

|----pom.xml

##### 主要类说明

###### ExcelRequest

自定义配置自定义参数的对象封装和默认值。

```java
/**
 * @intro 导入导出配置对象
 * @author zhaowb
 * @date 2022/02/07
 */
@Data
@Builder
@EqualsAndHashCode(callSuper = true)
@NoArgsConstructor
@AllArgsConstructor
@Comment("导入导出配置对象")
public class ExcelRequest extends BizPageRequest {

    @Comment(value = "sheet下标,默认0")
    private Integer sheetNo=0;

    @Comment(value = "sheet名称")
    private String sheetName;

    @Comment(value = "解析指定标题头行数，默认1")
    private Integer headRowNumber=1;

    @Comment(value = "导出忽略字段集")
    Set<String> excludeColumnFiledNames = new HashSet<String>();

    @Comment(value = "导出包括字段集")
    Set<String> includeColumnFiledNames = new HashSet<String>();

    @Comment(value = "是否读取全部sheet 0否 1是，默认0")
    private Integer readAllSheet=0;

    @Comment(value = "是否压缩 true是 false否")
    private Boolean isZip=false;

    @Comment(value = "是否删除临时文件 true是 false否")
    private Boolean isDelete=true;
  
}

```

###### ExcelUtils

说明：导入导出静态工具类，包含了导入导出多种场景的静态方法。一般场景推荐使用此工具类方法完成。

方法说明

1. 写入excel

   ```java
       /**
       * 写入excel
       * @param outputStream 输出流
       * @param excelRequest 自定义配置对象
       * @param clazz 表头类
       * @param datalist 表格数据
       */
       public static void writeExcel(OutputStream outputStream, ExcelRequest excelRequest, Class<?> clazz, List<?> datalist) {
           ...
       }

   ```
2. 写入excel

   ```java
       /**
       * 写入excel
       * @param outputStream 输出流
       * @param excelRequest 自定义配置对象
       * @param head 自定义表头
       * @param datalist 表格数据
       */
   public static void writeExcel(OutputStream outputStream, ExcelRequest excelRequest,List<List<String>> head, List<?> datalist) {
       ...
   } 
   ```
3. 写入excel

   ```java
    /**
     * 写入excel
     * @param filePath 文件路径
     * @param excelRequest 自定义配置对象
     * @param head 自定义表头
     * @param datalist 表格数据
     */
    public static void writeExcel(String filePath, ExcelRequest excelRequest,List<List<String>> head, List<?> datalist) {
        ...
    }
   ```
4. 写入excel

   ```java
   /**
     * 写入excel
     * @param filePath 文件路径
     * @param excelRequest 自定义配置对象
     * @param head 表头类
     * @param datalist 表格数据
     */
    public static void writeExcel(String filePath, ExcelRequest excelRequest, Class<?> head, List<?> datalist) {
        ...
    }
   ```
5. excel写入多sheet

   ```java
    /**
     * excel导出写入多sheet
     * @param filePath 文件路径
     * @param dataMap 表格数据
     * @param clazz 表格对象类
     * @param <T> 数据类型
     * @throws Exception
     */
    public static <T> void writeExcelManySheets(String filePath, Map<String, List<T>> dataMap, Class<T> clazz) throws Exception {
       ...
    }
   ```
6. excel写入输出流

   ```Java
    /**
     * excel写入输出流
     * @param outputStream 输出流
     * @param dataList 表格数据
     * @param sheetName 表格名称
     * @param clazz 表格对象类
     * @param <T> 数据类型
     * @throws Exception
     */
    public static <T> void writeExcelDownload(OutputStream outputStream, List<T> dataList, String sheetName, Class<T> clazz) throws Exception{
       ...
    }  
   ```
7. excel写入输出流异常返回信息

   ```Java
   /**
     * excel写入输出流
     * @param response HttpServletResponse
     * @param fileName 文件名
     * @param dataList 表格数据
     * @param excelRequest 自定义配置
     * @param <T> 数据类型
     * @throws Exception
     */
    public static <T> void writeExcelWeb(HttpServletResponse response,String fileName, ExcelRequest excelRequest,List<List<String>> headList,List<T> dataList) throws Exception{
        ...
    }
   ```
8. 导出数据写入多个sheet

   ```Java
    /**
     * 导出数据写入多个sheet
     * @param outputStream 输出流
     * @param dataMap 导出数据
     * @param clazz 表头类
     * @param <T> 数据类型
     * @throws Exception
     */
    public static <T> void writeExcelMultiSheets(OutputStream outputStream, Map<String, List<T>> dataMap, Class<T> clazz) throws Exception {
       ...
    }
   ```
9. 多页数据写入单sheet

   ```Java
    /**
     * 多页数据写入多sheet
     *
     * @param fileName  导出文件名称
     * @param sheetName 导出sheet名称
     * @param clazz     excel对象模型
     * @param pageRequest   查询参数
     * @param pageInvokeHandler  分页查询方法
     * @return excel文件
     */
    public static <T> void writePagesData2ExcelOneSheet(String fileName, String sheetName, ExcelRequest excelRequest
            , IPageInvokeHandler pageInvokeHandler, Class<T> clazz, List<List<String>> heads) {
      ...
    }
   ```
10. 多页数据写入多sheet

    ```Java
    /**
     * 多页数据写入多sheet
     *
     * @param fileName  导出文件名称
     * @param sheetName 导出sheet名称
     * @param clazz     excel对象模型
     * @param pageRequest   查询参数
     * @param pageInvokeHandler  分页查询方法
     * @return excel文件
     */
    public static <T> void writePagesData2ExcelMultiSheet(String fileName, String sheetName, BizPageRequest pageRequest
            ,IPageInvokeHandler pageInvokeHandler, Class<T> clazz, List<List<String>> heads) {
      ...
    }
    ```
11. 分页数据写入多个文件后ZIP压缩

    ```java
    /**
     * 分页数据写入多个文件后ZIP压缩
     * @param zipFileName 压缩文件名
     * @param sheetName 表格名称
     * @param pageRequest 分页参数对象
     * @param pageInvokeHandler  分页查询方法
     * @param clazz 表头类
     * @param heads 自定义表头
     */
    public static <T> void writePagesData2MultiFileAndZip(String zipFileName,String sheetName,BizPageRequest pageRequest,IPageInvokeHandler pageInvokeHandler, Class<T> clazz, List<List<String>> heads) {
      ...
    }
    ```
12. 模板导出

    ```Java
    /**
    * 模板导出
    * @param outputStream 输出流
    * @param templateName 模板路径
    * @param head 表头
    * @param datalist 表数据
    */
    public static void fillExcelWithTemplate(OutputStream outputStream,String templateName, Class<?> head, List<?> datalist) {
        ...
    }
    ```
13. 读取excel

    ```Java
     /**
     * 读取excel
     * @param inputStream
     * @param clazz 表头类
     * @param listener 读取监听类
     * @param excelRequest 自定义配置对象
     * @param converters 内容转换器
     */
    public static void readExcel(InputStream inputStream, Class clazz, BatchImportListener listener, ExcelRequest excelRequest, Converter ...converters){
        ...
    }
    ```
14. 读取excel

    ```java
    /**
     * 读取excel
     * @param file
     * @param head 表头类
     * @param excelRequest 自定义配置对象
     */
    public static void readExcel(File file, Class head,ExcelRequest excelRequest) throws FileNotFoundException {
        ...
    }
    ```
15. 读取无模型excel数据

    ```java
    /**
     * 读取excel,无表头模型
     * @param inputStream
     * @param excelRequest 自定义配置对象
     */
    public static void readExcel(InputStream inputStream,ExcelRequest excelRequest){
       ...
    }
    ```
16. 读取无模型excel数据

    ```java
    /**
     * 读取excel,无标题模型
     * @param filePath 文件路径
     * @param excelRequest 自定义配置对象
     */
    public static void readExcel(String filePath,ExcelRequest excelRequest) throws FileNotFoundException {
        ...
    }
    ```
17. 读取无模型excel数据

    ```java
    /**
     * 读取excel,无表头模型
     * @param file 文件
     * @param excelRequest 自定义配置对象
     */
    public static void readExcel(File file,ExcelRequest excelRequest) throws FileNotFoundException {
        ...
    }
    ```

###### ExcelReadHelper

说明：excel读取类，实例化后可以进行更灵活配置。优先推荐使用ExcelUtils工具类的静态方法。

###### ExcelWriteHelper

说明：excel写入类，实例化后可以进行更灵活配置。优先推荐使用ExcelUtils工具类的静态方法。

###### FileUtils

说明：文件工具类，包含写入excel路径生成、文件随机名称生成、ZIP压缩等方法。具体参看代码。

###### StyleUtils

说明：样式工具类，可自定义常用头和内容样式，方便统一设置。

###### excel读监听器

在读取excel解析时可以传入监听器接口ReadListener的实现类，对每一行数据进行自定义处理，异常处理等。一般处理不满足时可自定义监听器类，常用监听器实现类如下：

***BatchImportListener***

说明：excel读取监听器，每解析一行会回调invoke()方法,整个excel解析结束会执行doAfterAllAnalysed()方法。构造方法传入回调函数接口，方便对读取后的数据进行日志打印或持久化处理。

---

***HibernateValidateListener***

说明：excel读取数据校验监听器，可以使用后端常用的hibernate-validator注解。

###### excel读写转换器类

说明：excel单元格内容的读入和写入转换处理类。常用的如日期转换类、自定义格式转换类等。如需自定义,可实现Converter接口。参考实现：CustomStringConverter、CustomDateConverter等。

###### excel写处理类

说明：写的处理器。可以实现WorkbookWriteHandler,SheetWriteHandler,RowWriteHandler,CellWriteHandler，在写入excel的不同阶段会调用excel单元格内容的写入内容样式处理类

#### Demo工程

excel-demo是基于springboot2创建的导入导出示例代码。用代码示例说明比语言描述更直接，也对开发更友好。如果使用此工具包，建议查看示例和单元测试，以便快速了解不同场景的使用方式。

#### 写入示例

 1. 示例一

 ```java
    /**
     * 写入excel
     */
    @Test
    public void testHead(){
        //创建文件
        String filePath= FileUtils.generateFilePath("header测试.xlsx");
        try {
            ExcelRequest excelRequest=new ExcelRequest();
            excelRequest.setSheetName("header测试");
            ExcelUtils.writeExcel(filePath,excelRequest,PurchaseExecutionData.class,new ArrayList<>());
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

 ```

2. 示例二

```java
 /**
     * 样式测试
     * @throws FileNotFoundException
     */
    @Test
    public void testWrite() throws FileNotFoundException {
        String filePath = FileUtils.generateFilePath("样式测试.xlsx");
        System.out.println(filePath);
        // 输出流
        OutputStream outputStream = new FileOutputStream(new File(filePath));
        // 导出的数据
        List<List<Object>> dataList = new ArrayList<>();
        for (int i = 0; i < 10; i++) {
            List<Object> temp=ListUtil.toList(i+20,"vo" + i,"school" + i);
            dataList.add(temp);
        }
        // 标题
        List<List<String>> headList = new ArrayList<>();
        headList.add(ListUtil.of("姓名"));
        headList.add(ListUtil.of("年龄"));
        headList.add(ListUtil.of("学校"));

        String sheetName = "导出文件";
        ExcelRequest excelRequest=new ExcelRequest();
        excelRequest.setSheetName(sheetName);

        List<Integer> columnIndexes = Arrays.asList(0,1,2);
        List<Integer> rowIndexes = Arrays.asList(0);
        HeadColorSheetWriteHandler titleColorSheetWriteHandler = new HeadColorSheetWriteHandler(rowIndexes, columnIndexes, IndexedColors.RED.index);

        List<Integer> columnIndexes1 = Arrays.asList(0,1);
        List<Integer> rowIndexes1 = Arrays.asList(1,2,3,4);
        CellColorSheetWriteHandler colorSheetWriteHandler = new CellColorSheetWriteHandler(rowIndexes1, columnIndexes1, IndexedColors.RED.index);

        excelRequest.addWriteHandler(titleColorSheetWriteHandler).addWriteHandler(colorSheetWriteHandler);
        ExcelUtils.writeExcel(outputStream,excelRequest,headList,dataList);

    }

```

3. 示例三
```java

    /**
     * 测试模板写入
     */
    @Test
    public void testDemoDataTemplate(){
        List<DemoData> list = ListUtils.newArrayList();
        for (int i = 0; i < 10; i++) {
            DemoData data = new DemoData();
            data.setId(IdUtil.fastSimpleUUID());
            data.setString("字符串" + i);
            data.setDate(new Date());
            data.setDoubleData(0.56 * i);
            list.add(data);
        }
        String filePath = FileUtils.getConfigPath()+ "templateFill" + System.currentTimeMillis() + ".xlsx";
        System.out.println(filePath);
        String templatePath=WriteTest.class.getClassLoader().getResource("demoDataTemplate.xlsx").getPath();
        System.out.println(templatePath);
        try {
            OutputStream outputStream=new FileOutputStream(filePath);
            ExcelUtils.fillExcelWithTemplate(outputStream,templatePath,DemoData.class,list);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }

    }

```
4. 示例五
```java
/**
     * 图片写入
     * @throws Exception
     */
    @Test
    public void imageWrite() throws Exception {
        String fileName = FileUtils.getConfigPath() + "imageWrite" + System.currentTimeMillis() + ".xlsx";
        // 如果使用流 记得关闭
        InputStream inputStream = null;
        try {
            List<ImageData> list = new ArrayList<ImageData>();
            ImageData imageData = new ImageData();
            list.add(imageData);
            String imagePath = FileUtils.getConfigPath() + "img.png";
            System.out.println(imagePath);
            if(!FileUtil.exist(imagePath)){
                throw new BizException(404,"图片文件不存在");
            }
            // 放入四种类型的图片 实际使用只要选一种即可
            imageData.setByteArray(FileUtil.readBytes(imagePath));
            imageData.setFile(new File(imagePath));
            imageData.setString(imagePath);
            inputStream = new FileInputStream(imagePath);
            imageData.setInputStream(inputStream);
            EasyExcel.write(fileName, ImageData.class).sheet().doWrite(list);
        } finally {
            if (inputStream != null) {
                inputStream.close();
            }
        }
    }

```


#### 关于EasyExcel

##### 注解说明

| 字段注解                | 类注解                          |
| :---------------------- | ------------------------------- |
| @ColumnWith(列宽)       | @ColumnWidth(全局列宽)          |
| @ExcelProperty(字段配置 | @HeadFontStyle(头样式)          |
|                         | @HeadRowHeight(标题高度)        |
|                         | @ContentFontStyle(内容字体样式) |
|                         | @ContentRowHeight(内容高度)     |

###### **@ExcelProperty**

表格字段，必要的一个注解，注解中有三个参数 `value`,`index`,`converter`分别代表列明，列序号，数据转换方式
`value`和 `index`只能二选一，通常不用设置 `converter`
1.value 通过标题文本对应
2.index 通过文本行号对应
3.converter 转换器，通常入库和出库转换使用，如性别入库0和1，出库男和女

最佳实践

```java
public class ImeiEncrypt {
    @ExcelProperty(value = "值")
    private String valueField;

    @ExcelProperty(value = 1,converter =IndustryIdConverter.class)
    private String indexField;

    @ExcelProperty(value = "值对应和转换器",converter =IndustryIdConverter.class)
    private String valueAndConverterField;
}

```

###### **@ColumnWith**

设置列宽度,只有一个参数value，value的单位是字符长度，最大可以设置255个字符，因为一个excel单元格最大可以写入的字符个数就是255个字符。

最佳实践

```Java
public class ImeiEncrypt {
    @ColumnWidth(value = 18)
    private String imei;
}

```

###### **@ContentFontStyle**

文本字体样式，用于设置单元格内容字体格式的注解

参数：

| 参数               | 含义               |
| ------------------ | ------------------ |
| fontName           | 字体名称           |
| fontHeightInPoints | 字体高度           |
| italic             | 是否斜体           |
| strikeout          | 是否设置删除水平线 |
| color              | 字体颜色           |
| typeOffset         | 偏移量             |
| underline          | 下划线             |
| bold               | 是否加粗           |
| charset            | 编码格式           |

###### @ContentLoopMerge

用于设置合并单元格的注解

参数：

| 参数         | 含义 |
| ------------ | ---- |
| eachRow      |      |
| columnExtend |      |

###### @ContentRowHeight

用于设置行高

参数：

| 参数  | 含义                     |
| ----- | ------------------------ |
| value | 行高，`-1`代表自动行高 |

###### @ContentStyle

设置内容格式注解

参数：

| 参数                | 含义                                                                                                    |
| ------------------- | ------------------------------------------------------------------------------------------------------- |
| dataFormat          | 日期格式                                                                                                |
| hidden              | 设置单元格使用此样式隐藏                                                                                |
| locked              | 设置单元格使用此样式锁定                                                                                |
| quotePrefix         | 在单元格前面增加`符号，数字或公式将以字符串形式展示                                                     |
| horizontalAlignment | 设置是否水平居中                                                                                        |
| wrapped             | 设置文本是否应换行。将此标志设置为 `true`通过在多行上显示使单元格中的所有内容可见                     |
| verticalAlignment   | 设置是否垂直居中                                                                                        |
| rotation            | 设置单元格中文本旋转角度。03版本的Excel旋转角度区间为-90°~90°，07版本的Excel旋转角度区间为0°~ 180° |
| indent              | 设置单元格中缩进文本的空格数                                                                            |
| borderLeft          | 设置左边框的样式                                                                                        |
| borderRight         | 设置右边框样式                                                                                          |
| borderTop           | 设置上边框样式                                                                                          |
| borderBottom        | 设置下边框样式                                                                                          |
| leftBorderColor     | 设置左边框颜色                                                                                          |
| rightBorderColor    | 设置右边框颜色                                                                                          |
| topBorderColor      | 设置上边框颜色                                                                                          |
| bottomBorderColor   | 设置下边框颜色                                                                                          |
| fillPatternType     | 设置填充类型                                                                                            |
| fillBackgroundColor | 设置背景色                                                                                              |
| fillForegroundColor | 设置前景色                                                                                              |
| shrinkToFit         | 设置自动单元格自动大小                                                                                  |

###### @HeadFontStyle

用于定制标题字体格式

| 参数               | 含义             |
| ------------------ | ---------------- |
| fontName           | 设置字体名称     |
| fontHeightInPoints | 设置字体高度     |
| italic             | 设置字体是否斜体 |
| strikeout          | 是否设置删除线   |
| color              | 设置字体颜色     |
| typeOffset         | 设置偏移量       |
| underline          | 设置下划线       |
| charset            | 设置字体编码     |
| bold               | 设置字体是否加粗 |

###### @HeadRowHeight

设置标题行行高

| 参数  | 含义                     |
| ----- | ------------------------ |
| value | 设置行高，-1代表自动行高 |

###### @HeadStyle

设置标题样式

| 参数                | 含义                                                                                                    |
| ------------------- | ------------------------------------------------------------------------------------------------------- |
| dataFormat          | 日期格式                                                                                                |
| hidden              | 设置单元格使用此样式隐藏                                                                                |
| locked              | 设置单元格使用此样式锁定                                                                                |
| quotePrefix         | 在单元格前面增加`符号，数字或公式将以字符串形式展示                                                     |
| horizontalAlignment | 设置是否水平居中                                                                                        |
| wrapped             | 设置文本是否应换行。将此标志设置为 `true`通过在多行上显示使单元格中的所有内容可见                     |
| verticalAlignment   | 设置是否垂直居中                                                                                        |
| rotation            | 设置单元格中文本旋转角度。03版本的Excel旋转角度区间为-90°~90°，07版本的Excel旋转角度区间为0°~ 180° |
| indent              | 设置单元格中缩进文本的空格数                                                                            |
| borderLeft          | 设置左边框的样式                                                                                        |
| borderRight         | 设置右边框样式                                                                                          |
| borderTop           | 设置上边框样式                                                                                          |
| borderBottom        | 设置下边框样式                                                                                          |
| leftBorderColor     | 设置左边框颜色                                                                                          |
| rightBorderColor    | 设置右边框颜色                                                                                          |
| topBorderColor      | 设置上边框颜色                                                                                          |
| bottomBorderColor   | 设置下边框颜色                                                                                          |
| fillPatternType     | 设置填充类型                                                                                            |
| fillBackgroundColor | 设置背景色                                                                                              |
| fillForegroundColor | 设置前景色                                                                                              |
| shrinkToFit         | 设置自动单元格自动大小                                                                                  |

###### @ExcelIgnore

默认所有字段都会和excel去匹配，加了这个注解会忽略该字段不将该字段转换成Excel

###### @ExcelIgnoreUnannotated

默认不加ExcelProperty 的注解的都会参与读写，加了不会参与没有注解的字段都不转

###### @DateTimeFormat

日期转换，用String去接收excel日期格式的数据会调用这个注解。里面的value参照java.text.SimpleDateFormat

###### @NumberFormat

数字转换，用String去接收excel数字格式的数据会调用这个注解。里面的value参照java.text.DecimalFormat

##### 常用Api

EasyExcel 入口类，用于构建开始各种操作

ExcelReaderBuilder ExcelWriterBuilder 构建出一个 ReadWorkbook WriteWorkbook，可以理解成一个excel对象，一个excel只要构建一个

ExcelReaderSheetBuilder ExcelWriterSheetBuilder 构建出一个 ReadSheet WriteSheet对象，可以理解成excel里面的一页,每一页都要构建一个

ReadListener 在每一行读取完毕后都会调用ReadListener来处理数据

WriteHandler 在每一个操作包括创建单元格、创建表格等都会调用WriteHandler来处理数据

所有配置都是继承的，Workbook的配置会被Sheet继承，所以在用EasyExcel设置参数的时候，在EasyExcel…sheet()方法之前作用域是整个sheet,之后针对单个sheet

###### 读

**注解**

ExcelProperty 指定当前字段对应excel中的那一列。可以根据名字或者Index去匹配。当然也可以不写，默认第一个字段就是index=0，以此类推。千万注意，要么全部不写，要么全部用index，要么全部用名字去匹配。千万别三个混着用，除非你非常了解源代码中三个混着用怎么去排序的。

Ø ExcelIgnore 默认所有字段都会和excel去匹配，加了这个注解会忽略该字段

Ø DateTimeFormat 日期转换，用String去接收excel日期格式的数据会调用这个注解。里面的value参照java.text.SimpleDateFormat

Ø NumberFormat 数字转换，用String去接收excel数字格式的数据会调用这个注解。里面的value参照java.text.DecimalFormat

Ø ExcelIgnoreUnannotated 默认不加ExcelProperty 的注解的都会参与读写，加了不会参与

**参数**

Ø converter 转换器，默认加载了很多转换器。也可以自定义。

Ø readListener 监听器，在读取数据的过程中会不断的调用监听器。

Ø headRowNumber 需要读的表格有几行头数据。默认有一行头，也就是认为第二行开始起为数据。

Ø head 与clazz二选一。读取文件头对应的列表，会根据列表匹配数据，建议使用class。

Ø clazz 与head二选一。读取文件的头对应的class，也可以使用注解。如果两个都不指定，则会读取全部数据。

Ø autoTrim 字符串、表头等数据自动trim

Ø password 读的时候是否需要使用密码

ReadWorkbook（理解成excel对象）参数
Ø excelType 当前excel的类型 默认会自动判断

Ø inputStream 与file二选一。读取文件的流，如果接收到的是流就只用，不用流建议使用file参数。因为使用了inputStream easyexcel会帮忙创建临时文件，最终还是file

Ø file 与inputStream二选一。读取文件的文件。

Ø autoCloseStream 自动关闭流。

Ø readCache 默认小于5M用 内存，超过5M会使用 EhCache,这里不建议使用这个参数。

ReadSheet（就是excel的一个Sheet）参数
Ø sheetNo 需要读取Sheet的编码，建议使用这个来指定读取哪个Sheet

Ø sheetName 根据名字去匹配Sheet,excel 2003不支持根据名字去匹配

###### 写

**注解**

Ø ExcelProperty index 指定写到第几列，默认根据成员变量排序。value指定写入的名称，默认成员变量的名字，多个value可以参照快速开始中的复杂头

Ø ExcelIgnore 默认所有字段都会写入excel，这个注解会忽略这个字段

Ø DateTimeFormat 日期转换，将Date写到excel会调用这个注解。里面的value参照java.text.SimpleDateFormat

Ø NumberFormat 数字转换，用Number写excel会调用这个注解。里面的value参照java.text.DecimalFormat

Ø ExcelIgnoreUnannotated 默认不加ExcelProperty 的注解的都会参与读写，加了不会参与

**通用参数**

Ø WriteWorkbook,WriteSheet ,WriteTable都会有的参数，如果为空，默认使用上级。

Ø converter 转换器，默认加载了很多转换器。也可以自定义。

Ø writeHandler 写的处理器。可以实现WorkbookWriteHandler,SheetWriteHandler,RowWriteHandler,CellWriteHandler，在写入excel的不同阶段会调用

Ø relativeHeadRowIndex 距离多少行后开始。也就是开头空几行

Ø needHead 是否导出头

Ø head 与clazz二选一。写入文件的头列表，建议使用class。

Ø clazz 与head二选一。写入文件的头对应的class，也可以使用注解。

Ø autoTrim 字符串、表头等数据自动trim

**WriteWorkbook（理解成excel对象）参数**
Ø excelType 当前excel的类型 默认xlsx

Ø outputStream 与file二选一。写入文件的流

Ø file 与outputStream二选一。写入的文件

Ø templateInputStream 模板的文件流

Ø templateFile 模板文件

Ø autoCloseStream 自动关闭流。

Ø password 写的时候是否需要使用密码

Ø useDefaultStyle 写的时候是否是使用默认头

**WriteSheet（就是excel的一个Sheet）参数**
Ø sheetNo 需要写入的编码。默认0

Ø sheetName 需要写的Sheet名称，默认同sheetNo

**WriteTable（就把excel的一个Sheet的一块区域看一个table）参数**
Ø tableNo 需要写入的编码。默认0 参数
Ø ReadWorkbook,ReadSheet 都会有的参数，如果为空，默认使用上级。
