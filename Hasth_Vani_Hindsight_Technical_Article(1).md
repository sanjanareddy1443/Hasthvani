# Building Hasth Vani Around a Reliable Communication Pipeline---and Adding Persistent Memory

A wearable communication system looks simple from the outside: move a
hand, recognize the gesture, and produce a message. In practice, the
difficult part is making every step between those events predictable.

With Hasth Vani, I worked around that problem by separating the system
into sensing, gesture recognition, communication, and application-level
context. The result is a two-glove communication prototype that supports
two directions: SIGN → VOICE and VOICE → TEXT.

One additional requirement changes how I think about the software layer:
the system should not have to start from zero every time a new
interaction begins. That is where Hindsight fits. It belongs above the
real-time glove firmware, where it can provide persistent memory for
conversations, preferences, and other durable application context.

## What I built

Hasth Vani uses two gloves with different primary output roles.

The right glove is the audio path. Five flex sensors and an MPU6050
provide finger and hand information to an ESP32. The ESP32 processes the
readings and recognizes predefined gestures. A recognized gesture can
then be converted into a predefined spoken message through the audio
output chain.

The left glove is the display path. Its five flex sensors and MPU6050
provide another sensing input, while the ESP32 receives text from the
communication system and displays it on a TFT.

The mobile application provides the reverse communication path. A
communication partner speaks into the phone, speech is converted into
text, and the text is sent wirelessly to the display glove.

The overall flow is:

``` text
SIGN → VOICE
hand movement
    ↓
flex sensors + MPU6050
    ↓
ESP32
    ↓
gesture recognition
    ↓
audio output
    ↓
spoken message
```

and:

``` text
VOICE → TEXT
speech
    ↓
speech-to-text
    ↓
wireless communication
    ↓
ESP32
    ↓
TFT display
```

The important architectural decision is that these paths do not need to
share one large piece of firmware. Each layer has a clear
responsibility.

## The first problem was sensor variability

Flex sensors do not give me a universal "finger bent" value. Their
resistance changes with bending, and the measured ADC value also depends
on the physical sensor, mounting position, glove fit, wiring, and user.

The prototype uses a voltage-divider arrangement:

``` text
3.3 V
  │
Flex Sensor
  │
  ├──→ ESP32 ADC
  │
10 kΩ
  │
 GND
```

That means calibration is part of the system, not an optional setup
step.

Instead of assuming a fixed threshold, I treat each sensor as a
calibrated range. A practical calibration sequence is:

``` text
straight
   ↓
half bent
   ↓
fully bent
```

Those measurements give the recognition logic a reference for the actual
glove.

This is one of the places where a prototype can become unreliable very
quickly. A threshold that works on one glove may not work after a sensor
is repositioned or the glove is worn by another person. Range-based
calibration gives the firmware a more useful representation of the
physical device.

## Finger position alone is not enough

Five flex sensors tell me about finger configuration, but they do not
completely describe the hand.

Two gestures can have similar finger positions while differing in wrist
orientation or movement. The MPU6050 provides accelerometer and
gyroscope data that can be combined with the flex readings.

I therefore think of a gesture as:

``` text
finger configuration
        +
hand orientation / movement
        =
gesture candidate
```

The prototype documentation keeps this recognition approach deliberately
simple: calibrated sensor ranges, orientation information, stability
checking, an explicit unknown state, and cooldown logic.

That is important because I do not want to claim that the current
prototype already uses a trained machine-learning model. Machine
learning is a future extension, not something I should describe as
implemented unless it has actually been built and tested.

## I do not trust a single sensor reading

A gesture recognizer that immediately acts on one sample is likely to
produce false triggers.

Sensor values fluctuate. A user can also hold a gesture for several
seconds. Without stability and cooldown logic, one intentional gesture
could produce the same message repeatedly.

The recognition path is therefore closer to:

