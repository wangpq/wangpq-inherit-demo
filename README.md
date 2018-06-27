# wangpq-inherit-demo
> Javascript面向对象编程：封装、构造函数的继承、非构造函数的继承


**封装**


Javascript（这里通指es6以前版本）是一种基于对象（object-based）的语言，但是，它又不是一种真正的面向对象编程（OOP）语言，因为它的语法中没有class（类）。

那么，如果我们要把"属性"（property）和"方法"（method），封装成一个对象，甚至要从原型对象生成一个实例对象，我们应该怎么做呢？


一、 生成实例对象的原始模式


假定我们把Dog看成一个对象，它有"名字"和"颜色"两个属性。

```bash
    var Dog = {

        name : '',

        color : ''

    } 
```

现在，我们需要根据这个原型对象的规格（schema），生成两个实例对象。


```bash
　　var dog1 = {}; // 创建一个空对象

       dog1.name = "大宝"; // 按照原型对象的属性赋值

　　　　dog1.color = "黄色";

　　var dog2 = {};

　　　　dog2.name = "二宝";

　　　　dog2.color = "白色";
```

好了，这就是最简单的封装了，把两个属性封装在一个对象里面。但是，这样的写法有两个缺点，一是如果多生成几个实例，写起来就非常麻烦；二是实例与原型之间，没有任何办法，可以看出有什么联系。


二、 原始模式的改进


我们可以写一个函数，解决代码重复的问题。

```bash
　　function Dog(name,color) {

　　　　return {

　　　　　　name:name,

　　　　　　color:color

　　　　}

　　}
```


然后生成实例对象，就等于是在调用函数：

```bash
　　var dog1 = Dog("大宝","黄色");

　　var dog2 = Dog("二宝","白色");
```

这种方法的问题依然是，dog1和dog2之间没有内在的联系，不能反映出它们是同一个原型对象的实例。


三、 构造函数模式


为了解决从原型对象生成实例的问题，Javascript提供了一个构造函数（Constructor）模式。

所谓"构造函数"，其实就是一个普通函数，但是内部使用了this变量。对构造函数使用new运算符，就能生成实例，并且this变量会绑定在实例对象上。

比如，猫的原型对象现在可以这样写，

```bash
　　function Dog(name,color){

　　　　this.name=name;

　　　　this.color=color;

　　}
```

我们现在就可以生成实例对象了。

```bash
　　var dog1 = Dog("大宝","黄色");

　　var dog2 = Dog("二宝","白色");

　　alert(dog1.name); // 大宝

　　alert(dog1.color); // 黄色
```

这时cat1和cat2会自动含有一个constructor属性，指向它们的构造函数。

```bash
　　alert(dog1.constructor == Dog); //true

　　alert(dog1.constructor == Dog); //true
```

Javascript还提供了一个instanceof运算符，验证原型对象与实例对象之间的关系。

```bash
　　alert(dog1 instanceof Dog); //true

　　alert(dog2 instanceof Dog); //true
```

四、构造函数模式的问题

构造函数方法很好用，但是存在一个浪费内存的问题。

请看，我们现在为Dog对象添加一个不变的属性type（种类），再添加一个方法eat（吃）。那么，原型对象Cat就变成了下面这样：

```bash
　　function Dog(name,color){

　　　　 this.name = name;

　　　　 this.color = color;

　　　　 this.type = "犬科动物";

　　　　 this.eat = function(){

           alert("喜欢吃骨头");

        };

　　}
```

还是采用同样的方法，生成实例：

```bash

    var dog1 = Dog("大宝","黄色");

    var dog2 = Dog("二宝","白色");

    alert(dog1.type); // 犬科动物

    dog1.eat(); // 喜欢吃骨头

```

表面上好像没什么问题，但是实际上这样做，有一个很大的弊端。那就是对于每一个实例对象，type属性和eat()方法都是一模一样的内容，每一次生成一个实例，都必须为重复的内容，多占用一些内存。这样既不环保，也缺乏效率。

```bash
　　alert(dog1.eat == dog2.eat); //false
```
能不能让type属性和eat()方法在内存中只生成一次，然后所有实例都指向那个内存地址呢？

回答是可以的。那就是我们下面将要讲的Prototype模式。

五、 Prototype模式

Javascript规定，每一个构造函数都有一个prototype属性，指向另一个对象。这个对象的所有属性和方法，都会被构造函数的实例继承。

这意味着，我们可以把那些不变的属性和方法，直接定义在prototype对象上。

```bash
　　function Dog(name,color){

　　　　this.name = name;

　　　　this.color = color;

　　}

　　Dog.prototype.type = "犬科动物";

　　Dog.prototype.eat = function(){alert("喜欢吃骨头")};
```

然后，生成实例。

```bash
    var dog1 = Dog("大宝","黄色");

    var dog2 = Dog("二宝","白色");

    alert(dog1.type); // 犬科动物

    dog1.eat(); // 喜欢吃骨头
```

这时所有实例的type属性和eat()方法，其实都是同一个内存地址，指向prototype对象，因此就提高了运行效率。

```bash
　　alert(dog1.eat == dog2.eat); //true
```


六、 Prototype模式的验证方法


为了配合prototype属性，Javascript定义了一些辅助方法，帮助我们使用它。

6.1 isPrototypeOf()

这个方法用来判断，某个proptotype对象和某个实例之间的关系。

　　alert(Dog.prototype.isPrototypeOf(dog1)); //true

　　alert(Dog.prototype.isPrototypeOf(dog2)); //true

6.2 hasOwnProperty()

每个实例对象都有一个hasOwnProperty()方法，用来判断某一个属性到底是本地属性，还是继承自prototype对象的属性。

　　alert(dog1.hasOwnProperty("name")); // true

　　alert(dog1.hasOwnProperty("type")); // false

6.3 in运算符

in运算符可以用来判断，某个实例是否含有某个属性，不管是不是本地属性。

　　alert("name" in dog1); // true

　　alert("type" in dog1); // true

in运算符还可以用来遍历某个对象的所有属性。

　　for(var prop in dog1) { alert("dog1["+prop+"]="+dog1[prop]); }
