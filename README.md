# EMS-ESP Configuration Repository

This repository tracks the configuration files for an EMS-ESP device monitoring a Buderus GB192 boiler system.

## System Overview

### Hardware
- **EMS-ESP Device**: Version 3.7.2 (ESP32 E32V2)
- **Boiler**: Buderus CU DNA 1.0/Logamax Plus, GB192, Condens GC9000, Greenstar ErP
  - Device ID: 0x08
  - Product ID: 208
  - Firmware: 01.04
- **Thermostat**: RC300/RC3*0 series
  - Device ID: 0x10
  - Product ID: 158
  - Firmware: 11.09
- **Additional Modules**:
  - Controller Module BC30/9000i (DeviceID: 0x09)
  - Extension Module T1RF (DeviceID: 0x16)

### Network
- **Device IP**: YOUR_DEVICE_IP
- **Hostname**: ems-esp
- **MQTT Broker**: YOUR_MQTT_BROKER_IP:1883 (Home Assistant integration enabled)

## Configuration Files

### Template Files (Tracked in Git)
- `emsesp_settings.json` - Main system settings with CHANGE_ME placeholders
- `emsesp_allvalues.json` - Current system status and all readable values
- `emsesp_customizations.json` - Custom temperature and analog sensor configurations
- `emsesp_entities.json` - Custom entity definitions
- `emsesp_schedule.json` - Heating/DHW schedules

### Local Files (Not Tracked in Git)
- `emsesp_settings.local.json` - **Your actual settings with real credentials** (gitignored)
- Use this file for your real configuration - it contains actual passwords and IP addresses
- Never commit this file to version control

### How It Works
1. **Template files** (*.json) contain placeholders and are tracked in git
2. **Local files** (*.local.json) contain your real credentials and are excluded from git
3. When you export settings from your EMS-ESP device, save them as *.local.json
4. You can safely share or backup your repository without exposing credentials

## Current System Status

### Heating Circuit (HC1)
- **Mode**: Auto (weather compensated)
- **Selected Temperature**: 18°C
- **Comfort Temperature**: 24°C
- **Eco Temperature**: 15°C
- **Design Temperature**: 73°C at -10°C outdoor
- **Heating Type**: Radiator
- **Room Influence**: 0% (pure weather compensation)

### Hot Water (DHW)
- **Mode**: Own program (eco)
- **Setpoint**: 60°C
- **Low Setpoint**: 45°C
- **Disinfection**: Enabled (Tuesday 02:00, 65°C)
- **Charge Type**: 3-way valve
- **Buffer Type**: Buffer tank

### System Health
- **System Pressure**: 1.5 bar ✓
- **Outdoor Temperature**: 0.6°C
- **Current Flow Temperature**: 55.1°C
- **Return Temperature**: 40.4°C
- **Burner Modulation**: 16% (efficient)
- **Total Runtime**: 5,114,011 minutes (~9.7 years)
- **Heating Starts**: 123,066
- **DHW Starts**: 38,749

## Configuration Recommendations

### Security (Priority: High)
⚠️ **Action Required**:
1. Replace CHANGE_ME placeholders with secure passwords
2. Update MQTT broker IP and credentials
3. Change JWT secret to a strong random value
4. Rotate credentials periodically

### Heating Optimization

#### Room Temperature Control
- **Current**: Room influence = 0% (weather compensation only)
- **Recommendation**: Set room influence to 2-4% for better comfort
- **Benefit**: Compensates for varying room conditions (sun exposure, wind, etc.)

#### Comfort Features
- **Switch-on Optimization**: Currently OFF
  - **Recommendation**: Enable to pre-heat rooms to reach target temperature on schedule
- **Night Setback**: No schedule configured
  - **Recommendation**: Add heating schedule for energy savings
  - Example: 24°C (07:00-22:00), 18°C (22:00-07:00)

#### Current Setpoint
- **Current**: 18°C (quite low)
- **Review**: Consider if this provides adequate comfort at current outdoor temperature (0.6°C)

### System Configuration

#### NTP Time Synchronization
- **Status**: Currently disabled
- **Recommendation**: Enable for accurate scheduling
  - Server: time.google.com
  - Timezone: Europe/Amsterdam (already configured)

#### DHW Settings ✓
Current configuration is good:
- 60°C setpoint prevents legionella
- Weekly disinfection cycle at 65°C (Tuesday 02:00)
- Eco mode reduces standby losses

## MQTT Integration

### Home Assistant
- **Status**: Enabled
- **Discovery Prefix**: homeassistant
- **Base Topic**: ems-esp
- **Publish Intervals**:
  - Boiler/Thermostat: 10 seconds
  - Water: 10 seconds
  - Other: 60 seconds
  - Heartbeat: 60 seconds

### Topics Structure
```
ems-esp/boiler/...
ems-esp/thermostat/...
ems-esp/dhw/...
ems-esp/status/...
```

## Custom Sensors

### Temperature Sensors
- `gateway_temperature`: 28.4°C (Dallas sensor at GPIO 14)

### Analog Sensors
- `core_voltage`: 3.35V (GPIO 39)
- `supply_voltage`: 7.97V (GPIO 36)

## Accessing the Device

### Web Interface
```
http://YOUR_DEVICE_IP
```
Default credentials: admin/CHANGE_ME

### Telnet Console
```bash
telnet YOUR_DEVICE_IP 23
```
Access to EMS-ESP command line interface for diagnostics and configuration.

## Backup and Restore

### Export Configuration
1. Navigate to Settings → System
2. Click "Export Settings"
3. Save all JSON files to this repository

### Restore Configuration
1. Navigate to Settings → System
2. Upload each JSON file individually
3. Restart the device

## Maintenance

### Regular Checks
- Monitor system pressure (should be 1.2-1.5 bar when cold)
- Review error logs monthly
- Verify MQTT connectivity
- Check for firmware updates at https://emsesp.org

### Boiler Service History
- **Last Service Code**: 6L(229) on 02.03.2023 15:04
- **Current Status**: -H (200)
- **Maintenance Message**: H00
- **Next Service**: Due at 6000 hours

## Useful Commands

### EMS-ESP Telnet Commands
```bash
show                    # Show all system values
show boiler            # Show boiler values only
show thermostat        # Show thermostat values only
set boiler seltemp 60  # Set boiler selected temperature
set hc1 seltemp 21     # Set heating circuit 1 target temperature
```

### Home Assistant Integration
Monitor via MQTT topics or through the EMS-ESP Home Assistant integration.

## Links

- **EMS-ESP Documentation**: https://emsesp.org
- **GitHub**: https://github.com/emsesp/EMS-ESP32
- **Support Forum**: https://github.com/emsesp/EMS-ESP32/discussions

## Notes

- This is a **private repository** using a template/local file pattern for security
- **Sensitive credentials** are stored in `*.local.json` files (gitignored)
- **Template files** with placeholders are tracked in git for version control
- Configuration files are backed up here for disaster recovery
- Always test changes in a controlled manner before deploying to production
- Consult a qualified HVAC technician for critical system changes

## Version History

See commit history for configuration changes over time.
