# HASTH VANI

## Smart Two-Way Communication Gloves for Sign-to-Voice and Voice-to-Text Communication

> **Tagline:** *Sign Today. Connect Tomorrow.*

------------------------------------------------------------------------

## 1. Project Overview

**Hasth Vani** is a wearable communication system designed to help a
person communicate with another person through hand gestures, spoken
audio, and text.

The prototype consists of **two smart gloves** and a **mobile
application**:

-   **Left-hand glove --- Display Glove:** recognizes/receives
    information and displays text on a TFT screen.
-   **Right-hand glove --- Speaker Glove:** recognizes predefined hand
    gestures and converts them into spoken audio through a speaker.
-   **Mobile App:** provides the reverse communication path by
    converting a communication partner's speech into text and sending
    that text wirelessly to the left glove.

The core idea is to create a **two-way communication bridge**:

``` text
                    HASTH VANI
                         │
          ┌──────────────┴──────────────┐
          │                             │
       SIGN → VOICE                  VOICE → TEXT
          │                             │
   Right Smart Glove              Mobile Application
          │                             │
   Gesture Recognition             Speech-to-Text
          │                             │
     ESP32 + Sensors              Bluetooth / Wi-Fi
          │                             │
    Audio Amplifier                    │
          │                             │
       Speaker                    Left Smart Glove
                                        │
                                  TFT Display
                                        │
                                   Text Output
```

The supplied prototype plan defines the same overall system: five flex
sensors and an MPU6050 feed each glove's ESP32; the left glove drives a
TFT, while the right glove drives a MAX98357A amplifier and 3 W speaker.
The app provides speech-to-text and sends the resulting text to the left
glove. fileciteturn1file0L20-L54

------------------------------------------------------------------------

# 2. Problem Statement

Communication can become difficult when two people do not share the same
communication method.

A person communicating through hand gestures may need another person to
understand those gestures visually. In a normal conversation, the other
person may also respond by speaking, which creates an additional
communication barrier.

Hasth Vani addresses this problem by creating an electronic interface
that can:

1.  Sense finger bending.
2.  Sense hand orientation and movement.
3.  Recognize predefined gestures.
4.  Convert recognized gestures into predefined spoken messages.
5.  Receive spoken responses through a mobile phone.
6.  Convert speech into text.
7.  Display that text directly on the wearer's glove.

The important part of the project is that it is not only a
**gesture-to-output** device. It is designed as a **two-way
communication system**.

------------------------------------------------------------------------

# 3. Main Objective

The main objective of Hasth Vani is:

> **To develop a wearable smart-glove prototype that uses finger-bend
> sensing, hand-motion sensing, embedded processing, audio output,
> display output, and mobile speech-to-text communication to create a
> two-way communication interface.**

### Specific objectives

-   Detect finger positions using flex sensors.
-   Detect hand orientation and rotation using MPU6050 IMUs.
-   Process sensor readings using ESP32 microcontrollers.
-   Recognize predefined gestures.
-   Produce corresponding spoken messages from the right glove.
-   Display incoming text on the left glove.
-   Provide a mobile interface for communication and translation
    history.
-   Reduce false gesture detections using calibration, multiple samples,
    and cooldown logic.
-   Build the system as a wearable prototype rather than a stationary
    electronics project.

------------------------------------------------------------------------

# 4. Why Two Gloves?

The two-glove design separates the two major output functions.

## Left Glove --- Display Glove

The left glove is primarily responsible for **textual information**.

``` text
5 Flex Sensors
      +
   MPU6050
      │
      ▼
  ESP32 LEFT
      │
      ▼
   TFT Display
      │
      ▼
  Received Text
```

The TFT is mounted on the back of the hand so that the wearer can see
incoming messages.

## Right Glove --- Speaker Glove

The right glove is primarily responsible for **audio output**.

``` text
5 Flex Sensors
      +
   MPU6050
      │
      ▼
 ESP32 RIGHT
      │
      ▼
 MAX98357A
      │
      ▼
  3 W Speaker
      │
      ▼
 Spoken Message
```

This division makes the prototype easier to demonstrate because one
glove visibly displays information while the other produces audio.

------------------------------------------------------------------------

# 5. Complete System Architecture

``` text
                  ┌─────────────────────┐
                  │     RIGHT GLOVE     │
                  │                     │
                  │  5 Flex Sensors     │
                  │        +            │
                  │     MPU6050         │
                  │        │            │
                  │        ▼            │
                  │      ESP32          │
                  │        │            │
                  │       I²S           │
                  │        ▼            │
                  │    MAX98357A        │
                  │        │            │
                  │        ▼            │
                  │    3 W Speaker      │
                  └─────────┬───────────┘
                            │
                      Spoken Output
                            │
                            ▼
                       Communication
                          Partner


Signer / User
     │
     │ Spoken response
     ▼
┌─────────────────────┐
│    MOBILE APP       │
│                     │
│ Phone Microphone    │
│        │            │
│        ▼            │
│ Speech-to-Text      │
│        │            │
│        ▼            │
│ Bluetooth / Wi-Fi   │
└─────────┬───────────┘
          │
          ▼
┌─────────────────────┐
│     LEFT GLOVE      │
│                     │
│ Wireless Text       │
│        │            │
│        ▼            │
│      ESP32           │
│        │            │
│        ▼            │
│    TFT Display      │
└─────────────────────┘
```

