昨天和今天提交了几份文件，但是今天一看自己的绿格子，却没变绿，而且还显示no contribution ,哇这就很气。 
但是代码已经提交在github上了，那就是提交的帐号出错了， 
赶紧检查检查下在本机上的帐号

*   打开你的终端进入你的项目目录
*        $ git config user.email   
*        看看 上面打印出来的 邮箱地址 是不是你注册github的时候 的邮箱 如果不是的话 改成 你的那个邮箱
*        git config  user.email "xxxxxx@ｘｘ.com"  然后提交你的代码 github就会 点亮绿点了
*    如果你想全局修改你的git 邮箱 加一个 --global
*        git config --global user.email "xxxxxx@ｘｘ.com"
