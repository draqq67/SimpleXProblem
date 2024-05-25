# Optimization class project
**Realised by Zarnescu Dragos 321 AC**
## Problem

- In my work in organization of events, I stumble across different problems where one of each is volunteer repartization
- In the events, there needs to be taken different amount of volunteers that have different tasks on a given interval.
- The Problem consists in making this repartizations is that you have to take into account every preferrences mentioned. And when you got tens of volunteers or even hundred, it can take some precious time 

## Solution

- One of the solutions to automate this process is to formulate this problem as a Linear Programming Problem where I have to define:
    - the data 
    - Mathematical formulation :
        - the model
        - the constraints 

- Taken this into accounts, I can formulate a resolvation of this problem using Simplex Alghoritm

### The Data

- data taken from 53 volunteers enrolled into an event
- The data taken is from two differents csv that are called:
    - disponibility.csv, for each interval 1 means it can come, 0 means it can't: 
    ```
    nume      ,1,2,3,4,5,6,7
    Voluntar 1,1,1,1,1,1,1,1
    Voluntar 2,0,1,1,0,1,1,0
    Voluntar 3,1,1,1,1,1,1,1
    Voluntar 4,0,1,0,0,0,0,0
    Voluntar 5,1,0,0,0,0,0,0
    ```
    - preferences.csv, the task preffered: 6 the most desired, 1 the least desired
    ```
    nume      ,1,2,3,4,5,6
    Voluntar 1,6,1,3,4,2,5
    Voluntar 2,5,2,3,1,4,6
    Voluntar 3,3,6,1,2,4,5
    Voluntar 4,1,2,3,4,5,6
    Voluntar 5,1,2,3,4,5,6
    ```
    - demands -> randomize demands over the 7 tasks on each that sums up to 100 needed intervals
### Matemathical Formulation

**Sets:**
- _i_ : Set of volunteers ( i = 1, 2, ..., m ).
- _j_ : Set of tasks ( j = 1, 2, ..., n ).
- _d_ : Set of intervals ( d = 1, 2, ...,intervals ).

**Decision Variables:**
- _x<sub>i,j,d</sub>_ : Binary variable indicating whether volunteer ( i ) is assigned to task ( j ) during interval ( d ).

**Parameters:**
- _prefences<sub>i,j</sub>_ : Preference score of volunteer ( i ) when assigned to task (j).
- _demands<sub>j</sub>_ : Demand for task ( j ) across all intervals.
- _interval_preferences<sub>i,d</sub>_ : Preference of volunteer \( i \) for interval \( d \).

#### Objective Function
Maximize the objective function:

$$
\text{Maximize }  \sum_{i=1}^{m} \sum_{j=1}^{n} \sum_{d=1}^{\text{intervals}} \text{preferences}_{i,j} \cdot x_{i,j,d}
$$

#### Constraints
1. Each volunteer is assigned to at most one task in each interval:
$$
\sum_{j=1}^{n} x_{i,j,d} \leq 1 \quad \text{for all } {i, d}
$$
2. Demand constraint: Each task is assigned exactly its demand over all intervals:
$$
\sum_{i=1}^{m} x_{i,j,d} = \text{demands}_j \quad \text{for all } {j, d}
$$
3. Availability constraint: Volunteers can only be assigned to tasks in intervals they are available:
$$
\sum_{j=1}^{n} x_{i,j,d} \leq \text{disponibility}_{i,d} \quad \text {for all } {i,d}
$$
4. Each volunteer can't have more than 2 tasks:
$$
\sum_{j=1}^{n} x_{i,j,d} \leq 2 \quad \text{for all } i
$$

## Code Solution
- Presented in project.ipynb, data extracted from the csvs
```julia
#Needed imports
import Pkg; 
Pkg.add("JuMP") # for resolving the problem
Pkg.add("GLPK") # for loading the model
Pkg.add("DataFrames") # for getting the data from csv
Pkg.add("CSV") # for getting the data from csv
Pkg.add("Plots") # showing bars of the data prelucrated for being in a better visualising way
```
- Short visualization of the code
```julia
#Model Load
Model(GLPK.Optimizer)

@objective(model, Max, sum(preferences[i, j] * x[i, j, d] for i in 1:m, j in 1:n, d in 1:intervals)) #define the objective

@constraint(model,#=mathematical explanation of the constraint=#)

# Solve the model
optimize!(model)

```

