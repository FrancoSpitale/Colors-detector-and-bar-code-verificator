#include <hidboot.h>
#include <usbhub.h>
#include <Wire.h>
#include <EEPROM.h>
#ifdef dobogusinclude
#include <spi4teensy3.h>
#endif
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 2, 1, 0, 4, 5, 6, 7, 3, POSITIVE); // Set the LCD I2C address
/-----( Declare Constants )-----/
/-----( Declare objects )-----/
// set the LCD address to 0x27 for a 16 chars 2 line display
// Set the pins on the I2C chip used for LCD connections:
// addr, en,rw,rs,d4,d5,d6,d7,bl,blpol

class KbdRptParser : public KeyboardReportParser
{
void PrintKey(uint8_t mod, uint8_t key);

protected:
virtual void OnControlKeysChanged(uint8_t before, uint8_t after);

virtual void OnKeyDown	(uint8_t mod, uint8_t key);
virtual void OnKeyUp	(uint8_t mod, uint8_t key);
virtual void OnKeyPressed(uint8_t key);
};

void KbdRptParser::PrintKey(uint8_t m, uint8_t key)
{
MODIFIERKEYS mod;
((uint8_t)&mod) = m;
//Serial.print((mod.bmLeftCtrl == 1) ? "C" : " ");
//Serial.print((mod.bmLeftShift == 1) ? "S" : " ");
//Serial.print((mod.bmLeftAlt == 1) ? "A" : " ");
//Serial.print((mod.bmLeftGUI == 1) ? "G" : " ");

//Serial.print(" >");
PrintHex<uint8_t>(key, 0x80);
//Serial.print("< ");

//Serial.print((mod.bmRightCtrl   == 1) ? "C" : " ");
//Serial.print((mod.bmRightShift  == 1) ? "S" : " ");
//Serial.print((mod.bmRightAlt    == 1) ? "A" : " ");
// Serial.println((mod.bmRightGUI == 1) ? "G" : " ");
};

void KbdRptParser::OnKeyDown(uint8_t mod, uint8_t key)
{
//Serial.print("DN ");
//PrintKey(mod, key);
uint8_t c = OemToAscii(mod, key);

if (c)
    OnKeyPressed(c);
}

void KbdRptParser::OnControlKeysChanged(uint8_t before, uint8_t after) {

MODIFIERKEYS beforeMod;
*((uint8_t*)&beforeMod) = before;

MODIFIERKEYS afterMod;
*((uint8_t*)&afterMod) = after;

if (beforeMod.bmLeftCtrl != afterMod.bmLeftCtrl) {
    Serial.println("LeftCtrl changed");
}
if (beforeMod.bmLeftShift != afterMod.bmLeftShift) {
    Serial.println("LeftShift changed");
}
if (beforeMod.bmLeftAlt != afterMod.bmLeftAlt) {
    Serial.println("LeftAlt changed");
}
if (beforeMod.bmLeftGUI != afterMod.bmLeftGUI) {
    Serial.println("LeftGUI changed");
}

if (beforeMod.bmRightCtrl != afterMod.bmRightCtrl) {
    Serial.println("RightCtrl changed");
}
if (beforeMod.bmRightShift != afterMod.bmRightShift) {
    Serial.println("RightShift changed");
}
if (beforeMod.bmRightAlt != afterMod.bmRightAlt) {
    Serial.println("RightAlt changed");
}
if (beforeMod.bmRightGUI != afterMod.bmRightGUI) {
    Serial.println("RightGUI changed");
}
}

void KbdRptParser::OnKeyUp(uint8_t mod, uint8_t key)
{
//Serial.print("UP ");
//PrintKey(mod, key);
}
int i = 0;
int j = 0;

String Gdrive= "000005801394820";

//char Gdrive[15]="000005801394820";
//char Motor[15]= {0,0,0,0,0,5,8,0,1,3,9,4,8,2,0};
//0,0,0,0,0,5,8,0,1,3,9,4,8,2,0
String Ford= "000005801543412";
String Combine= "000005801720490";
String Motor = "000000000000000";
String Motor_C13="00000";

int Rele_Ford= 15;
int Rele_Gdrive= 16;
int Rele_Combine=17;

void KbdRptParser::OnKeyPressed(uint8_t key)
{
//Serial.print("ASCII: ");
Serial.println((char)key);
Motor[i] =(char)key;
Serial.println(Motor[i]);

if(i>4 && i<10)
{

Motor_C13[j] =(char)key;
j++;
}

i++;
}

USB Usb;
//USBHub Hub(&Usb);
HIDBoot<HID_PROTOCOL_KEYBOARD> HidKeyboard(&Usb);

uint32_t next_time;

KbdRptParser Prs;

