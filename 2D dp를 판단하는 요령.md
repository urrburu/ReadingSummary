
[Tutorial] 1D and 2D constant time per query range updates (a.k.a difference arrays)
튜토리얼 - 1차원, 2차원 쿼리 범위 업데이트 테크닉
](https://codeforces.com/blog/entry/86420)

By [the_algorithmic_eye](https://codeforces.com/profile/the_algorithmic_eye "Unrated, the_algorithmic_eye"), 22 months ago, ![In English](https://codeforces.org/s/89093/images/flags/24/gb.png "In English")

Small note (was in spoiler but spoiler broke): Many might say this is too trivial a topic to write a blog about but a [google search](https://www.google.com/search?rlz=1C1CHBF_enIN870IN870&ei=uGnzX5-zCPrE4-EPmcmvuAE&q=1D+and+2D+range+update+in+array+difference+array+codeforces&oq=1D+and+2D+range+update+in+array+difference+array+codeforces&gs_lcp=CgZwc3ktYWIQAzIHCCEQChCgAToECAAQRzoICCEQFhAdEB46BQghEKABOgYIIRANEBU6BQgAEM0COgQIIRAVUN-MA1jSnANgzp0DaABwAngAgAGgAogBmxGSAQUwLjIuOJgBAKABAaoBB2d3cy13aXrIAQjAAQE&sclient=psy-ab&ved=0ahUKEwif75f__oLuAhV64jgGHZnkCxcQ4dUDCA0&uact=5) only links to some blogs where people are asking for the concept, rather than some written tutorial with problems(e.g [1](https://codeforces.com/blog/entry/54807), [2](https://codeforces.com/blog/entry/46390)). [This](https://codeforces.com/blog/entry/78762) blog by [arujbansal](https://codeforces.com/profile/arujbansal "Pupil arujbansal") is an exception, but it only has the vanilla 1D version. Hopefully, this current blog can become a good collection of a variety of problems using this technique.

The general flow of the blog would be, first sharing a link to the problem that inspires the concept that follows. You may want to try the problem yourself first. Then I'll try explaining the concept and you can try submitting to get an AC if you want. Let's get started :)

# 1D range updates for adding a constant to the range

**Motivating problem**: [Array Manipulation](https://www.hackerrank.com/challenges/crush/problem)

**Problem Summary**: We are given an array and multiple queries asking us to add a constant value to all elements in the range [𝑙…𝑟][l…r].

**Approach**: Let's think about a somewhat easier problem first. What if we were asked to add a constant value to the suffix of the array starting at index 𝑙l, for multiple queries? Instead of updating the whole suffix for each query, we can only increment the first index of the suffix for each query, i.e 𝑎[𝑙]+=𝑘a[l]+=k. Then after all queries, we take the prefix sum of the whole array. This will "propagate" our increments till the end of the array and thus our task of updating the whole suffix is accomplished at the end, after taking prefix sums. Now, what about updating the range [𝑙…𝑟][l…r]? Can you decompose this range update as 2 suffix updates? It can be decomposed as such: if you need to increment the range [𝑙…𝑟][l…r] by 𝑘k, you can increment the suffix starting at index 𝑙l by 𝑘k and decrement the suffix starting at index 𝑟+1r+1 by 𝑘k, i.e perform the operations 𝑎[𝑙]+=𝑘a[l]+=k and 𝑎[𝑟+1]−=𝑘a[r+1]−=k for each query and then take prefix sum of the array as before. A visual summary:



**Similar problems**: [A. Greg and Array](https://codeforces.com/contest/295/problem/A), [B. Karen and Coffee](https://codeforces.com/contest/816/problem/B), [Maximum sum across all permutations](https://leetcode.com/contest/biweekly-contest-35/problems/maximum-sum-obtained-of-any-permutation/) ([my old edi](https://youtu.be/slTcWHIUfUE)) (similar: [C. Little Girl and maximum sum](https://codeforces.com/contest/276/problem/C)), [D2. Encrypting Messages](https://codeforces.com/contest/177/problem/D2)

# 1D range updates for adding an AP to the range

**Motivating problem**: [Angry Cyborg](https://www.codechef.com/BYTR20B/problems/AGCY)

**Problem Summary**: A small variation to the previous problem. This time we need to add 11 to 𝑙l, 22 to 𝑙+1l+1, and so on. In general, we need to add 𝑖+1i+1 to 𝑎[𝑙+𝑖]a[l+i] for each query.

**Approach**: How possibly we could add increasing values to the range? Just incrementing the first index by something won't help because while taking prefix sum, the same constant gets propagated for the whole range.

As before, how to increment a suffix? If we try to reverse-engineer the thought process, what array, after taking its prefix sum could give us an increasing sequence? For example, if we wish to update the range [2…5][2…5] in the array 𝑎=[0,0,0,0,0,0]a=[0,0,0,0,0,0] to get the array [0,0,1,2,3,4][0,0,1,2,3,4], what array would it have been before taking the prefix sum? It would have been 𝑎=[0,0,1,1,1,1]a=[0,0,1,1,1,1]. Performing 𝑎[𝑖]+=𝑎[𝑖−1]a[i]+=a[i−1] on this array will give us the desired array. Now how to get an array with some suffix with the value 11? Easy, just use the suffix update from section 1!

Now let's think about a general range [𝑙…𝑟][l…r]. To do increasing range updates from this section, the range [𝑙…𝑟][l…r] needs to be all 11s, like [0,0,0,1,…1,0,0][0,0,0,1,…1,0,0]. Let's think with the help of an example:

Say 𝑎=[0,0,0,0,0,0,0,0,0]a=[0,0,0,0,0,0,0,0,0] and we need to update the range [3…6][3…6] to get [0,0,0,1,2,3,4,0,0][0,0,0,1,2,3,4,0,0]. Using section 1, we can get the array 𝑎=[0,0,0,1,1,1,1,0,0]a=[0,0,0,1,1,1,1,0,0]. Try taking prefix sum over this and we get the array 𝑎=[0,0,0,1,2,3,4,4,4]a=[0,0,0,1,2,3,4,4,4]. As you can see, the last 2 indices too got incremented by 44. We can decrement the index 77 by 44 in order for the 44 to avoid being propagated towards the end of the array. Formally, perform the operations 𝑎[𝑟+1]−=𝑟−𝑙+1a[r+1]−=r−l+1 for each query before taking the prefix sum for the whole array. So, in summary, we take prefix sum twice, first when we want to get the arrays of 11s, then we do the 𝑎[𝑟+1]a[r+1] subtraction for each query and then take prefix sum again.

**Code snippet**

**Similar problems**: To be added from comments

# DP + 1D range update

**Motivating problem**: [Leaping Tak](https://atcoder.jp/contests/abc179/tasks/abc179_d)

**Problem Summary**: Find the number of ways to reach cell 𝑁N from cell 11, by making a jump of 𝑑d, where 𝑑d is any value that belongs to the union of the given ranges.

**Approach**: Let's discuss the brute force first, i.e. without range update optimisation. We define 𝑑𝑝[𝑖]dp[i] to be the number of ways to reach cell 𝑖i. Finally, we want 𝑑𝑝[𝑛]dp[n]. If you're a little familiar with DP problems, it is easy to come up with the following recurrence relation(let's ignore the modulo for now for simplicity):

𝑑𝑝[𝑖+𝑑]+=𝑑𝑝[𝑖],∀𝑑∈𝑆dp[i+d]+=dp[i],∀d∈S

**TLE**

You can notice that the above code is just doing range increments for several ranges with the value 𝑑𝑝[𝑖]dp[i], so we can use what we have learnt till now.

**~AC**

**Note** that we must take the prefix sum continuously rather than towards the end because, for 𝑖i, 𝑑𝑝[𝑖]dp[i] must be the true value since we're adding it in the range update. Also, don't forget taking modulus if you're submitting for an AC.

**Similar problems**: To be added from comments

# 2D range update

https://codeforces.com/blog/entry/86420#:~:text=Nevertheless%2C%20a%20visual%20summary%3A-,GIF,-Code%20snippet

**Motivating problem**: [Cherry and Bits](https://www.codechef.com/CENS2020/problems/CENS20A)

**(Modified) Problem Summary**: We are given a 2D matrix with multiple queries. Each query gives us a rectangle which we need to increment by a constant (the bit will just depend on whether the sum is even or odd). This is same as the 1st section, except for the 2D case

**Approach**:

The [editorial](https://discuss.codechef.com/t/cens20a-editorial/75404) for this problem is nice, it perhaps explains in a better way than I could here. You can read about the approach from there. Nevertheless, a visual summary:

**Similar problems**: To be added from comments

[**UPD**: New section]

# Range update with coordinate compression

**Motivating problem**: [Snuke prime](https://atcoder.jp/contests/abc188/tasks/abc188_d)

**Problem summary**: Range update similar to section 1, but with large ranges, i.e 𝑙,𝑟≤109l,r≤109

**Approach**: For a moment, forget the large constraints of 𝑙l and 𝑟r. How would you solve the problem when both are ≤106≤106? Easy, just create a difference array representing cost without prime membership, of size 106106, range update using the given pairs, take prefix sum, then iterate over each day 𝑖i and take the sum of the minimum of (𝑐𝑜𝑠𝑡𝑖,𝐶costi,C)

**Code (smaller constraints)**

We notice that there are at most 𝑁N pairs (~105105). So we can [coordinate-compress](https://codeforces.com/blog/entry/23180) the coordinates of the pairs and then do range update similar to section 1 on the compressed points. The [editorial](https://atcoder.jp/contests/abc188/editorial/553) is very well-written and detailed but in short: we notice that only points where meaningful changes take place are 𝑎𝑖ai and 𝑏𝑖+1bi+1, similar to 𝑙l and 𝑟+1r+1. We put them in a set and then assign an id to each point according to its order in the set (coordinate compression). Now we range update an array corresponding to these compressed points. Drawing analogy to section 1, whenever you want to do range update from 𝑎𝑖ai to 𝑏𝑖bi, use the `id`s of those points instead. It's not very hard to recover the original distance between the uncompressed points.

**Code**

It is also possible to shorten this code by directly using a map. i.e building a difference map instead of a difference array. The key will be the actual uncompressed point and its value will be the value corresponding to the difference array value as above. The difference between 2 keys will be the actual distance between the points.

**Using map**

Feel free to suggest problems in the comments and I'll add them to the relevant section.

UPD: Thanks for the appreciation/feedback in comments and contribution!

**UPD**: Check out [this](https://codeforces.com/blog/entry/88474) cool blog by [peltorator](https://codeforces.com/profile/peltorator "Grandmaster peltorator") on an advanced version of the same topic. He has added a bunch of generalizations for the updates. And just [like me](https://www.youtube.com/channel/UCvNmvZRPkkmkAtAF_mo53tw), [he makes](https://www.youtube.com/channel/UCuKxctbPIMh7yczEJ2KqAxg) helpful visualizations using manim too!