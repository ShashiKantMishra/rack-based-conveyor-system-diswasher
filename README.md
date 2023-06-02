# rack-based-conveyor-system-diswasher
// Define ultrasonic sensor pins
const int sensor1_trigger_pin = 12;
const int sensor1_echo_pin = 13;
const int sensor2_trigger_pin = 2;
const int sensor2_echo_pin = 4;

// Define relay pins
const int relay1_pin = 9;
const int relay2_pin = 11;
const int LED1 = 10;
const int LED2 =8;
const int LED3 = 7;
const int LED4 = 6;

void setup() {
  // Initialize sensor pins
  pinMode(sensor1_trigger_pin, OUTPUT);
  pinMode(sensor1_echo_pin, INPUT);
  pinMode(sensor2_trigger_pin, OUTPUT);
  pinMode(sensor2_echo_pin, INPUT);
  
  // Initialize relay pins
  pinMode(relay1_pin, OUTPUT);
  pinMode(relay2_pin, OUTPUT);
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);
  pinMode(LED3, OUTPUT);
  pinMode(LED4, OUTPUT);
  
  // Start with both relays off
  digitalWrite(relay1_pin, HIGH); // initial condition of motor for operating the diswasher. motor controll circuit is normally open so HIGH command is written.
  digitalWrite(relay2_pin, LOW); // initial condition of solenoid valve for ready to wash the disc. solenoid valve is the normally closed so LOW command is written.
  digitalWrite(LED1, LOW); // the LED1 is "OFF".
  digitalWrite(LED2, LOW); // the LED2 is "OFF".
  digitalWrite(LED3, LOW); // the LED3 is "OFF".
  digitalWrite(LED4, HIGH); // indication light  for the system is ready or not.
}

void loop() {
  // Read sensor 1
  long duration1, distance1;
  digitalWrite(sensor1_trigger_pin, LOW);
  delayMicroseconds(2);
  digitalWrite(sensor1_trigger_pin, HIGH);
  delayMicroseconds(10);
  digitalWrite(sensor1_trigger_pin, LOW);
  duration1 = pulseIn(sensor1_echo_pin, HIGH);
  distance1 = duration1 / 58;
  
  // Read sensor 2
  long duration2, distance2;
  digitalWrite(sensor2_trigger_pin, LOW);
  delayMicroseconds(2);
  digitalWrite(sensor2_trigger_pin, HIGH);
  delayMicroseconds(10);
  digitalWrite(sensor2_trigger_pin, LOW);
  duration2 = pulseIn(sensor2_echo_pin, HIGH);
  distance2 = duration2 / 58;
  
  // Check sensor 1 reading
  if (distance1 >= 10 && distance1 <= 40) {
    // Turn on relay 1 after 6 seconds.
    digitalWrite(LED1, HIGH); // indication for sensing the ultrasonic sensor 1.
    digitalWrite(LED4, LOW); // the indication light for the system is "OFF". and continue to further process.
    delay(6000);  // waiting delay in the loading area to start the motor in the safe manner.
    digitalWrite(relay1_pin, LOW); // motor will be start after 6 second.
    digitalWrite(relay2_pin, LOW); // solenoid valve is closed.
    digitalWrite(LED1, HIGH);
    digitalWrite(LED2, HIGH);
    digitalWrite(LED3, LOW);
    // Turn on relay 2 after 3 seconds and turn off relay 1.
    delay(6000); // the time taken is required by the motor to reach the tray at the wash area.
    digitalWrite(relay2_pin, HIGH); // motor will be stop at the wash area.
    digitalWrite(relay1_pin, HIGH); // solenoid valve is opened to wash the disc.
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, HIGH);
    digitalWrite(LED3, HIGH);
    // Turn off both relays after 21 seconds.
    delay(15000); // wash time 15 second (4 second for soapy water then the normal water will be sprayed with high jet pressure).
    digitalWrite(relay1_pin, LOW); // motor will be start after washing the disc.
    digitalWrite(relay2_pin, LOW); // solenoid valve is closed after washing the disc.
    digitalWrite(LED1, LOW);
    digitalWrite(LED2, LOW);
    digitalWrite(LED3, HIGH);
  }
  // Check sensor 2 reading
  if (distance2 >= 10 && distance2 <= 40) {
    // Do nothing, leave both relays off.
    digitalWrite(LED3, LOW); // indication light for the second ultrasonic sensor sensing or not  . 
    delay(1800); // delay for stop the tray after sensing the tray.
    digitalWrite(relay1_pin, HIGH); // motor will be stop at the unloading zone.
    digitalWrite(relay2_pin, LOW); // solenoid valve is closed.
    digitalWrite(LED1, HIGH); // indication light for the motor or solenoid valve is in "OFF" condition.
    digitalWrite(LED2, HIGH); // the all three LEDs are glow at the same time.
    digitalWrite(LED3, HIGH); //these three LEDs are glow until the first senser sense the tray.
  //{
    //return;// it return the loop without breaking the loop.
  //}
  }
}// the whole loop will be continue after the loop is completed.
