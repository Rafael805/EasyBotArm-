
```c++
#include <Servo.h>

//Constants
const int startBT = 7;
const int teachBT = 6;
const int potForwardBackward = A0;
const int potUpDown = A1;
const int potGrab = A2;
const int potRotate = A3;
int servoBottomval;
int servoLeftval;
int servoRightval;
int servoClawval;

//Max and Min values for servos ! Change them to meet your setup !
const int minGrab= -40;
const int maxGrab= 80;
const int minRotate= 100;
const int maxRotate= 0;
const int minUpDown= 170;
const int maxUpDown= 0;
const int minForwardBackward= 40;
const int maxForwardBackward= 160;

//Variables
int readUpDown,readForwardBackward,readRotate,readGrab,readTeach,readStart;
int teachUpDown[100],teachForwardBackward[100],teachRotate[100],teachGrab[100];
boolean started = false;
int index = 1;
int stepSpeed = 9; // Speed

//Create servo objects to control servo motors
Servo up_down;  
Servo forward_backward;
Servo rotate;
Servo grab;

void setup() {

  Serial.begin(9600);
  //Attach Servo motors
  forward_backward.attach(8);
  up_down.attach(9);
  grab.attach(10); 
  rotate.attach(11); 

  //Inputs-Outputs
  pinMode(teachBT, INPUT_PULLUP);
  pinMode(startBT, INPUT_PULLUP);
  readInputs();
 
}

void loop() {
  if (!started){
    readInputs();
    moveServos();
    if (readTeach==LOW){
      savePosition();
      delay(500);
    }
    if (readStart==LOW){
      started=true;
      delay(1000);
    }
  }
  else{
    runTeach();
  }
}

void readInputs(){
  //Read Analog Joysticks
  readUpDown = analogRead(potUpDown);
  readUpDown = map(readUpDown,0,1023,minUpDown,maxUpDown);
  readForwardBackward = analogRead(potForwardBackward);
  readForwardBackward = map(readForwardBackward,0,1023,minForwardBackward,maxForwardBackward);
  readRotate = analogRead(potRotate);
  readRotate = map(readRotate,0,1023,minRotate,maxRotate);
  readGrab = analogRead(potGrab);
  readGrab = map(readGrab,0,1023,minGrab,maxGrab);
  //Read buttons
  readTeach = digitalRead(teachBT);
  readStart = digitalRead(startBT);
  delay(50);
}

void moveServos(){
  up_down.write(readUpDown);
  forward_backward.write(readForwardBackward);
  rotate.write(readRotate);
  grab.write(readGrab);
}

void savePosition(){
  teachUpDown[index] = readUpDown;
  teachForwardBackward[index] = readForwardBackward;
  teachRotate[index] = readRotate;
  teachGrab[index] = readGrab;
  index++;
}

void runTeach(){
  for (int i=0; i<index-1; i++){
    if (teachRotate[i] < teachRotate[i+1]){
      for (int j = teachRotate[i]; j<= teachRotate[i+1]; j++){
        rotate.write(j);
        delay(stepSpeed);
      }
    }
    else if (teachRotate[i] > teachRotate[i+1]){
      for (int j = teachRotate[i]; j>= teachRotate[i+1]; j--){
        rotate.write(j);
        delay(stepSpeed);
      }  
    }
    else{
      rotate.write(teachRotate[i]);
    }
    if (teachGrab[i] < teachGrab[i+1]){
      for (int j = teachGrab[i]; j<= teachGrab[i+1]; j++){
        grab.write(j);
        delay(stepSpeed);
      }
    }
    else if (teachGrab[i] > teachGrab[i+1]){
      for (int j = teachGrab[i]; j>= teachGrab[i+1]; j--){
        grab.write(j);
        delay(stepSpeed);
      } 
    }
    else{
      grab.write(teachGrab[i]);
    }
    if (teachForwardBackward[i] < teachForwardBackward[i+1]){
      for (int j = teachForwardBackward[i]; j<= teachForwardBackward[i+1]; j++){
        forward_backward.write(j);
        delay(stepSpeed);
      }
    }
    else if (teachForwardBackward[i] > teachForwardBackward[i+1]){
      for (int j = teachForwardBackward[i]; j>= teachForwardBackward[i+1]; j--){
        forward_backward.write(j);
        delay(stepSpeed);
      }
    }
    else{
      forward_backward.write(teachForwardBackward[i]);
    }
    if (teachUpDown[i] < teachUpDown[i+1]){
      for (int j = teachUpDown[i]; j<= teachUpDown[i+1]; j++){
        up_down.write(j);
        delay(stepSpeed);
      }
    }
    else if (teachUpDown[i] > teachUpDown[i+1]){
      for (int j = teachUpDown[i]; j>= teachUpDown[i+1]; j--){
        up_down.write(j);
        delay(stepSpeed);
      }
    }
    else{
      up_down.write(teachUpDown[i]);
    }
  }
  started=false;
}
```
