## GPIO子系统接口的使用

- GPIO操作分为“输入操作”和“输出操作”，“输入操作”的GPIO引脚的电平由外设决定，“输出操作”的GPIO引脚的电平由CPU决定，Linux内核已经实现以下关于GPIO调用的库函数，只需要在需要的适合调用即可[Linux驱动开发——（Linux内核GPIO操作库函数）gpio（1）](https://blog.51cto.com/u_6043682/3705032#int__gpio_requestunsigned_gpio_%09const_char_label_5)

	---
	#### int gpio_request(unsigned gpio, const char *label)
	-   函数功能：CPU的任何一个GPIO引脚硬件资源对于Linux内核来说都是一种宝贵的资源，如果某个内核程序要想访问这个GPIO引脚资源，首先必须想Linux内核申请资源（类似malloc）
	- 参数说明
		- gpio：gpio:GPIO引脚硬件在linux内核中的软件编号,也就是  对于任何一个GPIO引脚,linux内核都给分配一个唯一的  软件编号(类似GPIO引脚的身份证号)
		 - label：给申请的硬件GPIO引脚指定的名称,随便取。
		 - 返回值：看内核源码
		 - 头文件：
		 \#include<linux/gpio.h>  
		 \#include <linux/kernel.h>  
		 \#include <linux/types.h>  
		 \#include <linux/errno.h>
	---
	
 	#### void gpio_free(unsigned gpio)
	
	- 函数功能：内核程序如果不再使用访问GPIO硬件资源记得要将硬件资源归还给linux内核,类似free
	- 参数说明：
		- gpio：要释放的GPIO硬件资源对应的软件编号
	---
	#### int gpio_direction_output(unsigned gpio, int value)
	
	-   函数功能：配置GPIO引脚为输出功能,并且输出一个value值(1高电平/0低电平)
	-    参数说明：
			- gpio：GPIO硬件对应的软件编号
			- value：输出的值

	---
	#### int gpio_direction_input(unsigned gpio)
	- 函数功能：配置GPIO为输入功能

	---
	
	#### int gpio_set_value(unsigned gpio, int value)
	-   函数功能：设置GPIO引脚的输出值为value(1:高/0:低)，前提是必须首先将GPIO配置为输出功能

	---
	#### int gpio_get_value(unsigned gpio)
	-   函数功能：获取GPIO引脚的电平状态,返回值就是引脚的电平状态(返回1：高电平;返回0：低电平)，此引脚到底是输入还是输出没关系！