The project documentation explicitly describes the two directions as
**SIGN → VOICE** and **VOICE → TEXT**. fileciteturn1file3L307-L330

------------------------------------------------------------------------

# 6. Hardware Components

  Component                      Quantity Purpose
  ------------------------- ------------- ---------------------------------------------------
  ESP32 development board               2 Processing and control, one per glove
  Flex sensors                         10 Five per glove; measure finger bending
  MPU6050                               2 One per glove; measures hand movement/orientation
  TFT display 2.4"/2.8"                 1 Displays text on the left glove
  MAX98357A I²S amplifier               1 Drives the speaker
  3 W speaker                           1 Produces spoken audio
  3.7 V Li-ion battery                  2 Power source, one per glove
  TP4056 charging module                2 Battery charging
  10 kΩ resistors                      10 Voltage-divider resistors for flex sensors
  Perfboard/PCB                         2 Mounting and interconnection
  Switches                              2 Power control
  Gloves                                2 Wearable base
  Jumper wires                As required Electrical connections

These quantities and component roles come directly from the prototype
bill of materials. fileciteturn1file8L651-L678

------------------------------------------------------------------------

# 7. Flex Sensors

## 7.1 What is a Flex Sensor?

A flex sensor changes its electrical resistance when it bends.

The ESP32 does not directly measure resistance. Therefore, each flex
sensor is connected as part of a **voltage divider**.

### One sensor channel

``` text
             3.3 V
               │
               │
        ┌─────────────┐
        │ Flex Sensor │
        └─────────────┘
               │
               ├──────────► ESP32 ADC
               │
             10 kΩ
               │
               ▼
              GND
```

The supplied design uses:

``` text
3.3 V → Flex Sensor → ADC point → 10 kΩ → GND
```

The documented relationship is:

``` text
Vout = 3.3 V × 10k / (Rflex + 10k)
```

As the sensor bends, its resistance changes and therefore the ADC
voltage changes. fileciteturn1file7L610-L627

------------------------------------------------------------------------

# 8. Five Sensors Per Glove

Each hand has five sensors:

``` text
T = Thumb
I = Index
M = Middle
R = Ring
L = Little
```

Therefore:

``` text
LEFT GLOVE
T ──► ADC
I ──► ADC
M ──► ADC
R ──► ADC
L ──► ADC

RIGHT GLOVE
T ──► ADC
I ──► ADC
M ──► ADC
R ──► ADC
L ──► ADC
```

Each sensor must have its **own voltage-divider circuit and ADC input**.
The sensor outputs must not be joined together.
fileciteturn1file7L628-L638

------------------------------------------------------------------------

# 9. Suggested ESP32 ADC Pin Mapping

The prototype documentation proposes the following ADC inputs for both
ESP32 boards:

  Finger     ESP32 GPIO
  -------- ------------
  Thumb          GPIO36
  Index          GPIO39
  Middle         GPIO34
  Ring           GPIO35
  Little         GPIO32

These are ADC1 pins, which are useful when wireless features are used
because ADC2 has conflicts with Wi-Fi on the ESP32. The pin map is
explicitly described as a suggested map and must be verified against the
actual development board. fileciteturn1file8L667-L678

------------------------------------------------------------------------

# 10. Flex Sensor Calibration

Calibration is extremely important.

Different sensors and different physical mounting positions will produce
different readings. Therefore, the example ADC numbers should **not** be
treated as universal thresholds.

For every finger, record:

``` text
1. Finger completely straight
2. Finger half bent
3. Finger completely bent
```

Example structure:

  -----------------------------------------------------------------------
  Finger                   Straight          Half Bent               Bent
  -------------- ------------------ ------------------ ------------------
  Thumb               Record actual      Record actual      Record actual
                              value              value              value

  Index               Record actual      Record actual      Record actual
                              value              value              value

  Middle              Record actual      Record actual      Record actual
                              value              value              value

  Ring                Record actual      Record actual      Record actual
                              value              value              value

  Little              Record actual      Record actual      Record actual
                              value              value              value
  -----------------------------------------------------------------------

Then create ranges:

``` text
BENT RANGE
      ↓
───────────────
INTERMEDIATE
───────────────
STRAIGHT RANGE
```

The build guide specifically recommends using **ranges instead of one
exact ADC value**. fileciteturn0file1L43-L50

------------------------------------------------------------------------

# 11. Sensor Mounting

The physical mounting of the flex sensor is as important as the
electronics.

The sensor should:

-   Follow the natural bend of the finger.
-   Be placed along the back of the finger.
-   Avoid sharp folds.
-   Avoid being tightly creased.
-   Have strain relief near the sensor wires.
-   Allow the sensor to move naturally with the finger.

A useful physical arrangement is:

``` text
Finger
────────────────────────
      FLEX SENSOR
────────────────────────
        │
        │ wire
        ▼
     strain relief
        │
        ▼
      glove
```

The prototype guide recommends fixing the tip area while allowing the
strip to slide naturally, and protecting the sensor base and wiring with
strain relief. fileciteturn1file0L175-L209

------------------------------------------------------------------------

