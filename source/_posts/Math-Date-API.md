---
title: Math Date API
date: 2020-04-26 16:53:45
tags: JS
---

#### Math常用 API

- Math.floor(number); 向下取整；即取最小的，多的舍去；
- Math.ceil(number); 向上取整
- Math.round(number); 四舍五入
- Math.abs(number);  绝对值
- Math.max(num1,num2,num3..numx); 取最大值
- Math.min();  取最小值  数组的话可以这样写 math.min(...[元素])

#### Math.random()

返回[0, 1)之间的一个伪随机数，包括0，不包括1

经典使用方式： 写一个函数返回0-100 的随机整数

分析：random()函数默认返回0-1之间的数字，那我把结果*100放大100倍就好了

案例2：写一个函数返回长度为 n 的随机字符串，包含a-z,0-9;

分析：题目中有随机关键字，考虑使用random()函数，可该函数返回的是0-1 之间的数字，要求是一个规定范围的字符串。此时可以考虑，将该字符串的取值范围组成一个数组，然后该数组有长度，然后使用下标取出每一个字符，最后拼接n次成为最后的字符串就可以了。那获取一个0到该数组长度的一个随机数就好了。默认是 0-1的随机，那把结果*数组长度则范围就变为0-数组长度的随机数了；

```javascript
function randStr(len){
 const dict = 'abcdefghijklmnopqrstuvwxyz0123456789'
 let res = '';
 for(let i=0; i<len; i++) {
    res += dict[Math.floor(Math.random()*dict.length)] //字符串也是可以用下标访问的，哈哈
}
 return res;
}
```

<!--more-->

#### Number

- 二进制写法 0b 11 转为十进制就是 每一位*基数（2进制是2）的位权，此例子：1乘2一次方加1乘2的0次方

- 八进制写法 011

- 十六进制写法 0x11

- 大数的表示方法 bigint  例如： let n = 999999999999n;  2n+3n;

- parseInt(str,radix)   第二个参数代表该数字的进制 

   parseInt('11',2),  表示 2进制的11 代表十进制的多少，答案是3；

#### DATE 日期函数

获取当前日期对象  let d = new Date();

- d.getTime() //返回实例对象距离1970年1月1日00:00:00对应的毫秒数
- d.getDate() //返回实例对象对应每个月的几号（从1开始）
- d.getDay() //返回星期，星期日为0，星期一为1，以此类推 
- d.getFullYear() //返回四位的年份 d.getMonth() //返回月份（0表示1月，11表示12月） 
- d.getHours() //返回小时(0~23) d.getMilliseconds() //返回毫秒（0-999） 
- d.getMinutes() //返回分钟（0-59）
-  d.getSeconds() //返回秒（0-59）

Date.now(); 返回实例对象距离1970年1月1日00:00:00对应的毫秒数 == d.getTime();

#### 案例

1.去年的今天是周几

思路分析：可以获取现在距离去年今天的时间差，然后变为一个日期对象，然后再获取相应的DATE；

```
let nowDate = Date.now()
let lastYearTime = Date.now() - 365 * 24 * 60 * 60 * 1000
let lastYearDate = new Date(lastYearTime)
console.log(`去年的今天是周${lastYearDate.getDay()}`)
//此例中其实可以更严谨，就是去年不一定距离现在是365天，但年份一定是相差一年，所以我们可用今年的年份-1，更严谨
```

2.距离元旦（或某一个特定的日期，比如秒杀日期）还剩x天x时x分x秒

思路分析，也是获取现在和目的时间的时间差，然后将这个时间差换算为相应的天，小时，分钟，秒

``` javascript
function getLeftTime() {
let targetDateTime = new Date('2020-01-01 00:00:00').getTime()
let nowDateTime = Date.now()
let timeBetween = Math.abs(nowDateTime - targetDateTime)
let day = Math.floor(timeBetween/(24*3600*1000)) //这个用法特别巧妙，得到的商是天
let left = timeBetween%(24*3600*1000)  //得到的余数肯定是不满一天的剩余时间，然后在细分
let hour = Math.floor(left/(3600*1000))
left = left%(3600*1000)
let minute = Math.floor(left/(60*1000))
left = left%(60*1000)
let second = Math.floor(left/1000)
return { day, hour, minute, second }
}
let obj = getLeftTime()
console.log(`举例元旦还有${obj.day}天${obj.hour}时${obj.minute}分
${obj.second}秒`)
```

3.格式化旧日期   刚刚、3分钟前、2小时前、10天前

思路分析：传入一个日期，算出当前时间距离这个时间的差，小于1分钟 限时 刚刚，小于1小时限时多少多少分钟，小于一天显示多少多少小时，再多就显示多少多少天之前

```javascript
function formatTime(dateStr) {
let between = parseInt((Date.now() - new
Date(dateStr).getTime())/1000)
switch (true) {
case between < 60:
return `刚刚`
case between < 3600:
return `${parseInt(between/60)}分钟前`
case between < 3600*24:
return `${parseInt(between/3600)}小时前`
default:
return `${parseInt(between/(3600*24))}天前`
}
}
formatTime('2019-12-11 17:33:39')
```

**完美，结束**



