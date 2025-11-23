# Athena Keyboard - ZMK Firmware

Custom ZMK firmware configuration for the Athena keyboard - a 72-key custom mechanical keyboard with RGB underglow, rotary encoder, and Bluetooth connectivity.

## Features

- **Microcontroller**: Nordic nRF52840
- **Connectivity**: USB-C and Bluetooth Low Energy (BLE)
- **RGB Underglow**: 74 WS2812 LEDs
- **Rotary Encoder**: Alps EC11 encoder with volume control
- **Battery Monitoring**: Built-in battery voltage divider
- **ZMK Studio Support**: Real-time keymap editing via ZMK Studio
- **Multiple Layers**: 3 functional layers + 2 reserved layers for ZMK Studio

## Repository Structure

```
athena-zmk/
├── config/
│   ├── athena.keymap          # Main keymap configuration
│   └── west.yml               # West manifest for ZMK dependencies
├── boards/arm/athena/
│   ├── athena.dts             # Device tree source (hardware definition)
│   ├── athena.zmk.yml         # ZMK board metadata
│   ├── athena-pinctrl.dtsi    # Pin control definitions
│   ├── layout_athena.dtsi     # Physical layout definitions
│   └── key_physical_attrs.dtsi # Key physical attributes
├── build.yaml                 # Build configuration
└── .github/workflows/build.yml # Automated firmware builds
```

## Layer Overview

### Layer 0: DEFAULT

Standard QWERTY layout with media controls via rotary encoder.

```
| ESC |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  |  0  |  -  |  =  | BSPC|DEL|  PG_UP  |
| TAB  |  Q  |  W  |  E  |  R  |  T  |  Y  |  U  |  I  |  O  |  P  |  [  |  ]  |   \     |  PG_DN  |
| CAPS  |  A  |  S  |  D  |  F  |  G  |  H  |  J  |  K  |  L  |  ;  |  '  |    ENTER     |  DEL    |
|SHIFT|\|  Z  |  X  |  C  |  V  |  B  |  N  |  M  |  ,  |  .  |  /  |    SHIFT   |   UP   |  HOME   |
|  CTL  |  WIN  |  ALT  |     SPACE      |    SPACE    |  ALT  |  CTRL  |  LEFT  |  DOWN  |  RIGHT  |
```

**Encoder**: Volume Up/Down

### Layer 1: RGB

RGB lighting controls and function keys. Access by holding the rightmost key in row 4.

```
| BT0 |  F1 |  F2 |  F3 |  F4 |  F5 |  F6 |  F7 |  F8 |  F9 | F10 | F11 | F12 |  DEL | BSPC | PG_UP |
| BT1 | HUE+| SAT+| BRI+| SPD+|  FX+|  —  |  —  |  —  |  —  |  —  |  —  |  —  |  —          | PG_DN |
| BT2 | HUE-| SAT-| BRI-| SPD-|  FX-|  —  |  —  |  —  |  —  |  —  |  —  |  —                |  DEL |
| BT3 |  —  | BTCLR| BTCLR|  —  |  —  |  —  | MUTE|  —  |  —  |  —  |  —  |  —  | VOL↑        |  —   |
|STUDIO|RGBTOG| LALT| SPACE| PLAY/PAUSE | SPACE | RALT | RCTRL  | PREV | VOL↓ | NEXT |
```

**Key Features**:

- **BT0-BT3**: Select Bluetooth profile (supports 4 devices)
- **RGB Controls**: Hue, saturation, brightness, speed, and effect cycling
- **Media Controls**: Play/pause, previous/next track, mute
- **STUDIO**: Unlock ZMK Studio mode for live editing

**Encoder**: Volume Up/Down

### Layer 2: CONTROL

System controls and advanced Bluetooth management. Access via combo keys (positions 60+61).

