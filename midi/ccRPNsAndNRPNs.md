# RPN/NRPN 목록
`(MSB << 7) + LSB` 라 적혀 있으면 MSB가 큰 자리수의 7비트, LSB가 작은 자릿수의 7비트로 기능한다는 뜻이다.

## RPN
미디 표준이다.

### Pitch bend range(Pitch bend sensitivity) - `00 00`
Pitch bend 메세지의 최대/최소 범위를 결정한다.

#### Data entry 형식
`MSB` = 0 - 127(`0x00` - `0x7f`), 기본값 = 2, 설명 = 이거 1당 ±1key씩 범위가 늘어남
`LSB` = 무시됨

### Master fine tuning - `00 01`
해당 채널의 전체 음높이를 cent 단위로 조율한다. 1키 = 100cent이다.

#### Data entry 형식
`(MSB << 7) + LSB` = 0 - 8192 - 16383 (이 값을 x라 할 때, cent 단위로 변환한 값 = 100 / 8192 * (x - 8192), 기본값 = 0)

### Master coarse tuning - `00 02`
해당 채널의 전체 음높이를 1키 단위로 조절한다.

#### Data entry 형식
`MSB` = 40 - 64 - 88 (-24 - 0 - +24, 기본값 = 0)
`LSB` = 무시됨

### Modulation depth range - `00 05`
Modulation(`CC#1`) 파라미터로 인한 pitch modulation의 depth를 조절한다.(상세 구현 계획 없음)

#### Data entry 형식
`MSB` = 0 - 127(`0x00` - `0x7f`), 기본값 = ?, 설명 = 이거 1당 ±1key씩 조절됨
`LSB` = 0 - 127(`0x00` - `0x7f`), 기본값 = ?, 설명 = 이거 1당 ±100/127cent씩 조절됨(CC값 127 = 100cent가 되도록 함)

### RPN null - `7F 7F`
이후의 Data entry 메세지를 무시하도록 지시한다.

#### Data entry 형식
`MSB` = 무시됨
`LSB` = 무시됨

## NRPN
미디음원 자체 기능이다. 근데 sc 8820이랑 mu2000이랑 비슷한 게 되게 많아서 좀 쓸어왔다.

### Soundfont 2 표준 관련
Soundfont 표준 쪽에 뭐가 좀 많다. 자세한 건 [여기](./soundfont2NRPNs.md)를 참조하라.

### Vibrato rate - `01 08`
`CC#76`의 값을 이 명령의 MSB 값으로 변경한다.

#### Data entry 형식
`MSB` = `CC#76`의 값 형식과 완전히 같음
`LSB` = 무시됨

### Vibrato depth - `01 09`
`CC#77`의 값을 이 명령의 MSB 값으로 변경한다.

#### Data entry 형식
`MSB` = `CC#77`의 값 형식과 완전히 같음
`LSB` = 무시됨

### Vibrato delay - `01 0A`
`CC#78`의 값을 이 명령의 MSB 값으로 변경한다.

#### Data entry 형식
`MSB` = `CC#78`의 값 형식과 완전히 같음
`LSB` = 무시됨

### Low-pass filter(LPF) cutoff - `01 20`
`CC#74`의 값을 이 명령의 MSB 값으로 변경한다.

#### Data entry 형식
`MSB` = `CC#74`의 값 형식과 완전히 같음
`LSB` = 무시됨

### Low-pass filter(LPF) resonance - `01 21`
`CC#71`의 값을 이 명령의 MSB 값으로 변경한다.

#### Data entry 형식
`MSB` = `CC#71`의 값 형식과 완전히 같음
`LSB` = 무시됨

### High-pass filter(HPF) cutoff - `01 24`
High-pass filter(HPF)의 cutoff 주파수를 설정한다.

기본값 64는 악기 자체에 설정된 기본값이 적용된 상태이다. 보통 이 상태에서는 특별히 잘려나가는 음역이 없다.

#### Data entry 형식
`MSB` = `CC#74`의 값 형식과 완전히 같음
`LSB` = 무시됨

### High-pass filter(HPF) resonance - `01 25`
High-pass filter(HPF)의 resonance의 정도를 설정한다.  
이 값을 기준으로 HPF의 Q 값을 산출한다.

기본값 64는 악기 자체에 설정된 기본값이 적용된 상태이다.

#### Data entry 형식
`MSB` = `CC#71`의 값 형식과 완전히 같음
`LSB` = 무시됨

### Drum inst. pitch coarse - `18 nn`
해당 드럼 악기의 음 높이를 1키 단위로 조절한다.

#### Data entry 형식
`MSB` = 1 - 64 - 127 (-63 - 0 - +63, 기본값 = 0)
`LSB` = 무시됨

### Drum inst. pitch fine - `19 nn`
해당 드럼 악기의 음 높이를 cent 단위로 조율한다.

#### Data entry 형식
`MSB` = 1 - 64 - 127 (-63 - 0 - +63, 기본값 = 0, NRPN 값을 x라 할 때 cent 단위로 변환된 값 = x * (100 / 64))
`LSB` = 무시됨

### Drum inst. volume level - `1A nn`
해당 드럼 악기의 음량을 조절한다.

#### Data entry 형식
`MSB` = 0 - 127 (0 = 그냥 0, 127 = 최대값, 기본값 = 악기에 따라 다름)
`LSB` = 무시됨

### Drum inst. panpot - `1C nn`
해당 드럼 악기의 좌우 음향을 조절한다.

#### Data entry 형식
`MSB` = 0 - 1 - 64 - 127 (Random - L63 - Center - R63, 기본값 = 악기 소리에 따라 다름: sf2 쪽에 있는 pan 설정이 기준임)
`LSB` = 무시됨

### Drum inst. reverb send level - `1D nn`
해당 드럼 악기의 reverb 이펙트 입력 volume을 설정한다.

#### Data entry 형식
`MSB` = 0 - 127 (기본값 = 악기 소리에 따라 다름: sf2 파일 형식 봐가면서 적당히 수정 예정)
`LSB` = 무시됨

### Drum inst. chorus send level - `1E nn`
해당 드럼 악기의 chorus 이펙트 입력 volume을 설정한다.

#### Data entry 형식
`MSB` = 0 - 127 (기본값 = 악기 소리에 따라 다름: sf2 파일 형식 봐가면서 적당히 수정 예정)
`LSB` = 무시됨

### Drum inst. delay send level - `1F nn`
해당 드럼 악기의 delay 이펙트 입력 volume을 설정한다.

#### Data entry 형식
`MSB` = 0 - 127 (기본값 = 악기 소리에 따라 다름: sf2 파일 형식 봐가면서 적당히 수정 예정)
`LSB` = 무시됨