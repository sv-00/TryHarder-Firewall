# TryHarder-Firewall

A custom firewall designed to detect and block potential malicious actors by identifying abnormal SYN requests across multiple ports. This project enhances network security by filtering unauthorized access attempts and responding with a challenge-based message.

## Logical Diagram
![firewall](https://github.com/user-attachments/assets/b78a5d14-6a69-445b-b223-847a98874f29)

### Identifying a Malicious Actor

When an unusual number of SYN requests are sent to different ports within a short timeframe, it may indicate a port-scanning attempt. The firewall detects these patterns and dynamically applies rules to mitigate the potential threat.

**Workflow:**

1. An attacker machine (VPS or local) sends multiple SYN requests across various ports.
2. The firewall captures and inspects network traffic using `tcpdump`.
3. If a pattern of suspicious activity is detected, a firewall rule is applied to mitigate further attempts.
4. Subsequent unauthorized SYN packets are dropped, preventing further reconnaissance.

## Steps to Implement the Firewall

### 1. Set Up the Target and Attacker Machines

- Get a **VPS** with password-based authentication to act as the target machine, replicating a real-world scenario where a server is exposed to external traffic.
- Install **Python 3** as we will be adding a firewall script to this VPS.
- Set up a separate **VPS or local machine** as the attacker machine to simulate real-world port scanning and attack attempts.

### 2. Detecting Port Scanning with `tcpdump`

- To identify SYN packets (connection requests), run:
  ```sh
  tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0'
  ```
- To avoid name resolution and speed up detection:
  ```sh
  tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0' -n
  ```

### 3. Simulating an Attack with Nmap

- From the attacker machine, reduce suspicion while scanning by introducing a delay between requests:
  ```sh
  nmap -T2 $TARGET
  ```
  This slows down the scan to avoid detection by standard rate-limiting techniques.

### 4. Configuring Firewall Rules with `iptables`

- To list current firewall rules:
  ```sh
  iptables -L
  ```
- To block suspicious traffic based on detected patterns:
  ```sh
  iptables -A INPUT -j DROP
  ```
- Once the rule is applied, an attacker attempting a scan will no longer receive an ACK response, preventing further reconnaissance.

## 5. Implementing the Firewall Script

- The `script.py` file contains a Python-based firewall implementation that dynamically detects and blocks malicious activity.
- Ensure Python 3 is installed on the VPS and run the script:
  ```sh
  python3 script.py
  ```
- The script will monitor network traffic, detect SYN flooding attempts, and update firewall rules in real-time.

## Conclusion

The **TryHarder-Firewall** helps detect and mitigate potential threats by identifying unusual SYN request patterns and blocking unauthorized scanning attempts. By using a VPS as a target machine, this setup effectively replicates real-world attack scenarios and strengthens server security against reconnaissance activities.

