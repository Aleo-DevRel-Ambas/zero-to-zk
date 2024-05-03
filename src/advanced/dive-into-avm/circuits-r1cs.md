# R1CS Circuits

Aleo Virtual Machine leverages a type of intermediate representation for their circuits called “Rank-1 Constrained System” (R1CS). This format is used to express computations as a system of equations that can later be formally verified.

## **Definition and Structure of R1CS**

R1CS is essentially a set of linear equations over a field, structured to represent computational problems. Each constraint in an R1CS is an equation of the form:

$$
(a_0+a_1x_1+...+a_nx_n)(b_0+b_1y_1+...+b_my_m) = (c_0+c_1z_1+...+c_lz_l)
$$

Where $x_i, y_j, z_k$ are variables and $a_i,b_j,c_k$ are constant from the given field. These constraints are designed to ensure that if the variables satisfy the left side of the equations, they must also satisfy the right side. Check the **Schwartz-Zippel Lemma** more further details.

## **R1CS flow in the AVM**

### **Circuit Generation**

1. **Leo** programs are compiled into “**Aleo instruction**s”: an assembly-like language that the AVM can understand.
2. **Aleo Instructions** are compiled into R1CS circuit representation.
3. **R1CS circuits** are exported and potentially deployed to the network.

### **Proof Generation**

Once a program is represented as an R1CS, Aleo uses this representation to generate zero-knowledge proofs. These proofs allow a prover (the one executing the program) to convince a verifier (Aleo’s verifier network of nodes) that they have correctly executed the logic included in the program without revealing the specific inputs or internal state of the computation.

### **Verification**

The verifier, who receives only the proof and the public outputs (if any), checks the proof against the R1CS circuit to ensure that the computation was performed correctly. This verification process does not require the verifier to know the specific details of the computation, thus maintaining privacy.

---
