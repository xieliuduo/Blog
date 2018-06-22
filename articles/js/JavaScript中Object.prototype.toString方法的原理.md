# JavaScript:Object.prototype.toString方法的原理

查阅各种资料，在javascript语言中为了准确判断对象的数据类型也是拼了，各种或土法或高大上法齐上阵也未必能得到预期的结果，这篇博客来做一个简单的总结。

### 一 typeof运算符的使用和局限
实际用例最能说明问题，测试之：

		console.info("typeof  undefined : ", typeof  undefined);  
		console.info("typeof  null : ", typeof  null);  
		console.info("typeof  NaN : ", typeof  NaN);  
		console.info("typeof  true : ", typeof  true);  
		console.info("typeof  3.65 : ", typeof  3.65);  
		console.info('typeof  "weather" : ', typeof  "weather");  
		console.info("typeof  {} : ", typeof  {});  
		console.info("typeof  [] : ", typeof  []);  
		console.info("typeof  function(){} : ", typeof  function(){});  
		console.info("typeof  new Object() : ", typeof  new Object());  
		console.info("typeof  new Array() : ", typeof  new Array());  
		console.info("typeof  new RegExp() : ", typeof  new RegExp());  
		
结果如下：

		typeof  undefined :  undefined  
		typeof  null :  object  
		typeof  NaN :  number  
		typeof  true :  boolean  
		typeof  3.65 :  number  
		typeof  "weather" :  string  
		typeof  {} :  object  
		typeof  [] :  object  
		typeof  function(){} :  function  
		typeof  new Object() :  object  
		typeof  new Array() :  object  
		typeof  new RegExp() :  object 
		 
可以看到typeof运算符的特征是：
undefined就是一种独立的名为undefined类型。
null是object类型的。
对象和数组均为object类型的。
函数有专门的function类型。
但是没有专门的array类型，对数组使用typeof运算符返回的还是'object'，即typeof无法区分Object和Array。这还不是最要命的，最要命的是typeof运算符也无法区分其他内建对象类型，对于Object/Math/RegExp它都认为反正都是'object'类型。

### 二 instanceof和constructor的使用和局限

再对instanceof运算符测试：
	
	console.info("new Boolean(true) instanceof Boolean : ", new Boolean(true) instanceof Boolean);  
	console.info('new String("abc") instanceof String : ', new String("abc") instanceof String);  
	console.info("new Number(3.1415) instanceof Number : ", new Number(3.1415) instanceof Number);  
	console.info("{} instanceof Object : ", {} instanceof Object);  
	console.info("[] instanceof Object : ", [] instanceof Object);  
	console.info("new Array() instanceof Array : ", new Array() instanceof Array);  
	console.info("(function(){}) instanceof Object : ", (function(){}) instanceof Object);  
	console.info("new Function('return;') instanceof Function : ", new Function('return;') instanceof Function);  
	console.info('new RegExp("\\d+") instanceof Object : ', new RegExp("\\d+") instanceof Object);  
	console.info('new RegExp("\\d+") instanceof RegExp : ', new RegExp("\\d+") instanceof RegExp);  
	
结果全部为true，
说明instanceof运算符符合预期的行为，即可以准确判断给定所有内建对象的实际类型和父类型。constructor属性类似，任何具备这个属性的对象都可以返回其对应构造函数。

`instanceof运算符的局限`是用来做类型通用判断时需要给定运算符右边一个构造函数名，对未知构造函数构造的对象来说无法提前给定，constructor属性返回的值又不是预期的单纯的构造函数名。
instanceof运算符还有一个局限，就是无法支持基本数据类型的类型判断，比如下面第一行会抛异常，其他的测试结果全部是false。


	console.info('undefined instanceof undefined : ', undefined instanceof undefined);  
	console.info('null instanceof Object : ', null instanceof Object);  
	console.info('true instanceof Boolean : ', true instanceof Boolean);  
	console.info('"abc" instanceof String : ', "abc" instanceof String);  
	console.info('3.1415 instanceof Number : ', 3.1415 instanceof Number);  
	console.info('Boolean(true) instanceof Boolean : ', Boolean(true) instanceof Boolean);  
	console.info('String("abc") instanceof String : ', String("abc") instanceof String);  
	console.info('Number(3.1415) instanceof Number : ', Number(3.1415) instanceof Number);  
	
### 三 Object.prototype.toString的使用和局限

听说Object.prototype.toString很强大，继续测试之：

	
	function Circle(x,y,r) {  
	    this.x=x;  
	    this.y=y;  
	    this.r=r;  
	}  
	  
	function Rectangle(x,y,width,height) {  
	    this.x=x;  
	    this.y=y;  
	    this.width=width;  
	    this.height=height;  
	}  
	  
	console.info(Object.prototype.toString.call({}));  
	console.info(Object.prototype.toString.call(Math));  
	console.info(Object.prototype.toString.call(new RegExp("\\d+")));  
	console.info(Object.prototype.toString.call(new Circle(1,1,1)));  
	console.info(Object.prototype.toString.call(new Rectangle(1,1,4,3)));  
	结果如下：
	[javascript] view plain copy
	[object Object]  
	[object Math]  
	[object RegExp]  
	[object Object]  
	[object Object]  
	
