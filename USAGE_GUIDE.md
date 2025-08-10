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

## Attack Playbooks

### Playbook 1: Residential Garage Door Penetration

**Objective:** Gain unauthorized access to residential garage

**Pre-Attack Reconnaissance:**
1. **Visual Inspection:**
   - Identify garage door manufacturer (common: Chamberlain, LiftMaster, Genie)
   - Note remote control model if visible
   - Check for external keypads (additional attack vector)

2. **RF Survey:**
   ```
   Initial Scan Parameters:
   - Frequency Range: 300-450 MHz
   - Common Frequencies: 315, 318, 390, 433.92 MHz
   - Modulation: Start with ASK/OOK
   - RxBW: 325 kHz (wide scan)
   ```

3. **Target Identification:**
   - Wait for legitimate use
   - Capture and analyze signal
   - Determine exact frequency and parameters

**Attack Execution:**

**Phase 1 - Fixed Code Attack (Older Systems):**
```
1. Configure RX:
   Module: 1
   Frequency: [Detected frequency]
   Modulation: ASK/OOK
   RxBW: 58 kHz
   Data Rate: 2-10 kbps

2. Capture legitimate signal
3. Analyze for fixed pattern
4. Configure TX with same parameters
5. Replay captured signal
```

**Phase 2 - Rolling Code Attack (Modern Systems):**
```
1. Setup Dual Module Configuration:
   Module 1 (Jamming):
   - Frequency: [Target freq]
   - Continuous noise transmission
   
   Module 2 (Capture):
   - Frequency: [Target freq]
   - RxBW: 58 kHz
   - Ready to capture

2. Execute RollJam:
   - Start jamming
   - Victim presses button (no response)
   - Capture first code
   - Victim presses again
   - Stop jamming briefly
   - Capture second code
   - Resume jamming

3. Exploitation:
   - Stop jamming
   - Transmit first code (door opens)
   - Save second code for future use
```

**Post-Exploitation:**
- Document successful parameters
- Test range limitations
- Identify detection methods
- Plan persistent access

### Playbook 2: Corporate Building Access System

**Objective:** Bypass RF-based access control

**Phase 1 - Intelligence Gathering:**
```
1. Identify Access Points:
   - Employee entrances
   - Parking gates
   - Loading docks
   - Emergency exits

2. Technology Assessment:
   - Observe employees using fobs
   - Identify reader manufacturers
   - Note LED/sound feedback
   - Check for visible FCC IDs

3. Timing Analysis:
   - Peak entry times (7-9 AM)
   - Shift changes
   - Lunch periods
   - After-hours access
```

**Phase 2 - Signal Intelligence:**
```
1. Passive Collection Setup:
   Module: 1
   Frequency: 315/433.92 MHz (common)
   Modulation: ASK/OOK
   RxBW: 325 kHz (wide capture)
   Duration: Full business day

2. Signal Analysis:
   - Identify unique fob IDs
   - Detect patterns in usage
   - Find high-privilege fobs
   - Map fob to entrance correlation
```

**Phase 3 - Active Exploitation:**
```
1. Target Selection:
   - Choose high-privilege fob
   - Verify after-hours access
   - Confirm no two-factor auth

2. Capture Strategy:
   - Position near target employee
   - Use directional antenna
   - Capture during legitimate use
   - Verify clean capture

3. Replay Attack:
   - Test at low-traffic times
   - Start with non-critical doors
   - Escalate to secure areas
   - Maintain operational security
```

### Playbook 3: Vehicle Keyless Entry Compromise

**Target:** Modern vehicles with RF key fobs

**Pre-Attack Vehicle Research:**
```
1. Identify Vehicle:
   - Make, model, year
   - Key fob type (check FCC ID)
   - Known vulnerabilities database
   - Regional frequency variants

2. Common Frequencies by Region:
   - North America: 315 MHz
   - Europe: 433.92 MHz
   - Japan: 312-315 MHz
   - Multi-region: 868 MHz
```

**Attack Methodology:**

