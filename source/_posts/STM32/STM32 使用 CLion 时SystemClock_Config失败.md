---
title: STM32 使用 CLion 时SystemClock_Config失败
date: 2023-11-15 13:42:48
id: 96
tags: STM32
categories: STM32
copyright: true
---

# STM32 使用 CLion 时SystemClock_Config失败

可能原因，来自[https://blog.t123yh.xyz:3/index.php/archives/922](https://blog.t123yh.xyz:3/index.php/archives/922)

## 故障分析

我们从 OpenOCD 的启动流程（`/usr/share/openocd/scripts/target/stm32f4x.cfg`）入手分析。首先，ARM Cortex 内核限制 SWD 时钟频率不能超过内核频率的 1/6，而单片机上电后如果不做任何配置，时钟来源为 HSI 的 16MHz，这样 SWD 频率就最高是 2.666MHz。因此，如果不做时钟上的更改，OpenOCD 默认只能使用 2000kHz 的 adapter speed：

```
# stm32f4x.cfg line 61-67
# JTAG speed should be <= F_CPU/6. F_CPU after reset is 16MHz, so use F_JTAG = 2MHz
#
# Since we may be running of an RC oscilator, we crank down the speed a
# bit more to be on the safe side. Perhaps superstition, but if are
# running off a crystal, we can run closer to the limit. Note
# that there can be a pretty wide band where things are more or less stable.
adapter speed 2000
```

而如果让 OpenOCD 启动调试，为了加快 SWD 时钟，OpenOCD 会把 MCU 里面的 PLL 打开并设置到 64MHz，然后将系统时钟来源切换为 PLL：

```c
# stm32f4x.cfg line 99-110
$_TARGETNAME configure -event reset-init {
    # Configure PLL to boost clock to HSI x 4 (64 MHz)
    mww 0x40023804 0x08012008   ;# RCC_PLLCFGR 16 Mhz /8 (M) * 128 (N) /4(P)
    mww 0x40023C00 0x00000102   ;# FLASH_ACR = PRFTBE | 2(Latency)
    mmw 0x40023800 0x01000000 0 ;# RCC_CR |= PLLON
    sleep 10                    ;# Wait for PLL to lock
    mmw 0x40023808 0x00001000 0 ;# RCC_CFGR |= RCC_CFGR_PPRE1_DIV2
    mmw 0x40023808 0x00000002 0 ;# RCC_CFGR |= RCC_CFGR_SW_PLL

    # Boost JTAG frequency
    adapter speed 8000
}
```

经过上面的配置，我们就可以将 adapter speed 设为最高 8000kHz，从而使调试更加丝滑顺畅。

那么，为什么会出现时钟不正常呢？我们分析 HAL 库设置系统时钟的流程。按照正常流程，HAL 库会启动 HSE，然后配置 PLL 参数，并将 PLL 时钟源设为 HSE，最后将系统时钟来源设置为 PLL。如果单片机上电后不做任何额外的时钟配置，这部分程序是可以正常工作的，单片机能够在正确的时钟上运行。

然而，巧合的是，启用调试器后，在运行用户代码前，系统时钟就处于已经配置 PLL 的状态。HAL 库如果检测到这种状态，则不会对 PLL 配置进行更改，只会检测现有的 PLL 配置与欲应用的配置是否相符，如果不相符，则返回 `HAL_ERROR`：

```c
__weak HAL_StatusTypeDef HAL_RCC_OscConfig(RCC_OscInitTypeDef  *RCC_OscInitStruct) {
  ... // 初始化 HSI，HSE 等，在此省略

  if ((RCC_OscInitStruct->PLL.PLLState) != RCC_PLL_NONE) {
    // 如果 PLL 当前不是系统时钟
    if (__HAL_RCC_GET_SYSCLK_SOURCE() != RCC_CFGR_SWS_PLL) {
      // 正常上电时，__HAL_RCC_GET_SYSCLK_SOURCE 应该返回 RCC_CFGR_SWS_HSI，从而进入这个分支
      ... // 对 PLL 进行配置
    } else {
      // 如果启动调试器，__HAL_RCC_GET_SYSCLK_SOURCE 应该返回 RCC_CFGR_SWS_PLL，就会进入这个分支
      // 对 PLL 配置进行检查，如果与给定的配置不符，就返回 ERROR
      pll_config = RCC->PLLCFGR;
      if((READ_BIT(pll_config, RCC_PLLCFGR_PLLSRC) != RCC_OscInitStruct->PLL.PLLSource) ||
         (READ_BIT(pll_config, RCC_PLLCFGR_PLLM) != RCC_OscInitStruct->PLL.PLLM) ||
         (READ_BIT(pll_config, RCC_PLLCFGR_PLLN) != RCC_OscInitStruct->PLL.PLLN) ||
         (READ_BIT(pll_config, RCC_PLLCFGR_PLLP) != RCC_OscInitStruct->PLL.PLLP) ||
         (READ_BIT(pll_config, RCC_PLLCFGR_PLLQ) != RCC_OscInitStruct->PLL.PLLQ))
      {
          return HAL_ERROR;
      }
    }
  }
}
```



## 解决方法

这个问题的解决方法很简单，在单片机进入 main 函数时，把系统时钟来源设为 HSI，就会让 HAL 库进入正确的分支。只需在 `HAL_Init` 函数之前增加两行代码即可解决问题：

```c
int main(void)
{
	__HAL_RCC_HSI_ENABLE();
  	__HAL_RCC_SYSCLK_CONFIG(RCC_SYSCLKSOURCE_HSI);

  	HAL_Init();
  	SystemClock_Config();
  	// 进行其他初始化
}
```

使用STM32CubeMX生成代码时可以写到USER CODE中

```c
int main(void)
{
    /* USER CODE BEGIN 1 */
    if (__HAL_RCC_GET_SYSCLK_SOURCE() == RCC_CFGR_SWS_PLL)
        HAL_RCC_DeInit();
    /* USER CODE END 1 */

  	HAL_Init();
  	SystemClock_Config();
  	// 进行其他初始化
}
```

