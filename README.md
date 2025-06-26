MuCoSim Seminar: MD-Bench Performance Analysis on AMD Milan and Intel Sapphire Rapids
Student: Ashwin Varkey (ashwin.varkey@fau.de)
Matriculation Number: 23115420
Seminar: MuCoSim WS 2024/25
📋 Project Overview
This project presents a comprehensive performance analysis of MD-Bench (Molecular Dynamics Benchmark) on two different HPC architectures: AMD Milan and Intel Sapphire Rapids. The study focuses on comparing Verlet List and Cluster Pair algorithms across various SIMD vectorization modes, precision formats, and neighbor list configurations.
🎯 Objectives
Phase 1: AMD Milan Analysis

Platform: AMD EPYC 7543 32-Core Processor (Zen3 architecture)
Focus: Verlet List algorithm optimization
Goals: Analyze SIMD vector length performance, compare full vs. half neighbor lists, and evaluate single vs. double precision

Phase 2: Intel Sapphire Rapids Analysis

Platform: Intel Xeon Platinum 8470 processor (Sapphire Rapids)
Focus: Verlet List vs. Cluster Pair comparison
Goals: Energy efficiency analysis, performance scaling, and algorithm comparison

🏗️ Application Details
MD-Bench is a performance-oriented prototyping harness for state-of-the-art Molecular Dynamics (MD) algorithms, developed by the Erlangen National High Performance Computing Center (NHR@FAU).

Repository: MD-Bench GitHub
Purpose: Performance engineering of short-range force calculation kernels
Test Case: Solid copper molecular dynamics simulation

🛠️ System Specifications
AMD Milan System

CPU: AMD EPYC 7543 32-Core Processor (Zen3)
Base Clock: 2.79 GHz (3.8 GHz Turbo)
Memory: 251.56 GB
Cores: 64 cores per node
Compiler: GCC 11.2.0
Tools: LIKWID 5.3.0

Intel Sapphire Rapids System

CPU: Intel Xeon Platinum 8470 processor
Base Clock: 2.00 GHz (3.80 GHz Turbo)
Memory: 503 GB
Cores: 52 cores, 2 threads per core (SMT active)
Compiler: Intel 2023.2.1 (ICC)
Tools: LIKWID 5.4.0

⚙️ Configuration Parameters
Build Configuration
makefileTOOLCHAIN: GCC/ICC
ISA: x86
SIMD: NONE, SSE, AVX2, AVX512
OPT_SCHEME: Verlet List (VL), Cluster Pair (CP)
ENABLE_LIKWID: True
DATA_TYPE: SP (Single Precision), DP (Double Precision)
DATA_LAYOUT: AOS (Array of Structures)
ENABLE_OMP_SIMD: True
Build Instructions
bash# Edit configuration
vim config.mk

# Build the application
make

# Allocate compute node
salloc -t 03:00:00 --exclusive -w <node> -c <cores> -p work -C hwperf
📊 Key Analysis Areas
1. Runtime Profiling

Force computation bottleneck identification
Reneighboring region memory analysis
SIMD vectorization impact assessment

2. Instruction Count Analysis
bash# SIMD arithmetic instruction analysis
likwid-perfctr -C S0:1 -g RETIRED_MMX_FP_INSTR_SSE:PMC0 -m ./MDBench-VL-GCC-X86-DP

# Advanced metrics (AMD Genoa reference)
likwid-perfctr -C 0 -g RETIRED_PACKED_FP_OPS_FP256_ADD:PMC0 ./MDBench-VL-GCC-X86-AVX2-DP
3. Performance Analysis
bash# Cache and memory bandwidth analysis
likwid-perfctr -C S0:1 -g L2/L3/MEM1/CACHE -m ./MDBench-VL-GCC-X86-DP
4. Energy Efficiency Analysis
bash# Energy consumption measurement
srun --cpu-freq=3800000-3800000:performance
likwid-perfctr -c S0:0@S1:0 -g ENERGY ./MDBench-VL-ICC-X86-AVX512-DP
🔍 Key Findings
AMD Milan Results

SIMD Impact: 3-4× performance improvement with AVX vectorization
Precision: Single precision shows marginal performance gains
Neighbor Lists: Full neighbor lists optimize SIMD better than half neighbor lists
Bottlenecks: Force computation (83% of runtime) is compute-bound

Intel Sapphire Rapids Results

