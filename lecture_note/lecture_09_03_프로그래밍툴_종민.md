### grep(global regular expression print) 텍스트 파일 내에서 특정 패턴과 일치하는 문자열을 검색하는 명령어입니다. 
정규 표현식을 사용하여 매우 강력한 검색 기능을 제공합니다.

``grep [옵션] '검색_패턴' [파일명]``  
``grep -rnw './' -e 'nvidia-l4t-core'``

- ``-r or --recursive`` : 현재 디렉토리와 모든 하위 디렉토리를 재귀적으로 검색합니다. 즉, 지정된 디렉토리 아래에 있는 모든 파일들을 검색 대상에 포함시킵니다.
- ``-n or --line-number`` : 검색 결과에 해당하는 각 행 앞에 파일 내의 줄 번호(line number)를 출력합니다. 
- -``w or --word-regexp`` : 패턴이 텍스트의 전체 단어와 정확히 일치할 때만 매칭되도록 합니다. 
- ``-e or --regexp=패턴`` : 검색할 패턴을 지정합니다. 이 옵션은 패턴이 특수 문자로 시작하는 경우나 여러 개의 패턴을 검색하고 싶을 때 유용합니다.


### awk는 텍스트 파일의 데이터를 조작하고 보고서를 생성하는 데 사용되는 프로그래밍 언어 및 유틸리티입니다.
#### 1970년대 후반에 Alfred Aho, Peter Weinberger, Brian Kernighan에 의해 개발되었으며, 이들의 성의 첫 글자를 따서 awk라는 이름이 붙여졌습니다.
<img src="https://upload.wikimedia.org/wikipedia/commons/0/0e/Alfred_Vaino_Aho.jpg" alt="Alfred Aho" width="auto" height="200"> <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b1/PeterWeinberger2009.jpg/640px-PeterWeinberger2009.jpg" alt="Peter Weinberger" width="auto" height="200"> <img src="https://patch.com/img/cdn/users/1045140/2012/02/raw/9fa8a60d251c0bc0462af02bf5a5f595.jpg" alt="brian kernighan" width="auto" height="200">

#### ``awk 'pattern { action }' input-file-names``  

#### ``awk '{print NR, $0}' filename.txt``  

- 텍스트 파일에서 모든 줄을 출력하되, 각 줄 앞에 줄 번호를 붙이고 싶습니다
- 여기서 NR은 현재 레코드의 번호(여기서는 줄 번호)를 나타내며, $0은 현재 레코드의 전체 텍스트를 나타냅니다.

``{ for (i = 1; i<=NF; i++) print $i }``  

입력 레코드가 ``"Apple Banana Cherry Date"`` 라면, 이 스크립트는 다음과 같이 출력합니다  
- Apple
- Banana
- Cherry
- Date


### Tcl에서 regsub 정규 표현식을 사용하여 문자열 내의 패턴을 찾아 다른 문자열로 대체하는 기능을 제공합니다.

#### ``regsub [옵션] {패턴} {입력문자열} {대체문자열} 변수명``
#### ``regsub "http://" $argv "" argv ;``

Perl (Practical Extraction and Report Language)은 1987년에 Larry Wall에 의해 만들어진 고급, 범용, 인터프리티드 프로그래밍 언어  
텍스트 처리에 강력하며, 웹 개발, 시스템 관리, GUI 개발, 네트워크 프로그래밍 등 다양한 분야에서 사용  
Perl 스크립트는 일반적으로 ``.pl`` 확장자를 사용  

$srt 변수에 저장된 문자열 내에서 '<'으로 시작하여 '>'로 끝나는 모든 부분(즉, HTML 태그들)을 찾아서 제거
#### ``$srt =~ s>[^>]*>//g;``

- s: 이는 substitute(대체) 패턴과 일치하는 부분을 찾아 다른 것으로 대체하라는 의미  
- '>: 이 경우에는 구분자로 사용 s 연산자의 기본 구분자는 슬래시(/)이지만, 다른 문자를 구분자로 사용할 수 있으며, 여기서는 >가 그 역할을 합니다. 이는 패턴 내부에 슬래시가 많을 때 유용하게 사용  
- [^>]*: 이 패턴은 '>'를 제외한 모든 문자에 대응합니다(^는 제외를 의미)  
- *는 앞의 문자가 0번 이상 반복될 수 있음을 나타냅니다. 즉, 이 부분은 '>'가 아닌 모든 문자열을 찾아내는 패턴입니다.
- //: 대체할 문자열입니다. 이 경우, 패턴과 일치하는 부분을 빈 문자열로 대체하라는 의미이므로, 태그를 제거하는 효과를 가집니다.
- g: 전역 검색을 의미하는 옵션입니다. 문자열 내의 모든 일치하는 부분을 찾아 대체하라는 의미입니다.
