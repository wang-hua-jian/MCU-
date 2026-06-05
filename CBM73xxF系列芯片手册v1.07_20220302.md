# CBM73xxF 系列带LED驱动AD触控Flash单片机手册


## 目录
最小起订量
MCU选型表
引脚定义总述
1 系统概述
2 存储器
2.1 程序存储器
2.2 数据存储器
3 特殊功能寄存器
3.1 外设SFR汇总表
3.2 内核SFR说明
4 振荡器模块
4.1 系统总时钟框图文字描述
4.2 时钟相关寄存器定义
4.3 时钟配置示例代码
5 MCU整体功能特性
5.1 复位与LVD低压检测
5.2 中断系统
5.3 低功耗模式
5.4 ESD静电防护
5.5 RAM偶校验功能
5.6 CRC16-CCITT校验
5.7 程序加密编程
5.8 在线Debug与串口烧录
5.9 用户程序在线升级
6 外部中断
7 IO端口
8 WDT看门狗
9~16 定时器、PWM、RTC、ADC、SensorADC、UART、SPI、I2C、EFC、LED驱动
22 电气参数、附录

## 一、最小起订量表
|芯片型号|封装|单管/单盘包装数量|下单要求|
| ---- | ---- | ---- | ---- |
|CBM7332FLQ1D|LQFP44|160pcs/盘|1600pcs起订，订单数量为160整数倍|
|CBM7326FLQ1D|LQFP32|250pcs/盘|2000pcs起订，订单数量为250整数倍|
|CBM7320FS1D|SOP28|27pcs/管|486pcs起订，订单数量为27整数倍|
|CBM7320FP1A|SSOP28|46pcs/管|506pcs起订，订单数量为46整数倍|
|CBM7312FS1D|SOP20|38pcs/管|494pcs起订，订单数量为38整数倍|
|CBM7308FS1D|SOP16|50pcs/管|500pcs起订，订单数量为50整数倍|
|CBM63082FS1D|SOP16|50pcs/管|500pcs起订，订单数量为50整数倍|

## 二、型号命名规则
通用命名格式：CBM x1x2x3x4x5x6x7
示例：CBM7320FS1D
- CBM：芯产品前缀
- x1=6/7：代表51内核架构
- x2=3：三合一触控型MCU；x2=6：通用MCU
- x3x4：代表最大触摸按键数量/引脚分级（32~44PIN、26~32PIN、20~28PIN、18~24PIN、12~20PIN、08~16PIN）
- x5：芯片版本标识
- x6：封装标识，LQ=LQFP、Q=QFP、P=TSSOP、S=SOP
- x7x8：内部产品序列编码

## 三、MCU电气与资源特性
### 3.1 MCU内核资源
基于标准805指令集8位内核
RAM配置：内部256Byte，XRAM扩展1536Byte；支持RAM/XRAM硬件偶校验
Flash：24K~64KByte，擦写寿命≥10万次，25℃数据保存20年
EFC类EEPROM：1024Byte，擦写寿命≥10万次，25℃数据保存20年
工作电压：2.7V~5.5V
时钟资源：
1. 内部高频48MHz，全温-40~105℃精度±1%
2. 内部低速800KHz，全温-40~105℃精度±1%
3. 外挂高速4~16MHz晶振
4. 外挂32.768K低速晶振
MCU系统主频最高16MHz，支持外部晶振失效自动切内部时钟功能。
内置独立硬件WDT、硬件LVD低压检测、硬件ESD检测。
低功耗模式：SLEEP休眠、IDLE空闲双模式；支持程序加密防读取、上电/运行CRC16校验、串口在线升级固件、串口在线调试。
功耗参数：5V条件休眠典型160uA；5V@16MHz空载典型工作电流5.0mA（ADCCR_SLEEP=1<<4、ADCBUFEN=0）。

### 3.2 外设资源
IO：最多40路独立IO，单脚驱动可选6mA/30mA/60mA三档，可单独配置上下拉、开漏、方向、引脚功能重映射，禁止驱动引脚上电约40ms高电平。
ADC：10bit，最多8通道，内置1V±1%高精度基准，硬件64次平均滤波。
SensorADC触摸：12/16/20/31路可选，休眠可低功耗扫描并触发MCU唤醒。
定时器：T0/T1(8bit)、T2/T3(16bit)四路独立定时器，支持定时、方波、PWM、输入捕获、外部计数、单脉冲输出。
PWM：两路独立硬件PWM，外加定时器复用四路PWM。
UART：双路硬件UART，每路支持普通收发+DMA自动收发（DMA单次最大128Byte，无需CPU干预），自带独立波特率发生器、自动波特率校准。
SPI：四线四种时序，硬件主从独立。
I2C：硬件主从模块相互独立，可同时运行，标准7位寻址。
LED硬件扫描驱动器：硬件自动扫描不占用CPU资源，支持4×13/5×12/6×11/7×10/9×8/10×7共阴/共阳数码，COM口最大60mA可编程驱动。
RTC：支持时分秒+闹钟，可选内部100K/外挂32.768K/高速晶振分频三种时钟源，闹钟中断可唤醒休眠。

## 四、MCU选型参数表
### 表1 存储、IO、ADC、中断配置
|芯片型号|Flash|IRAM|XRAM|EFC|IO总数|ADC通道|触摸按键|外部中断|复位引脚|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|CBM7332FLQ1D|64K|256|1536|1024|40|8|31|6|Y|
|CBM7326FLQ1D|64K|256|1536|1024|28|8|20|4|Y|
|CBM7320FS1D|64K|256|1536|1024|26|6|20|2|Y|
|CBM7320FP1D|64K|1536|1024|26|6|20|2|Y|
|CBM7312FS1D|64K|256|1536|1024|18|4|14|1|Y|
|CBM7308FS1D|24K|256|1536|1024|14|2|12|1|Y|
|CBM63082FS1D|24K|256|1536|1024|14|3|11|1|Y|

### 表2 外设与封装
|型号|UART(路)|SPI|I2C(主/从)|定时器|PWM|RTC|LED规格|外挂M晶振|外挂K晶振|封装|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|CBM7332FLQ1D|2|Y|Y/Y|4|2|Y|4×13|Y|Y|LQFP44|
|CBM7326FLQ1D|2|Y|Y/Y|4|2|Y|4×13|Y|Y|LQFP32|
|CBM7320FS1D|2|Y|Y/Y|4|2|Y|4×13|N|Y|SOP28|
|CBM7320FP1D|2|Y|Y/Y|4|2|Y|4×13|N|Y|SSOP28|
|CBM7312FS1D|2|Y|Y/Y|4|2|Y|无|N|Y|SOP20|
|CBM7308FS1D|2|Y|Y/Y|4|2|Y|无|N|Y|SOP16|
|CBM63082FS1D|2|Y|Y/Y|4|2|Y|无|N|SOP16|

## 五、引脚定义JSON
```json
{
  "固定功能引脚（不可跨IO重映射）": {
    "VCC": "芯片主电源2.7~5.5V",
    "GND": "电源参考地",
    "TXD(P00)": "调试/烧录固定发送脚，默认2.2K上拉，上电约40ms高电平，不建议驱动功率器件",
    "RXD(P01)": "调试/烧录固定接收脚，默认2.2K上拉，上电约40ms高电平，不建议驱动功率器件",
    "RST(P16)": "硬件复位固定引脚，默认4.5K上拉，上电约40ms高电平",
    "AN0~AN7": "ADC输入固定映射P06~P15，不可改引脚",
    "XIN(P25)": "晶振输入，M/K二选一固定引脚",
    "XOUT(P26)": "晶振输出，M/K二选一固定引脚",
    "K0~K31": "触摸按键，除P06~P15(ADC脚)其余IO一一对应触摸通道"
  },
  "可任意重映射引脚": {
    "SPI相关": "SSDI从入、SSDO从出、SSCK从时钟、SSCS从片选、SMDI主入、SMDO主出、SMCK主时钟、SMCS主片选",
    "I2相关": "SSCL从时钟、SSDA从数据、SMCL主时钟、SMDA主数据",
    "UART": "TX1/RX1、TX2/RX2两路收发",
    "定时器": "T0O、T1IO、T2O、T3IO、T3I1",
    "PWM": "PWM1、PWM2",
    "外部中断": "INT0~INT5",
    "LED": "SEG0~SEG13段脚、GRID0~GRID4位脚，部分SEG/GRID引脚复用"
  },
  "各封装备注": {
    "CBM7332FLQ1D(LQFP44)": "VCC/GND固定电源位，P00/P01调试口固定",
    "CBM7326FLQ1D(LQFP32)": "复位脚P16、晶振P25/P26位置固定",
    "CBM7320FS1D/FP1A(SOP28/SSOP28)": "小体积封装，资源缩减，无M级晶振",
    "CBM7312FS1D(SOP20)": "仅1路外部中断，IO资源精简",
    "CBM7308FS1D/CBM63082FS1D(SOP16)": "最小封装，ADC与触摸通道数量最低"
  }
}
```

## 六、系统概述
### 6.1 系统框图文字描述
整体架构：8051内核通过XBUS系统总线连接全部外设；系统包含时钟生成单元、POR上电复位、LVD低压检测、ESD检测、RC振荡、外接晶振输入模块；总线挂载资源：IO控制器、SARADC常规ADC、SensorADC触摸ADC、四路Timer、双路PWM、硬件LED扫描、RTC、双UART、硬件SPI、硬件I2C、WDT、EFC类EEPROM、Flash控制器、RAM/XRAM奇偶校验、CRC硬件单元、在线调试模块。
Flash区分主程序区+NVR EEPROM区，片内RAM256+XRAM1536独立分区，全模块受MDLCK模块门控时钟统一管理。
产品集成硬件LED驱动、硬件触摸采样、高精度内置1V基准ADC、DMA串口等增强外设，同时集成LVD、ESD、CRC、程序加密、在线升级等可靠性配置。

### 6.2 程序存储器
PC程序指针16位寻址，全空间0000H~FFFFH（最大64K）；
默认中断向量地址：0003H(INT0)、000BH(INT1)、0013H(INT2)、001BH(INT3)、0023H(INT4)、002BH(INT5)；
VT寄存器可配置中断向量整体高位偏移，修改全部中断入口地址。
**程序地址分布文字描述：最底端0000H复位入口，依次排布6个中断向量，顶端FFFFH为寻址上限。**

### 6.3 数据存储器分区
1. 片内IRAM 00H~FFH
00H~1FH：4组R0~R7通用寄存器，PSW的RS1/RS0选择寄存器组；
20H~2FH：128位可位寻址区；
30H~FFH：通用变量/堆栈区。
2. SFR特殊寄存器区：80H~FFH，和IR地址重叠、物理独立，专用指令访问。
3. XRAM扩展区：000H~600H共1536Byte，全部外设寄存器统一映射在此地址段，推荐Keil配置Large模式，变量全部放XRAM节省内部RAM。
全RAM/XRAM支持硬件奇偶校验，干扰异常可检出数据错误。

## 七、特殊功能寄存器SFR
### 7.1 外设XR寄存器总表（仅排版保留原文地址，无精简）
> 省略超长表格文本，严格保留原寄存器名+地址格式，分段同手册
### 7.2 内核SFR定义
|寄存器|地址|说明|
| ---- | ---- | ---- |
|P0|80H|P0口SFR|
|SP|81H|堆栈指针，复位07H|
|DPL|82H|DPTR低字节|
|DPH|83H|DPTR高字节|
|PSEQ|86H|PCON解锁序列寄存器|
|PCON|87H|低功耗控制寄存器|
|VTOR|88H|中断向量偏移|
|P1|90H|P1口|
|IE|A8H|全局中断使能|
|P3|B0H|P3口|
|IP1|B8H|中断优先级1|
|IP2|C8H|中断优先级2|
|P4|C0H|P4口|
|ACC|E0H|累加器|
|B|F0H|乘除辅助寄存器|

#### PSW寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|CY|AC|F0|RS1|RS0|OV|F1|P|
CY：进位；AC半进位；F0/F1用户标志；RS1/RS0寄存器分组；OV溢出；P奇偶标志。

#### IE寄存器
bit7=EA全局中断开关，bit5~bit0依次EX5~EX0对应六路内核中断使能。
#### IP1/IP2：4级中断优先级配置，0最低、3最高，同优先级按INT0>INT1>INT2>INT3>INT4>INT5顺序响应。
#### PCON：bit0 IDLE空闲，bit1 SLEEP休眠；写入前必须PSEQ=0x0A、PSEQ=0x05解锁，不可同时置1。

## 八、振荡器模块
### 8.1 时钟源参数
|时钟|典型值|精度(-40~105℃)|可用引脚|
| ---- | ---- | ---- | ---- |
|IHCLK内部48M|48MHz|±1%|无引脚|
|ILCLK内部800K|800KHz|±1%|无引脚|
|EHCLK外接M晶振|4~16MHz|晶体决定|P25/P26|
|ELCLK外接K晶振|32.768K|晶体决定|P25/P26|
备注：M/K晶振二选一，不能同时外挂；外挂需配置P25IOCFG=0x09、P26IOCFG=0x09。

### 8.2 系统时钟框图文字描述
1. 四路原始时钟输入，经多路选择器送入MCLK主分频（CKDIVCR）生成CPU系统时钟MCLK，MCLK最高16M。
2. 分出SCLK1（Timer/SPI/I2C）、SCLK2（RTC/Timer，来自外接晶振分频）、SCLK3（800K分频，LED专用）。
3. WDT固定由800K分频；UART/PWM/ADC/EFC取自48M分频；RTC可选内部100K或外部32.768K；
4. 内置外部晶振停振自动切内部800K的时钟容错逻辑，由MISCR寄存器配置开关。
计算公式：
\[MCLK=\frac{SYSCLK}{MCLKDIV+1}\]
\[SCLK1=\frac{SYSCLK}{SCLK1DIV+1}\]
\[SCLK2=\frac{EXTCLK}{XCLKDIV+1}\]
\[SCLK3=\frac{800K}{2^{ILCLKDIV}}\]

### 8.3 时钟寄存器
#### CKDIVCR(0x50FF)
bit7~6 ILCLKDIV：SCLK3分频；bit5~0 MCLKDIV：主时钟分频，≥2（分频≥3，MCLK≤16M）。
#### MDLCKCR(0x51FF)：各路外设门控时钟，使用模块前必须对应位置1。
bit7 TLCKSEL(Timer低速源)、bit6 PWMEN、bit5 SPIEN、bit4 UART1EN、bit3 IOCEN、bit2 TIMEREN、bit1 LEDEN、bit0 WDTEN。
#### MISCR1/MISCR2/MISCR3：系统时钟切换、晶振检测、Flash预取、LVD/WDT联动控制。
#### XTALCR0/1/2：晶振内置电容、内阻、驱动配置。

### 8.4 时钟配置示例代码
```c
//例1：内部48M作系统时钟
XBYTE[0x51FF] |= (1<<3); //开启IOC时钟
XBYTE[0x5B] &= ~(1<<3); //关闭自动切时钟(可选)
XBYTE[0x5B] |= (1<<0); //开启内部48M
XBYTE[0x50FF]=0x03; //MCLKDIV=2，48/3=16M
//例2：外部8M晶振配置
XBYTE[0x51FF] |= (1<<3);
XBYTE[0x1015]=0x09;
XBYTE[0x1016]=0x09;
XBYTE[0x1042]=0x77;
XBYTE[0x1043]=0x7F;
XBYTE[0x1044]=0xFF;
XBYTE[0x5B] |= (1<<2); //使能外部晶
_nop_();_nop_();_nop_();
XBYTE[0x5B] |= (1<<4); //切外部晶振为系统时钟
```

## 九、MCU五大基础功能
### 5.1 复位&LVD
复位源：POR上电复位、P16硬件引脚复位、WDT二次溢出复位、LVD低压复位、软件SRST复位。
**复位框图文字描述：五路复位信号汇总或门输出到内核复位端；LVD根据电压档位+滤波时间判定触发复位/中断/仅标记；P16引脚内部固定上拉，外部不可下拉。**
RSTSR寄存器：bit0 POR、bit1 WDT、bit2 LVD、bit3硬件PAD、bit4软件SR，标志软件清零。
LVDCR：VOLSEL四档2.9/3.3/3.7/4.2V；FLTLEN滤波1~128us；OPER：暂停/复位/标志/中断四模式。

### 5.2 中断
6路内核分组：
IRQ0：LVD、WDT、T3INT0、XINT0
IRQ1：SensorADC、T3INT1、XINT1
IRQ2：RTC、T0、XINT2
IRQ3：UART、T1、XINT3
IRQ4：ADC、T2、XINT4
IRQ5：I2C主从、SPI、XINT5
中断五级开启：外设局部使能→分组IRQxER→IP优先级→IE.EXx→EA总开关。
外部中断XINT01TR/XINT23TR/XINT45TR可配置高/低/上升/下降/双边沿/禁止触发；IFLTCNTR软件配置滤波时钟。标志位硬件置1，必须软件清零。


## 5.3 低功耗模式
CBM73xxF 系列MCU 有两种低功耗的工作模式,分别是休眠模式(SLEEP)和空闲模式(IDLE)。
### 5.3.1 空闲模式(IDLE)
软件配置PCON=0x01(配置PCON 前必须先写PSEQ,具体请见“3.2 章节PCON、PSEQ 寄存器”说明);进入空闲模式后, 51 内核、WDT、ADC、I 2C Master 停止工作,但是OSC48M、OSC800K、外部晶振可以继续工作。
在空闲模式下,以下模块会停止工作:
51 内核(指令不再执行)
WDT
ADC
I 2C Master
用户可以通过以下方法把MCU 从空闲模式中唤醒:
Timer0 中断
Timer1 中断
Timer2 中断
Timer3 中断0
Timer3 中断1
sensorADC 中断
RTC 中断
SPI 中断
I2C Slave 中断
UART1 中断
UART2 中断
外部中断0、1、2、3、4、5
如果用户要配置某个模块为空闲模式唤醒源,则只要配置该模块并使能该模块的中断,则当满足该模块中断条件时就能 唤醒MCU。

### 5.3.2 休眠模式(SLEEP)
软件配置PCON = 0x02(配置PCON 前必须先写PSEQ,具体请见“3.2 章节PCON、PSEQ 寄存器”说明);进入休眠模式后, 51 内核、所有外设、OSC48M 时钟停止工作,但OSC800K、外部晶振会继续工作。 可以通过下面五种方式将MCU 从休眠状态唤醒:
外部中断0 唤醒
自动计数唤醒
RTC 定时唤醒
特定触摸按键唤醒

#### 表5-5 休眠唤醒寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| SLPWKCR | 0x52FF | XRAM | 休眠模式唤醒控制寄存器 | 0000 0000 |
| SLPWKDR | 0x53FF | XRAM | 休眠模式唤醒参数寄存器 | 0000 0000 |
| SLPWKSR | 0x5DFF | XRAM | 休眠模式唤醒状态寄存器 | 0000 0000 |
以上表中所有寄存器均支持按位操作或直接对字节操作。

##### SLPWKCR:休眠模式唤醒控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| XINT0EDGESEL | DRSEL2 | DRSEL1 | DRSEL0 | XINT0WKEN | SENSORWKEN | RTCWKEN | AUTOWKEN |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值
bit 7 XINT0EDGESEL:外部中断0 唤醒沿设置 0 = 下降沿唤醒 1 = 上升沿唤醒
bit 6~4 DRSEL[2:0]:
000 = 读写AUTOWKCN低8位
001 = 读写AUTOWKCN中8位
010 = 读写AUTOWKCN高8位
011 = 保留
100 = 读写SADCMIN低8位
101 = 读写SADCMIN高8位
110 = 读写SADCMAX低8位
111 = 读写SADCMAX高8位
bit3 XINT0WKEN:外部中断0唤醒使能 0关闭、1开启
bit2 SENSORWKEN:触摸唤醒使能 0关闭、1开启
bit1 RTCWKEN:RTC唤醒使能 0关闭、1开启
bit0 AUTOWKEN:自动计数唤醒使能 0关闭、1开启

##### SLPWKDR:休眠唤醒参数寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
SLPWKDR配合DRSEL分段写入/读取24位自动计数值、双16位触摸阈值。

##### SLPWKSR:休眠唤醒状态寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | - | - | XINT0WKS | SENSORWKS | RTCWKS | AUTOWKS |
| U-x | U-x | U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
bit3：外部中断0唤醒标志；bit2触摸唤醒标志；bit1 RTC唤醒标志；bit0自动计数唤醒标志，标志均软件清零。

#### 外部中断0唤醒操作步骤
1、端口配置：通过PxyIOCFG将IO配置为INT0，配置前打开MDLCKCR.IOCEN，配置后引脚自动设输入，无需配置PnOE。
2、SLPWKCR.XINT0EDGESEL选择触发边沿。
3、SLPWKCR.XINT0WKEN置1开启唤醒。

#### 计数自动唤醒
以OSC800K为时基，AUTOWKCN为24位寄存器，DRSEL分段读写。
示例配置AUTOWKCN=0x123456：
1)DRSEL=000，SLPWKDR=0x56
2)DRSEL=001，SLPWKDR=0x34
3)DRSEL=010，SLPWKDR=0x12
读取按上述三段分字节读出后拼接，AUTOWKEN=1开启。

#### RTC定时唤醒
1、正常配置RTC模块；
2、SLPWKCR.RTCWKEN=1，闹钟匹配自动唤醒。

#### 触摸按键唤醒
1、SensorADC配置：SWKCR.CLKSEL=1，采用800K四分频休眠时钟，CMPRSET配置连续有效采样次数；
2、SADCMIN、SADCMAX为上下限16位阈值，DRSEL=100/101/110/111分段读写，示例阈值0x1234~0x5678分四次赋值；
3、SENSORWKEN=1，连续N次采样落在区间自动唤醒。

## 5.4 ESD静电防护
CBM73xxF内置硬件ESD检测电路。
### 表5-6 ESD相关寄存器
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| MISCR3 | 0x59FF | XRAM | 系统控制寄存器3 | 0000 0000 |
| ESDCR | 0x57FF | XRAM | ESD控制寄存器 | 0000 0000 |
MISCR3.bit3(ESD1EN)使能ESD，MISCR3.bit2(ESD1SR)中断标志；

##### ESDCR寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CLKSTOPEN | CLRCNT2 | CLRCNT1 | CLRCNT0 | VOUTSEL1 | VOUTSEL0 | RES1 | RES0 |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
bit7 CLKSTOPEN：1=ESD发生关闭系统时钟；
bit6~4 CLRCNT：滤波计数=(CNT+1)*T800K，000~110对应1~8倍周期；
bit3~2：告警等级配置；bit1~0：内部下拉电阻：00无/01=2K/10=1K/11=500Ω。
注：开启停时钟功能时，WDT喂狗周期需小于溢出时间一半。

