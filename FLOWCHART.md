# Sơ đồ luồng hoạt động chi tiết / Detailed Flowchart

## 1. Luồng chính của chương trình / Main Program Flow

```
┌─────────────────────────────────────────┐
│         START PROGRAM                   │
│     (aco_resolve_path.py)              │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Parse Command Line Arguments          │
│   • ants: số kiến / number of ants     │
│   • iterations: số vòng lặp / iterations│
│   • map: tên file bản đồ / map file    │
│   • p: tỷ lệ bay hơi / evaporation     │
│   • Q: hằng số pheromone / constant    │
│   • display: hiển thị / display flag   │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│         Initialize Map                  │
│   (Map class from map_class.py)        │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│      Initialize AntColony               │
│   (AntColony class from ant_colony.py) │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│      Calculate Path                     │
│   (Main ACO Algorithm)                  │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│      Print Result Path                  │
│   [(row1,col1), (row2,col2), ...]      │
└────────────────┬────────────────────────┘
                 │
                 ▼
          ┌──────┴──────┐
          │ display > 0? │
          └──────┬──────┘
                 │
       ┌─────────┴─────────┐
       │ YES              NO│
       ▼                   ▼
┌─────────────┐    ┌─────────────┐
│  Display    │    │    END      │
│  Map & Path │    └─────────────┘
└──────┬──────┘
       │
       ▼
┌─────────────┐
│    END      │
└─────────────┘
```

## 2. Chi tiết Map Initialization / Map Initialization Details

```
┌─────────────────────────────────────────┐
│        Map.__init__(map_name)           │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Read Map File (_read_map)             │
│   • Đọc từ ./maps/<map_name>           │
│   • Load vào numpy array (string)      │
│   → self.in_map                         │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Convert to Occupancy Map              │
│   (_map_2_occupancy_map)                │
│   • O → 0 (không đi được / blocked)    │
│   • E,S,F → 1 (đi được / passable)     │
│   → self.occupancy_map                  │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Find Start & End Points               │
│   • Tìm vị trí S → self.initial_node   │
│   • Tìm vị trí F → self.final_node     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Create Nodes Array (_create_nodes)    │
│   • Tạo Node cho mỗi ô                 │
│   • Tính edges cho mỗi Node            │
│   → self.nodes_array[i][j]             │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│        Map Ready to Use                 │
└─────────────────────────────────────────┘
```

## 3. Chi tiết Node.compute_edges / Node Edge Computation Details

```
┌─────────────────────────────────────────┐
│   Node.compute_edges(self, map_arr)     │
│   Input: vị trí node / node position    │
└────────────────┬────────────────────────┘
                 │
                 ▼
          ┌──────┴──────┐
          │ Node có thể │
          │ đi được?    │
          │ (value==1)  │
          └──────┬──────┘
                 │
       ┌─────────┴─────────┐
       │ YES              NO│
       ▼                   ▼
┌─────────────┐    ┌─────────────┐
│ Continue    │    │ Return []   │
└──────┬──────┘    │  (no edges) │
       │           └─────────────┘
       ▼
┌─────────────────────────────────────────┐
│   Loop through 8 directions:            │
│   di ∈ [-1, 0, 1]                      │
│   dj ∈ [-1, 0, 1]                      │
│   (except di=0 and dj=0)               │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Calculate neighbor position:          │
│   newi = node_pos[0] + di               │
│   newj = node_pos[1] + dj               │
└────────────────┬────────────────────────┘
                 │
                 ▼
          ┌──────┴──────┐
          │ Neighbor in │
          │ bounds?     │
          └──────┬──────┘
                 │
       ┌─────────┴─────────┐
       │ YES              NO│
       ▼                   ▼
┌─────────────┐    ┌─────────────┐
│ Continue    │    │ Skip        │
└──────┬──────┘    └─────────────┘
       │
       ▼
   ┌───┴────┐
   │Neighbor│
   │passable│
   │(val=1)?│
   └───┬────┘
       │
   ┌───┴────┐
   │  YES   │
   ▼        │
┌──────────────────────────┐
│ Add to edges:            │
│ {                        │
│   'FinalNode': (newi,newj)│
│   'Pheromone': 1.0       │
│   'Probability': 0.0     │
│ }                        │
└────────────┬─────────────┘
             │
             ▼
┌─────────────────────────────────────────┐
│   Return edges list                     │
└─────────────────────────────────────────┘
```

