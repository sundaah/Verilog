## 목차

- [10000진 카운터](#10000진-카운터)
  - [Schematic](#schematic)
  - [Counter(Sequential logic)](#countersequential-logic)
    - [특징](#특징)
    - [Code](#code)
      - [Code 설명](#code-설명)
- [Clk Divider(클럭 분주기)](#clk-divider클럭-분주기)
  - [CLK Divider](#clk-divider)
    - [특징](#특징-1)
    - [Code](#code-1)
      - [방법1.Duty-cycle 50%일정](#방법1duty-cycle-50일정)
      - [방법2.Duty-cycle 조절 가능(혹은 Tick Generator)](#방법2duty-cycle-조절-가능혹은-tick-generator)



# 10000진 카운터

## Schematic

![image](https://github.com/user-attachments/assets/909664b3-265c-41ec-acf9-819aae60a0ec)<br>

> 참고) 1Hz 분주기는 필자가 FPGA상에서 카운트되는 것을 확인하기 위해 추가한 모듈이다

> > 본 실습에서는 Basys3의 System CLK(100MHz)을 카운터 클럭으로 이용했었음



## Counter(Sequential logic)

### 특징

- 클럭에 맞추어 카운트를 함

  - CLK 동기 --> Sequential Logi운

- 클럭의 Edge에 맞추어 카운트를 하고, Max(혹은 Min)값에 도달하면 초기값부터 다시 카운트 시작

### Code

```verilog

// 10000진 카운터(Up Counter)

	module cnt (
    input clk,
    input rst,

    output reg [13:0] cnt

);

    parameter num = 10000 - 1;

    always @(posedge clk or posedge rst) begin

        if (rst) begin

            cnt <= 14'h0;

        end else if (cnt == num) begin

            cnt <= 14'h0;

        end else begin

            cnt <= cnt + 1'b1;

        end

    end

endmodule

```

#### Code 설명

- reset이 들어오면 카운터값을 초기화함

- cnt값이 목표값(==num)에 도달하면 카운터값을 0으로 다시 초기화함

  - 숫자를 0부터 세기때문에 **목표값 - 1**을 해준다

- CLK의 Positive edge마다 cnt값을 1씩 증가시킨다

> @(posedge clk or posedge rst)으로 정의했기에, Asynchronous Reset을 사용중이다



# Clk Divider(클럭 분주기)

> ⭐CLK 분주기는 카운터에서 파생한다



## CLK Divider

### 특징

- 분주비만큼 기존 클럭을 카운트하여 원하는 주파수 클럭을 만들어낸다

- 코드에 따라 주파수 뿐만 아니라 **Duty Cycle**를 조절할 수 있다



### Code

#### 방법1.Duty-cycle 50%일정
```verilog
//방법1: Duty Cycle 50% 일정

module clk_divider (

    input clk,
    input rst,

    output reg oclk

);
    parameter num = 분주비 / 2;

    reg [($clog2(num) - 1):0] r_cnt;

    always @(posedge clk or posedge rst) begin

        if(rst) begin

            r_cnt <= 0;

            oclk <= 1'b0;

        end

        else if (r_cnt == num) begin

            r_cnt <= 0;

            oclk <= ~oclk;

        end

        else

            r_cnt <= r_cnt + 1;

    end

endmodule
```
![image](https://github.com/user-attachments/assets/d69dd036-d8f7-4f87-9e50-605f14375347)<br>
> 4분주기 예시

- Duty 50으로 4분주가 된 것을 확인할 수 있다

#### 방법2.Duty-cycle 조절 가능(혹은 Tick Generator)
```verilog
//방법2.Duty-cycle 조절 가능

module clk_divider (

    input clk,
    input rst,

    output reg oclk

);
    parameter num = 분주비 - 1;

    reg [($clog2(num) - 1):0] r_cnt;

    always @(posedge clk or posedge rst) begin

        if(rst) begin
            r_cnt <= 0;
            oclk <= 1'b0;
        end

        else if (r_cnt == num) begin
            r_cnt <= 0;
            oclk <= 1'b1;
        end

        else begin
            r_cnt <= r_cnt + 1;
            oclk <= 1'b0;
        end
    end

endmodule
```
![image](https://github.com/user-attachments/assets/52175d6f-ca5b-4e4e-b6b9-0f00896e99f6)<br>
> 4분주기 예시

- 4분주이지만 Duty가 균등하지 않다
  - Duty Cycle을 조절할 수 있는 Code
- Tick 발생기로도 사용됨
  - 주로 신호를 한번만 보낼 때에도 사용
  - Power에도 유리

>참고) Duty의 기준은 High를 기준으로 한다
