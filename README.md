# _HAL-STM32F401-UART2-IT
This project uses STM32CubeIDE and it's a program created to practice my C habilities during the course 'Mastering Microcontroller: Timers, PWM, CAN, Low Power (MCU2)'. I am using a NUCLEO-F401RE board.

HAL FW version -> 1.27.1.

## Interrupt based API

The code goes from the previous code [_HAL-STM32F401-UART](https://github.com/Rafaelatff/_HAL-STM32F401-UART).

But, main code is basically:

```c
UART_Init(){
  huart2.Instance = UART2;
  huart2.Init.BaudRate = 9600;
  huart2.Init.WordLength = UART_WORDLENGTH_8B;
  huart2.Init.StopBits = UART_STOPBITS_1;
  huart2.Init.Parity = UART_PARITY_NONE;
  huart2.Init.Mode = UART_MODE_TX_RX;
  huart2.Init.HwFlowCtl = UART_HWCONTROL_NONE;
  huart2.Init.OverSampling = UART_OVERSAMPLING_16;
  huart2.Init.OneBitSampling = UART_ONE_BIT_SAMPLE_DISABLE;
  huart2.Init.ClockPrescaler = UART_PRESCALER_DIV1;

  if (HAL_UART_Init(&huart8) != HAL_OK)
  {
    Error_Handler();
  }
}
```

The IRQ handler is added into stm32f4xx_it.c file. In our case, that we are using the MX files, we don't need to change the headers (main.h has the stm32f4xx_hal.h already, and stm32f4xx_it.c already includes main.h).

![image](https://user-images.githubusercontent.com/58916022/210644444-f005c865-6c34-452f-9f9a-caec3abdf451.png)

The callback that will return from this part of the code is a __weak API. So we need to implement it at the main.c file if we desire to set a flag after transmittion (or reception) is finished.

## Calling the HAL_UART_Trasmit_IT

Then, on main.c or any other source file that may transmit data, just call the *HAL_UART_Trasmit_IT* function.

```c
char *userData = "ccc55";
// or I can use: char userData[] = "ccc55";

HAL_UART_Transmit_IT(&huart8,(uint8_t *) userData, (uint16_t) strlen(userData));
```
Note1: Insital HAL function, userData must be typecasted to a pointer type of uint8_t type. The same happens for the data size (strlen(userData)) but in this case is a uint16_t type.

Note2: strlen is used because it calculates and return a string size. The sizeof functions returns the size of a variable.

## Possible bugs

I was receiveing OOOff instead of aaa33, then NNNee instead of ccc55. The reason is because the hardware has a transistor to increse the logic level of the hw output (from 3.3V of uC logic level to 5V of the output). It is using a NPN transistor to do the logic, so hw is using inverted logic levels. To fix that, just add the following lines that will invert the logic level of the TX output.

```c
  huart2.AdvancedInit.AdvFeatureInit = UART_ADVFEATURE_TXINVERT_INIT;
  huart2.AdvancedInit.TxPinLevelInvert = UART_ADVFEATURE_TXINV_ENABLE;
```

## Results

![image](https://user-images.githubusercontent.com/58916022/229534770-71e0dd9c-5763-4a26-b500-47f5d5152dbc.png)

