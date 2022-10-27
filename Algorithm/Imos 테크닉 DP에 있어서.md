이는 [[2D dp를 판단하는 요령]] 글에서 나온 마지막 방법의 정식 서술이다. 이 방법이 인상적이어서 별도로 서술하고자 한다. 

## imos법 (いもす法, imos法)

### 개요

imos법은 다음과 같은 문제에서 쓸 수 있는 간단한 아이디어이다. (조금 더 formal한 설명을 할 수도 있지만, 예제만 봐도 충분히 이해할 수 있을 것이다.)

![](data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==)

가게에 QQ명의 손님이 방문한다. 가게는 시간 00부터 TT까지 운영한다. 각각의 손님 ii에 대해 입장 시각 s_isi​와 퇴장 시각 e_iei​ (1\le s_i\lt e_i\le T)(1≤si​<ei​≤T)이 주어진다. 가게 운영 중 손님이 가장 많이 방문했을 때의 손님 수는 몇 명인가? (단, 동일 시각에 입장과 퇴장이 같이 발생한 경우, 퇴장이 먼저 이루어진다고 가정한다.)

대충 저런 식의 쿼리 여러 개가 들어오는 모양새이다. 그림으로 나타낸다면, 수직선 위에 선을 여러 개 그어서, 가장 선이 많이 겹치는 부분을 구하는 것과 같다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa47091d0-ff03-4485-ad0d-1c082fba86d1%2FUntitled.png&blockId=a320c712-6b85-4d43-9123-667474fd8543)

각 선분을 손님이 가게에 있던 시간으로 생각한다면, 시간 5에서 손님이 4명으로 가장 많다.

나이브하게 푼다면? 길이 TT짜리 배열 cnt을 초기화해두고, 각 손님마다 방문한 시간에 해당하는 배열값 cnt[s_i..e_i]에 모두 1을 더하는 방법을 생각해볼 수 있다. 그렇게 쿼리를 모두 처리하고 나면, cnt 배열의 최댓값이 답이 된다.

이렇게 하면, 쿼리 하나당 처리 시간이 O(T)O(T)이므로, 시간 복잡도는 O(TQ)O(TQ)가 된다. 더 빠르게 풀 수는 없을까?

### 입장과 퇴장만 기록

더 빠르게 풀 수 있다! imos법의 기본 아이디어는, "입장과 퇴장만 기록한다"로 요약할 수 있다. 말 그대로, 각 손님이 입장해서 퇴장하기까지 존재하는 모든 시각에 1을 더하는 것이 아니고, 입장할 때 +1을 기록하고, 퇴장할 때 -1을 기록해서, 모든 쿼리를 처리하고 나서 답을 구하기 전에 O(T)O(T)짜리 후처리 하나만 거치면 된다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F69882682-c4e4-40a4-a029-ceef711aa0ef%2FUntitled.png&blockId=e8aae683-f8ee-43d5-acde-8204cced5b6c)

+1, -1만 기록해두고, 맨 마지막에 왼쪽부터 보면서 값을 시뮬레이팅한다.

결국 맨 마지막에 +1, -1이 기록된 배열의 누적합(Prefix sum) 배열을 구하면, 원래 우리가 구하려던 값이 나오는 것이다.

쿼리 하나당 두 값밖에 갱신하지 않으므로 쿼리당 처리 시간은 O(1)O(1)이고, 후처리는 O(T)O(T)이므로, 총 시간복잡도는 O(Q+T)O(Q+T)이다.

### 누적합과의 비교

어찌 보면 지금까지의 설명으로는 1차원 배열에서 쿼리를 처리하는 발상이라는 점에서 누적합과도 비슷해 보인다. Remind 차원에서 둘의 설명을 적어보았다.

• 누적합
◦ 입력 다 받고, 전처리 한번 거치고 나면, [s,\ e][s, e]의 합을 구하는 것과 같은 쿼리를 빠르게 처리할 수 있다.
◦ 쿼리 처리 도중 값 갱신이 일어나지 않는 경우에 사용한다.
• imos법
◦ 각 쿼리가 구간 [s,\ e][s, e] 내의 값을 갱신시키는 경우, 이를 매 쿼리마다 일일히 갱신하지 않고, 시작과 끝만 기록해두었다가 마지막에 한 번의 처리로 값을 시뮬레이팅하는 방법이다.

공식 레퍼런스와도 같은 いもす님의 블로그에서는 "imos법은 누적합 알고리즘을 다차원, 다차수로 확장시킨 것"이라고 설명되어 있다.

## 2차원에서의 imos법

### 격자 보드에 사각형 그리기