**Method 1 - RollJam Attack:**
```
Equipment Setup:
- Evil Crow RF V2
- Directional antenna (optional)
- Laptop for logging
- External battery pack

Execution:
1. Position near target vehicle
2. Configure dual-module setup
3. Wait for owner approach
4. Execute jamming sequence
5. Capture unlock codes
6. Allow final unlock
7. Retain codes for later
```

**Method 2 - Relay Attack (Passive Entry):**
```
Requirements:
- Two Evil Crow devices
- Communication channel
- Proximity to both car and key

Setup:
Device 1 (Near Car):
- Module 1: RX on LF (125-134 kHz)
- Module 2: TX on UHF

Device 2 (Near Key):
- Module 1: TX on LF
- Module 2: RX on UHF

Execution:
1. Car transmits LF challenge
2. Device 1 captures and relays
3. Device 2 transmits to key
4. Key responds with UHF
5. Device 2 captures response
6. Device 1 replays to car
7. Car unlocks/starts
```

### Playbook 4: Smart Home Ecosystem Attack

**Objective:** Compromise home automation system

**Target Devices:**
```
Priority Targets:
1. Smart locks
2. Alarm systems
3. Garage doors
4. Security cameras
5. Environmental controls
```

**Reconnaissance Phase:**
```
1. External Survey:
   - Identify visible devices
   - Note manufacturer labels
   - Check for FCC IDs
   - Map device locations

2. RF Mapping:
   Frequency Scan:
   - 315 MHz (Honeywell, DSC)
   - 319.5 MHz (Qolsys)
   - 345 MHz (Honeywell 5800)
   - 433.92 MHz (Generic)
   - 868 MHz (Z-Wave EU)
   - 908.42 MHz (Z-Wave US)

3. Signal Intelligence:
   - Monitor for 24 hours
   - Identify device patterns
   - Map communication flow
   - Find critical sensors
```

**Exploitation Strategies:**

**Strategy 1 - Sensor Spoofing:**
```
Target: Window/Door Sensors
1. Capture "closed" signal
2. Replay during intrusion
3. Suppress "open" alerts
4. Maintain stealth entry
```

**Strategy 2 - Alarm Disarmament:**
```
Target: Wireless Keypads
1. Capture disarm sequence
2. Analyze for rolling code
3. Replay or predict next
4. Disable alarm system
```

**Strategy 3 - Camera Blinding:**
```
Target: Wireless Cameras
1. Identify video frequency
2. Generate interference
3. Cause connection loss
4. Create blind spots
```

### Playbook 5: Industrial/SCADA Attack

**WARNING:** Only perform with explicit authorization!

**Target Systems:**
```
Common Targets:
- Crane controls
- Gate operators
- Emergency stops
- Sensor networks
- Telemetry systems
```

**Safety Considerations:**
```
Critical Safety:
1. Never interfere with emergency stops
2. Avoid active industrial processes
3. Coordinate with safety personnel
4. Have emergency shutdown plan
5. Document all actions
```

**Attack Phases:**

**Phase 1 - Passive Reconnaissance:**
```
RF Survey Parameters:
- 72-76 MHz (Industrial RC)
- 150-174 MHz (VHF Industrial)
- 450-470 MHz (UHF Industrial)
- 902-928 MHz (ISM Band)

Signal Collection:
- Extended monitoring period
- Identify control patterns
- Map operator schedules
- Document all findings
```

**Phase 2 - Protocol Analysis:**
```
1. Capture control sequences
2. Identify command structure
3. Decode safety interlocks
4. Map function codes
5. Build command library
```

**Phase 3 - Controlled Testing:**
```
1. Isolated system only
2. Start with status queries
3. Test non-critical functions
4. Verify safety systems
5. Document vulnerabilities
```

## Vulnerability Databases

### Manufacturer-Specific Vulnerabilities

#### Chamberlain/LiftMaster

**Security+ 1.0 (1998-2004):**
```
Frequency: 390 MHz
Modulation: ASK/OOK
Vulnerability: Weak rolling code
Attack: RollJam effective
Time to Compromise: <1 minute
```

