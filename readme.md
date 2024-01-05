Hier eine [Beispielseite](https://robocraze.com/blogs/post/interfacing-ultrasonic-sensor-with-raspberry-pi-4-gpio) für Verschaltung und den Python Code.
Der Python Code ist aber [hier](https://forums.raspberrypi.com/viewtopic.php?t=233824) besser gemacht, nicht besser erklärt.

# Vorbereitung
Um den Sensor nutzen zu können muss eine Schaltung gebaut werden um das Eingangssignal von 5V auf 3,3V runter zu regeln, da ansonsten Schaden am RasPi-Pin entstehen kann.

# Schaltplan
Hier der Schaltplan dazu, der im [Online](https://www.circuit-diagram.org/editor/) erstellt wurde:

![[circuit_hc-sr04_raspi.png]]

# Breadboard Schaltung

Hier die Schaltung auf dem Breadboard zusammen mit dem Raspi:
![[breadbord_hs-sr04_raspi.jpg]]

# Python Code

Sobald das Programm ausgeführt wird entsteht eine unendliche while-Schleife.
Durch diese Schleife wird im Sekundentakt eine Messung vorgenommen und wieder ausgegeben. Den Sleep-Timer kann man entsprechend modifzieren um ein schnelleres, oder langsameres Intervall einzustellen.

```python
# Import required Python libraries
import time
import RPi.GPIO as GPIO

# Use BCM GPIO references
# instead of physical pin numbers
GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)

# Define GPIO to use on Pi
GPIO_TRIGGER = 23
GPIO_ECHO = 24

# Set pins as output and input
GPIO.setup(GPIO_TRIGGER,GPIO.OUT) # Trigger
GPIO.setup(GPIO_ECHO,GPIO.IN) # Echo

# Set trigger to False (Low)
GPIO.output(GPIO_TRIGGER, False)

while True:
	print("Ultrasonic Measurement")

	# Allow module to settle
	time.sleep(1)
	
	# Send 10us pulse to trigger
	GPIO.output(GPIO_TRIGGER, True)
	time.sleep(0.00001)
	GPIO.output(GPIO_TRIGGER, False)
	start = time.time()
	
	while GPIO.input(GPIO_ECHO)==0:
		start = time.time()
	
	while GPIO.input(GPIO_ECHO)==1:
		stop = time.time()
		
	# Calculate pulse length
	elapsed = stop - start
	
	# Distance pulse travelled in that time is time
	# multiplied by the speed of sound (cm/s)
	distancet = elapsed * 34300
	
	# That was the distance there and back so halve the value
	distance = round(distancet / 2, 2)
	
	print ("Distance :", distance, " cm")
```