1차원 배열에서의 설명까지는 아마 많이들 알고 있을지도 모르겠다. 아니면 무의식 중에 그렇게 사용하고 있었거나... (나의 경우가 그렇다 ![](data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==))

하지만 imos법의 강력함은 이제부터이다. imos법은 특성상 고차원으로의 확장이 용이하다.

2차원 배열에서의 사용을 보이기 위해, 예시 문제를 보자. 이번에는 formal하게 작성해 보았다.

![](data:image/gif;base64,R0lGODlhAQABAIAAAP///wAAACH5BAEAAAAALAAAAAABAAEAAAICRAEAOw==)

H\times WH×W 크기의 격자 보드가 있다. 각 칸의 초깃값은 모두 0이다. 다음과 같은 쿼리가 QQ개 들어온다. x1 y1 x2 y2 : y_1\le h\le y_2y1​≤h≤y2​, x_1\le w\le x_2x1​≤w≤x2​에 해당하는 모든 칸 (h,\ w)(h, w)의 값에 1을 더한다. 쿼리를 모두 처리한 뒤의 최종 보드의 상태를 출력하라.

대충 감이 오는가? 그냥 1차원 배열에서 선분이 사각형으로 바뀌었을 뿐이다.

역시 나이브를 먼저 생각해본다면, 매 쿼리가 들어올 때마다 해당 사각형 영역에 1씩 직접 다 더해주고, 마지막에 출력만 해 주는 로직을 떠올려보자. 쿼리당 O(HW)O(HW)만큼 걸리고, 총 시간복잡도는 O(QHW)O(QHW)이다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd82a8e72-2193-42a5-93b0-13ea44457c68%2FUntitled.png&blockId=884865d7-3c89-4ebf-bcaf-26ec17032589)

매 사각형(오렌지 영역)이 들어올 때마다 해당 영역의 모든 칸에 1씩 다 더해주면? 너무 느리다.

### 가로로 한번, 세로로 한번 휩쓸기

사각형으로 바뀌어도 기본적인 아이디어는 같다. "시작과 끝만 기록한다" 단 2차원에서는 차원이 늘어난 만큼, 가로 방향으로 시작과 끝, 세로 방향으로 시작과 끝 두 번 기록하고, 시뮬레이팅할 때도 가로로 한번, 세로로 한번 휩쓸면 된다. 갑자기 뭐 휩쓰는 얘기가 나오고 대체 무슨 소리냐?

먼저 얘기하자면, 휩쓸으라는 얘기는 그냥 누적합을 구하라는 얘기다. 그래도 감이 안 잡힌다면, 그림으로 된 설명을 보자. 사각형 하나를 예로 들어보자.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7f2dc7dc-d9d9-484e-92cd-e03e5a7bdd97%2FUntitled.png&blockId=b28350a7-8a76-4892-a293-b12569a76c6b)

가로로 한번 휩쓸고, 세로로 한번 휩쓸어서 우리가 원하는 사각형 값이 나오게 하려면 값을 어떻게 찍어둬야 할까?

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F815e98da-0a0d-4c3b-8ba9-b011f28d17e0%2FUntitled.png&blockId=49b031ef-2084-4414-83ed-9e5b92070d9d)

결론부터 말하면, 저렇게 값 네 개를 찍어두기만 하면, 원래 사각형 값을 시뮬레이팅할 수 있다. 실제로 저 네 개의 값을 찍어둔 채로 한번 휩쓸어보자. 우선 가로 방향으로 휩쓸면?

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0fc261f4-1d94-47be-95c8-2a3c69a9fb7c%2FUntitled.png&blockId=82b18030-8f1c-4a07-b577-b99cc51ec73a)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F4a1ca6ec-ca12-4eb4-91d5-f6494e369650%2FUntitled.png&blockId=7ba9fc5e-f842-4659-95f3-99d31e93169c)

이제 휩쓴다는 게 무엇인지 감이 잡힐지도 모르겠다. 그냥 모든 행에 대해 왼쪽부터 누적합을 구하는 것이다. 세로 방향으로도 해 보자.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffe71910e-4738-4b71-9caf-abde526a2c9e%2FUntitled.png&blockId=5bf7bc1f-2d3e-49ce-9f64-93e07c6a669d)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6bceb832-f34f-4db9-8254-b1c4ec20663e%2FUntitled.png&blockId=2eddd162-0164-4636-a21d-9c6a4f55b05e)

마찬가지로, 모든 열에 대해 위에서부터 누적합을 구하면 된다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb399810a-0e9e-48c1-9a60-4597673e1756%2FUntitled.png&blockId=5afdc87e-80a1-421f-9550-c4c7b1162f17)

