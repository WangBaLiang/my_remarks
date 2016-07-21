---
layout: post
title: ExtJS问题记录
categories: [编程]
tags: []
disqus: y
---

##XXXstore.filter('id', 1) 报错：XXXstore.filter is not a function
检查XXXstore的_filters_


##Uncaught TypeError: Cannot read property 'getCount' of null
原因是store ajax没取到数据。    
额，不是上面的原因。又遇到了。  
恩，是写错了。    

        Ext.onReady(function(){
            var grid = Ext.create('Ext.panel.Panel', {
                .......
                Ext.apply(this, {
                    items:[{}]    
                })    
            })
        })  

以上写法会导致这个错误
##Ext.toolbar.Paging xtype: pagingtoolbar

    var itemsPerPage = 2; // set the number of items you want per page

    Ext.create('Ext.data.Store', {
        id: 'simpsonsStore',
        autoLoad: false,
        fields: ['name', 'email', 'phone'],
        pageSize: itemsPerPage, // items per page
        proxy: {
            type: 'ajax',
            url: 'pagingstore.js', // url that will load data with respect to start and limit params
            reader: {
                type: 'json',
                rootProperty: 'items',
                totalProperty: 'total'
            }
        }
    });

    // specify segment of data you want to load using params
    store.load({
        params: {
            start: 0,
            limit: itemsPerPage
        }
    });

    Ext.create('Ext.grid.Panel', {
        title: 'Simpsons',
        store: 'simpsonsStore',
        columns: [{
            text: 'Name',
            dataIndex: 'name'
        }, {
            text: 'Email',
            dataIndex: 'email',
            flex: 1
        }, {
            text: 'Phone',
            dataIndex: 'phone'
        }],
        width: 400,
        height: 125,
        dockedItems: [{
            xtype: 'pagingtoolbar',
            store: 'simpsonsStore', // same store GridPanel is using
            dock: 'bottom',
            displayInfo: true
        }],
        renderTo: Ext.getBody()
    });

注意pagingtoolbar的store和Panel的store应保持一致，否则会导致翻页按钮无法使用。



