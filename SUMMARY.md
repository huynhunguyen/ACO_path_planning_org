# Tóm tắt Giải thuật ACO Path Planning / ACO Path Planning Algorithm Summary

## Tóm tắt ngắn gọn / Quick Summary

**Giải thuật Ant Colony Optimization (ACO)** là một giải thuật tối ưu hóa dựa trên hành vi của đàn kiến trong tự nhiên, được áp dụng để tìm đường đi tối ưu trên bản đồ.

**Ant Colony Optimization (ACO)** is an optimization algorithm based on ant colony behavior in nature, applied to find optimal paths on maps.

---

## 🎯 Mục đích / Purpose

Tìm đường đi ngắn nhất từ điểm xuất phát (S) đến điểm đích (F) trên bản đồ có chướng ngại vật.

Find the shortest path from start point (S) to finish point (F) on a map with obstacles.

---

## 📥 Đầu vào / Input

### Tham số bắt buộc / Required Parameters:
```bash
python aco_resolve_path.py <ants> <iterations> <map> <p> <Q> [-d]
```

| Tham số | Kiểu | Ý nghĩa | Parameter | Type | Meaning |
|---------|------|---------|-----------|------|---------|
| ants | int | Số lượng kiến | ants | int | Number of ants |
| iterations | int | Số vòng lặp | iterations | int | Number of iterations |
| map | string | Tên file bản đồ | map | string | Map file name |
| p | float [0-1] | Tỷ lệ bay hơi pheromone | p | float [0-1] | Pheromone evaporation rate |
| Q | float | Hằng số thêm pheromone | Q | float | Pheromone adding constant |
| -d | flag | Hiển thị đồ họa | -d | flag | Display graphics |

### Định dạng bản đồ / Map Format:
- Ma trận vuông / Square matrix
- `S`: Điểm bắt đầu (1 điểm) / Starting point (1 only)
- `F`: Điểm đích (1 điểm) / Finish point (1 only)
- `E`: Ô trống (đi được) / Empty cell (passable)
- `O`: Ô chiếm (không đi được) / Occupied cell (blocked)

**Ví dụ / Example:**
```
S E E O O
E E E O O
O O E O O
E E E E O
O O E E F
```

---

## 📤 Đầu ra / Output

### 1. Console Output
```
Iteration: 0  lenght of the path: 15
Iteration: 1  lenght of the path: 13
...
[(0,1), (1,1), (1,2), ..., (4,4)]
```

### 2. Graphical Output (với flag -d / with -d flag)
- Bản đồ với đường đi được vẽ / Map with drawn path
- Điểm S: màu đỏ / S point: red
- Điểm F: màu xanh / F point: blue

---

## 🔄 Luồng hoạt động / Workflow

```
1. Đọc bản đồ → Read map
2. Khởi tạo đàn kiến → Initialize ant colony
3. FOR mỗi vòng lặp / FOR each iteration:
   a. Mỗi kiến tìm đường (ngẫu nhiên theo xác suất pheromone)
      Each ant finds path (randomly based on pheromone probability)
   b. Cập nhật pheromone (đường ngắn → thêm nhiều pheromone)
      Update pheromone (short path → add more pheromone)
   c. Lưu đường tốt nhất
      Save best path
4. Trả về đường đi ngắn nhất → Return shortest path
```

---

## 🧮 Công thức chính / Main Formulas

### 1. Xác suất chọn cạnh / Edge Selection Probability:
```
P(edge_i) = τ_i / Σ(τ_j)
```
- τ (tau): Mức pheromone / Pheromone level
- Cạnh có pheromone cao → xác suất được chọn cao
- High pheromone edge → high selection probability

### 2. Cập nhật Pheromone / Pheromone Update:
**Cạnh trong đường đi / Edge in path:**
```
τ_new = (1 - p) × τ_old + Q / L
```

**Cạnh không trong đường đi / Edge not in path:**
```
τ_new = (1 - p) × τ_old
```

- p: Tỷ lệ bay hơi / Evaporation rate
- Q: Hằng số / Constant
- L: Độ dài đường đi / Path length

