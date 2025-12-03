# Phân tích Giải thuật ACO Path Planning

## 1. Tổng quan về giải thuật

### 1.1. Giải thuật Ant Colony Optimization (ACO)
Đây là một giải thuật tối ưu hóa dựa trên hành vi tìm đường của đàn kiến trong tự nhiên. Giải thuật mô phỏng cách kiến sử dụng pheromone (chất thông tin hóa học) để đánh dấu đường đi và tìm ra con đường tối ưu từ tổ đến nguồn thức ăn.

### 1.2. Phiên bản triển khai
Repository này triển khai **Ant System Algorithm** - phiên bản cơ bản của ACO cho bài toán quy hoạch đường đi (path planning).

## 2. Điều kiện đầu vào (Input Conditions)

### 2.1. Cấu trúc lệnh thực thi
```bash
python aco_resolve_path.py <ants> <iterations> <map> <p> <Q> [-d|--display]
```

### 2.2. Các tham số bắt buộc

#### a) `ants` (số nguyên)
- **Mô tả**: Số lượng kiến trong đàn
- **Kiểu dữ liệu**: Integer (số nguyên dương)
- **Ý nghĩa**: Càng nhiều kiến, càng có khả năng tìm được đường đi tốt nhưng tốn nhiều tài nguyên tính toán hơn

#### b) `iterations` (số nguyên)
- **Mô tả**: Số vòng lặp của giải thuật
- **Kiểu dữ liệu**: Integer (số nguyên dương)
- **Ý nghĩa**: Số lần toàn bộ đàn kiến sẽ tìm đường. Nhiều vòng lặp giúp cải thiện chất lượng đường đi

#### c) `map` (chuỗi)
- **Mô tả**: Tên file bản đồ trong thư mục `maps/`
- **Kiểu dữ liệu**: String
- **Ví dụ**: "map1.txt", "map2.txt", "map3.txt"

#### d) `p` (số thực)
- **Mô tả**: Hệ số bay hơi pheromone (pheromone evaporation rate)
- **Kiểu dữ liệu**: Float
- **Phạm vi**: [0.0 - 1.0]
- **Độ chính xác**: 0.05
- **Ý nghĩa**: 
  - p = 0: Không có bay hơi, pheromone cũ tồn tại mãi
  - p = 1: Bay hơi hoàn toàn, không còn pheromone cũ
  - Giá trị thông thường: 0.1 - 0.5

#### e) `Q` (số thực)
- **Mô tả**: Hằng số thêm pheromone (pheromone adding constant)
- **Kiểu dữ liệu**: Float
- **Ý nghĩa**: Điều khiển lượng pheromone được thêm vào đường đi

#### f) `-d` hoặc `--display` (tùy chọn)
- **Mô tả**: Hiển thị bản đồ và đường đi kết quả dưới dạng đồ họa
- **Kiểu dữ liệu**: Flag (không cần giá trị)
- **Mặc định**: Không hiển thị (0)

### 2.3. Yêu cầu về bản đồ (Map Requirements)

#### Cấu trúc file bản đồ
Bản đồ phải được lưu trong thư mục `maps/` và đáp ứng các yêu cầu sau:

**a) Hình dạng**
- Ma trận vuông kích thước m×m
- Mỗi ô cách nhau bởi khoảng trắng

**b) Các ký hiệu**
- `S` (Start): Điểm bắt đầu
  - **Bắt buộc**: Chỉ có DUY NHẤT một điểm S
- `F` (Finish): Điểm đích
  - **Bắt buộc**: Chỉ có DUY NHẤT một điểm F
- `E` (Empty): Ô trống, có thể đi qua được
- `O` (Occupied): Ô bị chiếm, không thể đi qua

**c) Ví dụ bản đồ hợp lệ** (map1.txt):
```
E S E O O
E E E O O
O O E O O
E E E E O
O O E E F
```

**d) Ví dụ bản đồ phức tạp hơn** (map2.txt):
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

### 2.4. Điều kiện hợp lệ
- File bản đồ phải tồn tại trong thư mục `maps/`
- Bản đồ phải có đúng một điểm S và một điểm F
- Phải tồn tại ít nhất một đường đi từ S đến F
- Các tham số số học phải nằm trong phạm vi cho phép

## 3. Đầu ra (Output)

### 3.1. Output trên console
Chương trình in ra:

**a) Thông tin tiến trình:**
```
Iteration: <số vòng lặp> length of the path: <độ dài đường đi>
```
- In ra sau mỗi vòng lặp
- Hiển thị độ dài đường đi tốt nhất hiện tại