## 4. Chi tiết AntColony.calculate_path / Main Algorithm Details

```
┌─────────────────────────────────────────┐
│   AntColony.calculate_path()            │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   FOR iteration = 0 to iterations       │
└────────────────┬────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │ ITERATION LOOP │◄────────────┐
        └────────┬───────┘             │
                 │                      │
                 ▼                      │
┌─────────────────────────────────────────┐
│   FOR each ant in colony                │
└────────────────┬────────────────────────┘
                 │                      │
                 ▼                      │
        ┌────────────────┐             │
        │   ANT LOOP     │◄─────┐      │
        └────────┬───────┘      │      │
                 │              │      │
                 ▼              │      │
┌─────────────────────────────────┐   │
│   Setup Ant:                    │   │
│   • Clear visited_nodes         │   │
│   • Reset to start position     │   │
└────────────────┬────────────────┘   │
                 │                    │
                 ▼                    │
        ┌────────────────┐           │
        │ PATH FINDING   │◄───┐      │
        └────────┬───────┘    │      │
                 │             │      │
                 ▼             │      │
     ┌───────────┴───────────┐│      │
     │ Ant reached final     ││      │
     │ destination?          ││      │
     └───────────┬───────────┘│      │
                 │             │      │
       ┌─────────┴─────────┐  │      │
       │ NO               YES│ │      │
       ▼                   ▼  │      │
┌──────────────┐  ┌─────────────┐   │
│Select next   │  │Process path │   │
│node based on │  │(delete loops│   │
│pheromone     │  │& save)      │   │
│probability   │  └──────┬──────┘   │
└──────┬───────┘         │          │
       │                 │          │
       ▼                 │          │
┌──────────────┐         │          │
│Move ant to   │         │          │
│selected node │         │          │
└──────┬───────┘         │          │
       │                 │          │
       │                 │          │
       └─────────────────┘          │
                                    │
            (Loop for all ants)     │
                 │                  │
                 ▼                  │
┌─────────────────────────────────────┐
│   Pheromone Update:                 │
│   • Sort paths by length            │
│   • Update pheromone levels         │
│   • Save best result (shortest path)│
└────────────────┬────────────────────┘
                 │                  │
                 ▼                  │
┌─────────────────────────────────────┐
│   Clear paths list                  │
└────────────────┬────────────────────┘
                 │                  │
                 ▼                  │
┌─────────────────────────────────────┐
│   Print: Iteration X, length Y      │
└────────────────┬────────────────────┘
                 │                  │
                 └──────────────────┘
                                    
            (Loop for all iterations)
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Return best_result                    │
└─────────────────────────────────────────┘
```

## 5. Chi tiết select_next_node / Node Selection Algorithm

```
┌─────────────────────────────────────────┐
│   select_next_node(actual_node)         │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Initialize total_sum = 0.0            │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   FOR each edge in actual_node.edges:   │
│      total_sum += edge['Pheromone']     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   FOR each edge in actual_node.edges:   │
│   • prob = edge['Pheromone']/total_sum  │
│   • edge['Probability'] = prob          │
│   • Add edge to edges_list              │
│   • Add prob to p[]                     │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Clear probability values:              │
│   FOR each edge:                        │
│      edge['Probability'] = 0.0          │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Random selection based on probability: │
│   np.random.choice(edges_list, 1, p)    │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Return selected edge['FinalNode']     │
└─────────────────────────────────────────┘

Example với 3 edges / Example with 3 edges:
────────────────────────────────────────────
Edge 1: Pheromone = 2.0
Edge 2: Pheromone = 1.0
Edge 3: Pheromone = 1.0
────────────────────────────────────────────
Total = 4.0
────────────────────────────────────────────
Probability[1] = 2.0/4.0 = 0.5  (50%)
Probability[2] = 1.0/4.0 = 0.25 (25%)
Probability[3] = 1.0/4.0 = 0.25 (25%)
────────────────────────────────────────────
→ Edge 1 có khả năng được chọn cao nhất
  (Edge 1 has highest probability)
```