# 12. MPU6050 IMU

## What is MPU6050?

The MPU6050 is an **Inertial Measurement Unit (IMU)** containing:

-   Accelerometer
-   Gyroscope

It is used in Hasth Vani to detect the **orientation and movement of the
hand**.

This is important because finger positions alone may not distinguish
every gesture.

For example:

``` text
Finger shape
     +
Hand orientation
     =
More reliable gesture
```

------------------------------------------------------------------------

# 13. MPU6050 Communication

The MPU6050 communicates with the ESP32 using **I²C**.

Suggested connection:

``` text
MPU6050              ESP32
────────              ─────
VCC        ────────►  3.3 V*
GND        ────────►  GND
SDA        ────────►  GPIO21
SCL        ────────►  GPIO22
```

`*` Verify the actual module's voltage requirements before connecting.

The documentation recommends running an I²C scan and checking the
expected address. The common addresses documented are:

``` text
AD0 LOW  → 0x68
AD0 HIGH → 0x69
```

The exact module should be checked before permanent wiring.
fileciteturn0file0L213-L255

------------------------------------------------------------------------

# 14. What the MPU6050 Detects

The accelerometer provides:

``` text
AX
AY
AZ
```

The gyroscope provides:

``` text
GX
GY
GZ
```

Conceptually:

``` text
Tilt forward/backward → pitch-related change
Tilt left/right       → roll-related change
Rotate wrist          → gyroscope change
```

The prototype guide recommends testing these axes independently and
recording offsets while the glove is stationary.
fileciteturn0file0L248-L261

------------------------------------------------------------------------

# 15. Why Flex Sensors + MPU6050 Are Better Together

A gesture is not always defined only by which fingers are bent.

It may also depend on:

-   Palm direction
-   Wrist rotation
-   Hand tilt
-   Movement direction

Therefore the prototype creates a gesture record containing:

``` text
5 Finger States
       +
Hand Orientation
       │
       ▼
Gesture Classification
```

This helps distinguish gestures with similar finger configurations.
fileciteturn1file3L269-L306

------------------------------------------------------------------------

# 16. Left Glove --- Display Glove

The left glove contains:

-   5 flex sensors
-   1 MPU6050
-   1 ESP32
-   1 TFT display
-   Battery
-   TP4056
-   Power switch
-   Resistors/perfboard and wiring

Its main role is to show text.

### Data path

``` text
Received Message
      │
      ▼
   ESP32 LEFT
      │
      ▼
   TFT Display
      │
      ▼
"Do you need water?"
```

The prototype design places the TFT on the back of the hand so that the
wearer can see incoming messages. fileciteturn1file0L267-L300

------------------------------------------------------------------------

# 17. TFT Display

The proposed display is a **2.4-inch or 2.8-inch TFT**.

A common configuration in the prototype documentation uses SPI signals:

``` text
ESP32 LEFT        TFT
──────────        ───
GPIO23      ───►  MOSI
GPIO18      ───►  SCK
GPIO19      ───►  MISO
GPIO15      ───►  CS
GPIO2       ───►  DC
GPIO4       ───►  RESET
```

The exact TFT pinout and interface must be verified against the actual
module.

The prototype documentation refers to an ILI9341-style SPI TFT and
TFT_eSPI configuration as an example. fileciteturn0file0L272-L300

------------------------------------------------------------------------

# 18. Right Glove --- Speaker Glove

The right glove contains:

-   5 flex sensors
-   1 MPU6050
-   1 ESP32
-   MAX98357A I²S amplifier
-   3 W speaker
-   Battery
-   TP4056
-   Power switch
-   Resistors/perfboard and wiring

Its primary role is to produce audio from recognized gestures.

``` text
Gesture
   │
   ▼
5 Flex + MPU6050
   │
   ▼
ESP32 RIGHT
   │
   ▼
I²S Audio
   │
   ▼
MAX98357A
   │
   ▼
3 W Speaker
   │
   ▼
Spoken Message
```

------------------------------------------------------------------------

# 19. MAX98357A Audio System

The MAX98357A is an **I²S digital audio amplifier**.

The prototype's suggested I²S signals are:

  Signal     ESP32 RIGHT
  -------- -------------
  BCLK            GPIO26
  LRC/WS          GPIO25
  DIN             GPIO27

The amplifier then drives the 3 W speaker.

The speaker must be connected to the amplifier's marked speaker output.
The prototype guide specifically warns that the amplifier output is
bridged (BTL), so a speaker lead should not be connected to GND.
fileciteturn1file0L330-L361

------------------------------------------------------------------------

# 20. Gesture Recognition

The gesture-recognition system is the brain of the prototype.

The process is:

``` text
Read Sensors
     │
     ▼
5 Flex ADC Values
     +
MPU6050 Data
     │
     ▼
Normalize / Calibrate
     │
     ▼
Compare With Gesture Rules
     │
     ▼
Does It Match?
   ┌──┴──┐
  YES    NO
   │      │
   ▼      ▼
Check   READY /
Stability UNKNOWN
   │
   ▼
Cooldown
   │
   ▼
Output
```

The documented recognition loop uses stable consecutive readings and a
cooldown to reduce random and duplicate outputs.
fileciteturn1file3L269-L306