// pines de arduino para conectar el sensor
//tsl= tono(tinte) saturacion luminancia
// tinte hace referencia a un tono sólido o plano,
// esto es, que no contiene degradaciones.
struct TSL{double t; double s; double l;};//se define una funcion tipo estructura que nos permite definir y tomar varias variables
int out = 2; // definicion de salida
int S0 = 3;
int S1 = 4;
int S2 = 5;
int S3 = 6;
int OE = 7; // salida habilitada
int LED = 8; // pulso de activacion de un led para deteccion de color(opcional)

int LT = 13; // indicador de adquisicion de color segun codificacion RGB
int sombras_nb = 6;
double sombras_val[] = {30.0,60.0,120.0,180.0,240.0,330.0};
char* sombras_id[] = {"rojo","amarillo","verde","cyan","azul","magenta"};

void setup()
{

 lcd.begin(16,2); 
 for(int i = 0; i< 3; i++)
 {
  lcd.backlight();
  delay(250);
  lcd.noBacklight();
  delay(250);
  }
lcd.backlight(); // finish with backlight on ñ

TCS3210setup();//salta a configurar el sensor 
Serial.begin( 115200 );
while (!Serial); // Wait for serial port to connect - used on Leonardo, Teensy and other boards with built-in USB CDC serial connection
Serial.println("Start");
pinMode(Rele_Combine, OUTPUT);
pinMode(Rele_Gdrive, OUTPUT);
pinMode(Rele_Ford, OUTPUT);


if (Usb.Init() == -1)
    Serial.println("OSC did not start.");

delay( 200 );

next_time = millis() + 10000;

HidKeyboard.SetReportParser(0, (HIDReportParser*)&Prs);
}
int ford=0;
int gdrive=0;
double red;
double blue;
double green;
char sensor_color;
int contador=0;

