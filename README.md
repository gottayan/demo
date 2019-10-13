### 1、函数调用
```
var name = 'xiaozhang'
const sex = 'man'
function sum(a, b) {
   // 'use strict'
   console.log(this === window) // => true
   console.log(this.name) // => xiaozhang
   console.log(this.sex) // => undefined
   this.myNumber = 20 // 将'myNumber'属性添加到全局对象
   return a + b;
}
sum(10, 10)
```

### 2、箭头函数
```
const person = {
  name: 'xiaohong',
  sayName: function () {
    console.log(this) //person
    console.log(this.name) //xiaohong
  },
  sayNameArrow: () => {
    console.log(this) // window
    console.log(this.name) //undefined
  },
  parent: {
    name: 'parent',
    say() {
		setTimeout(() => {
			console.log(this.name)
		}, 0)
	}
  }
}
person.sayName();  //  person  xiaohong
person.sayNameArrow();  //  window  undefined
person.parent.say() // ??
```

```
new Vue({
  data() {
	return {
		name: 'vue'
	}
  },
  methods: {
    test() {
	  // var self = this
      const fn = () => {
		// console.log(self.name)
        console.log(this.name)
      }
    }
  }
})
```

### 3、方法调用
```
var name = 'window'
const person = {
 name: 'xiaohong',
 sayName () {
    console.log(this) //person
    console.log(this.name) //xiaohong
 },
 children: {
 	name: 'xiaozhang',
 	sayName () {
      console.log(this) // children
      console.log(this.name) // xiaozhang
 	}
 }
}

// 隐式绑定
person.sayName()
person.children.sayName()

// 隐式丢失
const personFn = person.children.sayName
peronFn() // window
setTimeout(person.children.sayName, 0)

```

### 4、显示绑定
```
function foo() { 
    console.log( this.a );
}

var obj = { 
    a: 2,
    foo: foo 
}

var a = "oops, global"; 

// 显式绑定
foo.call(obj) // 2
foo.apply(obj) // 2
var fooFn = foo.bind(obj)
fooFn()  // 2
```

### 5、new 绑定
```
class Person {
	constructor(name, age) {
		this.name = name
		this.age = age
	}
    static bar() {
    	this.baz() // Person
    }
    static baz() {
    	console.log('hello')
    }
}

const person = new Person('xiao', 19)
console.log(person.name) // xiao

Person.bar() // hello
```

### 6、call
```
var name = 'he'
var otherObj = {
	name: 'me'	
}

// 手写call实现
Function.prototype.myCall = function(context) {      
    context = context || window
    context.say = this // this指向执行的方法(Function.prototype ==> say)
    context.say() // context指向(otherObj)
}

function say() {
	console.log(this.name)
}

// 例子
say.myCall(otherObj)
```

### 7、apply
```
var name = 'he'
var obj = {
	name: 'me'
}
Function.prototype.myApply = function(context) {
    if (typeof context === "undefined" || context === null) {
        context = window
    }
    context.fn = this // this指向(Function.prototype ==> say)
    let arg = [...arguments].slice(1)
    context.fn(...arg.flat(Infinity)) // context指向obj
    delete context.fn

}


function say(a, b) {
	console.log(a + b)
	console.log(this.name)
}

// 例子
say.myApply(obj, [9, 9])
```

### 8、bind
```
Function.prototype.bind = function(oThis) {
      if (typeof this !== "function") {
          // 与 ECMAScript 5 最接近的
          // 内部 IsCallable 函数
          throw new TypeError(
              "Function.prototype.bind - what is trying " +
              "to be bound is not callable"
          ); 
      }

      var aArgs = Array.prototype.slice.call( arguments, 1 ), 
          fToBind = this,
          fNOP = function(){}, 
          fBound = function(){
              return fToBind.apply( 
                  (
                      this instanceof fNOP &&
                      oThis ? this : oThis 
                  ),
                  aArgs.concat(
                      Array.prototype.slice.call(arguments)
                  )
      )
      
          };
      fNOP.prototype = this.prototype;
      fBound.prototype = new fNOP(); 

      return fBound;
  };
```

### 9、nextTick
```
var name = 'window'

Vue.nextTick(function() {
	console.log(this.name) // otherThis
}, { name: 'otherThis' })

Vue.nextTick(function() {
	console.log(this.name) // window
})

Vue.prototype.$nextTick(function() {
	console.log(this.name) // undefined
}, { name: 'otherThis' })
```

### 10、class
```
class Logger {
	printName(name = 'there') {
		this.print(`Hello ${ name }`)
	}
	print() {
		console.log(text)
	}
}

const logger = new Logger()
const { printName } = logger
printName() // Uncaught TypeError

class Logger {
	constructor() {
    this.printName = this.printName.bind(this)
  }
}
```