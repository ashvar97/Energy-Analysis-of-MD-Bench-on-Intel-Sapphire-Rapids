
# Log: Energy efficiency and performance analysis verletlist vs clusterpair on Intel Sapphire Rapids


## Project Description

### Student Info

* Name: Ashwin Varkey
* E-Mail: [ashwin.varkey@fau.de](mailto:your_email@fau.de)
* Matriculation number: 23115420

####  The task is to choose an Intel Sapphire Rapids architecture and perform an energy and performance analysis on MD-Bench comparing verletlist and clusterpair for solid copper using one compiler, varying SIMD vector lengths, single vs. double precision study, and full vs. half neighbor list configurations

### Testsystem
* Host/Clustername: saprap2 
* Cluster Info URL: https://doc.nhr.fau.de/clusters/testcluster/
* CPU name: Intel® Xeon® Platinum 8470 processor
* CPU type: Intel Sapphire Rapids processor
* Base Frequency: 2.00 GHz
* Max Turbo Frequency: 3.80 GHz
* Memory capacity: 503 GB
* Number of cores per node: 52 cores 
* Interconnect: PCIe® 
* Threads per core:	2 (SMT active)


### Software Environment
* Compiler: intel/2023.2.1 (ICC) 
* LIKWID Tools: likwid/5.4.0 
* Make Version: GNU Make 4.3
* Slurm: slurm/23.02.7


### How to build software

Begin by editing `config.mk`, which contains various configurations that determine how the code will be compiled and optimized. For our case the conditions are:

- **TOOLCHAIN:** ICC
- **ISA:** x86
- **SIMD:** : NONE, SSE, AVX, AVX512
- **OPT\_SCHEME:** Verlet List (VL) and Cluster Pair (CP)
- **ENABLE\_LIKWID:** True
- **ENABLE_OPENMP:** True
- **DATA\_TYPE:** SP and DP
- **DATA\_LAYOUT:** AOS
- **SORT_ATOMS:** True
- **ONE_ATOM_TYPE:** False
- **ENABLE\_OMP\_SIMD:** True
- **COMPUTE\_STATS:** True  
  

### Build system

The build process outputs files in build/build-<TAG>, where <TAG> represents key configurations (e.g., VL-GCC-X86-AVX2-DP). To run the executable, use:

```bash
./MD-Bench-<TAG>-<OPT_SCHEME> [OPTION]...
```
<TAG> and <OPT_SCHEME> correspond to the specified build options. The Makefile employs pattern rules and automatic dependency handling, so source files added to ./src/verletlist/, ./src/clusterpair/, or ./src/common are included automatically. Changes to any file rebuild all dependent object files. Configuration variables can be overridden from the command line. For example, to build with ICC, run:

```shell=
make TOOLCHAIN=ICC
```

Multiple configurations can be built simultaneously, each producing a unique binary ./MDBENCH-<build tag>. Intermediate results are stored in ./build/build-<build tag>/. Make targets act on the configuration specified in ./config.mk, which can also be overridden on the command line. Default settings in config.mk are generally sufficient for X86-based systems. On NHR@FAU clusters, load the compiler module (e.g., module load intel) before building:

```shell=
make
```



### How to run software
- **Allocating node on testcluster**:  

```bash
salloc -t 03:00:00 --exclusive -w saprap2 -c 64 -p work -C hwperf
```

You can run MD-Bench without any arguments:

```shell=
./MDBench-VL-ICX-X86-AVX2-DP
```


The default behavior and other options can be changed using the following parameters:

```sh
-p <string>:          file to read parameters from 
-f <string>:          force field (lj or eam), default lj
-i <string>:          input file with atom positions (dump)
-e <string>:          input file for EAM
-n / --nsteps <int>:  set number of timesteps for simulation
-nx/-ny/-nz <int>:    set linear dimension of systembox in x/y/z direction
-r / --radius <real>: set cutoff radius
--freq <real>:        processor frequency (GHz)

For this testcase 
likwid-perfctr -m -C 0 -g FLOPS_DP ./MDBench-VL-ICC-X86-AVX512-DP

```
------    

    
<center><strong>Data volume analysis</strong></center>   
<div style="font-size: 0.80em;">

