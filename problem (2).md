#include<bits/stdc++.h>
using namespace std;

struct Order{
  long t;
  double px,py;
  double dx,dy;
  char type;
};

double dist(double x1,double x2,double y1,double y2)
{
  return squrt((x1-x2)*(x1-x2)+(y1-y2)*(y1-y2));
}
int main()
{
  ios::sync_with_stdio(false);
  cin.tie(nullptr);
  int N;
  cin>>N;

  vector<Order>a(N);
  for(int i=0;i<N;i++){
    cin>>a[i].t>>a[i].px>>a[i].py>>a[i].dx>>a[i].dy>>a[i].type;
  }
  sort(a.begin(),a.end(),[](const Order &A, const Order &B){
    return A.t<B.t;
  }
);
vector<double>dp(N,0,0);
double answer=0.0;
for(int i=0;i<N;i++){
  double profit = dist(a[i].px,a[i].py,a[i].dx,a[i].dy);
  double travel_from_start=dist(0,0,a[i].px,a[i].py);
  if(travel_from_start<=a[i].t){
    dp[i]=profit;
  }
  for(int j=0;j<i;j++){
    if(dp[j]==0) continue;
    double prev_finist_time=a[j].t+dist(a[j].px,a[j].py,a[j].dx,a[j].dy);
    double travel_time=dist(a[j].dx,a[j].dy,a[j].px,a[j].py);
    if(prev_finish_time+travel_time>a[i].t) continue;
      dp[i]=max(dp[i],dp[j]+profit);
  }
  answer=max(answer,dp[i]);
}
cout<<(long long)floor(answer)<<"\n";
return 0;
}
# SwiftWheel: Profit Maximization

Time Limit: **2 seconds**

Memory Limit: **256 MB**

## Problem

SwiftWheel is a two-wheeler ride and courier service.  
A driver receives a list of orders throughout the day.  
Each order must be accepted **exactly at its scheduled pickup time** if chosen.

Your task is to determine the **maximum profit** the driver can earn.

---

## Order Format

Each order consists of:

- **T** — scheduled pickup time  
- **x, y** — pickup coordinates  
- **p, q** — drop coordinates  
- **type** —  
  - `P` for passenger  
  - `C` for courier  

To accept an order, the driver must be **at the pickup location at time T**. He can reach there before T and wait. But reaching late is not allowed.

---

## Profit

**Profit of an order:**

$$
profit = \lceil EuclideanDistance(pickup, drop) \rceil
$$

**Travel time between any two points:**

$$
travelTime = \lceil EuclideanDistance(pointA, pointB) \rceil
$$



---

## Rules

1. Travel has no monetary cost.  
2. Only **one courier order** may be carried at a time.  
3. A courier and a passenger may be carried together, but:
   - **Passenger trips cannot be interrupted**.
   - No courier pickup or drop can occur during a passenger ride.
4. The driver starts at time **0** at location **(0,0)**.  
5. Every order may be accepted or rejected. If accepted, pickup time must be met exactly.

---

## Input Format

- The first line contains an integer **\( N \)** — the number of orders.
- Each of the next **\( N \)** lines contains **six values** describing a single order:
  - **$T_i$** — pickup time
  - **$x_i, y_i$** — pickup coordinates
  - **$p_i, q_i$** — drop coordinates
  - **$\text{type}_i$** — a character `P` (passenger) or `C` (courier)


---

## Output Format

Output a single integer — the maximum total profit achievable.

---

## Constraints

- $1 \le N \le 2000$
- $\lvert x_i \rvert, \lvert y_i \rvert, \lvert p_i \rvert, \lvert q_i \rvert \le 10^{6}$
- $0 \le T_i \le 10^{9}$

---

## Examples :-

### Example 1

#### **Input**
```
3
3 0 0 3 4 P
10 3 4 6 8 C
20 6 8 7 8 P
```

#### **Output**
```
11
```

#### Explanation

- **Order 1:** Pickup at time 3 at (0,0), drop at (3,4).  
  Profit = ceil(distance) = ceil(5) = **5**.

- **Order 2:** Pickup at time 10 at (3,4), drop at (6,8).  
  Profit = ceil(distance) = ceil(5) = **5**.

- **Order 3:** Pickup at time 20 at (6,8), drop at (7,8).  
  Profit = ceil(distance) = **1**.

From the starting point (0,0), the driver can:

1. Accept order 1 (reachable at time 3).  
2. Finish order 1 at time 3 + 5 = **8** at (3,4), then reach the pickup of order 2 at time 10.  
3. Finish order 2 at time 15 at (6,8), then reach the pickup of order 3 at time 20.
4. Finish order 3 at time 21 at (7,8)

So, all 3 orders can be completed giving total profit of 11.

---

### Example 2

#### Input
```
2
5 0 0 3 4 P
7 3 4 8 4 C
```
#### Output
```
5
```


#### Explanation

- **Order 1:** Must be picked at time 5 at (0,0).  
  Profit = ceil(5) = **5**  
  This order is always feasible from the starting point.

- **Order 2:** Must be picked at time 7 at (3,4).
  Profit = ceil(5) = **5**  
  If order 1 is accepted, the driver finishes at time 10 → too late to reach pickup at time 7.  
  So **order 1 and order 2 cannot be taken together**.

Thus, the best choice is to accept either order 1 or order 2, giving total profit **5**.

---

### Example 3

#### Input
```
2
5 3 4 15 4 C
6 4 4 8 4 P
```
#### Output
```
16
```

#### Explanation

- **Order 1:** Must be picked at time 5 at (3,4).  
  Profit = ceil(12) = **12**  
  This order is feasible from the starting point by reaching from (0,0) to (3,4) at time 5.

- **Order 2:** Must be picked at time 6 at (4,4).  
  Profit = ceil(4) = **4**  
  If order 1 of courier is accepted, the driver can reach from (3,4) to (4,4) at time 6.  
  As per rule, passenger needs to be dropped before the courier. So, passenger can be dropped at time 10 at (8,4) followed by courier drop at time 17 at (15,4).  
  So both the orders can be taken together giving a total profit of 16.