------------------------------------------------------------------------

# 21. Gesture Recognition Should Not Use One Raw Reading

Sensor readings naturally contain noise.

Therefore:

``` text
BAD METHOD

One reading
     ↓
Immediate decision
     ↓
Possible false gesture
```

Better method:

``` text
Read 1 ─┐
Read 2 ─┤
Read 3 ─┤
Read 4 ─┤──► Are they consistent?
Read 5 ─┘
              │
             YES
              │
              ▼
       Accept gesture
```

The prototype plan recommends accepting a gesture only when multiple
consecutive readings remain consistent. fileciteturn1file2L188-L195

------------------------------------------------------------------------

# 22. Cooldown Mechanism

After a gesture is recognized, the system should temporarily ignore the
same type of trigger.

Example:

``` text
Gesture detected
      │
      ▼
Output "HELLO"
      │
      ▼
Cooldown ≈ short delay
      │
      ▼
Resume recognition
```

The example recognition flow uses a cooldown such as approximately **1.5
seconds**. This value is an example and can be tuned during testing.
fileciteturn1file3L269-L292

This prevents:

``` text
HELLO
HELLO
HELLO
HELLO
```

from being triggered repeatedly because the hand remains in the same
position.

------------------------------------------------------------------------

# 23. Example Gesture Set

The prototype documentation provides example gesture records for:

-   HELLO
-   WATER
-   HELP
-   THANK YOU
-   YES
-   NO

The table is explicitly an **example template** and should be replaced
or calibrated using the team's real sensor samples.
fileciteturn0file0L304-L324

A conceptual representation is:

  ----------------------------------------------------------------------------
  Gesture           Finger Pattern    Orientation/Movement   Output
  ----------------- ----------------- ---------------------- -----------------
  HELLO             Defined by        Palm/orientation       "Hello"
                    calibration       condition              

  WATER             Defined by        Tilt/index-to-mouth    "I need water"
                    calibration       type condition         

  HELP              Defined by        Wrist/hand condition   "Help"
                    calibration                              

  THANK YOU         Defined by        Movement away from     "Thank you"
                    calibration       chin                   

  YES               Defined by        Nod/pitch condition    "Yes"
                    calibration                              

  NO                Defined by        Side-to-side movement  "No"
                    calibration                              
  ----------------------------------------------------------------------------

------------------------------------------------------------------------

# 24. Unknown Gesture State

The system should not always force a prediction.

If sensor values do not match a known gesture:

``` text
No reliable match
      │
      ▼
READY / UNKNOWN
```

This is an important reliability feature.

Instead of producing a wrong message, the glove can display or maintain:

``` text
READY
```

or

``` text
UNKNOWN
```

The project checklist also requires the unknown/no-gesture state to
work. fileciteturn1file4L389-L396

------------------------------------------------------------------------

# 25. Mobile Application

The mobile app is the bridge for the **reverse direction** of
communication.

The app can provide:

-   Connection status
-   Detected gesture
-   Confidence display
-   Speak button
-   Language selection
-   Translation history
-   Speech-to-text input
-   Sending text to the left glove

The supplied design shows these app functions as part of the prototype
UI. fileciteturn1file1L80-L99

------------------------------------------------------------------------

# 26. Communication Direction 1 --- Sign to Voice

This is the first major path.

``` text
USER
 │
 │ Hand gesture
 ▼
Flex Sensors + MPU6050
 │
 ▼
ESP32 RIGHT
 │
 │ Gesture recognized
 ▼
MAX98357A
 │
 ▼
Speaker
 │
 ▼
COMMUNICATION PARTNER HEARS MESSAGE
```

### Example

The user performs the gesture corresponding to:

``` text
"I NEED WATER"
```

The right ESP32 recognizes the gesture.

The ESP32 sends the corresponding audio data through I²S.

The MAX98357A amplifies the audio.

The speaker produces the spoken message.

------------------------------------------------------------------------

# 27. Communication Direction 2 --- Voice to Text

The second direction uses the mobile application.

``` text
PARTNER SPEAKS
      │
      ▼
PHONE MICROPHONE
      │
      ▼
SPEECH-TO-TEXT
      │
      ▼
Recognized Text
      │
      ▼
Bluetooth / Wi-Fi
      │
      ▼
LEFT ESP32
      │
      ▼
TFT DISPLAY
```

### Example

Partner says:

> "Do you need water?"

The app converts the speech into:

``` text
Do you need water?
```

The text is sent to the left glove.

The TFT displays the sentence for the wearer.

This complete reverse-direction test is explicitly included in the build
plan. fileciteturn1file1L100-L103

------------------------------------------------------------------------

# 28. Wireless Communication

The prototype plan allows the selected wireless method to be:

-   Bluetooth
-   Wi-Fi

The important requirement is that the selected method must reliably
connect the app and glove system.

The app should also show:

``` text
● Connected
```

or

``` text
○ Disconnected
```

so the user knows whether communication is currently available.
fileciteturn1file2L196-L212

------------------------------------------------------------------------

# 29. Translation / Language Layer

The prototype UI includes language selection and translation history.

Conceptually:

``` text
Detected Message
       │
       ▼
   App Message
       │
       ├──► History
       │
       └──► Language Selection
```

