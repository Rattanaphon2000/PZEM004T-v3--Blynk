# PZEM004T-v3--Blynk
#define BLYNK_PRINT Serial


#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

#include <PZEM004Tv30.h>  // <--- https://github.com/mandulaj/PZEM-004T-v30

// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "KgRqXbi6mb7QUGd-sXM3wT3LnhDMlfS-"; //<---Auth Token

// Your WiFi credentials.
// Set password to "" for open networks.
char ssid[] = "IG : _kubgif_"; //<---SSID name
char pass[] = "0904813367"; //<---Password

PZEM004Tv30 pzem(12, 14); //D6/Tx pzem ,D5/Rx pzem

BlynkTimer timer;

BLYNK_WRITE(V16)
{
    int pinValue = param.asInt(); // assigning incoming value from pin V1 to a variable   
    if (pinValue == 1){
    //pzem.resetEnergy(); 
    Serial.println("Reset");
    }

  // process received value
}

// This function sends Arduino's up time every second to Virtual Pin (5).
// In the app, Widget's reading frequency should be set to PUSH. This means
// that you define how often to send data to Blynk App.
void myTimerEvent()
{

float voltage = pzem.voltage();
    if( !isnan(voltage) ){
        Serial.print("Voltage: "); Serial.print(voltage); Serial.println("V");
        Blynk.virtualWrite(V10,voltage);
    } else {
        Serial.println("Error reading voltage");
    }

    float current = pzem.current();
    if( !isnan(current) ){
        Serial.print("Current: "); Serial.print(current); Serial.println("A");
        Blynk.virtualWrite(V11,current);
    } else {
        Serial.println("Error reading current");
    }

    float power = pzem.power();
    if( !isnan(power) ){
        Serial.print("Power: "); Serial.print(power); Serial.println("W");
        Blynk.virtualWrite(V12,power);
    } else {
        Serial.println("Error reading power");
    }

    float energy = pzem.energy();
    if( !isnan(energy) ){
        Serial.print("Energy: "); Serial.print(energy,3); Serial.println("kWh");
        Blynk.virtualWrite(V13,energy,3);
    } else {
        Serial.println("Error reading energy");
    }

    float frequency = pzem.frequency();
    if( !isnan(frequency) ){
        Serial.print("Frequency: "); Serial.print(frequency, 1); Serial.println("Hz");
        Blynk.virtualWrite(V14,frequency, 1);
    } else {
        Serial.println("Error reading frequency");
    }

    float pf = pzem.pf();
    if( !isnan(pf) ){
        Serial.print("PF: "); Serial.println(pf);
        Blynk.virtualWrite(V15,pf); 
    } else {
        Serial.println("Error reading power factor");
    }

    Serial.println();



  
  // You can send any value at any time.
  // Please don't send more that 10 values per second.
}

void setup()
{
  // Debug console
  Serial.begin(9600);

  Blynk.begin(auth, ssid, pass); //<--- Server เดิม
  // You can also specify server:
  //Blynk.begin(auth, ssid, pass, "192.168.0.123", 8080);
  //Blynk.begin(auth, ssid, pass, IPAddress(192,168,1,100), 8080);

  // Setup a function to be called every second
  timer.setInterval(2000L, myTimerEvent);
}

void loop()
{
  Blynk.run();
  timer.run(); // Initiates BlynkTimer
}
