# ARRAYS

---

## 🟦 LEVEL 1: ARRAY FUNDAMENTALS (Foundation Layer)

### 1️⃣ **What is an Array**

- Definition - contiguous memory block
- Fixed size at compile-time (static arrays)
- Homogeneous elements (same type)
- Zero-based indexing
- Why arrays exist (locality, cache efficiency)

### 2️⃣ **Array Declaration & Initialization**

- Declaration syntax: `int arr[5];`
- Initialization at declaration: `int arr[5] = {1, 2, 3, 4, 5};`
- Partial initialization: `int arr[5] = {1, 2};` (rest are zero)
- Zero initialization: `int arr[5] = {};` or `int arr[5] = {0};`
- Aggregate initialization
- Uniform initialization (C++11): `int arr[5]{1, 2, 3};`
- Array size deduction: `int arr[] = {1, 2, 3};` (compiler deduces size)
- Character array initialization: `char str[] = "hello";`
- Designated initializers (C++20): `int arr[5] = {[0] = 1, [4] = 5};`

### 3️⃣ **Array Indexing & Access**

- Square bracket notation: `arr[i]`
- Pointer notation: `*(arr + i)`
- Equivalence: `arr[i]` ≡ `*(arr + i)` ≡ `i[arr]` ≡ `*(i + arr)`
- Index bounds (0 to size-1)
- Front and back access: `arr[0]`, `arr[size-1]`

### 4️⃣ **Array Size**

- `sizeof(arr)` - total bytes
- `sizeof(arr) / sizeof(arr[0])` - number of elements
- Size at compile-time only
- C++17: `std::size(arr)` from `<iterator>`
- Limitations of sizeof after decay

---

## 🟩 LEVEL 2: MEMORY LAYOUT & REPRESENTATION

### 5️⃣ **Memory Layout**

- Contiguous memory allocation
- Elements stored back-to-back
- Starting address (base address)
- Memory address calculation: `base_address + (index × sizeof(element))`
- Stack allocation for static arrays
- Memory alignment
- Padding between elements (usually none for primitives)
- Cache-friendly access patterns

### 6️⃣ **Stack vs Heap Arrays**

- **Stack arrays**: `int arr[100];`
    - Automatic storage duration
    - Fast allocation/deallocation
    - Stack size limits (~1-8 MB)
    - Automatically cleaned up
- **Heap arrays**: `int* arr = new int[100];`
    - Dynamic allocation
    - Manual deallocation required (`delete[]`)
    - Larger size possible
    - Risk of memory leaks
    - Slower allocation

### 7️⃣ **Array Decay to Pointer**

- **What is decay**: Array name converts to pointer to first element
- When decay happens:
    - Passing to functions
    - Assignment to pointer
    - Arithmetic operations
- When decay doesn't happen:
    - `sizeof(arr)`
    - `&arr` (address-of array)
    - Initialization: `int arr2[5] = arr;` (not allowed)
- Loss of size information after decay
- Type difference: `int[5]` vs `int*`

### 8️⃣ **Arrays and Pointers Relationship**

- Array name as constant pointer
- Cannot reassign array name: `arr = other_arr;` (error)
- Pointer arithmetic on arrays
- Difference between `int arr[5]` and `int* ptr`
- Array is not a pointer (but decays to one)
- `&arr` vs `arr` vs `&arr[0]`
- Pointer to array: `int (*ptr)[5] = &arr;`

---

## 🟨 LEVEL 3: ARRAY OPERATIONS

### 9️⃣ **Traversal (Iteration)**

- **Index-based loop**:
    
    ```cpp
    for (int i = 0; i < size; i++) { arr[i] }
    ```
    
- **Pointer-based loop**:
    
    ```cpp
    for (int* p = arr; p != arr + size; p++) { *p }
    ```
    
- **Range-based for loop** (C++11):
    
    ```cpp
    for (int x : arr) { x }for (int& x : arr) { x }  // By reference
    ```
    
