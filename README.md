# Polyglottal Network S2S Protocol

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](http://tools.ietf.org/html/rfc2119).

Servers connect over the same TCP channel that a client connects to.

## Connection Process
1. Server 1 opens a TCP connection to Server 2
2. Server 1 sends a `SERVERCONNECT` with it’s hostname and the connecting password.
3. Server 2 replies back with RPL_SERVERCONNSYNAWK with server 2’s information if the provided hostname & password from Server 1 match local database. 
4. Server 1 will then respond with a RPL_SERVERCONNSUCCESS if the provided hostname and password match their local database
5. At this point the two servers have been connected into a network. 
6. Server 2 MAY send the required C2S packets to Server 1 needed to cause Server 1 to match the state of Server 2. These packets MUST include the tag `sync` 

## Packet Flow
The follow C2S packets MUST be forwarded across the network: NICK, USER, PRIVMSG, MODE (only on mutation of state), PART, QUIT, NOTICE, TOPIC (only on mutation of state).
All S2S packets MUST have the tags `server` and `id`.

#### Example
`@server=irc.hammy.network;id=F3430AE2 :hammy@client.hammy.network PRIVMSG #general Henlo`
`@server=irc.lefty.one;id=89C0B7C :mozzy@client.lefty.one JOIN #general`


## TAGS
### sync
The `sync` tag indicates a packet was solely sent with the job of syncing the newly connected server and MUST NOT be forwarded along the network
### server
The `server` tag shows the hostname of the originating server
### id
The `id` tag is 8 random hexadecimal characters (32 bits). It is attached to all S2S messages. After receiving an S2S message a server MUST check to see if they have received a message with the same `id` tag within the last minute, if so the server MUST drop the packet and not relay it. When assigning the `id` tag a server MUST check to make sure it has not seen the `id` within the last 2 minutes of operation.

## Commands

### SERVERCONNECT <hostname> <password> 
The SERVERCONNECT command is used during the connection between 2 servers on the network. It is sent directly after the TCP socket opens and is sent from the connecting party.
Replies to this command 

## Numerics

### 850 - RPL_SERVERCONNAWK
```
<hostname> <password>
```
This is sent in response to a `SERVERCONNECT` in which the provided hostname & password match the local server’s database. 

### 851 - ERR_SERVERCONN
This is sent in response to a `SERVERCONNECT` in which either the hostname or password is incorrect

### 852 - RPL_SERVERCONNSUCCESS
This is sent in response to a `RPL_SERVERCONNAWK` in which the prvoided hostname & password match the local server’s database.
