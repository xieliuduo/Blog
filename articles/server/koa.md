* koa 最大的特点就是 避免过多的异步回调
* 安装环境要求 要> node 7.6

```js
	var koa =require('koa');
	var app =new koa();
	
	app.use( async(ctx)=>{
	  ctx.body='hello koa2'
	})
	
	app.listen(3000); 
```