The current supplied build documentation defines the app's
speech-to-text and language-selection interface, but it does **not**
specify a particular translation API or AI translation model.

Therefore, a specific translation service should be treated as a future
implementation decision rather than a confirmed component of the current
hardware design.

------------------------------------------------------------------------

# 30. Is Hasth Vani an AI System?

The physical prototype shown in the supplied documentation primarily
describes **calibration + rule/range-based gesture recognition**.

The current documented recognition method is:

``` text
Sensor Readings
      ↓
Calibration
      ↓
Normalized Values
      ↓
Gesture Rules / Ranges
      ↓
Recognized Message
```

Therefore, it would be more accurate to describe the current prototype
as:

> **An embedded sensor-based gesture recognition system with an AI-ready
> architecture.**

If you later train a machine-learning model using collected flex + IMU
data, the recognition layer can become:

``` text
Flex + IMU Data
      ↓
Dataset
      ↓
Feature Processing
      ↓
ML Model
      ↓
Gesture Class
      ↓
Voice / Text
```

This would be a genuine ML extension.

------------------------------------------------------------------------

# 31. Data Collection for Future AI/ML

The prototype already has the right type of sensor data for creating a
gesture dataset.

For every gesture:

``` text
Timestamp
+
Thumb value
+
Index value
+
Middle value
+
Ring value
+
Little value
+
AX, AY, AZ
+
GX, GY, GZ
+
Gesture Label
```

Example:

``` text
T,I,M,R,L,AX,AY,AZ,GX,GY,GZ,label
...
...
...
```

You can collect multiple samples for:

``` text
HELLO
WATER
HELP
THANK YOU
YES
NO
```

and later train a classifier.

The build guide specifically recommends collecting repeated successful
and unsuccessful gesture samples. fileciteturn1file3L293-L302

------------------------------------------------------------------------

# 32. Power System

Each glove has its own battery arrangement.

The prototype design includes:

``` text
3.7 V Li-ion Battery
        │
        ▼
     TP4056
        │
        ▼
      Switch
        │
        ▼
    Power System
```

The supplied design notes that a single 3.7 V cell can be below the
input required by many ESP32 boards' VIN regulators, so a small **5 V
boost converter** is recommended where required.
fileciteturn1file1L104-L125

### Important

The exact power architecture must be checked against the actual ESP32
board, amplifier board, battery, and charging module being used.

Do not assume every ESP32 board accepts the same input voltage.

------------------------------------------------------------------------

# 33. TP4056

The TP4056 is used as the battery charging module.

The prototype BOM specifies one TP4056 per glove and recommends a
version with protection. fileciteturn1file0L54-L69

The battery system should be tested separately before connecting it to
the complete glove.

------------------------------------------------------------------------

# 34. Common Ground

The prototype power diagram specifies a common ground between the
connected modules.

Conceptually:

``` text
Battery / Power
      │
      ├── ESP32 GND
      ├── Sensor GND
      ├── MPU6050 GND
      ├── TFT GND
      └── Audio GND
```

The exact implementation must follow the selected modules and verified
power design.

------------------------------------------------------------------------

# 35. Physical Assembly

The prototype should be assembled so that the electronics do not
interfere with natural hand movement.

Recommended physical arrangement:

``` text
FINGERS
│
├── Flex Sensors
│
└── Sensor Wires
       │
       ▼
  Back of Hand
       │
       ├── TFT / Speaker
       │
       ▼
     Wrist
       │
       ├── ESP32
       ├── Battery
       └── Supporting electronics
```

The build guide recommends routing flex wires along the back of the
fingers rather than across knuckle creases, adding strain relief,
keeping boards removable until testing is complete, and insulating
solder joints. fileciteturn1file1L134-L164

------------------------------------------------------------------------

# 36. Safety Considerations

Because the prototype contains rechargeable Li-ion batteries and
wearable electronics, safety is important.

### Before powering

-   Check every connection.
-   Verify polarity.
-   Check for short circuits.
-   Verify module voltage requirements.
-   Keep exposed conductors insulated.
-   Keep batteries disconnected during signal wiring.

### During testing

-   Monitor for unexpected heating.
-   Stop power if a component becomes abnormally hot.
-   Avoid exposed conductive surfaces touching skin.
-   Secure loose wires.
-   Keep batteries physically protected.

The prototype guide explicitly recommends testing the power modules
separately, insulating exposed joints, adding strain relief, checking
comfort, and disconnecting power if unexpected heating occurs.
fileciteturn1file2L213-L229

------------------------------------------------------------------------

# 37. Complete Software Architecture

The software can be divided into several layers.

``` text
┌────────────────────────────────────┐
│       APPLICATION / OUTPUT         │
│ TFT / Speaker / Mobile App         │
└──────────────────┬─────────────────┘
                   │
┌──────────────────▼─────────────────┐
│       GESTURE LOGIC LAYER          │
│ Rules / thresholds / stability     │
│ cooldown / unknown state           │
└──────────────────┬─────────────────┘
                   │
┌──────────────────▼─────────────────┐
│       SENSOR PROCESSING            │
│ ADC normalization + IMU data       │
└──────────────────┬─────────────────┘
                   │
┌──────────────────▼─────────────────┐
│          HARDWARE LAYER            │
│ Flex sensors + MPU6050 + ESP32     │
└────────────────────────────────────┘
```

