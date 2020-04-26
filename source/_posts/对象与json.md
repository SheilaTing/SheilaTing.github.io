---
title: 对象与json
date: 2020-04-26 11:03:02
tags: 对象
---

#### 对象

ps:(最近刚学了对象，虽然之前也有了解，可一直模模糊糊，最近使用才发现原来自己对对象的掌握这么不扎实)

从定义说起：**对象：无序的数据集合，由若干个“键值对”（属性: 值）构成**

对象里面包含：1.属性 2. 方法

**对象的一些操作方法**

**Object.keys(对象)**：获取对象中的所有属性数组，注意此处是数组，然后就可以使用数组的方法来操作它。例如 ，arrr.map()  返回新的数组 ,arry.foreach()  arr.filter()返回新数组

删除属性： delete 对象.属性名或者 delete 对象【’属性名‘】

#### 基本使用

```javascript
//定义一个对象
let obj = {
    name:'jirengu',  //主义此处 name属性没有加引号，起始默认是有引号的，js这样写也没错
    age:5,
    sayHello:function(){
        console.lot(`hello ${this.name`)
    }
}
//通过.访问属性
obj.name
//通过[‘属性名’]  主义此时要加上引号，因为它本身是有引号的，定义时省略了而已
obj['name']
//通过[属性名] 注意此处没有加引号，不加引号js会把【】中的值解析为一个值来对待
let key = 'age';
obj[key] //的值就是5；就是属性age的值
//使用点操作符增加属性
obj.grade=5; //为obj 增加了一个 grade属性；
//使用【】符号来增加属性
obj['score']=100;
```

#### JSON

- JSON.stringify  用于把一个值变成符合JSON格式的字符串
- JSON.parse  用于把一个符合JSON格式的字符串还原对象