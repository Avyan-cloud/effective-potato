# effective-potato
from machine import Pin
from time import sleep

# Pin configuration
ir_sensor = Pin(1, Pin.IN)  # IR sensor pin
button1 = Pin(2, Pin.IN)  # Button 1 (Entrance)
button2 = Pin(3, Pin.IN)  # Button 2 (Exit)
green_led = Pin(4, Pin.OUT)  # Green LED
red_led = Pin(5, Pin.OUT)  # Red LED

capacity = 0
current_count = 0

while capacity <= 0:
  print("Please enter the event hall capacity: ")
  capacity = int(input())
  if capacity <= 0:
      print("Capacity must be a positive number. Please try again.")

print("Event hall capacity is set to", capacity)
print("Visitor Counter is ready!")
print("Press Button 1 for Entrance Mode")
print("Press Button 2 for Exit Mode")

mode = "Entrance"
printed_entrance_msg = False
printed_exit_msg = False
printed_full_msg = False
printed_empty_msg = False

while True:
  # Mode switching
  if button1.value() == 1:
      if mode != "Entrance":
          print("Switching to Entrance Mode.")
          mode = "Entrance"
          green_led.off()
          red_led.off()

  if button2.value() == 1:
      if mode != "Exit":
          print("Switching to Exit Mode.")
          mode = "Exit"
          green_led.off()
          red_led.off()

  # Entrance mode
  if mode == "Entrance":
      if not printed_entrance_msg:
          print("Entrance Mode: People entering.")
          printed_entrance_msg = True  # Print only once

      if current_count < capacity and ir_sensor.value() == 1:
          current_count += 1
          print("Current count:",current_count, "/", capacity)
          green_led.on()
          sleep(0.1)
          green_led.off()
          sleep(0.1)
          printed_full_msg = False  # Reset in case previously full

      if current_count >= capacity and not printed_full_msg:
          print("Event hall is full!")
          green_led.off()
          printed_full_msg = True

  # Exit mode
  elif mode == "Exit":
      if not printed_exit_msg:
          print("Exit Mode: People exiting.")
          printed_exit_msg = True  # Print only once

      if current_count > 0 and ir_sensor.value() == 1:
          current_count -= 1
          print("Current count:", current_count, "/", capacity)
          red_led.on()
          sleep(0.1)
          red_led.off()
          sleep(0.1)
          printed_empty_msg = False  # Reset in case previously empty

      if current_count == 0 and not printed_empty_msg:
          print("The event hall is empty!")
          red_led.off()
          printed_empty_msg = True

  sleep(0.1)

