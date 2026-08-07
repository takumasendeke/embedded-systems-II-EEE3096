/* USER CODE BEGIN Header */
/**
  @file           : main.c
  @brief          : Multi-mode LED control with timer interrupts skeleton
  */
/* USER CODE END Header */

/* Includes ------------------------------------------------------------------*/
#include "main.h"
#include "stm32f0xx.h"
#include <stdint.h>
#include <stdlib.h>      // for rand()
#include "lcd_stm32f0.h"

/* Private variables ---------------------------------------------------------*/
TIM_HandleTypeDef htim16;

/* USER CODE BEGIN PV */
// LED arrays
GPIO_TypeDef* led_ports[8] = {GPIOB, GPIOB, GPIOB, GPIOB, GPIOB, GPIOB, GPIOB, GPIOB};
uint16_t led_pins[8] = {GPIO_PIN_0, GPIO_PIN_1, GPIO_PIN_2, GPIO_PIN_3, GPIO_PIN_4, GPIO_PIN_5, GPIO_PIN_6, GPIO_PIN_7};

// Timer event flag (set by ISR)
volatile uint8_t timer_event = 0;

// Mode enumeration
typedef enum {
    MODE_1 = 0,
    MODE_2,
    MODE_3,
    MODE_OFF
} LED_Mode;
volatile LED_Mode current_mode = MODE_OFF;

// Mode 1 & 2 shared variables
volatile uint8_t current_led = 0;
volatile int8_t direction = 1;

// Speed toggle variables
#define DEBOUNCE_MS 50
uint32_t last_button_time[4] = {0, 0, 0, 0};
uint8_t speed_state = 0;        // 0 = slow (1s), 1 = fast (0.5s)

// start as pin sets to indicate button not pressed
uint8_t last_sw0 = GPIO_PIN_SET;
uint8_t last_sw1 = GPIO_PIN_SET;
uint8_t last_sw2 = GPIO_PIN_SET;
uint8_t last_sw3 = GPIO_PIN_SET;

// Mode 3 state machine
typedef enum {
    SPARKLE_IDLE = 0,
    SPARKLE_DISPLAY,
    SPARKLE_TURN_OFF
} SparkleState;

volatile SparkleState sparkle_state = SPARKLE_IDLE;
volatile uint8_t sparkle_pattern = 0;
volatile uint32_t sparkle_display_until = 0;
volatile uint32_t sparkle_next_off_time = 0;
volatile uint8_t sparkle_off_index = 0;
volatile uint8_t sparkle_leds_on[8];      // Track which LEDs are currently on
volatile uint8_t sparkle_num_leds_on = 0;

// Current period reference
uint32_t current_period_ms = 1000;
/* USER CODE END PV */

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);
static void MX_GPIO_Init(void);
static void MX_TIM16_Init(void);
void TIM16_IRQHandler(void);

/* USER CODE BEGIN PFP */
void clear_all_leds(void);
void turn_on_all_leds(void); // added for ease of implementation in Mode 2
void turn_on_led(uint8_t index);
void turn_off_led(uint8_t index);
void change_timer_period(uint32_t new_period_ms);
void handle_buttons(void);
void set_mode(LED_Mode new_mode);
void mode1_update(void);
void mode2_update(void);
void mode3_update(void);
/* USER CODE END PFP */

/* USER CODE BEGIN 0 */
void clear_all_leds(void){
    /* TODO: Iterate through the LED arrays and set all pins to GPIO_PIN_RESET */
	for (uint8_t i = 0; i < 8; i++){
		HAL_GPIO_WritePin(led_ports[i], led_pins[i], GPIO_PIN_RESET);
	}
}

void turn_on_all_leds(void){
	for (uint8_t i = 0; i < 8; i++){
		HAL_GPIO_WritePin(led_ports[i], led_pins[i], GPIO_PIN_SET);
	}
}

void turn_on_led(uint8_t index){
    /* TODO: Set the specified LED pin to GPIO_PIN_SET */
	HAL_GPIO_WritePin(led_ports[index], led_pins[index], GPIO_PIN_SET);
}

void turn_off_led(uint8_t index){
    /* TODO: Set the specified LED pin to GPIO_PIN_RESET */
	HAL_GPIO_WritePin(led_ports[index], led_pins[index], GPIO_PIN_RESET);
}

void change_timer_period(uint32_t new_period_ms){
    /* TODO: Calculate the new ARR value based on the requested millisecond period */
	float freq = 1 / ((float)new_period_ms / 1000); // convert ms -> s, /1000
	float new_arr_float  = (1000 / freq) - 1; // HCLK/PSC = 1000

	uint32_t new_arr = (uint32_t)new_arr_float;

    /* TODO: Update the TIM16 ARR register directly */
	TIM16 -> ARR = new_arr;

    /* TODO: Reset the TIM16 CNT register to 0 */
	TIM16 -> CNT = 0;

    current_period_ms = new_period_ms;
}

