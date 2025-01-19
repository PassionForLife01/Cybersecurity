# E. Wireshark Basics
## Introduction

Wireshark is an open-source, cross-platform analyzer tool capable of sniffing and investigating live traffic and inspecting packet captures (PCAP).

## Tool Overview
### Use Cases
- Detecting and troubleshooting network problems, such as network load failure points and congestion.
- Detecting security anomalies, such as rogue hosts, abnormal port usage, and suspicious traffic.
- Investigating and learning protocol details, such as response codes and payload data.

Note: Wireshark is not an Intrusion Detection System (IDS). It just reads, cannot change.

It works at user's side. All packets stand on the user's side.

### GUI and Data

|                                   |                                                                                                                                                                                                                                      |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Toolbar**                       | The main toolbar contains multiple menus and shortcuts for packet sniffing and processing, including filtering, sorting, summarising, exporting and merging.                                                                         |
| **Display Filter Bar**            | The main query and filtering section.                                                                                                                                                                                                |
| **Recent Files**                  | List of the recently investigated files. You can recall listed files with a double-click.                                                                                                                                            |
| **Capture Filter and Interfaces** | Capture filters and available sniffing points (network interfaces).  The network interface is the connection point between a computer and a network. The software connection (e.g., lo, eth0 and ens33) enables networking hardware. |
| **Status Bar**                    | Tool status, profile and numeric packet information.                                                                                                                                                                                 |

![[Pasted image 20250105091340.png]]

### Loading PCAP Files
- Recent files
- Double-click
- Tool bar > File > Open


![[Pasted image 20250105092127.png]]

|   |   |
|---|---|
|Packet List Pane|Summary of each packet (source and destination addresses, protocol, and packet info). You can click on the list to choose a packet for further investigation. Once you select a packet, the details will appear in the other panels.|
|Packet Details Pane|Detailed protocol breakdown of the selected packet.|
|Packet Bytes Pane|Hex and decoded ASCII representation of the selected packet. It highlights the packet field depending on the clicked section in the details pane.|

### Coloring Packets

- Permanent:
View -> Coloring Rules

![[Pasted image 20250105093153.png]]

### Traffic Sniffing

Blue button - Start
Red button  - Stop
Green button - Restart

![[Pasted image 20250105094205.png]]

### Merge PCAP Files

Merge two PCAPs into one file. Notice it will create a temporary file.

![[Pasted image 20250105094447.png]]
![[Pasted image 20250105094501.png]]

### View File Details

- File hash
- capture time
- capture file comments,
- interface
- statistics

![[Pasted image 20250105095251.png]]

Or **"`pcap` icon located on the left bottom"**

Statics > Capture File Properties contain the packet comment and file comment.

## Packet Dissection

Each time you click a detail, it will highlight the corresponding part in the packet bytes pane.

- The Frame (Layer 1)
	arrival time, physical connection
- Source \[MAC\] (Layer 2)
- Source \[IP\] (Layer 3) 
	TTL 
- Protocols (Layer 4)
	TCP payload size (`TCP Segment Len`)
- Protocols errors (TCP)
- Application Protocol (Layer 5)
- Application Data 

HTTP: e-tag, using the memory of user's browser.

## Packet Navigation
### Packet Numbers

Wireshark assigns a unique number for each packet.

### Go to Packet

Thanks to the packet numbers, we can easily go to a specific packet by a unique number.

![[Pasted image 20250105102523.png]]

### Find Packets

Edit > Find Packets

- Input type
	String and regex searches are the most commonly used search types.
- Search field
	Packet details
	Packet list
	Packet bytes

![[Pasted image 20250105103420.png]]

### Mark Packets

marked packets will turn to black regardless what color it is before.

marked packets will be lost after closing the capture file.

![[Pasted image 20250105103652.png]]

### Packet Comments

Unlike packet marking, the comments can stay within the capture file until the operator removes them.

![[Pasted image 20250105103855.png]]

### Export Packets

This functionality helps analysts share the only suspicious packages

![[Pasted image 20250105104531.png]]

### Export Object (Files)

Save the content of the packet, such as HTTP.

![[Pasted image 20250105105116.png]]

### Time Display Format

By default, Wireshark shows the time in "Seconds Since Beginning of Capture", the common usage is using the UTC Time Display Format for a better view.

View > Time Display Format > UTC Date

![[Pasted image 20250105105419.png]]

![[Pasted image 20250105105439.png]]

### Expert Info

Just advices from Wireshark, maybe wrong.

| **Severity** | **Colour** | **Info**                                                 |
| ------------ | ---------- | -------------------------------------------------------- |
| **Chat**     | **Blue**   | Information on usual workflow.                           |
| **Note**     | **Cyan**   | Notable events like application error codes.             |
| **Warn**     | **Yellow** | Warnings like unusual error codes or problem statements. |
| **Error**    | **Red**    | Problems like malformed packets.                         |

Frequently encountered information groups are listed in the table below.

| **Group**    | **Info**                  | **Group**      | **Info**                    |
| ------------ | ------------------------- | -------------- | --------------------------- |
| **Checksum** | Checksum errors.          | **Deprecated** | Deprecated protocol usage.  |
| **Comment**  | Packet comment detection. | **Malformed**  | Malformed packet detection. |

Usage:
- **"lower left bottom section"**
- **"Analyze --> Expert Information"**

![[Pasted image 20250105154542.png]]

### Tricks

To see the packet comment:
- right-click, and select the packet comment, scroll down if the content is too long.
- Statics > Capture File Properties > Packet Comments or File Comments

To get the get the MD5 hash value of file:
- right click the file > "Digest" tab > Select checkbox > "Hash" button

![[Pasted image 20250105155137.png]]

## Packet Filtering
### Intro
Two types of filtering approaches:
- Capture filters
	Capture only the packets valid for the user filter.
- Display filters
	View only the packets valid for the user filter.

We focus on the Display Filters.
### Apply as Filter

right-click menu.

![[Pasted image 20250105160642.png]]

Notice you can perform `and`, `or` logic operation.

### Conversation Filter

Group by ==the same packet in some extent==, such as `TCP` for same IP address and same TCP ports.

![[Pasted image 20250105161250.png]]

### Colorize Conversation

No different from conversation filter, just change the color based on the conversation filter.

![[Pasted image 20250105161947.png]]

Usage:
- Right-click menu > Colorize Conversation > TCP > Color ..
- View > Colorize Conversation > Color ..
	The packets that have the same features with the selected packet will be colorized.

Undo:
- View > Colorize Conversation > Reset Colorization

### Prepare as Filter

Similar to Apply as Filter, it will create the query sentence automatically, but needs an `Enter` to confirm the query, that's "Prepare" comes in.

![[Pasted image 20250105163540.png]]

### Apply as Column

Create a column to get the specific information.

Usage:
Select a section.
- Analyze > Apply as Column
- Right click > Apply as Column

Remove:
Right click that menu > Remove this Column

![[Pasted image 20250105164226.png]]

### Follow Stream

The packets are chaotic, but we know some of these packets are related in some extent, such as HTTP request and reply.
So we can "follow the stream" to see what happened in a data stream. Notice that TCP will create a stateful connection, that is easy to follow the stream.

Usage:
- right-click menu > "Follow" > "... Stream"
- "Analyze > "Follow" > "... Stream"
Notice the stream you can follow depends on what type of packet you are selecting.

The packets originating from the server are highlighted with blue, and those originating from the client are highlighted with red.

![[Pasted image 20250105165730.png]]

Use the "X button" to remove the display filter.

### Tricks

You can apply the `HTTP` in packet details pane as filter.

