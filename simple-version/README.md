
# Guide to Solving the Tanker Assignment Challenge with Linear Programming - Simple version

In this blog, we'll explore how to solve a complex tanker assignment problem using linear programming in Python. Our goal is to optimally assign tankers to segments within berths at a port, ensuring that the total capacity of assigned segments closely matches the tanker's capacity without significant over-assignment. We'll use the PuLP library, a popular linear programming package in Python, to model and solve this problem.

## Step 1: Setting Up the Problem

First, we import the necessary libraries and initialize our problem in PuLP. We define our problem as a minimization problem named "TankerAssignment".

```python
from pulp import *
import pandas as pd
import numpy as np

# Define problem
prob = LpProblem("TankerAssignment", LpMinimize)
```

## Step 2: Defining Data Structures

In this step, we will create a simple case that reflects a real-life situation without being overly complex. This initial setup will help us understand the problem and develop a basic solution. We will enhance and expand the data structures later as needed.

- Tankers: A list of tankers, each with a specific capacity.

- Berths: A list of berths, each with multiple segments.

- Segments: Segments within each berth, each with a default capacity


```python
# Data
# List of tankers
tankers = ["Tanker1", "Tanker2", "Tanker3", "Tanker4", "Tanker5", "Tanker6", "Tanker7", "Tanker8", "Tanker9", "Tanker10"]

# Dictionary of tanker capacities
tanker_capacities = {
    "Tanker1": 200,
    "Tanker2": 100,
    "Tanker3": 100,
    "Tanker4": 100,
    "Tanker5": 100,
    "Tanker6": 100,
    "Tanker7": 100,
    "Tanker8": 100,
   

berths = ["Berth1", "Berth2"]
segments = {"Berth1": ["Segment1", "Segment2"], "Berth2": ["Segment1", "Segment2", "Segment3"]}
segment_capacities = 100  # Default capacity for each segment
```

## Step 3: Creating Decision Variables
Decision variables are used in linear programming to represent the choices we need to make in order to solve our optimization problem
We create binary decision variables for assigning tankers to segments and an additional binary variable to track if a tanker is assigned.

Assignment Variables (assign): These binary variables indicate whether a particular tanker is assigned to a specific segment within a berth.
```python
# Decision variables for assignment
assign = LpVariable.dicts("assign", (tankers, berths, sum(segments.values(), [])), cat='Binary')

Assignment Status Variables (is_assigned): These binary variables track whether a tanker is assigned to any segment within any berth.
```python
# Decision variable for whether a tanker is assigned
is_assigned = LpVariable.dicts("is_assigned", tankers, lowBound=0, upBound=1, cat='Binary')
```
Assigment Status Variables  (tanker_berth_assigned) : binary variables track where a tanker is assigned to any berth
```python
#  decision variable for specific tanker is assigned to a specific berth
tanker_berth_assigned = LpVariable.dicts("tanker_berth_assigned", 
                                         (tankers, berths), 
                                         cat='Binary')
```
Example
- if we assign tanker1 into berth1 with segment1
   ```python
   assign["Tanker1"]["Berth1"]["Segment1"] = 1
   ```
- if we do not assign tanker2 into berth1 with segment1
  ```
   assign[tanker2][berth1][segment1] = 0
  ```
- if we assign tanker1 into berth1 with segment1,segment2
  ```
   assign[tanker1][berth1][segment1] = 1
   assign[tanker1][berth1][segment2] =1
  ```
- if tanker1 is assigned to any segment
```is_assigned[tanker1]=1
```
If tanker1 is not assigned to any segment
```
is_assigned[tanker1] = 0 
```

## Step 4: Formulating the Objective Function

Our objective is twofold: maximize the total capacity assigned to tankers and minimize the number of unassigned tankers. We introduce weights to balance these components.

```python
# Weights for the objective components
weight_capacity_assigned = 1  # Weight for the total capacity assigned
weight_unassigned_tankers = 0.001  # Smaller weight for minimizing unassigned tankers, ensuring capacity has higher priority

# Objective function
prob += (lpSum([-weight_capacity_assigned * assign[tanker][berth][seg] * tanker_capacities[tanker] 
                 for tanker in tankers 
                 for berth in berths 
                 for seg in segments[berth]]) 
         + lpSum([weight_unassigned_tankers * (1 - is_assigned[tanker]) for tanker in tankers]))
```
### Explain about objective components:

- weight_capacity_assigned = 1: This weight is applied to the total capacity assigned to the tankers. It indicates the relative importance of maximizing the total capacity assigned in the objective function. A higher weight means that assigning as much capacity as possible is a priority.
- weight_unassigned_tankers = 0.001: This smaller weight is for minimizing the number of unassigned tankers. The small value (0.001) compared to the weight for capacity assigned (1) ensures that the primary focus is on maximizing capacity, while still considering the minimization of unassigned tankers as a secondary goal
### The objective function is a combination of two parts:
- Maximizing total capacity assigned:
```python
  lpSum([-weight_capacity_assigned * assign[tanker][berth][seg] * tanker_capacities[tanker] 
                 for tanker in tankers 
                 for berth in berths 
                 for seg in segments[berth]])
