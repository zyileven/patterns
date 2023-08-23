# 代理模式

> 拦截与控制与目标对象的交互

代理：一般来说，代理指的是代替他人的人，你无需与目标直接进行交互，而是你的代理人与目标进行交互。在javascript中，我们使用代理对象来与目标交互，而不是直接与目标交互。

使用代理模式，我们可以更好的控制与某些对象的交互，当我们与对象进行交互时，代理对象能够决定我们如何获取值(get)和设置值(set)。

------

创建一个`person`对象：

```javascript
const person = {
  name: "Jhon Doe",
  age: 18,
  country: "USA"
}
```

假设现在我们不想与这个对象进行交互，而是交给代理去完成，于是我们创建代理对象：

```javascript
const personProxy = new Proxy(person, {})
```

new Proxy()第一个参数接受目标对象，第二个参数代表处理程序（我们需要让代理做的事情），我们可以在里面添加我们需要的处理方法，最常见的是：`get`和`set`

- `get`：尝试访问属性时调用的方法
- `set`：尝试修改属性时调用的方法

现在开始位代理程序添加一些方法：

```javascript
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    console.log(`The value of ${prop} is ${obj[prop]}`)
  },
  set: (obj, prop, newValue) => {
    console.log(`Changed ${prop} from ${obj[prop]} to ${value}`);
    obj[prop] = value;
  }
})
```

至此，我们就可以使用这个代理对象personProxy来对目标对象person进行一些操作了。

```javascript
personProxy.name; // get
// 将会输出：The value of name is John Doe.
personProxy.age = 28; // set
// 将会输出：Changed age from 18 to 28.
```

那么我们用代理的作用是为什么呢？我们直接使用`person.name`或者`person.age = 28` 也可以做到同样的事情。

首先，我们现在遇到一个情况，我需要更改person的age属性，但是有可能会接收到字符串，null或者其他数据结构的值，那么就会导致age的记录年龄的功能失效了。

这里，我们在代理对象中就可以限制他们不能更改为其他格式：

```javascript
const personProxy = new Proxy(person, {
  get: (obj, prop) => {
    if (!obj[prop]) { // 如果没有这个属性，则提示
      console.log(
        `Hmm.. this property doesn't seem to exist on the target object`
      );
    } else {
      console.log(`The value of ${prop} is ${obj[prop]}`);
    }
  },
  set: (obj, prop, value) => {
    if (prop === "age" && typeof value !== "number") { // 如果age属性接收到的不是数字，就提示
      console.log(`Sorry, you can only pass numeric values for age.`);
    } else if (prop === "name" && value.length < 2) { // 如果name接收到的长度小于2，就提示
      console.log(`You need to provide a valid name.`);
    } else {
      console.log(`Changed ${prop} from ${obj[prop]} to ${value}.`);
      obj[prop] = value;
    }
  },
});
```

这时，我们再使用代理对象来跟原始对象进行比较

```javascript
// 获取一个不存在的值
person.xxxx; // 输出：undefind
personProxy.xxxx; // 输出：Hmm.. this property doesn't seem to exist on the target object

// 修改一个值
person.age = "1000岁"; // 这时不合理的，但是依然将person.age改为了1000岁
personProxy.age = "1000岁" // Sorry, you can only pass numeric values for age.
```



## 总结

代理是一种增强对对象行为的控制的模式，常用用法有：帮助验证，格式化，通知或调试。

但是也不要过渡使用，因为会导致进行大量的handler操作，对应用程序的性能产生负面效果（最好不要子啊性能关键的位置使用代理）





















