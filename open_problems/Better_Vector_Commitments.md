---
# Towards Better Vector Commitments - Open Problems
---

This is a list of research questions in the area of Vector Commitments and their applications to Proof of Space (and useful space) in the Filecoin protocol.

**[Terminology]**

[Vector Commitments] 

[Proof of Space in Filecoin] 

**[Problems and Directions]**

[Problem 1:  Augmented Updatability and Aggregation for SVC] 

[Problem 2:  Functional Vector Commitments] 

[Problem 3:  Improving Merkle Trees Openings] 

[Problem 4:  Updatability Property for SVC] 

[Problem 5:  Cross Incremental Aggregation and Keyless-Updatability]

[Problem 6:  Assumptions and Algebraic Settings for VC]

[Problem 7:  Incrementally aggregatable SVC from prime order groups]

## Terminology 

### Vector Commitments

A **Vector commitment** (VC) first defined by (Catalano and Fiore)[https://eprint.iacr.org/2011/495.pdf] allow to commit to a sequence of values and 
later on reveal one or many values at a specific position and prove it consistent with the initial commitment.
Vector commitments are used to trade off storage (all values in a vector vs. one commitment) for bandwidth (taken up to reveal values and prove them). 
This means that the commitment and the proofs of opening should have a reduced size.

Vector Commitments can be equipped with further important features as follows:


-   *Subvector Openings* (SVC) This allows to open a committed vector at a set of positions with an opening of size independent of both the vector’s 
length and the number of opened positions.
-   *Updatability*  This allows to compute a new commitment C' to a vector that updates one position of the initially commited vector *m* to a 
different value. The computation of such C' can be performed without knowing the entire vector and more efficiently than recomputing the commitment
from scratch. Moreover, an updatable VC must also provide a method to update an opening for position i that is valid for a commitment C into a new 
opening for the same position that is valid for the updated commitment C' by only knowing the portion of the vector that is supposed to change and 
in time faster than recomputing the opening from scratch.
    - *Key-Updatability* Usually, updating commitments and openings requires some auxiliary information related to the positions that are changed.
    We will call this static auxiliary information the *update key*. Some schemes do not require at all update keys, we will consider them  keyless updatable.
    - *Hint-updatability* A weaker variant of the update need an opening for the position in which the vector update occurs.  
 
-  *Aggregation*  In an SVC, aggregation models the ability of computing an opening for a set of positions I and J starting from 
two openings and sets of positions I and J, respectively. 
    - *One-Hop Aggregation* Aggregation is said to be one-hop if one can aggregate only once, or alternatively if it is not possible to aggregate openings
    obtained through the aggregate algorithm.
    - *Incremental Aggregation* Aggregation is said to be incremental if it can be performed an unbounded number of times, and if one can also disaggregate
    any opening, i.e., from an opening for I one can compute an opening for any subset K of I.
    - *Cross-Commitment Aggregation* This notion allows to compute a succinct proof of opening for a set of positions from different vectors committed separately. 
 

### Arguments of Knowledge of Subvector Opening 

Arguments of Knowledge (AoK) for a Vector Commitment scheme comes in different flavours: 
  - *Classic AoK:* It proves knowledge of the subvector that opens a commitment at a public set of positions. 
  - *Extension of AoK:*  It can be used to prove that two commitments share a common subvector. 
  - *Commit Version of AoK:*  This is like the classic one, except that the subvector one proves knowledge of is also committed: 
   one can create a short proof that C' is a commitment to a subvector of the vector committed in C. 
 

### Applications to Proof of Space

Vector Commitments are essential in apllications to Proof of Space (Proof of Storage). 
Combined with Arguments of Knowledge of Subvector Opening (AoK) with constant-size proofs, the Vector Commitment scheme can lead to an 
efficient construction of Proof of Space for decentralized usages. 

#### Proof of Space (PoS) protocols allow a client to verify that a server is storing intactly a file via a short communication challenge-response protocol. 
More precisely, in a PoS protocol we have two main steps:
 - Initialization (Setup phase): on public input N, an advice A (eg, vector of random data) of size N is created and committed to.  
 The advice is stored by the prover, while the verifier knows only a commitment to the advice. 
 - Execution (Audit phase): the verifier and the prover run a protocol and the verifier outputs reject/accept.  
 Accept means that the verifier is convinced that the prover stores the advice. This phase can be repeated many times. 

 We require that the verifier is highly efficient in both phases, whereas 
 the prover is highly efficient in the execution phase providing it is honest and had stored and has random access to the data.
 

A PoS is sound if a verifier interacting with a malicious prover who stores a fraction of the advice that has size  N' < N,
and runs in at most $T$ steps during the execution phase, outputs accept with small probability (i.e., soundness error). 


*Keyless Proof of Space.* A PoS is said to be keyless if no secret key is needed by clients, a property useful in open systems where the 
client is a set of distrustful parties (e.g., verifiers in a blockchain) and the server may even be one of these clients. 
A classical keyless PoS is based on Merkle trees and random spot-checks, recently generalized to work with vector commitments.
A drawback of this construction is that proofs grow with the number of spot checks (and the size of the tree) and become undesirably large in 
some applications, e.g., if needed to be stored in a blockchain.  

*Filecoin System.*
As an illustrative application for a PoS, Filecoin system uses PoS to commit to an advice vector *A*.
Moreover, in Filecoin it is necessary to prove useful space, i.e. storage space that can be used to keep real-world data.  
 Proof of Replication (PoRep): When the vector to be committed encodes some real data *D*, we replace the advice with a “replica” vector *R* 
 defined as *R* = *D* + *A*.   
   
## Problems and Directions 

### Problem 1:  *Augmented Updatability and Aggregation for SVC*

In an SVC, the notion of Cross-CommitmentAggregation allows to compute a succinct proof of opening for a set of positions from different vectors
committed separately.
Moreover,  aggregation  models  the  ability  of  computing  an  opening  for  a  set  of  positions I and J starting from two openings for sets of positions
I and J respectively.
Seems that achieving both these properties for SVC is not a trivial task, and there are some limitations
when  considering  strong  requirements  all  together.    

#### Directions:
 - Better Understand Updatability and Aggregation:
  Seems that updatability is actually a more nuanced notion when considered in presence of aggregation: for example, not all VCs support updating 
  aggregated proofs. Same holds for updatability of cross-aggregated proofs. 
  Getting more insights about the relation between Aggregation and Updatability might be meaningful as a first step to 
  understand the limits of combining such properties and improve existing constructions 
  
 - Reduce  the  Size  of  Public  Parameters: 
 The  known pairing-based schemes  with  cross-commitment aggregation  rely  on  long  public  parameters.  
 They  require  public  parameters  of  size  linear  in  thesize  of  the  committed  vector.  
 The  trusted  setups  for  these  schemes  seem  to  be  compatible  withsome ceremonies performed in practice for pairing-based SNARKs like Groth16 
 (e.g. ”powers of tau”ceremonies from Zcash or from Filecoin). 
 Reducing the size of these parameters or their dependency on the length of vectors to be committed, possibly by means of aggregation 
 (not only for openings, butalso for commitments) is a interesting direction to explore in order to improve the storage-bandwidth trade-off.
 
 - Aggregation for Commitments and Openings:
 The RSA-based Key VC scheme from Tomescu et al. ([TXN20])[https://eprint.iacr.org/2020/1239] requires
 only constant-sized parameters and it achieves cross-commitments incremental aggregation for openings. 
 The drawback of these aggregation methods is that they still require the verifier to keep multiple
 commitment values around. We are interested to look at different ways to achieve more compact commitments or aggregation among commitments, 
 not only for openings. The commitments aggregation should still allow for opening positions of the committed vectors given the aggregations.
 Would be valuable to also understand what are the challenges, impossibilities or lower bounds in such a scenario.
 
 

### Problem 2: *Functional Vector Commitments* 
The only constructions of Functional VCs known today are for openings to linear functions, where messages are vectors and commitments can later 
be opened to a specific  linear  function of  the  vector  coordinates.  
We  would  like  to  extend  this property to broader classes of functions. 

#### Directions:
- Lower Bounds and Impossibilities: Understand the difficulty to construct functional VC for quadratic functions, then for other classes of functions. 
State the requirements and lower bounds for achieving such properties. 

- Arguments of Knowledge of Subvector Function Evaluation:
We can construct a functional version of AoK. This enables one to prove knowledge of a computation on the values in a subvector: one can
create two vector commitments C to m and C′ to a subvector m' of m together with a short proof that y is the result of applying f(m') computation 
to the subvector m' committed in C′.  Depending of the nature of this function, the difficulty of constructing such SVC can vary.
Some key requirements for such a scheme are:
    - pre-processing for proving the opening can be linear in the size of m' 
    - public parameters (opening keys) size is ideally sub-linear in size of vector m
    - proving time should be sub-linear in the vector size
  

### Problem 3: *Improving Merkle Trees Openings*
 The current PoS uses Merkle trees where k independent openings are aggregated via a general-purpose SNARK, using a SNARK-friendly collision-resistant 
 hash for the Merkle tree (Poseidon hash function). One interesting research direction is to optimize a SNARK for this
 particular type of problem. 

#### Directions:
- SNARK-friendly Hashing: 
We can think of replacing all the hash functions with SNARK-friendly (algebraic) hash functions. 
Unfortunately, this would result in a loss in the cost of commiting to a vector. A possible 
direction is trying to reduce this cost as much as possible while keeping
the benefits of an algebraic hash by means of memory-computation tradeoffs, pre-computations or by designing better hashes. 

- Tailored SNARKs for Merkle Trees: 
General-purpose SNARKs fo not fit the best this application. Another approach would be to 
design a SNARK specially for aggregating Merkle tree paths (the path needed for opening a subvector)

- SNARK-friendly q-ary hash functions: 
Replacing binary hash functions in Merkle trees would reduce the height of the Merkle tree and thus the number 
of hash function invocations in the SNARK circuit.
Algebraic hashes like Poseidon, to some extend, are more efficient in such a setting. But can we do better and develop new hashes ?

- Preprocessing memory-computation trade-off:
When opening Merkle tree commitments we can avoid the logarithmic overhead by either storing some levels of the tree, 
or portions of the path from leaves to the root. Finding the best trade-off in such scenarios is important for PoS applications. 

##Problem 4: *Updatability Property for SVC* 
While we know a few SVC schemes that have constant-size parameters, none of them is updatable but they are only hint-updatable.
Hint-updatability essentially requires more interaction to perform an update as a user should first obtain an opening for the position that changes, 
before performing its update locally.
The lattice-based construction from ([PSTY13])[https://www.iacr.org/archive/eurocrypt2013/78810351/78810351.pdf] has a strong updatability property, 
not requiring  any type  of  key, while it does not support neither subvector openings, nor any form of aggregation. 
Overcoming these limitations for known schemes is a further step to achieve better VC.

##Problem 5: *Cross Incremental Aggregation and Keyless-Updatability*
Seems that building VC with keyless-updatability and cross-commitment incremental  aggregationis still an open problem.  
No scheme with constant-size public parameters that achieves these two properties simultaneously is known to date. 
  
##Problem 6: *Assumptions and Algebraic Settings for VC* 

   - Lattice Assumptions: There is little work done to construct VCs based on lattice assumptions.  
    Papamanthou et al. instantiate a homomorphic Merkle tree construction using Ajtai's hash function to obtain a lattice-based VC 
    scheme but this has still logarithmic-size openings. A more challenging problem is finding a lattice-based scheme with constant-size openings. 
    A possible approach is to start from understanding the existing lattice-based accumulators with constant-sized proofs. 
    Turning them into a strongly-binding vector commitment would probably require 
    some extra challenging work.
   - DLog Groups for VC: All existing concise VC schemes are based either on groups of unknown order or on bilinear groups.
     It is thus an open question to find a VC that can be based on the discrete-logarithm assumption or a related one, 
     like CDH in groups without pairings.
     Bulletproofs is an example of such VC scheme with logarithmic-size openings. 
     The challenge is to obtain concise VCs with preferable constant size openings from DLog assumptions. 
      
##Problem 7: *Incrementally Aggregatable SVC from Prime Order Groups* 
Almost all known constructions for SVC are based on bilinear groups or group of unknown order. 
Would be interesting to explore constructing such schemes from discrete log assumptions in prime order groups. 
Such a scheme may be appealing for efficiency purposes since unknown order groups are typically less efficient than prime order groups.

  [Terminology]: #terminology
  [Vector Commitments]: #vc
  [Applications to Proof of Space]: #PoS
  [Problems and Directions]: #problems-and-directions
