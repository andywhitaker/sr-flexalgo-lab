# Segment Routing with EVPN and FlexAlgo Lab


## Deploying the Segment Routing with EVPN and FlexAlgo lab
We will deploy a multi-node lab with SROS routers running Segment Routing with EVPN and Flex Algo.

This is how the topology will look:
![topology](sr-flexalgo-lab.clab.drawio.svg)


In order to deploy the lab you will need to have a copy of the SROS 25.3.R2 image. This can be imported into docker using the following command:

``` bash
sudo docker load -i sros.tar.gz
```

To validate the image has been loaded you can run:
``` bash
docker image list
```
You should see the following:

``` text
REPOSITORY                          TAG            IMAGE ID       CREATED         SIZE
vrnetlab/nokia_sros                 25.3.R2        2bb661ed612e   6 weeks ago     971MB
```

Once the correct SROS image has been loaded you must ensure you have the license file called `sros25.lic` downloaded and loaded into the project folder.

With the image and license file correctly loaded, to deploy the lab type:

```bash
sudo clab deploy -c
```

The deployment will wait for the SR OS nodes to boot up.

At the end of the deployment, the following table will be displayed. Wait for the sros boot to be completed (see next section), before trying to login to sros.

```bash
╭─────────────┬────────────────────────────────────┬───────────┬───────────────────╮
│     Name    │             Kind/Image             │   State   │   IPv4/6 Address  │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ chi1        │ nokia_sros                         │ running   │ 172.20.20.3       │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::3 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ chi2        │ nokia_sros                         │ running   │ 172.20.20.9       │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::9 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-chi1 │ linux                              │ running   │ 172.20.20.6       │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::6 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-chi2 │ linux                              │ running   │ 172.20.20.10      │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::a │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-dal1 │ linux                              │ running   │ 172.20.20.12      │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::c │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-dal2 │ linux                              │ running   │ 172.20.20.5       │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::5 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-nyc1 │ linux                              │ running   │ 172.20.20.7       │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::7 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ client-nyc2 │ linux                              │ running   │ 172.20.20.2       │
│             │ ghcr.io/srl-labs/network-multitool │           │ 3fff:172:20:20::2 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ dal1        │ nokia_sros                         │ running   │ 172.20.20.11      │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::b │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ dal2        │ nokia_sros                         │ running   │ 172.20.20.4       │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::4 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ nyc1        │ nokia_sros                         │ running   │ 172.20.20.8       │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::8 │
├─────────────┼────────────────────────────────────┼───────────┼───────────────────┤
│ nyc2        │ nokia_sros                         │ running   │ 172.20.20.13      │
│             │ vrnetlab/nokia_sros:25.3.R2        │ (healthy) │ 3fff:172:20:20::d │
╰─────────────┴────────────────────────────────────┴───────────┴───────────────────╯
```

### Monitoring the boot process

To monitor the boot process of SR OS nodes, you can open a new terminal and run the following command:

```bash
sudo docker logs -f chi1
```

## Connecting to the nodes

To connect to an SROS node:

```bash
ssh admin@chi1
```
To connect to a linux client node:

``` bash
docker exec -it client-chi1 sh
```


## Verifying interface status

After the lab is deployed, let's start verifying the configuration.

Check the interface status on CHI1 (SROS):

``` text
show router interface
```

Expected output:

``` text
===============================================================================
Interface Table (Router: Base)
===============================================================================
Interface-Name                   Adm       Opr(v4/v6)  Mode    Port/SapId
   IP-Address                                                  PfxState
-------------------------------------------------------------------------------
To-CHI2                          Up        Up/Down     Network 1/1/c4/1
   10.21.22.21/24                                              n/a
To-DAL1                          Up        Up/Down     Network 1/1/c1/1
   10.11.21.21/24                                              n/a
To-NYC1                          Up        Up/Down     Network 1/1/c2/1
   10.21.31.21/24                                              n/a
system                           Up        Up/Down     Network system
   21.21.21.21/32                                              n/a
-------------------------------------------------------------------------------
Interfaces : 4
===============================================================================
```


## Verifying IS-IS

Check IS-IS status on CHI1 (SROS):

``` text
show router isis status
```

Expected output:

``` text
===============================================================================
Rtr Base ISIS Instance 0 Status
===============================================================================
ISIS Cfg System Id           : 0000.0000.0000
ISIS Oper System Id          : 0210.2102.1021
ISIS Cfg Router Id           : 0.0.0.0
ISIS Oper Router Id          : 21.21.21.21
ISIS Cfg IPv6 Router Id      : ::
ISIS Oper IPv6 Router Id     : ::
ASN                          : 0
Admin State                  : Up
Oper State                   : Up
Ipv4 Routing                 : Enabled
Ipv6 Routing                 : Disabled
<snip>
===============================================================================
```

Check IS-IS adjacency on CHI1 (SROS)

``` text
show router isis adjacency
```

Expected output:

``` text
===============================================================================
Rtr Base ISIS Instance 0 Adjacency
===============================================================================
System ID                Usage State Hold Interface                     MT-ID
-------------------------------------------------------------------------------
chi2-sr1                 L1L2  Up    20   To-CHI2                       0
dal1-sr1                 L1L2  Up    19   To-DAL1                       0
nyc1-sr1                 L1L2  Up    19   To-NYC1                       0
-------------------------------------------------------------------------------
Adjacencies : 3
===============================================================================
```

## Verifying Segment Routing

Check tunnel table on CHI1 (SROS):

``` text
show router tunnel-table
```

Expected output:

``` text
===============================================================================
IPv4 Tunnel Table (Router: Base)
===============================================================================
Destination           Owner     Encap TunnelId  Pref   Nexthop        Metric
   Color
-------------------------------------------------------------------------------
10.11.21.11/32        isis (0)  MPLS  524289    11     10.11.21.11    0
10.21.22.22/32        isis (0)  MPLS  524291    11     10.21.22.22    0
10.21.31.31/32        isis (0)  MPLS  524290    11     10.21.31.31    0
11.11.11.11/32        isis (0)  MPLS  524302    11     10.11.21.11    10
11.11.11.11/32        isis (0)  MPLS  524294    11     10.11.21.11    63
12.12.12.12/32        isis (0)  MPLS  524303    11     10.11.21.11    20
12.12.12.12/32        isis (0)  MPLS  524298    11     10.11.21.11    10063
22.22.22.22/32        isis (0)  MPLS  524296    11     10.21.22.22    10
22.22.22.22/32        isis (0)  MPLS  524293    11     10.21.22.22    63
31.31.31.31/32        isis (0)  MPLS  524295    11     10.21.31.31    10
31.31.31.31/32        isis (0)  MPLS  524292    11     10.21.31.31    63
32.32.32.32/32        isis (0)  MPLS  524301    11     10.21.22.22    20
32.32.32.32/32        isis (0)  MPLS  524299    11     10.21.22.22    10063
-------------------------------------------------------------------------------
Flags: B = BGP or MPLS backup hop available
       L = Loop-Free Alternate (LFA) hop available
       E = Inactive best-external BGP route
       k = RIB-API or Forwarding Policy backup hop
===============================================================================
```

## Verifying BGP

Check BGP status on CHI1 (SROS)

``` text
show router bgp summary
```

Expected output:

``` text
===============================================================================
 BGP Router ID:21.21.21.21      AS:65500       Local AS:65500
===============================================================================
BGP Admin State         : Up          BGP Oper State              : Up
Total Peer Groups       : 1           Total Peers                 : 5
<snip>
===============================================================================
BGP Summary
===============================================================================
Legend : D - Dynamic Neighbor
===============================================================================
Neighbor
Description
                   AS PktRcvd InQ  Up/Down   State|Rcv/Act/Sent (Addr Family)
                      PktSent OutQ
-------------------------------------------------------------------------------
11.11.11.11
                65500     967    0 07h59m24s 2/2/2 (Evpn)
                          966    0
12.12.12.12
                65500     967    0 07h59m24s 2/2/2 (Evpn)
                          966    0
22.22.22.22
                65500     966    0 07h59m24s 2/2/2 (Evpn)
                          967    0
31.31.31.31
                65500     966    0 07h59m24s 2/2/2 (Evpn)
                          966    0
32.32.32.32
                65500     966    0 07h59m24s 2/2/2 (Evpn)
                          967    0
-------------------------------------------------------------------------------
```

Check BGP EVPN routes on CHI1 (SROS)

``` text
show router bgp routes evpn ip-prefix
```

Expected output:

``` text
===============================================================================
 BGP Router ID:21.21.21.21      AS:65500       Local AS:65500
===============================================================================
 Legend -
 Status codes  : u - used, s - suppressed, h - history, d - decayed, * - valid
                 l - leaked, x - stale, > - best, b - backup, p - purge
 Origin codes  : i - IGP, e - EGP, ? - incomplete

===============================================================================
BGP EVPN IP-Prefix Routes
===============================================================================
Flag  Route Dist.         Prefix
      Tag                 Gw Address
                          NextHop
                          Label
                          ESI
-------------------------------------------------------------------------------
u*>i  11.11.11.11:200     192.168.11.0/24
      0                   00:00:00:00:00:00
                          11.11.11.11
                          LABEL 524287
                          ESI-0

u*>i  11.11.11.11:200     192.168.11.254/32
      0                   00:00:00:00:00:00
                          11.11.11.11
                          LABEL 524287
                          ESI-0

u*>i  12.12.12.12:200     192.168.12.0/24
      0                   00:00:00:00:00:00
                          12.12.12.12
                          LABEL 524287
                          ESI-0

u*>i  12.12.12.12:200     192.168.12.254/32
      0                   00:00:00:00:00:00
                          12.12.12.12
                          LABEL 524287
                          ESI-0

u*>i  22.22.22.22:200     192.168.22.0/24
      0                   00:00:00:00:00:00
                          22.22.22.22
                          LABEL 524287
                          ESI-0

u*>i  22.22.22.22:200     192.168.22.254/32
      0                   00:00:00:00:00:00
                          22.22.22.22
                          LABEL 524287
                          ESI-0

u*>i  31.31.31.31:200     192.168.31.0/24
      0                   00:00:00:00:00:00
                          31.31.31.31
                          LABEL 524287
                          ESI-0

u*>i  31.31.31.31:200     192.168.31.254/32
      0                   00:00:00:00:00:00
                          31.31.31.31
                          LABEL 524287
                          ESI-0

u*>i  32.32.32.32:200     192.168.32.0/24
      0                   00:00:00:00:00:00
                          32.32.32.32
                          LABEL 524287
                          ESI-0

u*>i  32.32.32.32:200     192.168.32.254/32
      0                   00:00:00:00:00:00
                          32.32.32.32
                          LABEL 524287
                          ESI-0

-------------------------------------------------------------------------------
Routes : 10
===============================================================================
```

## Verifying EVPN-MPLS service

Check service status on CHI1 (SROS)

``` text
show service id "tenant1" base
```

Expected output:

``` text
===============================================================================
Service Basic Information
===============================================================================
Service Id        : 200                 Vpn Id            : 0
Service Type      : VPRN
MACSec enabled    : no
Name              : tenant1
Description       : (Not Specified)
Customer Id       : 200                 Creation Origin   : manual
Last Status Change: 07/09/2025 22:01:41
Last Mgmt Change  : 07/09/2025 22:01:41
Admin State       : Up                  Oper State        : Up
<snip>
-------------------------------------------------------------------------------
Service Access & Destination Points
-------------------------------------------------------------------------------
Identifier                               Type         AdmMTU  OprMTU  Adm  Opr
-------------------------------------------------------------------------------
sap:1/1/c10/1                            null         1514    1514    Up   Up
===============================================================================
```


## Ping client-nyc2

Login to client-dal1

```bash
docker exec -it client-dal1 bash
```

Ping client-nyc2 IP from client-dal1

```bash
ping -c 3 192.168.32.32
```

Expected output

```bash
PING 192.168.32.32 (192.168.32.32) 56(84) bytes of data.
64 bytes from 192.168.32.32: icmp_seq=1 ttl=62 time=5.54 ms
64 bytes from 192.168.32.32: icmp_seq=2 ttl=62 time=6.16 ms
64 bytes from 192.168.32.32: icmp_seq=3 ttl=62 time=5.52 ms

--- 192.168.32.32 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 5.521/5.740/6.160/0.296 ms
```

## Client Machine IPs

| Client      | IP Address    |
| ----------- | ------------- |
| client-dal1 | 192.168.11.11 |
| client-dal2 | 192.168.12.12 |
| client-chi1 | 192.168.21.21 |
| client-chi2 | 192.168.22.22 |
| client-nyc1 | 192.168.31.31 |
| client-nyc2 | 192.168.32.32 |