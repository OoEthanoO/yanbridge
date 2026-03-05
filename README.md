# YanBridge (yb)

YanBridge is a robust, lightweight command-line tool designed to streamline the code deployment process for FTC REV Control Hubs on macOS and Linux. It manages the full lifecycle of pushing code: automatically connecting to the robot's Wi-Fi network, connecting ADB, triggering your Android Studio Gradle build (`sloth` or `teamcode`), and safely restoring your original network connection when finished.

## Features

- **Automated Deployments:** Run a single command to deploy code to your Robot.
- **Smart Wi-Fi Management:** Safely switches off your default Wi-Fi, connects to the Control Hub's network, and restores your original Wi-Fi connection as soon as the push completes.
- **Cross-Platform:** Supports macOS (via `networksetup`) and Linux (via `nmcli`).
- **macOS 14+ Privacy Aware:** If macOS Location Privacy prevents `yb` from reading your original Wi-Fi SSID, `yb` will gracefully cycle the Wi-Fi power to trigger the native macOS auto-reconnect feature, ensuring you return to your home/school network without any manual intervention.
- **Support for Sloth Load & TeamCode:** Deploy your Gradle builds using either the standard debug install or faster dynamic code loading techniques.

## Installation

Ensure you have Python 3 installed. Make the script executable and (optionally) place it in your `$PATH` or run it from your project directory:

```bash
chmod +x yb
```

## Usage

### 1. Initial Configuration
Before pushing code, configure your REV Control Hub's Wi-Fi credentials. This saves the credentials to `~/.ybconfig.json`.

```bash
./yb config --ssid "Your-Robot-SSID" --password "Your-Robot-Password"
```

### 2. Pushing Code
Ensure you are running the command from the root directory of your FTC Android Studio project (where `gradlew` is located).

```bash
./yb push [type]
```
- **`type`** (optional): Either `sloth` (default) or `teamcode`.
  - `sloth`: Uses `./gradlew deploySloth` for faster iterations.
  - `teamcode`: Uses `./gradlew installDebug` for standard deployments.

**Example:**
```bash
# Push using the default sloth deployment
./yb push

# Push using the standard teamcode deployment
./yb push teamcode
```

## Troubleshooting

- **Silent Wi-Fi Failures:** If `yb` immediately exits citing it "Could not find network" or "Failed to join", ensure your robot is powered on and within range, and verify your configured credentials are correct.
- **Instant Hotspots:** `yb` cannot natively wake up Apple Instant Hotspots (Personal Hotspots). If you are testing `yb` using your iPhone as a dummy robot, ensure the iPhone is unlocked and currently open to the **Settings > Personal Hotspot** page so it broadcasts a standard 802.11 signal.
- **macOS Wi-Fi Privacy Logging:** You may notice `yb` logging: `"Wi-Fi is ON, but SSID cannot be read..."`. This is normal on macOS 14 (Sonoma) and later. `yb` handles this by safely power-cycling your Wi-Fi interface after the deployment, prompting macOS to automatically rejoin your default network.
