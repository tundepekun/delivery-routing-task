# Capacitated Vehicle Routing Problem (CVRP)

## Problem Description
This project solves a Capacitated Vehicle Routing Problem (CVRP) with the objective of minimizing total travel distance while satisfying routing and capacity constraints. The problem consists of one depot (node 0), twenty delivery locations (nodes 1–20), and two identical delivery vehicles with fixed capacity. Each delivery location has a known demand, and the distance between every pair of nodes is given by a 21×21 distance matrix.

The goal is to assign all delivery locations to the two vehicles such that each location is visited exactly once, each vehicle starts and ends at the depot, vehicle capacity limits are respected, and the total distance traveled across all routes is minimized.



## Modeling Approach
The problem is formulated as a single-commodity flow-based mixed-integer linear program (MILP) and solved using Gurobi. Binary decision variables determine routing decisions, while continuous flow variables track vehicle load to enforce capacity constraints and eliminate subtours.

### Key Decision Variables
- x_ij ∈ {0,1}: equals 1 if a vehicle travels directly from node i to node j
- f_ij ≥ 0: remaining vehicle load on arc (i, j)

### Objective Function
Minimize total travel distance:

min ∑_{i ∈ N} ∑_{j ∈ N, j ≠ i} d_ij · x_ij

### Core Constraints

∑_{j ∈ N, j ≠ i} x_ij = 1   ∀ i ∈ C  
∑_{j ∈ N, j ≠ i} x_ji = 1   ∀ i ∈ C  

Each customer is visited exactly once


∑_{j ∈ C} x_0j = m  
∑_{i ∈ C} x_i0 = m 

Exactly m vehicles leave and return to the depot


∑_{j ∈ N, j ≠ i} f_ji − ∑_{j ∈ N, j ≠ i} f_ij = q_i   ∀ i ∈ C 

The flow conservation constraint ensures that when a vehicle visits customer i, the reduction in carried load equals the customer's demand q_i.


∑_{j ∈ C} f_0j = ∑_{i ∈ C} q_i  

The depot flow constarint ensures the total load leaving the depot equals the total customer demand


0 ≤ f_ij ≤ Q · x_ij   ∀ i, j ∈ N, i ≠ j 

The capacity linking constraints ensurers flow can occur only on used arcs and cannot exceed vehicle capacity


This modeling approach ensures demand is delivered along valid routes, prevents disconnected subtours, and tightly enforces vehicle capacity limits.



## Instructions to Run the Code
1. Install the required Python packages (e.g., Gurobi, Numpy, Matplotlib)

2. Ensure you have a working Gurobi installation and license.

3. Place the input data file `cvrp_problem_data.json` in the same directory as the .ipynb file.

4. Open the notebook and run all cells.

5. The program will solve the CVRP, print routes, loads, and distances for each vehicle, and display a grouped circular visualization of the routes



## Summary of Results 

For the given instance, the solver produces two feasible routes that jointly serve all customers while respecting capacity constraints. Each vehicle starts and ends at the depot, every customer is visited exactly once, and total travel distance is minimized.

The output reports the ordered sequence of nodes for each vehicle, the total load per vehicle, the distance traveled per route, the overall objective value. A visualization is generated to clearly illustrate route structure and travel direction.



## Key Design Decisions and Assumptions

1. A flow-based formulation was chosen over MTZ due to its stronger linear relaxation and improved solver performance.

2. Vehicles are assumed to be identical, with no fixed usage cost.

3. Visualization prioritizes clarity of routes rather than exact geometric distances.



## Limitations and Future Work 

While the flow-based formulation performs well for this problem size, it can become computationally expensive for larger instances due to the number of flow variables. For larger problems, performance can be improved by incorporating valid inequalities, such as rounded capacity cuts, using lazy constraints or callbacks.

Compared to the classical MTZ formulation, the flow-based model provides stronger LP relaxations and better subtour elimination. Future work could explore hybrid formulations or decomposition methods to improve scalability.

Possible extensions include heterogeneous vehicles, time windows, stochastic demand, and systematic benchmarking against MTZ-based formulations.
