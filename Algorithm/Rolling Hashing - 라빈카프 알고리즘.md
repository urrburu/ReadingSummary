http://courses.csail.mit.edu/6.006/spring11/rec/rec06.pdf

라빈 카프 알고리즘. 

# Objective 
If we have text string S and pattern string P, we want to determine whether or not P is found in S, i.e. P is a substring of S
# 목적
만약 우리가 S텍스트 문자열과 패턴 문자열 P를 갖고 있다면 우리는 P를 S에서 찾을 수 있을지 없을지 결정해야만 한다. 
-> 즉 P가 S의 부분문자열인가??
# Notes on Strings 
Strings are arrays of characters. Characters however can be interpreted as integers, with their exact values depending on what type of encoding is being used (e.g. ASCII, Unicode). This means we can treat strings as arrays of integers. Finding a way to convert an array of integers into a single integer allows us to hash strings with hash functions that expect numbers as input. Since strings are arrays and not single elements, comparing two strings for equality is not as straightforward as comparing two integers for equality. To check to see if string A and string B are equal, we would have to iterate through all of A’s elements and all of B’s elements, making sure that A[i] = B[i] for all i. This means that string comparison depends on the length of the strings. Comparing two n-length strings takes O(n) time. Also, since hashing a string usually involves iterating through the string’s elements, hashing a string of length n also takes O(n) time.

# 문자열 기록
문자열들은 문자들의 배열이다. 그러나 "문자들"은 정수형으로 번역될 수 있다. 

# Method 
Say P has length L and S has length n. One way to search for P in S: 
1. Hash P to get h(P) --------- O(L) 
	P를 해쉬한다 h(P)를 얻기 위해 여기에서는 P의 길이 L만큼의 비용이 소모된다. O(L)
2. Iterate through all length L substrings of S, hashing those substrings and comparing to h(P) ----------O(nL) 
	전체 길이 L을 돌아다니면서 서브스트링 S 이 서브스트링들을 해싱하면서 비교한다. 여기에는 L에 S의 길이 n만큼을 곱한만큼의 비용이 소모된다. O(nL)

3. If a substring hash value does match h(P), do a string comparison on that substring and P, stopping if they do match and continuing if they do not. O(L) 
만약 서브스트링의 해쉬값이 h(P)랑 맞아 떨어지면, 그 서브스트링과 P의 비교를 해라 만약 그들이  일치하면 멈추고 아니라면 계속 비교해나가라 ------O(L)

This method takes O(nL) time. We can improve on this runtime by using a rolling hash. In step 2. we looked at O(n) substrings independently and took O(L) to hash them all. These substrings however have a lot of overlap. For example, looking at length 5 substrings of “algorithms”, the first two substrings are “algor” and “lgori”. Wouldn’t it be nice if we could take advantage of the fact that the two substrings share “lgor”, which takes up most of each substring, to save some computation? It turns out we can with rolling hashes.

이 방법은 O(nL)의 시간이 걸린다. 우리는 롤링해쉬를 사용해서 소모되는 시간을 줄일 수 있다. 
2단계에서 우리는 O(n) 부분문자열을 봤다. 