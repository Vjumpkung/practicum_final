### peri.c
```c
#define F_CPU 16000000  // MCU@16MHz (for accuracy delay)
#include <avr/io.h>     // IO definition ex. PORTD,DDRD
#include <util/delay.h> // for _delay_ms()

void init_peri()
{
    DDRC = 0b00000111;   // pinMode(PC0-2,OUTPUT);  pinMode(PC-3,INPUT); pinMode(PC-4,INPUT);
    PORTC = 0b00000000;  // digitalWrite(PC0-7,LOW)
    PORTC |= (1 << PC3); // pinMode(PC3,INPUT-PULLUP); telling PULLUP by set 1
}

void set_led_value(uint8_t x)
{
    PORTC &= 0b11111000;       // clear 3 bits
    PORTC |= (x & 0b00000111); // extract 3 bits and apply
}

uint16_t read_adc(uint8_t channel)
{                                                         // 0-1023 range
    ADMUX = (0 << REFS1) | (1 << REFS0)                   // ระบุให้ใช้ VCC เป็นแรงดันอ้างอิง (Vref) และ
            | (0 << ADLAR)                                // บันทึกผลลัพธ์ชิดขวาในคู่รีจีสเตอร์ADCH/ADCL
            | (channel & 0b1111);                         // ตั้งค่า MUX เป็นค่า channel
    ADCSRA = (1 << ADEN)                                  // เปิดวงจร ADC
             | (1 << ADPS2) | (1 << ADPS1) | (1 << ADPS0) // ใช้ความเร็ว 1/128 ของคล็อกระบบ
             | (1 << ADSC);                               // สั่งวงจร ADC ให้เริ่มต้นการแปลง
    while ((ADCSRA & (1 << ADSC)))                        // รอจนบิต ADSC กลายเป็น 0 ซึ่งหมายถึงการแปลงเสร็จสิ้ น
        ;
    return ADCL + ADCH * 256; // ผลลัพธ์ถูกเก็บอยู่ในรีจีสเตอร์ADCL และ ADCH
    // สามารถใช้ return ADC ได้เช่นกัน
}
```

### peri.h

```c
#define F_CPU 16000000  // MCU@16MHz (for accuracy delay)
#include <avr/io.h>     // IO definition ex. PORTD,DDRD
#include <util/delay.h> // for _delay_ms()


#define IS_SWITCH_PRESSED() ((PINC & (1 << PC3)) == 0)
#define LDR_PIN PC4
void init_peri();
void set_led_value(uint8_t x);
uint16_t read_adc(uint8_t channel);
```

### count-sw.c
```c
#define F_CPU 16000000  // MCU@16MHz (for accuracy delay)
#include <avr/io.h>     // IO definition ex. PORTD,DDRD
#include <util/delay.h> // for _delay_ms()
#include "peri.h"

int main()
{
    init_peri();
    int count = 0;
    _delay_ms(500);
    while (1)
    {
        // while(not hold)
        while (!IS_SWITCH_PRESSED());
        _delay_ms(100);
        count += 1;
        count = count % 8;
        set_led_value(count);
        // while(not release)
        while (IS_SWITCH_PRESSED());
        _delay_ms(100);
    }
}
```

### light-3.c
```c
#define F_CPU 16000000  // MCU@16MHz (for accuracy delay)
#include <avr/io.h>     // IO definition ex. PORTD,DDRD
#include <util/delay.h> // for _delay_ms()
#include "peri.h"

int main()
{
    init_peri();
    uint8_t light = 0;
    uint16_t ldr = 0;
    while (1)
    {
        ldr = read_adc(LDR_PIN);
        if (ldr >= 0 && ldr < 1024 / 3)
        {
            light = 0b001; // red light=1
        }
        else if (ldr >= 1024 / 3 && ldr < 1024 * 2 / 3)
        {
            light = 0b010; // yellow light=1
        }
        else
        {
            light = 0b100; // green light=1
        }
        set_led_value(light);
        _delay_ms(100);
    }
    return 0;
}
```

### light-8.c
```c
#define F_CPU 16000000  // MCU@16MHz (for accuracy delay)
#include <avr/io.h>     // IO definition ex. PORTD,DDRD
#include <util/delay.h> // for _delay_ms()
#include <math.h>
#include "peri.h"

int main()
{
    init_peri();
    uint8_t light = 0;
    uint16_t ldr = 0;
    while (1)
    {
        ldr = read_adc(LDR_PIN);
        light = floor(ldr * 8 / 1024); // scale down to 0-7 (light=V/(1024/8);should use map)
        set_led_value(light);          // set led by scaled down value
        _delay_ms(100);
    }
    return 0;
}
```