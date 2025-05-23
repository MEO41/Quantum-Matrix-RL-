# ⚛️ Quantum Matrix RL

**Quantum Matrix RL** is a reinforcement learning framework that combines symbolic algorithm discovery with quantum-enhanced value function approximation. Inspired by **AlphaTensor** and stabilized using **Quantum Neural Networks (QNNs)**, this system enables agents to discover efficient algorithms for matrix multiplication.

---

## 🧠 Project Overview

This project addresses the **Deadly Triad** in reinforcement learning:  
> **Function approximation + bootstrapping + off-policy learning**

We counteract these challenges by using a **quantum-enhanced critic**, trained using off-policy methods in an actor-critic framework. The agent learns symbolic strategies for computing $$\( C = A x B \)$$ using tensor-based primitives, rather than naive multiplication.

---

## 🧩 System Architecture

The system is based on **Quantum Off-Policy Actor-Critic** training with the following components:

- **Environment** (`MatrixMultiplyDiscoveryEnv`): Provides $$\(A\)$$, $$\(B\)$$, and tracks $$\(C_t\)$$
- **Actor**: Outputs symbolic matrix operations
- **Critic (Quantum QNN)**: Approximates Q-values using variational quantum circuits
- **Replay Buffer**: Stores transitions for off-policy learning

### ⬇ Architecture Flow

```
[Environment] → (state, reward) → [Actor]
     ↑                             ↓
     └── [Replay Buffer] ← action ←┘
           ↓                     ↑
        (state, action, reward, next_state)
                  → [Critic (Quantum QNN)] → Q-value
```

---

## 🧪 Environment: `MatrixMultiplyDiscoveryEnv`

This custom environment simulates symbolic discovery of matrix multiplication.

### 🔢 Inputs:
- Two matrices $$\( A \in \mathbb{R}^{n x n} \), \( B \in \mathbb{R}^{n x n} \)$$
- Flattened into state observations: `[A_flat | B_flat]`

### 🎮 Actions:
- Each action represents a symbolic operation (e.g., low-rank outer product)
- Action modifies current approximation $$\(C_t\)$$

## 💰 Reward Function

The reward encourages the agent to produce accurate results in fewer, cheaper steps:

$$
r_t = -\|C_t - AB\|_2 - \alpha t - \beta \cdot \mathrm{op\_cost}_t
$$

Where:

- $$\( C_t \)$$ : current estimate of the product
- $$\( \|C_t - AB \|_2 \)$$ : Frobenius norm of error
- $$\( \alpha \)$$ : penalty for step count
- $$\( \beta \)$$ : penalty for operation complexity
- $$\( \mathrm{op\_cost}_t \)$$ : symbolic cost of the operation
  
---

## ✅ Desired Agent Behavior

The optimal RL agent should:
- Approximate \( C = AB \) with minimal symbolic operations
- Prefer low-cost, efficient operations
- Discover strategies that generalize to unseen inputs
- Remain stable during off-policy, bootstrapped training

---

## ⚛️ Quantum Critic Design

Quantum Q-value approximation is used to stabilize learning:

- Implemented with **TorchQuantum**
- Parameterized circuits: RX, RY, RZ rotations + CNOT entanglement
- Classical → quantum encodings map \((s, a)\) into circuit observables
- Expectation values output Q-values used in target updates

---

## 📦 Folder Structure

```
quantum_matrix_rl/
│
├── main.py                    # Train loop entry
├── config.py                  # Global hyperparameters
├── requirements.txt
├── README.md
│
├── assets/                    # Diagrams and architecture visualizations
│   └── architecture.png
│
├── envs/
│   ├── matrix_multiply_env.py
│   └── make_env.py
│
├── models/
│   ├── actor.py
│   ├── critic_classic.py
│   └── critic_quantum.py
│
├── rl/
│   ├── sac_agent.py
│   ├── replay_buffer.py
│   └── trainer.py
│
├── quantum_circuits/
│   ├── layers.py
│   └── encodings.py
│
├── utils/
│   ├── logger.py
│   ├── plots.py
│   └── eval.py
│
├── tests/
│   ├── test_env.py
│   ├── test_critic.py
│   └── test_agent.py
│
└── notebooks/
    ├── experiment_design.ipynb
    └── quantum_vs_classic_eval.ipynb
```

---

## 🚀 Getting Started

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

Ensure you have:
 - numpy==1.24.3
 - torch==2.0.1
 - torchvision==0.15.2
 - gymnasium==0.28.1
 - matplotlib==3.7.1
 - pandas==2.0.2
 - tensorboard==2.13.0
 - tqdm==4.65.0
 - torchquantum==0.1.0
 - pennylane==0.32.0

---
---

## 🧪 How to Test

This project includes unit tests and sanity checks to ensure all components are working correctly before training begins.

### ✅ Run All Sanity Checks

Run the full system check (environment, actor, critic, buffer, agent):

```bash
python tests/test_agent.py
```
### 2. Train an Agent

**Train with Classical Critic**:
```bash
python main.py --mode classic --env MatrixMultiplyDiscoveryEnv --episodes 100000
```

**Train with Quantum Critic**:
```bash
python main.py --mode quantum --env MatrixMultiplyDiscoveryEnv --episodes 100000
```

---

### 3. Visualize Learning

```bash
tensorboard --logdir runs/
```

Or generate reward/error plots manually:

```python
from utils.plots import plot_results
plot_results("runs/quantum/", "Quantum Critic")
```

---

## 🧠 Research Directions

- 🧮 Discover new matrix multiplication algorithms (like Strassen or beyond)
- 📊 Compare classical vs quantum critic stability in off-policy RL
- ⚡ Explore FLOP-efficient symbolic decompositions
- 🧩 Extend to other linear algebra primitives (e.g., inversion, convolution)

---

## 📘 References

- [AlphaTensor: DeepMind, 2022](https://www.nature.com/articles/s41586-022-05172-4)
- [TorchQuantum](https://github.com/mit-han-lab/torchquantum?tab=readme-ov-file)
- [Soft Actor-Critic Paper](https://arxiv.org/abs/1801.01290)
- [RL Book - Sutton & Barto](http://incompleteideas.net/book/the-book-2nd.html)

---

## 🪪 License

MIT License. Free for academic use, contributions welcome.

---

## 🙌 Acknowledgments

Special thanks to the open-source community and the researchers who made AlphaTensor and QML frameworks accessible and replicable.

---


