# Programmable Low-End Networks

## Newer version of the GitHub repo

For the newer version of the project related to the paper accepted for publication in IFIP/IEEE NOMS 2025 (to appear) [1], please visit https://github.com/ComputerNetworks-UFRGS/Programmable_Low_End_Networks.

[1] V. B. Alves, M. Basso, L. B. Ramos, J. Guillemot, A. Riker, A. Abelém, L. Gaspary, M. F. Zhani, J. Galan-Jimenez, J. A. Wickboldt, and W. Cordeiro, "Towards programmable low-end networking: Research challenges and lessons learned," in 2025 IEEE/IFIP Network Operations and Management Symposium (NOMS 2025). IEEE/IFIP, 2025.

## Introduction

This is the repository with the source code for the paper "Programmable Low-End Networks: Powering Internet Connectivity for the Other Three Billion", published in the IFIP/IEEE International Symposium on Integrated Network Management (IM 2021) [2]. In this project, we propose the following exploratory research question: can we boost low-end networking through forwarding plane programmability? The implications of a positive answer to this question are manifold, unlocking low-end networks for innovative use cases, such as powering affordable internet access to remote communities and helping bridge the digital divide. As a first step towards answering this question, we introduce and discuss in our paper a conceptual architecture for programmable low-end networking. In summary, we take advantage of Low Power Wide Area Network (LPWAN) transceivers and use a reconfigurable pipeline of match+action stages to enable researchers and practitioners to write lightweight networking protocols that can seamlessly bring isolated communities to the Internet. We then exercise our architecture through the design and implementation of a Lightweight Tunnel Protocol, for optimized data communication over narrow-band links, with a throughput gain as high as 23%. Our results mainly provide evidence of programmable low-end networks' potentialities, helping us close the digital divide and bring affordable Internet for the other three billion.

[2] A. Scheibe, W. Reichert, L. Gaspary, and W. Cordeiro, "Programmable low-end networks: Powering internet connectivity for the other three billion," in 2021 IFIP/IEEE International Symposium on Integrated Network Management (IM), 2021, pp. 187–195.

## Running Experiments

### Pre-requisites

To run these experiments, you will need mininet, bmv2, p4c-bm2-ss, and protobuf. You can have a fully prepared environment to run these experiments if you use the following commits:

```
BMV2_COMMIT="b447ac4c0cfd83e5e72a3cc6120251c1e91128ab"  # August 10, 2019
PI_COMMIT="41358da0ff32c94fa13179b9cee0ab597c9ccbcc"    # August 10, 2019
P4C_COMMIT="69e132d0d663e3408d740aaf8ed534ecefc88810"   # August 10, 2019
PROTOBUF_COMMIT="v3.2.0"
GRPC_COMMIT="v1.3.2"
```

You may use the user-bootstrap.sh script available in the vm/ dir of the repo https://github.com/p4lang/tutorials/ . Make sure you use in this scripts the commits shown above.

### Topology

This is the topology for the experiment:

```
h1 ----+                            +---- h4
       |                            |
h2 ----+---- s1 ---- s4 ---- s2 ----+---- h5 
       |             |              |
h3 ----+             |              +---- h6
                     s3
                     |
                     |
                +----+----+
                |    |    |
                |    |    |
               h7    h8   h9
```

### Running the Experiment

The commands below assume you are running the experiment as root. You might need to check some permissions if you wish to run these experiments as a ordinary user.

Open a terminal window. We will refer to this terminal window as terminal 0. To prepare the environment to run an experiment, enter directory ltp-proto/ 

```
(terminal 0)# cd ltp-proto/
```

Run script run-me.sh

```
(terminal 0)# ./run-me.sh
```

At this point, terminal 0 must be showing a mininet prompt.

Now, before you proceed, you must start three terminal windows, make sure you are in the ltp-proto/ dir of the project home (ProgrammableLowEndNetworks). In each window, you must run the controller for each switch that is part of the topology:

```
(terminal 1)# ./start_s1_controler.sh

(terminal 2)# ./start_s2_controler.sh

(terminal 3)# ./start_s3_controler.sh
```

After that, you are ready to run the experiments in the first terminal window, which now must be showing the mininet prompt:

```
(terminal 0) mininet>
```

To know the nodes enabled in the experiment, issue command "nodes". Likewise, to know the links enabled in the experiment, issue command "links". To know the network configurations used in the experiment, issue command "net". See the example below (we are running these commands in terminal 0):