## 5.5 RAM区偶校验
IRAM(256B)+XRAM(1536B支持硬件偶校验，可软件开关。
##### RAMCR寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
|----|----|----|----|----|----|----|----|
|IRAMERRSR|XRAMERRSR|-|-|IRAMEN|XRAMEN|U-x|U-x|
bit5 IRAMERRSR：IRAM出错标志(写1清零)；bit4 XRAMERRSR：XRAM出错标志；bit1 IRAMEN：IRAM校验使能；bit0 XRAMEN：XRAM校验使能。
说明：IRAM实际256×9bit、XRAM1536×9bit，多余1bit为校验位。
开启步骤：
1、启动文件初始化全部RAM；
2、使能对应EN位；
3、IRQ0ER开启对应出错中断。

## 5.6 CRC16-CCITT校验
硬件CRC16-CCITT，上电CRC可烧录配置开启，运行CRC软件可控，满足CLASS-B安全认证需求。

## 5.7 编程加密
2字节密钥通过烧录器写入，开启后禁止片内FLASH整片读出。

## 5.8 在线Debug & 串口烧录
四线接口VDD/GND/TXD/RXD；
1、Keil+调试器实现在线调试下载；
2、PC上位机在线烧写；
3、脱机烧录器量产烧写。
**图5-5文字描述：调试/在线烧/脱机烧接线框图，VDD、GND、TXD、RXD四线分别对应调试器、PC烧录器、脱机烧录器与MCU引脚连接。**

## 5.9 用户程序在线升级
上位机CBM_UARTUpdataTool通过UART/IIC/SPI下发固件，当前运行程序接收并烧写升级区。
**图5-6文字描述：上位通讯链路框图，上位软件经通讯总线连接MCU实现固件升级。**
### 5.9.1 开启方法
烧录器配置界面勾选remap选项后烧录芯片开启升级功能。
**图5-7文字描述：烧录器配置界面文字描述，界面包含产品编号、加密密钥、BOOT配置、CRC勾选、remap勾选等配置项。**

### 5.9.2 FLASH分区（总64K）
1、0000H~7DFFH：当前运行程序区（31.5K）；
2、8000H~FDFFH：待升级固件存放区（31.5K）；
3、FE00H~FFFFH：升级控制区（0.5K）；
单份程序最大31.5K，三区均可通过EFC读写擦除。

### 5.9.3 控制地址定义
FE00H：分区CRC总开关，bit0~bit3对应4个8K区块，bit7~4固定0；
FE01H：升级状态，bit7~2=101010代表升级成功；bit1~0奇偶计数，00偶数/01奇数；
FE02~FE09：4组CRC16校验码，{FE03,FE02}=区块1、{FE05,FE04}=区块2、{FE07,FE06}=区块3、{FE09,FE08}=区块4。

### 5.9.4 两种校验方案
1、自定义校验：软件自行校验数据包，校验成功后擦写，写完软件复位；
2、硬件CRC：烧录勾选上电CRC校验出错停机，升级后上位下发CRC码写入FE段，复位自动校验。
**图5-8文字描述：烧录配置界面，含CRC上电校验勾选、BOOT配置等选项。**

### 5.9.5 升级完整步骤
1、烧录勾选remap开启升级；
2、按需勾选上电CRC校验；
3、上位下发升级指令；
4、MCU擦除8000~FDFFH；
5、分包接收固件写入升级区；
6、上位回读校验；
7、改写FE00~FE09控制字；
8、软件复位；
9、开启CRC则复位自动校验，失败停机。

# 6 外部中断
最多6路外部中断XINT0~XINT5，单路支持高/低/上升/下降/双边沿/禁用6种触发；硬件数字滤波，滤波时间IFLTCN配置，默认16Tsys。
**图6-1文字描述：外部中断滤波时序，原始干扰窄脉冲<滤波时长被屏蔽，达标电平经滤波后生成有效中断电平。**

## 6.1 外部中断寄存器
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR复位 |
| --- | --- | --- | --- | --- |
| IFLTCNTR | 0xF0FF | XRAM | 中断滤波配置 | 00001111 |
| XINT01TRGR | 0xF1FF | X0/X1触发 | 00000000 |
| XINT23TRGR | 0xF2FF | X2/X3触发 | 00000000 |
| XINT45TRGR | 0xF3FF | X4/X5触发 | 00000000 |

### IFLTCNTR
bit7~0=IFLTCNT，数值N对应滤波=N×Tsys，00000001=2Tsys，00000010=3Tsys，00000011=4Tsys，依次至1111111=256Tsys。

### XINT01TRGR
bit7保留；bit6~4：XINT1触发配置；bit3保留；bit2~0：XINT0触发配置；
配置码：000高、001低、101上升、110下降、111双边，其余禁用。
XINT23TRGR、XINT45TRGR格式同上，XINT5无高低电平配置，仅边沿/禁用。

## 6.2 电平与边沿中断区别
1、电平触发：有效电平需持续至中断服务查询，外部必须拉无效电平清中断，否则重复进中断；
2、边沿触发：捕获跳变锁存中断标志，电平可恢复，软件清零标志才关闭中断。

## 6.3 配置示例步骤
1、PxyIOCFG=0x38~0x3D映射对应XINT，PnOE=1输入；
示例：P02IOCFG=0x38；P02OE=0x01；
2、IFLTCNTR配置滤波，XINT01TRGR配置边沿；
3、IRQxER开启对应分组中断；
4、IP配置优先级，IE.EXx=1、EA=1；
```c
//外部中断0配置示例
XBYTE[0x51FF] |= (1<<3);    //IOC时钟开
XBYTE[0x1002]=0x38;         //P02映射INT0
XBYTE[0x2AFF] |= 0x04;      //P02输入
XBYTE[0xF0FF]=0x80;         //滤波
XBYTE[0xF1FF] |=0x06;       //上升沿
XBYTE[0xE0FF] |=0x01;       //IRQ0ER
IE |=0x01;
EA=1;
```

# 7 IO端口
全部IO支持功能重映射；P00固定TXD、P01固定RXD；P06~P15固定ADC，不能做触摸；除ADC引脚外均可触摸；P16可配置硬件RST；IO驱动6/30/60mA三档。

## 7.1 IO寄存器总表
|寄存器名|地址|复位|
|----|----|----|
|P0~P4|80H、90H~C0H|0xFF|
|P00IOCFG~P47IOCFG|0x1000~0x1027|对应默认值|
|P0PU~P4PU|0x25~29FF|0xFF|
|P0OE~P4OE|0x2A~2EFF|0xFF|
|P0PURSELRL/H|0x30~39FF|
|P0OD~P4OD|0x3A~3EFF|0x00|
|P0LDRV~P4LDRV|0x40~49FF|0x00|
|P0SMEN~P4SMEN|0x4B~4FFF|

### PxyIOC功能编码
0x00=GPIO；0x01=UART1_TX(P00专属)；0x02=T1IO；0x03=T3IO0；0x04=I2SDA从；0x05=I2CMCL主；0x06=I2CSCL从；0x08=触摸K；0x09=ADC_AN；
0x10~0x24=LED SEG/GRID；0x25=T0O；0x26=T2O；0x27=UART1_TX；0x28=PWM1；0x29=PWM2；0x2A=UART2_TX；
0x30=UART1_RX(P01专属)；0x32=T3I1；0x33=UART1_RX；0x38~0x3D=INT0~INT5；0x40=高阻。
P16IOCFG.bit7=0则P16为硬件R，=1可复用其他功能。

### 上拉配置(PnPURSEL)
00=88K、01=2.2K、10=4.5K、11=10K；P00/P01/P16默认2.2K，其余默认88K。
### 驱动(PnLDRV)
00=6mA、01=30mA、10=60mA、11=6mA。
### PnPU=1开启上拉，PnOD=1开漏，PnOE=1输入/0输出。

## 7.2 GPIO配置步骤
1、MDLCKCR.IOCEN=1；
2、PxyIOCFG=0x00；
3、Pn赋值初始电平；
4、配置驱动档位；
5、配置上拉阻值；
6、PnPU选择上拉开关；
7、PnPD下拉开关；
8、PnOD开漏；
9、PnOE方向。
```c
//P1.0/P1.1初始化示例
XBYTE[0x51FF] |= (1<<3);
XBYTE[0x1008]=0x00;
XBYTE[0x1009]=0x00;
XBYTE[0x90]=0x00;
XBYTE[0x42FF] |=0x02;
XBYTE[0x43FF] |=0x08;
XBYTE[0x32FF] |=0x03;
XBYTE[0x33FF] |=0x0C;
XBYTE[0x26FF] |=0x03;
XBYTE[0x3BFF]=0x00;
XBYTE[0x2BFF] &= ~0x03;
```

# 8 WDT看门狗
WDT时钟固定800K/8=100K；
第一次溢出：中断；第二次溢出：硬件复位；溢出周期160us~21s，复位周期320us~42s；空闲/休眠自动停WDT。
## 8.1 WDT寄存器
|寄存器|地址|复位|
|----|----|----|
|MDLCKCR|0x51FF|bit0=WDTEN|
|WDTCR|0x60FF|喂狗：先写0x5A再0xA5|
|WDTSETR|0x61FF|分频+计数长度|
|WDTSR|0x62FF|溢出标志|
|WDTENR|0x63FF|CNTEN使能|
\[溢出周期=2^{CNTDIV}\times(CNTLEN+1)\times16\times10us\]
\[复位周期=2\times溢出周期\]
CNTDIV(0~11)：1~8192分频；CNTLEN(0~15)：1~16倍基数。

## 8.2 WDT配置流程
1、MDLCKCR |= 0x01；
2、WDTCR=0x5A;WDTCR=0xA5；
3、配置WDTSETR分频与长度；
4、IRQ0ER.WDTINT=1、EX0=1、EA=1；
5、WDTENR |=0x01；
```c
//喂狗函数
void FeedDog(void)
{
    XBYTE[0x60FF]=0x5A;
    XBYTE[0x60FF]=0xA5;
}
```
**图8-1文字描述：WDT时序图，系统复位启动、计数器使能、正确5A+A5清狗、顺序错误无效、单次溢出进中断、二次溢出复位。**

# 9 Timer0 8位定时器
## 9.1 时钟源
高速：SCLK1(48M/外接M晶振/800K)；低速SCLK2(32.768K/M晶振)；内部固定100K(800K/8)。
功能：定时、方波、PWM三种模式。
## 9.2 间隔定时步骤
1、MDLCKCR.TIMEREN=1；
2、T0CKCR配置时钟源、分频、CLKEN=1；
3、T0CR.MODE=0；
\[定时时间=T0CMP0\times T_{clk}\]
4、清INTSR；
5、IRQ2ER、EX2、EA开中断；
6、T0CR.TEN=1。
## 9.3 方波输出
IO配置PxyIOCFG=0x25，其余配置同定时，INVEN=1，\[方波周期=2\times T0CMP0\times T_{clk}\]。
**图9-1文字描述：T0定时方波时序：计数时钟、计数器、匹配值M、TEN、中断标志、输出电平，计数到M电平翻转置中断。**
## 9.4 PWM输出
MODE=1，\[PWM周期=T0CMP0\times T_{clk}\]，占空=T0CMP1/T0CMP0，T0CMP1运行可改。
```c
//T0 PWM初始化
XBYTE[0x51FF] |= (1<<2);
XBYTE[0x70FF]=0x11;
XBYTE[0x71FF]=0x0B;
XBYTE[0x72FF]=100;
XBYTE[0x73FF]=30;
```
##引脚定义JSON
```json
{
  "通用固定引脚": {
    "VCC": "2.7~5.5V电源",
    "GND": "电源地",
    "P00": "固定UART1_TX，调试口，不可重映射为其他硬件TX",
    "P01": "固定UART1_RX，调试口",
    "P06~P15": "固定ADC输入，禁止配置触摸",
    "P16": "默认硬件RST，P16IOCFG.bit7=1改为普通IO"
  },
  "功能重映射引脚组": {
    "T0O": "PxyIOCFG=0x25任意IO",
    "INT0~INT5": "0x38~0x3D配置",
    "UART2_TX/RX": "0x2A/0x33",
    "PWM1/PWM2": "0x28/0x29",
    "触摸K": "0x08(排除P06-P15)",
    "ADC_AN": "0x09仅限P06~P15"
  },
  "各封装配注": {
    "LQFP44": "全部IO资源完整",
    "LQFP32": "精简部分IO",
    "SOP28/SSOP28": "无高速外接晶振引脚",
    "SOP20/SOP16":ADC、触摸通道数量缩减
  }
}
```


# 10 8位定时器Timer1
## 10.1 Timer1模块概述
Timer1模块是一个8位定时器，具有如下特点：
8位定时器
3位预分频器
8位周期与脉宽调制寄存器(T1CMP)
可编程外部时钟边沿选择
Timer1还有高速时钟、低速时钟可供选择：高速时钟(系统副时钟SCLK1)、低速时钟(SCLK2或内部800K)、内部100K由内部800K固定8分频而来。

### 表10-1 Timer1时钟构成
|Timer1时钟分类|可选时钟|时钟来源|
| ---- | ---- | ---- |
|高速时钟SCLK1|IHCLK(48M)、EHCLK(M级晶振)、ILCLK(800K)|系统高速分频输出|
|低速SCLK2|EHCLK(M级晶振)、ELCLK(K级晶振)|外接晶振分频|
|内部低速800K|ILCLK(800K)|片内RC|
|内部100K|800K八分频|片内RC分频|

Timer1时钟框图详情请参考4.1系统总时钟框图描述。
注:T1定时器的时钟源选择与T1CKCR_CLKSEL、MDLCKCR_TLCKSEL、MISCR2_RTCCLK寄存器配置相关。

### 表10-2 T1时钟源选择组合
|T1CKCR_CLKSEL|MDLCKCR_TLCKSEL|MISCR2_RTCCLKSEL|实际选用时钟|
| ---- | ---- | ---- |
|0|任意|任意|SCLK1高速|
|1|0|任意|内部800K|
|1|1|0|内部100K|
|1|1|1|SCLK2|

## 10.1 Timer1支持的功能
Timer1支持四种工作功能：间隔定时、方波输出、PWM输出、外部事件计数。
### 10.1.1 间隔定时操作步骤
配置Timer1为间隔定时功能的操作步骤如下：
1、Timer模块时钟使能
将MDLCKCR寄存器的TIMEREN位置1，使能Timer模块时钟。
2、关闭Timer1
T1CR_TEN=0，定时器停止后再进行参数配置。
3、清中断标志：T1SR=0x00。
4、间隔时间配置
Timer1的间隔定时时间通过T1C寄存器配置，间隔时间计算公式：
\[间隔时间 = T1CMP \times Timer1时钟周期 = T1CMP \times Timer1计数分频 \times 基准时钟周期\]
Timer1可选基准时钟：内部48M、内部800K、外接32.768K/100K、外接M高速晶振。
5、计数时钟参数配置
计数选择：T1CKCR.CLKSEL=0选SCLK1，=1选低速；低速由MDLCKCR_TLCKSEL区分800K/100K/SCLK2。
CLKDIV配置分频系数，定时模式不能配置边沿触发；CLKEN=1打开计数时钟。
6、工作模式：T1CR.MODE=0（定时模式）。
7、中断配置：定时/方波/计数在CNT=T1CMP-1进中断；PWM溢出0xFF进中断；T1CR.INTEN开启模块中断。
8、外设中断：IRQ3ER.bit1置1，对应IRQ3分组。
9、内核中断：EX3=1，配置IP优先级，EA=1。
10、TEN=1启动；注：关闭修改T1CMP后，必须等待≥3倍基准时钟周期再开启TEN。

**图10-1文字描述：Timer1定时方波时序，纵向：计数时钟、计数器、比较值M、TEN、INTSR、OUTINIT=01输出、OUTINIT=10输出；使能TEN计数器从零累加，计数等于M，标志置1、电平翻转；软件清标志，关闭TEN计数器清零。**
\[间隔时间 = T1CMP \times T_{CLK}\]
\[方波周期 = 2\times T1CMP \times T_{CLK}\]

### 10.1.2 方波输出操作步骤
1、IO配置：MDLCK.IOCEN=1，PxyIOCFG=0x02配置T1IO引脚，引脚自动设输出。
2~10步骤和间隔定时完全一致，INVEN=1开启匹配电平翻转。

### 10.1.3 PWM输出操作步骤
1、端口配置：IOC时钟开启，对应IO配置T1复用输出。
2、TIMEREN=1开启模块时钟。
3、TEN=0关闭定时器。
4、清INTSR标志。
5、PWM参数：
\[PWM周期 = (0xFF+1)\times T_{CLK}=256\times T_{CLK}\]
\[PWM占空比 = T1CMP:256\]
OUTSEL配置输出极性，MODE=1进入PWM模式；PWM模式运行可实时改写T1CMP，下个周期生效。
6、时钟配置同定时。
7、INVEN=1、OUTSEL按需配置、MODE=1。
8、PWM溢出(0xFF)产生中断，配置INTEN。
9、IRQ3ER、EX3、EA逐层开中断。
10、延时≥3Tclk后TEN=1启动。
**图10-2文字描述：PWM时序，纵向：计数时钟、计数器、N=T1CMP、TEN、INTS、两路输出；计数到N电平翻转，到0xFF再次翻转并置中断标志。**

### 10.1.4 外部事件计数步骤
1、IO配置为T1输入，IOCEN提前开启。
2、TIMEREN=1。
3、CLKDIV配置上升/下降/双边沿，不再分频；CLKEN=1。
4、MODE=0定时模式，输入有效沿CNT+1，CNT=T1CMP-1触发中断；T1CMP=0等效256，T1CMP=1无中断。
5、中断配置同定时。
6、逐层开启分组、内核中断，TEN启动。
**图10-3文字描述：外部计数时序，输入脉冲有效沿计数器加1，匹配M触发中断，输入信号频率不能超过计数时钟1/2。**

## 10.2 Timer1寄存器定义
|寄存器名|地址|复位值|
| ---- | ---- | ---- |
|T1CKCR|0x78FF|0x00|
|T1CR|0x79FF|0x00|
|T1CMP|0x7AFF|0x00|
|T1SR|0x7BFF|0x02|
|T1CNT|0x7DFF|0x00|

### T1CKCR(0x78FF)
|bit7|bit6|bit5(CLKEN)|bit4(CLKSEL)|bit3~0(CLKDIV)|
| ---- | ---- | ---- | ---- | ---- |
|U0|U0|R/W|R/W|R/W|
CLKD：0000~0111分频；1001上升沿、1010下降沿、1011双边沿，其余1分频。

### T1CR(0x79FF)
|bit7|bit6(INVEN)|bit5~4(OUTINIT)|bit3(INTEN)|bit2(OUTSEL)|bit1(MODE)|bit0(TEN)|
|----|----|----|----|----|----|----|
U0|R/W|R/W|R/W|R/W|R/W|R/W|
READMODE：区分高速/低速时钟读取CNT时序。

### T1CMP：8位比较寄存器
定时模式停机+WREN才可修改；PWM随时改写。

### T1SR
bit1=WREN(可改写标志)，bit0=INTSR中断标志，软件清零。

### T1CNT：实时计数值寄存器，配合READ读取。
```c
//T1读取计数值示例
//M时钟
XBYTE[0x79FF] &= ~(1<<7);
XBYTE[0x79FF] &= ~(1<<0);
unsigned char dat1 = XBYTE[0x7DFF];
//K时钟
XBYTE[0x79FF] |= (1<<7);
XBYTE[0x79FF] &= ~(1<<0);
unsigned char dat2 = XBYTE[0x7DFF];
```

## 10.3 Timer1中断
定时/方波/外部计数：CNT=T1CMP-1中断；PWM：CNT=0xFF溢出中断；标志硬件置位、软件清零；休眠无法唤醒，空闲可唤醒。

# 11 16位定时器Timer2
## 11.1 Timer2概述
Timer2为16位定时器，资源：3位预分频、16位周期T2CMP0(H/L)、16位PWM T2CMP1(H/L)；支持间隔定时、方波、PWM三种功能，时钟选型规则同Timer1。
### 表11-1 Timer2时钟选择表（同T1时钟逻辑）
|T2CKCR_CLKSEL|MDLCKCR_TLCKSEL|MISCR2_RTCCLKSEL|时钟源|
| ---- | ---- | ---- |----|
|0|×|×|SCLK1|
|1|0|×|800K|
|1|1|0|100K|
|1|1|1|SCLK2|

## 11.1.1 间隔定时步骤
1.MDLCKCR.TIMEREN=1；
2.T2CR.TEN=0关闭；
3.T2SR=0清标志；
\[定时周期=(T2CMP0H:T2CMP0L)\times T_{CLK}\]
4.T2CK配置时钟、分频、CLKEN=1；
5.MODE=0定时；
6.匹配T2CMP0产生中断，开启INTEN；
7.IRQ4ER.bit2=1、EX4=1、EA=1；
8.写完CMP高低字节，等待≥3Tclk后TEN=1。

## 11.1.2 方波输出
IO配置T2输出，INVEN=1，\[方波周期=2\times(T2CMP0)\times T_{CLK}\]，其余步骤同定时。

## 11.1.3 PWM配置
\[PWM周期=(T2CMP0H:T2CMP0L)\times T_{CLK}\]
\[占空比=(T2CMP1H:T2CMP1L):(T2CMP0H:T2CMP0L)\]
MODE=1，T2CMP1运行可改，T2CMP0必须停机修改；先写低字节再写高字节。

## 11.2 Timer2寄存器清单
|寄存器|地址|复位|
| ---- | ---- | ---- |
|T2CKCR|0x80FF|0x00|
|T2CR|0x81FF|0x00|
|T2CMP0L|0x82FF|0x00|
|T2CMP0H|0x83FF|0x00|
|T2CMP1L|0x84FF|0x00|
|T2CMP1H|0x85FF|0x00|
|T2SR|0x86FF|0x02|
|T2CNTL|0xAAFF|0x00|
|T2CNTH|0xABFF|0x00|

### T2CKCR：bit5 CLKEN、bit4 CLKSEL，bit2~0分频配置。
### T2CR：bit4 READMODE，bit3 INTEN，bit2 OUTSEL，bit1 MODE，bit0 TEN。
### T2CMP0：周期寄存器，停机WREN才可改写；T2CMP1：PWM占空比，运行实时修改。
### T2SR：bit1 WREN，bit0 INTSR。
```c
//T2修改周期示例
XBYTE[0x81FF] &= ~1;
while((XBYTE[0x86FF] & 2)==0);
XBYTE[0x82FF]=0x60;
XBYTE[0x83FF]=0x02;
XBYTE[0x81FF] |=1;
```

## 11.3 Timer2中断
计数器等于T2CMP0触发中断，标志软件清零；空闲可唤醒，休眠不可唤醒。

# 12 16位定时器Timer3
## 12.1 Timer3模块基础说明
Timer3模块是一个16位定时器，具有如下特点：
16位定时器
3位预分频器
16位周期寄存器(T3CMP0H、T3CMP0L)
16位PWM脉宽调制寄存器(T3CMP1H、T3CMP1L)
16位捕捉功能寄存器(T3CMP0H、T3CMP0L、T3CMP1H、T3CMP1L)
可编程外部时钟边沿选择
支持实时读取计数器值

Timer3有高速时钟、低速时钟可供选择如下：高速时钟(系统副时钟SCLK)、低速时钟(系统副时钟SCLK2、内部低速800K、由内部800K固定8分频来的100K)。

### 表12-1 Timer3时钟构成
|Timer3时钟分类|可选时钟|时钟来源|
| ---- | ---- | ---- |
|高速时钟SCLK1|IHCLK(48M)、EHCLK(M级晶振)、ILCLK(800K)|系统高速分频输出|
|低速时钟SCLK2|EHCLK(M级晶振)、ELCLK(K级晶振)|外接晶振分频|
|内部低速800K|IL(800K)|片内RC振荡|
|内部100K|800K八分频输出|片内RC分频输出|

Timer3时钟框图详情请参考系统总时钟框图描述。
### 表12-2 T3时钟源选择寄存器关系表
|T3CKCR_CLKSEL|MDLCKCR_TLCKSEL|MISCR2_RTCCLKSEL|T3定时器所选时钟源|
| ---- | ---- | ---- |----|
|0|任意|任意|SCLK1高速时钟|
|1|0|任意|内部低速800K|
|1|1|0|内部100K|
|1|1|1|SCLK2低速晶振时钟|

## 12.1 Timer3支持的6项功能
Timer3支持以下6种功能：间隔定时(定时器模式)、方波输出(定时器模式输出)、PWM输出(PWM模式输出)、外部事件计数(定时器模式下对外部输入电平跳变计数)、可编程脉冲输出、外部电平宽度测量。

### 12.1.1 间隔定时的操作步骤
配置Timer3为间隔定时功能的操作步骤如下：
1、Timer模块时钟使能
将MDLCKCR寄存器的TIMEREN位置1，使能Timer模块时钟。
2、关闭Timer3
T3CR_MODE = 0即关闭Timer3，仅停止状态可配置寄存器。
3、清中断标志：`T3SR = 0x00;`
4、间隔时间配置
Timer3的间隔定时时间可以通过(T3CMP0H:T3CMP0L)寄存器配置，具体的间隔时间计算如下：
\[间隔时间 =(T3CMP0H:T3CMP0L) \times Timer3 时钟周期\]
Timer3计数时钟周期由选择的计数时钟决定，共有四种时钟：内部高速OSC48M、内部低速800K、外部32.768K晶振(必须确保外接了32.768K)、外部M级晶振(必须接M级晶振)。
注：必须先配置T3CMP0L，再配置T3CMP0H，新的值将在下一个周期生效。
5、Timer3计数时钟参数配置
计数时钟选择：
1)、T3CKCR寄存器的CLKSEL位可以选择高速时钟或者低速作为Timer3的时钟；
2)、选择低速时钟时，配置MDLCKCR_TLCKSEL区分800K/100K/SCLK2。
计数时钟分频配置：T3CKCR的CLKDIV<2:0>配置分频系数。
输入引脚有效沿设置：EDGESEL[1:0]=00，关闭边沿计数功能。
计数时钟使能：将T3CKCR寄存器的CLKEN位置1，使能Timer3计数时钟。
6、T3CMP0、T3CMP1寄存器模式选择
将T3CR寄存器的CMP0M位清0，CMP1M位置1，配置T3CMP0作为比较寄存器，T3CMP1作为捕获寄存器。
7、Timer3模块中断控制
Timer3拥有两路中断：中断0全部功能均可触发；中断1仅单脉冲、电平捕获模式触发。
T3CR的INT0EN、INT1EN为中断使能；T3SR的INT0SR、INT1SR为中断标志。
8、使能外设模块中断
使能寄存器IRQ0ER的bit1位(IRQ0ER_TCM3INT0 = 0x02)；使能寄存器IRQ1ER的bit1位(IRQ1ER_TCM3INT1 = 0x02)。
9、使能内核中断
1)、EX0 = 1；EX1 = 1；
2)、配置IP1_bit[1:0]、IP1_bit[3:0]设置两路中断优先级(0最低，3最高)；
3)、总中断使能`EA=1`。
10、工作模式选择
置T3CR_MODE[2:0]=110/111开启定时器；
注意：初始化配置完CMP寄存器后，需要等待≥3倍所选时钟周期，再开启TEN。

**图12-1文字描述：Timer3间隔/方波时序，纵向：计数时钟、计数器、M=T3CMP0、MODE配置位、INT0SR标志、OUTINIT=01输出、OUTINIT=10输出；时序逻辑：MODE配置有效后计数器从零递增，计数值等于M时，INT0SR置1、输出电平翻转，软件清中断；MODE清零计数器停止清零。**
\[间隔时间 =(T3CMP0H:T3CMP0L)\times Timer3时钟周期\]
\[方波周期 = 2\times间隔时间\]
注：T3CMP0全0等效65536；T3CMP0=1无方波、无中断。

### 12.1.2 方波输出的操作步骤
1、端口配置
通过PxyIOCFG寄存器将IO配置成定时器3输出功能，配置前MDLCKCR.IOCEN=1开启IO时钟，引脚方向硬件自动设为输出。
2~10步骤与间隔定时完全一致，T3OCR.INV0EN=1开启匹配翻转输出。

### 12.1.3 PWM输出的操作步骤
1、端口配置：PxyIOCFG配置T3输出引脚，IOC时钟提前开启，引脚自动输出。
2、Timer模块时钟使能：MDLCKCR.TIMEREN=1。
3、T3CR全部模式位清零，关闭定时器。
4、清中断标志:`T3SR = 0x00 ;`
5、PWM占空比配置:
Timer3 的PWM 占空比是通过T3CMP0H:T3CMP0L 与T3CMP1H:T3CMP1L 寄存器进行配置,具体计算如下:
PWM 周期 = (T3CMP0H:T3CMP0L) * Timer3 时钟周期 = (T3CMP0H:T3CMP0L) * Timer3 计数时钟分频 * Timer3 计数时钟周期
PWM 占空比 =(T3CMP1H:T3CMP1L)/(T3CMP0H:T3CMP0L) 或者等于((T3CMP0H:T3CMP0L)-( T3CMP1H:T3CMP1L) ) /(T3CMP0H:T3CMP0L))｡(占空比公式取决于配置的初始电平的极性)
Timer3 计数时钟周期由选择的计数时钟决定,共有三种时钟:
- 内部高速OSC48M 的3 分频
- 内部低速800K
- 外部32.768K 晶振(必须确保外接了32.768K 晶振)
- 外部M 级晶振时钟(必须接了外部M 级晶振)

简单地说,(T3CMP0H:T3CMP0L)是配置PWM 输出的周期,(T3CMP1H: T3CMP1L)配置的是输出低电平的时间,所以(T3CMP1H: T3CMP1L)必须小于(T3CMP0H:T3CMP0L)｡
注:
1)、当(T3CMP0H :T3CMP0L)= 0 时,按(T3CMP0H :T3CMP0L)= 65536 计算｡
2)、在PWM 模式下,可以直接修改(T3CMP1H:T3CMP1L)寄存器来改变PWM 的占空比,修改(T3CMP1H:T3CMP1L)时,新的 占空比配置将在下个PWM 周期生效｡
3)、必须先配置T3CMP0L,再配置T3CMP0H,再配置T3CMP1L,最后再配置T3CMP1H｡

6、Timer3 计数时钟参数配置:
计数时钟选择:
1)、T3CKCR 寄存器的CLKSEL 位可以选择高速时钟(系统副时钟SCLK1)或者低速时钟(SCLK2 或内部800K)作为 Timer3 的时钟｡
2)、选择低速时钟时,还必须配置寄存器MDLCKCR 的bit7 位MDLCKCR_TLCKSEL 来选择是内部低速800K 或外部M/K 级晶振作为Timer3 的时钟｡

计数时钟分频配置:
T3CKCR 寄存器的CLKDIV[2:0]位可对选择的时钟源进行分频｡此时不能配置为输入引脚的有效沿作为计数时钟选项｡

输入引脚的有效沿设置:
将T3CKCR 寄存器的EDGESEL[1:0]位配置为00,输入引脚的有效沿选择为禁止设置｡

计数时钟使能:
将T3CKCR 寄存器的CLKEN 位置1,使能Timer3 计数时钟｡

7、Timer3 输出配置:
输出初始电平选择:
T3OCR 寄存器的OUTSEL[1:0]位是Timer3 输出初始电平选择位,对于方波输出､PWM 输出和单脉冲输出都有效,OUTSEL 位决定了输出初始电平的极性｡

禁止单脉冲输出模式(连续脉冲输出模式):
将T3OCR 寄存器的SPOM 位清0,选择连续脉冲输出模式｡

计数器与T3CMP0､T3CMP1 寄存器组匹配输出翻转配置:
将T3OCR 寄存器的INV0EN､INV1EN 位同时置1,使能计数器与(T3CMP1H:T3CMP1L)匹配输出翻转,计数器与(T3CMP0H: T3CMP0L)匹配输出翻转｡

8、T3CMP0､T3CMP1 寄存器组的模式选择:
将T3CR 寄存器的CMP0M､CMP1M 位清0｡配置T3CMP0 寄存器组､T3CMP1 寄存器组都作为比较寄存器｡

9、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

10、使能外设模块中断
使能寄存器IRQOER 的bit1位, \(IRQ0ER_TCM3INT0 =0 ×02\) ,使能Timer3中断 0 ;使能寄存器IRQ1ER的bit1位, \(IRQ1ER_TCM3INT1 =0 ×02\) ,使能Timer3中断1。

11、使能内核中断
1)、EX0 = 1,EX1 = 1;//使能内核中断0､1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

12、工作模式选择
置T3CR_MODE[2:0]=110,即可使能Timer3 开始工作(注意:在初始化定时器或Timer3 关闭情况下,给T3CMP0L､T3CMP0H､ T3CMPIL、T3CMPIH寄存器赋值后必须等待>=3*T所选时钟源的时间后才能 \(T3CR_MODE [2: 0]=110\) 使能T3工作,T所选时钟源指"4.1、 系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟)｡

#### Timer3 PWM输出工作时序文字描述
计数时钟持续输入，计数器从零开始累加计数；设置计数器模式MODE [2:0]位 =110后，计数器启动计数；当计数器值等于N（T3CMP1对应计数值），Timer3输出电平发生翻转；当计数器值等于M（T3CMP0对应计数值），T3SR寄存器INT0SR置1触发Timer3中断0，Timer3输出再次翻转；软件进入中断服务函数清除中断标志；设置计数器模式MODE [2:0]位 =000后，计数器清零并停止计数。
\[PWM 周期 = (T3CMP0) * Timer3 时钟周期= (T3CMP0) * Timer3 计数时钟分频 * Timer3 计数时钟周期\]
\[PWM 占空比 = T3CMP1 / T3CMP0\]
注:T3CMP0 = 0 时,按T3CMP0 = 65536 计算｡

### 二、外部事件计数的操作步骤
配置Timer3 为外部事件计数功能的操作步骤如下:
1、端口配置
通过PxyIOCFG 寄存器将I/O 配置成定时器3 输出/输入0 功能,并且配置PnOE 寄存器,将定时器3 输出或输入0 引脚方 向置为输入｡(端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1)

2、Timer 模块时钟使能
将MDLCKCR 寄存器的TIMEREN 位置1,使能Timer 模块时钟｡

3、关闭Timer3
当Timer3在停止工作状态下才能完成Timer3的以下配置, \(T3CR_MODE =0\) 即关闭Timer3。

4、清中断标志: \(T 3 SR=0 ×00\) :

5、匹配值设置
在外部事件计数模式下,Timer3 计数器在定时器3 输入0 引脚的每个有效沿都会加1,当计数器值等于(T3CMP0H:T3CMP0L -1)时,会产生Timer3中断0。T3CMPOH:T3CMPOL≠1,当 \(T3CMPOH: T3CMPOL =0\) 时,按T3CMPOH:T3CMPOL=65536计算。

6、Timer3 计数时钟参数配置
计数时钟分频配置:
将T3CKCR 寄存器的CLKDIV[2:0]位置为1XX,计数时钟配置为输入引脚的有效沿｡(此时不能配置为计数时钟分 频选项｡)

输入引脚的有效沿设置:
T3CKCR 寄存器的EDGESEL[1:0]位可以选择输入引脚的有效沿,有上升沿､下降沿､双边沿三种,此时不能配置为禁 止设置选项｡

7、禁止单脉冲输出模式(连续脉冲输出模式)
将T3OCR 寄存器的SPOM 位清0,选择连续脉冲输出模式｡

8、T3CMP0､T3CMP1 寄存器组的模式选择
将T3CR 寄存器的CMP0M 位清0,CMP1M 位置1,配置T3CMP0 寄存器组作为比较寄存器,T3CMP1 寄存器组作为捕获寄存器｡

9、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断”｡
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

10、使能外设模块中断
使能寄存器IRQOER的bit1位, \(IRQ0ER_TCM3INT0 =0 ×02\) ,使能Timer3中断0:使能寄存器IRQ1ER的bit1位, \(IRQ1ER_TCM3INT1 =0 ×02\) ,使能Timer3中断1。

11、使能内核中断
1)、 \(EX0 =1\) , \(EX 1=1\) ://使能内核中断0、1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

