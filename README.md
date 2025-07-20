# BCD Arithmetic Calculator

## Overview

This project is a command-line calculator implemented in C that performs arithmetic operations on large integers using Binary-Coded Decimal (BCD) representation. Instead of relying on the system's native integer types, which have size limitations, this program stores numbers as a dynamic sequence of 4-bit BCD digits. This approach allows for arbitrary-precision arithmetic, limited only by available system memory.

The calculator supports addition, subtraction, multiplication, and comparison of signed integers. It provides a simple, interactive menu for users to input numbers and select operations.

## Features

- **Arbitrary-Precision Arithmetic:** Handles integers larger than what standard `int` or `long long` types can hold.
- **Signed Number Support:** Correctly processes and displays positive and negative numbers using a sign flag.
- **BCD Representation:** All core arithmetic is performed directly on a custom `Bitset` data structure representing BCD values.
- **Core Operations Implemented:**
  - Addition (`a + b`)
  - Subtraction (`a - b`) using 10's complement BCD arithmetic.
  - Multiplication (`a * b`) using the traditional long multiplication algorithm adapted for BCD.
  - Comparison (`a < > == b`)
- **Interactive Command-Line Interface (CLI):** A simple menu-driven interface for easy operation.
- **Detailed Output:** Results are displayed in both their raw BCD format (grouped in 4-bit nibbles) and their converted decimal equivalent.
- **Memory Management:** Dynamic memory allocation for bitsets with proper cleanup to prevent leaks.

## Data Structure

The core of the project is the `Bitset` struct, defined as:

```c
typedef struct
{
    unsigned long *data; // Dynamic array to store bits
    size_t size;         // Total number of bits in the bitset
    bool is_negative;    // Sign flag (true for negative)
} Bitset;
```

Each decimal digit of a number is stored as a 4-bit BCD nibble. For example, the number `-123` would be represented by a `Bitset` with:
- `is_negative = true`
- `size = 12` (3 digits * 4 bits/digit)
- `data` containing the binary sequence `0011 0010 0001` (representing 3, 2, 1).

## Core Algorithms

### BCD Addition

Addition is performed digit by digit, from least significant to most significant. Each 4-bit addition includes a carry-in from the previous digit. If a 4-bit sum exceeds 9 or generates a binary carry, a **BCD correction** is applied by adding 6 (`0110`) to the result, and a carry-out is passed to the next digit.

### BCD Subtraction

Subtraction is implemented using **10's complement arithmetic**, a standard method for BCD. The operation `A - B` is transformed into `A + (10's complement of B)`.
1.  The 9's complement of the subtrahend (B) is calculated by subtracting each of its digits from 9.
2.  1 is added to the 9's complement to get the 10's complement.
3.  The result is added to the minuend (A) using the BCD addition logic.
4.  The presence or absence of an "end-around carry" (a carry out of the most significant digit) determines the sign and final value of the result.

### BCD Multiplication

Multiplication is based on the grade-school **long multiplication algorithm**, adapted for BCD:
1.  The multiplicand is multiplied by each BCD digit of the multiplier, one at a time.
2.  Each of these intermediate multiplications generates a partial product.
3.  Each partial product is shifted left by the appropriate number of digit positions (i.e., by multiples of 4 bits).
4.  The shifted partial products are summed together using BCD addition to produce the final result.

## How to Compile and Run

This project is written in standard C and requires a C compiler like GCC or Clang.

### Prerequisites

- A C compiler (e.g., GCC)

### Running the Program

Execute the compiled binary from your terminal:

```bash
./bcd_calculator
```

You will be presented with a menu. Enter the corresponding number to select an option and follow the prompts.

```
Current Numbers:
Number 1: Not set
Number 2: Not set

Menu:
1. Enter Number 1
2. Enter Number 2
3. Add
4. Subtract
5. Multiply
6. Compare
7. Exit
Enter choice:
```

