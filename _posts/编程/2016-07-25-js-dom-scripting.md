---
layout: post
title: JavaScript读书笔记
categories: [编程]
tags: [Django REST]
disqus: y
---
###基础知识
* 全局变量和局部变量：var声明局部变量
* DOM
    1. D：文档
    2. O：对象
    3. M：模型/Map地图
* JavaScript里的对象：
    1. 用户定义兑现
    2. 内建对象，Array、Math、Date
    3. 宿主对象，浏览器提供的对象
* 节点
    1. 元素节点
    2. 文本节点
    3. 属性节点
* 获取元素
    1. getElementById
    2. getElementByTagName
        * 返回一个对象数组
    3. getElementByClassName
        * 旧浏览器不一定支持

                function getElementByClassName(node, classname){
                    if(node.getElementByClassName){
                        return node.getElementByClassName(classname);
                    }else{
                        var results = new Array();
                        var elems = node.getElementByTagName("*");
                        for(var i = 0; i<elemts.length; i++){
                            if(elems[i].className.indexOf(classname) != -1){
                                results[result.length] = elems[i];
                            }
                        }
                    }
                }

* 获取和设置属性
    * getAttribute
    * setAttribute
* childNodes
    * 返回所有类型的节点，不仅仅是元素节点。文档里几乎每一样东西都是一个节点，甚至连空格和换行符都会被解释为节点。
* nodetype
    * node.nodeType
    * 1：元素节点；2：属性节点；3：文本节点
* 使用node.nodeType查看节点属性
* firstChild和lastChild：node.XXX
* nodeValue更新节点内容
* window.open(url, name, feature)

                function popUp(winURL){
                    window.open(winURL, "popup", "width=320, height=480");
                }


###实践注意
* 使用setAttribute代替element.value="**"，可以避免兼容性问题

###最佳实践
####函数调用问题
1. 平稳退化
    * 伪协议：javascript: ；真协议：http:....；避免使用javascript: 调用js代码
    * <a href="#" onclick="popUp('www.exmple.com');return False">XXX<a> 这种也不好
    * 以上两种不利于平稳退化，如果用户禁用了浏览器javascript功能，链接将毫无用处
    * <a href="www.exmple.com" onclick="popUp('www.exmple.com');return False">XXX<a> 这种方式比上面好
2. 分离
    * CSS使用<link>代替style
    * 类似于使用style属性，在HTML文档里使用诸如onclick之类的属性也是一种既没有效率又容易引发问题的做法
    * 分离JavaScript，在外部JavaScript文件里把一个事件添加到HTML文档中的某个元素上

