# ACO Path Planning Algorithm Analysis

## 1. Algorithm Overview

### 1.1. Ant Colony Optimization (ACO)
This is an optimization algorithm based on the path-finding behavior of ant colonies in nature. The algorithm simulates how ants use pheromones (chemical information substances) to mark paths and find the optimal route from their nest to a food source.

### 1.2. Implementation Version
This repository implements the **Ant System Algorithm** - the basic version of ACO for path planning problems.

## 2. Input Conditions

### 2.1. Command Structure
```bash
python aco_resolve_path.py <ants> <iterations> <map> <p> <Q> [-d|--display]
```

### 2.2. Required Parameters

#### a) `ants` (integer)
- **Description**: Number of ants in the colony
- **Data Type**: Integer (positive)
- **Meaning**: More ants increase the chance of finding a good path but consume more computational resources

#### b) `iterations` (integer)
- **Description**: Number of algorithm iterations
- **Data Type**: Integer (positive)
- **Meaning**: Number of times the entire colony will search for paths. More iterations improve path quality

#### c) `map` (string)
- **Description**: Map file name in the `maps/` directory
- **Data Type**: String
- **Examples**: "map1.txt", "map2.txt", "map3.txt"

#### d) `p` (float)
- **Description**: Pheromone evaporation rate
- **Data Type**: Float
- **Range**: [0.0 - 1.0]
- **Precision**: 0.05
- **Meaning**: 
  - p = 0: No evaporation, old pheromones persist forever
  - p = 1: Complete evaporation, no old pheromones remain
  - Typical values: 0.1 - 0.5

#### e) `Q` (float)
- **Description**: Pheromone adding constant
- **Data Type**: Float
- **Meaning**: Controls the amount of pheromone added to paths

#### f) `-d` or `--display` (optional)
- **Description**: Display the map and resulting path graphically
- **Data Type**: Flag (no value needed)
- **Default**: No display (0)

### 2.3. Map Requirements

#### Map File Structure
Maps must be saved in the `maps/` directory and meet the following requirements:

**a) Shape**
- Square matrix of size m×m
- Each cell separated by spaces

**b) Symbols**
- `S` (Start): Starting point
  - **Required**: Only ONE S allowed
- `F` (Finish): Destination point
  - **Required**: Only ONE F allowed
- `E` (Empty): Empty cell, passable
- `O` (Occupied): Occupied cell, impassable

**c) Valid Map Example** (map1.txt):
```
E S E O O
E E E O O
O O E O O
E E E E O
O O E E F
```

**d) More Complex Map Example** (map2.txt):
```
S E E E E O O O O O O O
E E O O E O O O O O O E 
E E E E E E O E E E O E
E E O O O E E E O E E E
E E O O E E E E E E E E
O E E E E E E O O O E O
O E E E E E E E E E E O
O E E O O E E O O O F O
O E E E E E E O O O E O
O O O O O O E O O O E O
O O O O O O E E E E E O
O O O O O O O O O O O O
```

### 2.4. Validity Conditions
- Map file must exist in the `maps/` directory
- Map must have exactly one S and one F point
- At least one path must exist from S to F
- Numerical parameters must be within allowed ranges

## 3. Output

### 3.1. Console Output
The program prints:

**a) Progress Information:**
```
Iteration: <iteration number> length of the path: <path length>
```
- Printed after each iteration
- Shows the length of the current best path

**b) Final Result:**
```
[(row1, col1), (row2, col2), ..., (rowN, colN)]
```
- List of coordinates from starting point to destination
- Each coordinate is a tuple (row, column)
- In order from S to F

### 3.2. Graphical Output (if using -d flag)
Displays:
- Map as a grayscale image
- Starting point (S): red dot
- Destination point (F): blue dot
- Found path: line connecting points

### 3.3. Result Interpretation
- **Shorter path length**: Better path
- **Path without loops**: Algorithm has removed loops
- **Continuous path**: Adjacent points (including diagonals)

## 4. Operational Flow

### 4.1. High-Level Diagram

```
[Initialize] → [Create Map] → [Create Ant Colony] → [Main Loop] → [Return Result]
                                                          ↓
                                        [Each Ant Finds Path] ← ┐
                                                    ↓            │
                                        [Update Pheromone]      │
                                                    ↓            │
                                        [Save Best Path] ───────┘
```

### 4.2. Detailed Steps

