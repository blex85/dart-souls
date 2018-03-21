# 类 - Part I

_类的知识点较多，所以分成两节进行讲解，本节是Part I，下一节是_[_Part II_](/language/class_ii.md)_。_

---

类是对象的蓝本，用于创建对象。

类的声明是使用关键字`class`，所有类都直接或间接继承最顶端的`Object`类。

_\*示例代码使用了dart核心库的_`identical`_函数，用于检查两个变量是否指向同一对象_

## 属性和方法

类中可以声明数据和函数，即对象的属性和方法。

普通的属性和方法是跟对象实例绑定，它们常被称为实例变量和实例方法；使用`static`修饰的属性和方法，它们是与类绑定，即类变量和类方法。

实例变量和实例方法、类变量和类方法，在类中都可以通过名称直接访问，实例变量和实例方法还可以通过`this`访问；在类的外部，实例变量和方法是通过`实例.变量`方式进行访问，而类变量和类方法必须通过`类名.变量`方式才能访问

```dart
// 声明一个表示'大剑'（《黑暗之魂》系列游戏的一类武器）的类
class GreatSword {
  // 实例变量
  String name; // 名称
  final int damage = 100; // 基础伤害值（不可变）
  int extraDamage = 0; // 其他附加伤害

  // 类变量
  static String category = 'weapon'; // 大剑的分类是武器

  // 类方法
  static upgrade(GreatSword sword) { // 升级，即增加附加伤害
    sword.extraDamage += 10;
  }

  // 实例方法
  info() => 'GreatSword - damage: ${damage} , extraDamage: ${this.extraDamage}'; // 通过名称或this访问实例变量
}

main() {
  // 使用默认构造函数（见下一小节）进行实例化
  var sword = new GreatSword();
  print(sword.damage);
  print(sword.info()); // GreatSword - damage: 100 , extraDamage: 0

  // 使用类方法
  GreatSword.upgrade(sword);
  print(sword.extraDamage);
  print(sword.info()); // GreatSword - damage: 100 , extraDamage: 10
}
```

## 构造函数

构造函数用于将类实例化为对象，配合关键字`new`或`const`使用（在 Dart 2.0 中，`new`与`const`变为可选）。

没有显式声明构造函数的类，将默认拥有一个与类同名且没有参数的构造函数（无参默认构造函数）。

构造函数最常见的操作是使用参数对属性赋值，Dart 为此提供了简写方式（在参数列表中直接使用`this.属性`）。

除了普通的构造函数，Dart 还支持`类名.构造函数名`方式的命名构造函数

```dart
class GreatSword {
  String name; // 名称
  final int damage = 100; // 基础伤害值
  int extraDamage = 0; // 其他附加伤害

  // 构造函数
  GreatSword(String name) {
    this.name = name;
  }
  // 以上构造函数的简写方式
  // GreatSword(this.name);

  // 命名构造函数
  GreatSword.enhanced(this.name, this.extraDamage); 
  // 等同于以下写法
  // GreatSword.enhanced(String name, int extraDamage) {
  //   this.name = name;
  //   this.extraDamage = extraDamage;
  // }
}

main() {
  // 使用不同构造函数进行实例化
  var sword = new GreatSword('Claymore'); // 普通大剑
  var enhancedSword = new GreatSword.enhanced('Claymore', 20); // 强化版大剑
}
```

默认情况下，构造函数总是会自动新建一个对象，函数体内不需要也不能使用`return`语句。

如果不需要构造函数每次调用都新建对象，比如从缓存中获取已存在的对象，则可以使用工厂构造函数。

使用`factory`修饰的构造函数即工厂构造函数，跟普通构造函数不同，它必须使用`return`语句返回对象

```dart
class GreatSword {
  String name; // 名称

  // 使用Map类型（后续章节将进行讲解）作为对象缓存
  static final Map cache = {};

  // 工厂构造函数
  factory GreatSword(String name) {
    // 对象已存在缓存中
    if (cache.containsKey(name)) {
      return cache[name];
    } else {
      // 对象不存在缓存中，使用库私有的（后续章节将进行讲解）构造函数新建对象
      final sword = new GreatSword._(name);
      cache[name] = sword; // 存入缓存
      return sword;
    }
  }

  // 库私有的（后续章节将进行讲解）命名构造函数
  GreatSword._(this.name);
}

main() {
  // 进行多次实例化
  var sword1 = new GreatSword('Claymore');
  var sword2 = new GreatSword('Claymore');
  // 使用顶层函数identical检查是否同一对象
  print(identical(sword1, sword2)); // true - 是同一对象
}
```

如果希望创建不可变对象，可以将所有实例变量使用`final`修饰，构造函数使用`const`修饰，最后使用`const`创建对象即可

```dart
// 声明一个表示'篝火'（《黑暗之魂》系列游戏的存盘点）的类
class Bonfire {
  final String name; // 名称

  const Bonfire(this.name);
}

main() {
  // 使用const进行多次实例化
  var bonfire1 = const Bonfire('home');
  var bonfire2 = const Bonfire('home');
  // 使用顶层函数identical检查是否同一对象
  print(identical(bonfire1, bonfire2)); // true - 是同一对象
  // bonfire1.name = 'castle'; // 错误，尝试修改不可变对象
}
```

## 初始化列表

构造函数还支持初始化列表，书写方式是在参数列表后跟一个以冒号开头的，使用逗号分隔的赋值列表。