12、工作模式选择
置 \(T3CR_MODE [2: 0]=110\) ,即可使能Timer开始工作(注意:在初始化定时器或Timer3关闭情况下,给T3CMPOL、T3CMPOH 寄存器赋值后必须等待>=3*T 所选时钟源的时间后才能T3CR_MODE[2:0] = 110 使能T3 工作,T 所选时钟源指"4.1､系统总时钟框图 "中到达T3CKCR_CLKSEL 的时钟)｡

#### Timer3外部事件计数时序文字描述
配置EDGESEL=01选择上升沿作为有效触发沿，输入引脚0输入脉冲信号；MODE配置为110后计数器从0开始逐次加1；T3CMP0为16位比较寄存器，N=T3CMP0-1；当计数器计数值等于N时，T3SR的INT0SR置位，触发Timer3中断0；软件执行中断服务并清除中断标志；MODE配置为000时计数器清零停止计数。
注:
1、当 \(T3CMPO =0\) 时,按T3CMPO= 65536计算,此时 \(M=T 3 C M P 0-1=65535 .\)
2、计数时钟为输入引脚0 的有效沿,只有当输入引脚脉冲宽度大于两个时钟宽度才认为是一个有效边沿,以消除干扰;
3、计数器在每个输入引脚的有效沿都会加1;
4、T3CKCR 寄存器的EDGESEL 位可以选择有效沿｡
5、当 \(T3CMPO =1\) 时,不产生Timer3中断0。

### 三、单脉冲输出的操作步骤
配置Timer3 为单脉冲输出功能的操作步骤如下:
1、端口配置
通过PxyIOCFG 寄存器将I/O 配置成定时器3 输出/输入0 功能,并且配置PnOE 寄存器,将定时器3 输出或输入0 引脚 方向置为输出｡(端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1)

2、Timer 模块时钟使能
将MDLCKCR 寄存器的TIMEREN 位置1,使能Timer 模块时钟｡

3、关闭Timer3
当Timer3在停止工作状态下才能完成Timer3的以下配置, \(T3CR_MODE =0\) 即关闭Timer3。

4、清中断标志: \(T 3 SR=0 ×00\) :

5、Timer3 输出配置
使能单脉冲输出模式:
将T3OCR 寄存器的SPOM 位置1,选择单脉冲输出模式｡

输出初始状态选择:
T3OCR 寄存器的OUTINIT[1:0]位控制Timer3 输出初始状态,只对方波输出､单脉冲输出有效,对PWM 输出无效｡

计数器与T3CMP0､T3CMP1 寄存器组匹配输出翻转配置:
将T3OCR 寄存器的INV0EN､INV1EN 位置1,使能计数器与(T3CMP1H:T3CMP1L)匹配输出翻转,计数器与(T3CMP0H: T3CMP0L)匹配输出翻转｡

6、单脉冲宽度配置
Timer3 的单脉冲宽度是通过T3CMP0H:T3CMP0L 与T3CMP1H:T3CMP1L 寄存器进行配置,具体计算如下:
单脉冲宽度 = (T3CMP0H:T3CMP0L - T3CMP1H:T3CMP1L) * Timer3 时钟周期 = (T3CMP0H:T3CMP0L - T3CMP1H:T3CMP1L) * Timer3 计数时钟分频 * Timer3 计数时钟周期
Timer3 计数时钟周期由选择的计数时钟决定,共有三种时钟:
- 内部高速OSC48M 的3 分频
- 内部低速800K
- 外部32.768K 晶振(必须确保外接了32.768K 晶振)
- 外部M 级晶振时钟(必须接了外部M 级晶振)

注:
1)、当(T3CMP0H :T3CMP0L)= 0 时,按(T3CMP0H :T3CMP0L)= 65536 计算;
2)、其中(T3CMP1H:T3CMP1L)必须小于(T3CMP0H:T3CMP0L),当输出初始电平为低电平,单脉冲宽度是高电平宽 度;当输出初始电平为高电平,单脉冲宽度是低电平宽度｡

7、Timer3 计数时钟参数配置
计数时钟选择:
1)、T3CKCR 寄存器的CLKSEL 位可以选择高速时钟(系统副时钟SCLK1)或者低速时钟(SCLK2 或内部800K)作为 Timer3 的时钟｡
2)、选择低速时钟时,还必须配置寄存器MDLCKCR 的bit7 位MDLCKCR_TLCKSEL 来选择是内部低速800K 或外部M/K 级晶振作为Timer3 的时钟｡

计数时钟分频配置:
T3CKCR 寄存器的CLKDIV[2:0]位可以对选择的时钟源进行分频｡此时不能配置为输入引脚的有效沿作为计数时钟选项。

输入引脚的有效沿设置:
将T3CKCR 寄存器的EDGESEL[1:0]位配置为00,输入引脚的有效沿选择为禁止设置｡

计数时钟使能:
将T3CKCR 寄存器的CLKEN 位置1,使能Timer3 计数时钟｡

8、T3CMP0､T3CMP1 寄存器组的模式选择
将T3CR 寄存器的CMP0M､CMP1M 位同时清0,配置T3CMP0 寄存器组､T3CMP1 寄存器组都作为比较寄存器｡

9、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断”｡
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

10、使能外设模块中断
使能寄存器IRQ0ER 的bit1 位,IRQ0ER_TCM3INT0 = 0x02,使能Timer3 中断0;使能寄存器IRQ1ER 的bit1 位, IRQ1ER_TCM3INT1 = 0x02,使能Timer3 中断1｡

11、使能内核中断
1)、EX0 = 1,EX1 = 1;//使能内核中断0､1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

12、工作模式选择
置T3CR_MODE[2:0]=010,即可使能Timer3 开始工作(注意:在初始化定时器或Timer3 关闭情况下,给T3CMP0L､T3CMP0H､ T3CMP1L､T3CMP1H 寄存器赋值后必须等待>=3*T 所选时钟源的时间后才能T3CR_MODE[2:0] = 010 使能T3 工作,T 所选时钟源指"4.1､ 系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟)｡

13、触发单脉冲输出
每次写T3OCR 寄存器的SPO 位为1,即启动一次单脉冲输出,启动后应查询T3SR 寄存器的INT0SR 位是否置1,判断单脉 冲输出是否完成,完成一次单脉冲输出后才可以启动下一次｡

#### 单脉冲输出时序文字描述
MODE配置为010后计数器启动计数；软件写SPO=1触发单次脉冲输出；计数器从0开始递增计数，N=T3CMP1-1，M=T3CMP0-1；计数值等于N时，INT1SR置1产生Timer3中断1，输出电平翻转；软件清除INT1SR中断标志；计数值等于M时，INT0SR置1产生Timer3中断0，输出电平再次翻转；软件清除INT0SR；MODE写000，计数器清零停止计数。OUTINIT=01和OUTINIT=10分别对应两种初始输出电平。
注:写一次SPO 为1,只能启动1 个脉冲输出｡想发送多个脉冲必须重复写SPO｡

### 四、外部电平宽度测量
外部电平宽度有三种工作方式:
- 使用一组捕获寄存器测量一个输入电平的宽度
- 使用两组捕获寄存器测量两个输入电平的宽度
- 使用两组捕获寄存器测量一个输入电平的宽度

外部电平宽度的工作原理是当外部输入有效沿信号,会产生中断,同时计数器的值捕捉到捕捉寄存器(T3CMP0､T3CMP1 两组寄存器)中;在中断中读取捕捉寄存器的值,连续两次中断读取值的差值可以得出电平宽度｡

#### （1）使用一组捕获寄存器测量一个输入电平的宽度
此模式下,使用的是T3CMP1H:T3CMP1L 这组寄存器作为捕获寄存器,测量定时器3 输入0 的电平宽度｡配置Timer3 为使用一组捕获寄存器测量一个输入电平的宽度的操作步骤如下:
1、端口配置
通过PxyIOCFG 寄存器将I/O 配置成定时器3 输出/输入0 功能,并且配置PnOE 寄存器,将定时器3 输出或输入0 引脚 方向置为输入｡(端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1)

2、Timer 模块时钟使能
将MDLCKCR 寄存器的TIMEREN 位置1,使能Timer 模块时钟｡

3、关闭Timer3
当Timer3在停止工作状态下才能完成Timer3的以下配置, \(T3CR_MODE =0\) 即关闭Timer3。

4、清中断标志: \(T 3 SR=0 ×00\) :

5、Timer3 计数时钟参数配置
计数时钟选择:
1)、T3CKCR 寄存器的CLKSEL 位可以选择高速时钟(系统副时钟SCLK1)或者低速时钟(SCLK2 或内部800K)作为 Timer3 的时钟｡
2)、选择低速时钟时,还必须配置寄存器MDLCKCR 的bit7 位MDLCKCR_TLCKSEL 来选择是内部低速800K 或外部M/K 级晶振作为Timer3 的时钟｡

计数时钟分频配置:
T3CKCR 寄存器的CLKDIV[2:0]位可以对选择的时钟源进行分频｡此时不能配置为输入引脚的有效沿作为计数时钟选项。

输入引脚的有效沿设置:
配置T3CKCR 寄存器的EDGESEL[1:0]位 = 11,选择输入引脚的有效沿为双边沿｡

计数时钟使能:
将T3CKCR 寄存器的CLKEN 位置1,使能Timer3 计数时钟｡

6、T3CMP0､T3CMP1 寄存器组的模式选择
将T3CR 寄存器的CMP0M 位清0,CMP1M 位置1,配置T3CMP0 寄存器组作为比较寄存器,T3CMP1 寄存器组作为捕获寄存器｡

7、清Timer3 中断标志位
使能Timer3 中断前,先将T3SR 寄存器的INT0SR､INT1SR 位清0,清Timer3 中断标志位｡

8、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断”｡
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

9、使能外设模块中断
使能寄存器IRQ0ER 的bit1 位,IRQ0ER_TCM3INT0 = 0x02,使能Timer3 中断0;使能寄存器IRQ1ER 的bit1位,IRQ1ER_TCM3INT1 = 0x02,使能Timer3 中断1｡

10、使能内核中断
1)、 \(EX0 =1\) .EX1=1://使能内核中断0、1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

11、工作模式选择:
1)、置T3CR_MODE[2:0]=010,即可使能Timer3 开始工作(注意:在初始化定时器或Timer3 关闭情况下,给T3CMP0L､T3CMP0H､ T3CMP1L､T3CMP1H 寄存器赋值后必须等待>=3*T 所选时钟源的时间后才能T3CR_MODE[2:0]=010 使能T3 工作,T 所选时钟源指"4.1､ 系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟)｡
2)、配置双边沿为有效沿,如果使能了Timer3 中断1,在Timer3 输入0 引脚外部信号的上升沿､下降沿都会产生Timer3 中断1,连续3 次中断就可以得出一个低电平宽度时间,一个高电平宽度时间｡(具体哪个是高电平时间或低电平时间必须从 实际情况分析)

##### 一组捕获寄存器测脉宽时序文字描述
MODE配置010后计数器从零开始计数，EDGESEL=11配置双边沿触发；输入引脚0电平跳变（上升/下降沿）时，当前计数值锁存入T3CMP1，同时INT1SR置1触发中断1；软件读取T3CMP1数值并清除INT1SR；计数器发生溢出时T3OV置位，软件需在中断中记录溢出次数并清零溢出标志；MODE置000，计数器清零停止计数。
注:
1、输入引脚的有效沿必须设置为双边沿,这样检测到上升沿､下降沿都会产生Timer3 中断1,并锁存计数器的值｡
2、若输入引脚是周期稳定的信号,可以根据相邻3 次中断所捕获到的数据,得到输入引脚的信号的周期/频率｡
3、理论上是无法区分出得到的是高电平宽度还是低电平宽度,必须根据实际上输入信号的特征来区分｡

#### （2）使用两组捕获寄存器测量两个输入电平的宽度
此模式下,使用的是T3CMP0H､T3CMP0L 这组寄存器作为捕获寄存器,测量定时器3 输入1 的电平宽度;T3CMP1H､T3CMP1L 这组寄存器作为捕获寄存器,测量定时器3 输入0 的电平宽度｡配置Timer3 为使用两组捕获寄存器测量两个输入电平的宽度 的操作步骤如下:
1、端口配置
1)、通过PxyIOCFG 寄存器将I/O 配置成定时器3 输出/输入0 功能,并且配置PnOE 寄存器,将定时器3 输出或输入0 引脚方向置为输入｡
2)、通过PxyIOCFG 寄存器将I/O 配置成定时器3 输入1 功能,此时定时器3 输入1 的I/O 方向自动配置为输入｡不需要 配置对应的PnOE 寄存器,选择定时器3 输入1 引脚方向为输入｡
注:端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1｡

2、Timer 模块时钟使能
将MDLCKCR 寄存器的TIMEREN 位置1,使能Timer 模块时钟｡

3、关闭Timer3
当Timer3 在停止工作状态下才能完成Timer3 的以下配置,T3CR_MODE = 0 即关闭Timer3｡

4、清中断标志:T3SR = 0x00 ;

5、Timer3 计数时钟参数配置
计数时钟选择:
1)、T3CKCR 寄存器的CLKSEL 位可以选择高速时钟(系统副时钟SCLK1)或者低速时钟(SCLK2 或内部800K)作为 Timer3 的时钟｡
2)、选择低速时钟时,还必须配置寄存器MDLCKCR 的bit7 位MDLCKCR_TLCKSEL 来选择是内部低速800K 或外部M/K 级晶振作为Timer3 的时钟｡

计数时钟分频配置:
T3CKCR 寄存器的CLKDIV[2:0]位可以对选择的时钟源进行分频｡此时不能配置为输入引脚的有效沿作为计数时钟选项。

输入引脚的有效沿设置:
配置T3CKCR 寄存器的EDGESEL[1:0]位 = 11,选择输入引脚的有效沿为双边沿｡

计数时钟使能:
将T3CKCR 寄存器的CLKEN 位置1,使能Timer3 计数时钟｡

6、Timer3 模式配置
T3CMP0､T3CMP1 寄存器组的模式选择:
将T3CR 寄存器的CMP0M､CMP1M 位都置1,配置T3CMP0､T3CMP1 寄存器组都作为捕获寄存器｡

T3CMP0 寄存器组捕获时刻选择:
将T3CR 寄存器的CMP0CAP 位清0,选择T3CMP0 寄存器组捕获时刻为在输入引脚1 有效沿进行捕获｡

7、清Timer3 中断标志位
使能Timer3 中断前,先将T3SR 寄存器的INT0SR､INT1SR 位清0,清Timer3 中断标志位｡

8、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断”｡
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

9、使能外设模块中断
使能寄存器IRQ0ER 的bit1 位,IRQ0ER_TCM3INT0 = 0x02,使能Timer3 中断0;使能寄存器IRQ1ER 的bit1 位, IRQ1ER_TCM3INT1 = 0x02,使能Timer3 中断1｡

10、使能内核中断
1)、 \(EX0 =1\) ,EX1=1://使能内核中断0、1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

11、工作模式选择
1)、置T3CR_MODE[2:0]=010,即可使能Timer3开始工作(注意:在初始化定时器或Timer3关闭情况下,给T3CMP0L､T3CMP0H､ T3CMP1L､T3CMP1H 寄存器赋值后必须等待>=3*T 所选时钟源的时间后才能T3CR_MODE[2:0]=010 使能T3 工作,T 所选时钟源指"4.1､ 系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟)｡
2)、配置双边沿为有效沿,如果使能了Timer3 中断0､中断1 中断,在Timer3 输入0 引脚外部信号的上升沿､下降沿产 生Timer3 中断1,Timer3 输入1 引脚外部信号的上升沿､下降沿产生Timer3 中断0,连续3 次中断就可以得出一个低电平 宽度时间,一个高电平宽度时间｡(具体哪个是高电平时间或低电平时间必须从实际情况分析)

##### 双寄存器测两路输入时序文字描述
MODE=010开启定时器计数，EDGESEL=11双边沿触发；输入引脚0边沿跳变，计数值锁入T3CMP1，INT1SR置1触发中断1，软件读取数据并清标志；输入引脚1边沿跳变，计数值锁入T3CMP0，INT0SR置1触发中断0，软件读取数据并清标志；计数器溢出T3OV置位，软件清除溢出标记；MODE=000，计数器清零停止。
注:
1、输入引脚的有效沿必须设置为双边沿,这样检测到上升沿､下降沿都会产生中断并锁存计数器的值｡
2、若输入引脚是周期稳定的信号,可以根据相邻3 次中断所捕获到的数据,得到输入引脚的信号的周期/频率｡
3、理论上是无法区分出得到的是高电平宽度还是低电平宽度,必须根据实际上输入信号的特征来区分｡

#### （3）使用两组捕获寄存器测量一个输入电平的宽度
此模式下,使用的是T3CMP0H:T3CMP0L､T3CMP1H:T3CMP1L 这两组寄存器作为捕获寄存器,测量定时器3 输入0 的电平 宽度｡配置Timer3 为使用两组捕获寄存器测量一个输入电平的宽度的操作步骤如下:
1、端口配置
通过PxyIOCFG 寄存器将I/O 配置成定时器3 输出/输入0 功能,并且配置PnOE 寄存器,将定时器3 输出或输入0 引脚 方向置为输入｡(端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1)

2、Timer 模块时钟使能
将MDLCKCR 寄存器的TIMEREN 位置1,使能Timer 模块时钟｡

3、关闭Timer3
当Timer3在停止工作状态下才能完成Timer3的以下配置, \(T3CR_MODE =0\) 即关闭Timer3。

4、清中断标志:T3SR = 0x00 ;

5、Timer3 计数时钟参数配置
计数时钟选择:
1)、T3CKCR 寄存器的CLKSEL 位可以选择高速时钟(系统副时钟SCLK1)或者低速时钟(SCLK2 或内部800K)作为 Timer3 的时钟｡
2)、选择低速时钟时,还必须配置寄存器MDLCKCR 的bit7 位MDLCKCR_TLCKSEL 来选择是内部低速800K 或外部M/K 级晶振作为Timer3 的时钟｡

计数时钟分频配置:
T3CKCR 寄存器的CLKDIV[2:0]位可以对选择的时钟源进行分频｡此时不能配置为输入引脚的有效沿作为计数时钟选项。

输入引脚的有效沿设置:
T3CKCR 寄存器的EDGESEL[1:0]位可以选择输入引脚的有效沿,只能选择输入引脚的有效沿为上升沿或下降沿,不能 配置为其他选项｡

计数时钟使能:
将T3CKCR 寄存器的CLKEN 位置1,使能Timer3 计数时钟｡

6、Timer3 模式配置
T3CMP0､T3CMP1 寄存器组的模式选择:
将T3CR 寄存器的CMP0M､CMP1M 位都置1,配置T3CMP0､T3CMP1 寄存器组都作为捕获寄存器｡

T3CMP0 寄存器组捕获时刻选择:
将T3CR 寄存器的CMP0CAP 位置1,选择T3CMP0 寄存器组捕获时刻为在输入引脚0 有效沿的反沿进行捕获｡

7、清Timer3 中断标志位
使能Timer3 中断前,先将T3SR 寄存器的INT0SR､INT1SR 位清0,清Timer3 中断标志位｡

8、中断控制
Timer3 有两个中断,Timer3 中断0､Timer3 中断1:
1)、Timer3 中断1 只有在单脉冲输出､外部电平宽度测量模式下才可以产生;
2)、Timer3 中断0 在所有模式下都可以产生｡具体请看 “12.3､Timer3 中断
T3CR 寄存器的INT0EN､INT1EN 位是Timer3 中断使能位,T3SR 寄存器的INT0SR､INT1SR 位是Timer3 中断标志位｡

9、使能外设模块中断
使能寄存器IRQ0ER 的bit1 位,IRQ0ER_TCM3INT0 = 0x02,使能Timer3 中断0;使能寄存器IRQ1ER 的bit1位, IRQ1ER_TCM3INT1 = 0x02,使能Timer3 中断1｡

10、使能内核中断
1)、 \(EX0 =1\) ,EX1=1://使能内核中断0、1
2)、配置内核中断0､1 中断优先级IP1_bit[1:0]､IP1_bit[3:2](0:最低优先级;1:最高优先级)
3)、使能总内核中断 \(EA=1\) 。

11、工作模式选择
1)、置T3CR_MODE[2:0]=010,即可使能Timer3开始工作(注意:在初始化定时器或Timer3关闭情况下,给T3CMP0L､T3CMP0H､ T3CMP1L､T3CMP1H 寄存器赋值后必须等待>=3*T 所选时钟源的时间后才能T3CR_MODE[2:0]=010 使能T3 工作,T 所选时钟源指"4.1､ 系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟)｡
2)、如果使能了Timer3 中断0､中断1:
若配置输入引脚有效沿为上升沿,则在Timer3 输入0 引脚外部信号的上升沿,产生Timer3 中断1;在Timer3 输入0 引脚外部信号的下降沿,产生Timer3 中断0;
若配置输入引脚有效沿为下降沿,则在Timer3 输入0 引脚外部信号的下降沿,产生Timer3 中断1;在Timer3 输入 0 引脚外部信号的上升沿,产生Timer3 中断0｡
在Timer3 中断1 记录T3CMP1H､T3CMP1L 的值,在Timer3 中断0 记录T3CMP0H､T3CMP0L 的值,通过连续3 次中断就 可以得出高低电平的时间｡

##### 双寄存器单路测脉宽时序文字描述
EDGESEL=01选择上升沿作为有效触发，CMP0CAP=1配置T3CMP0在反沿捕获；MODE=010定时器启动计数；输入0上升沿触发，计数值存入T3CMP1，INT1SR置1触发中断1，软件保存数值到变量A并清标志；输入0下降沿（反沿）触发，计数值存入T3CMP0，INT0SR置1触发中断0，软件保存数值到变量B并清标志；计数器溢出T3OV置位，软件清除溢出标记；MODE=000定时器停止清零。
注:
1、输入引脚的有效沿可以设置为上升沿/下降沿,这样有效沿的反沿就是下降沿/上升沿｡在有效沿会产生Timer3 中断1,并锁存计数器的值到T3CMP1H:T3CMP1L 寄存器;在反沿产Timer3 中断0,并锁存计数器的值到T3CMP0H:T3CMP0L 寄存器｡
2、若输入引脚是周期稳定的信号,可以根据相邻3 次中断所捕获到的数据,得到输入引脚的信号的周期/频率｡
3、本模式可以分辨出得到的是高电平宽度还是低电平宽度｡

## 12.2、Timer3寄存器的定义
### 表12-3、Timer3寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| T3CR | 0x88FF | XRAM | Timer3模式控制寄存器 | 0000 0000 |
| T3CKCR | 0x89FF | XRAM | Timer3时钟控制寄存器 | 0000 0000 |
| T3OCR | 0x8AFF | XRAM | Timer3输出控制寄存器 | 0000 0000 |
| T3CMP0L | 0x8BFF | XRAM | Timer3比较寄存器0低8位 | 0000 0000 |
| T3CMP0H | 0x8CFF | XRAM | Timer3比较寄存器0高8位 | 0000 0000 |
| T3CMP1L | 0x8DFF | XRAM | Timer3比较寄存器1低8位 | 0000 0000 |
| T3CMP1H | 0x8EFF | XRAM | Timer3比较寄存器1高8位 | 0000 0000 |
| T3SR | 0x8FFF | XRAM | Timer3状态寄存器 | 0000 1000 |
| T3CNTL | 0xADFF | XRAM | Timer3计数寄存器低8位 | 0000 0000 |
| T3CNTH | 0xAEFF | XRAM | Timer3计数寄存器高8位 | 0000 0000 |

以上表中所有寄存器均支持按位操作或直接对字节操作｡

### T3CR:Timer3模式控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CMP1M | CMP0M | INT1EN | INT0EN | CMP0CAP | MODE2 | MODE1 | MODE0 |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 CMP1M:T3CMP1 寄存器组的模式选择位
0 = T3CMP1 寄存器组作为比较寄存器
1 = T3CMP1 寄存器组作为捕获寄存器
bit 6 CMP0M:T3CMP0 寄存器组的模式选择位
0 = T3CMP0 寄存器组作为比较寄存器
1 = T3CMP0 寄存器组作为捕获寄存器
bit 5 INT1EN:Timer3 中断1 使能位
0 = 禁止Timer3 中断1 中断
1 = 使能Timer3 中断1 中断
bit 4 INT0EN:Timer3 中断0 使能位
0 = 禁止Timer3 中断0 中断
1 = 使能Timer3 中断0 中断
bit 3 CMP0CAP:T3CMP0 寄存器组捕获时刻控制位
0 = T3CMP0 寄存器组在输入引脚1 有效沿进行捕获
1 = T3CMP0 寄存器组在输入引脚0 有效沿的反沿进行捕获
bit 2-0 MODE[2:0]:Timer3 模式控制位
00x = Timer3 停止操作,输出无变化,无中断产生
010 = 自由运行模式(计数器溢出清零)
011 = 保留
10x = 由输入引脚的有效沿清除计数器的模式
110 = 由比较匹配清除计数器的模式,当比较匹配时,输出翻转
111 = 由比较匹配清除计数器的模式,无波形输出

### T3CKCR:Timer3时钟控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CLKEN | CLKSEL | EDGESEL1 | EDGESEL0 | READMODE | CLKDIV2 | CLKDIV1 | CLKDIV0 |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 CLKEN:Timer3 计数时钟使能位
0 = 关闭Timer3 计数时钟
1 = 打开Timer3 计数时钟
bit 6 CLKSEL:Timer3 计数时钟选择位
0 = 选择高速时钟(系统副时钟SCLK1)作为Timer3 计数时钟
1 = 选择低速时钟(系统副时钟SCLK2､内部低速800K)作为Timer3 计数时钟(具体是800K 还是外部晶振需要配置MDLCKCR_TLCKSEL 来决定,详情请参考“4.2､MDCLKCR 寄存器”描述)｡
bit 5-4 EDGESEL[1:0]:Timer3 输入引脚的有效沿选择位
00 = 禁止设置
01 = 上升沿
10 = 下降沿
11 = 双边沿
bit 3 READMODE:读timer3 计数器模式选择,如果要读取T3CNT 的值,则必须根据Timer3 的时钟配置此位为正确的 模式值,否则读到的T3CNT 值与真实值之间可能存在误差
0 = Timer3 的时钟是M 级时钟源
1 = Timer3 的时钟是K 级时钟源(32.768K､100K､800K 或外部M 级时钟经过XCLKDIV 分频后的K 级时钟)
bit 2-0 CLKDIV[2:0]:Timer3 计数时钟分频选择位
000 = 2 分频
001=4 分频
010=8 分频
011 = 16 分频
1xx = 输入引脚的有效沿作为计数时钟

### T3OCR:Timer3输出控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | SPO | SPOM | OUTINIT1 | OUTINIT0 | INV1EN | INV0EN |
| U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5 SPO:单脉冲输出触发位
写1 后触发单脉冲输出,自动清零,该位读一直为0｡
bit 4 SPOM:单脉冲输出模式使能位
0 = 连续脉冲输出模式
1 = 单脉冲输出模式
bit 3-2 OUTINIT[1:0]:Timer3 输出电平初始状态(方波输出､PWM 输出､单脉冲输出才有效)
00 = 保持原状态
01 = 初始输出低电平
10 = 初始输出高电平
11 = 保持原状态
bit 1 INV1EN:Timer3 计数器与T3CMP1 匹配输出翻转使能位
0 = 当Timer3 计数器与T3CMP1 匹配时,输出不翻转
1 = 当Timer3 计数器与T3CMP1 匹配时,输出翻转
bit 0 INV0EN:Timer3 计数器与T3CMP0 匹配输出翻转使能位
0 = 当Timer3 计数器与T3CMP0 匹配时,输出不翻转
1 = 当Timer3 计数器与T3CMP0 匹配时,输出翻转

### T3CMP0L:Timer3比较寄存器0低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CMP0L[7:0]:Timer3 比较寄存器0 低8 位
Timer3 比较寄存器0 低8 位,在Timer3 工作期间,可以修改此寄存器

### T3CMP0H:Timer3比较寄存器0高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CMP0H[7:0]:Timer3 比较寄存器0 高8 位
Timer3 比较寄存器0 高8 位,在Timer3 工作期间,可以修改此寄存器
注:
1、T3CMP0H 和T3CMP0L 组成16 位的比较寄存器0(T3CMP0);
2、在比较模式下,T3CMP0 存储的是比较值,由软件写入;且在定时器工作期间,可以修改该寄存器的值;
3、在捕获模式下,T3CMP0 存储的是捕获值,由硬件写入;
4、必须先配置T3CMP0L,再配置T3CMP0H;
5、在初始化T3 或定时器关闭情况下,设置完T3CMP0L､T3CMP0H､T3CMP1L､T3CMP1H 后需要延时等待>=3*T 所选时钟源的时 间才能使能T3 工作,T 所选时钟源指"4.1､系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟｡

```c
//例程12-1:修改T3CMP0H、T3CMP0L寄存器
T3CMP0L = 0x60;    //修改T3CMP0L寄存器
T3CMP0H = 0x02;    //修改T3CMP0H寄存器
```

### T3CMP1L :Timer3比较寄存器1低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CMP1L[7:0]:Timer3 比较寄存器1 低8 位
Timer3 比较寄存器1 低8 位,在Timer3 工作期间,可以修改此寄存器｡

