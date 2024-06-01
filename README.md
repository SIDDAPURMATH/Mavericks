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
![THIS IS FINAL](https://github.com/SIDDAPURMATH/Mavericks/assets/171076803/e12ee5df-f34d-4a04-b6ff-d3e1e5104721)   

__Table for Pin Connection:-__  
![AJAY](https://github.com/SIDDAPURMATH/Mavericks/assets/164477694/b20d01f4-440e-4f76-81ea-e905f2bef259)  
__Code Required:-__  
#include <ch32v00x.h> // Include the library for CH32V00x series microcontroller
#include <debug.h>    // Include debugging library

// Define GPIO pins and ports for LED and Gas Sensor
#define GAS_SENSOR_PIN  GPIO_Pin_2  // Analog pin connected to the gas sensor output
#define LED_PIN         GPIO_Pin_0  // LED pin
#define LED_GPIO_PORT   GPIOD       // LED GPIO port
#define SENSOR_GPIO_PORT GPIOD      // Sensor GPIO port

// Function prototypes
void Delay_Init(void); // Initializes the delay function
void Delay_Ms(uint32_t n); // Delay function for n milliseconds

// Function to configure ADC (Analog to Digital Converter)
void configureADC() {
    ADC_InitTypeDef ADC_InitStructure; // Structure to configure ADC parameters

    // Enable ADC1 peripheral clock
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_ADC1, ENABLE);

    // Reset ADC1 to initial state
    ADC_DeInit(ADC1);

    // Configure ADC1
    ADC_InitStructure.ADC_Mode = ADC_Mode_Independent; // Independent ADC mode
    ADC_InitStructure.ADC_ScanConvMode = DISABLE; // Single channel conversion
    ADC_InitStructure.ADC_ContinuousConvMode = ENABLE; // Continuous conversion mode
    ADC_InitStructure.ADC_ExternalTrigConv = ADC_ExternalTrigConv_None; // No external trigger
    ADC_InitStructure.ADC_DataAlign = ADC_DataAlign_Right; // Right-aligned data
    ADC_InitStructure.ADC_NbrOfChannel = 1; // Single channel conversion
    ADC_Init(ADC1, &ADC_InitStructure);

    // Configure the regular channel to be converted by ADC1
    ADC_RegularChannelConfig(ADC1, ADC_Channel_2, 1, ADC_SampleTime_55Cycles5);

    // Enable ADC1
    ADC_Cmd(ADC1, ENABLE);

    // Calibrate ADC1
    ADC_ResetCalibration(ADC1);
    while (ADC_GetResetCalibrationStatus(ADC1));
    ADC_StartCalibration(ADC1);
    while (ADC_GetCalibrationStatus(ADC1));

    // Start ADC1 software conversion
    ADC_SoftwareStartConvCmd(ADC1, ENABLE);
}

// Function to read the ADC value
uint16_t readADC() {
    // Wait for the end of conversion flag
    while (!ADC_GetFlagStatus(ADC1, ADC_FLAG_EOC));
    // Return the ADC conversion result
    return ADC_GetConversionValue(ADC1);
}

// Main function
int main(void) {
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_2); // Set NVIC priority grouping
    SystemCoreClockUpdate(); // Update system core clock
    Delay_Init(); // Initialize delay functions

    // GPIO configuration structure
    GPIO_InitTypeDef GPIO_InitStructure;

    // Enable clock for GPIO port of LED
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);

    // Configure LED pin as output
    GPIO_InitStructure.GPIO_Pin = LED_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP; // Output push-pull mode
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz; // Set speed to 50 MHz
    GPIO_Init(LED_GPIO_PORT, &GPIO_InitStructure);

    // Configure gas sensor pin as analog input
    GPIO_InitStructure.GPIO_Pin = GAS_SENSOR_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AIN; // Analog input mode
    GPIO_Init(SENSOR_GPIO_PORT, &GPIO_InitStructure);

    // Configure ADC to read gas sensor data
    configureADC();

    // Main loop
    while (1) {
        // Read gas level from sensor
        uint16_t gasLevel = readADC(); // Read analog value from gas sensor

        // Check if gas level exceeds the threshold
        if (gasLevel > 512) { // Example threshold value (adjust as needed)
            GPIO_SetBits(LED_GPIO_PORT, LED_PIN); // Turn on LED
        } else {
            GPIO_ResetBits(LED_GPIO_PORT, LED_PIN); // Turn off LED
        }

        // Delay for 1 second
        Delay_Ms(1000); // Delay for 1000 milliseconds (1 second)
    }
}

// Non-Maskable Interrupt (NMI) handler
void NMI_Handler(void) {}

// Hard Fault handler
void HardFault_Handler(void) {
    while (1) {} // Stay in infinite loop if a hard fault occurs
}

// Initialize delay function
void Delay_Init(void) {
    // Configure SysTick timer for 1ms ticks
    SysTick_Config(SystemCoreClock / 1000);
}

// Millisecond delay function
void Delay_Ms(uint32_t n) {
    while (n--) {
        // Wait until the SysTick count flag is set
        while (!(SysTick->CTRL & SysTick_CTRL_COUNTFLAG_Msk)) {}
    }
}