**b) Kết quả cuối cùng:**
```
[(row1, col1), (row2, col2), ..., (rowN, colN)]
```
- Danh sách các tọa độ từ điểm xuất phát đến điểm đích
- Mỗi tọa độ là tuple (hàng, cột)
- Theo thứ tự từ S đến F

### 3.2. Output đồ họa (nếu dùng flag -d)
Hiển thị:
- Bản đồ dưới dạng ảnh grayscale
- Điểm xuất phát (S): chấm đỏ
- Điểm đích (F): chấm xanh
- Đường đi tìm được: đường nối các điểm

### 3.3. Ý nghĩa kết quả
- **Độ dài đường đi ngắn hơn**: Đường đi tốt hơn
- **Đường đi không có vòng lặp**: Giải thuật đã loại bỏ các vòng lặp
- **Đường đi liên tục**: Các điểm kề nhau (bao gồm đường chéo)

## 4. Luồng hoạt động (Operational Flow)

### 4.1. Sơ đồ tổng quan

```
[Khởi tạo] → [Tạo bản đồ] → [Tạo đàn kiến] → [Vòng lặp chính] → [Trả về kết quả]
                                                      ↓
                                    [Mỗi kiến tìm đường] ← ┐
                                                ↓          │
                                    [Cập nhật pheromone]   │
                                                ↓          │
                                    [Lưu đường tốt nhất] ──┘
```

### 4.2. Chi tiết từng bước

#### Bước 1: Khởi tạo và đọc dữ liệu
**File**: `aco_resolve_path.py`, function `arguments_parsing()`

```
1. Phân tích tham số dòng lệnh
2. Kiểm tra tính hợp lệ của các tham số
3. Trả về các giá trị: ants, iterations, map_path, p, Q, display
```

#### Bước 2: Tạo và xử lý bản đồ
**File**: `aco/map_class.py`, class `Map`

**2.1. Đọc bản đồ** (`_read_map`)
```
- Đọc file từ thư mục maps/
- Lưu vào mảng numpy kiểu string
```

**2.2. Chuyển đổi sang occupancy map** (`_map_2_occupancy_map`)
```
- O → 0 (không đi được)
- E, S, F → 1 (đi được)
- Chuyển sang mảng integer
```

**2.3. Xác định điểm đầu/cuối** (`__init__`)
```
- Tìm vị trí của S → initial_node
- Tìm vị trí của F → final_node
```

**2.4. Tạo các nodes** (`_create_nodes`)
```
- Tạo đối tượng Node cho mỗi ô
- Mỗi Node chứa:
  * Vị trí (row, col)
  * Danh sách cạnh (edges) đến các Node kề
  * Ký hiệu đặc biệt (S, F, E, O)
```

**2.5. Tính toán các cạnh** (`compute_edges`)
```
Với mỗi node đi được (value = 1):
  - Kiểm tra 8 ô xung quanh (trên, dưới, trái, phải, và 4 đường chéo)
  - Nếu ô xung quanh đi được:
    * Thêm vào danh sách edges
    * Khởi tạo Pheromone = 1.0
    * Khởi tạo Probability = 0.0
```

#### Bước 3: Khởi tạo đàn kiến
**File**: `aco/ant_colony.py`, class `AntColony`

**3.1. Khởi tạo AntColony** (`__init__`)
```
- Lưu tham chiếu đến map
- Lưu các tham số: no_ants, iterations, evaporation_factor (p), pheromone_adding_constant (Q)
- Khởi tạo danh sách paths (trống)
- Tạo đàn kiến
- Khởi tạo best_result (trống)
```

**3.2. Tạo kiến** (`create_ants`)
```
Với mỗi kiến (từ 0 đến no_ants):
  - Tạo đối tượng Ant
  - Đặt vị trí bắt đầu = initial_node
  - Đặt đích đến = final_node
  - Khởi tạo danh sách visited_nodes với điểm bắt đầu
```

#### Bước 4: Vòng lặp chính - Tìm đường
**File**: `aco/ant_colony.py`, function `calculate_path()`

