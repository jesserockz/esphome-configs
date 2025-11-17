# Jesse's ESPHome Configurations

Public ESPHome configurations for my smart home devices, organized in a clean and maintainable structure.

## Directory Structure

```
.
├── devices/          # Individual device configurations
│   ├── lights/       # Light devices (RGB, CT, LED strips, etc.)
│   ├── switches/     # Wall switches and relay switches
│   ├── plugs/        # Smart plugs and power strips
│   ├── climate/      # Climate control (heaters, fans, AC units)
│   ├── displays/     # Display devices (M5Stack Dial, desk displays)
│   ├── garage/       # Garage-related devices (door openers, etc.)
│   ├── sensors/      # Sensor devices (energy monitors, etc.)
│   ├── network/      # Network devices (Bluetooth proxies, RF bridges)
│   └── other/        # Other devices (alarm systems, pet fountains, etc.)
│
├── common/           # Shared configuration components
│   ├── platforms/    # Platform-specific base configurations
│   │   ├── common-base.yaml       # Core ESPHome configuration
│   │   ├── common-esp8266.yaml    # ESP8266 specific config
│   │   ├── common-esp32.yaml      # ESP32 specific config
│   │   ├── common-esp32s2.yaml    # ESP32-S2 specific config
│   │   ├── common-esp32s3.yaml    # ESP32-S3 specific config
│   │   ├── common-esp32c3.yaml    # ESP32-C3 specific config
│   │   ├── common-esp32c6.yaml    # ESP32-C6 specific config
│   │   └── common-32-solo.yaml    # ESP32 Solo specific config
│   │
│   ├── wifi.yaml             # WiFi configuration
│   ├── bluetooth-proxy.yaml  # Bluetooth proxy setup
│   ├── sensors/              # Common sensor components (uptime, etc.)
│   ├── binary_sensors/       # Common binary sensors (status, etc.)
│   └── buttons/              # Common buttons (restart, safe mode, factory reset)
│
├── templates/        # Device-specific templates by brand/type
│   ├── athom/        # Athom devices (smart plugs, lights, switches)
│   ├── kogan/        # Kogan devices (lights, heaters, fans)
│   ├── sonoff/       # Sonoff devices (basic switches, RF bridges)
│   ├── smartape/     # Smart Ape devices (kettles, power strips)
│   ├── zemismart/    # Zemismart switches
│   ├── m5stack/      # M5Stack devices
│   ├── alarm/        # Alarm system templates
│   ├── garage-panel/ # Garage panel templates
│   └── other/        # Other device templates
│
└── fonts/            # Custom fonts for displays
```

## How It Works

### Device Configurations

Device configuration files (in `devices/`) are **thin wrapper files** that:
1. Define device-specific settings (name, IP address, etc.)
2. Include the appropriate device template
3. May override or extend template settings

**Example** (`devices/lights/dining-light.yaml`):
```yaml
---
substitutions:
  name: dining-light
  friendly_name: Dining Light
  ip: '10.6.6.130'
  light_name: Dining

packages:
  light: !include ../../templates/athom/athom-rgbct-light.yaml
```

### Device Templates

Device templates (in `templates/`) provide **complete hardware configurations** for specific device models:
- Pin assignments
- Component definitions (lights, switches, sensors, etc.)
- Device-specific features

Templates automatically include:
- The appropriate platform configuration (ESP8266, ESP32, etc.)
- Common components (WiFi, sensors, buttons)

**Example** (`templates/athom/athom-rgbct-light.yaml`):
```yaml
---
packages:
  common: !include ../../common/platforms/common-esp8266.yaml
  wifi: !include ../../common/wifi.yaml

# Device-specific hardware configuration...
```

### Platform Configurations

Platform files (in `common/platforms/`) set up the ESP chip and include common components:
- OTA updates
- API encryption
- Logger settings
- Time/NTP configuration
- Common sensors, buttons, and status indicators

### Common Components

Reusable configuration snippets (in `common/`) that can be included anywhere:
- WiFi credentials and configuration
- Bluetooth proxy functionality
- Standard sensors (uptime)
- Standard buttons (restart, safe mode)

## Creating a New Device

### 1. Using an Existing Template

If you have a device that matches an existing template:

```yaml
---
substitutions:
  name: my-new-light
  friendly_name: My New Light
  ip: '10.6.6.XXX'

packages:
  light: !include ../../templates/athom/athom-rgbct-light.yaml
```

### 2. Creating a Custom Device

For unique devices, you can include the platform directly:

```yaml
---
substitutions:
  name: my-custom-device
  friendly_name: My Custom Device
  ip: '10.6.6.XXX'

packages:
  common: !include ../../common/platforms/common-esp32.yaml
  wifi: !include ../../common/wifi.yaml

# Your custom configuration here...
```

### 3. Creating a New Template

If you have multiple devices of the same model, create a template:

1. Create a new file in the appropriate `templates/` subdirectory
2. Include the platform and common components
3. Define the hardware-specific configuration
4. Reference it from your device files

## Path Reference Guide

Paths are **relative** to the file location:

### From Device Files (`devices/*/`)
- Platform configs: `../../common/platforms/common-esp32.yaml`
- Common components: `../../common/wifi.yaml`
- Templates: `../../templates/brand/template.yaml`

### From Template Files (`templates/*/`)
- Platform configs: `../../common/platforms/common-esp32.yaml`
- Common components: `../../common/wifi.yaml`

### From Platform Files (`common/platforms/`)
- Other platforms: `common-base.yaml` (same directory)
- Common components: `../wifi.yaml`

## Tips

- **Use substitutions** to customize templates without modifying them
- **Keep device files minimal** - put reusable config in templates
- **Group related devices** in the appropriate `devices/` subdirectory
- **Use descriptive names** following the pattern: `location-device.yaml`

## CI/CD

The repository includes GitHub Actions workflows for:
- Validating all YAML files with yamllint
- Checking ESPHome configuration syntax

Configuration files:
- `.yamllint` - YAML linting rules
- `secrets.ci.yaml` - Test secrets for CI/CD
