---
title: "OEO的一些电路和控制"
subtitle: "Read Notes - 欢庆的笔记"
layout: post
date:   2020-12-23
author: "Huanqing"
header-style: text
hidden: true
mathjax: true
catalog: true
tags:
  - 笔记
---

# OEO电路和程序

## 1. 锁相控制

### 硬件

#### 混频

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/87B406E8-D5A7-4ada-8A76-3E90067296D0.png" alt="87B406E8-D5A7-4ada-8A76-3E90067296D0" style="zoom:50%;" />

混频输出经过 **R3** **C3** 组成的16Hz低通滤波后，由 **R2** **R6** 增加偏压，再经过运放 **U2** 放大调理后送至 MCU 内部ADC。

---

#### 锁定信号检测

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/A0671030-8EB3-4218-B1EF-1469281E0589.png" alt="A0671030-8EB3-4218-B1EF-1469281E0589" style="zoom: 45%;" />

混频输出信号经运放 **U14** 放大成 5Vpp 的方波，再经过一系列整流滤波后，转换成高低电平信号，送至 MCU 检测当前状态。

---

#### 射频功率检测

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/683AA5C6-83FB-488d-B124-219350B33794.png" alt="683AA5C6-83FB-488d-B124-219350B33794" style="zoom:50%;" />

