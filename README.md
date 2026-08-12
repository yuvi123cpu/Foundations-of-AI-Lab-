# Foundations-of-AI-Lab-

[chatgpt link for water_jug_ref](https://chatgpt.com/share/6a69d539-c598-83e8-ae31-a7bf50bf649d)

# Water Jug Problem

def successors(state):
    x, y = state
    pour_x_to_y = min(x, 3 - y)     # X gives what it has, Y takes what fits
    pour_y_to_x = min(y, 4 - x)
    return [(4, y) # 1. Fill X
            (x, 3),                 # 2. Fill Y
            (0, y),                 # 3. Empty X
            (x, 0),                 # 4. Empty Y
            (x - pour_x_to_y, y + pour_x_to_y),   # 5. Pour X into Y
            (x + pour_y_to_x, y - pour_y_to_x)]   # 6. Pour Y into X
start    = (0, 0)
seen     = {start}      # EVERY state ever found  (a set)
frontier = [start]      # states found at the current level
level    = 0
while frontier and level < 8:
    new = []
    for s in frontier:
        for ns in successors(s):
            if ns not in seen:            # <-- the line that matters
                seen.add(ns)
                new.append(ns)
                if ns[0] == 2:
                    print("   *** GOAL: 2L in X at level", level+1, "***")
    level += 1
    print("Level", level, "->", len(new), "new states:", sorted(new))
    frontier = new          # what we just found becomes what we expand next
print("Total states found:", len(seen))



# BFS


graph = {'A': ['B','C'],
         'B': ['D','E'],
         'C': ['F','G'],
         'D': [],  'E': ['H'],
         'F': [],  'G': [],  'H': []}
def bfs(start, goal):
    queue   = [[start]]     # a list of PATHS, not nodes
    visited = []
    while queue:
        path = queue.pop(0)     # <-- FIFO: take from the FRONT
        node = path[-1]         # current node = last item of the path
        if node == goal:
            return path, visited
        if node not in visited:
            visited.append(node)
            for n in graph[node]:
                queue.append(path + [n])
    return None, visited
p, v = bfs('A', 'H')
print("BFS path   :", p)
print("BFS visited:", v)


# DFS



graph = {'A': ['B','C'],
         'B': ['D','E'],
         'C': ['F','G'],
         'D': [],  'E': ['H'],
         'F': [],  'G': [],  'H': []}
def dfs(start, goal):
    stack   = [[start]]         # same structure as BFS
    visited = []
    while stack:
        path = stack.pop()      # <-- LIFO: take from the END
        node = path[-1]
        if node == goal:
            return path, visited
        if node not in visited:
            visited.append(node)
            for n in reversed(graph[node]):   # reverse: left child on top
                stack.append(path + [n])
    return None, visited
p, v = dfs('A', 'H')
print("DFS path   :", p)
print("DFS visited:", v)



# UCS(Uniform Cost Search)


graph = {'A': [('B',1), ('C',4)],
         'B': [('D',5), ('C',2)],
         'C': [('D',1)],
         'D': []}
def ucs(start, goal):
    queue = [(0, [start])]      
    while queue:
        queue.sort()             
        cost, path = queue.pop(0)
        if path[-1] == goal:     
            return cost, path
        for n, w in graph[path[-1]]:
            queue.append((cost + w, path + [n]))
    return None
print("UCS:", ucs('A', 'D'))



# Greedy



graph = {'S': [('A',2), ('B',1)],
         'A': [('G',10)],
         'B': [('G',4)],
         'G': []}
h = {'S': 4, 'A': 2, 'B': 3, 'G': 0}     # estimated distance to G
def cost_of(path):
    """Add up the REAL edge costs of a finished path."""
    return sum([w for n, w in graph[a] if n == b][0]
               for a, b in zip(path, path[1:]))
def greedy(start, goal):
    queue = [(h[start], [start])]        # (heuristic, path)
    while queue:
        queue.sort()                     # smallest h first
        _, path = queue.pop(0)
        node = path[-1]
        if node == goal:
            return path, cost_of(path)   # measure damage only at the end
        for n, w in graph[node]:
            queue.append((h[n], path + [n]))    # <-- w is THROWN AWAY
path, cost = greedy('S', 'G')
print("Greedy path :", path)
print("Actual cost :", cost)


#A* Algo


graph = {'S': [('A',2), ('B',1)],
         'A': [('G',10)],
         'B': [('G',4)],
         'G': []}
h = {'S': 4, 'A': 2, 'B': 3, 'G': 0}
# ---- check the heuristic never overestimates ----
true_cost = {'S': 5, 'A': 10, 'B': 4, 'G': 0}
for node in h:
    ok = "OK" if h[node] <= true_cost[node] else "VIOLATION"
    print(f"  h({node}) = {h[node]}   true = {true_cost[node]}   {ok}")
def astar(start, goal):
    queue = [(h[start], 0, [start])]      # (f, g, path)  <-- CHANGE 1
    while queue:
        queue.sort()                      # smallest f first
        f, g, path = queue.pop(0)         # <-- CHANGE 2
        node = path[-1]
        if node == goal:
            return path, g                # g IS the answer, already tracked
        for n, w in graph[node]:
            queue.append((g + w + h[n],   # new f      <-- CHANGE 3
                          g + w,          # new g
                          path + [n]))
path, cost = astar('S', 'G')
print("A* path :", path)
print("Cost    :", cost)





