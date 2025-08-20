## 목차
- [구조적 Modeling](#구조적-modeling)
	- [parameter](#parameter)
		- [defparam문](#defparam문)
		- [parameter 값 변경](#parameter-값-변경)
	- [생성문(generate-endgenerate)](#생성문generate-endgenerate)
		- [generate문](#generate문)
			- [종류](#종류)
			- [사용](#사용)
			- [Format](#format)
- [함수와 Task](#함수와-task)
	- [함수 VS Task](#함수-vs-task)
	- [Task](#task)
- [Complier 지시어](#complier-지시어)
	- [Compiler 지시어](#compiler-지시어)
	- [문자매크로](#문자매크로)
	- [\`timescale](#timescale)
	- [\`ifdef, \`else, \`elsif, \`endif, \`ifndef](#ifdef-else-elsif-endif-ifndef)
- [System Task](#system-task)
	- [Display System task](#display-system-task)
		- [$display](#display)
		- [$write](#write)
		- [$monitor](#monitor)
		- [$strobe](#strobe)
	- [File 처리 System Task](#file-처리-system-task)
		- [File type](#file-type)
		- [fdisplay, fwrite](#fdisplay-fwrite)
- [실습: Debounce 회로](#실습-debounce-회로)
	- [Reset IC](#reset-ic)
	- [Debounce 회로](#debounce-회로)

# 구조적 Modeling
## parameter
### defparam문
```verilog
	defparam
		m1.size =5,
		m1.dalay = 10,
		m2.size = 10,
		m2.delay = 7;

	// defparam에서 정의된 파라미터가 들어감
	m1 U_M0();
	m2 U_M1();
```

### parameter 값 변경
```verilog
	module name #(paramter = value)(port);

	name #(.parameter(value)) INSTANCE ();
```
## 생성문(generate-endgenerate)
### generate문
> Module을 생성함
> >⭐반복 횟수가 정해져야함!
#### 종류
- generate-for: 반복 생성문
- generate-if: 조건 생성문
- generate-case: case 생성문

#### 사용
- 여러개의 Instance를 생성할 때 사용
- module 안에서 사용

#### Format
```verilog
// 보통 generate문 밖에서 선언
	genvar i; // integer type: 0, 1 state를 가짐

	generate

	endgenerate
```
- genvar
  - loop index의 변수
  - integer type
    - state를 0, 1만 가짐
  - Synthesis시 존재하는 변수가 아님
    - eleboration동안에만 존재
> for문안에 always문 사용가능
```verilog
//Example
generate
	for() :LABEL
	begin
		always begin

		end
	end
endgenerate
```
- 반복문 LABEL
  - ⭐generate문 안에서 for문, if문, case문을 사용할 때, LABEL을 붙여야 Error가 나지 않는다
```verilog
//Example
genvar i;

generate
	for(): FOR
	begin

	end

	if(): IF
		begin

		end
	else: IF
		begin

		end	

	case () begin
		1 : begin : CASE

		end
		2 : begin : CASE

		end
		default : begin : CASE

		end
	end
endgenerate
```
- LABEL도 계층구조가 가능하다
  - ex) IF.변수
  - 주로 테스트벤치에서 많이 사용한다
  - System verilog에서는 지원하나, verilog에서는 종종 지원하지않는 합성기 존재

# 함수와 Task
> 주로 시뮬레이션에서 많이 사용

## 함수 VS Task

|   함수    |       Task        |
| :-------: | :---------------: |
| 리턴 1개  |     다수 리턴     |
| Zero 지연 | Non-Zero지연 가능 |

- 함수보다 Task를 사용하는게 쉬움
  - 함수는 제약조건이 많음;;

## Task
```verilog
//Format

task name(port)

endtask
```
- 시뮬레이션할 때 주로 사용

# Complier 지시어
## Compiler 지시어
- ' : grave accent
  - 해당 문자 뒤에 단어는 컴파일러 지시어를 의미함
- 종류
  - `include: C언어 include와 동일
  - `ifdef DEBUG endif: 개발 시 Debugging할 때 사용
  - `pragma: 강제로 값을 할당할 때, 고정값을 쓰고싶을때
    - 펌웨어할 때, 자주 사용

## 문자매크로
- 문자 매크로앞에 `를 반드시 붙여야함
```verilog

`define wordsize 32

reg [1: `wordsize] data;
```

## `timescale
```verilog
`timescale 단위/ 해상도
```

## \`ifdef, \`else, \`elsif, \`endif, \`ifndef
- 코드 관리용으로 많이 사용
  - 주로 디버깅할 때

# System Task
> System == 합성기

## Display System task
### $display
- printf 비슷
- 줄바꿈 가능

### $write
- 줄바꿈 안됨
  - 딱 한줄 출력
- 그냥 $display를 사용하자

### $monitor
- 시뮬레이션 값이 **바뀔때마다** 출력함

### $strobe
- moniotr와 기능은 동일
  - **그냥 monitor쓰자**

## File 처리 System Task
```verilog
integer name = $fopen("file_name");

$fclose(file_name);
```

### File type
- Text type
  - ASCII 코드로 저장
  - 데이터사이즈가 큼
    - 글자 하나 == 1Byte
- Binary type
  - binary code로 저장
  - 데이터 사이즈가 작음
  - 압축할 때 주로 사용

### fdisplay, fwrite
- fdisplay: Text로 저장할 때
- fwrite: binary파일로 저장할 때

# 실습: Debounce 회로
## Reset IC
- MCU나 내장 메모리가 있는 SOC는 Reset IC를 따로 쓴다
  - 전압이 불안정하면 Flash memory가 깨질 수 있다
## Debounce 회로
- Shift register(SIPO)를 통해 입력을 여러 타이밍동안 받음
  - F/F의 개수가 많을수록 정밀도 올라감
  - parallel output을 AND gate로 묶어 출력을 내보냄
- 상승 순간만 포착해서 내보내고싶음 --> 사람 손은 눌렀다 떼는 구간이 굉장히 긺
  - Edge detection 회로
