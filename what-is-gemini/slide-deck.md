---
theme: gaia
_class: lead
paginate: true
backgroundColor: #f5f5f5
---

# **Grokking the Web**


### Through the Looking Glass with Gemini Protocol

---

#### What do we mean when we say "website"?

Websites are HyperText Markup Language (HTML) pages, served over HyperText Transport Protocol (HTTP).

Let's make a web page together!

Create a file at at /index.html with contents:
```sh
$ sudo bash -c 'echo "<h1> Hello, World! </h1>" > /index.html'
```

---

#### How do we view websites?

To view a web page, we need a client - a browser - that understands how to _render_ HTML, according to the HTML specifications.

You probably have one installed - think Mosaic, Netscape Navigator, Opera.

Browse to `file:///index.html`, and voila - you're on the web, baby!

---

#### But wait, that's not the web!

Okay, fine, you got me. It's not - but it's like, half of the web. That `file://` is the important bit that's different. 

We're saying "Hey $browser, please retrieve web page `/index.html` from the file system." 

Our communication _protocol_ is different.

Functionally, it's _the same client experience_ as browsing the web.

---

#### HTTP: The hard part

So how do we get _remote_ web pages? Let's use `http://` and browse to https://bry-guy.net/hello_world.html!

Press `F12` and look at devtools/network:

|Status|Method|Domain|File|Type|Transferred|
---|---|---|---|---|---|
|200|GET|bry-guy.net|hello_world.html|html|865 B| 

Status, Method, Domain, File, Type - each of these are _defined_ by the HTTP protocol!

--- 

#### HTTP request

Click on that request, peek at `headers`, and view the request headers:

```txt
GET /hello_world.html HTTP/2
Host: bry-guy.net
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
```

HTTP requests are ASCII text. Requests define a method (`GET`), target (`/hello_world.html`) and version (`HTTP/2`), and variable headers. `accepted-encoding` defines `POST` content data encoding.

---

#### HTTP response 

HTTP responses are much the same.

```txt
HTTP/2 200 OK
date: Tue, 22 Feb 2022 23:52:11 GMT
content-type: text/html
strict-transport-security: max-age=63072000; includeSubdomains
x-frame-options: DENY
x-content-type-options: nosniff
last-modified: Sun, 20 Feb 2022 05:16:35 GMT
server: cloudflare
content-encoding: br
```

The client reads `content-type` to understand this is HTML content, and `content-encoding` to understand what format the bytes are in.

---

#### How does a web browser view a website?

