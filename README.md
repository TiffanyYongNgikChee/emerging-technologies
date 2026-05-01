# Emerging Technologies

> Classical and quantum implementations of the Deutsch–Jozsa algorithm, built from first principles using Python and Qiskit.

---
 
## About
 
This repository is a Jupyter Notebook submission for the **Emerging Technologies** module (Summer 25/26, ATU). It explores the **[Deutsch–Jozsa algorithm](https://en.wikipedia.org/wiki/Deutsch%E2%80%93Jozsa_algorithm)** — one of the earliest proofs that a quantum computer can outperform a classical one for a specific task. The notebook is structured as five interconnected problems, starting from pure Python and progressing to full quantum circuits simulated with IBM's Qiskit framework.
 
---
 
## Target Audience
 
This submission is written for an **informed computing professional** — someone with a strong background in computing who may not be familiar with quantum computing, Qiskit, or the specific Python libraries used here. Every concept is explained from first principles within the notebook itself. No prior knowledge of quantum mechanics is assumed.
 
Anyone should be able to clone this repository, install the dependencies, and run the notebook from start to finish with no extra help. All setup instructions are in the [Installation](#installation) section below.
 

---

## Table of Contents

- [About](#about)
- [Target Audience](#target-audience)
- [Background](#background)
- [Algorithm Overview](#algorithm-overview)
- [Problems — Detailed Walkthrough](#problems--detailed-walkthrough)
  - [Problem 1 — Generating Random Boolean Functions](#problem-1--generating-random-boolean-functions)
  - [Problem 2 — Classical Testing](#problem-2--classical-testing)
  - [Problem 3 — Quantum Oracles](#problem-3--quantum-oracles)
  - [Problem 4 — Deutsch's Algorithm (1-bit)](#problem-4--deutschs-algorithm-1-bit)
  - [Problem 5 — Deutsch–Jozsa Algorithm (4-bit)](#problem-5--deutschjozsa-algorithm-4-bit)
- [Repository Structure](#repository-structure)
- [Environment](#environment)
- [Installation](#installation)
- [Usage](#usage)
- [Dependencies](#dependencies)
- [References](#references)
- [Author](#author)
- [License](#license)

---

## Background

### Deutsch and the Turing Machine — Where This All Started

Before there were quantum computers, computers were understood through a thought experiment called the **[Turing machine](https://en.wikipedia.org/wiki/Turing_machine)**, invented by [Alan Turing](https://en.wikipedia.org/wiki/Alan_Turing) in 1936. A Turing machine is an imaginary device that reads and writes symbols on a tape, one step at a time. Turing proved that any computation a real computer can do, this simple machine can also do — it became the universal definition of "what computers can compute."

In 1985, British physicist **[David Deutsch](https://en.wikipedia.org/wiki/David_Deutsch)** asked a deeper question: *what if the tape could be quantum?* He replaced Turing's classical tape with quantum mechanics — superposition, interference, entanglement — and described the first **[quantum Turing machine](https://en.wikipedia.org/wiki/Quantum_Turing_machine)**. This was the theoretical birth of quantum computing. In 1992, he and [Richard Jozsa](https://en.wikipedia.org/wiki/Richard_Jozsa) generalised the result to functions with many inputs, creating the **Deutsch–Jozsa algorithm** that this notebook implements.

### IBM — From Mainframes to Quantum

**[IBM](https://en.wikipedia.org/wiki/IBM)** has been at the centre of computing for over a century. Their mainframe computers still process an estimated 95 % of ATM transactions and 80 % of the world's credit card transactions daily. In 2016 IBM launched **[IBM Quantum](https://www.ibm.com/quantum)** — the world's first publicly accessible quantum computer over the cloud. This notebook uses IBM's open-source quantum framework, **[Qiskit](https://qiskit.org/)**, to simulate quantum circuits locally.

---

## Algorithm Overview

The Deutsch–Jozsa algorithm determines whether a Boolean function is **constant** (always returns the same output) or **balanced** (returns `True` for exactly half of all inputs). A classical computer needs up to $2^{n-1}+1$ queries in the worst case; the quantum algorithm answers with a **single query**, regardless of how many input bits there are.

| Problem | Topic | Approach | Key Result |
|:---:|:---|:---|:---|
| 1 | Generating Random Boolean Functions | Classical Python | Oracle factory for Problems 2–5 |
| 2 | Classical Testing | Classical — worst case $2^{n-1}+1$ queries | Establishes the baseline to beat |
| 3 | Quantum Oracles | Qiskit circuit construction | Bridges classical functions to quantum circuits |
| 4 | Deutsch's Algorithm (1-bit) | Quantum — 1 query | First proof of quantum advantage |
| 5 | Deutsch–Jozsa (4-bit) | Quantum — 1 query | Generalisation: works at any scale |

---

## Problems — Detailed Walkthrough

### Problem 1 — Generating Random Boolean Functions

**Goal:** Build a Python function `random_constant_balanced()` that returns a randomly chosen Boolean function on four inputs, guaranteed to be either constant or balanced.

**What is a Boolean function here?**  
A function $f: \{0,1\}^4 \to \{0,1\}$ takes four binary inputs and returns a single binary output. There are $2^4 = 16$ possible inputs (`0000` through `1111`). The function is:
- **Constant** — returns the same value (`0` or `1`) for every single one of the 16 inputs.
- **Balanced** — returns `0` for exactly 8 inputs and `1` for the other 8.

**How it works:**

The function is represented internally as a **16-element tuple** of `0`s and `1`s, where position `i` holds the output for input `i`. For example, a balanced function might look like `(0, 1, 0, 1, 0, 0, 1, 1, 0, 1, 1, 0, 1, 0, 0, 1)` — exactly eight `1`s and eight `0`s.

Three helper components are built step by step:

1. **`random_tuple(n)`** — generates a random constant or balanced tuple of length $2^n$. For constant functions it repeats one bit 16 times; for balanced functions it creates a list with eight `0`s and eight `1`s, shuffles it with Fisher-Yates (`random.shuffle`), and converts it to a tuple.

2. **`bin_args_to_int(*args)`** — converts a sequence of Boolean inputs (e.g. `1, 0, 1, 1`) into an integer index (e.g. `11`) so the function can look up the correct tuple entry.

3. **`fclosure(n)`** — returns a **closure**: a Python inner function that captures the randomly generated tuple in its scope. When called with four Boolean inputs, it looks up and returns the corresponding tuple entry. Because the tuple is captured inside the closure, the caller cannot see it — modelling the "black box" oracle used in quantum algorithm theory.

**Why this matters:**  
These functions are the "mystery oracles" that all subsequent problems must classify. Problem 2 classifies them classically; Problems 3–5 classify them with a quantum circuit. Getting the generator right is the essential first step because every other problem depends on it.

**Key numbers:**

| Type | Count (n=4) | Explanation |
|:---|:---:|:---|
| Constant functions | 2 | All-zeros tuple or all-ones tuple |
| Balanced functions | 12,870 | $\binom{16}{8}$ ways to place eight 1s in 16 positions |

---

### Problem 2 — Classical Testing

**Goal:** Write a function `determine_constant_balanced(f)` that takes a black-box function from Problem 1 and returns `'constant'` or `'balanced'` using only classical evaluation — then analyse how many queries it needs.

**The setup:**  
In Problem 1 you *built* the mystery function, so you know what it is. In Problem 2 you pretend you *don't* know. Someone hands you a function `f` and all you can do is call it with inputs and observe the outputs. This is the **oracle model**: you treat `f` as a black box.

**Two solutions are implemented and compared:**

1. **`is_constant_or_balanced(f, n)` — brute force**  
   Calls `try_all(f, n)` to evaluate `f` on all $2^4 = 16$ inputs, collects the output list, then checks: are all outputs identical (constant)? Or do exactly half equal `1` (balanced)? This always requires exactly **16 queries**, regardless of the answer.

2. **`determine_constant_balanced(f)` — early termination**  
   A smarter approach. It queries inputs one at a time and stops the moment certainty is achieved:
   - If any two outputs *differ* → must be balanced → **stop immediately** (as few as 2 queries).
   - If the same output appears $2^{n-1}+1 = 9$ times in a row → must be constant → **stop** (a balanced function cannot produce the same output more than 8 times).
   
   The worst case is **9 queries**. Both solutions are verified to agree on 10 random trials.

**Worst-case query count by approach:**

| Approach | Best case | Worst case |
|:---|:---:|:---:|
| Brute force | 16 queries | 16 queries |
| Early termination | 2 queries | **9 queries** |
| Deutsch–Jozsa (quantum, Problems 4–5) | **1 query** | **1 query** |

**Why this matters:**  
The exponential worst-case cost — which would be $2^{99}+1$ queries for a 100-bit function — is precisely the bottleneck that quantum computing eliminates. This problem establishes the classical baseline that Deutsch's algorithm beats.

**Mathematical background covered:**  
The problem also develops the linear algebra used in Problems 3–5, including probability column vectors, stochastic matrices, matrix multiplication, the NOT matrix, and the identity matrix — all built from scratch to show the classical analogues of the quantum operations that follow.

---

### Problem 3 — Quantum Oracles

**Goal:** Translate each of the four single-bit Boolean functions ($f_0$ through $f_3$) into a quantum circuit — a *quantum oracle* — using Qiskit. These circuits are the building blocks for Deutsch's algorithm in Problem 4.

**Why quantum circuits need oracles:**  
A quantum computer cannot evaluate a classical function directly — it can only apply *unitary* (reversible) operations. The oracle is the standard mechanism for encoding a classical function `f` into a reversible quantum gate. It uses a two-qubit mapping:

$$|x\rangle|y\rangle \;\xrightarrow{U_f}\; |x\rangle|y \oplus f(x)\rangle$$

- **Qubit `x`** (top wire, the input) passes through unchanged.
- **Qubit `y`** (bottom wire, the *ancilla*) is XOR'd with `f(x)`: flipped if `f(x) = 1`, unchanged if `f(x) = 0`.

XOR is used because it is reversible — no information is destroyed — satisfying the quantum requirement that all operations be unitary.

**The four single-bit functions and their oracle circuits:**

| Oracle | Function | Type | Gates Used | How It Works |
|:---:|:---:|:---:|:---:|:---|
| `oracle_f0` | $f(x) = 0$ | Constant | *None* | $y \oplus 0 = y$ — nothing changes, empty circuit |
| `oracle_f1` | $f(x) = x$ | Balanced | `CX` | CNOT flips `y` only when `x = 1` |
| `oracle_f2` | $f(x) = \neg x$ | Balanced | `X`, `CX`, `X` | Anti-CNOT: flip `y` when `x = 0` (wrap CNOT with X gates) |
| `oracle_f3` | $f(x) = 1$ | Constant | `X` on qubit 1 | Always flip `y`, regardless of `x` |

**Mathematical toolkit introduced:**  
To understand what these circuits actually do, the problem develops the full quantum linear algebra framework:
- Complex amplitudes and why quantum states use complex numbers instead of real probabilities.
- The **Born rule**: probability of measuring outcome $a$ is $|\alpha_a|^2$ (square the amplitude).
- **Unitary matrices**: the quantum equivalent of stochastic matrices — they preserve the total probability (norm) of the state.
- The **Hadamard gate** `H`: creates superposition ($|0\rangle \to |+\rangle$) and performs interference. Applying `H` twice returns to the original state.
- The **Kronecker product** `⊗`: combines single-qubit state vectors into multi-qubit state vectors (e.g. $|0\rangle \otimes |1\rangle = |01\rangle$).
- The **CNOT gate** as a $4 \times 4$ permutation matrix.
- **Bell states** and quantum entanglement demonstrated with a working Qiskit circuit.

All four oracles are stored in a dictionary at the end, ready to be plugged into Problem 4.

---

### Problem 4 — Deutsch's Algorithm (1-bit)

**Goal:** Assemble the full Deutsch circuit using Qiskit and demonstrate that it classifies each single-bit oracle from Problem 3 correctly using exactly **one query** — where a classical approach needs two.

**The circuit structure:**

```
|0⟩ ──H──[  Oracle Uf  ]──H──[measure]
|1⟩ ──H──[             ]
```

Three composable sub-circuits are built and stitched together with `qc.compose()`:

1. **`start`** — sets qubit 1 to $|1\rangle$ with `X`, then applies `H` to both qubits. This puts qubit 0 into equal superposition $|+\rangle$ and qubit 1 into $|-\rangle = \frac{1}{\sqrt{2}}(|0\rangle - |1\rangle)$. The $|-\rangle$ ancilla is essential — it enables phase kickback.

2. **`oracle`** — one of the four circuits from Problem 3 (swapped in per test).

3. **`end`** — applies `H` to qubit 0, then measures it into a classical bit.

**Why it works — phase kickback and interference:**

When the oracle acts on qubit 0 while qubit 1 is in $|-\rangle$, the result encodes $f(x)$ as a **phase** ($\pm 1$) on qubit 0's amplitude rather than as a bit flip on qubit 1:

$$|x\rangle|-\rangle \;\xrightarrow{U_f}\; (-1)^{f(x)}|x\rangle|-\rangle$$

The final `H` on qubit 0 converts this phase difference into a measurable amplitude:
- **Constant** ($f(0) = f(1)$) → phases are equal → **constructive interference** at $|0\rangle$ → always measure `0`.
- **Balanced** ($f(0) \neq f(1)$) → phases differ by sign → **destructive interference** at $|0\rangle$ → always measure `1`.

**Simulation results (1024 shots each on AerSimulator):**

| Oracle | Function | Expected | Result |
|:---:|:---:|:---:|:---:|
| `oracle_f0` | $f(x) = 0$ — Constant | `0` | `{'0': 1024}` |
| `oracle_f1` | $f(x) = x$ — Balanced | `1` | `{'1': 1024}` |
| `oracle_f2` | $f(x) = \neg x$ — Balanced | `1` | `{'1': 1024}` |
| `oracle_f3` | $f(x) = 1$ — Constant | `0` | `{'0': 1024}` |

The result is deterministic — all 1024 shots agree every time. This is the first concrete demonstration of quantum advantage in the notebook: **1 query** versus **2 queries** classically.

**Full matrix walkthrough:**  
The problem traces the complete matrix multiplication chain — $I \otimes X$, $H \otimes H$, CNOT, $H \otimes I$ — step by step with numerical values, showing how the amplitudes evolve from the initial state to the final measurement.

---

### Problem 5 — Deutsch–Jozsa Algorithm (4-bit)

**Goal:** Scale the single-bit Deutsch circuit to the four-bit Boolean functions from Problem 1 and verify that a single quantum query still suffices — where classically up to 9 queries are needed.

**The generalisation:**

| | Deutsch (Problem 4) | Deutsch–Jozsa (Problem 5) |
|:---|:---:|:---:|
| Input bits | 1 | **4** |
| Total qubits | 2 | **5** (4 inputs + 1 ancilla) |
| Possible inputs | 2 | **16** |
| Classical worst case | 2 queries | **9 queries** |
| Quantum queries | 1 | **1** |

**The circuit — three stages:**

```
|0000⟩ ──H⊗4──[    Oracle Uf    ]──H⊗4──[measure 4 bits]
  |1⟩  ──H────[                ]
```

1. **Initialisation** — `X` on the ancilla (qubit 4), then `H` on all 5 qubits. The 4 input qubits enter uniform superposition over all 16 inputs simultaneously; the ancilla enters $|-\rangle$.

2. **Oracle** (`build_dj_oracle`) — reads the 16-element truth-table tuple from Problem 1. For every input `x` where `f(x) = 1`, it uses a **wrap–MCX–unwrap** pattern to stamp a $-1$ phase onto that specific basis state:
   - *Wrap*: `X` every input qubit that is `0` in `x`'s bit pattern, so the state $|x\rangle$ looks like $|1111\rangle$.
   - *MCX*: a multi-controlled-X gate targeting the ancilla fires only when all 4 controls are $|1\rangle$ — via phase kickback on $|-\rangle$, this silently flips the sign of $|x\rangle$.
   - *Unwrap*: reapply the same `X` gates to restore the qubit values.
   
   This repeats once per input where `f(x) = 1`, encoding the full truth table as $\pm 1$ phases across all 16 basis states.

3. **Interference and measurement** — `H` on the 4 input qubits, then measure all 4 into classical bits. The phase pattern interferes:
   - **Constant**: all phases equal → constructive interference at `0000` → always measure `0000`.
   - **Balanced**: half $+1$, half $-1$ → cancel at `0000` → `0000` is impossible.

**Decision rule:** measure `0000` → **constant**. Anything else → **balanced**.

**Demonstration results:**

| Function | Truth Table | Oracle Action | Measurement | Verdict |
|:---|:---|:---|:---:|:---:|
| Constant-0 $f(x)=0$ | All zeros | No gates (identity) | `0000` always | constant |
| Constant-1 $f(x)=1$ | All ones | All 16 phases → $-1$ (global phase, undetectable) | `0000` always | constant |
| Balanced A $f(x)=x_0$ | First 8 zeros, last 8 ones | 8 phases flipped; $8\times(+1) + 8\times(-1) = 0$ at `0000` | `0000` never | balanced |
| Balanced B $f(x)=x_3$ | Alternating `0,1,0,1,…` | 8 alternating phases; same cancellation | `0000` never | balanced |

**Verified on 10 random functions** from `random_constant_balanced()` — quantum result matches classical ground truth every time.

---

## Repository Structure
 
```
emerging-technologies/
├── img/
│   ├── deutsch-circuit-matrix-chain.png  # Step-by-step matrix multiplication walkthrough for the Deutsch circuit (Problem 4)
│   ├── oracle_circuit.png                # Diagram of the standard two-qubit oracle structure |x⟩|y⟩ → |x⟩|y⊕f(x)⟩ (Problem 3)
│   └── oracle_diagram.png                # Full Deutsch algorithm circuit diagram showing H gates, oracle, and measurement (Problem 3)
├── problems.ipynb                        # Main notebook — all five problems from classical Python to full Deutsch–Jozsa quantum circuit
├── requirements.txt                      # Direct Python dependencies (Qiskit, Aer, NumPy, Matplotlib — install with pip)
├── .gitignore                            # Excludes virtual environments, Jupyter checkpoints, and Python cache files from version control
└── README.md                             # Project overview, problem explanations, installation guide, and references
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

Choose the section that matches your situation.

---

### Option A — GitHub Codespaces (recommended, zero setup)

If you are viewing this repository on GitHub:

1. Click the green **`<> Code`** button.
2. Select the **Codespaces** tab.
3. Click **Create codespace on main**.

GitHub will build a cloud environment with Python already installed. Once it opens, skip to [Usage](#usage).

---

### Option B — Local machine (Mac / Linux)

**Prerequisites:** Python 3.10 or newer. Check with:

```bash
python3 --version
```

If Python is not installed, download it from [python.org](https://www.python.org/downloads/) or use your system package manager:

```bash
# macOS (Homebrew)
brew install python

# Ubuntu / Debian
sudo apt update && sudo apt install python3 python3-venv python3-pip
```

**Steps:**

```bash
# 1. Clone the repository
git clone https://github.com/TiffanyYongNgikChee/emerging-technologies.git
cd emerging-technologies

# 2. Create a virtual environment (keeps dependencies isolated from your system Python)
python3 -m venv .venv

# 3. Activate the virtual environment
source .venv/bin/activate
# Your prompt should now show (.venv)

# 4. Install dependencies
pip install -r requirements.txt

# 5. Launch JupyterLab
jupyter lab
```

To deactivate the virtual environment when you are done:

```bash
deactivate
```

---

### Option C — Local machine (Windows)

**Prerequisites:** Python 3.10 or newer. Check in Command Prompt or PowerShell:

```powershell
python --version
```

If Python is not installed, download it from [python.org](https://www.python.org/downloads/windows/). During installation, tick **"Add Python to PATH"**.

**Steps (PowerShell):**

```powershell
# 1. Clone the repository
git clone https://github.com/TiffanyYongNgikChee/emerging-technologies.git
cd emerging-technologies

# 2. Create a virtual environment
python -m venv .venv

# 3. Activate the virtual environment
.venv\Scripts\Activate.ps1

# If you get an execution policy error, run this first, then try again:
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 4. Install dependencies
pip install -r requirements.txt

# 5. Launch JupyterLab
jupyter lab
```

**Steps (Command Prompt):**

```cmd
python -m venv .venv
.venv\Scripts\activate.bat
pip install -r requirements.txt
jupyter lab
```

---

### Option D — Conda / Anaconda / Miniconda

If you use Conda (common in academic and data science settings):

```bash
# 1. Clone the repository
git clone https://github.com/TiffanyYongNgikChee/emerging-technologies.git
cd emerging-technologies

# 2. Create a new Conda environment with Python 3.12
conda create -n emerging-tech python=3.12 -y

# 3. Activate the environment
conda activate emerging-tech

# 4. Install dependencies via pip inside the Conda environment
pip install -r requirements.txt

# 5. Launch JupyterLab
jupyter lab
```

> **Note:** Use `pip install` here rather than `conda install` — the Qiskit packages are not on the default Conda channels and must come from PyPI.
---

### Troubleshooting

| Problem | Likely Cause | Fix |
|:---|:---|:---|
| `ModuleNotFoundError: No module named 'qiskit'` | Virtual environment not activated | Run `source .venv/bin/activate` (Mac/Linux) or `.venv\Scripts\activate` (Windows) before `jupyter lab` |
| `jupyter: command not found` | JupyterLab not installed or venv not active | Activate venv, then run `pip install jupyterlab` |
| `pip install` fails with permission error | Installing into system Python without venv | Always create and activate a virtual environment first (see Options B/C) |
| Qiskit version conflicts | Mixing system and venv packages | Delete `.venv`, recreate it, and reinstall |
| Windows: `Activate.ps1 cannot be loaded` | PowerShell execution policy | Run `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| Slow install on Colab | Large package download | Normal — Qiskit with visualisation extras is around 400 MB |

---

## Usage

Once JupyterLab is open:

1. Open `problems.ipynb` from the file browser on the left.
2. Run all cells in order: **Kernel → Restart Kernel and Run All Cells**.
3. Each problem is self-contained with its own explanation, code, and output. Problems must be run in order (1 → 5) because later problems depend on functions defined earlier.

No real quantum hardware is required — all circuits are simulated locally using IBM's [Aer simulator](https://qiskit.github.io/qiskit-aer/).

---

## Dependencies

All direct dependencies are listed in [`requirements.txt`](requirements.txt). Key packages:

| Package | Purpose | Used In |
|:---|:---|:---|
| `qiskit[visualization]` | Quantum circuit construction and diagram drawing | Problems 3–5 |
| `qiskit-aer` | Local quantum circuit simulation (no hardware needed) | Problems 3–5 |
| `numpy` | Numerical array operations, matrix multiplication | Problems 1–5 |
| `matplotlib` | Circuit diagrams, measurement histograms | Problems 3–5 |
| `scipy` / `sympy` | Mathematical utilities | Problems 1–2 |
| `jupyterlab` | Notebook interface | All |

> **Note on pinning:** `requirements.txt` lists direct dependencies without pinned versions for readability and cross-platform compatibility. For a fully reproducible locked environment, use [`pip-tools`](https://github.com/jazzband/pip-tools): run `pip-compile requirements.txt` to generate a `requirements.lock` file with exact versions.

---

## References
References are grouped by the section of the notebook they directly support. Each entry includes a note explaining *why* it was used and how it connects to this submission.

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
 
This repository is submitted as academic coursework for the Emerging Technologies module, ATU, Summer 25/26. All original code and commentary is the work of the author unless otherwise cited.