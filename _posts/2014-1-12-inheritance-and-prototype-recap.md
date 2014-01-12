---
layout: post
title: Inheritance and prototype recap
date: 2014-1-12 11:36
---
##再回顾Object
> An object is a container of properties, ... A property name can be any string, include the empty string. A property value can be any JavaScipt value except for `undefined`.

Property name当然是string, 但是我这周的代码中就出现过在property中加number的情况。理论上这应该是一个syntax error, 但实际没有报错，最后应该是JavaScript将其转化成string。

Property值不能是`undefined`也可以这么说，当一个property还没有声明时，是不能使用它的（判断它是否存在例外）。想要获取一个未声明的property的property更是会引起js 报错。所以JavaScript在Object retrieval时经常使用两种技巧, 引自JavaScript: The Good Parts：

1. || 操作符用来定义默认值

```
var value = object["property"] || "defaultValue";
```

2. && 操作符用来保护从`undefined`中取值

```
if (object.someProperty && object.someProperty.someValue) {}
```

###delete

delete只会删除object的property，不会触动object的prototype chain.

##再回顾prototype
- constructor必须是一个function。
- 只有constructor才有一个属性`prototype`指向自己的prototype, constructor的instance是没有的。在ECMA-262 fifth edition中，instace会有一个隐性指针[[Prototype]], 在现代浏览器中你可以通过`__proto__`才获取。