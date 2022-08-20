# 왕초식 : 왕 파라미터

public void update(Data data){
	Some s = getSome(data);
	data.setKey(S.getKey());
	int ret = otherDao.update(data);
	if(ret == 1 ){
		data.setReg(new Date());
		// 새로운 값이 필요해지면 여기에 세터를 하나 더 추가해서 세팅해주는 수 밖에 없다.
		anyDao.insert(data);
	}
}


## 이러한 코드는 잘못되었다!

왜와이?

-> 이러한 구조는 한 파라미터 타입을 공유하는 구조로 이루어져있다. 
-> 매서드에 Data 타입이 입력될 경우, 패러미터를 신경쓰지 않고 여기에 맞는 연산을 매서드 내부에서 대령하는 방식 : 초기에는 뭔가 코드를 덜 만들어야 할 것 같음, 변경할게 적어 보임

-> 실제로는 그렇지 않다........

### 장기적인 문제점 

각 매서드에서 어떤 값을 사용하는지 알 수 없음
-> 데이터 타입을 추적하기 어려움
-> 머리로 매서드와 필드 간 매핑 기억 필요
-> Data 타입을 다른 곳에서도 사용하면 매핑을 기억할 수 없는 문제


조금 귀찮아도 매서드에 맞는 파라미터 사용

public void update(UpdateReq req){
	Some s = getSome(req.getSomeId());
	OtherUpdate otu = OtherUpdate.builder()
									.id(req.getOtherId()).key(s.getKey())
									.builder();
	int ret = otherDao.update(otu);
	if(ret == 1 ){
		AnyData any = AnyData.builder()
								.someId(s.getId()).ip(s.getIp())
								.builder();
		anyDao.insert(any);
	}
}


당장 만들 코드는 조금 증가하는 것처럼 보이지만 앞으로 코드분석시간이 상대적으로 감소한다. 

왜와이?

각 매서드에서 어떤 값을 사용하는지 알기 쉬움 
 - 명시적인 데이터 변환으로 흐름 추적이 쉬움
 - 머리로 매서드와 필드간의 매핑기억이 필요 없음

## 정리
### 패러미터 타입 만드는데 인색하지 말 것!
 - 여러 매서드를 위한 값을 한 파라미터 타입에 우겨 넣지 말 것
 - 매서드에서 필요한 값만 패러미터로 받을 것