void loop()
{
struct TSL tsl = detectorColor();//toma los datos de la funcion de los colores y lo almacena en struct
double t = tsl.t;
char salida=0;
double red = colorRead(out,1,1);
double blue = colorRead(out,2,1);
double green = colorRead(out,3,1);
sensor_color = EEPROM.read(0);

ford=0;
gdrive=0;

while(i<15)
{
Usb.Task();
}
/*
if (i<15 || i>15 )
{
contador++;

}
if (contador==1000)
{
Serial.print("comprobando");
Usb.Init();
i=0;
contador=0;
Motor = "000000000000000";
Motor_C13="00000";
}
*/
if (i>14)
{
j=0;

    if (Motor==Ford) 
    {
    pinMode(Rele_Ford, HIGH);
    pinMode(Rele_Gdrive, LOW);
    pinMode(Rele_Combine, LOW);
    Serial.print("SENSOR ROJO");
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("SENSOR ROJO C10");
    lcd.setCursor(0,1);
    lcd.print("CAJON 1 CURSOR10");
      
         
      while (salida==0)
      {
      double red = colorRead(out,1,1); 
      double blue = colorRead(out,2,1); 
      double green = colorRead(out,3,1);
      
        if (sensor_color==1) 
          {
          if (red > 225 && red < 270 && blue > 250 && blue < 380 && green >281  && green < 373) 
            {
            salida=1;  
            pinMode(Rele_Ford, LOW);
            lcd.setCursor(0,0);
            lcd.print("SENSOR CORRECTO");
            }
          }
           if (sensor_color==0) 
             {
              delay( 5000); 
              salida=1;
              ford=1;
              gdrive=0;
              pinMode(Rele_Ford, LOW);
             }
       }
          
       ford=1;
       gdrive=0;
      }
  
        if (Motor=="000005043620520" || Motor=="000005043620530" || Motor=="000005043620540" || Motor== "000005043620550" || Motor == "000005801394811" || Motor == "000005801394812" || Motor == "000005801394816" || Motor == "000005043689840" || Motor == "000005801394820" ) 
        {
          pinMode(Rele_Gdrive, HIGH);
          pinMode(Rele_Ford, LOW);
          pinMode(Rele_Combine, LOW);
          Serial.print("SENSOR MARRON");
          lcd.clear(); 
          lcd.print("SENSOR MARRON");
          lcd.setCursor(0,1);
          lcd.print("CAJON 2 G.DRIVE");
          
          
              while (salida==0)
                  {  
                  double red = colorRead(out,1,1); 
                  double blue = colorRead(out,2,1); 
                  double green = colorRead(out,3,1); 
                      
                      if (sensor_color==1) 
                          {
                            
                          if (red > 386 && red < 403 && blue > 331 && blue < 358 && green > 405 && green < 426) 
                            {
                            pinMode(Rele_Gdrive, LOW);
                            lcd.setCursor(0,0);
                            lcd.print("SENSOR CORRECTO");
                            gdrive=1;
                            ford=0;
                            salida=1; 
                            }
                          }
                       if (sensor_color==0) 
                          {
                           delay( 5000); 
                           salida=1;
                           gdrive=1;
                           ford=0; 
                           pinMode(Rele_Gdrive, LOW);
                          }
                   }
             
                  salida=0;
          }
                    if (Motor=="000000000000000" ) 
                         {
                          lcd.setCursor(0,0); 
                          lcd.print("SENSOR DE COLOR"); 
                          lcd.setCursor(0,1);
                          lcd.print("ELIMINADO"); 
                          delay(2500);
                          lcd.clear(); 
                          EEPROM.write(0, 0);
                          
                          sensor_color=0; 
                          ford=1;
                          gdrive=1;
                         } 
                         
                      if (Motor=="111111111111111" ) 
                         {
                          lcd.setCursor(0,0); 
                          lcd.print("SENSOR DE COLOR"); 
                          lcd.setCursor(0,1);
                          lcd.print("AGREGADO"); 
                          delay(2500);
                          lcd.clear(); 
                          EEPROM.write(0, 1);
                          
                          sensor_color=1; 
                          ford=1;
                          gdrive=1;
                         }

                    Usb.Task();
                   
                    if(ford==0 && gdrive==0)
                        {
                    if ( Motor_C13=="50437" || Motor_C13=="58013" || Motor_C13=="50438" || Motor_C13=="50432" || Motor_C13=="50415" || Motor_C13=="50425" || Motor_C13=="00952" || Motor_C13=="09520" || Motor_C13=="58014" || Motor_C13=="58016" || Motor_C13=="58015" || Motor_C13=="58017")
                        { 
                                                          
                         pinMode(Rele_Combine, HIGH);
                         pinMode(Rele_Ford, LOW);
                         pinMode(Rele_Gdrive, LOW);
                         Serial.print("SENSOR AMARILLO");
                         lcd.clear(); 
                         lcd.setCursor(0,0);
                         lcd.print("SENSOR AMARILLO");
                         lcd.setCursor(0,1);
                         lcd.print("CAJON 3 C13");
                                                    
                               while (salida==0)
                                   {
                                    double red = colorRead(out,1,1); 
                                    double blue = colorRead(out,2,1); 
                                    double green = colorRead(out,3,1); 
                                    
                                    if (sensor_color==1) 
                                      {
                                        if (red > 162 && red < 182 && blue > 221 && blue < 246 && green > 196 && green < 212 ) 
                                          {
                                           lcd.clear();
                                           pinMode(Rele_Combine, LOW);
                                           lcd.setCursor(0,0);
                                           lcd.print("SENSOR CORRECTO");
                                           ford=0;
                                           gdrive=1;
                                           salida=1; 
                                          }
                                      }
                                     if (sensor_color==0) 
                                      {
                                      delay( 5000 );  
                                      salida=1;
                                      ford=0;
                                      gdrive=1;
                                      pinMode(Rele_Combine, LOW); 
                                      }
                                    }    
                                  
                                     salida=0;
                            }
                             
                            }
                                               


         i=0;
         Motor_C13="00000";
          
  }
}

// comando para detectar si hay color alfrente del sensor.
bool isPresent()
{
//se cataloga el grado de luminosidad segun la hoja de datos
//para determinar el grado de luminosdad o oscuridad
double isPresentTolerance = 2;
// número se hace grande cuando algo está en frente del sensor.
double isPresent = colorRead(out,0,0)/colorRead(out,0,1);

if(isPresent < isPresentTolerance){
//Serial.println("no hay nada en frente del sensor");
return false;
}
return true;
}