**Security+ 2.0 (2011-present):**
```
Frequency: 310/315/390 MHz
Modulation: ASK/OOK
Vulnerability: Timing attacks
Attack: Advanced RollJam
Time to Compromise: 2-5 minutes
```

**MyQ Systems:**
```
Primary: 315/390 MHz RF
Secondary: WiFi connected
Vulnerability: RF still vulnerable
Attack: Capture RF, ignore WiFi
```

#### Linear/Moore-O-Matic

**MegaCode (1997-present):**
```
Frequency: 318 MHz
Modulation: ASK/OOK
Code Format: 20-bit fixed + 20-bit rolling
Vulnerability: Fixed portion replay
Attack: Partial code exploitation
```

**Multi-Code:**
```
Frequency: 300-318 MHz
Modulation: ASK/OOK
Code Format: 10-12 bit fixed
Vulnerability: Brute force feasible
Attack Time: <1 minute
```

#### Genie/Overhead Door

**Intellicode (1995-present):**
```
Frequency: 315/390 MHz
Modulation: ASK/OOK
Vulnerability: RollJam susceptible
Additional: Some fixed code compatible
```

**Fixed Code Models:**
```
Frequency: 390 MHz
Code Length: 9-12 bits
Attack: Direct brute force
Time: 10-40 seconds
```

### Vehicle Key Fob Database

#### Ford/Lincoln/Mercury

**2010-2017 Models:**
```
Frequency: 315 MHz (US), 433.92 MHz (EU)
Modulation: ASK/OOK
Protocol: 40-bit rolling code
Vulnerability: RollJam effective
```

**2018+ Models:**
```
Frequency: 902-928 MHz (some models)
Modulation: 2-FSK
Security: Enhanced rolling code
Attack: Relay attacks still possible
```

#### General Motors (Chevrolet/GMC/Cadillac)

**2006-2013:**
```
Frequency: 315 MHz
Modulation: ASK/OOK
Vulnerability: Weak randomization
Attack: RollJam, replay possible
```

**2014-2020:**
```
Frequency: 315/433.92 MHz
Enhanced Security: Yes
Vulnerability: Relay attacks
Attack: Requires two devices
```

#### Toyota/Lexus

**2008-2015:**
```
Frequency: 315 MHz (US), 433.92 MHz (EU)
Modulation: ASK/OOK
Code: 40-bit rolling
Vulnerability: Standard RollJam
```

**Smart Key Systems:**
```
LF: 125-134 kHz
UHF: 312-315 MHz
Attack: Relay attack effective
Range: Up to 100m with amplification
```

#### Honda/Acura

**2005-2014:**
```
Frequency: 315/433.92 MHz
Modulation: ASK/OOK
Vulnerability: Fixed portion in code
Attack: Partial replay possible
```

**2015+:**
```
Enhanced Rolling Code: Yes
Vulnerability: Relay attacks
Additional: Some models use 2-FSK
```

### Smart Home Device Database

#### Ring Alarm

**Sensors:**
```
Frequency: 908.42 MHz (Z-Wave)
Modulation: 2-FSK
Encryption: AES-128
Vulnerability: Jamming possible
```

**Keypads:**
```
Frequency: 908.42 MHz
Protocol: Z-Wave Plus
Attack: Capture during pairing
```

#### SimpliSafe

**Gen 1/2:**
```
Frequency: 315/433.92 MHz
Modulation: ASK/OOK
Encryption: None
Vulnerability: Replay attacks
```

**Gen 3:**
```
Frequency: 433.92 MHz
Encryption: Proprietary
Vulnerability: Jamming only
```

#### ADT/Honeywell

**5800 Series:**
```
Frequency: 345 MHz
Modulation: ASK/OOK
Protocol: Fixed 24-bit ID
Vulnerability: ID spoofing
```

**SiX Series:**
```
Frequency: 345 MHz
Encryption: 128-bit AES
Vulnerability: Limited
Attack: Jamming primary option
```

### TPMS Sensor Database

