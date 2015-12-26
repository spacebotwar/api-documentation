---
layout: article
title: Start
---

{% include section_title.html title="Web Sockets" %}

SpaceBotWar predominately uses Web Socket technology. This offers significant
advantages over HTTP requests, such as AJAX calls using HTTP.

  * The overhead for each request is much smaller. An AJAX call could easily 
have 1200 bytes of header for a simple 8 bytes of data. For the same amount
of data a Web Socket header is 8 bytes. This makes Web Sockets faster.
  * It is asynchronous and full-duplex. As well as the client being able to
push data or request data from the server at any time, the server can also push
data to the client when it changes. As a consequence the client does not have
to resort to frequent polling or similar techniques.
  * By only supporting Web Sockets the server code can be further simplified
making it much faster and cheaper. We can then scale out horizontally,
providing more power at a cheaper cost.

The downside is that client code needs to be asynchronous, but this is common
for Javascript and we can provide libraries for other languages such as Perl.



{% include section_title.html title="Message Format" %}

Messages both to, and from, the Server are in JSON encoded strings. For
example the following represents a client request for a new client-code.

{% highlight JSON %}
{
  "route" :     "/client-code",
  "content" : {
      "msg_id" :    "123"
  }
}
{% endhighlight %}

This message is from the CLIENT to the server. The **route** can be thought of
as the address (rather like a URL) to the specific routine that will handle
the request. The **content** is the main body of the message.

These will be documented in this API as follows.

  * We will show a **Title** for the message.
  * It will indicate if the message is sent from the CLIENT or the SERVER.
  * The **route** for the API will also be shown in the title bar.
  * We will just show the **content** part of the JSON payload, to show everything would be redundant.

As an example, the following section describes this particular call.



{% include section_header.html title="Client Code" method="CLIENT" url="/client-code" %}

The client either requests a new client-code, or asks for an existing code to
be validated.

{% highlight JSON %}
{
  "msg_id" :        "123",
  "client_code" :   "1b4e28ba-2fa1-11d2-883f-0016d3cca427"
}
{% endhighlight %}

The **msg_id** and the **client_code** are the most common attributes of a message
so we will describe them here.



{% include section_title.html title="Message Identifier" %}

In AJAX it is normal for a request to the server to respond immediately with
the requested data. With Web Sockets the response is simply a confirmation that
the request was received. The actual requested data will be sent some time later
in an asynchronous message from the Server.

So that the client can match up it's request with the Server response, it may
optionally send a **msg_id** (message Identifier). The server will use this ID
when it responds.

The simplest implementation is for the Client to increment this number on each
request although this choice is arbitrary.

If the msg_id is not supplied the server will not include a msg_id in it's response.



{% include section_title.html title="Client Code" %}

A Client Code is used to identify a client to the server. A Client Code is initially 
provided by the server and once given it should continue to be used by that
particula client, even if you log out and back in again. This enables the 
server to retain your settings.

Each browser (Internet Explorer, Chrome, Safari etc.) should have it's own unique
Client Code. Different computers should have their own Client Code and any script
that you may write should have it's own Client Code. You should not share your 
code otherwise you may get data corruption.

A session is associated with a Client Code and it may 'time-out' (after a few 
hours) but even so, you should still keep the same Client Code for subsequent 
sessions.

In most of the following API calls the **client_code** argument is mandatory.
If you do not supply the code the call will be rejected.



{% include section_title.html title="Making a Connection" %}

Here is an example connection to a Web Socket (in Perl)

{% highlight perl %}
use AnyEvent::WebSocket::Client;
my $client = AnyEvent::WebSocket::Client->new;
my $connection;
$client->connect("ws://spacebotwar.com/ws")->cb(sub {
   ...
{% endhighlight %}

And an example in Javascript

{% highlight javascript %}
var ws = new WebSocket("ws://spacebotwar.com/ws");
ws.onopen = function() {
  alert("Connection is open")
}

{% endhighlight %}

On making a successful connection the server will send a Welcome message as follows.



{% include section_header.html method="SERVER" url="/" title="Welcome" %}

When a client makes a Web Socket connection, the server will send a message indicating the
current status of the room. It may also send an update whenever the server status changes.

{% highlight JSON %}
{
  "code"          : 0,
  "message"       : "Welcome to the Space Bot War game server.",
  "data"          : "server",
}
{% endhighlight %}

code
----

The numeric code representing the status of the **server** where **0** represents success
and any other value indicates a fault.

message
-------

A human readable message, for example a message to the effect that the server is off-line.

data
----

Supplimentary data, for example the time at which the server is due back on line.

