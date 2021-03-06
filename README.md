CUDA Stream Compaction
======================

**University of Pennsylvania, CIS 565: GPU Programming and Architecture, Project 2**

* Name: Jiahao Liu
* Tested on: Windows 10, i7-3920XM CPU @ 2.90GHz 3.10 GHz 16GB, GTX 980m SLI 8192MB (personal computer)

Project Description and features implemented
======================

### Project Description

This project is tend to compare running performance difference in computing prefix sum between CPU scan, naive GPU scan, efficient GPU scan and thrust scan.

### Features implemented

* CPU scan

* Naive GPU scan with shared memory

* Efficient GPU scan

* Thrust scan

Performance Analysis
======================

After trying I found for all the block size the program has the same performance, so I just chose the largest power of two as my block size.

![](img/1.png)

![](img/2.png)

When includes the time of copy data from host to device, thrust scan and naive scan runs no faster then the CPU scan.

![](img/3.png)

Even with data copy, the efficient scan runs faster then the CPU scan.

![](img/4.png)

Only includes the time for computing, we can see the graph above.  Thrust scan may have some built-in operations that really cost time. Naive scan roughly has more memory access then efficient scan( n^2/2 vs 3*n) but fewer calculating and comparing operations. This means the battleneck is on the memory I/O. We cannot evaluate thrust since we don't even know what happens inside.

There is a very interesting that power-of-two thrust scan really spent some time. Comparing what I know for access array on ram with power-of-two length, this maybe the consequence for access confliction due to the time cost of finding next memory unit on hardware.

### Running Result

```
****************
** SCAN TESTS **
****************
    [  28  13   1  37  12  43  45  30  45  30  16  35  30 ...  33   0 ]
==== cpu scan, power-of-two ====
   elapsed time: 0.001324ms    (std::chrono Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9519 9552 ]
==== cpu scan, non-power-of-two ====
   elapsed time: 0.001655ms    (std::chrono Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9421 9449 ]
    passed
==== naive scan, power-of-two ====
   elapsed time: 0.006976ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9519 9552 ]
    passed
==== naive scan, non-power-of-two ====
   elapsed time: 0.00688ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ...   0   0 ]
    passed
==== work-efficient scan, power-of-two ====
   elapsed time: 0ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9519 9552 ]
    passed
==== work-efficient scan, non-power-of-two ====
   elapsed time: 0ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9421 9449 ]
    passed
==== thrust scan, power-of-two ====
   elapsed time: 0.025152ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9519 9552 ]
    passed
==== thrust scan, non-power-of-two ====
   elapsed time: 0.0216ms    (CUDA Measured)
    [   0  28  41  42  79  91 134 179 209 254 284 300 335 ... 9421 9449 ]
    passed

*****************************
** STREAM COMPACTION TESTS **
*****************************
    [   0   1   3   3   1   2   2   1   2   0   2   3   3 ...   2   0 ]
==== cpu compact without scan, power-of-two ====
   elapsed time: 0.001655ms    (std::chrono Measured)
    [   1   3   3   1   2   2   1   2   2   3   3   1   2 ...   3   2 ]
    passed
==== cpu compact without scan, non-power-of-two ====
   elapsed time: 0.001655ms    (std::chrono Measured)
    [   1   3   3   1   2   2   1   2   2   3   3   1   2 ...   3   1 ]
    passed
==== cpu compact with scan ====
   elapsed time: 0.001655ms    (std::chrono Measured)
    [   1   3   3   1   2   2   1   2   2   3   3   1   2 ...   3   2 ]
    passed
==== work-efficient compact, power-of-two ====
   elapsed time: 0.474944ms    (CUDA Measured)
    [   1   3   3   1   2   2   1   2   2   3   3   1   2 ...   3   2 ]
    passed
==== work-efficient compact, non-power-of-two ====
   elapsed time: 0.416544ms    (CUDA Measured)
    [   1   3   3   1   2   2   1   2   2   3   3   1   2 ...   3   1 ]
    passed

```