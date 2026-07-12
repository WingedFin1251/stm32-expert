# Peripheral Common HAL Reference

## GPIO

| Function | HAL Call | Notes |
|----------|----------|-------|
| Init | `HAL_GPIO_Init()` | Mode, Pull, Speed, Alternate |
| Write | `HAL_GPIO_WritePin()` | SET/RESET |
| Toggle | `HAL_GPIO_TogglePin()` | |
| Read | `HAL_GPIO_ReadPin()` | Returns GPIO_PIN_SET or RESET |
| Interrupt | `HAL_GPIO_EXTI_Callback()` | Override in user code |

### GPIO Modes

- `GPIO_MODE_INPUT` — Floating, pull-up, or pull-down
- `GPIO_MODE_OUTPUT_PP` — Push-pull output
- `GPIO_MODE_OUTPUT_OD` — Open-drain output (I2C)
- `GPIO_MODE_AF_PP` — Alternate function push-pull (UART TX, SPI SCK)
- `GPIO_MODE_AF_OD` — Alternate function open-drain (I2C SCL/SDA)
- `GPIO_MODE_ANALOG` — Analog mode (ADC input)

## UART

| Parameter | Typical | Notes |
|-----------|---------|-------|
| Baud Rate | 9600-921600 | Formula: Clock/(16×USARTDIV) |
| Word Length | 8 or 9 bits | `UART_WORDLENGTH_8B` / `9B` |
| Stop Bits | 1 or 2 | `UART_STOPBITS_1` / `2` |
| Parity | None/Even/Odd | `UART_PARITY_NONE` / `EVEN` / `ODD` |
| Hardware Flow | CTS/RTS | `UART_HWCONTROL_CTS` / `RTS` |

## SPI

| Parameter | Options | Notes |
|-----------|---------|-------|
| Mode | Master / Slave | `SPI_MODE_MASTER` / `SLAVE` |
| CPOL | Low / High | Clock polarity (0=idle low) |
| CPHA | 1 Edge / 2 Edge | Clock phase |
| Data Size | 8 or 16 bit | `SPI_DATASIZE_8BIT` / `16BIT` |
| First Bit | MSB / LSB | `SPI_FIRSTBIT_MSB` / `LSB` |

## I2C

- Timing register: `I2C_TIMINGR` = (SCLL + SCLL) × I2C clock period
- 7-bit addressing default; 10-bit requires `I2C_ADDRESSINGMODE_10BIT`

## Common HAL Init Sequence

```
1. __HAL_RCC_GPIOx_CLK_ENABLE()      — GPIO port clock
2. __HAL_RCC_USARTx_CLK_ENABLE()     — Peripheral clock
3. HAL_GPIO_Init()                    — Configure TX/RX pins with AF
4. HAL_UART_Init()                    — Initialize UART
5. HAL_UART_Receive_IT() / _DMA()    — Start interrupt/DMA receive
```

## Common Mistakes

- Forgetting `__HAL_RCC_xxx_CLK_ENABLE()` — peripheral silent
- Wrong GPIO Alternate Function number — no communication
- SPI master/slave CPOL/CPHA mismatch — garbage data
- I2C pins not set to open-drain (GPIO_MODE_AF_OD) — bus lock
- Not calling `HAL_UART_Receive_IT()` to start interrupt reception
