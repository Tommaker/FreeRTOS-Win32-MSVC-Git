# 项目说明
## 1. FreeRTOS移植到Windows Visual Studio开发平台的步骤
1. 直接双击FreeRTOS\FreeRTOS\Demo\WIN32-MSVC\WIN32.sln, 即可打开Visual Studio的FreeRTOS工程
2. 删除WIN32.vcxproj中未配置的文件和目录
3. 合并和移动相关的目录，调整目录结构

### 1.1 目录说明、
WIN32-MSVC: Visual Studio平台相关的接口文件, port.c/portmacro.h/FreeRTOSConfig.h/main.c
MemMang: 内存管理文件, heap_1~heap_5
FreeRTOS-Plus-Trace: 原FreeRTOS-Plus中关于Trace的文件依赖
Common/include: 头文件相关依赖

### 1.2 目前代码使用的FreeRTOS的版本为: V11.1.0+
问题：如何查看当前使用的FreeRTOS版本?
在task.h文件中查看tskKERNEL_VERSION_NUMBER的定义即可知道，例如下面的宏定义:
#define tskKERNEL_VERSION_NUMBER                      "V11.1.0+"



## 2. 内存管理
1. FreeRTOS中的内存管理用于管理系统中的内存资源，是操作系统的核心模块之一，主要用于内存初始化、内存分配、内存释放；

### 2.1 为什么不使用C标准库中的内存管理函数
在嵌入式实时操作系统中，调用malloc和free是危险的，原因如下：
1. 可能在小型的嵌入式设备中不可用，小型嵌入式设备的RAM不足；
2. malloc和free的实现需要占用较大的内存空间；
3. 可能存在线程安全问题；
4. 每次调用的所消耗的时间不确定；
5. 可能产生碎片；
6. 可能会使链接器变得更复杂。

### 2.2 五种内存管理算法
FreeRTOS根据不同的应用场景提供了五种内存管理算法，源文件位置：FreeRTOS\Source\portable\MemMang路径。
1. heap_1.c: 只能申请内存，不能释放内存，内存申请所消耗的时间使固定的；    使用场景：不存在删除动态内存的场景，如初始化后内存申请结束，后续不会再使用动态内存
2. heap_2.c: 支持内存的申请和释放，但是释放的内存不能与连续的空闲内存合并。使用场景：申请/释放的内存为固定大小的场景，如反复删除任务、队列、信号量等
3. heap_3.c：简单封装了标准C库函数中的malloc和free，再调用malloc/free前后添加调度器的挂起和恢复
4. heap_4.c：同于heap_2.c，在其基础上增加了合并连续的空闲内存功能。       使用场景：实用性很强，用于随机申请和释放内存的应用中，灵活性高；
5. heap_5.c：同于heap_4.c，在其基础上增加了允许内存跨越多个非连续的内存区。使用场景：若使用了多个非连续的内存区域，可以使用。