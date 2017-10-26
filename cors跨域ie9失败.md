##cors跨域ie9失败
####问题
在使用**cors**进行跨域的时候，谷歌浏览器没问题，但**ie9**打开时出现错误

	{"readyState":0,"status":0,"statusText":"Error:拒绝访问。\r\n"}

请求的地址为

	http://swtsrv01.swt1993.com:10443/swtsj

因为地址本身存在端口，而且后端服务器做了**https安全**，所以当请求使用http时，浏览器会默认将http改成https，并且将端口改成默认端口，此时端口不为10443，端口变化，所以cors跨域失效。
####解决
后端将端口又做了一层代理，把地址变成
	
	http://tech.swt1993.com/swtsj

此时端口为默认端口，这样贬称https时端口还是默认端口，于是跨域成功