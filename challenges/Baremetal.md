# Smartwatch Firmware Challenge



### Introduction



You are building the firmware for a compact smartwatch that monitors pulse, provides health/activity indicators, responds to touch gestures, and recovers from sudden power loss.

The firmware must operate under strict memory and real-time constraints. Don't worry about completing every task, focus on doing as much as you can thoroughly and submit how much ever progress you've made.



### Constraints

* Feel free to use any board of choice, or simulator like Wokwi
* `sizeof(EngineState) <= 512 bytes` — enforced using `static\_assert`.
* All smartwatch state, buffers, counters, and flags must be inside `EngineState`.
* No `malloc`, `calloc`, or `free`.
* No `float` or `double`; use integer arithmetic only.
* Use GPIOs, Timers, ADCs, and Flash arrays only.
* Can use CMSIS Header files, but use of HAL libraries and functions are not to be used.
* ISRs must be non-blocking.



### Hardware

|Component|Function|
|-|-|
|PPG Sensor|Pulse readings|
|Side Crown Button|Simulated battery brownout|
|Front Touch Key|Tap and long-press input|
|Green LED|Activity / heartbeat|
|Red LED|Pulse spike|
|Blue LED|Emergency SOS|



\---



### Task 1 — Pulse Monitoring

Process **2,000 raw 16-bit PPG samples** one at a time.

The watch must continuously maintain:

* Rolling peak pulse amplitude
* Rolling average pulse floor

The raw pulse buffer inside `EngineState` must use **less than 64 bytes**.

\---



### Task 2 — Brownout Recovery

Pressing the Side Crown Button simulates a sudden battery failure.

The watch should save its current state and halt.

After reset:

* A valid checkpoint → **resume from the saved sample index**
* Invalid checkpoint → **restart from sample 0**

The checkpoint must include the sample index, rolling average, and validation tag `0xA5`.

\---



### Task 3 — Watch Indicators

* **Green LED:** toggles every 50 processed samples.
* **Red LED:** turns ON when the incoming pulse is **50% or more above the running average**, and turns OFF when it falls below the threshold.

\---



### Task 4 — Touch Gestures



##### Double-Tap


Two taps within **200–500 ms** toggle between:

**ACTIVE\_MODE**

* Normal LED operation
* Pulse tracking active

**STEALTH\_MODE**

* All LEDs OFF
* Pulse tracking continues

### Long Press

Holding the Front Touch Key for **≥ 2 seconds**:

* Turns the **Blue LED ON**
* Forces the watch into `ACTIVE\_MODE`

\---

### Deliverables

##### Source Files

* Must include all files written by you
* Makefile and headers that you used
* Any drivers/ function files used

##### Demonstration

Show:

* Continuous pulse processing and Green LED activity
* Red LED response to a pulse spike
* Brownout → halt → reset → state recovery
* Double-tap → Stealth Mode
* Long-press → SOS

##### Memory Report

Provide a table showing the size of every `EngineState` variable and proving:

```text
EngineState <= 512 bytes
Raw pulse buffer < 64 bytes
```

The final firmware should demonstrate a **functional, memory-constrained smartwatch engine** with pulse monitoring, power recovery, indicators, and gesture-based controls.

