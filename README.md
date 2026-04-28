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

- Deutsch, D. (1985). *Quantum theory, the Church–Turing principle and the universal quantum computer.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1985.0070](https://doi.org/10.1098/rspa.1985.0070)
- Deutsch, D. & Jozsa, R. (1992). *Rapid solution of problems by quantum computation.* Proceedings of the Royal Society A. [doi:10.1098/rspa.1992.0167](https://doi.org/10.1098/rspa.1992.0167)
- Nielsen, M. A. & Chuang, I. L. (2010). *Quantum Computation and Quantum Information.* Cambridge University Press. [ISBN 9781107002173](https://www.cambridge.org/highereducation/books/quantum-computation-and-quantum-information/01E10196D0A682A6AEFFEA52D53BE9AE)
- IBM Quantum. (2016–present). *IBM Quantum Experience.* [ibm.com/quantum](https://www.ibm.com/quantum)
- Qiskit Contributors. (2023). *Qiskit: An open-source framework for quantum computing.* [qiskit.org](https://qiskit.org/)

---

## Author

**Tiffany Yong Ngik Chee**  
[github.com/TiffanyYongNgikChee](https://github.com/TiffanyYongNgikChee)

---

## License

This repository is submitted as academic coursework. All original code and commentary is the work of the author unless otherwise cited.