``` text
read sensors
     ↓
calibrate / normalize
     ↓
compare with gesture ranges
     ↓
check consecutive stable readings
     ↓
known gesture?
   ↙       ↘
 yes       no
  ↓         ↓
output    UNKNOWN
  ↓
cooldown
```

A simplified firmware structure is:

``` cpp
void loop() {
    readFlexSensors();
    readMPU6050();

    normalizeSensorValues();
    identifyGesture();

    if (gestureIsStable() && cooldownFinished()) {
        processGesture();
        startCooldown();
    }

    handleWirelessCommunication();
    updateOutput();
}
```

The exact function names are illustrative, but the separation is
important. Sensor acquisition, recognition, communication, and output
should not become one tangled block.

The prototype also uses an example cooldown of about 1.5 seconds. That
is a tuning parameter rather than a universal value.

The `UNKNOWN` state is equally important. If the sensor pattern does not
confidently match a known gesture, the system should not invent a
message.

## Where Hindsight belongs

Hindsight is useful for a different problem.

The ESP32 needs to make real-time decisions. It should read sensors,
recognize a gesture, control outputs, and communicate with the rest of
the system without waiting for a memory system to reason about every ADC
sample.

Hindsight belongs at the application layer.

Hindsight provides three core operations: `retain`, `recall`, and
`reflect`. `retain` stores information in a memory bank, `recall`
retrieves relevant memories, and `reflect` reasons over stored memories
to produce a synthesized response. citeturn0search3turn0search5

That gives Hasth Vani two different kinds of state:

``` text
REAL-TIME STATE

flex + IMU
    ↓
gesture recognition
    ↓
voice / text output
```

and:

``` text
LONG-TERM APPLICATION STATE

conversation / preference / event
    ↓
Hindsight memory bank
    ↓
recall or reflect
    ↓
future application context
```

The distinction prevents me from turning Hindsight into a database for
raw sensor data.

I would retain semantic information such as:

``` text
conversation events
user communication preferences
application decisions
supported gesture mappings
relevant interaction history
```

I would not continuously retain every flex-sensor ADC sample or every
IMU reading. Those belong to the real-time embedded pipeline or, when
needed for machine-learning development, to a dedicated sensor dataset.

## A careful Hindsight integration

The important correction to the earlier article is that Hindsight should
not be described as already integrated into the physical prototype
unless there is actual implementation code proving that.

For the Hasth Vani architecture, the intended integration boundary is:

``` python
client.retain(
    bank_id=user_id,
    content=conversation_event
)

memories = client.recall(
    bank_id=user_id,
    query=current_context
)
```

This is an integration pattern, not a claim that these calls are already
running inside the glove firmware.

If the application later needs a higher-level answer based on several
memories, it can use reflection:

``` python
response = client.reflect(
    bank_id=user_id,
    query=current_context
)
```

Hindsight's current documentation distinguishes these operations
clearly: recall is retrieval, while reflect performs reasoning over
retrieved memories. citeturn0search0turn0search8

That distinction is useful for Hasth Vani. A simple preference lookup
should not require an unnecessary reasoning step.

For example:

``` text
"What display language does this user prefer?"
        ↓
recall
```

whereas a broader application question such as:

``` text
"What communication pattern should the application remember from
previous interactions?"
        ↓
reflect
```

could justify a reasoning step.

## One interaction across the whole system

Consider the prototype gesture `I NEED WATER`.

The right glove reads five flex sensors and the MPU6050. The ESP32
evaluates the calibrated readings and accepts the gesture only after the
stability checks.

The communication path is then:

``` text
I NEED WATER gesture
        ↓
flex + IMU
        ↓
ESP32
        ↓
gesture recognized
        ↓
audio output
        ↓
spoken message
```

Now consider the reverse direction.

A communication partner says:

``` text
"Do you need water?"
```

The mobile application receives the speech, converts it to text, and
sends the text wirelessly to the left glove.

