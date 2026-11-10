
# N-Qubit Circuit Entanglement

Research adaptation by **Abiyzelalem27**, based on the original
[triple_e](https://github.com/vbelis/triple_e) project.

[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)

## Overview

This project investigates the simulation of n-qubit quantum gates and
circuits, with an emphasis on separability and entanglement analysis.

The existing triple_e package provides tools for calculating:

- Expressibility
- Entangling capability
- Effective dimension

The planned research extends this foundation with Qiskit circuit
construction, reduced-state analysis, and entanglement entropy.

## Research Objectives

- Construct and simulate parameterized quantum circuits.
- Investigate the effects of gate choices, circuit depth, and connectivity.
- Analyse separability across specified partitions.
- Calculate entanglement entropy for globally pure states.
- Compare circuit expressibility and entangling capability.
- Verify numerical results against known states and analytical predictions.

## Installation

Clone this repository:

```bash
git clone https://github.com/Abiyzelalem27/n_qubit_circuit_entanglement.git
cd n_qubit_circuit_entanglement
```

Install the local package:

```bash
python -m pip install -e .
```

If you have already cloned the repository, run the installation command
from its root folder.

The original project specifies older dependency versions. Compatibility
with your Python environment must be checked before installation.

## Usage

The Python package remains named `triple_e`.

The examples below demonstrate the original package interface for Qiskit
and PennyLane.

### Expressibility: Qiskit

```python
from triple_e import expressibility
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector


def circuit_b(weights):
    qc = QuantumCircuit(1)
    qc.h(0)
    qc.rz(weights[0], 0)
    qc.rx(weights[1], 0)
    return Statevector.from_instruction(qc)


n_params = 2
n_shots = 1000

result = expressibility(circuit_b, n_params, n_shots)
print("Expressibility:", result)
```

This single-qubit example evaluates expressibility. It does not evaluate
entanglement between qubits.

### Expressibility: PennyLane

```python
from triple_e import expressibility
import pennylane as qml


def circuit_a(params):
    qml.Hadamard(wires=0)
    qml.RZ(params[0], wires=0)
    return qml.density_matrix(0)


n_params = 1
n_shots = 1000

dev = qml.device("default.qubit", wires=1)
qnode = qml.QNode(circuit_a, dev)

result = expressibility(qnode, n_params, n_shots)
print("Expressibility:", result)
```

### Entangling Capability: Qiskit

```python
from triple_e import entanglement_capability
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector


def ghz4_circuit(_):
    qc = QuantumCircuit(4)
    qc.h(0)
    qc.cx(0, 1)
    qc.cx(0, 2)
    qc.cx(0, 3)
    return Statevector.from_instruction(qc)


n_params = 0
n_shots = 10

result = entanglement_capability(
    ghz4_circuit,
    n_params,
    n_shots,
)
print("Entangling capability:", result)
```

This circuit prepares the four-qubit GHZ state:

```text
(|0000> + |1111>) / sqrt(2)
```

The circuit has no adjustable parameters, but its function accepts an
argument to match the analysis interface.

### Entangling Capability: PennyLane

```python
from triple_e import entanglement_capability
import pennylane as qml


def separable_circuit(params):
    qml.U3(*params[:3], wires=0)
    qml.U3(*params[3:], wires=1)
    return qml.density_matrix([0, 1])


n_params = 6
n_shots = 10

dev = qml.device("default.qubit", wires=2)
qnode = qml.QNode(separable_circuit, dev)

result = entanglement_capability(
    qnode,
    n_params,
    n_shots,
)
print("Entangling capability:", result)
```

This circuit applies only local gates to an initial product state.
Its output remains separable.

## Interpretation

Entangling capability characterizes a circuit family over sampled
parameters. It does not fully describe multipartite entanglement.

For globally pure states, reduced-state entropy quantifies entanglement
across a chosen bipartition. For mixed states, reduced-state entropy alone
does not establish entanglement.

## Credits

This repository is adapted from
[vbelis/triple_e](https://github.com/vbelis/triple_e).

The original package was developed by Till Muser at the Trapped Ion
Quantum Information Group of ETH Zürich.

The expressibility and entangling-capability calculations are partially
based on code by Cenk Tüysüz.

Research adaptation and subsequent extensions in this repository:
**Abiyzelalem27**.

## References

1. S. Sim, P. D. Johnson, and A. Aspuru-Guzik.
   *Expressibility and Entangling Capability of Parameterized Quantum
   Circuits for Hybrid Quantum-Classical Algorithms.*
   Advanced Quantum Technologies, 2, 1900070 (2019).
   https://doi.org/10.1002/qute.201900070

2. A. Abbas, D. Sutter, C. Zoufal, A. Lucchi, A. Figalli, and S. Woerner.
   *The power of quantum neural networks.*
   Nature Computational Science, 1, 403–409 (2021).
   https://doi.org/10.1038/s43588-021-00084-1

## License

The original triple_e code is provided under the Apache License 2.0.
See [LICENSE](LICENSE) for the license text.