---
layout: blog
title: Version 0.5.0 released
---

A bit later than planned, Node-RED 0.5.0 is now available to [download](https://github.com/node-red/node-red/archive/0.5.0.zip) or [npm install](https://npmjs.org/package/node-red). Please read the [upgrade instructions](http://nodered.org/docs/getting-started/upgrading.html).

### What's new

#### Undeployed changes
One piece of feedback we've had is that simply changing the colour of the deploy button isn't good enough to indicate there are undeployed changes; for colour-blind users, it doesn't work at all.

To improve this, we've added a new badge to nodes that have been added or edited but not yet deployed.

![Changed node badge](/blog/content/images/2013/Dec/Selection_001.png)

#### Unknown nodes
When importing a flow from somewhere else, if it contains a node you don't have installed, we now substitute the missing node with a special unknown-node type rather then refuse to perform the import. This means you can import the flow, see what you're missing and either install the missing node types and re-attempt the import, or delete the missing nodes.

To make it very obvious you have unknown nodes, they take a very distinctive style:

![Unknown node type](/blog/content/images/2013/Dec/Selection_002.png)

#### Command-line arguments
With the previous release, we added the ability to move all of the user data to a location outside of the Node-RED install directory. This was configured in the `settings.js` file, which presented a problem - this file had to exist in the install directory so we could find it.

With this release, you can now point to the file as a command-line argument:

    $ node red.js --settings /home/nol/.node-red/settings.js

You can use either `--settings` or `-s`.

### Node updates
#### Twitter node
The twitter node can now track the tweets of specific users as well as the direct messages of the authenticated user. Both of these features use the polling api, rather than the more efficient streaming API of the other search types. This means they are subject to the fairly strict rate-limiting that twitter imposes. They will check for new tweets roughly every minute and new DM's every two minutes.

If you have multiple nodes using these features, all authenticated as the same user, you are likely to hit the rate limiting and not get all the tweets you expect.

#### TCP node
Before this release, the TCP nodes had no sense of individual connections; the TCP Out node would broadcast messages to all connections. With this release, the nodes are now session aware. Given a flow that starts with a TCP In node and ends with a TCP Out node, the Out node can now be configured to reply just to the connection that triggers the flow.

Here's a flow that acts as an Echo server:

![A TCP Echo server](/blog/content/images/2013/Dec/Selection_004.png)

    [{"id":"6534336a.9acbcc","type":"tcp in","server":"server","host":"","port":"9001","datamode":"stream","datatype":"buffer","newline":"","topic":"","name":"","base64":false,"x":80,"y":140,"z":"e1c9f85b.1e3608","wires":[["2f63c46b.d09c3c"]]},{"id":"c5d83ebc.3a27c","type":"tcp out","host":"","port":"","beserver":"reply","base64":false,"name":"","x":320,"y":140,"z":"e1c9f85b.1e3608","wires":[]},{"id":"2f63c46b.d09c3c","type":"function","name":"","func":"msg.payload = \"You said: \"+msg.payload.toString();

return msg;","outputs":1,"x":200,"y":140,"z":"e1c9f85b.1e3608","wires":[["c5d83ebc.3a27c"]]}]

With this flow deployed, you can telnet to `localhost:9001` and anything you type will be echoed back:

    $ telnet localhost 9001
    Trying 127.0.0.1...
    Connected to localhost.
    Escape character is '^]'.
    Hi There
    You said: Hi There

#### WebSocket node
It is now much easier to create live dashboards of events generated by Node-RED. The WebSocket nodes allow you to send and receive messages from within a web-page. As with the TCP nodes, they are connection-aware. This means you can easily create a flow that starts with a message over a WebSocket connection, performs some query and then returns the result over the same connection.

#### Other core node updates
- MQTT node now supports specifying the client id, username and password to use for a connection.
- Switch node now has an 'otherwise' option as well as an option to stop checking for matches once one has been found. This allows tradition `if-else` style logic flows to be constructed.
- Serial node allowed you to specify a delimiter to split incoming data with. It now has an option to automatically append the delimiter to messages being sent back out
- HTTP Request node will now automatically follow 301 redirects


#### New Nodes
The [node-red-nodes](https://github.com/node-red/node-red-nodes) repository has accepted some more pull-requests for new nodes:

- Snapchat node
- Phillips Hue node

### What's next?
npm-installable nodes have been on the to-do list for a while now; it's probably time I started working on that.

Another issue to tackle is a better separation of the admin UI from the runtime. Currently, you can use `httpRoot` to move where the admin UI is served from as well as apply basic authentication. An unfortunate side effect is that if you secure the admin UI, you end up also securing the HTTP In nodes - probably not the desired result. We need to separate these out, but the challenge is doing this in a backwardly compatible way.

There are also a few [pull-requests](https://github.com/node-red/node-red-nodes/pulls) for new nodes working their way through the system.


