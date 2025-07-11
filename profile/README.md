# 🌟 TermProject: LED Mode Controller with Device Driver
<img width="225" height="225" alt="image" src="https://github.com/user-attachments/assets/7e49f216-eeb1-46d4-80d1-d50ba632471c" />


> 코드 보기 https://github.com/Embedded-ES-101/TermProject
## 🧭 프로젝트 개요

4개의 스위치와 LED를 이용해 다양한 모드로 동작하는 시스템을 구현했습니다.  
커널 모듈과 디바이스 드라이버를 직접 작성하여 사용자 입력 및 인터럽트를 통해 LED를 제어합니다.

---

## ⚙️ 동작 흐름 요약

1. **4개의 모드**
   - **Mode 1**: 전체 LED 반복 점멸
   - **Mode 2**: 개별 LED 순차 점멸
   - **Mode 3**: 사용자가 수동으로 LED On/Off
   - **Mode 4**: 리셋 (모드 초기화)

2. **모드 전환 제한**  
   - 현재 모드를 종료하려면 반드시 **Mode 4 (Reset)** 를 눌러야 합니다.

3. **LED 제어 방식**
   - 스위치 입력 (Interrupt 기반)
   - 사용자 입력 (Device File I/O 기반)

---

## ⌨️ 사용자 입력 처리

- 사용자로부터 1~4 또는 0을 입력 받아 LED를 제어하거나 종료합니다.
- Mode 3 선택 시에는 반복 입력을 통해 개별 LED를 직접 제어할 수 있습니다.
- 입력값 유효성 검사와 에러 처리를 통해 안정적인 동작을 보장합니다.

---

## 🔌 커널 ↔ 유저 데이터 흐름

### memory_buffer
- 유저-커널 간 데이터 전달에 사용되는 전역 버퍼

### 주요 함수
- `my_driver_write`: 사용자 → 커널 (LED 제어 명령)
- `my_driver_read`: 커널 → 사용자 (필요 시 응답)
- `copy_from_user`, `copy_to_user` 함수 사용

---

## ⛓️ 모듈 내부 로직

### 📌 인터럽트 기반 흐름
- `request_irq()` 로 인터럽트를 등록
- `irq_handler()`에서 모드 플래그에 따라 분기 처리
- Mode 3이 켜져있을 경우, 다른 모드 실행은 제한되고 LED 직접 제어만 허용

---

## 🔀 모드별 동작 요약

### 🟢 Mode 1 - 전체 LED 점멸
- IRQ 발생 시 `module_led_mode_1()` 호출
- 타이머를 통해 모든 LED를 2초마다 On/Off 반복

### 🟡 Mode 2 - 개별 LED 순차 점멸
- `module_led_mode_2()` 호출 후 타이머로 순차 점등
- 배열 인덱스를 순환하며 LED를 하나씩 점멸

### 🔵 Mode 3 - 수동 제어
- `module_led_mode_3()` 실행 후, 각 스위치를 통해 개별 LED On/Off
- 비트 연산을 통해 LED 상태를 토글 (`XOR` 연산 사용)

### 🔴 Mode 4 - 리셋
- 모든 모드 플래그를 초기화하고 LED 상태를 OFF
- 다른 모드로 전환 가능하도록 시스템 리셋

---

## 🧹 개선 포인트

- 플래그 변수 간 중복 및 복잡도 증가
- 리셋 함수 최적화 및 플래그 통합 필요

---

## 👨‍🔧 제작자

| 이름     | 역할         |
|----------|--------------|
| 박준형   | 디바이스 드라이버 / 유저 인터페이스 구현 |
| 이정근   | 모듈 로직 설계 / 인터럽트 처리 / LED 제어 |

