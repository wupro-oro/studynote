## platform 总线
- 虚拟总线(抽象出来的)
- platform总线相对于i2c、usb、spi、等总线不同，它们属于物理总线
- platform工作体系都定义在/drivers/base/platform.c中，base这里面的文件不需要我们去动的改代码的，是由内核的人写的一种基础架构，platform.c中就是platform的实现原理，他作为内核中的一种机制，他也是被内核人员实现的
- [linux驱动之platform平台总线工作原理（一）](https://blog.51cto.com/whylinux/1905642)
- 
### 总线概念
- 总线的概念之所以有，就是为了让总线下面的设备和驱动进行管理起来。无论是虚拟的总线还是真实的总线，他们都有设备和驱动，他们需要被管理，所以有了总线的概念。
- 总线在linux中，下面有着两个分支，一个是**设备分支**，一个是**驱动分支**，设备和驱动都有名字，**总线利用名字**将设备和驱动进行匹配来管理，因为光有设备没有驱动无法工作，光有驱动没有设备也无法工作，但是设备会有很多，驱动会有更多，不能杂乱无章的进行管理，所以有了总线来进行管理，**同一个总线下的是一类设备**，平台总线下的设备就是扩展到cpu地址总线上去的设备，下面的驱动就是这些设备的驱动
### 为什么要有平台总线
- 有一些外设他是直接扩展到cpu的地址总线上进行链接的，所以这一类设备可以说根本就没有总线的概念，比如led，蜂鸣器（定时器），这类是可以直接通过地址就能访问到的，他们本没有总线的概念，不像i2c总线，他本身就对应于真实存在的i2c总线设备，但是我们又不能搞的有总线又没有总线，弄的不统一，因为这样会觉得很乱，你还要区分谁有总线，谁没有总线，所以弄的所有设备都有总线，真正的有总线的设备，像i2c设备，你有i2c总线你就用i2c总线的规则去玩，但是没有真正总线的设备，用地址去访问的设备，就给他们提供了一种平台总线，来给他们进行连接使用的，所以我们说平台总线是虚拟的。
### 平台总线下的两员大将
#### platform_device
- 设备结构体**platform_device**是platform总线下的一个具体的抽象出来的设备，这个结构体类型定义出来的变量，每一个定义出来的变量，就是平台总线中的一种设备
```c
struct platform_device {
	const char	*name;  //设备的名字
	int		id;			//一般填写-1
	bool		id_auto;
	struct device	dev;///每个设备通用的属性的部分的一些内容，被包含在这个结构体中，所以每一个platform_device平台总线的设备里面都需要有一个这个struct device结构体。
	u32		num_resources; //设备使用到的resource的个数
	struct resource	*resource;//一个确切的资源由该结构体定义资源，所以我们就

	const struct platform_device_id	*id_entry;
	char *driver_override; 
	//这也是一个数组，为了省内存，不让platform_device占用过多内存，所以用的都是指针来指向的方式，而不是直接使用数组的方式

	/* MFD cell pointer */
	struct mfd_cell *mfd_cell;

	/* arch specific additions */
	struct pdev_archdata	archdata;
	//留个我们自己用的，可以将设备特有的一些东西放到这里，用来提供扩展性的，因为有可能你的设备是一种特有的，所以可以让你自己
};
```
- struct resource 结构体：一个设备用到的资源定义成了一个资源数组，资源数组里面有好多个元素，每一个元素都是一个资源总之来说这个struct resource就是用来记录当前这设备用到了哪些资源的，因为每一个设备用的资源可能是多个，所以每一设备内部定义了一个struct resource类型的结构体指针，也就是资源数组
#### platform_driver
- **platform_driver**结构体类型定义出来的每一个变量，就是平台总线下的每一个驱动，有几个平台总线的设备就定义几个platform_device的变量，有几个平台总线的驱动就定义几个platform_driver的变量
```c
struct platform_driver {
	int (*probe)(struct platform_device *);
	int (*remove)(struct platform_device *);
	void (*shutdown)(struct platform_device *);
	int (*suspend)(struct platform_device *, pm_message_t state);
	int (*resume)(struct platform_device *);
	struct device_driver driver;
	const struct platform_device_id *id_table;
	bool prevent_deferred_probe;
};
```
- probe: 驱动探测函数，当我们的驱动找到一个设备的时候，这个驱动就会调用这个probe函数来试图去探测这个设备是不是我这个驱动对应的设备
- remove: 用来删除一个设备的，将当前驱动里面支持的这个设备给去掉，类似于卸载这个设备，此时这个设备已经在系统中看不到了
- shoutdown: 关闭一个设备，让一个工作停止控制，此时这个设备在系统中还能看到。
- suspend: 挂起一个设备，比如电源管理时，暂时将某一个设备给进行挂起，比如系统进入待机状态时，就需要将这个设备进行挂起。
- resume: 恢复一个挂起的状态，当系统待机状态结束时，就用这个函数将挂起状态的设备给恢复过来。唤醒函数。
- struct device_driver driver: 所有设备驱动共用的一些属性，是驱动程序在内核驱动框架中的抽象
- id_table:设备id表，因为一个驱动可能知识好多个设备。有多少同类设备就都写到这个驱动的id表中，将来只要设备id表中的id和这个驱动id表中的id对应起来，就都能够使用这个驱动来操作。

### 框架图
[[platform平台框图.png]]
