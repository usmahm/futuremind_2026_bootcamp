# Day 2 Student Lab — Make the Robot Move

## Today’s mission

Use friendly movement functions, speed values, and measured times to make a four-wheel robot drive a square.

## How the robot turns

The car uses **differential drive**. Its two left motors are connected and controlled as one side, while its two right motors are connected and controlled as the other side. It changes direction by driving the two sides differently.

| Robot action | Left side: two wheels | Right side: two wheels |
| ------------ | --------------------- | ---------------------- |
| Forward      | Forward               | Forward                |
| Backward     | Backward              | Backward               |
| Spin left    | Backward              | Forward                |
| Spin right   | Forward               | Backward               |
| Stop         | Stop                  | Stop                   |

The Arduino sends control signals, but the L298N and motor battery provide the motor power. Never connect a DC motor directly to an Arduino output pin.

## Words to know

| Word         | Meaning                                                                                     |
| ------------ | ------------------------------------------------------------------------------------------- |
| Actuator     | A component that creates a physical action.                                                 |
| Circuit      | A complete path of connected electrical components through which electric current can flow. |
| Motor driver | A circuit that controls power to motors.                                                    |
| Function     | A named block of instructions.                                                              |
| Argument     | Information placed inside a function’s parentheses.                                         |
| Calibration  | Measuring and adjusting for the real robot and environment.                                 |

### Understanding the Code

Your project contains two tabs: the instructor helper file and your student challenge sketch.

#### 1. The Helper Functions (Under the Hood)

The instructor tab translates high-level commands into pin signals for the L298N motor driver:

* `robotBegin()`: Configures digital pins 5, 6, 7, 8, 11, and 12 as outputs to control motor power and direction.


* `moveForward(speed)` and `moveBackward(speed)`: Commands both the left and right motor pairs to spin in the same direction at a speed between `0` (stopped) and `255` (full speed).


* `turnLeft(speed)` and `turnRight(speed)`: Drives one side forward and the opposite side backward to pivot the chassis on the spot (skid-steering).


* `stopRobot()`: Shuts off motor power immediately by setting PWM pins and directional inputs to zero.



#### 2. The Student Sketch Structure

* **`setup()`**: Executes once when the Arduino boots up or resets. Your entire driving sequence lives here so the square is completed once and does not repeat infinitely.


* **`loop()`**: Executes repeatedly forever. It contains only `stopRobot()` to ensure the car stays safely parked after finishing its route.


* **Timing & Calibration Constants**:

| Constant | Default Value | Role |
| --- | --- | --- |
| `DRIVE_SPEED` | `140` | Straight-line motor power ($0$ to $255$). |
| `TURN_SPEED` | `130` | Pivot motor power ($0$ to $255$). |
| `STRAIGHT_TIME_MS` | `1200` | Duration to travel one full side length ($1000\text{ ms} = 1\text{ second}$). |
| `LEFT_90_TIME_MS` | `600` | Duration to complete a precise $90^\circ$ left pivot turn. |
| `PAUSE_BETWEEN_MOVES_MS` | `300` | Settling delay to kill physical inertia before the next movement. |

---

### Step-by-Step Lab Procedure

#### Step 1: Bench Safety Check ("Wheels-Up")

Never place an unverified robot directly on the floor.

1. Prop your robot car on a stand or block so all four wheels spin freely in the air.
2. In `setup()`, find **CHECKPOINT 1** and remove the `//` comment markers:
```cpp
moveForward(DRIVE_SPEED);
delay(700);
stopRobot();

```


3. Upload the sketch. After a 2-second safety delay, all four wheels must rotate forward for 0.7 seconds and stop. If any wheel spins backward, alert your instructor to correct the motor wiring.


4. Add `//` back to the Checkpoint 1 lines before moving forward.

#### Step 2: Calibrate the Straight Side

1. Place a piece of masking tape on the floor as your starting line and another at your target distance (such as 1 meter).
2. Test one straight drive in your challenge area:
```cpp
moveForward(DRIVE_SPEED);
delay(STRAIGHT_TIME_MS);
stopRobot();

```


3. Run the car on the floor:
* **Stops before the mark:** Increase `STRAIGHT_TIME_MS` (e.g., from `1200` to `1400`).
* **Drives past the mark:** Decrease `STRAIGHT_TIME_MS` (e.g., from `1200` to `1000`).


4. Repeat until the front bumper stops consistently at your target tape line.

#### Step 3: Calibrate the $90^\circ$ Pivot

1. Align the car's wheels along a floor tile seam or a taped cross on the floor.
2. Run a single turn command:
```cpp
turnLeft(TURN_SPEED);
delay(LEFT_90_TIME_MS);
stopRobot();

```


3. Inspect the car’s final heading against the perpendicular line:
* **Under-turning ($< 90^\circ$):** The car did not turn enough. Increase `LEFT_90_TIME_MS` by 30–50 ms.
* **Over-turning ($> 90^\circ$):** The car turned too far. Decrease `LEFT_90_TIME_MS` by 30–50 ms.



---

### Step 4: Assemble the Full Square

Once your straight distance and turn timing are calibrated, you have two ways to program the full square.

**Option A: Sequential Blocks (Junior Level)**

Copy and paste the side-and-turn block 4 times inside the `STUDENT CHALLENGE AREA`:

```cpp
// Side 1
moveForward(DRIVE_SPEED);
delay(STRAIGHT_TIME_MS);
stopRobot();
delay(PAUSE_BETWEEN_MOVES_MS);
turnLeft(TURN_SPEED);
delay(LEFT_90_TIME_MS);
stopRobot();
delay(PAUSE_BETWEEN_MOVES_MS);

// Repeat Side 2, Side 3, and Side 4 below...

```

**Option B: The Loop Pattern (Senior Level)**

Instead of repeating code manually, use a `for` loop to repeat the pattern 4 times cleanly:

```cpp
for (int side = 0; side < 4; side++) {
  moveForward(DRIVE_SPEED);
  delay(STRAIGHT_TIME_MS);
  stopRobot();
  delay(PAUSE_BETWEEN_MOVES_MS);

  turnLeft(TURN_SPEED);
  delay(LEFT_90_TIME_MS);
  stopRobot();
  delay(PAUSE_BETWEEN_MOVES_MS);
}

```

---

### Engineering Tips & Pitfalls

* **Battery Fade:** DC motors spin slower as battery packs drain. If your robot turned perfectly 15 minutes ago but is now under-turning, recharge or swap your batteries rather than drastically changing your code.
* **Floor Friction:** If you calibrate on smooth tile and then test on rough carpet, the tires will grip differently and your turns will under-rotate. Always calibrate on the test surface.
* **Veering to One Side:** Cheap DC hobby motors rarely rotate at the exact same RPM. If your car curves slightly during straight drives, you can adjust the helper tab to send a slightly higher PWM speed to the slower motor side.