| Metrics (Verletlist)                                 | <span style="color:green;">FN AVX DP</span>      |FN AVX512 DP  | <span style="color:blue;">FN AVX512 SP</span>            | <span style="color:purple;">HN-AVX DP</span>  
| ---------------------------------- | -------------------------------------------- | ---------------------------------------------- | --------------------------------------------- | -------------------------------------------- 
| *SIMD iterations per atom           | <span style="color:green;">19.40</span>       | 9.91    | <span style="color:blue;">76.04</span>        | <span style="color:purple;">10.42</span>      |  
| *SIMD iterations count              | <span style="color:green;">511030162</span>   | 261298044  | <span style="color:blue;">2003183146</span>  | <span style="color:purple;">274415416</span>   | 
| *Cycles per SIMD                    | <span style="color:green;">29.92</span>       | 40.05     | <span style="color:blue;">17.61</span>       | <span style="color:purple;">92.37</span>        | 
| *Useful read data volume [Bytes]    | <span style="color:green;">65580000000</span> | 65580000000 | <span style="color:blue;">40910000000</span> | <span style="color:purple;">35340000000</span> | 65580000000 | 65580000000 |
| *Performance in atom updates [10e6] | <span style="color:green;">2.59</span>        | 3.18       | <span style="color:blue;">1.43</span>        | <span style="color:purple;">1.96</span>        | 
| Data volume per SIMD iteration     | <span style="color:green;">128.33</span>      | 250.98    | <span style="color:blue;">20.42</span>       | <span style="color:purple;">128.78</span>      | 
Metrics in * are not measurd from likwid but provided by application
</div>    
<div style="font-size: 0.90em;text-align: center;">
  <p style="text-align: center;"><em>Table 14: Data Volume Analysis by SIMD Mode for Verletlist at Freq 3.0 GHz </em></p> </div>     


<div style="font-size: 0.80em;">

| Metrics (Clusterpair)                                 | <span style="color:green;">FN AVX DP</span>      | <span style="color:orange;">FN AVX512 DP</span>  | <span style="color:blue;">FN AVX 512 SP</span>   | <span style="color:purple;">HN-AVX DP</span>  |
| ---------------------------------- | -------------------------------------------- | ---------------------------------------------- | --------------------------------------------- | ------------------------------------------ |
| *SIMD iterations per atom           | <span style="color:green;">14.01</span>     | <span style="color:orange;">8.48</span>     | <span style="color:blue;">8.61</span>      | <span style="color:purple;">7.68</span>   |
| *SIMD iterations count              | <span style="color:green;">369240786</span>   | <span style="color:orange;">223510671</span>   | <span style="color:blue;">226849414</span>   | <span style="color:purple;">202345787</span>|
| *Cycles per SIMD                    | <span style="color:green;">48.23</span>     | <span style="color:orange;">39.46</span>    | <span style="color:blue;">64.47</span>     | <span style="color:purple;">56.77</span>   |
| *Useful read data volume [bytes]    | <span style="color:green;">11710000000</span> | <span style="color:orange;">7630000000</span> | <span style="color:blue;">5380000000</span>  | <span style="color:purple;">7040000000</span>|
| *Performance in atom updates [10e6] | <span style="color:green;">2.86</span>        | <span style="color:orange;">5.13</span>       | <span style="color:blue;">3.51</span>        | <span style="color:purple;">4.26</span>     |
| Data volume per SIMD iteration     | <span style="color:green;">31.71</span>       | <span style="color:orange;">34.14</span>      | <span style="color:blue;">23.72</span>       | <span style="color:purple;">26.59</span>    |
Metrics in * are not measurd from likwid but provided by application
</div>     
<div style="font-size: 0.90em;text-align: center;">
  <p style="text-align: center;"><em>Table 15: Data Volume Analysis by SIMD Mode for Clusterpair at Freq 3.0 GHz </em></p> 