### T3CMP1H:Timer3比较寄存器1高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CMP1H[7:0]:Timer3 比较寄存器1 高8 位
Timer3 比较寄存器1 高8 位,在Timer3 工作期间,可以修改此寄存器｡
注:
1、T3CMP1H 和T3CMP1L 组成16 位的比较寄存器1(T3CMP1);
2、在比较模式下,T3CMP1 存储的是比较值,由软件写入;
3、在捕获模式下,T3CMP1 存储的是捕获值,由硬件写入;
4、软件配置时,必须先配置T3CMP1L,再配置T3CMP1H;
5、在初始化T3 或定时器关闭情况下,设置完T3CMP0L､T3CMP0H､T3CMP1L､T3CMP1H 后需要延时等待>=3*T 所选时钟源的时 间才能使能T3 工作,T 所选时钟源指"4.1､系统总时钟框图"中到达T3CKCR_CLKSEL 的时钟｡
T3CMP0H､T3CMP0L决定输出PWM的周期,T3CMP1H､ T3CMP1L决定输出PWM的占空比,在Timer3工作期间,可以修改T3CMP1H､ T3CMP1L 寄存器改变输出PWM 的占空比｡

### T3SR:Timer3状态寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | - | - | - | T3OV | INT1SR | INT0SR |
| U-x | U-x | U-x | U-x | U-x | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-3 保留未用
bit 2 T3OV:Timer3 计数器溢出状态位
0 = Timer3 计数器没有溢出过
1 = Timer3 计数器已经溢出,需软件写0 清0
bit 1 INT1SR:Timer3 中断1 标志位
0 = 没有产生Timer3 中断1 条件
1 = 产生了Timer3 中断1 条件,需软件写0 清0
bit 0 INT0SR:Timer3 中断0 标志位
0 = 没有产生Timer3 中断0 条件
1 = 产生了Timer3 中断0 条件,需软件写0 清0

### T3CNTL:Timer3计数寄存器低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R-0 | R-0 | R-0 | R-0 | R-0 | R-0 | R-0 | R-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CNTL[7:0]: Timer3 当前计数值低8 位 与T3CNTH 组成16 位计数器

### T3CNTH:Timer3计数寄存器高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R-0 | R-0 | R-0 | R-0 | R-0 | R-0 | R-0 | R-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 T3CNTH[7:0]: Timer3 当前计数值高8 位,与T3CNTL 组成16 位计数器
读寄存器T3CNT 可以获得Timer3 的当前计数值,读T3CNT 前需要配置正确的T3CR 的READMODE 值。

```c
//例程12-2:Timer3的时钟是M级时钟时,读T3CNT计数值
//初始化timer3配置
T3CR_MODE = 0x00;
T3CKCR_READMODE = 0x00;//M级时钟
T3CR_MODE = 0xXX;//配置timer3的工作模式,使能Timer3
……
tempL= T3CNTL;//读Timer3计数值低8位
tempH= T3CNTH;//读Timer3计数值高8位

//例程12-3:Timer3的时钟是K级时钟时,读T3CNT计数值
//初始化timer3配置
T3CR_MODE = 0x00;
T3CKCR_READMODE = 0x08; //K级时钟
T3CR_MODE = 0xXX;//配置timer3的工作模式,使能Timer3
……
tempL= T3CNTL;//读Timer3计数值低8位
tempH= T3CNTH;//读Timer3计数值高8位
```

## 12.3、Timer3中断
Timer3 有两个中断,Timer3 中断0 和Timer3 中断1,产生Timer3 中断有以下几种种方式:
间隔定时/方波输出/PWM 输出/外部事件计数模式下,当Timer3 计数器值等于(T3CMP0H:T3CMP0L - 1)时,产生Timer3 中断0;
单脉冲输出模式下,当Timer3 计数器值等于(T3CMP0H:T3CMP0L - 1)时,产生Timer3 中断0,当Timer3 计数器值等 于(T3CMP1H:T3CMP1L - 1)时,产生Timer3 中断1｡
外部电平宽度测量模式下,产生Timer3 中断0､Timer3 中断1｡具体请查看相应模式的介绍｡
只要有Timer3 中断条件产生,不论是否使能Timer3 中断,T3SR 寄存器的INT0SR､INT1SR 中断标志位都会置1,INT0SR、INT1SR 位必须在软件中清零｡Timer3 的中断使能位是T3CR 寄存器的INT0EN､INT1EN 位｡
注:
1、在休眠模式下,Timer3 中断无法唤醒处理器;
2、在空闲模式下,Timer3 中断可以唤醒 处理器｡

# 13、PWM模块
PWM 模块包括两个独立的模块:
16 位PWM(PWM1) 8 位PWM(PWM2)
PWM 模块的时基 = SYSCLK/(MCLKDIV+1)分频后的时钟MCLK,时钟详情请参考“4.1､系统总时钟框图”描述｡
PWM1 与PWM2 模块的工作原理完全一样,其中PWM1 是16 位,而PWM2 是8 位｡

## PWM1输出原理文字描述
PWM1EN置1开启PWM输出，PWM1HRH:PWM1HRL、PWM1LRH:PWM1LRL寄存器数值可在每个周期T3修改，新占空比下个周期生效；PWM1EN清0停止输出。OUTINIT=0初始输出低电平，OUTINIT=1初始输出高电平。
注:
1、初始电平输出为低电平:
\[低电平宽度 =( PWM1LRH: PWM1LRL + 1) * 2 ^{(PWM1CR_CLKDIV + 1)} *\left(T_{SYSCLK } /(CKDIVCR\_MCLKDIV [5: 0]+1)\right)\]
\[高电平宽度 =( PWM1HRH: PWM1HRL + 1) * 2^{(PWM1CR\_CLKDIV + 1)} *\left(T_{SYSCLK } /(CKDIVCR\_MCLKDIV [5: 0]+1)\right)\]

2、初始电平输出为高电平:
\[高电平宽度 =( PWM1LRH: PWM1LRL + 1) * 2^{(PWM1CR\_CLKDIV + 1)} *\left(T_{SYSCLK } /(CKDIVCR\_MCLKDIV [5: 0]+1)\right)\]
\[低电平宽度 =( PWM1HRH: PWM1HRL + 1) * 2^{(PWM1CR\_CLKDIV + 1)} *\left(T_{SYSCLK } /(CKDIVCR\_MCLKDIV [5: 0]+1)\right)\]

## 13.1、PWM的操作步骤
PWM1､PWM2 的操作步骤完全一样,这里以PWM1 为例,配置PWM1 的操作步骤如下:
1、端口配置
通过PxyIOCFG 寄存器将I/O 配置成PWM1 输出功能时,会自动将引脚方向配置为输出,不再需配置对应PnOE 寄存器｡
注:端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1｡

2、PWM模块时钟使能
将MDLCKCR 寄存器的PWMEN 位置1,使能PWM 模块时钟｡

3、PWM模块参数配置
输出初始状态选择:
配置PWM1CR 寄存器的OUTINIT 位,选择输出初始状态｡

计数时钟分频选择:
配置PWM1CR 寄存器的CLKDIV[2:0]位,选择计数时钟分频｡

调制占空比:
配置PWM1LRH:PWM1LRL 与PWM1HRH:PWM1HRL 寄存器,调整PWM1 输出的占空比｡
1)、当初始电平输出为低电平时,PWM1LRH:PWM1LRL 配置的是输出低电平的时间,PWM1HRH:PWM1HRL 配置的是 输出高电平的时间｡
2)、当初始电平输出为高电平时,PWM1LRH:PWM1LRL 配置的是输出高电平的时间,PWM1HRH:PWM1HRL 配置的是 输出低电平的时间｡

注:
1、配置PWM1 脉宽调制寄存器的顺序必须如下:先配置PWM1LRL､再配置PWM1LRH､再配置PWM1HRL､最后 配置PWM1HRH｡
2、配置PWM2 脉宽调制寄存器的顺序必须如下:先配置PWM2LR､再配置PWM2HR｡

4、使能PWM1:
将PWM1CR 寄存器的PWM1EN 位置1,PWM1 开始工作产生PWM 信号输出｡

## 13.2、PWM寄存器的定义
### 表13-1、PWM寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| PWM1CR | 0x68FF | XRAM | PWM1控制寄存器 | 0000 0000 |
| PWM1LRL | 0x6AFF | XRAM | PWM1脉宽调制寄存器0低8位 | 0000 0000 |
| PWM1LRH | 0x6BFF | XRAM | PWM1脉宽调制寄存器0高8位 | 0000 0000 |
| PWM1HRL | 0x6CFF | XRAM | PWM1脉宽调制寄存器1低8位 | 0000 0000 |
| PWM1HRH | 0x6DFF | XRAM | PWM1脉宽调制寄存器1高8位 | 0000 0000 |
| PWM2CR | 0x69FF | XRAM | PWM2控制寄存器 | 0000 0000 |
| PWM2LR | 0x6EFF | XRAM | PWM2脉宽调制寄存器0 | 0000 0000 |
| PWM2HR | 0x6FFF | XRAM | PWM2脉宽调制寄存器1 | 0000 0000 |

以上表中所有寄存器均支持按位操作或直接对字节操作｡

### PWM1CR:PWM1控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | PWM1EN | OUTINIT | - | CLKDIV2 | CLKDIV1 | CLKDIV0 |
| U-x | U-x | R/W-0 | R/W-0 | U-x | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5 PWM1EN:PWM1 使能位
0 = 禁止PWM1
1 = 使能PWM1,产生PWM 信号输出
bit 4 OUTINIT:PWM1 输出初始状态
0 = 初始输出低电平
1 = 初始输出高电平
bit 3 保留未用
bit 2-0 CLKDIV[2:0]:PWM1 计数时钟分频选择位
000 = 2 分频
001=4 分频
010=8 分频
011=16 分频
100=32 分频
101=64 分频
110=128 分频
111=256 分频

### PWM1LRL:PWM1脉宽调制寄存器0低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM1LRL[7:0]:PWM1 脉宽调制寄存器0 低8 位,与PWM1LRH 组成16 位脉宽调制寄存器0

### PWM1LRH:PWM1脉宽调制寄存器0高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM1LRH[7:0]:PWM1 脉宽调制寄存器0 高8 位,与PWM1LRL 组成16 位脉宽调制寄存器0

### PWM1HRL:PWM1脉宽调制寄存器1低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM1HRL[7:0]:PWM1 脉宽调制寄存器1 低8 位,与PWM1HRH 组成16 位脉宽调制寄存器1


### PWM1HRH:PWM1脉宽调制寄存器1高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM1HRH[7:0]:PWM1 脉宽调制寄存器1 高8 位,与PWM1HRL 组成16 位脉宽调制寄存器1

注:配置PWM1 脉宽调制寄存器的顺序必须如下:先配置PWM1LRL､再配置PWM1LRH､再配置PWM1HRL､最后配置 PWM1HRH｡

### PWM2CR:PWM2控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | PWM2EN | OUTINIT | CLKDIV3 | CLKDIV2 | CLKDIV1 | CLKDIV0 |
| U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5 PWM2EN:PWM2 使能位
0 = 禁止PWM2
1 = 使能PWM2,产生PWM 信号输出
bit 4 OUTINIT:PWM2 输出初始状态
0 = 初始输出低电平
1 = 初始输出高电平
bit 3-0 CLKDIV[3:0]:PWM2 计数时钟分频选择位
0000 = 2 分频
0001 = 4 分频
0010 = 8 分频
0011 = 16 分频
0100 = 32 分频
1100 = 8192 分频
1101 = 16384 分频
1110 = 32768 分频
\[1111=65536 分频\]

### PWM2LR:PWM2脉宽调制寄存器0
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM2LR[7:0]:PWM2 脉宽调制寄存器0

### PWM2HR:PWM2脉宽调制寄存器1
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 PWM2HR[7:0]:PWM2 脉宽调制寄存器1

注:配置PWM2 脉宽调制寄存器的顺序必须如下:先配置PWM2LR､再配置PWM2HR｡

## 14、实时时钟模块(RTC)
RTC 是实时时钟模块,支持以下功能:
- 时钟功能
- 定时(闹钟)功能(以秒为最小单位)

### 14.1 RTC工作原理
RTC 是以秒为最小计时单位,可以实现时､分､秒自动计时与定时闹钟功能:
RTCHDR､RTCMDR､RTCSDR 寄存器组成一组对应24 小时制时间的时､分､秒寄存器,用于RTC 的时钟功能｡设定好当前 时间(配置RTCHDR､RTCMDR､RTCSDR),启动RTC(RTCCR 寄存器的RTCEN 位置1),时钟功能就开始运行,之后软件读RTCHDR、RTCMDR､RTCSDR 寄存器就得到当前的实时时间(连续两次读到的值一样才认为正确),同时可产生对应的时､分､秒中断｡
RTCAHDR､RTCAMDR､RTCASDR 寄存器组成一组设置闹钟的时､分､秒寄存器,用于RTC 的定时闹钟功能｡设定好闹钟时 间(配置RTCAHDR､RTCAMDR､RTCASDR),启动RTC,当RTCHDR､RTCMDR､RTCSDR 的值与RTCAHDR､RTCAMDR､RTCASDR 的值匹 配时,就会产生闹钟中断｡

### 14.2 RTC时钟选择
RTC 有两种时钟源可供选择,通过配置MISCR2 寄存器的RTCCLKSEL 位实现｡
- = 1: 外部32.768K 时钟(必须确保外部接了32.768K 晶振,或者是外接M 级晶振经XCLKDIV 分频得到~32.768K 时钟)
- = 0: 内部固定100K 时钟(由内部800K 固定分频得到100K 时钟,分频不可配)

注:RTC 定时闹钟功能可以在休眠模式下､空闲模式下唤醒MCU 工作

### 14.3 RTC的操作步骤
配置RTC 的操作步骤如下:
1、选择RTC 工作时钟
RTC 可以选择内部或外部的时钟作为工作时钟,如下(时钟框图请参考“4.1 系统总时钟框图”描述):
1)、选择内部100K 为工作时钟,配置MISCR2_RTCCLKSEL = 0;
2)、选择外部32.768K 时钟为工作时钟,配置MISCR2_RTCCLKSEL = 1(必须外接晶振):
- 外部晶振接的是32.768K,则配置XCLKDIVR=0,对外部时钟1 分频后,作为RTC 工作时钟;
- 外部晶振接的是M 级或其它大小晶振,可以配置寄存器XCLKDIVR,对外部时钟进行(XCLKDIVR+1)分频后,得到 ~32.768K 时钟作为RTC 的工作时钟｡

2、配置当前RTC 时间
配置RTCHDR､RTCMDR､RTCSDR 寄存器可以设置当前时间,必须符合24 小时制的格式(即RTCHDR 寄存器的值不能大于23, RTCMDR､RTCSDR 寄存器得值不能大于59),否则会出错｡

3、配置定时闹钟时间:
配置RTCAHDR、RTCAMDR、RTCASDR 寄存器可以设置闹钟时间,必须符合24 小时制的格式(即RTCAHDR 寄存器的值不能大 于23,RTCAMDR､RTCASDR 寄存器得值不能大于59),否则会出错｡

4、清RTC 中断标志位:
在使能中断前,先写 \(RTCSR 寄存器 =0 ×00\) ,清除所有RTC中断标志位。

5、RTC 模块中断控制:
RTCCR 寄存器的INTSEN､INTMEN､INTHEN､INTDEN､INTCEN 位是RTC 中断使能位,RTCSR 寄存器的INTSSR､INTMSR､INTHSR､ INTDSR､INTCSR 位是对应的RTC 中断标志位｡

6、外设模块中断控制
使能寄存器IRQ2ER 的bit2 位,IRQ2ER_RTCINT = 0x04,即使能RTC 外设模块的中断｡

7、内核中断控制
```c
1) EX2 =1 ;//使能内核中断2
2) //配置内核中断2 中断优先级IP1_BIT[5:4](0:最低优先级;3:最高优先级)
3) EA=1 ;//使能总内核中断
```

8、RTC 使能:
将RTCCR 寄存器的RTCEN 位置1,RTC 开始工作｡

注:RTCSR寄存器的SETEN位默认值为1,只有当 \(SETEN =1\) 时,才以配置RTCHDR、RTCMDR、RTCSDR、RTCAHDR、RTCAMDR、 RTCASDR 寄存器中任一的一个,当配置其中一个寄存器时,硬件会先将SETEN 位清0,等待内部逻辑同步完成,再恢复为1｡ 软件在配置其中一个寄存器前,应查询到SETEN 位等于1 后,才可以配置RTCHDR､RTCMDR､RTCSDR､RTCAHDR､RTCAMDR、RTCASD 这几个寄存器,否则可能会出错｡

### 14.4 RTC寄存器的定义
#### 表14-1 RTC寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| RTCCR | 0xE8FF | XRAM | RTC 控制寄存器 | 0000 0000 |
| RTCSR | 0xE9FF | XRAM | RTC 状态寄存器 | 1000 0000 |
| RTCHDR | 0xEAFF | XRAM | RTC 时钟时寄存器 | 0000 0000 |
| RTCMDR | 0xEBFF | XRAM | RTC 时钟分寄存器 | 0000 0000 |
| RTCSDR | 0xECFF | XRAM | RTC 时钟秒寄存器 | 0000 0000 |
| RTCAHDR | 0xEDFF | XRAM | RTC 闹钟时寄存器 | 0001 0111 |
| RTCAMDR | 0xEEFF | XRAM | RTC 闹钟分寄存器 | 0011 1011 |
| RTCASDR | 0xEFFF | XRAM | RTC 闹钟秒寄存器 | 0011 1011 |

以上表中所有寄存器均支持按位操作或直接对字节操作｡

#### RTCCR:RTC控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RTCEN | - | - | INTCEN | INTDEN | INTHEN | INTMEN | INTSEN |
| R/W-0 | U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 RTCEN:RTC 使能位
0 = RTC 停止工作
1 = RTC 开始工作
bit 6-5 保留未用
bit 4 INTCEN:RTC 闹钟中断使能位
0 = 禁止RTC 闹钟中断
1 = 使能RTC 闹钟中断
bit 3 INTDEN:RTC 天中断使能位
0 = 禁止RTC 时钟跳到新的一天中断
1 = 使能RTC 时钟跳到新的一天中断
bit 2 INTHEN:RTC 时中断使能位
0 = 禁止RTC 时钟跳到新的小时中断
1 = 使能RTC 时钟跳到新的小时中断
bit 1 INTMEN:RTC 分中断使能位
0 = 禁止RTC 时钟跳到新的分钟中断
1 = 使能RTC 时钟跳到新的分钟中断
bit 0 INTSEN:RTC 秒中断使能位
0 = 禁止RTC 时钟跳到新的秒钟中断
1 = 使能RTC 时钟跳到新的秒钟中断

#### RTCSR:RTC状态寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| SETEN | - | - | INTCSR | INTDSR | INTHSR | INTMSR | INTSSR |
| R/W-1 | U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 SETEN:RTC 时钟､闹钟寄存器设置状态位
0 = 不允许配置时钟､闹钟寄存器
1 = 允许配置时钟､闹钟寄存器
bit 6-5 保留未用
bit 4 INTCSR:RTC 闹钟中断标志位
0 = 没有产生RTC 闹钟中断条件
1 = 产生了RTC 闹钟中断条件,需软件写0 清0
bit 3 INTDSR:RTC 天中断标志位
0 = RTC 时钟没有跳到新的一天
1 = RTC 时钟跳到了新的一天,需软件写0 清0
bit 2 INTHSR:RTC 时中断标志位
0 = RTC 时钟没有跳到新的一小时
1 = RTC 时钟跳到了新的一小时,需软件写0 清0
bit 1 INTMSR:RTC 分中断标志位
0 = RTC 时钟没有跳到新的一分钟
1 = RTC 时钟跳到了新的一分钟,需软件写0 清0
bit 0 INTSSR:RTC 秒中断标志位
0 = RTC 时钟没有跳到新的一秒
1 = RTC 时钟跳到了新的一秒,需软件写0 清0

注:RTCSR寄存器的SETEN位默认值为1,只有当 \(SETEN =1\) 时,才以配置RTCHDR、RTCMDR、RTCSDR、RTCAHDR、RTCAMDR、 RTCASDR 寄存器中的一个,当配置其中一个寄存器时,硬件会先将SETEN 位清0,等待内部逻辑同步完成,再恢复为1｡软件 在配置其中一个寄存器前,应查询到SETEN 位等于1 后,才可以配置RTCHDR､RTCMDR､RTCSDR､RTCAHDR､RTCAMDR、RTCASD 这几个寄存器,否则可能会出错｡

#### RTCHDR:RTC时钟时寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| - | - | - | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
| U-x | U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-5 保留未用
bit 4-0 RTCHDR[4:0]:RTC 时钟时寄存器
RTC 小时设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改当前时间｡

#### RTCMDR:RTC时钟分寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5-0 RTCMDR[5:0]:RTC 时钟分寄存器
RTC 分钟设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改当前时间｡

#### RTCSDR:RTC时钟秒寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5-0 RTCSDR[5:0]:RTC 时钟秒寄存器
RTC 秒钟设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改当前时间｡

#### RTCAHDR:RTC闹钟时寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | U-x | R/W-1 | R/W-0 | R/W-1 | R/W-1 | R/W-1 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-5 保留未用
bit 4-0 RTCAHDR[4:0]:RTC 闹钟时寄存器
RTC 闹钟小时设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改闹钟时间｡

#### RTCAMDR:RTC闹钟分寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | R/W-1 | R/W-1 | R/W-1 | R/W-0 | R/W-1 | R/W-1 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5-0 RTCAMDR[5:0]:RTC 闹钟分寄存器
RTC 闹钟分钟设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改闹钟时间｡

# RTC剩余、ADC模块、SensorADC触摸模块
## RTCASDR：RTC闹钟秒寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| U-x  | U-x  | R/W-1| R/W-1| R/W-1| R/W-0| R/W-1| R/W-1|

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-6 保留未用
bit 5-0 RTCASDR[5:0]:RTC 闹钟秒寄存器 RTC 闹钟秒钟设置寄存器,不管RTC 是否已启动,都可以配置此寄存器修改闹钟时间｡

## 14.5 RTC中断
RTC 有5 个中断,分别是:
- RTC 秒中断(RTCSDR 每增加1 时)
- RTC 分中断(时间从xx:xx:59 跳到xx:xx:00 时)
- RTC 时中断(时间从xx:59:59 跳到xx:00:00 时)
- RTC 天中断(时间从23:59:59 跳到00:00:00 时)
- RTC 定时闹钟中断(时钟寄存器与闹钟寄存器匹配时)

只要有RTC 中断条件产生,不论是否使能RTC 中断,RTCSR 寄存器的INTSSR､INTMSR､INTHSR､INTDSR､INTCSR 中断标志位都会置1,INTSSR、INTMSR、INTHSR、INTDSR、INTCSR 位必须在软件中清零｡RTC 的中断使能位是RTCCR 寄存器的INTSEN､ INTMEN､INTHEN､INTDEN､INTCEN 位｡

注:
1、在休眠模式下,RTC 闹钟中断可以唤醒处理器｡
2、在空闲模式下,RTC 中断可以唤醒处理器｡

# 15 ADC模块
CBM73xxF 系列MCU 内置一个10 位精度的ADC 硬件模块,支持外部输入8 路ADC 采样通道｡ADC 硬件模块采用逐次逼近法得到转换结果为10 位二进制数,MCU 根据用户所配置的工作模式对转换结果作对应处理,并将最终处理后的值保存在 ADC 结果寄存器(ADCDRL 和ADCDRH)中｡

## ADC特性
1. 10 位数据精度
2. ADC 最大采样时钟为1.4MHZ
3. 转换完成可产生中断
4. 以MCU电压VDD 为转换参考电压
5. 低的采样温漂误差(-40℃~105℃范围内误差 <= ±1%)
6. 可选的工作模式:常规模式或硬件模式(硬件模式以内部vreflv电压为基准, \(1 v 误差 <= \pm 10 mv )\)
7. 硬件滤波功能:用户可设置滤波级数,硬件滤波处理无需用户软件参与,可节省软件处理时间

### ADC工作框图文字描述
ADC工作框图包含：多路通道选择AOCSELRC3:0、LDO模块(输出1/4+VDD)、TRIG触发控制、ADC逐次逼近并行转换逻辑、结果输出至ADCDRN/ADCDRL，VDD作为基准电源接入模块。

## 15.1 ADC工作原理
### 温漂校准
当环境温度变化较大时,ADC 会存在一定的温漂,使ADC 转换结果有误差,因此对ADC 模块进行温漂校准是非常有必要的;建议定时地去对ADC 进行温漂校准(根据温度变化快慢,最好温差变化<+-10℃时进行温漂校准),这样可以确保ADC 的转换误差精度正常而不受温度影响｡

### 启动转换
启动ADC 转换前,必须先配置正确的工作模式(常规模式或硬件模式),然后再将ADCCR 寄存器的TRIG 位置1,开始模数转换｡
注:不能在配置ADCCR 寄存器其它位的同时将ADCCR_TRIG 位置1 启动ADC 转换｡

### 完成转换
启动ADC 转换后,当转换完成时,ADC 模块将:
1)、转换得到的结果保存到ADCDRH:ADCDRL 寄存器中;
2)、ADCSR 寄存器的SAMPEND 位置1;
3)、产生中断(如果使能了转换完成中断)｡

## 15.2 ADC的操作步骤
1、端口配置
```c
P06IOCFG =0x09; // 将 P06 口配置为 ADC 通道
P00E_6 =0x40;  // 将 P06 寄存器配置为输入方向
P0PU_6 =0x00;  // 关闭 P06 口上拉电阻
```
注:
1)、配置IO 端口寄存器前,应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1(MDLCKCR_IOCEN = 0x08);
2)、CBM73xxF 系列MCU 只有P06､P07､P10､P11､P12､P13､P14､P15 这8 个IO 可配置为ADC 通道(对应AN0 - AN7)｡

2、ADC 模块参数配置
使能ADC 模块硬件
```c
ADCCR_SLEEP = 0x00;//0x00:使能ADC 电路; 0x40:关闭ADC 电路
```
ADC 工作模式选择:配置ADCCR 寄存器的MODE 位来选择ADC 的工作模式:常规模式或硬件模式｡
1)、常规模式
```c
ADCCR_MODE =0x00; // 配置 MODE 位选择为常规模式
LDOCR1 |= 0x40;/*在常规模式下,当采样通道ADCCR_CHLNUM=8(VREF)时必须打开LDOCR1.bit6 置1; 当ADCCR_CHLNUM=0~7 或9(AIN0~AIN7 或内部1/4*VDD)时,LDOCR1.bit6 可置0 或1*/
```
2)、硬件模式
```c
ADCCR_MODE = 0x40;//配置MODE 位选择硬件模式;
LDOCR1 |= 0X40;// 硬件模式时必须打开LDOCR1.bit6 置1
```
采样时钟选择:
ADC 的时钟源是系统时钟源SYSCLK,参考“4.1､系统总时钟框图”;通过ADCDIVCR 寄存器可以进行分频,完成 一位转换的时间定义为TAD=采样时钟周期｡一个完整的10 位转化需要13 个TAD 周期。

采样保持时间配置:
ADCCR 寄存器的SAMPCNT[1:0]位控制采集时间｡是ADC 模块采集外部输入电压信号的时间,输入电压信号采集完成 后,才会启动转换｡(SAMPCNT 值必须大于等于 2)

ADC 采样通道及硬件滤波级数的配置:
采样通道控制位ADCCR_CHLSEL:=0x00 表示ADCSELR_CHLNUM 可设置为0~8(AN0~AN7､内部VREF)通道其中之一; =0x04:表示ADCSELR_CHLNUM 只能设置为9(即内部1/4*VDD);
设置采样通道ADCSELR_CHLNUM:= 0x00~0x09(AN0~AN7､内部VREF 1v､内部1/4*VDD);
设置硬件滤波级数ADCSELR_FLITNUM = n: 0<=n<=6,详情见下文寄存器ADCSELR｡
注:当采样通道ADCSELR_CHLNUM=(AN0~AN7､VREF)之一时,采样通道控制位ADCCR_CHLSEL 必须为0;当采样通道 ADCSELR_CHLNUM=内部1/4*VDD 时,采样通道ADCCR_CHLSEL 位必须置1｡

4、清ADC 中断标志位
使能ADC 中断前,先将ADCSR 寄存器的SAMPEND 位清0,清除ADC 采样完成标志位｡

5、中断控制
```c
ADCCR_INTEN = 1<<3;//使能ADC 中断
IRQ4ER_ADCINT = 0x04;//使能ADC 外设模块中断
EX4 = 1;//使能内核中断4
IP1_bit[7:6] = 0;//设置内核中断4 为最低优先级中断
EA = 1;//使能内核总中断
```

6、温漂校准
温漂校准分粗调和微调两部分,具体操作请参考“LDOCR0 & LDOCR1 寄存器”描述｡

7、启动转换
将ADCCR 寄存器的TRIG 位置1 启动转换,注意必需先配置ADCCR 寄存器的其他位,再将ADCCR 寄存器的TRIG 位置1 启动转换,两者不能同时进行｡选择好转换通道后最好延时>5us 再启动转换(即TRIG=1)

8、等待ADC 转换结束
1)、查询ADCSR 寄存器的SAMPEND 位,等于1 即转换结束
2)、等待ADC 中断(使能中断)

9、读取ADC 转换结果
1>、常规模式下,ADCDRH:ADCDRL 寄存器的结果表示的是采样电压转换量化值:
a、当硬件滤波级数 \(n=0\) 时,采样输出值为10位有效数据,则\(Vin=VDD*采样输出值/1024\ \mathrm{v}\)。
b、当硬件滤波级数1<=n<=6 时,采样输出值为16 位有效数据,其中高10 位bit[15:6]为转换量化值的有效值,低6 位bit[5:0]无效,则\(Vin =VDD*(采样输出值>>6)/1024\ \mathrm{v}\)。

