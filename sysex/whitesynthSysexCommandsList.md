# whitesynth 확장 Sysex 명령 목록
파일이 길어질 거 같아서 여기서 따로 설명한다.

## 분류

| 파라미터 번호 | 설명 |
| :---: | --------- |
| `00 00 nn nn` | 시스템 설정(리셋 sysex라던가) |
| `01 00 nn nn` | 기본 설정 |
| `02 00 nn nn` | 기본 이펙트 관련 설정 |
| `02 01 nn nn` | Variation effect 관련 설정 |
| `03 0x nn nn` | 채널별 설정(x번): Multi effect 관련 설정 |

## 추가사항
설명을 어떻게 해석할지에 대해서는 [여기](./sysexCommands.md)의 "추가사항" 부분을 참조하라.

GM Sysex에서는 일부 little endian을 사용하는 경우가 있는 것과 달리 여기서는 기본적으로 big endian만을 사용한다.

## 목록

### 기본 설정

#### System reset - `00 00 00 7F`
기본 모드로 리셋을 한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 00 00 00 7F F7`
- 데이터: 없음

#### Master tune - `01 00 00 00`
cent 단위로 전체 음높이를 조율한다. 1키 = 100cent이다. GM의 Master fine tuning과 연동된다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 00 01 00 00 mm ll F7`
- 데이터: `mm ll`
  - `mm ll`: 0 - 8192 - 16383 (기본값 = 8192. 이 값을 x라 할 때, cent 단위로 변환한 값 = `100 / 8192 * (x - 8192)`)
    - 기본값: 0

#### Master volume - `01 00 00 01`
최종 출력의 음량을 설정한다. GM의 Master volume과 연동된다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 00 01 00 01 mm ll F7`
- 데이터: `mm ll`
  - `mm ll`: 0 - 16383
    - 기본값: 16383

#### Master key shift - `01 00 00 02`
전체 음높이를 1키 단위로 조절한다. GM의 Master coarse tuning과 연동된다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 00 01 00 02 kk F7`
- 데이터: `kk`
  - `kk`: -24 - 0 - +24 (40 - 64 - 88)
    - 기본값: 0

#### Master pan - `01 00 00 03`
최종 출력의 좌우 음향을 조절한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 00 01 00 03 mm ll F7`
- 데이터: `mm ll`
  - `mm ll`: L63(1) - Center(8192) - R63(16383)
    - 기본값: Center(64)

### 기본 이펙트 관련 설정

<!-- #### Remove reverberation - `02 00 00 00`
reverb/delay 이펙트로 인해 volume을 줄이거나 all sound off를 해도 잔향이 남는 경우가 있는데, 이러한 잔향을 깔끔히 없앤다.
synth 구조상 이걸 채널별로 하는 것은 불가능하다.

- 값 형태: `0000 0000`
- 값: 0 (`0x00`)
- 기본값: 없음 -->

#### Reverb on/off - `02 00 01 00`
reverb를 켜고 끈다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 00 kk F7`
- 데이터: `kk`
  - `kk`: 0(off), 1(on)
    - 기본값: 1(on)

#### Reverb room size - `02 00 01 02`

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 02 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383

#### Reverb width - `02 00 01 03`

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 03 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383

#### Reverb dampening - `02 00 01 04`

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 04 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383

#### Reverb pre-lpf cutoff - `02 00 01 05`

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 05 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383
    - 기본값: 16383

#### Reverb level - `02 00 01 06`
reverb 출력음의 level을 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 00 01 06 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383
    - 기본값: 8192

### Variation effect 관련 설정

#### Variation effect type - `02 01 1y 00`
해당 이펙터의 이펙트 종류를 설정한다. 이걸 설정하면 모든 파라미터의 값이 이펙트별 기본값으로 지정된다.  
종류에 대해서는 [여기](./multiEffects.md)를 참조하라.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 1y 00 kk ll mm F7`
  - `y` = 이펙터 번호 (`0x0` - `0xF`)
- 데이터: `kk ll mm`
  - `kk ll mm`: `0x00 0x00 0x00` - `0x7F 0x7F 0x7F`
    - 기본값: `0x00 0x00 0x00` (None)

#### Variation effect parameter - `02 01 2y pp`
해당 이펙터의 파라미터를 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 2y pp kk ll F7`
  - `y` = 이펙터 번호 (`0x0` - `0xF`)
  - `pp` = 파라미터 번호 (`0x00` - `0x2F`)
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383 (big endian 정수형. 각 바이트의 제일 앞 비트는 무시함)
    - 기본값: 파라미터에 따라 다름

#### Variation effect reverb send level - `02 01 30 00`
variation effect 출력에 대한 reverb send level을 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 30 00 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383 (big endian 정수형. 각 바이트의 제일 앞 비트는 무시함)
    - 기본값: 5160

#### Variation effect chorus send level - `02 01 30 01`
variation effect 출력에 대한 chorus send level을 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 30 01 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383 (big endian 정수형. 각 바이트의 제일 앞 비트는 무시함)
    - 기본값: 0

#### Variation effect delay send level - `02 01 30 02`
variation effect 출력에 대한 delay send level을 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 30 02 kk ll F7`
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383 (big endian 정수형. 각 바이트의 제일 앞 비트는 무시함)
    - 기본값: 0

#### Variation effect on/off - `02 01 40 0x`
해당 채널의 출력을 variation effect에 넘길지 설정한다. 설정하는 경우 작동 방식 상 reverb/chorus/delay send level은 variation effect에서 설정한 쪽으로 맞춰진다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 02 01 40 0x kk F7`
  - `x` = 채널 번호 (`0x0` - `0xF`)
  - `y` = 이펙터 번호 (`0x0` - `0xF`)
  - `pp` = 파라미터 번호 (`0x00` - `0x2F`)
- 데이터: `kk`
  - `kk`: 0(off), 1(on)
    - 기본값: 0(off)

### Multi effect 관련 설정

#### Multi effect type - `03 0x 1y 00`
해당 채널의 해당 이펙터의 이펙트 종류를 설정한다. 이걸 설정하면 모든 파라미터의 값이 이펙트별 기본값으로 지정된다.  
종류에 대해서는 [여기](./multiEffects.md)를 참조하라.

- 전체 Sysex 형태: `F0 7D 10 0B 15 03 0x 1y 00 kk ll mm F7`
  - `x` = 채널 번호 (`0x0` - `0xF`)
  - `y` = 이펙터 번호 (`0x0` - `0xF`)
- 데이터: `kk ll mm`
  - `kk ll mm`: `0x00 0x00 0x00` - `0x7F 0x7F 0x7F`
    - 기본값: `0x00 0x00 0x00` (None)

#### Multi effect parameter - `03 0x 2y pp`
해당 채널의 해당 이펙터의 파라미터를 설정한다.

- 전체 Sysex 형태: `F0 7D 10 0B 15 03 0x 2y pp kk ll F7`
  - `x` = 채널 번호 (`0x0` - `0xF`)
  - `y` = 이펙터 번호 (`0x0` - `0xF`)
  - `pp` = 파라미터 번호 (`0x00` - `0x2F`)
- 데이터: `kk ll`
  - `kk ll`: 0 - 16383 (big endian 정수형. 각 바이트의 제일 앞 비트는 무시함)
    - 기본값: 파라미터에 따라 다름