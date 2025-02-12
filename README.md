# 批量获取金融监管总局-行政处罚-板块的处罚信息
## 一、设计需求
因为工作需要统计`金融监管总局-行政处罚板块-监管局本级`有关人寿保险公司的行政处罚的详细处罚内容。

[金融监管局-监管本级处罚信息的网页地址](https://www.cbirc.gov.cn/cn/view/pages/ItemList.html?itemPId=923&itemId=4114&itemUrl=ItemListRightList.html&itemName=%E7%9B%91%E7%AE%A1%E5%B1%80%E6%9C%AC%E7%BA%A7&itemsubPId=931&itemsubPName=%E8%A1%8C%E6%94%BF%E5%A4%84%E7%BD%9A#1)

![image-20241203144528697](https://github.com/TonaSmith/AdminPenaltiesCollector/blob/master/assets/image-20241203144528697.png)

## 二、设计准备
1. 经观察，该网页均为超链接，且超链接文本均为"xxx行政处罚信息公开表" ；
2. 点击每个"xxx行政处罚信息公开表" 跳转进新页面，发现处罚信息均以表格形式展现；
3. 在表格上F12后发现，表格内容均存储在<table><td></td></tabel>标签中。

![image-20241203144714937](https://github.com/TonaSmith/AdminPenaltiesCollector/blob/master/assets/image-20241203144714937.png)

## 三、程序设计
1. 本程序分为两步：
- 第1步：使用Python将符合要求的内容保存在txt中。
- 第2步：使用Python遍历txt文件，并将内容保存在excel中。
2. 第1步：使用了以下几个Python库
- **os** - 用于与操作系统进行交互，比如检查文件路径是否存在、创建目录等。
- **BeautifulSoup (from bs4)** - 一个用于解析HTML和XML文档的库，这里用来从网页中提取表格数据。
- **random** - 用于生成随机数，这里用来在处理链接时引入随机延迟，以模拟人类操作并避免被网站识别为自动化脚本。
- **time** - 提供各种时间相关的函数，这里主要用来暂停程序执行一段时间。
- **selenium** - 一个Web测试框架，可以用来控制浏览器行为。这里用到了多个selenium模块：
   - `webdriver` - 用于启动浏览器实例，并通过它来执行各种操作。
   - `Service` - 用于管理WebDriver服务。
   - `By` - 用于指定元素定位方式（如XPath, CSS选择器等）。
   - `WebDriverWait` 和 `expected_conditions as EC` - 用于等待页面元素加载完成或满足特定条件。
   - `Options` - 用于设置ChromeDriver的各种选项。

**由于监管总局具有反爬机制，无法使用request进行批量操作，只能使用selenium调用GoogleDriver模拟操作**

3. 第2步：使用了pandas库
- 从指定路径读取文件，对于特定字段后仅保留一个制表符，删除多余制表符，并保存结果；
- 遍历指定目录下的所有.txt文件，并对每个文件应用clean_file函数，将清理后的文件保存到output_directory；
- 从指定目录下的所有cleaned .txt文件中读取数据，并创建一个Excel文件；
- 将'行政处罚决定书文号', '被处罚当事人', '主要违法违规事实','行政处罚依据', '行政处罚决定', '作出处罚决定的机关名称','作出处罚决定的日期'设置为excel表头；
- 将处理后txt文件内容保存在excel中。

**请注意：这里只处理了"监管本级"的相关内容，如需处理"总局机关"、"监管分局本级"，请替换链接**

## 四、程序运行

1. 先运行`监管处罚-本级处罚情况.py`
   - 输入要访问的目标网址
   - 输入要翻页的数量（默认从第1页开始）
   - 输入GoogleDriver的文件路径
2. 再运行`处罚 txt2excel .py`