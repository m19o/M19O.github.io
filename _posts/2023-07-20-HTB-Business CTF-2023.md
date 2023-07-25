---
published: true
title: HTB Business CTF 2023
date: 2023-07-20 04:11:00 +0200
categories: [CTF]
tags: CTF
---
TL:DR Hello Folks, I will share my writeup for the Scada Challenge. I hope you enjoy and benefit from the blog post.


<img src="https://i.ibb.co/wQpyJvY/6a0133f264aa62970b0282e1497ddf200b-800wi.png" alt="6a0133f264aa62970b0282e1497ddf200b-800wi" border="0">

# WatchTower

## Challenge Description

Our infrastructure monitoring system detected some abnormal behavior and initiated a network capture. We need to identify the information the intruders collected and altered on the network.

---

## Methodology

As someone who has zero knowledge of Scada, the first thing I did was take the PCAP file and check what protocol is used. After that, I started to search for what that protocol is used for, and then I asked a friend who is an expert in OT hacking ^_^ . He guided me and told me about the functions used by that protocol.

Before diving into traffic analysis, there are some basics you should know.

---

### What is Modbus ?

Simply, it’s a data communication protocol over serial lines between devices, and it’s used in SCADA systems. If you want to know more about it, you can check out this <a href="https://www.se.com/us/en/faqs/FA168406/" target="_blank">Link</a>

### Modbus Objects :

1. **Coils** are 1-bit registers, are used to control discrete outputs, and may be read or written
2. **Discrete inputs** are 1-bit registers used as inputs, and may only be read.
3. **Input Registers** are 16-bit registers used for input, and may only be read
4. **Holding Registers** are the most universal 16-bit register, may be read or written, and may be used for a variety of things including inputs, outputs, configuration data, or any requirement for "holding" data.

### Data Access Functions :

What we need to know the functions that are used to access data. There are more functions for Modbus, but they are not in our interest.

| Function type |  |  | Function name | Function code |
| --- | --- | --- | --- | --- |
| Data Access | Bit access | Physical Discrete Inputs | https://ozeki.hu/p_5877-mobdbus-function-code-2-read-discrete-inputs.html | 2 |
|  |  | Internal Bits orPhysical Coils | https://ozeki.hu/p_5876-mobdbus-function-code-1-read-coils.html | 1 |
|  |  |  | https://ozeki.hu/p_5880-mobdbus-function-code-5-write-single-coil.html | 5 |
|  |  |  | https://ozeki.hu/p_5882-mobdbus-function-code-15-write-multiple-coils.html | 15 |
|  | 16-bit access | Physical Input Registers | https://ozeki.hu/p_5879-mobdbus-function-code-4-read-input-registers.html | 4 |
|  |  | Internal Registers orPhysical Output Registers | https://ozeki.hu/p_5878-mobdbus-function-code-3-read-multiple-holding-registers.html | 3 |
|  |  |  | https://ozeki.hu/p_5881-mobdbus-function-code-6-write-single-holding-register.html | 6 |
|  |  |  | https://ozeki.hu/p_5883-mobdbus-function-code-16-write-multiple-holding-registers.html | 16 |
|  |  |  | Read/Write Multiple Registers | 23 |
|  |  |  | Mask Write Register | 22 |
|  |  |  | Read FIFO Queue | 24 |

---

### Solution :

As shown in the screenshot below, you can find the function calls and the data accessed by that function.

<img src="https://i.ibb.co/LrCgfM8/Untitled.png" alt="Open">

I created a filter to focus on write calls to see if I could find any interesting data.

**Create a filter in Wireshark:**
  1. Choose what you want to filter out from the packet details pane.
  2. Right-click on it, then choose Apply as a Filter.
  3. Then choose selected

<img src="https://i.ibb.co/sw8GC7r/Untitled-1.png" alt="Filter">

After filtering the traffic, I checked the data sent by using the write multiple registers function. In a normal scenario, you will find the payload in a row called data. Traffic here contains ASCII in the reference number.

<img src="https://i.ibb.co/zb9yvYZ/Untitled-2.png" alt="Flag">

### Flag

After converting all the ASCII to text, I found that flag : HTB{m0d8u5_724ff1c_15_un3nc2yp73d!@^}

# Conclusion

Modbus is not secure by design. Attackers can sniff packets and perform MiTM attacks. Modbus doesn’t have any authentication layer. Security consultants advise implementing controls preventing any access to Modbus traffic and monitoring it 24/7 to catch any kind of malicious activity before it affects the system.

# References

1. https://www.vanimpe.eu/2015/12/07/introduction-to-modbus-tcp-traffic/
2. https://www.youtube.com/watch?v=OAsLdXzKQo8
3. https://theautomization.com/what-is-modbus-tcp-ip/
4. https://ipc2u.com/articles/knowledge-base/detailed-description-of-the-modbus-tcp-protocol-with-command-examples/

