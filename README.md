# Make Mom Walk 🚶‍♀️❤️

A native Android motivation and digital wellbeing application built with **Kotlin** and **Jetpack Compose (Material 3)**. It is specially designed to motivate Mom to complete her daily walking goal (e.g., 2.0 km) before unrestricted phone usage is permitted.

---

## 📱 How It Works: The Golden Walking Model

### 1. The Core Principle: "Walk Today to Earn Tomorrow"
* **Day 1 (Fresh Install):** Starts **UNLOCKED**. Mom has until midnight to complete her initial walk.
* **Target Completed (e.g. 2.0 km):**
  * When Mom walks 2.0 km before the daily reset deadline (default 12:00 Midnight), her device remains **UNLOCKED** for the entire following day.
* **Target Missed:**
  * If Mom does not complete her 2.0 km before midnight, the phone becomes **LOCKED** at midnight.

### 2. The Solution to the "Fundamental Problem" (No 4 km Penalty)
* When locked, Mom only needs to walk her standard **2.0 km target** to unlock her device.
* The moment she completes 2.0 km today:
  1. The device **immediately unlocks** for the rest of today!
  2. That same 2.0 km is **credited to today's daily cycle**.
  3. Because today's target is now satisfied, the phone **remains unlocked tomorrow as well**!
  4. She **never has to walk 4.0 km** in a single day.

---

## 🎨 Home Screen Circular Progress Widget 📲

Mom can add the beautiful **Make Mom Walk Widget** directly onto her phone\'s home screen launcher:
* **Circular Progress Ring:** Live anti-aliased gradient arc showing walking progress in real time.
* **Remaining Distance:** Displays large, bold remaining distance (e.g. `1.2 km left`) so Mom always knows exactly how much she has left to walk.
* **Lock Status Badge:** Shows `🔒 Locked` (in warm amber) or `✨ Unlocked` (in mint green).
* **Walk & Step Stats:** Live summary of distance walked vs target and steps taken (e.g. `0.80 / 2.00 km • 1,192 steps`).
* **One-Tap Access:** Tapping anywhere on the widget instantly opens the app dashboard.
* **How to Add:** Long-press on any empty spot on the phone\'s Home Screen -> Tap **Widgets** -> Find **Make Mom Walk** -> Drag and drop the 2x2 widget.

---

Screen-time and app-blocking applications (like Regain, Freedom, and AppBlock) require specific system permissions to function reliably. `MakeMomWalk` features an interactive **Permission Onboarding Flow** (`PermissionSetupScreen`) covering all 6 system permissions:

1. **Physical Activity / Step Counter (`android.permission.ACTIVITY_RECOGNITION`)**:
   * Reads Android's hardware `Sensor.TYPE_STEP_COUNTER` and `Sensor.TYPE_STEP_DETECTOR`.
   * Ultra low battery consumption (runs on hardware sensor DSP, zero battery drain).
2. **Display Over Other Apps (`android.permission.SYSTEM_ALERT_WINDOW`)**:
   * Enables the app to draw the high-priority lock screen overlay (`LockOverlayActivity`) over restricted apps.
3. **Accessibility Service (`android.permission.BIND_ACCESSIBILITY_SERVICE`)**:
   * Provides **0-latency instant detection** (`TYPE_WINDOW_STATE_CHANGED`) when restricted apps are launched.
   * **Anti-Tampering Protection:** Intercepts attempts to open Android Settings or package managers, preventing unauthorized force-stops or uninstalls without the Admin PIN.
4. **Usage Access (`android.permission.PACKAGE_USAGE_STATS`)**:
   * Secondary background monitoring layer to inspect active applications and foreground status.
5. **Battery Optimization Exemption (`REQUEST_IGNORE_BATTERY_OPTIMIZATIONS`)**:
   * Exempts the app from aggressive manufacturer battery savers (Samsung, Xiaomi, Vivo, Oppo) so tracking remains active when the phone is in Mom's pocket.
6. **Notification Permission (`android.permission.POST_NOTIFICATIONS`)**:
   * Keeps an ongoing notification in the status shade showing Mom's live walking progress (`1.4 / 2.0 km`).

---

## 📐 Biometrics & Distance Calculation for Mom (5'4")

* **Mom's Height:** `5'4"` = 64 inches = `162.56 cm`
* **Female Walking Stride Factor:** `0.413` (established biomechanical ratio for walking stride length)
* **Stride Length:** `162.56 cm × 0.413 = 67.137 cm = 0.6714 meters / step`
* **Step Conversions:**
  * **1.0 km:** `1000 / 0.6714 ≈ 1,489 steps`
  * **2.0 km:** `2000 / 0.6714 ≈ 2,979 steps` (~2,980 steps)
