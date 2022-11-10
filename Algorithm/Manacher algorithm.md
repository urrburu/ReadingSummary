https://www.geeksforgeeks.org/manachers-algorithm-linear-time-longest-palindromic-substring-part-1/

# Manacher’s Algorithm – Linear Time Longest Palindromic Substring – Part 1
# 매네체 알고리즘  - 선형시간 가장 긴 팰린드롬 서브스트링 알고리즘
-   Difficulty Level : Hard
-   Last Updated : 11 Feb, 2021


Given a string, find the longest substring which is palindrome. 
스트링이 주어졌을 때, 가장 긴 팰린드롬 서브스트링을 찾아라.
-   if the given string is “forgeeksskeegfor”, the output should be “geeksskeeg”
-   if the given string is “abaaba”, the output should be “abaaba”
-   if the given string is “abababa”, the output should be “abababa”
-   if the given string is “abcbabcbabcba”, the output should be “abcbabcbabcba”

We have already discussed Naïve [O(n3)] and quadratic [O(n2)] approaches at [Set 1](https://www.geeksforgeeks.org/longest-palindrome-substring-set-1/) and [Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/).
우리는 이미 N의 2승, 3승에 대해서 팰린드롬 접근을 했었다. 
In this article, we will talk about [Manacher’s algorithm](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher.27s_algorithm) which finds Longest Palindromic Substring in linear time.   
이 글에서 우리는 매네체 알고리즘에 대해서 이야기할 것이다. 선형시간에 가장 긴 서브 스트링 팰린드롬을 찾는 알고리즘이 바로 매네체 알고리즘이다. 
One way ([Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/)) to find a palindrome is to start from the center of the string and compare characters in both directions one by one. If corresponding characters on both sides (left and right of the center) match, then they will make a palindrome.
팰린드롬을 찾는 하나의 방법은 스트링의 가운데에서 시작해 두 방향으로 글자를 비교해가면서 찾는 것이다. 만약 양쪽의 문자가 일치한다면, 이것들은 팰린드롬을 만들 것이다.
Let’s consider string “abababa”.   
abababa 라는 문자열을 생각해보자
Here center of the string is 4th character (with index 3) b. If we match characters in left and right of the center, all characters match and so string “abababa” is a palindrome.   
문자열의 가운데는 4번째 문자이면서 인덱스는 3인 b이다. 만약 우리가 왼쪽과 오른쪽에 있는 문자열을 일치시킨다면, 모든 캐릭터들은 맞아 떨어지고, abababa는 팰린드롬이 된다. 

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp1.jpg)

Here center position is not only the actual string character position but it could be the position between two characters also. 
여기 중심 포지션은 실제 스트링 문자위치 뿐만이 아니고 이것은 두 문사 사이의 어떤 위치든지 다 될 수 있다.
Consider string “abaaba” of even length. This string is palindrome around the position between 3rd and 4th characters a and a respectively.   
짝수길이의 "abaaba"문자열을 생각해보자. 이 문자열은 3번째와 4번째 문자 a와 a 사이의 위치 주변으로 만들어지는 회문이다.

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp2.jpg)

To find Longest Palindromic Substring of a string of length N, one way is take each possible 2*N + 1 centers (the N character positions, N-1 between two character positions and 2 positions at left and right ends), do the character match in both left and right directions at each 2*N+ 1 centers and keep track of LPS. This approach takes O(N^2) time and that’s what we are doing in [Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/). 
가장 긴 팰린드롬인 길이 N인 서브스트링을 찾기 위해, 가능한 각 2*N + 1 센터(N 문자 위치, 두 문자 위치와 왼쪽과 오른쪽 끝의 두 위치 사이의 N-1)를 취하고 각 2*N + 1 센터에서 왼쪽과 오른쪽 방향으로 문자를 일치시키고 가장 긴 팰린드롬 서브스트링을 찾는 한 가지 방법이 있다. 이 접근 방식에는 O(N^2) 시간이 소요되며, [Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/)에서 그렇게 하고 있습니다.

Let’s consider two strings “abababa” and “abaaba” as shown below:  

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp3.jpg)  
![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp4.jpg)

In these two strings, left and right side of the center positions (position 7 in 1st string and position 6 in 2nd string) are symmetric. Why? Because the whole string is palindrome around the center position.   
두 문자열 안에서, 가운데포지션에서 왼쪽과 오른쪽 방향은 비슷하다. 왜냐? 왜냐하면 전체 문자열이 팰린드롬이기 때문이다. 가운데포지션을 중심으로 한.
If we need to calculate Longest Palindromic Substring at each 2*N+1 positions from left to right, then palindrome’s symmetric property could help to avoid some of the unnecessary computations (i.e. character comparison).

