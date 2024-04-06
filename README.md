# PR5_EricAyala

## PRACTICA 5:  Buses de comunicación I (introducción y I2c) 

### Ejercicio Practico 1 ESCÁNER I2C

``` cpp
#include <Arduino.h>
#include <Wire.h>

void setup()
{
Wire.begin();
Serial.begin(115200);
while (!Serial){

Serial.println("\nI2C Scanner");
}
}
void loop()
{
byte error, address;
int nDevices;
Serial.println("Scanning...");
nDevices = 0;
for(address = 1; address < 127; address++ )
{
Wire.beginTransmission(address);
error = Wire.endTransmission();
if (error == 0)
{
nDvices++;
Serial.print("I2C device found at address 0x");
if (address<16) {
Serial.print("0");
Serial.print(address,HEX);
Serial.println(" !");
}
}
}
}
```

**FUNCIONAMIENTO**
Con este primer codigo vamos a detectar dispositivos conectados a un bus I2C. 

- Las nueva biblioteca necesaria es `Wire.h` es necesaria para la comunicación I2C.
- En el `setup()`, tenemos el `wire.begin()`, el cual inicia la comunicación I2C.
- En el bucle principal `loop()`, primero se declara una variable *error* y una variable *address*, que sirve para iterar a través de las direcciones de los dispositivos I2C. La variable *nDevices* cuenta el número de dispositivos detectados.

- La función `Wire.beginTransmission(address)`, inicia una transmisión hacia un dispositivo en el bus I2C. 
    - Address: es el argumento que especifica la dirección del dispositivo al que se enviarán los datos

- La función `Wire.endTransmission()`, finaliza la transmisión I2C y envía los datos almacenados. Devuelve un valor que indicará el estado de transmisión
    - 0: éxito
    - 1: error de datos
    - 2: error de dirección
    - 3: otro error

- Iteramos a través de las direcciones de los dispositivos I2C del 1 al 127, inicializando nDevices en 0.
```cpp
nDevices = 0;
for(address = 1; address < 127; address++)
```
- Finalmente, se comprueba si hubo errores, si error == 0, significa que hay un dispositivo conectado a esa dirección. Si se detecta un dispositivo, se incrementa el contador *nDevices*.

## Ejercicio práctico 2

```cpp
#include <Adafruit_AHTX0.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <Wire.h>

#define SCREEN_WIDTH 128 // Ancho de la pantalla OLED, en píxeles
#define SCREEN_HEIGHT 32 // Alto de la pantalla OLED, en píxeles

#define OLED_RESET -1 // Pin de reset (o -1 si se comparte el pin de reset del Arduino)
#define SCREEN_ADDRESS 0x3C ///< Ver la hoja de datos para la dirección; 0x3D para 128x64, 0x3C para 128x32

#define AHTX0_I2C_SCL 6
#define AHTX0_I2C_SDA 5

#define OLED_SDA 16
#define OLED_SCL 17

Adafruit_AHTX0 aht;
Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire1, OLED_RESET);

void setup() {
  Wire.begin(AHTX0_I2C_SDA, AHTX0_I2C_SCL);
  Wire1.begin(OLED_SDA, OLED_SCL);
  Serial.begin(115200);
  Serial.println("Adafruit AHT10/AHT20 demo!");

  if (!aht.begin()) {
    Serial.println("¡No se pudo encontrar el AHT! ¡Verifique la conexión!");
    while (1) delay(10);
  }
  Serial.println("AHT10 o AHT20 encontrado");

  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
    Serial.println(F("¡Error al asignar memoria para SSD1306!"));
    for(;;);
  }

  display.display();
  delay(2000);
  display.clearDisplay();
}

void loop() {
  sensors_event_t humidity, temp;
  aht.getEvent(&humidity, &temp); // Obtener los objetos de temperatura y humedad con datos actualizados

  display.clearDisplay();

  display.setTextSize(1);
  display.setTextColor(SSD1306_WHITE);
  display.setCursor(0, 0);
  display.print("Temperatura: ");
  display.println(temp.temperature);
  display.print("Humedad: ");
  display.println(humidity.relative_humidity);

  display.display();
  
  Serial.print("Temperatura: ");
  Serial.print(temp.temperature);
  Serial.println(" grados C");
  Serial.print("Humedad: ");
  Serial.print(humidity.relative_humidity);
  Serial.println("% HR");

  delay(500);
}
```

Para poder llevar a cabo el código y hacerlo funcionar, hemos tenido que instalar las librerias para el sensor y para el display. Este proceso ha sido el que más nos ha complicado la práctica, ya que en cuanto a la creación del código, nos hemos ayudado de chat gpt, lo cúal ha hecho mucho menos tediosa la creación del código. 
- Las librerias descargadas han sido:
    - **Adafruit_AHTX0.h**: sensor.
    -**Adafruit_SSD1306.h**: display.

**FUNCIONAMIENTO**
- En la función `setup()`, se inicializan las comunicaciones I2C y serial, se verifica la conexión con el sensor AHTX0 y se inicializa la pantalla OLED. Si hay algún error en alguna de estas etapas, el programa se detiene.

- En el `loop()`, se obtienen los datos de temperatura y humedad del sensor AHTX0. Estos datos se muestran en la pantalla OLED y se envían por el puerto serial para su visualización en la computadora. El bucle se repite cada 500 milisegundos.

- Las salidas en el print serán:
    - Mensajes indicando el estado del sensor AHTX10
    ```cpp
    if (!aht.begin()) {
   Serial.println("¡No se pudo encontrar el AHT! ¡Verifique la conexión!"); //COMPROBAR SI EL **SENSOR** ESTA DISPONIBLE
   while (1) delay(10);
    }
    Serial.println("AHT10 o AHT20 encontrado");

    if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)) {
   Serial.println(F("¡Error al asignar memoria para SSD1306!")); //COMPROBAR SI EL **DISPLAY** ESTA DISPONIBLE
   for(;;);
    }
    
    - Temperatura: <24,65> grados C" y "Humedad: <42,89> % HR.
    ```
    - Por último, también saldrán mensajes de error si no se puede encontrar el sensor AHTX0

    Imagen: 
    
