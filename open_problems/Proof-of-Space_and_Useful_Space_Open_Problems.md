---
# Proof of Space and Useful Space - Open Problems"
---

This document contains a list of research questions related to Proof of Space (and useful space) constructions that can have high impact for the future versions of the Filecoin protocol.

**[Terminology] **

[Proof of Space] 

[Filecoin, Useful Space and Replication] 

**[Problems and Directions]**

[Problem 1: Simple graph-labeling based PoS in the time model.] 

[Problem 2: Graph-labeling based PoS in the cost model.] 

[Problem 3: Less communication rounds for repeated audits.] 

[Problem 4: Proof of Useful Space from hash-based PoS] 

[Problem 5: Proof of Useful Space with Data Updatability]

[Problem 6: Tight hash-table based PoS construction] 

[Problem 7: Incremental Cost for Parameters Upgrades] 

[Problem 8: Verifiable Capacity Bound Functions]

[Why it is important to Filecoin] 

## Terminology 

### Proof of Space

A **Poof of Space** (PoS, see for example: [eprint 2013/796][10]) is a protocol that allows a prover to convince a verifier that he has a minimum specified amount of space (ie, used storage). More precisely in a PoS protocol we have two main sub-protocols:

-   *Initialization* (ie, setup phase): on public input N, an *advice* (eg, vector of random data) of size N is created. The advice is stored by the prover, while the verifier does not know the advice (in some protocols, the verifier may know a commitment to the advice).

-   *Execution* (ie, audit phase): the verifier and the prover run a protocol and the verifier outputs reject/accept. Accept means that the verifier is convinced that the prover stores the advice. This phase can be repeated many times.

A PoS is sound if a verifier interacting with a malicious prover who

-   Stores a fraction of the advice that has size N' \< N,

-   And runs in at most T steps during the execution phase,

