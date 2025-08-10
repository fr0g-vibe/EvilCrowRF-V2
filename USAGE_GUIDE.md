# Evil Crow RF V2 - Comprehensive Usage Guide

## Table of Contents
1. [Device Overview](#device-overview)
2. [Hardware Capabilities](#hardware-capabilities)
3. [Modulation Types and Use Cases](#modulation-types-and-use-cases)
4. [Attack Techniques](#attack-techniques)
5. [Integration with Flipper Zero](#integration-with-flipper-zero)
6. [Advanced Attack Scenarios](#advanced-attack-scenarios)
7. [Protocol-Specific Attacks](#protocol-specific-attacks)
8. [Defensive Measures and Detection](#defensive-measures-and-detection)

## Device Overview

The Evil Crow RF V2 is a sophisticated radiofrequency hacking device designed for penetration testing and security research. It features two CC1101 RF modules and one NRF24L01 module, enabling simultaneous transmission and reception on different frequencies.

## Hardware Capabilities

### Frequency Bands
- **300-348 MHz**: Common for garage door openers, remote controls
- **387-464 MHz**: Used for car key fobs, industrial remotes, medical devices
- **779-928 MHz**: Smart home devices, IoT sensors, some car keys
- **2.4 GHz**: Via NRF24L01 module for mousejacking attacks

### Modules
1. **CC1101 Module 1**: General purpose RX/TX (ASK/OOK preferred)
2. **CC1101 Module 2**: Optimized for 2-FSK modulation and general RX/TX
3. **NRF24L01 Module**: 2.4 GHz operations, mousejacking attacks

## Modulation Types and Use Cases

### ASK/OOK (Amplitude Shift Keying/On-Off Keying)
**Most common for:**
- Garage door openers
- Car remotes (older models)
- Wireless doorbells
- Simple remote controls
- Weather sensors

**Typical Settings:**
```
Modulation: ASK/OOK
Frequency: 433.92 MHz (most common)
RxBW: 58-325 kHz (start with 58)
Deviation: 0
Data Rate: 2-10 kbps (5 is good default)
```

### 2-FSK (Frequency Shift Keying)
**Used for:**
- Modern car key fobs
- TPMS (Tire Pressure Monitoring Systems)
- Advanced home automation
- Some medical devices

**Important:** Only use Module 2 for 2-FSK RX!

**Typical Settings:**
```
Module: 2 (mandatory for RX)
Modulation: 2-FSK
Frequency: Device-specific
RxBW: 100-200 kHz
Deviation: 20-50 kHz
Data Rate: 2-20 kbps
```

## Attack Techniques

### 1. Basic Replay Attack
The simplest and most effective attack against fixed-code systems.

**Target Systems:**
- Older garage doors (pre-1996)
- Basic remote controls
- Some gate openers
- Simple car alarms

**Procedure:**
1. Set RX parameters matching target device
2. Capture signal when legitimate remote is used
3. Save RAW data from RX log
4. Configure TX with same parameters
5. Replay captured signal

**Success Indicators:**
- Target device responds to replayed signal
- Works repeatedly with same code

### 2. Rolling Code Bypass (RollJam Attack)

**Target Systems:**
- Modern garage doors with rolling codes
- Car key fobs with KeeLoq or similar
- Advanced gate systems

**Attack Method:**
1. **Jam and Capture First Code:**
   - Use Module 1 to jam on target frequency
   - Use Module 2 to capture legitimate signal
   - User presses button again due to jam
   
2. **Capture Second Code:**
   - Stop jamming briefly
   - Capture second transmission
   - Resume jamming

3. **Replay First Code:**
   - Stop jamming
   - Transmit first captured code
   - Save second code for later use

**Configuration Example:**
```
Module 1 (Jamming):
- Modulation: ASK/OOK
- Frequency: 433.92 MHz
- Continuous transmission of noise

Module 2 (Capture):
- Modulation: ASK/OOK
- Frequency: 433.92 MHz
- RxBW: 58 kHz
- Data Rate: 5 kbps
```

### 3. De Bruijn Sequence Attack

**Target:** Fixed-code systems with short codes (8-12 bits)

**Concept:** Transmit all possible codes in an optimized sequence

**Implementation:**
1. Determine code length (usually 8, 10, or 12 bits)
2. Generate De Bruijn sequence
3. Transmit at high speed

**Example for 8-bit codes:**
```
Total combinations: 256
Time per code: 10ms
Total time: 2.56 seconds
```

### 4. Frequency Hopping Analysis

**Target:** Advanced systems using FHSS (Frequency Hopping Spread Spectrum)

**Method:**
1. Use both modules to monitor different frequencies
2. Identify hopping pattern
3. Predict next frequency
4. Attempt synchronized transmission

### 5. TPMS (Tire Pressure Monitoring System) Attacks

**Attack Vectors:**

**a) TPMS Spoofing:**
```
Module: 2
Modulation: 2-FSK
Frequency: 315 MHz (US) or 433.92 MHz (EU)
Deviation: 38 kHz
Data Rate: 19.2 kbps
```

**Attack Goals:**
- Trigger false low/high pressure warnings
- Track vehicles by TPMS ID
- Clone TPMS sensors

**b) TPMS Replay:**
- Capture legitimate TPMS packet
- Modify pressure values
- Replay to vehicle

### 6. Smart Home Device Exploitation

**Common Targets:**
- Wireless doorbells
- Motion sensors
- Window/door sensors
- Smart plugs
- Weather stations

**Attack Methods:**

**a) Sensor Spoofing:**
```
Target: Window sensor
Frequency: 433.92 MHz
Modulation: ASK/OOK
Attack: Replay "closed" signal while opening
```

**b) Command Injection:**
```
Target: Smart plug
Frequency: 433.92 MHz
Modulation: ASK/OOK
Attack: Capture and modify ON/OFF commands
```

### 7. Medical Device Attacks (Research Only!)

**WARNING:** Never perform on active medical devices!

**Vulnerable Devices:**
- Some insulin pumps
- Pacemaker programmers
- Patient monitors

**Research Parameters:**
```
Frequency: 403-405 MHz (MICS band)
Modulation: 2-FSK
Data Rate: 4.8-16 kbps
```

### 8. Industrial Control Systems

**Target Systems:**
- SCADA sensors
- Industrial remotes
- Crane controls
- Emergency stops

**Typical Configuration:**
```
Frequency: 433.92 MHz or 868 MHz
Modulation: ASK/OOK or 2-FSK
Data Rate: 2.4-9.6 kbps
```

### 9. Mousejacking (2.4 GHz Attacks)

**Vulnerable Devices:**
- Logitech Unifying receivers
- Microsoft wireless keyboards
- Dell wireless mice
- Many non-Bluetooth wireless peripherals

**Attack Capabilities:**
- Keystroke injection
- Mouse movement control
- Forced pairing
- Eavesdropping

**Range:** Up to 100 meters

### 10. Tesla Charge Port Opening

**Specific Attack:** Opens Tesla charging ports

**Method:** Transmits specific signal pattern recognized by Tesla vehicles

## Integration with Flipper Zero

### Combined Operations

**1. Reconnaissance with Flipper:**
- Use Flipper for initial frequency scanning
- Identify modulation and data rate
- Transfer parameters to Evil Crow for advanced attacks

**2. Dual Device Attacks:**
- Flipper performs jamming
- Evil Crow captures signals
- Coordinate via timing

**3. Protocol Analysis:**
- Flipper for quick field analysis
- Evil Crow for detailed capture
- Export to URH for deep analysis

### Data Exchange Methods

**Via SD Card:**
1. Capture with Flipper (.sub format)
2. Convert to Evil Crow format
3. Load on Evil Crow SD card

**Manual Transfer:**
1. Read Flipper captured data
2. Input RAW data to Evil Crow web interface
3. Transmit with Evil Crow's superior power

## Advanced Attack Scenarios

### 1. Parking Garage Infiltration

**Target:** Multi-tenant parking with remote access

**Attack Chain:**
1. Identify frequency (usually 433.92 MHz)
2. Capture tenant remote signal
3. Analyze for vulnerabilities
4. Replay or RollJam attack
5. Gain unauthorized access

### 2. Hotel Room Key Fob Cloning

**Target:** RF-based hotel locks (not RFID)

**Method:**
1. Identify lock manufacturer
2. Research known frequencies
3. Capture legitimate key signal
4. Clone for persistent access

### 3. Vehicle Keyless Entry Exploitation

**Modern Vehicle Attack:**
1. Identify key fob frequency
2. Perform RollJam attack
3. Capture multiple codes
4. Use codes strategically

### 4. Building Access Control

**Target:** RF badge systems (not RFID)

**Attack:**
1. Capture employee badge signal
2. Analyze protocol
3. Replay during off-hours
4. Combine with physical security bypass

## Protocol-Specific Attacks

### KeeLoq Vulnerabilities

**Known Weaknesses:**
- Side-channel attacks possible
- Some implementations use weak keys
- Manufacturer key extraction possible

**Attack Method:**
1. Capture multiple transmissions
2. Analyze for patterns
3. Attempt cryptanalysis
4. Extract manufacturer key if possible

### Princeton PT2262/PT2272

**Vulnerability:** Fixed code, no encryption

**Attack:**
```
Frequency: 433.92 MHz
Modulation: ASK/OOK
Code Length: 12 bits
Attack Time: <3 seconds for brute force
```

### HCS301 Rolling Code

**Attack Vectors:**
- RollJam still effective
- Some implementations vulnerable to replay
- Weak random number generation in clones

## Defensive Measures and Detection

### Detecting RF Attacks

**Indicators:**
1. Unusual RF activity on known frequencies
2. Repeated failed authentication attempts
3. Jamming detected on security frequencies
4. Unexpected device behavior

### Countermeasures

**For Defenders:**
1. Implement rolling codes properly
2. Use encryption beyond simple XOR
3. Add timestamp validation
4. Monitor for jamming
5. Use frequency hopping
6. Implement two-way authentication

### Legal Considerations

**Always Remember:**
- Only test devices you own
- Get written permission for client testing
- Understand local RF regulations
- Document all testing activities
- Never interfere with emergency frequencies
- Avoid testing near hospitals, airports

## Troubleshooting

### Common Issues and Solutions

**No Signal Captured:**
1. Increase RxBW gradually (58→100→200→325 kHz)
2. Verify correct frequency for your region
3. Check antenna connections
4. Try different modulation types
5. Ensure target device is transmitting

**2-FSK Not Working:**
- Must use Module 2 for RX
- Don't use Module 1 simultaneously
- Verify deviation settings (typically 20-50 kHz)
- Check data rate matches target

**Replay Not Working:**
- Target may use rolling codes
- Check timing between captures
- Verify exact frequency match
- Ensure sufficient transmission power
- Try multiple transmissions

**Web Interface Issues:**
- Verify SD card formatted correctly (FAT32)
- Check HTML folder copied completely
- Use recommended SD card size (≤32GB)
- Clear browser cache
- Try different browser

## Best Practices

### For Effective Testing

1. **Documentation:**
   - Log all parameters for successful attacks
   - Note environmental conditions
   - Record device responses
   - Create attack playbooks

2. **Methodology:**
   - Start with passive reconnaissance
   - Progress to active attacks
   - Always have rollback plan
   - Test during authorized windows

3. **Safety:**
   - Never jam emergency frequencies
   - Avoid interference with critical systems
   - Use minimum necessary power
   - Have legal authorization

### For Learning

1. **Start Simple:**
   - Begin with fixed-code devices
   - Master replay attacks first
   - Progress to rolling codes
   - Learn protocol analysis

2. **Build Skills:**
   - Understand modulation types
   - Learn common protocols
   - Practice with test devices
   - Study RF fundamentals

3. **Resources:**
   - Join Evil Crow Discord
   - Study FCC ID database
   - Use RTL-SDR for analysis
   - Practice with HackRF/YARD Stick One

## Additional Attack Techniques

### 11. Relay Attack on Keyless Entry Systems

**Target:** Modern cars with passive keyless entry

**Attack Chain:**
1. **Equipment Setup:**
   - Evil Crow near car
   - Second Evil Crow or SDR near key holder
   - Communication channel between devices

2. **Attack Execution:**
   ```
   Module 1: Capture car's challenge signal
   Module 2: Relay to second device
   Second device: Capture key response
   Module 1: Replay key response to car
   ```

3. **Success Rate:** High if within range of both car and key

### 12. Brute Force Attacks on Fixed Codes

**Common Code Lengths and Time Estimates:**
```
8-bit code: 256 combinations = ~2.5 seconds
10-bit code: 1024 combinations = ~10 seconds
12-bit code: 4096 combinations = ~40 seconds
16-bit code: 65536 combinations = ~10 minutes
```

**Optimization Techniques:**
- Use De Bruijn sequences
- Start with common codes (all 0s, all 1s)
- Try manufacturer defaults first

### 13. Weather Station Hijacking

**Target Devices:**
- AcuRite weather sensors
- La Crosse Technology stations
- Ambient Weather devices

**Attack Parameters:**
```
Frequency: 433.92 MHz or 915 MHz
Modulation: ASK/OOK
Data Rate: 1-4 kbps
Attack: Spoof temperature/humidity readings
```

### 14. Baby Monitor Exploitation

**Security Issues:**
- Many use unencrypted analog signals
- Digital ones often lack proper authentication
- Audio can be intercepted and injected

**Common Frequencies:**
```
Analog: 49.830-49.890 MHz, 902-928 MHz
Digital: 900 MHz, 2.4 GHz
```

### 15. Wireless Alarm System Attacks

**Vulnerable Components:**
- Door/window sensors
- Motion detectors
- Smoke detectors
- Key fobs

**Attack Methods:**
1. **Sensor Suppression:**
   - Continuously transmit "all clear" signal
   - Prevent real alerts from reaching base

2. **False Alarm Generation:**
   - Replay alarm signals
   - Cause system fatigue

3. **System Disarmament:**
   - Capture disarm code
   - Replay when needed

## Specific Manufacturer Vulnerabilities

### Linear MegaCode (Garage Doors)
```
Frequency: 318 MHz
Modulation: ASK/OOK
Vulnerability: Fixed portion of rolling code
Attack: Capture and analyze multiple transmissions
```

### Chamberlain/LiftMaster Security+ 2.0
```
Frequency: 310/315/390 MHz
Modulation: ASK/OOK
Vulnerability: Timing-based attacks possible
Attack: RollJam with precise timing
```

### Somfy RTS (Blinds/Awnings)
```
Frequency: 433.42 MHz
Modulation: ASK/OOK
Vulnerability: Rolling code can be predicted
Attack: Capture multiple codes, analyze sequence
```

## Advanced Flipper Zero Integration

### Workflow for Maximum Effectiveness

1. **Initial Reconnaissance (Flipper):**
   - Frequency detection
   - Basic signal analysis
   - Quick capture for testing

2. **Deep Analysis (Evil Crow):**
   - Precise parameter tuning
   - Long-duration captures
   - Multi-frequency monitoring

3. **Attack Execution:**
   - Use Evil Crow for power and range
   - Flipper for portability and stealth
   - Coordinate for complex attacks

### Data Format Conversion

**Flipper .sub to Evil Crow:**
```python
# Example conversion script structure
def convert_sub_to_raw(flipper_file):
    # Read Flipper .sub format
    # Extract frequency, modulation, data
    # Convert to Evil Crow RAW format
    # Return formatted string
```

### Combined Attack Scenarios

**Scenario 1: Corporate Parking Access**
1. Flipper: Quick scan of employee remotes
2. Evil Crow: Detailed capture and analysis
3. Flipper: Test replay in field
4. Evil Crow: Execute from distance

**Scenario 2: Multi-Factor RF Systems**
1. Evil Crow Module 1: Jam primary frequency
2. Evil Crow Module 2: Capture on secondary
3. Flipper: Mobile replay capability
4. Coordinate timing via smartphone

## Detailed Protocol Analysis

### Manchester Encoding
**Recognition:**
- Each bit represented by transition
- Common in RFID and some remotes
- Look for consistent pulse widths

**Decoding Tips:**
- Measure transition timing
- Identify clock rate
- Decode bit patterns

### PWM (Pulse Width Modulation)
**Recognition:**
- Variable pulse widths
- Fixed period between pulses
- Common in RC systems

**Analysis:**
- Measure high/low durations
- Calculate duty cycle
- Map to control values

### Rolling Code Algorithms

**KeeLoq:**
- 32-bit hopping code
- 28-bit serial number
- 64-bit manufacturer key
- Vulnerable to side-channel attacks

**AES-CCM (Advanced):**
- Used in newer systems
- 128-bit encryption
- Requires more sophisticated attacks

## Environmental Considerations

### Urban RF Interference
**Common Sources:**
- WiFi networks (2.4 GHz)
- Cell towers
- Industrial equipment
- Other hackers!

**Mitigation:**
- Use directional antennas
- Increase signal strength
- Choose off-peak hours
- Shield sensitive equipment

### Weather Effects
**Rain/Humidity:**
- Increases attenuation
- May require power adjustment
- Can affect frequency stability

**Temperature:**
- Crystal oscillator drift
- Battery performance impact
- Component tolerance changes

## Power and Range Optimization

### Antenna Selection
**Stock Antenna:**
- General purpose
- Moderate range
- Good for testing

**Directional Antenna:**
- Increased range
- Better noise rejection
- Requires aiming

**High-Gain Antenna:**
- Maximum range
- May violate regulations
- Use with caution

### Power Management
**Battery Life Extension:**
- Use sleep mode between attacks
- Minimize TX power when possible
- Disable unused modules
- Use efficient attack patterns

## Legal and Ethical Framework

### Testing Authorization
**Required Documentation:**
- Written permission from owner
- Defined scope of testing
- Time and location constraints
- Emergency contact information

### Responsible Disclosure
**If Vulnerability Found:**
1. Document thoroughly
2. Contact manufacturer
3. Allow reasonable fix time
4. Consider CVE submission
5. Share with community responsibly

### Emergency Frequency Awareness
**Never Interfere With:**
- 121.5 MHz (Aviation emergency)
- 156.8 MHz (Marine emergency)
- 406 MHz (Emergency beacons)
- Public safety frequencies

This comprehensive guide should help you effectively use the Evil Crow RF V2 for legitimate security testing and research purposes. Remember to always comply with local laws and regulations regarding RF transmission and only test devices you own or have explicit permission to test.