---

## 📊 Tham số khuyến nghị / Recommended Parameters

Cho bản đồ 10×10 đến 20×20 / For 10×10 to 20×20 maps:

| Tham số | Giá trị đề xuất | Parameter | Recommended Value |
|---------|----------------|-----------|-------------------|
| ants | 20-30 | ants | 20-30 |
| iterations | 50-100 | iterations | 50-100 |
| p | 0.3-0.5 | p | 0.3-0.5 |
| Q | 50-100 | Q | 50-100 |

---

## ✅ Ưu điểm / Advantages

✓ Tìm được đường gần tối ưu / Finds near-optimal paths  
✓ Hoạt động tốt với nhiều loại bản đồ / Works well with various maps  
✓ Tự điều chỉnh qua các vòng lặp / Self-adjusts through iterations  
✓ Loại bỏ vòng lặp trong đường đi / Removes loops from paths

## ❌ Nhược điểm / Disadvantages

✗ Cần nhiều vòng lặp → chậm / Needs many iterations → slow  
✗ Không đảm bảo tối ưu tuyệt đối / No absolute optimality guarantee  
✗ Kết quả phụ thuộc tham số / Results depend on parameters  
✗ Kết quả có thể khác nhau mỗi lần chạy / Results may vary between runs

---

## 📖 Tài liệu chi tiết / Detailed Documentation

Để hiểu sâu hơn về giải thuật, xem / For deeper understanding, see:

1. **[ALGORITHM_ANALYSIS.md](ALGORITHM_ANALYSIS.md)** - Phân tích đầy đủ bằng tiếng Việt
   - 13 phần chi tiết về giải thuật
   - Công thức, sơ đồ, ví dụ cụ thể

2. **[ALGORITHM_ANALYSIS_EN.md](ALGORITHM_ANALYSIS_EN.md)** - Full analysis in English
   - 13 detailed sections about the algorithm
   - Formulas, diagrams, concrete examples

3. **[FLOWCHART.md](FLOWCHART.md)** - Sơ đồ luồng / Flowcharts
   - Sơ đồ chi tiết từng bước
   - Sơ đồ dữ liệu và trạng thái

---

## 🚀 Ví dụ sử dụng / Usage Example

### Ví dụ đơn giản / Simple Example:
```bash
python aco_resolve_path.py 20 50 map1.txt 0.3 100
```
- 20 kiến / 20 ants
- 50 vòng lặp / 50 iterations
- Bản đồ map1.txt / Map map1.txt
- p = 0.3 (30% bay hơi / 30% evaporation)
- Q = 100

### Với hiển thị đồ họa / With graphical display:
```bash
python aco_resolve_path.py 30 100 map2.txt 0.5 50 -d
```

### Kết quả mẫu / Sample Output:
```
Iteration: 0  lenght of the path: 15
Iteration: 1  lenght of the path: 13
Iteration: 2  lenght of the path: 12
...
Iteration: 49  lenght of the path: 9
[(0, 1), (1, 1), (1, 2), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4), (4, 3)]
```

---

## 🔬 Cách hoạt động cơ bản / How It Works (Basic)

### Bước 1: Khởi tạo / Step 1: Initialization
- Tất cả cạnh có pheromone = 1.0
- All edges have pheromone = 1.0
- Đảm bảo xác suất ban đầu bằng nhau
- Ensures equal initial probability

### Bước 2: Kiến tìm đường / Step 2: Ants Find Paths
- Mỗi kiến bắt đầu từ S
- Each ant starts from S
- Chọn cạnh tiếp theo dựa trên xác suất pheromone
- Select next edge based on pheromone probability
- Đến khi đến F
- Until reaching F

### Bước 3: Cập nhật / Step 3: Update
- Đường ngắn → thêm nhiều pheromone
- Short path → add more pheromone
- Đường dài → thêm ít pheromone
- Long path → add less pheromone
- Tất cả cạnh → bay hơi một phần
- All edges → partial evaporation

