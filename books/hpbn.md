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
  - Flow control
  - Congestion control
  - Congestion avoidance

#### Flow Control
- Mechanism to prevent the sender from overwhelming the receiver with data it may not be able to process.
- A receive window (rwnd) on each side of the TCP connection communicates size of the available buffer.
- Doesn't solve the problem of congestion collapse by itself.

#### Congestion control
- Slow start
  - Flow control solves a sender ovewhelming the receiver. But no mechanism to prevent each side overwhelming the underlying network.
  - Congestion window size (cwnd) - Sender-side limit on the amount of data the sender can have in flight before receiving an acknowledgment (ACK) from the client.
  - Private variable
  - Data is min(rwnd, cwnd)
  - Slow start comes in because we want to grow the window size over time. I.e. we start slow and grow as packets are ack'd.
  - The congestion window is doubled every round trip. Ensuring we do not use the full capacity of the link immediately.
  - The fact that the client and server can transmit Mbps+ data rates has no effect when a new connection is established.
  - Limits the available bandwidth which has an adverse affect on performance of small transfers.
  - Restarts aren't great - if possible re-use an existing TCP connection.
  - Increasing window size from 10 segments is an easy way  to improve performance.

#### Congestion avoidance
- TCP uses packet loss as feedback,
- Congestion avoidance takes over if a packet is lost.
- If a packet is lost, we will adjust our window to prevent more packets from being lost.
- Proportional Rate Reduction (PRR) is the algorithm used for congestion avoidance.

#### Bandwidth Delay Product
- Product of data link’s capacity and its end-to-end delay. The result is the maximum amount of unacknowledged data that can be in flight at any point in time.
- Window sizes should be just big enough, such that either side can continue sending data until an ACK arrives back from the client for an earlier packet.
- No gaps, maximum throughput.
- The optimal window size depends on round trip time.

#### Head of Line blocking.
- Happens because TCP sends packets in order. If one packet is lost all subsequent packets must be held in the receivers TCP buffer until the lost packet is retransmitted and arrives at the receiver.
- This causes something commonly known as jitter - unpredictable latency variation in the packet arrival times.
- Applications that can deal with out of order deliver, or are jitter sensitive should use something like UDP. WebRTC uses UDP.

- The core principles of TCP
  - TCP three-way handshake introduces a full roundtrip of latency.
  - TCP slow-start is applied to every new connection.
  - TCP flow and congestion control regulate throughput of all connections.
  - TCP throughput is regulated by current congestion window size.

## Chapter 3
