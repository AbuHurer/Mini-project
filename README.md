# Real-time Object Detection using YOLOv8

This project is a Python application that performs real-time object detection using **YOLOv8**. The application features a **Graphical User Interface (GUI)** created with **Tkinter**, allowing users to select a video file or use a webcam as the input source.

## Features
- **Real-time Object Detection**: Detect objects in videos or live webcam streams using YOLOv8.
- **User-Friendly Interface**: A GUI with options to select a video file or switch to webcam.
- **Live Display**: Annotated frames are displayed in real-time.
- **Dark-Themed Design**: A modern and intuitive dark-themed interface.

## How It Works

The application uses **YOLOv8** from the `ultralytics` library for object detection. It processes each frame from the selected video or webcam feed, detects objects, and displays the results with bounding boxes and labels.

## Prerequisites
1. Install Python 3.8 or above.
2. Install the required libraries:
   ```bash
   pip install ultralytics opencv-python-headless tkinter
   ```

## Code Overview
Below is a detailed explanation of the implementation:

### Import Statements
```python
import tkinter as tk
from tkinter import filedialog, Label
from ultralytics import YOLO
import cv2
from threading import Thread
```
- **tkinter**: For creating the GUI and handling user inputs.
- **ultralytics**: To load and run YOLOv8 for object detection.
- **cv2 (OpenCV)**: For video handling and displaying annotated frames.
- **Thread**: To run detection on a separate thread for a responsive GUI.

### YOLOv8 Model Loading
```python
model = YOLO('yolov8n.pt')  # Load the YOLOv8 model (lightweight version)
```
This loads a pre-trained YOLOv8 model for object detection. You can replace `'yolov8n.pt'` with other YOLOv8 weights as needed.

### GUI Initialization
```python
window = tk.Tk()
window.title("YOLOv8 Object Detection")
window.geometry("500x400")
window.configure(bg="#2C2F33")
```
- Creates the main window for the application.
- Sets the title, size, and background color (dark theme).

### Global Variables
```python
video_source = None
running = False
```
- **`video_source`**: Stores the path to the selected video file or indicates webcam use (`0`).
- **`running`**: A flag to control whether detection is active.

### Core Functions

#### Select Video
```python
def select_video():
    global video_source
    video_source = filedialog.askopenfilename(
        title="Select Video File",
        filetypes=[("Video Files", "*.mp4 *.avi *.mkv")]
    )
    if video_source:
        status_label.config(text=f"Selected Video: {video_source}", fg="white")
    else:
        status_label.config(text="No video selected.", fg="red")
```
- Opens a file dialog to let the user select a video file.
- Updates the status label with the selected file path.

#### Start Detection
```python
def start_detection():
    global running
    running = True
    if video_source or video_source == 0:
        Thread(target=run_detection).start()
    else:
        status_label.config(text="Please select a video source!", fg="red")
```
- Starts object detection by running `run_detection` on a separate thread to keep the GUI responsive.

#### Stop Detection
```python
def stop_detection():
    global running
    running = False
    status_label.config(text="Detection stopped.", fg="yellow")
```
- Stops the detection process by setting the `running` flag to `False`.

#### Run Detection
```python
def run_detection():
    global running
    cap = cv2.VideoCapture(video_source if video_source != 0 else 0)

    if not cap.isOpened():
        status_label.config(text="Error: Could not open video source.", fg="red")
        return

    while running:
        ret, frame = cap.read()
        if not ret:
            status_label.config(text="Video ended or no frame captured.", fg="yellow")
            break

        # Perform detection
        results = model(frame)
        annotated_frame = results[0].plot()

        # Display frame
        cv2.imshow("YOLOv8 Detection", annotated_frame)

        # Stop if 'q' is pressed
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    cap.release()
    cv2.destroyAllWindows()
```
- Opens the selected video file or webcam stream using OpenCV.
- Processes each frame:
  1. Runs YOLOv8 object detection.
  2. Annotates the frame with bounding boxes and labels.
  3. Displays the frame in a new window.
- Stops the process if the video ends or the user presses 'q'.

#### Set Webcam
```python
def set_webcam():
    global video_source
    video_source = 0
    status_label.config(text="Using Webcam.", fg="white")
```
- Switches the input source to the webcam by setting `video_source` to `0`.

### GUI Widgets
The application includes buttons and labels for interactivity:
```python
# Title
title_label = Label(window, text="YOLOv8 Object Detection", font=("Helvetica", 18, "bold"), bg="#23272A", fg="white")
title_label.pack(pady=20)

# Buttons
select_button = tk.Button(window, text="Select Video", command=select_video, font=("Helvetica", 12), bg="#7289DA", fg="white", relief="flat")
select_button.pack(pady=10)

webcam_button = tk.Button(window, text="Use Webcam", command=set_webcam, font=("Helvetica", 12), bg="#7289DA", fg="white", relief="flat")
webcam_button.pack(pady=10)

start_button = tk.Button(window, text="Start Detection", command=start_detection, font=("Helvetica", 12), bg="#43B581", fg="white", relief="flat")
start_button.pack(pady=10)

stop_button = tk.Button(window, text="Stop Detection", command=stop_detection, font=("Helvetica", 12), bg="#F04747", fg="white", relief="flat")
stop_button.pack(pady=10)

# Status Label
status_label = Label(window, text="Status: Idle", font=("Helvetica", 10), bg="#2C2F33", fg="white")
status_label.pack(pady=20)
```
- **Select Video**: Opens a file dialog.
- **Use Webcam**: Switches to webcam input.
- **Start Detection**: Begins object detection.
- **Stop Detection**: Stops the detection process.

### Run the Application
```python
window.mainloop()
```
- Starts the Tkinter main event loop.

## Usage Instructions
1. Run the script using Python:
   ```bash
   python your_script_name.py
   ```
2. Use the GUI to select a video file or switch to the webcam.
3. Click **Start Detection** to begin object detection.
4. View the results in the OpenCV display window.
5. Press **Stop Detection** or close the application to exit.

## Improvements to Consider
- Add options to save annotated video output.
- Allow users to adjust confidence thresholds for detections.
- Add support for multiple YOLO model variants.

---
This project demonstrates the power of YOLOv8 for real-time object detection in an intuitive and accessible manner.
<h2>Acknowledgement</h2>
<p> I would like to extend my deepest gratitude to Dr. Victor A.I, professor at Maharaja institure of technology Mysore, for his invaluable guidance and support throught the course of this project.</p>
<hr>

<b><h1><i>"success consists of going from failure to failure without loss of enthusiasm."</i></h1></b>
