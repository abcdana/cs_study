## 가상 메모리

: 적은 메모리로 여러 프로세스를 실행시키기 위해 **메모리 자원을 추상화**하여 사용자들에게 **매우 큰 메모리로 보이게 만드는 시스템**으로 페이징 방식으로 동작한다.

→ 어떤 프로세스가 실행될 때 <span style="color:#3397FF">**메모리에 해당 프로세스 전체가 올라가지 않더라도 실행이 가능**</span>하다는 점에 착안하여 고안되었다.

→ 실행에 필요한 <span style="color:#3397FF"> **일부분만 메모리에 올라가며 애플리케이션의 나머지는 디스크에**</span> 남게 된다. 즉, **디스크가 RAM의 보조 기억장치(backing store)처럼 작동**하는 것.

- 가상 주소 : 가상적으로 주어진 주소
→ **메모리관리장치(MMU)에 의해 실제 주소로 변환**된다.
- 실제 주소 : 실제 메모리상에 있는 주소

가상 메모리는 페이지테이블로 관리되며 속도 향상을 위해 TLB도 같이 사용한다.

> 💡 **- 페이지 테이블**   
: 가상 메모리 시스템에서 **가상 주소와 물리적 주소 간의 매핑 정보를 저장**하는 데이터 구조 <br>
→ 페이지 테이블은 메인 메모리에 존재한다. <br>
→ **물리적 주소를 얻기 위해 메인 메모리의 테이블을 참조**한 후 **물리적 주소에 다시 방문**하는 2번의 메모리 접근을 하게 되어 속도가 저하된다. <br>
→ 이를 해결하기 위해 TLB를 함께 사용한다. <br><br> - **TLB** : 메모리와 CPU 사이에 있는 **주소 변환(가상 주소 → 실제 주소)을 위한 캐시**로 페이지 테이블의 리스트를 보관한다.


### 가상 메모리의 장점

- 프로그램의 일부분만 메모리에 올리는 방식으로 물리 메모리의 크기에 제약을 받지 않을 수 있다.
    
    → **더 많은 프로그램을 동시에 실행**할 수 있다.
    
- 프로그램을 메모리에 올리고 스왑 하는 데 필요한 I/O가 적어진다.
    
    

## 요구 페이징 (Demand Paging)

: 프로세스가 실행되는 동안 실제로 필요한 페이지만 메모리에 올리는 방법

- Swap-in : 필요한 페이지를 디스크에서 메모리로 가져오는 작업
- Swap-out : 메모리가 부족한 상황에서 메모리에 저장된 프로세스나 프로세스의 일부를 디스크로 내보내는 작업

    → <span style="color:red">**페이지 교체 알고리즘**</span>을 통해 어떤 페이지를 swap-in, swap-out할지 정한다.

<br>

- valid-invaid bit : 페이지 테이블에서 **해당 page가 메모리에 있는지**를 나타내는 bit
    - 0 / false : 해당 페이지가 메모리에 올라와있고 유효한 상태
    - 1 / true : 해당 페이지가 메모리에 없거나 무효한 상태
        
        → 이 경우 프로세스가 접근할 때 <span style="color:red">**Page Fault 오류**</span>가 발생한다.
        
        ![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTVWjW%2FbtsyFpeUDLG%2F2vDZfKPIOC0mVI7KJ3SP5k%2Fimg.png)
        

<br>

### 페이지 폴트

: **프로세스의 주소 공간(가상 메모리)에는 존재하지만 실제 메모리인 RAM에는 없는 데이터에 접근**했을 경우 발생하며 **스와핑**을 통해 해결할 수 있다.

> 💡 **스와핑 :** 메모리에서 사용하지 않는 영역을 하드디스크에 저장하고 나중에 다시 메모리로 불러오는 것


1. 어떤 명령어로 인해 페이지 폴트가 발생하면 운영체제에 알린 후 해당 프로세스를 일시중지한다.
2. 운영체제는 **실제 디스크에서 사용하지 않는 프레임**을 찾는다.
3. 이 프레임을 **실제 메모리에 가져와서 페이지 교체 알고리즘을 기반으로 특정 페이지와 교체**한다.
→ **가상 메모리에만 있던 것을 실제 메모리로 가져온다.**
1. 페이지 테이블을 갱신시킨 후 해당 명령어를 다시 시작한다.

### 요구 페이징 동작 과정

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FI406u%2FbtrgbyB1G84%2FMi8YP75e4DMy9mboLGngf1%2Fimg.png)

1. 하드웨어가 **TLB를 확인**한다.
2. **TLB hit인 경우 곧바로 주소를 변환**하고,**TLB miss인 경우 page table**을 확인한다
3. page table의valid-invalid bit가 **valid로 되어 있다면 주소를 변환**하고 TLB에 page를 올린다. **invalid라면 page fault**가 발생한다
4. page fault가 발생하면 **MMU가 운영체제에 trap**을 걸고 커널 모드로 들어가 page fault handler가 invoke된다.
5. 유효하지 않은 참조인 경우 프로세스를 종료시키고, 그렇지 않다면 **빈 page**을 얻는다.
6. 운영체제는 **참조된 page을 디스크에서 메모리로 로드(I/O)**하고 이 과정 동안(disk I/O) 프로세스는 CPU를 빼앗긴다.
7. disk I/O가 끝나면 **page table이 업데이트** 되고 valid-invalid bit가 valid로 바뀌며 ready-queue에 프로세스가 대기한다.
8. 프로세스가 다시 CPU를 할당받으면서 작업을 수행한다.