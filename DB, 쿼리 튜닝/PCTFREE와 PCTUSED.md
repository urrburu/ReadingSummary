# PCTFREE란?  
 - 사용가능한 Block 공간 중에서 데이터 Row의 Update 등 데이터의 변경에 대비해서 확보해 놓은 BLOCK의 %값입니다.  
 - PCTFREE의 Default 값은 10%입니다.  
 - PCTFREE와 PCTUSED의 합이 100을 초과하지 않는 범위내에서 0~99까지 값을 PCTFREE값을 PCTFREE 값으로 사용할 수 있습니다.  
  
 - 위 그림은 PCTFREE = 20%인 그림입니다.  
 - BLOCK의 20%를 사용가능한 빈영역으로 유지하며 빈영역은 각 BLOCK의 ROW의 UPDATE 등 데이터를 갱신하는 데 사용합니다.  

  

  
*** PCTFREE가 적을 경우**  
 - 기존 테이블 행 갱신에 의한 확장을 위해 적은 공간을 확보합니다.  
 - 많은 ROW가 한 BLOCK안에 INSERT 될 수 있습니다.  
 - 수정이 적은 SEGMENT에 적합합니다.  

  
*** PCTFREE가 클 경우**  
 - BLOCK당 적은 ROW가 INSERT됩니다. 즉 같은 ROW를 입력하기 위해 많은 BLOCK이 필요합니다.  
 - ROW의 조각을 자주 CACHING할 필요가 없으므로 수행속도가 증가합니다.  
 - 자주 수정되는 SEGMENT에 적합합니다.



*** PCTUSED란?**  
 - DATA BLOCK의 FREE SPACE의 %가 PCTFREE에 도달 후, 사용된 공간이 PCTUSED  
   이하의 값이 되기 전까지 해당 BLOCK에 새로운 ROW를 추가할 수 없습니다.  
 - PCTFREE에 도달하기 전까지는 새로운 행을 INSERT하지 못하고 기존의 ROW를 UPDATE만 할 수 있습니다.  
  
  
 - 위 그림은 PCTUSED값이 40% BLOCK의 사용영역이 39%보다 적어지지 않으면 새로운 행을 INSERT 할 수 없음을 의미합니다.

출처: [https://12bme.tistory.com/304](https://12bme.tistory.com/304) [길은 가면, 뒤에 있다.:티스토리]