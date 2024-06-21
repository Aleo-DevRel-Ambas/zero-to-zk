# Understanding proof systems

Most proof system can be described in three major phases: **Preprocessing, Proving and Verification**. Let’s take a look at each phase:

## Preprocessing Phase (Offline)

- **Indexing the Circuit:** The prover preprocesses the circuit to produce an index consisting of low-degree polynomials that encode the circuit's description. The index $i = (F, n, m, A, B, C)$ includes finite field $F$, size parameters $n$ and $m$, and coefficient matrices $A, B, C$.
- **Commitments to Polynomials:** The prover generates commitments to these polynomials using a polynomial commitment scheme. This involves computing commitments such as $c = p(\beta)G$, where $p$ is the polynomial and $\beta$ is a secret parameter.

## Proving Phase (Online)

- **Prover's Oracles:** The prover generates proof oracles by computing polynomials based on the input and the witness. For example, the prover constructs $z(X)$, a univariate polynomial of degree less than $n$ that interpolates the assignment vector $z$ over a subset $H$ of the field $F$.
- **Polynomial Commitments:** The prover commits to these polynomials and sends the commitments to the verifier. The commitments $c$ are based on polynomials such as $z(X), zA(X), zB(X), zC(X)$.
- **Query Phase:** The verifier queries specific points in the committed polynomials. For example, the verifier might query the polynomial $z(X)$ at a random point $\beta \in F$.
- **Evaluation Proofs:** The prover sends evaluations of the queried polynomials along with proofs that these evaluations are correct. This involves sending evaluation proofs $\pi$ and polynomials $h(X)$ and $g(X)$ that satisfy the necessary polynomial relationships.

## Verification Phase

- **Verification of Proofs:** The verifier checks the proofs of the evaluations using the commitments and the polynomial commitment scheme. For example, the verifier checks the equation $e(c - vG, H) = e(\pi, \beta H - zH)$ to validate an evaluation proof.
- **Final Decision:** If the proofs are valid, the verifier accepts the proof, otherwise, it rejects it.
