CMake 정리 

작성 방식 
- 최상위 디렉토리에 CMakeList.txt 파일이 있어야 한다. 
- CMakeList.txt => MakeFile => 실행파일 순으로 생성된다. 
- CMakeList.txt 에는 아래의 내용이 들어가야 한다. 
	- cmake_minimum_required() : Cmake 프로그램의 최소 버전 
	- project() : 프로젝트 정보 
	- add_executable() : [실행 파일 이름] {[소스]...}
	- target_compile_options : [실행 파일 이름] [PUBLIC|PRIVATE|INTERFACE] {[컴파일 옵션]...}
		- 컴파일 옵션 
			- -Wall : 모든 경고 표시
			- -Werror : 경고는 컴파일 오류로 간주 
	- include 경로 지정
		- 가장 중요한 점은 절대 경로를 지정하지 말아야 한다는 것.
		- include <> : 헤더 파일들을 시스템 경로에서 찾음 
		- include "" : 현재 코드 위치 기준으로 찾음 
		- 경우에 따라서 라이브러리를 만드는 경우에는 헤더 파일을 다른 곳에 위치시켰을 때, 컴파일 시에 경로를 지정해줘야 한다. 
			- 최상위 디렉토리 안에 includes 디렉토리가 존재하고 있을 때 
				- target_compile_options(program PUBLIC ${CMAKE_SOURCE_DIR}/includes) 
		- ${CMAKE_SOURCE_DIR} : CMake에서 기본으로 제공하는 변수로 최상위 CMakeList.txt 의 경로를 의미한다.
		
- 라이브러리 만들기 
	* 라이브러리를 만드는 이유 : 라이브러리로 쪼개지 않고 하나의 거대한 실행 파일로 관리하게 되면, 코드가 바뀔 때마다 전체를 다시 컴파일 해야한다. 
						하지만 라이브러리의 경우 바뀌지 않은 부분은 컴파일 하지 않아도 되서 개발 속도가 빠르다. 
						라이브러리의 각 요소들을 전체로 묶어놨을 때보다 테스팅이 용이하다. 
	- 실행파일에서 참조하는 라이브러리들이 외부 라이브러리를 사용했을 경우 
		- 확인 사항
			- 외부 라이브러리들이 잘 설치되었는지 
			- 라이브러리가 각각에 맞는 외부 랑이브러리를 참조할 수 있도록 설정해야 함 
			- 실행파일을 만들때 라이브러리를 사용하도록 해야 함 
	- add_library() : [라이브러리 이름] [STATIC|SHARED|MODULE] {[소스]..} 라이브러리 파일 생성 
		- STATIC : 정적 라이브러리 
		- SHARED : 동적 라이브러리 
		- MODULE : 동적으로 링크되진 않지만 런타임시에 불러올 수 있는 라이브러리 
	- target_include_directories() : [라이브러리 이름] [PUBLIC|PRIVATE|INTERFACE] {[경로]...}
		
	e.g) target_include_directories(shape PUBLIC ${CMAKE_SOURCE_DIR}/includes)
		- shape를 컴파일 할 때 헤더 파일 검색 경로에 ${CMAKE_SOURCE_DIR}/includes 를 추가한다. 
		- shape를 참조하는 타겟의 헤더 파일 검색 경로에 ${CMAKE_SOURCE_DIR}/includes 를 추가한다. 
		=> program이 shape 라이브러리를 사용한다면, program을 컴파일 할 때 파일 검색 경로에 ${CMAKE_SOURCE_DIR}/includes 이 자동으로 추가된다. 
			
- compile option
	CMake에서 A 라이브러리가 B 라이브러리를 사용한다고 가정한다.
	- PUBLIC : A는 B의 컴파일 옵션들과 헤더 파일 탐색 디렉토리를 물려받는다.
	- PRIVATE : 물려받지 않는다.		
	e.g) target_compile_options(shape PRIVATE -Wall -Werror)
		- shape를 빌드할 때 -Wall -Werror 를 사용하고 싶지만, shape를 사용하는 그 외의 것들은 이 옵션을 강제하지 않는다. 
	- 실행 파일은 PUBLIC PRIVATE 여부가 중요하지 않다. 왜냐면, 실행파일은 다른 타겟을 참조할 수 없기 때문이다. (endpoint)
		
- 라이브러리 링킹 
	- add_subdirectory() : CMake가 추가로 확인해야 할 디렉토리 경로 지정한다. CMake 실행 시에, 해당 디렉토리 안의 CMakeList.txt를 실행하게 된다. 
	- target_link_libraries() : [실행파일 이름] [PUBLIC|PRIVATE|INTERFACE]{[라이브러리 이름] ...}
		- PUBLIC : 헤더파일과 구현 내부에서 모두 사용
		- PRIVATE : 내부 구현에서만 사용하고 헤더 파일에서는 사용하지 않은 경우 
		- INTERFACE : 헤더 파일에서만 사용하고 내부 구현에서는 사용하지 않은 경우
		* 구별이 필요한 이유 : shape를 사용하는 다른 라이브러리들이 불필요하게 pthread 와 같은 외부 라이브러리들을 링크하는 일을 막을 수 있다.
	
- 모듈
	- find_package() : 이미 정의된 CMake 파일을 읽어서 그 안에 정의된 함수, 매크로, 변수를 사용할 수 있게 해준다. 
					외부 라이브러리를 사용하기 위해 만들어진 모듈을 부르는데 사용된다.
	e.g) find_package(${PACKAGE_NAME}) :  CMAKE_MODULS_PATH 에서 $PACKAGE_NAME.cmake 파일을 찾거나, 
										CMAKE_INSTALL_PREFIX/lib/$PACKAGE_NAME 폴더에서 PACKAGE_NAMEconfig.cmake 파일을 찾아서 읽는다. 
	
- build 과정 
	- build 디렉토리를 생성한다. 
	- cmake .. : 최상위 디렉토리에 CMakeList.txt가 있기 때문에 .. 을 붙여줘야 인식이 가능하다. 
	- make
	- 실행 파일 생성 
	
	
- tips
	- Make 이외의 빌드 시스템 사용하기
		- default는 Make를 사용하지만 경우에 따라 다른 빌드 시스템을 사용할 수 있다. 
		- cmake .. -DCMAKE_GENERATOR="Visual Studio 15 2019"
		
출처: https://modoocode.com/332
