## Capturing UDP Packets

#### Basic UDP Capture

- Captures all UDP traffic on interface `eth0`.

```bash
tcpdump -i eth0 udp
```

**UDP Port Filtering**

- Captures DNS traffic (UDP port 53).

```bash
tcpdump -i eth0 udp port 53
```

- Captures NTP (Network Time Protocol) traffic (port 123).

```bash
tcpdump -i eth0 udp port 123
```

- Captures UDP traffic in the given port range.

```bash
tcpdump -i eth0 udp portrange 1000-2000
```

**UDP Source and Destination Filtering**

- Captures traffic coming **from** port 53 (usually server DNS responses).

```bash
tcpdump -i eth0 src port 53 and udp
```

- Captures traffic going **to** port 53 (usually DNS queries).

```bash
tcpdump -i eth0 dst port 53 and udp
```

- SNMP query from a specific host.

```bash
tcpdump -i eth0 src host 192.168.1.5 and dst port 161 and udp
```

**UDP with Host Filters**

- Captures all UDP traffic to/from host 192.168.1.10.

```bash
tcpdump -i eth0 udp and host 192.168.1.10
```

- Captures UDP packets **from** 192.168.1.10.

```bash
tcpdump -i eth0 udp and src host 192.168.1.10
```

- Captures packets **to** 8.8.8.8.

```bash
tcpdump -i eth0 udp and dst host 8.8.8.8
```

- Saving UDP PCAP to File

```bash
tcpdump -i eth0 udp port 53 -w dns_udp.pcap
```

- Saves DNS UDP traffic to `dns_udp.pcap`.

**To View UDP PCAPs**

- Reads the file and prints info without DNS/port resolution (`-n`), in verbose mode (`-v`).

```bash
tcpdump -r dns_udp.pcap -n -v
```

- Shows packet contents in both hex and ASCII.

```bash
tcpdump -r dns_udp.pcap -X
```

**Capture Size, Count, and Snaplength**

- Captures only 100 packets.

```bash
tcpdump -i eth0 udp -c 100 -w udp_sample.pcap
```

- Captures full packet content (`-s 0` sets max snap length).

```bash
tcpdump -i eth0 udp -s 0 -w full_udp.pcap
```

**Combined Filters**

- SNMP queries from a specific device.

```bash
tcpdump -i eth0 udp and port 161 and src host 192.168.1.10
```

- Captures DNS and NTP traffic excluding traffic from 10.0.0.1.

```bash
tcpdump -i eth0 '(udp port 53 or udp port 123) and not src host 10.0.0.1'
```

#### ***Useful Real-World Filters***

- **DHCP Traffic:**

```bash
tcpdump -i eth0 port 67 or port 68
```

- **mDNS (Bonjour/Apple Discovery):**

```bash
tcpdump -i eth0 udp port 5353
```

- **Syslog (UDP):**

```bash
tcpdump -i eth0 udp port 514
```

- **TFTP:**

```bash
tcpdump -i eth0 udp port 69
```