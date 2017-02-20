#JavaScript作用域

   关于作用域的名词：作用域(scope),闭包(closure),this,命名空间(namespace),函数作用域(function scope),
   全局作用域(global scope),词法作用域(lexical scope),公有变量(public scope),私有变量(private scope)；

   相关作用域的问题：什么是作用域? 什么是全局(局部)作用域? 什么是命名空间,它和作用域有什么不同?
   this关键字是什么,作用域又是怎么影响它的? 什么是函数/词法作用域? 什么是闭包? 什么是共有/私有作用域?

##作用域
   在JavaScript中,作用域指的是指代码的当前上下文环境.作用域可以被全局或者局部地定义。
##全局作用域
   在你开始写javascript的时候，处在的作用域被称作是全局作用域，如下：
 ```
   // global scope
	var name = 'Todd';
   jQuery('.myClass');  
  ```
##局部作用域
   局部作用域是指那从全局作用域中定义的许多作用域。javascript只有一个全局作用域，定义一个函数会产生一个局部作用
   定义在别的函数中的函数有一个局部的作用域, 并且这个作用域指向外部的函数.例如：
```
   // Scope A: Global scope out here
	var myFunction = function () {
	  // Scope B: Local scope in here
	};  
```
在全局作用域里，任何一个局部作用域是不可见，除非暴露接口，意思是如果定义了一个局部变量或者函数，外部无法访问此变量和函数，如下：
```
	var myFunction = function () {
	  var name = 'Todd';
	  console.log(name); // Todd
	};
	// Uncaught ReferenceError: name is not defined
	console.log(name);
```
name是一个局部变量，它没有向父级作用域暴露接口，所以是undefined.
##函数作用域
在javascript里面，所有的作用域被创建都是通过Function Scope，循环语句像for或者while,条件语句像if或者switch都不能够产生新的作用域. 新的函数 = 新的作用域，如下：

```
	// Scope A
	var myFunction = function () {
	  // Scope B
	  var myOtherFunction = function () {
	    // Scope C
	  };
	}; 
```  

##词法作用域
当看到函数里面有另一个函数，内部函数能够访问外部函数的作用域，这个叫做词法作用域或闭包，也被认为是静态作用域，如下：

```
	// Scope A
	var myFunction = function () {
	  // Scope B
	  var name = 'Todd'; // defined in Scope B
	  var myOtherFunction = function () {
	    // Scope C: `name` is accessible here!
	  };
	};
```
myOtherFunction在这里没有被调用，只是简单的定义。它的调用顺序也会影响到作用域里面变量的表现，如下定义并调用：
```
	var myFunction = function () {
	var name = 'Todd';
	var myOtherFunction = function () {
	    console.log('My name is ' + name);
	  };
	  console.log(name);
	  myOtherFunction(); // call function
	};

	// Will then log out:
	// `Todd`
	// `My name is Todd`
```
很容易理解和使用词法作用域,任何被定义在它父作用域上的变量/对象/函数,在作用域链上都是可以访问到的.例如:
 ```
    var name = 'Todd';
	var scope1 = function () {
	  // name is available here
	  var scope2 = function () {
	    // name is available here too
	    var scope3 = function () {
	      // name is also available here!
	    };
	  };
	};
```
词法作用域不可逆，如下：
```
	// name = undefined
	var scope1 = function () {
	  // name = undefined
	  var scope2 = function () {
	    // name = undefined
	    var scope3 = function () {
	      var name = 'Todd'; // locally scoped
	    };
	  };
	};
```
可以返回一个name的引用，但是不能直接使用变量。

##作用域链
   作用域链为给定的函数创建作用域，每个函数被定义都有自己嵌套的的作用域，任何定义在别的函数中的函数都有一个 连接外部函数的局部作用域，这个链被称作是作用域中的链，它常常是在代码中那些能够定义作用域的位置，当我们访问一个变量时，JavaScript总是从最里面的作用域沿着做外面的作用域去查找，直到找到我们要的那个变量、对象、函数。

