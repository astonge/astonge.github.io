+++
title = "Wireshark Fun With Python and Lua Part 2"
date = "2025-10-10T23:09:45-07:00"
draft = true
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = true
+++
<iframe data-testid="embed-iframe" style="border-radius:12px" src="https://open.spotify.com/embed/track/3kKVqFF4pv4EXeQe428zl2?utm_source=generator" width="100%" height="352" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>

[Go here for part 1](https://a570.bearblog.dev/wireshark-fun-with-python-and-lua-part-1/)

### wireshark
- explain how wireshark displays data and what we want to accomplish
- small blurb about display filters

### dissector

```
local asto_protocol = Proto("astoproto", "ASTO Protocol")

local  f_sender = ProtoField.string("astoproto.sender", "Sender")
local  f_recver = ProtoField.string("astoproto.recver", "Recver")
local  f_message = ProtoField.string("astoproto.message", "Message")

asto_protocol.fields = { f_sender, f_recver, f_message }

function asto_protocol.dissector(buffer, pinfo, tree)
    pinfo.cols.protocol:set("ASTO-PROTO")
    local subtree = tree:add(asto_protocol, buffer(0))

    local sender_value = buffer(0, 16):string()
    subtree:add(f_sender, buffer(0, 16))
    
    local recver_value = buffer(16, 16):string()
    subtree:add(f_recver, buffer(16,16))

    local message_value = buffer(32, 24):string()
    subtree:add(f_message, buffer(32,24))
end

local udp_port = DissectorTable.get("udp.port")
udp_port:add(1234, asto_protocol)
```
- explain

### testing
- send data
- display data

### summary
- blah blah

[Code is on github](https://github.com/astonge/astoproto-dissector)