* **Formulas (`DistanceCalculator.kt`):**
  $$\text{Distance (km)} = \frac{\text{Steps} \times \text{Stride Length (m)}}{1000}$$
  $$\text{Required Steps} = \frac{\text{Target (km)} \times 1000}{\text{Stride Length (m)}}$$

---

## 🔑 Universal Admin Passcode & Portal

* **Universal Passcode:** Secure master unlock PIN (all inputs masked with `PasswordVisualTransformation()`, never displayed in hints, titles, or error messages).
* **Admin Portal Capabilities:**
  1. **Immediate Unlock or Settings from Lock Screen:** When unlocking from the lock screen, Admin can choose "Unlock Today" (removes lock immediately) or "Settings" (starts an uninterrupted Admin Session).
  2. **Today's Relief Pass:** One-tap toggle to immediately give Mom a break today without changing targets.
  3. **Target Distance Slider:** Adjustable from `0.5 km` to `8.0 km` (default: `2.0 km`).
  4. **Mom's Height Calibration:** Adjustable height slider with live stride length re-calculation.
  5. **Daily Reset Time:** Configurable time picker (default: `12:00 Midnight 00:00`).
  6. **Whitelisted Apps Selector:** List of all installed user applications. Admin can toggle which apps Mom can use while locked (e.g. WhatsApp, Camera, Calculator). *Phone dialer is always permanently whitelisted for safety.*
  7. **Settings Anti-Tamper:** Toggle whether Android Settings requires Admin PIN to open.
  8. **Testing & Simulation Controls:** Tucked safely inside the Admin Portal (removed from Mom's home screen) so Mom cannot self-complete walks. Quick `+500 steps`, `Complete Walk`, `Test Lock Now`, and `Reset Steps` buttons for instant testing.

---

## 📦 Project Structure

```
make mom walk/
├── MakeMomWalk.apk                             # Ready-to-install debug APK
├── app/
│   ├── src/main/
│   │   ├── AndroidManifest.xml                 # 6 permissions, accessibility, services, activities
│   │   ├── java/com/makemomwalk/app/
│   │   │   ├── MakeMomWalkApp.kt               # Application entry point, daily reset initialization
│   │   │   ├── MainActivity.kt                 # Navigation host (Home, Admin, Permissions)
│   │   │   ├── data/
│   │   │   │   └── AppPreferences.kt           # Persistent state, universal PIN 09102007, cycle logic
│   │   │   ├── service/
│   │   │   │   ├── StepTrackerService.kt       # Hardware step counter listener & foreground notification
│   │   │   │   ├── MomLockAccessibilityService.kt # 0-delay app interceptor & anti-tampering
│   │   │   │   ├── DailyResetReceiver.kt       # Exact alarm receiver for midnight reset evaluation
│   │   │   │   └── BootReceiver.kt             # Re-arms alarms and tracking on device reboot
│   │   │   ├── ui/
│   │   │   │   ├── screens/
│   │   │   │   │   ├── MomHomeScreen.kt        # Big progress circle, steps, friendly UI for Mom
│   │   │   │   │   ├── LockOverlayActivity.kt   # High-priority lock screen with live steps & SOS
│   │   │   │   │   ├── AdminSettingsScreen.kt  # PIN protected configuration portal
│   │   │   │   │   └── PermissionSetupScreen.kt# Regain-style guided permission wizard
│   │   │   │   └── theme/
│   │   │   │       ├── Color.kt
│   │   │   │       └── Theme.kt
│   │   │   └── utils/
│   │   │       ├── DistanceCalculator.kt       # 5'4" biomechanical stride calculation
│   │   │       └── PermissionManager.kt        # System settings intents & status checks
│   │   └── res/
│   │       ├── values/strings.xml, themes.xml
│   │       └── xml/accessibility_service_config.xml
│   └── build.gradle.kts
├── gradle/
│   ├── libs.versions.toml
│   └── wrapper/
├── build.gradle.kts
└── settings.gradle.kts
```

---

## 🚀 How to Install & Run

### Option A: Install Pre-Built APK Directly
The compiled APK is ready in the root folder:
`c:\Users\marvi\Downloads\make mom walk\MakeMomWalk.apk`

1. Transfer `MakeMomWalk.apk` to Mom's phone via USB cable, WhatsApp, Google Drive, or Bluetooth.
2. Tap the APK to install (enable "Install from Unknown Sources" if prompted).
3. Open **Make Mom Walk**. The app will guide you through the **Permission Setup Wizard** to enable:
   * Step Sensor
   * Display Over Other Apps
   * Accessibility Service (`Make Mom Walk` -> ON)
   * Battery Optimization Exemption

### Option B: Open in Android Studio
1. Launch **Android Studio**.
2. Select **Open** and select:
   `c:\Users\marvi\Downloads\make mom walk`
3. Gradle will sync automatically using the included Gradle wrapper (`8.10.2` with Java 21).
4. Connect an Android device or start an emulator and click **Run (Shift + F10)**.
