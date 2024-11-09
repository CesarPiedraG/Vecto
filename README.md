// Simulador de flujo

#include <Servo.h>
#include <DHT.h>

#define DHTTYPE DHT11
#define DHTPIN 11

DHT dht(DHTPIN, DHTTYPE);
Servo servo;
int servoPin = 9;

void setup() {
  Serial.begin(9600);
  dht.begin();
  servo.attach(servoPin);
}

void loop() {
  servo.write(0);
  
  float humedad = dht.readHumidity();
  float temp = dht.readTemperature();

  // Verifica si el sensor funciona correctamente
  if (isnan(humedad) || isnan(temp)) {
    Serial.println("ERROR EN EL SENSOR");
    return;
  }

  // Imprime los datos de humedad y temperatura en el monitor serial
  Serial.print("Humedad: ");
  Serial.print(humedad);
  Serial.print("%   Temperatura: ");
  Serial.print(temp);
  Serial.println("°C");

  // Calcula el ángulo en función de tiempo y temperatura, para simular la vorticidad
  // Este valor cambiará el ángulo del servo entre 0 y 180 grados

  float tiempo = millis() / 1000.0;          // Devuelve el tiempo transcurrido en milisegundos desde que la placa comenzó a ejecutar el programa
  float velocidad =  1.5 + (temp - 10) * 0.05;        // Ajusta la "velocidad" en función de la temperatura
  float angulo = 90 + 90 * sin(velocidad * tiempo);  // Rango (oscila entre 0 y 180)

  // Mueve el servo al ángulo calculado
  servo.write(angulo);

  // Imprime el ángulo actual en el monitor serial
  Serial.print("Velocidad del Servo: ");
  Serial.println(velocidad);
  Serial.print("Ángulo del Servo: ");
  Serial.println(angulo);
   // Calcula la vorticidad
  float vorticidad = 90 * cos(velocidad * tiempo) * velocidad;  // Derivada del ángulo con respecto al tiempo

  // Imprime la vorticidad
  Serial.print("Vorticidad: ");
  Serial.println(vorticidad);

  delay(100); 
}
