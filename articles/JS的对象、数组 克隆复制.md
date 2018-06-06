首先看一个例子：

```js
 var student = {
    name:"yxz",
    age:25
 }
  var newStudent = student;
  newStudent.sex = "male";
  console.log(student); //{name:"yxz",age:25,sex:"male"}
```
由此可见，将一个对象通过简单的传递赋值传递给一个新的变量时，仅仅是给该对象增添了一个别名。所以，对该别名的操作也会作用到原对象上去，所以通过newStudent.sex给对象student添加属性可以实现。然而，更多的时候我们希望newStudent和student对象是独立的，那么就需要生成一个原对象的副本，请看下面的例子：

```js
var cloneObj = function(obj){
    var str, newobj = obj.constructor === Array ? [] : {};
    if(typeof obj !== 'object'){
        return;
    } else if(window.JSON){
        str = JSON.stringify(obj), //序列化对象
        newobj = JSON.parse(str); //还原
    } else {
        for(var i in obj){
            newobj[i] = typeof obj[i] === 'object' ? cloneObj(obj[i]) : obj[i]; 
        }
    }
    return newobj;
};
//测试
var student = {
    name:"yxz",
    age:25,
    sex:"male"
};
//执行深度克隆
var newStudent = cloneObj(student);
delete newStudent.sex;
console.log(newStudent); //{name:"yxz",age:25}
console.log(student); //{name:"yxz",age:25,sex:"male"}
```


1.如果克隆对象是基本类型,直接复制就可以


    var str1 = 'abc'
    var str2 = str1
    console.log(str2)  //abc
    str1 = '123'
    console.log(str2)  //abc
   
2.不是基本类型就不可以直接复制,以数组为例


    var a=[1,2,3,4];  
    var b= a;  
    var c=b;  
    c.pop();  
    
    console.log(a)  //[1,2,3]
    console.log(b)  //[1,2,3]
    console.log(c)  //[1,2,3]
​
    

    
克隆的几种方法

1.比较简单的遍历数组赋值

    var a = [1,2,3,4];  
    var b = [];
    for(var i = 0; i < a.length; i++){
        b.push(a[i])
    }
    console.log(b)     //[1,2,3,4]
    


2.slice方法

	1)  作用：从一个数组中截取出新的数组
	2)  格式：数组名.slice（begin，end）；
	   begin表示开的下标
	   end表示结束的下标，但是在截取时不包含结束下标对应的元素
	3)  注意：
	   原数组不受影响
	   如果只设置一个参数，即begin，那么会从begin截取到最后
	   如果不写参数，那么slice方法可以实现数组的复制 */
    var arr1 = [1, 2, 3, 4]
    var arr2 = arr1.slice(0)
    console.log(arr2)        //[1,2,3,4]
    arr1 = [4, 3, 2, 1]
    console.log(arr2)     //[1,2,3,4]
​
3.assign实现深浅克隆

	//浅克隆
	var obj = { a: 1 };
	    var copy = Object.assign({}, obj);
	    console.log(copy); // { a: 1 }
	        
	​
	    var arr = [1,2,3];
	    var copy = Object.assign([], arr);
	    console.log(copy); // [1,2,3]
    
​
//深克隆

    let arr1 = [1,2,3,4,5];
    let arr2 = JSON.parse(JSON.stringify(arr1));
    console.log(arr2)  //[1,2,3,4,5]
​


//4.concat方法

* 1)    作用：拼接数组
* 2)  格式：数组名1.concat(数组名2);就是指将数组2拼接在数组1后面
* 3)  返回值：为拼接后的新的数组
* 4)  注意：原数组不受影响

	
	    let arr1 = [1,2,3,4];
	    let arr2 = arr1.concat()
	    arr2[1] = 9
	    console.log(arr1, arr2) //[1,2,3,4], [1,9,3,4]
