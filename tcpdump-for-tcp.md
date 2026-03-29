## Capturing TCP Packets with Host & Flag Filters

- Capture All TCP Packets from/to a Host

```shell
tcpdump -i eth0 tcp and host 192.168.1.10 -w host_tcp.pcap
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/filter-tcpandhost.png)

- Capture Only SYN Packets from a Host

```shell
tcpdump -i eth0 'tcp[tcpflags] == tcp-syn and host 192.168.1.10' -w syn_only.pcap
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/filter-tcp-flagwithhost.png)

- Capture SYN+ACK Packets **from** the Host

```shell
tcpdump -i eth0 'tcp[tcpflags] == (tcp-syn|tcp-ack) and src host 192.168.1.10' -w synack_from_host.pcap
```

- Capture Packets with Any of These Flags

```shell
tcpdump -i eth0 'tcp[tcpflags] & (tcp-syn|tcp-ack|tcp-fin) != 0 and host 192.168.1.10' -w flags_combo.pcap
```

### Reading the Captured PCAP with Filters

- Filter by Host during Read

```shell
tcpdump -r flags_combo.pcap host 192.168.1.10
```

- Filter by TCP SYN Flag

```shell
tcpdump -r flags_combo.pcap 'tcp[tcpflags] == tcp-syn' -nn
```

- Filter by Host and SYN Flag

```shell
tcpdump -r flags_combo.pcap 'tcp[tcpflags] == tcp-syn and host 192.168.1.10'
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/tcp-flagfilter.png)

- Verbose, No DNS/Port Resolution, and Time

```
tcpdump -r flags_combo.pcap -n -tttt -v
```

### Pro Tips

- Combine filters while capturing **and** reading for precise analysis.
- Always use `-s 0` during capture to avoid truncating packet payloads.
- Use `-nn` to prevent DNS/port lookups when you're focused on raw IPs/ports.

#### Capture TCP for **Multiple Hosts & Multiple Flags**

- Basic Format

```shell
tcpdump -i <interface> 'tcp and (host <IP1> or host <IP2>) and (tcpflags filter)' -w <output>.pcap
```

- Capture from **192.168.1.10 or 10.0.0.5** with **SYN or FIN**

```shell
tcpdump -i eth0 'tcp and (host 192.168.1.10 or host 10.0.0.5) and (tcp[tcpflags] & (tcp-syn|tcp-fin) != 0)' -w syn_fin_hosts.pcap
```

- Capture from 3 Hosts with SYN+ACK Only

```shell
tcpdump -i eth0 'tcp and (host 192.168.1.10 or host 10.0.0.5 or host 172.16.0.9) and (tcp[tcpflags] == (tcp-syn|tcp-ack))' -w synack_3hosts.pcap
```

- Capture Any of 2 IPs Sending RST or FIN Packets

```shell
tcpdump -i eth0 'tcp and (src host 192.168.1.20 or src host 10.0.0.30) and (tcp[tcpflags] & (tcp-rst|tcp-fin) != 0)' -w reset_fin.pcap
```

#### TCP Flag Reference (Quick)

| **Flag** | **Name**    | **Filter**                     |
| -------- | ----------- | ------------------------------ |
| SYN      | Sync        | `tcp[tcpflags] & tcp-syn != 0` |
| ACK      | Acknowledge | `tcp[tcpflags] & tcp-ack != 0` |
| FIN      | Finish      | `tcp[tcpflags] & tcp-fin != 0` |
| RST      | Reset       | `tcp[tcpflags] & tcp-rst != 0` |
| PSH      | Push        | `tcp[tcpflags] & tcp-psh != 0` |

#### Pro Tip: Group with Parentheses `()` Properly!

When combining `or` and `and`, **always group host and flag conditions separately** to avoid logical errors.

- **Capture Only Incomplete TCP Handshakes (SYN without ACK)**

```sh
tcpdump -i eth0 'tcp[tcpflags] == tcp-syn'
```
Great for detecting **SYN flood** attacks or connection drops.

- **Connection Resets**

```sh
tcpdump -i eth0 'tcp[tcpflags] & tcp-rst != 0'
```

- **TCP Packet Size Anomalies**

```sh
tcpdump -i eth0 'tcp and less 80'
```
Detect minimal packets like heartbeat pings, scanning, or stealthy tools.

- **IPv6 TCP Traffic**
```sh
tcpdump -i eth0 ip6 and tcp and host 2001:db8::1
```


##### TCP Flag Legend in `tcpdump`:

|Letter|TCP Flag|Description|
|---|---|---|
|`S`|SYN|Start of a TCP connection (synchronization)|
|`.`|ACK|Acknowledgment|
|`P`|PSH|Push — tells receiver to process data immediately|
|`F`|FIN|Finish — requests connection termination|
|`R`|RST|Reset — forces termination of a connection|
|`U`|URG|Urgent flag|
|`E`|ECE|Explicit Congestion Notification Echo|
|`W`|CWR|Congestion Window Reduced|

#### 🧩 Meaning of Combined Flags:

 `S`
- Only **SYN** set.
- Usually first step of **3-way handshake**.

`S.`
- **SYN + ACK**
- Step 2 of 3-way handshake (server replies to SYN).

`P.`
- **PSH + ACK**
- This packet has data, and the sender asks to push it immediately (often application data).

 `.`
- **ACK only**
- Acknowledgment of a previous packet.

 `F.`
- **FIN + ACK**
- Request to gracefully **terminate** the connection.

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/save-op-pcap-and-atsame-view.png)

```
Flags [S]      → SYN from client to initiate connection  
Flags [S.]     → SYN+ACK from server to accept connection  
Flags [.]      → ACK from client to complete handshake  
Flags [P.]     → Client sends data (PSH+ACK)  
Flags [.]      → Server acknowledges  
Flags [P.]     → Server sends data  
Flags [F.]     → Client starts connection close  
Flags [.]      → Server acknowledges  
```