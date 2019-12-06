---
title: Smalltalk
date: 2018-08-29 10:29:20
tags:
---

# 简介

Smalltalk是一个面向对象的、动态类型的编程语言。

IDE：[Pharo](https://pharo.org/)、[Squeak](https://squeak.org/)

# 词法

## 字符集

- a-z
- A-Z
- 0-9
- .+/*~<>@%|&?
- blank, tab, cr, ff, lf

## 标识符

全局变量的首字母必须大写，局部变量的首字母必须小写。

## 关键字

关键字实际上是以冒号结尾的标识符（例如`keyword:`），它只有在它形成“关键字消息”的时候才有意义。

## 保留字

 `nil`、`true`、`false`、`self`、`super`和`Smalltalk`

`self`引用消息接收者。

`super`引用定义当前代码的对象的父类，而不是引用接收者的父类。

## 数值

```smalltalk
5        "十进制数5"
16r7f    "十六进制数7f"
基数r111 "任意进制数11"
2e-5     "浮点数"
22/7     "分数"
```

## 字符

```smalltalk
$h    "字符h"
$     "$后跟一个空格表示空格字符"
```

## 字符串

```smalltalk
'这是字符串'
'I''m here'

x := 'String', 'Concatenation'.                    "string concatenation"
```

字符串中的单引号，使用两个单引号（`''`）表示。

## 布尔值

`true`：`True`类的唯一实例。

`false`：`False`类的唯一实例。

## 空值

 `nil`：代表未初始化的值。

## 符号

```smalltalk
#red    "符号red"
```

## 注释

```smalltalk
"这是注释"
```

# 变量

Smalltalk中的变量是没有类型的，即它可以引用任意类型对象。因此，声明变量时，不需要指定类型。

Smalltalk的对象是有类型的（实际上是类）。

变量必须在使用前声明。

## 作用域

### 全局作用域

在`Smalltalk`字典中定义，对所有对象可见。

全局变量命名，约定以大写字母开始。

### 方法临时作用域

在方法内部声明，只在方法内部可见。

方法临时变量要在方法体的开头处声明。

方法临时变量命名，约定以小写字母开始。

### 块临时作用域

在块中声明，只在块中可见。

### 池作用域

在字典对象中的变量。

### 方法参数作用域

### 块参数作用域

### 类作用域

类变量在类和类的实例中可见。

类变量命名，约定以大写字母开始。

### 类实例作用域

类实例变量只在类中可见，在实例中不可见。相当于，java中的`private static`。

类实例变量命名，约定以大写字母开始。

### 实例变量作用域

实例变量只在对象中可见，相当于私有成员变量。

实例变量命名，约定以小写字母开始。

## 局部变量声明

```smalltalk
| x y |
```

声明了两个局部变量`x`和`y`。

# 表达式

## 赋值

```smalltalk
x := 5.
x := y := z := 6.
x := (y := 6) + 1.
```

## 块

块（也叫闭包）也是对象。

除非有显式的返回，否则块的值是它的最后一个表达式的值 。

块可以嵌套。

块的一般形式：

```smalltalk
[ 参数 | 局部变量声明 表达式 ]
```

无参块：

```smalltalk
[ y := 1. z := 2. ]
```

带参数块：

```smalltalk
| block |
block := [:x :y | x * 2 + y].
block value: 5 value: 3     "结果为13"
```

块也是对象，value是它的一个方法，它的作用就是返回块的结果。



## 消息

消息实际上就是方法调用。

### 一元消息

```smalltalk
object unaryMessage     "相当于Java：object.unaryMessage()"
myDept manager name last  "相当于Java：myDept.manager().name().last()"
```

上面例子中，`object`是接收者，而`unaryMessage`是发送给接收者的消息。

`myDept`是接收者，`manager`是发送给`myDept`的消息，`name`是发送给`myDept manager`的结果的消息，`last`是发送给`myDept manager name`的结果的消息。这形成了一个消息链。

### 二元消息

```smalltalk
5 + 3  "5是接收者对象，(+ 3)是消息，这个消息带一个参数3。"
```

预定义的二元消息：

+

\-

*

/

**：幂

//：整除

\\：求模

<

<=

\>

\>=

=：内容相等

~=：内容不相等

==：引用相等

~~：引用不相等

&：逻辑与

|：逻辑或

,：拼接两个集合

@：创建Point类的实例

->：创建Association类的实例

### 关键字消息

```smalltalk
'elephant' copyFrom: 3 to: 5
```

`'elephant'`是接收者对象，`(copyFrom: 3 to: 5)`是消息，这个消息有两个关键字`copyFrom:`和`to:`，每个关键字带一个参数。

上面例子相当于Java：`"elephant".copyFromTo(3, 5)`。

Smalltalk没有内在的控制结构，其他语言中的条件语句、循环语句，在Smalltalk中就是一个关键字消息。

### 消息优先级

一元消息优先级最高，二元消息其次，关键字消息最低。

通过圆括号可以改变优先级。

同等优先级下，从左到右进行运算。

### 消息级联

分号表示消息级联：

```smalltalk
|p|
p := Client new
  name: 'Jack';
  age: 32;
  address: 'Earth'.
```

相当于：

```smalltalk
|p|
p := Client new.
p name: 'Jack'.
p age: 32.
p address: 'Earth'.
```



# 集合

Smalltalk中，集合的索引都是从1开始。

## 数组

```smalltalk
| b x y sum max |
x := #(4 3 2 1).                                 "constant array"
x := Array with: 5 with: 4 with: 3 with: 2.      "create array with up to 4 elements"
x := Array new: 4.                               "allocate an array with specified size"
x                                                "set array elements"
   at: 1 put: 5;
   at: 2 put: 4;
   at: 3 put: 3;
   at: 4 put: 2.
b := x isEmpty.                                  "test if array is empty"
y := x size.                                     "array size"
y := x at: 4.                                    "get array element at index"
b := x includes: 3.                              "test if element is in array"
y := x copyFrom: 2 to: 4.                        "subarray"
y := x indexOf: 3 ifAbsent: [0].                 "first position of element within array"
y := x occurrencesOf: 3.                         "number of times object in collection"
x do: [:a | Transcript show: a printString; cr]. "iterate over the array"
b := x conform: [:a | (a >= 1) & (a <= 4)].      "test if all elements meet condition"
y := x select: [:a | a > 2].                     "return collection of elements that pass test"
y := x reject: [:a | a < 2].                     "return collection of elements that fail test"
y := x collect: [:a | a + a].                    "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].          "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.      "sum array elements"
sum := 0. 1 to: (x size) 
            do: [:a | sum := sum + (x at: a)].   "sum array elements"
sum := x inject: 0 into: [:a :c | a + c].        "sum array elements"
max := x inject: 0 into: [:a :c | (a > c)        "find max element in array"
   ifTrue: [a]
   ifFalse: [c]].
y := x shuffled.                                 "randomly shuffle collection"
y := x asArray.                                  "convert to array"
"y := x asByteArray."                            "note: this instruction not available on Squeak"
y := x asWordArray.                              "convert to word array"
y := x asOrderedCollection.                      "convert to ordered collection"
y := x asSortedCollection.                       "convert to sorted collection"
y := x asBag.                                    "convert to bag collection"
y := x asSet.                                    "convert to set collection"
```

数组元素的类型可以不相同。

## 有序集合

类似一个可扩展的数组 。

```smalltalk
| b x y sum max |
x := OrderedCollection 
     with: 4 with: 3 with: 2 with: 1.            "create collection with up to 4 elements"
x := OrderedCollection new.                      "allocate collection"
x add: 3; add: 2; add: 1; add: 4; yourself.      "add element to collection"
y := x addFirst: 5.                              "add element at beginning of collection"
y := x removeFirst.                              "remove first element in collection"
y := x addLast: 6.                               "add element at end of collection"
y := x removeLast.                               "remove last element in collection"
y := x addAll: #(7 8 9).                         "add multiple elements to collection"
y := x removeAll: #(7 8 9).                      "remove multiple elements from collection"
x at: 2 put: 3.                                  "set element at index"
y := x remove: 5 ifAbsent: [].                   "remove element from collection"
b := x isEmpty.                                  "test if empty"
y := x size.                                     "number of elements"
y := x at: 2.                                    "retrieve element at index"
y := x first.                                    "retrieve first element in collection"
y := x last.                                     "retrieve last element in collection"
b := x includes: 5.                              "test if element is in collection"
y := x copyFrom: 2 to: 3.                        "subcollection"
y := x indexOf: 3 ifAbsent: [0].                 "first position of element within collection"
y := x occurrencesOf: 3.                         "number of times object in collection"
x do: [:a | Transcript show: a printString; cr]. "iterate over the collection"
b := x conform: [:a | (a >= 1) & (a <= 4)].      "test if all elements meet condition"
y := x select: [:a | a > 2].                     "return collection of elements that pass test"
y := x reject: [:a | a < 2].                     "return collection of elements that fail test"
y := x collect: [:a | a + a].                    "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].          "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.      "sum elements"
sum := 0. 1 to: (x size) 
            do: [:a | sum := sum + (x at: a)].   "sum elements"
sum := x inject: 0 into: [:a :c | a + c].        "sum elements"
max := x inject: 0 into: [:a :c | (a > c)        "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x shuffled.                                 "randomly shuffle collection"
y := x asArray.                                  "convert to array"
y := x asOrderedCollection.                      "convert to ordered collection"
y := x asSortedCollection.                       "convert to sorted collection"
y := x asBag.                                    "convert to bag collection"
y := x asSet.                                    "convert to set collection"
```

## 排序集合

 类似于OrderedCollection，但可指定排序标准。

```smalltalk
x := SortedCollection 
     with: 4 with: 3 with: 2 with: 1.              "create collection with up to 4 elements"
x := SortedCollection new.                         "allocate collection"
x := SortedCollection sortBlock: [:a :c | a > c].  "set sort criteria"
x add: 3; add: 2; add: 1; add: 4; yourself.        "add element to collection"
y := x addFirst: 5.                                "add element at beginning of collection"
y := x removeFirst.                                "remove first element in collection"
y := x addLast: 6.                                 "add element at end of collection"
y := x removeLast.                                 "remove last element in collection"
y := x addAll: #(7 8 9).                           "add multiple elements to collection"
y := x removeAll: #(7 8 9).                        "remove multiple elements from collection"
y := x remove: 5 ifAbsent: [].                     "remove element from collection"
b := x isEmpty.                                    "test if empty"
y := x size.                                       "number of elements"
y := x at: 2.                                      "retrieve element at index"
y := x first.                                      "retrieve first element in collection"
y := x last.                                       "retrieve last element in collection"
b := x includes: 4.                                "test if element is in collection"
y := x copyFrom: 2 to: 3.                          "subcollection"
y := x indexOf: 3 ifAbsent: [0].                   "first position of element within collection"
y := x occurrencesOf: 3.                           "number of times object in collection"
x do: [:a | Transcript show: a printString; cr].   "iterate over the collection"
b := x conform: [:a | (a >= 1) & (a <= 4)].        "test if all elements meet condition"
y := x select: [:a | a > 2].                       "return collection of elements that pass test"
y := x reject: [:a | a < 2].                       "return collection of elements that fail test"
y := x collect: [:a | a + a].                      "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].            "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.        "sum elements"
sum := 0. 1 to: (x size) 
            do: [:a | sum := sum + (x at: a)].     "sum elements"
sum := x inject: 0 into: [:a :c | a + c].          "sum elements"
max := x inject: 0 into: [:a :c | (a > c)          "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x asArray.                                     "convert to array"
y := x asOrderedCollection.                         "convert to ordered collection"
y := x asSortedCollection.                          "convert to sorted collection"
y := x asBag.                                       "convert to bag collection"
y := x asSet.                                       "convert to set collection"
```

## 包

 类似于OrderedCollection，但元素没有特定的顺序。 

 ```smalltalk
x := Bag with: 4 with: 3 with: 2 with: 1.        "create collection with up to 4 elements"
x := Bag new.                                    "allocate collection"
x add: 4; add: 3; add: 1; add: 2; yourself.      "add element to collection"
x add: 3 withOccurrences: 2.                     "add multiple copies to collection"
y := x addAll: #(7 8 9).                         "add multiple elements to collection"
y := x removeAll: #(7 8 9).                      "remove multiple elements from collection"
y := x remove: 4 ifAbsent: [].                   "remove element from collection"
b := x isEmpty.                                  "test if empty"
y := x size.                                     "number of elements"
b := x includes: 3.                              "test if element is in collection"
y := x occurrencesOf: 3.                         "number of times object in collection"
x do: [:a | Transcript show: a printString; cr]. "iterate over the collection"
b := x conform: [:a | (a >= 1) & (a <= 4)].      "test if all elements meet condition"
y := x select: [:a | a > 2].                     "return collection of elements that pass test"
y := x reject: [:a | a < 2].                     "return collection of elements that fail test"
y := x collect: [:a | a + a].                    "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].          "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.      "sum elements"
sum := x inject: 0 into: [:a :c | a + c].        "sum elements"
max := x inject: 0 into: [:a :c | (a > c)        "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x asOrderedCollection.                       "convert to ordered collection"
y := x asSortedCollection.                        "convert to sorted collection"
y := x asBag.                                     "convert to bag collection"
y := x asSet.                                     "convert to set collection"
 ```

## 集

 像Bag一样，但不允许重复元素。

### IdentitySet

 使用同一测试（即`==`而非`=`） 

 ```smalltalk
x := Set with: 4 with: 3 with: 2 with: 1.        "create collection with up to 4 elements"
x := Set new.                                    "allocate collection"
x add: 4; add: 3; add: 1; add: 2; yourself.      "add element to collection"
y := x addAll: #(7 8 9).                         "add multiple elements to collection"
y := x removeAll: #(7 8 9).                      "remove multiple elements from collection"
y := x remove: 4 ifAbsent: [].                   "remove element from collection"
b := x isEmpty.                                  "test if empty"
y := x size.                                     "number of elements"
x includes: 4.                                   "test if element is in collection"
x do: [:a | Transcript show: a printString; cr]. "iterate over the collection"
b := x conform: [:a | (a >= 1) & (a <= 4)].      "test if all elements meet condition"
y := x select: [:a | a > 2].                     "return collection of elements that pass test"
y := x reject: [:a | a < 2].                     "return collection of elements that fail test"
y := x collect: [:a | a + a].                    "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].          "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.      "sum elements"
sum := x inject: 0 into: [:a :c | a + c].        "sum elements"
max := x inject: 0 into: [:a :c | (a > c)        "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x asArray.                                  "convert to array"
y := x asOrderedCollection.                      "convert to ordered collection"
y := x asSortedCollection.                       "convert to sorted collection"
y := x asBag.                                    "convert to bag collection"
y := x asSet.                                    "convert to set collection"
 ```

### Interval

```smalltalk
| b x y sum max |
x := Interval from: 5 to: 10.                     "create interval object"
x := 5 to: 10.
x := Interval from: 5 to: 10 by: 2.               "create interval object with specified increment"
x := 5 to: 10 by: 2.
b := x isEmpty.                                   "test if empty"
y := x size.                                      "number of elements"
x includes: 9.                                    "test if element is in collection"
x do: [:k | Transcript show: k printString; cr].  "iterate over interval"
b := x conform: [:a | (a >= 1) & (a <= 4)].       "test if all elements meet condition"
y := x select: [:a | a > 7].                      "return collection of elements that pass test"
y := x reject: [:a | a < 2].                      "return collection of elements that fail test"
y := x collect: [:a | a + a].                     "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].           "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.       "sum elements"
sum := 0. 1 to: (x size) 
            do: [:a | sum := sum + (x at: a)].    "sum elements"
sum := x inject: 0 into: [:a :c | a + c].         "sum elements"
max := x inject: 0 into: [:a :c | (a > c)         "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x asArray.                                   "convert to array"
y := x asOrderedCollection.                       "convert to ordered collection"
y := x asSortedCollection.                        "convert to sorted collection"
y := x asBag.                                     "convert to bag collection"
y := x asSet.                                     "convert to set collection"
```

### 关联表

```smalltalk
| x y |
x := #myVar->'hello'.
y := x key.
y := x value.
```

### 字典和同一字典

```smalltalk
| b x y |
x := Dictionary new.                   "allocate collection"
x add: #a->4; 
  add: #b->3; 
  add: #c->1; 
  add: #d->2; yourself.                "add element to collection"
x at: #e put: 3.                       "set element at index"
b := x isEmpty.                        "test if empty"
y := x size.                           "number of elements"
y := x at: #a ifAbsent: [].            "retrieve element at index"
y := x keyAtValue: 3 ifAbsent: [].     "retrieve key for given value with error block"
y := x removeKey: #e ifAbsent: [].     "remove element from collection"
b := x includes: 3.                    "test if element is in values collection"
b := x includesKey: #a.                "test if element is in keys collection"
y := x occurrencesOf: 3.               "number of times object in collection"
y := x keys.                           "set of keys"
y := x values.                         "bag of values"
x do: [:a | Transcript show: a printString; cr].            "iterate over the values collection"
x keysDo: [:a | Transcript show: a printString; cr].        "iterate over the keys collection"
x associationsDo: [:a | Transcript show: a printString; cr]."iterate over the associations"
x keysAndValuesDo: [:aKey :aValue | Transcript              "iterate over keys and values"
   show: aKey printString; space;
   show: aValue printString; cr].
b := x conform: [:a | (a >= 1) & (a <= 4)].      "test if all elements meet condition"
y := x select: [:a | a > 2].                     "return collection of elements that pass test"
y := x reject: [:a | a < 2].                     "return collection of elements that fail test"
y := x collect: [:a | a + a].                    "transform each element for new collection"
y := x detect: [:a | a > 3] ifNone: [].          "find position of first element that passes test"
sum := 0. x do: [:a | sum := sum + a]. sum.      "sum elements"
sum := x inject: 0 into: [:a :c | a + c].        "sum elements"
max := x inject: 0 into: [:a :c | (a > c)        "find max element in collection"
   ifTrue: [a]
   ifFalse: [c]].
y := x asArray.                                   "convert to array"
y := x asOrderedCollection.                       "convert to ordered collection"
y := x asSortedCollection.                        "convert to sorted collection"
y := x asBag.                                     "convert to bag collection"
y := x asSet.                                     "convert to set collection"

Smalltalk at: #CMRGlobal put: 'CMR entry'.        "put global in Smalltalk Dictionary"
x := Smalltalk at: #CMRGlobal.                    "read global from Smalltalk Dictionary"
Transcript show: (CMRGlobal printString).         "entries are directly accessible by name"
Smalltalk keys do: [ :k |                         "print out all classes"
   ((Smalltalk at: k) isKindOf: Class)
      ifFalse: [Transcript show: k printString; cr]].
Smalltalk at: #CMRDictionary put: (Dictionary new). "set up user defined dictionary"
CMRDictionary at: #MyVar1 put: 'hello1'.            "put entry in dictionary"
CMRDictionary add: #MyVar2->'hello2'.               "add entry to dictionary use key->value combo"
CMRDictionary size.                                 "dictionary size"
CMRDictionary keys do: [ :k |                       "print out keys in dictionary"
   Transcript show: k printString; cr].
CMRDictionary values do: [ :k |                     "print out values in dictionary"
   Transcript show: k printString; cr].
CMRDictionary keysAndValuesDo: [:aKey :aValue |     "print out keys and values"
   Transcript
      show: aKey printString;
      space;
      show: aValue printString;
      cr].
CMRDictionary associationsDo: [:aKeyValue |           "another iterator for printing key values"
   Transcript show: aKeyValue printString; cr].
Smalltalk removeKey: #CMRGlobal ifAbsent: [].         "remove entry from Smalltalk dictionary"
Smalltalk removeKey: #CMRDictionary ifAbsent: [].     "remove user dictionary from Smalltalk dictionary"
```

### 内部流

```smalltalk

```



# 语句

语句使用`.`作为分隔符，最后一条语句不需要加`.`号。

## 返回语句

```smalltalk
^ 返回对象
```

## 表达式语句

```smalltalk
x := 5.
```

### 条件语句

```smalltalk
tries > 5
  ifTrue: [^ 'Too many tries']
  ifFalse: [^ 'Trying again'].
  
x > 10
  ifTrue: [x > 5
    ifTrue: ['A']
  	ifFalse: ['B']]
  ifFalse: ['C'].
```

### 循环语句

```smalltalk
| x y |
x := 4. y := 1.
[x > 0] whileTrue: [x := x - 1. y := y * 2].     "while true loop"
[x >= 4] whileFalse: [x := x + 1. y := y * 2].   "while false loop"
x timesRepeat: [y := y * 2].                     "times repeat loop (i := 1 to x)"
1 to: x do: [:a | y := y * 2].                   "for loop"
1 to: x by: 2 do: [:a | y := y / 2].             "for loop with specified increment"
#(5 4 3) do: [:a | x := x + a].                  "iterate over array elements"
```



# 类

类是定义某种对象特性的对象。

## 创建实例

```smalltalk
AClass new   "相当于Java：new AClass()"
```



## 方法

任何方法都会至少有一个返回值，如果有显式返回，默认将会返回`^self`。