```
FOR iteration từ 0 đến iterations:
    
    # Giai đoạn 4.1: Mỗi kiến tìm đường
    FOR mỗi ant trong đàn kiến:
        
        4.1.1. Setup ant (setup_ant):
            - Xóa lịch sử visited_nodes (giữ lại điểm đầu)
            - Reset vị trí hiện tại về start_pos
        
        4.1.2. Tìm đường (WHILE chưa đến đích):
            
            a) Chọn node tiếp theo (select_next_node):
                - Lấy node hiện tại của kiến
                - Tính tổng pheromone của tất cả các cạnh
                - Tính xác suất cho mỗi cạnh:
                  probability = pheromone_of_edge / total_pheromone
                - Chọn ngẫu nhiên cạnh dựa trên xác suất
                - Trả về FinalNode của cạnh được chọn
            
            b) Di chuyển kiến (move_ant):
                - Cập nhật actual_node = node_to_visit
                - Thêm node_to_visit vào visited_nodes
            
            c) Kiểm tra đến đích (is_final_node_reached):
                - Nếu actual_node == final_node:
                  → Đặt final_node_reached = True
        
        4.1.3. Xử lý đường đi tìm được:
            - Xóa vòng lặp (delete_loops):
              * Tìm các node xuất hiện nhiều lần
              * Xóa đoạn giữa các lần xuất hiện (là vòng lặp)
            - Thêm đường đi vào danh sách paths
        
        4.1.4. Reset kiến:
            - Đặt final_node_reached = False
            - Chuẩn bị cho lần tìm tiếp theo
    
    # Giai đoạn 4.2: Cập nhật pheromone
    4.2.1. Sắp xếp đường đi (sort_paths):
        - Sắp xếp paths theo độ dài (ngắn nhất trước)
    
    4.2.2. Cập nhật pheromone cho mỗi cạnh (pheromone_update):
        FOR mỗi đường đi trong paths:
            FOR mỗi node trong đường đi:
                FOR mỗi cạnh của node:
                    
                    IF cạnh này nằm trong đường đi:
                        # Thêm pheromone (đường đi tốt)
                        new_pheromone = (1 - p) * old_pheromone + Q / length_of_path
                    ELSE:
                        # Chỉ bay hơi
                        new_pheromone = (1 - p) * old_pheromone
    
    4.2.3. Lưu kết quả tốt nhất:
        - best_result = paths[0] (đường ngắn nhất)
    
    4.2.4. Xóa danh sách paths:
        - Chuẩn bị cho vòng lặp tiếp theo
    
    4.2.5. In tiến trình:
        - In số iteration và độ dài đường tốt nhất

RETURN best_result
```

#### Bước 5: Trả về và hiển thị kết quả
**File**: `aco_resolve_path.py`

```
1. Nhận best_result từ Colony.calculate_path()
2. In ra đường đi (list of tuples)
3. Nếu display > 0:
   - Gọi Map.represent_path(path)
   - Hiển thị bản đồ với đường đi
```

### 4.3. Chi tiết giải thuật xóa vòng lặp
**Function**: `delete_loops()`

```
Mục đích: Loại bỏ các vòng lặp trong đường đi

Ví dụ: [A, B, C, D, C, E, F]
       → C xuất hiện 2 lần tại vị trí 2 và 4
       → Xóa đoạn [D] giữa hai lần xuất hiện
       → Kết quả: [A, B, C, E, F]

Thuật toán:
1. Tạo bản sao res_path của in_path
2. FOR mỗi element trong res_path:
     - Tìm tất cả vị trí xuất hiện của element
     - Đảo ngược danh sách vị trí (xóa từ cuối về đầu)
     - FOR mỗi cặp vị trí liền kề:
         * Xóa đoạn giữa hai vị trí
3. RETURN res_path đã xóa vòng lặp
```

## 5. Cấu trúc mã nguồn (Code Structure)

### 5.1. Sơ đồ quan hệ các class

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

### 5.2. Luồng dữ liệu

```
Command Line Arguments
        ↓
[arguments_parsing]
        ↓
Map File → [Map.__init__] → Map Object
                              |--- occupancy_map
                              |--- nodes_array (with edges)
        ↓
[AntColony.__init__]
        ↓
[create_ants] → List of Ant objects
        ↓
[calculate_path] ←─────┐
        ↓               │
    FOR each iteration  │
        ↓               │
    FOR each ant        │
        ↓               │
    [setup_ant]         │
        ↓               │
    WHILE not reached   │
        ↓               │
    [select_next_node]  │ (Loop iterations times)
        ↓               │
    [move_ant]          │
        ↓               │
    [is_final_node_reached]
        ↓               │
    [delete_loops]      │
        ↓               │
    [add_to_path_results]
        ↓               │
    [pheromone_update]  │
        ↓               │
    [sort_paths]        │
        ↓               │
    best_result ────────┘
        ↓
[RETURN best_result]
        ↓
Print path
        ↓
[Optional: represent_path] → Display graphic
```