------------------------------------------------------------------------

# 38. Firmware Logic

A simplified firmware loop:

``` cpp
void loop() {

    readFlexSensors();

    readMPU6050();

    normalizeSensorValues();

    identifyGesture();

    if (gestureIsStable()) {

        if (cooldownFinished()) {

            processGesture();

            startCooldown();
        }
    }

    handleWirelessCommunication();

    updateOutput();
}
```

The exact implementation will depend on the final gesture algorithm and
selected wireless protocol.

------------------------------------------------------------------------

# 39. Recognition Pipeline

The complete recognition process can be described as:

### Step 1 --- Read

Read:

``` text
5 flex sensors
+
accelerometer
+
gyroscope
```

### Step 2 --- Calibrate

Convert raw values into usable ranges.

### Step 3 --- Normalize

For example:

``` text
0% = calibrated straight
100% = calibrated bent
```

### Step 4 --- Compare

Compare the current state with gesture definitions.

### Step 5 --- Stability Check

Confirm the same result across multiple readings.

### Step 6 --- Cooldown

Prevent immediate duplicate recognition.

### Step 7 --- Output

Send the recognized message to:

-   TFT
-   Speaker
-   Mobile app

The documented flow uses normalization, comparison, consecutive-read
stability, cooldown, and output. fileciteturn1file3L269-L292

------------------------------------------------------------------------

# 40. Testing Strategy

The prototype should be tested in stages instead of connecting
everything at once.

## Stage 1 --- Flex Sensors

Test:

``` text
Sensor 1
Sensor 2
Sensor 3
Sensor 4
Sensor 5
```

Verify that bending one finger changes only its corresponding reading.

## Stage 2 --- MPU6050

Check:

``` text
SDA
SCL
I²C address
AX/AY/AZ
GX/GY/GZ
```

## Stage 3 --- TFT

Display:

``` text
HASTH VANI
READY
TEST MESSAGE
```

## Stage 4 --- Speaker

Play a known audio test.

## Stage 5 --- Gesture Recognition

Start with one gesture:

``` text
HELLO
```

Then add:

``` text
WATER
HELP
THANK YOU
YES
NO
```

## Stage 6 --- Mobile App

Test:

``` text
Microphone
↓
Speech-to-text
↓
Wireless
↓
ESP32
↓
TFT
```

## Stage 7 --- Complete Demo

Run:

``` text
Gesture
  ↓
Recognition
  ↓
Voice/Text

then

Speech
  ↓
Speech-to-text
  ↓
Wireless
  ↓
TFT
```

The supplied build plan defines this exact staged testing sequence.
fileciteturn1file5L427-L462

------------------------------------------------------------------------

# 41. Performance Metrics

For a proper prototype evaluation, record:

## Gesture Accuracy

``` text
Accuracy =
Correct Recognitions / Total Attempts × 100
```

For example, perform each gesture multiple times.

## False Trigger Rate

Measure how often the system produces a message when no intentional
gesture is made.

## Response Time

Measure:

``` text
Gesture completed
        ↓
Recognition
        ↓
Output
```

## Wireless Reliability

Test the communication at the required demonstration distance.

## Battery Stability

Run the prototype using the intended battery arrangement and observe
stability.

The build plan explicitly calls for gesture accuracy, false-trigger,
response-time, wireless-range, restart, and battery tests.
fileciteturn1file5L432-L445

------------------------------------------------------------------------

# 42. Known Limitations

The current prototype has several important limitations.

### 1. Limited Gesture Vocabulary

The documented design uses a predefined set of gestures. It is not
automatically capable of recognizing every possible sign.

### 2. Calibration Dependency

Sensor thresholds depend on:

-   Sensor variation
-   Glove fit
-   Sensor position
-   User hand size
-   Wiring
-   Mounting

Therefore, recalibration may be required.

### 3. Gesture Similarity

Some gestures may have similar finger positions. MPU6050 orientation
helps separate them, but more advanced recognition may be required.

### 4. Noise and False Triggers

Sensor readings can fluctuate. The design addresses this with:

-   Multiple samples
-   Normalization
-   Threshold ranges
-   Cooldown
-   Unknown state

### 5. Battery Constraints

The gloves contain several electronics modules, so battery size and
power architecture affect operating time and physical comfort.

### 6. Wearability

Adding ESP32 boards, batteries, wires, display, amplifier, and speaker
makes the prototype heavier and less compact than a finished product.

### 7. Language/Translation

The supplied design includes language selection, but the specific
translation engine is not defined in the current build documentation.

### 8. AI/ML

The current documented recognition approach is primarily threshold/rule
based. A trained ML model would be an enhancement rather than something
that should be claimed as already implemented unless it is actually
built and tested.

------------------------------------------------------------------------

