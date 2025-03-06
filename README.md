FreeRTOS移植到Windows Visual Studio开发平台的步骤：
1. 直接双击FreeRTOS\FreeRTOS\Demo\WIN32-MSVC\WIN32.sln, 即可打开Visual Studio的FreeRTOS工程
2. 删除WIN32.vcxproj中未配置的文件和目录
3. 合并和移动相关的目录，调整目录结构

目录说明：
WIN32-MSVC: Visual Studio平台相关的接口文件, port.c/portmacro.h/FreeRTOSConfig.h/main.c
MemMang: 内存管理文件, heap_1~heap_5
FreeRTOS-Plus-Trace: 原FreeRTOS-Plus中关于Trace的文件依赖
Common/include: 头文件相关依赖