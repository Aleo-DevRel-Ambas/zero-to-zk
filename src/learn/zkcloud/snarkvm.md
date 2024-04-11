# snarkVM
The snarkVM platform allows users to write and execute smart contracts in an efficient, yet privacy-preserving manner by leveraging zero- knowledge succinct non-interactive arguments of knowledge (zk-SNARKs).

## Architectural Components
- `snarkVM synthesizer` - used to translate code into circuits that are compatible with the underlying zk-SNARK cryptographic proof system.
- `snarkVM algorithms` - the implementation and execution of the proof system and the primitives that support it
- `snarkVM ledger` - data structures and methods that enable storage and interaction with the Aleo blockchain.

Proving System Overview (Varuna)