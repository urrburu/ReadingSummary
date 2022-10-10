## AVL 트리(Tree) 개념 및 구현

AVL 트리는 스스로 균형을 잡는 이진 탐색 트리입니다.

트리의 높이가 h일 때 이진 탐색 트리의 시간 복잡도는 O(h)입니다.

한쪽으로 치우친 편향 이진트리가 되면 트리의 높이가 높아지기 때문에 이를 방지하고자 높이 균형을 유지하는 AVL 트리를 사용하게 됩니다.

AVL트리는 다음과 같은 특징을 가집니다.

-   AVL 트리는 이진 탐색 트리의 속성을 가집니다.
-   왼쪽, 오른쪽 서브 트리의 높이 차이가 최대 1 입니다.
-   어떤 시점에서 높이 차이가 1보다 커지면 회전(rotation)을 통해 균형을 잡아 높이 차이를 줄입니다.
-   AVL 트리는 높이를 logN으로 유지하기 때문에 삽입, 검색, 삭제의 시간 복잡도는 O(logN) 입니다.

## BalanceFactor 
AVL 트리에서 가장 중요한 부분이 BalanceFactor이다. "회전"의 기준이 되는 밸런스 팩터는 균형이 무너졌는지 확인할 수 있는 요소이다. ![Balance Factor](https://blog.kakaocdn.net/dn/Rvk7y/btro38nIs7K/bMDyjafrvsozbbucjHWU5K/img.png)

밸런스 팩터를 구하는 법은 왼쪽 노드의 서브트리의 높이와 오른쪽 노드의 서브트리 높이의 차를 이용해 구한다. 

## 우회전
![Right Rotation](https://blog.kakaocdn.net/dn/wxXjj/btro6alLimV/o7oeM9EtG3PDAfNd7Nnlqk/img.png)

1. y노드의 오른쪽 자식 노드를 z노드로 변경한다.  
2. z노드 왼쪽 자식 노드를 y노드의 오른쪽 서브트리(T2)로 변경한다.


## 좌회전
![Left Rotation](https://blog.kakaocdn.net/dn/bvi2dr/btrpaIoxOIj/vbPMbWybCbhmgCqkwLYFg0/img.png)

좌회전의 경우는 부모노드인 Z노드가 왼쪽 서브트리로 변경괼때를 의미한다. 이때 Z노드에게 기존의 자식 노드를 물려준다. 