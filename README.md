Añadimos 9 librerias diferentes, las 5 primeras ya han sido explicadas y las cuatro últimas son librerias de la biblioteca de audio ESP8266 de Earle F.Philhower:
```
#include "Arduino.h"
#include "FS.h"
#include "HTTPClient.h"
#include "SPIFFS.h"
#include "SD.h"
#include "AudioGeneratorAAC.h"
#include "AudioOutputI2S.h"
#include "AudioFileSourcePROGMEM.h"
#include "sampleaac.h"
```
Comenzamos generando 3 punteros:
- AudioFileSourcePROGMEM lee un archivo de una matriz PROGREM
- AudioGeneratorAAC reproduce un archivo AAC mono o estéreo utilizando el decodificador AAC de punto fijo Helix
- AudioOutputI2 envía señales estéreo o mono a cualquier frecuencia establecida
```
AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;
```
Despues de los punteros comenzamos con el void setup() estableciendo una comunciacion serie de 115200 bauds. Declaramos un puntero *in como variable para el fichero de audio, seguimos con el puntero *acc para establecer la comunicación entre decodificador y la misma placa. Finalmente configuramos lo siguiente:
- Ganancia
- Pines de salida
- Salida mono forzada
Para eso utilizaremos el *out
```
void setup(){
Serial.begin(9600);
in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
aac = new AudioGeneratorAAC();
out = new AudioOutputI2S();
out -> SetGain(0.125);
out -> SetPinout(26,25,22);
aac->begin(in, out);
}
```
Por ultimo nos pondremos con el void loop() en este creamos un if en el que comprobamos si Running es true para que no salten errores, seguidamente llamamos a la función loop(), en caso de que nos devuelva false, se llamaria a la función stop().
```
void loop(){
if (aac->isRunning()) {
aac->loop();
} else {
aac -> stop();
Serial.printf("Sound Generator\n");
delay(1000);
}
}
```