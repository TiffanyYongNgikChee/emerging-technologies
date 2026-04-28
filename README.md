# Emerging Technologies

> Classical and quantum implementations of the Deutsch–Jozsa algorithm, built from first principles using Python and Qiskit.

---

## Table of Contents

- [About](#about)
- [Algorithm Overview](#algorithm-overview)
- [Repository Structure](#repository-structure)
- [Environment](#environment)
- [Installation](#installation)
- [Usage](#usage)
- [Dependencies](#dependencies)
- [References](#references)
- [Author](#author)
- [License](#license)

---

## About

This repository is a Jupyter Notebook submission for the **Emerging Technologies** module. It explores the **[Deutsch–Jozsa algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm)** — one of the earliest proofs that a quantum computer can outperform a classical one for a specific task. The notebook is structured as five interconnected problems, starting from pure Python and progressing to full quantum circuits simulated with IBM's Qiskit framework.

### Before We Begin

### Deutsch and the Turing Machine — Where This All Started

Before there were quantum computers, computers were understood through a thought experiment called the **[Turing machine](https://en.wikipedia.org/wiki/Turing_machine)**, invented by [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing) in 1936. A Turing machine is an imaginary device that reads and writes symbols on a tape, one step at a time. Turing proved that any computation a real computer can do, this simple machine can also do — it became the universal definition of "what computers can compute." Every classical computer you have ever used — phone, laptop, ATM — is essentially a Turing machine running very fast.

In 1985, British physicist **[David Deutsch](https://en.wikipedia.org/wiki/David_Deutsch)** asked a deeper question: *what if the tape could be quantum?* He replaced Turing's classical tape with quantum mechanics — superposition, interference, entanglement — and described the first **[quantum Turing machine](https://en.wikipedia.org/wiki/Quantum_Turing_machine)**. This was the theoretical birth of quantum computing. Deutsch was the first person to prove that a quantum computer is strictly more powerful than a classical one for certain tasks, not just faster hardware but a fundamentally different model of computation.

From that theoretical foundation, Deutsch designed the first concrete quantum algorithm — **[Deutsch's algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm)** — which checks whether a function always returns the same output using only *one* query instead of two. In 1992, he and [Richard Jozsa](https://en.wikipedia.org/wiki/Richard_Jozsa) generalised it to functions with many inputs, creating the **Deutsch–Jozsa algorithm** that this notebook implements. It is the direct descendant of the idea Deutsch first sketched on the theoretical foundations that Turing laid.

---

### IBM — From ThinkPads and Mainframes to Quantum

**[IBM](https://en.wikipedia.org/wiki/IBM)** (International Business Machines) has been at the centre of computing for over a century.

- **Hardware roots** IBM built the computers that powered the Apollo missions and the first airline reservation systems. Their **[mainframe computers](https://en.wikipedia.org/wiki/IBM_mainframe)** still run the world's financial infrastructure today — an estimated 95 % of ATM transactions, 80 % of the world's credit card transactions, and nearly all of the world's major stock exchanges pass through IBM mainframes every day ([IBM, 2023](https://www.ibm.com/thought-leadership/institute-business-value/en-us/report/mainframe)).
- **ThinkPad** IBM introduced the **[ThinkPad laptop](https://en.wikipedia.org/wiki/ThinkPad)** in 1992. It became the most iconic business laptop of its generation — used everywhere from boardrooms to the International Space Station. IBM sold the ThinkPad line to [Lenovo](https://en.wikipedia.org/wiki/Lenovo) in 2005, but its legacy shaped how the world works on portable computers.
- **Banking and business** IBM's software and cloud services handle the back-end of banking, insurance, logistics, and healthcare worldwide. When you tap your bank card or transfer money, the systems verifying that transaction almost certainly ran on IBM technology at some point in the chain.
- **Why quantum?** The problems that matter most to IBM's clients — finding the lowest-energy drug molecule, optimising global supply chains, breaking or protecting encryption — are too hard for even the fastest classical computers. Quantum computing offers a path to solving them. In 2016 IBM launched **[IBM Quantum](https://www.ibm.com/quantum)** (originally IBM Q Experience), the world's first publicly accessible quantum computer over the cloud. Any researcher, student, or business can submit a quantum circuit and have it run on real quantum hardware in IBM's data centres. This notebook uses IBM's open-source quantum software framework, **[Qiskit](https://qiskit.org/)**, to simulate that same hardware locally.

---

## Algorithm Overview

The Deutsch–Jozsa algorithm determines whether a Boolean function is **constant** (always returns the same output) or **balanced** (returns `True` for exactly half of all inputs). A classical computer needs up to $2^{n-1}+1$ queries in the worst case; the quantum algorithm answers with a **single query**, regardless of how many input bits there are.

| Problem | Topic | Approach |
|:---:|:---|:---|
| 1 | Generating Random Boolean Functions | Classical Python |
| 2 | Classical Testing | Classical (worst-case $2^{n-1}+1$ queries) |
| 3 | Quantum Oracles | Qiskit circuit construction |
| 4 | Deutsch's Algorithm (1-bit) | Quantum — 1 query |
| 5 | Deutsch–Jozsa (4-bit) | Quantum — 1 query |

---

## Repository Structure

```
emerging-technologies/
├── problems.ipynb      # Main notebook — all five problems
├── requirements.txt    # Direct Python dependencies
├── .gitignore          # Standard Python/Jupyter ignore rules
└── README.md           # This file
```

---

## Environment

| Tool | Version |
|:---|:---|
| Python | 3.12.1 |
| Qiskit | 2.3.0 |
| qiskit-aer | 0.17.2 |
| JupyterLab | latest via `requirements.txt` |
| OS (dev) | Ubuntu 24.04 (GitHub Codespaces) |

> The notebook was developed and tested inside a **[GitHub Codespaces](https://github.com/features/codespaces)** dev container. It will also run in any standard Python 3.10+ environment with the dependencies installed.

---

## Installation

**1. Clone the repository**

```bash
git clone https://github.com/TiffanyYongNgikChee/emerging-technologies.git
cd emerging-technologies
```

**2. Create and activate a virtual environment** *(recommended)*

```bash
python3 -m venv .venv
source .venv/bin/activate        # macOS / Linux
.venv\Scripts\activate           # Windows
```

**3. Install dependencies**

```bash
pip install -r requirements.txt
```

---

## Usage

**Launch JupyterLab**

```bash
jupyter lab
```

Then open `problems.ipynb` and run all cells top to bottom (**Kernel → Restart Kernel and Run All Cells**).

No real quantum hardware is required — all circuits are simulated locally using IBM's [Aer simulator](https://qiskit.github.io/qiskit-aer/).

---

## Dependencies

All direct dependencies are listed in [`requirements.txt`](requirements.txt). Key packages:

| Package | Purpose |
|:---|:---|
| `qiskit[visualization]` | Quantum circuit construction and drawing |
| `qiskit-aer` | Local quantum circuit simulation |
| `numpy` | Numerical array operations |
| `matplotlib` | Circuit diagrams and plots |
| `scipy` / `sympy` | Mathematical utilities |

> **Note on `pip freeze`:** `requirements.txt` lists only direct dependencies without pinned versions, keeping it readable and cross-platform. For a fully reproducible locked environment, use [`pip-tools`](https://github.com/jazzband/pip-tools) (`pip-compile`) to generate a pinned lockfile from this file.

---

## References

References are grouped by the section of the notebook they directly support. Each entry includes a note explaining *why* it was used and how it connects to this submission, along with comparisons to similar works where relevant.

---

### Introduction & Background

**Turing, A. M. (1936). *On Computable Numbers, with an Application to the Entscheidungsproblem.* Proceedings of the London Mathematical Society. [doi:10.1112/plms/s2-42.1.230](https://doi.org/10.1112/plms/s2-42.1.230)**
> The foundational paper that defined what it means to compute. Turing's abstract machine became the universal baseline for classical computation, and every classical algorithm in Problems 1 and 2 is — in the Turing sense — a sequence of deterministic state transitions. The quantum model in Problems 3–5 is a deliberate departure from this classical model, making this the natural conceptual starting point for the notebook.

**Deutsch, D. (1985). *Quantum theory, the Church–Turing principle and the universal quantum computer.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1985.0070](https://doi.org/10.1098/rspa.1985.0070)**
> The paper that launched quantum computing as a field. Deutsch extended Turing's model to quantum mechanics, defining the quantum Turing machine and proving for the first time that a quantum computer can solve certain problems no classical Turing machine can solve efficiently. The single-bit algorithm implemented in Problem 4 is taken directly from this paper.

**Deutsch, D. & Jozsa, R. (1992). *Rapid solution of problems by quantum computation.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1992.0167](https://doi.org/10.1098/rspa.1992.0167)**
> The primary source for the algorithm this entire notebook implements. Deutsch and Jozsa generalised the 1985 single-bit result to functions of any number of bits, proving a single quantum query suffices regardless of input size. Problem 5 is a direct implementation of the *n* = 4 case described in this paper.

**Nielsen, M. A. & Chuang, I. L. (2010). *Quantum Computation and Quantum Information.* Cambridge University Press. [cambridge.org](https://www.cambridge.org/highereducation/books/quantum-computation-and-quantum-information/01E10196D0A682A6AEFFEA52D53BE9AE)**
> The standard textbook in quantum computing. Used throughout Problems 3–5 for the mathematical treatment of unitary gates, quantum interference, oracle circuits, and the Deutsch–Jozsa proof. *Compared to the original 1992 paper, Nielsen & Chuang provides a more pedagogical treatment with worked examples. Both are cited because the original paper establishes priority and the textbook clarifies the modern circuit formulation.*

**IBM. (2023). *Why mainframe?* IBM Institute for Business Value. [ibm.com/thought-leadership/mainframe](https://www.ibm.com/thought-leadership/institute-business-value/en-us/report/mainframe)**
> Cited in the "Before We Begin" section to support the claim that IBM mainframes process the majority of global ATM and credit card transactions. Included to contextualise why IBM's pivot to quantum computing is significant — it is a company whose classical hardware already underpins global financial infrastructure.

**IBM Quantum. (2016–present). *IBM Quantum Experience.* [ibm.com/quantum](https://www.ibm.com/quantum)**
> IBM Quantum is the cloud platform that first made real quantum hardware publicly accessible in 2016. This submission uses IBM's open-source software layer (Qiskit) to simulate circuits that could also be run on IBM's real quantum hardware. *Alternative cloud quantum platforms exist — Google Quantum AI and Amazon Braket — but IBM Quantum was the first public cloud quantum service and remains the dominant platform for educational use.*

**Qiskit Contributors. (2023). *Qiskit: An open-source framework for quantum computing.* [qiskit.org](https://qiskit.org/)**
> Qiskit is the software framework used throughout Problems 3–5 to construct, simulate, and visualise quantum circuits. It is the implementation layer between the mathematical formalism and runnable code. *Compared to Google's Cirq or Amazon's Braket SDK, Qiskit has the largest community, the most educational resources, and native integration with IBM's hardware — the primary reasons it was chosen.*

**Wikipedia. *Quantum Turing machine.* [en.wikipedia.org/wiki/Quantum_Turing_machine](https://en.wikipedia.org/wiki/Quantum_Turing_machine)**
> Cited in the "Before We Begin" section to explain Deutsch's 1985 extension of the classical Turing machine to quantum mechanics. Provides background for readers unfamiliar with the theoretical model that precedes the circuit model used in the submission.

**Wikipedia. *ThinkPad.* [en.wikipedia.org/wiki/ThinkPad](https://en.wikipedia.org/wiki/ThinkPad)**
> Cited in the IBM history segment to support the claim that IBM introduced the ThinkPad in 1992. Provides factual grounding for the narrative about IBM's evolution from classical hardware to quantum.

---

### Problem 1 — Generating Random Boolean Functions

**IBM Quantum Learning. *Measuring Probabilistic States.* [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information/single-systems/classical-information#measuring-probabilistic-states)**
> The pedagogical structure of Problem 1's background theory — introducing probability vectors and the measurement update rule before any quantum content — follows the approach used in IBM's own Quantum Learning course. Cited to show this mathematical framework is consistent with how IBM teaches the same concepts as a precursor to quantum states.

**IBM Quantum Learning. *Classical states and probability vectors.* [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information/single-systems/classical-information#classical-states-and-probability-vectors)**
> Used alongside the above IBM Learning page as the source for the column-vector representation of classical probabilistic states. The two IBM Learning pages together define the full progression from classical state → probability vector → standard basis vector that forms the mathematical backbone of Problem 1.

**Watrous, J. *The Theory of Quantum Information.* University of Waterloo. [cs.uwaterloo.ca/~watrous/TQI](https://cs.uwaterloo.ca/~watrous/TQI/)**
> Watrous's notes use the same column-vector / bra–ket notation adopted in this submission. Referenced when introducing `|0⟩` and `|1⟩` as column vectors. *Compared to Nielsen & Chuang, Watrous takes a more rigorous linear-algebraic approach; his framework was preferred here for the mathematical foundations sections to ensure consistency of notation.*

**Wikipedia. *Boolean function.* [en.wikipedia.org/wiki/Boolean_function](https://en.wikipedia.org/wiki/Boolean_function)** · **Wikipedia. *Boolean data type.* [en.wikipedia.org/wiki/Boolean_data_type](https://en.wikipedia.org/wiki/Boolean_data_type)**
> The Deutsch–Jozsa problem is defined over functions from Boolean inputs to Boolean outputs. These articles define the domain precisely and support the discussion in Problem 1 of how `True`/`False`, `1`/`0`, and truthy/falsy values are all representations of the same two-valued logic.

**Wikipedia. *Bra–ket notation.* [en.wikipedia.org/wiki/Bra%E2%80%93ket_notation](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation)** · **Wikipedia. *Linear algebra.* [en.wikipedia.org/wiki/Linear_algebra](https://en.wikipedia.org/wiki/Linear_algebra)** · **Wikipedia. *Basis (linear algebra).* [en.wikipedia.org/wiki/Basis_(linear_algebra)](https://en.wikipedia.org/wiki/Basis_(linear_algebra))** · **Wikipedia. *Standard basis.* [en.wikipedia.org/wiki/Standard_basis](https://en.wikipedia.org/wiki/Standard_basis)**
> These four articles collectively define the mathematical vocabulary used throughout the notebook. `|0⟩` and `|1⟩` are standard basis vectors; all quantum states are linear combinations of them; all gates are linear maps. Cited at the point in Problem 1 where this notation is first introduced, before it is used in quantum contexts.

**Wikipedia. *Probability vector.* [en.wikipedia.org/wiki/Probability_vector](https://en.wikipedia.org/wiki/Probability_vector)** · **Wikipedia. *Stochastic matrix.* [en.wikipedia.org/wiki/Stochastic_matrix](https://en.wikipedia.org/wiki/Stochastic_matrix)**
> A probability vector encodes the state of a classical probabilistic bit; a stochastic matrix transforms one valid probability vector into another. These are the classical analogues of quantum state vectors and unitary matrices. Establishing these classical concepts in Problem 1 makes the quantum analogues in Problem 3 less surprising. *The stochastic / unitary parallel is a key pedagogical device in this submission that is not always made explicit in other introductory quantum computing materials.*

**Wikipedia. *Bayesian probability.* [en.wikipedia.org/wiki/Bayesian_probability](https://en.wikipedia.org/wiki/Bayesian_probability)** · **Wikipedia. *Quantum Bayesianism.* [en.wikipedia.org/wiki/Quantum_Bayesianism](https://en.wikipedia.org/wiki/Quantum_Bayesianism)**
> Cited in Problem 1's discussion of measurement: probability vectors describe an observer's *knowledge* rather than an objective physical state. The Bayesian framing is used to explain why different observers can legitimately hold different probability vectors for the same system. QBism (Quantum Bayesianism) shows this interpretation extends naturally into quantum mechanics.

**Wikipedia. *Cryptographic hash function.* [en.wikipedia.org/wiki/Cryptographic_hash_function](https://en.wikipedia.org/wiki/Cryptographic_hash_function)** · **Wikipedia. *SHA-2.* [en.wikipedia.org/wiki/SHA-2](https://en.wikipedia.org/wiki/SHA-2)**
> Used in Problem 1 to provide a concrete real-world example of why balanced Boolean functions matter: good cryptographic hash functions distribute their output bits as close to 50/50 as possible — the same "balanced" property being formalised here. This grounds an abstract definition in a practically significant application.

**Wikipedia. *Fisher–Yates shuffle.* [en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)**
> Python's `random.shuffle` — used in `random_constant_balanced` to generate balanced function tuples — implements the Fisher–Yates algorithm, which guarantees a truly uniform random permutation. Any bias in the shuffle would mean certain balanced functions are more likely than others, violating the "uniformly at random" requirement of Problem 1.

**Wikipedia. *Closure (computer programming).* [en.wikipedia.org/wiki/Closure_(computer_programming)](https://en.wikipedia.org/wiki/Closure_(computer_programming))** · **Real Python. *Python Inner Functions — Closures and Factory Functions.* [realpython.com](https://realpython.com/inner-functions-what-are-they-good-for/#closures-and-factory-functions)**
> The `fclosure` function wraps each oracle using a Python closure to capture the randomly generated tuple. This is the standard pattern for implementing oracle black-box functions in Python — the closure hides its internal representation from the caller, mirroring the "black box" model in query complexity theory. The Real Python article is cited as a practical reference for this pattern. *Compared to using a class-based callable, the closure approach is lighter and more idiomatic for single-function oracles.*

**Wikipedia. *Variadic function.* [en.wikipedia.org/wiki/Variadic_function](https://en.wikipedia.org/wiki/Variadic_function)** · **Python Software Foundation. *Argument unpacking (`*args`).* [docs.python.org/3/tutorial/controlflow.html#unpacking-argument-lists](https://docs.python.org/3/tutorial/controlflow.html#unpacking-argument-lists)** · **PEP 3132.* [python.org/dev/peps/pep-3132](https://www.python.org/dev/peps/pep-3132/)** · **Real Python. *`*args` and `**kwargs`.* [realpython.com/python-kwargs-and-args](https://realpython.com/python-kwargs-and-args/)**
> The oracle functions in Problem 1 use `*args` to accept any number of Boolean inputs — a design choice that allows the same oracle interface to work for 1-bit (Problem 4) and 4-bit (Problem 5) inputs without modification. These four sources define the `*args` syntax, its specification history in PEP 3132, and practical usage.

**Python Software Foundation. *Python Data Model.* [docs.python.org/3/reference/datamodel.html](https://docs.python.org/3/reference/datamodel.html)**
> Cited in Problem 1 when explaining Python's reference-counting memory model and why the closure's captured variables persist beyond the outer function's scope. Relevant because the oracle's captured tuple must live as long as the oracle object itself.

**Wikipedia. *Discrete uniform distribution.* [en.wikipedia.org/wiki/Discrete_uniform_distribution](https://en.wikipedia.org/wiki/Discrete_uniform_distribution)**
> The oracle sampling in `random_constant_balanced` and `random_one_bit_function` must pick uniformly at random from the set of valid functions. The discrete uniform distribution is the formal mathematical description of this requirement and is cited to justify why `random.choice` and `random.shuffle` are the correct tools.

**Wikipedia. *Query complexity.* [en.wikipedia.org/wiki/Query_complexity](https://en.wikipedia.org/wiki/Query_complexity)** · **Aaronson, S. *Quantum Algorithm Zoo.* [quantumalgorithmzoo.org](https://quantumalgorithmzoo.org/)**
> Both sources define the oracle/query model that underpins the Deutsch–Jozsa problem. Query complexity is the formal framework for counting how many times an algorithm must call the oracle. The Quantum Algorithm Zoo catalogues all known quantum speedups, placing Deutsch–Jozsa in context as the earliest and simplest example. *Unlike Nielsen & Chuang, the Zoo provides a searchable catalogue useful for understanding which other problems share the same query-complexity separation.*

---

### Problem 2 — Classical Testing

**Wikipedia. *Oracle machine.* [en.wikipedia.org/wiki/Oracle_machine](https://en.wikipedia.org/wiki/Oracle_machine)**
> Problem 2 frames the constant-vs-balanced decision problem in the oracle model: the function is a black box that can only be learned by calling it. This article explains the formal setting in which classical and quantum query complexities are compared — the entire point of Problems 2–5 is to show that quantum wins in this model.

**Wikipedia. *Constant function.* [en.wikipedia.org/wiki/Constant_function](https://en.wikipedia.org/wiki/Constant_function)** · **Wikipedia. *Deutsch–Jozsa algorithm — Problem statement.* [en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm#Problem_statement](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm#Problem_statement)**
> The definitions of "constant" (always same output) and "balanced" (exactly half of outputs are 1) used throughout this submission come directly from the formal problem statement on Wikipedia. Using the canonical definitions ensures Problem 2's classical baseline tests exactly the same decision problem that the quantum algorithm in Problems 4–5 solves.

**Wikipedia. *Binomial coefficient.* [en.wikipedia.org/wiki/Binomial_coefficient](https://en.wikipedia.org/wiki/Binomial_coefficient)**
> The number of distinct balanced functions on $n$ bits is $\binom{2^n}{2^{n-1}}$. Cited to explain why the space of valid oracles is astronomically large and why uniform random sampling (rather than enumeration) is the only tractable approach in Problem 1. For $n=4$ this is $\binom{16}{8} = 12{,}870$ balanced functions.

**Wikipedia. *First-class function.* [en.wikipedia.org/wiki/First-class_function](https://en.wikipedia.org/wiki/First-class_function)**
> The classical testing in Problem 2 passes oracle functions as arguments to `try_all` and `deutsch_jozsa_classical`. This is only possible because Python treats functions as first-class objects. Cited to make this design choice explicit and to connect it to the broader concept in language theory.

**Python Software Foundation. *random.choice.* [docs.python.org/3/library/random.html#random.choice](https://docs.python.org/3/library/random.html#random.choice)**
> Used in `random_one_bit_function` to select a random function from the list of all one-bit functions with uniform probability. Cited as the authoritative documentation for the uniformity guarantee of `random.choice`.

**Python Software Foundation. *itertools — Functions creating iterators for efficient looping.* [docs.python.org/3/library/itertools.html](https://docs.python.org/3/library/itertools.html)**
> `itertools.product` is used in `try_all` to enumerate all $2^n$ binary input tuples. This is the classical brute-force enumeration that Problem 2 performs and that the quantum algorithm renders unnecessary. Cited as the authoritative documentation for this standard-library function.

**Wikipedia. *Cartesian product.* [en.wikipedia.org/wiki/Cartesian_product](https://en.wikipedia.org/wiki/Cartesian_product)**
> `itertools.product({0,1}, repeat=n)` computes $\{0,1\}^n$, the Cartesian product of the two-element set with itself $n$ times. This generates all $2^n$ binary input tuples — the set over which Problem 2's classical testing must exhaustively evaluate the oracle.

**Wikipedia. *Brute-force search.* [en.wikipedia.org/wiki/Brute-force_search](https://en.wikipedia.org/wiki/Brute-force_search)**
> `deutsch_jozsa_classical` implements brute-force search over the input space, requiring $O(2^n)$ oracle queries in the worst case. This exponential classical cost is the benchmark against which the quantum algorithm's constant-query solution ($O(1)$) is compared throughout Problems 4 and 5.

---

### Problem 3 — Quantum Oracles

**Wikipedia. *Qubit.* [en.wikipedia.org/wiki/Qubit](https://en.wikipedia.org/wiki/Qubit)** · **Wikipedia. *Quantum state.* [en.wikipedia.org/wiki/Quantum_state](https://en.wikipedia.org/wiki/Quantum_state)**
> Problem 3 introduces the qubit as the quantum analogue of the classical bit. These articles define the qubit formally as a unit vector in a two-dimensional complex Hilbert space. The classical-to-quantum transition in Problem 3 is built on the parallel between probability vectors (classical) and quantum state vectors (quantum) established in Problem 1.

**Wikipedia. *Quantum superposition.* [en.wikipedia.org/wiki/Quantum_superposition](https://en.wikipedia.org/wiki/Quantum_superposition)** · **Science Exchange, Caltech. *Quantum Superposition.* [scienceexchange.caltech.edu](https://scienceexchange.caltech.edu/topics/quantum-science-explained/quantum-superposition)**
> Superposition is the mechanism that allows the Deutsch–Jozsa algorithm to query all inputs simultaneously. Problem 3 explains this using the Hadamard gate, which places a qubit in an equal superposition of `|0⟩` and `|1⟩`. The Caltech Science Exchange article is cited as an accessible lay explanation alongside the more formal Wikipedia definition.

**Wikipedia. *Complex number.* [en.wikipedia.org/wiki/Complex_number](https://en.wikipedia.org/wiki/Complex_number)** · **Wikipedia. *Wave function.* [en.wikipedia.org/wiki/Wave_function](https://en.wikipedia.org/wiki/Wave_function)** · **Wikipedia. *Euler's formula.* [en.wikipedia.org/wiki/Euler%27s_formula](https://en.wikipedia.org/wiki/Euler%27s_formula)**
> Quantum amplitudes are complex numbers. Problem 3 explains why: wave functions are built from sine and cosine waves, and complex numbers are the natural language for rotation. Euler's formula $e^{i\theta} = \cos\theta + i\sin\theta$ connects the two. These three articles support the section bridging real-valued probability vectors and complex-valued quantum state vectors.

**Wikipedia. *Probability amplitude.* [en.wikipedia.org/wiki/Probability_amplitude](https://en.wikipedia.org/wiki/Probability_amplitude)**
> Quantum state vectors use complex amplitudes rather than real probabilities. A measurement outcome's probability is the squared modulus of its amplitude. This is the key difference between the stochastic-matrix model of Problem 1 and the unitary-matrix model of Problem 3 — cited to define this distinction precisely.

**Wikipedia. *Unitary matrix.* [en.wikipedia.org/wiki/Unitary_matrix](https://en.wikipedia.org/wiki/Unitary_matrix)**
> All quantum gates are unitary matrices. A matrix is unitary if $U^\dagger U = I$, which guarantees that applying the gate preserves the norm of the state vector — the quantum analogue of stochastic matrices preserving the sum-to-1 constraint in the classical case. Every gate used in Problems 3–5 (H, X, CX) satisfies this condition.

**Wikipedia. *Quantum logic gate.* [en.wikipedia.org/wiki/Quantum_logic_gate](https://en.wikipedia.org/wiki/Quantum_logic_gate)** · **Wikipedia. *Hadamard matrix.* [en.wikipedia.org/wiki/Hadamard_matrix](https://en.wikipedia.org/wiki/Hadamard_matrix)** · **Wikipedia. *Jacques Hadamard.* [en.wikipedia.org/wiki/Jacques_Hadamard](https://en.wikipedia.org/wiki/Jacques_Hadamard)**
> The H (Hadamard), X (Pauli-X/NOT), and CX (CNOT) gates introduced in Problem 3 are the only gates needed for the entire Deutsch–Jozsa circuit. These articles define their matrices and properties. The Hadamard gate is the most important — it creates superposition and later performs the interference step. Named after the French mathematician Jacques Hadamard, whose matrix construction appears throughout signal processing.

**Wikipedia. *Controlled NOT gate.* [en.wikipedia.org/wiki/Controlled_NOT_gate](https://en.wikipedia.org/wiki/Controlled_NOT_gate)**
> The CNOT gate is the two-qubit gate used inside `build_dj_oracle` to implement XOR-based phase encoding. Flips the target qubit only when the control qubit is `|1⟩`, implementing $|x\rangle|y\rangle \mapsto |x\rangle|y \oplus f(x)\rangle$. This is cited to define the gate matrix and explain its role in the oracle construction.

**Wikipedia. *Reversible computing.* [en.wikipedia.org/wiki/Reversible_computing](https://en.wikipedia.org/wiki/Reversible_computing)** · **Wikipedia. *AND gate.* [en.wikipedia.org/wiki/AND_gate](https://en.wikipedia.org/wiki/AND_gate)**
> All quantum gates must be reversible (unitary), which rules out irreversible classical gates like AND. Problem 3 explains why the oracle takes the form $|x\rangle|y\rangle \mapsto |x\rangle|y \oplus f(x)\rangle$ — the only way to embed an irreversible Boolean function in a reversible quantum circuit. The AND gate is used as a concrete example of a classically irreversible operation.

**Wikipedia. *Kronecker product.* [en.wikipedia.org/wiki/Kronecker_product](https://en.wikipedia.org/wiki/Kronecker_product)**
> Multi-qubit state vectors and multi-qubit gate matrices are formed by Kronecker (tensor) products of single-qubit vectors and matrices. Applying $n$ independent Hadamard gates simultaneously is represented as $H^{\otimes n}$. This is the mathematical operation behind "all inputs at once" superposition.

**Wikipedia. *Measurement in quantum mechanics.* [en.wikipedia.org/wiki/Measurement_in_quantum_mechanics](https://en.wikipedia.org/wiki/Measurement_in_quantum_mechanics)**
> Quantum measurement collapses a superposition to a definite classical outcome. The final step of every circuit in Problems 3–5 is a measurement; this article explains formally what that means. *Compared to the classical measurement update in Problem 1, quantum measurement involves the Born rule (probability proportional to amplitude squared) rather than a simple knowledge update.*

**IBM Quantum Learning. *Basics of Quantum Information — Single Systems: Quantum Information.* [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com/learning/en/courses/basics-of-quantum-information/single-systems/quantum-information)**
> This IBM Learning module covers quantum state vectors, unitary operations, and the standard qubit gates in the same order used by this submission. Cited at the point in Problem 3 where the notebook compares classical bits to qubits in a side-by-side table, following the definitions given in this course.

**IBM Quantum Learning. *Fundamentals of Quantum Algorithms — Deutsch's Algorithm.* [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com/learning/en/courses/fundamentals-of-quantum-algorithms/quantum-query-algorithms/deutsch-algorithm)**
> IBM's own course treatment of Deutsch's algorithm, cited in Problem 3 when explaining the oracle construction and phase kickback. This is used as a secondary pedagogical reference alongside Nielsen & Chuang for the reversible-oracle design that is central to both Problems 3 and 4.

**IBM Quantum. *Quantum Computing and Group Theory.* [ibm.com/quantum/blog/group-theory](https://www.ibm.com/quantum/blog/group-theory)**
> Cited in Problem 3's introduction as an additional IBM source contextualising quantum algorithms as examples of exploiting algebraic structure. Used to support the framing of the oracle evaluation as determining a global property of the function with a single evaluation.

**Stanford Encyclopedia of Philosophy. *Quantum Entanglement and Information.* [plato.stanford.edu](https://plato.stanford.edu/archives/fall2008/entries/qt-entangle/#5)**
> Cited when introducing the concept of "global properties" of quantum functions — determining a global property (constant vs balanced) of a function from a single evaluation rather than testing each input individually. The SEP article provides a philosophically grounded treatment of the non-local and holistic nature of quantum information.

**Qiskit. *QuantumCircuit API reference.* [docs.quantum.ibm.com/api/qiskit/qiskit.circuit.QuantumCircuit](https://docs.quantum.ibm.com/api/qiskit/qiskit.circuit.QuantumCircuit)** · **Qiskit. *Visualize circuits.* [docs.quantum.ibm.com/guides/visualize-circuits](https://docs.quantum.ibm.com/guides/visualize-circuits)**
> The `QuantumCircuit` class is the primary object used to build all circuits in Problems 3–5. The circuit visualisation guide explains the diagram notation (`●` for control, `⊕` for target, `H` and `X` boxes) used to interpret every circuit diagram in the notebook.

**Qiskit. *Aer Simulator documentation.* [qiskit.github.io/qiskit-aer](https://qiskit.github.io/qiskit-aer/)**
> All quantum circuits in Problems 3–5 are run on the Aer statevector and shot-based simulators. Aer is IBM's local simulation backend; no real hardware access is required. *Compared to running on real IBM Quantum hardware, Aer provides noise-free, deterministic simulation — correct for verifying algorithm correctness but not for characterising hardware error rates.*

**ScienceDirect. *Compact disc encoding.* [sciencedirect.com/topics/engineering/compact-disc](https://www.sciencedirect.com/topics/engineering/compact-disc)**
> Used in the notebook introduction to contrast classical information storage (sequential, deterministic bit reading from a CD) with quantum superposition. This analogy grounds the abstract concept of quantum parallelism in a familiar physical system before any circuit is introduced.

**Wikipedia. *Endianness.* [en.wikipedia.org/wiki/Endianness](https://en.wikipedia.org/wiki/Endianness)** · **Wikipedia. *Quantum circuit.* [en.wikipedia.org/wiki/Quantum_circuit](https://en.wikipedia.org/wiki/Quantum_circuit)**
> Qiskit uses little-endian qubit ordering (least significant qubit first), which is the opposite of most textbooks. These articles are cited in Problem 3 when explaining why measurement bitstrings in Qiskit appear reversed relative to documentation conventions — a common source of confusion that will affect the interpretation of results in Problems 4 and 5.

**Wikipedia. *Superconducting quantum computing.* [en.wikipedia.org/wiki/Superconducting_quantum_computing](https://en.wikipedia.org/wiki/Superconducting_quantum_computing)** · **Wikipedia. *Trapped-ion quantum computer.* [en.wikipedia.org/wiki/Trapped-ion_quantum_computer](https://en.wikipedia.org/wiki/Trapped-ion_quantum_computer)** · **Wikipedia. *Linear optical quantum computing.* [en.wikipedia.org/wiki/Linear_optical_quantum_computing](https://en.wikipedia.org/wiki/Linear_optical_quantum_computing)**
> Cited when introducing Qiskit's hardware-agnostic abstraction. These three articles represent the main physical implementations of qubits: IBM uses superconducting qubits; IonQ and Quantinuum use trapped ions; PsiQuantum targets photonics. Qiskit's transpiler maps the same circuit to any of these backends — the notebook uses Aer to simulate this without targeting one specific hardware type.

---

### Problem 4 — Deutsch's Algorithm (1-bit)

**Deutsch, D. (1985). *Quantum theory, the Church–Turing principle and the universal quantum computer.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1985.0070](https://doi.org/10.1098/rspa.1985.0070)** *(primary source)*
> Problem 4 implements the original 1985 algorithm exactly as described by Deutsch. Every design decision — two qubits, one Hadamard on each before the oracle, Hadamard again on the input qubit, measure the input — traces directly back to this paper. All other Problem 4 references are secondary to this primary source.

**Cleve, R., Ekert, A., Macchiavello, C. & Mosca, M. (1998). *Quantum algorithms revisited.* Proceedings of the Royal Society A. [arxiv.org/abs/quant-ph/9708016](https://arxiv.org/abs/quant-ph/9708016)**
> This paper re-derives the Deutsch–Jozsa algorithm in the cleaner phase-kickback formulation that modern textbooks and Qiskit tutorials use. *The original Deutsch & Jozsa (1992) paper used a different, more complex circuit; the Cleve et al. version is the one actually implemented in Problems 4 and 5.* Citing both makes clear that the implementation follows the modernised form, not the literal 1992 circuit.

**Wikipedia. *Quantum interference.* [en.wikipedia.org/wiki/Wave_interference#Quantum_interference](https://en.wikipedia.org/wiki/Wave_interference#Quantum_interference)**
> Quantum interference is the mechanism by which the algorithm amplifies the correct answer. For constant functions, the amplitudes interfere constructively at `|0⟩` (probability 1 of measuring 0); for balanced functions, they interfere destructively (probability 0 of measuring 0). Understanding this is essential to interpreting the circuit's output in both Problems 4 and 5.

**Wikipedia. *Quantum supremacy / advantage.* [en.wikipedia.org/wiki/Quantum_supremacy](https://en.wikipedia.org/wiki/Quantum_supremacy)**
> Cited when explaining what "quantum speedup" means in context: Deutsch's algorithm solves in 1 query what requires 2 queries classically. This article provides the formal definition and broader context within which this submission's speedup demonstration sits.

**Wikipedia. *Grover's algorithm.* [en.wikipedia.org/wiki/Grover%27s_algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm)** · **Wikipedia. *Shor's algorithm.* [en.wikipedia.org/wiki/Shor%27s_algorithm](https://en.wikipedia.org/wiki/Shor%27s_algorithm)** · **Wikipedia. *Quantum algorithm.* [en.wikipedia.org/wiki/Quantum_algorithm](https://en.wikipedia.org/wiki/Quantum_algorithm)**
> These three references place Deutsch–Jozsa in the landscape of quantum algorithms. Grover's algorithm achieves a quadratic speedup for unstructured search; Shor's achieves an exponential speedup for factoring integers. *Compared to both, the Deutsch–Jozsa algorithm's speedup is exponential but for a more artificial problem. Shor's is widely considered the most practically important; Deutsch–Jozsa is the most pedagogically important as the simplest proof of quantum advantage.*

---

### Problem 5 — Deutsch–Jozsa Algorithm (4-bit)

**Deutsch, D. & Jozsa, R. (1992). *Rapid solution of problems by quantum computation.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1992.0167](https://doi.org/10.1098/rspa.1992.0167)** *(primary source)*
> Problem 5 is the direct generalisation of Problem 4 to $n = 4$ input bits — precisely what the 1992 paper introduced. The four test cases (constant-0, constant-1, balanced-A, balanced-B) verify the two distinguishable function types exactly as described in the original paper.

**Nielsen, M. A. & Chuang, I. L. (2010). *Quantum Computation and Quantum Information.* Ch. 1. Cambridge University Press. [cambridge.org](https://www.cambridge.org/highereducation/books/quantum-computation-and-quantum-information/01E10196D0A682A6AEFFEA52D53BE9AE)**
> The constructive interference (`|0000⟩` measured with certainty for constant functions) and destructive interference (`|0000⟩` has zero probability for balanced functions) used to interpret circuit outputs in Problem 5 are derived from the amplitude analysis in Chapter 1. The textbook provides the mathematical justification for the simulation results.

**Wikipedia. *Deutsch–Jozsa algorithm.* [en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm)**
> The Wikipedia article presents the full generalised algorithm in the Cleve et al. circuit formulation used in this submission. Cited in Problem 5 as an accessible secondary source for the complete $n$-bit algorithm that confirms the circuit design used here is canonical. *Compared to the original paper, the Wikipedia presentation is clearer and uses the modern phase-kickback formulation.*

**Wikipedia. *Global phase factor.* [en.wikipedia.org/wiki/Global_phase_factor](https://en.wikipedia.org/wiki/Global_phase_factor)**
> The constant-1 oracle flips all amplitudes to $-1$, yet the circuit measures `0000` every time — identical to constant-0. This is because a global phase of $-1$ is physically unobservable. Cited to explain why two oracles that look mathematically different produce identical measurement statistics, which would otherwise appear to be a bug.

**Wikipedia. *Hadamard transform — Quantum computing applications.* [en.wikipedia.org/wiki/Hadamard_transform#Quantum_computing_applications](https://en.wikipedia.org/wiki/Hadamard_transform#Quantum_computing_applications)**
> The $n$-qubit Hadamard transform $H^{\otimes n}$ is applied twice in the Deutsch–Jozsa circuit — once to create uniform superposition over all inputs and once to perform interference. This article defines the full $n$-qubit transform and the amplitude formula that explains why the circuit works for any $n$, not just $n = 4$.

**Gerlach, W. & Stern, O. (1922). *Der experimentelle Nachweis der Richtungsquantelung im Magnetfeld.* Zeitschrift für Physik. Wikipedia: [en.wikipedia.org/wiki/Stern%E2%80%93Gerlach_experiment](https://en.wikipedia.org/wiki/Stern%E2%80%93Gerlach_experiment)**
> Used in Problem 5 for two related purposes: (1) to explain why quantum properties such as spin are discrete rather than continuous — motivating why a qubit has exactly two basis states `|0⟩` and `|1⟩`; and (2) as an analogy for qubit initialisation — just as a Stern–Gerlach measurement forces a particle into a definite spin eigenstate, quantum circuits always begin with qubits in the definite `|0⟩` state. *Compared to a purely algebraic introduction, the Stern–Gerlach experiment provides physical grounding for readers without a physics background.*

**Bernstein, E. & Vazirani, U. (1997). *Quantum complexity theory.* SIAM Journal on Computing. [doi:10.1137/S0097539796300921](https://doi.org/10.1137/S0097539796300921)**
> Bernstein and Vazirani extended the Deutsch–Jozsa result to a problem (finding a hidden bit string) where the quantum advantage is also exponential but the problem is more natural. This is the most closely related work to Problem 5: it uses the same circuit structure and the same interference principle. *Where Deutsch–Jozsa distinguishes constant from balanced in one query, Bernstein–Vazirani recovers a hidden $n$-bit string in one query compared to $n$ classical queries — a more natural and practically motivated problem.*

**IBM Quantum Learning. *Computer Science — Deutsch–Jozsa.* [quantum.cloud.ibm.com](https://quantum.cloud.ibm.com/learning/en/modules/computer-science/deutsch-jozsa)**
> IBM's dedicated module on the Deutsch–Jozsa algorithm, used as a cross-check that the circuit structure and interpretation in Problem 5 match IBM's canonical treatment. Confirms that the oracle construction, Hadamard layers, and measurement interpretation used here are consistent with IBM's own educational materials.

---

## Author

**Tiffany Yong Ngik Chee**  
[github.com/TiffanyYongNgikChee](https://github.com/TiffanyYongNgikChee)

---

## License

This repository is submitted as academic coursework. All original code and commentary is the work of the author unless otherwise cited.
