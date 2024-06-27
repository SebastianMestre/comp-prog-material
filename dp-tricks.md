
# Duality of "take 1..k" and "take/no take"

In many DP problems we want to split a stick of size n into pieces that have some special property.

For example, say we want to count the number of ways to express a number as a sum of multiples of 7 (where the order matters).

> I know this can be solved directly by `if (n%7 != 0) then 0 else pow(2, n/7-1)`, but let's pretend we didn't notice that.

We can implement it straightforwardly like this:

```
dp(n) {
  if (n == 0) return 1
  ans = 0
  for (len = 1..n) {
    if (len%7 == 0) ans += dp(n-len)
  }
  return ans;
}
```

The idea is that we just try all the options for the first number in the decomposition of `n`, which
we call `len` , then count the number of solutions for piece of `n` we haven't added yet (`n-len`).

Assuming memoization, this is $O(N^2)$, so pretty slow. Let's see how to optimize it.

## Direct optimization

First, we make it iterative. Every state `n` looks at states `n-len < n`, so we can compute them in increasing order.

```
dp(N) {
  ans = [0 for i =0..N]
  ans[0] = 1
  for (n = 1..N) {
    for (len = 1..n) {
      if (len%7 == 0) ans[n] += and[n-len]
    }
  }
  return ans[N]
}
```

Now, we need some way to compute the inner loop efficiently. This part will change depending on the problem. On this
problem, the observation is simple enough: `ans[n]` will use `ans[n']` if and only if `0 <= n' < n` and `n'%7 = n%7`.

If we separate the results by `r = n%7` we can add them with no additional condition:

```
dp(N) {
  ans = [[0 for i =0..N] for r = 0..6]
  ans[0][0] = 1
  for (n = 1..N) {
    r = n%7
    for (n' = 0..n-1) {
      ans[r][n] += ans[r][n']
    }
  }
  return ans[N%7][N]
}
```

Now the inner loop computes a prefix sum, so we can use some sort of prefix sum data structure. For example a Fenwick
tree or, since our updates would be after all the previous ones, we could iteratively construct a partial sums array as we go.

Those are reasonable approaches for many problems but way overkill this problem. In this case, we
only ever use the full sum and never partial sums, so a simple accumulator suffices:

```
dp(N) {
  ans = [[0 for i =0..N] for r = 0..6]
  acc = [0 for r = 0..6]
  ans[0][0] = 1
  acc[0] = 1
  for (n = 1..N) {
    r = n%7
    ans[r][n] = acc[r]
    acc[r] += ans[r][n]
  }
  return ans[N%7][N]
}
```

And thus, we have a linear time approach. This is fine. It would pass during a contest, but we
had to do a lot of ad-hoc mental work to make it efficient and it can get a lot worse in more
complicated problems, so let's try a different approach.

## "take/no take" approach

The trick here is that this type of "try taking all different prefixes" algorithm can be
mechanically transformed to the simpler form of "keep taking/stop taking" by moving the `len`
variable (and any other auxiliary variables) into the dp state.

It's hard to do when you have a lot of state floating around, so i'll just go back to the
original version to do the transformation.

```
Transformed version (simplified by hand):
dp(n, len) {
  if (n == 0) return 1
  if (len == n+1) return 0
  ans = dp(n, len+1)                      // keep expanding
  if (len%7 == 0) ans += dp(n-len, 1)     // stop now, start a new piece
  return ans
}
```

This code is not particularly optimization friendly, but it does follow the "take/don't take"
form so, rethinking it from that perspective, we can come up with this:

```
dp(n, len) {
  if (n == 0) return len%7 == 0;
  ans = 0
                  ans += dp(n-1, len+1)       // keep expanding
  if (len%7 == 0) ans += dp(n-1, 1)           // stop now, start a new piece
  return ans
}
```

Now the observation would be that we don't actually care about the value of `len`, but
only `len%7`, so we can change the argument to be that instead:

```
dp(n, len_7) {
  if (n == 0) return len_7 == 0;
  ans = 0
                  ans += dp(n-1, (len_7+1)%7) // keep expanding
  if (len_7 == 0) ans += dp(n-1, 1)           // stop now, start a new piece
  return ans
}
```

And this is O(N) and much simpler. Many many problems can be expressed in both ways, but often
one of the two will be much easier to optimize, so this is a trick worth keeping in mind.

Another similar problem would be: count the number of ways to express a number `n` as a sum of numbers not
equal to `5`. Follow up: What if only one number can be equal to `5`? What if only the first and last (or
both) can be equal to `5`?

If you're curious, here is what the mechanically transformed version looks like with no simplification applied:

```
dp(n, len, ans) {
  if (n == 0) return 1
  if (len == n+1) return ans
  if (len%7 == 0) return dp(n, len+1, ans + dp(n-len, 1, 0))
  else            return dp(n, len+1, ans)
}
```