- **Iterator-based** (with std::begin/end):
    
    ```cpp
    for (auto it = std::begin(arr); it != std::end(arr); ++it)
    ```
    

### 🔟 **Array Algorithms (Manual Implementation)**

- **Linear search**
- **Binary search** (on sorted array)
- **Insertion** (shifting elements)
- **Deletion** (shifting elements)
- **Rotation** (left/right)
- **Reversal**
- **Sorting** (bubble, selection, insertion, merge, quick)
- **Finding min/max**
- **Sum and average**
- **Frequency counting**

### 1️⃣1️⃣ **Array Manipulation Patterns**

- Two-pointer technique
- Sliding window
- Prefix sum array
- Difference array
- Kadane's algorithm (max subarray)
- Dutch National Flag (3-way partitioning)
- In-place modifications
- Cyclic rotation

---

## 🟧 LEVEL 4: MULTIDIMENSIONAL ARRAYS

### 1️⃣2️⃣ **2D Arrays (Matrices)**

- **Declaration**: `int arr[3][4];`
- **Initialization**:
    
    ```cpp
    int arr[2][3] = {{1, 2, 3}, {4, 5, 6}};int arr[2][3] = {1, 2, 3, 4, 5, 6};  // Row-major
    ```
    
- **Row-major order** (C++ memory layout)
- **Accessing elements**: `arr[i][j]`
- **Size calculation**: `rows × cols × sizeof(element)`

### 1️⃣3️⃣ **2D Array Memory Layout**

- Contiguous storage in row-major order
- Memory address: `base + (i × cols + j) × sizeof(element)`
- Flattening: 2D to 1D mapping
- Cache locality implications
- Pointer arithmetic in 2D arrays

### 1️⃣4️⃣ **Jagged Arrays (Array of Arrays)**

- Declaration: `int* arr[3];` (array of pointers)
- Each row can have different length
- Manual allocation:
    
    ```cpp
    int* arr[3];arr[0] = new int[5];arr[1] = new int[3];arr[2] = new int[7];
    ```
    
- Memory layout: non-contiguous rows
- Deallocation requires loop

### 1️⃣5️⃣ **3D and N-Dimensional Arrays**

- Declaration: `int arr[2][3][4];`
- Memory layout: still contiguous
- Address calculation: `base + ((i × dim2 × dim3) + (j × dim3) + k) × sizeof(element)`
- Use cases: tensors, 3D graphics, scientific computing
- Practical limit: readability and cache efficiency

### 1️⃣6️⃣ **Dynamic 2D Arrays**

- **Method 1: Array of pointers**:
    
    ```cpp
    int** arr = new int*[rows];for (int i = 0; i < rows; i++)    arr[i] = new int[cols];
    ```
    
- **Method 2: Single allocation**:
    
    ```cpp
    int* arr = new int[rows * cols];// Access: arr[i * cols + j]
    ```
    
- **Method 3: std::vector of vectors**:
    
    ```cpp
    vector<vector<int>> arr(rows, vector<int>(cols));
    ```
    

---

## 🟥 LEVEL 5: FUNCTIONS & ARRAYS

### 1️⃣7️⃣ **Passing Arrays to Functions**

