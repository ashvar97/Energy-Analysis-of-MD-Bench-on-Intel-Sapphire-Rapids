# Energy Analysis of MD-Bench on Intel Sapphire Rapids

## Overview
This project focuses on analyzing the **energy consumption and performance** of MD-Bench when executed on **Intel's Sapphire Rapids processors**. With the increasing demand for energy-efficient high-performance computing, this study explores the impact of Sapphire Rapids' architectural innovations on molecular dynamics simulations.

## What is MD-Bench?
[MD-Bench](https://github.com/ashvar97/Energy-Analysis-of-MD-Bench-on-Intel-Sapphire-Rapids) is a **proxy application** designed to emulate **short-range molecular dynamics kernels** used in leading simulation packages such as:
- **LAMMPS** (Large-scale Atomic/Molecular Massively Parallel Simulator)
- **GROMACS** (GROningen MAchine for Chemical Simulations)

It serves as a benchmark for evaluating performance engineering and optimization strategies in molecular dynamics workloads.

## Why Intel Sapphire Rapids?
Intel's **Sapphire Rapids** processors introduce cutting-edge features tailored for high-performance computing (HPC) and AI-driven workloads:
- **DDR5 Memory Support** – Higher bandwidth and efficiency for data-intensive applications.
- **PCIe 5.0** – Faster communication between compute and storage components.
- **Integrated Accelerators** – Hardware-based optimizations for AI, cryptographic workloads, and high-throughput computing.
- **Advanced Power Management** – Enhanced power efficiency through dynamic scaling and workload-aware optimizations.

## Key Research Questions
This project aims to answer the following:
- How does **Sapphire Rapids' architecture** affect the **energy consumption** of MD-Bench?
- What are the **performance trade-offs** between power efficiency and computational speed?
- Can hardware optimizations lead to **better energy-to-computation ratios** for molecular dynamics simulations?
- How do different **memory and compute configurations** influence power consumption?

## Methodology
1. **Benchmarking Setup**:
   - Running MD-Bench on **Sapphire Rapids** CPUs under different configurations.
   - Monitoring performance metrics such as **execution time, FLOPS, memory bandwidth, and cache efficiency**.
2. **Energy Measurement**:
   - Utilizing **Intel RAPL (Running Average Power Limit)** for real-time power monitoring.
   - Comparing energy usage across different workloads and optimizations.
3. **Analysis & Optimization**:
   - Evaluating **power-performance trade-offs**.
   - Identifying potential **software and hardware optimizations** for reducing energy consumption.

## Expected Outcomes
- A **detailed energy profile** of MD-Bench on Sapphire Rapids.
- Insights into **power-aware optimizations** for molecular dynamics simulations.
- Recommendations for improving **energy efficiency** in next-generation HPC workloads.


## References
- [Intel Sapphire Rapids White Paper](https://www.intel.com/content/www/us/en/products/docs/processors/xeon/sapphire-rapids.html)
- [LAMMPS](https://www.lammps.org/)
- [GROMACS](https://www.gromacs.org/)


## How to Contribute
If you're interested in contributing to this project, feel free to submit a **pull request** or open an **issue**. Suggestions, bug reports, and performance improvement ideas are always welcome!

---
🚀 **Author**: Ashwin Varkey  
📧 **Contact**: [Email](mailto:ashvar97@gmail.com) | [LinkedIn](https://www.linkedin.com/in/ashvar97/)
