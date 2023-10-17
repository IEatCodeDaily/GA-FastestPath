---
Course: IK6013 Optimization Technique
Creation Date: 2023-10-03 09:09
Due Date: 2023-10-17 00:00
Status: false
tags:
  - College/IK6013
  - Assignment/IK6013
---
Name: Raisal P Wardana
NIM: 23823004

# Assignment
Cari kasus dan selesaikan dengan GA atau SA
Submit laporan dan computation code:
1. Introduction
2. Formulation
3. Code Explanation
4. Result and Discussion
5. References

# Introduction
## Objective: A-to-B Path Optimization
The primary objective of this project is to develop a Generative Algorithm (GA) that optimizes the path from A-to-B in a city with varying traffic conditions.

## Background
The task of optimizing package delivery paths is a complex problem, often resembling the well-known Traveling Salesman Problem (TSP). The challenge is to find the most efficient route that minimizes the time spent on the road, considering factors such as traffic speed on different streets.

# Problem Formulation
## Problem Statement
Given a set of nodes representing intersections and edges representing streets with varying speeds, find the most efficient route from a start node to an end node for package delivery.

## Variables
$N:$ The set of nodes (intersections)
$E:$ The set of edges (streets), each with an associated speed

## Constraints
* The route must start at a given node $S$ and end at a given node $E$.

## Objective Function
Minimize the total time required for the travel time from $S$ to $E$.

# Code Explanation
## Environment Generation
### Node Placement
Nodes are randomly generated within a 1000x1000 grid to represent intersections in the city.
### Edge Connection
Edges connect each node to 1-7 of its closest neighbors, representing streets. Some streets can only be traveled one-way.
### Traffic Data
Each edge is assigned a randomized "speed" value from a minimum of 5 and a maximum of 50 to simulate different traffic conditions. The speed of an edge is different depending on the direction of travel. To put it simply, A-to-B and B-to-A can have different speed.
### Generated City Environment
Below is the generated city environment, with the color of each arrow representing the speed of an agent when travelling in the same direction as the arrow.
![[Pasted image 20231017204351.png]]
## Generative Algorithm
The agents will start at node `30,95` and try to reach the goal at node `828,890`.
### Initialization
Chromosomes are initialized with random walks from the start node to the end node, following the existing edges. A chromosome consists of all the nodes the agent will visit. An example of chromosome is
`[(30, 95), (228, 142), (344, 104), (296, 81), (432, 32), (549, 127), (432, 32), (591, 196), (654, 114), (591, 196), (603, 284), (591, 196), (603, 284), (565, 300), (714, 432), (747, 470), (882, 370), (854, 373), (913, 558), (849, 643), (828, 890)]`
and below is the corresponding path.
![[Pasted image 20231017205437.png]]
### Fitness Evaluation
Each chromosome is evaluated based on the sum of the squared time required to traverse each edge.The time for each edge is calculated as the distance divided by the speed.
* Not reaching the end node is very heavily penalized
* Staying put is heavily penalized
* Revisiting a node is penalized
Agents with lower scores are valued highly compared to other agents. Below is the code snippets to evaluate the fitness of an agent.
```python
def evaluate_fitness(chromosome, edge_lengths, edge_speeds):
    fitness = 0  # Starting fitness points
    visited = []
    for i in range(len(chromosome) - 1):
        edge = (chromosome[i], chromosome[i + 1])
        # Retrieve the pre-calculated length of the edge
        if edge in edge_lengths:
          edge_length = edge_lengths[edge]
          # Calculate the travel time for the edge
          travel_time = edge_length / edge_speeds[edge]
          # Deduct the travel time from the fitness
          fitness += (travel_time**2)
        # No action penalty
        else:
          fitness += 5000
        # Revisiting penalty
        if chromosome[i] in visited:
          fitness += 1000
        visited.append(chromosome[i])
    # Not reaching end node penalty
    if chromosome[-1] != end_node:
      fitness += 100000
    return fitness
```
### Selection, Crossover, and Mutation
* **Selection**: Roulette Wheel Selection is used, with a higher chance of selecting chromosomes that have better fitness scores.
* **Crossover**: two parents chromosomes are randomly chosen with a weighted probability depending on the chromosome's fitness score. A random identical node within the chromosome will be chosen and spliced together, creating two different offsprings.
* **Mutation**: Random mutations are introduced to add variability by choosing a random node within a chromosome and propagating it.
* **Random Population**: A certain amount of randomly generated population are introduced in each generation to prevent the agent getting stuck in a local optima.
#### Parameters
mutation rate = 0.95 (mutation rate are set very high as the chance of an identical node existing in two parents is erxtremely low)
random population_rate = 0.4
population size = 100
chromosome length = 50
max generations = 1000
# Result and Discussion
## Performance Metrics
The algorithm's performance was evaluated based on the average travel time and best fitness over 1000 generations.
![[Pasted image 20231017211227.png|400]]![[Pasted image 20231017211234.png|400]]
The algorithm quickly converges to a near-optimal solution within the first few generations. Below is the path taken by the best agent of several generations.
![[Pasted image 20231017211318.png]]
Initially, the agent's behavior is highly inefficient, frequently retracing its steps between nodes. However, as the generations progress, the agent's movements become more optimized. By the 1000th generation, there are no more instances of the agent moving back and forth between the same nodes.
Intriguingly, the 500th generation exhibits a faster travel time compared to the 1000th generation. This is likely due to the limitations of the fitness score formula, which may not be sensitive enough to capture minute improvements.

Animation of the best agent for some key generations is attached to this assignment with the file name `best_path_animation_final.gif`.
![[best_path_animation_final.gif]]
## Limitations and Future Work
While the algorithm performs well, there are limitations, such as not considering real-time traffic updates. Future work could integrate real-time data to improve performance.

References
1. Goldberg, D. E. (1989). Genetic Algorithms in Search, Optimization, and Machine Learning. Addison-Wesley.
2. GeeksforGeeks. (n.d.). Traveling Salesman Problem using Genetic Algorithm. https://www.geeksforgeeks.org/traveling-salesman-problem-using-genetic-algorithm/