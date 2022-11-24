튜플이 100,000개 미만이라면 엥간한 악조건이여도 쿼리가 금방금방 이루어진다.  
_(여기서 악조건 : DB가 올라가있는 하드웨어가 좋지 않거나, 쿼리가 거지같거나)_  
하지만 **튜플이 1,000,000개 가량 되거나 그 이상이되면, 사람이 체감할 수 있을 정도로 쿼리에 시간이 걸린다.**  

## Bulk Inserting

DB에 INSERT 해야하는 튜플이 많은 경우 사용되는 기법이다.  
**한 번에 다량에 데이터을 DB에 집어넣으려고 할 때** 유용하다.  
  
예를 들어, 기존의 쿼리가  
```

INSERT INTO table VALUES (1, "hello");

INSERT INTO table VALUES (2, "world");

INSERT INTO table VALUES (3, "!");
```

였다면,  
```
INSERT INTO table VALUES (1, "hello"), (2, "world"), (3, "!");
```

처럼 **한 쿼리에 다량의 튜플을 묶어서 쿼리를 넣어주면 된다.**  
  
**실제로 사용할 때에는, 한번에 수천개씩 묶어서 쿼리를 넣어주면 된다.**  
  
_백만개 정도의 데이터를 한번 MySQL에 넣어보자._  
_정말 기하급수적으로 성능이 향상되는 모습을 볼 수 있다._  
  

### [성능이 향상되는 이유]

DB의 다양한 요소들에 의해, 쿼리 한번이 이루어지면 그 전후에 이루어지는 작업이 꽤나 있다. (Transaction, Index 등)  
  
DB에 1,000,000개의 데이터를 INSERT 하는 상황을 생각해보자.  
DB에 1개의 데이터를 넣을때 N, 쿼리 전후에 M이라는 값이 소모된다고 가정해보자.  
  
만약 쿼리 하나에 1개의 튜플을 INSERT 하게 되면,  
소모값은 (1,000,000 * N + 1,000,000 * M)이다.  
  
하지만 쿼리 하나에 1,000개의 튜플을 INSERT 하게 되면,  
소모값은 (1,000,000 * N + 1,000 * M)이다.  
  
_별거 아닌거 같아도 실제로 엄청난 속도차이를 나에게 보여줬다._  
  
  

### [MySQL 튜닝]

Bulk Inserting 중에는 최대한 DB가 다른 작업을 덜 하게 하는 것이 키 포인트이다.  
MySQL의 B+ Tree 구조를 잘 생각하고, (뭐가 Clustered Index인지 잘 생각하고)  
InnoDB인지 MyISAM인지를 잘 생각해야 한다.  
  
**- Auto Commit**  
mysql> SET autocommit=0;  
Bulk Inserting......  
mysql> commit;  
  
INSERT가 끝나고 Transaction을 걸어주자~  
  
  
**- INDEX**  
테이벌에 걸려있는 INDEX가 많다면, Bulk Insert하면서 INDEX를 조정하는 것 보다 Bulk Insert가 끝나고 INDEX를 조정하는 것이 훨씬 빠르다.  
  
  
**- KEY / UNIQUE**  
Bulk Insert 되는 데이터가 믿을수 있는 데이터라면, 잠시 UNIQUE 검사 / FK 검사 등은 꺼두는 것이 좋다.  
  
mysql> SET unique_checks=0;  
Bulk Inserting......  
mysql> SET unique_checks=1;  
  
mysql> SET foreign_key_checks=0;  
Bulk Inserting......  
mysql> SET foreign_key_checks=1;  
  
  
**- 버퍼/로깅 등**  
MySQL의 잡다한 설정을 바꿔주는 것도 큰 도움이 된다고 한다.  
  
**/etc/my.cnf** 에서  

```
innodb_change_buffering=all

innodb_change_buffer_max_size=25

# innodb_buffer_pool_instances=1 Change at your own discretion

innodb_buffer_pool_size=3072M // RAM의 80% 가량으로 설정

innodb_log_file_size=384M //buffer_pool_size에 비례해서 조정

innodb_log_buffer_size=128M

innodb_flush_log_at_trx_commit=1

skip-innodb_doublewrite
```