```
The decision variable assign[tanker][berth][seg] indicates whether a tanker is assigned to a specific segment in a berth. Multiplying by the tanker capacities and summing these products aims to **maximize the total capacity assigned**
The use of negative values is a common technique in optimization to turn a maximization problem into a minimization problem because the **solver is set to minimize the objective function.**
- Minimizing number of unassigned tankers:
```python
lpSum([weight_unassigned_tankers * (1 - is_assigned[tanker]) for tanker in tankers])
```
  This part sums up the product of the weight weight_unassigned_tankers and (1 - is_assigned[tanker]) for all tankers. The decision variable is_assigned[tanker] indicates whether a tanker is assigned (1) or not (0). Subtracting it from 1 and multiplying by the small weight prioritizes minimizing the number of unassigned tankers, but with much less importance than maximizing the total capacity assigned.


## Step 5: Adding Constraints

We add several constraints to our model:

- **Total Capacity Constraint**: Ensures that the total capacity of the segments assigned to a tanker does not exceed the tanker's capacity by more than a predefined threshold (e.g., 10%). This is achieved by setting a minimum and maximum capacity limit for each tanker based on its capacity and the allowable over-capacity threshold.

- **Assignment Linking Constraint:** Directly links the assign variables to the is_assigned variables for each tanker, ensuring that a segment can only be assigned to a tanker if the tanker is marked as assigned. Additionally, it links the assignment of a tanker to a berth with the assignment of the tanker to a segment within that berth.

- **Single Berth Assignment Constrain**t: Ensures that each tanker is assigned to no more than one berth.

- **Unique Segment Assignment Constraint**: Ensures that no more than one tanker is assigned to each segment.

- **Priority Assignment Constraint**: Ensures that segments are assigned in order of priority, with lower priority segments (higher priority number) only being assigned if all higher priority segments (lower priority number) are already assigned. This is based on the assumption that segments within each berth are pre-ordered from lower to higher priority.



```python
     
# Constraint 1 :  Add constraints to ensure the total capacity of assigned segments does not exceed the tanker's capacity by more than the threshold
 
# Define a small allowable over-capacity threshold (e.g., 10% of the tanker's capacity)
over_capacity_threshold = 0.1
for tanker in tankers:
    tanker_capacity = tanker_capacities[tanker]
    allowable_capacity = tanker_capacity + (tanker_capacity * over_capacity_threshold)
    prob += lpSum(assign[tanker][berth][seg]* segment_capacities for berth in berths for seg in segments[berth] ) >=is_assigned[tanker]* tanker_capacities[tanker]
    prob += lpSum(assign[tanker][berth][seg] * segment_capacities for berth in berths for seg in segments[berth]) <= allowable_capacity
    
   
 # Constraint Directly link assign variables to is_assigned for each tanker
for tanker in tankers:
    for berth in berths:
        for seg in segments[berth]:
            # Ensure assign[tanker][berth][seg] can only be 1 if is_assigned[tanker] is 1
            prob += assign[tanker][berth][seg] <= is_assigned[tanker]
             # Linking tanker-berth assignment to segment assignment
            prob += assign[tanker][berth][seg] <= tanker_berth_assigned[tanker][berth]
#Constraint 2:  Constraint to ensure a tanker is assigned to within one berth
for tanker in tankers:
    prob += lpSum(tanker_berth_assigned[tanker][berth] for berth in berths) <= 1
# Constraint 3 : No more than one tanker per segment
for berth in berths:
    for seg in segments[berth]:
        prob += lpSum([assign[tanker][berth][seg] for tanker in tankers]) <= 1

#Constraint 4:  Add constraints to ensure the segments are assigned in order of priority
# Assuming segments are already ordered from lower to higher within each berth
segment_priorities = {berth: {seg: idx for idx, seg in enumerate(segments[berth])} for berth in berths}

# Step 2: Add priority constraints
for tanker in tankers:
    for berth in berths:
        for seg in segments[berth]:
            # Get the priority of the current segment
            current_priority = segment_priorities[berth][seg]
            # For each segment with a lower priority (higher priority number), ensure it's assigned if the current one is
            for lower_seg in segments[berth]:
                if segment_priorities[berth][lower_seg] < current_priority:
                    # Ensure lower segments are assigned before the current segment
                    prob += lpSum(assign[other_tanker][berth][lower_seg] for other_tanker in tankers) >= assign[tanker][berth][seg]



```

## Step 6: Solving the Problem

We solve the problem and print the status of the solution. If the problem is solved successfully, we extract and print the assignments.

```python
# Solve the problem
prob.solve()

# Print the status of the solution
print("Status:", LpStatus[prob.status])
```

## Step 7: Displaying the Results

Finally, we organize the assignment results into a DataFrame for easy viewing and print the total optimized value of our objective function.

```python
# Print the results
assignments = []
for tanker in tankers:
    for berth in berths:
        for seg in segments[berth]:
            if value(assign[tanker][berth][seg]) == 1:
                assignments.append((tanker, berth, seg))

assignment_df = pd.DataFrame(assignments, columns=['Tanker', 'Berth', 'Segment'])
print("\nAssignments:")
print(assignment_df)
```

Output 
```
Assignments:
     Tanker   Berth   Segment
0   Tanker2  Berth2  Segment2
1   Tanker2  Berth2  Segment3
2   Tanker3  Berth2  Segment1
3   Tanker7  Berth1  Segment2
4  Tanker10  Berth1  Segment1
```

## Conclusion

In this blog, we've demonstrated how to tackle a tanker assignment problem using linear programming with Python's PuLP library. By carefully defining our decision variables, objective function, and constraints, we've developed a model that optimizes tanker assignments to port segments, ensuring efficient utilization of both tanker and segment capacities. This approach can be adapted to various resource allocation problems, showcasing the power and flexibility of linear programming in operational research.
```
