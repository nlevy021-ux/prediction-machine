import gab.opencv.*;
import processing.video.*;
import java.awt.*;
import java.util.ArrayList;

Capture video;
OpenCV opencv;

int maxBreaths = 25; // Set the desired number of breaths for "game over" condition

// Movement tracking variables
int previousNoseX = -1;
int previousLeftEyeX = -1;
int previousRightEyeX = -1;
int movementThreshold = 10;
int countdownTime = 5000;
int movementTime = 1000;
int timer;
boolean actualMovementCaptured = false;
int breathCount = 0; // Initialize breath count

int attempts = 0; // Tracks the number of calls to freezeMovement
float initialErrorRate = 0.35; // Starting error rate at 25%

// Optical flow variables for breath tracking
PVector prevChestPosition;
float prevVerticalDisplacement = 0;
float breathThreshold = 1.5; // Adjust as needed for sensitivity

// Enum and other movement tracking variables
enum MovementState { LEFT, RIGHT, NO_MOVEMENT }
MovementState currentState = MovementState.NO_MOVEMENT;
MovementState actualMovement = MovementState.NO_MOVEMENT;
MovementState displayedMovement = MovementState.NO_MOVEMENT;

ArrayList<Shape> shapes = new ArrayList<>();

void setup() {
  size(960, 720);
  video = new Capture(this, 960, 720);
  opencv = new OpenCV(this, 960, 720);
  opencv.loadCascade(OpenCV.CASCADE_FRONTALFACE);
  
  video.start();
  timer = millis();
}

void draw() {
 if (breathCount >= maxBreaths) {
    displayGameOverScreen();
    return;
  }

  if (video.available()) {
    video.read();
  }
  
  pushMatrix();
  scale(-1, 1);
  image(video, -width, 0);
  popMatrix();
  
  opencv.loadImage(video);
  Rectangle[] faces = opencv.detect();

  for (Shape shape : shapes) {
    shape.display();
  }
  
  if (faces.length > 0) {
    Rectangle face = faces[0];
    face.x = width - (face.x + face.width);
    noFill();
    stroke(0, 255, 0);
    strokeWeight(2);
    rect(face.x, face.y, face.width, face.height);
    
    Point leftEye = new Point(face.x + face.width / 4, face.y + face.height / 4);
    Point rightEye = new Point(face.x + 3 * face.width / 4, face.y + face.height / 4);
    Point nose = new Point(face.x + face.width / 2, face.y + face.height / 2);
    
    String movement = updateMovementState(face, leftEye, rightEye, nose);
    currentState = getMovementState(movement);

    // Track breathing by monitoring chest movement
    trackBreathing(face);
  }

  manageCountdownAndMovement();

}

void trackBreathing(Rectangle face) {
  // Define chest ROI slightly below the nose
  PVector chestPosition = new PVector(face.x + face.width / 2, face.y + face.height * 0.75);

  // Initialize previous chest position if not set
  if (prevChestPosition == null) {
    prevChestPosition = chestPosition;
    return;
  }

  // Calculate vertical displacement between frames
  float verticalDisplacement = chestPosition.y - prevChestPosition.y;

  // Detect a breath cycle: from inhalation (up) to exhalation (down)
  if (prevVerticalDisplacement > breathThreshold && verticalDisplacement < -breathThreshold) {
    breathCount++; // Increment breath count on each cycle
  }

  // Update previous positions for the next frame
  prevChestPosition = chestPosition;
  prevVerticalDisplacement = verticalDisplacement;
}

void manageCountdownAndMovement() {
  int elapsedTime = millis() - timer;

  if (elapsedTime < countdownTime) {
    int secondsLeft = (countdownTime - elapsedTime) / 1000 + 1;
    fill(0,250,30);
    textSize(32);
    textAlign(CENTER, CENTER);
    text(secondsLeft, width / 2, height / 2);

    actualMovementCaptured = false;
    actualMovement = MovementState.NO_MOVEMENT;
    displayedMovement = MovementState.NO_MOVEMENT; // Reset displayed movement each countdown

  } else if (elapsedTime < countdownTime + movementTime + 500) {
    fill(0,250,30);
    textSize(32);
    textAlign(CENTER, CENTER);
    text("Move!", width / 2, height / 2);

    // Capture movement only if not already displayed
    if (!actualMovementCaptured && currentState != MovementState.NO_MOVEMENT) {
      freezeMovement();
      actualMovementCaptured = true;
    }

    // Show arrow immediately upon movement detection within the window
    if (displayedMovement != MovementState.NO_MOVEMENT) {
      drawArrowBasedOnDisplayedMovement();
    }
  } else {
    checkMovementAccuracy();
    timer = millis();
    actualMovementCaptured = false;
  }
}

