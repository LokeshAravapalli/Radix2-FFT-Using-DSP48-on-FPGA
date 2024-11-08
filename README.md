# Radix2-FFT-Using-DSP48-on-FPGA


## Table of Contents

1. [Overview](#overview)  
2. [Introduction](#introduction)  
3. [Methodology](#methodology)  
   3.1 [Approach – Multiplexing of the DSPs](#approach---multiplexing-of-the-dsps)  
   3.2 [Implementation Details](#implementation-details)  
4. [Results](#results)  
5. [Visual Aids](#visual-aids)  


---

## Overview

This project implements a 128-point Fast Fourier Transform (FFT) using the Radix-2 Decimation in Time (DIT) algorithm (Cooley-Tukey algorithm) with DSP48 IP blocks on an FPGA. The implementation is optimized to reduce resource utilization on the Basys3 FPGA board by using a multiplexing approach to share DSP resources across stages.

---
## Introduction
A Fast Fourier Transform (FFT) is an algorithm that computes the Discrete Fourier Transform (DFT) of a sequence, decomposing it into components of different frequencies. While the DFT has a time complexity of \( O(n^2) \), an FFT reduces this to 
\( O(N log N) \) by factorizing the DFT matrix into a product of sparse factors. The Cooley-Tukey algorithm, specifically the radix-2 decimation-in-time (DIT) FFT, achieves this by recursively dividing the DFT into smaller parts, ultimately down to 2-point FFTs (butterfly stages) that require minimal computational effort.

In this project, we utilized DSP48 IP blocks to handle the multiplication operations within the FFT, making the process more efficient on FPGA hardware.

---

## Methodology

We can implement the 128-point (N-point) FFT by dividing it into two smaller FFTs of size \( N/2 \), continuing this process recursively until reaching 2-point FFTs. However, this method has significant resource limitations on the Basys3 FPGA due to high DSP usage, making it unsuitable for implementation.


### Approach – Multiplexing of the DSPs

To address the resource limitations, we arrived at a solution to maximize DSP usage in each stage and then reuse them for subsequent stages (multiplexing of DSPs). We identified that an 8-point FFT requires 48 DSPs, making it the largest stage feasible to implement at once on Basys3. Thus, a 16-point FFT can be implemented as two 8-point FFT stages, with their outputs passed through butterfly operations to obtain the final results. Similarly, 32-point, 64-point, and 128-point FFTs can be implemented in this staged manner.

For a 16-point FFT, we divide it into two 8-point FFT stages, but we initiate only one 8-point FFT. Once the first FFT completes, a done signal triggers the change of inputs needed for the second FFT. After the second FFT sends its done signal, we perform butterfly operations on the corresponding outputs to obtain the final results. This process is applied to 32-point, 64-point, and 128-point FFTs, with each divided into two stages and finalized through butterfly operations on their outputs to produce correct results.
### Implementation Details

1. **8-Point FFT Stage:** Used as the building block, requiring 48 DSPs.
2. **Stage Control:** A done signal is generated after each FFT stage, triggering the start of the next stage.
3. **Final Outputs:** For each 2-stage process (16-point, 32-point, 64-point, 128-point), butterfly operations on the corresponding outputs provide the correct results.

---

## Results

The final implementation of the FFT achieved significant improvement in resource utilization, although it resulted in a reduction in processing speed a bit.

---

## Visual Aids

Here are some visuals that illustrate our design and results:

- **Block Diagram of FFT Implementation**
 ![WhatsApp Image 2024-11-02 at 22 02 57](https://github.com/user-attachments/assets/0878c9d0-51cc-4512-badf-8150d2512c56)
- **Resource Utilization Report**: (Attach a screenshot of resource usage here)
  ![WhatsApp Image 2024-11-08 at 18 36 14](https://github.com/user-attachments/assets/3239d428-f4bc-4b05-8d87-6b158317a092)
- **Timing Analysis Diagram**: (Attach timing analysis or any relevant diagrams here)

To add images, you can use:
```markdown
![Alt text](path_to_image "Optional Title")
