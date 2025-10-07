# UART Chat Between Two Raspberry Pi Picos
# Both Picos must run this same code

from machine import UART, Pin
import time

# --- Setup UART (communication) ---
# TX = GP8, RX = GP9, baudrate = 9600 (speed)
uart = UART(1, baudrate=9600, tx=Pin(8), rx=Pin(9))

print("UART chat started! Type a message and press Enter.")

# --- Keep-alive timer setup ---
last_send_time = time.time()
keep_alive_interval = 5  # seconds between "still here" messages

while True:
    # 1️⃣ Check if the other Pico sent a message
    if uart.any():
        incoming = uart.readline()
        if incoming:
            print("Received:", incoming.decode().strip())

    # 2️⃣ Ask for your own message
    msg = input("You: ")
    if msg:
        uart.write(msg + "\n")
        last_send_time = time.time()  # reset timer when you send a message

    # 3️⃣ If no message sent recently, send a "still here" message
    if time.time() - last_send_time > keep_alive_interval:
        uart.write("[still here]\n")
        last_send_time = time.time()

