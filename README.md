# STM32F429I-DISC1 Blinky (CubeMX + CMSIS-RTOS2)

Small Keil uVision project generated from STM32CubeMX for the STM32F429I-DISC1 board. It exercises GPIO, EXTI, UART stdio retargeting, a CMSIS-RTOS2 thread, and a timer-based HAL timebase.

## What this project does
- Blinks LED0 in a CMSIS-RTOS2 thread and prints "Hello World" over UART1 at 115200 baud (stdio retargeted to USART1).
- Toggles LED1 on the blue user button (PA0) EXTI rising edge.
- Uses TIM8 as the HAL tick source so `HAL_Delay()` works reliably with the RTOS.

Key code lives in [STM32CubeMX/Target_1/STM32CubeMX/Src/main.c](STM32CubeMX/Target_1/STM32CubeMX/Src/main.c) with pin defines in [STM32CubeMX/Target_1/STM32CubeMX/Inc/main.h](STM32CubeMX/Target_1/STM32CubeMX/Inc/main.h). Stdio retargeting is provided by [retarget_stdio.c](retarget_stdio.c).

## Commit-driven change log
- Make empty project, add button, two leds and usart: initial CubeMX setup for LED0/LED1 on GPIOG, user button on PA0, USART1 enabled; Keil project files added.
- Toggle LED0 and LED1: basic GPIO control to verify hardware wiring.
- Retarget stdio to USART1: `printf`/`scanf` routed through CMSIS Driver USART1 at 115200 baud.
- Detect button press with EXTI interrupt and toggle LED1 in ISR: PA0 rising-edge EXTI callback flips LED1 state.
- Add RTOS and thread for toggling led0: CMSIS-RTOS2 initialized, `app_main` thread blinks LED0 and prints "Hello World" once per second using `HAL_Delay()`.
- Add Timer8 as Timebase: TIM8 drives `HAL_IncTick()` so delays are accurate alongside the RTOS scheduler.

## Hardware
- Board: STM32F429I-DISC1
- LEDs: LED0 (PG13), LED1 (PG14)
- User button: PA0 (EXTI0)
- UART: USART1, 115200-8-N-1 on CN3 (ST-Link VCOM)

## Building and running
1. Open `blinky-cubemx.uvprojx` in Keil uVision (MDK-ARM) on Windows.
2. Select the `Target_1` configuration.
3. Build and flash (Ctrl+F7 then F8). ST-Link on the DISC1 handles programming.
4. Open a serial terminal at 115200 baud to view "Hello World" prints and LED0 blink interval.
5. Press the blue button to toggle LED1 via EXTI.

## Regenerating from STM32CubeMX
- CubeMX metadata is under [STM32CubeMX/Target_1/STM32CubeMX](STM32CubeMX/Target_1/STM32CubeMX). The `.ioc` file captures pin and peripheral setup; regenerate code there, then reopen the Keil project if middleware settings change.

## Notes
- The project uses CMSIS-RTOS2 (Keil RTX5). The HAL timebase is overridden to TIM8; if you regenerate code, keep the timebase configuration to preserve `HAL_Delay()` behavior.
- Stdio uses the CMSIS USART driver. Ensure VCOM is enabled on the DISC1 and your terminal uses the matching COM port.
