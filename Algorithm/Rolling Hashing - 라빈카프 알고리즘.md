![](https://blog.kakaocdn.net/dn/cgfpxr/btqQn0QPe8F/nmpbD22Gm9BKInzqk5OAn1/img.png)

출처 : https://dzone.com/articles/algorithm-week-rabin-karp

라빈카프 알고리즘은 **해싱(Hashing)**을 사용해서 문자열에서 특정 패턴과 일치하는지 찾아주는 알고리즘입니다.

기본적인 아이디어는 **(1)**비교할 문자열과 패턴을 Hash function을 통해 해시값으로 변환하고, **(2)**해시값의 비교를 통해서 문자열이 일치하는지 확인하는데, 일치하지 않으면 다음 문자열로 넘어가고, 일치한다면 해당 문자열과 패턴의 1:1 매칭을 통해서 최종적으로 일치하는지 확인합니다.

즉, 해시값이 일치하면 문자열이 같다라고 판단할 수 있는데, 이는 해시 충돌(hash collison)이 없는 경우에 해당하긴 하지만 대부분 유효하다고 볼 수 있으며, 혹시나 해시값이 같지만 다른 문자열인 경우를 대비해서 해시값이 일치하는 경우에는 일대일 매칭으로 일치하는지 확인하는 과정을 거치면 됩니다.

그리고 라빈카프에서는 패턴과 일치하지 않는 부분을 빠르게 걸러주기 위해서 **rolling hash**를 hash function으로 사용합니다.

**Rolling hash**는 sliding window와 같이 문자열을 훑으면서 Hash 값을 구하는데 유용한 것으로, 주어진 문자열을 처음부터 끝까지 패턴과 비교하면서 해시값을 구하는데, 중복되는 부분의 해시값은 그대로 두고 업데이트되는 부분만 해시값을 계산해주어서 중복되는 연산을 줄여줍니다.

![](https://blog.kakaocdn.net/dn/dDwHsP/btqQwm59GVZ/uCvXmDk3eXA89X7pD7kcKK/img.png)

출처 : https://medium.com/algorithm-and-datastructure/rolling-hash-a-constant-time-window-search-f8af6ee12d3f

위 이미지에서 패턴의 사이즈가 4(window size = 4)일 때,

처음 ABCD의 해시값을 구해서 비교한 다음에 다음 문자인 BCDB로 이동할 때, BCDB의 해시값을 처음부터 구하는 것이 아니라, ABCD의 해시값에서 제거되는 A에 해당하는 해시값만 빼주고, 추가되는 B에 해당하는 해시값을 더해주어서 BCDB의 해시값을 구하게 되는 것입니다.

문자열 탐색이 sliding window처럼 고정된 window가 문자열을 훑고 지나가기 때문에 sliding window처럼 보입니다.

물론 hash function은 다양하기 때문에, 다른 hash function을 사용해도 무방합니다.

#### **Rabin fingerprint**

라빈 카프 알고리즘에서는 Rabin fingerprint라는 rolling hash function을 사용합니다.

![](https://blog.kakaocdn.net/dn/blKGCx/btqQmO4kGm5/F6MyT4HNKvllvxIBJUB2C0/img.png)

위 식과 같은 형태를 띄고 있는데, m은 문자열의 각 자리에 해당하는 문자이며 보통 아스키코드를 많이 사용합니다. x는 무엇이든 상관없지만, 보통 2를 사용합니다.

따라서 'abr'라는 문자열의 해시값을 구하면 다음과 같습니다.(a의 ASCII : 97, b의 ASCII : 98, r의 ASCII : 114)

97 \times 2^2 + 98 \times 2^1 + 114 \times 2^0 = 69897×22+98×21+114×20=698
(m_0m0​가 가장 오른쪽의 문자이므로 순서에 주의)
그렇다면 비교할 문자열이 'bra'로 변경되면, 이 문자열의 해시값은 어떻게 될까요? 

위 식을 사용해서 구하면 다음과 같습니다.
98 \times 2^2 + 114 \times 2^1 + 97 \times 2^0 = 71798×22+114×21+97×20=717

하지만, 실제로 이렇게 처음부터 구하지 않습니다. 

우리는 이전 문자열 'abr'에서 'bra'로 변경되었을 때, 'br'이라는 문자가 중복된다는 것을 알고 있습니다. 따라서, rolling hash의 특징을 사용해서 중복된 이전 문자열에서 제거되는 'a'에 해당하는 해시값만 빼주고 2를 곱해준 다음, 추가되는 문자열 'a'의 해시값을 더해주면 됩니다.

기존 문자열 'abr'의 해시값은 698이고, 제거되는 'a'의 해시값은 97 \times 2^2 = 38897×22=388 입니다.

따라서, **698 - 388 = 310**이 되며, b의 위치가 처음 위치가 되므로 2를 곱해주면 **310 x 2 = 620**이 됩니다.

마지막으로 추가되는 'a'의 해시값을 더해주면 **620 + 97 = 717**이 우리가 구하는 'bra'의 해시값이 됩니다.

(717 - (97 \times 2^2)) \times 2^2 + 97 \times 2^0 = 717(717−(97×22))×22+97×20=717

#### **예시**

다음의 문자열과 패턴을 예시로 라빈 카프의 과정을 살펴보도록 하겠습니다.

S = "ABC ABCDAB ABCDABCDABDE"

P = "ABCDABD"

![](https://blog.kakaocdn.net/dn/clve3i/btqQu4rfHXv/BBorCWaGziYSRMCcTS2BmK/img.png)

S 해시값 = 65 \times 2^6 + 66 \times 2^5 + 67 \times 2^4 + 32 \times 2^3 + 65 \times 2^2 + 66 \times 2^1 + 67 \times 2^065×26+66×25+67×24+32×23+65×22+66×21+67×20 = 8059

P 해시값 = 65 \times 2^6 + 66 \times 2^5 + 67 \times 2^4 + 68 \times 2^3 + 65 \times 2^2 + 66 \times 2^1 + 68 \times 2^065×26+66×25+67×24+68×23+65×22+66×21+68×20 = 8348

두 문자열의 해시값이 다르므로, 다음 단계로 넘어갑니다.

![](https://blog.kakaocdn.net/dn/drRLhP/btqQt6bWZJP/mSDtQ8oUYvprTyqkjZJfLk/img.png)

다음 S의 문자열 해시값은 이전 해시값에서 가장 앞쪽의 A에 해당하는 해시값을 빼주고, x2를 한뒤에 가장 뒤쪽의 추가되는 D의 해시값을 더해줍니다. 패턴은 동일하므로 같은 값이 유지됩니다.

S 해시값 = (이전 S 해시값(8059) - 65 \times 2^665×26) X 2 + 68 \times 2^068×20 = 7866
P 해시값 = 8348

해시값이 다르므로 이번에도 넘어갑니다.

![](https://blog.kakaocdn.net/dn/bJv5eG/btqQBGDjoCo/J50rmgZ6wP4vJEiK197Pd1/img.png)

S 해시값 = 7349

P 해시값 = 8348

다르므로 넘어갑니다.

이런 식으로 문자열을 훑으면서 해시값들을 비교하면 됩니다.

그러다가 같은 문자열을 만나게되면, 해시값이 같아지게 됩니다.

![](https://blog.kakaocdn.net/dn/uowxV/btqQBGDjzP3/XVzPAH9y5zGJGcm6XdhRk0/img.png)

S 해시값 = 8347

P 해시값 = 8348

해시값이 같기 때문에 해시 충돌이 발생하지 않는 이상, 두 문자열은 동일하다고 볼 수 있습니다. 하지만, 해시충돌이 발생할 수도 있다면, 두 문자열이 동일한지 직접 확인하면 됩니다.

#### **알고리즘 구현**

![](https://blog.kakaocdn.net/dn/urZxC/btqQptlgaP0/ceBsuiUFNxsPnFyWkJkXq0/img.png)

라빈 카프의 알고리즘은 위와 같습니다. 

패턴의 해시값은 동일하므로 알고리즘 초기에 구한 값으로 계속 사용되며, 매 for문마다 찾으려는 sub문자열의 해시값을 업데이트하고, 패턴의 해시값과 비교해서 일치하는지 확인합니다.

주어진 문자열의 길이가 n, 패턴의 길이가 m이라고 할때, for문은 총 n-m+1번 반복되기 때문에 O(n)번 반복되고, line 5의 단순비교는 O(m)의 시간이 소요되지만, 해시값이 같을 때에만 계산되므로 전체적인 평균 시간은 O(nm)보다 적게 소요됩니다. 

따라서, Hash function의 성능이 좋지 않아서 해시 충돌(hash collision)이 자주 발생한다면, 단순비교를 많이 해야하므로 최악의 경우 O(nm)의 시간이 발생할 수 있습니다. 또한, "aaaaaaaaaaaaa"에서 "aaaa"를 찾는 케이스같은 경우에도 단순비교가 매번 발생하기 때문에 O(nm)의 시간이 소요됩니다.

위에서도 언급했지만, 좋은 hash function을 사용하면, 그냥 해시값이 일치하면 같은 문자열로 판단하는 방법이 많이 사용되기는 합니다.

알고리즘을 구현하면 다음과 같습니다.

```C++
vector<int> rabin_karp(string S, string P){    
	int n = S.length();    
	int m = P.length();    
	vector<int> findAt;     
	int Shash = 0, Phash = 0, base = 1;     
	// get hash value    
	for (int i = m - 1; i >= 0; i--)    
	{        
	Shash = Shash + S[i] * base;        
	Phash = Phash + P[i] * base;               
	if (i > 0)base *= 2;    
	}     
	for (int i = 0; i < n - m + 1; i++)    {        
		if (i > 0)            
			Shash = (Shash - S[i - 1] * base) * 2 + S[m - 1 + i];         
		if (Phash == Shash)        {            
			bool finded = true;            
			for(int j = 0; j < m; j++)            
			{                
				if(S[i + j] != P[j]){                    
					finded = false;
					break;                
				}            
			}            
			if (finded){
				printf("Find pattern at index %d\n", i);
				findAt.push_back(i);            
				}        
			}    
		}     
	return findAt;
}
```

초기에 해시값(Shash, Hhash)을 구해주고, 문자열 탐색을 시작합니다.

그리고 매 루프마다 Shash값을 업데이트해주며, 두 해시값이 일치하면 실제로 두 문자가 동일한지 확인하는 작업을 수행합니다.

만약 비교할 필요가 없이 해시값만 일치하면 찾은 걸로 하겠다 하신다면, 내부 for문은 삭제하면 되겠습니다.

#### **추가 내용**

추가적인 내용은 wiki의 rabin-karp 알고리즘은 해시값을 계산할 때 모듈러 연산을 사용하고 있습니다.

만약 패턴의 문자열이 매우 길다면, int형으로는 해당 값을 담을 수가 없기 때문에 오버플로우(overflow)가 발생하게 되고, 정확한 해시값의 일치 여부를 검증하기 위해서 모듈러 연산을 사용하지만, 일반적으로 오버플로우가 발생하더라도 해시값은 동일하다고 볼 수 있기 때문에 모듈러 연산을 사용하지 않아도 정상적으로 동작하게 됩니다.

곱해주는 값을 371로 변경했을 때, 어떻게 되는지 살펴보도록 하겠습니다.

```c++
vector<int> rabin_karp(string S, string P){    
	int n = S.length();    
	int m = P.length();    
	vector<int> findAt;     
	int Shash = 0, Phash = 0, base = 1;     // get hash value    
	for (int i = m - 1; i >= 0; i--)    {        
		Shash = Shash + S[i] * base;        
		Phash = Phash + P[i] * base;                
		if (i > 0)base *= 371;    
	}     
	for (int i = 0; i < n - m + 1; i++)    {        
		if (i > 0)Shash = (Shash - S[i - 1] * base) * 371 + S[m - 1 + i];
		printf("Shash : %15d / Phash : %15d\n", Shash, Phash);        
		if (Phash == Shash){            
			printf("Find pattern at index %d\n", i);
			findAt.push_back(i);        
		}    
	}     
	return findAt;
} 
		
int main(){    
	vector<int> v;     
	string S = "ABC ABCDAB ABCDABCDABDE";    
	string P = "ABCDABD";     
	v = rabin_karp(S, P);     
	return 0;
}
```

![](https://blog.kakaocdn.net/dn/vUYYq/btqQps7S2Ay/xV3kACmRVvekJvpX47iDJ0/img.png)