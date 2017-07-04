---
layout: default
---
[返回文档目录](../)

## 20170702周刊分享页迭代需求文档

> 注：此版本所有web页面，暂时不添加相关文章推荐。

### 1. 单行本文章分享页

从APP中可向第三方平台分享单行本文章，单行本文章分付费文章，以及后台勾选试读的文章。

付费文章：从cms中可配置文章可免费阅读的次数，以及单篇付费的价格。付费单行本文章前XXX次浏览（分享页的浏览量，非此文章中的浏览量）免费。达到浏览次数之后，只向用户展示随机一段内容，需付费后才可阅读。

试读文章：所有用户均可任意次数浏览，同时页面包含向单行本介绍页的引导浮层。

#### 1.1 未达到免费次数

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|文章配图||
|作者头像、作者名称||
|单行本名称||
|免费阅读次数，已阅读次数||
|文章标题摘要||
|文章正文||
|精选评论||
|周刊会员宣传文案||
|打开APP引导||

&emsp;&emsp;  

设计稿：

[单行本文章分享-未达到免费次数](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/595afc3126ccd5b0184a828f)

#### 1.2 达到免费次数，未付费

达到免费次数时，对于因为可支付后查看全文，对于其他平台包括手机浏览器，无支付选项。

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|文章配图||
|作者头像、作者名称||
|单行本名称||
|文章标题摘要||
|阅读次数已被抢完||
|支付按钮及已支付人数|微信以外环境无|
|文章随机一段正文||
|精选评论||
|周刊会员宣传文案||
|打开APP引导||

&emsp;&emsp;  

设计稿：

[单行本文章分享-达到免费次数-微信](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/595afc2ffbf77bcf07b1f078)

[单行本文章分享-达到免费次数-非微信](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/595afc301841702302a1da5d)


#### 1.3 达到免费次数，已付费

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|文章配图||
|作者头像、作者名称||
|单行本名称||
|文章标题摘要||
|文章正文||
|精选评论||
|周刊会员宣传文案||
|打开APP引导||


### 2. 试读文章分享页

CMS中标记试读的文章，分享出去的样式如下：

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|文章配图||
|作者头像、作者名称||
|单行本名称||
|文章标题摘要||
|文章正文||
|精选评论||
|周刊会员宣传文案||
|打开APP引导||
|底部浮层|点击跳转单行本介绍页|

&emsp;&emsp;  

设计稿：

[试读文章分享-底部浮层](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/5955be18e8f435965a439b2a)


[试读文章分享-详情](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/5955be1c44bf569c2c71af49)



### 3. 单行本介绍页

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|单行本封面|不可点击|
|单行本背景|不可点击|
|单行本标题|不可点击|
|单行本一句话简介|不可点击|
|单行本介绍|不可点击|
|推荐语|不可点击|
|作者介绍|不可点击|
|关键词|不可点击|
|订阅须知|不可点击|
|最新文章|最新四篇文章标题及摘要，若无文章则不显示此块，不可点击|
|试读按钮|点击进入试读列表|
|打开APP购买|唤醒或下载APP|

&emsp;&emsp;  

设计稿：

[试读文章分享-底部浮层](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/5955be19e8f435965a439bb5)

### 4. 试读列表

元素包括：

|元素类型|说明|
|:---:| :----------- |
|顶部打开APP引导|IOS：点击唤醒APP（已安装），或跳转应用宝链接下载（未安装）；Android：可尝试唤醒，若不行则跳转应用宝链接|
|文章配图||
|文章标题||
|文章发布时间||
|文章阅读时长||
|喜欢人数||
|点击跳转|跳转试读文章分享页，见第2节|

&emsp;&emsp;  

设计稿：

[试读文章列表](https://app.zeplin.io/project/57fe0db25a50ccf660d5bd6d/screen/5955be1f5f54dc862cbe642a)