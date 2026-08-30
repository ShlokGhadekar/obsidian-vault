![[Pasted image 20260830101103.png]]


## Hardware Setup :

### **Air Quality Sensors:**

1.*MiCS-6814*
**measures** - CO, NO₂, NH₃, C₃H₈, C₄H₁₀, H₂, ethanol (3 elements in one package)
**size** - 20 * 20mm
**Justification -** Replaces three separate MQ sensors with one package. Heater draws only ~40–60 mA vs ~150 mA per MQ sensor, which matters a lot on a battery-powered wearable. Covers the general urban-pollution gases traffic police are exposed to (vehicle exhaust NOx, benzene-family VOCs).

**2.*MQ-7***
**measures -** CO(dedicated)
**size -** 32 x 20mm can
**justification -** CO is the single most acute, fast-acting threat for both users — vehicle exhaust for traffic police, blasting/engine fumes for miners. A dedicated CO sensor gives better accuracy/response time than MiCS's general CO channel, so it's worth the extra current draw.

**3.*PMS5003***
**measures -** PM1.0, PM2.5, PM10 
**size -** 65x43x23mm
**justification -** Particulate matter is the dominant chronic-exposure pollutant for traffic police and mine dust for miners. Laser scattering is far more accurate than a resistive dust sensor. Can be duty-cycled (on for 30s every few minutes) to cut its ~100 mA active draw.

**4.*BME280***
**measures -** Temperature, humidity, pressure
**size -** 10mm
**justification -** Tiny, negligible power. Temperature/humidity feed heat-stress calculations; pressure is a useful proxy for ventilation state in enclosed mine tunnels.

**5.*Electrochemical O2 sensor***
**measures -** oxygen %
**size -** 20mm disc
**justification -** Mining-specific but important: oxygen depletion in a confined shaft kills faster than most toxic gases. No heater — passive electrochemical cell, very low power.

### **Health Sensors**

*1.MAX30102*
**measures -** SpO₂, heart rate
**size -** 11x11mm
**justification -** Smallest reliable pulse-oximeter module, worn as a fingertip or earlobe clip on a short lead back to the belt unit. SpO₂ is the direct physiological consequence of CO/toxic-gas exposure — it's the key "effect" variable your causal model needs.

*2.MLX90614*
**measures -** Body temperature (non-contact IR)
**size -** ~17 mm TO-39 can
**justification -** No electrode/skin contact needed — just faces the wrist skin from inside the strap. More hygienic and easier to mount reliably than a contact thermistor.

*3.MPU6050*
**measures -** Accelerometer + gyroscope
**size -** 21x16mm
**justification -** Two jobs: fall/collapse detection (critical if someone passes out from gas exposure), and separating exertion-driven heart-rate rise from toxin-driven rise — without a motion signal your causal model can't tell "he's running" from "he's poisoned.

### **Power System**