##闭包
  闭包和语法作用域关系密切，关于闭包是如何工作的一个好例子就是当我们返回一个函数的引用的时候,这是一个更实际的用法。 在我们的作用域里，我们可以返回一些东西以便这些东西能够在父作用域里被访问和使用，如下：
	```
	var sayHello = function (name) {
	  var text = 'Hello, ' + name;
	  return function () {
	    console.log(text);
	  };
	};
	```
我们这里使用的闭包概念使我们在sayHello的作用域不能够被外部(公共的)作用域访问.单独运行这个函数不会有什么结果因为它只是返回了一个函数:

```
	sayHello('Todd'); // nothing happens, no errors, just silence...
```
这个函数返回了一个函数,那就意味着我们需要对它进行赋值,然后对它进行调用:
```
	var helloTodd = sayHello('Todd');
	helloTodd(); // will call the closure and log 'Hello, Todd'
```
你也可以直接调用它,你也许之前已经见到过像这样的函数,这种方式也是可以运行你的闭包:

```
	sayHello('Bob')(); // calls the returned function without assignment
```
AngularJS的$compile方法使用了上面的技术,你可以将当前作用的引用域传递给这个闭包:
```
	$compile(template)(scope);
```
我们可以猜测他们关于这个方法的(简化)代码大概是下面这个样子:
```
	var $compile = function (template) {
	  // some magic stuff here
	  // scope is out of scope, though...
	  return function (scope) {
	    // access to `template` and `scope` to do magic with too
	  };
	};
```
当然一个函数不必有返回值也能够被称为一个闭包.只要能够访问外部变量的一个即时的词法作用域就创建了一个闭包.

##作用域和this
	每一个作用域都绑定了一个不同值的this,这取决于这个函数是如何调用的.我们都使用过this关键词,但是并不是所有的人都理解它,还有当它被调用的时候是如何的不同. 默认情况下,this指向的是最外层的全局对象window.我们可以很容易的展示关于不同的调用方式我们绑定的this的值也是不同的:
```
	var myFunction = function () {
	  console.log(this); // this = global, [object Window]
	};
	myFunction();

	var myObject = {};
	myObject.myMethod = function () {
	  console.log(this); // this = Object { myObject }
	};

	var nav = document.querySelector('.nav'); // <nav class="nav">
	var toggleNav = function () {
	  console.log(this); // this = <nav> element
	};
	nav.addEventListener('click', toggleNav, false);
```
当我们处理this的值的时候我们又遇到了一些问题,举个例子如果我添加一些代码在上面的例子中.就算是在同一个函数内部,作用域和this都是会发生改变的:
```
	var nav = document.querySelector('.nav'); // <nav class="nav">
	var toggleNav = function () {
	  console.log(this); // <nav> element
	  setTimeout(function () {
	    console.log(this); // [object Window]
	  }, 1000);
	};
	nav.addEventListener('click', toggleNav, false);
```
	所以这里发生了什么?我们创建了一个新的作用域,这个作用域没有被我们的事件处理程序调用,所以默认情况下,这里的this指向的是window对象. 当然我们可以做一些事情不让这个新的作用域影响我们,以便我们能够访问到这个正确的this值.你也许已经见到过我们这样做的方法了,我们可以使用that变量缓存当前的this值, 然后在新的作用域中使用它.
```
	var nav = document.querySelector('.nav'); // <nav class="nav">
	var toggleNav = function () {
	  var that = this;
	  console.log(that); // <nav> element
	  setTimeout(function () {
	    console.log(that); // <nav> element
	  }, 1000);
	};
	nav.addEventListener('click', toggleNav, false);
```
##使用.call(),.apply()或者.bind()改变作用域