# 43. Troubleshooting

  -----------------------------------------------------------------------
  Problem                             First Things to Check
  ----------------------------------- -----------------------------------
  Flex values do not change           Voltage divider, ADC pin, ground,
                                      sensor orientation, wire continuity

  MPU6050 not detected                VCC/GND, SDA/SCL, I²C address,
                                      module configuration

  TFT blank                           Power, ground, interface,
                                      CS/DC/RST, display initialization

  Speaker silent                      I²S pins, amplifier power, speaker
                                      connection, audio configuration

  Random gestures                     Recalibration, thresholds, multiple
                                      samples, cooldown

  Wireless connection drops           Power stability, distance,
                                      reconnection logic, communication
                                      settings

  ESP32 resets during audio           Battery voltage sag, power wiring,
                                      boost supply, amplifier power
                                      stability
  -----------------------------------------------------------------------

These checks are included in the project's troubleshooting guide.
fileciteturn1file4L397-L410

------------------------------------------------------------------------

# 44. Team Development Structure

The prototype can be divided into parallel work packages:

  Role       Responsibility
  ---------- -------------------------------------------------------
  Member 1   Left-glove flex sensors, calibration and mounting
  Member 2   Right-glove flex sensors, calibration and mounting
  Member 3   ESP32 firmware and gesture-recognition logic
  Member 4   TFT/display glove and wireless message display
  Member 5   MAX98357A, speaker and right-glove audio
  Member 6   Mobile app, speech-to-text, testing and documentation

This role division matches the supplied project plan.
fileciteturn1file4L380-L387

------------------------------------------------------------------------

# 45. Recommended Development Order

Do not try to build the whole system at once.

Use this order:

``` text
1. Test one flex sensor
        ↓
2. Test all five flex sensors
        ↓
3. Calibrate sensors
        ↓
4. Test MPU6050
        ↓
5. Combine flex + IMU
        ↓
6. Recognize HELLO
        ↓
7. Add more gestures
        ↓
8. Add TFT
        ↓
9. Add speaker
        ↓
10. Add mobile app
        ↓
11. Add two-way communication
        ↓
12. Final calibration
        ↓
13. Final wearable assembly
```

This staged approach minimizes debugging complexity.

------------------------------------------------------------------------

# 46. Complete Demonstration Scenario

A strong demonstration can follow this sequence.

## Part A --- Sign to Voice

1.  Wear both gloves.
2.  Turn on the system.
3.  Verify connection.
4.  Perform the `I NEED WATER` gesture.
5.  Flex sensors detect finger positions.
6.  MPU6050 provides hand orientation.
7.  ESP32 processes the readings.
8.  Gesture is accepted after stability checking.
9.  Cooldown starts.
10. Right glove sends audio to MAX98357A.
11. Speaker says the predefined message.
12. Communication partner hears it.

## Part B --- Voice to Text

1.  Communication partner speaks.
2.  Mobile app receives microphone input.
3.  Speech-to-text converts speech to text.
4.  App sends the text wirelessly.
5.  Left ESP32 receives the message.
6.  TFT displays the message.
7.  Wearer reads the response.

This demonstrates the central value proposition:

``` text
SIGN → VOICE

and

VOICE → TEXT
```

------------------------------------------------------------------------

# 47. What Makes the Prototype Interesting

The strongest part of Hasth Vani is not any single component.

The value comes from the **integration of multiple systems**:

``` text
Wearable Hardware
      +
Sensor Fusion
      +
Embedded Processing
      +
Gesture Recognition
      +
Audio Output
      +
Text Display
      +
Wireless Communication
      +
Mobile Speech-to-Text
      =
Two-Way Communication Prototype
```

The prototype turns physical hand movement into a digital communication
channel.

------------------------------------------------------------------------

# 48. Future Improvements

## 48.1 Machine Learning Gesture Recognition

Replace fixed thresholds with a trained model.

``` text
Sensor Dataset
      ↓
ML Model
      ↓
Gesture Prediction
```

Possible model families can be evaluated later depending on the dataset
and ESP32 constraints.

## 48.2 More Gestures

Expand beyond the initial prototype vocabulary.

## 48.3 Personalized Calibration

Store calibration values for individual users.

## 48.4 Better Wearability

Move from:

``` text
Prototype wiring
```

to:

``` text
Custom PCB
Flexible PCB / compact wiring
Smaller battery
Better enclosure
```

## 48.5 Better Audio

Improve:

-   Speaker enclosure
-   Volume
-   Audio clarity
-   Power efficiency

## 48.6 Better Mobile Application

Add:

-   Conversation history
-   User profiles
-   Language selection
-   Translation
-   Connection diagnostics
-   Gesture-learning mode

## 48.7 Offline Operation

Move more processing to the ESP32/mobile device so basic communication
can continue with limited or no internet connectivity where technically
feasible.

## 48.8 Feedback to the User

The TFT could show:

``` text
READY
LISTENING
GESTURE DETECTED
CONFIDENCE
SENT
UNKNOWN
CONNECTED
```

------------------------------------------------------------------------

# 49. Possible Advanced Architecture

A future version could look like:

``` text
                 ┌────────────────────┐
                 │  FLEX + IMU DATA   │
                 └─────────┬──────────┘
                           │
                           ▼
                 ┌────────────────────┐
                 │ Signal Processing  │
                 └─────────┬──────────┘
                           │
                           ▼
                 ┌────────────────────┐
                 │ ML Gesture Model   │
                 └─────────┬──────────┘
                           │
                           ▼
                 ┌────────────────────┐
                 │ Language Layer     │
                 └──────┬─────┬───────┘
                        │     │
                     Voice   Text
                        │     │
                        ▼     ▼
                   Speaker   TFT
```

