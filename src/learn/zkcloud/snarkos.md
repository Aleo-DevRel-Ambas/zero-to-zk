# SnarkOS
snarkOS is a decentralized operating system for zero-knowledge applications. This code forms the backbone of Aleo network, which verifies transactions and stores the encrypted state applications in a publicly-verifiable manner.

The network client has to take care of verifying the transactions computed off chain using snarkvm, allowing all snarkos nodes to reach consensus and to store the private and non private state in Aleo's distributed ledgers.

The snarkOs platform enforces data availability guarantees on Aleo for all programs and transactions on the network, while interacting with AleoBFT to ensure verifiers compute zero-knowledge proofs to checkpoint state on-chain.