#### Step 1: Initialize and Read Data
**File**: `aco_resolve_path.py`, function `arguments_parsing()`

```
1. Parse command line parameters
2. Validate parameters
3. Return values: ants, iterations, map_path, p, Q, display
```

#### Step 2: Create and Process Map
**File**: `aco/map_class.py`, class `Map`

**2.1. Read Map** (`_read_map`)
```
- Read file from maps/ directory
- Store in numpy array as strings
```

**2.2. Convert to Occupancy Map** (`_map_2_occupancy_map`)
```
- O → 0 (impassable)
- E, S, F → 1 (passable)
- Convert to integer array
```

**2.3. Identify Start/End Points** (`__init__`)
```
- Find position of S → initial_node
- Find position of F → final_node
```

**2.4. Create Nodes** (`_create_nodes`)
```
- Create Node object for each cell
- Each Node contains:
  * Position (row, col)
  * List of edges to adjacent Nodes
  * Special symbol (S, F, E, O)
```

**2.5. Calculate Edges** (`compute_edges`)
```
For each passable node (value = 1):
  - Check 8 surrounding cells (up, down, left, right, and 4 diagonals)
  - If surrounding cell is passable:
    * Add to edges list
    * Initialize Pheromone = 1.0
    * Initialize Probability = 0.0
```

#### Step 3: Initialize Ant Colony
**File**: `aco/ant_colony.py`, class `AntColony`

**3.1. Initialize AntColony** (`__init__`)
```
- Store reference to map
- Store parameters: no_ants, iterations, evaporation_factor (p), pheromone_adding_constant (Q)
- Initialize paths list (empty)
- Create ant colony
- Initialize best_result (empty)
```

**3.2. Create Ants** (`create_ants`)
```
For each ant (from 0 to no_ants):
  - Create Ant object
  - Set starting position = initial_node
  - Set destination = final_node
  - Initialize visited_nodes list with starting point
```

#### Step 4: Main Loop - Path Finding
**File**: `aco/ant_colony.py`, function `calculate_path()`

```
FOR iteration from 0 to iterations:
    
    # Phase 4.1: Each ant finds a path
    FOR each ant in colony:
        
        4.1.1. Setup ant (setup_ant):
            - Clear visited_nodes history (keep start point)
            - Reset current position to start_pos
        
        4.1.2. Find path (WHILE not at destination):
            
            a) Select next node (select_next_node):
                - Get ant's current node
                - Calculate total pheromone of all edges
                - Calculate probability for each edge:
                  probability = pheromone_of_edge / total_pheromone
                - Randomly select edge based on probability
                - Return FinalNode of selected edge
            
            b) Move ant (move_ant):
                - Update actual_node = node_to_visit
                - Add node_to_visit to visited_nodes
            
            c) Check if reached (is_final_node_reached):
                - If actual_node == final_node:
                  → Set final_node_reached = True
        
        4.1.3. Process found path:
            - Remove loops (delete_loops):
              * Find nodes that appear multiple times
              * Remove segments between occurrences (loops)
            - Add path to paths list
        
        4.1.4. Reset ant:
            - Set final_node_reached = False
            - Prepare for next search
    
    # Phase 4.2: Update pheromone
    4.2.1. Sort paths (sort_paths):
        - Sort paths by length (shortest first)
    
    4.2.2. Update pheromone for each edge (pheromone_update):
        FOR each path in paths:
            FOR each node in path:
                FOR each edge of node:
                    
                    IF this edge is in the path:
                        # Add pheromone (good path)
                        new_pheromone = (1 - p) * old_pheromone + Q / length_of_path
                    ELSE:
                        # Only evaporation
                        new_pheromone = (1 - p) * old_pheromone
    
    4.2.3. Save best result:
        - best_result = paths[0] (shortest path)
    
    4.2.4. Clear paths list:
        - Prepare for next iteration
    
    4.2.5. Print progress:
        - Print iteration number and best path length

RETURN best_result
```

#### Step 5: Return and Display Result
**File**: `aco_resolve_path.py`

```
1. Receive best_result from Colony.calculate_path()
2. Print path (list of tuples)
3. If display > 0:
   - Call Map.represent_path(path)
   - Display map with path
```

### 4.3. Loop Deletion Algorithm Details
**Function**: `delete_loops()`

