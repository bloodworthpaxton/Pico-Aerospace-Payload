# ENGINEERING JOURNAL: FLIGHT PAYLOAD DEVELOPMENT
**Project:** Modular Aerospace Telemetry Unit (CanSat Standard)  
**Author:** Paxton Bloodworth  
**Grade:** 11   
**Target Program:** Aerospace Engineering, UT Austin (Cockrell School)  

---

## 1. DESIGN OBJECTIVES & CONSTRAINTS
The objective of this phase is to design a 3D-printable internal mounting sled to securely house a flight computer and sensor array. The design must integrate all hardware and software components while strictly adhering to international competition envelope restrictions.

### Chassis Space Envelope:
*   **External Frame Limit:** Hollow cylinder, 115.0 mm high × 66.0 mm outer diameter.
*   **Chassis Wall Thickness:** 2.0 mm structural wall.
*   **Internal Clearance Limit (\(D_{max}\)):** 62.0 mm maximum to prevent internal mechanical binding.
*   **Target Sled Boundary:** 58.0 mm – 60.0 mm to allow smooth translation inside the tube.

---

## 2. HARDWARE ARCHITECTURE & BALANCING
To avoid inflated marketplace markups and generic software libraries, standard commercial electronics bundles were passed over. Components were individually sourced from domestic suppliers to ensure exact technical documentation and layout control.

### Hardware Bill of Materials:
1.  **Microcontroller:** Raspberry Pi Pico 2W (Pre-soldered headers). Chosen for its dual-core processing capability, direct CircuitPython support, and integrated Wi-Fi telemetry broadcasting.
2.  **Altimeter:** GY-BMP280 Barometric Pressure Sensor.
3.  **Inertial Measurement Unit (IMU):** Adafruit MPU-6050 Accelerometer and Gyroscope.
4.  **Actuator:** SG90 9g Micro Servo Motor (for managing altitude-dependent parachute deployment sequences).
5.  **Prototyping Medium:** Full-sized 830-point solderless breadboard (modified to fit tight dimensional bounds).

---

## 3. MATHEMATICAL BASELINES & SOFTWARE LOGIC

### Local Pressure Baseline (\(P_0\))
The digital altimeter relies on a fixed sea-level pressure variable to calculate height shifts independent of localized weather fronts.
*   **Ontario Baseline Pressure (\(P_0\)):** `1026.0 hPa` (High-pressure system datum).
*   **Altimeter Formula Matrix:** 
    \[Altitude = 44330 \times \left(1.0 - \left(\frac{P}{1026.0}\right)^{0.1903}\right)\]

### Script Verification (`altimeter_math_test.py`)
To validate the equation framework before hardware deployment, I ran a Python simulation loop on macOS using an M3 architecture. The code initialized at a simulated room pressure (\(P\)) of `1020.0 hPa` and decremented pressure by `1.5 hPa` per iteration to mimic a rapid vertical ascent.
*   **Initial Calculative Output:** `49.45 meters` above sea level.
*   **Result:** Software math logic, loop counters, and floating-point precision parameters performed with zero syntax errors.

---

## 4. MECHANICAL CAD DIMENSION TRACE (ITERATION 1)

### The Spatial Orientation Bottleneck:
A standard 830-point breadboard measures 165.0 mm × 55.0 mm. Because the board length (165.0 mm) physically exceeds the total cylinder height restriction (115.0 mm), a standard vertical layout is impossible. 

*   **Engineering Fix:** I decided to perform a precision physical cut across the plastic breadboard grid rows, dropping the footprint down to an adjusted sizing of **115.0 mm (Length) × 55.0 mm (Width)**.

### Sled Layout Design (Onshape):
To construct physical retention rims around the shortened board, I calculated the structural wall profiles to shield the electronics without creating volume interference:
*   **Internal Slot Targets:** 115.0 mm Length × 55.0 mm Width.
*   **Wall Parameters:** 2.0 mm thick outer perimeter, 2.0 mm height.
*   **Floor Thickness:** 2.0 mm structural base (adjusted from 0.5 mm to ensure high structural integrity under printing and handling stresses).
*   **External Component Dimensions:** **119.0 mm Length × 59.0 mm Width**.

---

## 5. GEOMETRIC RE-ENGINEERING & DESIGN PIVOT

### Dimensional Interference Discovery:
An evaluation of the rectangular sled geometry against the circular vehicle tube revealed a critical clearance failure. Using the Pythagorean theorem to calculate the corner-to-corner diagonal distance:
\[\text{Diagonal} = \sqrt{119^2 + 59^2} = 132.82\text{ mm}\]

Because the diagonal footprint (\(132.82\text{ mm}\)) drastically overshot the maximum internal clearance diameter of the rocket tube (\(60.0\text{ mm}\)), the sharp corners made the assembly physically impossible to insert.

```unset
       GEOMETRIC INTERFERENCE VISUALIZATION 
       
              /XXXXXXXXXXXXX\         ◄── 60.0 mm Internal Cylinder
            /XX             XX\
           /X  ┌───────────┐  X\
          │X   │           │   X│
          │X   │ 59mm Box  │   X│     ◄── Width Clearance: Valid
          │X   │           │   X│
           \X  └───────────┘  X/
            \XX             XX/       ◄── CORNERS BREACH TUBE WALLS
              \XXXXXXXXXXXXX/             (132.82 mm Diagonal vs 60.0 mm Space)
```

### The Conformal Capsule Solution:
To bypass this structural roadblock, I modified the base sketch layout in Onshape from a sharp rectangle to a conformal capsule profile (a circular-ended sled) matching the inner curve of the rocket vehicle.

1.  I deleted the flat vertical end walls of the rectangle in Sketch 1.
2.  I used the 3-Point Arc tool to bridge the horizontal spans with curved boundaries bowing outward.
3.  I constrained the arc radius to `30.0 mm` using the dimension tool. 

This update sets the width of the curved ends to exactly `60.0 mm`, allowing the entire component sled to utilize maximum layout volume while sliding smoothly inside the circular chassis without scraping.

```unset
        UPGRADED CAPSULE MODEL SLED
       
              /XXXXXXXXXXXXX\         ◄── 60.0 mm Rocket Tube
            /XX  ┌───────┐  XX\
           /X   (  Cargo  )   X\      ◄── Conformal Curved Ends
          │X     └───────┘     X│         Perfectly Clear Internal Shell
           \X                 X/
            \XX             XX/
              \XXXXXXXXXXXXX/
```
### Potential Mission: Autonomous Active Parachute Deployment Control
The idea of my 'mission' is to ultimately turn this satellite into an atmospheric probe that maps out invisible wind currents. 
As the satellite drops in altitude, it will inevitably sway and experience turbulence. The Python code that I will write will consult a MPU-6050 Gyroscope to track rotational forces and then reference them with the BMP280's pressure altitude readings; with that data I will create a 3d graph demonstrating just how turbulent air is at specific altitudes, which can be helpful data for aerospace teams planning a rocket launch.

