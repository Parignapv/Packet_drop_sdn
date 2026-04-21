\# Packet Drop Simulation using SDN



\## Problem Statement



Simulate packet loss using Software Defined Networking (SDN) by selectively dropping traffic between hosts.



\---



\## Tools Used



\* Mininet

\* Open vSwitch (OVS)

\* Ryu Controller

\* Ubuntu Virtual Machine



\---



\## Topology



Single switch with three hosts:



h1 ——\\

s1 —— h2

h3 ——/



\---



\## Setup / Execution



```

sudo mn -c

/usr/bin/ryu-manager drop\_controller.py

sudo mn --topo single,3 --controller=remote --switch ovsk,protocols=OpenFlow13

```



\---



\## Testing



```

h1 ping h2   # blocked

h3 ping h1   # allowed

```



\---



\## Expected Output



\* h1 → h2 → 100% packet loss (blocked)

\* h3 → h1 → 0% packet loss (allowed)



\---



\## Proof of Execution



\### Controller Running



!\[Controller](images/controller.png)



\### Blocked Traffic (h1 → h2)



!\[Blocked](images/blocked.png)



\### Allowed Traffic (h3 → h1)



!\[Allowed](images/allowed.png)



\---



\## Flow Table



```

sudo ovs-ofctl -O OpenFlow13 dump-flows s1



cookie=0x0, duration=44.150s, table=0, n\_packets=0, n\_bytes=0, priority=1,in\_port="s1-eth1",dl\_src=96:56:9e:e1:dc:a1,dl\_dst=33:33:ff:e1:dc:a1 actions=FLOOD

cookie=0x0, duration=43.510s, table=0, n\_packets=0, n\_bytes=0, priority=1,in\_port="s1-eth2",dl\_src=32:09:8f:42:03:72,dl\_dst=33:33:ff:42:03:72 actions=FLOOD

cookie=0x0, duration=43.221s, table=0, n\_packets=1, n\_bytes=90, priority=1,in\_port="s1-eth3",dl\_src=72:48:63:c2:49:c1,dl\_dst=33:33:00:00:00:16 actions=FLOOD

cookie=0x0, duration=43.220s, table=0, n\_packets=1, n\_bytes=210, priority=1,in\_port="s1-eth3",dl\_src=72:48:63:c2:49:c1,dl\_dst=33:33:00:00:00:02 actions=FLOOD

cookie=0x0, duration=43.120s, table=0, n\_packets=1, n\_bytes=90, priority=1,in\_port="s1-eth1",dl\_src=96:56:9e:e1:dc:a1,dl\_dst=33:33:00:00:00:16 actions=FLOOD

cookie=0x0, duration=43.113s, table=0, n\_packets=3, n\_bytes=210, priority=1,in\_port="s1-eth1",dl\_src=96:56:9e:e1:dc:a1,dl\_dst=33:33:00:00:00:02 actions=FLOOD

cookie=0x0, duration=42.486s, table=0, n\_packets=1, n\_bytes=90, priority=1,in\_port="s1-eth2",dl\_src=32:09:8f:42:03:72,dl\_dst=33:33:00:00:00:16 actions=FLOOD

cookie=0x0, duration=42.479s, table=0, n\_packets=3, n\_bytes=210, priority=1,in\_port="s1-eth2",dl\_src=32:09:8f:42:03:72,dl\_dst=33:33:00:00:00:02 actions=FLOOD

cookie=0x0, duration=44.158s, table=0, n\_packets=8, n\_bytes=652, priority=0 actions=CONTROLLER:65535


## Regression Testing

After implementing the packet drop rule (blocking traffic from h1 to h2), regression testing was performed to ensure that other network communications were not affected.

| Test Case | Before Change | After Change | Result     |
| --------- | ------------- | ------------ | -----------|
| h1 → h2   | Allowed       | Blocked      | Expected   |
| h2 → h1   | Allowed       | Allowed      | Unaffected |
| h3 → h1   | Allowed       | Allowed      | Unaffected |
| h1 → h3   | Allowed       | Allowed      | Unaffected |

This confirms that only the intended traffic (h1 → h2) is blocked, while all other communications continue to function normally.


\## Conclusion

This experiment demonstrates how SDN can control network behavior by selectively dropping packets using match-action rules.