```
Purpose: Remove loops from path

Example: [A, B, C, D, C, E, F]
       → C appears twice at positions 2 and 4
       → Delete segment [D] between occurrences
       → Result: [A, B, C, E, F]

Algorithm:
1. Create copy res_path of in_path
2. FOR each element in res_path:
     - Find all occurrence positions of element
     - Reverse position list (delete from end to start)
     - FOR each adjacent position pair:
         * Delete segment between positions
3. RETURN res_path with loops removed
```

## 5. Code Structure

### 5.1. Class Relationship Diagram

```
aco_resolve_path.py (Main)
        |
        |--- arguments_parsing() → Parse command line
        |
        |--- Map (from aco.map_class)
        |     |
        |     |--- Nodes (inner class)
        |     |     |--- node_pos: (row, col)
        |     |     |--- edges: [{FinalNode, Pheromone, Probability}]
        |     |     |--- spec: S/F/E/O
        |     |     |--- compute_edges()
        |     |
        |     |--- in_map: original map
        |     |--- occupancy_map: 0/1 map
        |     |--- initial_node: (row, col)
        |     |--- final_node: (row, col)
        |     |--- nodes_array: 2D array of Nodes
        |     |--- represent_map()
        |     |--- represent_path()
        |
        |--- AntColony (from aco.ant_colony)
              |
              |--- Ant (inner class)
              |     |--- start_pos: starting position
              |     |--- actual_node: current position
              |     |--- final_node: destination
              |     |--- visited_nodes: list of visited positions
              |     |--- final_node_reached: boolean flag
              |     |--- move_ant()
              |     |--- remember_visited_node()
              |     |--- is_final_node_reached()
              |     |--- setup_ant()
              |
              |--- map: reference to Map object
              |--- no_ants: number of ants
              |--- iterations: number of iterations
              |--- evaporation_factor: p
              |--- pheromone_adding_constant: Q
              |--- paths: list of found paths
              |--- ants: list of Ant objects
              |--- best_result: best path found
              |--- calculate_path() → Main algorithm
              |--- select_next_node() → Probabilistic selection
              |--- pheromone_update() → Update pheromone levels
              |--- delete_loops() → Remove loops from path
```

## 6. Pheromone Details

### 6.1. Initialization
- All edges start with Pheromone = 1.0
- Ensures all edges have equal initial selection probability

### 6.2. Update Formula

**For edges in the path:**
```
τ_new = (1 - p) × τ_old + Q / L
```
Where:
- τ (tau): Pheromone level
- p: Evaporation factor
- Q: Pheromone adding constant
- L: Path length

**For edges not in the path:**
```
τ_new = (1 - p) × τ_old
```

### 6.3. Meaning
- **Evaporation (1 - p)**: Reduces influence of old information
- **Add pheromone Q/L**: 
  - Short path (small L) → add more pheromone
  - Long path (large L) → add less pheromone
- **Result**: Good paths reinforced, bad paths gradually forgotten

## 7. Node Selection Details

### 7.1. Probability Formula
```
P(edge_i) = τ_i / Σ(τ_j)
```
Where:
- P(edge_i): Probability of selecting edge i
- τ_i: Pheromone level of edge i
- Σ(τ_j): Sum of pheromone levels of all edges

### 7.2. Selection Process
```
1. Calculate total pheromone: total_sum = Σ(τ_j)
2. Calculate probability for each edge: P_i = τ_i / total_sum
3. Random selection according to probability distribution (np.random.choice)
```

### 7.3. Characteristics
- **Stochastic**: Not always selecting the best edge
- **Probabilistic**: Edges with higher pheromone → higher probability
- **Exploration**: Still possibility to select new paths

## 8. Parameters and Effects

### 8.1. Number of Ants
| Value | Effect |
|-------|--------|
| Few (5-10) | - Fast computation<br>- Limited exploration, may miss good paths |
| Medium (20-50) | - Good balance<br>- Adequate space exploration |
| Many (>100) | - Good exploration<br>- Costly computation time |

### 8.2. Iterations
| Value | Effect |
|-------|--------|
| Few (<10) | - Suboptimal results<br>- Pheromone not converged |
| Medium (20-100) | - Good results for small/medium maps |
| Many (>200) | - Needed for complex maps<br>- More stable results |

