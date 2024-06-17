# Polynomial Commitment Scheme - Extending KZG

The polynomial commitment scheme used in Marlin is based on the construction proposed by Kate, Zaverucha, and Goldberg ([KZG](https://www.iacr.org/archive/asiacrypt2010/6477178/6477178.pdf)). This scheme allows a prover to commit to a polynomial and later provide proofs that the committed polynomial evaluates to certain values at specified points. The primary components and steps of the scheme are as follows:

## Setup

The setup phase begins by sampling a cryptographically secure bilinear group $(G_1, G_2, G_T, q, G, H, e)$. Here, $G_1$ and $G_2$ are two groups of prime order $q, G \in G_1$ and $H \in G_2$ are generators, and $e: G_1 \times G_2 \rightarrow G_T$ is a bilinear pairing. The security parameter determines the size of these groups, ensuring the system’s robustness against cryptographic attacks.

The committer key ($ck$) and receiver key ($rk$) are then generated. The committer key is a sequence of group elements encoding powers of a random field element $\beta$:

$$
ck = \{ G, \beta G, \beta^2 G, \ldots, \beta^D G \} \in G_1^{D+1}.
$$

The receiver key consists of:

$$
rk = (G, H, \beta H) \in G_1 \times G_2^2.
$$

These keys are fundamental for committing to polynomials and verifying their evaluations.

## Commitment Phase

The prover commits to a polynomial p(X) of degree $d \leq D$ with coefficients $\{a_0, a_1, \ldots, a_d\}$. The commitment $c$ to $p(X)$ is computed as:

$$

c = p(\beta)G = \left( \sum_{i=0}^{d} a_i \beta^i \right) G
$$

This step effectively binds the polynomial to a single group element, ensuring that any later evaluation will be consistent with this commitment.

## Evaluation Proof Phase

When generating an evaluation proof, the prover needs to demonstrate that $p(z) = v$ for some $z \in F$. To achieve this, the prover computes the witness polynomial $w(X)$:

$$

w(X) = \frac{p(X) - p(z)}{X - z}
$$

The commitment to $w(X)$ is:

$$

\pi = w(\beta)G.
$$

The existence and validity of the witness polynomial $w(X)$ confirm that $p(z) = v$.

For batch evaluation proofs, where multiple polynomials are evaluated at a single point z, the prover combines the polynomials into a single linear combination using a random scalar $\xi$. Let $p_1, p_2, \ldots, p_n$ be the polynomials and $v_1, v_2, \ldots, v_n$ their respective evaluations at $z$. The combined polynomial $p(X)$ is:

$$

p(X) = \sum_{i=1}^{n} \xi^i p_i(X)
$$

And the combined commitment with the combined evaluation $v$ are as follow:

$$

c = \sum_{i=1}^{n} \xi^i c_i
$$

$$

v = \sum_{i=1}^{n} \xi^i v_i
$$

The proof $\pi$ for $p(X)$ at $z$ is then generated accordingly.

## Verification Phase

In the verification phase, the verifier needs to check the validity of the evaluation proofs. For a single evaluation, the verifier confirms that $p(z) = v$ using the proof $\pi$. This is done by verifying the bilinear pairing equation:

$$

e(c - vG, H) = e(\pi, \beta H - zH)
$$

If this equation holds, the proof is valid, confirming that $p(z) = v$.

For batch evaluations, the verifier checks the combined proof by using the combined commitment $c$ and evaluation $v$. The verifier ensures the validity by verifying the pairing equation:

$$

e\left( \sum_{i=1}^{n} \xi^i (c_i - v_i G), H \right) = e\left( \pi, \beta H - z H \right)
$$

This approach allows efficient verification of multiple polynomial evaluations with a single proof, enhancing the scalability of the system.

## Extractability and Security

The polynomial commitment scheme in Marlin ensures **extractability**, meaning that if an adversary can produce a valid commitment and evaluation proof, there exists an extractor that can recover the polynomial p(X) from the commitment. This is enforced either by using knowledge commitments or conducting the security analysis in the Algebraic Group Model (AGM). Additionally, the commitment hides the polynomial $p(X)$ except for the information revealed by the evaluation proofs, ensuring the zero-knowledge properties required in zkSNARKs.

## Efficiency Considerations

The efficiency of the polynomial commitment scheme is notable. The commitment $c$ is a single group element, regardless of the polynomial’s degree, and the evaluation proof $\pi$ is also a single group element. Verification involves a constant number of pairing operations, making it highly efficient. This efficiency is crucial for enabling scalable and practical zero-knowledge proofs, as demonstrated in the Marlin protocol and its applications in the Aleo Network.

By integrating these elements, Marlin achieves a robust and efficient polynomial commitment scheme that supports succinct, non-interactive zero-knowledge proofs. This scheme is pivotal in enabling the preprocessing zkSNARKs with universal and updatable SRS, facilitating secure and scalable cryptographic protocols.
