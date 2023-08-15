---
title: 正方教务管理系统详细成绩获取的实例
date: 2023-08-15 22:25:35
cover: /2023/08/15/hziee-score-detail/cover.png
tags:
  - Vue
  - Element Plus
  - 前端
categories:
  - 前端
---
## 说明
- 本文的实例基于杭州电子科技大学信息工程学院教务系统编写，对于其它使用正方教务管理系统的学校教务可能并不完全适用，请具体情况具体分析
- 本文以及本文所提到的工具并不是杭州电子科技大学信息工程学院官方的工具，也不代表杭州电子科技大学信息工程学院的任何立场
## 原理
正方教务系统在点击查询成绩时会发出一个请求`xscjcx_dq.aspx`
，通过F12的网络中查看这个请求的载荷，我们会发现一个叫做`__VIEWSTATE`的数据  
![图1-1 请求负载](/2023/08/15/hziee-score-detail/1-1.png '图1-1 请求负载')
这串数据是一个Base64编码的字符串，它编码前的内容就是页面中显示成绩的表格，其中包括了姓名，学号等详细信息。  
解码后的文本存在许多乱码是因为除了数据之外它还包括了用于渲染表格时用的格式化字符  
我所做的就是去掉这些乱码，提取出有用的信息，提高可读性
![图1-2 Base64解码后](/2023/08/15/hziee-score-detail/1-2.png '图1-2 Base64解码后')

## 具体实现
### 信息处理
1. 首先初步过滤出正常字符串  
   这里我选择使用正则表达式`/[\u4e00-\u9fa5a-zA-Z0-9\-()（）.+\s]+(?=dd)/g`来提取解码后字符串中的有效信息:
    - `\u4e00-\u9fa5a-zA-Z0-9\`用来匹配中英文及数字字符串，筛选出科目名称、成绩、开课学院等
    - `-()（）.+\s`用来匹配连字符‘-’，中英文括号、小数点，筛选出课号，学分等
    - `+(?=dd)/g`有效信息后总是以**dd**结尾
   ```javascript JS代码实现
   function fliter(string) {
      let text = this.base64ToString(string)
      const regex = /[\u4e00-\u9fa5a-zA-Z0-9\-()（）.+\s]+(?=dd)/g;
      const result = text.match(regex).map(str => str.replace(/dd$/, ''));
              
      return result
   }
   ```
2. 格式化信息  
   使用正则表达式过滤出正常字符串后我们获得了一个数组，但是这个数组对于程序读取还是有点困难，因此我们要将它格式化成方便程序读取展示的数据
    - 遍历数组，使用正则表达式`/([\d-]+)-[a-zA-Z0-9-]+-\d+/`来判断某一门课程的数据是否已经结束~~正则真好用~~
    - 如果当前和正则匹配上，说明一门课程已经结束，则将当前数据之前的数据以数组的形式添加到结果数组中
    - 最后会将数据格式化成一个数组，数组的元素是每一门课的信息数组，方便遍历展示
   ```javascript JS代码实现
   function split(courseList) {
    const regex = /([\d-]+)-[a-zA-Z0-9-]+-\d+/;
    const result = [];
    let currentSection = [];

    for (let i = 0; i < courseList.length; i++) {
        const item = courseList[i];
        const match = item.match(regex);

        if (match) {
            if (currentSection.length > 0) {
                result.push(currentSection);
                currentSection = [];
            }
        }

        currentSection.push(item);
    }

    if (currentSection.length > 0) {
        result.push(currentSection);
    }

    this.resultList = result

    return result;

   }
   ```
### 信息展示
1. 经过格式化后的信息就可以传到前端页面让组件渲染了
   - 前端使用`v-for`配合Element Plus的Card组件进行成绩的展示
   ![图2-1 前端页面渲染](/2023/08/15/hziee-score-detail/2-1.png '图2-1 前端页面渲染')
   - 根据index值判断科目名称
   - 使用正则/`^(\d|\d\.\d|\.\d|\d\.)$/`判断是否为学分
   - 根据分数的下一个元素是否为开课学院判断是否为最终成绩
## 其它
- 项目源代码：[Github](https://github.com/ChiyukiRuon/hziee-score-detail)
- 项目地址：
  - [杭电信工详细成绩查询](https://scoredetail.chiyukiruon.com/)
  - [杭电信工详细成绩查询(备用)](https://scoredetail.chiyukiruon.top/)