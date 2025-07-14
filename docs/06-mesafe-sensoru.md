# Proje 06 - Teremin

```c
#include <HCSR04.h>

const byte triggerPin = 6;
const byte echoPin = 5;
UltraSonicDistanceSensor mesafeOlcer(triggerPin, echoPin);

int buzzerPin = 3;

const int min_mesafe = 2;
const int max_mesafe = 32;
const int min_frekans = 262;
const int max_frekans = 1048;

// 3. Oktav
const int DO_3 = 262;
const int RE_3 = 294;
const int MI_3 = 330;
const int FA_3 = 349;
const int SOL_3 = 392;
const int LA_3 = 440;
const int SI_3 = 494;

// 4. Oktav
const int DO_4 = 523;
const int RE_4 = 587;
const int MI_4 = 659;
const int FA_4 = 698;
const int SOL_4 = 784;
const int LA_4 = 880;
const int SI_4 = 987;

const int DO_5 = 1048;

int notalar[] = { DO_3, RE_3, MI_3, FA_3, SOL_3, LA_3, SI_3, DO_4, RE_4, MI_4, FA_4, SOL_4, LA_4, SI_4, DO_5 };
int nota_adedi = 14;


void setup() {

}

void loop() {
  float mesafe = mesafeOlcer.measureDistanceCm();
  int mesafeMm = round(mesafe);
  if (mesafeMm > max_mesafe) {
    noTone(buzzerPin);
    delay(10);
    return;
  }
  int indeks = map(mesafeMm, min_mesafe, max_mesafe, 0, nota_adedi-1);
  tone(buzzerPin,notalar[indeks]);
  delay(10);
}
```