## 6. Chi tiết pheromone_update / Pheromone Update Algorithm

```
┌─────────────────────────────────────────┐
│   pheromone_update()                    │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Sort paths by length (shortest first) │
│   self.paths.sort(key=len)              │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   FOR each path in self.paths:          │
└────────────────┬────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │   PATH LOOP    │◄─────┐
        └────────┬───────┘      │
                 │              │
                 ▼              │
┌─────────────────────────────────┐      │
│   FOR each node in path:        │      │
└────────────────┬────────────────┘      │
                 │                       │
                 ▼                       │
        ┌────────────────┐              │
        │   NODE LOOP    │◄───┐         │
        └────────┬───────┘    │         │
                 │             │         │
                 ▼             │         │
┌───────────────────────────────────┐   │
│   FOR each edge of node:          │   │
└────────────────┬──────────────────┘   │
                 │                  │   │
                 ▼                  │   │
        ┌────────────────┐         │   │
        │   EDGE LOOP    │◄─┐      │   │
        └────────┬───────┘  │      │   │
                 │          │      │   │
                 ▼          │      │   │
     ┌───────────┴───────────┐    │   │
     │ Is this edge in the   │    │   │
     │ path (connects to     │    │   │
     │ next node)?           │    │   │
     └───────────┬───────────┘    │   │
                 │                │   │
       ┌─────────┴─────────┐     │   │
       │ YES              NO│     │   │
       ▼                   ▼     │   │
┌──────────────┐  ┌──────────────┐  │   │
│ ADD MORE     │  │ EVAPORATE    │  │   │
│ PHEROMONE:   │  │ ONLY:        │  │   │
│ τ_new =      │  │ τ_new =      │  │   │
│ (1-p)*τ_old  │  │ (1-p)*τ_old  │  │   │
│ + Q/L        │  │              │  │   │
└──────┬───────┘  └──────┬───────┘  │   │
       │                 │          │   │
       └─────────┬───────┘          │   │
                 │                  │   │
                 └──────────────────┘   │
                                        │
            (Loop for all edges)        │
                 │                      │
                 └──────────────────────┘
                                        
            (Loop for all nodes)        │
                 │                      │
                 └──────────────────────┘
                                        
            (Loop for all paths)
                 │
                 ▼
┌─────────────────────────────────────────┐
│   All pheromones updated                │
└─────────────────────────────────────────┘
```

## 7. Chi tiết delete_loops / Loop Deletion Algorithm

