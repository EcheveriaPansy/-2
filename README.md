# 电控组第二次培训作业
- **徐振轩**  
- ***20263003843***

## 基础任务
- 我首先通过cubeMX设置A4,A5,A6三个引脚，同时用user label分别命名为LED0,LED1,LED2，将代码产出到指定文件夹中，接着通过clion对main.c进行编辑高电平点亮的流水灯代码.  
  将面包板，最小系统板，stlink，线路以及LED连接好，通过clion将代码烧录进系统板中，从而实现高电平间隔500ms的流水灯效果.  

### 加分项
- SysTick+时间戳  
  实现原理：利用HAL_GetTick()函数。STM32 的 SysTick 定时器每 1ms 产生一次中断，HAL_GetTick() 返回从开机到现在经过的毫秒数。主循环不停地跑， 到了就换下一个灯，没到就继续循环干别的。这样 CPU 可以脱离死等程序。  
  > 核心代码片段：
~~~

{
  /* USER CODE BEGIN 0 */
#define STEP_DELAY 500

typedef enum {
  STATE_LED0,
  STATE_LED1,
  STATE_LED2
} LedState_t;

LedState_t ledState = STATE_LED0; 
uint32_t   lastTick = 0;
/* USER CODE END 0 */
}

~~~

- Timer Interrupt(定时器中断）  
  实现原理：CubeMX中配置一个定时器TIM2，使其配置为500ms溢出中断，在回调函数里直接切换 LED 。  
> 核心代码片段：  
~~~

{
  // USER CODE BEGIN 4
static void MX_TIM2_Init(void)
{
  __HAL_RCC_TIM2_CLK_ENABLE();
  htim2.Instance = TIM2;
  htim2.Init.Prescaler = 7200 - 1;     // 72MHz/7200 = 10kHz
  htim2.Init.Period = 5000 - 1;        // 5000 × 0.1ms = 500ms
  ... 
  HAL_NVIC_SetPriority(TIM2_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(TIM2_IRQn);
}

void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
  if (htim->Instance == TIM2) {
    ledStep = (ledStep + 1) % 3;
    switch (ledStep)
}

void TIM2_IRQHandler(void) { HAL_TIM_IRQHandler(&htim2); }  

~~~




