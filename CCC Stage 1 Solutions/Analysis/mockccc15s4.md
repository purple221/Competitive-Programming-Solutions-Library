Approach 1 – Brute Force – O(N!×N)
Generate all permutations in which to break the blocks. Simulate the chain reactions when they occur. 
This is enough to obtain 20% of the points.

Approach 2 – Divide and Conquer – O(N<sup>3</sup>)
A faster solution uses memoization on a divide and conquer principle. T
he subproblem is determining the cost to knock down every pillar in the range [L, R] 
given whether pillar L − 1 is broken and whether pillar R + 1 is broken.

In the base case, L = R, and there is only 1 pillar to consider. The cost to knock it down is just its durability minus the possible 
damages done by the pillars to its left and right.
Otherwise, if there is more than 1 pillar to consider:
If pillar L − 1 is broken and completely knocks over pillar L without you having to do any work, then we can narrow the range by 1 from 
the left and mark the newest left outside-border pillar as broken.
If pillar R + 1 is broken and completely knocks over pillar R without you having to do any work, then we can narrow the range by 1 from 
the right and mark the newest right outside-border pillar as broken.
Otherwise, we test every pillar between L and R as the first pillar to knock down, taking into account collateral damage. 
The total cost is the cost to break down this pillar, plus the cost to break all the pillars to the left of it, plus the cost to break 
all of the pillars to the right of it. In other words, we divide the range in half and let recursion handle each side. 
The minimum cost across all these tests is the answer.
If we cache the results every time we compute them, we only have O(N<sup>2</sup>) possible states to consider 
(all possible L values × all possible R values). Each state requires O(N) time to transition 
(trying to break down each pillar in the range). Overall, the solution is O(N<sup>3</sup>) and is given 60% of the points.

60% Solution (C++)
```cpp
#include <cstdio>
#include <cstring>
using namespace std;

int N, D[305], W[305];
long long memo[305][305][2][2];

long long solve(int L, int R, int Lbroke, int Rbroke) {
  if (L > R) return 0;
  long long &ret = memo[L][R][Lbroke][Rbroke];
  if (ret != -1) return ret;
  if (L == R) { //base case
    ret = D[L];
    if (Lbroke) ret -= W[L - 1];
    if (Rbroke) ret -= W[R + 1];
    if (ret < 0) ret = 0;
    return ret;
  }
  if (Lbroke && W[L - 1] >= D[L])
    return ret = solve(L + 1, R, 1, Rbroke);
  if (Rbroke && W[R + 1] > D[R])
    return ret = solve(L, R - 1, Lbroke, 1);
  ret = 0x3f3f3f3f3f3f3f3fLL;
  for (int i = L; i <= R; i++) {
    long long cost = D[i];
    if (i == L && Lbroke) cost -= W[i - 1];
    else if (i == R && Rbroke) cost -= W[i + 1];
    cost += solve(L, i - 1, Lbroke, 1);
    cost += solve(i + 1, R, 1, Rbroke);
    if (ret > cost) ret = cost;
  }
  return ret;
}

int main() {
  memset(memo, -1, sizeof memo);
  scanf("%d", &N);
  for (int i = 1; i <= N; i++)
    scanf("%d%d", D + i, W + i);
  printf("%Ld\n", solve(1, N, 0, 0));
  return 0;
}
```
Approach 3 – Dynamic Programming – O(N)
To get full marks, we abandon recursion altogether and go for pure dynamic programming. 
This problem displays optimal substructure in the following way. Consider the base case where we only want to break only the first 
(leftmost) block. Given this value, we can then use it to calculate the cost of breaking the two leftmost pillars. 
Likewise, if we know the cost to break the first i pillars, then we can calculate the cost to break the first i + 1 pillars.

There are 2 values in our state: the current pillar we are on, and whether it is broken (either 0 or 1). DP[i][broken?] 
stores the minimum cost to achieve this state. You can think of DP[][0] as the "important" values, since our goal is ultimately to 
knock pillars down. However, we will still need DP[][1] values to calculate the DP[][0] values along the way.

Once again, DP[i][0] represents the cost to break all the pillars from 1 to i. 
Every DP[i][0] state is equal to the minimum cost of two cases:

the pillar before it is already knocked down (i.e. [i − 1][0]): Here, the additional cost to knock down one more pillar 
(the current one) is just the current pillar's durability Di, minus the damage done by the previous pillar Wi − 1.
the pillar before it is not knocked down (i.e. [i − 1][1]): Here, there is no collateral damage at all, so the extra cost to break 
one more pillar (the current one) is just its durability Di.
Calculating DP[i][1] is slightly trickier. We must always assume that the pillar after it is broken. 
This is a valid assumption because in the next iteration, we always ensure that the assumption made in the previous iteration is 
fulfilled by actually assuming again that it is broken (i.e. [i + 1][0]). In other words, the assumptions in one iteration 
depends on the assumptions made in the next, and the assumption in the current iteration depends on the assumptions made in the 
previous. An interesting form of induction indeed. Yet again, we have 2 cases to consider, of which we take the minimum cost:

the pillar before it is already knocked down (i.e. [i − 1][0]): Here, the additional cost to knock down one more pillar 
(the current one) is just the current pillar's durability Di, minus the damage done by the previous pillar Wi − 1, minus the 
damage done by the next pillar Wi + 1.
the pillar before it is not knocked down (i.e. [i − 1][1]): Here, there is no collateral damage at all from the previous pillar, 
so the extra cost to break one more pillar (the current one) is just its durability Di minus the damage done by the next pillar Wi + 1.
The following is my solution implementing this exact idea.

```cpp
#include <algorithm>
#include <cstdio>
using namespace std;
#define hit(a, b) max(0, (a) - (b))

int N, D[100005], W[100005];
long long DP[100005][2] = {0};

int main() {
  scanf("%d", &N);
  for (int i = 1; i <= N; i++) scanf("%d%d", D + i, W + i);
  for (int i = 1; i <= N; i++) {
    DP[i][0] = min( DP[i - 1][0] + hit(D[i], W[i - 1]),
                    DP[i - 1][1] + D[i] );
    DP[i][1] = min( DP[i - 1][0] + hit(hit(D[i], W[i + 1]), W[i - 1]),
                    DP[i - 1][1] + hit(D[i], W[i + 1]));
  }
  printf("%Ld\n", DP[N][0]);
  return 0;
}
```
