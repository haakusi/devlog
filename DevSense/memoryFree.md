## 동적 메모리 해제 후, NULL 대입 이유 

: 동적 메모리, 정적 메모리에 대해 알야아 한다. 메모리 해제는 왜 필요한지. 그리고 NULL로 대입 연산 하는 이유에 대해 알아본다.

    free(variable)를 통해 할당 된, 메모리 해제가 가능하나, 해제된 메모리 영역이 다른 곳에서 사용될 경우,
    variable에 값이 들어 있어 예기치 못한 버그를 만들 수 있다. 그러므로 보통 선언부, 메모리 해제후, NULL를 활용 한다.

    char* variable = NULL;
    if(!variable) free(variable);
    variable = NULL;        // NULL 처리를 안해줄 경우 for문이나 반복적인 구문에서 사용시 바로 위의
                            // if(variable) ... 함수를 또 타서 해제 했는데, 또 메모리 해제 호출 하다가 뻗을 수도 있음.

+.. 조금만, 사설을 덧붙이면..<br>
특정 함수안에서 케이스에 맡게 1,2,3,4 수행하는데, 1,2,3,4에서 각각 메모리 할당해서 특정 변수들이 맞물려 있는경우,
메모리 해제를 제일 마지막에 한번 해주거나, 각각 바로 1,2,3,4에서 return를 시키면 1,2,3,4 안에서 반복해서 메모리 해제를 해줘야해서...
고민의 고민을 하다가.. 결국은 플래그 변수를 통해서 if(flag) { 1 } if(flag) { 2 } ... 식으로 구현해서 마지막에 한번만 mem free를 해서 해결했다.
외부 lib를 사용하다보니, 인자로 포인터를 넘겨주고, 거기서 malloc를 하기 때문에, smart pointer를 사용하기가 힘든 구조였다..
사설은 줄이고! 아래를 읽어가자!
<br>

## 동적 메모리(Dynamic Memory Allocation)에 대해
- 프로그램 실행 중에, 동적으로 메모리 크기를 할당 시켜야 할 경우
- malloc()은 메모리 상의 연속된 공간 할당을 받기 위해 사용 (byte 단위)
- 할당 되지 않은 포인터에 데이터를 입력할 경우 임의의 주소에 값을 입력하게 된다.
- 메모리 누수, 포인터의 잘못된 사용에 의한 참조 등 버그 발생 가능성 존재. 

<details markdown="1">
<summary>동적 메모리 - malloc 활용</summary>
malloc()은 인자로 할당 받고자 하는 메모리 크기를 byte 단위로 명시. 메모리 할당이 성공하면, malloc()은 할당된 메모리의 시작 위치를 가리키는 포인터를 리턴한다.
(void*)를 받는 이유는 어떤 타입으로든 형변환이 가능케 하기 위해서이다.

    - 100byte 문자를 저장하기 위한 공간 할당을 받기 및 활용
    ex1)
    char *tmpStr = NULL;
    tmpStr = (char *) malloc(100);
    
    ex2)
    char *tmpStr;
    memcpy(tmpStr, "1234", 100); ---- x

    ex3)
    char *tmpStr;
    tmpStr = (char *)malloc(100);
    memcpy(tmpStr, "1234", 100); ---- o
</details>

<details markdown="1">
<summary>동적 메모리 - realloc 활용</summary>
realloc()은 메모리의 크기를 조정하고자 할때 사용. 파일의 크기를 읽은 다음 파일의 크기만큼 한번에 메모리 할당을 해버리는 방법,
1024 바이트 정도로 할당, 파일을 읽다가 1024를 초과하게 되면, realloc를 이용해서 1024를 더 할당하는 방법으로 활용 된다.
단, 재할당 하는 과정에서 기존의 데이터가 복사되므로 malloc에 비해 많은 비용이 소모 된다.

    - relloc()
    char *tmpStr;
    tmpStr = (char *)malloc(100);
    memcpy(tmpStr, "1234", 1024);
    tmpStr = (char *)realloc(tmpStr, 2048);
</details>

<details markdown="1">
<summary>동적 메모리 - sizeof 활용</summary>
sizeof는 c의 키워드로 해당 자료형의 크기를 돌려준다. 메모리 할당을 할때 직접 int를 입력 할 수도 있지만,
각 자료형 마다 byte 크기가 다르며, 같은 자료형도 os에 따라 크기가 다를 수 있기 때문에, 직접 입력보다는 sizeof를 활용하도록 한다.

    int *intP;
    intP = (int *)malloc(16); ---- x
    
    int *intP;
    intP = (int *)malloc(sizeof(int)*4); ---- o
</details>

<details markdown="1">
<summary>동적 메모리 - free</summary>
malloc()은 메모리 할당을 커널에 요청하는 시스템 함수다. malloc()에 의해 할당받은 메모리는 프로세스가 종료시점 까지 커널에 의해 보호 받는다.
즉, malloc()를 잘못 사용할 경우 메모리 공간 낭비, 메모리 누수를 일으킬 수 있다.

    char *cp;
    while(1) {
        cp = (char *)malloc(1024);
        printf("%x\n", cp);
        sleep(1);
    }
    : 운영체제에서 계속 1024byte 크기의 새로운 메모리 공간을 프로세스에게 할당함.
    8049611
    8061d38
    80c36a5
    ...

    따라서, 사용한뒤, free(var)를 꼭 해줘야한다.
    char *cp;
    while(1) {
        cp = (char *)malloc(1024);
        printf("%x\n", cp);
        sleep(1);
        free(cp);
    }
    : 메모리 누수 없음

</details>
  
## 정적 메모리(Static Memory Allocation)에 대해
- 메모리 크기를 고정시켜서 할당 받는다.
- 할당 시켜야 할 메모리의 size를 정확히 알때 사용한다.
- 프로그램 시작 시, 미리 고정시켜 할당
- 최대 size보다 조금 더 크게 할당하는 경우가 많아, 메모리 낭비가 발생할 수 있다.
- 동적 메모리 할당에 비해 버그가 발생할 확률이 낮음.

## 그래서 결론은 smart_pointer를 사용하자!
C++에서는 new 키워드를 사용해 동적으로 할당받은 메모리는 반드시 delete 키워드를 사용해 해제해야한다.
따라서 C++에서는 메모리 누수(memory leak)으로 부터 프로그램의 안전성 확보를 위해 스마트 포인터를 제공한다!
사용이 끝난 메모리를 자동으로 해제 해준다. C++11이후 3가지의 스마트 포인터가 제공 된다. 잘 활용 하도록 한다!
- unique_ptr
- shared_ptr
- weak_ptr

