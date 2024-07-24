# Tanker Assignment Optimization

This Python script uses the PuLP library to solve a linear programming problem aimed at optimizing the assignment of tankers to berths and segments within a port, ensuring efficient utilization of space and resources.

## Dependencies

- PuLP
- pandas
- numpy

## Problem Definition

The goal is to minimize the total unassigned tanker capacity while ensuring that each tanker is assigned to a set of contiguous segments within a single berth. The problem also considers the capacity of each tanker and segment, aiming to maximize the capacity utilization.

## Data

- `tankers`: List of tanker names.
- `tanker_capacities`: Dictionary mapping each tanker to its capacity.
- `berths`: List of berth names.
- `segments`: Dictionary mapping each berth to its segments.
- `segment_capacities`: Capacity of each segment (assumed uniform for simplicity).

## Decision Variables

- `assign`: Binary variable indicating whether a tanker is assigned to a specific segment within a berth.
- `is_assigned`: Binary variable indicating whether a tanker is assigned to any segment.
- `tanker_berth_assigned`: Binary variable indicating whether a tanker is assigned to a specific berth.

## Objective Function

Minimize the negative of total capacity assigned to maximize it, and minimize the number of unassigned tankers.

## Constraints

1. Each tanker is assigned to exactly one set of contiguous segments within a single berth.
2. No more than one tanker per segment.
3. Tanker capacity does not exceed segment capacity.
4. A tanker is assigned to only one berth.
5. Priority constraints to ensure segments are assigned in order within each berth.
6. Total capacity of assigned segments does not exceed the tanker's capacity by more than a predefined threshold.

## Solution

The script solves the problem using PuLP's solver, prints the status of the solution, and displays the assignments of tankers to berths and segments.

## Results

- The status of the solution (Optimal, Infeasible, etc.).
- The assignments of tankers to berths and segments.
- The total optimized value of the objective function.

This approach provides a structured and efficient way to allocate tanker space, optimizing port operations and utilization.
