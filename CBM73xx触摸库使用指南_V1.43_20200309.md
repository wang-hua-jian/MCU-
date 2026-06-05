# CBM73XX 触摸驱动库开发手册(V1.43｜芯邦)

## 一、库文件组成
由 `S_touchkey.h(头文件)` + `S_touchkey.LIB(库文件)` 组成
1. **73C芯片**：V1.42之后版本，时钟初始化后需手动开启Flash读判；
2. **73D芯片**：库默认关闭Flash读判，无需配置；
3. **73F芯片**：库命名 `touchKeyLib_73F_xxxx`。

### 寄存器地址定义
```json
{
  "D版":{
    "KeyValueL":"XWORD[510]","KeyValueH":"XWORD[511]"
  },
  "F版":{
    "KeyValueL":"XWORD[766]","KeyValueH":"XWORD[767]"
  },
  "说明":"KeyValueH+KeyValueL组成32bit按键状态，bitX=1：X键按下；bitX=0：无按键，最大支持18键(KEY_NUM_MAX=18)"
}
```

## 二、核心API函数
### 1. `uint8 TouchKey_init_fun(uint8 SensorNum,uint8 *phySensor, uint8 *SensorSensitivity,uint16 flag)`
> 触摸初始化，上电电压稳定调用**仅一次**
- 参数
|参数|说明|
|----|----|
|SensorNum|触摸按键总数 ≤18|
|phySensor|物理IO编号数组指针|
|SensorSensitivity|灵敏度数组(0~48，数值越小越灵敏，默认12)|
|flag|配置位掩码|
#### flag位定义
|bit|功能|
|----|----|
|bit4|保留|
|bit5|1=开启快速响应，0=关闭|
|bit6|0=开启60s长按自校准(默认)，1=关闭|
|bit7|1=允许TouchKey_parm_set生效，0=参数配置无效|
|bit8~bit9|多键校准触发键数：0关闭/01=2键/02=3键/03=4键|
|bit10~bit12|多键校准等待：0=6s(默认)、1~6=Ns、7=0.5s|
|bit13~bit14|临键抑制：00关闭/01两键择优/02三键择优/03无效|
|bit15|保留|
- 返回：0=初始化成功，非0=失败
- 示例
```c
uint8 num=4;
uint8 phy[]={2,1,3,4};
uint8 sen[]={12,12,12,12};
TouchKey_init_fun(num,phy,sen,0x000B);
```

### 2. `void TouchKey_timer_fun(void)`
- 必须**5ms定时中断内调用**，触摸计时基准，缺则无法输出按键。

### 3. `void GetTouchKey_fun(void)`
- 主循环调用(循环周期<10ms)，单次耗时1~4ms；
- 自动20ms软件消抖，按键结果存入KeyValueH/L；bitX=1代表对应按键按下。

### 4. `void TouchKey_parm_set(uint8 ADCParm0,uint8 ADCParm1,uint8 LeveParm)`
> 需在TouchKey_init_fun之前调用，且初始化flag.bit7=1才生效
#### ADCParm0
- bit0~3：ADC精度：0=10bit，其余=16bit
- bit4~7：ADC分频：0/4分频、1/6、2/8、3/12、4/16，默认8分频
#### ADCParm1
- bit0~3：响应速度(0最慢~15最快，默认4)
- bit4~7：ADC电流(0最小~6偏大，默认4)
#### LeveParm
- bit0~3：滤波级数=(n+1)*2，0=2级、15=32级，默认8
- bit4~7：消抖=(n+1)*5ms，0=5ms、15=80ms，默认20ms
#### 行业推荐参数
|场景|ADCParm0|ADCParm1|LeveParm|
|----|----|----|
|默认|0x23|0x43|0x33|
|常规家电|0x23|0x44|0x89|
|快速按键|0x23|0x48|0x73|
|消费电子|0x20|0x46|0x80|

### 5. `uint16 GetKey_inf(uint8 Key,uint8 Mode)`
调试专用，获取单键内部参数
- Mode=0：ADC采样值
- Mode=1：环境基线
- Mode=2：电容偏移值

### 6 ESD防护接口(仅73D/73F可用)
```c
void CBM_ESDInit(void);        //main初始化调用
void CBM_ESDH_ISR1_fun(void);  //中断1内调用
void CBM_ESD0_ISR2_fun(void);  //中断2内调用
```

### 7 `void TouchSensor_init_Fun(void)`
环境一键自校准，环境稳定时调用。

## 三、标准工程使用步骤
1. 工程添加 `S_touchkey.LIB`，头文件引入 `#include "S_touchkey.h"`
2. 系统时钟初始化，**C版开启FMULRDEN(Flash使能)**
3. 可选：`TouchKey_parm_set()`配置参数
4. IO寄存器配置为触摸通道
5. `TouchKey_init_fun()`初始化触摸
6. 5ms定时器中断：`TouchKey_timer_fun()`
7. 主循环轮询：`GetTouchKey_fun()`读取按键

## 四、补充说明
1. 消抖默认20ms，可通过LeveParm修改；
2. 临键抑制：多键同时按下只返回灵敏度最高单键；
3. 长按自校准默认60s，多键校准默认6s。