有时,你需要根据你所处理的情况来处理JavaScript的作用域.一个简单的例子展示如何在循环的时候改变作用域:
```
	var links = document.querySelectorAll('nav li');
	for (var i = 0; i < links.length; i++) {
	  console.log(this); // [object Window]
	}
```
这里的this没有指向我们需要的元素,我们不能够在这里使用this调用我们需要的元素,或者改变循环里面的作用域. 让我们来思考一下如何能够改变我们的作用域(好吧,看起来好像是我们改变了作用域,但是实际上我们真正做的事情是去改变我们那个函数的运行上下文).

.call()和.apply() .call()和.apply()函数是非常实用的,它们允许你传递一个作用域到一个函数里面,这个作用与绑定了正确的this值. 让我们来处理上面的那些代码吧,让循环里面的this指向正确的元素值:
```
	var links = document.querySelectorAll('nav li');
	for (var i = 0; i < links.length; i++) {
	  (function () {
	    console.log(this);
	  }).call(links[i]);
	}
```
	你可以看到我是如何做的,首先我们创建了一个立即执行的函数(新的函数就表明创建了新的作用域), 然后我们调用了.call()方法,将数组里面的循环元素link[i]当做参数传递给了.call()方法, 然后我们就改变了哪个立即执行的函数的作用域.我们可以使用.call()或者.apply()方法,但是它们的不同之处是参数的传递形式, .call()方法的参数的传递形式是这样的.call(scope, arg1, arg2, arg3),.apply()的参数的传递形式是这样的.apply(scope, [arg1, arg2]).

所以当你需要改变你的函数的作用域的时候,不要使用下面的方法:
  ```
	myFunction(); // invoke myFunction
  ```
而应该是这样,使用.call()去调用我们的方法
  ```
	myFunction.call(scope); // invoke myFunction using .call()
 ```
.bind() 不像上面的方法,使用.bind()方法不会调用一个函数,它仅仅在函数调用之前,绑定我们需要的值.就像我们知道的那样, 我们不能够给函数的引用传递参数.就像下面这样:
```
	// works
	nav.addEventListener('click', toggleNav, false);

	// will invoke the function immediately
	nav.addEventListener('click', toggleNav(arg1, arg2), false);
	```
我们可以解决这个问题,通过在它里面创建一个新的函数:
```
	nav.addEventListener('click', function () {
	  toggleNav(arg1, arg2);
	}, false);
```
	但是这样就改变了作用域,我们又一次创建了一个不需要的函数,这样做需要花费很多,当我们在一个循环中绑定事件监听的时候. 这时候就需要.bind()闪亮登场了,因为我们可以使用他来进行绑定作用域,传递参数,并且函数还不会立即执行:

	nav.addEventListener('click', toggleNav.bind(scope, arg1, arg2), false);
	上面的函数没有被立即调用,并且作用域在需要的情况下也会改变,而且函数的参数也是可以通过这个方法传入的.

##私有/共有的作用域

在许多编程语言中,你应该听到过私有作用域或者共有作用域,在JavaScript中,是没有这些概念的.当然我们也可以通过一些手段比如闭包来模拟公共作用域或者是私有作用域.

通过使用JavaScript的设计模式,比如模块模式,我们可以创造公共作用域和私有作用域.一个简单的方法创建私有作用域就是使用一个函数去包裹我们自己定义的函数. 就像上面所说的那样,函数创建了一个与全局作用域隔离的一个作用域:
```
	(function () {
	  // private scope inside here
	})();
```
我们可能需要为我们的应用添加一些函数:
```
	(function () {
	  var myFunction = function () {
	    // do some stuff here
	  };
	})();
	```
但是当我们去调用位于函数内部的函数的时候,这些函数在外部的作用域是不可得到的:
```
	(function () {
	  var myFunction = function () {
	    // do some stuff here
	  };
	})();

	myFunction(); // Uncaught ReferenceError: myFunction is not defined
	```
