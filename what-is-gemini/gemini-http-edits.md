#### Special case: HTTP POSTing data

Our request defined `accepted-encodings`, one of which was `gzip`. HTTP specifies that a client and server can "negotiate" a preferred encoding for content data. POST data can be large, so oftentimes we want to compress it.

```sh
brain@minipop:~$ echo "GET /hello_world.html HTTP/2" | gzip | tee request.gz | od -A n -t x1
 1f 8b 08 00 00 00 00 00 00 03 73 77 0d 51 d0 cf
 48 cd c9 c9 8f 2f cf 2f ca 49 d1 cb 28 c9 cd 51
 f0 08 09 09 d0 37 e2 02 00 04 48 79 4f 1d 00 00
 00
```

Notice that it's smaller - or, shit, uh, _deep breaths, no one is looking at you_, calm down - namaste.

---

#### What was that?

Nothing. Welcome to intermission. Inteerrrrmissssiiooooonnn.

<br></br>
<br></br>

Sweet, sweet bliss.

---

#### Binary: The Internet Language

Yeah, so, some algorithms include _error checking_, via a _check value_ (or [_cyclic redundancy check_](https://en.wikipedia.org/wiki/Cyclic_redundancy_check#Application)). Gzip is one such algorithm, including a 32-bit (or 4-byte) CRC.

Without going into it - those extra bytes we saw after compressing the text were the check value bytes, information stored

---

#### Binary: The Internet Language

What does a `.gz` file look like, I wonder?

```sh
$ cat request.gz
|brequest.txt]Qk@+^Ye"S؛\x=_VBoh]3f`iO>]j'tl:aoGQO_Iöh7945-t$gX<Z>޵Ãmy/XwC=7kKKV&~I+><u8?t:$~$^@tFx
```

Egads!! iT'S a BiNArY FiLE!!! But wait, we know what this is!

```sh
brain@minipop:~$ cat request.gz | od -A n -t x1
 1f 8b 08 08 fe 7c 15 62 00 03 72 65 71 75 65 73 74 2e 74 78 74 00 5d 90 51 6b c2 40 0c 80 df fb
 ... skip 256 bytes...
 34 b0 88 69 01 00 00
```

Neat - we've compressed our request, and we've learned that

---

#### How does an HTTP client send a request?

UDP came first, and is a "fire-and-forget" protocol. Bytes are batched into packets, and we hope packets reach their destination.

UDP is lightweight; it is used in applications like video streaming, video games, and voice chat.

Applications using UDP are built to deal with missed packets - you might miss a few frames of your twitch streamer, or @mockra might get pwned by a 12 year old in League Of Legends (and blame it on lag).

---

#### How does an HTTP client send a request?

TCP came later, and is significantly more complex. This complexity makes it slower, but guarantees data is _entirely_ transmitted; no packets are lost.

It is "connection-oriented", the client must send "handshake" packets to the server, and the server must respond. Once a connection is established, data packets can be transmitted.

TCP is error-checked; the protocol defines a methodology to check if a bit has flipped, or been lost, in a packet. This makes TCP _accurate_.

---