初始化列表先于构造函数体执行，常用于`final`属性的初始化，即没有初始化的`final`属性可以在初始化列表中进行赋值。

初始化列表还可使用`this`进行构造函数转发，即复用构造函数逻辑，构造函数转发和赋值操作不能同时出现

```dart
class GreatSword {
  String name; // 名称
  final int damage; // 基础伤害值（没有初始化）
  int extraDamage = 0; // 其他附加伤害

  // 在初始化列表中设置final变量damage
  GreatSword(this.name, [this.extraDamage = 0]) : damage = 100; // 普通大剑基础伤害100

  // 在初始化列表中调用静态方法计算extraDamage
  GreatSword.magic(this.name, this.damage) : extraDamage = magicPower(damage); // 魔法大剑的附加伤害要根据基础伤害计算得出

  // 通过初始化列表转发到其他构造函数，其中this代表类名
  GreatSword.bastard(): this('Bastard Sword', 10); // 混种大剑
  GreatSword.moonlight(): this.magic('Moonlight GreatSword', 90); // 月光大剑

  // 计算魔法武器的附加伤害
  static magicPower(int damage) {
    return damage * 0.15;
  }
}

main() {
  var sword = new GreatSword('Claymore');
  var bastard = new GreatSword.bastard();
  var moonlight = new GreatSword.moonlight();
  print(sword.name); // Claymore
  print(sword.damage); // 100
  print(sword.extraDamage); // 0
  print(bastard.name); // Bastard Sword
  print(bastard.damage); // 100
  print(bastard.extraDamage); // 10
  print(moonlight.name); // Moonlight GreatSword
  print(moonlight.damage); // 90
  print(moonlight.extraDamage); // 13.5
}
```

## Getter/Setter 方法

跟很多语言不同，Dart 通过一种特殊的`getter/setter`方法对对象属性进行读写，它们虽是方法却有跟属性一样的访问方式。

所有普通属性都有一对隐含的`getter/setter`，`final`属性只有`getter`。

自定义`getter/setter`也是支持的，书写方式是在方法名前添加`get`或`set`，`getter`有返回值无参数而`setter`正好相反

```dart
class GreatSword {
  String name; // 名称
  final int damage = 100; // 基础伤害值
  int extraDamage = 0; // 其他附加伤害

  // damage隐含的getter
  // int get damage => damage;

  // name和extraDamage隐含的getter和setter
  // String get name => name;
  // set name(String name) => this.name = name;
  // int get extraDamage => extraDamage;
  // set extraDamage(int extraDamage) => this.extraDamage = extraDamage;

  // 自定义的getter和setter
  // 总伤害
  int get totalDamage {
    return damage + extraDamage; // 基础伤害与附加伤害之和
  }
  set totalDamage(int totalDamage) {
    extraDamage = totalDamage - damage; // 通过总伤害计算出附加伤害
  }

  // 打印信息
  info() {
    return '${name} - totalDamage: ' + this.totalDamage.toString(); // 通过this或直接访问属性
  }
}

main() {
  // 所有对属性的读写都是通过getter和setter
  var sword = new GreatSword();
  sword.name = 'GreatSword';
  sword.totalDamage = 200;
  print(sword.damage); // 100
  print(sword.extraDamage); // 100
  print(sword.info()); // GreatSword - totalDamage: 200

  sword.damage = 180; // 错误，damage的setter不存在  
}
```

`getter`和`setter`带来的好处是，你可以随时更改属性的内部实现，而且不用修改外部调用代码

```dart
// 版本1的GreatSword
class GreatSword1 {
  final String name = 'GreatSword';
}

// 版本2的GreatSword，将属性name转换为一个getter
class GreatSword2 {
  String get name {
    return greatName();
  }

  greatName() => 'GreatSword';
}

main() {
  // 使用两个不同版本的GreatSword，对name的访问方式不变
  var sword = new GreatSword1();
  print(sword.name); // GreatSword
  sword = new GreatSword2();
  print(sword.name); // GreatSword
}
```

## 子类

使用`extends`来创建子类，子类中使用`super`来访问父类。

除了构造函数，父类所有的属性（setter/getter）和方法都被子类继承，而且都可以被重写（override）。

子类的构造函数在执行前，将隐含调用父类的`无参默认构造函数`（没有参数且与类同名的构造函数）。

如果父类没有`无参默认构造函数`，则子类的构造函数必须在初始化列表中通过`super`显式调用父类的某个构造函数。

```dart
// 大剑
class GreatSword {
  String name; // 名称
  final int damage = 100; // 基础伤害值

  // 总伤害的getter
  int get totalDamage => damage; // 总伤害

  // 一个参数的构造函数
  GreatSword(this.name);
}

// 特大剑
class UltraGreatSword extends GreatSword {
  int extraDamage = 20; // 附加伤害值

  // 重写getter
  int get totalDamage => super.damage + extraDamage; // 父类的伤害加上自己的附加伤害为总伤害（super可省略）

  // 父类没有无参默认构造函数，必须使用super（代表父类名）调用父类的构造函数
  UltraGreatSword(String name) : extraDamage = 50, super(name);
}

main() {
  var black = new UltraGreatSword('Black Knight Greatsword');
  print(black.name); // Black Knight Greatsword
  print(black.damage); // 100
  print(black.extraDamage); // 50
}
```



