


# 🦀 DST: Universal Deterministic Solution Algorithm (Crab Analysis)

## 🎯 Introduction and Fundamentals

The **Deterministic Solution Algorithm (DST)** is a universal analytical framework designed to completely solve any zero-sum, perfect information, and alternating-turn game. This method, known as **Crab Analysis** (Backward Induction), labels every game state with a forced outcome: **Win (G_n), Loss (P_x), or Draw (T_z)**.

The algorithm's goal is not only to find the solution but also to determine the **path of maximum resistance** that the winning player must anticipate to guarantee victory or minimize loss.

---

## 🌐 The Game of the Two Pirates (Canonical Model)

The DST proves its universality by reducing any 2-player game (Chess, Checkers, Cruzados) to an abstract canonical model: **The Game of the Two Pirates**.

This game is a **Partisan and Directed State Graph** where:

* The **Islands** are the possible States of the game.
* The **Pirates** are the players (White and Black).
* The **Seas/Bridges** are the allowed transitions/moves (Bridges).
* The **Chest** is the State label that is "stolen" and transported from island to island.

The DST solution is the **propagation of the label** (G_n, P_x, T_z) across this map, backward to the initial position. Terminal states (G_1) are the islands with no outgoing bridges.

---

## 1. Structure and Notation

### A. The Island (Unique State)
An **Island** is a unique node in the game graph, representing a board configuration and whose turn it is. Its key is defined to maintain turn consistency and handle cycles:

> Unique Key = Board Serialization + Letter of the Player whose **Turn is Now**

This implies the Island's color is determined by the player who currently has the turn.

---

## 2. G_n/P_x Propagation (The Induction Loop)

The algorithm solves the game backward from the terminal states (G_1). **Global Accommodation** (updating the Solution Table) is crucial after every G_n or P_x assignment.

| Assignment | Successor Condition | Index Rule (Index in Antecessor) | Logic (Maximum Resistance) |
| :--- | :--- | :--- | :--- |
| **Loss (P_x)** | **ALL** successors are **G_n**. | **x = G_minima + 1** | The opponent (winner) will choose the **LOWEST** G_n route to end the game as quickly as possible. |
| **Win (G_n)** | **ALL** successors are **P_x**. | **n = P_maxima + 1** | The player must anticipate that the opponent (loser) will choose the **HIGHEST** P_x route to maximize the duration of the game. |

---

## 3. T_z Assignment (Draws)

The **T_z** label is assigned to Islands that remain unresolved after the G_n/P_x loop has stopped (Exclusion Rule).

### A. Indexing z (Strategic Information)

The z index provides an informational value about the maximum trap duration or the minimum draw cycle duration.

| Scenario | Successors Include | Index Rule (Strategic) | Logic (Goal: Force Deep Error) |
| :--- | :--- | :--- | :--- |
| **Risk/Trap** | At least one P_x | **z = P_maxima** | Indicates the path containing the **DEEPEST** loss risk. The longest path is the one the opponent will fail to calculate (Depth of Error). |
| **Pure Cycle** | ONLY T | **z = T_minima** | Represents the shortest path to establish the draw. |

### B. Advanced Strategy: Weighted Heuristic Sum (Z)

As an advanced strategic alternative to model the opponent's total potential for error (Risk Density), the T_z index can be replaced by a Heuristic Score **Z**.

The Z value is calculated as a linear sum, where the **highest Z value** is the optimal move:

$$Z = (N_T \cdot A) + (N_P \cdot B) + \sum_{i=1}^{N_P} (x_i \cdot C)$$

**Strategic Rationale:** This formula is highly tunable. To favor the **depth of the traps** over the quantity and complexity, the following weight hierarchy is recommended:

**Weight Priority:** **C** > **B** > **A**

---

## 4. Priority Hierarchy (Optimal Move)

The player's decision when moving is based on a strict priority hierarchy that defines optimal play:

1.  **Maximum Priority: Win (G)**
    * **Action:** Choose the **G** with the **LOWEST** n value (win fastest).
2.  **Intermediate Priority: Draw (T)**
    * **Action:** Choose the **T** with the **HIGHEST** z value (maximum resistance).
3.  **Minimum Priority: Loss (P)**
    * **Action:** Choose the **P** with the **HIGHEST** x value (minimize the speed of loss).

---

## 5. Universal DST Pseudocode

This is the central algorithm that implements the theory above. 

## 5. Universal DST Pseudocode

This is the central algorithm that implements the theory above.

```pseudocode
// ----------------------------------------------------------------------
// GLOBAL STRUCTURES
// ----------------------------------------------------------------------
SOLUTION_TABLE = Dictionary<Key_Island, Solution> // Stores (1N : G3, 2B : P4, etc.)
PHOTO_COUNTER = 0 // Progressive counter for unique 'Photo' ID
// ----------------------------------------------------------------------
// DST FUNCTIONS
// ----------------------------------------------------------------------
FUNCTION Solve_DST(Initial_State)
// 1. Graph Construction and ID Assignment
Expand_Graph(Initial_State)
// 2. Win/Loss Solution (G_n and P_x)
Loop_Gn_Px()
// 3. Draw Solution (T_z)
Loop_Tz()
RETURN SOLUTION_TABLE[Initial_State]
END FUNCTION

// Builds the Island structure and its Bridges (uses the game-specific function Generate_Outgoing_Bridges)
FUNCTION Expand_Graph(Current_Serialization)
// ... Logic to assign the progressive ID and Unique Key ...
// ... Recursive logic to explore all Bridges and build the INVERSE_GRAPH ...
END FUNCTION

// Applies the G_n/P_x rules (P_max + 1, G_min + 1) iteratively and applies Global Accommodation.
FUNCTION Loop_Gn_Px()
WHILE Changes_In_SOLUTION_TABLE
// ... Logic for G_n and P_x Propagation ...
// ... If assigned, apply Accommodation (update predecessors) ...
END WHILE
END FUNCTION

// Solves T_z using the Exclusion Rule and the z index (P_minima or T_minima).
FUNCTION Loop_Tz()
WHILE Changes_In_Tz_Assigned
// ... Logic for T_z Propagation and z index assignment ...
END WHILE
END FUNCTION

```

## 6. Pragmatic Implementation and Limited Horizon

For games with massive graphs (like Chess), the DST is applied using a **Limited Horizon Search** with an Evaluation Function (E).

### A. The T_1 Tie-breaking Rule

When the search horizon (depth D) is not sufficient to find an absolute G_n or P_x, a pragmatic tie-breaking rule is applied:

1.  **T_1 Default:** If neither G nor P is found in the horizon, all unresolved states are assumed to be **T_1**. The z index is simplified to z=1 due to the absence of definitive P_x values.
2.  **Random Choice:** If multiple moves lead to an identical T_1 outcome (the highest available in this scenario), the choice of the next move is made **randomly**.

---

## 7. Case Study

The game **Cruzados** (2x2, H/V/D movement, diagonal KO) is a perfect example to demonstrate the complete construction of the G, P, T state graph and the propagation of the DST solution.

---

## Authors

* Alex
* Logos

---

## License

This project is under the **MIT License**. You may use, copy, modify, and distribute the code provided the original copyright notice is included.
