# 集合与泛型

除了[变量与基本类型](/language/basics.md)介绍的基本类型，Dart 标准库还提供了很多其他类型，比如最常用的集合类型（Collections）。

泛型（Generics）可以提高类型安全并减少重复代码，而集合类型几乎都支持泛型，所以本章也将对它进行讲解。

## 集合

Dart 的集合类型主要是来自 `dart:core` 库的 `List` 、`Set` 和 `Map`。

### List

`List`表示有序的数据列表，它其实就是其他语言中常见的数组（array）。

`List` 可以使用字面量（如：`[a1, a2]的形式`）或构造函数创建。

```dart
var gameList = ['Halo', 'God of War']; // 字面量形式

gameList = new List(); // 使用构造函数
gameList.add('The Witcher'); // 使用add方法添加数据
gameList.add('Dark Souls');

// 通过getter或索引访问元素
print(gameList.frist);
print(gameList.last);
print(gameList[0]);

// 通过for...in循环遍历
for (var game in gameList) {
    print(game);
}
```

### Set

`Set`是无序的数据集合，其中对象不可（不会）重复。

`Set` 可以使用字面量（`如：{'dart', 'javascript'}`）或构造函数创建。

注意：访问`Set`中的元素时，它们的顺序不能得到保证，默认顺序为元素的添加顺序。

```dart
var gameSet = {'Halo', 'God of War'}; // 字面量形式

gameSet = new Set(); // 使用构造函数
gameSet.add('The Witcher'); // 使用add方法添加数据
gameSet.add('Dark Souls');

// 通过getter访问元素
print(gameSet.frist);
print(gameSet.last);

// 通过for...in循环遍历
for (var game in gameSet) {
    print(game);
}
```

### Map

`Map`是无序的键值对集合，键(key)和值(value)可以是任意类型，其中键不可（不会）重复，值可以重复。

`Map` 可以使用字面量（`如：{'first': 'dart', 'second': 'javascript'}`）或构造函数创建。

注意：在没有[泛型](/language/generics.md)参数的情况下，空`Map`字面量和空`Set`字面量的写法都是`{}`。因为 Dart 的`Set`字面量是后加入的，所以`{}`将默认识别为`Map`类型。

```dart
var gameMap = {'microsoft': 'Halo', 'sony' :'God of War'}; // 字面量形式

gameMap = new Map(); // 使用构造函数
gameMap['cdpr'] = 'The Witcher'; // 使用[]添加数据
gameMap['fromsoft'] = 'Dark Souls';

print(gameMap['fromsoft']); // 使用[]访问数据

// 通过for...in循环遍历值
for (var game in gameMap.values) {
    print(game);
}
```

## 泛型

泛型（Generics）即泛化的类型，就是参数化的类型，Dart 标准库中的很多类型都支持泛型，集合类型是其中的代表。

### 语法

与其他很多支持泛型的语言一样（如：Java，C#），Dart 的泛型写法也是在类型名称后添加 `<类型参数1, 类型参数2, ...>`，比如: `var gameList = new List<String>()`。

注意：如果对象是通过字面量形式声明，类型参数则必须写在前面，比如：`var scoreMap = new <String, int>{}`。

TODO: 
