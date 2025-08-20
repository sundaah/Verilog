# 🧭 Verilog – Basys3 통합 디지털 시스템

> Basys3 FPGA 보드 기반의 **디지털 시계(Watch & Stopwatch)**, **UART 통신**, **초음파 센서(HC-SR04)**, **온습도 센서(DHT11)** 를 **하나의 SoC 스타일 Top 모듈**로 통합한 학습·프로젝트 저장소입니다.

<p align="left">
  <img src="https://img.shields.io/badge/board-Basys3-blue" />
  <img src="https://img.shields.io/badge/lang-Verilog%20HDL-orange" />
  <img src="https://img.shields.io/badge/tool-Xilinx%20Vivado-6f42c1" />
  <img src="https://img.shields.io/badge/status-Active-green" />
</p>

---

## ✨ What’s Inside
- ⏰ **Digital Watch & Stopwatch**: 시·분·초 표시, 스톱워치 랩/리셋  
- 📡 **UART 통신**: PC–FPGA 간 명령/상태 교환 (보레이트 및 프로토콜 명세)  
- 📏 **HC-SR04 거리 측정**: 에코 펄스 폭 기반 거리 계산  
- 🌡️ **DHT11 온습도 취득**: 1-wire 타이밍 시퀀스 처리  
- 🔀 **Top-Level 통합**: 클럭 분주기, FSM 기반 모드 전환, 7-세그(FND) 멀티플렉싱  

---

## 🗺️ Repository Map
> FPGA 최종프로젝트

---

## 🧩 System Architecture
- **Clocking**: 100 MHz 입력 → 분주 → 1 kHz(스캔), 1 Hz(시계) 등 다중 도메인  
- **Mode FSM**: `WATCH` / `STOPWATCH` / `SENSOR` 모드 전환 (버튼/스위치/ UART 명령)  
- **Display**: 7-세그 4~6자리 멀티플렉싱, 디바운싱 포함 입력 처리  
- **I/O Abstraction**: 각 센서는 독립 모듈화 → Top에서 단순 와이어링  

---

## 🔢 Pinout (Basys3)

| 기능 | 핀 | 비고 |
|---|---|---|
| CLK100MHz | W5 | 기본 시스템 클럭 |
| BTN(CENTER) | U18 | 리셋/모드 |
| UART_TXD | A18 | PC로 출력 |
| UART_RXD | B18 | PC에서 입력 |
| TRIG(HC-SR04) | A14 | 초음파 트리거 |
| ECHO(HC-SR04) | A16 | 초음파 에코 입력 |
| DHT11_DATA | J1 | 1-wire 데이터 |
| FND | 공통 애노드 |
| FND | 세그먼트 |

---

## 🕒 Watch & Stopwatch
- **시계**: 1 Hz tick 기반 `HH:MM:SS` 증가, UART로 시간 설정 명령 지원(예: `T,12:34:56\n`).  
- **스톱워치**: 시작/정지/리셋, 랩(옵션). 1 kHz 내부 카운터 → 표시 단위 10 ms/100 ms 선택.  

## 📏 HC-SR04 Timing 요약
- TRIG 10 µs High → ECHO High 펄스 폭 ∝ 거리  
- 거리[cm] ≈ `ECHO_폭[µs] / 58`  
- 타임아웃/에러 플래그 제공, UART로 최근 결과 조회 가능  

## 🌡️ DHT11 Timing 요약
- Start Low ≥18 ms → DHT11 응답(80 µs Low/High) → 40bit 데이터  
- 비트 판정 임계치 파라미터화, 재시도/센서 미연결 검사 포함  

---

## 🧪 Simulation
- 각 모듈별 TB 제공(`tb_units/…`), Top 통합 TB에서 모드 전환/명령 시나리오 검증  

---

## 🧱 Design Notes
- 모든 비동기 입력은 **2-FF 동기화 + 디바운스**  
- 정수 기반 고정소수점 스케일링 → 나눗셈 회피  
- FSM 모듈화, 인터페이스 문서화  

---


