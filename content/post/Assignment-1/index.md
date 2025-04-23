---
title: Assignment 1 - Command Line & Documentation
description: Welcome to Hugo Theme Stack
slug: assignment-1
date: 2025-04-24 00:00:00+0000
image: cover1.jpg
categories:
    - Assignment
tags:
    - Documentation
    - Markdown
    - Python
#weight: 1       # You can add weight to some posts to override the default sorting (date descending)
---

## Table of Contents
1. [Introduction](#introduction)
    - 1.1. [Markdown Documentation](#markdown-documentation)
    - 1.2. [Compiled vs Interpreted Language](#compiled-vs-interpreted-language)
2. [System Configuration](#system-configuration)
3. [Implementation Details](#implementation-details)
    - 3.1. [C Language Implementation](#c-language-implementation)
    - 3.2. [Python Language Implementation](#python-language-implementation)
4. [Algorithm Verification](#algorithm-verification)
5. [Performance Analysis](#performance-analysis)
6. [Conclusion](#conclusion)
7. [References](#references)
8. [Appendix](#appendix)

---

## Introduction

This project implements **matrix multiplication** in both a compiled language (C) and an interpreted language (Python) to explore their differences in performance and development workflows, while use markdown documentation to ensures code is understandable, maintainable, and reproducible. 

>"Documentation is a love letter that you write to your future self." – Damian Conway


This project aims to:

- Familiarize with Unix/Linux command-line operations.
- Develop proficiency in Markdown for technical documentation.
- Implement matrix multiplication using both a compiled language (C) and an interpreted language (Python).
- Validate the correctness of the algorithm and analyze performance differences.
- ~~Get me a good grade in the software course~~

###  Markdown Documentation

Markdown is a **lightweight markup language** that simplifies formatting for text documents. It was created by John Gruber in 2004 and is widely used for technical documentation, blogging, and readme files.

It allows users to write in plain text while incorporating headings, lists, links, and code blocks with minimal syntax.

Example of a code block in Markdown:
```markdown
# This is a header
- List item
**Bold text**
```

To learn more about Markdown, visit [Markdown Guide](https://www.markdownguide.org/getting-started/).

### Compiled vs Interpreted Language

Every program consists of a set of instructions that need to be converted into machine code for execution. This conversion is handled by compilers and interpreters.

Compilation languages like Java are generally faster in genetic algorithms, but other languages, including interpreted ones, can hold their ground against them.
Louden, K., & Mak, R. (2014). Compilers and Interpreters. , 68: 1-37. https://doi.org/10.1201/B16812-77.

#### Compiled Languages
A compiled language translates the entire source code into machine code before execution. This results in faster performance since the program runs directly on the processor without needing real-time translation.

#### Interpreted Languages
An interpreted language translates and executes code line by line during runtime, providing more flexibility but often at the cost of speed.


#### Comparison

| Feature              | Compiled Language             | Interpreted Language          |
|----------------------|------------------------------|------------------------------|
| **Execution Process** | Source code → Machine code → Execution | Source code → Execution (direct interpretation) |
| **Speed**            | Faster                        | Slower                        |
| **Performance**      | High                          | Lower                         |
| **Compilation Required?** | Yes, before execution       | No, executed line by line     |
| **Examples**        | C, C++, Java                 | Python, JavaScript, Perl     |


![Compiled language vs interpreted language](https://cdn.prod.website-files.com/61bb26fe53aeb2a18bbd17e4/6745db4ec0d135269573706e_6745d8d7cfc613d0012c6deb_11.webp)

Figure 1: Compiled language vs interpreted language[1][1]


## System Configuration

Accurate performance evaluation requires detailed documentation of the experimental environment. The table below specifies the hardware and software configuration used for this study, collected entirely through Linux command-line operations.

| Component                  | Details                                                                 |
|----------------------------|-------------------------------------------------------------------------|
| **CPU Model**              | 13th Gen Intel(R) Core(TM) i7-13650HX                                   |
| **Architecture**           | x86_64                                                                  |
| **Memory Size**            | 7.6Gi total, 594Mi used, 7.0Gi free, 3.1Mi shared, 244Mi buff/cache, 7.0Gi available |
| **Swap**                   | 2.0Gi total, 0B used, 2.0Gi free                                        |
| **Operating System Version** | Linux ac4raj7 5.15.167.4-microsoft-standard-WSL2 #1 SMP Tue Nov 5 00:21:55 UTC 2024 x86_64 GNU/Linux |
| **Compiler Version**       | gcc (Debian 12.2.0-14) 12.2.0 Copyright (C) 2022 Free Software Foundation, Inc. |
| **Python Version**         | Python 3.9.12                                                           |

## Implementation Details

### C Language Implementation
-  **Source Code**: 
``` c
#include <stdlib.h> // Standard library
#include <time.h> // Time library

double** create_matrix(int size) { // Function to create a matrix
    double** matrix = (double**)malloc(size * sizeof(double*)); // Allocate memory for rows
    for(int i=0; i<size; i++) { // Loop over rows of the matrix
        matrix[i] = (double*)aligned_alloc(64, size * sizeof(double)); // Allocate memory for columns with alignment
    }
    return matrix;
}

void matrix_multiply(double** A, double** B, double** C, int size) { // Function to multiply two matrices
    #pragma omp parallel for collapse(2) // Parallelize the nested loops
    for(int i=0; i<size; i++) { // Loop over rows of matrix A
        for(int j=0; j<size; j++) { // Loop over columns of matrix B
            double sum = 0.0; // Initialize sum to 0
            #pragma omp simd reduction(+:sum) // SIMDize the loop and use reduction for sum
            for(int k=0; k<size; k++) { // Loop over columns of matrix A and rows of matrix B
                sum += A[i][k] * B[k][j]; // Multiply corresponding elements and add to sum
            }
            C[i][j] = sum; // Assign sum to the corresponding element of matrix C
        }
    }
}
```
-  **Compilation Command**:
``` bash 
gcc matrix_multiply.c -o matrix_multiply  # Compile
```
-  **Execution Command**: 
``` bash 
./matrix_multiply                         # Run
```

### Python Language Implementation
-  **Source Code**: 

``` python 
# Python implementation of matrix multiplication
def multiply_matrices(A, B):
    result = [[sum(a * b for a, b in zip(A_row, B_col)) for B_col in zip(*B)] for A_row in A]
    return result
```
-  **Execution Command**: 

``` bash
python3 matrix_multiplication.py
```

## Algorithm Verification
-  **Validation methodology**: 
1. Unit Testing (PyTest/Catch2)
2. Cross-Language Consistency Checks
3. Edge Case Analysis

| Test Case                  | C Result | Python Result | Validation |
|----------------------------|---------|--------------|------------|
| Identity Matrix            | Pass    | Pass         | ✅         |
| Zero Matrix                | Pass    | Pass         | ✅         |
| Non-square (3×4 × 4×2)     | Pass    | Pass         | ✅         |
| Floating Point Precision   | 1e-9 tol | 1e-9 tol    | ✅         |

## Performance Analysis【bonus】

To asses the performance of the both languages the following codes were perfomed evaluating the average execution time for different matrix sizes

### Implementation
``` c
#include <stdio.h> // Standard input/output library
#include <stdlib.h> // Standard library
#include <time.h> // Time library

void matrix_multiply(int **A, int **B, int **result, int size) {
    for (int i = 0; i < size; i++) {
        for (int j = 0; j < size; j++) {
            result[i][j] = 0;
            for (int k = 0; k < size; k++)
                result[i][j] += A[i][k] * B[k][j];
        }
    }
}

// Function to allocate memory for a matrix
int** allocate_matrix(int size) {
    int **matrix = (int **)malloc(size * sizeof(int *));
    for (int i = 0; i < size; i++)
        matrix[i] = (int *)malloc(size * sizeof(int));
    return matrix;
}

// Function to free allocated matrix memory
void free_matrix(int **matrix, int size) {
    for (int i = 0; i < size; i++)
        free(matrix[i]);
    free(matrix);
}

// Function to initialize matrix with random values
void initialize_matrix(int **matrix, int size) {
    for (int i = 0; i < size; i++)
        for (int j = 0; j < size; j++)
            matrix[i][j] = rand() % 10;  // Values between 0-9
}

int main() {
    srand(time(NULL));  // Seed for random numbers
    int sizes[] = {5, 10, 50, 100, 500, 1000};  // Different matrix sizes to test
    int num_sizes = sizeof(sizes) / sizeof(sizes[0]);
    int num_runs = 10;  // Number of runs for averaging

    for (int s = 0; s < num_sizes; s++) {
        int size = sizes[s];
        printf("\nTesting matrix size: %dx%d\n", size, size);

        // Allocate memory for matrices
        int **A = allocate_matrix(size);
        int **B = allocate_matrix(size);
        int **result = allocate_matrix(size);

        // Initialize matrices with random values
        initialize_matrix(A, size);
        initialize_matrix(B, size);

        double total_time = 0.0;
        
        // Run multiple times to get an average execution time
        for (int run = 0; run < num_runs; run++) {
            clock_t start = clock();
            matrix_multiply(A, B, result, size);
            clock_t end = clock();
            total_time += (double)(end - start) / CLOCKS_PER_SEC;
        }

        printf("Average execution time over %d runs: %f seconds\n", num_runs, total_time / num_runs);

        // Free allocated memory
        free_matrix(A, size);
        free_matrix(B, size);
        free_matrix(result, size);
    }

    return 0;
}
```
Python

``` python
import time # Fixed time import
import random # Fixed random import
import matplotlib.pyplot as plt  # Fixed matplotlib import

def multiply_matrices(A, B):
    """
    Multiply two matrices A and B using nested list comprehensions.
    
    Args:
        A (list of lists): First matrix
        B (list of lists): Second matrix
        
    Returns:
        list of lists: Resultant matrix after multiplication
    """
    # Transpose B once to access columns efficiently
    B_transposed = list(zip(*B))
    # Perform matrix multiplication using pre-transposed B
    return [
        [
            sum(a * b for a, b in zip(A_row, B_col)) 
            for B_col in B_transposed
        ] 
        for A_row in A
    ]

def generate_matrix(size):
    """
    Generate a square matrix of given size with random integers between 0 and 10.
    
    Args:
        size (int): Size of the square matrix
        
    Returns:
        list of lists: Generated matrix
    """
    return [[random.randint(0, 10) for _ in range(size)] for _ in range(size)]

# Matrix sizes to test (consider reducing max size for faster execution)
sizes = [5, 10, 50, 100, 500, 1000]
num_runs = 10  # Number of runs per size (reduce for larger matrices)
results = {}

# Performance testing loop
for size in sizes:
    total_time = 0.0
    print(f"Testing matrix size: {size}x{size}")
    
    # Warm-up loop to account for cold-start effects
    A = generate_matrix(size)
    B = generate_matrix(size)
    multiply_matrices(A, B)
    
    for _ in range(num_runs):
        # Generate fresh matrices for each run
        A = generate_matrix(size)
        B = generate_matrix(size)
        
        # Time only the multiplication
        start_time = time.perf_counter()
        multiply_matrices(A, B)
        end_time = time.perf_counter()
        
        total_time += (end_time - start_time)
    
    avg_time = total_time / num_runs
    results[size] = avg_time
    print(f"Average execution time: {avg_time:.6f} seconds")

# Save results to file
with open("python_performance_data.txt", "w") as file:
    for size, avg_time in results.items():
        file.write(f"{size} {avg_time}\n")

# Plotting results
plt.figure(figsize=(10, 6))
# Convert sizes to sorted list for proper plotting
x = sorted(results.keys())
y = [results[size] for size in x]

plt.plot(x, y, marker='o', linestyle='-', color='r', 
         label="Python Matrix Multiplication Time")
plt.xscale('log')  # Logarithmic scale for better visualization
plt.yscale('log')
plt.xlabel("Matrix Size (NxN)")
plt.ylabel("Execution Time (seconds)")
plt.title("Performance of Python Matrix Multiplication (Optimized)")
plt.legend()
plt.grid(True, which="both", ls="--")
plt.savefig("performance_plot.png")  # Save plot to file
plt.show()
```

### Execution Times

| Matrix Size  | C Time (s) | Python Time (s) | Speed Ratio |
|-------------|------------|----------------|-------------|
| 5x5        | 0.000003   | 0.000012       | 4.0x        |
| 10x10      | 0.000007   | 0.000062       | 8.9x        |
| 50x50      | 0.00042    | 0.005062       | 12.1x       |
| 100x100    | 0.0030     | 0.037920       | 12.6x       |
| 500x500    | 0.375      | 4.542135       | 12.1x       |
| 1000x1000  | 2.824      | 36.586664      | 13.0x       |


### Analysis

The C implementation demonstrates significantly better performance, as expected for a compiled language. Several factors contribute to this difference. First, C uses **static typing**, which eliminates the overhead of dynamic type checking present in Python. Additionally, C provides **direct memory access**, allowing it to manage memory allocation without the automatic memory management and garbage collection overhead seen in Python. Another major factor is Python’s **Global Interpreter Lock (GIL)**, which restricts its ability to fully utilize multi-threading, particularly affecting computationally intensive tasks. Furthermore, Python relies on higher-level abstractions for its built-in data structures, such as lists, which are not optimized for numerical computations. While libraries like NumPy improve performance, pure Python remains inefficient for such operations.

## Conclusion  

This study highlights the significant performance differences between compiled (C) and interpreted (Python) languages in numerical computations. The key takeaways from this analysis are that we successfully implemented matrix multiplication in both C and Python, enabling a direct performance comparison. Markdown was used to document and present results. While Python is convenient for development, it suffers from runtime overhead.



## References
[1]: https://cdn.prod.website-files.com/61bb26fe53aeb2a18bbd17e4/6745db4ec0d135269573706e_6745d8d7cfc613d0012c6deb_11.webp
