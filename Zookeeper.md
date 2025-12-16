# Zookeeper

## What is consistency?

### CAP Theorem

We are not able to acchive these 3 points.

- Consistency

- Availability

- Partition Tolerance
  - For example, two area, US and China, when the connection between them crashed, they still able seperately.

### Consistency model

- weak consistency
  - acchive consistency at the end : DNS, Gossip

- strong consistency
  - Raft

### Strong Consistency Algorythms

- master slave 同步
  - master recive write reqs
  - master copy logs to slave
  - master wait, till all slaves return a state that complite
  - **Problem with this one is : if one slave failed, master sucks, and the whole cluster is not available, in the end, high consistency but low Availability**

- 多数派
  - each time perform a write, make sure to write in greater than n/2 nodes, and read from greater than n/2 nodes.
  - **Problem with this one is : in a high concurrency condition, it cannot garentee the correctness of the cluster, for the order matters**

- Paxos
  - basic paxos
    - **Problem with this one is : liveness(活鎖), which is the whole system cannot move to the next state**
  - multi paxos

- Raft (very like multi paxos)
  - Leader election
  - Log replication
  - safety
  - **Some roles : Leader, Follower, Candidates**