### 8.3. Evaporation Factor p
| Value | Effect |
|-------|--------|
| p ≈ 0 (0.1) | - Retains old information longer<br>- Slow convergence<br>- Easily stuck in local optimum |
| p ≈ 0.5 | - Good balance<br>- Recommended for most cases |
| p ≈ 1 (0.9) | - Forgets old information quickly<br>- More exploration<br>- Slow convergence |

### 8.4. Constant Q
| Value | Effect |
|-------|--------|
| Small Q (1-10) | - Low pheromone influence<br>- Needs many iterations |
| Medium Q (50-100) | - Good balance |
| Large Q (>500) | - Rapid pheromone increase<br>- Fast convergence<br>- May converge to local optimum early |

### 8.5. Recommended Parameters
For medium-sized maps (10x10 - 20x20):
```
ants = 20-30
iterations = 50-100
p = 0.3-0.5
Q = 50-100
```

## 9. Advantages and Disadvantages

### 9.1. Advantages
✓ **Finds good paths**: Usually finds near-optimal paths
✓ **Robust**: Works well with various map types
✓ **Adaptive**: Self-adjusts through iterations
✓ **Parallel-friendly**: Ants operate independently
✓ **Loop handling**: Has mechanism to remove loops from paths

### 9.2. Disadvantages
✗ **Slow**: Needs many iterations to converge
✗ **No optimality guarantee**: Only finds near-optimal solutions
✗ **Parameter-dependent**: Results heavily depend on p, Q
✗ **Memory**: Needs to store pheromone for all edges
✗ **Stochastic**: Results may vary between runs

## 10. Execution Examples

### 10.1. Basic Example
```bash
python aco_resolve_path.py 20 50 map1.txt 0.3 100
```
- 20 ants
- 50 iterations
- Map map1.txt
- p = 0.3 (30% evaporation)
- Q = 100
- No graphical display

### 10.2. Example with Display
```bash
python aco_resolve_path.py 30 100 map2.txt 0.5 50 -d
```
- 30 ants
- 100 iterations
- Map map2.txt
- p = 0.5 (50% evaporation)
- Q = 50
- Graphical display (-d)

### 10.3. Sample Output
```
Iteration: 0  length of the path: 15
Iteration: 1  length of the path: 13
Iteration: 2  length of the path: 12
Iteration: 3  length of the path: 11
...
Iteration: 49  length of the path: 9
[(0, 1), (1, 1), (1, 2), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4), (4, 3)]
```

## 11. Special Cases

### 11.1. No Path Exists
- Algorithm will not converge
- Ants cannot reach destination
- Program may hang in infinite loop

### 11.2. Multiple Optimal Paths
- Algorithm may find any path
- Result depends on random seed
- All paths with equal length are acceptable results

### 11.3. Small Maps
- Very small maps may need fewer iterations
- Number of ants can be reduced

### 11.4. Complex Maps
- Need to increase ants and iterations
- May need to adjust p and Q

## 12. Technical Notes

### 12.1. Python Version
- Code written for **Python 2.x**
- Modifications needed for Python 3.x:
  - `print path` → `print(path)` (line 38, 186 in ant_colony.py)
  - Need to update print statement syntax throughout
  - May need to adjust imports in __init__.py if encountering relative import issues

### 12.2. Dependencies
- numpy: Array handling and computation
- matplotlib: Graphical display

### 12.3. Limitations
- Only supports 8-directional movement (including diagonals)
- Each move has equal cost (no weights)
- Does not consider actual distance (Manhattan vs Euclidean)

## 13. Summary

### 13.1. Algorithm Nature
ACO Path Planning is a meta-heuristic algorithm based on:
- **Collective intelligence**: Swarm intelligence of ant colony
- **Positive feedback**: Good paths are reinforced
- **Probability**: Decisions based on probability
- **Memory**: Information stored through pheromone

### 13.2. Process Summary
```
Input: Map, Parameters → Initialize → Iterations Loop → Output: Best Path
```

### 13.3. When to Use
- Path finding problems on grids/graphs
- No need for absolute optimality guarantee
- Acceptable computation time
- Want robust and adaptive solution

---

## References

1. Dorigo, M., & Stützle, T. (2004). Ant Colony Optimization. MIT Press.
2. Repository: https://github.com/huynhunguyen/ACO_path_planning_org
3. Ant System Algorithm: Dorigo, M. (1992). Optimization, Learning and Natural Algorithms. PhD thesis.