If there is a palindrome of some length L centered at any position P, then we may not need to compare all characters in left and right side at position P+1. We already calculated LPS at positions before P and they can help to avoid some of the comparisons after position P.   
This use of information from previous positions at a later point of time makes the Manacher’s algorithm linear. In [Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/), there is no reuse of previous information and so that is quadratic. 

만약 우리가 왼쪽에서 오른쪽으로 각 2*N+1 위치에서 가장 긴 회문인 부분문자열을 계산해야 한다면, 회문의 대칭 특성은 불필요한 계산(즉, 문자 비교)의 일부를 피하는 데 도움이 될 수 있다.  
  
만약 어떤 위치 P에 집중된 어떤 길이 L의 회문이 있다면, 우리는 위치 P+1에서 왼쪽과 오른쪽의 모든 문자를 비교할 필요가 없을 수도 있다. 우리는 이미 P 이전의 위치에서 가장 긴 회문인 부분문자열을 계산했고, 그것들은 P 위치 이후의 일부 비교를 피하는 데 도움이 될 수 있다.   
이후 시점에서 이전 위치의 정보를 사용하는 것은 매네체 알고리즘을 선형으로 만든다. [Set 2](https://www.geeksforgeeks.org/longest-palindromic-substring-set-2/),에서는 이전 정보를 재사용하지 않으므로 2차적입니다.

Manacher’s algorithm is probably considered complex to understand, so here we will discuss it in as detailed way as we can. Some of it’s portions may require multiple reading to understand it properly. 
매네체 알고리즘은 아마도 이해하기 힘들수도 있다. 그래서 여기 우리는 우리가 가능한한 자세한 방식으로 설명할 것이다. 이 일부분은 여러번 읽어야할 수 있다. 

Let’s look at string “abababa”. In 3rd figure above, 15 center positions are shown. We need to calculate length of longest palindromic string at each of these positions. 
abababa 라는 문자열을 생각해보자 위의 3번째 그림에서는 15개의 중심 위치가 가능하다. 우리는 각각의 위치들에서 가장 긴 팰린드롬의 길이를 계산할 필요가 있습니다. 

-   At position 0, there is no LPS at all (no character on left side to compare), so length of LPS will be 0.
-   At position 1, LPS is a, so length of LPS will be 1.
-   At position 2, there is no LPS at all (left and right characters a and b don’t match), so length of LPS will be 0.
-   At position 3, LPS is aba, so length of LPS will be 3.
-   At position 4, there is no LPS at all (left and right characters b and a don’t match), so length of LPS will be 0.
-   At position 5, LPS is ababa, so length of LPS will be 5.

…… and so on 

We store all these palindromic lengths in an array, say L. Then string S and LPS Length L look like below:  

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp5.jpg)

Similarly, LPS Length L of string “abaaba” will look like: 

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp6.jpg)

In LPS Array L: 

-   LPS length value at odd positions (the actual character positions) will be odd and greater than or equal to 1 (1 will come from the center character itself if nothing else matches in left and right side of it)
-   LPS length value at even positions (the positions between two characters, extreme left and right positions) will be even and greater than or equal to 0 (0 will come when there is no match in left and right side)

배열 L에서 가장 긴 회문인 부분문자열은:   
  
- 홀수 위치(실제 문자 위치)의 LPS 길이 값은 홀수이고 1 이상입니다(1은 왼쪽과 오른쪽에서 일치하는 것이 없는 경우 중앙 문자 자체에서 나옵니다).  
- 짝수 위치(두 문자 사이의 위치, 왼쪽과 오른쪽 끝 위치)에서 LPS 길이 값은 짝수이고 0 이상입니다(왼쪽과 오른쪽이 일치하지 않을 때 0이 나타남).  

**Position and index for the string are two different things here. For a given string S of length N, indexes will be from 0 to N-1 (total N indexes) and positions will be from 0 to 2*N (total 2*N+1 positions).** 

**여기서 문자열의 위치와 인덱스는 서로 다릅니다. 길이 N의 주어진 문자열 S에 대해 인덱스는 0에서 N-1(총 N 인덱스)이고 위치는 0에서 2*N(총 2*N+1 위치)입니다.**

LPS length value can be interpreted in two ways, one in terms of index and second in terms of position. LPS value d at position I (L[i] = d) tells that: 

