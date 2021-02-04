title: WX - 微信原生转化Uni-App
author: Aliang
tags: []
categories: [小程序]
date: 2021-01-28 17:00:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410300/%E5%B0%8F%E7%A8%8B%E5%BA%8F/5_bwa9fk.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410300/%E5%B0%8F%E7%A8%8B%E5%BA%8F/5_bwa9fk.png
---


#### 一、它是谁？
【miniprogram-to-uniapp】转换微信小程序”项目为uni-app项目。
原则上混淆过的项目，也可以进转换，因为关键字丢失，不一定会完美。

#### 二、它的原理是什么？
最初是学了半节课堆和栈，觉得词法分析挺有意思的，再加入转换小程序插件时，发现这些繁琐的操作完全可以使用程序来完成。
核心是使用Babel获取AST（词法分析)，然后或使用Babel自带函数增删，或正则分析替换等等操作。
可能有的朋友觉得，这种为啥不是纯正则分析？ 纯正则可以解决一部分问题，对于标签与标签内容，其实正则是很难进行区分的哈。

#### 三、它能做哪些事情？
支持无云开发的小程序项目转换为uni-app项目
支持有云开发的小程序项目转换为uni-app项目(cloudfunctions目录将被忽略，uni-app结合小程序云开发见：使用uni-app进行微信小程序云开发经验分享)
支持解析TypeScript小程序项目
支持解析使用npm模块的小程序项目
支持解析include标签
支持解析template标签
支持解析Behavior文件为mixins文件
支持.js', .wxml和*.wxss文件进行相应转换，并做了大量的优化
支持识别App、Page、Component、VantComponent、Behavior和纯Javascript文件的转换
使用jyf-parser替换wxParse(感谢网友 “爱瑞巴勒康忙北鼻” 的建议)
搜索未在data声明，而直接在setData()里使用的变量，并修复
合并使用require导入的wxs文件
因uni-app会将所有非static目录的资源文件删除，因此将所有资源文件移入static目录，并修复所有能修复到的路径
修复变量名与函数重名的情况(目前uni编译时会将非static目录的文件复制一份到static目录，但并不完全，因此本功能仍保留)
支持wxs文件转换，可以通过参数配置(-w)，默认为false(目前uni-app已支持wxs，不再推荐转换wxs)
支持vue-cli模式，可以通过参数配置(-c)，默认为false，即生成为vue-cli项目，转换完成需运行npm -i安装包，然后再导入hbuilder x里开发(建议爱折腾人士使用)
支持vant转换，可以通过参数配置(-z)，默认为false：自动识别（无须添加参数，工具已支持自动识别vant项目），如果需要转换使用vant-weapp组件的小程序项目，必须配置这个参数，否则转换后有问题。（另外，转换后的项目，目前仅支持v3和h5两个平台）
支持wx.xxx()转换为uni.xxx()，可以通过参数配置(-r)，默认为false（因uni已经对wx相关函数做了兼容，但仍有很多朋友有此需求，特作为可配置项，按需自取）
#### 四、它还有哪些不支持转换？
不支持替换：wxaSortPicker
setData的变量是props里定义的
抽象节点：componentGenerics
语法错误(原始代码就有语法错误，请仔细检查，工具无法帮助你将所有代码都修复)
更多请参见miniprogram to uniapp 工具答疑
#### 五、怎么使用？
##### 第一步
在命令行里，运行【 npm install miniprogram-to-uniapp -g 】进行安装，因为这个包是工具，要求全局都能使用，所以需要-g进行全局安装。
如果运行npm报错，请先安装Node.js，下载地址：https://nodejs.org/zh-cn/
 
![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410294/%E5%B0%8F%E7%A8%8B%E5%BA%8F/1_jvieea.png)

##### 第二步
继续在命令行里，运行【 wtu -V 】，执行结果如下：

![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410295/%E5%B0%8F%E7%A8%8B%E5%BA%8F/2_imxt7p.png)
 

显示版本号，说明已经安装成功了。（wtu -> 取自wx to uni之意，后面都用这个全局命令）

##### 第三步
在命令行里，输入【wtu -i "你的小程序项目路径"】
注意 -i 前面和后面都有空格！！！
注意 -i 前面和后面都有空格！！！
注意 -i 前面和后面都有空格！！！
如：【wtu -i "E:\zpWork\Project_self\miniprogram-to-uniapp\test\test-wx-to-uni"】 ，回车后即可以在源项目同及目录得到一个后缀为_uni的目录，即转换成功。

转换前：

![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410297/%E5%B0%8F%E7%A8%8B%E5%BA%8F/3_rrzqx9.png)
 

转换后：

![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410299/%E5%B0%8F%E7%A8%8B%E5%BA%8F/4_l1gfdc.png)
 

转换后的项目文件对比（左边是小程序项目，右边是Uni-app项目目录）：


![](https://res.cloudinary.com/dt3vcmqdt/image/upload/v1612410300/%E5%B0%8F%E7%A8%8B%E5%BA%8F/5_bwa9fk.png)

##### 第四步
将转换后的xxx_uni项目导入到hbuilder X，
点击菜单 运行--> 运行到小程序模拟器-->微信开发者工具！（如果是使用了vant的项目，请运行到H5，vant项目转换后仅支持H5和app）
点击菜单 运行--> 运行到小程序模拟器-->微信开发者工具！（如果是使用了vant的项目，请运行到H5，vant项目转换后仅支持H5和app）
点击菜单 运行--> 运行到小程序模拟器-->微信开发者工具！（如果是使用了vant的项目，请运行到H5，vant项目转换后仅支持H5和app）
重要的话说三遍！
然后查看转换后的项目运行到小程序，是否可以正常运行无报错！
(因为这种转换非100%，所以至少需要保证 小程序-->uniapp-->小程序仍然能正常运行，再考虑运行到其他小程序或app)，
如有报错，请根据miniprogram to uniapp 工具答疑 进行修改，保证无报错，然后再运行到其他平台。

工具升级#
因为工具更新比较频繁，安装后，可以使用如下命令进行升级：
npm update miniprogram-to-uniapp -g

原文链接：https://ask.dcloud.net.cn/article/36037