- **Decay to pointer**: `void func(int arr[])`
- **Explicit pointer**: `void func(int* arr)`
- **Size parameter required**: `void func(int arr[], int size)`
- **Cannot pass by value** (arrays don't copy)
- **Pass by reference** (C++11):
    
    ```cpp
    template<size_t N>void func(int (&arr)[N])
    ```
    
- **Const arrays**: `void func(const int arr[], int size)`

### 1️⃣8️⃣ **Returning Arrays from Functions**

- **Cannot return local array**:
    
    ```cpp
    int* func() {    int arr[5] = {1, 2, 3, 4, 5};    return arr;  // DANGER! Undefined behavior}
    ```
    
- **Return pointer to dynamically allocated array**:
    
    ```cpp
    int* func() {    int* arr = new int[5]{1, 2, 3, 4, 5};    return arr;  // Caller must delete[]}
    ```
    
- **Return std::array or std::vector** (modern approach)
- **Return via output parameter**:
    
    ```cpp
    void func(int arr[], int size)
    ```
    

### 1️⃣9️⃣ **Array Size Deduction in Functions**

- **Template with size**:
    
    ```cpp
    template<size_t N>void func(int (&arr)[N]) {    // N is compile-time constant}
    ```
    
- **sizeof works correctly** (no decay)
- **Constrains function to specific size**

---

## 🟪 LEVEL 6: ADVANCED ARRAY CONCEPTS

### 2️⃣0️⃣ **Variable Length Arrays (VLA)**

- **NOT standard C++** (but GCC extension)
- Size determined at runtime:
    
    ```cpp
    int n;cin >> n;int arr[n];  // Non-standard!
    ```
    
- **Avoid in portable code**
- Use `std::vector` instead

### 2️⃣1️⃣ **Array Bounds Checking**

- **C-style arrays**: No bounds checking
- **Undefined behavior on out-of-bounds access**
- **Buffer overflow vulnerabilities**
- **at() method**: Only in `std::array` and `std::vector`
- **Manual validation required**

### 2️⃣2️⃣ **Array as Function Pointer Storage**

- Array of function pointers:
    
    ```cpp
    int (*arr[5])(int, int);  // Array of 5 function pointers
    ```
    
- Use case: dispatch tables, state machines
- Modern alternative: `std::function` in `std::vector`

### 2️⃣3️⃣ **Const Arrays**

- **Const elements**: `const int arr[5] = {1, 2, 3, 4, 5};`
- Cannot modify elements
- Compiler optimizations
- Read-only memory placement (sometimes)
- **Const pointer to array**: `int* const ptr = arr;`
- **Pointer to const array**: `const int* ptr = arr;`

### 2️⃣4️⃣ **Static Arrays in Functions**

- **Static local array**:
    
    ```cpp
    void func() {    static int arr[5] = {1, 2, 3, 4, 5};    // Persists across function calls}
    ```
    
- Initialized once
- Lifetime: entire program
- Retains values between calls

### 2️⃣5️⃣ **Array Aliasing & Strict Aliasing Rule**

- Accessing array through incompatible pointer type
- Undefined behavior:
    
    ```cpp
    int arr[5];float* ptr = (float*)arr;  // Type punning*ptr = 3.14f;  // UB!
    ```
    
- Exception: `char*` or `unsigned char*` can alias anything
- Compiler optimizations assume no aliasing

---

## 🟫 LEVEL 7: C++11 STL ARRAYS

### 2️⃣6️⃣ **std::array<T, N>**

- **Fixed-size container** (C++11)
- Stack-allocated like C arrays
- **No decay to pointer** (unless explicit)
- **Size known at compile-time**
- **Declaration**:
    
    ```cpp
    std::array<int, 5> arr = {1, 2, 3, 4, 5};
    ```
    
- Template parameters: type and size

### 2️⃣7️⃣ **std::array Features**

- **size()** method (returns compile-time constant)
- **at()** method (bounds-checked access)
- **front()** / **back()** methods
- **data()** - raw pointer access
- **fill()** - set all elements to value
- **swap()** - exchange contents
- **Iterators**: `begin()`, `end()`, `rbegin()`, `rend()`
- **empty()** method
- **Comparison operators** (==, !=, <, >, <=, >=)

### 2️⃣8️⃣ **std::array vs C-style Arrays**

|Feature|C Array|std::array|
|---|---|---|
|Size tracking|No|Yes|
|Bounds checking|No|Yes (at())|
|Decay|Always|Only explicit|
|Pass to functions|Decays|By value/ref|
|STL algorithms|Manual|Direct|
|Assignment|No|Yes|
|Comparison|No|Yes|
|Zero overhead|Yes|Yes|

### 2️⃣9️⃣ **std::array Edge Cases**

- **Empty array**: `std::array<int, 0> arr;` (valid, size 1 byte)
- **Large arrays**: Stack overflow risk (same as C arrays)
- **Non-copyable types**: `std::array<std::unique_ptr<int>, 5>`
- **Template deduction**: C++17 CTAD
    
    ```cpp
    std::array arr{1, 2, 3};  // Deduces std::array<int, 3>
    ```
    

---

## ⚫ LEVEL 8: DYNAMIC ARRAYS (std::vector)

### 3️⃣0️⃣ **std::vector Basics**

- **Dynamic size** (resizable at runtime)
- **Heap-allocated** contiguous memory
- **Automatic memory management**
- **Declaration**:
    
    ```cpp
    std::vector<int> vec;std::vector<int> vec(10);        // 10 default elementsstd::vector<int> vec(10, 5);     // 10 elements, all 5std::vector<int> vec{1, 2, 3};   // Initializer list
    ```
    

### 3️⃣1️⃣ **Capacity vs Size**

- **size()**: Number of elements
- **capacity()**: Allocated space
- **reserve(n)**: Pre-allocate space (no construction)
- **shrink_to_fit()**: Reduce capacity to size
- **Reallocation** when size exceeds capacity
- **Growth factor**: Usually 1.5x or 2x

### 3️⃣2️⃣ **Vector Operations**

- **push_back()**: Add element at end
- **emplace_back()**: Construct in-place (C++11)
- **pop_back()**: Remove last element
- **insert()**: Insert at position (expensive)
- **erase()**: Remove element(s)
- **clear()**: Remove all elements (size = 0)
- **resize()**: Change size (construct/destroy as needed)
- **assign()**: Replace contents

### 3️⃣3️⃣ **Vector Memory Management**

- **Contiguous storage guarantee**
- **Reallocation invalidates iterators/pointers**
- **Move semantics** for efficiency (C++11)
- **Small vector optimization** (not standard, some implementations)
- **Custom allocators** support

### 3️⃣4️⃣ **Vector of Vectors (2D Dynamic Array)**

- **Declaration**: `std::vector<std::vector<int>> matrix;`
- **Initialization**:
    
    ```cpp
    vector<vector<int>> matrix(rows, vector<int>(cols, 0));
    ```
    
- **Jagged array** support (rows of different sizes)
- **Non-contiguous** memory (each row separate allocation)
- **Cache unfriendly** compared to flat array

### 3️⃣5️⃣ **Vector vs Array Performance**

- **Array advantages**: No allocation overhead, cache-local
- **Vector advantages**: Dynamic sizing, RAII
- **When to use array**: Size known at compile-time, performance critical
- **When to use vector**: Size unknown, need flexibility

---

## 🔴 LEVEL 9: SPECIALIZED ARRAY TYPES

### 3️⃣6️⃣ **std::valarray**

- **Numerical array** for math operations
- Element-wise operations
- Slicing support
- **Not commonly used** (prefer Eigen, etc.)
- Example:
    
    ```cpp
    std::valarray<int> arr{1, 2, 3, 4, 5};arr = arr * 2 + 1;  // Element-wise
    ```
    

### 3️⃣7️⃣ **std::span (C++20)**

- **Non-owning view** of contiguous sequence
- **Replaces pointer + size pattern**
- Works with C arrays, std::array, std::vector
- **Declaration**:
    
    ```cpp
    void func(std::span<int> data) {    // Works with any contiguous container}
    ```
    
- **Fixed or dynamic extent**:
    
    ```cpp
    std::span<int, 5> fixed;     // Compile-time sizestd::span<int> dynamic;       // Runtime size
    ```
    

### 3️⃣8️⃣  **`std::deque`**

- **Double-ended queue**
    
- Fast insertion and deletion at **both front and back**
    
- **Not contiguous** (uses chunked storage)
    
- Random access is **slower than `std::vector`**
    
- No full reallocation → **better iterator stability** than vector
    

---

## **3️⃣9️⃣ Bit Arrays**

### **`std::bitset<N>`**

- Fixed-size bit array (compile-time size)
    
- Extremely fast and memory-efficient
    

```cpp
std::bitset<8> bits("10110101");
bits[0] = 1;
bits.flip();
```

### **`std::vector<bool>`**

- Space-optimized (1 bit per element)
    
- **Controversial**: not a real container
    
- Uses proxy references → unexpected behavior
    

### **Bit Manipulation**

- Bitwise operators (`& | ^ ~ << >>`)
    
- Masking, toggling, testing bits
    
- Common in CP, embedded, and performance code
    

---

## **4️⃣0️⃣ Character Arrays (C-Strings)**

- **Null-terminated strings**
    
    ```cpp
    char str[] = "hello";
    ```
    
- Stored as characters ending with `'\0'`
    
- **`strlen()` vs `sizeof()`**
    
- Classic functions (unsafe):
    
    - `strcpy`, `strcat`, `strcmp`
        
- **Modern alternative**: `std::string`
    
- **C++17**: `std::string_view` (non-owning view)
    

---

# ☠️ **LEVEL 10: UNDEFINED BEHAVIOR & PITFALLS**

## **4️⃣1️⃣ Common Array Bugs**

### Out-of-bounds access

```cpp
int arr[5];
arr[10] = 42;   // UB
```

### Uninitialized arrays

```cpp
int arr[5];
cout << arr[0];  // Garbage
```

### Returning local array

```cpp
int* func() {
    int arr[5];
    return arr;   // UB
}
```

### Dangling pointer

```cpp
int* arr = new int[5];
delete[] arr;
arr[0] = 1;      // UB
```

---

## **4️⃣2️⃣ Memory Leaks with Arrays**

- Forgetting `delete[]`
    
- Mismatching `new[]` with `delete`
    

```cpp
int* arr = new int[5];
delete arr;   // WRONG → UB
```

- Exception safety issues
    

---

## **4️⃣3️⃣ Array Size Misconceptions**

### Array decay

```cpp
void func(int arr[]) {
    sizeof(arr);   // size of int*, not array
}
```

### Size in parameters is ignored

```cpp
void func(int arr[100]) {
    // still just int*
}
```

---

## **4️⃣4️⃣ Buffer Overflow**

- Stack smashing
    
- Heap corruption
    
- Security exploits
    
- Mitigations:
    
    - Bounds checking
        
    - ASLR, stack canaries
        
- Tools:
    
    - Valgrind
        
    - AddressSanitizer
        
    - Static analyzers
        

---

## **4️⃣5️⃣ Aliasing Issues**

- Overlapping `memcpy` → UB (use `memmove`)
    
- Type punning through arrays
    
- Pointer arithmetic out of bounds
    

---

# 🧠 **LEVEL 11: PERFORMANCE & OPTIMIZATION**

## **4️⃣6️⃣ Cache Efficiency**

- Spatial locality
    
- Temporal locality
    
- Cache lines (≈64 bytes)
    
- Row-major vs column-major
    
- SoA vs AoS
    

---

## **4️⃣7️⃣ SIMD & Vectorization**

- Auto-vectorization
    
- Alignment (16/32 bytes)
    
- Intrinsics (SSE/AVX)
    
- Flags: `-O3 -march=native`
    

```cpp
alignas(32) int arr[100];
```

---

## **4️⃣8️⃣ Memory Access Patterns**

- Sequential → fastest
    
- Strided → slower
    
- Random → slowest
    
- False sharing in multithreading
    

---

## **4️⃣9️⃣ Padding & Alignment**

- Natural alignment
    
- Cache-line padding
    
- `alignas`
    
- Memory overhead trade-offs
    

---

## **5️⃣0️⃣ Array Optimizations**

- Loop fusion
    
- Loop tiling
    
- Strength reduction
    
- Constant folding
    
- Inlining
    

---

# 🎮 **LEVEL 12: COMPETITIVE PROGRAMMING**

## **5️⃣1️⃣ Fast I/O**

```cpp
ios::sync_with_stdio(false);
cin.tie(nullptr);
```

```cpp
for (int& x : arr) cin >> x;
```

---

## **5️⃣2️⃣ Core Techniques**

- Prefix sum
    
- Difference array
    
- Sliding window
    
- Two pointers
    
- Binary search
    
- Frequency array
    
- Coordinate compression
    

---

## **5️⃣3️⃣ Array-Based DS**

- Stack
    
- Queue (circular)
    
- Deque
    
- Heap
    
- DSU
    
- Fenwick Tree
    
- Segment Tree
    
- Sparse Table
    

---

## **5️⃣4️⃣ Matrix Operations**

- Multiplication
    
- Exponentiation
    
- Transpose
    
- Rotation
    
- Spiral traversal
    

---

## **5️⃣5️⃣ Problem Patterns**

- Subarrays
    
- Subsequences
    
- Partitioning
    
- Rearrangement
    
- Inversion count
    
- Merge intervals
    

---

# 🔬 **LEVEL 13: SYSTEM & EMBEDDED**

## **5️⃣6️⃣ Embedded Arrays**

- Fixed-size
    
- Static allocation
    
- Memory-mapped I/O
    

```cpp
volatile uint32_t* GPIO = (uint32_t*)0x40020000;
GPIO[0] = 0xFF;
```

---

## **5️⃣7️⃣ Volatile Arrays**

- Prevents optimization
    
- Used for hardware registers
    
- `std::atomic` preferred for threading
    

---

## **5️⃣8️⃣ Shared Memory Arrays**

- IPC
    
- Memory-mapped files
    
- Alignment concerns
    

---

## **5️⃣9️⃣ Serialization**

- Binary serialization
    
- Endianness
    
- Portable formats
    
- Checksums
    

---

# 🌐 **LEVEL 14: MULTITHREADING**

## **6️⃣0️⃣ Thread Safety**

- Data races
    
- Mutexes
    
- Atomic arrays
    

---

## **6️⃣1️⃣ Parallel Processing**

- OpenMP
    
- Execution policies
    

---

## **6️⃣2️⃣ False Sharing**

- Cache line contention
    
- Padding fix
    

```cpp
struct alignas(64) Padded { int value; };
```

---

## **6️⃣3️⃣ Lock-Free Arrays**

- CAS
    
- ABA problem
    
- Memory ordering
    

---

# 🧪 **LEVEL 15: STL & ALGORITHMS**

- `sort`, `find`, `accumulate`
    
- `copy`, `fill`, `reverse`
    
- Binary search variants
    
- Heap algorithms
    
- Permutations
    

---

# 🎯 **LEVEL 16: DESIGN PATTERNS**

- RAII with arrays
    
- `std::span`
    
- Ring buffer
    
- Flyweight
    
- Pool allocators
    

---

# 🔧 **LEVEL 17: DEBUGGING**

- GDB
    
- Valgrind
    
- AddressSanitizer
    
- UBSan
    
- Static analysis
    

---

# 🏆 **LEVEL 18: EXPERT TOPICS**

- Compile-time arrays
    
- SoA vs AoS
    
- Custom allocators
    
- SIMD intrinsics
    
- GPU array processing
    

---

# 📚 **LEVEL 19: STANDARDS**

- C vs C++ arrays
    
- Endianness
    
- Alignment differences
    
- C++ evolution (98 → 23)
    

---

# 🧩 **LEVEL 20: REAL-WORLD USE**

- Graphics (buffers)
    
- Audio (sample arrays)
    
- Networking (packet buffers)
    
- Databases (page arrays)
    
- ML (tensors, matrices)
    
---




