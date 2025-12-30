
import math

def solve():
    import sys
    input = sys.stdin.read
    data = input().split()
    if not data:
        return
    N = int(data[0])
    orders = []
    idx = 1
    for i in range(N):
        t = int(data[idx])
        x = int(data[idx+1])
        y = int(data[idx+2])
        p = int(data[idx+3])
        q = int(data[idx+4])
        otype = data[idx+5]
        orders.append({
            'id': i, 't': t, 'x': x, 'y': y, 'p': p, 'q': q, 'type': otype
        })
        idx += 6
    # Sort orders by pickup time
    orders.sort(key=lambda x: x['t'])
    def get_dist(x1, y1, x2, y2):
        d = math.sqrt((x1 - x2)**2 + (y1 - y2)**2)
        return int(math.ceil(d))
    # dp[i] = max profit where order i is the LAST order STARTED
    dp = [0] * N
    # Precompute individual order profits and durations
    profits = []
    for i in range(N):
        profits.append(get_dist(orders[i]['x'], orders[i]['y'], orders[i]['p'], orders[i]['q']))
    for i in range(N):
        # 1. Base case: Can we reach order i from start (0,0) at time 0?
        dist_from_start = get_dist(0, 0, orders[i]['x'], orders[i]['y'])
        if dist_from_start <= orders[i]['t']:
            dp[i] = profits[i]
        else:
            dp[i] = -float('inf') # Mark as unreachable
        # 2. Try to transition from a previous order j
        for j in range(i):
            if dp[j] == -float('inf'):
                continue
            # Case A: Order j and Order i are independent (Sequential)
            # We must check if we can pick up i after dropping off j (and whatever j was paired with)
            # This requires knowing if j was part of a combo. To simplify, we'll handle combos 
            # inside the loop as a single "mega-order".  
            # Check if j was a standalone order
            finish_time_j = orders[j]['t'] + profits[j]
            dist_to_i = get_dist(orders[j]['p'], orders[j]['q'], orders[i]['x'], orders[i]['y'])
            if finish_time_j + dist_to_i <= orders[i]['t']:
                dp[i] = max(dp[i], dp[j] + profits[i])
            # Case B: Order i is a Passenger picked up while Order j (Courier) is active
            if orders[j]['type'] == 'C' and orders[i]['type'] == 'P':
                dist_j_to_i = get_dist(orders[j]['x'], orders[j]['y'], orders[i]['x'], orders[i]['y'])
                if orders[j]['t'] + dist_j_to_i <= orders[i]['t']:
                    # This is a combo. However, the profit for i is already added.
                    # We need to ensure that the logic doesn't double count or skip constraints.
                    # A better DP approach for this specific rule:
                    pass
    # REFINED APPROACH for Courier/Passenger overlap:
    # Let's redefine DP: dp[i] is max profit after COMPLETING order i's dropoff.
    dp = [0] * N
    # finish_info[i] stores (time, x, y) after order i is dropped
    # Because of the overlap rule, we'll calculate transitions specifically.
    for i in range(N):
        # Option 1: Start with order i as the first order
        if get_dist(0,0, orders[i]['x'], orders[i]['y']) <= orders[i]['t']:
            dp[i] = profits[i]     
        for j in range(i):
            # Try to come from order j
            # Scenario 1: j finished, then travel to i
            end_time_j = orders[j]['t'] + profits[j]
            if end_time_j + get_dist(orders[j]['p'], orders[j]['q'], orders[i]['x'], orders[i]['y']) <= orders[i]['t']:
                dp[i] = max(dp[i], dp[j] + profits[i]) 
            # Scenario 2: j was a Courier, i is a Passenger picked up during j
            if orders[j]['type'] == 'C' and orders[i]['type'] == 'P':
                if orders[j]['t'] + get_dist(orders[j]['x'], orders[j]['y'], orders[i]['x'], orders[i]['y']) <= orders[i]['t']:
                    # We pick up j, then pick up i, drop i, then drop j.
                    # Total profit = profit[j] + profit[i]
                    # This "task" ends at orders[j].drop location at a specific time.
                    # To handle this, we treat the "Combo" as a potential predecessor for future orders.
                    combo_profit = profits[j] + profits[i]
                    # Time finished = Time i dropped + dist(i_drop to j_drop)
                    finish_time_combo = orders[i]['t'] + profits[i] + get_dist(orders[i]['p'], orders[i]['q'], orders[j]['p'], orders[j]['q'])
                    # Now, can we take any order 'k' AFTER this combo? 
                    # This suggests we need a slightly different DP or to update dp[i] 
                    # considering i is the "last" order picked up.
                    dp[i] = max(dp[i], (dp[j] - profits[j]) + combo_profit if dp[j] > 0 else combo_profit)

    print(max(dp) if dp else 0)

solve()
