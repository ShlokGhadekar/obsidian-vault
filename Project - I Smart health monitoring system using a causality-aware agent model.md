![[Pasted image 20260830101103.png]]


## Hardware Setup :

### **Air Quality Sensors:**

1.*MiCS-6814* 953/-
**measures** - CO, NO₂, NH₃, C₃H₈, C₄H₁₀, H₂, ethanol (3 elements in one package)
**size** - 20 * 20mm
**Justification -** Replaces three separate MQ sensors with one package. Heater draws only ~40–60 mA vs ~150 mA per MQ sensor, which matters a lot on a battery-powered wearable. Covers the general urban-pollution gases traffic police are exposed to (vehicle exhaust NOx, benzene-family VOCs).

**2.*MQ-7*** 125/-
**measures -** CO(dedicated)
**size -** 32 x 20mm can
**justification -** CO is the single most acute, fast-acting threat for both users — vehicle exhaust for traffic police, blasting/engine fumes for miners. A dedicated CO sensor gives better accuracy/response time than MiCS's general CO channel, so it's worth the extra current draw.

**3.*PMS5003*** 2200/-
**measures -** PM1.0, PM2.5, PM10 
**size -** 65x43x23mm
**justification -** Particulate matter is the dominant chronic-exposure pollutant for traffic police and mine dust for miners. Laser scattering is far more accurate than a resistive dust sensor. Can be duty-cycled (on for 30s every few minutes) to cut its ~100 mA active draw.

**4.*BME280*** 430/-
**measures -** Temperature, humidity, pressure
**size -** 10mm
**justification -** Tiny, negligible power. Temperature/humidity feed heat-stress calculations; pressure is a useful proxy for ventilation state in enclosed mine tunnels.

**5.*Electrochemical O2 sensor*** 5150/-
**measures -** oxygen %
**size -** 20mm disc
**justification -** Mining-specific but important: oxygen depletion in a confined shaft kills faster than most toxic gases. No heater — passive electrochemical cell, very low power.

### **Health Sensors**

*1.MAX30102* 200/-
**measures -** SpO₂, heart rate
**size -** 11x11mm
**justification -** Smallest reliable pulse-oximeter module, worn as a fingertip or earlobe clip on a short lead back to the belt unit. SpO₂ is the direct physiological consequence of CO/toxic-gas exposure — it's the key "effect" variable your causal model needs.

*2.MLX90614* 994/-
**measures -** Body temperature (non-contact IR)
**size -** ~17 mm TO-39 can
**justification -** No electrode/skin contact needed — just faces the wrist skin from inside the strap. More hygienic and easier to mount reliably than a contact thermistor.

*3.MPU6050* 280/-
**measures -** Accelerometer + gyroscope
**size -** 21x16mm
**justification -** Two jobs: fall/collapse detection (critical if someone passes out from gas exposure), and separating exertion-driven heart-rate rise from toxin-driven rise — without a motion signal your causal model can't tell "he's running" from "he's poisoned.

### **Power System**

- **2× 18650 Li-ion cells in parallel** (3.7 V, 3400 mAh each → 6800 mAh) → **TP4056** charge/protection module → **MT3608** boost converter to a regulated 5 V rail.

### **Additional components** 

1.**An ADC** — the Pi has zero analog pins, and three of your sensors (MiCS-6814, MQ-7, O2 cell) are analog. **MCP3008** is the standard choice. 540/-
**2.Buzzer** 10/-
**3.LED** 5/-
4.**Signal conditioning between those analog sensors and the ADC** — MiCS-6814 and MQ-7 output up to 5V, but the MCP3008 has to run at 3.3V to keep its SPI output safe for the Pi's GPIO (feeding it 5V would risk damaging the Pi's SPI input pin). So you need resistor voltage dividers to scale 5V down to ≤3.3V on each gas-sensor channel. The O2 cell is a separate case — if it's a bare electrochemical cell it outputs microamps and needs a transimpedance op-amp (e.g. MCP6002) to turn that into a voltage; if you bought a pre-amplified O2 breakout module, it likely already outputs 0–2V or 0–3V and can skip straight to the divider/ADC stage.

![[Pasted image 20260830104038.png]]

**Sensor data acquisition layer (I2C/SPI/UART sync across all sensors)**
Calibration & noise filtering (MOX sensor drift correction, PPG filtering)
Causal-attribution model (core ML contribution)
Agent decision & alerting logic
Edge deployment on Raspberry Pi 4 (power-aware, real-time)
**Datasets & Validation**
WESAD, PPG-DaLiA → motion/physiology disambiguation
UCI Air Quality, Gas Sensor Drift Dataset → gas-sensor calibration validation
Own pilot data collection → required to validate the actual causal exposure-response link (no public dataset covers this combination)
**Ayoos: Agent Layer**
Consumes causal-attribution output → decides action
Rule-based decision logic: log / alert / buzzer+LED trigger
Independent fall/collapse detection from accelerometer (bypasses causal layer for speed)
Future scope: lightweight RL for adaptive thresholds

