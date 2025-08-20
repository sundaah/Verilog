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
> 실제 폴더/파일명은 저장소 구조에 맞게 조정하세요. (예: `FPGA 최종프로젝트`, `Verilog STUDY` 등)

---

## 🧩 System Architecture
- **Clocking**: 100 MHz 입력 → 분주 → 1 kHz(스캔), 1 Hz(시계) 등 다중 도메인  
- **Mode FSM**: `WATCH` / `STOPWATCH` / `SENSOR` 모드 전환 (버튼/스위치/ UART 명령)  
- **Display**: 7-세그 4~6자리 멀티플렉싱, 디바운싱 포함 입력 처리  
- **I/O Abstraction**: 각 센서는 독립 모듈화 → Top에서 단순 와이어링  

### Block Diagram (개략)