#### Schrader (OEM for many brands)

**Gen 3/4:**
```
Frequency: 315/433.92 MHz
Modulation: 2-FSK
Data Rate: 9.6-19.2 kbps
ID Length: 32 bits
Attack: ID cloning possible
```

#### Continental/VDO

**REDI-Sensor:**
```
Frequency: 315/433.92 MHz
Protocol: Proprietary
Vulnerability: Replay during learning
Attack: Trigger warning lights
```

#### Pacific/TRW

**Standard Sensors:**
```
Frequency: 315/433.92 MHz
Modulation: ASK/OOK or 2-FSK
Vulnerability: No encryption
Attack: Direct spoofing
```

## Attack Pattern Recognition

### Defensive Signatures

**Rolling Code Indicators:**
```
1. Signal changes each transmission
2. No two captures identical
3. Counter incrementing visible
4. Timestamp correlation
```

**Encrypted Signal Indicators:**
```
1. No visible patterns
2. High entropy data
3. Consistent packet length
4. Preamble followed by noise
```

**Frequency Hopping Indicators:**
```
1. Signal appears intermittent
2. Multiple frequencies active
3. Synchronized patterns
4. Predictable hop sequence
```

### Attack Success Indicators

**Fixed Code Success:**
```
1. Replay works repeatedly
2. Same code always effective
3. No timeout observed
4. Simple implementation
```

**Rolling Code Compromise:**
```
1. Captured code works once
2. System accepts old codes
3. Counter can be manipulated
4. Desynchronization possible
```

**Jamming Effectiveness:**
```
1. Legitimate signals blocked
2. Target shows no response
3. User repeatedly tries
4. Opportunity for capture
```

## Advanced Techniques Database

### Signal Manipulation Techniques

**Bit Flipping:**
```
Purpose: Modify specific commands
Method: 
1. Identify command bits
2. Flip targeted bits
3. Recalculate checksum
4. Transmit modified signal
```

**Packet Injection:**
```
Purpose: Insert malicious commands
Method:
1. Understand packet structure
2. Craft custom packets
3. Time injection properly
4. Overwhelm legitimate signals
```

**Replay with Modification:**
```
Purpose: Alter captured signals
Method:
1. Capture legitimate signal
2. Decode protocol structure
3. Modify payload data
4. Update error checking
5. Transmit modified version
```

### Protocol Reverse Engineering

**Step-by-Step Methodology:**
```
1. Capture Multiple Samples:
   - Same command repeated
   - Different commands
   - Different devices
   - Various conditions

2. Identify Structure:
   - Preamble/sync pattern
   - Device ID location
   - Command bits
   - Checksum/CRC

3. Decode Encoding:
   - Manchester
   - PWM
   - Raw binary
   - Custom schemes

4. Understand Logic:
   - Command mapping
   - State machines
   - Security features
   - Error handling
```

## Tool Integration Database

### Software Tools

**URH (Universal Radio Hacker):**
```
Purpose: Signal analysis
Integration: 
- Export Evil Crow captures
- Import for deep analysis
- Generate attack patterns
- Export back to Evil Crow
```

**GNU Radio:**
```
Purpose: Advanced signal processing
Use Cases:
- Custom modulation schemes
- Protocol development
- Signal generation
- Real-time analysis
```

**Inspectrum:**
```
Purpose: Visual signal analysis
Benefits:
- Waterfall visualization
- Timing measurement
- Pattern recognition
- Export capabilities
```

### Hardware Combinations

**Evil Crow + HackRF:**
```
Setup: 
- Evil Crow for targeted TX
- HackRF for wideband RX
- Coordinated attacks
- Extended frequency range
```

**Evil Crow + RTL-SDR:**
```
Configuration:
- RTL-SDR for monitoring
- Evil Crow for active attacks
- Cost-effective solution
- Multiple frequency coverage
```

**Evil Crow + YARD Stick One:**
```
Advantages:
- Redundant capabilities
- Different frequency bands
- Backup device ready
- Specialized attacks each
```

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