void handle_buttons(void)
{
    uint32_t now = HAL_GetTick();

    /* TODO: Read the state of all four buttons (PA0 to PA3) */
    uint8_t current_sw0 = HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_0);
    uint8_t current_sw1 = HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_1);
    uint8_t current_sw2 = HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_2);
    uint8_t current_sw3 = HAL_GPIO_ReadPin(GPIOA, GPIO_PIN_3);

    uint32_t elapsedTime_sw0 = now - last_button_time[0];
    uint32_t elapsedTime_sw1 = now - last_button_time[1];
    uint32_t elapsedTime_sw2 = now - last_button_time[2];
    uint32_t elapsedTime_sw3 = now - last_button_time[3];

    /* TODO: Implement debounce logic for PA0. Toggle the timer speed between 500ms and 1000ms. */
    if (elapsedTime_sw0 >= DEBOUNCE_MS){ // fix to allow edge detection
		if ((last_sw0 == GPIO_PIN_SET) && (current_sw0 == GPIO_PIN_RESET)){
			last_button_time[0] = HAL_GetTick();
			if(current_period_ms == 1000){
				change_timer_period(500);
				current_period_ms = 500;
			} else {
				change_timer_period(1000);
				current_period_ms = 1000;
			}
		}
		last_sw0 = current_sw0;
    }
    /* TODO: Implement debounce logic for PA1. Call set_mode(MODE_1). */
    if (elapsedTime_sw1 >= DEBOUNCE_MS){
    	if((last_sw1 == GPIO_PIN_SET) && (current_sw1 == GPIO_PIN_RESET)){
    		last_button_time[1] = HAL_GetTick();

    		set_mode(MODE_1);
    	}
    	last_sw1 = current_sw1;
    }

    /* TODO: Implement debounce logic for PA2. Call set_mode(MODE_2). */
    if (elapsedTime_sw2 >= DEBOUNCE_MS){
    	if ((last_sw2 == GPIO_PIN_RESET) && (current_sw2 == GPIO_PIN_SET)){
    		last_button_time[2] = HAL_GetTick();

    		set_mode(MODE_2);
    	}
    	last_sw2 = current_sw2;
    }

    /* TODO: Implement debounce logic for PA3. Call set_mode(MODE_3). */
    if (elapsedTime_sw3 >= DEBOUNCE_MS ){
    	if((last_sw3 == GPIO_PIN_SET) && (current_sw3 == GPIO_PIN_RESET)){
    		last_button_time[3] = HAL_GetTick();

    		set_mode(MODE_3);
    	}
    	last_button_time[3] = current_sw3;
    }
}

void set_mode(LED_Mode new_mode){
    current_mode = new_mode;

    /* TODO: Clear all LEDs to ensure a clean slate */
    clear_all_leds();

    /* TODO: Reset mode-specific tracking variables (like current_led, direction, or sparkle_state) */
    current_led = 0;
    direction = 1;
    sparkle_state = SPARKLE_IDLE;

}

void mode1_update(void){
    /* TODO: Implement the standard running light sequence (Task 3 logic) */
	clear_all_leds();

	turn_on_led(current_led);
	current_led += direction;

	if (current_led == 7) {direction = -1;}
	else if (current_led == 0) {direction = 1;}
}

void mode2_update(void){
    /* TODO: Implement the inverse running light sequence. All LEDs on except one. */
	turn_on_all_leds();

	turn_off_led(current_led);
	current_led += direction;

	if (current_led == 7) {direction = -1;}
	else if (current_led == 0) {direction = 1;}
}

void mode3_update(void){
    uint32_t now = HAL_GetTick();

    switch (sparkle_state) {
        case SPARKLE_IDLE:
            /* TODO: Generate a random 8-bit pattern using rand() */
            /* TODO: Turn on the LEDs according to the generated pattern */
            /* TODO: Store the indices of the active LEDs in the sparkle_leds_on array */
            /* TODO: Generate a random display duration between 100ms and 1500ms */
            /* TODO: Transition to SPARKLE_DISPLAY state */
            break;

        case SPARKLE_DISPLAY:
            /* TODO: Wait for the random display duration to elapse using HAL_GetTick() */
            /* TODO: Once elapsed, transition to SPARKLE_TURN_OFF state */
            break;

        case SPARKLE_TURN_OFF:
            /* TODO: Wait for the random turn-off delay (100ms to 150ms) to elapse */
            /* TODO: Turn off one LED from the sparkle_leds_on array */
            /* TODO: Generate a new random turn-off delay for the next LED */
            /* TODO: If all LEDs are turned off, transition back to SPARKLE_IDLE state */
            break;

        default:
            sparkle_state = SPARKLE_IDLE;
            break;
    }
}
/* USER CODE END 0 */

