# jpa-association

## 1단계 - OneToMany (FetchType.EAGER)
### 요구사항 1 - Join Query 만들기
- 기존 SelectQueryBuilder에서 map으로 추가 정보들을 받을 수 있다.
```sql
select orders.id, orders.orderNumber, orderItem.id, orderItem.produce, orderItem.quantity
from orders
join orderItem on orders.id = orderItem.id
where orders.id = ?;
```

### 요구사항 2 -Join Query 를 만들어 Entity 화 해보기
- EntityOneToManyColumn
  - OneToMany 어노테이션이 필드에 없는 경우 예외가 발생한다.
  - fetchType을 추출하여 저장한다.
  - Many field의 제네릭 타입을 저장한다.
  - 필드 값을 assign한다.
    - null인 경우 arraylist를 생성하여 추가한다.
    - 이미 list가 있는 경우 add 한다.
- EntityColumns
  - OneToMany가 있는 어노테이션으로 해당 일급컬랙션을 생성한다.
  - EAGER인 EntityJoinColumn들을 반환한다.
- RowMapper
  - EagerJoinColumn이 있는 경우 select 결과에서 즉시로딩된 값을 assign해준다.

## 3단계 - OneToMany (FetchType.LAZY)
- SelectQueryBuilder
  - SelectAllQueryBuilder의 모든 기능을 이관한다
  - join절을 사용한 select all 쿼리를 생성한다.
- PersistentCollection
  - list value와 load되었는지 여부를 가진다.
  - List를 상속하여 모든 메소드는 list value를 바라보도록 한다.
    - 모든 메소드는 실행 전 load를 실행해야 한다.
      - load 여부가 false인 경우 entityLoader로 부터 load하여 value에 넣는다.
      - load 여부가 true인 경우 return하여 보유중인 value를 사용한다.
- EntityLoader
  - LazyJoinColumns에 대해서 poxy 객체를 주입한다.
