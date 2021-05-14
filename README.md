# UDP-websockets-with-pythonAll work should be submitted to a Git repo and the URL submitted to Blackboard.
Work should be demonstrated, and you should include tests to demostrate this, with details in
the README how to run them and so on.
Similar to previous tasks there is a server running the remote development machine that transmits
and receives UDP packets.
Note the packets are just UDP there is no IP or transport information included in the packets.
The following URL is used to talk to the server:
uri = "ws://localhost:5612"
Unlike previous worksheets you will need to not only receive messages from the server, but also decode
them as UDP. For example, simply connecting to the UDP server, waiting for a response, will
yield the following data:
b 'CgAqACEAyztXZWxjb21lIHRvIElvVCBVRFAgU2VydmVy'
The prefix b' implies that the following literal is expressed in bytes instead of a string. They may
contain non ASCII characters, which values above 127 must be escaped, i.e. a \ must preceed
them.
As noted above UDP packets are Base 64 encoded and before they can be
processed must be decoded.
It is straight forward to decode Base 64 encoded byte streams in Python using the base64 package:
import base64
Decoding the above message gives:
b '\n\x00 *\x00 !\x00\xcb ;Welcome to IoT UDP Server'
The above sequence of bytes are a UDP packet, devided as follows:
• First 8 bytes defined the header:
– \n\x00*\x00\x19\x00\xcb\x90
• The remaining bytes define the payload, which in this case is:
– Welcome to IoT UDP Server\x00
Individual or sequences of bytes can be accessed using Python’s slice notation:
• packet[start:stop] # items start through stop-1
• packet[start:] # items start through the rest of the array
• packet[:stop] # items from the beginning through stop-1
• packet[:] # a copy of the whole array
where start and stop are offsets into the bytes sequence.
As defined above each field in the header is 2 bytes long:
• Source port identifies the port number of the source and is defined as bytes 0 and 1.
• Destination Port identifies the port of the destined packet and is defined as bytes 2 and 3.
• Length is the length of the UDP packet, including the payload, and is defined as bytes 4 and 5.
• Checksum is the 16-bit one’s complement of the one’s complement sum of the UDP header, and
is defined as bytes 6 and 7.
To read a field from the header you need to access the bytes for that field and convert to an integer
value, specifing the endianness. For example, the following reads the length field from the byte stream
packet:
length = int.from_bytes(packet[ 4 :6 ], 'little')
Which in the case of the above packet is 25.
The remaining header fields can be decoded similarly to give:
Source Port: 10
Dest Port: 42
Data Length: 25
Checksum: 37067
The payload itself can be any kind of binary data and is application dependent. In this case we can
safely assume it is a string encoded as UTF-8, and thus we just need to convert it:
payload = packet= [ 8:(size +8 )].decode( "utf-8" )
At this point you should have a reasonable idea how a UDP packet can be decoded, putting aside
calulation of the checksum.