</div>

#### Key takeaways 

- The performance of atom updates does not scale proportionally with FLOPS with increasing SIMD, particularly for Verletlist, as the BuildNeighbour() function does not leverage SIMD optimizations effectively.
    
- AVX512 DP offers the best performance for both Verletlist and Clusterpair, but Clusterpair outperforms Verletlist in atom updates (5.13M vs. 3.18M) while significantly reducing useful read data (7.63 GB vs. 65.58 GB). Clusterpair maintains a lower data volume across all SIMD modes.
    
- While Single Precision (SP) modes consume less data, they fall short in performance compared to Double Precision (DP) modes:


  - Verletlist: 1.43M updates with 20.42 bytes/iteration in SP (compared to 3.18M updates in DP).
  - Clusterpair: 3.51M updates with 23.72 bytes/iteration in SP (compared to 5.13M updates in DP).
    
- Clusterpair shows superior memory efficiency in high-performance AVX512 DP setups, outperforming Verletlist with lower computational overhead. For HN-AVX DP, Clusterpair processes 26.59 bytes per SIMD iteration, significantly less than Verletlist's 128.78 bytes, even with a full neighbor list.


<center><strong>Performance Analysis</strong></center>
    
In this analysis, we primarily focus on the interaction between hardware and software, specifically on data traffic and performance. Therefore, it is essential to have a solid understanding of both the architecture and the implementation. 

#### System Architecture Overview  

The system is equipped with two **Intel Xeon Platinum 8470 processors**, based on the **Intel Sapphire Rapids architecture**. It features the following specifications:

#### Cache Topology

The system includes three levels of cache:

- **L1 Cache**: 
  - Size: 48 kB per core
  - Distribution: Spread across all cores
- **L2 Cache**: 
  - Size: 2 MB per core group
  - Distribution: Evenly distributed across cores
- **L3 Cache**: 
  - Size: 105 MB
  - Distribution: Shared across all cores


```bash
srun --cpu-freq=3000000-3000000:performance 
likwid-perfctr -C 0 -g MEM/L2/L3 -m ./MDBench-VL-ICC-X86-AVX512-DP   
srun --cpu-freq=3000000-3000000:performance 
likwid-perfctr -C 0 -g FLOPS_DP -m ./MDBench-CP-ICC-X86-AVX512-DP      
```
<div style="font-size: 0.90em;">  
    
| Verletlist  ("Force region")                  | HN-SP    | HN-DP    | FN-SP    | FN-DP    |
| ------------------------------ | -------- | -------- | -------- | -------- |
| L2 [MBytes/s]                  | 3164.28  | 4900.79  | 4960.18  | 5257.45  |
| L2 [GB]                        | 12.96    | 19.48    | 18.09    | 23.10    |
| L3 [MBytes/s]                  | 5908.66  | 7257.98  | 7031.70  | 6935.52  |
| L3 [GB]                        | 24.22    | 28.68    | 25.64    | 30.12    |
| MEM [MBytes/s]                 | 204.99   | 172.23   | 119.33   | 113.53   |
| MEM [GB]                       | 0.86     | 0.69     | 0.44     | 0.48     |
| PERF [MFLOP/S]                 | 11958.09 | 11228.84 | 17756.52 | 15485.67 |
| \*Useful read data volume [GB] | 22.01    | 35.34    | 40.91    |<span style="color:red;"> 65.58    |    
    
<div style="text-align: center;"> <p style="text-align: center;"><em>Table 16: Performance Analysis for MDBench-VL-ICC-X86-AVX512-DP at Freq. 3.0 GHz </em></p>   </div>    </div>
    
<div style="font-size: 0.90em;">    
 