射频功率检测电路用于调整调制器偏压，采用 [LTC5532](https://www.analog.com/cn/products/ltc5532.html) 将射频电压峰值检波并输出给 MCU ，

---

#### 移相、偏压等

调制器的偏压，模拟移相器的电压由8通道12bit DAC [AD5328](https://www.analog.com/cn/products/ad5328.html) 提供

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/A1652813-198F-42b8-9257-DB31A6FFA672.png" alt="A1652813-198F-42b8-9257-DB31A6FFA672" style="zoom:50%;" />

DAC输出先经过一级滤波输入到低噪声运放，再经运放放大滤波后输出。

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/B852C97F-85E3-4fa8-9244-1F853D0736E0.png" alt="B852C97F-85E3-4fa8-9244-1F853D0736E0" style="zoom:50%;" />

---

### 2. 软件

#### 总体结构

代码总体参考了Arduino的结构，分为硬件初始化和软件初始化。硬件初始化在MCU上电时执行，主要初始化片内外设；软件初始化在失锁时复位执行，内容有扫描调制器偏压，扫描锁定点，调整注入信号相位等。

```
int main(void)
{
  /* USER CODE BEGIN 1 */

  /* USER CODE END 1 */

  /* MCU Configuration--------------------------------------------------------*/

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  /* USER CODE BEGIN Init */

  /* USER CODE END Init */

  /* Configure the system clock */
  SystemClock_Config();

  /* USER CODE BEGIN SysInit */

  /* USER CODE END SysInit */

  /* Initialize all configured peripherals */
  MX_GPIO_Init();
  MX_DMA_Init();
  MX_ADC1_Init();
  MX_DAC1_Init();
  MX_USART1_UART_Init();
  MX_USART4_UART_Init();
  MX_TIM6_Init();

  /* Initialize interrupts */
  MX_NVIC_Init();
  /* USER CODE BEGIN 2 */
	
	HAL_ADC_Stop_IT(&hadc1);
	HAL_ADCEx_Calibration_Start(&hadc1);    //ADC校准
	//HAL_ADC_Start_IT(&hadc1);
	
	__HAL_UART_ENABLE_IT(&huart1, UART_IT_IDLE);           //@ 开启空闲中断
	HAL_UART_Receive_DMA(&huart1, U1Receiver.buffer, UART_RXBUF_SIZE);  //@ 开启串口 DMA 接收
	
	HAL_DAC_SetValue(&hdac1, DAC_CHANNEL_1, DAC_ALIGN_12B_R, 0);
	HAL_DAC_SetValue(&hdac1, DAC_CHANNEL_2, DAC_ALIGN_12B_R, 0);
	HAL_DAC_Start(&hdac1, DAC_CHANNEL_1);
	HAL_DAC_Start(&hdac1, DAC_CHANNEL_2);
	
	//开启AD转换
	HAL_ADC_Start_DMA(&hadc1, ADC_ValueBuffer, ChanelNum);
	//--------启动定时器(1ms中断)---------
	HAL_TIM_Base_Start_IT(&htim6);

	float feedback;  //锁相用的反馈电压
	
	HAL_Delay(15000);
  /* USER CODE END 2 */

  /* Infinite loop */
  /* USER CODE BEGIN WHILE */
  while (1)
  {
		SetUp();//初始化
		
		//1.两种模式锁相控制
		feedback = Get_RF_Fix();
		if(fabs(feedback - LockPointVoltage) > 30) //±30mV以外调节
		{
			if(ControlMode == 1)      //正相关
			{
				if((feedback - LockPointVoltage) > 0) //反馈电压太大了
					insidePhaseShiftValue--;
				else
					insidePhaseShiftValue++;
				SetDAC_PhaseShifter1(insidePhaseShiftValue);
				SetDAC_PhaseShifter2(insidePhaseShiftValue);
			}
		
			if(ControlMode ==-1)  //负相关
			{
				if((feedback - LockPointVoltage) > 0) //反馈电压太大了
					insidePhaseShiftValue++;
				else
					insidePhaseShiftValue--;
				SetDAC_PhaseShifter1(insidePhaseShiftValue);
				SetDAC_PhaseShifter2(insidePhaseShiftValue);
			}
		}
		
		//2.失锁信号检查
		static uint64_t CheckTime=0;
		if(timesMs - CheckTime > 1000) //间隔1S
		{
			if(HAL_GPIO_ReadPin(Lost_GPIO_Port, Lost_Pin) == GPIO_PIN_RESET ) //低电平，有交流，失锁了
				LostNum++;
			else
				LostNum=0;
			
			if(LostNum>5) //连续5次，5秒都失锁
			{
				LostNum=0;
				SetUp_flag = 1;   //软件复位重新扫描
			}
			CheckTime = timesMs;
		}

		HAL_Delay(20);
		
    /* USER CODE END WHILE */

    /* USER CODE BEGIN 3 */
  }
  /* USER CODE END 3 */
}
```

---

#### 软件初始化

软件初始化程序在非锁定状态时会触发执行，步骤：

```flow
st=>start: 开始框
op1=>operation: 扫描最大功率点
op2=>operation: 扫描锁定点
op3=>operation: 设置环外移相
cond=>condition: 检查混频
sub1=>subroutine: 子流程
io=>inputoutput: 输入输出框
e=>end: 退出
op1(right)->op2(right)->cond
cond(yes)->e
cond(no)->op3(right)->op2
```

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/80CF9DDE-6AE8-41da-B5E7-AD49C3E7BEE0.png" alt="80CF9DDE-6AE8-41da-B5E7-AD49C3E7BEE0" style="zoom: 67%;" />


```
void SetUp(void) 
{
	if(SetUp_flag)
	{
		HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_SET);   //锁定灯熄灭
		HAL_GPIO_WritePin(AMP_EN_GPIO_Port, AMP_EN_Pin, GPIO_PIN_SET); //打开放大器
		//1.扫描最大功率，设置调制器偏压
		ScanModulator();
		
		
		//2.扫描起振点
		outsidePhaseShiftDegree = 20;  //环外移相初始值
		uint16_t outsidePhaseShiftValue;
		outsidePhaseSetUp = 1;
		while(outsidePhaseSetUp)
		{
			outsidePhaseShiftDegree += 20;  //初始40度，步进20度调环外
			if(outsidePhaseShiftDegree>180)
				outsidePhaseShiftDegree = 40;
			outsidePhaseShiftValue = (uint16_t)((0.2498f*(outsidePhaseShiftDegree*outsidePhaseShiftDegree)+8.7918f*outsidePhaseShiftDegree)/11000.0f*4095);
			AD5328_WriteDAC(3, outsidePhaseShiftValue);
			HAL_Delay(100);
			ScanLockPointNew();
			HAL_Delay(100);
		}
		//3.开关放大器
		HAL_GPIO_WritePin(AMP_EN_GPIO_Port, AMP_EN_Pin, GPIO_PIN_RESET);
		HAL_Delay(200);
		HAL_GPIO_WritePin(AMP_EN_GPIO_Port, AMP_EN_Pin, GPIO_PIN_SET);
		
		HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, GPIO_PIN_RESET);   //锁定灯点亮
		SetUp_flag=0;
	}
}
```

---

#### 锁相策略

1.混频输出电压在整个移相范围内会从0V左右先增大后减小再到0V，或先减小后增大，所以有两个相反的控制逻辑。

2.由于移相器的非线性，锁定点会控制在中间段落。

3.移相器非线性补偿

```
outsidePhaseShiftValue = (uint16_t)((0.2498f*(outsidePhaseShiftDegree*outsidePhaseShiftDegree)+8.7918f*outsidePhaseShiftDegree)/11000.0f*4095);
```

4.扫描锁定点时，检测到电压尖峰到来就计次，覆盖整个锁定区域后就退出，代码：

```
void ScanLockPointNew(void)
{
	uint16_t breakFlag=0; //跳出循环标志
	float Voltage_max = -100.0f, Voltage_min = 100.0f;    //最大和最小电压，计算锁定中点时需要用到
	float Vfb;
	uint16_t max_DACvalue=0,min_DACvalue=0; //最大和最小电压对应的DAC值
	SetDAC_PhaseShifter1(800);
	SetDAC_PhaseShifter2(800);
	HAL_Delay(100);
	
	//扫描循环体
	for(uint16_t DACvalue=800; DACvalue<4096; DACvalue++)
	{
		SetDAC_PhaseShifter1(DACvalue);
		SetDAC_PhaseShifter2(DACvalue);
		HAL_Delay(20);
		Vfb = Get_RF_Fix();
		if(Vfb > Voltage_max)
			{Voltage_max = Vfb; max_DACvalue = DACvalue;} //保存最大值
		if(Vfb < Voltage_min)
			{Voltage_min = Vfb; min_DACvalue = DACvalue;} //保存最小值
			
			/**********串口波形发送***********/
		uint8_t Send_Count;
		DataScope_Get_Channel_Data( Vfb, 1 );           //将数据写入缓存1	
		DataScope_Get_Channel_Data( HAL_GPIO_ReadPin(Lost_GPIO_Port, Lost_Pin)*1000.0f, 2 );           //将数据写入缓存2	
		Send_Count = DataScope_Data_Generate(2);                 //创建数据包格式，该函数会返回发送的字节数
		HAL_UART_Transmit(&huart4, DataScope_OutPut_Buffer, Send_Count, 0xFFFF);//串口发送
			
		//if(DACvalue>600)//防止启动时误识别
		if((Voltage_max>200)||(Voltage_min<-200)) //准备跳出循环
		{
			breakFlag++;
		}
		if(breakFlag>600) //大于锁定区间宽度, 跳出循环
			break;
			
	}
	//==========================检查环外移相是否合适=========================================
	if((Voltage_max>200) && (Voltage_min <-200))
		outsidePhaseSetUp = 0;  //合适
	else 
		outsidePhaseSetUp = 1;  //不合适，调环外标志
	
		LockPointVoltage = (Voltage_max + Voltage_min)/2; //计算锁相点电压
		if(max_DACvalue < min_DACvalue) // 最大点在前，负相关
			ControlMode = -1;
		else                            //最大点在后，正相关
			ControlMode = 1;
	
	if(insidePhaseShiftValue > (max_DACvalue/2+min_DACvalue/2)) //现在的值比设定值大
	{
		for( uint16_t value=insidePhaseShiftValue; value>(max_DACvalue/2+min_DACvalue/2); value--)
		{
			SetDAC_PhaseShifter1(value);
			SetDAC_PhaseShifter2(value);
		}
	}
	else //现在的值比设定值小
	{
		for( uint16_t value=insidePhaseShiftValue; value<(max_DACvalue/2+min_DACvalue/2); value++)
		{
			SetDAC_PhaseShifter1(value);
			SetDAC_PhaseShifter2(value);
		}
	}
}
```



---

#### 底层驱动

##### 软件SPI

软件模拟SPI，将需要用到的IO定义成结构体，支持任意引脚交叉复用SPI，甚至同一引脚可以既当时钟又当数据。

```
//软件SPI操作的GPIO结构体定义
typedef struct
{
	GPIO_TypeDef* Port; 
	uint16_t Pin;          
}GPIO_RW_TypeDef;

typedef struct
{
   GPIO_RW_TypeDef* MOSI;
	 GPIO_RW_TypeDef* MISO;
	 GPIO_RW_TypeDef* SCK;
}SoftSPI_TypeDef;
```

以SPI模式0为例：

```
uint8_t SoftSPI_MODE0_RW(SoftSPI_TypeDef *SSPI, uint8_t WriteByte) 
{
	uint8_t i,ReadByte=0; 
	for(i=0;i<8;i++)   // 循环8次 
	{
		//******写MOSI脚******//
		if(WriteByte&0x80) 
			HAL_GPIO_WritePin(SSPI->MOSI->Port, SSPI->MOSI->Pin, GPIO_PIN_SET);
		else       
			HAL_GPIO_WritePin(SSPI->MOSI->Port, SSPI->MOSI->Pin, GPIO_PIN_RESET);
		WriteByte <<= 1;
		
		//******延时半个时钟******//
		clk_delay(); 
		
		//******拉高时钟，在上升沿时读写数据******//
		HAL_GPIO_WritePin(SSPI->SCK->Port, SSPI->SCK->Pin, GPIO_PIN_SET);
		
		//******读MISO脚数据******//
		ReadByte <<= 1;                                           //先把数据左移，准备存储 
		if(HAL_GPIO_ReadPin(SSPI->MISO->Port, SSPI->MISO->Pin))   //读MISO脚
			ReadByte |= 0X01;                                       //存储数据
		
		//******延时半个时钟******//
		clk_delay();
		
		//******拉低时钟，结束一个时钟周期******//
		HAL_GPIO_WritePin(SSPI->SCK->Port, SSPI->SCK->Pin, GPIO_PIN_RESET);
	} 
	return (ReadByte);     //返回数据 
}
```

##### DAC

DAC基于上面的软件SPI，只需初始化再写数据即可。

```
void AD5328_Init(void)
{
	CS_DAC_Low;
	SOFT_SPI_MODE1_RW_2Bytes(0x800C);
	SOFT_SPI_MODE1_RW_2Bytes(0xA000);
	SOFT_SPI_MODE1_RW_2Bytes(0xC000);
	CS_DAC_High;
}

void AD5328_WriteDAC(uint8_t ch , uint16_t DAC_Value)
{
	CS_DAC_Low;
	uint16_t WriteData;
	WriteData = (ch<<12) | DAC_Value;
	SOFT_SPI_MODE1_RW_2Bytes(WriteData);
	CS_DAC_High;
}
```

##### 串口波形发送

扫描锁定点时发送当前的混频电压到数据波形显示上位机，调试时非常重要。该代码将数据转换成上位机可以识别的数据帧。

```
/********************************************************************************
1.写入数据到通道
2.转换格式
3.要发送的数据
DataScope_Get_Channel_Data( rand()/100000000 , 1 );
Send_Count = DataScope_Data_Generate(1); //该函数会返回发送的字节数
DataScope_OutPut_Buffer[i]; 
*********************************************************************************/

unsigned char DataScope_OutPut_Buffer[42] = {0};	   //串口发送缓冲区

//函数说明：将单精度浮点数据转成4字节数据并存入指定地址 
//附加说明：用户无需直接操作此函数 
//target:目标单精度数据
//buf:待写入数组
//beg:指定从数组第几个元素开始写入
//函数无返回 
void Float2Byte(float *target,unsigned char *buf,unsigned char beg)
{
    unsigned char *point;
    point = (unsigned char*)target;	  //得到float的地址
    buf[beg]   = point[0];
    buf[beg+1] = point[1];
    buf[beg+2] = point[2];
    buf[beg+3] = point[3];
}
 
//函数说明：将待发送通道的单精度浮点数据写入发送缓冲区
//Data：通道数据
//Channel：选择通道（1-10）
//函数无返回 
void DataScope_Get_Channel_Data(float Data,unsigned char Channel)
{
	if ( (Channel > 10) || (Channel == 0) ) return;  //通道个数大于10或等于0，直接跳出，不执行函数
  else
  {
     switch (Channel)
		{
      case 1:  Float2Byte(&Data,DataScope_OutPut_Buffer,1); break;
      case 2:  Float2Byte(&Data,DataScope_OutPut_Buffer,5); break;
		  case 3:  Float2Byte(&Data,DataScope_OutPut_Buffer,9); break;
		  case 4:  Float2Byte(&Data,DataScope_OutPut_Buffer,13); break;
		  case 5:  Float2Byte(&Data,DataScope_OutPut_Buffer,17); break;
		  case 6:  Float2Byte(&Data,DataScope_OutPut_Buffer,21); break;
		  case 7:  Float2Byte(&Data,DataScope_OutPut_Buffer,25); break;
		  case 8:  Float2Byte(&Data,DataScope_OutPut_Buffer,29); break;
		  case 9:  Float2Byte(&Data,DataScope_OutPut_Buffer,33); break;
		  case 10: Float2Byte(&Data,DataScope_OutPut_Buffer,37); break;
		}
  }
}

//函数说明：生成 DataScopeV1.0 能正确识别的帧格式
//Channel_Number，需要发送的通道个数
//返回发送缓冲区数据个数
//返回0表示帧格式生成失败 
unsigned char DataScope_Data_Generate(unsigned char Channel_Number)
{
	if ( (Channel_Number > 10) || (Channel_Number == 0) ) { return 0; }  //通道个数大于10或等于0，直接跳出，不执行函数
  else
  {	
	 DataScope_OutPut_Buffer[0] = '$';  //帧头
		
	 switch(Channel_Number)   
   { 
		 case 1:   DataScope_OutPut_Buffer[5]  =  5; return  6; break;   
		 case 2:   DataScope_OutPut_Buffer[9]  =  9; return 10; break;
		 case 3:   DataScope_OutPut_Buffer[13] = 13; return 14; break;
		 case 4:   DataScope_OutPut_Buffer[17] = 17; return 18; break;
		 case 5:   DataScope_OutPut_Buffer[21] = 21; return 22; break; 
		 case 6:   DataScope_OutPut_Buffer[25] = 25; return 26; break;
		 case 7:   DataScope_OutPut_Buffer[29] = 29; return 30; break;
		 case 8:   DataScope_OutPut_Buffer[33] = 33; return 34; break;
		 case 9:   DataScope_OutPut_Buffer[37] = 37; return 38; break;
     case 10:  DataScope_OutPut_Buffer[41] = 41; return 42; break;
   }	 
  }
	return 0;
}
```



## 2. 温控



## 3. 电源

