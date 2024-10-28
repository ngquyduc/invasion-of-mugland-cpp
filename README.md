# Invasion of Mugland Simulation

A high-performance parallel implementation of the Invasion of Mugland simulation using OpenMP and C++. This project demonstrates advanced parallel programming techniques and performance optimization strategies.

## 👥 Authors

- Nguyen Quy Duc
- Pham Ba Thang

## 🎯 Overview

This simulation implements a cellular automaton that models the invasion of Mugland, utilizing OpenMP for parallel processing to achieve significant performance improvements across different matrix sizes and generations.

## 🚀 Key Features

- Parallel processing using OpenMP
- Configurable thread count
- Support for various matrix sizes
- Performance optimized for large-scale simulations
- Death toll tracking across generations

## 💻 Technical Implementation

### Architecture

```cpp
// Main simulation loop with OpenMP parallelization
#pragma omp parallel for reduction(+ : deathToll) collapse(2)
for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        // Process cell state changes and calculate death toll
    }
}
```

### Parallel Strategy

1. **Thread Management**

   ```cpp
   omp_set_num_threads(nThreads);  // Set thread count
   ```

2. **Work Distribution**

   - Parallelized nested loops using collapse
   - Optimized chunk distribution
   - Reduction for death toll calculation

3. **Performance Optimizations**
   - Minimized false sharing
   - Loop tiling for cache efficiency
   - Optimized condition checking order

## 📊 Performance Analysis

### Test Configurations

- **Small**: 7x7 matrix, 10 generations
- **Medium**: 20x20 matrix, 100,000 generations
- **Large**: 50x40 matrix, 100,000 generations

### Benchmark Results

#### Intel i7-7700 (8 cores)

| Threads | Small (s)          | Medium (s)       | Large (s)       |
| ------- | ------------------ | ---------------- | --------------- |
| 1       | 0.01036 ±0.00210   | 3.9298 ±0.0281   | 19.6424 ±0.0391 |
| 4       | 0.005949 ±0.000223 | 1.29737 ±0.00547 | 6.362 ±0.1060   |
| 8       | 0.006346 ±0.000443 | 1.45810 ±0.00721 | 6.6376 ±0.0518  |

#### Xeon Silver 4114 (20 cores)

| Threads | Small (s)        | Medium (s)       | Large (s)       |
| ------- | ---------------- | ---------------- | --------------- |
| 1       | 0.01807 ±0.00620 | 3.91016 ±0.00738 | 19.6627 ±0.0335 |
| 4       | 0.01538 ±0.00326 | 1.3341 ±0.0400   | 5.9537 ±0.0267  |
| 8       | 0.01648 ±0.00307 | 1.5218 ±0.0430   | 1.3341 ±0.0400  |

## 🛠️ Performance Optimizations

### 1. Efficient Cell Processing

```cpp
// Optimized cell state calculation
void calculateNextState(int i, int j) {
    // Check invasion first as it's deterministic
    if (checkInvasion(i, j)) return;

    // Get neighbor counts once
    auto counts = getNeighborCounts(i, j);

    // Combined condition checking
    if (checkFighting(counts)) return;
    if (checkReproduction(counts)) return;
    if (checkOverpopulation(counts)) return;

    // Survival is implicit if no other conditions met
}
```

### 2. Cache Optimization

```cpp
// Loop tiling implementation
const int TILE_SIZE = 32;
#pragma omp parallel for collapse(2)
for (int i = 0; i < rows; i += TILE_SIZE) {
    for (int j = 0; j < cols; j += TILE_SIZE) {
        processTile(i, j, min(TILE_SIZE, rows - i),
                         min(TILE_SIZE, cols - j));
    }
}
```

## 🚀 Getting Started

### Prerequisites

- C++ compiler with OpenMP support
- Make build system
- Performance measurement tools (perf)

### Building

```bash
# Clone the repository
git clone https://github.com/yourusername/invasion-simulation.git

# Build the project
make

# Run with specific configuration
./run_slurm.sh ./large_input.in ./large_input.out <num_threads>
```

### Running Tests

```bash
# Run performance tests
./run_tests.sh

# Generate test cases
./generate_tests.sh
```

## 📈 Key Findings

### Performance Patterns

1. **Thread Scaling**

   - Optimal performance at 4 threads for medium workloads
   - Diminishing returns with 8 threads due to overhead

2. **Workload Impact**

   - Generation count significantly affects speedup
   - Matrix size affects parallelization efficiency

3. **Hardware Differences**
   - Similar patterns across different CPU architectures
   - Better scaling on Xeon for larger matrices

## 📚 References

- OpenMP Documentation
- Parallel Programming Patterns
- Cache Optimization Techniques
