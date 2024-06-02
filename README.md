__Introduction:-__ 

The LPG is an essential need of every household, its leakage could lead to a disaster. To alert on LPG leakage and prevent any mishappening there are various products to detect the leakage. Here we have developed an LPG gas detector alarm. If gas leakage occurs, this system detects it and makes an alert by buzing the buzzer attached with the circuit. This system is easy to build and anyone who have some knowledge of electronics and programing, can build it..The  prime  aim of  paper  is  to  detect Gas  leakage  in home,  hotels,  schools  and other  domestic  areas,  and gives  alert  message  to  the  surrounding  people. Nowadays Gas sensors are  being used globally  in  the field  like  safety,  health,  instrumentation  etc.  This paper is  an implementation of  the same  using gas  sensor.This module is very easy to interface with microcontrollers  and easily available in market by name “LPG Gas Sensor Module”  

__Methodology:-__  

Sensor Technologies is a potential future route for the creation of a gas leakage detecting system. By using cloud technology to store sensor data for later use and incorporating a GPS module to accurately pinpoint gas leaks, the suggested system offers a better strategy that outperforms earlier techniques. To find smoke leaks, the system may also have a smoke detection sensor.We have used a LPG gas sensor module to detect LPG Gas. When LPG gas leakage occurs, it gives a HIGH pulse on its DO pin and arduino continuously reads its DO pin. When VSD squardron gets a HIGH pulse from LPG Gas module it shows “LPG Gas Leakage Alert” message on 16x2 LCD and activates buzzer which beeps again and again until the gas detector module doesn't sense the gas in environment. When LPG gas detector module gives LOW pulse to VSD squardron, then LCD shows “No LPG Gas Leakage” message.LCD as a substitute componet can be used for LED for better results.  

__Components:-__  

1.VSD squadron mini.  
2.LPG gas sensor module.  
3.Buzzer.   
5. LED.  
6.1K resistor.  
7.Bread board.  
8. 9V battery.  
9.Connecting wires.
  

__Circuit Diagram:-__  
![NEW CIRCUIT](https://github.com/SIDDAPURMATH/Mavericks/assets/171076803/73ba4401-c156-42e1-a3cc-b416028e5212)  


__Table for Pin Connection:-__  
![KRISHANA](https://github.com/SIDDAPURMATH/Mavericks/assets/171076803/67e12648-d28c-4877-82fe-1f5fa61423cd)  

__Code Required:-__  
#include <ch32v00x.h>
#include <debug.h>

#define LED_GPIO_PORT GPIOD
#define LED_GPIO_PIN GPIO_Pin_4

#define LED2_GPIO_PORT GPIOD
#define LED2_GPIO_PIN GPIO_Pin_6


#define BUZZER_GPIO_PORT GPIOD
#define BUZZER_GPIO_PIN GPIO_Pin_3
#define GAS_SENSOR_GPIO_PIN GPIO_Pin_2 // LPG gas sensor output connected to GPIO Pin 2


// #define BLINKY_GPIO_PORT GPIOD
// #define BLINKY_GPIO_PIN GPIO_Pin_6
// #define BLINKY_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

#define LED2_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define LED_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define BUZZER_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)
#define GAS_SENSOR_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

// Function prototypes
//(void) _attribute_((interrupt("WCH-Interrupt-fast")));
//void HardFault_Handler(void) _attribute_((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);

// Main function
int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2); // Configure NVIC priority grouping
    SystemCoreClockUpdate(); // Update system core clock
    Delay_Init(); // Initialize delay function

    // GPIO configuration structure
    GPIO_InitTypeDef GPIO_InitStructure = {0};

    // Enable clock for GPIO port
    LED_CLOCK_ENABLE;
	LED2_CLOCK_ENABLE;
    BUZZER_CLOCK_ENABLE;
    GAS_SENSOR_CLOCK_ENABLE;


	// BLINKY_CLOCK_ENABLE;
	// GPIO_InitStructure.GPIO_Pin = BLINKY_GPIO_PIN;
	// GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	// GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	// GPIO_Init(BLINKY_GPIO_PORT, &GPIO_InitStructure);

    // Configure GPIO pin for LED
    GPIO_InitStructure.GPIO_Pin = LED_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Output push-pull mode
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // GPIO speed
    GPIO_Init(LED_GPIO_PORT, &GPIO_InitStructure);

    // Configure GPIO pin for LED2
    GPIO_InitStructure.GPIO_Pin = LED2_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Output push-pull mode
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // GPIO speed
    GPIO_Init(LED2_GPIO_PORT, &GPIO_InitStructure);

	
	// Configure GPIO pin for buzzer
    GPIO_InitStructure.GPIO_Pin = BUZZER_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Output push-pull mode
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // GPIO speed
    GPIO_Init(BUZZER_GPIO_PORT, &GPIO_InitStructure);

    // Configure GPIO pin for gas sensor input
    GPIO_InitStructure.GPIO_Pin = GAS_SENSOR_GPIO_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPU; // Input mode with pull-up resistor
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    // Initialize UART for debugging
    USART_Printf_Init(115200); // Initialize UART with baud rate 115200
    printf("System Initialized\n");

    // Main loop
	//uint8_t ledState = 0;
    while (1)

    {	
		// GPIO_WriteBit(BLINKY_GPIO_PORT, BLINKY_GPIO_PIN, ledState);
		// ledState ^= 1; // invert for the next run
        // Read gas sensor status
        uint8_t gasStatus = !GPIO_ReadInputDataBit(GPIOD, GAS_SENSOR_GPIO_PIN);
        printf("Gas Sensor Status: %d\n", gasStatus);

        // Control the buzzer and LED based on gas sensor output
        if (gasStatus == 1) // Gas sensor detected gas leak
        {
            GPIO_WriteBit(BUZZER_GPIO_PORT, BUZZER_GPIO_PIN, SET); // Turn on buzzer
            GPIO_WriteBit(LED_GPIO_PORT, LED_GPIO_PIN, SET); // Turn on LED
			GPIO_WriteBit(LED2_GPIO_PORT, LED2_GPIO_PIN, RESET); // Turn off LED
            printf("Gas Detected! Buzzer ON, LED ON\n");
        }
        else
        {
            GPIO_WriteBit(BUZZER_GPIO_PORT, BUZZER_GPIO_PIN, RESET); // Turn off buzzer
            GPIO_WriteBit(LED_GPIO_PORT, LED_GPIO_PIN, RESET); // Turn off LED
            GPIO_WriteBit(LED2_GPIO_PORT, LED2_GPIO_PIN, SET); // Turn on LED
			printf("No Gas. Buzzer OFF, LED OFF\n");
        }

        Delay_Ms(250); // Delay for debouncing and visualization
    }
}

// // Non-Maskable Interrupt handler
// void NMI_Handler(void) {}

// // Hard Fault handler
// void HardFault_Handler(void)
// {
//     while (1) {}
// }

// Delay initialization function
// void Delay_Init(void)
// {
//     SysTick_Config(SystemCoreClock / 1000); // Configure SysTick for 1ms ticks
// }

// // Millisecond delay function
// void Delay_Ms(uint32_t n)
// {
//     while (n--)
//     {
//         while (!(SysTick->CTRL & SysTick_CTRL_COUNTFLAG_Msk)) {} // Wait for SysTick flag
//     }
// }

