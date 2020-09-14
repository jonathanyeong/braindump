# High Performance Browser Networking

## Chapter 1

- 4 types of delays when we talk about latency.
  - Propogation delay - distance over speed.
  - Transimission delay - packets rate and data rate of link.
  - Processing delay - Process packets.
  - Queuing delay - Time waiting for process.
- We want our applications to respond within hundreds of milliseconds.
- Last mile latencies between ISPs are a big factor of speed. The last mile is when all the connections to your home is made, which results in a lot of cables and boxes -> i.e. lots of hops.
- Latency not bandwidth is the performance bottleneck for most websites.
- Increasing bandwidth is easier than increasing latency.

## Chapter 2
- IP - Internet Protocol, provides the host-to-host routing and addressing.
- TCP - Transmission Control Protocol, provides the abstraction of a reliable network running over an unreliable channel.
- TCP is organised for accurate delivery not timely delivery.
- HTTP can be delivered using other protocols (like UDP), but in practice it's mostly TCP.

### TCP
- All TCP connections begin with a three way handshake. The keywords are:
  - SYN - client picks a random sequence number X and sends a SYN packet.
  - SYN ACK - Server increments X by 1, and responds with a random sequence number Y.
  - ACK - Client increments X and Y by 1 and completes the handshake.
- After the three way handshake, the client can send a data packet after the ACK packet. And the server must wait for an ACK packet before it can dispatch data.
- This carries an important implication for performance:

> each new connection will have a full roundtrip of latency before any application data can be transferred.

- New connections are expensive, which is why we want to re-use connections as much as possible.
- **TCP Fast open** - mechanism that aims to eliminate latency penalty on new connections by allowing data transfer within the SYN packet.
- Due to congestion collapse mechanisms were put in place to govern rate of data sent in both directions.
  - Flow control - **I'm up to flow control**
  - Congestion control
  - Congestion avoidance