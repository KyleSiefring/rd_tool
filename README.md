rd_tool
=======

rd_tool.py is a script for collecting rate-distortion curves across a series of either local or Amazon AWS nodes.

This script is run by arewecompressedyet.com, but can also be run locally to produce the same data.

Dependencies
============

You will need Python 3.4 or later, as well as [boto3](https://github.com/boto/boto3).

On Ubuntu, install with:
```
sudo apt install python3-tornado python3-boto3
```

Node dependencies
=================

Individual build machines do not need Python, but do need bash. Each machine
should be configured with a user and work_root (such as that user's home
directory). This work directory must be populated with a folder called
daalatool, which needs to contain a checkout of Daala git with the tools built:

```
sudo apt install autoconf libogg-dev libjpeg-dev libpng-dev check
```

```
git clone https://git.xiph.org/daala.git daalatool
cd daalatool
./autogen.sh
./configure --disable-player
make tools -j4
```

rd_tool will automatically create one slot directory per core, and upload
codec binaries into that directory.

Using AWS nodes
===============

You will need a ~/.aws configuration for boto with your AWS information and credentials.

Specify the autoscaling groukp to use with -awsgroup.

Using local nodes
=================

You can specify all of the machines you want to use in a JSON file:

```json
[
  {
    "host": "localhost",
    "user": "thomas",
    "cores": 4,
    "port": 22,
    "work_root": "/home/thomas/tmp",
    "media_path": "/home/thomas/sets"
  },
  {
    ...
  }
]

```

Specify this configuration on the command line with -machineconf.

Builds
======

Specify the path to the git checkout of thetested codec with the -bindir
argument. rd_tool.py expects to find prebuilt binaries of the codec - it will
not build the codec automatically.

Output files
============

Output files are written into the current directory. The format of the .out
files is one quantizer per line, of the format

<quantizer> <number of pixels> <file size in bytes> <metric1> <metric2> ...

See RDRun.reduce() in work.py for an up-to-date list of metrics.
