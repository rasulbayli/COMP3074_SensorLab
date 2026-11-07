# SensorLab: Submission Notes

COMP3074 - Mobile App Development I

## Sensor availability on my test device

Tested on the Android Emulator (Pixel 10 virtual device, API 37), not a physical phone. The emulator provides virtual versions of these sensors, so this does not show what a physical phone includes. Sensor values were set manually through the emulator.

- Device: Android Emulator, Pixel 10 virtual device
- Ambient temperature sensor: available (virtual)
- Gyroscope: available (virtual)
- Light sensor (extension): available (virtual)

Screenshot: see `screenshot_emulator.png`.

## Independent practice (Part 18)

Added a third sensor: the ambient light sensor (`Sensor.TYPE_LIGHT`), shown in lux in its own card. It uses the same null check and listener lifecycle as the other two sensors.

## Change from the handout code

Temperature readings below absolute zero (-273.15 °C) are ignored. The Android Emulator sends a placeholder of about -1e30 when the temperature sensor activates, which the handout code would display as a very long negative number. With this check, the screen stays on "Waiting for sensor reading..." until a real value arrives.

## Reflection questions

**1. Why must `getDefaultSensor()` be treated as potentially returning null?**

Android devices are not required to include every sensor, and `getDefaultSensor()` returns null when the requested one is missing. Many phones have no ambient temperature sensor, for example. Using the result without checking it would crash the app on those devices.

**2. Why should sensor listeners be unregistered when the activity is paused?**

Once the activity is in the background, nobody is looking at the values. A registered listener would keep the sensor running and keep delivering callbacks, which wastes battery and CPU. Registering in `onResume()` and unregistering in `onPause()` means the app only listens while its data is needed.

**3. What is the difference between gyroscope angular velocity and device orientation?**

Angular velocity is how fast the device is rotating right now. Orientation is the angle the device is currently held at. A phone lying still at a tilt has a fixed orientation, but its angular velocity is near zero. Getting orientation from a gyroscope would mean integrating angular velocity over time, which drifts, so it is usually combined with other sensors.

**4. What units are used for the gyroscope values in this lab?**

Radians per second (rad/s), for rotation around the X, Y and Z axes.

**5. Why is battery temperature not a valid replacement for `TYPE_AMBIENT_TEMPERATURE`?**

Battery temperature measures the battery, not the surrounding air. It is affected by charging, CPU load and heat from the phone itself, and it can be well above room temperature. Displaying it as ambient temperature would be misleading.

**6. How does `mutableStateOf` allow a `SensorEvent` callback to update the Compose UI?**

`mutableStateOf` creates observable state. When a composable reads that state during composition, Compose records the dependency. When `onSensorChanged()` writes a new value, Compose schedules a recomposition of the composables that read it, so the screen updates without the callback touching the UI directly.
