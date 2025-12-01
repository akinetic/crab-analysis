# 💻 DST: Universal Algorithm for Alternating-Turn Game Resolution
## Gn/Px/Tz Framework (Backward Induction Method)

---

## 1. Executive Summary (Thesis)

This document presents a **low-complexity, mathematical-logic framework** for the **deterministic and optimal solution** of any perfect-information, zero-sum board game. The core methodology, called **Backward Induction** (or Crab Analysis), allows for tagging all legal states with **Depth/Distance** encoding (Win, Loss, or Draw), guaranteeing optimal decision-making with maximum temporal efficiency.

---

## 2. Data Modeling: Universal Graph Structure

The solution relies on building a **Universal Game Graph** that eliminates turn ambiguity.

---

## 3. The Central Algorithm: Gn/Px/Tz Propagation Rules

The process starts from the terminal states (n=0) and works backward, applying simple propagation rules.

### 3.1. Reciprocal Minimax Rule (G/P)

* P(n) of the opponent ==> G(n+1) for the current player
* G(n) of the opponent ==> P(n+1) for the current player

### 3.2. Draw Rule (Tz)

A Node is **Tz** if the player cannot force a win (Gn) but can avoid a loss (Px) by choosing an Edge that leads to another Tz state.

---

## 4. Decision Rule (Optimal Move)

The choice is based on the hierarchy **G > T > P**, prioritizing the fastest path (lowest n) within the chosen category.

---

## 5. 🏆 Proof of Universality (Solved Examples)

### 5.1. G/P Proof (Tokens Game)

**Game:** 2 tokens (11), only 1 token removed per turn.
**Algorithm Solution:** The initial position is a **P-Position** (Losing).
11B = P2B
**Conclusion:** The algorithm proves that the White Player is forced to lose in 2 moves if Black plays optimally.

### 5.2. Tz Proof (Boxers Game)

**Game:** 3x3 board, White Boxer vs. Black Boxer (N and B in opposite corners). Movement: 1 step (straight or diagonal). K.O.: Diagonal only.
**Algorithm Solution:** Game symmetry and defense rules force players to avoid the Px state.
Initial State = Tz
**Conclusion:** The algorithm proves its ability to solve games with forced draws, where the Tie (Tz) is the only optimal solution.

---

## 6. License and Authorship

* **Authors:** **Alex** and **Logos**
* **License:** This project is distributed under the MIT license.