[Textual ASCII maps directly to bytes](https://www.asciitable.com/). Let's convert `GET /hello_world.html HTTP/2` to bytes using `od` (octal dump):

```sh
brain@minipop:~$ echo "GET /hello_world.html HTTP/2" | od -A n -t x1
 47 45 54 20 2f 68 65 6c 6c 6f 5f 77 6f 72 6c 64
 2e 68 74 6d 6c 20 48 54 54 50 2f 32 0a`
```

Each of those numbers represents a byte, in hexadecimal. ASCII maps bytes to characters, such as `47` to `G`.

The neat thing about hexadecimal, is that it can also be expressed in _binary_ - the language of computers. `47` hex is `01000111` binary.

---

#### How does a web browser receive view a website?

Bytes - binary encoding - are the encoding of The Internet! 

There is a difference between the World Wide Web (HTML/HTTP), and The Internet. 

Internet technology is much older, and at a [lower level](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_layer). 

The most common Internet protcols are [Transmission Control Protocol (TCP)](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) and [User Datagram Protocol (UDP)](https://en.wikipedia.org/wiki/User_Datagram_Protocol). 

HTTP runs on the more-resilient-but-slower TCP protocol.

---

#### How does a web browser receive view a website?

A browser uses TCP to establish a connection with a destination web server - e.g., Apache or Nginx, applications that "speak" HTTP.

The browser crafts and sends the HTTP request over TCP.

The web server interprets the HTTP request, selects the `hello_world.html` file and sets `content-type` to `text/html`. It encodes the content according to the request's `accept-encoding`. The payload is sent back via the same TCP connection.

The client recives the HTTP response with the HTML payload and decodes, interprets, and renders it to screen.

---

#### So what's your point?

My point is, even after a cursory examination, the web is a pretty complex system. It's hard to fully understand.

There's a templating language (HTML) that a client must interpret and render - and later-added support for N other types of content.

There's an extremely complex application protocol (HTTP) with many permutations of methods, status codes, headers, and versions.

---

#### So what's your point?

Even the most voracious readers will balk at casually reading [the HTTP/1.1 spec](https://www.w3.org/Protocols/rfc2616/rfc2616.html) - and HTTP/2 is even more complex.

Even the [HTML2 spec](https://www.ietf.org/rfc/rfc1866.txt) is huge - and we're on _HTML5_ now!

There's myriad other web topics; CSS, TLS, XHTML... many more than I can fathom.

---

#### So what's your point?

Understanding the web is hard - there's a large surface area of topics to cover. 

It's not essential or useful to have protocol-designer expertise. 

<br></br>

However, some intuition would go a _long_ way!

---

#### Introducing Gemini Protocol

Gemini Protocol is a new application-over-internet protocol, created in 2019. It is nascent, simple, and inspired by the web.

From [gemini://gemini.circumlunar.space](https://gemini.flounder.online/)*:

```
Gemini is a new internet protocol which:
   - Is heavier than gopher
   - Is lighter than the web
   - Will not replace either
   - Strives for maximum power to weight ratio
   - Takes user privacy very seriously
```

*[gemini.circumlunar.space](gemini://gemini.circumlunar.space) is Gemini's "home", and is currently down.

---

#### Heavier than Gopher?

Gemini is not the first application-over-internet protocol other than the Web. [Gopher is yet another internet protocol](https://en.wikipedia.org/wiki/Gopher_(protocol)#Origins), consisting of a series of hierarchical, hyperlinkable, text-only pages, over TCP - much like the Web.

Another famous communications protocol, later adapted to the internet, is [Usenet](https://en.wikipedia.org/wiki/Usenet).

There are many, _many_ more internet protocols in existence, and in use, that you cannot access from your web browser - because it only browses the web!

---

#### Lighter than the web?

Gemini is intended to be a simple protocol - like Gopher - while still leveraging some of the best lessons of the Web. From the [FAQ](gemini://gemini.flounder.online/docs/faq.gmi):

```txt
Gemini strives for simplicity of client implementation. 
Modern web browsers are so complicated that they can only 
be developed by very large and expensive projects. 

Somebody who had no part in designing the protocol should 
be able to hold the entire protocol spec in their head.

A basic but usable client should fit comfortably within 50 
or so lines of code in a modern high-level language.

A client which implements every single protocol feature 
should be a feasible weekend programming project.
```

---

#### More ethos

Privacy is designed in from the beginning:

```
Gemini is designed with an acute awareness that the modern web is a privacy disaster, 
and that the internet is not a safe place for plaintext... 

This concern manifests as a deliberate non-extensibility in many parts of the Gemini protocol.
```

Gemini's simplicitly means it eschews some ubiquitous web features:

```
Gemini has no support for caching, compression, or resumption of interrupted downloads. 
As such, it's not very well suited to distributing large files, for values of "large" 
which depend upon the speed and reliability of your network connection.
```

---

#### A Specification You Can Grok

It's hard to understate how much simpler Gemini is than the Web. 

Here, [read the protocol specification](gemini://gemini.flounder.online/docs/specification.gmi) yourself! 

While you're at it, consider checking out the [informal gemtext specification](gemini://gemini.flounder.online/docs/gemtext.gmi). Gemtext is Gemini's HTML.

From a writing perspective, [Gemtext is _much_ simpler](gemini://gemini.flounder.online/docs/cheatsheet.gmi) than HTML _or_ Markdown.

---

#### Quick Example of Simplicity

A Gemini request is a single CRLF-terminated line with a UTF-8 URL:

```
<URL><CR><LF>
```

Similarly, a Gemini response is a single CRLF-terminated header line, followed by a response body. It is lead by a two-digit status code, and optional metadata:

```
<STATUS><SPACE><META><CR><LF><BODY>
```

You can only issue a single request type - equivalent to GET - and there are only 6 required STATUS codes.


---

#### This is Not An Ad

The reason I came here to talk to you about the Web, and about Gemini, is not to convince you is better or worse than the other.

It's to commiserate. I mean honestly, do we even understand what we're doing most days? The Web is _old_, and _complex_, and trying to learn about it has felt like an insurmountable burden.

Discovering Gemini showed me that I, too, can understand how ~~blogs~~ gemlogs on The Internet magically appear in front of our eyes.

De-mystify the magic. Learn Gemini, so you can grok the Web.


---

#### How to Browse Gemini

Write your own client!

But if you must be lazy, [there are a variety of browsers](https://geminiquickst.art/) available for every major platform, and Windows.

**Buyer Beware**!

Browsing Gemini may introduce you to content you may not as easily find on the web. Most of this will be weird, some will be interesting, and some amount may be hateful.

I have not encountered any myself, but it is an unmoderated space.

---

#### Thanks for coming to my ~~TED~~ Gem Talk

I look forward to seeing your own clients, servers, and capsules!


<br></br>

Thanks!