### Bước 4: Lặp lại / Step 4: Repeat
- Lặp lại bước 2-3 nhiều lần
- Repeat steps 2-3 many times
- Pheromone hội tụ về đường tốt
- Pheromone converges to good paths

---

## 🎓 Khái niệm chính / Key Concepts

### Pheromone (Chất thông tin hóa học)
- Thông tin "mùi" để đánh dấu đường đi
- "Scent" information to mark paths
- Càng nhiều pheromone, càng hấp dẫn
- More pheromone → more attractive

### Bay hơi (Evaporation)
- Pheromone giảm dần theo thời gian
- Pheromone decreases over time
- Giúp quên đường xấu
- Helps forget bad paths

### Positive Feedback (Phản hồi tích cực)
- Đường tốt → nhiều kiến đi
- Good path → many ants use it
- Nhiều kiến đi → thêm pheromone
- Many ants → add pheromone
- Thêm pheromone → càng hấp dẫn
- More pheromone → more attractive

### Xác suất (Probability)
- Không chọn cạnh tốt nhất 100%
- Not always choose best edge 100%
- Vẫn khám phá đường mới
- Still explore new paths
- Tránh kẹt local optimum
- Avoid getting stuck in local optimum

---

## 🛠️ Cấu trúc mã nguồn / Code Structure

```
aco_resolve_path.py          # Main program / Chương trình chính
├── arguments_parsing()      # Parse parameters / Phân tích tham số
└── main execution           # Main logic / Logic chính

aco/
├── map_class.py             # Map handling / Xử lý bản đồ
│   ├── Map class            # Map object / Đối tượng bản đồ
│   └── Nodes class          # Node & edges / Node và cạnh
│
└── ant_colony.py            # ACO algorithm / Giải thuật ACO
    ├── AntColony class      # Colony management / Quản lý đàn kiến
    │   ├── calculate_path() # Main algorithm / Giải thuật chính
    │   ├── select_next_node() # Node selection / Chọn node
    │   ├── pheromone_update() # Update pheromone / Cập nhật
    │   └── delete_loops()   # Remove loops / Xóa vòng lặp
    │
    └── Ant class            # Individual ant / Kiến đơn lẻ
        ├── move_ant()       # Movement / Di chuyển
        └── visited_nodes[]  # Path history / Lịch sử đường đi

maps/                        # Map files / File bản đồ
├── map1.txt                 # Example maps / Bản đồ mẫu
├── map2.txt
└── map3.txt
```

---

## 📝 Ghi chú kỹ thuật / Technical Notes

### Python Version
- Code viết cho Python 2.x
- Code written for Python 2.x
- Cần sửa đổi cho Python 3.x
- Needs modifications for Python 3.x

### Dependencies
```bash
pip install numpy matplotlib
```

### Giới hạn / Limitations
- Chỉ di chuyển 8 hướng (kể cả chéo)
- Only 8-directional movement (including diagonals)
- Chi phí mỗi bước bằng nhau
- Equal cost for each step
- Không có trọng số cạnh
- No edge weights

---

## 🔍 Khi nào nên dùng / When to Use

✅ **Nên dùng khi / Use when:**
- Tìm đường trên grid/graph
- Path finding on grids/graphs
- Chấp nhận gần tối ưu
- Accept near-optimal solutions
- Muốn giải pháp robust
- Want robust solutions

❌ **Không nên dùng khi / Don't use when:**
- Cần tối ưu tuyệt đối
- Need absolute optimum
- Cần kết quả ngay lập tức
- Need immediate results
- Bản đồ rất lớn (>100×100)
- Very large maps (>100×100)

---

## 📚 Tham khảo / References

1. Dorigo, M., & Stützle, T. (2004). *Ant Colony Optimization*. MIT Press.
2. Repository: [github.com/huynhunguyen/ACO_path_planning_org](https://github.com/huynhunguyen/ACO_path_planning_org)

---

**Tác giả / Author:** Repository analysis documentation  
**Ngày / Date:** 2025-12-03  
**Phiên bản / Version:** 1.0
