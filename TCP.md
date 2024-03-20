# TCP (Transmission Control Protocol)

TCP is a core protocol of the Internet Protocol Suite, providing reliable, ordered, and error-checked delivery of a stream of bytes between applications running on hosts communicating over an IP network.

## TCP Flags
One of the key mechanisms in TCP's functionality is the use of flags in the TCP header, which signal the sender's intentions to the receiver and manage the state of the connection. Here are the main TCP flags explained in detail:

- SYN (Synchronize)
  - **Purpose**: Used to initiate a connection between two hosts.
  - **Function**: The SYN flag is set in the initial packet sent from the client to the server. It is used to synchronize sequence numbers between devices at the start of a TCP session.
- ACK (Acknowledgment)
  - **Purpose**: Confirms receipt of packets.
  - **Function**: The ACK flag is set in packets sent to acknowledge the successful receipt of packets. After the initial SYN packet, all the following TCP packets have the ACK flag set, as TCP is a bidirectional communication protocol.
- FIN (Finish)
  - **Purpose**: Used to gracefully close a connection.
  - **Function**: The FIN flag indicates the end of data transmission from the sender, allowing the session to be terminated gracefully. It signals that the sender has finished sending data.
- RST (Reset)
  - **Purpose**: Abruptly terminates a connection or indicates a problem.
  - **Function**: The RST flag is used to reset the connection. It can be used if a party wants to abort the connection due to an error or if a packet is received unexpectedly on a non-existent session.
- PSH (Push)
  - **Purpose**: Instructs the receiving host to push the data to the application immediately.
  - **Function**: The PSH flag is used to tell the receiver to process these packets as they are received instead of buffering them, ensuring that data is sent to the receiving application without delay.
- URG (Urgent)
  - **Purpose**: Indicates that the data contained in the packet should be processed urgently.
  - **Function**: The URG flag is used along with the urgent pointer field in the TCP header to indicate that this packet contains urgent data. The receiver is informed that certain data within the packet should be prioritized.

**How TCP Flags Are Used**
- **Connection Establishment**: A TCP connection is established using the three-way handshake process, involving SYN and ACK flags. Initially, the client sends a packet with SYN, the server responds with SYN-ACK, and the client sends ACK back to confirm.
- **Data Transmission**: During the session, PSH and URG flags can be used to manage how data is processed, ensuring efficiency and responsiveness in communication.
- **Connection Termination**: To gracefully end a session, FIN flags are used. A connection can be terminated using a four-way handshake (FIN, ACK, FIN, ACK). If a session needs to be terminated unexpectedly, the RST flag is used.
- These flags are critical for the management of TCP connections, enabling the protocol to provide reliable, ordered, and error-checked delivery of data.

## TCP Threeway Handshake
The TCP three-way handshake is a fundamental process used to establish a TCP connection between two hosts on a network. It involves three steps, designed to synchronize the sequence numbers of both the client and the server, and to acknowledge the synchronization. This process ensures that both ends are ready for data transmission and agree on initial sequence numbers, which are crucial for maintaining the order of the packets sent over the connection.

- Step 1: SYN
  - **Initiation by the Client**: The client initiates the connection by sending a TCP segment with the SYN (synchronize) flag set. This packet includes the initial sequence number (ISN) chosen by the client, which is a randomly generated value. The sequence number is used to keep track of the bytes in the stream of data that the TCP connection will send.
  - **Purpose**: This step is meant to inform the server that the client wants to establish a connection and to share the client's initial sequence number.
- Step 2: SYN-ACK
  - **Response by the Server**: Upon receiving the SYN packet, the server responds with its own TCP segment, which has both the SYN and ACK (acknowledgment) flags set. This serves two purposes:
  - **Acknowledgment of the Client's SYN**: The ACK flag indicates that the server acknowledges the client's initial sequence number. The acknowledgment number sent back to the client is the client's ISN plus one.
  - **Server's Initial Sequence Number**: The SYN flag indicates that this packet also serves as the server's way of communicating its own initial sequence number to the client. This number is also randomly chosen by the server.
  - **Purpose**: This step confirms the server's readiness to establish the connection and shares its own sequence number, while acknowledging the client's initial sequence number.
- Step 3: ACK
  - **Final Acknowledgment by the Client**: The client sends a TCP segment with the ACK flag set. This acknowledges the receipt of the server's SYN packet. The acknowledgment number is the server's initial sequence number plus one.
  - **Establishment of the Connection**: With this step, the handshake is complete. Both the client and the server have acknowledged each other's initial sequence numbers, and the TCP connection is established. From this point on, data can be sent over the connection.
  - **Purpose**: This final step completes the negotiation, ensuring both parties are synchronized and ready to begin data transmission.

- Significance of the Three-Way Handshake
  - **Reliability and Sequence Integrity**: The handshake ensures that both parties agree on the sequence numbers, which is crucial for the reliable transmission of packets. TCP relies on sequence numbers to order packets correctly and to manage data retransmission.
  - **Connection Establishment**: It verifies that both the client and the server are ready to communicate and have the resources available for managing the connection.
  - **Prevention of Old Duplicate Connection Initialization**: The use of random initial sequence numbers and the handshake process help prevent the accidental initiation of connections based on delayed, duplicate, or old packets from previous connections.

The three-way handshake is a cornerstone of TCP's reliability and ordered data transmission capabilities, allowing for robust, bidirectional communication between hosts on a network.