## 6. Chi tiết về Pheromone

### 6.1. Khởi tạo
- Tất cả các cạnh bắt đầu với Pheromone = 1.0
- Đảm bảo tất cả cạnh có xác suất được chọn ban đầu bằng nhau

### 6.2. Công thức cập nhật

**Với cạnh nằm trong đường đi:**
```
τ_new = (1 - p) × τ_old + Q / L
```
Trong đó:
- τ (tau): Pheromone level
- p: Evaporation factor (hệ số bay hơi)
- Q: Pheromone adding constant
- L: Length of path (độ dài đường đi)

**Với cạnh không nằm trong đường đi:**
```
τ_new = (1 - p) × τ_old
```

### 6.3. Ý nghĩa
- **Bay hơi (1 - p)**: Làm giảm ảnh hưởng của thông tin cũ
- **Thêm pheromone Q/L**: 
  - Đường ngắn (L nhỏ) → thêm nhiều pheromone
  - Đường dài (L lớn) → thêm ít pheromone
- **Kết quả**: Đường tốt được tăng cường, đường xấu bị quên dần

## 7. Chi tiết về Selection (Chọn node tiếp theo)

### 7.1. Công thức tính xác suất
```
P(edge_i) = τ_i / Σ(τ_j)
```
Trong đó:
- P(edge_i): Probability of selecting edge i
- τ_i: Pheromone level of edge i
- Σ(τ_j): Sum of pheromone levels of all edges

### 7.2. Quy trình chọn
```
1. Tính tổng pheromone: total_sum = Σ(τ_j)
2. Tính xác suất cho mỗi cạnh: P_i = τ_i / total_sum
3. Chọn ngẫu nhiên theo phân phối xác suất (np.random.choice)
```

### 7.3. Đặc điểm
- **Stochastic (ngẫu nhiên)**: Không phải lúc nào cũng chọn cạnh tốt nhất
- **Probabilistic**: Cạnh có pheromone cao → xác suất cao hơn
- **Exploration**: Vẫn có khả năng chọn đường mới

## 8. Tham số và ảnh hưởng

### 8.1. Số lượng kiến (ants)
| Giá trị | Ảnh hưởng |
|---------|-----------|
| Ít (5-10) | - Tính toán nhanh<br>- Khám phá ít, dễ bỏ sót đường tốt |
| Vừa (20-50) | - Cân bằng tốt<br>- Khám phá đủ không gian |
| Nhiều (>100) | - Khám phá tốt<br>- Tốn thời gian tính toán |

### 8.2. Số vòng lặp (iterations)
| Giá trị | Ảnh hưởng |
|---------|-----------|
| Ít (<10) | - Kết quả chưa tối ưu<br>- Pheromone chưa hội tụ |
| Vừa (20-100) | - Kết quả tốt cho bản đồ nhỏ/vừa |
| Nhiều (>200) | - Cần cho bản đồ phức tạp<br>- Kết quả ổn định hơn |

### 8.3. Hệ số bay hơi p
| Giá trị | Ảnh hưởng |
|---------|-----------|
| p ≈ 0 (0.1) | - Giữ thông tin cũ lâu<br>- Hội tụ chậm<br>- Dễ bị kẹt local optimum |
| p ≈ 0.5 | - Cân bằng tốt<br>- Khuyến nghị cho hầu hết trường hợp |
| p ≈ 1 (0.9) | - Quên thông tin cũ nhanh<br>- Khám phá nhiều<br>- Hội tụ chậm |

### 8.4. Hằng số Q
| Giá trị | Ảnh hưởng |
|---------|-----------|
| Q nhỏ (1-10) | - Ảnh hưởng pheromone ít<br>- Cần nhiều vòng lặp |
| Q vừa (50-100) | - Cân bằng tốt |
| Q lớn (>500) | - Pheromone tăng nhanh<br>- Hội tụ nhanh<br>- Có thể sớm tối ưu local |

### 8.5. Khuyến nghị tham số
Cho bản đồ kích thước trung bình (10x10 - 20x20):
```
ants = 20-30
iterations = 50-100
p = 0.3-0.5
Q = 50-100
```

## 9. Ưu và nhược điểm

### 9.1. Ưu điểm
✓ **Tìm được đường tốt**: Thường tìm được đường gần tối ưu
✓ **Robust**: Hoạt động tốt với nhiều loại bản đồ
✓ **Adaptive**: Tự điều chỉnh qua các vòng lặp
✓ **Parallel-friendly**: Các kiến hoạt động độc lập
✓ **Xử lý vòng lặp**: Có cơ chế loại bỏ vòng lặp trong đường đi

