---
layout: post
title: JavaScript DOM编程艺术
categories: [编程]
tags: [Django REST]
disqus: y
---
### 一. 基础知识
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

* 使用setAttribute代替element.value="**"，可以避免兼容性问题
* DOM Core和HTML DOM

        // DOM Core
        document.getElementByTagName("form")
        element.getAttribute("src")
        // HTML DOM
        document.forms
        element.src

### 二. 最佳实践

#### 平稳退化

    * 伪协议：javascript: ；真协议：http:....；避免使用javascript: 调用js代码
    * <a href="#" onclick="popUp('www.exmple.com');return False">XXX<a> 这种也不好
    * 以上两种不利于平稳退化，如果用户禁用了浏览器javascript功能，链接将毫无用处
    * <a href="www.exmple.com" onclick="popUp('www.exmple.com');return False">XXX<a> 这种方式比上面好

#### 分离
* CSS使用<link>代替style
* 类似于使用style属性，在HTML文档里使用诸如onclick之类的属性也是一种既没有效率又容易引发问题的做法
* 分离JavaScript，在外部JavaScript文件里把一个事件添加到HTML文档中的某个元素上
* 以下例子参考css的做法，通过html中元素节点的class改变其点击事件

        // html文件
        .....
        <a href="www.example.com" class="popup">exmaple<a>
        .....
        // 外部js文件
        window.onload = prepareLinks;
        function prepareLinks(){
            var links = document.getElementByTagName("a");
            for (var i=0;i<links.length; i++){
                if (links[i].getAttribute("class") == "popup"){
                    links[i].onclick = function(){
                        popUp(this.getAttribute("href"));
                        return false;
                    }
                }
            }
        }
        function popUp(winURL){
            window.open(winURL, "popup", "width=320, height=480");
        }

#### 向后兼容

        // 对象检测
        window.onload = function(){
            if(!document.getElementsByTagName) return false;
            var lnks = document.getElementsByTagName("a");
            for (var i=0;i<lnks.length;i++){
                lnks[i].onclick = function(){
                    popUp(this.getAttribute("href"));
                    return false;
                }
            }
        }

#### 性能考虑
* 尽量少访问DOM和尽量减少标记
* 合并和防止脚本
    * 合并脚本减少页面请求次数
    * 放在<head>标签中的js文件会导致浏览器无法并行加载其他文件；根据HTTP规范，浏览器每次从同一个域名中最多只能同时下载两个文件
    * 把<script>放到文档末尾，<body>标记前
* 使用工具压缩脚本

### 三. 图片库改进

        // html
        <ul id="imagegallery">
            // 为ul指定id，方便js和css操作
            <li><a href="images/firstpic.jpg" title="first picture">first</a></li>
            <li><a href="images/secondpic.jpg" title="second picture">second</a></li>
            <li><a href="images/thirdpic.jpg" title="third picture">third</a></li>
        </ul>

        function showPic(whichpic){
            // 将占位的图片替换为链接所指的图片

            // 优化后，增加了一些判断
            if (!document.getElementById("placeholder"))return false;
            var source = whichpic.getAttribute("href");

            // 判断占位图片是不是真的，nodeName返回的都是大写字母的值
            if (placeholder.nodeName != "IMG") return false;
            var placeholder = document.getElementById("placeholder");
            placeholder.setAttribute("src", source);

            if (document.getElementById("description")){
                var text = whichpic.getAttribute("title") ? whichpic.getAttribute("title") : "";
                var description = document.getElementById("description");
                if (description.firstChild.nodeType ==3){
                    description.firstChild.nodeValue = text;
                }
            }

            // 最开始的版本没有return值
            // return true是为了方便prepareGallery判断
            // 在改变链接点击事件后，是return false阻止链接的默认跳转行为
            // 还是return true允许该行为
            return true;
        }

        function prepareGallery(){
            // 不必过于拘泥于 结构化程序设计原则：函数应该只有一个入口和一个出口
            if (!document.getElementByTagName) return false;
            if (!document.getElementById) return false;
            if (!document.getElementById("imagegallery")) return false;

            var gallery = document.getElementById("imagegallery");
            var links = gallery.getElementByTagName("a");
            for (var i=0;i<links.length;i++){
                links[i].onclick = function(){
                    // showPic(this);
                    // reutrn false;
                    // 改进
                    return showPic(this) ? false : true;
                }
            }
        }

        // onload事件
        // 不能简单地使用 window.onload = firstFunction
        // 否则window.onload = secondFunction会覆盖第一个
        window.onload = function(){
            firstFunction();
            secondFunction();
        }

        // 更好的方法
        function addLoadEvent(func){
            var oldonload = window.onload;
            if (typeof window.onload != 'function') {
                window.onload = func;
            } else {
                window.onload = function() {
                    oldonload();
                    func();
                }
            }
        }
        addLoadEvent(prepareGallery);

### 四. 动态创建标记
#### document.write和innerHTML属性
1. 把结构、行为和样式分开。只要有可能，就应该用外部CSS文件代替<font>标签去设定和管理网页的样式信息，最好用外部JavaScript文件去控制网页的行为。应该避免在<body>部分乱用<script>标签，避免使用document.write方法
2.