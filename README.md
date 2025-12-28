# prediction-machine

An interactive “prediction machine” inspired by Ted Chiang’s *The Button*: a system that asks you to perform a simple action, claims it can judge you, and attempts to predict your behavior based on prior states.

The piece uses a webcam to detect a face and track small head movements as a left/right “choice.” It then displays a prediction (an arrow) and permanently marks each round with a symbol on the screen, creating a visual record of interaction, belief, and compliance over time.

---

## Concept

**prediction-machine** explores belief under uncertainty:  
how quickly we accept a system’s authority when it offers prediction, and how our bodies become part of a closed feedback loop.

Themes / questions:
- **Control vs. uncertainty**: when outcomes feel uncontrollable, we reach for prediction.
- **Real vs. constructed intelligence**: is the system “knowing,” or are we supplying meaning?
- **Surveillance and extraction**: the green facial bounding box frames the user as data.
- **Early photography and the “stolen soul”**: the camera as an apparatus that claims more than it shows.
- **Arbitrary rules**: simple gestures are transformed into judgments that feel consequential.

---

## How it works (experience)

Each round:
1. A countdown begins.
2. You are prompted to **move** (left or right).
3. The system detects your movement from facial feature tracking.
4. The machine displays a **prediction arrow**.
5. Feedback is rendered visually:
   - **Black circle** = alignment between prediction and movement
   - **Red X** = divergence
6. These marks **accumulate permanently**, forming a growing visual archive of interaction.

Breathing is tracked continuously via subtle vertical displacement in a chest region-of-interest. After **25 breaths**, the system stops and displays a final message indicating the total number of breaths taken.

---

## Prediction model

The machine adapts over time using a **Markov chain–inspired prediction model**.

- Each movement (LEFT or RIGHT) is treated as a state.
- The system compares the current movement to recent prior states.
- Transition tendencies between states are reinforced over repeated rounds.
- Predictions increasingly reflect observed patterns in the user’s behavior.

Rather than reacting to a single gesture, the system builds expectation from **state-to-state transitions**, encouraging the perception of learning, memory, and intent.

---

## Tech stack

- **Processing (Java mode)**
- `processing.video` for webcam capture
- **OpenCV for Processing** (`gab.opencv`) for face detection
- Facial feature–based movement classification (nose position deltas)
- Persistent generative marks rendered as stored shape objects

---

## Setup

### Requirements
- Processing (tested in Processing 4.x)
- OpenCV for Processing  
  (`Sketch → Import Library → Add Library… → OpenCV for Processing`)
- A webcam

### Run
1. Open the sketch in Processing
2. Press **Run**
3. Stand in view of the camera and follow the on-screen prompts