2>、硬件模式下,ADCDRH:ADCDRL 寄存器的结果表示的是采样电压绝对值(以内部Vref_1v 为参考基准转换得出):
硬件滤波级数设为 \(0<=n<=6\) 时,ADCDRH:ADCDRL寄存器的结果都固定为13bit,其中高3位bit[12:10]为整数部分,低10 位bit[9:0]为小数部分,则\(Vin =(采样输出值>>10) + (采样输出值\&0x3FF)/1024\ \mathrm{v}\)。

10、清ADC 中断标志位
清ADC 中断标志位(ADCSR 寄存器的SAMPEND 位),必须清除才能再次进行ADC 转换｡

```c
//例15-1: ADC 转换配置例程
P06IOCFG = 0x09;//将P06 口配置为ADC 通道
P0OE_6 = 0x40;//将P06 寄存器配置为输入方向
P0PU_6 = 0x00;//关闭P06 口上拉电阻
ADCCR_SLEEP = 0x00;//使能ADC 硬件电路
ADCCR_MODE = 0x00;//常规模式
LDOCR1 |= 0x40;//常规模式采样内部vref_1v 或硬件模式时,LDOCR1 的bit6 必须要置1;其它情况可以置0 或置1
ADCDIVCR = 0x20;//配置采样时钟
ADCCR_SAMPCNT = 0x02;//采样保持时钟
ADCSELR_FLITNUM = 0x00;//0 级滤波 0<=n<=7
ADCCR_CHLSEL = 0x00;//0x00:采样通道可选择为AN0~AN7､VREF 其中之一; 0x04:采样通道只能选择为内部1/4VDD;
ADCCR_CHLNUM = 0x00;//选择P06(AN0)通道为ADC 采样通道
ADCSR = 0x00;//清ADC 中断标志位
i=20;
while(i--);//选择通道后延时>=5us 再启动
ADCCR_TRIG = 0x80;//启动ADC 转换
while(0x00 == ADCSR_SAMPEND){;} //等待转换完成
ADCSR_SAMPEND = 0x00;//清中断标志位
datl = ADCDRL;//保存采样结果
dath = ADCDRH;
```

### ADC转换时序文字描述
ADCCR.TRIG置1开启转换，首先占用若干TAD周期完成外部电压采样保持，之后占用多段TAD完成逐次串行转并行运算，转换结束后数据存入ADCDRH、ADCDRL，同时ADCSR的SAMPEND硬件置1。

## 15.3 ADC寄存器定义
### 表15-1 ADC寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| ---- | ---- | ---- | ---- | ---- |
| ADCDIVCR | 0xB8FF | XRAM | ADC 时钟分频寄存器 | 0001 1111 |
| ADCSELR | 0xB9FF | XRAM | ADC 转换通道控制寄存器 | 0000 0000 |
| ADCCR | 0xBBFF | XRAM | ADC 控制寄存器 | 0000 0010 |
| ADCSR | 0xBAFF | XRAM | ADC 状态寄存器 | 0000 0000 |
| ADCDRL | 0xBCFF | XRAM | ADC 数据寄存器低8位 | 0000 0000 |
| ADCDRH | 0xBDFF | XRAM | ADC 数据寄存器高8位 | 0000 0000 |
| LDOCR0 | 0x1045 | XRAM | LDO 控制寄存器0 | 000xxxxx |
| LDOCR1 | 0x1046 | XRAM | LDO 控制寄存器1 | 010xxxxx |

以上表中所有寄存器均支持按位操作或直接对字节操作｡且配置寄存器LDOCR0､LDOCR1 之前必须保证IOC 模块的时钟打开｡

#### ADCDIVCR：ADC时钟分频寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-0 | R/W-0 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-0 ADCDIVCR[7:0]:ADC 采样时钟分频寄存器
\[ADC 采样时钟 =f_{SYSCLK} /(2 *(ADCDIVCR+1))\]
当 \(f_{SYSCLK }=48 M\) 时,ADCDIVCR 的值必须 \(=17\)

#### ADCSELR：ADC转换通道控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 保留未用
bit 6-4 FLITNUM[2:0]:硬件滤波级数控制
000 = 1 次采样滤波结果
001 = 2 次采样滤波结果
010 = 4 次采样滤波结果
011 = 8 次采样滤波结果
100 = 16 次采样滤波结果
101 = 32 次采样滤波结果
110 = 64 次采样滤波结果
111 = 保留不可设

bit 3-0 CHLNUM[3:0]:采样通道选择
\[0000=AN0\]
\[0001=AN1\]
\[0010=AN2\]
\[0011 = AN3\]
\[0100=AN4\]
\[0101=AN5\]
\[0110= AN6\]
\[0111=AN7\]
\[1000= VREF (内部 1 v 电压)\]
1001~1111 = 内部1/4*VDD (此通道不支持硬件模式)
注:采样通道选择寄存器ADCSELR_CHLNUM,必须与采样通道控制位寄存器ADCCR_CHLSEL 的配置相匹配｡

#### ADCCR：ADC控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-1 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 TRIG:ADC 触发转换控制位 0 = 不触发 1 = 写1 触发一次ADC 转换
bit 6 MODE:ADC 工作模式
0 = 常规模式(转换结果为VDD 的量化值)
1 = 硬件模式(转换结果为绝对电压值,即以内部1v 电压为基准得到的绝对电压)
bit 5 VREFS:ADC 参考电压选择位(必须选择为0) 0 = 选择内部固定参考电压 1 = 保留不可设
bit 4 SLEEP:ADC 模块休眠使能位 0 = 使能ADC 模块硬件电路 1 = 关闭ADC 模块硬件电路
bit 3 INTEN:转换完成中断 0 = 不使能转换完成中断 1 = 使能转换完成中断
bit 2 CHLSEL:ADC 模块采样通道控制位 0 = 选择AN0~AN7 或VREF 其中之一 1 = 选择1/4*VDD 采样通道
bit 1-0 SAMPCNT[1:0]:ADC 采样保持时间 = (SAMPCNT[1:0] * 2 + 1)*Tadcclk 确保SAMPCNT[1:0]的值>=2
注:ADCCR_CHLSEL 要与ADCCR_CHLNUM 相匹配｡

#### ADCSR：ADC状态寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| U-x | U-x | U-x | U-x | U-x | U-x | U-x | R-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7-1 保留未用
bit 0 SAMPEND:ADC 转换完成标志位 0 = 未完成 1 = 转换完成,需软件写0 清0

#### ADCDRL：ADC数据低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)

#### ADCDRH：ADC数据高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)

#### LDOCR0：LDO控制寄存器0
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-0 | R/W-0 | R/W-x | R/W-x | R/W-x | R/W-x | R/W-x |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 TRIM0OUT:温漂校准粗调输出
bit 6 VDDSEL:MCU 输入电压VDD 选择位 1 = 3.3v；\(0=4.7 v-5.5 v\)
bit 5 ENTRIM0:温漂校准粗调使能位 0 = 不使能温漂校准粗调 1 =使能温漂校准粗调
bit 4-0 TRIM0VAL[4:0]:温漂校准粗调值范围设置(0~31)

温漂校准粗调步骤:
1、将ENTRIM0 位值1,使能粗调功能;
2、修改粗调值TRIM0VAL[4:0](从00000 到11111 递增);
3、延时约10us,再判断粗调输出的结果TRIM0OUT 值是否与上一次不同,如果不同则当前的TRIM0VAL[4:0]即为温漂粗调结 果;如果相同则继续第2 步骤校准｡
注:配置寄存器OSC800KTRIMR､PLLTRIMR､XTALCR0､XTALCR1､XTALCR2､LDOCR0､LDOCR1 前必须打开IOC 模块的时钟｡

#### LDOCR1：LDO控制寄存器1
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| R/W-0 | R/W-1 | R/W-0 | R/W-x | R/W-x | R/W-x | R/W-x | R/W-x |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 TRIM1OUT:温漂校准微调输出
bit 6 ADCBUFEN:adc buf 开关
0 = 关闭adc buf
1 = 打开adc buf
以下情况时必须置1打开：adc 常规模式下采样vref_1v 通道､adc 工作在硬件模式､使能了LVD 低电压监测模块
bit 5 ENTRIM1:温漂校准微调使能位 0 = 不使能温漂校准微调 1 =使能温漂校准微调
bit 4-0 TRIM1VAL[4:0]:温漂校准微调值范围设置(0~31)

温漂校准微调步骤(先进行粗调再微调):
1、将ENTRIM1 位值1,使能微调功能;
2、修改微调值TRIM1VAL[4:0](从00000 到11111 递增);
3、延时约10us,再判断微调输出的结果TRIM1OUT 值是否与上一次不同,如果不同则当前的TRIM1VAL[4:0]即为温漂微调结果;如果相同则继续第2 步骤校准｡
注:配置寄存器OSC800KTRIMR､PLLTRIMR､XTALCR0､XTALCR1､XTALCR2､LDOCR0､LDOCR1 前必须打开IOC 模块的时钟｡

# 16 触摸按键功能(sensorADC)
CBM73xxF 系列芯片内建触摸按键功能模块,最大能连接31 个按键｡内部集成了4 阶可选灵敏度电容,不需要外接电容,可以根据实际电路板材质以及触摸按键介质调节合适的灵敏度｡电容值越小,灵敏度越高;电容值越大,灵敏度越低｡

## sensorADC系统框图文字描述
TK_sel[4:0]为5位通道选择配置位，编码0x00~0x1F依次对应TK0~TK31共31路触摸通道；START为转换启动信号；各路触摸通道接入sensorADC内核，转换完成后24bit结果从DOUT[23:0]输出。

## sensorADC工作原理
### 启动转换
选择软件控制方式,必须先配置好参数,使能sensorADC 模块,然后将SCR0 寄存器的START 位置1,启动sensorADC 转换｡(START 位为只写位,读为0)
注:不能在配置SCR0 寄存器的同时将START 位置1 启 动sensorADC 采样｡

### 完成转换
启动sensorADC 采样后,当采样完成时,sensorADC 模块将:
1)、SSR 寄存器的SAMPEND 位置1
2)、转换得到的结果更新到SDRM:SDRL 寄存器中

## SensorADC软件模式操作步骤
1、端口配置:
将sensorADC 输入引脚的I/O 功能复用配置成sensorADC 通道(PxyIOCFG)时,会自动将引脚方向配置为输入｡不需要配置对应的PnOE 寄存器,选择sensorADC 通道引脚方向为输入,关闭上拉电阻功能 ｡(端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1)
CBM73xxF 系列芯片除P0.6､P0.7､P1.0､P1.1､P1.2､P1.3､P1.4､P1.5 八个引脚外，其余IO均可配置为sensorADC通道，最大支持32路。

2、sensorADC 模块参数配置
配置sensorADC 控制方式:
将SCR0 寄存器的CTRLMODE<1:0>位配置为00,选择软件方式控制sensorADC 的使能及通道切换｡
sensorADC 有三种控制方式:
1) 软件方式:软件方式控制sensorADC 使能以及通道切换;
2) 休眠模式硬件方式:在休眠期间,由硬件控制对选定的通道进行反复采样,直到满足唤醒条件将芯片从休眠模式下唤醒｡
3) 正常工作硬件方式:应用于触摸库,此处不做介绍｡

选择硬件滤波器的工作方式:
将SCR0 寄存器的FLTMODE 位清0,选择仅使用当前sensorADC 的采样值｡另一种硬件滤波器的工作方式是与触摸库相关｡

选择sensorADC 工作方式:
SCR1 寄存器的WORKMODE 位用于配置sensorADC 工作方式,置1 为跟随模式,跟随模式可提高sensorADC 的抗干扰能力｡

配置灵敏度电容:
sensorADC 模块内部集成了灵敏度电容,可通过SCR1 寄存器的CFSEL<1:0>位选择灵敏度电容的值,有4 种可供选择:
00:CI
01:2/3*CI
10:1/2*CI
11:1/3*CI

选择工作电流:配置SCR1 寄存器的CURSEL<3:0>位选择sensorADC 的电流｡

配置转化稳定时间:
启动sensorADC 转换后,硬件上会先将模拟信号转化为数字波形,这段转化时间可以通过SCR2 寄存器的 STABLEN<5:0>位进行配置｡
实际的拍数 = STABLEN<5:0>× 4。
转化时钟就是OSC48M 经过SCR3 寄存器的TRDIV<3:0> 位分频后的时钟｡

选择采样精度:
配置SCR2 寄存器的BITLEN<1:0>位选择sensorADC 的采样精度:
00 = 10 位 (采样时间拍数:31)
01 = 12 位 (采样时间拍数:63)
10 = 14 位 (采样时间拍数:127)
11 = 16 位 (采样时间拍数:256)
采样精度越高,采样时间也越长｡括号内是采样时间的拍数,以OSC48M 经过SCR3 寄存器的TRDIV<3:0> 位分频后时钟的一个clock 作为一拍｡

配置当前通道抵消电容:
由于实际电路板材质以及触摸按键介质存在差异,从而导致外部寄生电容的差异,所以需要根据实际情况配置 不同的内部抵消电容｡通过SSCAPH:SSCAPL 寄存器可以配置9bit 当前通道抵消电容｡若采样通道变化,则当前通道抵消电容也应相应的改变｡

配置采样时钟:
sensorADC 的时钟源是OSC48M,采样时钟是OSC48M 经过SCR3 寄存器的SHDIV<3:0>位分频后的时钟｡

配置转化时钟:
sensorADC 的时钟源是OSC48M,转化时钟是OSC48M 经过SCR3 寄存器的TRDIV<3:0>位分频后的时钟｡

选择sensorADC 采样通道:由SSELR 寄存器决定将哪个通道连接到sensorADC 采样电路｡

3、禁止休眠唤醒相关配置
将SWKCR 寄存器的CLKSEL 位清0,选择内部高速OSC48M 经过SCR3 寄存器分频后的时钟作为sensorADC 时钟｡

4、使能sensorADC:
将SCR1 寄存器的ANALOGEN 位置1,使能sensorADC 模块,使能后sensorADC 才能正常工作｡

5、清软件方式采样完成中断标志位:
使能sensorADC 中断前,先将SSR 寄存器清0,清sensorADC 相关中断标志位｡

6、中断控制:
sensorADC 模块允许在软件方式采样完成后产生一个中断｡sensorADC 采样完成中断使能位是SCR0 寄存器的SAMPEN 位,sensorADC 中断标志位是SSR 寄存器的SAMPEND 位,SAMPEND 位必须软件清0｡
同时还有应用于触摸库的两个中断,中断使能位是SCR0 寄存器的FLTEN､CAPEN 位,中断标志位是SSR 寄存器的 FLTEND､CAPEND 位｡这里不做详细介绍,用户使用时直接禁止这两个中断｡
SSR 寄存器中BLEND 位基线更新完成标志位,不产生中断,应用于触摸库｡

7、启动转换:
将SCR0 寄存器的START 位置1 启动转换｡注意必需先配置SCR0 寄存器的其他位,再将SCR0 寄存器的START 位置1 启动转换,两者不能同时进行｡

8、等待sensorADC 转换结束:
1)、查询SSR 寄存器的SAMPEND 位,等于1 即转换结束
2)、等待sensorADC 软件方式采样完成中断(使能中断)

9、读取sensorADC 采样结果:
sensorADC 转换结束后,可以通过读取SDRH:SDRL 寄存器得到sensorADC 采样结果｡

10、清软件方式采样完成中断标志位:
将SSR 寄存器的SAMPEND 位清0,清sensorADC 软件方式采样完成中断标志位｡以便再次进行sensorADC 采样｡

```c
//例16-1: sensorADC 转换
P00IOCFG = 0x08; //配置P0.0(TK0)为sensor 通道
SSR = 0x00; //清sensorADC 相关中断标志位
SCR0= 0x00; //配置软件方式控制､禁止中断
SCR1 = 0xC0; //使能sensorADC､配置部分参数
SCR2 = 0x43; //配置转化稳定时间,选择精度
SCR3 = 0x82; //配置采样时钟､转化时钟
SSCAPL = 0x00; //配置抵消电容
SSCAPH = 0x01;
SWKCR = 0x03; //选择OSC48M 分频后的时钟
SSELR = 0x00; //选择P0.0(TK0)作为sensorADC 输入
SCR0_START = 0x40; //启动ADC 转换
while(0x00 == SSR_SAMPEND){;} //等待转换完成
SSR_SAMPEND = 0x00; //清中断标志位
datl = SDRL; //保存采样结果
dath = SDRH;
```

## sensorADC寄存器定义
### 表16-1 SensorADC寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| ---- | ---- | ---- | ---- | ---- |
| SCR0 | 90FFH | XRAM | sensorADC 控制寄存器0 | 0000 0000 |
| SCR1 | 91FFH | XRAM | sensorADC 控制寄存器1 | 0011 0100 |
| SCR2 | 92FFH | XRAM | sensorADC 控制寄存器2 | 0100 0010 |
| SCR3 | 93FFH | XRAM | sensorADC 控制寄存器3 | 1000 0010 |
| SSCAPL | 94FFH | XRAM | sensorADC 抵消电容配置寄存器低8位 | 0000 0000 |
| SSCAPH | 95FFH | XRAM | sensorADC 抵消电容配置寄存器高1位 | 0000 0000 |
| SSR | 96FFH | XRAM | sensorADC 中断状态寄存器 | 0000 0000 |
| SDRL | 97FFH | XRAM | sensorADC 结果寄存器低8位 | 0000 0000 |
| SDRH | 98FFH | XRAM | sensorADC 结果寄存器高8位 | 0000 0000 |
| SSELR | 9AFFH | XRAM | sensorADC 通道选择寄存器 | 0000 0000 |
| SWKCR | 9EFFH | XRAM | SensorADC SLEEP 唤醒配置寄存器 | 0000 0011 |

以上表中所有寄存器均支持按位操作或直接对字节操作｡

#### SCR0: sensorADC 控制寄存器0
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| U-x | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
bit 7 保留未用
bit 6 START:sensorADC 转换启动位
bit5~4 CTRLMODE[1:0]：工作模式选择
bit3 FLTMODE：滤波模式选择
bit2 BLEN：基线使能
bit1 FLTEN：滤波中断使能
bit0 SAMPEN：采样完成中断使能

# 15.2、ADC 的操作步骤
配置ADC 的操作步骤如下:
## 1、端口配置
```c
P06I0CFG =0x09; // 将 P06 口配置为 ADC 通道
P00E_6 =0x40; // 将 P06 寄存器配置为输入方向
POPU_6 =0x00; // 关闭 P06 口上拉电阻
```
> 注:
> 1)、配置IO 端口寄存器前,应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1(MDLCKCR_IOCEN = 0x08);
> 2)、CBM73xxF 系列MCU 只有P06、P07、P10、P11、P12、P13、P14、P15 这8 个IO 可配置为ADC 通道(对应AN0 - AN7)。

```json
{
    "可用ADC引脚": ["P06","P07","P10","P11","P12","P13","P14","P15"],
    "引脚对应通道": {
        "P06": "AN0",
        "P07": "AN1",
        "P10": "AN2",
        "P11": "AN3",
        "P12": "AN4",
        "P13": "AN5",
        "P14": "AN6",
        "P15": "AN7"
    }
}
```

## 2、ADC 模块参数配置
### 使能ADC 模块硬件电路
```c
ADCCR_SLEEP = 0x00;//0x00:使能ADC 电路; 0x40:关闭ADC 电路
```

### ADC 工作模式选择
配置ADCCR 寄存器的MODE 位来选择ADC 的工作模式:常规模式或硬件模式。
#### 1)、常规模式
```c
ADCCR_MODE =0x00 ; // 配置 MODE 位选择为常规模式
LDOCR1 |= 0x40;/*在常规模式下,当采样通道ADCCR_CHLNUM=8(VREF)时必须打开LDOCR1.bit6 置1; 当ADCCR_CHLNUM=0~7 或9(AIN0~AIN7 或内部1/4*VDD)时,LDOCR1.bit6 可置0 或1*/
```
#### 2)、硬件模式
```c
ADCCR_MODE = 0x40;//配置MODE 位选择硬件模式;
LDOCR1 |= 0X40;// 硬件模式时必须打开LDOCR1.bit6 置1
```

### 采样时钟选择
ADC 的时钟源是系统时钟源SYSCLK,参考“4.1、系统总时钟框图”;通过ADCDIVCR 寄存器可以进行分频,完成 一位转换的时间定义为TAD=采样时钟周期。一个完整的10 位转化需要13 个TAD 周期。

### 采样保持时间配置
ADCCR 寄存器的SAMPCNT[1:0]位控制采集时间。是ADC 模块采集外部输入电压信号的时间,输入电压信号采集完成 后,才会启动转换。(SAMPCNT 值必须大于等于 2)

### ADC 采样通道选择及硬件滤波级数的配置
采样通道控制位ADCCR_CHLSEL:=0x00 表示ADCSELR_CHLNUM 可设置为0~8(AN0~AN7、内部VREF)通道其中之一; =0x04:表示ADCSELR_CHLNUM 只能设置为9(即内部1/4*VDD 通道);
设置采样通道ADCSELR_CHLNUM:= 0x00~0x09(AN0~AN7、内部VREF 1v、内部1/4*VDD);
设置硬件滤波级数ADCSELR_FLITNUM = n: 0<=n<=6,详情见下文寄存器ADCSELR。

> 注:当采样通道ADCSELR_CHLNUM=(AN0~AN7、VREF)之一时,采样通道控制位ADCCR_CHLSEL 必须为0;当采样通道 ADCSELR_CHLNUM=内部1/4*VDD 时,采样通道控制位ADCCR_CHLSEL 位必须置1。

## 3、清ADC 中断标志位
使能ADC 中断前,先将ADCSR 寄存器的SAMPEND 位清0,清除ADC 采样完成标志位。

## 4、中断控制
ADC 模块允许在完成模数转换后产生一个中断,ADC 中断使能位是ADCCR 寄存器的INTEN 位;同时还必须配置相关内核中 断控制。
```c
ADCCR_INTEN = 1<<3;//使能ADC 中断 
IRQ4ER_ADCINT = 0x04;//使能ADC 外设模块中断 
EX4 = 1;//使能内核中断4 
IP1_bit[7:6] = 0;//设置内核中断4 为最低优先级中断 
EA = 1;//使能内核总中断
```


### SPI中断寄存器说明
SPIIER 寄存器是SPI 中断使能寄存器，有SPIIER[4:0]5 个位与SPISR[4:0]一一对应，分别是发送FIFO 空中断、发送FIFO 满中断、接收FIFO 非空中断、接收FIFO 半满中断、接收FIFO 满中断。SPISR[4:0]就是相应的中断标志位。不论中断是否使能，只要产生中断条件，相应的标志位就会置1。

SPISR 寄存器还有RXFOV(接收FIFO 溢出标志位)、 FWRSR(发送FIFO 写状态位)、TRANS(SPI Master 数据传送启动位)。这些都只是标志位，不会产生中断。详细请查看寄存器介绍。

### 5、外设模块中断控制
使能寄存器IRQ5ER的bit2位，$IRQ5SR\_SPIINT =0 ×04$，即使能SPI外设模块的中断。

### 6、内核中断控制
```c
EX5 = 1;//使能内核中断5
//配置内核中断5 中断优先级IP2_BIT[3:2](0:低优先级;1:高优先级)
EA=1; //使能总内核中断
```

### 7、接收发送使能
当参数都配置好以后，可以将SPICR 寄存器的RXEN 位置1 使能数据接收，TXEN 置1 使能数据发送。数据的发送和接收功能是相互独立的，不会受另一方的影响。

### SPI通讯时序文字描述
#### 图18-1、SPI 通讯时序图(CPHA = 0)
时序条件：CPHA=0
1. CPOL=0时SCK空闲为低电平，CPOL=1时SCK空闲为高电平；
2. DORD=0（低位先行）：MOSI从LSB依次输出1~6位至MSB；MISO从LSB依次采集1~6位至MSB；
3. DORD=1（高位先行）：MOSI从MSB依次输出6~1位至LSB；MISO从MSB依次采集6~1位至LSB；
4. CS为片选控制线，红线标注位置为数据采样时刻。

#### 图18-2、SPI通讯时序图(CPHA = 1)
时序条件：CPHA=1，时钟编号1~8
1. CPOL=0时SCK空闲低电平，CPOL=1时SCK空闲高电平；
2. DORD=0（低位先行）：MOSI从LSB依次输出2~6位至MSB；MISO从LSB依次采集1~6位至MSB；
3. DORD=1（高位先行）：MOSI从MSB依次输出6~1位至LSB；MISO从MSB依次采集6~1位至LSB；
4. CS为片选控制线，红线标注位置为数据采样时刻。

### 18.2、主控模式
主器件控制时钟线，因此可以随时启动数据传输。主器件根据软件协议确定从器件何时传输数据。
#### 1)、数据发送:
在主控模式下，软件通过写SPIDR 寄存器，将数据写入到发送FIFO 中。最多可写4 字节数据，FIFO 已满将不能再写入数据。软件应先查询发送FIFO 满标志位为0，再写SPIDR。
数据写入发送FIFO 后，将SPISR 寄存器的TRANS 位置1 即启动1Byte 的数据传送，软件查询TRANS 位为0 时，即表示 Byte 传输结束。必须等待TRANS 位为0 后，才能再次将TRANS 位置1 启动下次Byte 传输。每完成1Byte 数据传送，发送FIFO 的数据都会减少1Byte，SPISR 寄存器的相应标志位也会做出改变。

#### 2)、数据接收:
在主控模式下，接收的数据先存入到接收FIFO 中，深度也为4Byte，软件读SPIDR 寄存器，可将接收FIFO 中的数据取出。
每读一次SPIDR 寄存器，接收FIFO 中缓存的数据都会减少1Byte，SPISR 寄存器的相应标志位也会做出改变。软件上先查询接收FIFO 非空标志位为1，再读取SPIDR。如果接收FIFO 已满，SPI 仍继续接收数据，则会溢出。

#### 3)、时钟速度:
理论上SPI 的时钟速度可达到1.14Mhz，但是需要通过相关寄存器来配置。

### 18.3、从动模式
在从动模式下，当时钟引脚上出现外部时钟脉冲时，即启动数据发送和接收。当最后一位数据被锁存后，SPISR 寄存器的RXFNOTEMPTY、RXFHALF、RXFFULL 位会产生相应变化。
#### 1)、数据发送和接收:
在从动模式下，发送接收数据与主控模式相同，区别是，从动模式不能控制何时通讯，只能被动的响应主器件的数据传送。
#### 2)、片选信号:
从动模式下，必须接收到片选信号，才会响应数据传送。就是说只有片选引脚为低电平时，才能发送和接收数据。
> 注:软件仅在SPISR 寄存器的FWRSR 位为0 时，才可以写SPIDR 寄存器将待发送数据写入发送FIFO 中。

### 18.4、SPI 寄存器的定义
表18-1、SPI 寄存器列表

|寄存器名称|寄存器地址|地址类型|说明|POR 复位值|
| ---- | ---- | ---- | ---- | ---- |
|SPICR|0xE0FF|XRAM|SPI 控制寄存器|0000 0000|
|SPISFMR|0xE1FF|XRAM|SPI 软件控制片选信号寄存器|0000 0001|
|SPIIER|0xE2FF|XRAM|SPI 中断使能寄存器|0000 0000|
|SPIBPR|0xE3FF|XRAM|SPI 波特率配置寄存器|0010 0000|
|SPISR|0xE4FF|XRAM|SPI 状态寄存器|0000 0001|
|SPIDR|0xE5FF|XRAM|SPI 数据接收发送寄存器|0000 0000|

以上表中所有寄存器均支持按位操作或直接对字节操作。

#### SPICR:SPI 控制寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|MSSEL|CSMODE|DORD|FIFOCLR|CPHA|CPOL|TXEN|RXEN|
|R/W-0|R/W-0|R/W-0|W-0|R/W-0|R/W-0|R/W-0|R/W-0|

> 注:
> R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7 MSSEL:主从模式控制位
> 0 = 从模式，1 = 主模式；必须先选择好模式才能再配置其它寄存器。
> bit 6 CSMODE:片选信号产生方式选择位
> 0 = 硬件方式产生，通讯过程中，每个字节的通讯，CS 都会自动变低再变高；
> 1 = 软件方式产生，此后，软件写SPISFMR 为1即拉高CS，写0即拉低CS；软件方式可以实现 CS 保持为低时，完成多个字节通讯。
> bit 5 DORD:发送接收位顺序控制位
> 0 = LSB 方式，1 = MSB 方式。
> bit 4 FIFOCLR:清FIFO 位，只写位，软件写1 清空FIFO。
> bit 3 CPHA:时钟相位选择位，0 = 在时钟的第一个边沿采样数据，1 = 在时钟的第二个边沿采样数据。
> bit 2 CPOL:时钟极性选择位，0 = 时钟的空闲电平为低电平，1 = 时钟的空闲电平为高电平。
> bit 1 TXEN:SPI 发送数据使能位，0 = 禁止发送，1 = 使能发送。
> bit 0 RXEN:SPI 接收数据使能位，0 = 禁止接收，1 = 使能接收。

#### SPISFMR:SPI 软件控制片选信号寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|-|-|-|-|-|-|-|CS|
|U-x|U-x|U-x|U-x|U-x|U-x|U-x|R/W-1|

> 注:
> bit 7-1：保留未用
> bit 0 CS:SPI 软件控制片选信号，0 = 拉低片选信号，1 = 拉高片选信号。

