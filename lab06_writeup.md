# Lab 06 Writeup – GrovePi Sensors

## Names:  
- Ardaan Bhatia (ardaanbh@usc.edu)  git: ardaanbhatia7 
- Kyna Rochlani (rochlani@usc.edu) git: kynarochlani2006

---

## 4.1

Suppose you just cloned a repository that included one Python file `my_first_file.py`, and you now want to add a second file `my_second_file.py` that contains the following code:
```python
print("Hello World")
```
### 1. Clone the repository
git clone git@github.com:my-name/my-imaginary-repo.git

### 2. Move into the repository directory
cd my-imaginary-repo

### 3. Create a new Python file
touch my_second_file.py

### 4. Add the code to the file
echo 'print("Hello World")' > my_second_file.py

### 5. Stage the new file
git add my_second_file.py

### 6. Commit the change
git commit -m "Add my_second_file.py with Hello World print statement"

### 7. Push the change to GitHub
git push origin main


## 4.2
For this lab, one partner connected to the Raspberry Pi using SSH and handled sensor testing and debugging, while the other partner simultaneously edited the code using vim on their local machine. The updated code was pushed to the shared GitHub repository, allowing the us to pull the latest version directly to the Pi for testing. This setup allowed both of us to work concurrently and not having to edit on rpi. To improve efficiency in future labs:
- Using VS Code Remote SSH could streamline live editing and testing on the Raspberry Pi.
- Learning more advanced Git commands and branch management would help both partners commit and merge code without conflicts.
- Automating repetitive Git operations (like pull, commit, and push) through a simple shell script would save time during debugging and iteration.

## 4.3 
The delay is confirmed directly in the GrovePi library’s grovepi.py file, as shown in the official documentation and the source code snippet below:
```python
def ultrasonicRead(pin):
    write_i2c_block(address, uRead_cmd + [pin, 0, 0])
    time.sleep(0.2)
    read_i2c_byte(address)
    number = read_i2c_block(address)
    return (number[1] * 256 + number[2])

```
As shown, the time.sleep(0.2) line introduces a 200 ms delay between I2C transactions.
This pause allows the Atmega328P microcontroller to complete the ultrasonic pulse timing and data transmission before the Raspberry Pi attempts to read the result.
The communication between the Raspberry Pi and Atmega328P happens over the I2C (Inter-Integrated Circuit) protocol. The Pi sends a read command via I2C, the Atmega reads the sensor and returns the measured value through the same bus.

## 4.4 
The Grove Rotary Angle Sensor outputs a voltage between 0 V and 5 V depending on rotation. The Atmega328P on the GrovePi board uses a 10-bit ADC (Analog-to-Digital Converter) to convert this voltage into an integer between 0 and 1023.

The conversion formula is:

Digital Value = Vin / 5V × 1023

Example:
0 V → 0
2.5 V → 512
5 V → 1023

The Raspberry Pi cannot perform this conversion directly because it does not include an ADC. It only supports digital input/output (0 or 1). The GrovePi board’s microcontroller performs the analog-to-digital conversion and sends the result to the Pi via I2C.

## 4.5 – Debugging the LCD RGB Backlight

If the LCD RGB Backlight screen shows no text but the program runs without errors, it’s likely an I2C communication or library path issue.

Debugging Steps

1. Check I²C Devices
- sudo i2cdetect -y 1

You should see addresses 0x3e (text display) and 0x62 (backlight).
If not, the LCD isn’t connected properly or I2C is disabled.

2. Verify GrovePi Connection
- sudo i2cdetect -y 1

Confirms the GrovePi and sensors are connected.

3. Test with Sample Code
python3 grove_rgb_lcd/hello_world.py

If text appears, your wiring and I²C configuration are correct.

4. Ensure I²C Interface Is Enabled
- sudo raspi-config
- Interface Options → I2C → Enable → Reboot


5. Recheck physical connections
- Make sure the LCD is connected to the I2C port (not digital or analog).
