# TLB


## TLB 란?
`Translation Lookaside Buffer, (변환 색인 버퍼)`            

### 정의
- 가상 메모리 주소를 물리적인 주소로 변환하는 속도를 높이기 위해 사용되는 캐시
- 일종의 주소 변환 캐시로 최근에 일어난 가상 메모리 주소와 물리 주소의 변환 테이블을 저장


<br>
<br>

### 특징
- 하드웨어적으로 지원하며 페이지 테이블의 임시저장 캐시 역할을 함
- 캐시 메모리(하드웨어)에 별도로 `페이지 넘버 - 프레임 넘버`에 대한 매핑 정보를 담고 있는 테이블 
- 여러가지 다른 레벨의 캐시들 사이에서 주소를 변환하는데 사용
    - EX) CPU와 CPU캐시 사이, CPU캐시와 메인 메모리 사이 등
- 현재 모든 데스크탑 및 서버용 프로세서는 하나 이상의 TLB를 메모리 관리 하드웨어에 가지고 있음
- 페이지, 세그먼트 단위로 처리하는 가상 메모리를 사용하는 거의 모든 하드웨어는 TLB를 사용
- 작은 크기
    - 64~1024 entry 정도로 작은 크기
    - TLB에 내용이 있을 경우, 메모리에 접근하기 전에 사용해야하므로 크기를 작게하여 속도를 높임
    - 보통 SRAM으로 구성

<br>
<br>


### 실행

<img src ="https://upload.wikimedia.org/wikipedia/commons/6/6e/Translation_Lookaside_Buffer.png" width="500" heigth="300">

1. CPU는 1차적으로 TLB에 접근해 원하는 페이지가 존재하는지 탐색         
2. TLB에 존재하지 않을 경우 [MMU](https://github.com/jmxx219/CS-Study/blob/main/OperatingSystem/가상%20메모리.md#mmumemory-management-unit-메모리-관리-장치)
 페이지 테이블 참조

 > MMU는 CPU 내부에 존재하며, 일반적으로 MMU안에 TLB가 포함되어 있다.

<br>
<br>

### 사용 이유
- 페이지 테이블은 메인 메모리에 저장되기에 프로그램에 의한 모든 메모리 접근은 최소 두 번 필요함
    - 실제 주소를 얻기 위한 메모리 주소 접근 (`가상 주소 → 실제 주소`)
    - 데이터를 얻기 위한 접근
- 결과적으로 접근 시간이 2배 걸리는 것을 해결하기 위해 사용

<br>
<br>

### Hit & Miss & Flushing
- TLB Hit
    - TLB에 페이지 넘버가 존재하는 경우
    - 메모리에 1번 접근
        - TLB에 접근하며 페이지 테이블에 접근하지 않음
- TLB Miss
    - TLB에 페이지 넘버가 없는 경우
    - TLB엔 없지만 main memory에는 올라가 있는 상태
    - 기존보다 더 많은 시간 소요
        - 기존 방식 + TLB 접근 시간 추가
- TLB Flushing
    - TLB를 강제로 초기화
        - Full flushing - 전부 초기화
        - Partial Flushing - 부분 초기화
    - Context Switching이 발생하면 이전 프로세스의 Entry는 필요하지 않으므로 초기화
    - 페이지 테이블이 변경되면 페이지의 위치가 변할 수 있으므로 초기화

- Hit ratio
    - 접근하려는 메모리의 페이지 번호가 TLB에서 발견되는 비율
    - 이를 통해 `평균 접근 시간 EAT(effective memory access time)` 계산 가능
        - `EAT = (Hit ratio) * (TLB Hit시 접근시간) + (1 - Hit ratio) * (TLB Miss시 접근 시간)`

> 이와 같은 이유로 TLB Miss를 줄이는 방법(TLB replacement)가 필요하다.      
> 여기에 해당하는 알고리즘으로 LRU, FIFO, RANDOM등이 있다.


<br>
<br>



### TLB 구성

- VPN(Virtual Page Number)
    - 가상 페이지 번호

- PFN(Page Frame Number)
    - 물리 메모리의 페이지 프레임 번호

- Other bits
    - Vaild bit
        - 페이지가 물리 메모리에 적재되어 있으면 1, 아니면 0 값

    - Protection bit
        - 읽기(read), 쓰기(write), 실행(execute) 등의 권한을 설정하여 메모리와 시스템을 보호

    - Drity bit
        - TLB의 내용의 write가 있었는지 확인하는 비트
            - 1일 경우: write되었으므로 TLB에서 나갈때 메모리에 write 해줌
            - 0일 경우: 기존의 메모리에 있는 값가 동일함으로 그냥 나감

    - Reference bit
        - 메모리에서 값이 참조 될 때 1로 변환
        - TLB가 꽉 찼을때 내보낼 페이지 선택시 LRU알고리즘 사용
            - Reference bit를 기준으로 비트가 0인 페이지부터 교체
            - Reference bit가 모두 1이 되면 주기적으로 0으로 변환

    - ASID(Address Space Identifier)
        - 프로세스 구분을 위해 사용

<br>
<br>

### ASID
- 프로세스는, 프로세스 마다 다른 페이지 테이블을 가짐
- TLB는 전역에 있기에 전체 프로세스에 대한 캐시를 다뤄야 함
- 따라서 TLB는 테이블에 단순히 페이지 넘버 뿐 아니라, `프로세스 아이디`도 가지고 있어야 함
    - 이를 `ASID(Address-space Identifier)`라고 함

<br>
<br>

### Ref
[빽 투더 기본기 [OS 7편]. 메모리 관리 2](https://dailyheumsi.tistory.com/138)           
[MMU, TLB](https://hojunking.tistory.com/119)