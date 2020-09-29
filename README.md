# -Knapsack-problem-Special-Case in Python
# Efficient Allocation Problem Statement: Filling 10 Backpacks with 20 different size boxes might seem a common task BUT different variables have to be considered to achieve this. What is an efficient allocation of these 20 boxes into the available backpacks?
# Hints:
# 1) Box type: assume type A and type B
# 2) Box size: they are not the same
# 3) Backpack capacity: They are not the same
# 4) Box timming: Boxes at the end of the truck might be a better fit
#
#
# PROBLEM APPROACH: 
# This is a special case of “Knapsack problem”, because boxes show only sizes not values, in consequence one decision variable will be size of Boxes and Backpacks.
# As size criteria is involved, order of loading should be replicated in the same way anybody naturally do. And this is another decision variable: order of Boxes to be loaded and order of Backpack to be loaded.
# Winning approach (efficient allocation) will be the one that carries the most of Boxes and uses the least of Backpacks simulating loading scenarios, starting from known sizes that will be fixed for all scenarios and load shifts that will vary for each scenario, as it happened in real life.
#
# SIZES DATA: 
# Boxes = 20 items
# Backpacks = 10 items
# 20 random numbers between 1 & 100 were generated:
# 45 30 61 82 78 5 70 3 79 15 69 7 51 34	22 21 92 29 16 72
# 10 random numbers between 1 & 100 were generated:
# 32 10 19 96 43 63 85 33 60 54
# Both sets are showed here as random generator throw them.
#
# SCENARIOS TO SIMULATE:
# In the next pages, 5 scenarios were created to simulate loading process in the most naturally way it occurs.
# Without changed sizes for Boxes neither Backpacks, stated on section  “ Sizes Data”, scenarios propose only order to load and be loaded:
# Boxes Sizes smaller to bigger & Backpacks too
# Boxes Sizes smaller to bigger & Backpacks upside down
# Boxes Sizes bigger to smaller & Backpacks too
# Boxes Sizes bigger to smaller & Backpacks upside down
# Boxes Sizes & Backpacks randomly ordered
#
# CONCLUSION:
# Several set of data were tested throw the 5 ordering scenarios within next conclusion:
# Both, Boxes & Backpacks should be ordered from bigger to smaller to be loaded, and max Boxes load using least Backpacks arise.
# Imagine 20 Boxes on the floor in front of you ordered from left to right in bigger to smaller. Back of you 10 Backpacks follow same order than Boxes, biggest Box has in front biggest Backpack. Both showing size (Box) and size capacity (Backpack).
# 1st Backpack (biggest available) is taken and choose from Boxes starting from smaller ones until reach its max size capacity. Full Backpack is stored in truck and starts again.
# 
# METHODOLOGY:
# Code=>Code in Python was built to reach a solution as a knapsack problem variation using dynamic programming with top-down approach. In last section, full code is showed.
# Program Code explanation=>
# 1. Initial variables are set for quantity of Boxes (20) and no values or explicit preference per each. In this instance all valued equal to 1.
# 2. The user is prompted to enter 20 Boxes sizes, if efficient allocation is pursued should ordered from bigger to smaller as discussed previously.
# 3. Simulating loading process, user is asked to enter one by one each of the 10 Backpacks (considerer order as point # 2). 
# 4. The result is a summary table of simulation result, showed in previous slides (Scenario pages).

# PYTHON CODE:
import pandas as pd

def knapsack(value, weight, capacity):
    """Return the quantity of Boxes that doesn't exceed capacity.
 
    value[i] is the quantity of item i and weight[i] is the size of item i
    for 1 <= i <= n where n is the number of items.
 
    capacity is the maximum size (volume).
    """
    n = len(value) - 1
 
    # m[i][w] will store the maximum quantity that can be attained with a maximum
    # capacity of w and using only the first i items
    m = [[-1]*(capacity + 1) for _ in range(n + 1)]
 
    return knapsack_helper(value, weight, m, n, capacity)
 
 
def knapsack_helper(value, weight, m, i, w):
    """Return maximum quantity of first i items attainable with size <= w.
 
    m[i][w] will store the maximum quantity that can be attained with a maximum
    capacity of w and using only the first i items
    This function fills m as smaller subproblems needed to compute m[i][w] are
    solved.
 
    value[i] is the quantity of item i and weight[i] is the size of item i
    for 1 <= i <= n where n is the number of items.
    """
    if m[i][w] >= 0:
        return m[i][w]
 
    if i == 0:
        q = 0
    elif weight[i] <= w:
        q = max(knapsack_helper(value, weight,
                                m, i - 1 , w - weight[i])
                + value[i],
                knapsack_helper(value, weight,
                                m, i - 1 , w))
    else:
        q = knapsack_helper(value, weight,
                            m, i - 1 , w)
    m[i][w] = q
    return q
 
 
n = 20
value = ('1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1'
               .format(n)).split()
value = [int(v) for v in value]
value.insert(0, None) # so that the quantity of the ith item is at value[i]
weight = input('Enter the positive integer size (volume) of the 20 boxes: '
               .format(n)).split()
weight = [int(w) for w in weight]
weight.insert(0, None) # so that the size of the ith item is at weight[i]
Summary = pd.DataFrame(columns=['Max.Vol.Cap.', 'Tot. Boxes'])
bag_null = 0
for b in range(10):
    capacity = 0 
    if n != 0:
        if capacity < int(min(weight[1:])):
            capacity = int(input('Enter maximum positive integer size(volume) of next available backpack (max 10 backpacks): ')) 
            if capacity < int(min(weight[1:])):
                print(' BackPack given will be discarded because it is smaller than boxes pending to load')
                bag_null = bag_null + 1
            ans = knapsack(value, weight, capacity)
            Summary.loc[b] = [capacity, ans]
            n=n-ans
            del(value[1:(ans+1)])
            del(weight[1:(ans+1)])
        else: b = 10  
Summary = Summary.rename(index = lambda x: x + 1)
Summary.index.name = 'Backpack'
display(Summary)
print(' Total Backpacks discarded: ',  bag_null)
print(' Total Boxes not loaded: ',n)
