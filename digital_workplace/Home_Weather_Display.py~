#!/usr/bin/env python

# Home_Weather_Display.py
#
# This is an project for using the Grove RGB LCD Display and the Grove DHT Sensor from the GrovePi starter kit
# 
# In this project, the Temperature and humidity from the DHT sensor is printed on the RGB-LCD Display
#
#
# Note the dht_sensor_type below may need to be changed depending on which DHT sensor you have:
#  0 - DHT11 - blue one - comes with the GrovePi+ Starter Kit
#  1 - DHT22 - white one, aka DHT Pro or AM2302
#  2 - DHT21 - black one, aka AM2301
#
# For more info please see: http://www.dexterindustries.com/topic/537-6c-displayed-in-home-weather-project/
#
'''
The MIT License (MIT)

GrovePi for the Raspberry Pi: an open source platform for connecting Grove Sensors to the Raspberry Pi.
Copyright (C) 2017  Dexter Industries

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
'''

import time

import grovepi
import psycopg2 as ps
#import grove_sound_sensor

from grove_rgb_lcd import *


########### FUNCTION DEFINITIONS

#def create_record(database, table, record)

def get_sensor_value(mode, port):
	value = 0
	if mode == "digital" or mode == "d":
		value = grovepi.digitalRead(port)  
	elif mode == "analog" or mode == "analogue" or mode == "a":
		value = grovepi.analogRead(port)
	elif mode == "dht":
		# change this depending on your sensor type - see header comment
		dht_sensor_type = 0
		#Get the temperature and Humidity from the DHT sensor      
		value = grovepi.dht(port, dht_sensor_type)
	else:
		print("The mode you provided is not supported. Try 'a' for analog or 'd' for digital, or 'dht' for digital humidity temperature sensor")   
	return value

def write_to_lcd(color_array, message):
	setRGB(color_array[0],color_array[1],color_array[2])
	setText(message)
	
def blink_led(value, threshold_value):
	if value > threshold_value:
		grovepi.digitalWrite(led,1)
	else:
		grovepi.digitalWrite(led,0)

########## PORT DEFINITIONS

dht_sensor_port = 7		# Connect the DHt sensor to port 7

sound_sensor = 0 # Connect the Grove Sound Sensor to analog port A0

led = 5 # Connect the Grove LED to digital port D5


########## INITIALIZATION

grovepi.pinMode(sound_sensor, "INPUT")
grovepi.pinMode(led, "OUTPUT")

# The threshold to turn the led on threshold_value * 5 / 1024 = 1.95v
threshold_value = 100

########## MAIN LOOP


try:
	conn = ps.connect(dbname='template1', user='postgres', host='127.0.0.1', password='bigFGbigdata')
	
	while True:
		try:
			# Read the sound level
			sensor_value = get_sensor_value("analog", sound_sensor)
			# If loud, illuminate LED, otherwise dim
			blink_led(sensor_value, threshold_value)
   
   		# Here is the code for temperature and humidity
			[ temp, hum ] = get_sensor_value("dht", dht_sensor_port)
			color_array = [255, 255, 255]
			message = "Temp:" + str(temp) + "C      " + "Humidity :" + str(hum) + "%"
			print(message)
			write_to_lcd(color_array, message)
			
			time.sleep(.5)
		except (IOError,TypeError) as e:
			print("Error")
except (ps.DatabaseError) as e:
	print("Error found:  "+ str(e)) 
finally:
	conn.close()
