+++
title = "Understanding Quantum Circuits with Qiskit"
date = "2022-10-09"
author = "Mayukh Deb"
tags = ["video"]
+++

On this sunday, we plan to gain a basic understanding of how quantum circuits work by making the world's most glorified not gate.

The original video can be [found here](https://www.youtube.com/watch?v=tBnWG_95F9c).

## The `x` operation (i.e `not`)

The `x` operation simply flips a state.

`x|0> = 1`and `x|1> = 0`

Jargon alert: `x|0>` means "x gate applied to 0".

In fact, we can also represent this gate with matrices! In this matrix world, 0 is represented by `[[1], [0]]` and 1 is represented by `[[0], [1]]` (one-hot vectors).

The x gate can be represented by a simple matrix multiplication:

```python
import numpy as np

zero_onehot = np.array([[1], [0]])
one_onehot = np.array([[0], [1]])

class XGate:
    def __init__(self):
        self.matrix = np.array(
            [
                [0,1], 
                [1,0]
            ]
        )
    def apply(self, a):
        return np.matmul(self.matrix, a)

gate = XGate()
y = gate.apply(one_onehot) ## returns: ## array([[1],[0]]) -> one_onehot
y = gate.apply(zero_onehot) ## returns: ## array([[0],[1]]) -> zero_onehot
```

Now, let's make our first circuit in qiskit:
```python
import qiskit
from qiskit import QuantumCircuit, Aer
from qiskit.tools.visualization import plot_bloch_multivector


## create a quantum circuit with one qubit and one classical bit
circuit = QuantumCircuit(1,1)

## and now, we keep the `x` operation inside the circuit
circuit.x(0)
```

Now, let's run this circuit within a simulator (backend):

```python
## next, we would need a simulator
simulator = Aer.get_backend('statevector_simulator')
## run results in simulator
result = qiskit.execute(circuit, backend = simulator).result()
statevector = result.get_statevector()
print(statevector)
```

and we get this output:
```
Statevector([0.+0.j, 1.+0.j],
            dims=(2,))
```

We can also visualize the simple circuit :
```python
## now lets try drawing the circuit
circuit.draw(output = 'mpl')

'''
     ┌───┐
  q: ┤ X ├
     └───┘
c: 1/═════
'''
```

On top of this, we can also measure results across multiple shots

```python
## measure value of qubit 0 and store it on classical bit 0
circuit.measure([0], [0])
print(circuit)
```

We'll now be able to see a measure gate (`M` thingy) in the circuit:
```
     ┌───┐┌─┐
  q: ┤ X ├┤M├
     └───┘└╥┘
c: 1/══════╩═
           0 
```

When we run the circuit, we would now be able to track measurements across n shots:

```python
## now lets execute this on a backend
simulator = Aer.get_backend('qasm_simulator')
result = execute(circuit, backend = simulator, shots = 1024).result()
counts = result.get_counts()
print(counts)
```

The output of the above snippet would be `{'1': 1024}`, which means that every time we ran the circuit (1024 shots), the output was 1.

