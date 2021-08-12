## What is QAOA?

QAOA is short for "Quantum Approximate Optimization Algorithm".

* Protocol for approximately solving an optimization problem on a quantum computer
* Can be implemented on near-term devices!
* Variable parameter $$p$$ (depth) -- the optimal performance increases with $$p$$ and solves the problem as $$p \to \infty$$ (by recovering adiabatic evolution)
* In order to use this algorithm optimally, you have to tune $$2p$$ parameters (this gets hard as $$p$$ grows!)
* This is a 'local' algorithm, which may limit its power.

For deeper mathematical descriptions, see [the paper](https://arxiv.org/abs/1411.4028) or tutorials ([here](https://pennylane.ai/qml/demos/tutorial_qaoa_maxcut.html) and [here](https://qiskit.org/textbook/ch-applications/qaoa.html)). Some important jargon: mixing terms, cost functions, circuit depth, Trotterization

## Performance analysis at low depth

The QAOA at low depth is easiest to analyze. Here is a table of known results:

| Problem             | QAOA depth | Graph                                       | optimal performance (satisfying fraction) | best known algorithm?               | Papers                                                                                                                                           |
|---------------------|------------|---------------------------------------------|-------------------------------------------|-------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| MAX-CUT             | 1          | triangle-free                               | $$1/2 + 0.30/\sqrt{D}$$                   | No                                  | [Wang+ 2018](https://arxiv.org/abs/1706.02998), [Hastings 2019](https://arxiv.org/abs/1905.07047)                                                |
| MAX-CUT             | 1          | any                                         | see formula                               | ? (KM project in progress)          | [Wang+ 2018](https://arxiv.org/abs/1706.02998), [Hastings 2019](https://arxiv.org/abs/1905.07047)                                                |
| MAX-CUT             | 2          | girth above 5                               | $$1/2 + 0.41/\sqrt{D}$$                   | No                                  | [Marwaha 2021](https://arxiv.org/abs/2101.05513)                                                                                                 |
| MAX-3-XOR (E3LIN2)  | 1          | no overlapping constraints, or random signs | at least $$1/2 + O(1/\sqrt{D})$$          | ? (KM project in progress)          | [Farhi+ 2015](https://arxiv.org/abs/1412.6062v2), [Barak+ 2015](https://arxiv.org/abs/1505.03424), [Lin+ 2016](https://arxiv.org/abs/1601.01744) |
| MAX-3-XOR (E3LIN2)  | 1          | any                                         | at least $$1/2 + O(1/\sqrt{D log D})$$    | so far, No                          | [Farhi+ 2015](https://arxiv.org/abs/1412.6062v2), [Barak+ 2015](https://arxiv.org/abs/1505.03424)                                                |
| SK Model            | 1-12       | infinite size                               | see formula                               | ?                                   | [Farhi+ 2019](https://arxiv.org/abs/1910.08187)                                                                                                  |
| Max Independent Set | 1          | any                                         | at least $$O(n/d)$$                       | so far, No (KM project in progress) | [Farhi+ 2020](https://arxiv.org/abs/2004.09002)                                                                                                  |

There are some other results on low-depth QAOA (perhaps could be added to the table above):
* Marika Svensson's [papers](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C5&q=author%3A%22m+svensson%22+qaoa&btnG=)
* The performance of QAOA at $$p=1$$ for a "QUBO" (quadratic cost function) is known
* Stuart Hadfield's thesis (for example, on MaxDiCut)
* [NASA QuAIL's paper](https://arxiv.org/abs/1702.02577) on Grover search with QAOA

The QAOA at low depth has some known performance limitations. For example, see [Farhi+ 2020a](https://arxiv.org/abs/2004.09002) and [Farhi+ 2020b](https://arxiv.org/abs/2005.08747), and [Bravyi+ 2019](https://arxiv.org/abs/1910.08980).

The graph structure may impact QAOA performance; see [Herrman+ 2021](https://arxiv.org/abs/2102.05997) for a study on small graphs.

## Choosing optimal parameters

At high depth, the algorithm's success is dependent on choosing the best parameters. There are different strategies to best set parameters, depending on your goal. (Note: Optimally tuning your parameters [is a NP-hard problem](https://arxiv.org/abs/2101.07267)!) Here is a table of different parameter-setting strategies:

| Strategy                         | Description                                                                                                                                 | Drawbacks                                                                                                                                                                           | References                                     |
|----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------|
| brute force                      | Pre-compute the optimal parameters. Then use optimal parameters to run the algorithm.                                                       | Although the parameters are found, this takes time exponential with the graph size.                                                                                                 | [Farhi+ 2014](https://arxiv.org/abs/1411.4028) |
| variational                      | Run the algorithm several times on a quantum computer, then use the results to choose new parameters. Repeat until you achieve convergence. | This requires using a quantum computer many times to find the right parameters. Noise in quantum computers may make it more challenging to choose improved parameters at each step. | [Farhi+ 2014](https://arxiv.org/abs/1411.4028) |
| interpolative or Fourier methods | Solve optimal beta and gamma at small p, and use that to guess optimal betas and gammas at larger p.                                        | This does not guarantee that the optimal parameters are the best possible for QAOA.                                                                                                 | [Zhou+ 2018](https://arxiv.org/abs/1812.01041) |

Barren plateaus pose a challenge to numerically calculating optimal parameters. Parameter landscapes have many local optima and a lot of "flat" regions ("barren plateaus"). This means that local changes in parameters (e.g. gradient methods) may not improve the QAOA, making it very difficult to identify optimal parameters. See [McClean+ 2018](https://arxiv.org/abs/1803.11173) and [Wang+ 2020](https://arxiv.org/abs/2007.14384).

Some studies suggest that optimal QAOA parameters can transfer from graph instance to graph instance: see [Lotshaw+ 2021](https://arxiv.org/abs/2102.06813) for study at small graphs, [Galda+ 2021](https://arxiv.org/abs/2106.07531), and [Wurtz+ 2021](https://arxiv.org/abs/2107.00677). This is also true on large graphs; see a theoretical result of instance independence in [Brandao+ 2018](https://arxiv.org/abs/1812.04170).

## Extensions to QAOA

Several adjustments to QAOA have been proposed. Some of these are new protocols that use QAOA as a subroutine; others add penalty terms, use thermal state based objectives, or adjust the mixing Hamiltonian. Here is a list:

| Extension                           | How it works | References                                                                                             |
|-------------------------------------|--------------|--------------------------------------------------------------------------------------------------------|
| ST-QAOA (Spanning Tree QAOA)        |  Changes the mixer to a sum of $$X_i X_j$$ terms on a spanning tree         | [Wurtz+ 2021a](https://arxiv.org/abs/2103.17065)                                                       |
| CD-QAOA (Counter-diabatic QAOA)     | Adds terms to the mixer to mimic corrections to adiabaticity | [Wurtz+ 2021b](https://arxiv.org/abs/2106.15645)                                                       |
| Quantum Alternating Operator Ansatz | Generalization of QAOA (parameterized unitaries and mixers; incorporating constraints into the mixers)          | [Hadfield+ 2017](https://arxiv.org/abs/1709.03489), [Hadfield+ 2021](https://arxiv.org/abs/2105.06996) |
| RQAOA (Recursive QAOA)              | Recursively generates a more and more constrained problem. Uses QAOA (or some other quantum routine) to add constraints.         | [Bravyi+ 2019](https://arxiv.org/abs/1910.08980)                                                       |
| GM-QAOA (Grover Mixer QAOA) and Th-QAOA (Threshold-based QAOA)              |  Change the mixers to "flip phase" for a marked element (GM) or above a certain threshold (Th); imitates Grover search operators | [Bärtschi+ 2020](https://arxiv.org/abs/2006.00354), [Golden+ 2021](https://arxiv.org/abs/2106.13860)                                                       |

## Open problems

* Which problems and families of instances can we show that QAOA provides advantage over classical algorithms?
* How can we analyze QAOA at higher depth? (for example, some ideas in [Hadfield+ 2021](https://arxiv.org/abs/2105.06996))
  * What happens at log depth?
  * What happens at poly depth?
* How does the QAOA performance scale with $$p$$?
* How can we better choose the QAOA parameters?
* How do we best implement constrained optimization?
  * When are penalty terms useful?
* What are the optimal choices of mixers? (comparing $$p$$, or circuit size)
* How can we best mitigate error from noise?

Figure from [Barak+ 2021](https://arxiv.org/abs/2106.05900):

![A plot of conjectured performance of classical and quantum algorithms. It asks the question if NISQ Advantage for Optimization exists.](nisq_advantage.png)

## Myths about QAOA

| Myth | Explanation |
|------|-------------|
| MAX-CUT is clearly an interesting problem for quantum advantage     |    Goemans-Williamson (SDP) is already potentially optimal if Unique Games Conjecture is true. There may be families of graphs where quantum computing can help; but other problems have much bigger gaps between best classical algorithm and limits of approximation.  |
|   VQE is the same as QAOA   | VQE is a routine for finding the minimum eigenvalue of an unknown Hamiltonian  (e.g. electronic structure problem). The variational method is required because we don't know the Hamiltonian classically. In QAOA, by contrast, the Hamiltonian is known classically, but the best solution is not known. Stuart Hadfield refers to this distinction as non-diagonal eigenvalue problem vs diagonal eigenvalue problem.|
|   The expected performance is what matters    |   You really want the highest value the QAOA returns; when you run the experiment many times, you take the best one, not the average one. But we use the average because it's easier to calculate, and bound the chance of higher performance with tail bounds. (Caveat: at high enough $$n$$ and $$p$$, there may be concentration -- basically every bitstring you sample will achieve exactly the expected performance.) The QAOA may also perform well even if the overlap with optimal value is low.<br/> There are alternative objective functions that may give more information about the distribution tail, including CVaR (Conditional Value-at-Risk) ([Barkoutsos+ 2019](https://arxiv.org/abs/1907.04769)) and feedback-based approaches ([Magann+ 2021](https://arxiv.org/abs/2103.08619)).                                                      |
|   QAOA is just adiabatic computation   |  Although there are many similarities, there are settings where QAOA improves upon adiabatic computation. See [Zhou+ 2018](https://arxiv.org/abs/1812.01041) and also [Brady+ 2021](https://arxiv.org/abs/2107.01218).   |
|   Using QAOA gives quantum advantage   |  Although sampling from a $$p=1$$ QAOA distribution is not possible classically ([Farhi+ 2016](https://arxiv.org/abs/1602.07674)), simply *using* QAOA does not necessarily mean you are calculating things classical computers cannot. In fact, there are no examples of provable quantum advantage using QAOA on an optimization problem. |
