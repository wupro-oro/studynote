# 解析
```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```
- **参数说明:**
	- struct pollfd: 
		```c
		struct pollfd {
			int fd;		   //文件描述符
			short events;  //等待的发生事件类型
			short revents; //实际返回的事件类型
		}
		```
	- nfds:
		- int型的变量用来说明poll同时监听的个数

	- timeout:
		- int型变量
		- 等于-1: 永久等待
		- 等于0 : 立即返回
		- 大于0 : 等待超时时间(以毫秒为单位) 
- **返回值:**
	- <0 错误返回
	- =0 超时返回
	- \>0 返回结构体中revents不为0的文件描述符的个数








