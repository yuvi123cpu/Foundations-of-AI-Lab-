# Foundations-of-AI-Lab-

[chatgpt link for water_jug_ref](https://chatgpt.com/share/6a69d539-c598-83e8-ae31-a7bf50bf649d)

#CODE

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