/**
  @brief  The application entry point.
  @retval int
*/
int main(void)
{
    HAL_Init();
    SystemClock_Config();
    MX_GPIO_Init();
    MX_TIM16_Init();
    init_LCD();

    /* USER CODE BEGIN 2 */
    // Seed random number generator
    srand(HAL_GetTick());

    clear_all_leds();
    change_timer_period(1000);
    HAL_TIM_Base_Start_IT(&htim16);
    lcd_putstring("Task 4");
    lcd_command(LINE_TWO);
    lcd_putstring("Multi-mode LED");
    /* USER CODE END 2 */

    while (1)
    {
        /* USER CODE BEGIN WHILE */
        // Check for debounced button presses
        handle_buttons();

        // Handle scheduled timer events for Mode 1 and Mode 2
        if (timer_event) {
            timer_event = 0;

            switch (current_mode) {
                case MODE_1:
                    mode1_update();
                    break;
                case MODE_2:
                    mode2_update();
                    break;
                case MODE_3:
                    // Mode 3 is non-blocking and driven continuously by HAL_GetTick
                    break;
                case MODE_OFF:
                default:
                    clear_all_leds();
                    break;
            }
        }

        // Mode 3 requires continuous polling to operate its state machine delays accurately
        if (current_mode == MODE_3) {
            mode3_update();
        }
        /* USER CODE END WHILE */
    }
}

/**
  @brief System Clock Configuration (HSI 8 MHz)
*/
void SystemClock_Config(void)
{
    LL_FLASH_SetLatency(LL_FLASH_LATENCY_0);
    while(LL_FLASH_GetLatency() != LL_FLASH_LATENCY_0) {}

    LL_RCC_HSI_Enable();
    while(LL_RCC_HSI_IsReady() != 1) {}

    LL_RCC_HSI_SetCalibTrimming(16);
    LL_RCC_SetAHBPrescaler(LL_RCC_SYSCLK_DIV_1);
    LL_RCC_SetAPB1Prescaler(LL_RCC_APB1_DIV_1);
    LL_RCC_SetSysClkSource(LL_RCC_SYS_CLKSOURCE_HSI);
    while(LL_RCC_GetSysClkSource() != LL_RCC_SYS_CLKSOURCE_STATUS_HSI) {}

    LL_SetSystemCoreClock(8000000);

    if (HAL_InitTick(TICK_INT_PRIORITY) != HAL_OK) {
        Error_Handler();
    }
}

/**
  @brief TIM16 Initialization
*/
static void MX_TIM16_Init(void)
{
    htim16.Instance = TIM16;
    htim16.Init.Prescaler = 7999;      // 8000 - 1
    htim16.Init.CounterMode = TIM_COUNTERMODE_UP;
    htim16.Init.Period = 999;          // Will be changed dynamically
    htim16.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
    htim16.Init.RepetitionCounter = 0;
    htim16.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_ENABLE;

    if (HAL_TIM_Base_Init(&htim16) != HAL_OK) {
        Error_Handler();
    }

    NVIC_EnableIRQ(TIM16_IRQn);
}

/**
  @brief GPIO Initialization
*/
static void MX_GPIO_Init(void)
{
    __HAL_RCC_GPIOA_CLK_ENABLE();
    __HAL_RCC_GPIOB_CLK_ENABLE();

    GPIO_InitTypeDef GPIO_InitStruct = {0};

    // LEDs PB0..PB7 as outputs
    GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
    GPIO_InitStruct.Pull = GPIO_NOPULL;
    GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;

    for (uint8_t i = 0; i < 8; i++) {
        GPIO_InitStruct.Pin = led_pins[i];
        HAL_GPIO_Init(led_ports[i], &GPIO_InitStruct);
    }

    // Buttons PA0..PA3 as inputs with pull-up (active low)
    GPIO_InitStruct.Pin = GPIO_PIN_0 | GPIO_PIN_1 | GPIO_PIN_2 | GPIO_PIN_3;
    GPIO_InitStruct.Mode = GPIO_MODE_INPUT;
    GPIO_InitStruct.Pull = GPIO_PULLUP;
    HAL_GPIO_Init(GPIOA, &GPIO_InitStruct);
}

/**
  @brief TIM16 interrupt handler - sets flag only
*/
void TIM16_IRQHandler(void)
{
    HAL_TIM_IRQHandler(&htim16);
    timer_event = 1;
}

/**
  @brief Error handler
*/
void Error_Handler(void)
{
    __disable_irq();
    while (1) {}
}
