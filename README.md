# _HAL-STM32F401-UART2-IT
This project uses STM32CubeIDE and it's a program created to practice my C habilities during the course 'Mastering Microcontroller: Timers, PWM, CAN, Low Power (MCU2)'. I am using a NUCLEO-F401RE board.

HAL FW version -> 1.27.1.

## Interrupt based API

The code goes from the previous code [_HAL-STM32F401-UART](https://github.com/Rafaelatff/_HAL-STM32F401-UART).

The IRQ handler is added into stm32f4xx_it.c file. In our case, that we are using the MX files, we don't need to change the headers (main.h has the stm32f4xx_hal.h already, and stm32f4xx_it.c already includes main.h).

![image](https://user-images.githubusercontent.com/58916022/210644444-f005c865-6c34-452f-9f9a-caec3abdf451.png)

The callback that will return from this part of the code is a __weak API. So we need to implement it at the main.c file.

Then, on main.c just call the 

```c
uint8_t user_data[] = "The application is running\r\n";
or
char user_data[] = "The application is running\r\n";
or
char *userData = "THE APPLICATION IS RUNING\r\n";

HAL_UART_Transmit_IT(&huart8, user_data, sizeof(user_data));
or
HAL_UART_Transmit_IT(&huart8, (uint8_t) user_data,(uint16_t) sizeof(user_data));
or
HAL_UART_Transmit_IT(&huart8, (uint8_t*) userData, strlen(userData));
```
