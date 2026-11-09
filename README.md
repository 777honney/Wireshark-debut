# Transport and Data Security

## Context
This project is part of a lab on transport and data security during my cybersecurity lectures (in my previous Bachelor). The goal was to analyze a network capture file (`twitter.pcap`) using **Wireshark** in order to identify a **security vulnerability**. That was like a ctf, the whole class as to search for it. That was the first step unfortunattely I couldn't find the rest.

## Methodology
The `twitter.pcap` file was opened in Wireshark to inspect the captured network traffic. Packet analysis revealed an HTTP request sent to `twitter.com`:

GET /statuses/replies.xml HTTP/1.1
Host: twitter.com
User-Agent: CFNetwork/330
Authorization: Basic dXNlcnRlc3Q6cGFzc3dvcmQ=
Connection: keep-alive

<img width="440" height="265" alt="image" src="https://github.com/user-attachments/assets/6ce28ed7-8e35-4a47-9d40-6a1d48f707c3" />


## Vulnerability identified
The request uses the **HTTP** protocol (unencrypted) with **HTTP Basic Authentication**. The `Authorization: Basic` header contains the credentials encoded in **Base64**, which is a reversible encoding, not an encryption method.

Decoding the string `dXNlcnRlc3Q6cGFzc3dvcmQ=` directly reveals the username/password pair in plain text.

**➡️ Vulnerability: login credentials travel in clear text over the network.** Anyone capturing this traffic (shared Wi-Fi, a compromised router, a man-in-the-middle attack) can instantly decode this information without needing to break any encryption, since Base64 provides no protection — it's just a representation encoding.

<img width="435" height="188" alt="image" src="https://github.com/user-attachments/assets/8e3399ba-5f07-4ae6-be92-366865447006" />

## Why this is dangerous
- HTTP transmits all data (headers, cookies, credentials) without encryption.
- A passive attacker on the network can retrieve the credentials with minimal effort.

## Recommendation
Use **HTTPS (TLS)** for any request containing credentials or sensitive data. With TLS, header content (including `Authorization`) is encrypted end-to-end and becomes unreadable to an attacker intercepting the traffic.

## Tools used
- [Wireshark](https://www.wireshark.org/) — network traffic capture and analysis
- Base64 decoding to inspect the `Authorization` header content
