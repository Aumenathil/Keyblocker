# Keyblocker
Used to block unintentional key strokes using global python commands.
# The below is Python code used to block key strokes after an inactivity period.
# Take the time to import keyboard, mouse, time, threading and sys to your Python setup.

import keyboard
import mouse
import time
import threading
import sys

# Global state
last_press_time = time.time()
typing_disabled = False
alphabet = "abcdefghijklmnopqrstuvwxyz"

def on_key(e):
    global last_press_time
    # Update the inactivity timer for every key press
    last_press_time = time.time()

# Register a handler for all key presses
keyboard.on_press(on_key)

def check_inactivity():
    global last_press_time, typing_disabled
    while True:
        time.sleep(0.1)
        if not typing_disabled and (time.time() - last_press_time > 3):
            typing_disabled = True
            # Block each alphabet key
            for letter in alphabet:
                keyboard.block_key(letter)
            print("Keyboard disabled due to inactivity. Double-click to resume.")

def resume_typing():
    global typing_disabled, last_press_time
    if typing_disabled:
        for letter in alphabet:
            keyboard.unblock_key(letter)
        typing_disabled = False
        last_press_time = time.time()
        print("Keyboard resumed via double-click.")

def exit_program():
    print("Exiting.")
    sys.exit(0)

# Use the mouse module to detect a double-click anywhere
mouse.on_double_click(lambda: resume_typing())

# Set Escape key to exit the program
keyboard.add_hotkey("esc", exit_program)

# Start the inactivity checker in a background thread
threading.Thread(target=check_inactivity, daemon=True).start()

# Wait indefinitely (global hooks keep running)
keyboard.wait()
