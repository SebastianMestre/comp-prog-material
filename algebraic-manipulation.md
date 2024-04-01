
Reason about programs using the algebra of code: Hoare Logic.

stuff like optimizing trial division prime checking by using algebra

EXAMPLE

initial code:

    int i = 2;
    while (true) {
        if (i >= n) break;
        if (n%i == 0) return false;
        i++;
    }
    return true;

unroll 2x, then observe loop invariant

    int i = 2;
    while (true) {
        // Here i%2 == 0

        if (i >= n) break;
        if (n%i == 0) return false;
        i++;

        // Here i%2 == 1

        if (i >= n) break;
        if (n%i == 0) return false;
        i++;

        // Here i%2 == 0
    }
    return true;

add more precondition by modifying the program

    int i = 2;
    if (2 < n && n%2 == 0) {
        return false;
    }
    while (true) {
        if (i >= n) break;
        // Here i is even (loop invariant), and we know n is odd, so this
        // check is always false. We can remove it.
        // if (n%i == 0) return false;
        i++;
        if (i >= n) break;
        if (n%i == 0) return false;
        i++;
    }
    return true;

final code after some cleanups

    if (n%2 == 0) return false;
    int i = 3;
    while (i < n) {
        if (n%i == 0) return false;
        i += 2;
    }
    return true;

Same trick can be done but unrolling `2*3*5` times and skipping checks against multiples of 2, 3 or 5. It looks like this after some (very mechanical) cleanup and compression:

    if (n%2 == 0) return false;
    if (n%3 == 0) return false;
    if (n%5 == 0) return false;
    int i = 7;
    while (true) {
        if (i >= n) break; if (n%i == 0) return false; i += 4;
        if (i >= n) break; if (n%i == 0) return false; i += 2;
        if (i >= n) break; if (n%i == 0) return false; i += 4;
        if (i >= n) break; if (n%i == 0) return false; i += 2;
        if (i >= n) break; if (n%i == 0) return false; i += 4;
        if (i >= n) break; if (n%i == 0) return false; i += 6;
        if (i >= n) break; if (n%i == 0) return false; i += 2;
        if (i >= n) break; if (n%i == 0) return false; i += 6;
    }
    return true;

This precise transformatoin is actually wrong because it breaks the bounds checks. (TODO: do it properly)