| Clusterpair ("Force region")                   | HN-SP    | HN-DP    | FN-SP    | FN-DP    |
| ------------------------------ | -------- | -------- | -------- | -------- |
| L2 [MBytes/s]                  | 2176.53  | 6204.06  | 1745.39  | 4176.25  |
| L2 [GB]                        | 8.85     | 16.60    | 8.38    | 14.93    |
| L3 [MBytes/s]                  | 2349.24  | 4163.52  | 1706.57  | 2583.35  |
| L3 [GB]                        | 9.56     | 11.13    | 7.15    | 9.48     |
| MEM [MBytes/s]                 | 100.05   | 101.74   | 94.21    | 91.65    |
| MEM [GB]                       | 0.11     | 0.27     | 0.15     | 0.33     |
| PERF [MFLOP/S]                 | 60029.86 | 40616.95 | <span style="color:green;"> 73,456.79 | 48226.10 |
| \*Useful read data volume [GB] | 2.37     | 4.85     | 3.7     |<span style="color:red;"> 7.63         |
    
<div style="text-align: center;">
  <p style="text-align: center;"><em>Table 17: Performance Analysis for MDBench-CP-ICC-X86-AVX512-DP at at Freq. 3.0 GHz </em></p>
</div> </div>    

#### Key takeaways
- Both optimization schemes minimize main memory access, effectively reusing data stored in L2 and L3 caches.
   
- The performance in clusterpair is way higher than verletlist in all configurations (FN vs HN, SP vs DP) because of better use of memory locality (stored in an Array-of-Struct-of-Arrays (AoSoA) layout)
    
- The Clusterpair achieves 73,456.79 MFlops/s in FN-SP, with single precision outperforming double precision, whereas in Verlet list configurations, both precisions exhibit similar performance.     
    