// retorna TSL segun color detectado
//
struct TSL detectorColor(){
struct TSL tsl;
double white = colorRead(out,0,1);//out es el pin 2 del arduino
double red = colorRead(out,1,1);
double blue = colorRead(out,2,1);
double green = colorRead(out,3,1);
Serial.print("datos capturados blanco : "); Serial.println(white);
Serial.print("datos capturados R : "); Serial.println(red);
Serial.print("datos capturados G : "); Serial.println(green);
Serial.print("datos capturados B : "); Serial.println(blue);Serial.println();

double r,v,b; // r,v,b € [0..1]
double offset = 3.0/white; // compensacion de luminosidad
//r=min(1.0, offset+(white/red));
//v=min(1.0, offset+(white/green));
//b=min(1.0, offset+(white/blue));
//Serial.print(" Rojo : "); Serial.println( r);
//Serial.print(" verde : "); Serial.println( v);
//Serial.print(" azul : "); Serial.println( b);
//Serial.println();

// transformacion RVB -> TSL
// r,v,b € [0..1]
// t € [0°..360°]; s,l € [%]
double t,s,l;

double maxRVB = max(max(r, b),v);
double minRVB = min(min(r, b),v);
double delta = maxRVB-minRVB;
double somme = maxRVB+minRVB;

// luminancia basada en valores de colores segun software de diseño grafico
l=(somme/2.0);

if(delta==0.0) // gris
{
t=s=0.0;
}else{
//saturacion
if ( l < 0.5 ) s = delta / somme;
else s = delta / ( 2.0 - delta );

// tinte
double del_R = ( ( ( maxRVB - r ) / 6.0 ) + ( delta / 2.0 ) ) / delta;
double del_G = ( ( ( maxRVB - v ) / 6.0 ) + ( delta / 2.0 ) ) / delta;
double del_B = ( ( ( maxRVB - b ) / 6.0 ) + ( delta / 2.0 ) ) / delta;

if ( r == maxRVB ) t = del_B - del_G;
else if ( v == maxRVB ) t = ( 1.0 / 3.0 ) + del_R - del_B;
else if ( b == maxRVB ) t = ( 2.0 / 3.0 ) + del_G - del_R;
if ( t < 0 ) t += 1.0;
if ( t > 1 ) t -= 1.0;
}

// normalizacion
t*=360.0; // [0°..360°]
s*=100.0; // [%]
l*=100.0; // [%]
tsl.t=t; tsl.s=s; tsl.l=l;

//Serial.print(" tinte [0..360]: "); Serial.println(t);
//Serial.print(" saturacion [%]: "); Serial.println(s);
//Serial.print(" luminancia [%]: "); Serial.println(l);
return tsl;
}

/***
Este método devolverá el valor medido por el sensor seleccionado. Dado que la frecuencia
es proporcional a la intensidad de la luz del filtro de color seleccionado, el valor más pequeño es
(Cerca de 10 con setMode (2)), además de que es la luz.
params:
outPin: pin de salida
color: 0 = blanco, 1 = rojo, 2 = azul, verde = 3
Los LEDs se encienden LEDstate.
*/
double colorRead(int outPin, int color, boolean LEDstate){
setMode(2); // frecuencia max 100Khz => 10us
//sélectiona el filtro
if(color == 0){//clear
digitalWrite(S3, LOW); //S3
digitalWrite(S2, HIGH); //S2
}else if(color == 1){//red
digitalWrite(S3, LOW); //S3
digitalWrite(S2, LOW); //S2
}else if(color == 2){//blue
digitalWrite(S3, HIGH); //S3
digitalWrite(S2, LOW); //S2
}else if(color == 3){//green
digitalWrite(S3, HIGH); //S3
digitalWrite(S2, HIGH); //S2
}

if(LEDstate){
digitalWrite(LED, HIGH);

digitalWrite(LT, HIGH);
}else{
digitalWrite(LED, LOW);
digitalWrite(LT, LOW);
}

//tiempo para que el sensor tome su lectura y se estabilice
int sensorDelay = 10; //ms
// lee el pulso
double readPulse;
delay(sensorDelay);
readPulse = pulseIn(outPin, LOW, 25000000);
//si el tiempo es mayor de lo que lee pulsein regresa cero
if(readPulse < .1){ readPulse = 25000000; }

//descativa el sensor para ahorrar energia
setMode(0);
return readPulse;
}

/*** selecciona el modo
*/
void setMode(int mode){
if(mode == 0){
//power OFF
digitalWrite(LED, LOW);
digitalWrite(LT, LOW);
digitalWrite(S0, LOW); //S0
digitalWrite(S1, LOW); //S1
}else if(mode == 1){
//escala 1:1
digitalWrite(S0, HIGH); //S0
digitalWrite(S1, HIGH); //S1
}else if(mode == 2){
//escala 1:5
digitalWrite(S0, HIGH); //S0
digitalWrite(S1, LOW); //S1
}else{ //if(mode == 3)
//escala 1:50
digitalWrite(S0, LOW); //S0
digitalWrite(S1, HIGH); //S1
}
}
///////////////////funcion de configuracion de pines/////////////////
void TCS3210setup(){
//configuracion de frecuencia en el setup estan los pines respectivos
pinMode(S0,OUTPUT);
pinMode(S1,OUTPUT);
//seleccion de color
pinMode(S2,OUTPUT);
pinMode(S3,OUTPUT);
//salida
pinMode(out, INPUT);
//habilita la salida
pinMode(OE,OUTPUT); //habilita el modulo de luz
// activa el led
pinMode(LED,OUTPUT);
// halita la deteccion de color
pinMode(LT,OUTPUT);
}
