# Updated README - AWS IoT Configuration for ESP8266/ESP32 (WORKING CODE)

## Introduction
This guide explains how to connect your ESP8266/ESP32 device to AWS IoT Core using X.509 certificate authentication. The code has been optimized for better performance and security, following AWS IoT best practices.

## Development Environment
**IDE Used**: Arduino IDE 1.8.10  
**File Upload Plugin**: ESP8266FS (for uploading certificates to ESP8266/ESP32 flash memory)  

### Required Libraries
| Library | Purpose | Link |
|---------|---------|------|
| `WiFiClientSecure` | Secure TLS connection | [Link](https://github.com/esp8266/Arduino) |
| `PubSubClient` | MQTT communication | [Link](https://github.com/knolleary/pubsubclient) |
| `FS.h` | Flash filesystem management | Included in ESP8266 Core |
| `BearSSLHelpers.h` | SSL/TLS protocol support | Included in ESP8266 Core |
| `CertStoreBearSSL.h` | Certificate storage | Included in ESP8266 Core |
| `NTPClient.h` | Time synchronization | [Link](https://github.com/arduino-libraries/NTPClient) |
| `WiFiUdp.h` | UDP protocol for NTP | Included in ESP8266 Core |
| `ESP8266WiFi.h` | WiFi connection (ESP8266) | Included in ESP8266 Core |
| `WiFi.h` | WiFi connection (ESP32) | Included in ESP32 Core |

## Required Configuration

### 1. WiFi Credentials
```cpp
const char* ssid = "xxxxxxxxx";
const char* password = "xxxxxxxxxxxx";
```

### 2. AWS IoT Endpoint
```cpp
const char* AWS_endpoint = "xxxxxxxxxxx-ats.iot.us-east-2.amazonaws.com";
```

### 3. Certificate Files
Ensure these files are in SPIFFS:
```
/CertificadosAmazon/AmazonRootCA1.pem
/CertificadosAmazon/certificate.pem.crt 
/CertificadosAmazon/private.pem.key
```

## Memory Management (Critical!)
The ESP8266 has strict memory limitations:

### Current Memory Status:
- **RAM**: 36% used (29.3KB/80KB) - ✅ Safe
- **IRAM**: 92% used (60.6KB/64KB) - ⚠️ Critical!
- **Flash**: 38% used (399KB/1MB) - ✅ Plenty available

### Optimization Recommendations:
1. **For critical IRAM**:
```cpp
void ICACHE_FLASH_ATTR nonCriticalFunction() {
  // Move code to Flash
}
```

2. **To save RAM**:
```cpp
const char largeText[] PROGMEM = "Long text...";
```

3. **Dynamic management**:
```cpp
// Always free allocated memory
delete[] buffer; 
free(ptr);
```

## Secure Connection Flow
1. Initialize SPIFFS filesystem
2. Connect to WiFi network
3. Synchronize time via NTP (essential for TLS)
4. Load X.509 certificates from flash memory
5. Establish TLS connection with AWS IoT Core
6. Maintain active MQTT connection

## Future Implementations
### 1. Advanced Security
- Automatic certificate rotation
- Certificate revocation checking (CRL/OCSP)
- Additional application-level encryption

### 2. Monitoring
- Periodic performance metrics reporting
- Hierarchical remote logging system
- Critical condition alerts

## Testing the Connection
1. In AWS Console:
   - Navigate to IoT Core > Test > MQTT test client
   - Subscribe to topic `outTopic`

2. To send commands:
   ```json
   {
     "command": "status",
     "device": "ESP8266_01"
   }
   ```

## Troubleshooting Common Issues
| Issue | Solution |
|-------|----------|
| TLS connection failure | Verify: 1) Correct certificates 2) Synced time 3) AWS policies |
| Random reboots | Monitor memory usage with `ESP.getFreeHeap()` |
| MQTT disconnections | Implement automatic reconnect mechanism |

## Useful Links
- [AWS IoT Core Guide](https://docs.aws.amazon.com/iot/latest/developerguide/what-is-aws-iot.html)
- [ESP8266 Documentation](https://arduino-esp8266.readthedocs.io/en/latest/)
- [AWS Official Examples](https://github.com/aws-samples/aws-iot-esp8266-examples)