```
| BT CLR | BT1 | BT2 | BT3 | BT4 | BT5 | OUT_BT | OUT_USB | STU |   –  |   –  |   –  |BOOTLDR| TRANS | TRANS |   –   |
|  BT1   | BT2 | BT3 | BT4 | BT5 |   –  | OUT_BT | OUT_USB | STU |   –  |   –  |   –  |   –  |    –    |   –     |
|  RGBTG | FX+ | HUE+| SAT+| BRI+| SPD+|   –    |   –     |   – |   –  |   –  |   –  |   –  |   –     |
|   –    |  –  |  –  |  –  |  –  |  –  |  –     |  –      |  –  |  –   |  –   |  –   |  –   |   –     |   –     |
|   –    |  –  |            – SPACE –            |   –     |   –  |   –  |   –  |   –  |   –  |
```

**Key Features**:

- **BT CLR**: Clear all Bluetooth bonds
- **OUT_BT/OUT_USB**: Switch between Bluetooth and USB output
- **BOOTLDR**: Enter bootloader mode for firmware flashing

**Encoder**: Volume Up/Down

## How to Edit Your Firmware

### Quick Start

1. **Edit Keymap**: Modify [config/athena.keymap](config/athena.keymap)
2. **Commit & Push**: Changes to the repository
3. **Download Firmware**: From GitHub Actions artifacts
4. **Flash**: Copy `.uf2` file to your keyboard in bootloader mode

### Using ZMK Studio (Recommended for Beginners)

ZMK Studio allows you to edit your keymap in real-time without rebuilding firmware:

