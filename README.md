# RELIABLE MULTICAST
Reliable UDP Multicast using a separate TCP ack channel per subscriber.

# LOCAL BUILD

```shell
make
```

You can also create debian packages

```shell
make debian
```

# BUILDING WITH DOCKER CONTAINER
   
Install docker on your Linux box as described in the official 
[Docker documentation](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

1. Check out the docker generic build repo

    ```shell
    git clone git@github.com:magnusfeuer/docker-generic-build.git
    ```
    

2. Create a docker image to build reliable multicast

    ```shell
    cd docker-generic-build/docker-build
    ./build-docker-image.sh ${RMC_SOURCE_DIR}/rmc-build.dockerfile 
    ```
    Replace `${RMC_SOURCE_DIR}` with the directory that this `README.md` file is in.


3. Launch the docker container to build reliable multicast

   ```shell
   cd ${RMC_SOURCE_DIR}
   ${DOCKER_DIR}/build.sh . make
   ${DOCKER_DIR}/build.sh . make debian
   ```

    Replace `${RMC_SOURCE_DIR}` with the directory that this `README.md` file is in.  
    Replace `${DOCKER_DIR}` with the directory that the docker generic build repo was checked out to.


# TESTING [NEEDS UPDATING!]

Please note that a wireshark dissector plugin is available.

```shell
make wireshark
```

## Test program usage

```shell
./rmc_test -?
```
## Send single signal between publisher and subscriber
Start a publisher that:

1. Emit periodic announce packets to potential subscribrers
2. Waits for one subscriber to connect
3. Sends a single packet (in a single multicast packet)
4. Exits

Window 1:

    ./rmc_test -c 1


Start subscriber that:

1. Waits for an announce packet from a publisher
2. Connects to publisher and sets up subscribtion
3. Receives however many packets that the publisher has to send
4. Validates all packets
5. Extits

Window 2:

    ./rmc_test -S

**NOTE: There is a bug in the system that will prohibit a subscriber
from finding a publisher if the two processes are started immediately
after each other. Make sure that there is a 200 msec delay between
starting the processes.**

## Send a million signals between one publisher and one subscriber

Bandwidth will be dependent on the interface that the multicast is bound to. WiFi is slower than gbit Ethernet.
In this example we will use the localhost loopback interface for speed.

    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -c 1000000
    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -S



## Send a million signals from two publishers to a single subscriber.

The ```-i``` argument sets up node id to distinguish betwen two publishers.<br>
The ```-e``` argument lists all publishers that the subscriber is to expect announce packets from.<br>

    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -S -e1 -e2
    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -c 1000000 -i1
    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -c 1000000 -i2

## Send a million signals from one publishers to two subscribers

    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -S
    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -S
    ./rmc_test -m 127.0.0.1 -l 127.0.0.1 -c 1000000