위에서 아까 봤던 이 사각형에도 적용할 수 있을까? 몇 번이고 말하지만, 각 사각형들에 대해 실제로 1을 모두 찍는 게 아니고, 값 4개만 제대로 찍어두고 나중에 두 번 휩쓸면 되는 것이다.

사각형 영역은 총 3개이다. 각 사각형들에 대해 값을 찍으면 이렇게 된다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F97c8c973-afa9-4525-8c8e-bbe304986442%2FUntitled.png&blockId=7bc46440-0090-4c73-aeb7-8f2e69f14db8)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7c0e680d-8664-4685-82bf-1dc344806244%2FUntitled.png&blockId=829c4b9d-b5fd-4af9-b1fe-f4a475e06dc7)

설명의 편의상, 각 사각형 영역이 관여하는 값에 같은 색으로 동그라미 표시하였다.

이제 이렇게 찍은 값들을 휩쓸어보자. 우선 오른쪽으로 한 번!

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff85f0ec7-78dc-4e62-addd-0183b6c29d3a%2FUntitled.png&blockId=c1b27fd6-c48d-4720-9f7f-c8ad044335f6)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F77bda0c9-0eaf-463f-b96f-3263ef08559e%2FUntitled.png&blockId=b2310370-cdad-4231-8bca-b6b0c92439ba)

밑으로 한 번 휩쓸면?

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F61051fac-3052-45e8-97ba-b52e77d26014%2FUntitled.png&blockId=3b3e20ee-6021-4b35-9daf-799a6094d874)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9558268d-4a15-45b1-8d54-cbd8dbed5de2%2FUntitled.png&blockId=4f5f5bc9-4c75-4ec2-9654-6713a01a02cd)

짜잔! 원래 값을 복원할 수 있다!

## 특수 좌표계에서의 imos법

이 부분이 특히 재미있었고, 이 주제를 글로 쓰게 만든 이유이다. imos법은 그 특성상 다른 특수 좌표계에도 쉽게 적용이 가능하다.

특수 좌표계라고 하면 감이 잘 오지 않을 것이다. 정삼각형을 우선 예시로 들겠다.

삼각형에 imos법을 어떻게 적용할 수 있을까? 우선 삼각형은 2차원 배열에서 왼쪽 하단만 쓰는 식으로 쉽게 모델링할 수 있다. 몇 번이고 이야기하지만, imos법의 강력함은 영역을 '영역의 시작과 끝'으로만 일단 나타내고, 나중에 한번에 쓸어내면서 원래 값을 복원할 수 있다는 것이다.

삼각형 하나에 대해서 먼저 생각해보자.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb377244f-0810-4efb-a486-392fb5f6835f%2FUntitled.png&blockId=c48d3d47-48f4-4984-9394-fb6b07250388)

이렇게 생긴 삼각형도 사각형처럼 값만 딱 딱 찍어두고, 나중에 한 번에 복원할 수 있을까? 정답은 다음과 같다.

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F51adacf3-4327-47d8-a2d0-9c51ac893766%2FUntitled.png&blockId=52bdf58f-d261-4fa0-8f2f-1f62069407bf)

이렇게 찍어둔 뒤에 어떻게 하면 될까? 총 3번 휩쓸면 된다. 어렵지 않다!

하나, 오른쪽으로 휩쓴다!

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9e8b067a-88fe-4122-9749-8cb82b93ecf6%2FUntitled.png&blockId=81da134c-f31f-44fb-87c3-aa506ba5d7d8)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc2add86d-f1c4-44fc-a72c-3a97f3b892cd%2FUntitled.png&blockId=6609d58f-4e33-4600-8427-2a9fa5ec625a)

둘, 밑으로 휩쓴다!

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F71e0b4e2-93ac-4d4b-9ec2-c98b0de4055f%2FUntitled.png&blockId=beebdc44-3951-4ae2-b832-88ff77088acc)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F07ca9151-6873-465c-963b-d6b44ad0e89b%2FUntitled.png&blockId=ef6d7a42-7ece-452c-b919-5788fa92a24b)

여기까지는 똑같다. 셋, 대각선으로 휩쓴다!

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe71dad46-d84a-4a65-9295-2233d1c50cf5%2FUntitled.png&blockId=5a176ef4-ef1e-4e48-9a8d-e35e04b432ae)

![](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F33135b37-5369-4a7c-964a-651c7db4ba07%2FUntitled.png&blockId=f3979945-ccb4-47bc-8c36-f1293744e307)

짠! 원래 삼각형이 복원되었다. 즉, 삼각형 모양도 값 여섯 개만 찍어두면 복원할 수 있는 것이다!

삼각형에 imos법을 적용하는 문제를 풀어보면서 바로 익혀보자.