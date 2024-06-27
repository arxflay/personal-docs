# GPIO STM32WB55

This section contain's all important informations about GPIO that i learned so far

## GPIO port Address mapping

* GPIO A - *0x48000000*
* GPIO B - *0x48000400*
* GPIO C - *0x48000800*
* GPIO D - *0x48000C00*
* GPIO H - *0x48001C00*

## GPIO bit-banding

Unforutunately, bit-banding **is supported only for AHB1 and APBx** as stated in 
[source](https://community.st.com/t5/stm32-mcus-products/bit-banding-ahb2-in-stm32l4-is-it-possible/td-p/483299).
GPIO ports are part of AHB2 memory region (0x48000000) which doesn't fit
in bit-banding memory alias for peripherals on address space is on 0x42000000. 
I think this change is made because using bit-banding on GPIO usually doesn't give speed benefits 
as stated in many books. Bit-banding is replaced with BSRR and BRR registers.

## GPIO accessing ports and registers

Each GPIO section has 11 registers, each register has size of 4 bytes and each bit of register 
corresponds to specific port (expect some registers, where 2 or 4 bits are used for single port)

| offset | register name | description |
|:------:|:-------------:|:-----------:|
| 0x0  | MODER   | Specifies mode of GPIO port. 2bit per port |
| 0x4  | OTYPER  | Specifies type of output (push-pull or open-drain) |
| 0x8  | OSPEEDR | Specifies output speed time that affects switch from low state to high state |
| 0x0B | PUPDR   | Specifies if pull-up or pull-down resistors should be used. 2bit per port |
| 0x10 | IDR     | Used for reading value from port (only for input ports) |
| 0x14 | ODR     | Used for setting output value - low, high (only for output ports) |
| 0x18 | BSRR    | Used for atomic reset and set port (only for write). Very fast. </br> First 16 bits are used set, last 16 bits are used for reset, zero's are ignored |
| 0x1B | LCKK    | Used for mode locking. After locking port can't be reset until restart |
| 0x20 | AFRL    | Used for setting port "special" function, like UART (if MODER is equal to 0x10) </br> 4 bits per port are used |
| 0x24 | AFRH    | Same as AFRL but for next 8 ports (because all ports could not fit in AFRL)
| 0x28 | BRR     | Similar to BSRR but used only for port reset |

More about registers on page 300 in
[rm0434](https://www.st.com/resource/en/reference_manual/rm0434-multiprotocol-wireless-32bit-mcu-armbased-cortexm4-with-fpu-bluetooth-lowenergy-and-802154-radio-solution-stmicroelectronics.pdf)

## Initialization

