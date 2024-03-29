### 8.9 요약

- 호환 가능한 코드란 유지보수 하기가 어렵다
	- 디바이스나 환경 (eg. python. java 등의 버전, windows 버전 등)이 다양하고 계속 변화하기 때문에 이 조합만큼 유지하고, 변화할 때 마다 계속 코드가 돌아가게끔 만드는데는 많은 노력이 든다. 
- 호환성을 얻기 위한 두가지 접근 방식 : 합집합식, 교집합식
	- 합집합식 (union)
		- 여러버전에 대응되는 코드를 만들고 컴파일 등을 통하여 모두 동작되도록 하는 것
			- eg) '#ifdef windows' 
			- 많은 코드, 복잡한 코드 필요. 
			- 최신버전 유지 어렵고 테스트 어려움
	- 교집합식(intersection)
		- 변경 없이 돌아갈 수 있는 한가지 형태로 작성
		- 시스템 의존성은 프로그램과 하부시스템 사이에서 인터페이스 역할을 하는 하나의 소스 파일에 캡슐화 (eg. HAL ?  )
			- 효율성이 떨어질 가능성이 있으며 기능이 적어질 수 있음. 

- 정답은 없음. 
- 성능이 좋은 (= 기계어에 가까운) 프로그램을 만들기 위해서는 합집합식을 갈 수 밖에 없고, 그러다 보면 힘든 삶을 살 수 있음. (코드 변경량은 10줄, test 는 일주일 ??!!!  )
- ~~이런 환경의 다양성을 고려하면 애플 개발자들이 안드로이드 개발자 보다 훨 나은 삶을 살 수도 있음. (하지만 마켓 수수료가 비싸다던가 비싸다던가 비싸다던가... )~~

- 참고
	- 모바일 앱 개발의 경우 자체 보유 단말로 테스트 할 수도 있지만, 이런것이 힘들다 보니 테스트 센터를 방문, 기기를 대여하여 테스트 하는 것도 방법임. 
	- 글로벌 테스트베드
		- 홈페이지 : https://www.koreastartupinnovation.com/testbed
		- 보유 단말 목록 : https://www.koreastartupinnovation.com/_files/ugd/bbb2e0_6a422b3d3b2c4291bbbacc044c8fc66a.pdf
