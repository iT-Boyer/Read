# JavaScript函数式编程

## 1. JavaScript函数式编程简介

函数式编程: 通过使用函数来将值转换成抽象单元,接着用于构建软件系统

实践中的函数式编程并不以消除状态改变为主要目的,而是将任何已知系统中突变的出现尽量压缩到最小区域中去

函数式编程

1. 一个队`存在`的抽象函数的定义
2. 一个建立在存在函数智商的,对`真`的抽象函数的定义
3. 通过其他函数来使用上面的两个函数,以实现更多的行为  

## 2. 一等函数与Application编程

用100个函数操作一个数据结构,要比10个函数控制10个数据结构要好

高阶函数至少会有的动作

1. 以一个函数为参数
2. 以返回一个函数作为结果 

函数式编程最常用的方法

1. map: 遍历集合并对每一个值调用一个函数,返回结果的集合
2. reduce: 利用函数将值的集合合并成一个值,该函数接受一个累计值和本次处理的值
3. filter: 对集合每一个值调用一个返回boolean的函数,抽取返回true的值的集合
4. find: 接受集合和返回Boolean的函数,返回第一个为true的元素   
5. reject: 与find反向查找
6. all: 接受一个集合和return Boolean的函数,全部true则最终返回true
7. any: 接受一个集合和return Boolean的函数,有一个true则返回true
8. sortBy: 接受一个集合和一个返回比较条件的函数
9. groupBy: 接受一个集合和一个条件函数,并返回一个对象,键为传入函数所返回的条件,值为与其对应的元素

      ```javascript
      var albums = [
        {title: "K_title_1",genre: "Metal"},
        {title: "K_title_2",genre: "Metal"},
        {title: "F_Title_1",genre: "Fuck"},
      ]
      _.groupBy(albums,function(a){ return a.genre});
      //返回值为 =>
      {
        Metal: [
          {title: "K_title_1",genre: "Metal"},
          {title: "K_title_2",genre: "Metal"},
        ]
        Fuck: [
          {title: "F_Title_1",genre: "Fuck"},
        ]
      }
      
      ```
10. countBy:        


 
 