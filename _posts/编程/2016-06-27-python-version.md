---
layout: post
title: python版本管理
categories: [编程]
tags: []
disqus: y
---
##virtualenv安装和使用


##pyenv安装和使用备忘                                                                                       
安装git                 

        git clone https://github.com/yyuu/pyenv.git ~/.pyenv           
        echo 'export PYENV_ROOT="$HOME/.pyenv"'>> ~/.bashrc      
        echo 'export PATH="$PYENV_ROOT/bin:$PATH"'>> ~/.bashrc
        echo 'eval "$(pyenv init -)"' >> ~/.bashrc
        exec $SHELL -l                                                     
                         

安装gcc，否则无法编译安装python                                                                           
yum install gcc                                                                                           
                                                                                                          
确认安装以下模块                                                                                          
libreadline-dev, zlib-dev, libbz2-dev, libsqlite3-dev, libssl-dev                
                                                                                                     
安装python2.7.11                                                                                          
pyenv install --list                                                                                      
pyenv install 2.7.11                                                                                      
                                                                                                          
pyenv命令                                                                                                 
pyenv local 2.7.11 #切换版本，向当前目录下的.python-version写入版本名            
pyenv local --unset                                                                                       
pyenv local 2.7.6 3.3.3 #设置多个版本                                                                     
                                                                                                          
pyenv global 2.7.11 #.pyenv/version，改变其内容，切换全局的python                
pyenv global 2.7.6 3.3.3                                                                                  
                                                                                                     
pyenv shell pypy-2.2.1 #设置PYENV\_VERSION环境变量来切换shell的python             
pyenv shell --unset                                                                                       
                                                                                                          
pyenv rehash #装完2.7.11后执行                                                                            
                                                                                                          
                                                                                                          
pip过慢解决                                                                                               
在~/.pip/pip.conf文件中添加或修改                                                                         
                                                                                                          
[global]                                                                                                  
index-url = http://mirrors.aliyun.com/pypi/simple/                                                        
[install]                                                                                                 
trusted-host=mirrors.aliyun.com

##pip备忘
pip freeze > requirements.txt    
pip install -r requirements.txt

##问题
###在为多个用户配置环境的时候，pyenv放在root下不行
将用户分为一个组，新建文件夹，chgrp修改文件组，确保用户组有执行权限。把.pyenv放到新建的文件夹里，修改.pyenv/shims/python中的路径，否则用户使用Python的时候会指向错误的位置。