Algorithm Comparison: Cluster Pair outperforms Verlet List significantly
Vectorization: Cluster Pair achieves 100% SIMD utilization vs. ~95% for Verlet List
Energy Efficiency: AVX-512 provides best performance-per-watt ratio
Scalability: Verlet List scales linearly; Cluster Pair limited by workload imbalance

Performance Metrics Comparison
AlgorithmArchitectureSIMD ModePerformance (GFlops/s)Energy EfficiencyVerlet ListAMD MilanAVX215.3ModerateVerlet ListIntel SPRAVX51218.9GoodCluster PairIntel SPRAVX51248.2Excellent
📈 Visualization and Analysis
The project includes comprehensive performance visualization:

Runtime Profiles: Bottleneck identification across different configurations
Instruction Analysis: SIMD vectorization efficiency metrics
Energy Z-plots: Energy-delay product optimization
Scalability Studies: Core count vs. performance scaling
Memory Analysis: Cache hierarchy utilization patterns

🔬 Research Insights
SIMD Optimization

Vectorization Ratio: Cluster Pair achieves near-perfect SIMD utilization
Instruction Efficiency: Higher SIMD modes reduce total instruction counts
Performance Scaling: Linear improvement with vector width increases

Memory Hierarchy

Cache Reuse: L2 cache shows 2× data reuse patterns
Bandwidth Utilization: Kernels operate well below memory bandwidth limits
Data Layout Impact: Array-of-Struct-of-Arrays (AoSoA) layout benefits SIMD

Energy Efficiency

Frequency Scaling: Performance scales linearly with frequency up to turbo limits
SIMD Energy Benefits: Wider SIMD modes provide better energy-delay products
Algorithm Impact: Cluster Pair demonstrates superior energy efficiency

📁 Repository Structure
├── phase1_amd_milan/
│   ├── runtime_profiles/
│   ├── instruction_analysis/
│   └── performance_results/
├── phase2_intel_spr/
│   ├── algorithm_comparison/
│   ├── energy_analysis/
│   ├── scalability_studies/
│   └── mpi_analysis/
├── configs/
│   ├── milan_config.mk
│   └── spr_config.mk
├── scripts/
│   ├── benchmark_runner.sh
│   └── analysis_tools.sh
└── results/
    ├── graphs/
    └── summary_reports/
🚀 Running the Benchmarks
Basic Execution
bash# Configure for target architecture
cp configs/milan_config.mk config.mk  # or spr_config.mk
make clean && make

# Run with LIKWID profiling
likwid-perfctr -C 0 -g FLOPS_DP -m ./MDBench-VL-GCC-X86-DP
Advanced Analysis
bash# Frequency scaling study
srun --cpu-freq=1600000-1600000:performance likwid-perfctr -C 0 -g FLOPS_DP -m ./MDBench

# Multi-core scaling
srun -n 52 --mpi=pmi2 ./MDBench-CP-ICC-X86-AVX512-DP

# Energy analysis
likwid-perfctr -c S0:0@S1:0 -g ENERGY ./MDBench-VL-ICC-X86-AVX512-DP
📊 Performance Summary
Key Performance Indicators
AMD Milan (Zen3):

Best SIMD Mode: AVX2
Optimal Configuration: Full Neighbor + Double Precision
Peak Performance: 15.3 GFlops/s
Scaling: Linear up to 32 cores

Intel Sapphire Rapids:

Best Algorithm: Cluster Pair
Best SIMD Mode: AVX-512
Peak Performance: 48.2 GFlops/s (Cluster Pair)
Scaling: Linear up to 40 cores (Cluster Pair)

🎯 Conclusions

Algorithm Efficiency: Cluster Pair significantly outperforms Verlet List on modern architectures with advanced SIMD capabilities
SIMD Utilization: Proper data layout (AoSoA) is crucial for maximizing SIMD efficiency
Energy Optimization: Higher SIMD modes provide better energy-delay products despite increased power consumption
Scalability: Algorithm choice impacts parallel scalability, with Verlet List showing better scaling characteristics
Architecture Impact: Modern processors with wider SIMD units benefit more from algorithm optimizations

🤝 Contributing
This analysis is part of the MuCoSim seminar series. For questions or collaboration:
Contact: Ashwin Varkey - ashwin.varkey@fau.de
📚 References

MD-Bench Repository
LIKWID Performance Tools
NHR@FAU Documentation

📄 License
This project is part of academic research at Friedrich-Alexander-Universität Erlangen-Nürnberg. Results and methodologies are available for academic use and collaboration.

Performance analysis conducted as part of the MuCoSim Seminar WS 2024/25 at Friedrich-Alexander-Universität Erlangen-Nürnberg.