1. Press `STUDIO UNLOCK` (bottom-left key on RGB layer)
2. Connect to [ZMK Studio web interface](https://zmk.studio/)
3. Edit keymaps visually
4. Changes are saved to keyboard EEPROM

> **Note**: ZMK Studio changes are temporary. To make them permanent, update [config/athena.keymap](config/athena.keymap) and rebuild.

### Editing the Keymap File

The keymap is defined in [config/athena.keymap](config/athena.keymap). Here's what you can customize:

#### 1. Change Key Bindings

Find the `bindings` array for your layer and replace key codes:

```c
&kp A      // Regular key press of 'A'
&kp ESC    // Escape key
&mo RGB    // Momentary layer toggle to RGB layer
&mt LSHFT A // Mod-tap: Shift when held, 'A' when tapped
&trans     // Transparent (passes through to lower layer)
```

#### 2. Adjust Mod-Tap Behavior

The `&mt` behavior is configured at the top of the file:

```c
&mt {
    flavor = "tap-preferred";
    tapping-term-ms = <200>;  // Adjust timing (milliseconds)
};
```

#### 3. Modify RGB Underglow

RGB controls use the `&rgb_ug` behavior:

```c
&rgb_ug RGB_TOG    // Toggle RGB on/off
&rgb_ug RGB_HUI    // Increase hue
&rgb_ug RGB_BRI    // Increase brightness
&rgb_ug RGB_EFF    // Next effect
```

#### 4. Configure Bluetooth

Bluetooth controls use the `&bt` behavior:

```c
&bt BT_SEL 0       // Select profile 0
&bt BT_CLR         // Clear current profile
&bt BT_CLR_ALL     // Clear all profiles
```

#### 5. Adjust Encoder Sensitivity

Modify the encoder settings in [boards/arm/athena/athena.dts](boards/arm/athena/athena.dts):

```c
sensors: sensors {
    compatible = "zmk,keymap-sensors";
    sensors = <&encoder>;
    triggers-per-rotation = <12>;  // Adjust this value (current: 12)
};
```

Higher values = more sensitive rotation detection.

### Building Firmware Locally

If you prefer to build locally instead of using GitHub Actions:

```bash
# Install West and dependencies
pip3 install west

# Initialize workspace
west init -l config/
west update

# Build firmware
west build -s zmk/app -b athena -- -DZMK_CONFIG="$(pwd)/config"

# Firmware will be in: build/zephyr/zmk.uf2
```

### Flashing Firmware

1. **Enter Bootloader Mode**:

   - Use the `BOOTLDR` key on the CONTROL layer

2. **Copy Firmware**:
   - Keyboard appears as USB drive
   - Copy `zmk.uf2` file to the drive
   - Keyboard will automatically reboot with new firmware

## Hardware Specifications

- **MCU**: Nordic nRF52840 (ARM Cortex-M4)
- **Matrix**: 6 rows × 14 columns
- **Diode Direction**: COL2ROW
- **RGB LEDs**: 74 × WS2812 (GRB color order)
- **Encoder**: Alps EC11, 12 steps per rotation
- **Battery Monitoring**: Voltage divider (510kΩ / 1510kΩ)
- **Connectivity**: USB-C, Bluetooth 5.0
- **Flash Partitioning**:
  - Softdevice: 4KB
  - Code: 844KB
  - Storage: 128KB
  - Bootloader: 48KB

## Combo Keys

- **CONTROL Layer Access**: Press keys at positions 60 + 61 simultaneously (timeout: 50ms)

## Useful ZMK Resources

### Official Documentation

- [ZMK Firmware Documentation](https://zmk.dev/docs)
- [ZMK Studio Guide](https://zmk.dev/docs/features/studio)
- [Keymap Configuration](https://zmk.dev/docs/features/keymaps)
- [Behaviors Reference](https://zmk.dev/docs/behaviors)
  - [Key Press](https://zmk.dev/docs/behaviors/key-press)
  - [Mod-Tap](https://zmk.dev/docs/behaviors/mod-tap)
  - [Layer Behaviors](https://zmk.dev/docs/behaviors/layers)
  - [Bluetooth](https://zmk.dev/docs/behaviors/bluetooth)
  - [RGB Underglow](https://zmk.dev/docs/behaviors/underglow)

### Hardware Features

- [RGB Underglow](https://zmk.dev/docs/features/underglow)
- [Encoders](https://zmk.dev/docs/features/encoders)
- [Battery Level](https://zmk.dev/docs/features/battery)
- [Bluetooth](https://zmk.dev/docs/features/bluetooth)

### Key Code References

- [Key Codes List](https://zmk.dev/docs/codes)
  - [Keyboard Keys](https://zmk.dev/docs/codes/keyboard)
  - [Media Keys](https://zmk.dev/docs/codes/media)
  - [Modifiers](https://zmk.dev/docs/codes/modifiers)

### Configuration

- [Kconfig Options](https://zmk.dev/docs/config)
- [Device Tree](https://zmk.dev/docs/config/system)

## Troubleshooting

### Keyboard Not Detected

- Check USB cable (must support data transfer)
- Try different USB port
- Enter bootloader mode manually

### Bluetooth Connection Issues

- Clear Bluetooth bond: Use `BT_CLR` on RGB/CONTROL layer
- Forget device on host computer
- Re-pair keyboard

### RGB LEDs Not Working

- Check `EXT_POWER` settings in [athena.dts](boards/arm/athena/athena.dts)
- Toggle RGB with `RGB_TOG` on RGB/CONTROL layer
- Verify power consumption doesn't exceed USB/battery limits

### Build Failures

- Check syntax in [athena.keymap](config/athena.keymap)
- Verify all key codes exist in [ZMK documentation](https://zmk.dev/docs/codes)
- Review GitHub Actions build logs for specific errors

## Contributing

This is a personal keyboard firmware configuration. If you have a similar Athena keyboard and want to use this as a base:

1. Fork this repository
2. Modify [config/athena.keymap](config/athena.keymap) to your preferences
3. GitHub Actions will automatically build your firmware

## License

This configuration is provided as-is for the Athena keyboard. ZMK firmware itself is licensed under the MIT License. See the [ZMK repository](https://github.com/zmkfirmware/zmk) for details.

## Additional Notes

- **West Manifest**: This project uses the main ZMK branch and includes the `zmk-poor-mans-led-indicator` module for enhanced LED functionality
- **Studio RPC**: Configured via USB UART for ZMK Studio communication
- **Reserved Layers**: Two extra layers are reserved for future ZMK Studio use

## Support

For ZMK-specific questions, refer to:

- [ZMK Discord Server](https://zmk.dev/community/discord/invite)
- [ZMK GitHub Discussions](https://github.com/zmkfirmware/zmk/discussions)
- [ZMK Documentation](https://zmk.dev/docs)

For Athena keyboard hardware questions, see the [hardware repository](https://github.com/Shiva1796/athena-zmk).