This is a possible future architecture, not a claim about the current
implemented prototype.

------------------------------------------------------------------------

# 50. Final Prototype Checklist

Before calling the prototype complete:

-   [ ] All 10 flex sensors tested.
-   [ ] All 10 flex sensors calibrated.
-   [ ] Both MPU6050 modules detected.
-   [ ] Both IMUs calibrated.
-   [ ] Left ESP32 working.
-   [ ] Right ESP32 working.
-   [ ] TFT working.
-   [ ] MAX98357A working.
-   [ ] Speaker working.
-   [ ] Gesture recognition tested.
-   [ ] Unknown state works.
-   [ ] Multiple-sample stability works.
-   [ ] Cooldown works.
-   [ ] Mobile speech-to-text works.
-   [ ] Wireless communication works.
-   [ ] Text reaches the left glove.
-   [ ] Two-way demonstration works.
-   [ ] Battery operation tested.
-   [ ] Charging system checked safely.
-   [ ] Wires insulated.
-   [ ] Strain relief added.
-   [ ] Components securely mounted.
-   [ ] Final calibration completed.
-   [ ] Firmware backed up.
-   [ ] Supported gestures documented.
-   [ ] Known limitations documented.

The supplied final checklist contains the same major completion
criteria, including all 10 flex sensors, both IMUs, both glove outputs,
gesture testing, speech-to-text, two-way communication, battery safety,
wiring protection, firmware backup, and documented limitations.
fileciteturn1file6L540-L561

------------------------------------------------------------------------

# 51. One-Minute Explanation for Judges

> **Hasth Vani is a smart two-glove communication prototype designed for
> two-way interaction. The right glove uses five flex sensors and an
> MPU6050 to capture finger positions and hand orientation. An ESP32
> processes these readings and recognizes predefined gestures. The
> recognized gesture is converted into an audio message using a
> MAX98357A amplifier and a speaker.**
>
> **For the reverse direction, a communication partner speaks into our
> mobile application. The app converts speech into text and sends it
> wirelessly to the ESP32 on the left glove, where a TFT display shows
> the message to the wearer.**
>
> **So our system creates two communication paths: SIGN → VOICE and
> VOICE → TEXT. The prototype also uses calibration, multiple
> consecutive readings, an unknown state, and cooldown logic to improve
> reliability.**

------------------------------------------------------------------------

# 52. Technical Summary

``` text
PROJECT
Hasth Vani

TYPE
Wearable two-way communication prototype

INPUTS
10 Flex Sensors
2 MPU6050 IMUs
Mobile Phone Microphone

PROCESSING
2 × ESP32

OUTPUTS
TFT Display
MAX98357A + 3 W Speaker
Mobile Application

COMMUNICATION
Bluetooth / Wi-Fi

SENSOR INTERFACES
Flex → ADC
MPU6050 → I²C
TFT → SPI
MAX98357A → I²S

MAIN DIRECTIONS
SIGN → VOICE
VOICE → TEXT

CURRENT RECOGNITION APPROACH
Calibrated sensor ranges + orientation + stability checking + cooldown

FUTURE INTELLIGENCE
Machine-learning-based gesture recognition
```

------------------------------------------------------------------------

# 53. Final Conclusion

**Hasth Vani is a wearable communication bridge rather than simply a
glove with sensors.**

Its architecture combines:

-   Finger-bend sensing
-   Hand orientation sensing
-   ESP32 embedded processing
-   Gesture recognition
-   Audio generation
-   Text display
-   Wireless communication
-   Mobile speech-to-text

The left glove provides a visual communication channel, while the right
glove provides an audio communication channel. The mobile application
completes the reverse communication path.

The prototype documentation emphasizes a staged development process:
first validate individual sensors, then calibration, then gesture
recognition, then each output subsystem, followed by mobile integration
and finally the complete two-way demonstration.
fileciteturn1file5L464-L485

The most important next step is **not immediately adding more
features**. It is to make the basic pipeline reliable:

``` text
FINGER BEND
     ↓
SENSOR VALUES
     ↓
CALIBRATION
     ↓
GESTURE RECOGNITION
     ↓
RELIABLE OUTPUT
     ↓
TWO-WAY COMMUNICATION
```

Once that foundation works consistently, Hasth Vani can be extended
toward a more compact wearable device, a larger gesture vocabulary,
personalized calibration, machine-learning recognition, better language
support, and a more polished mobile application.

------------------------------------------------------------------------

## Source Basis

This document is based primarily on the supplied **Hasth Vani Prototype
Build Plan --- Illustrated Directions**, **Hasth Vani Prototype Build
Plan --- With Directions**, the supplied prototype design image, and the
supplied wiring reference. Component roles, suggested wiring,
gesture-recognition flow, mobile communication flow, power guidance,
testing sequence, team roles, checklist, and troubleshooting have been
kept aligned with those materials.

**Important engineering note:** the supplied documents repeatedly state
that actual module pin labels, voltage requirements, TFT interfaces, and
amplifier connections must be verified against the exact hardware before
permanent wiring. The prototype's GPIO values are therefore a
**suggested design map**, not a universal pinout.
