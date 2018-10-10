---
layout: post
title:  "Mybatis 逆向工程"
date:   2015-04-26 23:45:00
categories: MyBatis Reverse
tags: MyBatis Reverse
---

* content
{:toc}

## MyBatis 逆向工程

### 简介
MyBatis官方提供逆向工程，可以实现由数据库表生成mapper.xml、mapper.java、po类 及相关类。

[mybatis逆向工程开发文档](http://www.mybatis.org/generator/configreference/xmlconfig.html)

### 方式一：程序实现逆向

1. 准备jar包
mybatis-generator-core-1.3.2.jar 
数据库驱动包

2. 准备逆向工程配置文件genreatorConfig.xml
注意需要配置：
数据库连接参数
Po类存放位置
Mapper映射文件和mapper接口的存放位置
配置表名


3. 执行程序类

```
import java.awt.geom.GeneralPath;
import java.awt.im.InputContext;
import java.io.File;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.log4j.chainsaw.Main;
import org.apache.log4j.lf5.util.Resource;
import org.mybatis.generator.api.MyBatisGenerator;
import org.mybatis.generator.config.Configuration;
import org.mybatis.generator.config.xml.ConfigurationParser;
import org.mybatis.generator.internal.DefaultShellCallback;

public class MybatisGen {
    public static void generator() throws Exception{
           List<String> warnings = new ArrayList<String>();
           boolean overwrite = true;
           File configFile = new File("generatorConfig.xml");
           ConfigurationParser cp = new ConfigurationParser(warnings);
           Configuration config = cp.parseConfiguration(configFile);
           DefaultShellCallback callback = new DefaultShellCallback(overwrite);
           MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
           myBatisGenerator.generate(null);
    }
    public static void main(String[] args) {
        try {
            generator();
        } catch (Exception e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
    }
}
```

***

### 方式二：Eclipse插件实现逆向
第一步：如果你的eclipse中没有安装mybatis插件，需要先安装mybatis插件
第二步：下载完插件后，解压为以下两个文件
第三步：找到eclipse的安装目录，将以上两个文件，拷贝到eclipse的安装目录中
如果安装目录中已经存在这两个文件，则把这两个文件进行合并即可。

第四步：重新启动eclipse,点击'New--other'，即可找到mybatis在eclipse中的插件
第五步：选中Mybatis Generator Configuration File 点击 next 创建配置文件信息
选择要将文件创建的位置，点击 finish 

第六步：在创建的文件中配置响应的信息，即可
第七步：逆向生成代码generatorConfig.xml右键Generator Mybatis/iBATIS Artfacts
