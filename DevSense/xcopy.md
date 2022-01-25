## XCOPY 대해 (fit.visual studio 빌드 후 이벤트 활용 하기) 

: XCOPY는 여러 파일, 전체 디렉토리 트리를 기반으로 하나의 디렉토리에서 다른 디렉토리로 파일을 복사할 때 사용한다. <br>
즉, 스크립트를 이용하여 파일 복사하는 기능이다. 윈도우나 다른 os에서 GUI를 지원하는 경우 Ctrl+C, Ctrl+V로 복사가 용이하다. <br>
대량의 파일을 복사하거나, 덮어써야할 파일 구분이 필요한 경우 XCOPY 명령어를 활용하면 좋다.

    xcopy /추가명령어 "원본 주소" "복사할 주소"
    xcopy /S /Y /E /I "C:\source\model" "C:\result\build\msvc\..\..\bin64\model"

<br>
개발 릴리즈를 하다보면, 빌드 후 결과물에 .dll, folder 등 파일을 바라보게 해야한다. <br>
이 경우 git이나 server쪽에서 관리하는 파일 디렉토리와 실제 소스코드상에서 ../ 상대경로로 바라보는 곳과 차이가 생기게 된다. <br>
따라서 그런 경우에 아래와 같이 Visual Studio 솔루션 탐색기 - 속성 - 구성속성 - 빌드 이벤트 - 빌드 후 이벤트에서 xcopy 명령어를 활용하면 <br>
빌드가 끝나고 원하는 경로에 파일을 복사 할 수 있다. <br><br>
주로 개발 할 때 사용한 .dll, folder등을 빌드 경로에 추가로 넣어 활용 할 수 있다.  

## visual studio 빌드 후 이벤트 활용하기 (xcopy)

1. 현재 프로젝트 솔루션 - 솔루션 우측 클릭 - 속성
![img.png](../img/xcopy_1.png)

2. 구성속성 - 빌드 이벤트 - 빌드 후 이벤트 - 명령줄 - 편집
![img.png](../img/xcopy_2.png)

3. visual studio 빌드 - 빌드가 끝나고 원하는 폴더에 폴더 및 파일들 복사 완료
![img.png](../img/xcopy_3.png)

