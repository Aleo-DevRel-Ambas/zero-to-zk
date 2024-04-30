# Consensus mechanism
## Overview
Aleo Network employs a unique consensus mechanism known as AleoBFT to achieve a secure and resilient consensus system with instant finality for block confirmation. This mechanism combines proof-of-stake (POS) to ensure that validators are rewarded for maintaining the overall system integrity and performance.

Aleo Network is run and maintained by three groups of participants:  
- **Stakers** - Delegate staked Aleo Credits (AC) to help onboard more validators and participate in consensus on the Network.  
- **Provers** - Utilize specialized hardware to generate proofs and solve coinbase puzzles, contributing to the security of the network.
- **Validators** - Validate transactions by verifying zero knowledge (ZK) proofs and actively participate in the consensus process on the network.

Check [this FAQs](https://aleo.org/faq/) out in regards of the groups mentioned above.

Everyone can become a staker by locking up their Aleo Credits for a certain period of time to support the security of the Aleo Network. While the minimum amount to stake is 1 AC, stakers will only start earning rewards once they have staked at least 10 ACs. Stakers help lower the barriers to becoming a validator by delegating their stakes to validators of their choice.  

Learn more about **stakers** at [here](). (TODO: redirect to relative docs)  

Provers are required to run specialized GPUs and CPUs to generate solutions in SNARK proofs for PoSW (Proof-of-Succinct-Work) coinbase puzzles. They are rewarded based on their efficiency and effectiveness in generating solutions to the puzzles. It's important to note that provers do not produce blocks, but they are incentivized to improve the process of generating proofs, reducing costs, and decreasing latency for program execution.  

Learn more about **provers** at [here](). (TODO: redirect to relative docs)  

Validators play a crucial role in securing the network through AleoBFT (to be discussed further below) and must have at least 1 million AC of stakes to get started. The main function of validators is to verify ZK proofs and validate transactions before including them in a confirmed block.

Learn more about **validators** at [here](https://aleo.org/faq/). (TODO: redirect to relative docs)  

## AleoBFT
AleoBFT is a new hybrid architecture for consensus. It is a DAG-based BFT protocol inspired by Narwhal and Bullshark. It incentivises validators to preserve network liveness and provers to scale proving capacity for Aleo ecosystem.

AleoBFT guarantees instant finality once validators achieve consensus for each block. With instant finality, not only validators enjoy better node stability but also creates smooth experience for applications developers and users. And this guarantee makes interoperability with other ecosystem simpler.

AleoBFT provers are computing core components of ZK proofs and receive shares of coinbase rewards by solving and producing these coinbase proofs, which is called Proof of Succint Work (PoSW). This incentivise provers to also become a validator themselves by accumulate and stake 1 million ACs. By having broader rewards distribution, it helps Aleo Network to achieve greater proving capacity, further decentralise and scaling Aleo network and fortifies censorship-resistence guarantee.

## Bullshark and Narwhal

### Bullshark
Bullshark is a DAG(Directed Acyclic Graph)-based BFT (Byzantine Fault Tolerance) protocol, it separate the network communication layer from the ordering (consensus) logic. Each message contains a set of transactions, and a set of references to previous messages. Together, all the messages form a DAG that keeps growing – a message is a vertex and its references are edges. A vertex can be a proposal and an edge can be a vote.

While different parties may see slightly different DAGs at any point in time due to the asynchronous nature of the network, each validator can still totally (fully) orders all the vertices (blocks) without sending a single extra message by just looking at its local view of the DAG.

The DAG being used here is round-based DAG, each vertex is associated with round number, each round has at most `n` vertices. Each validator broadcasts one message per round and each message references at least `n − f` messages in previous round. `n` is the total number of validating nodes in the network and `f` is the number of Byzantine nodes. Below shows a diagram of how it looks like with `n = 4` and `f = 1`.

![DAG](./images/DAG1.png)
Diagram 1: Round-based DAG  
Image from https://decentralizedthoughts.github.io/2022-06-28-DAG-meets-BFT/

### Ordering Logic
A predefined leader is elected in every even round and the vertex associated with it is referred as anchor. Anchor is a more suitable term to describe here because unlike typical leader-based protocols where the leader has to do all the works at each round and disseminate data to all other nodes, the anchor here only get chosen to commit its casual history once gather enough votes (`2f + 1`) (3 in this example).  

Each vertex in odd round can contribute one vote for the anchor in previous round. The anchor is commited if it has at least `f + 1` (2 in this example) votes. Once anchor is committed, its casual history is ordered by some deterministic rule. Green-outlined vertices shown in Diagram 2 is Anchor 2 (A2) casual history. Diagram 3 shows A2 is committed with 3 votes but A1 is not committed with only 1 vote.

![DAG](./images/DAG2.png)
Diagram 2: Anchor and casual history  
Image from https://decentralizedthoughts.github.io/2022-06-28-DAG-meets-BFT/

![DAG](./images/DAG3.png)
Diagram 3: Commit rule  
Image from https://decentralizedthoughts.github.io/2022-06-28-DAG-meets-BFT/

Due to asynchronous nature of the network, the local views of the DAG might differ for different parties. A1 might have committed by other validator. As shown in diagram 4, validator 2 sees two (`f + 1`) votes for anchor A1 and thus commits it even though validator 1 has not.

![DAG](./images/DAG4.png)
Diagram 4: Different local view  
Image from https://decentralizedthoughts.github.io/2022-06-28-DAG-meets-BFT/

Because to commit an anchor requires `f + 1` (2 in this example) votes and each vertex in the DAG has at least `n − f` (3 in this example) edges to vertices from the previous round, it is guaranteed that if some party commits an anchor A then all anchors in higher rounds will have a path to at least one vertex that voted for A, and thus will have a path to A.

This is also means that if there is no path to a anchor A from a future anchor, then no party committed A and it is safe to skip it. Diagram 5 shows that A2 is not committed by any party and thus A2 is safe to skip.

![DAG](./images/DAG5.png)
Diagram 5: Skipping uncommitted anchor  
Image from https://decentralizedthoughts.github.io/2022-06-28-DAG-meets-BFT/

When an achor is committed, the validator checks if there's a path to previous uncommitted anchor. If there is, it will commit the previous anchor as well. This process is repeated until it reaches previous committed anchor. Diagram 5 shows that A3 is committed and A1 is in the path of A3 thus A1 is committed as well.