```
┌─────────────────────────────────────────┐
│   delete_loops(in_path)                 │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   res_path = copy of in_path            │
└────────────────┬────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────┐
│   FOR each element in res_path:         │
└────────────────┬────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │ ELEMENT LOOP   │◄─────┐
        └────────┬───────┘      │
                 │              │
                 ▼              │
┌─────────────────────────────────┐      │
│   Find all occurrences of       │      │
│   element in res_path           │      │
│   → coincidences[]              │      │
└────────────────┬────────────────┘      │
                 │                       │
                 ▼                       │
     ┌───────────┴───────────┐          │
     │ Has multiple          │          │
     │ occurrences?          │          │
     │ (len > 1)             │          │
     └───────────┬───────────┘          │
                 │                      │
       ┌─────────┴─────────┐            │
       │ YES              NO│            │
       ▼                   ▼            │
┌──────────────┐  ┌─────────────┐      │
│ Continue     │  │ Skip        │      │
└──────┬───────┘  └─────────────┘      │
       │                               │
       ▼                               │
┌─────────────────────────────────────┐ │
│   Reverse coincidences list         │ │
│   (to delete from back to front)    │ │
└────────────────┬────────────────────┘ │
                 │                      │
                 ▼                      │
┌─────────────────────────────────────┐ │
│   FOR each pair of adjacent         │ │
│   coincidences:                     │ │
│   Delete res_path[i+1 : i]          │ │
└────────────────┬────────────────────┘ │
                 │                      │
                 └──────────────────────┘
                 
         (Loop for all elements)
                 │
                 ▼
┌─────────────────────────────────────────┐
│   Return res_path (without loops)       │
└─────────────────────────────────────────┘

Ví dụ / Example:
────────────────────────────────────────────
Input:  [A, B, C, D, C, E, C, F]
        C xuất hiện tại / C appears at:
        index 2, 4, 6
────────────────────────────────────────────
Step 1: Xóa / Delete [D] giữa index 2 và 4
        → [A, B, C, E, C, F]
────────────────────────────────────────────
Step 2: Xóa / Delete [E] giữa index 2 và 4
        → [A, B, C, F]
────────────────────────────────────────────
Output: [A, B, C, F]
```

## 8. Sơ đồ tương tác dữ liệu / Data Flow Diagram

```
┌─────────────────┐
│  Command Line   │
│   Arguments     │
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Map File (./maps/map.txt)              │
│  ┌──────┬──────┬──────┬──────┐          │
│  │  S   │  E   │  E   │  O   │          │
│  ├──────┼──────┼──────┼──────┤          │
│  │  E   │  E   │  O   │  E   │          │
│  ├──────┼──────┼──────┼──────┤          │
│  │  E   │  E   │  E   │  F   │          │
│  └──────┴──────┴──────┴──────┘          │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Map Object                             │
│  • in_map: original strings             │
│  • occupancy_map: 0/1 matrix            │
│  • initial_node: (0,0)                  │
│  • final_node: (2,3)                    │
│  • nodes_array[i][j]:                   │
│    ┌────────────────────────┐           │
│    │ Node(i,j)              │           │
│    │ • position: (i,j)      │           │
│    │ • edges: [...]         │           │
│    │   ├─ FinalNode         │           │
│    │   ├─ Pheromone: 1.0    │           │
│    │   └─ Probability: 0.0  │           │
│    └────────────────────────┘           │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  AntColony Object                       │
│  • map: reference                       │
│  • no_ants: N                          │
│  • iterations: M                        │
│  • evaporation_factor: p                │
│  • pheromone_adding_constant: Q         │
│  • ants[]: list of Ant objects          │
│    ┌────────────────────────┐           │
│    │ Ant                    │           │
│    │ • start_pos            │           │
│    │ • actual_node          │           │
│    │ • visited_nodes: []    │           │
│    │ • final_node_reached   │           │
│    └────────────────────────┘           │
│  • paths[]: temporary results           │
│  • best_result: final path              │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Algorithm Execution                    │
│  ┌──────────────────────────────┐       │
│  │ Iteration 1:                 │       │
│  │   Ant 1 → Path: length 15    │       │
│  │   Ant 2 → Path: length 12    │       │
│  │   ...                        │       │
│  │   Update pheromones          │       │
│  │   Best: length 12            │       │
│  ├──────────────────────────────┤       │
│  │ Iteration 2:                 │       │
│  │   Ant 1 → Path: length 11    │       │
│  │   Ant 2 → Path: length 10    │       │
│  │   ...                        │       │
│  │   Update pheromones          │       │
│  │   Best: length 10            │       │
│  ├──────────────────────────────┤       │
│  │ ...                          │       │
│  └──────────────────────────────┘       │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Final Result                           │
│  best_path = [(0,0), (1,1), (2,2),     │
│               (2,3)]                    │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Console Output                         │
│  Iteration: 0  length of the path: 15   │
│  Iteration: 1  length of the path: 12   │
│  ...                                    │
│  [(0,0), (1,1), (2,2), (2,3)]          │
└────────┬────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────┐
│  Graphical Display (optional)           │
│  [Map with path drawn on it]            │
└─────────────────────────────────────────┘
```

