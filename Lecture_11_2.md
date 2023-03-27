# ภาษาซีสำหรับไมโครคอนโทรลเลอร์ (II) (elab)

## ตัวแปร reg ถูกประกาศเอาไว้เป็นชนิด uint8_t ดังนี้

```c
uint8_t reg;
```

### set bit เป็น 1

```c
reg |= 1 << 5; // set bit 5
```

### clear but เป็น 0

```c
reg &= ~(1 << 3); // clear bit 3
reg &= ~(1 << 6); // clear bit 6
```

### check bit
```c
if (reg & (1 << 2)) // check bit 2
    printf("Bit 2 is set.");
else
    printf("Bit 2 is clear.");
```

### เปิดปิด LED
```c
#include <avr/io.h>
#define RED    PC0
#define YELLOW PC1
#define GREEN  PC2

#define ON     1
#define OFF    0

void set_led(uint8_t pin, uint8_t state)
{
    if (state)
        PORTC |= (1 << pin); // set pin to high
    else
        PORTC &= ~(1 << pin); // set pin to low
}
```

### LED ฐาน 2

```c
#include <avr/io.h>

void set_led_value(uint8_t x)
{
    PORTC = (PORTC & ~(7)) | ((x & 7)); // set LED from 0 to 7
}
```

### init peripheral 

```c
void init_peripheral()
{
    // set PC0, PC1, PC2 as output and PC3, PC4 as input
    DDRC |= 1 << PC0;
    DDRC |= 1 << PC1;
    DDRC |= 1 << PC2;
    DDRC &= ~(1 << PC3);
    DDRC &= ~(1 << PC4);

    // set PC3 pull-up resistor to high and PC4 pull-up resistor to low
    PORTC |= 1 << PC3;
    PORTC &= ~(1 << PC0);
    PORTC &= ~(1 << PC1);
    PORTC &= ~(1 << PC2);
    PORTC &= ~(1 << PC4);
}
```

### ตรวจสอบสวิตช์
```c
#define IS_SWITCH_PRESSED()  (!(PINC & (1 << PC3)))
```