#### SPIIER:SPI 中断使能寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|-|-|RXFOV|RXFFULL|RXFHALF|RXFNOTEMPTY|TXFFULL|TXFEMPTY|
|U-x|U-x|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|

> 注:
> bit 7-6：保留未用
> bit 5 RXFOV:接收FIFO 溢出中断使能位，0 = 禁止接收FIFO溢出中断，1 = 使能FIFO溢出中断。
> bit 4 RXFFULL:接收FIFO 满中断使能位，0 = 禁止接收FIFO 满中断，1 = 使能接收FIFO 满中断。
> bit 3 RXFHALF:接收FIFO 半满中断使能位，0 = 禁止接收FIFO 半满中断，1 = 使能接收FIFO 半满中断。
> bit 2 RXFNOTEMPTY:接收FIFO 非空中断使能位，0 = 禁止接收FIFO 非空中断，1 = 使能接收FIFO 非空中断。
> bit 1 TXFFULL:发送FIFO 满中断使能位，0 = 禁止发送FIFO 满中断，1 = 使能发送FIFO 满中断。
> bit 0 TXFEMPTY:发送FIFO 空中断使能位，0 = 禁止发送FIFO 空中断，1 = 使能发送FIFO 空中断。

#### SPIBPR:SPI 波特率配置寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|-|SPIBPR6|SPIBPR5|SPIBPR4|SPIBPR3|SPIBPR2|SPIBPR1|SPIBPR0|
|U-x|R/W-0|R/W-1|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|

> 注:
> bit 7：保留未用
> bit 6-0：SPIBPR[6:0]:SPI 波特率配置寄存器，SPIBPR 必须大于等于0x06(SCLK1=16M)，spi 的时钟速度必须<=1.14Mhz。当SPI 作为主控模式时，SPIBPR 控制SPI 通讯的波特率。
\[SPI 每个 bit 的时间 =2 *( SPIBPR +1) * Tspi\]
(Tspi 是等于系统副时钟SCLK1)

#### SPIDR:SPI 数据接收发送寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|

> 注:bit7~0为SPIDR<7:0>，接口发送数据时，软件通过写此寄存器，将数据写入到发送FIFO 中；接口接收数据时，数据首先存入到接收FIFO 中，软件读此寄存器，将接收FIFO 中的数据取出。

#### SPISR:SPI 状态寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|TRANS|FWRSR|RXFOV|RXFFULL|RXFHALF|RXFNOTEMPTY|TXFFULL|TXFEMPTY|
|R/W-0|R-0|R/W-0|R-0|R-0|R-0|R-0|R-1|

> 注:
> bit 7 TRANS:主控模式下，Byte 数据传送启动位，0 = 数据传送已完成或不在进行中，1 = 启动数据传送，完成后自动清0。
> bit 6 FWRSR:SPI 发送FIFO 写状态位，0 = 可写SPIDR 寄存器将数据写入发送FIFO，1 = 不可写SPIDR 寄存器将数据写入发送FIFO。
> bit 5 RXFOV:接收FIFO 溢出标志位，0 = 接收FIFO 未溢出，1 = 接收FIFO 已经溢出，需软件写0 清0。
> bit 4 RXFFULL:接收FIFO 满标志位，0 = 接收FIFO 不满，1 = 接收FIFO 已满。
> bit 3 RXFHALF:接收FIFO 半满标志位，0 = 接收FIFO 没有半满，1 = 接收FIFO 半满。
> bit 2 RXFNOTEMPTY:接收FIFO 非空标志位，0 = 接收FIFO 空，1 = 接收FIFO 非空。
> bit 1 TXFFULL:发送FIFO 满标志位，0 = 发送FIFO 不满，1 = 发送FIFO 已满。
> bit 0 TXFEMPTY:发送FIFO 空标志位，0 = 发送FIFO 非空，1 = 发送FIFO 已空。
> 补充备注：
> 1、发送或接收每个Byte 都需要写TRANS 位。软件查询TRANS 位为0 时，即表示Byte 传输结束。
> 2、软件仅在FWRSR 位为0 时，才可以写SPIDR 寄存器将待发送数据写入发送FIFO 中。
> 3、当RXFFULL 位为1，SPI 继续接收到新的Byte 数据，则会溢出，RXFOV 位会置1。所以软件应及时将接收FIFO 中的数据读出。

## 19、I2C 模块
I2C 模块包含主控和从动两个模块，这两个模块是相互独立的，可以同时工作。
I2C 模块支持7 位寻址功能即I2CSADDR_bit[7:0]；两根传输通讯信号线，分别是时钟线和数据线。当IO 复用功能寄存器配置为I2C 功能(I2C Slave 数据线、I2C Slave 时钟线；I2C Master 数据线、I2C Master 时钟线)后，会自动将引脚方向置为相应的方向，不需要再配置对应的PnOE 寄存器。

### 19.1、I2C Slave(从动模块)
I2C Slave 有4 种工作方式:
- I2C Slave 软件快速模式(读写)《常用模式》
- I2C Slave 软件标准模式(读写)
- I2C Slave 硬件快速模式(只读)
- I2C Slave 硬件标准模式(只读)

这4 种模式下的通讯时序要求有所不同。在软件快速、软件标准模式下，主机可以对从机进行读写。而在硬件快速、硬件标准模式下，主机只能从从机读数据而不能对从机写数据。具体的通讯时序见对应小节。

> 注:
> 1、以上所说的标准模式和快速模式是指I2C 的工作方式(请参下考图)，并非指I2C 的传输速率级别；
> 2、标准模式:指必须严格按照读写EEPROM 器件的I2C 接口设备协议来操作，此协议中必须有从机设备地址、从机硬件地址(即从机的内存地址)；
> 3、快速模式:指通讯协议中不需要从机设备硬件地址，只需要从机设备地址匹配即可进行正常数据收发通讯。

#### I2C Slave 的操作步骤
##### 1、端口配置
1)、配置对应IO 复用功能寄存器，将IO 口重映射为I2C Slave 数据线、I2C Slave 时钟线功能(无需再配置PnOE)。
2)、配置对应PnPURSELRH:PnPURSELRL 寄存器可以选择对应时钟、数据线的内部上拉电阻，建议配置为10K 上拉。
3)、配置对应PnPU 寄存器，使能对应时钟、数据线的内部上拉电阻。
> 注:端口配置前应使能IOC 模块时钟，即将MDLCKCR 寄存器的IOCEN 位置1。

##### 2、I2C Slave 模块参数配置
1)、从机设备地址配置
主机发送开始信号后，紧接发7 位地址+1 位读/写位，主机发的这7 位地址数据必须与从机I2CSADDR 寄存器的bit[7:1] 位匹配，从机才会做出应答。通过配置I2CSADDR 寄存器为不同的值，可以实现多机通讯(一主多从)。I2CSADDR 的bit0 位是记录主机读写命令状态位，0 表示写，1 表示读。

2)、从机硬件地址配置(在硬件标准模式下)
硬件地址配置只在硬件标准模式下有作用。只有在硬件标准模式下，当主机发送的设备地址匹配后，需要接着发送 8 位硬件地址。当这8 位硬件地址数据与从机的I2CSHADR 寄存器匹配时，从机才会做出应答。

3)、待发送数据配置
主机读从机数据时，从机必须先把待发送数据写入内部数据寄存器中。通过I2CSDR 数据缓存寄存器，将待发送数据写入到内部数据寄存器中。
硬件模式下，内部数据缓存寄存器为4 个字节：依次将0、1、2、3 位数据字节写到I2CSDR 寄存器；
软件模式下，内部数据缓存寄存器为2 个字节：首次写时，依次将第0、1 位数据字节写到I2CSDR 寄存器；之后在每传输完一个字节产生发送中断(或查询到发送完成中断标志)时再往I2CSDR 寄存器写入下一个字节数据。

4)、工作模式配置
I2C Slave 有4 种工作模式，通过I2CSCR 寄存器的MODE<1:0>位可以配置I2C Slave 的工作模式:
- MODE<1:0> = 00;软件标准模式(可读写)
- MODE<1:0> = 01;软件快速模式(可读写)《常用模式》
- MODE<1:0> = 10;硬件标准模式(只读)
- MODE<1:0>=11;硬件快速模式(只读)

##### 3、清I2C Slave 中断标志位
使能I2C Slave 中断前，先将I2CSSR 寄存器清0，清I2C Slave 相关中断标志位。

##### 4、中断控制
I2CSCR 寄存器的INTEN 位是I2C Slave 中断使能位，有INTEN[3:0]4 个位与I2CSSR 寄存器的[3:0]位一一对应，分别是 I2C Slave 接收到硬件地址中断、I2C Slave 接收到字节数据中断、I2C Slave 发送完字节数据中断、I2C Slave 接收到设备地址中断(只在软件快速模式下产生)。I2CSSR[3:0]就是相应的中断标志位，不论中断是否使能，只要产生中断条件，相应的标志位就会置1。

需要注意的是，在不同模式下，产生不同的中断:
- 软件快速模式下，只产生I2C Slave 接收设备地址中断、接收完数据字节中断、发送完数据字节中断；《常用模式》
- 软件标准模式下，只产生I2C Slave 接收硬件地址中断(即内存地址中断)、I2C Slave 接收完数据字节中断、I2C Slave 发送完数据字节中断。
- 硬件快速模式下，不产生中断；
- 硬件标准模式下，只产生I2C Slave 接收硬件地址中断(即内存地址中断)，接收到的硬件地址保存在I2CSRHADR 寄存器中；

##### 5、外设模块中断控制
使能寄存器IRQ5ER 的bit1 位，IRQ5ER_I2CSINT = 0x02，即使能I2C Slave 外设模块的中断。

##### 6、内核中断控制
```c
EX5 = 1;//使能内核中断5
//配置内核中断5 中断优先级IP2_bit[3:2](0:最低优先级;3:最高优先级)
EA=1;//使能总内核中断
```

##### 7、使能响应主机读写数据
I2CSCR 寄存器的ACKEN 位是使能从机CBM73xxF 响应主机读写数据控制位，只有将ACKEN 置为1，从机才会响应主机的命令；否则就算时序正确，从机也不会回应主机。

### I2C Slave 软件快速模式(可读写)《常用模式》
#### 软件读操作步骤:
1、I2C Slave 配置成软件快速模式，使能响应主机读写；
2、从机使能接收到设备地址中断、使能发送完成中断；
3、I2C 主机发送匹配的从机设备地址和读标志，即可从Slave 开始读取字节数据；
4、从机产生接收到设备地址中断时，将预发送数据写入到内部数据缓存寄存器I2CSDR 中(第一次写入2 个待发字节数据)。
5、主机延时小段时间等待4 步骤完成，然后开始从从机读数据；
6、从机每次产生发送数据完成中断时(通过查询到发送完数据中断标志位)，将下一个待发送数据写入到内部数据缓存寄存器I2CSDR 中。

> 时序图19-3软件快速模式主机读时序文字描述：
> SCL为时钟线、SDA为数据线；主机发送从机设备地址，R/W=1代表读操作；从机收到设备地址触发I2CSSR_RXDADR中断，软件清除该中断；从机依次输出数据字节0~n，每发送1字节触发I2CSSR_TXDAT中断，软件清除中断；SDA上包含启动条件、ACK应答位、NACK非应答位、停止条件。

#### 软件写操作步骤:
1、I2C Slave 配置成软件快速模式，使能响应主机读写；
2、使能接收到设备地址中断、使能接收数据完成中断；
3、I2C 主机发送匹配的设备地址和读标志，即可往Slave 开始写字节数据；
4、从机产生接收到设备地址中断时，表示主机将会发送数据给从机，从机初始化数据Buffer 准备接收后续主机发送来的数据；
5、主机延时小段时间等待4 步骤完成，开始继续往从机写数据；
6、从机产生接收数据完成中断时，通过查询接收完字节数据中断标志位，通过读I2CSDR 寄存器，将主机发来的数据保存下来。

> 时序图19-3软件快速模式主机写时序文字描述：
> SCL为时钟线、SDA为数据线；主机发送从机设备地址，R/W=0代表写操作；从机收到设备地址触发I2CSSR_RXDADR中断并软件清零；主机依次下发数据字节0~n，从机每收到一字节触发I2CSSR_RXDAT中断，软件清除中断；总线包含起始条件、ACK应答、停止条件。

### I2C Slave 软件标准模式(可读写)
#### 软件操作步骤:
1、I2C Slave 配置成软件标准模式，使能响应主机读写；
2、使能接收到地址中断；
3、通过I2CSDR 寄存器，将预发送数据写入到内部数据寄存器中。最多可以写入2 字节数据；
4、若主机执行写操作，I2C 主机发送匹配的设备地址和写标志，再送入内存地址，再紧跟待写入数据。从机中断触发后，根据接收到的内存地址的值以及接收数据，将数据存入到对应的地址中；
6、若主机执行读操作，I2C 主机发送匹配的设备地址和写标志，再送入内存地址。然后主机再次发送匹配的设备地址和读标志。从机中断触发后，通过查询发送完字节数据中断标志位，根据接收到的内存地址的值，调配相应的数据，通过I2CSDR 寄存器，将后续待发送数据写入到内部数据寄存器中。

> 时序19-4软件标准模式主机读时序：主机先发设备地址+R/W=0、内存地址，从机收到内存地址触发RXADR中断；主机重新发起设备地址+R/W=1，从机连续最多输出2字节数据，每次收发伴随ACK应答；
> 时序19-5软件标准模式主机写时序：主机发送设备地址(R/W=0)→内存地址→多字节写入数据，从机收到内存地址产生RXADR中断、收到字节产生RXDAT中断，总线带起始、停止与ACK信号。

### I2C Slave 硬件快速模式(只读)
#### 软件操作步骤:
1、I2C Slave 配置成硬件快速模式，使能响应主机读写；
2、通过I2CSDR 寄存器，将预发送数据写入到内部数据寄存器中，最多可写入4 字节数据；
3、I2C 主机发送匹配的设备地址和读标志，即可从Slave 读取最多4 个字节数据。

> 时序19-1硬件快速模式读时序：主机起始+设备地址(R/W=1)，从机连续自动送出最多4字节数据，末尾主机NACK结束通讯。

### I2C Slave 硬件标准模式(只读)
#### 软件操作步骤:
1、I2C Slave 配置成硬件标准模式，使能响应主机读写；
2、配置I2CSHADR 寄存器；
3、通过I2CSDR 寄存器，将预发送数据写入到内部数据寄存器中，最多可写入4 字节数据。
4、I2C 主机发送匹配的设备地址和写标志，再送入硬件地址(主机发送的硬件地址必须要与从机软件配置的I2CSHADR 寄存器一致)，然后主机再次发送匹配的设备地址和读标志，即可从Slave 读取最多4 个字节数据。

> 时序19-2硬件标准模式读时序：主机先发设备地址+写位+硬件地址，再次发起设备地址+读位后从机连续输出最多4字节数据。

### I2C Slave 寄存器的定义
表19-1、I2C Slave 寄存器列表

|寄存器名称|寄存器地址|地址类型|说明|POR 复位值|
| ---- | ---- | ---- | ---- | ---- |
|I2CSCR|0xD8FF|XRAM|I2C Slave 控制寄存器|0000 0000|
|I2CSSR|0xD9FF|XRAM|I2C Slave 状态寄存器|0000 0000|
|I2CSDR|0xDBFF|XRAM|I2C Slave 数据寄存器|0000 0000|
|I2CSADDR|0xDCFF|XRAM|I2C Slave 设备地址寄存器|0010 0010|
|I2CSRHADR|0xDDFF|XRAM|I2C Slave 接收硬件地址寄存器|0000 0000|
|I2CSHADR|0xDEFF|XRAM|I2C Slave 硬件地址寄存器|0000 0000|

以上表中所有寄存器均支持按位操作或直接对字节操作。

#### I2CSCR:I2C Slave 控制寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|-|MODE1|MODE0|ACKEN|RXIDADDRINTEN|TXDATINTEN|RXDATINTEN|RXHADDRINTEN|
|U-x|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|R/W-0|

> 注：
> bit7：保留未用
> bit6~5 MODE[1:0]：I2C Slave工作模式选择位
> 00 = 软件标准模式；01 = 软件快速模式；10 = 硬件标准模式；11 = 硬件快速模式
> bit4 ACKEN：响应主机读写使能位，0 = 禁止响应主机读写，1 = 使能响应主机读写
> bit3 RXIDADDRINTEN：I2C Slave 接收到设备地址中断使能位(只在软件快速模式下产生)，0=禁止，1=使能
> bit2 TXDATINTEN：I2C Slave 发送完字节数据中断使能位(只在软件快速、软件标准模式下产生)，0=禁止，1=使能
> bit1 RXDATINTEN：I2C Slave 接收到字节数据中断使能位，0=禁止，1=使能
> bit0 RXHADDRINTEN：I2C Slave 接收到硬件地址(即内存地址)中断使能位，0=禁止，1=使能

#### I2CSSR:I2C Slave 状态寄存器
|bit7|bit6|bit5|bit4|bit3|bit2|bit1|bit0|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|-|-|-|-|RXIDADDR|TXDATA|RXDATA|RXHADDR|
|U-x|U-x|U-x|U-x|R/W-0|R/W-0|R/W-0|R/W-0|

> 注：
> bit7~4：保留位
> bit3 RXIDADDR：收到从机设备地址标志
> bit2 TXDATA：数据发送完成标志
> bit1 RXDATA：收到单字节数据标志
> bit0 RXHADDR：收到硬件内存地址标志

# I2C Slave剩余寄存器、I2C Master、EFC(类EEPROM)模块说明
## I2CSSR 剩余位说明
| bit 7-5 | 保留未用 |
| --- | --- |
| bit 4 | SENDBUSY:I2C Slave 发送忙标志<br>0 = 空闲<br>1 = 正在发送数据 |
| bit 3 | RXIDADDR:I2C Slave 接收到设备地址中断标志位(只在软件快速模式下产生)<br>0 = I2C Slave 未接收到设备地址<br>1 = I2C Slave 接收到了设备地址,需软件写0 清0 |
| bit 2 | TXDATA:I2C Slave 发送完字节数据中断标志位(只在软件快速、软件标准模式下产生)<br>0 = I2C Slave 字节数据还未发送完<br>1 = I2C Slave 字节数据发送完成,需软件写0 清0 |
| bit 1 | RXDATA:I2C Slave 接收到字节数据中断标志位<br>0 = I2C Slave 未接收到字节数据<br>1 = I2C Slave 接收到了字节数据,需软件写0 清0 |
| bit 0 | RXHADDR:I2C Slave 接收到硬件地址(即内存地址)中断标志位<br>0 = I2C Slave 未接收到地址<br>1 = I2C Slave 接收到了硬件地址,需软件写0 清0 |

## I2CSDR:I2C Slave 数据寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CSDR[7:0]:I2C Slave 数据寄存器。将待发送数据写入到内部数据寄存器,是通过I2CSDR 寄存器的配合来实现的。详细请查看18.1.1.4 小节“待发送数据配置”。

## I2CSADDR:I2C Slave 设备地址寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ADDR6 | ADDR5 | ADDR4 | ADDR3 | ADDR2 | ADDR1 | ADDR0 | WEN |
| R/W-0 | R/W-0 | R/W-1 | R/W-0 | R/W-0 | R/W-0 | R/W-1 | R/W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-1 ADDR[6:0]:I2C Slave 设备地址位，只有当I2C 主设备发送的设备地址与此寄存器数值匹配时,才能正常通讯。
> bit 0 WEN:从机接收到I2C 主机发送来的读写状态位，0 = I2C 主机执行写操作，1 = I2C 主机执行读操作。

## I2CSRHADR:I2C Slave 接收硬件地址寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CSRHADR[7:0]:I2C Slave 接收硬件地址寄存器，I2C 主设备发送的硬件地址会保存在I2CSRHADR 中,软件可以读取I2CSRHADR 获得硬件地址值。

## I2CSHADR:I2C Slave 硬件地址寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CSHADR[7:0]:I2C Slave 硬件地址寄存器。只有在硬件标准模式下,I2C 主设备发送的硬件地址必须与此寄存器值匹配时才能读取数据。其它模式下配置I2CSHADR 无效。

# 19.2、I2C Master(主控模块)
主控模式下所有串行时钟脉冲和启动/停止条件均由主器件CBM73xxF 产生。
在主控发送模式下,串行数据通过数据线输出,而串行时钟由时钟线输出。发送的第一个字节由接收器件(从机)的设备地址(7 位)和R/W 位(0:W 表示写;1:R 表示读)组成。在发送模式下,R/W 位将是逻辑0。因此,发送的第一个字节是一个7 位从器件设备地址,后面跟0 表示发送。串行数据每个字节发送8 位,每发送一个字节,主机会收到一个从机返回的应答位。主机控制启动和停止条件的输出表明串行传输的开始和结束。

在主控接收模式下,串行数据通过数据线接收,而串行时钟由时钟线输出。发送的第一个字节由发送器件(从机)的设备地址(7 位)和R/W 位(0:W 表示写;1:R 表示读)组成。在接收模式下,R/W 位将是逻辑1。因此,发送的第一个字节是一个7 位从器件设备地址,后面跟1 表示接收。每次接收8 位串行数据。每接收到一个字节,主机都会发送一个应答位给从机。主机控制启动和停止条件的输出表明串行传输的开始和结束。

在I2C 主控模式下,可以通过I2CMPRERH:I2CMPRERL 寄存器配置I2C 通讯的波特率。

## I2C Master 的操作步骤
配置I2C Master 的操作步骤如下:
### 1、端口配置
1)、配置IO 复用功能寄存器,将对应IO 口配置为I2C Master 数据线、I2C Master 时钟线后(无需再配置PnOE);
2)、配置PnPURSELRH:PnPURSELRL 寄存器可以选择对应的时钟数据线的内部上拉电阻,建议设置10K 上拉。
> 注:端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1。

### 2、波特率配置
I2C Master 模块的时钟是由SYSCLK/(SCLK1DIV+1)分频后的时钟(详情时钟框图请参考“4.1、系统总时钟框图”)。通过配置I2CMPRERH: I2CMPRERL 寄存器的值,可以调整I2C Master 的通讯波特率。
计算方法如下(SYSCLK 是系统所选择的时钟源):
\[I2C Master 波特率 = SYSCLK /((SCLK1DIV+1)* (I2CMPRERH:I2CMPRERL + 1) * 5)\]
例如:SYSCLK 选择内部高速48M 时钟,SCLK1DIV = 2,I2C Master 波特率= 100KHz;
I2CMPRERH:I2CMPRERL = 48MHz/ (3 *(5*100KHz)) - 1 = 31;

### 3、清I2C Master 中断标志位
使能I2C Master 中断前,先将I2CMCTR 寄存器的CLRIF 位置1,清I2C Master 中断标志位。

### 4、中断控制
I2C Master 只有一个中断,若中断使能,每完成8 位数据传输或者当总线仲裁失败就会产生中断。中断使能位是I2CMCR的IE 位,I2CMSR 寄存器的IF 位是对应的中断标志位。不论中断是否使能,只要产生中断条件,相应的标志位就会置1。I2CMSR 寄存器除了IF 中断标志位,还有许多标志位,这些只是标志位,不会产生中断。详细请查看寄存器定义。

### 5、外设模块中断控制
使能寄存器IRQ5ER 的bit3 位,IRQ5ER_I2CMINT = 0x08,即使能I2C Master 外设模块的中断。

### 6、内核中断控制
```c
EX5 = 1;//使能内核中断5
//配置内核中断5 中断优先级IP2_BIT[3:2](0:最低优先级;3:最高优先级)
EA=1;//使能总内核中断
```

### 7、主控模式使能
将I2CMCR 寄存器的I2CMEN 位置1,使能I2C Master 模块功能。使能I2C Master 后,才能进行数据传输。

### 8、传输控制
一旦使能主控模式,用户可以进行以下操作:
- 发出一个启动条件
- 产生一个停止条件
- 开始数据/地址的发送
- 开始接收数据
- 在接收到数据字节后产生应答条件

这些操作都是通过I2CMCTR 寄存器来控制的,但是需要注意的是启动条件、停止条件和应答条件都不是单独控制的,必须和8 位数据/地址的传输组合在一起。

例如:发出一个启动条件
1)、因为启动条件后面必须接一个7 位设备地址加一位R/W 位,所以先把传送的7 位设备地址+1 位R/W 位的数据写入I2CMDR 寄存器。
2)、把I2CMCTR 寄存器的START 位、WR 位同时置1。
3)、当START、WR 位同时置1 后,即启动传输。I2C Master 模块就会发出一个启动条件,后面接着发送7 位设备地址+1位R/W 位组成的8 位数据。传送完成后,START 和WR 位会自动清0。

## 读写例程
### 例19-1:向从机写1 个字节数据
从机地址:0x4E
字节数据:0x65
```c
I2CMTRDR = 0x9C;//设备地址+写(0x4E<<1 | 0x00)
I2CMCTR = 0x90;//START+WR
while(0x02 == I2CMSR_TIP);//等待传送完成
if(0x00 == I2CMSR_RXACK)//查询是否收到应答信号
{
    I2CMTRDR = 0x65;//待发送数据
    I2CMCTR = 0x50;//STOP+WR
    while(0x02 == I2CMSR_TIP) ;//等待传送完成
}
```

### 例19-2:读取从机1 个字节数据
从机地址:0x4E
读数据地址:0x20
```c
unsigned char dat;
I2CMTRDR = 0x9D; //设备地址+写(0):0x4E<<1 + 0
I2CMCTR = 0x90; //START+WR
while(0x02 == I2CMSR_TIP);//等待传送完成
if(0x00 == I2CMSR_RXACK) //查询是否收到应答信号
{
    I2CMTRDR = 0x20; //读数据地址
    I2CMCTR = 0x10; //WR
    while(0x02 == I2CMSR_TIP);//等待传送完成
    if(0x00 == I2CMSR_RXACK) //查询是否收到应答信号
    {
        I2CMTRDR = 0x9D; //设备地址+读(1):0x4E<<1 + 1
        I2CMCTR = 0x90; //START+WR
        while(0x02 == I2CMSR_TIP);//等待传送完成
        if(0x00 == I2CMSR_RXACK) //查询是否收到应答信号
        {
            I2CMCTR = 0x62; //STOP+RD+不发送应答信号
            while(0x02 == I2CMSR_TIP);//等待传送完成
            dat = I2CMTRDR; //保存读到的数据
        }
    }
}
```

## I2C Master 时序文字描述
1. **主控模式启动条件时序**
START位置1后产生启动时序，SDA先拉低再配合SCL时序，起始位发送完成硬件置位I2CMSR.BUSY，传输结束硬件清零TIP标志。
2. **主控模式停止条件时序**
时钟上升沿前拉低SDA，之后SDA拉高保持，完成停止信号后硬件清除BUSY、置位TIP标志。
3. **主控模式应答时序**
第9个SCL时钟周期从机拉低SDA产生ACK应答信号。
4. **主控发送时序**
主机先发从机地址+R/W=0，逐字节发送数据，每字节后等待从机ACK；START位开启起始、STOP开启停止，WR控制写传输，传输结束TIP自动清零、IF置中断标志。
5. **主控接收时序**
主机先发从机地址+R/W=1，从机逐字节返回数据；主机通过ACK位控制是否回复应答，STOP配合RD结束读操作，传输标志位变化规则同发送时序。

## I2C Master 寄存器的定义
表19-2、I2C Master 寄存器列表

| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| I2CMPRERL | 0xC8FF | XRAM | I2C Master 波特率配置寄存器低8 位 | 1111 1111 |
| I2CMPRERH | 0xC9FF | XRAM | I2C Master 波特率配置寄存器高8 位 | 1111 1111 |
| I2CMCR | 0xCAFF | XRAM | I2C Master 控制寄存器 | 0000 0000 |
| I2CMDR | 0xCBFF | XRAM | I2C Master 数据寄存器 | 0000 0000 |
| I2CMCTR | 0xCCFF | XRAM | I2C Master 传输控制寄存器 | 0000 0000 |
| I2CMSR | 0xCDFF | XRAM | I2C Master 状态寄存器 | 0000 0000 |

以上表中所有寄存器均支持按位操作或直接对字节操作。

### I2CMPRERL:I2C Master 波特率配置寄存器低8 位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 |
> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CMPRERL[7:0]：与I2CMPRERH 寄存器组成16 位I2C Master 波特率配置寄存器低8 位。

### I2CMPRERH:I2C Master 波特率配置寄存器高8 位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 | R/W-1 |
> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CMPRERH[7:0]：与I2CMPRERL 寄存器组成16 位I2C Master 波特率配置寄存器。
\[I2C Master 的波特率 = SYSCLK / ((SCLK1DIV+1) *(I2CMPRERH:I2CMPRERL + 1)* 5)\]
(SYSCLK 为系统所选择的时钟源)

### I2CMCR:I2C Master 控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| I2CMEN | I2CMIE | U-x | U-x | U-x | U-x | U-x | U-x |
| R/W-0 | R/W-0 | U-x | U-x | U-x | U-x | U-x | U-x |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7 I2CMEN:I2C 主控模式使能位，0 = 禁止I2C 主控模式，1 = 使能I2C 主控模式
> bit 6 I2CMIE:I2C Master 中断使能位，0 = 禁止I2C Master 中断，1 = 使能I2C Master 中断
> bit 5-0 未实现:读为0

### I2CMDR:I2C Master 数据寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 I2CMDR[7:0]:I2C Master 数据寄存器，保存待发送数据以及接收到的数据
> 注:
> 1、发送数据时,保存待发送的8 位数据;
> 2、发送从机设备地址时,保存7 位设备地址+1 位R/W 位,其中bit7-1 是设备地址,bit0 是R/W 位;
> 3、接收数据时,保存接收到的8 位数据。

