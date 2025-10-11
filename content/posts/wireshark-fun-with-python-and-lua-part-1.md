+++
title = "Wireshark Fun With Python and Lua Part 1"
date = "2025-09-29T22:43:20-07:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
+++
<iframe data-testid="embed-iframe" style="border-radius:12px" src="https://open.spotify.com/embed/track/5aDYAqdhQXZwm3Y3mWj62U?utm_source=generator" width="100%" height="352" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>

## dissectors

I've always been curious how wireshark displays the protocol information that gets captured. A quick googling shows that's called a dissector.
Neat-o! \
Seems like there are two ways to make dissectors. Code them in C or script them in lua.

So I chose lua to create a simple dissector but the first thing that comes to mind as I'm setting it up.. what protocol should I dissect?\
There is a lot to protocols, a lot more than I wanted to dig into at the time. just want to dip my toes in as it were. so, I thought to myself, let's make my own protocol! \
\ 
What could go wrong???

![this is fine](https://i.imgur.com/hpz3eWS.jpeg)

## my own protocol
Need a quick and dirty poc? Pythons got your back baby boo.

```python
import socket
import struct

# send string data via UDP
def send_udp_data(s_to, s_from, s_message):
    # set UDP connection info
    UDP_IP="192.168.0.1"
    UDP_PORT=1234
    proto_format = f'>16s16s24s'

    # convert strings to bytes
    b_to = s_to.encode(encoding="utf-8")
    b_from = s_from.encode(encoding="utf-8")
    b_message = s_message.encode(encoding="utf-8")

    # create UDP socket
    sock = socket.socket(socket.AF_INET,socket.SOCK_DGRAM)

    # pack data using defined protocl format
    data = struct.pack(proto_format, b_to, b_from, b_message)

    # send data over UDP
    sock.sendto(data, (UDP_IP,UDP_PORT))

send_udp_data('Player 1', 'Player 2', 'Good luck, have fun!')
send_udp_data('Player 2', 'Player 1', 'lol gg')
print("Data Sent")

```

After a bit of thinking I figured the easiest approach would be to simulate a video game "chat system". I love first person shooters (quake, call of duty, etc.) and thinking back to my quake2 modding days I know UDP is used for sending game data.

So let's use that.

![udp joke](https://img-9gag-fun.9cache.com/photo/aqNXRp7_700bwp.webp)

I'm not going to deep dive into this code but I'll give a general overview:

### main section
- import 'socket' and 'struct' packages\
('struct' will help us "format" the protocol data)
- define a function for sending three strings over UDP
- cool. cool cool cool.

### send_udp_data function
- hardcode our network settings\
(IP and PORT to send data to)
- `proto_format`\
(this is passed to `struct.pack()` to format the data for sending over the network more on this in a bit)
- encode strings to bytes (convert data to binary! yay!)
- create a socket
- pack/format data as defined in `proto_format`
- finally, we send everything to the IP address and PORT we defined earlier

### proto_format

I'll explain this a bit further because otherwise it just looks like black magic, and I don't want to be labeled a witch!

.. and by explain I mean I'll copy/paste from the [struct package documentation](https://docs.python.org/3/library/struct.html) obviously..

> Format Strings
>
> Format strings describe the data layout when packing and unpacking data. They are built up from format characters, which specify the type of data being packed/unpacked. In addition, special characters control the byte order, size and alignment. Each format string consists of an optional prefix character which describes the overall properties of the data and one or more format characters which describe the actual data values and padding.

So... that really takes the fiddles out of prepping the data for transport. Obviously, I used big-endian. What are the special characters all about?

* The format string:
```>16s16s24s```

It can be read as: `big-endian` followed by three fields which are `16 byte` string .. followed by a `16 byte` string .. followed by a `24 byte` string.

At least that's the way I understand it but sometimes I have no idea what's going on..

![come on..](https://giffiles.alphacoders.com/361/36140.gif)

Again, [RTFM about struct](https://docs.python.org/3/library/struct.html#format-characters).. \
Looks like I can pack various types of data using different format strings:
- 's' == char array in C types
- 'c' == singular char in C types
- 'i' == int in C types
- 'f' for 'float' type
- 'd' for double

.. and so on. Pretty neat huh?

The first field is `16s` which translate to `16 byte string\char array`

Back to the 'video game chat system simulation'. A couple of messages between players are sent (again, this is just a simulation/poc). Using `<string>.encode()` to make sure the strings are encoded as bytes because the `struct.pack()` method only accepts data in bytes.

Here is what the `data` variable looks like before sending out over UDP:
```
b'Player 1\x00\x00\x00\x00\x00\x00\x00\x00Player 2\x00\x00\x00\x00\x00\x00\x00\x00Good luck, have fun!\x00\x00\x00\x00'
b'Player 2\x00\x00\x00\x00\x00\x00\x00\x00Player 1\x00\x00\x00\x00\x00\x00\x00\x00lol gg\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'
```
Notice how it adds padding (`\x00`) to fill out the data so it matches what was specified in the format string. \
`Player 1` is 8 characters long and its followed by eight `\x00`s which equals 16! `Player 2`.. same thing. \
The "message" field should be 24 bytes in size. \
"Good luck, have fun!" is 20 chars long followed by four `\x00`s!

:D *nice nice*.

Okay dokay.. that was a lot. The data is being encoded, formatted and sent over the wire. Now the other side needs to be worked on, I'll cover the actual wireshark dissector'ing in part 2

-- asto