成功了,我们创建了私有的作用域.但是问题又来了,我如何在公共作用域内使用我们之前定义好的函数?不要担心,我们的模块设计模式或者说是提示模块模式, 允许我们将我们的函数在公共作用域内发挥作用,它们使用了公共作用域和私有作用域以及对象.在下面我定义了我的全局命名空间,叫做Module, 这个命名空间里包含了与那个模块相关的所有代码:
```
	// define module
	var Module = (function () {
	  return {
	    myMethod: function () {
	      console.log('myMethod has been called.');
	    }
	  };
	})();

	// call module + methods
	Module.myMethod();
	```
	上面的return声明表明了我们返回了我们的public方法,这些方法是可以在全局作用域里使用的,不过需要通过命名空间来调用. 这就表明了我们的那个模块只是存在于哪个命名空间中,它可以包含我们想要的任意多的方法或者变量.我们也可以按照我们的意愿来扩展这个模块:
	```
	// define module
	var Module = (function () {
	  return {
	    myMethod: function () {

	    },
	    someOtherMethod: function () {

	    }
	  };
	})();

	// call module + methods
	Module.myMethod();
	Module.someOtherMethod();
	```
	那么我们的私有方法该如何使用以及定义呢?总是有许多的开发者随意的堆砌他们的方法在那个模块里面,这样的做法污染了全局的命名空间. 那些帮助我们的代码运行并且是不必要出现在全局作用域的方法,就不要导出在全局作用域中,我们只导出那些需要在全局作用域内被调用的函数. 我们可以定义私有的方法,只要不返回它们就行:
	```
	var Module = (function () {
	  var privateMethod = function () {

	  };
	  return {
	    publicMethod: function () {

	    }
	  };
	})();
	```
	上面的代码意味着,publicMethod是可以在全局的命名空间里调用的,但是privateMethod是不可以的,因为它是在私有的作用域中被定义的. 这些私有的函数方法一般都是一些帮助性的函数,比如addClass,removeClass,Ajax/XHR calls,Arrays,Objects等等.

	这里有一些概念需要我们知道,就是同一个作用域中的函数变量可以访问在同一个作用域中的函数或者变量,甚至是这些函数已经被作为结果返回. 这意味着,我们的公共函数可以访问我们的私有函数,所以这些私有的函数是仍然可以运行的,只不过他们不可以在公共的作用域里被访问而已.
	```
	var Module = (function () {
	  var privateMethod = function () {

	  };
	  return {
	    publicMethod: function () {
	      // has access to `privateMethod`, we can call it:
	      // privateMethod();
	    }
	  };
	})();
	```
	这允许一个非常强大级别的交互,以及代码的安全;JavaScript非常重要的一个部分就是确保安全.这就是为什么我们不能够把所有的函数都放在公共的作用域内, 因为一旦那样做了就会暴漏我们系统的漏洞,让一些心怀恶意的人能够对这些漏洞进行攻击.

	下面的例子就是返回了一个对象,然后在这个对象上面调用一些公有的方法的例子:
	```
	var Module = (function () {
	  var myModule = {};
	  var privateMethod = function () {

	  };
	  myModule.publicMethod = function () {

	  };
	  myModule.anotherPublicMethod = function () {

	  };
	  return myModule; // returns the Object with public methods
	})();

	// usage
	Module.publicMethod();
	```
	一个比较规范的命名私有方法的约定是,在私有方法的名字前面加上一个下划线,这可以快速的帮助你区分公有方法或者私有方法:
	```
	var Module = (function () {
	  var _privateMethod = function () {

	  };
	  var publicMethod = function () {

	  };
	})();
	```
	这个约定帮助我们可以简单地给我们的函数索引赋值,当我们返回一个匿名对象的时候:
	```
	var Module = (function () {
	  var _privateMethod = function () {

	  };
	  var publicMethod = function () {

	  };
	  return {
	    publicMethod: publicMethod,
	    anotherPublicMethod: anotherPublicMethod
	  }
	})();
	```