### I2CMCTR:I2C Master 传输控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| START | STOP | RD | WR | U-x | U-x | ACK | CLRIF |
| W-0 | W-0 | W-0 | W-0 | U-x | U-x | W-0 | W-0 |

> 注: R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7 START:启动条件控制位，0 = 不发送启动条件，1 = 发送启动条件
> bit 6 STOP:停止条件控制位，0 = 不发送停止条件，1 = 发送停止条件
> bit 5 RD:从从机读数据控制位，0 = 不启动读数据，1 = 启动读,开始从从机读数据
> bit 4 WR:向从机写数据控制位，0 = 不启动写数据，1 = 启动写,开始向从机写数据
> bit 3-2 保留未用
> bit 1 ACK:主机接收到数据时,应答条件控制位，0 =发送应答条件，1 = 不发送应答条件
> bit 0 CLRIF:清除中断标志，写0 无效;写1 清除I2C Master 中断标志位
> 补充备注:
> 1、这些位写1 后,会自动清零;
> 2、START、STOP、ACK 位不能单独使用,必须配合WR、RD 位使用。但是RD、WR 位可以单独使用,具体操作请参考以上“例程”部分描述。

### I2CMSR:I2C Master 状态寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| RXACK | BUSY | AL | U-x | U-x | U-x | TIP | IF |
| R-0 | R-0 | R-0 | U-x | U-x | U-x | R-0 | R-0 |

> 注:R=可读位; W=可写位; U=保留位(读为0); -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7 RXACK:I2C Master 接收到应答条件标志位，0 = 接收到应答条件，1 = 未接收到应答条件
> bit 6 BUSY:I2C Master 总线忙标志位，0 = 发送了STOP 信号探测到总线忙,STOP 命令失败，1 = 发送了START 信号探测到总线忙,START 命令失败
> bit 5 AL:I2C Master 总线仲裁失败标志位，0 = 总线仲裁成功，1 = 总线仲裁失败
> bit 4-2 保留未用
> bit 1 TIP:I2C Master 传输状态标志位，0 = 数据传输完成，1 = 数据传输中
> bit 0 IF:I2C Master 中断标志位，0 = 数据正在传输或没有传输，1 = 8 位数据传输完成或仲裁失败

# 20、 EFC 模块(类EEPROM)
CBM73xxF 系列MCU 的Flash 存储器包括Main 区和NVR 区两部分。Main 区为芯片的程序存储器(ROM),大小为24K~64K; NVR 区可供用户程序访问,用于存储数据(类EEPROM),支持掉电保存功能,大小为1K。
Main 区和NVR 区分为若干Sector 区,每个Sector 区为512Byte,各区对应地址如下:
- Sector 0(0x0000~0x01FF)、Sector 1(0x0200~0x03FF)……Sector 126(0xFC00~0xFDFF)、Sector 127(0xFE00~0xFFFF)（Main区）
- NVR0(0xF800~0xF9FF)、NVR1(0xFA00~0xFBFF)（NVR区，NVR 区禁止越界访问）

Flash 存储器在正常工作条件下(整个VDD 范围内)是可读写的。这些存储器并不直接映射到寄存器文件空间,而是通过特殊功能寄存器对其进行间接寻址。共有8 个特殊功能寄存器用于访问这些存储器:FDR、FADRL、FADRH、FCR、FPSR、FDIV、FCRCER、FCRCSR。
> 注:操作EFC 模块前,必须通过配置FDIV 时钟分频器,将EFC 模块的时钟配置为1MHZ,即\[EFCCLK = SYSCLK / FDIV\] (时钟源为SYSCLK 详情见“系统总时钟框图”)。

## 20.1、NVR 区的操作(类EEPROM)
NVR 区的数据具有掉电保存的功能,同时NVR 区支持对sector 擦除、字节编程、字节读取操作。

### sector 擦除
要写NVR 区存储器之前,必须要先进行擦除操作。EFC 模块只支持sector 区擦除(不支持字节擦除),即每次擦除sector 内任意地址都会将1 个sector 的整个区域擦除。
对于sector 擦除操作,FADRL 和FADRH 寄存器组成一个16 位Flash 存储器地址。FCR 寄存器的NVRSEL 位必须为1,ERASE 位用于控制启动sector 擦除。只要设置Flash 存储器地址落在目标sector 内,将ERASE 位置1,即启动sector 擦除,擦除完成后ERASE 位自动清0,所以可查询ERASE 位的值,来判断擦除是否完成。注意,在操作EFC 模块前,必须先按顺序写FPSR 等于0x55、0xAA。
#### 例20-1:NVR sector 擦除
```c
void eraseSector(uint8 sel,uint16 adr)
{
    //FDIV = 0x30;//配置EFCCLK=SYSCLK/48=1M,SYSCLK 默认为内部48M,FDIV 只需在配置系统时钟源时初始化一次即可
    FPSR = 0x55;
    FPSR = 0xAA;
    FCR_NVRSEL = sel << 2 ;//1:NVR 区;0:main 区
    FADRL = adr & 0x00ff; //adr 是编程地址
    FADRH = (adr & 0xff00) >> 8;
    FCR = 0x06;//启动擦出NVR 区
    while(0x02 == FCR_ERASE){;}//判断是否完成
}
```

### 字节编程
EFC 模块支持对NVR 区存储器字节编程,用户应该先将编程字节的地址写入FADRL 和FADRH 寄存器,编程数据写入FDR 寄存器,FCR 寄存器的NVRSEL 位必须为1,PROG 位用于控制启动字节编程。将PROG 位置1,即启动字节编程,编程完成后 PROG 位自动清0,所以可查询PROG位是否为0,来判断编程是否完成。注意,在操作EFC 模块前,必须先按顺序写FPSR 等于0x55、0xAA。
#### 例20-2:NVR 区字节编程
```c
void writeByte(uint8 sel,uint16 adr,uint8 dat)
{
    //FDIV = 0x30; //配置EFCCLK=SYSCLK/48=1M,SYSCLK 默认为内部48M,FDIV 只需在配置系统时钟源时初始化一次即可
    FPSR = 0x55;
    FPSR = 0xAA;
    FCR_NVRSEL = sel << 2 ;//1:NVR 区;0:main 区
    FDR = dat;//dat 是编程数据
    FADRL = adr & 0x00ff;//adr 是编程地址
    FADRH = (adr & 0xff00) >> 8;
    FCR = 0x05;//启动对NVR 区字节编程
    while(0x01 == FCR_PROG){;}//判断是否完成
}
```

### 字节读
读NVR 区存储器是通过movc 指令实现的。
#### 例20-3:NVR 字节读取
```c
unsigned char readByte(uint8 sel,uint16 adr)
{
    unsigned char dat;
    //FDIV = 0x30; //配置EFCCLK=SYSCLK/48=1M,SYSCLK 默认为内部48M,FDIV 只需在配置系统时钟源时初始化一次即可
    FCR_NVRREADSEL = sel<<3 ;//1:NVR 区;0:main 区
    dat = CBYTE[adr];
    return dat;
}
```

## 20.2、MAIN 区CRC16-CCITT 校验
CBM73xxF 系列MCU 的MAIN 区支持CRC16-CCITT 校验,支持:上电校验、动态校验。但CRC 校验过程中MCU 内核是停止工作的,不响应中断事件的发生,但会滞后一定时间(当MCLK=16M 时,对8 块main 区进行CRC 校验的总时间约等于1.5ms)。
CRC 校验功能把MCU 的MAIN flash 分成8 大块,每块大小为8 Kbyte(第4 块可能为8k 或7.5kbytes),这样可以方便单独对某些块进行CRC 校验操作。

表20-2、CRC 校验块地址详表(未使能5.9 章节的在线更新功能时)
| CRC 校验块 | MAIN 区 flash 地址 | 空间大小 |
| --- | --- | --- |
| 第 1 块 main flash | 0x0000~0x1FFF | 8 Kbytes |
| 第 2 块 main flash | 0x2000~0x3FFF | 8 Kbytes |
| 第 3 块 main flash | 0x4000~0x5FFF | 8 Kbytes |
| 第 4 块 main flash | 0x6000~0x7FFF | 8 Kbytes |
| 第 5 块 main flash | 0x8000~0x9FFF | 8 Kbytes |
| 第 6 块 main flash | 0xA000~0xBFFF | 8 Kbytes |
| 第 7 块 main flash | 0xC000~0xDFFF | 8 Kbytes |
| 第 8 块 main flash | 0xE000~0xFFFF | 8 Kbytes |

表20-3、CRC 校验块地址详表(使能5.9 章节的在线更新功能时)
| CRC 校验块 | MAIN 区 flash 地址 | 空间大小 |
| --- | --- | --- |
| 第 1 块 main flash | 0x0000~0x1FFF | 8 Kbytes |
| 第 2 块 main flash | 0x2000~0x3FFF | 8 Kbytes |
| 第 3 块 main flash | 0x4000~0x5FFF | 8 Kbytes |
| 第 4 块 main flash | 0x6000~0x7DFF | 7.5 Kbytes |

### 20.2.1、上电校验
是指在MCU 上电的过程中,MCU 可以开启对MAIN 区flash 程序存储块的CRC 自检功能,如果自检结果正确则上电成功,然后开始执行用户程序;如果自检失败,则可以选择停止MCU 的上电过程,用户程序也不会运行。
> 注:
> 1、通过配置上位机编程软件,可以选择是否使能上电校验,可以选择上电校验出错时是否停止MCU 的上电;(详情参考推广资料里的《CBM7X 系列烧录器使用手册_V1.1.pdf》)
> 2、用户软件也可以在上电时通过查询寄存器FCRCSR 对应位来获取CRC 上电校验的结果,0 表示无误,1 表示出错; 如果查询到上电校验结果出错,可以执行软件复位或其它保护动作。

### 20.2.2、动态校验
是指MCU 上电成功后,用户程序可以在主程序中进行软件控制,定时对MAIN 区Flash 程序存储块进行CRC 自检。FCRCER 寄存器控制是否使能动态校验,FCRCSR 为动态校验结果状态寄存器。如果查询到校验结果出错,可以执行软件复位或其它保护动作。
#### 例程20-4:CRC 动态校验例程
```c
unsigned crc_Check(uint16secten)//当使能remap 功能, secten 的bit[7:4]必须配置为0
{
    uint16 crcErrflg ;
    FCRCER = secten ;//使能对应扇区动态CRC
    MISCR1_NVR2CRCEN=((secten&0x100)>>7) ;//使能NVR2 区动态crc
    FPSR = 0x55 ;
    FPSR = 0xaa ;
    FCR_CRCSEQ = 0x50 ;
    FCR_CRCSEQ = 0xa0 ;
    FCR_CRCSEQ = 0x10 ;
    crcErrflg = MISCR3_NVR2CRCSR&0x02 ;
    crcErrflg <<= 7 ;
    crcErrflg |= FCRCSR ;
    return crcErrflg ;//返回CRC 校验结果
}
```

### 20.2.3、CRC16 校验公式及校验代码
CBM73xxF 系列MCU 的crc 校验算法使用标准的CRC16-CCITT 进行上电校验和动态校验,其生成多项式为 x16+x12+x5+1,简记式为1021,初始值采用0xFFFF。
```c
unsigned int CRC16_Bin(unsigned char *message, unsigned int len)
{
    unsigned int crc_reg =0xffff;//初值
    unsigned int i, j;
    for(i=0;i<len;i++)
    {
        unsigned int tmp=message[i];
        crc_reg^= (unsigned short)tmp<<8 ;
        for(j=0;j<8;j++)
        {
            if( (crc_reg&0x8000)==0x8000)
            {
                crc_reg=(crc_reg<<1)^0x1021;
            }
            else
            {
                crc_reg<<=1;
            }
        }
    }
    crc_reg^=0xffff;
    return crc_reg;
}
```

### 20.2.4、CRC16 校验的长度及数据内容
分两种情形:
1>、未使能5.9 章节的在线更新功能时
此时main flash 分为8 块CRC(参考上表20-2),每块的固定大小为8kbytes。假设.bin 烧录文件的实际数据长度为 len(len 为16bit 变量),则需要进行CRC 校验的有效块数为\[n=(((len-1)/8k)+1)\],且.bin 烧录文件的最后一块需要补数据到8k 进行校验,补的内容分为两部分:一是.bin 烧录文件实际长度页的最后一页补\[k=(256-len\%256)\]个数据0x00(k 为8bit变量):二是之后的页,补\[l=(n * 8 k-len-k)\]个数据0xFF直到8k大小。(每页为256byte, \(1 k=1024 byte\))
例如 \(len=10655bytes\) ,则 \(n=(((len-1) / 8 k)+1)=((10654 / 8 k)+1)=2\) 块crc, \(k=(256-len \% 256)=(256-10655 \% 256)=97\) 个数据 \(0x00;\) 因为 \(n=2\) 符合<=3的情况则 \(l=(n * 8 k-len-k)=(2 * 8 * 1024-10655-97)=5632\) 个数据0xFF。

2>、使能5.9 章节的在线更新功能时
此时main flash分为4块CRC(参考上表20-3),其中第 \(1^{~} 3\) 块固定大小为8kbytes,第4块固定大小为7.5k。假设.bin 烧录文件的实际数据长度为len(len为16bit变量),则需要进行CRC校验的有效块数为 \[n=(((len-1) / 8 k)+1)\],且.bin 烧录文件的最后一块需要补数据到8k 或7.5k 进行校验,补的内容分为两部分:一是.bin 烧录文件实际长度页的最后一页补 \[k=(256-len \% 256)\]个数据0x00(k为8bit变量):二是,如果 \(n<=3\) 时则之后的页,补 \[l=(n * 8 k-len-k)\]个数据0xFF直到8k大小,如果 \(n=4\) 时则之后的页,补 \[l=((3 * 8 k+7.5 k)-len-k)\]个数据0xFF直到8k大小。(每页为256bytes, \(1 k=1024 bytes\))
例如 \(len =10655 bytes\) ,则 \(n=(((len-1) / 8 k)+1)=((10654 / 8 k)+1)=2\) 块 \(crc, k=(256-len \% 256)=(256-10655 \% 256)=97\) 个 \(0×00\); 因为 \(n=2\) 符合<=3的情况则\[l=(n * 8 k-len-k)=(2 * 8 * 1024-10655-97)=5632\]个 0xFF。
例如 \(len=28569bytes\) ,则 \(n=(((len-1) / 8 k)+1)=((28568 / 8 k)+1)=4\) 块crc, \(k=(256-len \% 256)=(256-28569 \% 256)=103\) 个 0x00;因为n=4 则\[l=((3*8k+7.5k)-len-k)=((3*8*1024+7.5*1024)-28569-103)=3584\]个0xFF。


## 20.3 EFC寄存器的定义
表20-3 EFC寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| FDR | 0xB0FF | XRAM | EFC 编程数据寄存器 | 0000 0000 |
| FADRL | 0xB1FF | XRAM | EFC 编程地址寄存器低8位 | 0000 0000 |
| FADRH | 0xB2FF | XRAM | EFC 编程地址寄存器高8位 | 0000 0000 |
| FCR | 0xB3FF | XRAM | EFC 控制寄存器 | 0000 0000 |
| FPSR | 0xB4FF | XRAM | EFC 写保护序列寄存器 | 0000 0000 |
| FDIVR | 0xB5FF | XRAM | EFC 时钟分频寄存器 | 0011 0000 |
| FCRCER | 0xB6FF | XRAM | CRC 动态使能寄存器 | 0000 0000 |
| FCRCSR | 0xB7FF | XRAM | CRC 校验结果寄存器 | 0000 0000 |

以上表中所有寄存器均支持按位操作或直接对字节操作。

### FDR：EFC 编程数据寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 FDR[7:0]:EFC 编程数据寄存器,用于配置编程数据。

### FADRL：EFC 编程地址寄存器低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 FADRL[7:0]:EFC 编程地址寄存器低8位,与FADRH 组成16 位编程地址。

### FADRH：EFC 编程地址寄存器高8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 FADRH[7:0]:EFC 编程地址寄存器高8位,与FADRL 组成16 位编程地址。对于Sector 擦除操作,只需要设置编程地址落在目标sector 内即可。

### FCR：EFC 控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CRCSEQ3 | CRCSEQ2 | CRCSEQ1 | CRCSEQ0 | NVRREADSEL | NVRSEL | ERA | PORG |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | U-x | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-4 CRCSEQ[3:0]:CRC 校验启动命令时序寄存器位。启动CRC 校验,需要按照顺序配置高4bit 为0x5,0xa,0x1 才能启动CRC 校验,当CRC_EN 配置全为0 时,无法启动CRC 校验;
> bit 3 NVRREADSEL:读数据区域选择位。0 = 选择Main 区，1 = 选择NVR 区；
> bit 2 NVRSEL:编程区域选择位。0 = 选择Main 区，1 = 选择NVR 区；
> bit 1 ERASE:编程区域选择位。当要启动sector 擦除时,将此位置1,擦除结束后会自动清0；
> bit 0 PORG:编程区域选择位。当要启动字节编程时,将此位置1,编程结束后会自动清0；
> 注:写FCR 寄存器之前,必须按顺序先写FPSR 等于0x55、0xAA。

### FPSR：EFC 写保护序列寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 FPSR<7:0>:写保护序列寄存器。
> 在操作FCR 寄存器之前,需将FPSR 写成0xAA;
> 将FPSR 写成0xAA 的步骤是:
> 1、当FPSR 等于0x00 时,可以将FPSR 写成0x55;
> 2、当FPSR 等于0x55 时,可以将FPSR 写成0xAA;
> 3、当编程结束或擦除结束时,FPSR 被强制置为0x00;

### FDIVR：EFC 时钟分频寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-1 | R/W-1 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 FDIVR<7:0>:EFC 时钟分频寄存器,必须通过配置此寄存器给EFC 模块提供1MHZ 的时钟。
> \[CLKEFC= SYSCLK/FDIVR\]
> SYSCLK 为系统时钟源默认为内部48M,时钟详情请参考“4.1、系统总时钟框图”。

### FCRCER：CRC 动态校验使能寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CRC7EN | CRC6EN | CRC5EN | CRC4EN | CRC3EN | CRC2EN | CRC1EN | CRC0EN |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 CRCnEN:第n 块MAIN 区flash CRC 动态校验使能位(\(0<=n<=7\))，0 = 不使能，1 = 使能第n 块MAIN 区flash 的crc 动态校验；当使能remap 功能,高4bit 必须配置为0。对应第n 块MAIN 区flash 地址请参考CRC校验地址表格。

### FCRCSR：CRC 校验结果寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CRC7SR | CRC6SR | CRC5SR | CRC4SR | CRC2SR | CRC1SR | CRC0SR |
| R-0 | R-0 | R-0 | R-0 | R-0 | R-0 | R-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7-0 CRCnSR[7-0]:CRC 校验结果状态位(\(0<=n<=7\))，0=CRC 校验结果正确，1 = CRC 校验结果错误。

# 21 LED驱动器
CBM73xxF 系列MCU 支持硬件LED 驱动扫描单元,仅需要很少的软件操作即可实现LED 显示控制。支持多种LED 数码管规格,配合IO 电流驱动能力控制寄存器(PnLDRVRH:PnLDRVRL)及本模块中的灰度等级选择位(LEDSETR 寄存器的GRAY[2:0]位), 可实现多级灰度显示。

## 21.1 LED的操作步骤
### 1、端口配置
1)、配置PxyIOCFG寄存器将IO口复用为LED GRID(COM)/SEG口功能;引脚方向会自动为输出方向(无需软件再配置PnOE)。
2)、PnLDRVRH:PnLDRVRL 寄存器用于配置IO 的驱动电流大小,与LEDSETR 寄存器的GRAY[2:0]位配合,可以满足不同的显示需求。
> 注:端口配置前应使能IOC 模块时钟,即将MDLCKCR 寄存器的IOCEN 位置1;另外不能将IO 口电流寄存器 PnLDRVRH/PnLDRVRL 的对应控制位配置成00,否则可能出错。

### 2、LED模块时钟使能
在操作LED 模块相关寄存器前,必须先使能LED 模块时钟,即将MDLCKCR 寄存器的LEDEN 位置1。

### 3、LED模块参数配置
#### 3.1 数码管的类型选择
LED 驱动器可以驱动不同类型的LED,LEDSETR 寄存器的TYPE 位可以选择所驱动的数码管是共阴极还是共阳极; MODE[2:0]位可以配置段位数,有6 种类型可以选择:
- 4 位13 段(IO 复用为GRIDm 的引脚接数码管的位选引脚,复用为SEGn 的引脚接数码管段引脚)
- 5 位12 段(IO 复用为GRIDm 的引脚接数码管的位选引脚,复用为SEGn 的引脚接数码管段引脚)
- 6 位11 段(IO 复用为GRIDm 的引脚接数码管的位选引脚,复用为SEGn 的引脚接数码管段引脚)
- 7 位10 段(IO 复用为GRIDm 的引脚接数码管的位选引脚,复用为SEGn 的引脚接数码管段引脚)
- 9 位8 段(IO 复用为GRIDm 的引脚接数码管的段引脚,复用为SEGn 的引脚接数码管位选引脚)
- 10 位7 段(IO 复用为GRIDm 的引脚接数码管的段引脚,复用为SEGn 的引脚接数码管位选引脚)

#### 3.2 显示的灰度等级设置
LEDSETR 寄存器的GRAY[2:0]位可以配置LED 显示的灰度等级,最多支持8 级;值越大,显示越亮。(与IO 的驱动电流配置寄存器配合后可以满足不同的显示需求)

#### 3.3 刷新率配置
LED 模块的时钟是内部OSC800K 时钟经过CKDIVCR 寄存器的ILCLKDIV[1:0]位分频后的时钟。详情请见“4.1 系统总时钟框图”;LED 显示的刷新率由LEDDIVR 寄存器控制,LEDDIVR 寄存器可以配置LED 时钟的分频数,分频数越大, LED 时钟越慢,LED 刷新率越低(闪烁明显),反之,刷新率越高(闪烁不明显)。

计算刷新频率:刷新频率是指1s 时间内,某位某段点亮的次数。
设显示模式为M位N段,但实际接的数码管为 \(Y(1<=Y<=M)\) 位。LED时钟分频数为DIV1(LEDDIVR寄存器配置):LED 时钟来自于内部OSC800K 经过ILCLKDIV 分频后的时钟,设ILCLKDIV 分频数为DIV2(ILCLKDIV[1:0]配置)。

则Y 位数码管依次被点亮的总时间为:
\[T=Tosc 800k \times(DIV1+1) × 2^{DIV2} × 8 × N × Y\]

因为控制方式为逐位扫描,所以,在T 时间内,某位某段只被点亮了一次。那么,刷新率计算公式为:
\[F=1 / T=1 /\left((Tosc800k \times(DIV1+1) × 2^{DIV2} × 8 × N × Y\right)\]

实例: \(M=7\) , \(N=10\) , \(Y=5\) , \(DIV1 =3\) ,DIV2=3(7位10断模式实际只驱动5位数码管情况),则刷新率:
\[F=1 s /\left(1.25 us × 4 × 2^{3} × 8 × 10 × 5\right)=62.5 Hz 。\]

实践表明:刷新频率为60Hz 时闪烁明显,达到70Hz 时可基本消除闪烁。

#### 3.4 数码管的有效位选择
GRIDVLDL 和GRIDVLDH 寄存器组成10bit 的GRIDVLD,用于配置LED 数码管的有效位数。LED 模块支持最多10 位数码管,实际使用中,若不足10 位,可配置GRIDVLD,将使用到的位置1,未使用的位清0(应用中根据实际使用数码管 的位数来配置,可提高显示刷新率)。
举例:若实际使用6 位数码管,使用的是第1,2,3,5,6,10 位,则可将GRIDVLD 设置成0x0237 (0010_0011_0111)。

### 4、显示数据模式配置
根据LEDSETR 寄存器MODE[2:0]位的配置,LEDDRn 寄存器的用法有所差异。
1)、当配置成4 位13 段、5 位12 段、6 位11 段、7 位10 段时:
LEDR1和LEDR0组成16位数据,取低n位 \((n=10\sim13)\) 作为数码管第1位的数据:LEDR3和LEDR2组成16位数据,取低n位 \((n=10\sim13)\) 作为数码管第2位的数据:以此类推。
2)、当配置成9 位8 段时:
\(LEDRn (n=0\sim8)\) 作为9位数码管的数据。
3)、当配置成10 位7 段时:
\(LEDRn (n=0\sim9)\) 作为10位数码管的数据,LEDRn取低7位。

根据实际的硬件接法,给数码管配置合适的数据。数据的1 个bit 对应于1 位数码管的1 个段;不管共阴还是共阳 的数码管,bit 位写1 亮,写0 灭。

### 5、显示使能
LEDCR 寄存器的ONOFF 位置1,即使能LED 显示,清0,即关闭LED 显示。需注意的是ONOFF 位置1 后,等到WREN 位被清0 时,才表示显示真正使能;ONOFF 位清0,等到WREN 位被置1 时,才表示显示已经关闭。
> 注:当需要更改显示数据时,必须先关闭LED 显示,查询LEDCR 寄存器的WREN 位为1 后,才能配置新的显示数据,然后再使 能LED 显示。

## 21.2 数码管的接法
1)当配置成4 位13 段、5 位12 段、6 位11 段、7 位10 段时,IO 复用为GRIDm 的引脚接数码管的位选引脚,复用为SEGn 的引脚接数码管段引脚。
2)当配置成9 位8 段、10 位7 段时,IO 复用为GRIDm 的引脚接数码管的段引脚,复用为SEGn 的引脚接数码管位选引脚。

## 21.3 LED显示控制原理
### 共阳LED驱动文字描述
采用逐位扫描驱动方式，以3位8段共阳数码管为例：
1、3路GRID引脚依次逐个输出高电平；
2、单路GRID高电平保持8个时间单位，7段数码管则对应7个时间单位；
3、8路SEG引脚在对应时间段按照显示数据电平输出；共阳规格GRID=高、SEG=低对应点亮，软件数据bit=1时SEG输出低电平；
4、1个时间单位 = 8个LED系统时钟，时钟由CKDIVCR、LEDDIVR分频配置，分频、有效位数变化会改变刷新速率。

### 共阴LED驱动文字描述
扫描逻辑和共阳一致，电平逻辑相反：GRID为低电平、SEG为高电平点亮，软件数据bit=1时SEG输出高电平。

## 21.4 LED寄存器的定义
表21-1 LED寄存器列表
| 寄存器名称 | 寄存器地址 | 地址类型 | 说明 | POR 复位值 |
| --- | --- | --- | --- | --- |
| LEDCR | 0xC0FF | XRAM | LED 控制寄存器 | 0000 0010 |
| LEDSETR | 0xC1FF | XRAM | LED 参数配置寄存器 | 0000 0111 |
| LEDDIVR | 0xC2FF | XRAM | LED 时钟分频寄存器 | 0000 0010 |
| GRIDVLDL | 0xC3FF | XRAM | LED 有效位配置寄存器低8位 | 0000 0000 |
| GRIDVLDH | 0xC4FF | XRAM | LED 有效位配置寄存器高2位 | 0000 0000 |
| LEDDR0 | 0x1100 | XRAM | LED 数据寄存器0 | 0000 0000 |
| LEDDR1 | 0x1101 | XRAM | LED 数据寄存器1 | 0000 0000 |
| LEDDR2 | 0x1102 | XRAM | LED 数据寄存器2 | 0000 0000 |
| LEDDR3 | 0x1103 | XRAM | LED 数据寄存器3 | 0000 0000 |
| LEDDR4 | 0x1104 | XRAM | LED 数据寄存器4 | 0000 0000 |
| LEDDR5 | 0x1105 | XRAM | LED 数据寄存器5 | 0000 0000 |
| LEDDR6 | 0x1106 | XRAM | LED 数据寄存器6 | 0000 0000 |
| LEDDR7 | 0x1107 | XRAM | LED 数据寄存器7 | 0000 0000 |
| LEDDR8 | 0x1108 | XRAM | LED 数据寄存器8 | 0000 0000 |
| LEDDR9 | 0x1109 | XRAM | LED 数据寄存器9 | 0000 0000 |
| LEDDR10 | 0x110A | XRAM | LED 数据寄存器10 | 0000 0000 |
| LEDDR11 | 0x110B | XRAM | LED 数据寄存器11 | 0000 0000 |
| LEDDR12 | 0x110C | XRAM | LED 数据寄存器12 | 0000 0000 |
| LEDDR13 | 0x110D | XRAM | LED 数据寄存器13 | 0000 0000 |

> 以上表中寄存器LEDDRn(0~13)均不支持按位操作,只能对整个寄存器进行字节的操作;其它寄存器支持按位操作或直接对字节操作。

### LEDCR：LED控制寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | U-x | U-x | U-x | U-x | WREN | ONOFF |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit 7~2：保留未用；
> bit1 WREN:可写LED 数据寄存器标志位，只读。改变LED 显示信息前,需先写ONOFF 使LED 显示灭,然后查询等待WREN 为1 时,才可以更改LED 显示信息;开启显示后WREN自动变低；
> bit0 ONOFF:LED 显示使能位，0 = 关闭LED 显示，1 = 使能LED 显示。

