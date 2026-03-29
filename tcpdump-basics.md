## Basic tcpdump commands

-  To check `tcpdump` version.

```shell
tcpdump --version

OP:-

tcpdump version 4.99.5
libpcap version 1.10.5 (with TPACKET_V3)
OpenSSL 3.2.4 11 Feb 2025
64-bit build, 64-bit time_t
```


-  To see list of `interface`

```shell
tcpdump -D

op:-

1.ens160 [Up, Running, Connected]
2.any (Pseudo-device that captures on all interfaces) [Up, Running]
3.lo [Up, Running, Loopback]
4.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
5.usbmon3 (Raw USB traffic, bus number 3)
6.usbmon2 (Raw USB traffic, bus number 2)
7.usbmon1 (Raw USB traffic, bus number 1)
8.usbmon0 (Raw USB traffic, all USB buses) [none]
9.nflog (Linux netfilter log (NFLOG) interface) [none]
10.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
```

##### **Filtering Traffic**

 - Capture Specific Host Traffic

```shell
tcpdump host 192.168.1.1
```

-  Capture Based on Port

``` shell
tcpdump port 80             # Any traffic to or from port 80 
tcpdump src port 443        # Source port 443 only 
tcpdump dst port 22         # Destination port 22
```

- Protocol Filters

```shell
tcpdump tcp 
tcpdump udp 
tcpdump icmp
```

-  Combine Filters

```shell
tcpdump 'tcp and port 443 and src host 10.0.0.1'
```

- Want to filter `protocol`

```shell
sudo tcpdump -n -i ens160 icmp
```

Output:-

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/icmp-filter.png)

- Versbose output

```sh
sudo tcpdump -n -v -i ens160 icmp
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/icmp-verbose-op.png)

- Write the output in a file

```sh
sudo tcpdump -n -i ens160 icmp -w /file/path/icmp-tcpdump.pcap
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/write-op-in-file.png)

- Specify the number of packet to capture (Limit the packet)

```sh
sudo tcpdump -n -i ens160 icmp -c 10 -w icmp-tcpdump.pcap
```

```sh
sudo tcpdump -n -i ens160 icmp -c 10
```

- To read a pcap file using tcpdump

```sh
tcpdump -r icmp-tcpdump.pcap
```

- To read first 4 packet

```sh
tcpdump -r icmp-tcpdump.pcap -c 4
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/read-4pkts.png)

- Read with verbosity level

```sh
tcpdump -r icmp-tcpdump.pcap -X -vvv
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/read-verbose.png)

- Read the ethernet header

```sh
tcpdump -r icmp-tcpdump.pcap -e
```

- To capture traffic and save it to a file while simultaneously seeing the output in your terminal

```sh
sudo tcpdump -i ens160 tcp port 443 -s 0 -w - | tee https.pcap | tcpdump -r - -nn
```

![](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/snapshot/save-op-pcap-and-atsame-view.png)
