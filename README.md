# flutter-ssl-pinning-bypass
Hands-on SSL pinning bypass in Flutter Android apps using Frida and Burp Suite

# 🔐 Bypassing SSL Pinning in Flutter Android Applications (Hands-on)

## 📌 Overview

This project documents a hands-on approach to bypass SSL pinning in a Flutter-based Android application using dynamic instrumentation and traffic interception techniques.

The objective was to analyze application API communication by intercepting HTTPS traffic, which was initially blocked due to SSL pinning and Flutter-specific networking behavior.

---

## 🧠 Introduction

SSL pinning is a security mechanism used in mobile applications to ensure that only trusted certificates are accepted during secure communication. This helps prevent man-in-the-middle (MITM) attacks and unauthorized traffic interception.

During a mobile application security assessment, I analyzed a Flutter-based Android application that implemented SSL pinning, which prevented HTTPS traffic interception using tools such as Burp Suite.

Due to Flutter’s architecture, standard interception techniques were not effective, requiring a different approach.

---

## ⚠️ Challenges

- Application built using Flutter (`libflutter.so`)
- Standard SSL bypass techniques were ineffective
- Application ignored system proxy settings
- No traffic appeared in proxy tools
- SSL verification implemented within Flutter engine

---

## 🧰 Prerequisites

- Rooted Android device
- Laptop / computer
- USB cable
- ADB installed
- Frida installed
- Burp Suite installed

---

## 🔬 Methodology

### 1. Connect Device

Connect the Android device to your laptop and verify:

```bash
adb devices
2. Install and Run Frida Server

Install Frida on your laptop:

pip install frida-tools
pip install frida

Download the correct frida-server binary (based on device architecture), then push it to the device:

adb push frida-server /data/local/tmp/

Set permissions:

adb shell "su -c 'chmod 755 /data/local/tmp/frida-server'"

Start the server:

adb shell "su -c '/data/local/tmp/frida-server &'"

Note: ADB runs on the laptop, while frida-server runs on the mobile device.

3. Verify Frida Connection
frida-ps -U
4. Identify Target App
frida-ps -Uai | grep -i <app_name>

Example:

9290  com.example.app
5. Attach Frida (SSL Bypass)
frida -U -p <PID> --codeshare TheDauntless/disable-flutter-tls-v1

Expected output:

[+] Flutter library located
[+] ssl_verify_peer_cert found
[+] ssl_verify_peer_cert has been patched
6. Redirect Traffic (iptables)
adb shell
su
iptables -t nat -A OUTPUT -p tcp --dport 443 -j DNAT --to-destination <BURP_IP>:8081
iptables -t nat -A OUTPUT -p tcp --dport 80 -j DNAT --to-destination <BURP_IP>:8081
iptables -t nat -A OUTPUT -p udp --dport 443 -j DNAT --to-destination <BURP_IP>:8081
7. Configure Burp Suite
Listener on port 8081
Bind to All interfaces
Enable Invisible proxying
✅ Results
Successfully bypassed SSL pinning in a Flutter Android app
Intercepted HTTPS traffic using Burp Suite
Achieved full visibility of API communication
Demonstrated Flutter-specific interception challenges
💡 Key Takeaways
Flutter apps require specialized SSL bypass techniques
Standard Android methods may not work
Some apps ignore proxy settings → require forced routing
Understanding framework internals is critical
🧾 Conclusion

Bypassing SSL pinning in Flutter applications requires:

Dynamic instrumentation (Frida)
Understanding Flutter networking behavior
Network-level traffic redirection
Adaptive testing techniques
⚠️ Disclaimer

This work was conducted in an authorized environment for security testing purposes only.
