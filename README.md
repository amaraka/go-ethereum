## LivePeer

Livepeer is a decentralized live streaming broadcast platform. This
repo is a proof-of-concept spike built on top of Swarm and 
[go-ethereum](https://github.com/ethereum/go-ethereum) developed by
the [Ethereum Foundation](http://ethereum.org).

Building and running this node allows you to:

* Create a local Livepeer Network, or join the existing Livepeer POC
network.
* Broadcast a live stream into the network.
* Request that your stream be transcoded into multiple formats.
* Consume a live stream from the network.

For full documentation and a project overview, check out the
[Livepeer Documentation](https://github.com/livepeer/wiki/wiki)

## Installation

The Livepeer POC requires ffmpeg. On OSX:

`brew install ffmpeg --with-ffplay`

`git clone https://github.com/livepeer/go-ethereum.git`  
`cd go-ethereum`  
`go install ./cmd/geth`  
`go install ./cmd/livepeer`

## Setup

If you already have an ethereum node running, you can create a new
node account, and launch Livepeer with the following commands.

`geth --datadir ./<your-geth-data-directory> account new`

Copy the output account address, perhpas into an environment variable $BZZKEY

`livepeer --bzzaccount $BZZKEY --datadir <your-geth-data-directory> --ethapi $DATADIR/geth.ipc`

By default this should connect you to the Livepeer POC network. For
detailed instructions, refer to the following section.

### Detailed setup including running a private network

Since this spike runs on top of Swarm and an Ethereum node, you'll
need to do a little ethereum setup to get a node running and
initialize an account. Follow the instructions in the
[go-ethereum README](http://github.com/ethereum/go-ethereum) for
running a private network.

## Usage

To run the node with video streaming, use the --rtmp flag when starting the livepeer node.  For example:

`livepeer --bzzaccount $BZZKEY --datadir $DATADIR --ethapi $DATADIR/geth.ipc --bzznetworkid 412 --rtmp 1935`

To run a second node, so that you can test streaming to one another,
specify a new `--port`, `--rtmp`, `--bzzport`, `--datadir`, and `--bzzaccount` argument:

`livepeer --bzzaccount $BZZKEY2 --datadir $DATADIR2 --ethapi $DATADIR/geth.ipc --verbosity 4 --maxpeers 3 --port 30402 --bzznetworkid 412 --rtmp 1936`

Now that you have two nodes running, make sure they are talking to
each other, then stream into one node and play from the other.  You
can stream a saved video using `ffmpeg`, For example:

`ffmpeg -re -i bunny.mp4 -c copy -f flv rtmp://localhost:1935/movie`

This will output the `streamID` to the console of node running on port
`1935` (the default). Copy the `streamID`. And you can play from the
second node (running on RTMP port 1936) using the livepeer command.

`livepeer stream --rtmp 1936 <streamID>`

To start a true livestream instead of playing a pre-recorded video, visit our web client or use a broadcasting
platform such as OBS, and point the output at `rtmp://localhost:1935/movie`

### Transcoding

To enable transcoding, add `?transcode=true` in your RTMP uplink video.  For ease of testing, start SRS manually.  (So you can
see the output).  To start srs, create a `./objs` directory, and run `./bin/srs -c srs.conf`

## Metrics and monitoring

To look at a list of metrics, use the --metrics flag when starting swarm, and use `geth monitor` to monitor metrics.  For example:  

`livepeer --bzzaccount $BZZKEY --datadir $DATADIR --ethapi $DATADIR/geth.ipc --verbosity 4 --maxpeers 3 --port 30402 --bzznetworkid 412 --rtmp 1935 --metrics`

`geth monitor --attach ipc:/Users/erictang/Sandbox/swarmdata1/bzzd.ipc
livepeer/test livepeer/chunks/`

It is also possible to run a network visualization server which will
let you view the current state of your network for a given
streamID. See the documentation at the
[streamingviz repository](https://github.com/livepeer/streamingviz). To
start a livepeer node that reports to the visualization server, add
the `--viz` flag:


`livepeer --bzzaccount $BZZKEY --datadir $DATADIR --ethapi $DATADIR/geth.ipc --bzznetworkid 412 --rtmp 1935 --viz`

## Additional functionality available through Swarm and Ethereum

Since this spike is built on top of Swarm and Ethereum, all the
available functionality is exposed through this project. Please see
the documentation at [the go-ethereum repository](http://github.com/ethereum/go-ethereum)

### License from Go-Ethereum

The go-ethereum library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html), also
included in our repository in the `COPYING.LESSER` file.

The go-ethereum binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html), also included
in our repository in the `COPYING` file.
