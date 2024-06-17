# Varuna: a recursive Marlin

Varuna is an upgrade to Marlin developed by Aleo to enhance the recursion capabilities of zero-knowledge proofs, making zkSNARKs more efficient and scalable. This upgrade focuses on improving the handling of nested proofs and reducing the overhead associated with recursive zkSNARKs.

## **Recursive zkSNARKs: Background**

Recursive zkSNARKs enable the verification of a proof by another proof, creating a chain of verifiable proofs. This capability is crucial for applications requiring multiple layers of verification, such as blockchain rollups, where the state transitions are verified recursively. The primary challenge in recursive zkSNARKs is to maintain efficiency while ensuring that the proofs remain succinct and the verification process is fast.

## Optimized Polynomial Commitments

Varuna enhances the polynomial commitment scheme used in Marlin by introducing several optimizations that improve efficiency and scalability:

### **Commitment Size Reduction**

The commitment size in Varuna is reduced by optimizing the encoding of the polynomials. This is achieved by leveraging more compact representations of the polynomials and using advanced cryptographic techniques to minimize the size of the commitments.

For instance, Varuna may use a more efficient basis for polynomial representation, such as a basis that aligns better with the underlying field structure, reducing the number of terms required for the commitment.

For a polynomial $p(X)$ of degree $d \leq D$ with coefficients $\{a_0, a_1, \ldots, a_d\}$, the commitment $c$ to $p(X)$ is computed as:

$$

c = p(\beta)G = \left( \sum_{i=0}^{d} a_i \beta^i \right) G
$$

### **Evaluation Proof Efficiency**

The efficiency of evaluation proofs is improved by optimizing the computation of witness polynomials. Varuna ensures that the witness polynomials are computed quickly and the corresponding proofs are generated with minimal overhead.

The enhancements may include more efficient algorithms for polynomial division and interpolation, ensuring that the witness polynomials can be computed and committed efficiently.

To prove that $p(z) = v$ for some $z \in F$, compute the witness polynomial $w(X)$:

$$

w(X) = \frac{p(X) - p(z)}{X - z}
$$

The commitment to $w(X)$ is:

$$
⁍.
$$

The verifier checks the bilinear pairing equation:

$$
e(c - vG, H) = e(\pi, \beta H - zH)
$$

## **Improved Handling of Nested Proofs**

Varuna addresses the challenges of nested proofs by introducing several enhancements to the sumcheck protocols and polynomial evaluations.

### **Enhanced Sumcheck Protocols**

The sumcheck protocols in Varuna are optimized to handle nested proofs more efficiently. This involves reducing the number of rounds required for the sumcheck and optimizing the polynomial evaluations within each round.

For example, Varuna may use more efficient techniques for evaluating the sumcheck polynomials, such as leveraging precomputed tables or using more efficient algorithms for polynomial multiplication.

Given a polynomial $P(X)$ that needs to be evaluated efficiently within the sumcheck protocol, Varuna optimizes this by reducing the number of rounds and leveraging precomputed tables. The sumcheck for $P(X)$ can be expressed as:

$$
\sum_{x \in \mathbb{F}} P(x) = \sum_{i=0}^{d} \sum_{x \in \mathbb{F}} a_i x^i
$$

This expression is optimized by using efficient polynomial multiplication and sparse representation techniques.

### **Efficient Polynomial Evaluations**

Varuna ensures that the polynomial evaluations are performed efficiently, even in the context of nested proofs. This is achieved by optimizing the data structures used for storing the polynomials and leveraging efficient algorithms for evaluating the polynomials at multiple points.

The enhancements may include using more efficient data structures for polynomial storage, such as sparse representations, and leveraging parallel algorithms for polynomial evaluation.

## **Recursive Proof Compression**

Varuna introduces advanced techniques for compressing recursive proofs, ensuring that the proof chain remains compact and efficient.

To compress recursive proofs, Varuna employs advanced polynomial commitments and encoding techniques. The recursive proof compression can be illustrated as follows:

1. **Generate Recursive Proof**: for each recursive step, generate a proof $\pi_i$ for the polynomial commitment $c_i$.
2. **Combine Proofs**: combine the proofs $\pi_i$ into a single compressed proof $\Pi$ using homomorphic encryption or zkSNARK techniques.
3. **Verify Compressed Proof:** verifier checks the compressed proof $\Pi$ using efficient polynomial representations and optimized algorithms for polynomial multiplication and evaluation.

## **Application in Aleo Network**

Aleo Network leverages Varuna to enhance the scalability and efficiency of its zero-knowledge proofs. The recursive zkSNARKs enabled by Varuna allow Aleo to verify complex computations efficiently, supporting a wide range of applications from simple transactions to complex smart contracts.

Aleo’s use of Varuna ensures that proofs remain succinct and verification times are kept minimal, even for nested proofs. This efficiency is particularly beneficial in a blockchain context, where verifying transactions quickly and securely is paramount. By enhancing Marlin with Varuna, Aleo can provide more scalable and efficient zero-knowledge proofs, supporting its mission to deliver private, scalable, and efficient blockchain applications.
