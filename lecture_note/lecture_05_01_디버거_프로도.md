## 5장 디버깅

- 지금까지 계속 프로그램을 만들어왔음.  우리는 이제 버그를 없애는 디버깅을 다룰 것임.   
- 버그 : 프로그래머가 만들지는 않았지만 분명 컴퓨터 분야에서 가장 널리 쓰이는 용어 중 하나  
- 왜 버그라고 할까요? 
	- (사실 초기 컴퓨터는 진공관을 이용하여 만들었는데, 여기에 벌레가 들어갈 경우 고장을 일으켜서, 이런 의도하지 않은 류의 고장을 "버그가 있다" 라고 하고, 이 고장 혹은 오류를 없내는 과정을 "벌레를 없앤다" 라고 하여 "디버깅" 이라고 하게 되었음. )

 - 왜 버그가 발생 할까요? -> 프로그램의 복잡성 때문
 - 프로그램의 복잡성을 줄이는 방법은? 
	- 상호작용 방법 : 컴포넌트 사이의 연결을 줄여 상호작용할 부분들의 수 적게 하기  
	- 정보 은닉, 추상화, 인터페이스, 이를 지원하기 위한 프로그래밍 언어의 기능  
- 그 외에 버그를 예방할 수 있는 방법은요? 
	- 소프트웨어의 무결성 보장 방법 : 프로그램 증명(proof), 모델링, 요구사항 분석, 정형 검증 등  
  
- 계속 테스트 해서 버그를 찾고, 디버깅 할 것임.  
	- (I'll test you, find you and fix you)  


- 디버깅은 안 하는 것이 가장 좋음 (하지만 그렇게 될 리가.. )  
- 디버깅 줄이는 방법들  
	- 좋은 설계, 좋은 스타일  
	- 경계조건 테스트, 단정문 (assertion), 코드 정상성(sanity) 체크  
	- 방어적 프로그래밍, 잘 설계된 인터페이스, 한정된 전역 데이터, 검사 도구  
- "치료 보다는 예방이 우선"  
  
- 언어에서 지원되는 버그 방지 기능들  
	- 인덱스를 이용한 범위 검사 
		- index 가 out of range 가 나면 sigsegv 가 나거나 다른 변수등이 바뀔 수 있음. 
	- 포인터를 제한적 사용하거나 아예 사용하지 않는 것 
		- 포인터는 잘 쓰면 아름답지만, 데게 완벽하게 사용하기는 쉽지 않음. 
	- 가비지 컬렉션 
	- 문자열(string) 타입
		- 문자 배열도 잘못 쓰는 경우가 많음. '\\n' 의 공간을 계산하지 않는다던가 등등... 
	- 형식 입출력  
	- 엄격한 타입검사  
		- 의도한 casting 이 아닌 경우 (factory pattern 등을 위한 upcasting 등) 에는 대게 잠재적 에러를 유발하기 쉬움 
			- unsigned / signed 간의 비교
			- casting 으로 인한 정밀도 손실 등
  
- 에러를 유발하기 좋은 기능  
	- goto 문  : 어디로 가는지 모름
	- 전역변수  : 누가 언제 바꾸는지 모름
	- 포인터  : 타입은 뭐였더라.. 범위는 어디였더라.... 
	- 자동 타입 변환  : 잘못된 비교, 정밀도 손실에 의한 오류 등.. 
  
- 그래서 우리는 경고 메시지를 잘 봐야 함.  
- 디버깅을 잘 하는 방법을 5장에서 보고, 6장에서 테스트를 다룰 예정.  
  
  
  
## 5.1 디버거  
  
- 대부분 컴파일러에는 보통 정교한 디버거가 따라옴. 
- 대게 소스코드를 작성, 편집하고 컴파일, 실행, 디버깅 하는 작업을 한 시스템에서 통합해서 수행할 수 있는 개발환경 일부에 편입되어 있다.  
	- visual studio 나 pycharm 같은것을 생각해보면 언어를 컴파일 하며 디버깅까지 할 수 있음.  
	- 꼭 그렇지 않더라도 gcc 같은 cui (Command User Interface) 조차도 강력한 gdb 를 제공하고 있음.  
	- (저같은) 프로그램을 잘 못 쓰는 사람은 printf(c), cout(c++) print(python) 등으로 디버깅 하지만, 디버깅 툴을 잘 쓰면 메모리를 바라보고 의도대로 잘 바꼈는지 보면 훨씬 쉽게 디버깅 할 수 있음.  
  
- 문제가 생겼을때 바로 디버거를 실행할 수 있다.  
	- 어떤 디버거는 문제가 생기면 자동 실행 됨 (visual studio)  
	- 어디까지 실행 중이었는지, 활성화된 함수 순서 검사 (스택 추적), 지역변수, 전역변수의 값 표시  
	- 브레이크 포인트 (중단점), 스텝 (함수 실행시 한단계 안으로 들어가 실행) 도 편리한 기능  
  
- 디버거를 한장이나 할애해서까지 보는 이유? 어려운 상황에서 어떻게 디버깅 할지 고민해보라고.  
	- 비주류 언어들은 디버거 없음. (R 같은게 디버거가 있었던가.... )  
	- 디버거는 시스템 의존적이라 다른 시스템에서 개발 할 때는 익숙한 디버거 사용할 수 없음. (윈도우 사용자가 리눅스에서 개발하거나 등등)  
	- 멀티 프로세스, 멀티쓰레드 프로그램은 디버깅 어려움. (인디언~~ 밥! 누가 맨 마지막에 때렸게? )  
	- 운영체제, 분산형 시스템은 더 저수준 접근 방식 디버깅 필요. (레지스터에 스택 까 보신분? 시스템 동시에 다 까면서 여러 서버 로그 보신분? )  
  
- 모든 것을 다 디버거에 의존하는 것은 효율적이진 않다.  
	- 디버거도 생각보다 무거운 경우도 있다.  
	- 소 잡는 칼을 닭 잡는데 굳이 쓰지 말자. (변수 뭔지 궁금하면 그냥 print 하는게 더 빠를 수 있다. )  
  
- 그래서, print 나 log 도 적절히 사용하는게 좋다.  
	- 출력이 부담스러우면 log level 을 조정, build type (debugging, release) 에 따라 출력되는 log level 을 제한하는 것도 방법  
	- 그 조차도 부담스러우면 #define DEBUG #ifdef DEBUG #endif 등을 사용하는 것도 방법임.  
	- 단 조심.. print 나 log 는 프로그램의 코드 사이즈를 영구적으로 소모한다. (위 define 을 이용한 방법은 precompile 과정에서 코드가 사라져 잡아먹지 않음)  

- 결론
	- 디버거가 있으면 잘 써봅시다.  
	- 디버거가 없으면 이후 장에서 소개하는 디버깅 기법들을 잘 둘러봅시다.
