# **README – Network Sniffing Detection using iptraf-ng (2-VM Setup on VirtualBox)**

This README provides complete instructions for setting up, running, and analyzing a simple network sniffing scenario using **two Ubuntu Virtual Machines** inside VirtualBox. The goal is to demonstrate **how iptraf-ng can detect suspicious traffic that indicates sniffing activity**, as required in the assignment.

---

## **📌 1. Overview**

This project simulates a basic network environment consisting of:

* **Client/Target VM (Ubuntu)** → Runs *iptraf-ng* to monitor network traffic
* **Attacker VM (Ubuntu)** → Runs sniffing tools such as *tcpdump* or *arpspoof*

Both VMs are connected using **VirtualBox Internal Network**, ensuring they communicate only with each other.

The objective is to show how sniffing tools create unusual network behavior that can be detected by iptraf-ng.

---

## **📌 2. System Requirements**

* Host OS: Windows 10/11
* VirtualBox installed
* Ubuntu ISO (20.04 or 22.04)
* At least **2 GB RAM per VM** recommended

---

## **📌 3. Virtual Machine Setup**

### **3.1 Create Two VM Instances**

1. Install Ubuntu normally for the first VM.
2. Clone the VM: Right Click → **Clone** → **Full Clone**.

### **3.2 Configure Network**

On both VMs:

* Settings → Network → Adapter 1
* Set **Attached to: Internal Network**
* Name: `intnet`

This ensures both VMs communicate through a private virtual network.

---

## **📌 4. IP Address Configuration (Updated for enp0s8 Only)**

To simplify the setup and match your VM configuration, each VM will use **only interface enp0s8** for the sniffing experiment.

### **Client/Target VM**

Edit netplan:

```
sudo nano /etc/netplan/01-netcfg.yaml
```

Use this configuration:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      addresses:
        - 10.0.10.10/24
```

Apply changes:

```
sudo netplan apply
```

### **Attacker VM**

Edit netplan:

```
sudo nano /etc/netplan/01-netcfg.yaml
```

Use this configuration:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      addresses:
        - 10.0.10.20/24
```

Apply changes:

```
sudo netplan apply
```

### **Ping Test (Using enp0s8 IPs)**

From Attacker → Client:

```
ping 10.0.10.10
```

From Client → Attacker:

```
ping 10.0.10.20
```

If both replies are successful, the internal network is working properly.

---

## **📌 5. Installing Monitoring & Sniffing Tools**

### **Client/Target VM**

Install iptraf-ng:

```
sudo apt update
sudo apt install iptraf-ng -y
```

Run iptraf:

```
sudo iptraf-ng
```

### **Attacker VM**

Install tcpdump (required):

```
sudo apt install tcpdump -y
```

Optional ARP spoofing tools:

```
sudo apt install dsniff -y
```

---

## **📌 6. Observing Normal Traffic**

1. Open iptraf-ng on the Client VM.
2. Select **IP traffic monitor**.
3. Observe normal conditions:

   * Minimal ARP traffic
   * No unusual MAC/IP addresses
   * Stable packet flow

Take **screenshot #1** for documentation.

---

## **📌 7. Running Sniffing Simulation**

### **Method 1 – Using tcpdump (Simple & Recommended)**

On Attacker VM:

```
sudo tcpdump -i enp0s3
```

This places the NIC into *promiscuous mode*.

**Effect on iptraf:**

* Increased ARP traffic
* More broadcast packets
* Unusual packet patterns

Take **screenshot #2**.

---

## **📌 8. Analysis (Why iptraf Detects Sniffing)**

Sniffing tools such as *tcpdump* and *arpspoof* create detectable anomalies:

### **1. Promiscuous Mode**

Sniffing requires the attacker’s NIC to accept all packets, not just those addressed to itself.

### **2. Unexpected MAC/IP Activity**

The Client VM receives packets that **should not normally reach it**, which iptraf displays.

### **3. Broadcast Traffic Spikes**

Sniffing frequently generates excessive broadcast frames.

These abnormal patterns are why **iptraf warns about possible sniffing activity**.

---

## **📌 9. Cleanup Instructions**

If you want to clean up the system:

### Remove tools:

```
sudo apt remove iptraf-ng tcpdump dsniff -y
sudo apt autoremove -y
sudo apt clean
```

### Delete VM folders (Windows host):

```
C:\Users\YOUR_USER\VirtualBox VMs\
```

---

## **📌 10. Group Task Division (3 Members — Updated)****

**Member 1 – VM Handler (Only one person)**

* Create / clone both VMs
* Configure networking (Internal Network)
* Set static IP
* Install tools (iptraf-ng, tcpdump)
* Perform sniffing simulation and gather screenshots

**Member 2 – Document Analyst**

* Analyze normal vs suspicious traffic from screenshots
* Explain why sniffing is detectable in iptraf
* Describe ARP anomalies, promiscuous mode effects, and packet patterns

**Member 3 – Report Writer / Final Documentation**

* Compile all analysis into a structured report
* Create conclusions and recommendations
* Clean up writing, diagrams, and formatting
* Assemble the final submission

---

## **📌 11. Conclusion**

This project demonstrates how a simple VirtualBox environment can be used to analyze suspicious network activity. Using iptraf-ng alongside sniffing tools provides a clear understanding of how network monitoring works and how sniffing attempts can be detected based on abnormal traffic patterns.

---