### 9.2. Nhược điểm
✗ **Chậm**: Cần nhiều vòng lặp để hội tụ
✗ **Không đảm bảo tối ưu**: Chỉ tìm được gần tối ưu
✗ **Phụ thuộc tham số**: Kết quả phụ thuộc nhiều vào p, Q
✗ **Memory**: Cần lưu trữ pheromone cho tất cả cạnh
✗ **Stochastic**: Kết quả có thể khác nhau mỗi lần chạy

## 10. Ví dụ thực thi

### 10.1. Ví dụ cơ bản
```bash
python aco_resolve_path.py 20 50 map1.txt 0.3 100
```
- 20 kiến
- 50 vòng lặp
- Bản đồ map1.txt
- p = 0.3 (30% bay hơi)
- Q = 100
- Không hiển thị đồ họa

### 10.2. Ví dụ với hiển thị
```bash
python aco_resolve_path.py 30 100 map2.txt 0.5 50 -d
```
- 30 kiến
- 100 vòng lặp
- Bản đồ map2.txt
- p = 0.5 (50% bay hơi)
- Q = 50
- Hiển thị đồ họa (-d)

### 10.3. Output mẫu
```
Iteration: 0  length of the path: 15
Iteration: 1  length of the path: 13
Iteration: 2  length of the path: 12
Iteration: 3  length of the path: 11
...
Iteration: 49  length of the path: 9
[(0, 1), (1, 1), (1, 2), (2, 2), (3, 2), (3, 3), (3, 4), (4, 4), (4, 3)]
```

## 11. Các trường hợp đặc biệt

### 11.1. Không tồn tại đường đi
- Giải thuật sẽ không hội tụ
- Kiến sẽ không thể đến đích
- Chương trình có thể bị treo trong vòng lặp vô hạn

### 11.2. Nhiều đường đi tối ưu
- Giải thuật có thể tìm ra bất kỳ đường nào
- Kết quả phụ thuộc vào random seed
- Tất cả đường có độ dài bằng nhau đều là kết quả chấp nhận được

### 11.3. Bản đồ nhỏ
- Với bản đồ rất nhỏ, có thể cần ít vòng lặp hơn
- Số kiến có thể giảm xuống

### 11.4. Bản đồ phức tạp
- Cần tăng số kiến và vòng lặp
- Có thể cần điều chỉnh p và Q

## 12. Lưu ý kỹ thuật

### 12.1. Phiên bản Python
- Code được viết cho **Python 2.x**
- Cần sửa đổi để chạy trên Python 3.x:
  - `print path` → `print(path)` (line 38, 186 trong ant_colony.py)
  - Cần cập nhật syntax cho print statements
  - Có thể cần thay đổi cách import trong __init__.py nếu gặp vấn đề về relative imports

### 12.2. Dependencies
- numpy: Xử lý mảng và tính toán
- matplotlib: Hiển thị đồ họa

### 12.3. Giới hạn
- Chỉ hỗ trợ di chuyển 8 hướng (kể cả đường chéo)
- Mỗi bước di chuyển có chi phí bằng nhau (không có trọng số)
- Không xem xét độ dài thực tế (Manhattan vs Euclidean distance)

## 13. Tổng kết

### 13.1. Bản chất của giải thuật
ACO Path Planning là một giải thuật meta-heuristic dựa trên:
- **Collective intelligence**: Trí tuệ tập thể của đàn kiến
- **Positive feedback**: Đường tốt được tăng cường
- **Probability**: Quyết định dựa trên xác suất
- **Memory**: Lưu trữ thông tin qua pheromone

### 13.2. Quy trình tóm tắt
```
Input: Map, Parameters → Initialize → Iterations Loop → Output: Best Path
```

### 13.3. Khi nào nên sử dụng
- Bài toán tìm đường trên grid/graph
- Không cần đảm bảo tối ưu tuyệt đối
- Có thể chấp nhận thời gian tính toán
- Muốn giải pháp robust và adaptive

---

## Tài liệu tham khảo

1. Dorigo, M., & Stützle, T. (2004). Ant Colony Optimization. MIT Press.
2. Repository: https://github.com/huynhunguyen/ACO_path_planning_org
3. Ant System Algorithm: Dorigo, M. (1992). Optimization, Learning and Natural Algorithms. PhD thesis.
