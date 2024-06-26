# Marlin - Holography & Universal Setups

The white paper that introduced [Marlin](https://eprint.iacr.org/2019/1047.pdf) was first published in 2020 as “Marlin: Preprocessing zkSNARKs with Universal and Updatable SRS” by Alessandro Chiesa (et al.). This methodology for constructing preprocessing zkSNARKS allows Aleo Network to provide scalable and efficient zero-knowledge proofs for private applications.

## TL;DR

- **Polynomial Commitments:** Marlin uses an efficient polynomial commitment scheme that is extractable and hiding, ensuring that no information about the committed polynomial is revealed other than what is explicitly shown in the evaluations.
- **Algebraic Holographic Proofs (AHPs):** These are used to preprocess the circuit, transforming it into low-degree polynomials. AHPs ensure that the verifier only needs to make a small number of queries, significantly reducing verification time.
- **Universal and Updatable SRS:** Marlin’s SRS can be used for any circuit size (bounded by a maximum degree of polynomial committed during setup) and can be updated securely by any party, simplifying the setup process and improving trust assumptions.

## Algebraic Holographic Proofs (AHPs)

The biggest contribution proposed in Marlin white paper is the introduction of AHP since they enable efficient preprocessing zkSNARKs. AHPs transform complex computational statements into algebraic forms that are easy to handle and verify.

AHPs modify the concept of Interactive Oracle Proofs (IOPs) by integrating two primary features:

- **Holography:** allows the verifier to access an encoded form of the input, significantly reducing verification time.
- **Algebraicity:** ensures that both the prover and the verifier operate on low-degree polynomials, maintaining the structure and efficiency of the proofs.

Achieving holography in AHPs involves allowing the verifier to work with encoded representations of the circuit. This encoding reduces the verifier’s workload, making verification efficient even for large circuits. Marlin achieves this by transforming circuit descriptions into low-degree polynomials, enabling the verifier to perform sub-linear checks.

The protocol relies on efficiently handling sumchecks and leveraging algebraic properties to ensure that the verifier’s operations remain within acceptable complexity bounds. By encoding the circuit as low-degree polynomials and using random evaluations, Marlin ensures robust and efficient verification with minimal overhead.

## SRS Universality

The universal and updatable SRS of Marlin supports any circuit size (bounded by a maximum degree of the polynomial committed during the setup) without requiring a trusted setup ceremony for each circuit. This capability is crucial for the flexibility and scalability of Aleo’s private applications, which can range from simple transactions to complex smart contracts.

Aleo uses the efficient encoding and verification properties of AHPs to ensure that proofs remain succinct and verification times are kept minimal. This efficiency is particularly beneficial in a blockchain context, where verifying transactions quickly and securely is paramount.

## **Using AHP and Universal SRS**

Let’s re-visit the three basic phases of a proof system, this time including the AHP and Universal SRS approach.

### **Preprocessing Phase (Offline Phase)**

The prover preprocesses the circuit and generates a universal SRS. Initially, the finite field $F$ is defined, and a random element $\beta$ is chosen. The circuit $C$ is then transformed into its Rank-1 Constraint System (R1CS) representation, resulting in coefficient matrices $A$, $B$, and $C$. These matrices, along with the finite field and size parameters $n$ and $m$, form the indexed representation of the circuit $i = (F, n, m, A, B, C)$.

Following this, the committer key ($ck$) and receiver key ($rk$) for the polynomial commitment scheme are generated. The committer key consists of the elements $\{G, \beta G, \beta^2 G, \ldots, \beta^D G\} \in G_1^{D+1}$, while the receiver key includes $(G, H, \beta H) \in G_1 \times G_2^2$.

This setup phase outputs the indexed circuit $i$ and the universal SRS, setting the stage for the proving and verification phases.

### **Proving Phase (Online Phase)**

The prover generates the proof for a given statement by committing to polynomials and interacting with the verifier using the AHP protocol. This phase ensures that the prover can efficiently convince the verifier of the statement’s validity.

The process begins with the prover computing the polynomial $z(X)$ that interpolates the assignment vector $z = (x, w)$ over a subset $H$ of the field $F$. This polynomial is represented as:

$$
z(X) = \sum_{i=0}^{n-1} z_i L_i(X)
$$

Where $L_i(X)$ are the Lagrange basis polynomials for $H$. The prover then computes the polynomials $z_A(X)$, $z_B(X)$, and $z_C(X)$, which correspond to the linear combinations of the variables with the R1CS matrices $A$$,$ $B$, and $C$, respectively.

The prover commits to these polynomials using the SRS, resulting in commitments:

- $c_z = z(\beta) G$
- $c_{z_A} = z_A(\beta) G$
- $c_{z_B} = z_B(\beta) G$
- $c_{z_C} = z_C(\beta) G$

To prove that $(z_A(κ) z_B(κ) - z_C(κ) = 0)$ for all $(κ \in H)$, the prover sends the polynomial $(h_0(X))$ such that:

$$
z_A(X) z_B(X) - z_C(X) = h_0(X) v_H(X)
$$

The verifier checks this equation at a random point $\beta \in F$, ensuring that the polynomials satisfy the required relations.

Next, the prover must demonstrate that $z_M(κ) = \sum_{i \in H} M[κ, i] z(i)$ for each matrix $(M \in {A, B, C})$. This is achieved by sending polynomials $h_M(X)$ and $g_M(X)$ to the verifier, which verify the relation:

$$
r(\alpha, X) z_M(X) - r_M(α, X) z(X) = h_M(X) v_H(X) + X g_M(X)
$$

The verifier evaluates these at random points $\alpha$ and $\beta$.

Finally, the prover combines these commitments and evaluations to generate the proof $\pi$, which encapsulates the entire proving process.

### **Verification Phase**

The verifier checks the proof by querying the committed polynomials and validating the sumcheck equations. This phase ensures that the verifier can quickly and efficiently validate the proof. The verifier queries the polynomials $z(X$), $z_A(X$), $z_B(X)$, $z_C(X)$, $h_0(X)$, $h_M(X)$, and $g_M(X)$ at random points $\beta$ and $\alpha$.

To verify the entry-wise product relation, the verifier checks the sumcheck equation:

$$
e(c_{z_A} \cdot c_{z_B} - c_{z_C}, H) = e(h_0(\beta) G, \beta H)
$$

For the linear relations, the verifier ensures that:

$$
e(r(α, β) c_{z_M} - r_M(α, β) c_z, H) = e(h_M(\beta) G, \beta H)
$$

Additionally, the verifier checks:

$$
r(α, β) z_M(β) - r_M(α, β) z(β) = h_M(β) v_H(β) + β g_M(β)
$$

If all these checks pass, the verifier accepts the proof; otherwise, the proof is rejected.

By integrating Algebraic Holographic Proofs (AHP) and a Universal Structured Reference String (SRS), Marlin achieves efficient preprocessing zkSNARKs that support fast and scalable proof generation and verification. This approach enhances the practical application of zero-knowledge proofs in various domains, including blockchain and privacy-preserving technologies.
