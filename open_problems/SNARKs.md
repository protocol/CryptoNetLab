---
# Research Plan for SNARKs
---

This is a list of research questions in the area of SNARKs and their applications to Filecoin System. For more background, and details about the state-of-the-art see the complete [statement document](https://drive.google.com/file/d/1YCgQhl9d2xUfbv03sJVWHyLzF6DHuzQp/view?usp=sharing). 

**[Background](#background)**

[Definitions](#definitions)

[Applications of SNARKs](#applications-of-snarks)


**[Problems and Directions](#problems-and-directions)**

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
 
### Problem 2: Transparent SNARKs
Removing trusted setup from SNARKS is a crucially important problem both theoretically and practically. However, 
the cost of transparent SNARKs can generally be seen in the proof sizes and verification time.
While recently many advances have been made towards more efficient transparent SNARKs, a more ambitious goal would be to achieve such SNARKs with
better proof size and prover and verifier time. 
All the known candidates for transparent SNARKs are off by (at least) some logarithmic factor in either of the following parameters, which
result in slowdowns in practical applications. 

**Future Goals:**
 - Prover Time: O(|C|) i.e. linear in circuit size
 - Proof size: O(log |C|)
 - Verifier Time: O(n + log |C|) where n is the length of the input to the circuit (without the witness)
    
 
### Problem 3:  SNARKs with Constant Proof Size
The state of the art for SNARKs with constant proof size is 
Groth16 construction \cite{EC:Groth16}. This scheme achieves great efficiency for the verifier and proof size, 
but it relies on a trusted setup, the generation of a structured common reference string CRS. 
 A first natural question to ask is if SNARKs with constant proof size 
require a trusted setup.

Another concern in building constant-size SNARKs is that they are inherently based on non-falsifiable assumptions, as stated by  Gentry and Wichs 
in impossibility result from STOC 2011.

 Bitansky et al. show in an article from STOC 2014 that, when assuming indistinguishability obfuscation (iO), there do not exist extractable one-way functions and thus SNARKs.
  Given the recent results that establish iO under reasonable assumptions, the next important question 
is the impact these constructions have on the provable security of SNARKs.

The result of Bitanski et al. does not hold if the auxiliary input of the adversary 
(i.e. any “side information” the adversary has) is independent of the secret being extracted by the knowledge assumption
(so called “benign” auxiliary input). That
is the assumption that is made nowadays underpinning the
security of the SNARKs. However if such proofs are going to be deployed on large peer-to-peer
network it would be hard to enforce and assure the “benign” nature of such auxiliary input.

Moreover, currently there is not satisfactory security definition for SNARKs that captures the composability with 
other primitives such as signatures in larger protocols.
Considering the Knowledge Soundness (KS) in real-life scenarios where a prover has black-box access to different functionalities has 
important implications when designing SNARK schemes.
 It is therefore important to understand well the scenarios where SNARKS might be
deployed and avoid non-benign auxiliary input or black-box access to other functionalities.

Another avenue is to avoid knowledge assumptions altogether by building SNARKs for deterministic
computations (in this case the Gentry Wichs result does not apply).  
 Given that smart contracts are deterministic, there is a great motivation to improve the state of
the art in this area.
This would greatly impact the feasibility of level-2 off-chain solutions as well.

**Future Goals:**
- Do SNARKs with constant proof size require a trusted setup?
- What is the impact of iO constructions on the extractability property of SNARKs
- Extend SNARK definition to capture the setting where proofs are used in composition to other crypto primitives such as signatures 
- Design SNARKs for deterministic computations
 

### Problem 4: Multiprover Interactive Proofs
  One interesting approach to build better SNARKs is to have as a departing point a very efficient Multiprover Interactive Proofs (MIPs)
e.g. [Clover](https://eprint.iacr.org/2014/846). Clover proposes a new circuit arithmetization and a
soundness analysis that avoids repetition for better performances.
The classical compilation technique from [Bitansky and Chiesa](https://eprint.iacr.org/2012/461) show how to obtaining SNARKs from MIPs
using fully-homomorphic encryption (FHE). 
They compile any complexity preserving MIP that also has
a knowledge-soundness property into a complexity preserving SNARK under a natural but non-standard
assumption. This reduction creates a potential avenue toward
a concretely efficient SNARKs.

**Future Goals:**
- Design a more practical compiler (in particular, one not based on FHE)
- Develop a complexity preserving MIP with knowledge-soundness that with low concrete costs (few provers, few queries, etc.)
- Find MIPs and MIP-based SNARKs that are adapted to the blockchain without any loss of security or functionality
 
### Problem 5: Better Polynomial Commitments for SNARKs
Many recent SNARKs rely on polynomial commitments to achieve transparent setup at the cost of logarithmic prover time. 
The bottleneck seems to be the computation of expensive Fast Fourier Transform (FFT) in these polynomial
commitment schemes. Some recent techniques such as (single-layer)
proof composition are introduced in [Poppins](https://eprint.iacr.org/2020/1318)
to allow for constant-time verifier. In Poppins they also design a 
 new polynomial commitment scheme for evaluation-based representations of polynomials,
 an asymptotically optimal inner-product argument system, an asymptotically optimal multi-Hadamard-product argument system, and
 a new constraint system for NP that may be suited for other SNARK constructions. 
  
**Future Goals:**
- Overcome the bottleneck of the computation of expensive FFTs in these polynomial commitment. 
- Explore recent
techniques from Poppins \cite{poppins} and improve them.
- Find out how to use polynomial commitments to replace one of the
provers in MIP without incurring any of the expensive log factors. 
      
### Problem 6: SNARKs for Privacy-Preserving Computation
The problem of ensuring both the correctness and the privacy of computation performed on 
untrusted machines is also very important for many applications in the decentralised web scenario.

Early solutions are not very satisfactory in terms of efficiency and not feasible for practical use.  
 
More recent works by [Fiore et al.](https://eprint.iacr.org/2020/132) and [Bois et al.](https://eprint.iacr.org/2020/1526) 
have improve the efficiency and the expressivity of schemes that allow to veryfy computations on encrypted data, but there is still a lot of 
room for improvement before considering such schemes practical. 

**Future Goals:**
- Build more efficient proofs on encrypted data for studied FHE schemes
to ensure correctness of computation
- Study proofs on distributed data (secret shared data) and their applications to descentralised settings

 
## Problem 7: Post-Quantum SNARKs
The advent of general-purpose quantum
computers would render insecure the constructions based on factoring or discrete-log type assumptions, 
hence the necessity to consider alternatives to them. Some more desirable assumptions that are believed to withstand quantum attacks are the lattice
problems, and elliptic curve isogenies that are a lot more compact than lattices.
 Efforts were made to design new  designated-verifier post-quantum arguments
 from lattices, but there is a lot more to do in this area. 
 
**Future Goals:**
- Constructing publicly-verifiable zk-SNARKs that are post-quantum
- Study isogenies and try to build SNARKs based on isogenies

 
### Problem 8: New Levels of Security for SNARKs
  Another worth to investigate path is how to build SNARKs in alternative security models. 
  This will require to define these models and better understand the techniques proper to these (e.g. fine-grained cryptography) and apply them to the target departure schemes.

*Fine-Grained Model.*
 Fine-grained cryptographic primitives are ones that are secure against adversaries with a bounded polynomial amount of resources (time, space or parallel-time), where the honest
algorithms use less resources than the adversaries they are designed to be secure against. 
Such primitives
were previously studied in the context of time-bounded adversaries (Merkle, CACM 1978)
space-bounded adversaries (Cachin and Maurer, CRYPTO 1997) and parallel-time-bounded
adversaries (Haastad, IPL 1987).   

*Rational Adversary Model.*
Defining proofs against rational adversaries  sounds like a suited 
model to apply to the delegation of computation.
Breiefly, a system where a prover convinces a verifier 
that a certain computation y=F(x) is correct, and where cheating by the prover would require “more resources” than computing F. 
As opposed to classical cryptographic security definitions, where such cheating is either impossible or requires super-polynomial time, in this case the requirement is just that it is more expensive than computing the actual function. 
In many scenarios a rational adversary therefore would not have a motivation to cheat, as
the “cheapest” option is to compute F(x) correctly.

*Space-Bounded Model.*
Another model that it is interesting to explore is SNARKs where security is based on assuming the
adversary is space-bounded (rather than time bounded). There is a wealth of cryptographic
schemes in the bounded storage model, and some theoretical research in the area of interactive
proofs secure against either a space-bound prover or verifier. Can we build better SNARKs in this
model? Is this a realistic model? 

In the cryptographic schemes there is a “random beacon” that transmit random bits at a high rate and the assumption is that the adversary cannot store them all. Is such a model (or an approximation of it) realizable in a blockchain environment?
Can it help building better SNARKS?

**Future Goals:**
- Build a fine-grained SNARK where there is a not necessarily super-polynomial gap between the running
time of the adversary and that of honest parties (the classic example of this is a Merkle puzzle where the adversary must run in time quadratic on the running time of the honest players to break
the scheme). 
- Define a new model that has interest for practical applications and understand what are the key points to be explored to build efficient SNARKs in this model.
- Explore more scenarios where fine-grained crypto make sense (rational model, bounded storage model, etc.) and design new protocols for them. 
- Answer positively or negatively to the questions raised above.
