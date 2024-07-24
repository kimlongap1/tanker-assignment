
# Guide to Solving the Tanker Assignment Challenge with Linear Programming - Simple version

In this blog post, I will explore how to solve a complex tanker assignment problem using linear programming in Python. This is a continuation of my series on using AI for product development.You can refer to my previous post, [My Journey into AI: How I Started Using AI for Product Development](https://www.linkedin.com/pulse/my-journey-ai-how-i-started-using-product-development-phu-hoang-10efc/?trackingId=XXHufOjXQoOYKtQc4hWFTQ%3D%3D),  My objective is to optimally assign tankers to segments within berths at a port, ensuring that the total capacity of assigned segments closely matches the tanker's capacity without significant over-assignment. I'll use the PuLP library, a popular linear programming package in Python, to model and solve this problem.

## Introduction
The tanker assignment challenge is a common problem in the maritime industry. Efficiently allocating tankers to berths and their respective segments is crucial for maximizing port utilization and minimizing idle times. By leveraging linear programming, we can create a model that helps in making these assignments more efficient and effective.

## They way approach
- **Defining the Data**: Start by defining our tankers, berths, and segments. Each tanker has a capacity, each berth has a number of segments, and each segment has its own capacity.
- **Modeling the Problem:**
  - **Step 1 - Decision Variables:** Define binary decision variables indicating whether a tanker is assigned to a specific segment.
  - ** Step 2 - Objective Function:** Create an objective function that minimizes the total difference between the tankers' capacities and the assigned segments' capacities.
  -** Step 3- Constraints:** Ensure that each tanker is assigned to exactly one segment and that the capacity of assigned segments does not exceed the tanker's capacity.

I will use this approach with mutil version from simple to complex

## 1. [First version - the simplest one](simple-version/berth-schedule-simple-version.ipnyb)
In this version, we will create a simple case that reflects a real-life situation without being overly complex. This initial setup will help us understand the problem and develop a basic solution. We will enhance and expand the data structures later as needed.

- Tankers: A list of tankers, each with a specific capacity.

- Berths: A list of berths, each with multiple segments.

- Segments: Segments within each berth, each with a default capacity

Detail in [ berth schedule simple version](simple-version/simple-version/berth-schedule-simple-version.ipnyb)

## Conclusion

In this blog, we've demonstrated how to tackle a tanker assignment problem using linear programming with Python's PuLP library. By carefully defining our decision variables, objective function, and constraints, we've developed a model that optimizes tanker assignments to port segments, ensuring efficient utilization of both tanker and segment capacities. This approach can be adapted to various resource allocation problems, showcasing the power and flexibility of linear programming in operational research.
```