## 9. Sơ đồ trạng thái Pheromone / Pheromone State Diagram

```
Initial State (Iteration 0):
─────────────────────────────
All edges have τ = 1.0

        A ──(1.0)── B
        │           │
      (1.0)       (1.0)
        │           │
        C ──(1.0)── D

After Iteration 1:
─────────────────────────────
Ants found paths, pheromone updated
Path: A→B→D (length=2)

        A ──(1.5)── B
        │           │
      (0.7)       (1.5)
        │           │
        C ──(0.7)── D

τ(A→B) = (1-p)*1.0 + Q/2 = 1.5  (in path)
τ(B→D) = (1-p)*1.0 + Q/2 = 1.5  (in path)
τ(A→C) = (1-p)*1.0 = 0.7        (not in path)
τ(C→D) = (1-p)*1.0 = 0.7        (not in path)

After Iteration 2:
─────────────────────────────
More pheromone on good path

        A ──(2.0)── B
        │           │
      (0.5)       (2.0)
        │           │
        C ──(0.5)── D

Convergence:
─────────────────────────────
Good paths have high pheromone
Bad paths have low pheromone

        A ──(5.0)── B
        │           │
      (0.1)       (5.0)
        │           │
        C ──(0.1)── D

→ Most ants will choose A→B→D
```

## 10. Sơ đồ so sánh kết quả / Result Comparison Diagram

```
┌─────────────────────────────────────────┐
│  Different Parameters Lead to           │
│  Different Results                      │
└─────────────────────────────────────────┘

Scenario 1: Low evaporation (p=0.1)
────────────────────────────────────────
┌─────────────────────────────────────┐
│ Iterations: 1-50                    │
│ ▓▓▓░░░░░░░░░░░░░ Path length        │
│ Slow convergence                    │
│ Keeps old information               │
└─────────────────────────────────────┘

Scenario 2: Medium evaporation (p=0.5)
────────────────────────────────────────
┌─────────────────────────────────────┐
│ Iterations: 1-50                    │
│ ▓▓▓▓▓▓░░░░░░░░░ Path length         │
│ Good convergence                    │
│ Balanced exploration                │
└─────────────────────────────────────┘

Scenario 3: High evaporation (p=0.9)
────────────────────────────────────────
┌─────────────────────────────────────┐
│ Iterations: 1-50                    │
│ ▓▓░░▓░▓░░▓░░░░░ Path length         │
│ Unstable convergence                │
│ Too much exploration                │
└─────────────────────────────────────┘

Legend / Chú thích:
▓ = Longer path / Đường dài
░ = Shorter path / Đường ngắn
```

---

## Ghi chú / Notes:

1. **Ký hiệu trong sơ đồ / Symbols in diagrams:**
   - `→` hoặc `│`: Luồng điều khiển / Control flow
   - `┌─┐`: Khối xử lý / Processing block
   - `◊`: Điều kiện / Decision point
   - `○`: Điểm bắt đầu/kết thúc / Start/end point

2. **Đọc sơ đồ / Reading diagrams:**
   - Từ trên xuống dưới / Top to bottom
   - Từ trái sang phải / Left to right
   - Các vòng lặp được đánh dấu bằng mũi tên quay lại / Loops marked with back arrows

3. **Màu sắc (nếu in màu) / Colors (if printed in color):**
   - Xanh: Khối khởi tạo / Blue: Initialization
   - Vàng: Khối xử lý / Yellow: Processing
   - Đỏ: Điều kiện/quyết định / Red: Conditions/decisions
   - Xanh lá: Kết quả / Green: Results