说明Object.prototype.toString对所有的内建对象都能支持得非常好，经测试对客户端对象也可以支持得非常好，唯独遗憾的是对用户自定义类型无法区分，但是作为常见的判断内建类型对象和客户端类型对象已经足够使用了，进一步的测试带来更大的惊喜：该方法对基本类型也支持得非常好，测试以下语句输出：

	
	console.info(Object.prototype.toString.call(undefined));  
	console.info(Object.prototype.toString.call(null));  
	console.info(Object.prototype.toString.call(3.1415));  
	console.info(Object.prototype.toString.call(true));  
	console.info(Object.prototype.toString.call(""));  

作为通用的判断所有数据(基本类型和对象类型)类型的方法，稍微调整一下以便该方法对基本类型数据的行为与typeof保持一致(对null的处理不一致，但是显然这里更符合预期)且能对非用户自定义对象类型输出精确类型描述：


	function type(o) {  
	    var show = Object.prototype.toString.call(o);  
	    return show.substring(8,show.length - 1).toLowerCase();  
	}  
### 四 jQuery对对象的识别

搜集整理下jQuery1.6.1版本中关于对象识别的代码：

	
	// [[Class]] -> type pairs  
	class2type = {};  
	  
	// Populate the class2type map  
	jQuery.each("Boolean Number String Function Array Date RegExp Object".split(" "), function(i, name) {  
	    class2type[ "[object " + name + "]" ] = name.toLowerCase();  
	});  
	  
	jQuery.type: function( obj ) {  
	        return obj == null ?  
	            String( obj ) :  
	            class2type[ toString.call(obj) ] || "object";  
	    },  

由此可见这个版本(1.6.1)用于对象类型的判定方法并不高级，不知道最新的2.x版本内部实现如何。


--
--


在JavaScript中,想要判断某个对象值属于哪种内置类型,最靠谱的做法就是通过Object.prototype.toString方法.
		
		var arr = [];
		console.log(Object.prototype.toString.call(arr))  //"[object Array]"
		
本文要讲的就是,toString方法是如何做到这一点的,原理是什么.

### ECMAScript 3
在ES3中,Object.prototype.toString方法的规范如下:

> 15.2.4.2 Object.prototype.toString()
在toString方法被调用时,会执行下面的操作步骤:

> 1. 获取this对象的[[Class]]属性的值.
> 
> 2. 计算出三个字符串"[object ", 第一步的操作结果Result(1), 以及 "]"连接后的新字符串.
> 
> 3. 返回第二步的操作结果Result(2).

[[Class]]是一个内部属性,所有的对象(原生对象和宿主对象)都拥有该属性.在规范中,[[Class]]是这么定义的

### 内部属性	描述
[[Class]]	一个字符串值,表明了该对象的类型.
然后给了一段解释:

所有内置对象的[[Class]]属性的值是由本规范定义的.所有宿主对象的[[Class]]属性的值可以是任意值,甚至可以是内置对象使用过的[[Class]]属性的值.[[Class]]属性的值可以用来判断一个原生对象属于哪种内置类型.需要注意的是,除了通过Object.prototype.toString方法之外,本规范没有提供任何其他方式来让程序访问该属性的值(查看 15.2.4.2).

也就是说,把Object.prototype.toString方法返回的字符串,去掉前面固定的"[object "和后面固定的"]",就是内部属性[[class]]的值,也就达到了判断对象类型的目的.jQuery中的工具方法$.type(),就是干这个的.

在ES3中,规范文档并没有总结出[[class]]内部属性一共有几种,不过我们可以自己统计一下,原生对象的[[class]]内部属性的值一共有10种.分别是:
> "Array", "Boolean", "Date", "Error", "Function", "Math", "Number", "Object", "RegExp", "String".

### ECMAScript 5
在ES5.1中,除了规范写的更详细一些以外,Object.prototype.toString方法和[[class]]内部属性的定义上也有一些变化,Object.prototype.toString方法的规范如下:

15.2.4.2 Object.prototype.toString ( )
> 在toString方法被调用时,会执行下面的操作步骤:
> 
> 1. 如果this的值为undefined,则返回"[object Undefined]".
> 1. 如果this的值为null,则返回"[object Null]".
> 1. 让O成为调用ToObject(this)的结果.
> 1. 让class成为O的内部属性[[Class]]的值.
> 1. 返回三个字符串"[object ", class, 以及 "]"连接后的新字符串.


可以看出,比ES3多了1,2,3步.第1,2步属于新规则,比较特殊,因为"Undefined"和"Null"并不属于[[class]]属性的值,需要注意的是,这里和严格模式无关(大部分函数在严格模式下,this的值才会保持undefined或null,非严格模式下会自动成为全局对象).第3步并不算是新规则,因为在ES3的引擎中,也都会在这一步将三种原始值类型转换成对应的包装对象,只是规范中没写出来.ES5中,[[Class]]属性的解释更加详细:

