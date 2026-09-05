# AMS1117-3.3 Ultra-Compact LDO Power Module

KiCad를 활용하여 설계하고 JLCPCB를 통해 제작한 초소형 3.3V LDO 전원 레귤레이터 모듈입니다.  
5V~12V 입력을 받아 MCU, 센서 및 통신 모듈 구동에 필요한 저노이즈 3.3V 전원을 공급합니다.

---

## 1. 주요 사양 (Specifications)

| 항목 | 사양 (Specifications) | 비고 |
| :--- | :--- | :--- |
| **Topology** | Linear LDO Regulator | 저노이즈 3.3V 전원 출력 |
| **Input Voltage ($V_{in}$)** | DC 4.75V ~ 12V | 권장 입력 전압: 5V |
| **Output Voltage ($V_{out}$)** | DC 3.3V (±1.5%) | 고정 출력 (Fixed Output) |
| **Max Output Current ($I_{out}$)** | Max 800mA | 입력 전압 및 방열 조건에 따라 상이 |
| **PCB Dimensions** | 23.75 mm × 11.5 mm (1.6T) | 초소형 2-Layer FR-4 |
| **Surface Finish** | HASL (with lead) | 0805 수동 납땜성(Wettability) 최적화 |
| **Interface** | Wire Direct Soldering Pad | 실장 높이 최소화 및 기구 매립형 |

---

## 2. 부품 구성표 (BOM: Bill of Materials)

| Reference | Component / Part Number | Package | Description | Qty |
| :--- | :--- | :--- | :--- | :--- |
| **U1** | AMS1117-3.3V | SOT-223 | 3.3V 800mA LDO Regulator | 1 |
| **C1, C2** | 10µF / 25V (CL21A106KAFN3NE) | 0805 | 입·출력 평활 MLCC (DC 바이어스 마진 확보) | 2 |
| **R1** | 1kΩ ±1% 125mW (ERJ6ENF1001V) | 0805 | 상태 표시 LED 전류 제한 저항 | 1 |
| **D1** | Red-Orange SMD LED (599-0120-007F) | 0805 | 전원 인디케이터 LED ($I_f \approx 1.3\text{mA}$) | 1 |

---

## 3. 핵심 설계 포인트 (Engineering Points)

- **BOM 최적화 및 DC 바이어스 마진 확보**: 입·출력 평활 커패시터를 25V 내압의 10µF 0805 MLCC 1종으로 통합하여 자재 조달 효율을 극대화하고, DC 바이어스 전압 인가에 따른 실효 커패시턴스 감소 현상을 방지했습니다.
- **저전력 LED 회로 설계**: 3.3V 출력단에 1kΩ 저항을 직렬 매칭하여 약 1.3mA의 저전류 구동을 유도, 불필요한 대기 전력 소모와 눈부심을 줄였습니다.
- **방열 및 소형화 설계**: SOT-223 탭 하단에 넓은 구리 배선을 배치하여 열 방출 경로를 확보했으며, 커넥터 대신 와이어 직결 패드를 채택해 완제품 매립 시 부피를 최소화했습니다.

---

## 4. 디렉터리 구조 (Directory Structure)

```text
├── docs/               # 회로도, PCB 3D 렌더링, 실물 사진
├── gerber/             # JLCPCB 양산 발주용 거버 파일 (ZIP)
├── hardware/           # KiCad 프로젝트 파일 (.kicad_pro, .kicad_sch, .kicad_pcb)
└── README.md
```

---

## 5. 제작 및 검증 가이드 (Assembly & Verification)

### 5.1 실장 전 기판 검수 (Bare Board Inspection)
- **외관 검사**: 거버 파일과 실물 PCB를 대조하여 트레이스 단선, 비아 도금 상태, 솔더마스크 오픈 영역을 육안 점검합니다.
- **전원단 도통 검사**: 부품 실장 전 멀티미터 도통 모드로 $V_{in}\text{-}GND$ 및 $V_{out}\text{-}GND$ 간 단락(Short)이 없는지 확인합니다.

### 5.2 수동 SMD 납땜 (Hand Soldering)
간섭을 최소화하기 위해 **소형 수동 소자 $\rightarrow$ 대형 IC** 순서로 실장합니다.

| 순서 | 대상 부품 | 패키지 | 실장 요령 및 주의사항 |
| :---: | :--- | :--- | :--- |
| **1** | `R1` (1kΩ) | 0805 | 한쪽 패드 선납 후 핀셋으로 밀어 넣어 고정, 반대쪽 패드 납땜 |
| **2** | `C1, C2` (10µF) | 0805 | 극성이 없으므로 수평을 맞춰 대칭으로 실장 |
| **3** | `D1` (LED) | 0805 | 뒷면 캐소드(Cathode, $-$) 마킹 방향을 $GND$ 패드 쪽으로 정렬 |
| **4** | `U1` (AMS1117) | SOT-223 | Pin 1(GND) 가납으로 정렬 후 나머지 리드 납땜. 대형 방열 탭은 열을 충분히 가한 후 납 도포 |

### 5.3 전원 투입 및 성능 검증 (Power-on & Test)
1. **무부하 전압 측정**:
   - DC 파워서플라이를 **$5.0\text{V}$**, 전류 제한 **$100\text{mA}$**로 설정하여 인가합니다.
   - `D1` LED 정상 점등 여부와 출력단 전압을 멀티미터로 측정합니다.
   - **판정 기준**: $3.3\text{V} \pm 1.5\%$ ($3.25\text{V} \sim 3.35\text{V}$) 이내 유지 시 정상
2. **부하 테스트**:
   - 출력단에 부하 저항(약 $100\text{mA}$ 부하)을 연결하여 전압 강하(Load Regulation) 및 IC 발열을 모니터링합니다.