### LEDSETR：LED参数配置寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TYPE | MODE2 | MODE1 | MODE0 | U-x | GRAY2 | GRAY1 | GRAY0 |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | U-x | R/W-1 | R/W-1 | R/W-1 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit7 TYPE:LED类型，0=共阴极，1=共阳极；
> bit6~4 MODE[2:0]：
> 000 = 4 位13 段;SEG 脚作为段,GRID 脚作为位
> 001 = 5 位12 段;SEG 脚作为段,GRID 脚作为位
> 010 = 6 位11 段;SEG 脚作为段,GRID 脚作为位
> 011 = 7 位10 段;SEG 脚作为段,GRID 脚作为位
> 10x = 9 位8 段;GRID 脚作为段,SEG 脚作为位
> 11x = 10 位7 段;GRID 脚作为段,SEG 脚作为位
> bit3：保留未用；
> bit2~0 GRAY[2:0]灰度：
> 000 = 时间单位的12.5%
> 001 = 时间单位的25%
> 010 = 时间单位的37.5%
> 011 = 时间单位的50%
> 100 = 时间单位的62.5%
> 101 = 时间单位的75%
> 110 = 时间单位的87.5%
> 111 = 时间单位的100%

### LEDDIVR：LED时钟分频寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-x | U-x | U-x | U-x | U-x | R/W-0 | R/W-1 | R/W-0 |
> 注: R=可读位; W=可写位; U=保留位; -x=上电复位默认值(1:置1; 0:清零; x:未知值)
> bit7~3保留；bit2~0分频配置：
> 000= SCLK3 1分频
> 001= SCLK3 2分频
> 010= SCLK3 3分频
> 011= SCLK3 4分频
> 100= SCLK3 5分频
> 101 = SCLK3 6分频
> 110 = SCLK3 7分频
> 111= SCLK3 8分频

### GRIDVLDL：有效位低8位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 与GRIDVLDH拼接组成10bit GRIDVLD。

### GRIDVLDH：有效位高2位
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| U-0 | U-0 | U-0 | U-0 | U-0 | U-0 | R/W-0 | R/W-0 |
> bit7~2保留，bit1~0为GRIDVLD高2bit。

### LEDDRn：LED数据寄存器
| bit7 | bit6 | bit5 | bit4 | bit3 | bit2 | bit1 | bit0 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 | R/W-0 |
> 根据LEDSETR的MODE配置决定寄存器使用规则。

# 22 电气特性
## 极限参数
| 参数 | 参数范围 |
| ---- | ---- |
| 电源供应电压 | VSS-0.3V~VSS+6.0V |
| 端口输入电压 | VSS-0.3V~VDD+0.3V |
| 储存温度 | -55°C~125°C |
| 工作温度 | -40°C ~ 105°C |
| VSS 引脚的最大输出电流 | 120mA |
| VDD 引脚的最大输入电流 | 120mA |
| 任一 I/O 引脚的最大输出灌电流 | 85mA |
| 任一 I/O 引脚的最大输出拉电流 | 60mA |
| 总功耗 | 600mW |
> 注:如果芯片的工作条件超过极限参数所规定的范围,就可能对芯片造成永久性的损坏。上述值仅为运行条件极大值, 我们建议不要使芯片在规定的范围以外运行。芯片长时间工作在最大值条件下,其稳定性会受到影响。

## 直流电气特性(T=25℃)
|符号|参数描述|测试条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|VDD|工作电压|低压复位不使能,POR使能|2.7|-|5.5|V|
|VDD|工作电压|低压复位使能|2.9|-|5.5|V|
|IDD|正常工作电流5V|Touch Key off,LED off,ADC硬件无负载,Fsys=8MHz,WDT使能,ADCBUFEN关闭|-|4.5|-|mA|
|IDD|正常工作电流5V|Touch Key off,LED off,ADC硬件无负载,Fsys=16MHz,WDT使能,ADCBUFEN关闭|-|5|-|mA|
|IDD|空闲电流5V|Touch Key off,LED off,ADC硬件无负载,Fsys=8MHz,WDT关闭|-|2.8|-|mA|
|IDD|空闲电流5V|Touch Key off,LED off,ADC硬件无负载,Fsys=16MHz,WDT关闭|-|3|-|mA|
|IDD|休眠电流5V|全模块关闭,Fsys关闭,IO上拉高阻|-|160|-|uA|
|VIL|IO低输入电平|5V|0|-|0.65|V|
|VIL|IO低输入电平|通用|0|-|0.15VDD|V|
|VIH|IO高输入电平|5V|3.5|-|5.0|V|
|VIH|IO高输入电平|通用|0.8VDD|-|VDD|V|
|VPOR|上电复位电压|POR使能|2.185|2.3|2.415|V|
|VLVD|低压复位电压|2.9V档位|2.755|2.9|3.045|V|
|VLVD|低压复位电压|3.3V档位|3.135|3.3|3.465|V|
|VLVD|低压复位电压|3.7V档位|3.515|3.7|3.885|V|
|VLVD|低压复位电压|4.2V档位|3.99|4.2|4.41|V|
|IOL|灌电流 00/11配置|VOL=0.1VDD|5|6|8|mA|
|IOL|灌电流 01配置|VOL=0.1VDD|25|30|40|mA|
|IOL|灌电流 10配置|VOL=0.1VDD|50|60|85|mA|
|IOH|拉电流 00/11配置|VOH=0.9VDD|5|6|8|mA|
|IOH|拉电流 01配置|VOH=0.9VDD|25|30|40|mA|
|IOH|拉电流 10配置|VOH=0.9VDD|50|60|85|mA|
|RPH|IO上拉电阻 00档位|-|-|88|-|kΩ|
|RPH|IO上拉电阻 01档位|-|-|2.2|-|kΩ|
|RPH|IO上拉电阻 10档位|-|-|4.5|-|kΩ|
|RPH|IO上拉电阻 11档位|-|-|10|-|kΩ|

## 交流电气特性(T=25℃)
|符号|参数|测试条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|FIHCLK|内部48M晶振精度|3.3/5V,25℃|-1%|48M|+1%|MHz|
|FIHCLKT|48M温漂|-40~105℃|-1%|48M|+1%|MHz|
|TIHCLK|48M起振时间|3.3/5V|-|-|0.05|ms|
|FILCLK|800K精度|3.3/5V,25℃|-1%|800|+1%|KHz|
|FILCLKT|800K温漂|-40~105℃|-1%|800|+1%|KHz|
|TILCLK|800K起振时间|3.3/5V|-|-|0.05|ms|
|TECLK_M|外部高速晶振起振|3.3/5V|0.05|0.1|-|s|
|TECLK_K|外部低速晶振起振|3.3/5V|-|1|-|s|
|TEINT|外部中断最小脉宽|-|16|-|MCLK|
|TPOR|上电复位时长|-|-|40|-|ms|
|TWKT|IDLE唤醒时间|-|-|-|2|TSYS|
|TWKT|SLEEP唤醒时间|-|-|-|5|TSYS|

## 上电复位特性(T=25℃)
|符号|参数|条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|VPOR|复位电压|-40~105℃|-|-|2.3|V|
|RRVDD|电压上升速率|-40~105℃|0.05|-|-|V/ms|
|TPOR|VPOR维持时间|-|-|10|-|ms|

## 触控按键电气(T=25℃)
|符号|参数|条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|IKEY|整机按键工作电流|5V|-|3.3|-|mA|
|IREFKEY|单路参考电流|5V|-|30|-|uA|
|CKEY|按键感应电容|5V|5|30|60|pF|
|CREFKEY|可调参考电容|5V|5|-|60|pF|

## ADC电气(T=25℃)
|符号|参数|条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|VDD|ADC工作电压|-|2.7|-|5.5|V|
|VADI|ADC输入范围|-|0|-|VDD|V|
|Vref1v|内部基准1V|-|0.99|1.00|1.01|V|
|DNL|微分非线性3.3V|-|-|±2|-|LSB|
|INL|积分非线性3.3V|-|-|±2|-|LSB|
|IADC|3.3V模块功耗|SCLK=16M|-|1|1.25|mA|
|IADC|5V模块功耗|SCLK=16M|-|2.5|-|mA|
|IVref1v|基准开启功耗|-|-|1.5|2.5|mA|
|TADCK|ADC最大时钟|-|-|-|1.4|Mhz|
|TADC|单次转换时间(10bit)|-|13|-|17|TADCK|
|TADS|采样保持时间|-|2|-|6|TADCK|
|TON2ST|启动建立时间|-|-|-|1|us|
|TVref1v|基准稳定时间|-|-|-|500|us|

## Flash与NVR(类EEPROM)电气
|符号|参数|条件|最小|典型|最大|单位|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|VDD|擦写电压|-40~105℃|2.7|5.0|5.5|V|
|Tcycle|擦写寿命|5V|100000|-|-|次|
|Tsave|数据保存年限|25℃|20|-|-|年|
|Tsector|单扇区擦除时间(512B)|5V|4|5|6|ms|
|Tchip|64K全片擦除|5V|45|53|60|ms|

# 23 封装信息：SOP16尺寸规格
|符号|说明|最小(mm)|标准(mm)|最大(mm)|
| ---- | ---- | ---- | ---- | ---- |
|A|本体总跨度|5.81|—|6.24|
|B|塑体宽度|3.81|—|4.04|
|C|单引脚宽度|0.36|—|0.46|
|C’|塑体总长|9.91|—|10.10|
|D|芯片总厚度|1.40|—|1.70|
|E|引脚中心间距|—|1.27|—|
|F|引脚下沉高度|0.05|—|0.18|
|G|引脚外露长度|0.40|—|0.70|
|H|引脚厚度|—|0.2|—|
|a|引脚折弯角度|—|0°|8°|

# 封装尺寸、汇编指令与各附录参数
## 一、各封装外形尺寸
### (2) SOP20封装尺寸
| Symbol | 英寸(Min) | 英寸(Max) | mm(Min) | mm(Max) |
| ---- | ---- | ---- | ---- | ---- |
| A | 0.093 | 0.104 | 2.35 | 2.65 |
| A1 | 0.004 | 0.012 | 0.10 | 0.30 |
| A2 | 0.083 | 0.098 | 2.10 | 2.50 |
| b | 0.013 | 0.020 | 0.33 | 0.51 |
| C | 0.008 | 0.013 | 0.20 | 0.33 |
| D | 0.493 | 0.512 | 12.52 | 13.00 |
| E | 0.291 | 0.299 | 7.40 | 7.60 |
| e | 0.050(BSC) | — | 1.27(BSC) | — |
| HE | 0.398 | 0.418 | 10.11 | 10.61 |
| L | 0.016 | 0.050 | 0.40 | 1.27 |
| θ | 0° | 8° | 0° | 8° |

### (3) SOP28封装尺寸
| Symbol | 英寸(Min) | 英寸(Max) | mm(Min) | mm(Max) |
| ---- | ---- | ---- | ---- | ---- |
| A | 0.085 | 0.104 | 2.15 | 2.65 |
| A1 | 0.004 | 0.012 | 0.10 | 0.30 |
| A2 | 0.081 | 0.098 | 2.05 | 2.50 |
| b | 0.013 | 0.02 | 0.33 | 0.51 |
| C | 0.008 | 0.014 | 0.20 | 0.36 |
| D | 0.697 | 0.713 | 17.70 | 18.10 |
| E | 0.291 | 0.3 | 7.40 | 7.62 |
| e | 0.050(BSC) | — | 1.27(BSC) | — |
| HE | 0.402 | 0.418 | 10.21 | 10.61 |
| L | 0.016 | 0.05 | 0.40 | 1.27 |
| θ | 0° | 8° | 0° | 8° |

### (4) SSOP28封装尺寸(单位mm)
| 符号 | 说明 | 最小 | 正常 | 最大 |
| ---- | ---- | ---- | ---- | ---- |
| A | 跨度 | 7.6 | 7.8 | 8.0 |
| B | 塑胶跨度 | 5.1 | 5.3 | 5.5 |
| C | 脚宽度 | 0.29 | — | 0.33 |
| C’ | 塑胶体长 | 10 | 10.2 | 10.4 |
| D | 总高 | — | — | 2.0 |
| E | 脚间距 | 0.65 | — | — |
| F | 站高 | 0.15 | — | 0.2 |
| G | 脚长 | 0.55 | 0.75 | 0.95 |
| H | 脚厚度 | 0.15 | — | 0.2 |
| a | 脚角度 | — | 0 | 8 |

### (5) LQFP32封装尺寸(单位mm)
| SYMBOL | MIN | NOM | MAX |
| ---- | ---- | ---- | ---- |
| A1 | 0.05 | — | 0.15 |
| A2 | 1.35 | 1.40 | 1.45 |
| A3 | 0.59 | 0.64 | 0.69 |
| b | 0.33 | 0.35 | 0.41 |
| b1 | 0.32 | — | 0.38 |
| c | 0.13 | — | 0.17 |
| c1 | 0.12 | — | 0.14 |
| D | 8.80 | 9.00 | 9.20 |
| D1 | 6.90 | 7.00 | 7.10 |
| E | 8.80 | 9.00 | 9.20 |
| E1 | 6.90 | 7.00 | 7.10 |
| eB | 8.10 | — | 8.25 |
| e | 0.80BSC | 1.00REF | — |
| L | 0.45 | — | 0.75 |
| θ | 0° | — | 7° |

### (6) LQFP44封装尺寸(单位mm)
| SYMBOL | MIN | NOM | MAX |
| ---- | ---- | ---- | ---- |
| A | — | 1.60 | — |
| A1 | 0.05 | — | 0.15 |
| A2 | 1.35 | 1.40 | 1.45 |
| A3 | — | — | — |
| b | 0.275 | 0.30 | 0.325 |
| b1 | 0.59 | 0.64 | 0.69 |
| Dh | 0.13 | — | 0.18 |
| D | 11.80 | 12.00 | 12.20 |
| D1 | 9.90 | 10.00 | 10.10 |
| E | 11.80 | 12.00 | 12.20 |
| E1 | 9.90 | 10.00 | 10.10 |
| e | 0.80BSC | — | — |
| L | 0.45 | — | 0.75 |
| L1 | 1.00REF | — | — |
| R1 | 0.08 | — | 0.20 |
| θ1 | 0° | — | 7° |
| θ2 | 3° | — | 13° |

## 附录Ⅰ 汇编指令周期表
备注:@Ri 为@R0,@R1;Rn 为R0~R7
|序号|指令|指令代码|指令长度|(取指+执行)周期|
| ---- | ---- | ---- | ---- | ---- |
|1|ACALL addr11|xxx1\_0001|2|2|
|2|ADD A,Rn|0010\_1xxx|1|2|
|3|ADD A,direct|0x25|2|3|
|4|ADD A,@Ri|0010\_011x|1|2|
|5|ADD A,#data|0x24|2|2|
|6|ADDC A,Rn|0011\_1xxx|1|2|
|7|ADDC A,direct|0x35|2|3|
|8|ADDC A,@Ri|0011\_011x|1|2|
|9|ADDC A,#data|0x34|2|2|
|10|AJMP addr11|xxx0\_0001|2|2|
|11|ANL A,Rn|0101\_1xxx|1|2|
|12|ANL A,direct|0x55|2|3|
|13|ANL A,@Ri|0101\_011x|1|2|
|14|ANL A,#data|0x54|2|2|
|15|ANL direct,A|0x52|2|3|
|16|ANL direct ,#data|0x53|3|3|
|17|ANL C,bit|0x82|2|3|
|18|ANL C,~bit|0xb0|2|3|
|19|CJNE A,direct,rel|0xb5|3|3|
|20|CJNE A,#data,rel|0xb4|3|3|
|21|CJNE Rn,#data,rel|1011\_1xxx|3|3|
|22|CJNE @Ri,#data,rel|1011\_011x|3|3|
|23|CLR A|0xe4|1|1|
|24|CLR C|0xc3|1|1|
|25|CLR bit|0xc2|2|2|
|26|CPL A|0xf4|1|1|
|27|CPL C|0xb3|1|1|
|28|CPL bit|0xb2|2|3|
|29|DA A|0xd4|1|1|
|30|DEC A|0x14|1|1|
|31|DEC Rn|0001\_1xxx|1|2|
|32|DEC direct|0x15|2|3|
|33|DEC @Ri|0001\_011x|1|2|
|34|DIV AB|0x84|1|3|
|35|DJNZ Rn,rel|1101\_1xxx|2|2|
|36|DJNZ direct,rel|0xd5|3|3|
|37|INC A|0x04|1|1|
|38|INC Rn|0000\_1xxx|1|2|
|39|INC direct|0x05|2|3|
|40|INC @Ri|0000\_011x|1|2|
|41|INC DPTR|0xa3|1|4|
|42|JB bit,rel|0x20|3|3|
|43|JBC bit,rel|0x10|3|3|
|44|JC rel|0x40|2|2|
|45|JMP @A+DPTR|0x73|1|1|
|46|JNB bit,rel|0x30|3|3|
|47|JNC rel|0x50|2|2|
|48|JNZ rel|0x70|2|2|
|49|JZ rel|0x60|2|2|
|50|LCALL addr16|0x12|3|3|
|51|LJMP addr16|0x02|3|3|
|52|MOV A ,Rn|1110\_1xxx|1|2|
|53|MOV A,direct|0xe5|2|3|
|54|MOV A,@Ri|1110\_011x|1|2|
|55|MOV A,#data|0xe4|2|2|
|56|MOV Rn,A|1111\_1xxx|1|1|
|57|MOV Rn,direct|1010\_1xxx|2|3|
|58|MOV Rn,#data|0111\_1xxx|2|1|
|59|MOV direct,A|0xf5|2|2|
|60|MOV direct,Rn|1000\_1xxx|2|2|
|61|MOV direct,direct|0x85|3|3|
|62|MOV direct @Ri|1000\_011x|2|2|
|63|MOV direct,#data|0x75|3|3|
|64|MOV @Ri,A|1111\_011x|1|1|
|65|MOV @Ri,direct|1010\_011x|2|3|
|66|MOV @Ri,#data|0111\_011x|2|2|
|67|MOVC A,@A+DPTR|0x93|1|2|
|68|MOVC A,@A+PC|0x83|1|2|
|69|MOVX A,@Ri|1110\_001x|1|2|
|70|MOVX A,@DPTR|0xe0|1|2|
|71|MOVX @Ri,A|1111\_001x|1|1|
|72|MOVX @DPTR,A|0xf0|1|1|
|73|MOV C,bit|0xa2|2|3|
|74|MOV bit,C|0x92|2|2|
|75|MOV DPTR,#data16|0x90|3|3|
|76|MUL AB|0xa4|1|3|
|77|NOP|0x0|1|1|
|78|ORL A,Rn|0100\_1xxx|1|2|
|79|ORL A,direct|0x45|2|3|
|80|ORL A,@Ri|0100\_011x|1|2|
|81|ORL A,#data|0x44|2|2|
|82|ORL direct,A|0x42|2|3|
|83|ORL direct,#data|0x43|3|3|
|84|ORL C,bit|0x72|2|3|
|85|ORL C,~bit|0xa0|2|3|
|86|POP direct|0xd0|2|2|
|87|PUSH direct|0xc0|2|3|
|88|RET|0x22|1|3|
|89|RETI|0x32|1|3|
|90|RL A|0x23|1|1|
|91|RLC A|0x33|1|1|
|92|RR A|0x03|1|1|
|93|RRC A|0x13|1|1|
|94|SETB C|0xd3|1|1|
|95|SETB bit|0xd2|2|2|
|96|SJMP rel|0x80|2|2|
|97|SUBB A,Rn|1001\_1xxx|1|2|
|98|SUBB A,direct|0x95|2|3|
|99|SUBB A,@Ri|1001\_011x|1|2|
|100|SUBB A,#data|0x94|2|2|
|101|SWAP A|0xC4|1|1|
|102|XCH A,Rn|1100\_1xxx|1|3|
|103|XCH A,direct|0xc5|2|4|
|104|XCH A,@Ri|1100\_011x|1|3|
|105|XCHD A,@Ri|1101\_011x|1|3|
|106|XRL A,Rn|0110\_1xxx|1|2|
|107|XRL A,direct|0x65|2|3|
|108|XRL A,@Ri|0110\_011x|1|2|
|109|XRL A,#data|0x64|2|2|
|110|XRL direct,A|0x62|2|3|
|111|XRL direct,#data|0x63|3|3|

## 附录Ⅱ 内部48M高速时钟温漂曲线文字描述
测试条件：$VDD=5v$，温度范围$-40℃～105℃$；
横轴为环境温度（℃）：-60、-40、-20、0、20、40、60、80、100；
纵轴为输出频率（MHz）：范围47.60~48.40MHz，基准48.00MHz；
曲线整体在47.70MHz~48.30MHz区间小幅波动，全温域频率偏差在±1%规格内。

## 附录Ⅲ 内部800K低速时钟温漂曲线文字描述
测试条件：$VDD=5v$，温度$-40℃～105℃$；
横轴温度(℃)：-60~100；纵轴频率(KHz)：792.00~808.00，基准800.00KHz；
实测曲线波动范围796.00~806.00K，全温漂满足±1%指标。

## 附录Ⅳ ADC采样温漂(含内部1V基准偏差)
### 1、ADC采样温漂测试数据表
|温度/℃|0.5V(常规/硬件)|1.5V(常规/硬件)|2.5V(常规/硬件)|3.5V(常规/硬件)|4.5V(常规/硬件)|5V(常规/硬件)|Vref_1V(常规/硬件)|
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |----|
|-40|101.4/508.0|307.5/1549.4|514.6/2585.0|721.3/3625.5|926.5/4653.7|1023.0/5136.9|203.8/1024.7|
|-20|101.3/507.8|307.4/1548.2|514.6/2585.4|721.3/3624.0|926.4/4653.2|1023.0/5140.4|203.7/1024.1|
|0|101.4/508.1|307.1/1544.8|514.7/2584.3|721.5/3622.6|926.5/4650.8|1023.0/5135.6|204.0/1024.4|
|20|101.4/507.7|307.3/1545.3|514.9/2583.7|721.7/3621.2|926.6/4649.6|1023.0/5134.3|204.0/1024.4|
|40|101.5/507.7|307.6/1547.2|514.8/2584.2|721.9/3622.3|926.7/4650.7|1023.0/5134.5|204.0/1025.0|
|60|101.6/508.9|308.0/1548.7|515.0/2585.5|721.9/3623.8|926.9/4654.2|1023.0/5135.3|203.9/1024.1|
|85|101.6/510.1|308.2/1552.0|515.1/2591.3|722.0/3631.6|926.9/4662.7|1023.0/5144.6|203.6/1023.8|
|MAX|101.6/510.1|308.2/1552.0|515.1/2591.3|722.0/3631.6|926.9/4662.7|1023.0/5144.6|204.0/1025.0|
|MIN|101.3/507.7|307.1/1544.8|514.6/2583.7|721.3/3621.2|926.4/4649.6|1023.0/5134.3|203.6/1023.8|
|正偏差%|0.16/0.46|0.27/0.43|0.03/0.29|0.05/0.29|0.04/0.28|0.00/0.20|0.00/0.06|
|负偏差%|-0.10/0.00|-0.07/-0.03|-0.07/0.00|-0.05/0.00|-0.02/0.00|0.00/0.00|-0.19/-0.06|
|20℃实测电压(V)|0.495/0.495|1.501/1.509|2.514/2.522|3.524/3.536|4.524/4.540|4.995/5.014|0.996/1.000|
|电压误差(V)|0.005/0.005|-0.001/-0.009|-0.014/-0.022|-0.024/-0.036|-0.024/-0.040|0.005/-0.014|0.004/0.000|

>注:100*(MAX-20℃)/20℃:相对20℃的最大正偏差采样值百分比; 100*(MIN-20℃)/20℃:相对20℃的最大负偏差采样值百分比。

### 温漂结论
1、MCU 在-40℃~105℃工作环境下，常规模式采样值相对于20℃的漂移为+0.3%；
2、MCU 在-40℃~105℃工作环境下，硬件模式采样值相对于20℃的漂移为+0.5%；
3、常规模式全温采样误差最大+1.5%；
4、硬件模式以内部1VREF为基准全温误差+2.5%(内部VREF固有误差+1%)。

### 常规/硬件温漂曲线文字描述
1. 常规模式曲线：横轴温度-40~85℃，纵轴采样值/误差百分比；0.5V/1.5V/2.5V/3.5V/4.5V/5V/1VREF多条曲线，全温误差≤+1.5%；
2. 硬件模式曲线：同温度区间，采样数值整体约5倍常规档位，全温误差≤+2.5%。

## 附录Ⅴ XBYTE类型地址寄存器操作
```c
#define P0OE7 XBYTE[0x2AFF]
#define P0OE6 XBYTE[0x2A80]
#define P0OE5 XBYTE[0x2A40]
#define P0OE4 XBYTE[0x2A10]
#define P0OE3 XBYTE[0x2A08]
#define P0OE2 XBYTE[0x2A04]
#define P0OE1 XBYTE[0x2A02]
#define P0OE0 XBYTE[0x2A01]

#define P0PURSELRL3 XBYTE[0x30FF]
#define P0PURSELRL2 XBYTE[0x30C0]
#define P0PURSELRL1 XBYTE[0x3030]
#define P0PURSELRL0 XBYTE[0x300C]
#define P0PURSELRL XBYTE[0x3003]
```
### 一、P0OE寄存器操作
1、P0OE_7清0
```c
//方式一(字节操作)
P0OE &= 0x7F;
//方式二(位操作)
P0OE_7 = 0<<7;
```
2、P0OE_7置1
```c
//方式一
P0OE |= 0x80;
//方式二
P0OE_7 = 1<<7;
```
### 二、P0PURSELRL寄存器(P0PURSELRL_3)
```c
//1、清0(00档位)
//字节
P0PURSELRL &= 0x3F;
//位
P0PURSEL_3 = 0x00;

//2、置1(01档位)
P0PURSELRL &= 0x3F;
P0PURSELRL |= 0x40;
P0PURSEL_3 = 0x40;

//3、置2(10档位)
P0PURSELRL &= 0x3F;
P0PURSELRL |= 0x80;
P0PURSEL_3 = 0x80;

//4、置3(11档位)
P0PURSELRL &= 0x3F;
P0PURSELRL |= 0xC0;
P0PURSEL_3 = 0xC0;
```
>其它XBYTE寄存器操作方法同理。

## 附录Ⅵ IO端口三档驱动特性(6mA/30mA/60mA，VCC=5.11V)
### 说明
1、io口具有限流能力，在一定程度上可以避免io短路异常时引起电流过大而损坏IO或芯片的情况；
2、当IO所接的负载一定时，如果得到的驱动电流I或者驱动电压Vio达不到需求，则需调大IO电流驱动档位；
3、当测量工具不一致时，测得数据可能与表格数据存在一定误差，对应电流档位值的范围请参考“22 章节的直流电气特性”。

### 1、6mA驱动档位
#### 拉电流(IO输出高电平)
|Vcc(V)|Vio(V)|负载R(Ω)|I拉(mA)|
| ---- | ---- | ---- | ---- |
|5.11|4.96|10000|0.5|
|5.11|4.81|4700|1.0|
|5.11|4.48|2200|2.0|
|5.11|3.78|1000|3.8|
|5.11|2.76|510|5.4|
|5.11|0.67|100|6.7|
|5.11|0.34|50|6.8|
|5.11|0.07|10|7.0|

#### 灌电流(IO输出低电平)
|Vcc(V)|Vio(V)|负载R(Ω)|I灌(mA)|
| ---- | ---- | ---- | ---- |
|5.11|0.08|10000|0.5|
|5.11|0.16|4700|1.1|
|5.11|0.34|2200|2.2|
|5.11|0.73|1000|4.4|
|5.11|1.39|510|7.3|
|5.11|4.06|10|10.5|
|5.11|4.57|50|10.8|
|5.11|4.98|10|13.0|

### 2、30mA驱动档位
#### 拉电流
|Vcc(V)|Vio(V)|负载R(Ω)|I拉(mA)|
| ---- | ---- | ---- | ---- |
|5.11|5.08|10000|0.5|
|5.11|5.04|4700|1.1|
|5.11|4.97|2200|2.3|
|5.11|4.81|1000|4.8|
|5.11|4.53|510|8.9|
|5.11|2.61|100|26.1|
|5.11|1.44|50|28.8|
|5.11|0.31|10|31.0|

#### 灌电流
|Vcc(V)|Vio(V)|负载R(Ω)|I灌(mA)|
| ---- | ---- | ---- | ---- |
|5.11|0.01|10000|0.5|
|5.11|0.03|4700|1.1|
|5.11|0.07|2200|2.3|
|5.11|0.15|1000|5.0|
|5.11|0.30|510|9.4|
|5.11|1.69|100|34.2|
|5.11|3.17|50|38.8|
|5.11|4.64|10|47.0|

### 3、60mA驱动档位
#### 拉电流
|Vcc(V)|Vio(V)|负载R(Ω)|I拉(mA)|
| ---- | ---- | ---- | ---- |
|5.11|5.09|10000|0.5|
|5.11|5.08|4700|1.1|
|5.11|5.05|2200|2.3|
|5.11|4.98|1000|5.0|
|5.11|4.86|510|9.5|
|5.11|3.93|100|39.3|
|5.11|2.83|50|56.6|
|5.11|0.71|10|71.0|

#### 灌电流
|Vcc(V)|Vio(V)|负载R(Ω)|I灌(mA)|
| ---- | ---- | ---- | ---- |
|5.11|0.00|10000|0.5|
|5.11|0.00|4700|1.1|
|5.11|0.02|2200|2.3|
|5.11|0.06|1000|5.1|
|5.11|0.12|510|9.8|
|5.11|0.61|100|45.0|
|5.11|1.41|50|74.0|
|5.11|4.06|10|105.0|