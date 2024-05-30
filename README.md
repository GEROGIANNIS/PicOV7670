
# PicOV7670

This project demonstrates how to capture an image using the OV7670 camera with a Raspberry Pi Pico microcontroller and display the captured image using OpenCV in Python.

## Table of Contents

- [Overview](#overview)
- [Hardware Setup](#hardware-setup)
- [Software Setup](#software-setup)
- [Usage](#usage)
- [License](#license)

## Overview

The PicOV7670 project involves capturing an image using the OV7670 camera module connected to a Raspberry Pi Pico and then processing and displaying the captured image using Python and OpenCV.

## Hardware Setup

### Components

- Raspberry Pi Pico
- OV7670 Camera Module
- Connecting Wires
- Two 3.3V I2C Pull-up Resistors

### Wiring Diagram

#### Power & Ground

- Connect GND of Pico to Camera GND
- Connect 3V3 from Pico to Camera 3V3
- Connect 3V3 from Pico to I2C pull-up resistors (×2)

#### I2C Connections

- Connect one I2C pull-up resistor to Pico GP8
- Connect the other I2C pull-up resistor to Pico GP9
- Connect Pico GP8 to Camera SDA
- Connect Pico GP9 to Camera SCL

#### Camera Control Connections

| Camera Pin | Pico Pin |
|------------|----------|
| VSYNC      | GP7      |
| RESET      | GP10     |
| CLOCK      | GP11     |
| MCLK       | GP20     |
| HREF       | GP21     |

#### Camera Data Connections

| Camera Pin | Pico Pin |
|------------|----------|
| D0         | GP12     |
| D1         | GP13     |
| D2         | GP14     |
| D3         | GP15     |
| D4         | GP16     |
| D5         | GP17     |
| D6         | GP18     |
| D7         | GP19     |

## Software Setup

### Microcontroller Code

1. Install the required libraries for the microcontroller:
   - `adafruit_bus_device`
   - `adafruit_ov7670`

2. Upload the following code to the Raspberry Pi Pico:

```python
import board
import busio
from adafruit_ov7670 import OV7670, OV7670_SIZE_DIV16

def capture_image():
    """
    Captures an image using the OV7670 camera and prints the image data to serial output.
    """
    # Initialize I2C bus
    i2c = busio.I2C(scl=board.GP9, sda=board.GP8)

    # Initialize the OV7670 camera
    cam = OV7670(
        i2c,
        data_pins=[
            board.GP12, board.GP13, board.GP14, board.GP15,
            board.GP16, board.GP17, board.GP18, board.GP19
        ],
        clock=board.GP11,
        vsync=board.GP7,
        href=board.GP21,
        mclk=board.GP20,
        shutdown=None,
        reset=board.GP10,
    )

    # Set camera resolution
    cam.size = OV7670_SIZE_DIV16

    # Create a buffer to store the captured image
    buf = bytearray(25 * cam.width * cam.height)

    # Capture an image
    cam.capture(buf)

    # Print the image data to serial output
    print(buf)
```

### Python Code

1. Install the required Python libraries:
   - `numpy`
   - `opencv-python`

2. Save the following code to a Python script (e.g., `display_image.py`):

```python
import numpy as np
import cv2

# Assuming your image data is stored in a variable called 'image_data'
image_data = bytearray(b'*|ENTER HERE EXTRACTED DATA|*')

# Assuming the image resolution is 200x150
width = 200
height = 150

# Check if the size of the image data matches the expected size
expected_size = width * height
if len(image_data) != expected_size:
    raise ValueError(f"Expected size {expected_size}, but got {len(image_data)}")

# Convert the bytearray to a NumPy array
np_data = np.frombuffer(image_data, dtype=np.uint8)

# Reshape the NumPy array to the image dimensions
image_np = np_data.reshape(height, width)

# Display or save the image as needed using OpenCV
cv2.imshow("Decoded Image", image_np)  # Display the image
cv2.waitKey(0)
cv2.destroyAllWindows()  # Close the window after a key is pressed
```

## Usage

1. Capture an image using the microcontroller code.
2. Copy the printed image data to the `image_data` variable in the Python script.
3. Run the Python script to display the captured image.

## License

This project is licensed under the MIT License.
