# Evil-Twin_Node_MCU

## Key Components

**Fake Access Point:**
- Creates Wi-Fi network with specified SSID
- Uses IP 192.168.1.1 for gateway

**Captive Portal:**
- DNS server redirects all domains to ESP
- Any HTTP request shows login page
- Professional-looking login form

**Credential Harvesting:**
- Captures username/password via POST
- Prints credentials to serial monitor
- Shows "success" redirect after submission

## Setup Instructions
- Upload code to NodeMCU
- Open Serial Monitor (115200 baud)
- Connect device to Free Public WiFi
- Attempt to browse any website
- Enter test credentials in login page
- Observe credentials in Serial Monitor

## Ethical Considerations
- **Legal Compliance:** Only test on your own networks
- **Disclosure:** Add disclaimer to login page in real tests
- **Data Handling:** Never store captured credentials permanently
- **Permissions:** Requires written consent for penetration testing

## Enhancements (Optional)
- Add EAP/TLS encryption simulation
- Implement WiFi deauthentication (requires external hardware)
- Add SSL/TLS (requires HTTPS library)
- Store credentials to SPIFFS/SD card