![](https://pad.nhr.fau.de/uploads/2d011574-9b48-4a04-9656-dabea7e5a392.png)
<div style="font-size: 0.90em;text-align: center;">
  <p style="text-align: center;"><em>Graph 8: Performance comparision b/w Verletlist and Clusterpair (HN vs FN) on Intel Sapphire Rapids   
</em></p>
</div>  
    

   
### Multicore    
    
<center><strong>Scalability Studies</strong></center><br>
 
Perform strong scaling:
- within a memory domain
- across sockets
 
Ensure pinning with likwid-pin and check that the pinning worked correctly. You can do this, for example, by enabling LIKWID instrumentation and checking with `likwid-perfctr` or, on systems without LIKWID support, by outputting the thread core placement from the source code.

```bash
srun -n 1 -c 208 --cpu-freq=3000000-3000000:performance 
likwid-perfctr -m -C N:0-52 -g FLOPS_DP ./MDBench-VL-ICC-X86-DP  

srun -n 1 -c 208 --cpu-freq=3000000-3000000:performance
likwid-perfctr -c S0:0@S1:0 -g MEM 
likwid-pin -c N:0-52  ./MDBench-VL-ICC-X86-AVX512-DP        
 
srun -n 1 -c 208 --cpu-freq=3000000-3000000:performance
likwid-perfctr -c S0:0@S1:0 -g ENERGY 
likwid-pin -c N:0  ./MDBench-VL-ICC-X86-AVX512-DP -half 1      
```    
    
#### Performance Scalability - Verletlist
When a CPU has multiple cores, it can handle multiple threads, allowing it to execute various tasks more efficiently. As the number of cores increases, the performance (measured in GFLOPs/s) generally improves. However, the rate of improvement appears to diminish after a certain number of cores, indicating diminishing returns due to parallelization overhead or resource contention.    
    
![](https://pad.nhr.fau.de/uploads/4a4de5e8-bf89-42af-805b-102bade1e06f.png)


<div style="font-size: 0.90em;text-align: center;">
  <p style="text-align: center;"><em>Graph 11: Performance vs no. of cores (Verletlist MDBench-VL-ICC-X86-AVX512-DP) on Intel Sapphire Rapids
</em></p>
</div>


    
#### Key takeaways
- As the core count rises, performance enhances across all frequencies, demonstrating scalability. However, the improvement is not linear, particularly when the number of cores exceeds 64, suggesting a point of saturation or diminishing returns.
- Higher frequencies (3.0 GHz and 3.8 GHz) result in improved performance compared to 1.6 GHz at all core counts. The performance gap between 3.0 GHz and 3.8 GHz decreases after approximately 56 cores, likely due to memory bandwidth limitations or other bottlenecks.
- For the 3.8 GHz (turbo) configuration, performance reaches its peak at around 88 cores and begins to decline slightly after 96 cores, likely due to overhead, contention issues, or workload-related factors.    
    
#### Performance by SIMD mode and neighbourlist  - Verletlist

![](https://pad.nhr.fau.de/uploads/d296dfc0-b42b-415e-8a16-e4d762cbdf19.png)

    
<div style="font-size: 0.90em;text-align: center;">
  <p style="text-align: center;"><em>Graph 12: Performance vs no. of cores by SIMD mode (Verletlist MDBench-VL-ICC-X86-AVX512-DP) on Intel Sapphire Rapids
</em></p>
</div>    


#### Key takeaways
- Performance in full neighbourlist improves significantly with advanced SIMD modes: Scalar offers the lowest performance, SSE provides a slight boost, while AVX and AVX512 deliver much higher performance, with AVX512 achieving the best scalability, at ~850 GFLOPs/s on 104 cores.
- Scalar mode scales poorly and is inefficient at high core counts, whereas AVX and AVX512 scale well, with AVX512 maintaining near-linear scaling up to ~96 cores before leveling off.
- The Half Neighbor List (HN) scales slower than the Full Neighbor List (FN) with more cores, as FN benefits more from parallelism. FN offers better performance but at a higher computational cost, while HN is suited for simpler or resource-limited workloads. Both HN and FN perform best with AVX-512, maximizing core and memory bandwidth utilization.
- Single precision and double precision configurations in verletlist follow a similar trend with FN curves above HN curves.    
- At higher core counts, thermal throttling or power constraints can hinder full-speed operation across all cores. Additionally, uneven workload distribution lowers efficiency, and as thread counts increase, the interconnect bandwidth becomes saturated, leading to higher memory latency and reduced scalability.
    

### Energy analysis -Verletlist

The Z-plot shows the energy-to-solution versus performance (inversely related to runtime), with a third variable (e.g., processor frequency or core count) represented along the line, highlighting efficiency across different configurations.

Energy-to-Solution is the total energy consumed during a run, measured by the LIKWID-perfctr ENERGY group, which sums PKG and DRAM Energy/Power from socket-specific counters, read using a single hardware thread per socket.

The Energy-Delay Product (EDP) is used to find a balance between low energy consumption and good performance.


```bash      
srun -n 1 -c 208 --cpu-freq=3800000-3800000:performance
likwid-perfctr -c S0:0@S1:0 -g ENERGY 
likwid-pin -c N:0-103  ./MDBench-VL-ICC-X86-AVX512-DP      
    
srun -n 1 -c 208 --cpu-freq=2400000-2400000:performance
likwid-perfctr -m -C N:1-103 -g FLOPS_DP ./MDBench-VL-ICC-X86-AVX512-DP    
```       
![](https://pad.nhr.fau.de/uploads/b857f87f-b514-495f-9d15-dce2bcf65df3.png)


    
<p><em>Graph 4: Z-plot for varying frequencies in verletlist with AVX 512 SIMD vectorization.</em></p>

    
#### Key Observations

* The energy-delay product (EDP) decreases as frequency increases at a fixed core count, reaching its lowest value at 16 cores and 3.8 GHz (blue line).  
    
* The power cap is set to 508.45 W at 3.0 GHz, with the PKG domain power cap configured to 700 W.

    
* SIMD modes excel in energy efficiency, with AVX-512 providing the best performance (~850 GFlops/s) and the lowest energy-to-solution.


![](https://pad.nhr.fau.de/uploads/8be2ad6c-0352-4f11-bc5f-e238b5fff23e.png)



<p><em>Graph 5: Z-plot for Verletlist by SIMD modes </em></p>

     
* The half-neighbor list reduces energy-to-solution but does not match the performance of the full-neighbor configuration, leading to an unbalanced solution.
    
      
![](https://pad.nhr.fau.de/uploads/dc4cf4d0-c8c9-41de-a417-a322d4be2c18.png)

   
<p><em>Graph 6: Z-plot comparing verletlist for Half Neighbor vs Full Neighborlist </em></p>    
    

### Scalability Studies - Verletlist vs Clusterpair
   
![](https://pad.nhr.fau.de/uploads/4a4de5e8-bf89-42af-805b-102bade1e06f.png)

<p><em>Graph 7: Performance scaling with core count for verletlist  </em></p>
   
Clusterpair struggles with OpenMP scaling due to uneven workload distribution, prompting the use of MPI for studying performance scaling. 
    
#### Performance Scalability using MPI
    
* Verletlist scales linearly with core count due to balanced workload distribution, while clusterpair maintains linear scaling upto 40 cores.
    
 
![](https://pad.nhr.fau.de/uploads/56f55cec-a14e-4141-bb1a-829e9424936c.png)

    
<p><em>Graph 8: Performance scaling with core count for verletlist vs. clusterpair </em></p>


#### Energy Analysis using MPI 
    
```bash    
srun --cpu-freq=3800000-3800000:performance  -n 104 
--mpi=pmi2 ./MDBench-CP-ICC-X86-AVX512-DP –half 1 -method 0 -bal 0
```   
    
* The Z-plot shows that clusterpair offers a lower energy-to-solution ratio with high performance, making it preferable, even though verletlist achieves better core scaling and higher performance gains at the expense of higher energy-to-solution values.
    
![](https://pad.nhr.fau.de/uploads/6c3d12ea-48d3-4745-8b95-3a75b9105e76.png)

<p><em>Graph 9: Z-plot comparing clusterpair and verletlist for AVX 512 SIMD vectorization  </em></p>

    
     
#### Workload distribution: 

The arithmetic instruction and data volume highlight the trade-off: in Clusterpair, memory access is more efficient, requiring fewer instructions due to its SIMD-optimized format.
    
Clusterpair shows workload imbalance as core scaling increases, with a wider gap in arithmetic instructions, while Verletlist maintains a <1% difference up to 104 cores, explaining its better scalability.
 
![](https://pad.nhr.fau.de/uploads/6d27b6e1-ec18-46b7-872e-4529d3945378.png)

<p><em>Table 9: Workload analysis comparing verletlist and clusterpair at Frequency 3.8 GHz</em></p>       

### Summary   
* Clusterpair's efficiency in memory and SIMD storage reduces instructions, but its 3x higher computations compared to verletlist limits time savings.

* Verletlist is constrained by data gathering and SIMD setup, while clusterpair boosts efficiency by eliminating gathers, though this results in a higher arithmetic instruction count.

* The critical difference lies in instruction throughput. The verletlist algorithm is bound by instruction throughput, with performance constrained by data gathering and SIMD register setup.

* Clusterpair outperforms verletlist in performance across all configurations while using less memory, thanks to improved locality with its Array-of-Struct-of-Arrays layout.

* Clusterpair fully utilizes SIMD, achieving 100% vectorization, a higher arithmetic percentage, and lower runtime than verletlist across all configurations.

* Performance improves with core count and frequency, with verletlist scaling linearly, while clusterpair faces OpenMP scaling issues due to uneven workload distribution, necessitating an MPI study where it scales linearly up to 40 cores.

* The Z-plot shows clusterpair's lower energy-to-solution, making it preferable despite verletlist's better core scaling, while AVX-512 excels in efficiency, providing the highest performance with the lowest energy-to-solution.    
 
    

    

   