``` text
speech
   ↓
phone
   ↓
speech-to-text
   ↓
wireless message
   ↓
left ESP32
   ↓
TFT
```

Hindsight does not need to sit between the TFT and the ESP32. Instead,
the application can use memory before or after the interaction when
durable context is relevant.

For example, the application could remember a communication preference
or a previous interaction, then provide that context to the next
application-level operation.

That is a much cleaner boundary than trying to make the wearable
hardware itself responsible for agent memory.

## The hardware pipeline should stay deterministic

There is a temptation to add intelligence everywhere.

I deliberately would not.

The embedded path should remain easy to inspect:

``` text
sensor reading
    ↓
calibration
    ↓
normalization
    ↓
gesture rule
    ↓
stability check
    ↓
cooldown
    ↓
output
```

If the gesture is wrong, I can debug that pipeline.

If the mobile response is wrong, I can debug:

``` text
microphone
    ↓
speech-to-text
    ↓
application message
    ↓
wireless transport
    ↓
left ESP32
    ↓
TFT
```

If application context is wrong, I can inspect:

``` text
event
    ↓
retain
    ↓
memory bank
    ↓
recall / reflect
    ↓
application context
```

Those boundaries make failures easier to locate.

## What I learned from the design

### Calibration is part of the product

The sensor reading is only useful after I know what that reading means
for the particular glove. Calibration has to account for physical
variation instead of assuming universal ADC thresholds.

### Unknown is a valid result

A communication system should prefer an explicit unknown state over
confidently producing the wrong message.

### Reliability comes from small controls

Multiple samples, normalization, threshold ranges, orientation data, and
cooldown are individually simple. Together they make gesture recognition
much more predictable.

### Real-time state and long-term memory are different

The glove needs immediate sensor processing. The application needs
durable context. Hindsight is appropriate for the second problem, not
the first.

### Machine learning should be earned by data

The current documented recognition approach is rule and range based. A
future ML system can be trained from collected flex and IMU data, but
that should be presented as a future improvement rather than a feature
that already exists.

### Build the vertical slice first

The development sequence should remain incremental:

``` text
one flex sensor
    ↓
five flex sensors
    ↓
calibration
    ↓
MPU6050
    ↓
one gesture
    ↓
more gestures
    ↓
TFT
    ↓
audio
    ↓
mobile application
    ↓
two-way communication
    ↓
final calibration
```

Only after this basic path is reliable does it make sense to add a
larger gesture vocabulary, ML recognition, personalization, or more
advanced language features.

## The architecture I would keep

The clean architecture is:

``` text
┌─────────────────────────────────────────┐
│ Application Layer                       │
│ Mobile app + speech-to-text + context  │
│                                         │
│ Hindsight: retain / recall / reflect   │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│ Communication Layer                     │
│ Wireless messages + audio + TFT        │
└────────────────────┬────────────────────┘
                     │
┌────────────────────▼────────────────────┐
│ Embedded Layer                          │
│ Flex + MPU6050 + ESP32                 │
│ Calibration + gesture recognition     │
└─────────────────────────────────────────┘
```

The prototype documentation describes Hasth Vani as a two-way wearable
communication system built around ten flex sensors, two MPU6050 IMUs,
two ESP32 boards, a TFT display, an audio output path, wireless
communication, and mobile speech-to-text.

The same documentation also identifies important limitations: the
gesture vocabulary is predefined, calibration depends on the physical
setup, some gestures can be similar, battery and wearability are
constraints, and machine-learning recognition remains a future
extension.

That is a useful engineering boundary to keep.

Hasth Vani does not need to become an "AI glove" in order to be
technically interesting. The more important achievement is a reliable
chain from physical movement to digital communication, with a clear
application layer that can remember useful context when the system grows
beyond a single interaction.

For me, the key lesson is simple: keep the real-time path deterministic,
keep uncertainty explicit, and put persistent memory where it actually
belongs---the software layer that understands the interaction over time.
