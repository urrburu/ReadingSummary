백준 버블소트 문제를 풀면서 코드를 작성하면서 MergeSort에 대해서 생각해보았다. 

```C++
vector<int> Array(500005);
vector<int> Temp(500005);

int Sort(int start, int end){
	int mid = (start+end)/2;
	int left = start;
	int right = mid+1;
	int total = start;
	int Cnt =0, TotalCnt =0; //-> Swap의 횟수
	while(left<=mid && right<=end){
		if(Arr[left]<=Arr[right]){
			Temp[total++] = Array[left++];
			TotalCnt+= Cnt;
		}else{
			Temp[total++] = Array[right++];
			++Cnt;
		}
	}
	if(left<=mid){
		while(left<=mid){
		Temp[total++] = Array[first++]; TotalCnt+= Cnt;
		}
	}else{
		while(right<=end){
		Temp[total++] = Array[second++]; Cnt++;
		}
	}
	for(int i=start;i<=end;++i)Array[i] = Temp[i];
	return Cnt;
}

```

이 문제의 핵심은 머지소트의 스왑기능을 이용해서 실제 스왑이 몇번이나 일어나는지 세는 것이다. 실제로도 유용한 테크닉이라고 생각한다. 템프 벡터만큼의 공간만 확보할 수 있다면.