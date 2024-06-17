# Aleo’s Proof System - Marlin & Varuna

Aleo leverages both Marlin and Varuna to build a robust proof system that supports private, scalable, and efficient execution of smart contracts. The system is designed to ensure the privacy of both transactions and the logic of smart contracts, which is a significant advancement in the field of privacy-preserving blockchain technology.

## Marlin

Marlin is a universal, updatable, and efficient zk-SNARK (Zero-Knowledge Succinct Non-Interactive Argument of Knowledge) protocol. Some key characteristics of Marlin:

1. **Universal and Updatable**: Marlin supports a universal setup that can be used for any computation, and this setup can be updated securely to support new computations without starting from scratch. This feature enhances the scalability and flexibility of the system.
2. **Efficiency**: Marlin is designed to be efficient in terms of both proof generation and verification. It significantly reduces the computational overhead compared to previous zk-SNARK protocols.
3. **Linear Prover and Verifier Time**: The protocol achieves linear prover time and quasi-linear verifier time, making it practical for a wide range of applications.
4. **Post-Quantum Security**: Marlin uses polynomial commitment schemes that are secure under standard cryptographic assumptions, and its security holds even in the presence of quantum computers.

Check out Marlin’s white paper: [Marlin: Preprocessing zkSNARKs with Universal and Updatable SRS](https://eprint.iacr.org/2019/1047.pdf)

## Varuna

Varuna is an improvement over Marlin and introduces additional enhancements to further optimize the proof system. Some key features of Varuna include:

1. **Efficient Recursive Proof Composition**: Varuna is designed to support efficient recursive proof composition, which allows for the aggregation of multiple proofs into a single proof. This feature is particularly useful for scaling blockchain systems and reducing the overall size of proofs.
2. **Better Performance**: Varuna offers improved performance metrics over Marlin, including faster proof generation and verification times, which are crucial for high-throughput applications.
3. **Optimized for Blockchain**: Varuna is specifically optimized for use in blockchain environments, ensuring that it meets the performance and security requirements of modern decentralized applications.

Check out [Varuna’s implementation](https://github.com/AleoNet/snarkVM/tree/mainnet-staging/algorithms/src/snark/varuna) in snarkVM codebase.
