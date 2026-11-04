# Truck.AI – Driver Drowsiness Detection

This project is a simple safety system for truck drivers.

The basic idea is: if the driver’s eyes stay closed for more than 3 seconds, the system wakes him up with a voice message, sends an alert to the owner on Telegram, and also saves a short video of that moment. This way we have proof if something happens.

I made this because sleepy driving is a real problem on long routes. I wanted to build something small and useful that can at least give a warning.

## What the system does

- Continuously watches the driver’s face through the webcam
- If eyes remain closed for more than 3 seconds:
  - Speaks a voice alert (“Ramuji, please take a rest.”)
  - Sends a message to the owner on Telegram
  - Saves a video clip (15 seconds before and 15 seconds after the event)

That’s the whole idea. Nothing too complicated.

## How it works

First the camera captures frames.  
Then MediaPipe (a ready-made model from Google) finds 468 points on the face.  
I only use 12 points around the eyes.

From these points I calculate something called **EAR (Eye Aspect Ratio)**.  
When the eyes are open, EAR is high. When they are closed, EAR becomes low.

If the EAR stays below the threshold for more than 3 seconds, the system raises an alert.

In the first 3 seconds the system calibrates itself. It checks the normal open-eye EAR of the current driver and sets the threshold based on that. This helps it work for people with different eye sizes.

## Tools I used

- **OpenCV** – for opening the camera, reading frames, and saving video
- **MediaPipe** – for getting face landmarks (already trained by Google)
- **pyttsx3** – for offline voice alerts
- **requests** – for sending Telegram messages and videos
- **threading** – so that voice and internet work without freezing the camera
- **collections.deque** – to keep the last 20 seconds of frames in memory

I did not train any model myself. MediaPipe is already trained. I just added the logic on top of it.

## How to run

1. Install the required packages:

```bash
pip install mediapipe==0.10.14 pyttsx3 requests opencv-python
```

2. Open the notebook `AiDashCam.ipynb`
3. Add your Telegram token (optional)
4. Run the main cell
5. Sit in front of the camera and keep your eyes open for the first 3 seconds
6. Press **Esc** to stop

Video clips are saved in the `events` folder.

## Settings you can change

```python
DRIVER_NAME     = "Ramuji"
TRUCK_NO        = "UP32 XX 5276"
ALERT_SECONDS   = 3.0
PRE_SECONDS     = 15
POST_SECONDS    = 15
NOTIFY_COOLDOWN = 120
```

You can change these values as per your need.

## Telegram setup (optional)

1. Create a bot using `@BotFather` and get the token
2. Send a message to your bot
3. Run `Telegram.test.ipynb` to get your Chat ID
4. Put both values in the code

If you don’t add the token, the system still works. It just skips the Telegram part.

## What is working right now

- Camera + face detection + EAR calculation
- 3-second rule and calibration
- Voice alert
- Video clip saving
- Telegram (works after adding token)

## Pros

- Very low cost (only a webcam is needed)
- Works offline for voice and video
- Adjusts itself for each driver
- Gives a proof video of the incident

## Limitations

- Needs decent lighting on the face
- Glasses or mask can reduce accuracy
- Currently only detects closed eyes
- Telegram needs internet

## Possible improvements

- Yawning detection
- Head pose / nodding detection
- Better eye state model
- GPS and speed with the alert
- Simple dashboard for fleet owners

That’s all. I tried to keep the project simple so that it is easy to understand and explain. If you have any questions, feel free to ask.