```
mininet> nodes
available nodes are: 
h1 h2 h3 h4 h5 h6 h7 h8 h9 s1 s2 s3 s4
mininet> links
h1-eth0<->s1-eth2 (OK OK) 
h2-eth0<->s1-eth3 (OK OK) 
h3-eth0<->s1-eth4 (OK OK) 
h4-eth0<->s2-eth2 (OK OK) 
h5-eth0<->s2-eth3 (OK OK) 
h6-eth0<->s2-eth4 (OK OK) 
h7-eth0<->s3-eth2 (OK OK) 
h8-eth0<->s3-eth3 (OK OK) 
h9-eth0<->s3-eth4 (OK OK) 
s1-eth1<->s4-eth1 (OK OK) 
s2-eth1<->s4-eth2 (OK OK) 
s3-eth1<->s4-eth3 (OK OK) 
mininet> net
h1 h1-eth0:s1-eth2
h2 h2-eth0:s1-eth3
h3 h3-eth0:s1-eth4
h4 h4-eth0:s2-eth2
h5 h5-eth0:s2-eth3
h6 h6-eth0:s2-eth4
h7 h7-eth0:s3-eth2
h8 h8-eth0:s3-eth3
h9 h9-eth0:s3-eth4
s1 lo:  s1-eth1:s4-eth1 s1-eth2:h1-eth0 s1-eth3:h2-eth0 s1-eth4:h3-eth0 enp0s3: 
s2 lo:  s2-eth1:s4-eth2 s2-eth2:h4-eth0 s2-eth3:h5-eth0 s2-eth4:h6-eth0
s3 lo:  s3-eth1:s4-eth3 s3-eth2:h7-eth0 s3-eth3:h8-eth0 s3-eth4:h9-eth0
s4 lo:  s4-eth1:s1-eth1 s4-eth2:s2-eth1 s4-eth3:s3-eth1
mininet> 
```

You may run ping commands between hosts easily with the following commands. You will need to allow some time for the tables to configure (this is the reason why you will see some packet loss).

```
mininet> h1 ping h4
PING 10.0.2.1 (10.0.2.1) 56(84) bytes of data.
64 bytes from 10.0.2.1: icmp_seq=3 ttl=128 time=7.67 ms
64 bytes from 10.0.2.1: icmp_seq=4 ttl=128 time=9.38 ms
64 bytes from 10.0.2.1: icmp_seq=5 ttl=128 time=4.34 ms
64 bytes from 10.0.2.1: icmp_seq=6 ttl=128 time=5.63 ms
^C
--- 10.0.2.1 ping statistics ---
6 packets transmitted, 4 received, 33% packet loss, time 5023ms
rtt min/avg/max/mdev = 4.344/6.760/9.384/1.925 ms
mininet> 
```

NOTE: You can only run ping commands between nodes that are not connected to the same switch. For example, a ping command between h1 and h2 will not work. This is because switch s1 is not configured with the table rules to make traffic flow between nodes connected to a same switch.

### Running iperf3 within the experiment

Here, we are going to test the links using iperf3 tool. Before you proceed, you must have iperf3 installed.

```
(terminal 0)# apt-get install iperf3
```

Also, you need to make sure that the switch tables are configured between the hosts that will exchange the iperf3 flow. To this end, simply issue a ping command between the nodes that you will use for iperf3. For example:

```
mininet> h1 ping h4
```

At this point, run script ./run-me.sh and after that run the three controllers. Once you are in the mininet prompt, you must open a terminal window for each host you want to experiment with iperf3. Assuming we are taking h1 and h4:

```
mininet> xterm h1 h4
```

Now, in xterm h1, run the server:

```
(Node: h1)# iperf3 -s
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
```

Now, in xterm h4, run the client, that will send a flow of UDP packets to the server, and report every one second the statistics:

```
(Node: h4)# iperf3 -c 10.0.1.1 -u -i 1 (10.0.1.1 is the ip address of h1)
Connecting to host 10.0.1.1, port 5201
[ 36] local 10.0.2.1 port 49960 connected to 10.0.1.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Total Datagrams
[ 36]   0.00-1.00   sec   120 KBytes   983 Kbits/sec  15  
[ 36]   1.00-2.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   2.00-3.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   3.00-4.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   4.00-5.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   5.00-6.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   6.00-7.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   7.00-8.01   sec   128 KBytes  1.04 Mbits/sec  16  
[ 36]   8.01-9.00   sec   128 KBytes  1.05 Mbits/sec  16  
[ 36]   9.00-10.00  sec   128 KBytes  1.05 Mbits/sec  16  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Jitter    Lost/Total Datagrams
[ 36]   0.00-10.00  sec  1.24 MBytes  1.04 Mbits/sec  0.000 ms  0/0 (-nan%)  
[ 36] Sent 0 datagrams

iperf Done.
```

### Finishing the Experiments

To kill the experiment, simply type CTRL+C or CTRL+D in the mininet prompt.
