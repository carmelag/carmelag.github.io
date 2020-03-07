---
title: Consensus Algorithm
permalink: /docs/consensus-algorithm/
author: 'Carmela Greco'
---

There are different types of consensus implementations. They are different according to the communication model(synchronous or asynchronous) and to the failure detection they use.
This project uses the Chandra-Toueg algorithm: it uses a synchronous message exchange and includes a “S type failure detection module.
A “S” fault detection has the following properties:
1. *__Strong Completeness__*
2. *__Weak Accuracy__*

That means:
- in the end every faulty process will be suspected by non-faulty processes
- some correct process is never suspected

This algoritm in an asynchronous system containing __n entities__, tolerates __n-1 faulty processes__.

The algorithm has 3 phases:

- __Phase 1:__
   a process p executes n-2 asynchronous rounds. During a round the process broadcasts a vector of proposed values (one for each process, there could be some empty entries), then it receives the proposed values from the other processes. So every process p proposed a value vp, contained within Vp[p], by sending it within the round r to all the other processes. After that the process waits to receive the proposed values coming from the processes that are not listed in the suspectslist provided by the local module of failure detection and updates the vector of proposed values. Then it goes to round r+1.

- __Phase 2:__ 
   after n-1 rounds, the final vectors with the proposed values is sent to all the processes. So p waits the proposed values vector from every process non listes within the suspects list provided by the local module of failure detection and it updates its vector with the received values. After this phase the proposed values vector will be the same for each correct process, the i-element within the vector is the proposed value coming from process i or is null.
- __Phase 3:__ 
   the correct processes decides the first not-null value within the proposed values vector.
---
#### __Initialization__
```javascript
Array Vp := [null, null, . . ., null];
Value Vp[p] := vp;
Array ∆p := Vp;
```

#### __Phase 1__
```javascript
for each rp := 1, 1 ≤ rp ≤ n − 1 
send [rp, ∆p, p] to all; 
wait until (deliver [rp, ∆q, q] from ∀q ∈/ FDp) 
msgsp[rp] := {(rp, ∆q, q)|delivered(rp, ∆q, q)};
∆p := [null, null, . . ., null]; 
for each k := 1, 1 ≤ k ≤ n 
if (Vp[k]=null && ∃(rp, ∆q, q)∈ msgsp[rp] | ∆q[k]=null) 
then
Vp[k] := ∆q[k];
∆p[k] := ∆q[k];
```

#### __Phase 2__
```javascript
send [Vp] to all;
wait until (deliver [Vq] from ∀q ∈/ FDp) 
lastmsgsp := {Vq|delivered(Vq)}; 
for each k := 1, 1 ≤ k ≤ n
if (∃Vq ∈ lastmsgsp|Vq[k] = null)
then  
Vp[k] := ∆q[k];
Vp[k] := null;
```
#### __Phase 3__ 
```javascript           
decide(first non-null element of Vp);
```