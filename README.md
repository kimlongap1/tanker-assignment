In this blog, we'll explore how to solve a complex tanker assignment problem using linear programming in Python. Our goal is to optimally assign tankers to segments within berths at a port, ensuring that the total capacity of assigned segments closely matches the tanker's capacity without significant over-assignment. We'll use the PuLP library, a popular linear programming package in Python, to model and solve this problem.

Step 1: Setting Up the Problem
First, we import the necessary libraries and initialize our problem in PuLP. We define our problem as a minimization problem named "TankerAssignment".
from pulp import *
import pandas as pd
import numpy as np

# Define problem
prob = LpProblem("TankerAssignment", LpMinimize)
)
Step 2: Defining Data Structures
We define the data structures to hold our tankers, their capacities, berths, segments within each berth, and the capacity of each segment.
