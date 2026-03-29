<div align='center'>

# `TCPDUMP`

</div>

We recently had a technical discussion regarding an issue I identified on a Linux server using `tcpdump`. During the conversation, I briefly explained how I approached and resolved the issue, and provided a high-level overview of the `TCP header` and the role of `tcpdump`.

One of my colleagues raised a valid question: *Is understanding* `tcpdump` and the `TCP header` truly necessary for a Network Engineer, especially when tools like Wireshark offer such a user-friendly interface for analysing packet captures?

That question got me thinking — why not share some insights on why `tcpdump` and a solid understanding of the `TCP header` are still essential tools in a network engineer’s toolbox?

### Real Use Case – Diagnosing a Connection Issue

Recently, I encountered a situation where clients were unable to establish HTTPS connections to a backend service. At first glance, many suspected the issue might be related to the firewall — a common assumption in such cases.

To dig deeper, I used `tcpdump` on the server side to inspect the traffic flow. What I observed was interesting: while the client was sending TCP SYN packets to initiate the connection, there was no SYN-ACK response from the server — meaning the TCP handshake wasn't completing.

This pointed to something deeper than a simple firewall block. Upon further analysis, I discovered that the application was running inside a containerised environment. The IP address assigned to the container happened to overlap with the client’s source IP, which caused the container to misinterpret the request.

Instead of replying to the SYN, the container attempted to resolve the IP via ARP (thinking the client IP was on the same local network). Since it never got a valid ARP reply, the connection failed silently.

Thanks to `tcpdump`, I was able to trace this subtle issue down to a network overlap between the container network and the external client. The fix involved isolating the container IP range to avoid conflicts — something that would have been much harder to spot without low-level packet inspection.

## How to Use *TCPDUMP* ?

Now that we understand why `tcpdump` is so important for network engineers, let's dive into how to use it effectively. `tcpdump` is a command-line tool, and while its interface may seem intimidating at first, once you learn a few key commands, you'll find that it's a powerful tool for troubleshooting network issues.

- [Basic Commands of TCPDUMP](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/tcpdump-basics.md)
- [TCPDUMP used for TCP](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/tcpdump-for-tcp.md)
- [TCPDUMP used for UDP](https://github.com/Diptiranjan9/Tcpdump-Notes/blob/main/tcpdump-for-udp.md)