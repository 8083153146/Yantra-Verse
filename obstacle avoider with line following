#include <Ultrasonic.h>

#define S1 2   // 5-segment IR sensor array
#define S2 3
#define S3 4
#define S4 5
#define S5 6

#define enableRightMotor 7   // right connector motor
#define rightMotorPin1 8 
#define rightMotorPin2 9

#define enableLeftMotor 10   // left connector motor
#define leftMotorPin1 11   
#define leftMotorPin2 12

#define MOTOR_SPEED 180

#define trigPin A1   // ultrasonic sensor
#define echoPin A2

int count = 0;
int table_no = 1;
int total_tables = 3;
long duration, obstacle;

void setup() 
{
  //The problem with TT gear motors is that, at very low pwm value it does not even rotate.
  //If we increase the PWM value then it rotates faster and our robot is not controlled in that speed and goes out of line.
  //For that we need to increase the frequency of analogWrite.
  //Below line is important to change the frequency of PWM signal on pin D5 and D6
  //Because of this, motor runs in controlled manner (lower speed) at high PWM value.
  //This sets frequecny as 7812.5 hz.
  TCCR0B = TCCR0B & B11111000 | B00000010;

  pinMode(S1, INPUT);
  pinMode(S2, INPUT);
  pinMode(S3, INPUT);
  pinMode(S4, INPUT);
  pinMode(S5, INPUT);

  pinMode(enableRightMotor, OUTPUT);
  pinMode(rightMotorPin1, OUTPUT);
  pinMode(rightMotorPin2, OUTPUT);
  
  pinMode(enableRightMotor, OUTPUT);
  pinMode(leftMotorPin1, OUTPUT);
  pinMode(leftMotorPin2, OUTPUT);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  Serial.begin(9600);

  rotateMotor(0, 0);
}

void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)   // to rotate the motors
{
  
  if (rightMotorSpeed < 0)
  {
    digitalWrite(rightMotorPin1, LOW);
    digitalWrite(rightMotorPin2, HIGH);    
  }
  else if (rightMotorSpeed > 0)
  {
    digitalWrite(rightMotorPin1, HIGH);
    digitalWrite(rightMotorPin2, LOW);      
  }
  else
  {
    digitalWrite(rightMotorPin1, LOW);
    digitalWrite(rightMotorPin2, LOW);      
  }

  if (leftMotorSpeed < 0)
  {
    digitalWrite(leftMotorPin1, LOW);
    digitalWrite(leftMotorPin2, HIGH);    
  }
  else if (leftMotorSpeed > 0)
  {
    digitalWrite(leftMotorPin1, HIGH);
    digitalWrite(leftMotorPin2, LOW);      
  }
  else 
  {
    digitalWrite(leftMotorPin1, LOW);
    digitalWrite(leftMotorPin2, LOW);      
  }

  analogWrite(enableRightMotor, abs(rightMotorSpeed));
  analogWrite(enableLeftMotor, abs(leftMotorSpeed));    
}

void table_count(int table)   // to stop at the table whose table number is specified
{   
  if (count > total_tables)
  {
    rotateMotor(0, 0);   // stop at start position
    delay(10000);
    count = 0;
  }

  if (count == table) 
  {
    rotateMotor(0, 0);   // stop at table number specified
    delay(10000);
  }
  
  rotateMotor(MOTOR_SPEED, MOTOR_SPEED);
}

long calc_dist(long x)   // to calculate distance or location of the obstacle in front of the robot
{   
  long distance = x * 0.0343 / 2;
  delay(10);

  return distance;
}

void loop() 
{
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  duration = pulseIn(echoPin, HIGH);
  
  obstacle = calc_dist(duration);
  Serial.println(obstacle);
  
  if ( digitalRead(S2) == HIGH && digitalRead(S4) == HIGH )  
  {
    if (obstacle>10) 
    {
      rotateMotor(MOTOR_SPEED, MOTOR_SPEED);   // move forward when sensors 2 and 4 do not detect black line
    }
    else 
    {
      rotateMotor(0, 0);
    }
  }

  if ( digitalRead(S2) == LOW && digitalRead(S4) == HIGH )  
  {
    rotateMotor(-MOTOR_SPEED, MOTOR_SPEED);   // turn left when sensor 2 detect black line
  }

  if ( digitalRead(S2) == HIGH && digitalRead(S4) == LOW )  
  {
    rotateMotor(MOTOR_SPEED, -MOTOR_SPEED);   // turn right when sensor 4 detect black line
  }

  if ( digitalRead(S1) == LOW && digitalRead(S5) == LOW )  
  {
    count++;   // when sensors 1 and 5 detect black line
    table_count(table_no);   // stop at table number specified for 10 seconds
  }
}
