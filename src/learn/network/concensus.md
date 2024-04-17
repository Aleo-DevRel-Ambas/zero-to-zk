# Consensus
Aleo uses a novel consensus mechanism to achieve the goal of secure, resilient consensus system and instant finality for block confirmation called AleoBFT. It uses proof-of-stake (POS) ensure validators and rewarded for maintaining overall system integrity and performance.  

Aleo Network is run and maintained by three groups of participants:  
Stakers - lock up Aleo Credits (AC) to help more validators participate in consensus on the Network.  
Provers - run specialized hardwares to generate proofs and solve coinbase puzzles that help secure the Network.  
Validator - validate transactions and participate in consensus on the Network.  

Everyone can be a staker and locks up their Aleo Credits for a period of time to help support the security of Aleo Network. While the minimum amount to stake is 1 AC but it won't start earning rewards until the amount staked is at least 10 ACs. Stakers help lowering the barriers of becoming a validator by delegating their stakes to validator of their choice.

Provers are required to run specialized GPUs and CPUs to generate solutions in SNARK proofs for PoSW (Proof-of-Succint-Work) coinbase puzzles. They are rewarded based on how efficient and effective on generating solutions to the puzzles. One key thing to note is that provers do not produce blocks but they are incentivised to help improve the process of generating proofs and thus reducing costs and decreasing latency for program execution.

Validators helps secure the network through AleoBFT (to be discuss more at below) and must have at least 1 million AC of stakes. The main function of validators is to verify proofs and validate transactions before including them into a confirmed block.  


