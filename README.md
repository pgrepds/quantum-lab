# quantum-lab
My Qiskit notebook for testing the IBM Quantum Lab. It successfully run on a real quantum computer! How incredible is that? 

## Solving the Maximum-Clique Problem via reduction to SAT (since there is a solver given in the Qiskit tutorial :P)

As nicely described by Yuval Filmus [here](https://cs.stackexchange.com/questions/70531/reduction-3sat-and-clique):

> Given a simple, finite graph $G=(V,E)$ and an integer $k$. We introduce a variable $x_{iv}$ for every $1 \leq i \leq k$ and every $v \in V$. The variable $x_{iv}$ is stating that $v$ is the $i$th vertex in the clique. 

Do the following.

> 1. For each $i$, there is an $i$th vertex in the clique: 
> $$
> \bigvee_{v \in V} x_{iv}=x_{iv_1} \lor x_{iv_2} > \lor x_{iv_3} \lor \cdots \lor x_{iv_n}
> $$
> 2. For each $i,j$, the $i$th vertex is different from the $j$th vertex: for each $v \in V, \lnot x_{iv} \lor \lnot x_{jv}$.
> 3. For each non-edge $(u,v) \notin E, u,v$ cannot both belong to the clique: for each $i,j, \lnot x_{iu} \lor \lnot x_{jv}$.

We are hardcoding a test graph to the SAT problem and try to find a solution using [Grover's Algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm).

The code in [Qiskit](https://qiskit.org/) looks as follows.

```Python
expression = '(x1v1 ^ x1v2) & (x2v1 ^ x2v2) & (~x1v1 ^ ~x2v1) & (~x1v2 ^ ~x2v2)'
try:
    oracle = PhaseOracle(expression)
    problem = AmplificationProblem(oracle, is_good_state=oracle.evaluate_bitstring)
    grover = Grover(quantum_instance=QuantumInstance(provider.get_backend('ibmq_quito'), shots=10000))
    result = grover.amplify(problem)
    display(plot_histogram(result.circuit_results[0]))
except MissingOptionalLibraryError as ex:
    print(ex)
```
