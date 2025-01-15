# 트랜잭션의 성질(ACID)의 정의와 각 특성의 역할
## 트랜잭션
- DB 시스템에서 수행하는 작업의 단위
- 하나 이상의 쿼리가 모여 하나의 작업을 수행하는 것

## ACID(트랜잭션의 특징)
### Atomicity(원자성)
- 트랜잭션 작업 **전부 DB에 반영**되던가 **아예 반영되지 않거나** 여야 한다.
- 트랜잭션 중간에 오류가 발생하면 이전 상태로 복구되어야 한다.

### Consistency(일관성)
- 트랜잭션의 작업의 결과가 항상 일관성 있어야 한다.
- 한 트랜잭션 작업 내에서 DB가 변경되어도 업데이트된 DB로 진행되는 것이 아닌 처음부터 참조하던 DB로 진행되어야 한다.

### Isolation(고립성/독립성)
- 여러 트랜잭션이 동시에 실행되더라도 각 트랜잭션은 서로 영향을 미치지 않고 독립적으로 실행되어야 한다.
- 트랜잭션은 다른 트랜잭션의 연산에 끼어들 수 없다.

> 격리 수준(Isolation Level)을 통해 설정 가능
> - **READ UNCOMMITTED**: 커밋되지 않은 데이터를 읽을 수 있다.
> - **READ COMMITTED**: 커밋된 데이터만 읽을 수 있다.
> - **REPEATABLE READ**: 자신의 트랜잭션이 생성되기 전의 트랜잭션에서 commit된 데이터만 읽을 수 있다.
> - **SERIALIZABLE**: 여러 트랜잭션이 동일한 레코드에 동시에 접근할 수 없으며, 트랜잭션을 순차적으로 진행시키는 것으로 성능이 매우 떨어짐.

### Durability(지속성)
- 트랜잭션 작업이 성공적으로 완료되었을 때, 해당 결과는 영구적으로 반영되어야 한다.

# Commit과 Rollback의 동작 원리와 활용
- DB의 원자성을 보장하기 위해 사용되는 기능이며, 데이터의 무결성을 보장한다.

## Commit
- 수행한 작업(트랜잭션)을 DB에 **영구적으로 반영**하는 것
- 트랜잭션의 연산이 성공적으로 완료되었을 때 수행

## Rollback
- 수행한 작업(트랜잭션)을 **취소**하는 것
- 트랜잭션의 연산이 실패하여 원자성이 깨졌을 때 모든 연산을 취소하는 연산

## Commit과 Rollback의 활용
### commit, rollback 이전의 데이터 상태
- DB의 데이터를 임시로 저장하는 공간(메모리 BUFFER)에만 영향을 받아, 변경 이전 상태로 복구 가능
- 현재 사용자만이 SELECT 문장으로 결과를 확인할 수 있지만 다른 사용자는 확인 불가
- 변경된 행은 잠금 설정되어 다른 사용자가 변경할 수 없다.

### commit 이후의 데이터 상태
- 변경 사항이 DB에 저장되어, 모든 사용자가 결과를 볼 수 있다.
- 이전 데이터는 사라져 복구할 수 없다.
- 관련된 행에 대한 잠금이 풀리고, 다른 사용자가 행을 조작할 수 있게 된다.

### rollback 이후의 데이터 상태
- 작업 수행 전 처음 DB 상태로 복구된다.
- 관련된 행에 대한 잠금이 풀리고, 다른 사용자가 행을 조작할 수 있게 된다.