가장 긴 회문 부분문자열의 길이값은 두가지 방법으로 번역될 수 있다. 하나는 인덱스의 의미로 하나는 포지션의 의미로 포지션 L의 LPS 값 d는 이런 것들을 말해준다 :

-   Substring from position i-d to i+d is a palindrome of length d (in terms of position)
-   Substring from index (i-d)/2 to [(i+d)/2 – 1] is a palindrome of length d (in terms of index)

e.g. in string “abaaba”, L[3] = 3 means substring from position 0 (3-3) to 6 (3+3) is a palindrome which is “aba” of length 3, it also means that substring from index 0 [(3-3)/2] to 2 [(3+3)/2 – 1] is a palindrome which is “aba” of length 3.  

![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltp7.jpg)

Now the main task is to compute LPS array efficiently. Once this array is computed, LPS of string S will be centered at position with maximum LPS length value.   
We will see it in [Part 2](https://www.geeksforgeeks.org/manachers-algorithm-linear-time-longest-palindromic-substring-part-2/). 

This article is contributed by **Anurag Singh**. Please write comments if you find anything incorrect, or you want to share more information about the topic discussed above.

In [Manacher’s Algorithm – Part 1](https://www.geeksforgeeks.org/manachers-algorithm-linear-time-longest-palindromic-substring-part-1/), we gone through some of the basics and LPS length array.  
Here we will see how to calculate LPS length array efficiently.

To calculate LPS array efficiently, we need to understand how LPS length for any position may relate to LPS length value of any previous already calculated position.  
For string “abaaba”, we see following:

[![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp1.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/ltlp1.jpg)

If we look around position 3:

-   LPS length value at position 2 and position 4 are same
-   LPS length value at position 1 and position 5 are same

We calculate LPS length values from left to right starting from position 0, so we can see if we already know LPS length values at positions 1, 2 and 3 already then we may not need to calculate LPS length at positions 4 and 5 because they are equal to LPS length values at corresponding positions on left side of position 3.

If we look around position 6:

-   LPS length value at position 5 and position 7 are same
-   LPS length value at position 4 and position 8 are same

…………. and so on.  
If we already know LPS length values at positions 1, 2, 3, 4, 5 and 6 already then we may not need to calculate LPS length at positions 7, 8, 9, 10 and 11 because they are equal to LPS length values at corresponding positions on left side of position 6.  
For string “abababa”, we see following:

[![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp2.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/ltlp2.jpg)

If we already know LPS length values at positions 1, 2, 3, 4, 5, 6 and 7 already then we may not need to calculate LPS length at positions 8, 9, 10, 11, 12 and 13 because they are equal to LPS length values at corresponding positions on left side of position 7.

Can you see why LPS length values are symmetric around positions 3, 6, 9 in string “abaaba”? That’s because there is a palindromic substring around these positions. Same is the case in string “abababa” around position 7.  
Is it always true that LPS length values around at palindromic center position are always symmetric (same)?  
Answer is NO.  
Look at positions 3 and 11 in string “abababa”. Both positions have LPS length 3. Immediate left and right positions are symmetric (with value 0), but not the next one. Positions 1 and 5 (around position 3) are not symmetric. Similarly, positions 9 and 13 (around position 11) are not symmetric.

At this point, we can see that if there is a palindrome in a string centered at some position, then LPS length values around the center position may or may not be symmetric depending on some situation. If we can identify the situation when left and right positions WILL BE SYMMETRIC around the center position, we NEED NOT calculate LPS length of the right position because it will be exactly same as LPS value of corresponding position on the left side which is already known. And this fact where we are avoiding LPS length computation at few positions makes Manacher’s Algorithm linear.

In situations when left and right positions WILL NOT BE SYMMETRIC around the center position, we compare characters in left and right side to find palindrome, but here also algorithm tries to avoid certain no of comparisons. We will see all these scenarios soon.

Let’s introduce few terms to proceed further:  
[![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp3.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/ltlp3.jpg)

-   **centerPosition** – This is the position for which LPS length is calculated and let’s say LPS length at centerPosition is d (i.e. L[centerPosition] = d)
-   **centerRightPosition** – This is the position which is right to the centerPosition and d position away from centerPosition (i.e. **centerRightPosition = centerPosition + d**)
-   **centerLeftPosition** – This is the position which is left to the centerPosition and d position away from centerPosition (i.e. **centerLeftPosition = centerPosition – d**)
-   **currentRightPosition** – This is the position which is right of the centerPosition for which LPS length is not yet known and has to be calculated
-   **currentLeftPosition** – This is the position on the left side of centerPosition which corresponds to the currentRightPosition  
    **centerPosition – currentLeftPosition = currentRightPosition – centerPosition**  
    **currentLeftPosition = 2* centerPosition – currentRightPosition**
-   **i-left palindrome** – The palindrome i positions left of centerPosition, i.e. at currentLeftPosition
-   **i-right palindrome** – The palindrome i positions right of centerPosition, i.e. at currentRightPosition
-   **center palindrome** – The palindrome at centerPosition

When we are at centerPosition for which LPS length is known, then we also know LPS length of all positions smaller than centerPosition. Let’s say LPS length at centerPosition is d, i.e.  
L[centerPosition] = d

It means that substring between positions “centerPosition-d” to “centerPosition+d” is a palindrom.  
Now we proceed further to calculate LPS length of positions greater than centerPosition.  
Let’s say we are at currentRightPosition ( > centerPosition) where we need to find LPS length.  
For this we look at LPS length of currentLeftPosition which is already calculated.

If LPS length of currentLeftPosition is less than “centerRightPosition – currentRightPosition”, then LPS length of currentRightPosition will be equal to LPS length of currentLeftPosition. So  
L[currentRightPosition] = L[currentLeftPosition] if L[currentLeftPosition] < centerRightPosition – currentRightPosition. This is **Case 1**.

Let’s consider below scenario for string “abababa”:
  [![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp4.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/ltlp4.jpg)

We have calculated LPS length up-to position 7 where L[7] = 7, if we consider position 7 as centerPosition, then centerLeftPosition will be 0 and centerRightPosition will be 14.  
Now we need to calculate LPS length of other positions on the right of centerPosition.

For currentRightPosition = 8, currentLeftPosition is 6 and L[currentLeftPosition] = 0  
Also centerRightPosition – currentRightPosition = 14 – 8 = 6  
Case 1 applies here and so L[currentRightPosition] = L[8] = 0  
Case 1 applies to positions 10 and 12, so,  
L[10] = L[4] = 0  
L[12] = L[2] = 0

If we look at position 9, then:  
currentRightPosition = 9  
currentLeftPosition = 2* centerPosition – currentRightPosition = 2*7 – 9 = 5  
centerRightPosition – currentRightPosition = 14 – 9 = 5

Here L[currentLeftPosition] = centerRightPosition – currentRightPosition, so Case 1 doesn’t apply here. Also note that centerRightPosition is the extreme end position of the string. That means center palindrome is suffix of input string. In that case, L[currentRightPosition] = L[currentLeftPosition]. This is **Case 2**.

Case 2 applies to positions 9, 11, 13 and 14, so:  
L[9] = L[5] = 5  
L[11] = L[3] = 3  
L[13] = L[1] = 1  
L[14] = L[0] = 0

What is really happening in Case 1 and Case 2? This is just utilizing the palindromic symmetric property and without any character match, it is finding LPS length of new positions.

When a bigger length palindrome contains a smaller length palindrome centered at left side of it’s own center, then based on symmetric property, there will be another same smaller palindrome centered on the right of bigger palindrome center. If left side smaller palindrome is not prefix of bigger palindrome, then **Case 1** applies and if it is a prefix AND bigger palindrome is suffix of the input string itself, then **Case 2** applies.

_The longest palindrome i places to the right of the current center (the i-right palindrome) is as long as the longest palindrome i places to the left of the current center (the i-left palindrome) if the i-left palindrome is completely contained in the longest palindrome around the current center (the center palindrome) and the i-left palindrome is not a prefix of the center palindrome (**Case 1**) or (i.e. when i-left palindrome is a prefix of center palindrome) if the center palindrome is a suffix of the entire string (**Case 2**)._

In Case 1 and Case 2, i-right palindrome can’t expand more than corresponding i-left palindrome (can you visualize why it can’t expand more?), and so LPS length of i-right palindrome is exactly same as LPS length of i-left palindrome.

Here both i-left and i-right palindromes are completely contained in center palindrome (i.e. L[currentLeftPosition] <= centerRightPosition – currentRightPosition)  
Now if i-left palindrome is not a prefix of center palindrome (L[currentLeftPosition] < centerRightPosition – currentRightPosition), that means that i-left palindrome was not able to expand up-to position centerLeftPosition.

If we look at following with centerPosition = 11, then
![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp5.jpg)

centerLeftPosition would be 11 – 9 = 2, and centerRightPosition would be 11 + 9 = 20  
If we take currentRightPosition = 15, it’s currentLeftPosition is 7. Case 1 applies here and so L[15] = 3. i-left palindrome at position 7 is “bab” which is completely contained in center palindrome at position 11 (which is “dbabcbabd”). We can see that i-right palindrome (at position 15) can’t expand more than i-left palindrome (at position 7).

If there was a possibility of expansion, i-left palindrome could have expanded itself more already. But there is no such possibility as i-left palindrome is prefix of center palindrome. So due to symmetry property, i-right palindrome will be exactly same as i-left palindrome and it can’t expand more. This makes L[currentRightPosition] = L[currentLeftPosition] in Case 1.

Now if we consider centerPosition = 19, then centerLeftPosition = 12 and centerRightPosition = 26  
If we take currentRightPosition = 23, it’s currentLeftPosition is 15. Case 2 applies here and so L[23] = 3. i-left palindrome at position 15 is “bab” which is completely contained in center palindrome at position 19 (which is “babdbab”). In Case 2, where i-left palindrome is prefix of center palindrome, i-right palindrome can’t expand more than length of i-left palindrome because center palindrome is suffix of input string so there are no more character left to compare and expand. This makes L[currentRightPosition] = L[currentLeftPosition] in Case 2.

**Case 1:** L[currentRightPosition] = L[currentLeftPosition] applies when:

-   i-left palindrome is completely contained in center palindrome
-   i-left palindrome is NOT a prefix of center palindrome

Both above conditions are satisfied when  
L[currentLeftPosition] < centerRightPosition – currentRightPosition

**Case 2:** L[currentRightPosition] = L[currentLeftPosition] applies when:

-   i-left palindrome is prefix of center palindrome (means completely contained also)
-   center palindrome is suffix of input string

Above conditions are satisfied when  
L[currentLeftPosition] = centerRightPosition – currentRightPosition (For 1st condition) AND  
centerRightPosition = 2*N where N is input string length N (For 2nd condition).

**Case 3:** L[currentRightPosition] > = L[currentLeftPosition] applies when:

-   i-left palindrome is prefix of center palindrome (and so i-left palindrome is completely contained in center palindrome)
-   center palindrome is NOT suffix of input string

Above conditions are satisfied when  
L[currentLeftPosition] = centerRightPosition – currentRightPosition (For 1st condition) AND  
centerRightPosition < 2*N where N is input string length N (For 2nd condition).  
In this case, there is a possibility of i-right palindrome expansion and so length of i-right palindrome is at least as long as length of i-left palindrome.

**Case 4:** L[currentRightPosition] > = centerRightPosition – currentRightPosition applies when:

-   i-left palindrome is NOT completely contained in center palindrome

Above condition is satisfied when  
L[currentLeftPosition] > centerRightPosition – currentRightPosition  
In this case, length of i-right palindrome is at least as long (centerRightPosition – currentRightPosition) and there is a possibility of i-right palindrome expansion.

In following figure,

[![Manacher's Algorithm – Linear Time Longest Palindromic Substring](https://media.geeksforgeeks.org/wp-content/uploads/ltlp6.jpg)](https://media.geeksforgeeks.org/wp-content/uploads/ltlp6.jpg)

If we take center position 7, then Case 3 applies at currentRightPosition 11 because i-left palindrome at currentLeftPosition 3 is a prefix of center palindrome and i-right palindrome is not suffix of input string, so here L[11] = 9, which is greater than i-left palindrome length L[3] = 3. In the case, it is guaranteed that L[11] will be at least 3, and so in implementation, we 1st set L[11] = 3 and then we try to expand it by comparing characters in left and right side starting from distance 4 (As up-to distance 3, it is already known that characters will match).

If we take center position 11, then Case 4 applies at currentRightPosition 15 because L[currentLeftPosition] = L[7] = 7 > centerRightPosition – currentRightPosition = 20 – 15 = 5. In the case, it is guaranteed that L[15] will be at least 5, and so in implementation, we 1st set L[15] = 5 and then we try to expand it by comparing characters in left and right side starting from distance 5 (As up-to distance 5, it is already known that characters will match).

Now one point left to discuss is, when we work at one center position and compute LPS lengths for different rightPositions, how to know that what would be next center position. We change centerPosition to currentRightPosition if palindrome centered at currentRightPosition expands beyond centerRightPosition.

Here we have seen four different cases on how LPS length of a position will depend on a previous position’s LPS length.  
In [Part 3](https://www.geeksforgeeks.org/manachers-algorithm-linear-time-longest-palindromic-substring-part-3-2/), we have discussed code implementation of it and also we have looked at these four cases in a different way and implement that too.

This article is contributed by **Anurag Singh**. Please write comments if you find anything incorrect, or you want to share more information about the topic discussed above