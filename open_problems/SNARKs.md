---
# Research Plan for SNARKs
---

This is a list of research questions in the area of SNARKs and their applications to Filecoin System.

**[Background](#background)**

[Definitions](#definitions)

[Applications of SNARKs](#applications-of-snarks)


**[Problems and Directions]**

[Problem 1:  Scaling SNARKs](#problem-1-scaling-snarks) 

[Problem 2:  Transparent SNARKs](#problem-2-transparent-snarks) 

## Background

### Definitions  

Zero-Knowledge SNARKs (zkSNARKs) are protocols that allow for a  prover to post a statement together with a 
*short* proof, such that any  verifier can  publicly check
that the statement (e.g., correctness of a computation, claims of storage etc.) is true and this with a minimal 
communication overhead and while expending fewer resources, e.g. less time than would be required to re-execute the function computed by the prover.

More precisely, a proof system is a SNARK if it has the following properties:
 
-   **succinct**: the size of the proof is independent   
of the size of the witness, i.e. the size of the computation itself,
-   **non-interactive**:  it does not require rounds of interaction between the prover and the verifier,
-   **argument**:  we consider it secure only against adversarial  provers that have bounded computational resources,  
-   **knowledge-sound**: it is not possible for an efficient prover to convince the verifier without knowing a witness for the statement; formally, for any prover able to produce a valid proof, there is an extractor capable of extracting a witness for the statement (''the knowledge'').
-   **zero-knowledge**: optional property that enables the proof to be done without revealing anything about the intermediate steps (the witness). 
 
### Applications of SNARKs
SNARKs play a significant role both in the theory of proof systems and in
practice as essential building blocks for many cryptographic protocols.

**Decentralised Systems.**  In many Decentralised Systems there is need for
 protocols that allow users to post public information and generally, any statement to some public ledger. 
 The other users in the system should be capable of verifying that such statements are true in an efficient way. 
 zk-SNARKs are perfect tools to address this need. 

*SNARKs in Filecoin:* 
 Filecoin system relies on SNARKS to aggregate Proof of Space (PoS), which is a way for independent storage providers (miners)
 to convince the network that they are dedicating physical disk-space. 
Moreover, in Filecoin it is necessary to prove useful space, i.e. storage space that can be used to keep real-world data using a 
Proof of Replication (PoRep).    

To do so, miners run a special encoding function (the Proof of Space) which works in consecutive steps.
At the end, the miner must create a proof that they did all these computations correctly by revealing openings 
of random challenges from the verifier.
So miners just post to the chain a SNARK showing that they correctly generated the steps in the PoS to onboard storage in the network.

A scalability problem comes from the fact that proving the entire encoding steps together is not possible. 
The trusted setup of Groth16 SNARK is bounded to circuits of maximum $2^{27}$ gates. Therefore, the current PoS
is splitted into $10$ smaller circuits that use each a separate SNARK. 

The chain currently processes a large number of proofs each day: approximately 2 million Groth16 SNARK proofs, representing 60 PiB of storage. 
Fortunately, we can verify many SNARKs together using batch verification and soon new aggregation techniques for SNARKs will be implemented.  

*SNARKs in Rollup Off-Chain*
Another interesting application of SNARKs is the so-called *zero knowledge rollups*, also known as ZK-rollups, 
which bundle or "roll up" hundreds of transfers off-chain and generate a SNARK as a validity proof which is posted on the main blockchain.

To better understand the context, we briefly revise the blockchain terminology:
- Layer-1 is the term used to describe the main blockchain architecture. 
- Layer-2 is an overlaying network that lies on top of the main blockchain.
- Transactions in blockchains are cryptographically signed instructions from accounts. An account will initiate a transaction to update the state of the blockchain network.  
- Rollups are solutions that perform transaction execution outside the main chain (in layer-2) and post a proof of correct execution on layer-1
 
 ZK-rollup smart contract maintains the state of all transfers on layer-2, and this state can only be updated in layer-1 with a validity proof.
  ZK-rollups only need to post the unique validity proof to the chain, instead of all transaction data. With a ZK-rollup, 
  validating a block is quicker and cheaper because less data is included.
The sidechain (layer-2) where ZK-rollups happen can be optimised to reduce transaction size further. 


## Problems and  Directions
   
### Problem 1: Scaling SNARKs

 Multiple solutions have been developed to face the SNARKs scaling challenge. 
 The most recent and efficient is based on the notion of proof carrying data, which enables 
 fully recursive proof system: one proof can verify another proof and the level of recursion is unbounded in theory. 
 Unfortunately, this approach requires a complete new proof system which is incompatible with the current 
 Groth16 proof system used by Filecoin. 
In order to be able to scale the proofs used by Filecoin, we consider the following 2 approaches, 
one preserving current Groth16 SNARKs and the other using recursive SNARKs.
 
 **Future Goals:**
- Reducing on-chain footprint of Proof of Space even more with better tools.
- Aggregate multiple transactions together using SNARKs efficiently and securely (via SNARK-friendly signature schemes)
- Improved ZK-rollup smart contracts via aggregation, better SNARKs, circuits, etc.
 
### Problem 1: Transparent SNARKs
Removing trusted setup from SNARKS is a crucially important problem both theoretically and practically. 
While many advances have been made towards transparent SNARKs, a more ambitious goal would be to achieve: 
 - Prover Time: O(|C|) i.e. linear in circuit size
 - Proof size: O(log |C|)
 -  Verifier Time: O(n + log |C|) where n is the length of the input to the circuit (without the witness)
    
All the known candidates for transparent SNARKs are off by (at least)
some logarithmic  factor in either of those parameters, which
result in slowdowns in practical applications. 
 