outputs accept with small probability (ie, soundness error). The value (N-N')/N is called the *spacegap*.

When instantiating a PoS in a real-word protocol, we need to specify what "the malicious prover runs in at most T step" means. Two ways of doing this are:

-   *Putting a* *time bound on the prover* (for short, "time model") : ie, the verifier accepts only proofs that are produced in less than x seconds after the execution protocol starts because we estimate that T steps correspond to at least to x seconds. In other words, the proved is force to use at least N' storage;

-   *Estimating the prover's cost* (for short, "cost model"): ie, the prover is allowed to choose among running in T' \> T steps (and using \< N' storage) or using at least N' storage, however the first strategy always costs more than the second one.

Note that the "real" persistence of the space depends on the frequency of the audits (eg, if audits are not frequent enough a prover can always re-run the initialization phase between two execution phases). We call "*audit frequency"* this parameter that depends on the prover running time in the initialization phase and on the model (time bound or cost estimation) we choose.

### Filecoin, Useful Space and Replication

In Filecoin we are interested in proving *[useful]* space, that is storage space that can be used to keep real-world data. Therefore, we want the advice A of the PoS to encode some real data D, instead of just being a random incompressible sequence of bytes. This is the informal new property we are interested in each time we will talk about **Proof of Useful Space.**

A more formal way to capture the security requirements of the Filecon decentralized storage network is using a proof of useful space that is actually a **Proof of Replication** (PoRep, ref, [eprint 2018/678][11]).

Informally, this means that in addition to the space hardness property seen before for a PoS , the replica (that is the advice that now contains encoded data) has the extraction property. In other words, there is an extraction algorithm that can recover the original data from the interaction with a successful prover during the execution phases.

## Problems and Directions 

### Problem 1: Simple graph-labeling based PoS in the time model.

So far we have mainly seen two kinds of PoS constructions, one based on *graph labeling* and the other based on *hash tables*. We know that graph-labeling based constructions can achieve good asymptotic parameters and be secure in the time model: the [Stacked-DRGs][12] construction achieves both these properties. However, it comes with complicated graph-pebbling proofs (this is common to other graph-labeling construction, see [eprint 2013/796][10]), graph assumptions (e.g., security based on best known attacks on a DRG graph) and unsatisfactory practical efficiency (eg, the audit frequency is too high). Can we improve on these aspects?

#### Directions

To improve the Stacked-DRGs construction:

1.  Simplify the graph pebbling proof using a different expander graph in the construction;

2.  Find a DRG graph with better parameters; understand the practical attack that can be done on this kind of graph.

In general, find a graph that gives smaller hidden constants in the prover and verifier running time and in the proof size.

#### What and Why is important for Filecoin 

Filecon uses a PoRep that is constructed taking the advice A of a Stacked-DRGs PoS and adding to it the data D (ie, a "*replica"* R is defined as R = D + A, "additive compiler")[^1]. Improving the concrete efficiency or the security assumptions of Stacked-DRGs will overall improve the Filecoin network.

### Problem 2: Graph-labeling based PoS in the cost model.

We know that construction based on hash-tables can be used in the cost model (eg, [eprint 2016/035][13]).\
Can a graph-labeling based construction be used in this model? While researching this direction we found out the [Stacked-Expanders][14] PoS, can be used in the cost model. Indeed, adapting the pebbling analysis used for the Stacked-DRG graph for the stacked-expanders graph we are able to count the number of hash calls required to answer an audit challenge when the storage requirement is not fulfilled. Then, assuming a price for each hash call, we can estimate the cost for a malicious prover that deletes more than a fraction of the advice. However, this PoS does not achieve a good enough space-computation tradeoff, we only know that deleting a fraction larger than the spacegap requires a minimum number of hash. Can we design graph-labelling constructions, native in the cost model, with a better tradeoff? Moreover, hash-table construction has the "upgradability property": if the ratio between cost of computation and cost of storage change, the advices already created can be "upgraded" running an algorithm that costs less than re-running the whole initialization with upgraded parameters. Can we design graph-labeling constructions, native in the cost model, with the same property? [See Problem 7.][### Problem 7: Incremental Cost for Parameters Upgrades] [15]

#### Directions

1.  Find a graph that gives a granular space-computation tradeoff, that is the computation is a continue function of the space deleted (instead of a step function as described before);

    -   Note that a graph-pebbling PoS designed in the cost model could achieve a *fast initialization phase* in practice thanks to the use of parallelization (ie, the initialization algorithm that the prover has to run is highly parallelizable). On the other hand, such a result is impossible in the time model where parallelization of the computation could break the time bound.

2.  More in general, what is the best way to estimate the cost of a computation like labeling a graph using a hash function (assuming that some nodes already have labels)? Before we talked about counting the number of hash calls needed as a function of the labels stored . Another interesting approach is looking at something similar to [bandwidth hardness][16] and express the cost of the labeling operation as a tradeoff of *memory accesses*, computation (hash call) and storage. In this case the questions are: How to design a graph that can provide precise tradeoff between storage, memory access and computation for the labeling operation? And which techniques can we use to prove the tradeoff? Could we use some of the graphs used for memory-hard function analysis? Other techniques?

    -   We have a proposed graph construction, based on stacking bipartite expanders and "butterfly-style" graphs for which we know how to prove the computation/storage tradeoff and we conjecture a computation/storage/memory tradeoff. Proving this second tradeoff, will positively answer the questions posed above.

#### What and Why is important for Filecoin 

The cost model is used to argue the security of an important piece of the Filecoin system: the WindowPoSt protocol (ie, periodic repetition of the audit phase of the PoRep based on the Stacked-DRGs PoS described above). Designing a graph-labeling PoS with optimal parameters in the cost model can improve the practical efficiency of WindowPoSt (eg, less frequent check or smaller proof size). Moreover, if the new PoS has a *fast initialization phase*, this will allow faster power on-boarding (ie, the blockchain can grow faster) and shorter retrieval time for the data.

### Problem 3: Less communication rounds for repeated audits.

Repeating the execution step multiple times needs interaction between Prover and Verifier, since those proving steps need to be spaced out and can not make non-interactive using standard techniques (eg, Fiat-Shamir heuristic). Indeed, if different execution phases are not spaced out, the prover can create all the proofs at the same time, failing in proving persistence of the storage.

How can we enforce different execution proofs to be spaced out without requiring interaction between Prover and Verifier at each execution?

#### Directions

1.  One way to do so is to use VDFs. However, this primitive has some challenges for practical applications:

    -   Some VDF constructions (eg, RSA based) need a trusted setup. How do we instantiate this efficiently in a decentralized system? For example, can we design an MPC protocol that runs this teptu phase efficiently with \~1000 participants?

    -   Some VDF use not very common assumptions (eg, class group), can we improve on this?

    -   How can we aggregate multiple proofs and/or perform batched verification?

        -   Can we use SNARKs technology for this? Or in other words, are there SNARK Friendly VDF?

    -   Note that using VDFs also requires research in the area of special hardware and algorithm complexity in order to precisely know the A~max~ parameter.


1.  Are there solutions that do not use VDF?

#### What and Why is important for Filecoin

In Filecoin, storage is repeatedly audited running periodical execution phases of the Stacked-DRGs PoS. If we find a way to "pack together" several executions we can reduce the on-chain footprint of storage audit.

### Problem 4: Proof of Useful Space from hash-based PoS

We know how to compile a graph-labeling based PoS into a Proof of Useful Space using the "additive compiler". Is the same compiler good for hash-table based constructions? In particular, hash-based constructions, such as the one in [eprint 2017/893][17], are appealing because they naturally have a non-interactive initialization phase. What is the best way to embed datas inside the advice of a hash-table based PoS that maintains the non-interactivity?,

#### Directions

-   Does the concepts of catalytic space and replication (along with the examples) sketched in Pietrzak's [paper][18] help in this sense?

    -   For example: Is it possible to inject data inside the Proof of Space while running the initialization step instead of doing the addition at the end?

#### What and Why is important for Filecoin

One downside of the current Filecoin Proof of Replication based on Stacked-DRGs is represented by the need of a challenge response protocol at initialization time, where the prover has to open multiple positions of the replica in order to prove that the encoding of data has been done correctly. Getting rid of this step would be highly beneficial both for reduced on-chain footprint and for minimizing the required interaction (note that due to a non-negligible soundness error, at the moment we are not using Fiat Shamir). Moreover, constructions based on hash tables rather than graph labeling may be easier to analyze and implement.

### Problem 5: Proof of Useful Space with Data Updatability

Updating the data encoded and stored with a Proof of Useful Space can be as expensive as re-running the initialization phase, no matter how significant the update is (ie, updating a small portion of the data is as expensive as encoding all data in the replica). In particular, this is true for Filecoin where we use the "additive compiler" (Replica = Advice + Data) and the advice is constructed using a graph-labeling PoS. Can we design a proof of useful space with "cheap" updates (ie, updating data requires running an algorithm that is much more efficient than initialization)?

#### Directions

1.  "Local encoding": That is, keep the additive construction, but make the advice depending only on a small piece of data. For example, say that data are divided into blocks of 32 bytes each (or other sizes), then we use an advice that has the same block structure and for which the i-th block can be generated by re-computing few other blocks (in principle even zero other blocks). This would give much more flexibility: to update the block i in the data the prover needs to recompute only a few advice blocks instead of re-running entirely the initialization. This solution could work well with Proof of Useful Space constructed on top of hash-table based PoS. See [Problem 4][Problem 4: Proof of Useful Space from hash-based PoS].

2.  In the idea sketched above, the updated cost is linear in the size of the update, which is not ideal. As an example, this would be acceptable in case of updating a small portion of the data, but would not be any beneficial for a complete data update. For example, for Filecoin we are interested in the possibility of transitioning from a replica that encodes no useful data (eg, zeros, called "Committed Capacity, CC, sector") to a replica that encodes useful data from a client ("Real Data sector"). Can we have the initialization cost as a real one time cost per replica, no matter what happens to the data encoded into the replica itself?

    -   Note that we can update a CC sector with replica R, which encodes zeros, to a real data without re-running initialization. Indeed the prover can get a new replica that encodes D just computing R' = R + Enc(D, rand) where rand is a random seed that is revealed to the prover on;y after commD is made public on chain. However, the general update problem is not solved: it is still unclear how to update R\' to encode other real world data D' ≠ D.

#### What and Why is important for Filecoin

There are two main reasons for which this aspect is crucial:

-   Having an efficient way to update data into a sector would make the storage market much more efficient. Users could update data into a sector without incurring in the current overhead.

### Problem 6: Tight hash-table based PoS construction

While we have tight (ie, with negligible spacegap) PoS constructions based on graph-pebbling (eg, Stacked-DRGs), it is not clear if and how we can achieve such a result with hash table-based constructions.

#### What and Why is important for Filecoin

Hash tables are a way to get PoS constructions that have a simpler analysis compared with graph-labelling based constructions. If we can achieve tightness, and at the same time we can solve [Problem 4][Problem 4: Proof of Useful Space from hash-based PoS], we would have an alternative to Stacked-DRG which has a simple analysis (no need of graph assumptions) and a non-interactive initialization phase.

### Problem 7: Incremental Cost for Parameters Upgrades 

For a PoS designed in a cost model, the parameters of the initialization phase are chosen as a function of the ratio between cost of computation and cost of storage (in order to make storing the cheapest strategy to pass the adutids). If this ratio changes, we would like that the advices already created can be "upgraded" running an algorithm that costs less than re-running the whole initialization with upgraded parameters. We know of hash-table PoS that have this property (ref [eprint 2016/035][13]). Can we design a graph-labeling construction with upgradable parameters? See [Problem 2][Problem 2: Graph-labeling based PoS in the cost model.].

#### What and Why is important for Filecoin

In Filecoin we do not have a dynamic way of tuning the initialization parameters in order to face the changes of costs (the ratio computation/storage over time). As a result, if the cost of computation/storage decreases significantly, rationality of storing could not be argued anymore by using the WindowPoSt protocol (note that we have another protocol, WinningPoSt, that will like keep storing as the rational option). Having a way to upgrade our parameters for initialization based on the dynamic of computation/storage cost would solve this problem.

### Problem 8: Verifiable Capacity Bound Functions

Recently introduced by Ateniese et al., [VCBFs][19] can be described as a space analogous to VDFs. Intuitively, anyone computing a VCBF must read a minimum amount of bits in memory. The key part of a VCBF (which makes it similar to a VDF) is the fact that it is asymmetric: while evaluation is expensive, verification is efficient. This is enforced by using a Verifiable Computation (VC) scheme where the function that has to be verifiably computed is the polynomial itself. Notably, the VC scheme proposed in the paper needs to run a setup phase for each polynomial being evaluated. Moreover, the creation of the polynomial coefficients need to be computed in a trusted manner. The general question here is: can we use a VCBF to design a PoS in the cost model?

#### Directions

1.  Can we use VCBFs in order to make regeneration expensive for malicious provers? For example, take a graph-labelling based PoS and use VCBF in the place of the hash function to label the graph (in order to make this operation more expensive). Open problems:

    a.  Is the dictionary attack a problem here? If yes, can we solve this using bivariate polynomials? For instance, what about using one variable is used for the first polynomial evaluation and the second one to answer challenges?

    b.  It seems plausible that using a Kate polynomial commitment instead of the proposed VC scheme can allow for one time setup instead of per polynomial setup, without any downside regarding the applicability of the Kolmogorov bound on memory reads

    c.  Is it possible to get rid of trusted setup for polynomial coefficients? How can we mitigate this limitation (for instance, is it possible to re-randomize polynomials?)

#### Why it is important to Filecoin

Having a tool like VCBFs that can be applied to our proofs as mentioned above can be highly beneficial in terms of Proof of Space complexity. If VCBF can grant additional cost/latency each time partial reconstruction is put in place, this would dramatically simplify our analysis and possibly make our construction simpler. Moreover, since we currently are in the cost model, and memory access cost can be efficiently estimated, VCBF seems to be in principle a really nice tool to enforce rationality of storage rather than regeneration in our security model.

[^1]: The advice A depends on a commitment of the data D, commD (ie, commD is included as prefix in the hash used for the graph labelling procedure). This avoids malicious miners choosing D as D = -A and getting a compressible replica R).

  [Terminology]: #terminology
  [Proof of Space]: #proof-of-space
  [Filecoin, Useful Space and Replication]: #filecoin-useful-space-and-replication
  [Problems and Directions]: #problems-and-directions
  [Problem 1: Simple graph-labeling based PoS in the time model.]: #problem-1-simple-graph-labeling-based-pos-in-the-time-model.
  [Directions]: #directions
  [What and Why is important for Filecoin]: #what-and-why-is-important-for-filecoin
  [Problem 2: Graph-labeling based PoS in the cost model.]: #problem-2-graph-labeling-based-pos-in-the-cost-model.
  [1]: #directions-1
  [2]: #what-and-why-is-important-for-filecoin-1
  [Problem 3: Less communication rounds for repeated audits.]: #problem-3-less-communication-rounds-for-repeated-audits.
  [3]: #directions-2
  [4]: #what-and-why-is-important-for-filecoin-2
  [Problem 4: Proof of Useful Space from hash-based PoS]: #problem-4-proof-of-useful-space-from-hash-based-pos
  [5]: #directions-3
  [6]: #what-and-why-is-important-for-filecoin-3
  [Problem 5: Proof of Useful Space with Data Updatability]: #problem-5-proof-of-useful-space-with-data-updatability
  [7]: #what-and-why-is-important-for-filecoin-4
  [Problem 6: Tight hash-table based PoS construction]: #problem-6-tight-hash-table-based-pos-construction
  [8]: #what-and-why-is-important-for-filecoin-5
  [Problem 7: Incremental Cost for Parameters Upgrades]: #problem-7-incremental-cost-for-parameters-upgrades
  [9]: #what-and-why-is-important-for-filecoin-6
  [Problem 8: Verifiable Capacity Bound Functions]: #problem-8-verifiable-capacity-bound-functions
  [Why it is important to Filecoin]: #why-it-is-important-to-filecoin
  [10]: https://eprint.iacr.org/2013/796.pdf
  [11]: https://eprint.iacr.org/2018/678
  [12]: https://eprint.iacr.org/2018/702.pdf
  [13]: https://eprint.iacr.org/2016/035.pdf
  [14]: https://eprint.iacr.org/2016/333/20160521:182051
  [15]: #_cnxy1tyfqcwb
  [16]: https://eprint.iacr.org/2017/225
  [17]: https://eprint.iacr.org/2017/893
  [18]: https://eprint.iacr.org/2018/194
  [19]: https://eprint.iacr.org/2021/162.pdf
