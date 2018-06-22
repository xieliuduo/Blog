# JS判断一个对象是不是数组Array


1.typeof真的那么厉害吗？？

		//首先看代码
		var ary = [1,23,4];
		console.log(typeof ary); //输出结果是Object
		
上面的办法并不能实时的检测出是否是数组，只能判断其类型，所以说typeof判断基本类型数据还是挺好的，但是不能准确测试出是否是数组（typeof的具体用法以后提及，现在回归正题）
	
 2.instanceof 判断

	var ary = [1,23,4];
	console.log(ary instanceof Array)//true;
	
从输出的效果来看，还是挺令人满意的，能准确的检测出数据类型是否是数组，不要高兴的太早，大家先想想这个的缺点，我们接着说第三种方法


3.原型链方法

	var ary = [1,23,4];
	console.log(ary.__proto__.constructor==Array);//true
	console.log(ary.constructor==Array)//true 这两段代码是一样的
	
这个办法开起来好高大上哦~~，利用了原型链的方法，但是但是，这个是有兼容的哦，在IE早期版本里面`__proto__`是没有定义的哦~而且，这个仍然有局限性，我们现在就来总结一下第2种方法和第3种方法局限性

总结一下第2种方法和第3种方法局限性

instanceof 和constructor 判断的变量，必须在当前页面声明的，比如，一个页面（父页面）有一个框架，框架中引用了一个页面（子页面），在子页面中声明了一个ary，并将其赋值给父页面的一个变量，这时判断该变量，Array == object.constructor;会返回false；

原因：

1、array属于引用型数据，在传递过程中，仅仅是引用地址的传递。

2、每个页面的Array原生对象所引用的地址是不一样的，在子页面声明的array，所对应的构造函数，是子页面的Array对象；父页面来进行判断，使用的Array并不等于子页面的Array；切记，不然很难跟踪问题！

4.通用的方法

	var ary = [1,23,4];
	function isArray(o){
	return Object.prototype.toString.call(o)=='[object Array]';
	}
	console.log(isArray(ary));



  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  1.Array.isArray(obj) 调用数组的isArray方法
  
  2.obj instanceof Array  判断对象是否是Array的实例
  
  3.Object.prototype.toString.call(obj) ===‘[object Array]’  
  
  Object.prototype.toString方法会取得对象的一个内部属性［［Class］］，然后依据这个属性，返回一个类似于［object Array］的字符串作为结果，call用来改变toString的this指向为待检测的对象
     
  4.判断对象是否有push等数组的一些方法。（这个方法有兼容问题，但也是一个简单易用的方法）
    
  5.obj.constructor===Array   //true


同理判断一个对象是否是函数：
console.log(Object.prototype.toString.call(obj)==='[object Function]')    //true或false 