void freezeMovement() {
  attempts++; // Increment the attempts on each call
  
  actualMovement = currentState; // Set the detected movement
  
  // Calculate the error rate using an exponential decay formula
  float errorRate = initialErrorRate * exp(-0.10 * attempts); // Error rate decreases with attempts
  
  float randomChance = random(1);
  if (randomChance < errorRate) {
    displayedMovement = (actualMovement == MovementState.LEFT) ? MovementState.RIGHT :
                        (actualMovement == MovementState.RIGHT) ? MovementState.LEFT :
                        MovementState.NO_MOVEMENT;
  } else {
    displayedMovement = actualMovement;
  }
}

// Function to check if the movement direction was correct or incorrect
void checkMovementAccuracy() {
  if (displayedMovement == actualMovement) {
    drawBlackCircle();
  } else {
    drawRedX();
  }
}

void displayBreathCountOverlay() {
  fill(0, 180); // Black with some transparency
  rect(0, 0, width, height);

  fill(255);
  textSize(32);
  textAlign(CENTER, CENTER);
  text("You took " + breathCount + " breaths", width / 2, height / 2);
}

// Draws a black circle when the movement is correct
void drawBlackCircle() {
  float radius = random(10, 50);
  float x = random(radius, width - radius);
  float y = random(radius, height - radius);
  Shape circle = new Shape(x, y, radius, color(0), "circle");
  shapes.add(circle);
}

// Draws a red X when the movement is incorrect
void drawRedX() {
  float x = random(50, width - 50);
  float y = random(50, height - 50);
  Shape xShape = new Shape(x, y, 20, color(255, 0, 0), "x");
  shapes.add(xShape);
}

// Draws an arrow based on the displayed movement direction
void drawArrowBasedOnDisplayedMovement() {
  float x = width / 2;
  float y = height / 4;

  fill(0, 0, 255);
  noStroke();
  float arrowLength = 60;
  float arrowWidth = 30;

  switch (displayedMovement) {
    case LEFT:
      triangle(x + arrowLength, y, x - arrowWidth, y - arrowWidth, x - arrowWidth, y + arrowWidth);
      break;
    case RIGHT:
      triangle(x - arrowLength, y, x + arrowWidth, y - arrowWidth, x + arrowWidth, y + arrowWidth);
      break;
    case NO_MOVEMENT:
      break;
  }
}

// Updates the movement state based on facial feature positions
String updateMovementState(Rectangle face, Point leftEye, Point rightEye, Point nose) {
  int noseX = nose.x;
  int leftEyeX = leftEye.x;
  int rightEyeX = rightEye.x;

  String movement = "No Movement";

  if (previousNoseX != -1) {
    int movementNose = noseX - previousNoseX;

    if (abs(movementNose) > movementThreshold) {
      if (movementNose < 0) {
        movement = "Right";
      } else {
        movement = "Left";
      }
    }
  }

  previousNoseX = noseX;
  previousLeftEyeX = leftEyeX;
  previousRightEyeX = rightEyeX;

  return movement;
}

// Converts a string movement to the MovementState enum
MovementState getMovementState(String movement) {
  switch (movement) {
    case "Left":
      return MovementState.LEFT;
    case "Right":
      return MovementState.RIGHT;
    default:
      return MovementState.NO_MOVEMENT;
  }
}

// Helper class for shapes to add them permanently to the screen
class Shape {
  float x, y, size;
  int col;
  String type;
  
  Shape(float x, float y, float size, int col, String type) {
    this.x = x;
    this.y = y;
    this.size = size;
    this.col = col;
    this.type = type;
  }
  
  void display() {
    fill(col);
    noStroke();
    if (type.equals("circle")) {
      ellipse(x, y, size * 2, size * 2);
    } else if (type.equals("x")) {
      stroke(col);
      strokeWeight(5);
      line(x - size, y - size, x + size, y + size);
      line(x - size, y + size, x + size, y - size);
    }
  }
}

void displayGameOverScreen() {
  background(255); // White background
  fill(0); // Black text
  textSize(32);
  textAlign(CENTER, CENTER);
  text("You took " + breathCount + " breaths so the prediction machine has stopped", width / 2, height / 2);
}
