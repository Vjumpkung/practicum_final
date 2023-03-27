### chase.c

```c
#define F_CPU 16000000 // MCU@16MHz (for accuracy delay)
#include <avr/io.h>
#include <util/delay.h>

int main()
{
    DDRC  = 0b00000111; // init PC0, PC1, PC2 as output
    while (1)
    {
        PORTC = 0b00000001; // set PC0 to high
        _delay_ms(500); // delay 500ms
        PORTC = 0b00000010; // set PC1 to high
        _delay_ms(500); // delay 500ms
        PORTC = 0b00000100; // set PC2 to high
        _delay_ms(500); // delay 500ms
    }
}
```