所有内置对象的[[Class]]属性的值是由本规范定义的.所有宿主对象的[[Class]]属性的值可以是除了

"Arguments", "Array", "Boolean", "Date", "Error", "Function", "JSON", "Math", "Number", "Object", "RegExp", "String"

之外的的任何字符串.[[Class]]内部属性是引擎内部用来判断一个对象属于哪种类型的值的.需要注意的是,除了通过Object.prototype.toString方法之外,本规范没有提供任何其他方式来让程序访问该属性的值(查看 15.2.4.2).

和ES3对比一下,第一个差别就是[[class]]内部属性的值多了两种,成了12种,一种是arguments对象的[[class]]成了"Arguments",而不是以前的"Object",还有就是多个了全局对象JSON,它的[[class]]值为"JSON".第二个差别就是,宿主对象的[[class]]内部属性的值,不能和这12种值冲突,不过在支持ES3的浏览器中,貌似也没有发现哪些宿主对象故意使用那10个值.

### ECMAScript 6
ES6能够肯定的是,[[class]]内部属性没有了,取而代之的是另外一个内部属性[[NativeBrand]].[[NativeBrand]]属性是这么定义的:

内部属性	属性值	描述
[[NativeBrand]]	枚举NativeBrand的一个成员.	该属性的值对应一个标志值(tag value),可以用来区分原生对象的类型.
 [[NativeBrand]]属性的解释:

[[NativeBrand]]内部属性用来识别某个原生对象是否为符合本规范的某一种特定类型的对象.[[NativeBrand]]内部属性的值为下面这些枚举类型的值中的一个:NativeFunction, NativeArray, StringWrapper, BooleanWrapper, NumberWrapper, NativeMath, NativeDate, NativeRegExp, NativeError, NativeJSON, NativeArguments, NativePrivateName.[[NativeBrand]]内部属性仅用来区分区分特定类型的ECMAScript原生对象.只有在表10中明确指出的对象类型才有[[NativeBrand]]内部属性.

表10 — [[NativeBrand]]内部属性的值


属性值	对应类型

* NativeFunction 	 Function objects
* NativeArray	Array  objects
* StringWrapper	    String objects
* BooleanWrapper	     Boolean objects
* NumberWrapper    Number objects
* NativeMath	The Math object
* NativeDate	Date objects
* NativeRegExp	RegExp objects
* NativeError	Error objects
* NativeJSON	The JSON object
* NativeArguments	Arguments objects
* NativePrivateName	Private Name objects

可见,和[[class]]不同的是,并不是每个对象都拥有[[NativeBrand]].同时,Object.prototype.toString方法的规范也改成了下面这样:

15.2.4.2 Object.prototype.toString ( )
在toString方法被调用时,会执行下面的操作步骤:

如果this的值为undefined,则返回"[object Undefined]".
如果this的值为null,则返回"[object Null]".
让O成为调用ToObject(this)的结果.
如果O有[[NativeBrand]]内部属性,让tag成为表29中对应的值.
否则
让hasTag成为调用O的[[HasProperty]]内部方法后的结果,参数为@@toStringTag.
如果hasTag为false,则让tag为"Object".
否则,
让tag成为调用O的[[Get]]内部方法后的结果,参数为@@toStringTag.
如果tag是一个abrupt completion,则让tag成为NormalCompletion("???").
让tag成为tag.[[value]].
如果Type(tag)不是字符串,则让tag成为"???".
如果tag的值为"Arguments", "Array", "Boolean", "Date", "Error", "Function", "JSON", "Math", "Number", "Object", "RegExp",或者"String"中的任一个,则让tag成为字符串"~"和tag当前的值连接后的结果.
返回三个字符串"[object ", tag, and "]"连接后的新字符串.
表29 — [[NativeBrand]] 标志值

[[NativeBrand]]值	标志值

* NativeFunction	"Function"
* NativeArray	"Array"
* StringWrapper	"String"
* BooleanWrapper	"Boolean"
* NumberWrapper	"Number"
* NativeMath	"Math"
* NativeDate	"Date"
* NativeRegExp	"RegExp"
* NativeError	"Error"
* NativeJSON	"JSON"
* NativeArguments	"Arguments"
 
可以看到,在规范上有了很大的变化,不过对于普通用户来说,貌似感觉不到.

也许你发现了,ES6里的新类型Map,Set等,都没有在表29中.它们在执行toString方法的时候返回的是什么?
	
	console.log(Object.prototype.toString.call(Map()))   //"[object Map]"
	
	console.log(Object.prototype.toString.call(Set()))   //"[object Set]"
其中的字符串"Map"是怎么来的呢:

> 15.14.5.13 Map.prototype.@@toStringTag
> 
> @@toStringTag 属性的初始值为字符串"Map".