---
layout: post
title: Python解析XML文件
categories: Blog
description: 用Python解析XML简单封装的接口。
keywords: Python，XML文件解析， lxml
---

### 需求说明：
在一个基于tornado的web服务中有一项需求：从xml文件读取内容并展示在界面，且界面可以修改内容并再次保存到文件，于是针对需求简单封装了一个小模块。

### 特点：
使用lxml模块，支持增删改查保存操作，增删改都是基于单项的操作，多项删除可以通过循环单项操作实现。

### 环境：
python3.7, lxml模块

### 示例代码：
```
# -*- coding:utf-8 -*-
from lxml import etree

class XMLParse:
    def __init__(self, config):
        parser = etree.XMLParser(remove_blank_text=True)
        self.root = etree.XML(config, parser)

    def __find_data(self, xpath, **kwargs):
        row_data = self.root.xpath(xpath, **kwargs)
        return row_data

    def add_data(self, xpath, data, param='Param', **kwargs):
        row_data = self.__find_data(xpath, **kwargs)
        if len(row_data) != 1:
            return False
        row_data[0].append(etree.Element(param))
        for k, v in data.items():
            row_data[0][-1].attrib[k] = v
        return True

    def delete_data(self, xpath, **kwargs):
        row_data = self.__find_data(xpath, **kwargs)
        if len(row_data) != 1:
            return False
        row_data[0].getparent().remove(row_data[0])
        return True

    def edit_data(self, xpath, data, **kwargs):
        row_data = self.__find_data(xpath, **kwargs)
        if len(row_data) != 1:
            return False
        for k, v in data.items():
            row_data[0].attrib[k] = v
        return True

    def find_data(self, xpath, **kwargs):
        row_data = self.__find_data(xpath, **kwargs)
        result = []
        if len(row_data) >= 1:
            for item in row_data:
                res = {}
                for k, v in item.attrib.items():
                    res[k] = v
                    result.append(res)
        else:
            result = None
        return result

      def save_data(self):
          return etree.tostring(self.root, encoding="utf-8", pretty_print=True)

def main():
    xml_string = """
    <Params>
    <Config>
      <Param key="Ip" value="192.168.100.99" desc=""/>
      <Param key="Port" value="10102" desc=""/>
      <Param key="New" value="10102" desc=""/>
    </Config>
    <Log>
      <Param key="Log" value="3" desc="取值范围1-5"/>
    </Log>
    </Params>"""
    a = XMLParse(xml_string)
    res = a.find_data('//Param')
    res1 = a.find_data('//Param[@key=$name]', name='Ip')
    res2 = a.add_data('//Config', {'key': 'test', 'value': 'test', 'desc': 'test'})
    res3 = a.edit_data('//Param[@key=$name]', {'key': 'Old', 'value': 'Old', 'desc': 'te'}, name='New')
    res4 = a.delete_data("//Param[@key=$name]", name='Del')
    res5 = a.save_data()
    print(res)
    print(res1)
    print(res2)
    print(res3)
    print(res4)
    print(res5)

if __name__ == '__main__':
    main()

```
