# 삼국지 2 3DS 한글 패치 작업 기록

마지막 갱신: 2026-09-10

## 작업 규칙

- 작업 루트: `E:\Codex_01\Sangokushi2`
- `Sangokushi 2 Original`은 읽기 전용이다.
- 수정 게임 파일은 추후 `Sangokushi 2 Patch`에 원본과 동일한 상대 경로로만 둔다.
- 최종 3DS 리패키징은 사용자가 수행한다.
- 번역자는 제어 코드·서식·원시 바이트를 건드리지 않는다. 에이전트가 보존·복원·삽입·검증을 맡는다.

## 완료한 작업

### 2026-09-10 v229-intermediate — Issue #203 신군주/신무장 설정 후속 및 주변 일본어 잔존 완료

- 로컬 `Github_Issue\Issue203.html`과 첨부 화면을 기준으로 #202 직후 `msgsec01.dat`의 신군주/신무장 설정 흐름을 확장 감사했다. 처음에는 direct110~124가 전부 Original 일본어 그대로였고, tail prompt direct183~197에도 신군주/신무장 선택·소재지·연령·SP무장 관련 일본어가 남아 있었다. 요청량을 한 번에 섞지 않고 source class별로 v227→v228→v229 세 단계로 나눴다.
- **v227 1차:** exact v226을 봉인하고 `msgsec01`의 direct96, 111~114, 185~191, 193~194 및 direct 사이 physical popup `0x15CA/0x15FA`를 처리했다. `신무장 설정 중지?` 계열의 short generic direct96은 사용자 요청대로 `초기화 할까요？`로 바꿨고, `16세 미만 무장은 / 16세에 등장합니다`, `상황에 따라 / 직책이 반영 안 될 수 있음`, 연령 증감 확인, 신군주/신무장 선택 prompt를 fixed-position으로 한글화했다. v227 SHA-256은 당시 `msgsec01=5F6A23A092A0DE77585F41C9F4A6D8E237634D4CEB1BBECE92B956E521F3A342`이며 정적 verifier/check PASS였다.
- **v228 2차:** direct115~124의 SP무장 설정 상단 설명 전체와 direct197 `사용 가능한 SP무장이 추가됨`을 추가로 한글화했다. `SP무장 등장 / 소재·신분 변경 / 초기화 / 확정`, 100세 이상 선택 불가, 게임 중 기능 커맨드에서 SP무장을 추가하는 설명까지 포함한다. 원 `ESC C9/C4/C1`, K/H control sequence와 줄바꿈 수를 보존했다. v228 `msgsec01` SHA-256=`FD5AC3AE09F1BC8CF77551BF8D59B5E8DE3123208390CFB9E149E1DD4AB47E1C`, verifier/check PASS.
- 이후 사용자 Citra Nightly 2104 확인에서 v227/v228 계열이 대부분 정상 적용되는 것을 확인했지만 두 후속 문제가 드러났다. 첫째 `연령 변경 없음 / 진행할까요？`는 의미는 전달되나 문장이 어색했고, 둘째 popup이 **`新君主 설정 중지할까요？`**로 표시되어 generic 한국어 문장 앞의 label만 일본어로 남았다.
- 첫 문제는 Original `年齢ｶﾞ変更ｻﾚﾃｲﾏｾﾝｶﾞ / ﾖﾛｼｲﾃﾞｽｶ？`의 의미상 “연령 변경을 취소할지”가 아니라 “변경하지 않은 채 진행할지”를 묻는 문장이다. 사용자 1안을 채택해 `msgsec01 0x15FA` 30B physical span을 **`연령 변경 없이 / 진행할까요？`**로 수정했다. 원 1개 줄바꿈, fullwidth `？`, separator `0x1618`은 유지했다.
- 두 번째 문제는 #158과 같은 **standalone label + generic `%s` popup 조립형**으로 확정했다. `msgsec01 direct97`은 이미 `%s 설정 중지할까요？`로 정상이며 수정하지 않는다. current v228 `code.bin`에서 raw `新君主`는 **`0x1CFFE4`에 유일 1건**, pointer table `0x1DC5CC→VA 0x002CFFE4`가 이를 가리킨다. 바로 다음 entries `0x1DC5D0/1DC5D4/1DC5D8`은 과거 v154에서 처리한 `신무장 / SP 무장 / 나이` sibling이다. string-pool relocation 때문에 immutable Original은 같은 label을 `0x1CFFD4`, pointer `0x1DC5CC→0x002CFFD4`에 보유하므로 **현재 위치와 Original 위치가 같다고 가정하지 않는다.** current 6B `新君主`를 exact-width **`신군주` 6B**로 바꾸고 pointer/NUL/code size를 보존했다. 기대 runtime은 `신군주 설정 중지할까요？`다.
- #203 주변 잔존 마무리로 direct110/148/183/184/195/196을 추가 수정했다. direct110은 `02 14 CA 66 C8`가 프로젝트 전체에서 유일한 opaque dynamic prefix라 전체 의미를 추측하지 않고 이 **5B를 byte-exact 보존**, 뒤의 명확한 literal `ﾉ / 配下ﾆｼﾏｽｶ？`만 `의 / 휘하로？`로 번역했다. direct148=`볼 장을 선택`, 183=`확인할 항목을 선택`, 184=`확인할 내용을 선택`, 195=`열람할 순위를 선택`, 196=`영웅전 미진행으로 열람 불가`로 정리했다. `랭`과 `틀`이 current effective font map에 없어 신규 donor를 만들지 않고 `순위`와 `영웅전`으로 우회했으며 direct195/196의 기존 K/H pair 수와 순서는 보존했다.
- 최종 재감사에서 `msgsec01` **direct87~197의 Original-exact direct residue는 0건**이다. `code.bin`/`msgsec01.dat`를 대상으로 raw `新君主`, `新武将`, `ＳＰ武将`, `新君主ﾄｽﾙ武将ｦ選ﾝﾃﾞｸﾀﾞｻｲ`, `設定ｽﾙ新君主ｦ選ﾝﾃﾞｸﾀﾞｻｲ`, `見ﾀｲ章...`, `知ﾘﾀｲ項目...`, `知ﾘﾀｲ内容...`, `閲覧ｽﾙﾗﾝｷﾝｸﾞ...`, `英雄ﾊﾞﾄﾙﾛｰﾄﾞ未ﾌﾟﾚｲ...`, `年齢ｶﾞ変更...`을 직접 검색해 모두 0건임을 확인했다. 이는 #203 감사 범위의 정적 residue 0을 뜻하며, 다른 화면/별도 runtime duplicate까지 전 게임에 일본어가 0이라는 일반화는 하지 않는다.
- exact v228 Patch 84파일을 `analysis\v229_issue203_completion_baseline\PatchSnapshot`에 봉인했다. v228 대비 변경 게임 파일은 **`ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 2개뿐**이다. 최종 SHA-256은 `code.bin=A78DA44F99519B93E5F2D7D7ECF40E9BFE280C01595029EFE072DA35A1A72839`, `msgsec01.dat=EEDF10022398203D69A5B59A13F30AE193BA0F19DC01F4292A1BF7FE83E9F0CE`; msgsec01 크기는 계속 **9183B**, 201개 direct pointer와 모든 대상 `05 05 05` separator 위치는 byte-exact다. font/PNG/G1T/다른 Message/Scenario는 v228과 동일하고 Original/Rebuild/Backup/Dummy update/패키징도 수정하지 않았다.
- builder → 독립 verifier → deterministic `--check` → verifier 재실행 모두 PASS. v229 신규 두 Citra 피드백 수정과 3차 주변 잔존 문구의 실화면 재확인은 pending이다. 권위 자료: `analysis\v229_issue203_completion_targets.json`, `analysis\v229_issue203_completion_report.json`, `tools\build_sangokushi2_v229_issue203_completion.py`, `tools\verify_sangokushi2_v229_issue203_completion.py`.

### 2026-09-10 v226-intermediate — Issue #202 신군주/신무장 등장 설정 상단 도움말 한글화

- 로컬 `Github_Issue\Issue202.html`의 8개 제보 항목(신군주 등장/도시 설정/초기화, 신무장 등장/소재·신분/연령 변경/초기화/확정)을 current v225 `msgsec01.dat`와 대조했다. `Extracted_Text\text_review.html` 및 `sangokushi2_translation.json`에는 대응 번역이 이미 상당수 존재했지만 `msgsec01_0049~0063` 부근은 Shift-JIS/fragment 경계가 깨진 legacy 번역도 포함돼 있어 그대로 runtime materialize하지 않았다.
- actual current source는 `msgsec01.dat` direct **87~93, 98~109 중 19개**의 fixed-position body다. 이 direct들은 v225에서도 Original 일본어 body를 그대로 가리켰고, direct94~97은 과거 #018 계열로 이미 한국어여서 이번 수정에서 동결했다.
- 제목은 `신군주 등장 / 도시 설정 / 초기화 / 신무장 등장 / 소재·신분 변경 / 연령 모두 변경 / 확정`으로 정리했다. 모든 제목의 원 **`ESC C9 ... ESC C1` 색상 제어코드**는 순서·개수를 그대로 보존했다. 설명의 기존 `ESC K/H`도 순서·개수를 보존하면서 한국어 visible text가 최종 H 상태에서 출력되도록 배치했다.
- 대표 설명은 `등장할 신군주를 / 선택합니다.`, `신군주가 등장할 도시를 / 설정합니다.`, `신무장의 소재지와 신분을 / 설정합니다.`, `신무장의 연령을 모두 변경합니다.` 등으로 fixed span 안에서 자연스럽게 축약했다. 과거 v12의 `괄=8CC7`은 current 안전 runtime map에 없으므로 `연령 일괄 변경`을 억지로 되살리지 않고 **`연령 모두 변경`**을 사용해 font 수정 없이 처리했다.
- 구현은 pointer relocation/repack 없이 각 direct의 기존 visible span만 교체하고 남는 바이트를 ASCII space로 채웠다. **201개 direct pointer 전체, 대상 `05 05 05` separator 위치, msgsec01 파일 크기 9183B를 모두 byte-exact 보존**했다. v225 #201 direct198~200과 기존 direct94~97도 byte-exact다.
- exact v225 Patch 84파일을 `analysis\v226_issue202_new_ruler_officer_help_baseline\PatchSnapshot`에 봉인했다. v225 대비 변경 게임 파일은 **`RomFS/Message/msgsec01.dat` 1개뿐**, 최종 SHA-256=`A04993E99B251CC071C5258D5EEC1624635D842886F4563B9F4B50731AAE6A29`다. code/font/PNG/G1T/다른 Message/Scenario는 v225와 byte-exact이며 Original/Rebuild/Backup/Dummy update/패키징도 수정하지 않았다.
- builder → 독립 verifier → deterministic `--check` → verifier 재실행이 모두 PASS했다. 정적 readback 19/19, pointer table byte-exact, separator 위치 byte-exact, 줄폭 최대 20 units 이하를 확인했다. Citra 실화면 확인은 pending이다. 권위 자료: `analysis\v226_issue202_new_ruler_officer_help_targets.json`, `analysis\v226_issue202_new_ruler_officer_help_report.json`, `tools\build_sangokushi2_v226_issue202_new_ruler_officer_help.py`, `tools\verify_sangokushi2_v226_issue202_new_ruler_officer_help.py`.

### 2026-09-10 v225-intermediate — Issue #201 등용 직후 장군/군사 자격 부족 direct 보완

- 로컬 `Github_Issue\Issue201.html`의 등용 직후 장군/군사 임명 자격 부족 화면을 current v224와 대조했다. 사용자가 예상한 대로 관련 번역은 이미 `Extracted_Text\sangokushi2_translation.json`의 `msgsec01_0092_000~002`에 존재했다. 기존 값은 `R이 되려면 무력이 부족합니다`(legacy fragment가 선두 `将軍` 3B를 놓친 추출 오류), `장군이 되려면 지휘 능력 등이 부족합니다`, `군사가 되려면 정치 또는 지력이 부족합니다`다.
- 하지만 current v224 전체 Patch를 실제 인코딩 바이트로 검색하면 위 완성 한국어 payload는 0건이었다. `msgsec01.dat` 마지막 live direct 198/199/200이 각각 `0x2309/0x2321/0x2340`을 가리키며, 세 body는 Original과 byte-exact인 `将軍ﾆﾅﾙﾆﾊ武力ｶﾞ足ﾘﾏｾﾝ`, `将軍ﾆﾅﾙﾆﾊ指揮能力ﾅﾄﾞｶﾞ足ﾘﾏｾﾝ`, `軍師ﾆﾅﾙﾆﾊ、政治ﾏﾀﾊ知力ｶﾞ足ﾘﾏｾﾝ`이었다. 따라서 원인은 **번역 DB에는 번역이 있으나 이 tail-direct class가 Patch에 materialize되지 않은 것**으로 확정했다.
- 사용자 Issue wording을 우선해 direct198=`장군이 되기에는 무력이 부족합니다`, direct199 sibling=`장군이 되기에는 지휘 능력 등이 부족합니다`, direct200=`군사가 되기에는 정치 또는 지력이 부족합니다`로 정리했다. 원 fixed span 21/28/30B에는 각각 33/41/43B target이 들어가지 않으므로 in-place 확장은 하지 않았다.
- `msgsec01`은 201-entry 16-bit direct table이므로 안전한 append+redirect를 사용했다. 새 payload는 old EOF `0x2361`부터 순서대로 append하고 direct198/199/200만 `0x2361 / 0x2385 / 0x23B1`로 변경했다. direct0~197 pointer와 기존 body, old Japanese physical body `0x2309/0x2321/0x2340` 자체는 byte-exact 보존한다.
- exact v224 Patch 84파일을 `analysis\v225_issue201_recruit_appointment_baseline\PatchSnapshot`에 봉인했다. v224 대비 변경 게임 파일은 **`RomFS/Message/msgsec01.dat` 1개뿐**, 크기 9057→9183B(+126B), SHA-256=`288DFB271ACC21614676F7ED48E91FF36097DFA283354822C31A43FE16535F04`다. v224 `code.bin` SHA-256=`B2B615422B2F68B133EFAC23CDA582EB95A2BBBBE856F96394C60A7069556644`, v223 `title_up.g1t` SHA-256=`17C8DEC766178EEB505088CE749216874F9DDBFCB6A275C91A281713A052D3C7` 및 나머지 82파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier PASS. Citra 실화면 확인은 pending이다. Rebuild·`0004000000174D00`·Dummy update·Backup·패키징·PNG/G1T는 수정하지 않았다. 권위 자료: `analysis\v225_issue201_recruit_appointment_targets.json`, `analysis\v225_issue201_recruit_appointment_report.json`, sealed v224 baseline, `tools\build_sangokushi2_v225_issue201_recruit_appointment.py`, `tools\verify_sangokushi2_v225_issue201_recruit_appointment.py`.

### 2026-09-10 v224-intermediate — 전투 장수 건강상태 `負傷/病気` 잔존 수정

- 사용자 Citra Nightly 2104 전투 장수 정보창에서 초상화 아래 붉은 **`負傷`**이 그대로 표시되는 runtime 증거를 확인했다. v99/v151에서 이미 번역한 `msgsec03 direct20=부상` 및 일반 health-state pointer table과 화면이 일치하지 않아, current v223 `code.bin`의 별도 duplicate source를 재감사했다.
- 일반 상태 source는 이미 정상이다: `0x1DC580→0x1CFA24=질병`, `0x1DC584→0x1CFDCC=부상`, `0x1DC588→0x1CFA74=건강`; `msgsec03 direct19/20`도 각각 `질병/부상`이다. 반면 current code에는 standalone raw `病気` 2건, `負傷` 2건이 별도로 남아 있었다.
- 실제 중복 literal은 `0x6420C=病気`, `0x64214=負傷`, `0x10EE58=負傷`, `0x10EE60=病気`이며 모두 4B + 4B NUL/alignment 구조다. 기존 검증된 동일 길이 한글 encoding으로 각각 **`질병 / 부상 / 부상 / 질병`**을 same-width 치환했다. 주변 ARM/data 및 문자열 뒤 NUL/alignment는 움직이지 않았다.
- health-state class를 `건강/질병/부상` 기준으로 전수 감사한 결과 code raw residue는 `病気/負傷/健康 = 2/2/0 → 0/0/0`이다. `msgsec02.dat 0x244` 부근의 문장형 old physical `...負傷しました!` 1건은 현재 157개 direct pointer 어느 것도 가리키지 않고 이번 단독 상태 라벨 owner로 증명되지 않아 byte-exact 보존했다.
- exact v223 Patch 84파일을 `analysis\v224_battle_health_status_baseline\PatchSnapshot`에 봉인했다. v223 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개 / 16B**, 최종 SHA-256=`B2B615422B2F68B133EFAC23CDA582EB95A2BBBBE856F96394C60A7069556644`다. v223 `title_up.g1t` SHA-256=`17C8DEC766178EEB505088CE749216874F9DDBFCB6A275C91A281713A052D3C7`와 나머지 83파일은 byte-exact 보존했다.
- builder → 독립 verifier → deterministic `--check` → verifier PASS. Citra 실화면 재확인은 pending이다. PNG/G1T/font/Message/Scenario와 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. 권위 자료: `analysis\v224_battle_health_status_targets.json`, `analysis\v224_battle_health_status_report.json`, `tools\build_sangokushi2_v224_battle_health_status.py`, `tools\verify_sangokushi2_v224_battle_health_status.py`.

### 2026-09-10 v223-intermediate — `title_up_002.png` 단일 이미지 갱신

- 필수 MD에서 최신 권위를 v222-intermediate로 확인하고, 현재 Patch 84파일이 `analysis\v222_issue199_runtime_source_fix_report.json`의 전체 manifest와 byte-exact임을 검증한 뒤 `analysis\v223_title_up_002_image_update_baseline\PatchSnapshot`에 봉인했다.
- 사용자가 명시한 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png` 한 장만 열었다. PNG는 RGBA 64×16, SHA-256=`10C55948D7B6AD9C23F8FA0493ACF03D6B95DECAD540EA411DDC3433D982D63B`이다. 이미지 생성·보정·리사이징은 하지 않았다.
- 최신 v222 `RomFS\StartMenu\title_up.g1t`의 공식 v1.1 texture index 2(`0x09 RGBA8`, 64×16) payload만 표준 encoder로 교체했다. 역디코드 결과가 source PNG와 pixel-exact이고, 3-texture 구조/header와 미지정 index 0·1 payload는 v222와 byte-exact다.
- v222 대비 실제 게임 변경 파일은 **`RomFS/StartMenu/title_up.g1t` 1개뿐**이고 최종 SHA-256=`17C8DEC766178EEB505088CE749216874F9DDBFCB6A275C91A281713A052D3C7`이다. v222의 #199 runtime-PASS `code.bin`과 나머지 83파일은 byte-exact다. builder → 독립 verifier → deterministic `--check` PASS. Citra 확인은 pending이다.
- Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. 권위 자료: `analysis\v223_title_up_002_image_update_targets.json`, `analysis\v223_title_up_002_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v223_title_up_002_image.py`, `tools\verify_sangokushi2_v223_title_up_002_image.py`.

### 2026-09-10 v222-intermediate — Issue #199 actual `code.bin` formatter 재분석/수정

- 사용자 Citra 재검수에서 v221을 적용했음에도 `조조님, 북해の원담が / 野に下ってしまいました`가 그대로 출력됐다. Patch와 Rebuild의 `msgsec05.dat` SHA가 모두 v221 `F71E8177...D147`로 일치하므로 **v221 수정이 배포되지 않은 것이 아니라, actual runtime source 판정을 잘못한 것**으로 확정했다.
- current v221 `code.bin` 전체에서 fullwidth `野に下ってしまいました`를 검색해 정확히 2곳을 찾았다. 첫 actual 화면 route는 `0x14A030=%s님, ` + `0x14A038=%s%sの%sが\n野に下ってしまいました`; ADR은 `0x149FC8→0x14A030`, `0x149FFC→0x14A038`이다. 이 조립 구조가 제보 화면의 `조조님, 북해の원담が`와 정확히 일치한다. sibling은 `0x14FB38=%s님, ` + `0x14FB40=%sの配下%sが野に下ってしまいました`, ADR `0x14FA58/0x14FA94`다.
- 첫 formatter의 source visible capacity는 33B다. 사용자 목표 **`%s%s의 %s(이)가\n하야했습니다`**는 28B이므로 `(이)가`를 그대로 유지하고도 5B 여유가 있다. v221에서 22B `msgsec05` span만 보고 존댓말이 불가능하다고 판단한 분석은 actual source가 틀렸기 때문에 잘못된 결론이었다. 사용자 fallback `이/가`는 필요하지 않았다.
- sibling formatter는 `%s의 %s(이)가\n하야했습니다` 26/34B로 함께 한글화했다. 두 formatter 모두 `%s` 개수, outer `%s님, ` prefix, ARM ADR instruction, 원 C-string allocation과 직후 code/data를 보존했다. target 뒤는 allocation 안에서 NUL로 종료하고, allocation을 확장하거나 code cave/relocation은 사용하지 않았다.
- current `code.bin`의 fullwidth `野に下ってしまいました` residue는 2→0건이다. exact v221 Patch 84파일을 `analysis\v222_issue199_runtime_source_fix_baseline\PatchSnapshot`에 봉인했으며 v221 대비 실제 게임 변경 파일은 **`ExeFS/code.bin` 1개 / 59B**뿐이다. 최종 SHA-256=`B0BBAB197068068175F5D23D8AF86934CB12540DAED1340172EDCF4E7A11F706`.
- v221 `msgsec05.dat`는 SHA-256=`F71E81773A64F4D96623DB2E32826574A9871420ECD781A99F2BB2D6CDC7D147`로 byte-exact 보존했다. font/PNG/G1T/Scenario 및 나머지 82파일도 v221 byte-exact다. builder → 독립 verifier → deterministic `--check` → verifier PASS. 이후 사용자 Citra Nightly 2104 실화면에서 **`조조님, 북해의 원담(이)가 / 하야했습니다`가 정상 출력되는 것을 확인해 v222 #199 actual `code.bin` formatter route는 runtime PASS**로 확정했다.
- `Sangokushi 2 Rebuild`는 규칙대로 수정하지 않았다. 검증 시 Rebuild code SHA는 여전히 v221 `F92B07D7...913B`, Patch code는 v222 `B0BBAB19...F706`으로 달라 실제 사용자 리빌드 전 상태임을 확인했다. 이미지 편집/생성은 수행하지 않았다. 권위 자료: `analysis\v222_issue199_runtime_source_fix_targets.json`, `analysis\v222_issue199_runtime_source_fix_report.json`, `tools\build_sangokushi2_v222_issue199_runtime_source_fix.py`, `tools\verify_sangokushi2_v222_issue199_runtime_source_fix.py`.

### 2026-09-10 v221-intermediate — Issue #199 하야 일본어 물리 경로 및 배반 계열 점검 (actual-source 판정은 v222에서 폐기)

- 필수 MD와 로컬 `Github_Issue\Issue199.html`/첨부 PNG를 확인했다. exact v220 Patch 84파일을 `analysis\v221_issue199_departure_betrayal_baseline\PatchSnapshot`에 봉인했다. `msgsec05.dat` direct 0/1은 v169부터 완전한 한글 EOF target을 가리키고 있었지만, Original 위치 `0x2E`, `0x47`의 `%sﾉ...野ﾆ下ｯﾃ...` 일본어 본문이 v220에도 byte-exact로 남아 있었다. #199 실화면은 direct readback과 달리 이 옛 물리 본문을 소비하는 우회 경로로 판정했다.
- runtime 우회 경로의 `하야함`은 사용자가 지나치게 짧고 어색하다고 지적했고, 장소/소속 `{북해}`도 필수라고 재확인했다. 따라서 `%s`를 생략한 시도는 폐기했다. 정식 current direct의 `재야로 내려갔습니다` 완전문은 그대로 보존하고, 물리 fallback은 모든 동적 문맥을 유지하는 `0x2E=%s의<NAME1>(이)가 / 하야했다` 22/22B, `0x47=%s의 <NAME1>(이)가 / 하야했다` 23/25B로 정리했다. 원하는 존댓말 `%s의 <NAME1>(이)가 / 하야했습니다`는 27B라 첫 22B span을 5B 초과한다. 정확한 문구를 적용할 수 있는 안전안은 사용자 도안 `하야/했습/니다` 14×14 합자 3개를 fresh local-only 한자 alias에 넣어 21B로 줄이는 방식이며, 카나 영역은 사용하지 않는다. 별도의 1인자 `code.bin 0x1841C0=%s 재야로 이동`은 #199의 2인자 문장과 호출 규약이 달라 재사용하지 않았다. 인접 복귀 본문 `0x63`은 `%s / <NAME1>(이)가 복귀` 17/20B로 유지했다.
- 사용자 후속 요청에 따라 배반 계열을 확대 감사했다. 전투 중 설득·매복 배반 `code.bin` 4개 C-string, `msgsec02` 10 direct, `msgsec05` 5 direct, 모반/반기 `msgsec08` 6 direct 등 25개 현재 경로는 이미 한글이며 일본어 잔존이 없었다. 다만 `msgsec05 direct7`의 옛 `0xE3` 물리 본문은 일본어 `埋伏武将...`가 그대로여서 `<NAME1>님,<NAME2>(이)가 / <NAME3>의 매복자였음` 32/33B로 미러링했다.
- 처음에는 같은 충성·배반 계열의 밀담 옛 본문 `0x197`도 예방 수정했으나, 사용자가 해당 `밀담 중` 화면은 이미 정상 한글임을 확인했다. 그 지적을 반영해 최종 v221에서는 `0x197..0x1BC`를 sealed v220과 byte-exact로 복구하고 direct14도 변경하지 않았다. **정상 출력이 확인된 인접 문구는 추측만으로 물리 미러를 수정하지 않는다**는 규칙을 프로젝트 규칙에 추가했다.
- `msgsec05.dat` 파일 크기 1231B, 22개 direct pointer/46B header, 모든 current direct target, 각 separator를 보존했다. v220 대비 실제 변경 게임 파일은 정확히 **`RomFS/Message/msgsec05.dat` 1개뿐**이며 최종 SHA-256=`F71E81773A64F4D96623DB2E32826574A9871420ECD781A99F2BB2D6CDC7D147`다. code/font/PNG/G1T/Scenario와 나머지 83파일은 v220 byte-exact다. builder → 독립 verifier → deterministic `--check` PASS, Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v221_issue199_departure_betrayal_targets.json`, `analysis\v221_issue199_departure_betrayal_report.json`, sealed baseline, `tools\build_sangokushi2_v221_issue199_departure_betrayal.py`, `tools\verify_sangokushi2_v221_issue199_departure_betrayal.py`다. Rebuild, `0004000000174D00`, Dummy update, Backup, 패키징은 수정하지 않았다.

### 2026-09-08 v220-intermediate — 수정된 `title_up_002.png` 1:1 재갱신

- 처음에는 v218을 기준으로 볼 수 있었으나, 사용자 지적에 따라 필수 MD를 다시 확인해 이후 포상 동작 수정이 반영된 **v219-intermediate가 실제 최신 권위**임을 정정했다. 이미지 주입 전 `tools\verify_sangokushi2_v219_reward_item_no_action.py`와 v219 report manifest를 통해 현재 Patch 84파일이 exact v219임을 검증하고 `analysis\v220_title_up_002_image_update_baseline\PatchSnapshot`에 봉인했다.
- 사용자가 다시 수정한 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png` 한 장만 입력으로 사용했다. PNG는 RGBA 64×16, SHA-256=`A25E3DE4131C807C033726AB263ACE3135CA5F381879C64D569D86B45CDA0EB6`이며 이미지 생성은 사용하지 않았다.
- 최신 v219 `RomFS\StartMenu\title_up.g1t`의 texture index 2 RGBA8 payload만 교체했다. 공식 v1.1의 3-texture 구조, G1T header, 미지정 index 0·1은 v219와 byte-exact이고 built texture 2 readback은 source PNG와 pixel-exact다.
- v219 대비 변경 게임 파일은 정확히 **`RomFS\StartMenu\title_up.g1t` 1개뿐**이다. 최종 SHA-256=`F78E2BD15E5FA15059175D7AFAB716D3ECB3AB510989AEDED3E0C6CF10FD4451`, 크기 593996B, Patch 84파일을 유지한다. v219 `code.bin` SHA-256=`F92B07D75722C84022A06489AAF4A2E7002243FB51CBA96D918066898A8C913B`와 포상 아이템 행동 유지 로직, font/Message/Scenario/Hex 및 나머지 82파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. Rebuild, `0004000000174D00`, `Dummy update`, Backup, 패키징 영역은 수정하지 않았다. 권위 자료는 `analysis\v220_title_up_002_image_update_targets.json`, `analysis\v220_title_up_002_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v220_title_up_002_image.py`, `tools\verify_sangokushi2_v220_title_up_002_image.py`다. Citra 실화면 확인은 pending이다.

### 2026-09-08 v219-intermediate — 포상 `아이템` 수여만 군주 행동완료 예외 처리

- exact v218 Patch 84파일을 `analysis\v219_reward_item_no_action_baseline\PatchSnapshot`에 봉인하고 current `code.bin`의 포상 처리 루틴을 추적했다. 포상 메인 경로에서 `0=금 / 1=아이템 / 그 외=서적`으로 분기한 뒤 `0x162110 -> 0x16215C`의 포상 전용 후처리로 합류하며, 후처리 진입 시 `0x16216C mov sb,r1`이 포상 종류를 `sb/r9`에 보존한다.
- 기존 `0x162190 bl 0xF0B24`는 종류와 무관하게 행동완료 setter를 호출한다. `0xF0B24`는 `r1=1`을 공급하고 shared body가 대상 무장 상태 구조체의 `+0x08` 바이트에 bit0을 OR한다. 이 setter body와 다른 caller는 수정하지 않았다.
- code cave/trampoline을 사용하지 않고 로컬 명령 재배치로 구현했다: `0x162190=cmp sb,#1`, `0x162194=blne 0xF0B24`. 기존 `ldr r4,[pc,#0x110]`는 `0x16219C=ldr r4,[pc,#0x108]`로 옮겨 동일 literal `0x1622AC`을 유지했고, 그 자리에 있던 `mov r6,#0`은 기존 NOP `0x1621C4`로 이동했다. `mov`는 condition flags를 바꾸지 않으므로 `0x1621C0 subs` → `0x1621C8 ble` 흐름도 그대로다.
- 결과적으로 `금(0)`과 `서적(2)`은 `BLNE`가 실행되어 기존처럼 행동완료가 되고, **아이템(1)만 setter를 건너뛴다.** 아이템 실제 지급, 충성도 변화 및 이후 포상 후처리는 기존 공통 경로를 계속 탄다.
- v218 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**, 실제 변경 바이트는 14B다. 최종 SHA-256=`F92B07D75722C84022A06489AAF4A2E7002243FB51CBA96D918066898A8C913B`. v218의 `title_up.g1t`와 나머지 83파일은 byte-exact 보존했다. 독립 verifier와 deterministic `--check`, Python compile이 PASS했다. 이후 사용자가 실제 Citra에서 **아이템 포상 후 군주 행동이 유지되는 것을 확인**했으므로 핵심 목표 경로는 runtime PASS다. 이 성공 사례는 특정 하위 명령만 행동 소모에서 제외할 때, 하위 종류를 보존하는 register를 조건으로 공통 행동완료 setter 호출만 skip하고 실제 효과/후처리 경로는 그대로 유지하는 재사용 패턴으로 기록한다. 금/서적은 기존처럼 setter를 호출하는 구조를 유지한다. Rebuild/Dummy update/`0004000000174D00`/Backup/패키징/이미지는 수정하지 않았다.
- 권위 자료: `analysis\v219_reward_item_no_action_targets.json`, `analysis\v219_reward_item_no_action_report.json`, sealed v218 baseline, `tools\build_sangokushi2_v219_reward_item_no_action.py`, `tools\verify_sangokushi2_v219_reward_item_no_action.py`.

### 2026-09-08 v218-intermediate — `title_up_002.png` 1:1 이미지 갱신

- 시작 전 `PROJECT_RULES.md`, `WORK_LOG.md`, `PATCH_HANDOFF.md`, `번역.md`와 v217 권위 report를 확인하고 `tools\verify_sangokushi2_v217_issue198_skill_name.py`를 실행해 현재 Patch가 exact v217-intermediate 84파일임을 검증했다. 전체 v217 Patch를 `analysis\v218_title_up_002_image_update_baseline\PatchSnapshot`에 봉인했다.
- 사용자가 명시한 입력은 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png` 한 장뿐이다. PNG는 RGBA 64×16, SHA-256=`BF76158B2F628F0BFD363F2E7CBDADC5F2E18313816A385F5877C2B9835F30E5`이며 AI/GPT 이미지 생성 없이 해당 파일을 그대로 사용했다.
- 최신 v217 `RomFS\StartMenu\title_up.g1t`를 기반으로 texture index 2의 4096B RGBA8 payload만 교체했다. 공식 v1.1의 texture 3개 구조와 G1T header, 미지정 texture index 0·1 payload는 v217과 byte-exact이며 built texture 2의 decode 결과는 source PNG와 pixel-exact다.
- v217 대비 변경 게임 파일은 정확히 **`RomFS\StartMenu\title_up.g1t` 1개뿐**이다. 최종 SHA-256=`1DE07427A7108AE9E6AFD51B88D7CA6BD02D08BBC78DA79B60D91A858AEEE270`, 크기 593996B, Patch 파일 수 84개를 유지한다. v217의 `code.bin`, font, 모든 Message/Scenario, Hex 이미지와 나머지 83파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. 사용자 관리 영역인 Rebuild, `0004000000174D00`, `Dummy update`, Backup, CIA/CXI/IPS에는 쓰지 않았으며 실제 리빌드는 사용자가 담당한다. 권위 자료는 `analysis\v218_title_up_002_image_update_targets.json`, `analysis\v218_title_up_002_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v218_title_up_002_image.py`, `tools\verify_sangokushi2_v218_title_up_002_image.py`다. Citra 실화면 확인은 pending이다.

### 2026-09-08 v217-intermediate — Issue #198 습득용 장문 특기명 `戦闘術` → `전투술`

- 작업 시작 전 사용자 지정 최우선 문서 `작업지시폴더\\이슈198_작업의견.md`를 먼저 읽고, `Github_Issue\\Issue198.html`의 제보와 current v216 Patch를 대조했다. 사용자가 지적한 것처럼 과거 v81에서 번역한 `code.bin 0x1DC300`의 **44개 compact 특기명 테이블**과 습득/장문 표시용 특기명은 별도 source class였다.
- immutable Original의 장문 전투특기 문자열 pool을 추적해 `戦闘術` actual source가 Original `0x1CEF5C`, current v216에서는 pool shift를 반영한 **`code.bin 0x1CEF6C`**임을 확정했다. 실제 pointer는 **`0x1DBE14 -> VA 0x002CEF6C`**이고 current v216에서도 같은 target을 직접 참조한다. `0x1CEF6B=FF` 선행 marker와 `0x1CEF72=NUL` terminator를 별도로 확인했다.
- 같은 contiguous 장문 특기명 블록을 전수 대조했다. current v216에서 `기마돌격 / 견제 / 단기돌진 / 흘리기 / 튕겨내기 / 일기토 / 기습 / 용병술 / 벽넘기 / 위협사격 / 일제사격 / 문사격 / 저격 / 일제 / 화시 / 논파 / 대화계 / 화계 / 함정 / 복병 / 상잔 / 진화 / 위령 / 장악술 / 생존술 / 고무 / 기도 / 도발 / 설득 / 치료 / 충차 / 유인` **32개 sibling은 이미 한국어로 정상**이었다. #198 제보와 동일하게 **`戦闘術` 한 항목만 일본어 raw가 남아 있었다.**
- exact v216 Patch 84파일을 `analysis\\v217_issue198_skill_name_baseline\\PatchSnapshot`에 봉인했다. source `戦闘術=90 ED 93 AC 8F 70` 6B를 current map의 **`전투술=8A 83 8E 5F 93 4A` 6B**로 exact-width 제자리 치환했다. 변경 바이트는 정확히 `0x1CEF6C..0x1CEF71` 6B이고 pointer, marker, NUL, code size는 그대로다.
- v216 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다. `code.bin 0x1DC300..0x1DC3AF`의 44개 compact 특기 pointer table 176B는 byte-exact이며, 위 32개 장문 sibling도 byte-exact 보존했다. font/Message/Scenario/PNG/G1T 및 모든 비-code Patch 파일은 v216과 동일하다. 최종 code SHA-256=`4EB0EB0E1559CB53BEA2D5A2EE0F5CAF817371F01E87E5527085BDBFB737052B`.
- 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. `戦闘術` raw residue는 current `code.bin`에서 0건이다. 이후 사용자가 실제 습득 화면에서 **`전투술`과 sibling `충차`가 모두 정상 표시되는 것을 확인**했으므로 #198 장문/습득 특기명 route는 **Citra runtime PASS**로 승격한다. Rebuild/Dummy update/`0004000000174D00`/패키징/이미지 자산은 수정하지 않았다. 권위 자료는 `analysis\\v217_issue198_skill_name_targets.json`, `analysis\\v217_issue198_skill_name_report.json`, sealed baseline, `tools\\build_sangokushi2_v217_issue198_skill_name.py`, `tools\\verify_sangokushi2_v217_issue198_skill_name.py`다.

### 2026-09-08 v216-intermediate — 독음 키보드 `ブ` shared-pointer 복원

- `작업지시폴더\Sangokushi2_BEU_Kana_Issue_Handoff.md`와 사용자 Citra 화면을 기준으로 v215 후속 원인을 재추적했다. `RomFS/EditBushou/edit_bushou_input_name.g1t`는 Original/Rebuild SHA-256 `AF127F5EBAC531D6063A9503E5CF74705C728C85EAF38E2E577B470547D74DC0`로 byte-exact이며 버튼 배경만 포함하므로 원인이 아니다.
- 실제 키보드 pointer table의 voiced-kana 행에서 `バ→0x2D0300`, `ビ→0x2D031C`, `ベ→0x2D0350`, `ボ→0x2D036C`인데, `ブ`만 `code.bin 0x1E3FB4→0x002CF784`로 메인 메뉴 문자열의 마지막 글자를 재사용하고 있었다. Original의 대상은 `セーブ` suffix `ブ\0`이었지만 v102 이후 같은 주소가 한국어 `세이브`의 `브\0`가 되어 키보드가 오염됐다.
- exact v215 Patch 84파일을 `analysis\v216_beu_kana_pointer_restore_baseline\PatchSnapshot`에 봉인했다. 종료된 `로드` label 뒤의 미참조 3B padding `0x1CF77D..0x1CF77F`에 `ブ\0=83 75 00`을 배치하고 `0x1E3FB4` pointer를 `0x002CF77D`로 redirect했다. 실제 변경 바이트는 `0x1CF77D`, `0x1CF77E`, `0x1E3FB4` 세 곳이며 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다.
- v215의 `브=9546/physical2908`, `세이브` code ref 7개와 msgsec10 ref, old physical3197 Original 복원, stock `ブ` physical505, font/fix_data/Message/Scenario/PNG/G1T/ARC는 모두 byte-exact 보존했다. 최종 code SHA-256=`C8082CF824CF9FFD3A39C4D1E39CF5E86DF4DF2AA65880D6FE440D6C92F876BA`.
- builder→독립 verifier→deterministic `--check`→verifier PASS. 사용자 지시에 따라 Rebuild, `0004000000174D00`, Dummy update, IPS/CIA/CXI는 빌드 대상에서 제외하며 실제 리빌드는 사용자가 담당한다. Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v216_beu_kana_pointer_restore_targets.json`, `analysis\v216_beu_kana_pointer_restore_report.json`, `tools\build_sangokushi2_v216_beu_kana_pointer_restore.py`, `tools\verify_sangokushi2_v216_beu_kana_pointer_restore.py`다.

### v215 후속 정정 — font-only 복원 판정 실패

- v215은 한국어 `브` relocation 자체는 보존할 가치가 있지만, old physical3197 복원만으로 독음 키보드 `ブ`가 돌아온다는 가정은 사용자 Citra에서 실패했다. 원인은 font/G1T가 아니라 Original의 `セーブ` suffix를 재사용한 `0x1E3FB4` shared pointer였다. 앞으로 원문 문자열 일부를 substring pointer로 공유하는 UI table을 수정할 때는 문자열 본문뿐 아니라 그 내부 모든 pointer xref를 전수 감사한다.

### 2026-09-08 v214-intermediate — Issue #197 무장 편집 `読み` → `독음`

- 로컬 `Github_Issue\\Issue197.html`의 무장 편집 독음 설정 화면에서 일본어 `読み`가 한국어 공용 확인문과 조립되어 표시되는 것을 확인했다. #158의 `名前→이름`과 같은 source class인지 current v213 `code.bin`을 직접 대조했다.
- actual owner는 **`code.bin 0x1CFFCC=読み`**, absolute pointer는 **`0x1DC57C→VA 0x2CFFCC`**다. 바로 앞 sibling `0x1DC578→VA 0x2CFA88`은 이미 한글화된 `이름`을 가리켜 #158과 같은 editor label table의 인접 항목임을 확인했다.
- 최신 target은 사용자 요청대로 **`독음`**이다. source `読み=93C782DD` 4B와 target `독음=90A48EF7` 4B가 exact-width이므로 pointer relocation 없이 `0x1CFFCC..0x1CFFCF` 4B만 제자리 치환했다. 기존 NUL, 두 pointer, code size는 보존한다.
- 질문 본문은 v162에서 이미 Citra 표시를 보정한 `msgsec01` 공용 **`%s 설정 중지할까요？`**를 그대로 재사용한다. 이번 revision에서 `msgsec01`은 수정하지 않았으며 기대 runtime은 **`독음 설정 중지할까요？`**다. `이름 설정 중지할까요？` sibling도 byte-exact 보존했다.
- exact v213 전체 Patch 84파일을 `analysis\\v214_issue197_reading_label_baseline\\PatchSnapshot`에 봉인했다. v213 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin` 1개 / 4B**, 최종 SHA-256=`B99FB679951A262C91366ABC86DBA38CEB31527DA7C61F42B3DE21BCBD2D8E1A`다. `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. v213 `title_up.g1t`, 모든 Message/font/Scenario/PNG/G1T/ARC는 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. Citra 실화면 확인은 pending이다. Rebuild/3DS/CIA 패키징과 이미지 편집은 수행하지 않았다. 권위 자료는 `analysis\\v214_issue197_reading_label_targets.json`, `analysis\\v214_issue197_reading_label_report.json`, sealed baseline, `tools\\build_sangokushi2_v214_issue197_reading_label.py`, `tools\\verify_sangokushi2_v214_issue197_reading_label.py`다.

### 2026-09-08 v213-intermediate — `title_up_002.png` 단일 이미지 리빌드

- MD와 current Patch manifest를 대조해 실제 최신 기준이 **v212-intermediate 84파일**임을 확인했다. exact v212 전체 Patch를 `analysis\v213_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고 최신 Patch의 3-texture `RomFS/StartMenu/title_up.g1t`를 base로 사용했다.
- 사용자가 명시한 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 texture index 2(64×16, type 0x09 RGBA8)에 1:1 반영했다. 다른 PNG는 수정하지 않았고 이미지 생성은 사용하지 않았다.
- G1T의 header·3-texture 구조·파일 크기와 비대상 index 0·1 payload는 v212와 byte-exact다. source codec identity 및 완성 Patch 역디코드 pixel-exact를 통과했다.
- v212 대비 변경 게임 파일은 정확히 `RomFS/StartMenu/title_up.g1t` 1개이며 Patch 파일 수는 84개다. v212의 code/font/Message/Scenario/Hex 이미지와 모든 비대상 파일은 byte-exact다. 최종 G1T SHA-256=`F4F228B1559E0137220B29CFE959B0F38855FA1731042F9D26AC4E6EB08443DF`.
- `PROJECT_RULES.md`의 Dummy Update 동기화 규칙에 따라 `Dummy update\RomFS\StartMenu\title_up.g1t` 입력도 Patch와 동일 SHA로 동기화했다. Dummy의 code/ExHeader/icon/RSF 및 기존 CIA/CXI/update.romfs 패키징 산출물은 byte-exact이며, 패키징 재빌드는 사용자 담당으로 실행하지 않았다.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v213_title_up_002_image_update_targets.json`, `analysis\v213_title_up_002_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v213_title_up_002_image.py`, `tools\verify_sangokushi2_v213_title_up_002_image.py`다.

### 2026-09-08 v212-intermediate — Issue #195 v211 fixed-copy NUL side-effect hotfix

- 사용자 Citra 실화면에서 v211 적용 후 **`역적 + 이전 버퍼 잔여문 + 조조를 토벌해 / 짐의 고통을 끝내라`** 형태의 새 side-effect가 확인됐다. 따라서 v211의 source 판정은 유지하되 `역적 ` 5B + NUL 이동 구현은 runtime 실패로 폐기했다.
- ARM caller를 다시 디스어셈블했다. `0x1671E4`가 호출하는 `0x0F55B4`는 일반 `strcpy`가 아니라 `ldr/str` 4B + `ldrb/strb` 1B로 **정확히 5B만 복사하는 helper**다. 원래 `逆賊`은 4B + NUL 1B라 copy contract 안에서 종결되지만, v211의 `역적 `은 5번째 복사 byte가 space가 되어 NUL이 destination buffer에 복사되지 않았다. 이어지는 `strcat(동적 군주명)`이 shared buffer의 이전 내용까지 스캔한 것이 이상한 문구 삽입의 직접 원인이다.
- v212는 `0x167414..0x167417=역적(8AFB88E5)`을 그대로 유지하고 **`0x167418` 한 바이트만 `20→00`으로 되돌려 NUL을 copy5 범위 안에 복원**했다. v211 대비 실제 game diff는 `ExeFS/code.bin` 1개/1B뿐이며 최종 SHA-256=`AD59A97FA70C005E8EAB7D577DCCA02A48AD285B5117D40C02FEFCA6AAC27D67`; Dummy update code도 동일 SHA다. Message/font/PNG/G1T/ARC는 v211 byte-exact다.
- 기대 runtime은 **`역적<NAME1>를 토벌해 / 짐의 고통을 끝내라`**다. 자연스러운 공백 1B는 현재 5B copy contract 안에는 들어가지 않는다. 이 경로는 포인터를 긴 문자열로 redirect해도 helper가 5B만 복사하므로 해결되지 않으며, 공백을 꼭 넣으려면 별도 ARM 조립 로직 변경을 독립적으로 설계·검증해야 한다. 이번 hotfix에서는 안정성을 우선해 ARM 흐름은 건드리지 않았다.
- exact v211 Patch 84파일을 `analysis\\v212_issue195_fixedcopy_hotfix_baseline\\PatchSnapshot`에 봉인했다. builder → 독립 verifier → deterministic `--check` → verifier 모두 PASS. common review는 live v212 **2241행**에서 `C437=역적<NAME1>를 토벌해 / 짐의 고통을 끝내라`, Xiandi review는 live v212 **39행**으로 재생성/검증 PASS했다.
- 권위 자료: `analysis\\v212_issue195_fixedcopy_hotfix_targets.json`, `analysis\\v212_issue195_fixedcopy_hotfix_report.json`, `tools\\build_sangokushi2_v212_issue195_fixedcopy_hotfix.py`, `tools\\verify_sangokushi2_v212_issue195_fixedcopy_hotfix.py`. Rebuild/3DS 패키징 및 이미지 편집은 수행하지 않았다.

### 2026-09-08 v211-intermediate — Issue #195 헌제 밀사 `逆賊` 혼입 source 수정 (공백/NUL 구현은 runtime 실패로 폐기)

- 로컬 `Github_Issue\\Issue195.html`의 실화면 **`逆賊조조를 토벌해 / 짐의 고통을 끝내라`**를 current v210과 대조했다. 헌제 리뷰에서 `M08-B112=역적 <NAME1>를 토벌하여 / 짐의 고통을 끝내라`가 정상으로 보였던 것은 **다른 Message duplicate**였고, `C064`도 actual runtime 완문이 아니라 suffix **`를 토벌해 / 짐의 고통을 끝내라`**만 노출한 행이었다.
- actual code runtime은 세 조각을 조립한다: **`0x167414=逆賊` short prefix + 동적 군주명 + `0x167420=C064 suffix`**. same function의 ADR `0x1671E0→0x167414`, `0x167204→0x167420`을 확인했다. 따라서 기존 리뷰가 prefix를 inventory에 포함하지 않아 false negative가 발생한 것이 root-cause다.
- source `逆賊=8B7491AF`는 4B이고 current Korean map의 `역적=8AFB88E5`도 4B다. 다만 source 직후 `0x167418..0x16741B`가 4B zero alignment padding이고 다음 literal word는 `0x16741C`부터이므로, 자연스러운 출력 **`역적 조조를...`**을 위해 zero padding 1B를 안전하게 빌려 `역적 ` 5B + NUL at `0x167419`로 적용했다. `0x16741A..0x16741B`와 `0x16741C` 다음 literal word, 두 ADR, suffix는 byte-exact 보존한다.
- exact v210 Patch 84파일을 `analysis\\v211_issue195_xiandi_rebel_prefix_baseline\\PatchSnapshot`에 봉인했고 v210 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin` 1개**, 실제 diff는 `0x167414..0x167418` 5B다. 최종 code SHA-256=`88898285B50F1D2910A946DFF8B485D0F0DF3C67A8B8FD3B3E1751DBB822A3F2`; `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. 모든 Message/font/PNG/G1T/ARC는 v210 byte-exact다.
- 동일 `逆賊` raw를 전체 runtime 파일에서 감사했다. v211 후 **active code residue는 0건**이다. `msgsec00.dat@0x19BC`는 direct17~18 사이의 `04/02` embedded command/data block 안에 있고 해당 block을 가리키는 direct header가 없어 사용자 대사 owner로 증명되지 않았다. `msgsec16.dat@0xD3`는 49-word runtime pointer table이 가리키지 않는 old physical body이며 뒤쪽에 한국어 `역적` copy가 이미 존재한다. 둘은 증거 없이 수정하지 않고 비-live/stale 후보로 기록한다.
- `common_dialogue_review_v180`은 기존 C000~C436을 유지하고 **C437=`역적 <NAME1>를 토벌해 / 짐의 고통을 끝내라`** composed route를 후첨했다. live v211 verifier PASS: **2241행(code438 / formal957 / binary846)**.
- `xiandi_dialogue_review`은 v211 권위로 재생성했다. C437을 `헌제 화자 강한 후보`로 추가하고 C064는 `같은 이벤트 문맥 / suffix fragment`로 명시해, 앞으로 prefix가 일본어면 full composed row에서 바로 검출되도록 바꿨다. verifier PASS: **39행 / 강한 후보 4 / 기존 화자미상 일본어 잔존 13행**, HTML SHA-256=`B093510867FB4830DD505E8FCF7488A13E9A4E79F57414961E96C5840CC04A41`.
- `open_common_dialogue_review_v180.bat`과 `open_xiandi_dialogue_review.bat`은 둘 다 current Patch 기반 generator/verifier를 사용하도록 **v211까지 동기화 확인**했다. 브라우저/이미지 편집은 열지 않았고 Rebuild/3DS 패키징도 실행하지 않았다.
- 권위 자료: `analysis\\v211_issue195_xiandi_rebel_prefix_targets.json`, `analysis\\v211_issue195_xiandi_rebel_prefix_report.json`, `tools\\build_sangokushi2_v211_issue195_xiandi_rebel_prefix.py`, `tools\\verify_sangokushi2_v211_issue195_xiandi_rebel_prefix.py`.

### 2026-09-08 — v210 문서/리뷰 런처 동기화 완료 (게임 Revision 변경 없음)

- v210 #194 기록을 `PROJECT_RULES.md`, `WORK_LOG.md`, `PATCH_HANDOFF.md`, `번역.md`의 current authority에 반영했다.
- `common_dialogue_review_v180` verifier를 live v210에서 PASS 확인했고 C436 조립형 메뚜기 route가 포함됨을 확인했다. `xiandi_dialogue_review` generator/verifier의 hardcoded authority도 v209→v210으로 올리고 HTML을 재생성했다. 이후 같은 작업 턴의 #195 적용으로 두 리뷰는 다시 v211까지 승격했다.
- launcher 자체는 revision을 하드코딩하지 않고 current Patch를 읽는 구조를 유지한다. `open_common_dialogue_review_v180.bat`은 generator→verifier→read-only server, `open_xiandi_dialogue_review.bat`은 generator→verifier→HTML open 흐름을 그대로 유지하며 최신 verifier가 v211을 권위로 판정한다.

### 2026-09-08 v210-intermediate — Issue #194 메뚜기 피해 확산 팝업 `いなご` 잔존 수정

- 로컬 `Github_Issue\\Issue194.html`과 첨부 Citra 화면의 **`いなご 피해가 확산 중입니다`**를 기준으로 source를 재추적했다. v124 #102에서 번역했던 피해 formatter는 공식 v1.1 병합 후 current `code.bin 0x0B269C`에 **`%s 피해가 확산 중입니다`**로 정상 존재하고 caller `0x0B25FC`도 해당 formatter를 정확히 참조한다.
- 실제 일본어 잔존 owner는 formatter가 아니라 `%s` 인자였다. current `code.bin 0x15EB84`의 literal-pool pointer가 `0x0025D720`을 가리키며, 해당 `0x15D720` C-string은 immutable Update에서 재도입된 **`いなご`**다. 이 문자열은 6B visible + NUL/align 뒤 `0x15D728`부터 ARM code라 9B `메뚜기 떼`를 in-place 확장할 수 없다.
- current v209 string pool `0x1CFE6C`에는 이미 #118에서 확립된 **`메뚜기 떼`** 9B가 정상 존재하므로 새 문자열/폰트 donor를 만들지 않고 `0x15EB84` pointer word만 **`0x0025D720 → 0x002CFE6C`**로 redirect했다. 실제 byte diff는 pointer word 내부 3B뿐이다. old `いなご` literal 자체는 보존하되 이 runtime route에서 분리했다.
- exact v209 Patch 84파일을 `analysis\\v210_issue194_locust_damage_baseline\\PatchSnapshot`에 봉인했고 v209 대비 실제 게임 변경 파일은 정확히 **`ExeFS/code.bin` 1개**다. 최종 SHA-256=`6C64CF63FFC8A29FEF233776FC24B8F408FC4F9AFDC1C308C387AC53E64FE063`; `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. font/Message/PNG/G1T/ARC는 v209 byte-exact다.
- builder → 독립 verifier → deterministic `--check` 모두 PASS. 기대 runtime 조립문은 **`메뚜기 떼 피해가 확산 중입니다`**다. 이후 사용자가 Citra 실화면에서 **메뚜기 떼 표시가 정상 해결되었음을 확인**했으므로 v210 #194 pointer redirect 경로는 runtime PASS로 승격한다.
- `common_dialogue_review_v180`에서 이 경로가 누락된 원인은 기존 corpus가 개별 C-string 위주로 수집되어 **별도 literal-pool `%s` 인자 + formatter 조립 경로**를 행으로 만들지 않았기 때문이다. 기존 C000~C435 ID를 유지하고 신규 **`C436`**을 후첨하여 `いなごの被害が広がっています → 메뚜기 떼 피해가 확산 중입니다` 조립 source를 노출했다. live v210 verifier PASS: 총 **2240행(code437 / formal957 / binary846)**.
- Rebuild/3DS 패키징은 실행하지 않았고, 이미지 편집/PNG/G1T 수정도 수행하지 않았다. 권위 자료: `analysis\\v210_issue194_locust_damage_targets.json`, `analysis\\v210_issue194_locust_damage_report.json`, `tools\\build_sangokushi2_v210_issue194_locust_damage.py`, `tools\\verify_sangokushi2_v210_issue194_locust_damage.py`.

### 2026-09-07 — 헌제 대사 전용 자가점검 HTML 추가 (게임 Revision 변경 없음)

- `Extracted_Text\\xiandi_dialogue_review.html`과 `open_xiandi_dialogue_review.bat`을 추가했다. launcher는 실행 시 current Patch 기준으로 HTML을 재생성하고 독립 verifier PASS 후 브라우저로 연다.
- 3DS 대사 인벤토리에 speaker ID가 직접 없으므로 헌제 화자를 100% 확정한다고 주장하지 않는다. 대신 **朕(짐)+献帝 발신자**, **선제 유협(劉協) 양위 연속 이벤트**, 헌제 직접 언급 문맥을 근거별로 `헌제 화자 강한 후보 / 황제 화자 후보 / 같은 이벤트 문맥 / 헌제 언급`으로 분리한다.
- current v209 기준 총 **38행**: 헌제 화자 강한 후보 4행(`C064`, `M08-B112`, `SC-C097`, `SC-C099`), 황제 화자 후보 1행(`C290`), 나머지 관련 문맥 및 언급 행을 포함한다.
- 사용자가 지나가며 본 일본어 혼입 대사를 다시 찾을 수 있도록 current common review의 일본어 잔존 **13행**을 `화자 미상 일본어 잔존`으로 별도 추가했다: `M06-B000`, `M07-B067/068/082/154/160/169/177/191/192/193/194/201`.
- 헌제 강한 후보 4행 자체의 current v209 자동 점검에서는 일본어 잔존/한일 혼합/미번역 의심이 **0건**이었다. 대표 readback은 `역적 <NAME1>를 토벌하여 / 짐의 고통을 끝내라`, `하늘의 뜻... / 방법이 없군...`, `...네`다.
- generator=`tools\\generate_sangokushi2_xiandi_dialogue_review.py`, verifier=`tools\\verify_sangokushi2_xiandi_dialogue_review.py`, report=`analysis\\xiandi_dialogue_review_report.json`. 최종 verifier PASS, HTML SHA-256=`492E71348771FAF2D795E5FCDB17DEB18DCAE92553BA192D2C204FAE2D28DD72`.
- 이 작업은 검수 도구 추가만 수행했으며 Patch/Original/Rebuild/Backup, code/Message/font/PNG/G1T/ARC 및 게임 Revision을 추가 변경하지 않았다.

### 2026-09-07 v209-intermediate — Issue #193 상단 모드명 한글화

- 로컬 `Github_Issue\\Issue193.html`과 첨부 asset 2개를 기준으로 상단 모드명 owner를 추적했다. `code.bin`에 **3개 연속 pointer**가 실제로 존재하며 `0x1AB3F0→오리지널`, `0x1AB3F4→3DS`, `0x1AB3F8→챌린지` 경로다.
- current v208 source는 `0x1CFD38=オリジナル` 10B, `0x1CFAB4=3DS` 3B, `0x1CFE7C=ﾁｬﾚﾝｼﾞｼﾅﾘｵ` 10B다. Korean target은 **`오리지널` 8B / `3DS` 3B / `챌린지` 6B**로 모두 각 fixed slot 안에 들어간다.
- `3DS`는 source와 target이 동일하므로 byte-exact 보존했다. `오리지널`과 `챌린지`만 same-slot shorter C-string으로 교체하고 pointer table 및 `code.bin` 크기를 유지했다. 별도 `0x1CFA4C=チャレンジシナリオ`는 해당 top-mode table의 owner가 아니므로 건드리지 않았다.
- exact v208 Patch 84파일을 `analysis\\v209_issue193_mode_names_baseline\\PatchSnapshot`에 봉인했고 v208 대비 실제 게임 변경 파일은 **`ExeFS/code.bin` 1개**뿐이다. 최종 SHA-256=`A9FCAB0775C6AB3C56CA74B7406B004729B6C14931460000BA95DF4BEE142794`.
- code 변경 revision 규칙에 따라 `Dummy update\\ExeFS\\code.bin`도 동일 SHA-256으로 동기화했다. font/Message/PNG/G1T/ARC는 v208 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier 모두 PASS. common dialogue review는 live v209 **2239행**으로 재생성했고 Issue193 code rows `C433=오리지널`, `C434=3DS`, `C435=챌린지`를 추가했다. battle review도 live v209 **520행** verifier PASS다.
- `Extracted_Text\\open_common_dialogue_review_v180.bat`은 기존 자동 재생성→검증→서버 실행 구조 그대로 v209를 인식한다. Rebuild/3DS 패키징 및 이미지 편집은 수행하지 않았다.
- report=`analysis\\v209_issue193_mode_names_report.json`, targets=`analysis\\v209_issue193_mode_names_targets.json`.

### 2026-09-07 — common dialogue review launcher 자동 갱신 보강 (게임 Revision 변경 없음)

- `Extracted_Text\\common_dialogue_review_v180.html`을 live `v208-intermediate` Patch 기준으로 다시 생성하고 독립 verifier PASS를 재확인했다. 현재 총 2236행(code 433 / formal Message 957 / current binary 846)이며 `msgsec04.dat SHA-256=861EE0A11402ABC5BCDFE047A92A8256AF73E19107612C8C5E421DF1EDB0FF0A`를 읽는다.
- `Extracted_Text\\open_common_dialogue_review_v180.bat`을 단순 서버 실행형에서 **최신 Patch 기준 HTML 재생성 → verifier 검증 → read-only 서버 실행**의 3단계 launcher로 갱신했다. 생성 또는 검증 실패 시 서버를 열지 않고 오류로 종료한다.
- 현재 `common_dialogue_review_v180.html SHA-256=EE92B9C85C50CF4AA3EB1E55EC4F6F38B61D391221DBBF671EE00DC5B2F06E4B`. Patch/Original/Rebuild/Backup 및 게임 Revision은 이 launcher 갱신으로 추가 변경하지 않았다.

### 2026-09-07 v208-intermediate — 상대군 전멸 대사 띄어쓰기 개선

- 사용자 제보 문구 `**군을전멸시켰습니다`의 실제 live source를 `common_dialogue_review_v180.html` 기준으로 추적했다. source fragment 권위는 `msgsec04_0020_000`, current physical/live row는 **`M04-B032 / RomFS/Message/msgsec04.dat / BYTE header[47] / pointer 0x6EA`**다.
- v207 current raw는 `<NAME1>군을전멸시켰습니다 `로 **22B fixed span**을 정확히 사용하고 있었으며 마지막 1B는 ASCII space padding이었다. 다음 direct pointer는 `0x703`, target block 뒤 `05 05 05` separator는 `0x700`이다.
- 사용자 제안의 바이트를 current mapping으로 직접 측정했다. 1안 `<NAME1>군을 전멸 시켰습니다.`는 **24B/22B(+2)**, 2안 `<NAME1>군을 전멸 시켰습니다`는 **23B/22B(+1)**로 overflow한다. 3안 **`<NAME1>군을 전멸시켰습니다`**는 **22/22B exact fit**이라 선택했다.
- 구현은 끝 padding 1B를 `군을` 뒤 공백으로 이동하는 same-span 수정이다. header 55 words, header[47]=`0x6EA`, header[48]=`0x703`, separator `0x700`, file size 2553B와 다음 physical block을 전부 byte-exact 보존했다. v207 대비 변경 게임 파일은 정확히 **`RomFS/Message/msgsec04.dat` 1개**, 실제 diff는 target span 내부 15B뿐이다.
- 최종 `msgsec04.dat SHA-256=861EE0A11402ABC5BCDFE047A92A8256AF73E19107612C8C5E421DF1EDB0FF0A`. `ExeFS/code.bin`, font, PNG/G1T/ARC, Dummy update code 및 다른 83개 Patch 파일은 v207 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier를 모두 PASS했다. `common_dialogue_review_v180.html`은 live **v208 / 2236행**, `battle_text_review_v180.html`은 live **v208 / 520행**으로 재생성·검증했고 `M04-B032` readback은 `<NAME1>군을 전멸시켰습니다`로 확인했다. `Extracted_Text/sangokushi2_translation.json`과 `text_review.html`의 `msgsec04_0020_000` 권위값도 fragment 조립을 위해 선두 공백을 포함한 ` 전멸시켰습니다`로 갱신했다.
- **리빌드는 수행하지 않았다.** `Sangokushi 2 Rebuild`, `san2.3ds`, 3DS/CIA 패키징은 사용자 전용이며 이미지 편집/PNG/G1T 수정은 하지 않았다.
- 권위 자료: `analysis\\v208_annihilation_spacing_targets.json`, `analysis\\v208_annihilation_spacing_report.json`, `analysis\\v208_annihilation_spacing_baseline\\PatchSnapshot`, `tools\\build_sangokushi2_v208_annihilation_spacing.py`, `tools\\verify_sangokushi2_v208_annihilation_spacing.py`.

### 2026-09-07 — `code_ips.bat` IPS 생성 유틸리티 추가 (게임 Revision 변경 없음)

- 작업 루트에 `code_ips.bat`을 추가했다. 더블클릭/실행 시 `Sangokushi 2 Original\ExeFS\code.bin`을 source, `Sangokushi 2 Patch\ExeFS\code.bin`을 target으로 사용해 루트의 `code.ips`를 생성한다.
- 외부 IPS 프로그램이나 Python 설치 없이 Windows 기본 `powershell.exe`만 사용하며 실제 생성 로직은 `tools\make_code_ips.ps1`에 둔다.
- classic IPS `PATCH ... EOF` 형식을 사용하고 24-bit offset / 16-bit record size 제한을 검증한다. 이 프로젝트의 `code.bin`은 fixed-size이므로 source/target 크기가 다르면 호환성 문제를 피하기 위해 생성 자체를 중단한다.
- 서로 가까운 변경 영역(동일 바이트 gap 최대 4B)은 한 IPS record로 안전하게 묶어 record overhead를 줄인다. 현재 v207 code 기준 테스트 결과는 **6,046 records / 실제 변경 1,488,704B / IPS payload 1,672,703B / IPS 1,702,941B**다.
- 생성 직후 IPS를 Original `code.bin`에 메모리상 다시 적용하는 독립 round-trip 검증을 수행하고, 결과 SHA-256이 현재 Patch `code.bin`의 **`BA9C6C398BB9D0B6A75ADE0F77112E909238A871A0A525496DAE2F5F1FFD08E1`**과 일치할 때만 최종 `code.ips`를 교체한다. 실패 시 기존 `code.ips`는 확정 저장하지 않는다.
- 테스트 출력은 Windows TEMP에서 생성·검증 후 삭제했다. 현재 게임 파일, Original/Patch/Rebuild/Backup, PNG/G1T/ARC는 수정하지 않았고 3DS 리빌드도 수행하지 않았다.

### 2026-09-07 v207-intermediate — M02-120 전투 대사 줄바꿈 개선

- 사용자 Citra 화면에서 `M02-120`의 첫 문장 **`적 부대에 거짓 전령을 보내라!`**가 폭을 넘겨 마지막 느낌표 `!`만 다음 줄에 떨어지는 것을 확인했다. 사용자가 요청한 대로 첫 구만 `적 부대에`→**`적에게`**로 축약하고 나머지 의미·문장 구조는 그대로 유지했다.
- actual live source는 `RomFS/Message/msgsec02.dat direct120`, pointer **`0x1A72`**, separator **`0x1AA7`**, current fixed span **53B**다. v206 source는 `적 부대에 거짓 전령을 보내라!\n적을 혼란시키는 것이다!` 53B, v207 target은 **`적에게 거짓 전령을 보내라!\n적을 혼란시키는 것이다!` 50B**다.
- pointer/header/separator/file size를 움직이지 않고 same-span in-place로 **50B target + ASCII padding 3B**를 적용했다. 다른 156 direct payload는 v206 byte-exact이며, v205에서 만든 별도 old-physical route `M02-P120`도 건드리지 않았다.
- exact v206 전체 Patch 84파일을 `analysis\\v207_m02_120_layout_baseline\\PatchSnapshot`에 봉인했다. v206 대비 변경 게임 파일은 정확히 **`RomFS/Message/msgsec02.dat` 1개**이며 최종 SHA-256=`4327C0EE9D7AD7B2BB54E33902793B59B7A0F1986FE88ACF82B257B1E5FE8D68`다. `ExeFS/code.bin`, font, PNG/G1T/ARC와 Dummy update code는 v206 byte-exact다.
- v207 builder → 독립 verifier → deterministic `--check` → verifier 모두 PASS했다. verifier는 direct120 pointer/separator/53B span, 다른 156 direct payload, v205 M02-P120 physical mirror, msgsec02 file size가 보존되었음을 확인한다.
- `battle_text_review_v180.html`은 stable v180 ID / live v207 **520행**으로 재생성했고 `M02-120` current readback을 새 문구로 갱신했다. HTML SHA-256=`F4D4D0C46A83DFA8683DEABA956CA11DCA8389EC8B592051114B1B1E7AF1BCB7`. common dialogue review도 live v207 **2236행** verifier PASS다.
- **리빌드는 수행하지 않았다.** `Sangokushi 2 Rebuild`, `san2.3ds`, 3DS/CIA 패키징은 사용자 전용 규칙을 유지한다.
- 권위 자료: `analysis\\v207_m02_120_layout_targets.json`, `analysis\\v207_m02_120_layout_report.json`, sealed v206 baseline, `tools\\build_sangokushi2_v207_m02_120_layout.py`, `tools\\verify_sangokushi2_v207_m02_120_layout.py`, 갱신된 battle/common review HTML·report.

### 2026-09-07 v206-intermediate — #190 위협사격 상태문 조사 표기 개선

- 사용자가 v205 Citra에서 **`OO / 움직일 수 없습니다!!`**가 정상 출력되는 것을 확인했다. 따라서 v205의 actual runtime source 판정(`code.bin 0x1257F4 / VA 0x2257F4`, refs `0x1257AC`, `0x1264A0`)은 runtime PASS로 유지하고, 이번에는 문구 자체만 자연스럽게 개선했다.
- 요청안 `OO는(은) / 움직일 수 없습니다!`는 formatter 기준 `%s는(은)\n움직일 수 없습니다!` **28B**로 visible allocation 27B를 1B 초과한다. 바로 뒤 `0x125810`부터 ARM code이므로 NUL을 뒤로 밀거나 1B를 침범하는 방식은 금지했다. relocation 없이 안전하게 들어가는 사용자 확정안 **`OO은/는 / 움직일 수 없습니다!`**를 선택했다.
- 최종 formatter는 **`%s은/는\n움직일 수 없습니다!` = 27B/27B exact fit**이다. target hex=`257388EF2F88EE0A8C948C7E8E902088E320895089A888AB88AC21`. 기존 terminator `0x12580F`, 두 live ref, 직후 ARM 16B는 byte-exact 보존했다. v205의 `%s\n움직일 수 없습니다!!`는 23B였고 남은 4B가 NUL padding이었으나 v206에서는 visible 27B를 정확히 채우고 동일 NUL을 사용한다.
- exact v205 전체 Patch 84파일을 `analysis\\v206_issue190_status_particle_baseline\\PatchSnapshot`에 봉인했다. v205 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이며 `RomFS/Message/msgsec02.dat`는 26개 physical mirror를 포함해 byte-exact 보존했다. 최종 SHA-256은 `code=BA9C6C398BB9D0B6A75ADE0F77112E909238A871A0A525496DAE2F5F1FFD08E1`, `msg02=EB0806E8975446A726CB4D77401DE14C9B8A0A2402D7AAB1FA4912AFC74401EC`다. font/PNG/G1T/ARC도 v205와 동일하다.
- `Dummy update\\ExeFS\\code.bin`도 v206 code와 동일 SHA로 동기화했다. builder → 독립 verifier → deterministic `--check` → verifier가 모두 PASS했고, verifier는 27B exact fit, 기존 NUL, 두 runtime ref, ARM 경계, v205 msgsec02 전체 보존을 확인한다.
- `battle_text_review_v180.html`은 stable v180 ID를 유지한 채 live v206 **520행**으로 갱신했다. `C190-STATUS` current readback은 `%s은/는 / 움직일 수 없습니다!`이며 verifier PASS, HTML SHA-256=`4FFA0D2D48C838E98E9388AD15D68C489693206DA9A0B1592D55159AE789EA70`. `common_dialogue_review_v180`도 live v206 **2236행(code433 / formal957 / binary846)**으로 재생성/검증 PASS했다.
- **리빌드는 수행하지 않았다.** `Sangokushi 2 Rebuild`, `san2.3ds`, 3DS/CIA 리패키징은 사용자 전용 규칙을 유지한다.
- 권위 자료: `analysis\\v206_issue190_status_particle_targets.json`, `analysis\\v206_issue190_status_particle_report.json`, sealed v205 baseline, `tools\\build_sangokushi2_v206_issue190_status_particle.py`, `tools\\verify_sangokushi2_v206_issue190_status_particle.py`, 갱신된 battle/common review HTML·report.

### 2026-09-07 v205-intermediate — #190 전투 중 일본어 잔존 / runtime source 분리 복구

- #190 첫 제보 `奮ｲ立ﾃ！ / ｺﾉ戦ｲ、勝ﾃﾙｿﾞ！！`를 current v204 `msgsec02.dat`와 대조했다. direct127은 이미 EOF `0x1B0E`의 한국어 **`분발하라! / 이 싸움은 이길 수 있다!!`**를 가리키고 있었고 모든 direct pointer 중 원 physical `0x0BF0`을 가리키는 값은 없었다. 따라서 이를 단순한 "direct pointer가 엉뚱한 일본어 위치를 가리킨다" 문제로 처리하지 않았다. Citra runtime이 old physical sequence를 소비하는 별도 전투 경로가 존재하는 것으로 분류하고 동일 전투 특기·상태변화 class를 전수 감사했다.
- 추가로 사용자가 제시한 위협사격 성공 일본판 화면의 정확한 문장은 **`馬超は / 身動きが取れません！！`**였다. 앞서 주변 `動揺` 문장을 이 화면과 직접 대응시킨 추정은 폐기했다. immutable Original 전체 검색으로 actual owner가 Message가 아니라 **`code.bin` hardcoded formatter `%sは\n身動きが取れません！！`**임을 확인했다. 공식 v1.1 병합 후 current 위치는 `0x1257F4 / VA 0x2257F4`, runtime refs는 **`0x1257AC`, `0x1264A0`** 두 곳이며 둘 다 같은 formatter를 참조한다. `msgsec04`의 반각 유사 문장은 현재 이 Citra 화면의 live owner가 아니다.
- code formatter는 **`%s\n움직일 수 없습니다!!`**로 변경했다. Original visible 27B에 target 23B가 들어가며 target 직후 즉시 NUL을 두고 남는 local bytes는 NUL padding으로 유지했다. caller의 ADR/LDR ref와 바로 뒤 ARM/data 경계는 그대로 보존했다. `꼼짝할 수 없습니다`는 current map에 `꼼/짝` 안전 alias가 없어 새 font donor를 만들지 않고 의미가 동일한 `움직일 수 없습니다`를 사용했다.
- `msgsec02`는 direct/header를 다시 redirect하지 않았다. Citra 제보와 같은 old-physical 소비 가능성이 있는 전투 특기·상태변화 구간을 감사해 **26개 old physical span**만 각각 Original 고정 capacity 안의 compact Korean mirror로 제자리 교체했다. 대상 direct index는 `42,48,89,100,120,122,124,125,127,128,130,131,132,133,134,135,137,138,139,140,141,142,143,144,146,147`이다. 대표 readback은 `M02-P127=분발하라! / 이길 수 있다!!`(24/26B), `M02-P100=성공! / 적이 동요했다`(19/21B), `M02-P089=<NAME1> / 분노 상태!`(14/19B)다. relocated EOF direct127의 장문 **`분발하라! / 이 싸움은 이길 수 있다!!`**와 direct100의 **`성공입니다 / 적이 동요하고 있습니다`**는 그대로 보존했다.
- exact v204 전체 Patch 84파일을 `analysis\\v205_issue190_battle_runtime_residue_baseline\\PatchSnapshot`에 봉인했고 v204 대비 실제 게임 변경은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec02.dat` 2개**다. 최종 SHA-256은 `code=239070217EA84E611999D72F7208C18C5FAE10E0D0D37791C99370593D24153F`, `msg02=EB0806E8975446A726CB4D77401DE14C9B8A0A2402D7AAB1FA4912AFC74401EC`. 157개 direct pointer/header, 기존 relocated direct payload, target `05 05 05` separator, msgsec02 파일 크기 8729B가 모두 v204와 동일 구조다. font/PNG/G1T/ARC와 비대상 Patch 파일은 v204 byte-exact이며 이미지 편집/생성은 하지 않았다.
- code 변경에 따라 `Dummy update\\ExeFS\\code.bin`도 현재 Patch code와 동일 SHA로 동기화했다. v205 builder → 독립 verifier → deterministic `--check` → verifier가 모두 PASS했다. verifier는 hardcoded 일본어 source 잔여 0, 두 runtime ref 보존, physical mirror 26개, direct/header 보존, Dummy code sync를 확인한다. 이후 사용자가 위협사격 hardcoded status 경로의 `OO / 움직일 수 없습니다!!` 정상 출력을 Citra에서 확인했다. 26개 physical mirror 각각의 개별 runtime 확인은 별도다.
- `battle_text_review_v180.html`은 stable v180 ID를 유지하면서 live v205 **520행**으로 재생성했다. 기존 493행에 `msgsec02-physical` 26행(`M02-Pxxx`)과 hardcoded formatter `C190-STATUS` 1행을 추가했으며 독립 review verifier PASS, HTML SHA-256=`9B7BBBA40E2382683A26FDB6DADCDDE68B03680F4BA1F79BA650F7FFD2FFADEB`다. `common_dialogue_review_v180` resolver도 v205 manifest/code hash를 지원하도록 확장하고 다시 생성·검증해 **2236행(code433 / formal957 / binary846)** PASS했다.
- **주의/정정:** 작업 중 에이전트가 실수로 `Rebuild_San2.bat`을 실행해 `Sangokushi 2 Rebuild`와 `san2.3ds`를 건드린 일이 있었다. 이는 프로젝트 규칙 위반이며 정식 작업 절차로 간주하지 않는다. **Rebuild 동기화 및 3DS/CIA 리패키징은 사용자 전용 작업**이고, 이후 에이전트는 Patch/검증/HTML/MD까지만 수행하며 `Sangokushi 2 Rebuild`, `san2.3ds`, 리패키징 명령을 실행하지 않는다.
- 권위 자료: `analysis\\v205_issue190_battle_runtime_residue_targets.json`, `analysis\\v205_issue190_battle_runtime_residue_report.json`, sealed v204 baseline, `tools\\audit_sangokushi2_v205_issue190_runtime_sources.py`, `tools\\build_sangokushi2_v205_issue190_battle_runtime_residue.py`, `tools\\verify_sangokushi2_v205_issue190_battle_runtime_residue.py`, 갱신된 `Extracted_Text\\battle_text_review_v180.html` 및 review verifier.

### 2026-09-07 v204-intermediate — #189 전투 중 기능 메뉴 `중단 / 게임종료` 한글화

- 로컬 `Github_Issue\\Issue189.html`과 첨부 Citra 화면을 현재 v203 Patch와 대조했다. 화면의 `中 断 / ゲーム終了`는 이미지가 아니라 `code.bin` 공통 UI C-string table의 실제 텍스트이며, 바로 옆 `BGM변경`과 `HEX ON`이 같은 table에 연속 배치되어 있다. PNG/G1T는 열거나 수정하지 않았다.
- 실제 runtime source는 **`code.bin 0x1CF8C8 = 中 断`**과 **`0x1CF8D0 = halfwidth ゲーム終了`**다. literal pointer owner도 각각 **`0x1DC268 -> VA 0x2CF8C8`**, **`0x1DC27C -> VA 0x2CF8D0`**로 확인했다. v203 전체에서 두 source signature는 각 1개이며, pointer를 움직이지 않는 fixed-slot 수정으로 처리했다.
- 최신 표기는 **`중단 / 게임종료`**다. `중단`은 current map에서 4B(`8A49 92AD`)라 8B local slot 안에서 즉시 NUL 종료하고 뒤 3B는 NUL padding으로 유지했다. 중앙정렬 UI에서 trailing space가 폭에 포함되어 위치가 틀어질 수 있었던 v157 회귀를 반복하지 않기 위해 `중단   `처럼 NUL 앞 공백 padding은 넣지 않았다. `게임종료`는 8B(`88A8 88A9 94FA 88CF`)로 원 visible 8B와 exact-width이며 기존 `0x1CF8D8` NUL은 그대로 보존했다.
- exact v203 전체 Patch 84파일을 `analysis\\v204_issue189_battle_function_menu_baseline\\PatchSnapshot`에 봉인했고 v203 대비 실제 게임 변경은 **`ExeFS/code.bin` 1개 / 13B**뿐이다. font, 모든 Message, PNG/G1T/ARC, Original/Rebuild/Backup은 v203 byte-exact다. 최종 `code.bin SHA-256=86966C881B28377428F4F3201C0E868A03364567A112490ABF1AF558A3D2C43C`.
- `PROJECT_RULES.md`의 v193 이후 규칙에 따라 `Dummy update\\ExeFS\\code.bin`도 현재 Patch code와 동일 SHA로 동기화했다. `title_up.g1t`는 기존 SHA `FAF199F252DEE6CB1E7D49FBA7CE763836DF0F9BD0DD1B99A5047BF2FD06FD63` 그대로다. Dummy update CIA/CXI 리패키징 자체는 수행하지 않았다.
- v204 builder → 독립 verifier → deterministic `--check` → verifier가 모두 PASS했다. 공통대사 stable v180 review resolver도 v204 manifest를 지원하도록 확장해 **2236행**, 전투 review도 live v204 **493행**으로 재생성/검증 PASS했다. #188 `받아라! / 비켜라! / 성문이 열렸습니다!` 경로는 그대로 보존된다. #189 두 버튼의 Citra 실화면 재확인은 pending이다.
- 권위 자료: `analysis\\v204_issue189_battle_function_menu_targets.json`, `analysis\\v204_issue189_battle_function_menu_report.json`, sealed v203 baseline, `tools\\build_sangokushi2_v204_issue189_battle_function_menu.py`, `tools\\verify_sangokushi2_v204_issue189_battle_function_menu.py`.

### 2026-09-07 v203-intermediate — #188 통상 공격 `성문이 열렸습니다!` 오출력 / e2·e3·e4 복수대사 구조 복구

- 사용자 비교 화면에서 같은 통상 공격 조건의 일본어판은 `くらえ！`, `邪魔だ！`처럼 공격 대사가 번갈아 나오지만 Patch에서는 모두 **`성문이 열렸습니다!`**가 출력되는 것을 확인했다. `msgsec02 direct9=門ｶﾞ開ｷﾏｼﾀ！/성문이 열렸습니다!` 자체는 정상 독립 이벤트라 문구를 바꾸지 않고 selector/물리 구조를 역추적했다.
- immutable Original의 `msgsec02 direct8 @ 0x01E2`는 단일 `くらえ！`가 아니라 **`01 S 02 e2 + くらえ！` → `05 05 05` → `邪魔だ！` → `05 05 05`**의 2-variant chain이다. `e2/e3/e4` 값은 실제 연속 variant 수 2/3/4와 일치한다. v164 전쟁 전수 번역에서 direct를 첫 separator까지만 standalone EOF로 옮기면서 direct8의 variant2가 소실됐고, runtime이 두 번째 변형을 고르면 다음 물리 direct9의 `성문이 열렸습니다!`를 소비하는 구조가 #188의 정확한 원인이었다.
- v203은 `msgsec02 direct8`을 완전한 **`e2 + 받아라! / 비켜라!`** chain으로 새 EOF에 append하고 direct8 pointer만 새 chain으로 redirect했다. `direct9` pointer `0x0F79`와 `성문이 열렸습니다!` payload는 v202와 byte-exact 보존했다. 독립 verifier readback도 variant1=`받아라!`, variant2=`비켜라!`, direct9=`성문이 열렸습니다!`를 각각 별도 경로로 확인한다.
- 동일 구조를 Message 전체에서 감사해 총 20개 eN 그룹을 분류했고, 이미 정상인 `msgsec02 direct25`, `msgsec08 direct4`, `msgsec09 direct25`, `msgsec12 direct0/10/13/14/15/19` 등은 보존했다. 실제 복구 대상은 **11그룹**이다: `msgsec00 direct59~62` 4그룹(숨은 변형 일본어 잔존), `msgsec02 direct8`, `msgsec07 direct76`, `msgsec08 direct6/11/13/14/15`.
- flat bank인 `msgsec00/msgsec02` 5그룹은 기존 body를 byte-exact 보존하고 완전한 eN chain을 append한 뒤 해당 direct pointer만 변경했다. mixed bank인 `msgsec07/msgsec08` 6그룹은 BYTE-direct/WORD-parent shared header를 **한 바이트도 수정하지 않고**, 각 target부터 다음 active direct/valid parent 시작 전까지의 물리 span을 계산해 그 안에서만 in-place 수정했다. 6그룹 모두 active direct 시작 overlap=0, valid parent interval overlap=0을 gate로 통과했다. `msgsec07 direct76`은 31B fixed span에 `거점 변경? / 거점 포기?`가 정확히 들어가며 ASCII `?`는 이 capacity 전용 예외다.
- v202 대비 변경 게임 파일은 정확히 `RomFS/Message/msgsec00.dat`, `msgsec02.dat`, `msgsec07.dat`, `msgsec08.dat` 4개다. SHA-256은 `msg00=140E1FDD18A06D3EE9BA8240FCAD6C23E8510B7CBA37E81F3CE5AB7C27A46C2A`, `msg02=82B86CAB7E3603A2BE22F40AC4477E64577A347FCD91D32691EF29087AC6C4AC`, `msg07=892C05849666174ACF72C3A8CDEFC3A64BA858FA0968C6C920D6EC5457268C33`, `msg08=7E759D6628C07AD41A01C440F15531CB2BD85219DFF298616D10FFC321B9EE20`이다. `code.bin`은 v202 SHA `436181F1...C6906CE`, font/모든 이미지와 Dummy update 입력도 byte-exact다.
- `battle_text_review_v180.html`은 stable ID를 유지하면서 **493행**으로 확장했다. 기존 157개 `msgsec02` direct와 25개 `msgsec12` BYTE-direct 외에 `msgsec02-hidden` 3행, `msgsec12-hidden` 11행을 추가해 eN variant2+를 직접 볼 수 있다. #188은 `M02-008=받아라!`, `M02-008-V2=비켜라!`, `M02-009=성문이 열렸습니다!`로 분리 표기한다. 새 `tools/verify_sangokushi2_battle_text_review_v180.py`도 493행 결정적 rebuild/HTML hash/#188 분리 경로를 검증해 PASS했다.
- `common_dialogue_review_v180`의 live authority도 v203까지 확장했다. v203 mixed span에서 새 `05 05 05` physical block이 생겨 current-binary 행이 841→846으로 늘었고 총 **2236행(code433/formal957/binary846)**이다. generator/verifier PASS. 게임 builder→독립 verifier→deterministic `--check`→verifier도 모두 PASS했다. 이후 사용자 Citra 실화면에서 통상 공격 e2 두 번째 변형이 **`비켜라!`**로 정상 출력되고, 이전의 `성문이 열렸습니다!` 오출력이 사라진 것을 확인했다. 따라서 **#188 핵심 통상 공격 경로는 runtime PASS**로 승격한다. 나머지 sibling eN 그룹은 정적 검증 PASS 상태를 유지하며 개별 실화면 확인은 별도다.
- 권위 자료: `analysis\v203_issue188_special_group_repair_targets.json`, `analysis\v203_issue188_special_group_repair_report.json`, sealed v202 baseline, `tools\audit_sangokushi2_v203_special_groups.py`, `tools\build_sangokushi2_v203_issue188_special_group_repair.py`, `tools\verify_sangokushi2_v203_issue188_special_group_repair.py`.

### 2026-09-07 v202-intermediate — #185 Citra runtime 실패 복구 (`???` / 감소문 중복 / 메시지 직후 freeze)

- 사용자 Citra 실화면에서 v200/v201 #185 조립형 전투 결과가 `관평의 ???체력이, 사기가...`, `허저의 ???체력이, 사기가...체력이 줄었습니다`처럼 깨지고 메시지 직후 게임이 멈추는 현상을 확인했다. 단순 폰트/쉼표 문제가 아니라 `code.bin 0x0CACE0` 함수의 실제 ARM 흐름을 재분석했다.
- 원인은 `0x1F55B4` helper가 prefix를 **정확히 5B(4B word + byte[4])만 복사**하는데 v200의 `%s의 `가 visible 5B라 NUL이 6번째 바이트에 놓였던 것이다. 따라서 stack buffer가 NUL-terminate되지 않아 후속 `strcat`이 미초기화 stack을 읽으며 `???`, 중복 문자열, freeze로 이어졌다. shared 5B helper는 direct caller 10곳이 있어 전역 수정하지 않았다.
- v202는 prefix를 **`%s의\0`**로 되돌려 NUL이 정확히 5B copy 안에 들어가게 했다. 각 항목 slot은 ` 체력이`, ` 사기가`, ` 병력이`처럼 첫 바이트에 ASCII space를 둔다. morale/soldier block의 ARM 6개 instruction만 국소 변경해 **첫 항목이면 slot 시작을 사용하고, 앞 항목이 있으면 separator `/`를 붙인 뒤 pointer +1로 선행 공백을 건너뛴다.** 따라서 `관평의 체력이/사기가/병력이`처럼 `/` 전후 공백 없이 출력된다.
- separator literal `0x0CADC8 -> VA 0x001CADE0`은 v201과 동일하게 유지하고 local payload만 `, `→`/`로 바꿨다. prefix/각 label/suffix/local separator는 모두 NUL-terminated이며, 7개 non-empty 감소 조합을 전부 시뮬레이션했다. 최장 `%s의 체력이/사기가/병력이\n줄었습니다`도 **NUL 포함 37B / stack buffer 40B**로 안전하다.
- v201 대비 변경 게임 파일은 정확히 `ExeFS/code.bin` 1개, 실제 diff 34B다. #186/#187 Message 7개와 font/PNG/G1T는 v201 byte-exact다. Dummy update code 동기화 완료. builder + 독립 verifier PASS, 최종 code SHA-256=`436181F129DFBDEB747096431DA043AD041E76289B6224E3BC750DF67C6906CE`. Citra 재확인은 pending이다. 권위 자료는 `analysis\v202_issue185_runtime_repair_targets.json`, `analysis\v202_issue185_runtime_repair_report.json`, sealed v201 baseline, v202 audit/builder/verifier다.

### 2026-09-07 v201-intermediate — #186 아이템 정보 확인문 + #187 `(이)가` 조사 순서 전수 보정

- exact v200 84파일을 `analysis\v201_issue186_187_baseline\PatchSnapshot`에 봉인하고 로컬 `Github_Issue\Issue186.html`, `Issue187.html`을 current Patch와 대조했다. 이미지 편집/생성은 사용하지 않았고 PNG/G1T/font는 변경하지 않았다.
- #186 실제 runtime source는 mixed/dual-use `RomFS/Message/msgsec09.dat` direct19의 **`0x266..0x27A` 21B fixed span**이다. 요청문 `아이템 정보를 볼까요？`는 폭 제어를 제거해도 22B라 1B 초과하고, 인접 header word를 움직이면 WORD-parent 의미가 바뀔 수 있어 구조 변경을 금지했다. 따라서 exact 21B **`아이템 정보를 볼까요?`**를 적용했다. 기존 `ESC K/H`를 제거하고 ASCII `?`를 fixed-capacity 예외로 사용했으며 header word20=`0x266`, separator `0x27B`, 파일 크기는 byte-exact 보존했다.
- #187은 `이(가)` 6B → `(이)가` 6B의 **same-width** 수정이다. binary 전체 raw replace는 하지 않고 실제 runtime source를 전수 분류해 code text-range C-string 22곳 + `msgsec02` active direct 10곳 + `msgsec05` active direct 5곳 + `msgsec04/06/07/08` header/parent/direct 참조 physical body 25곳 = **총 62곳**만 치환했다. 참조가 없는 stale physical copy `msgsec07 0x1D6D`, `msgsec08 0x1B0B` 두 곳은 의도적으로 보존했다.
- v200 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `msgsec02.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat` 8개다. 모든 파일 크기와 pointer/separator geometry는 유지됐다. 최종 code SHA-256=`9275D1A89B27AC5B8BCA134A6BA3975BB4221AB91D91ACBDDEE8155AD0BADCC7`이며 `Dummy update\ExeFS\code.bin`도 동일 SHA로 동기화했다.
- builder + 독립 verifier PASS. verifier는 active replacement 62, stale 보존 2, active flat old `이(가)` 0, code text-range old `이(가)` 0, 모든 diff가 선언된 target 범위 안임을 확인했다. `battle_text_review_v180.html`도 live v201 기준 479행으로 재생성해 `M09-D019=아이템 정보를 볼까요?` 21/21B, battle subset old `이(가)` 0을 확인했다. `common_dialogue_review_v180` stable ID도 v201 live authority까지 확장했고 verifier 2231행 PASS다. Citra 실화면 확인은 pending이다.
- 권위 자료: `analysis\v201_issue186_187_targets.json`, `analysis\v201_issue186_187_report.json`, sealed v200 baseline, `tools\build_sangokushi2_v201_issue186_187.py`, `tools\verify_sangokushi2_v201_issue186_187.py`.

### 2026-09-07 v200-intermediate — #185 전투 공통 감소 조립형 메시지 보완

- #185의 미번역은 `msgsec02`의 이미 번역된 체력/사기/병사 감소 direct와 별개인 `code.bin` 조립형 runtime source였다. immutable Original `0x0CAC60` 계열은 공식 v1.1 병합 후 current에서 **+0x150**, 즉 `0x0CADB0` 계열로 이동했으며 ADR caller를 함께 확인했다.
- current 조립을 `%s의 ` + `체력이/사기가/병력이` + `\n줄었습니다`로 한글화하고 복수 항목 구분자는 해당 함수 전용 local `, `로 분리했다. 예: `%s의 체력이, 사기가, 병력이\n줄었습니다`. 기존 할당 안에서만 수정했고 separator literal redirect도 이 함수의 두 LDR 경로에만 영향을 주도록 검증했다.
- v199 대비 변경 게임 파일은 `ExeFS/code.bin` 1개, 변경 바이트 38B다. 최종 SHA-256=`D9379B871E7E04D9143A03CAF6CFFC5E1BAEC35CC369E59D7888DCE85238A331`; Dummy update code도 동기화했다. builder/독립 verifier PASS, Citra 확인 pending이다.
- `battle_text_review_v180.html`은 stable v180 ID를 유지하면서 #185 조립형 source 6행과 `msgsec12` BYTE-direct 25/25를 추가해 **479행**으로 확장했다. 일기토 WORD-parent/physical-fragment 정본은 계속 `duel_text_review_v172.html`이다.

### 2026-09-07 v199-intermediate — `HexFontEffect_007.png` 단일 이미지 리빌드

- MD와 current Patch manifest를 대조해 실제 최신 기준이 **v198-intermediate 84파일**임을 확인했다. exact v198 전체 Patch를 `analysis\v199_hexfonteffect_007_image_update_baseline\PatchSnapshot`에 봉인하고, 최신 Patch의 `RomFS/Hex/UI/HexFontEffect.g1t`를 base로 사용했다.
- 사용자가 명시한 `Extracted_Image\RomFS\Hex\UI\HexFontEffect\HexFontEffect_007.png`만 texture index 7(256×64, type 0x09 RGBA8)에 1:1 반영했다. 다른 PNG는 수정하지 않았고 이미지 생성은 사용하지 않았다.
- v130 이후 확정된 Hex 방향 규칙대로 **정상 편집 PNG를 추가 상하반전·회전 없이 표준 RGBA8 encoder에 직접 입력**했다. 완성 G1T 역디코드는 입력 PNG와 pixel-exact이며, `FLIP_TOP_BOTTOM(PNG)` 실패형과는 불일치함을 gate로 확인했다.
- v198 대비 변경 게임 파일은 정확히 `RomFS/Hex/UI/HexFontEffect.g1t` 1개다. G1T의 32-texture 구조·파일 크기·header와 비대상 31개 payload, v198의 code/font/Message/title_up 및 나머지 Patch 파일은 byte-exact다. 최종 G1T SHA-256=`BF250F4AE8CFB92F56F1C7A3E615E5689CC5F1FCEE07927722B73ABFCD83B341`.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v199_hexfonteffect_007_image_update_targets.json`, `analysis\v199_hexfonteffect_007_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v199_hexfonteffect_007_image.py`, `tools\verify_sangokushi2_v199_hexfonteffect_007_image.py`다.

### 2026-09-07 v198-intermediate — 공통 대사 이중 물음표 제거 + v180 공통대사 리뷰 최신 Revision 지원

- 사용자 Citra 화면의 **`누구에게 명하시겠습니까？？`**를 current v197 `RomFS/Message/msgsec07.dat`에서 역추적했다. 실제 active runtime source는 **header[375] = `0x3409`** 블록이며, 블록 끝 `0x3420..0x3423`이 전각 물음표 두 개 `81 48 81 48`로 저장되어 있었다.
- 두 번째 물음표 `0x3422..0x3423`만 `20 20`으로 치환해 기대 문구를 **`누구에게 명하시겠습니까？`**로 만들었다. `388H` header 전체, header[375] pointer, `0x3424`의 `05 05 05` separator, 파일 크기 60406B는 byte-exact 보존했다. v197 대비 변경 게임 파일은 **`RomFS/Message/msgsec07.dat` 1개**, 실제 변경은 2B이며 최종 SHA-256=`E500E27FD08803A6AA24D2BF9308CCDE0330BB1CDE6DE5B64961593053453F11`이다.
- 공통 대사 파일 전체에서 raw `？？ = 81 48 81 48`는 v197 기준 2곳이었다. 이번 active source `0x3420` 외 `0x1EB0`에도 하나가 남아 있지만, 후자는 큰 stale physical body의 끝부분이고 authoritative BYTE header가 그 문장 끝을 직접 가리키지 않는다. runtime source 증거가 없으므로 **0x1EB0은 의도적으로 미수정**하고 review에서 계속 노출한다.
- builder→독립 verifier PASS. 권위 자료는 `analysis\v198_double_question_cleanup_targets.json`, `analysis\v198_double_question_cleanup_report.json`, `analysis\v198_double_question_cleanup_baseline\msgsec07.dat`, `tools\build_sangokushi2_v198_double_question_cleanup.py`, `tools\verify_sangokushi2_v198_double_question_cleanup.py`다. Citra 실화면 확인은 pending이다.
- `Extracted_Text\open_common_dialogue_review_v180.bat`의 **v180 고정 ID는 유지**하면서 live Patch authority를 v198까지 확장했다. `generate_sangokushi2_common_dialogue_review_v180.py`는 exact v180~v198 manifest를 판정하고, v192 이후 code relocation을 v195~v198의 후속 code 수정에도 계속 remap하며, v186 donor와 v197 `뤄=89AA` mapping도 반영한다.
- 최신 review verifier PASS: **v198-intermediate / 2231행 = code 433 + Message formal 957 + current binary 841**. 이번 문구는 **`M07-B238 / 0x3409 / BYTE=375`**로 `누구에게 명하시겠습니까？` readback, v197 `미뤄두겠다!`/`미뤄 두겠다!`도 새 `뤄` alias로 정상 decode된다. `analysis\v180_common_dialogue_review_report.json`을 v198 live authority 기준으로 갱신했다. review 갱신 자체는 Patch game file을 추가 수정하지 않는다.
- Dummy Update/CIA 빌드는 사용자 담당이며 이번 작업에서는 실행하지 않았다.

### 2026-09-07 v197-intermediate — 철수 문구 자연화 + #183 `뤄→앵` runtime alias 전수 보완

- 이전 v196에서 capacity 때문에 보류했던 `한중에서 병을 철수해주십시오` 요청은 사용자가 문구를 **`한중의 군을 철수해주십시오`**로 변경했다. 실제 조립 prefix `code.bin 0x1D103C`의 `서 병을\n철수해\0`와 새 `의 군을\n철수해\0`는 둘 다 **NUL 포함 15B**로 동일 길이다. 따라서 relocation 없이 fixed slot만 치환했고 `0x1E51A4 -> 0x002D103C` pointer, 다음 문자열 `0x1D104C` 및 shared `주십시오` suffix는 byte-exact 보존했다.
- 로컬 `Github_Issue\Issue183.html`과 `Issue183_assets\3d552a92-e819-417a-a491-2fdda6e3b940.png` 존재를 확인했다. 신고 화면은 `이 승부는 다음 기회까지 / 미앵두겠다!`이며, 검수 DB의 의도 문구는 `미뤄두겠다!`다. 실제 원인은 `뤄`의 구 alias **`9949`**가 정적 packed-font readback과 달리 Citra에서 `앵`으로 표시되는 far-alias 런타임 실패다.
- current v196 Patch에서 `9949`의 **실제 semantic runtime source를 전수 감사**한 결과 정확히 2곳이었다: `ExeFS/code.bin` C-string `0x1D0678` 안 alias `0x1D0692`, 그리고 `RomFS/Message/msgsec04.dat` body `0x0F7` 안 alias `0x0111`. 둘 다 같은 정전/승부 연기 대사 계열이며, 한쪽만 고치면 경로에 따라 재현될 수 있어 두 곳 모두 새 alias로 재인코딩했다. Message/Scenario 전체에서 `9949` text hit는 msgsec04 이 1곳뿐이며 code의 의미 있는 text hit도 이 대사 1곳뿐이다.
- 새 `뤄` donor는 **`89AA / physical 751`**을 사용했다. current owner=0, code text ref=0, Message/Scenario ref=0, current cell이 immutable Original stock `岡`과 pixel-exact, 알려진 Citra 실패 alias가 아니며 low-lead다. `font.g1t`에서 변경된 physical cell은 **751 단 1개**이고 packed readback pixel-exact를 통과했다. 구 `9949`는 `tools\audit_sangokushi2_v103_dialogue.py`의 known-failed 목록에 `뤄_old`로 등록해 재사용을 금지했다.
- v196 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec04.dat` 3개다. `msgsec04` header/`05 05 05` separator/file size는 그대로이며 모든 비대상 Patch 파일은 v196 byte-exact다. 최종 SHA-256은 `code=FFC0CB9C2FA793FA72D9CF103872109E8F76591D914329603630BB86A7663A26`, `font=F5D102E9B1A08FA4127BF346278897E97BF9DAF9D1DCDF572F83C0204DECA924`, `msgsec04=1F5FDDB0A50C080FB4491D8C60A1FAC09D7579CEF59D8D3EDCCE571EFC01CEA8`다.
- builder→독립 verifier PASS, builder 재실행 `already_applied`, verifier 재실행 PASS. 이후 사용자 Citra 실화면에서 **`한중의 군을 철수해주십시오`와 `미뤄두겠다!` 두 수정 모두 정상 출력**을 확인해 v197 runtime 검증도 PASS로 확정했다. 권위 자료는 `analysis\v197_issue183_ryeo_alias_audit.json`, `analysis\v197_issue183_ryeo_and_retreat_targets.json`, `analysis\v197_issue183_ryeo_and_retreat_report.json`, `tools\build_sangokushi2_v197_issue183_ryeo_and_retreat.py`, `tools\verify_sangokushi2_v197_issue183_ryeo_and_retreat.py`다. Dummy Update/CIA 빌드는 사용자 담당으로 이번 작업에서는 실행하지 않았다.

### 2026-09-07 v196-intermediate — 동적 도시명 뒤 띄어쓰기 보완 / 철수 문구 capacity 보류

- Citra 화면의 `대현에관구검(이)라는 자가 있습니다`는 runtime에서 `[도시명] + "에" + [무장명] + "(이)라는\n자가 있습니다"`로 이어 붙이는 구조다. current `code.bin 0x15BBAC`의 4B `에` 전용 slot에 공백 1B를 추가해 기대 화면을 `대현에 관구검(이)라는 자가 있습니다`로 수정했다. slot 크기와 다음 데이터 위치는 유지되고 실제 변경 바이트는 1B다.
- `한중서 병을 철수해주십시오`의 fixed prefix는 current `code.bin 0x1D103C`의 `서 병을\n철수해`이며 proven slot은 NUL 포함 15B다. 요청한 `에서 병을\n철수해`는 NUL 포함 17B이고 다음 문자열 전까지 실제 공간도 16B뿐이어서 최소 1B가 부족하다. 따라서 relocation 없이 보류했고 해당 prefix/pointer/다음 문자열은 v195와 byte-exact로 유지했다.
- v195 대비 변경 게임 파일은 `ExeFS/code.bin` 1개, 변경 바이트는 1B다. 최종 code SHA-256=`722806ADB27C69C2BD1FDA581A7F2D20E5535CDB243AB94534F0752CC2B72FA5`. builder + 독립 verifier PASS, 모든 비-code Patch 파일은 v195 byte-exact, Citra 확인 pending이다. 권위 자료는 `analysis\v196_dynamic_city_spacing_targets.json`, `analysis\v196_dynamic_city_spacing_report.json`, `tools\build_sangokushi2_v196_dynamic_city_spacing.py`, `tools\verify_sangokushi2_v196_dynamic_city_spacing.py`다.
- Dummy Update/CIA 빌드는 사용자 담당이며 이번 턴에는 실행하지 않았다.

### 2026-09-07 v195-intermediate — 외교 조건값 `なし` runtime owner 회귀 보완

- 사용자 실화면에서 외교 패널의 `내용 / 조건` 아래 조건값이 일본어 **`なし`**로 회귀한 것을 확인했다. 과거 #099와 동일하게 같은 패널 안의 문자열이 서로 다른 `code.bin` source-owner를 사용하는 유형으로 판정했다.
- v125의 설정값 `なし→없음` 원래 target `0xD6308`은 공식 v1.1에서 +0x150 이동한 `0xD6458`로 정상 이식되어 현재도 **`없음`**을 유지하고 있었다. 따라서 v192 병합기의 해당 target 누락이 아니라, 별도 standalone `なし` C-string이 runtime에서 사용되는 문제였다.
- current v194 `code.bin`의 standalone `なし\0` 두 곳 `0x0B4734`(ADR refs `0x0B4678`, `0x0B46E0`)와 `0x17A93C`(ADR ref `0x17A904`)를 동일 길이 4B **`없음`**으로 fixed-slot 치환했다. NUL과 ADR instruction은 byte-exact 보존했고, standalone `なし\0` 잔여는 0개다.
- v191→v194 historical code target 313개를 별도 감사한 결과, 공식 v1.1 통합 때문에 v191에서 0개였던 일본어 원문이 새로 생긴 strong reintroduction은 **0건**, count 증가도 **0건**이었다. 잔여 후보 `兵士/名/君主/武将/病気/負傷/金`은 v191부터 동일 수로 존재해 이번 통합 회귀 증거가 아니므로 수정하지 않았다.
- v194 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin` 1개**, 변경 바이트는 8B다. 최종 code SHA-256=`29EC6238393F440DBA64FFE78A43316DFB566BE5692068944DE37B985A125CA1`. builder→독립 verifier PASS, builder 재실행 `already_applied`, verifier 재실행 PASS. 모든 비-code Patch 파일은 v194 byte-exact다. Citra/실기 확인은 pending이다.
- 권위 자료: `analysis\v195_none_owner_regression_targets.json`, `analysis\v195_none_owner_regression_report.json`, `tools\build_sangokushi2_v195_none_owner_regression.py`, `tools\verify_sangokushi2_v195_none_owner_regression.py`, `tools\audit_v195_update_text_owner_regressions.py`, `tools\audit_v195_historical_code_source_residue.py`.
- Dummy Update CIA는 사용자 담당으로 유지한다. `Rebuild_Update.bat`을 실행하면 최신 Patch `code.bin`을 자동 동기화하고 v1072 CIA + 내장 검증까지 수행한다.

### 2026-09-07 v194-intermediate — #180 치수 상단 스크롤 설명 보완

- 로컬 `Github_Issue\Issue180.html`과 첨부 asset 존재를 확인하고, immutable Original `RomFS/Message/msgsec10.dat`의 fixed span `0x846..0x87D`가 `自都市ﾉ治水・潅漑ｦ上ｹﾞﾏｽ。潅漑ｶﾞ高ｲﾎﾄﾞ兵糧収入ｶﾞ増ｴﾏｽ。`임을 현재 v193 Patch와 직접 대조했다. v193 runtime readback은 **`치수를 올려 군량 수입을 늘립니다.`**였다.
- 사용자 요청안 **`치수・관개를 올려 군량 수입을 늘립니다.`**는 current alias map으로 39B이며, 해당 fixed group의 writable 55B 안에 들어가 **16B 여유**가 있다. `관=8B6B`, `개=8DEE`는 기존 글리프를 그대로 사용하므로 신규 font donor가 필요 없다.
- `msgsec10.dat 0x846..0x87D` visible 영역만 새 문구 + trailing space로 제자리 치환하고 마지막 `0x87D=05` 경계를 그대로 보존했다. 스크롤형 상단 도움말이므로 수동 줄바꿈은 넣지 않았다. v193 대비 변경 게임 파일은 정확히 **`RomFS/Message/msgsec10.dat` 1개**이고 파일 크기 7237B는 유지된다. 최종 SHA-256=`138ABCB4B575E71119A0ABFE83F5D19CA804ED62DF43E2DB146FEFC2BF9FF920`.
- `ExeFS/code.bin`, `font.g1t`, `title_up.g1t`, 모든 PNG/G1T/ARC와 나머지 Patch 파일은 v193 byte-exact다. builder→독립 verifier PASS 후 builder 재실행은 `already_applied`, verifier 재실행도 PASS했다. Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v194_issue180_flood_control_help_targets.json`, `analysis\v194_issue180_flood_control_help_report.json`, `analysis\v194_issue180_flood_control_help_baseline\v193_msgsec10.dat`, `tools\build_sangokushi2_v194_issue180_flood_control_help.py`, `tools\verify_sangokushi2_v194_issue180_flood_control_help.py`다.

### 2026-09-07 — Dummy update 실기 부팅 성공 후 `title_up`/SMDH 보완 (게임 revision 변경 없음)

- 이전 full-RSF + v1072 Dummy update는 3DS 실기에서 **정상 부팅까지 성공**해 `0xC8804464` 크래시 해결을 확인했다. 다만 update RomFS를 0파일로 만든 탓에 상단 StartMenu 로고 영역이 뭉개져 보였고, ExeFS에 SMDH `icon`이 없어서 3DS 데이터 관리 화면에서 이름/아이콘이 일반 `[업데이트 데이터]` 상태로 표시되는 문제가 남았다.
- 공식 v1.1 Update RomFS의 Level3를 다시 분석해 실제 구조가 정확히 `/StartMenu/title_up.g1t` 단일파일임을 확인했다. 현재 v193 Patch와 공식 Update의 `title_up.g1t`는 둘 다 **593,996B (`0x9104C`)**이며, 공식 geometry는 file data offset `0xC0`, Level3 size `0x9110C`, IVFC RomFS 전체 size `0x96000`이다. `tools\build_dummy_update_romfs.ps1`을 추가해 이 공식 one-file directory/hash/metadata/IVFC 구조를 재생성하고 payload는 항상 최신 `Sangokushi 2 Patch\RomFS\StartMenu\title_up.g1t`을 사용한다. 현재 title SHA-256=`FAF199F252DEE6CB1E7D49FBA7CE763836DF0F9BD0DD1B99A5047BF2FD06FD63`, 생성 RomFS SHA-256=`16EA11C01FBAC514ACBEBD81FD7EA697A733290231A7ED2EF95D31B107723657`이다.
- `Update Data\ExeFS\icon.icn`(SMDH)을 base로 `Dummy update\ExeFS\icon.icn`을 생성한다. 아이콘 그림과 한국어 title 이외 모든 byte는 공식 Update와 byte-exact로 보존하고, Korean language record(index 7)의 short/long title만 **`삼국지2 3DS 업데이트`**로 기록한다. Windows PowerShell 5.1 코드페이지 오염 방지를 위해 한글 literal 대신 명시적 UTF-16 code point를 조립한다. 실제 Korean short field raw는 `BC C0 6D AD C0 C9 32 00 20 00 33 00 44 00 53 00 20 00 C5 C5 70 B3 74 C7 B8 D2 00 00`으로 확인했다. 현재 icon SHA-256=`9EE179A184A180ADC4A5B9830E6CA9B52B2950B5DD9CC3E78C977F9E203913AE`이다.
- `tools\sync_dummy_update_inputs.ps1`은 이제 ExHeader + 최신 code + 최신 title_up + official-derived Korean SMDH를 한 번에 동기화한다. `Rebuild_Update.bat`은 sync → one-file RomFS → makerom CXI(`-icon` 포함) → ExHeader finalize → v1072 CIA → 독립 verify의 6단계로 갱신했다. 구 `empty.romfs`는 자동 삭제하며 더 이상 사용하지 않는다.
- 독립 verifier는 최신 Patch/Dummy `code.bin`·`title_up.g1t` SHA 일치, SMDH 한국어 제목, SMDH 비대상 byte 보존, CXI ExeFS 역추출 `code.bin/icon.bin` exact, CXI RomFS 역추출 파일 수 1 및 `StartMenu/title_up.g1t` exact, ExHeader core 0x400B 공식 byte-exact, Kernel 2.48, nfc/qtm dependency/service, TMD v1072를 모두 검사한다. 실제 `Rebuild_Update.bat --nopause`가 6/6 PASS했으며 CXI SHA-256=`50A638C86F1918CDEE2B27506A66F934D8B695C977FE095BDE84134211115F3F`, 현재 canonical CIA=`Dummy update\0004000E00174D00-DummyUpdate-v1072.cia`다. 실기에서 새 title 로고/SMDH 표시 최종 확인은 pending이다.

### 2026-09-06 — Dummy update 실기 크래시 원인 분석 + 공식 v1.1 메타 기반 재빌드 (게임 revision 변경 없음)

- 첫 Dummy CIA는 실기에서 `SangokuC`, address `0x0011351C`, error `0xC8804464`로 실행 직후 실패했다. 해당 실패본은 `makerom -desc app:7` + RomFS 없는 CXI였다. 공식 Update와 비교해 `-desc app:7`이 AccessControl을 재생성하면서 Kernel release `2.48→2.39`, dependency `0004013000004002`(nfc) / `0004013020004202`(qtm) 및 service `nfc:u` / `qtm:u` 누락을 일으킨 것을 확인했다. 따라서 `-desc app:7` 사용은 폐기했다.
- 루트의 실제 CIA 두 개를 `ctrtool`로 직접 비교했다. `0004000E00174D00-decrypted.cia`는 TMD v1024로 재패키징되어 있지만, 원본 `0004000E00174D00 삼국지2 Update v1.1.cia`는 **Title ID `0004000E00174D00`, Title Version `1.3.0 (v1072) = 0x0430`, content count 2**다. HOME Menu 업데이트 판정의 권위 버전은 원본 v1.1 CIA의 **v1072**이며 decrypted CIA의 v1024는 버전 권위로 쓰지 않는다.
- 공식 content0은 실행 Patch CXI이고 content1은 `CTR-P-CTAP` 전자매뉴얼 CFA다. Dummy는 최소화를 위해 content0 하나만 만든다. 실제 게임/이미지 자산(`title_up.g1t`, Message/font/Scenario/icon/Manual 등)은 넣지 않는다. 대신 update RomFS mount 자체를 유지하기 위해 root directory만 가진 **16KB / 파일 0개** IVFC `Dummy update\empty.romfs`를 생성한다. `ctrtool --romfsdir` 역추출 결과 항목 수 0이며 SHA-256=`AFCD2D8FD818654D196A385F7207D8011644F89B168DBF6FA9C4904515088F3D`다.
- `Dummy update\dummy_update.rsf`를 공식 content0 AccessControl 기준으로 확장해 dependency 33개, service access, SVC, static mapping, Kernel 2.48, ARM9 desc v3, Priority 48, Program ID `0004000000174D00`, Jump ID `0004000E00174D00`, SaveData 512K, stack 0x80000, Remaster 1을 재현했다. full RSF + 최신 Patch `code.bin` + 공식 `ExHeader.bin` + empty RomFS로 CXI를 만들면 makerom이 ExHeader core의 두 semantic byte(+0x0D/+0x24F)만 공식값과 다르게 만들기 때문에 `tools\finalize_dummy_update_cxi.ps1`로 두 바이트를 공식값으로 되돌리고 NCCH ExHeader SHA를 재계산한다. 최종 ExHeader SCI+ACI core 0x400B는 공식 `Update Data\ExHeader.bin`과 byte-exact이며 ExHeader SHA-256=`1FDE4B670290EBAF2FFCF0AC14433991FEECCEC2630DE0DB4B264805D85AE769`다.
- 자동 빌드 `Rebuild_Update.bat`을 6단계(sync→empty RomFS→CXI→ExHeader finalize→CIA v1072→독립 verify)로 교체했다. `tools\verify_dummy_update_build.ps1`은 최신 Patch/Dummy code SHA 일치, empty RomFS 0파일, ExHeader core exact, Kernel 2.48, nfc/qtm dependency+service, Program/Jump ID, TMD v1072/content0을 검사한다. 실제 BAT `--nopause` 실행이 6/6 PASS했다. 현재 v193 code SHA-256=`63397CCC9EC1A7E940B0B2BB1A3B162B56FB938625F19E54D787EBD9BC81B680`, CXI SHA-256=`D21AD6D06182131720F4CF13AC78116BC0666F4DA6F599DE81588369C64D9681`, canonical CIA는 `Dummy update\0004000E00174D00-DummyUpdate-v1072.cia`다. makerom `-rand` 때문에 CIA SHA는 빌드마다 달라질 수 있다. 이후 실기 재설치에서 이 full-RSF/v1072 구조의 **부팅 성공**을 확인했고, 남은 title/SMDH 표시 문제는 바로 위 2026-09-07 항목에서 보완했다.

### 2026-09-06 v193-intermediate — StartMenu `title_up` 지정 PNG 2개 리빌드

- exact v192-intermediate 전체 Patch 84파일을 `analysis\v193_title_up_image_update_baseline\PatchSnapshot`에 봉인하고, 현재 최종 Patch의 `RomFS/StartMenu/title_up.g1t`를 베이스로 사용했다.
- 사용자가 명시한 `title_up_000.png`(index 0, 512×256 RGBA8)와 `title_up_002.png`(index 2, 64×16 RGBA8)만 1:1 재삽입했다. 이미지 생성은 사용하지 않았으며 다른 PNG는 수정하지 않았다.
- G1T의 3-texture 구조·파일 크기·header·지정 payload 밖 모든 바이트와 비대상 index 1은 v192와 byte-exact다. 두 대상 모두 source codec identity 및 완성 Patch 역디코드 pixel-exact를 통과했다.
- v192 대비 변경 게임 파일은 정확히 `RomFS/StartMenu/title_up.g1t` 1개이고 Patch 파일 수는 84개로 유지된다. `code.bin`, font, Message, Scenario 및 모든 비대상 이미지는 v192와 byte-exact다. 최종 G1T SHA-256=`FAF199F252DEE6CB1E7D49FBA7CE763836DF0F9BD0DD1B99A5047BF2FD06FD63`.
- builder → 독립 verifier → deterministic `--check` → 독립 verifier가 모두 PASS했다. Citra 실화면 확인은 pending이다. 권위 자료는 `analysis\v193_title_up_image_update_targets.json`, `analysis\v193_title_up_image_update_report.json`, sealed baseline, `tools\build_sangokushi2_v193_title_up_images.py`, `tools\verify_sangokushi2_v193_title_up_images.py`다.
- `common_dialogue_review_v180`도 v193 manifest까지 동기화했다. v192+ `code.bin`은 Original 주소와 current 주소가 달라지므로 리뷰 생성기는 이제 번역된 current Patch를 anchor로 삼지 않고 **Original → 공식 Update code.bin → 현재 Patch** 순서로 offset을 remap한다. v193 기준 2,231행(code 433 / formal 957 / binary 841), code rejected 0, format mismatch 0으로 생성 PASS했고 C250은 `0x1D12D0→0x1D12E0`, C366은 `0x1D3014→0x1D3024`에서 기존 한국어를 정상 readback한다. HTML/README/server도 v193 기준으로 갱신했다.

### 2026-09-06 v192-intermediate — 공식 v1.1 업데이트 병합 기준 확인 및 `Ver.1.1` 텍스처 추출

- 현재 `Sangokushi 2 Patch` 84파일이 `analysis\v192_official_update_merge_report.json`의 봉인 manifest와 정확히 일치함을 재확인했다. 최신 기준은 **v192-intermediate**이며, v191이 아니다.
- v192의 `RomFS/StartMenu/title_up.g1t`는 공식 업데이트에서 추가된 index 2 텍스처를 포함한다. 해당 항목은 **64×16, type 0x09 RGBA8, payload 4096B**이고 payload SHA-256은 `106E37DCA7CC6131EDFFA82B98526D2AB403B56473410B92DC093C3A05B07D83`이다.
- 위 항목만 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`로 추출했다. 실물 판독 결과 `Ver.1.1`이며 PNG SHA-256은 `02B7CD29472F36F53BFAF69B8B0E6A4358A60EEC74B294A693D254E35DE81BCD`다. RGBA8 decode→encode payload identity 및 저장 PNG 재읽기 pixel-exact를 확인했다.
- 기존 `title_up_000.png`, `title_up_001.png`는 byte-exact이며 Patch manifest도 작업 전후 동일하다. **추출 전용 작업이므로 새 Revision을 발행하지 않았고 최신 Revision은 그대로 v192**다. 도구/증거는 `tools\extract_sangokushi2_v192_title_up_002.py`, `analysis\v192_title_up_002_extraction_report.json`이다.

### 2026-09-06 v191-intermediate — 일기토 #26/direct16 문구 개선

- 사용자가 `duel_text_review_v172.html` logical #26 `0x28F..0x2A3` / direct16 `0x28F`의 기존 `드디어 / 참 사내군!`을 **`간만의 호걸이로군!`**으로 변경 요청했다. 최초안의 `랜` 글리프는 current map에 없어 사용하지 않고, 사용자 대체안은 현재 v190 map의 기존 글리프만 사용한다.
- 최종 문구는 **18B/20B, 여유 2B**다. mixed 내부 경계 `0x298` 기준 첫 span `0x28F..0x297` 9B에는 `간만의 호`가 exact 9B, 둘째 span `0x298..0x2A2` 11B에는 `걸이로군!` 9B + padding 2B로 들어간다. 따라서 header, direct16 pointer `0x28F`, parent boundary `0x298`, separator 36개 위치, hidden direct24, v190 `word10=0xF4`를 모두 그대로 유지했다.
- 실제 변경 게임 파일은 `RomFS/Message/msgsec12.dat` 1개뿐이고 다른 24개 BYTE direct는 byte/readback 불변이다. 신규 glyph/font/이미지 변경은 없다. 최종 msgsec12 SHA-256=`1C8BADDE2693E627DB2ACD4673317941EA82AEE88E34178FECF6C643F6B2D439`; 독립 verifier PASS, Citra 실화면 확인 pending이다.
- `Extracted_Text\duel_text_review_v172.html`은 v172 고정 ID를 유지하면서 현재 v191 Patch 기준으로 갱신했다. 권위 자료는 `analysis\v191_duel26_targets.json`, `analysis\v191_duel26_report.json`, `tools\build_sangokushi2_v191_duel26.py`, `tools\verify_sangokushi2_v191_duel26.py`다.

### 2026-09-06 v190-intermediate — #179 일기토 `네놈은 어림없다` + mixed 경계 안전 조정

- 로컬 `Github_Issue\Issue179.html`을 확인했다. 첨부 asset은 없고 `duel_text_review_v172.html`의 logical #19 `0x1E2..0x1F2`를 `너는 상대 못돼` → **`네놈은 어림없다`**로 변경하는 요청이다. exact v189 Patch 84파일을 `analysis\v190_issue179_duel_text_baseline\PatchSnapshot`에 봉인했으며 Original/Rebuild/Backup/이미지는 수정하지 않았다.
- 요청 문구는 current map으로 15B/16B라 총량은 맞지만 기존 내부 parent 경계 `0x1EC`(10B+6B)가 `림`의 2바이트 중간을 잘라 단순 in-place가 불가능했다. 쉼표나 문구 변경 없이 exact 문구를 유지하기 위해 shared `word10`을 `0x00F6→0x00F4`로 바꿔 parent 시작을 `0x1EC→0x1E8`로 이동했다.
- 같은 word10은 동시에 BYTE direct9 pointer이므로 direct9 `크윽`을 `0xF6→0xF4`로 2B 앞당겼다. 공간은 direct8 `죽을 순...`의 기존 trailing padding 4B 중 2B를 사용하고, 그 사이 separator만 `0xF3→0xF1`로 2B 이동했다. direct8/9 포함 **25개 direct의 visible readback은 v189와 전부 동일**하고 separator는 여전히 36개/각 3B, hidden direct24와 file size 860B도 보존된다.
- 실제 변경 게임 파일은 `RomFS/Message/msgsec12.dat` 1개뿐이다. 최종 SHA-256은 `F281A8293E1C3F9F5D8ECB1A42831F7E4359F5746EA4733EDD6A54218E4F15AE`. builder deterministic `--check` + 독립 verifier PASS, v189 #178 msgsec06와 v188/v187 이미지/font 등 비대상 83파일은 byte-exact다. Citra 실화면 확인은 pending이다.
- `Extracted_Text\duel_text_review_v172.html`은 파일명/고정 ID를 유지하면서 **현재 v190 Patch** 기준으로 갱신했다. #19은 15B/16B, 내부 경계 `0x1E8`(6B+10B), direct8은 12B cap/2B 여유, direct9은 pointer `0xF4`, 6B cap/2B 여유로 표시한다. 권위 자료는 `analysis\v190_issue179_duel_text_targets.json`, `analysis\v190_issue179_duel_text_report.json`, sealed v189 baseline, v190 builder/verifier다.

### 2026-09-06 v189-intermediate — #178 아이템 발견 문구 이중조사 처리

- 로컬 Issue178 본문/첨부 asset을 확인했다. v188은 Dialog.g1t만 변경한 이미지 전용 revision이므로 msgsec06.dat는 v187과 동일한 text 기준이다.
- 실제 runtime은 msgsec06 direct index17, header word18(0x24), 기존 pointer 0x0396이다. 현재 포맷은 `% s` 바이트를 사용하며 실게임에서 아이템명이 치환되므로 이 형태를 그대로 보존했다.
- `s를 찾았습니다` 14B를 `s을(를) 찾았습니다` 18B로 바꾸면 +4B가 필요하다. shared body를 늘리지 않고 완성 문자열 38B를 EOF 0x1AD7에 append하고 separator를 붙인 뒤 direct index17만 새 위치로 redirect했다. 파일 크기는 6871B→6912B다.
- 독립 검증 결과 v188 preimage의 기존 영역 변경은 header 0x24/0x25 두 바이트뿐이며 기존 msgsec06 body는 byte-exact다. 새 glyph는 없고 v188 Dialog.g1t도 byte-exact 보존됐다. 아이템/특산품 DB 67개 이름을 `이름+을(를) 찾았습니다`로 대입한 폭 감사도 최대 14 units / 20 초과 0건이다. 최종 msgsec06 SHA-256은 `BE9D327FC75EF6E0648634F5B1E33406C50CFA50EFAE2A2A6A41421682F5A0CC`다.
- 번역 권위 JSON/HTML의 msgsec06_0004_000도 `s을(를) 찾았습니다`로 갱신했다. 권위 자료는 `analysis\v189_issue178_double_particle_report.json`, v189 builder/verifier이며 Citra 실화면 확인은 pending이다.

### 2026-09-06 v188-intermediate — Common Dialog 지정 PNG 5개 재리빌드

- exact v187 전체 Patch 84파일을 `analysis\v188_dialog_image_update_baseline\PatchSnapshot`에 봉인했다. 대상 `RomFS/Common/Dialog/Dialog.g1t`는 v109 이후 Patch에 존재하므로 immutable Original이 아니라 sealed v187 Patch 컨테이너 SHA-256 `20F1C4E8D6F4317567CA2B334AE1F65DBD28F2E7765950F4ACBC438B4B0F87C5`를 base로 사용했다. Original/Rebuild/Backup은 수정하지 않았다.
- 사용자가 지정한 `Dialog_015.png`, `Dialog_016.png`, `Dialog_017.png`, `Dialog_018.png`, `Dialog_021.png`만 사용했으며 이미지 생성이나 다른 PNG 편집은 하지 않았다. 대응 index는 15/16/17/18/21이고 모두 `0x09 RGBA8 / 128×64 / payload 32768B`다.
- 각 target의 base decode→encode identity가 byte-exact이고, 완성 Dialog.g1t에서 다시 decode한 결과가 사용자 PNG와 5/5 pixel-exact임을 확인했다. G1T header/entry geometry/파일 크기와 비대상 texture payload는 v187과 byte-exact다.
- v187 대비 실제 Patch 변경 게임 파일은 정확히 `RomFS/Common/Dialog/Dialog.g1t` 1개다. 컨테이너 크기 1192396B, 최종 SHA-256 `2B0DAECD7B8C48E17C678D5632FF0917B7F0D8A2D2A598915FF4BBAFC543D292`, Patch 파일 수 84 유지다. v187 `Title.arc`와 v186 text/font/Message를 포함한 나머지 83개 파일은 byte-exact다.
- builder write→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 권위 자료는 `analysis\v188_dialog_image_update_report.json`, sealed v187 baseline, `tools\build_sangokushi2_v188_dialog_images.py`, `tools\verify_sangokushi2_v188_dialog_images.py`다. 최신 정적 Patch 권위는 **v188-intermediate**, Citra 실화면 확인은 pending이다.

### 2026-09-06 v187-intermediate — Opening 진입 전 로고·credit 이미지 리빌드

- exact v186 전체 Patch 83파일을 `analysis\v187_title_arc_image_update_baseline\PatchSnapshot`에 봉인했다. v186 Patch에는 `RomFS/Opening/Title.arc`가 없었으므로 최초 편입 규칙에 따라 immutable Original의 archive SHA-256 `6F5F38985CFF5D3E90728F0E1D104F8B629216AE9040331F493CFC989249002B`를 base로 사용했다. Original/Rebuild/Backup은 수정하지 않았다.
- 사용자가 지정한 `Extracted_Image\RomFS\Opening\Title\title_logo_2.png`와 `shoki_up_00_title_credit.png`만 사용했다. 이미지 생성은 하지 않았다. 내부 target은 각각 `timg/title_logo_2.bflim`, `timg/shoki_up_00_title_credit.bflim`이며, SARC의 나머지 레이아웃·애니메이션·배경 member는 모두 Original과 byte-exact다.
- `title_logo_2`는 302×144, native format `0x08 RGBA4`, orientation 4다. source decode→encode는 byte-exact였다. 편집 PNG는 8비트 중간색을 포함해 native RGBA4의 16단계로 결정적 nearest quantization했으며 24202 pixel에 변환이 있고 최대 채널 오차는 8/255다. 실제 built member 재추출 결과는 그 native quantized image와 pixel-exact다.
- `shoki_up_00_title_credit`는 320×24, native format `0x03 LA8`, orientation 4다. 기존 실패 사례대로 CTR 저장 순서 `A,L`을 사용했고 PNG grayscale 조건, source codec identity, built readback 원 PNG pixel-exact를 모두 통과했다.
- v186 대비 실제 Patch 변경/추가는 정확히 `RomFS/Opening/Title.arc` 1개이며 Patch 파일 수는 83→84다. archive 크기는 1741352B로 유지되고 SHA-256은 `7D49110E481DA2E2675A339E60E62DA87881D1D637523F60AB95D5C5E2AB9736`이다. target member SHA는 `title_logo_2=85018D7328D8D26C8AC9E762BCA64A4C3ECA3C34587CD8D6E96FBA0833A44AE4`, `credit=C6DA809FDA33949CBDCA9B26E78A639C1E5F7AF1BFAB9EF6BDA3D793F8ACC2C0`이다.
- builder write→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 권위 자료는 `analysis\v187_title_arc_image_update_report.json`, v187 sealed baseline, `tools\build_sangokushi2_v187_title_arc_images.py`, `tools\verify_sangokushi2_v187_title_arc_images.py`다. 최신 정적 Patch 권위는 **v187-intermediate**, Citra 실화면 확인은 pending이다.

### 2026-09-06 v186-intermediate — 남만왕 열전 포인터 복원 + 미번역 특수 열전 166건

- exact v185 전체 Patch 83파일을 `analysis\v186_biography_translation_baseline\PatchSnapshot`에 봉인했다. v180 열전 DB의 상태는 한글 808 / 일본어·혼입 166 / 의도된 빈 항목 4였으며, 일본어·혼입 166건은 정확히 `msgsec20 direct0..165 = record840..1005`였다.
- B0000은 파일에 `남만왕…` 128B 완문이 있었지만 v145 계열에서 header와 body가 겹치며 direct0이 `0x2A4`를 가리켜 첫 글자 `남`을 건너뛰었다. 완전 payload를 msgsec13 EOF `0xAFBB`에 append하고 direct0을 `0xAFBB`로, 숨은 direct336을 기존 빈 SEP `0xAFB8`로 복원했다. 최종 크기 44987→45118B, record1~335와 기존 body 보존을 검증했다.
- msgsec20의 미번역 특수·역사 무장 열전 166건을 원문과 인명표에 맞춰 한국어로 작성하고 19.5칸×4줄로 재행갈이했다. direct0~165만 append redirect했고 direct166~169 빈 항목과 direct170~179 기존 한국어를 보존했다. 최종 44836B, 16비트 loader 여유 20699B다.
- 자연스러운 번역을 유지하기 위해 미사용 한자 donor 21개를 사용했다: `케=9659`, `쿠=89FA`, `테=8A5B`, `텐=8BAA`, `톤=8CDF`, `헤=8E86`, `혹=8E89`, `딱=8FD9`, `릎=916A`, `붉=9291`, `뼈=8DD3`, `쇼=8B84`, `슈=93BC`, `즈=8E5C`, `첩=91D7`, `렬=8CAD`, `덴=8F86`, `뛰=8AE9`, `룻=90EB`, `놀=8E8B`, `털=935D`. exact v185에서 전부 current owner=0, code ref=0, Message/Scenario raw ref=0, Original stock pixel-exact, low-lead/valid JIS/known-failed 제외를 통과했다. guard 포함 font changed cell은 49개뿐이다.
- 첫 독립 심사에서 `장렬히`, 일본 지명, `뛰어넘다`, `하룻밤`, `관자놀이·털`의 전역 치환 회귀를 발견해 record-scoped로 수정했다. 새 동결 target/donor 해시에 결속한 전반 80건, 후반 86건, 전체 166건+donor+B0000 심사가 모두 issues 0 / PASS다.
- 실제 게임 변경은 정확히 `font.g1t`, `msgsec13.dat`, `msgsec20.dat` 3개다. 최종 SHA-256은 `font=FFC1D34D01D71F12B670967DD080F33387CC3BDC9F0F0C5CBE9381DB4CE2AC8A`, `msg13=50AAC85614908178B28B03FA16A403C4012806DF15BA8AFF597694E989A127D8`, `msg20=8194660D276DA5AF5D640194A48737762476FE5D084EB9D6D11AF47324B995D7`다. builder write→독립 verifier→deterministic check→verifier가 모두 PASS했다.
- `Extracted_Text\biography_text_review_v186.html`을 재생성했다. 총 978행, 한글 974 / 일본어·혼입 0 / 의도된 빈 항목 4, layout 위반 0이며 각 행의 바이트·공용 파일 여유를 표시한다. 최신 정적 Patch 권위는 **v186-intermediate**, Citra 실화면 확인은 pending이다.

### 2026-09-02 v125-intermediate — #106 설정 선택지 + #107 공통 ADR 대사 전수 + #108 전투 보고서 지표

- exact `v124-intermediate` 전체 Patch를 `analysis\v125_issue106_107_108_baseline\PatchSnapshot`에 봉인했다. 로컬 `Issue106/107/108.html`과 첨부 PNG를 먼저 확인하고 immutable Original과 v124 `code.bin`의 실제 runtime source를 대조했다. Original/Rebuild/Backup은 수정하지 않았으며 v125의 게임 파일 변경은 `ExeFS/code.bin` 1개뿐이다.
- **#106:** 게임 시작 설정 화면의 값 문자열을 같은 설정 class로 전부 확인해 15개를 fixed slot 안에서 수정했다. 최신 값은 **`사실 / 가상`**, **`초급 / 상급 / 특급`**, **`반영함 / 반영안함`**, **`있음 / 없음`**, **`표준 / 정지`**, **`본다 / 안본다`**, **`3DS 모드 / 오리지널 모드`**다. `0x1DC2DC/2E0`, `0x1DC620..630` 등 관련 pointer table/word는 수정하지 않았다.
- **#107:** 제보 화면의 실제 source는 `code.bin 0xB90B8` Original **`従事中のコマンドがキャンセル\nされますがよろしいですか？`**였고 최신 문구는 **`진행 중인 명령이 취소됩니다\n계속하시겠습니까？`**다. 한 건만 수정하지 않고 Original ARM `ADR`가 직접 참조하는 `0xB0000..0xBA800` 공통 text bank에서 `원문 10B 이상 / kana 2자 이상 / v124 일본어 잔존`을 만족하는 장문 대사/event를 재도출했다. v124 잔여는 정확히 **6개**였고 `0xB0194`, `0xB01B0`, `0xB01D0`, `0xB3630`, `0xB8F04`, `0xB90B8`을 모두 한글화해 v125 잔여 **0건**으로 닫았다. `0xB01B0` 원문을 처음 target JSON에 `臥龍`으로 기록했으나 immutable Original은 **`臥竜`**이어서 첫 빌더가 Patch write 전에 source gate로 중단했고, 원문 기록을 바로잡은 뒤 재빌드했다. 짧은 `なし` 같은 enum/data는 공통 대사로 오분류하지 않는다.
- #107 추가 조립형 문구는 고정 슬롯과 format token을 유지해 ` 유비에게 군사로\n등용된 ` + `%s이(가)%s듯함`, ` 와룡의 초려를\n찾는 `, `%s%s이(가)\n동맹을 파기했습니다`, `%s%s에서\n%s이(가) 왔습니다`로 보정했다. 각 Original ADR instruction/ref는 byte-exact다.
- **#108:** 전투 보고서의 상승 항목은 `code.bin 0x1AB378`에서 시작하는 7-word metric pointer table이 실제 sibling class다. 기존 `병사사기`와 빈 항목은 그대로 두고 미번역 5개를 **`訓練度→훈련도`, `開発領域→개발영역`, `耕作率→경작률`, `治水度→치수도`, `商業値→상업치`**로 수정했다. 최종 table visible readback은 `병사사기 / 훈련도 / (빈 항목) / 개발영역 / 경작률 / 치수도 / 상업치`이며 7개 pointer는 v124와 byte-exact다.
- 전체 code target은 **26개 = #106 15 + #107 6 + #108 5**이며 모두 원 fixed slot 안에 들어가 relocation/new glyph 없이 처리했다. v124 대비 code diff는 **285B**, 최종 SHA-256은 `62C2751A7437C031EED12FDFBD6005F9936D4369A60194812D59883BF76A0F25`다. `font.g1t`, Message, Scenario, 이미지 및 모든 비-code Patch 파일은 v124 byte-exact다.
- 첫 성공 빌드 뒤 독립 verifier PASS, 완성 v125 위 빌더 재실행 전후 `code.bin`과 report SHA가 완전히 동일했다. report SHA-256=`1EDD0E550B35D04D583135A7A4E353C89273D49BC88EED1DF4C9DCD1CB74EDE8`. 재빌드 후 verifier를 다시 실행해 26/26 readback, #107 common ADR residue 6→0, #108 metric table, setting pointer guard, 비대상 manifest를 재검증했다. 정적/결정적 검증은 완료됐으며 **Citra 실화면 확인은 pending**이다.
- 권위 자료: `analysis\v125_issue106_107_108_targets.json`, `analysis\v125_issue106_107_108_report.json`, sealed baseline `analysis\v125_issue106_107_108_baseline\PatchSnapshot`, `tools\build_sangokushi2_v125_issue106_107_108.py`, `tools\verify_sangokushi2_v125_issue106_107_108.py`.

### 2026-09-02 v124-intermediate — #101/#110 `곁` 렌더 + #102 자연재해/event + #109 고립바이트 전수 + #111 `(이)라는`

- exact `v123-intermediate` 전체 Patch를 `analysis\v124_issue101_102_109_110_111_baseline\PatchSnapshot`에 봉인하고 작업했다. 로컬 `Issue101/102/109/110/111.html`과 첨부 PNG를 확인한 뒤 immutable Original과 현재 v123의 runtime source를 대조했다. Original/Rebuild/Backup은 수정하지 않았다.
- **#101/#110:** 제보 화면 `장각 휘하의 장개이(가) / 손견의 곁으로 간 듯합니다`에서 `곁=9978/physical3710`이 Citra에서 stock CJK **`凅`**로 렌더되는 far-alias 실패를 확인했다. code/Message 전체 실제 text use를 감사해 `code.bin` 1곳, `msgsec07` 4곳, `msgsec08` 3곳 = **8곳 전부**를 새 low-lead `곁=96A0/physical3185`로 재인코딩했다. `code.bin`에 우연히 존재하는 nontext `9978` 2곳은 보존했다. physical3185는 위/아래 vertical edge가 깨끗하고 v123 text runtime ref=0/current owner=0이며 packed Sam3 `곁` readback이 pixel-exact다. 왼쪽 인접 edge 2px 때문에 최종 런타임 승인 상태는 Citra-pending으로 둔다.
- **#102:** 기존 `msgsec08` 한국어 이벤트 대사와 별도로 Citra가 사용하는 **`code.bin` event formatter + event-name pointer table**을 실제 owner로 확정했다. `いなご/疫病/洪水/台風/反乱/豊作/凶作`은 각각 **`황충/역병/홍수/태풍/반란/풍작/흉작`**, 공통 `各地`는 **`각지`**로 수정했다. hardcoded formatter는 `각지에서 역병이 유행 중입니다`, `홍수가 발생했습니다\n각지에서 피해 발생 중`, `태풍이 발생했습니다\n각지에서 피해 발생 중`, `각지가 풍작으로 번성 중입니다`, `각지에 흉작이 발생 중입니다`로 정리했다. `풍`은 기존 low-lead `9559/physical2927`, `흉`은 신규 `97FA/physical3463`을 사용한다. event pointer table 첫 7개 word와 formatter ADR/xref는 byte-exact로 보존했다.
- **#109 + 동일 오류 class:** `%s에서? 원군을`의 `?`는 문장부호가 아니라 번역 문자열 사이에 홀로 남은 Shift-JIS lead byte **`0x89`**였다. active `msgsec04/06/07/08` 전체를 Original header와 source-aware 대조해 같은 고립 `0x81..0x9F` class를 전수 감사했고 **14개 runtime correction / 최종 orphan lead 0건**으로 닫았다. 대표 보정은 `%s에서 원군을\n파견하시겠습니까？`, `지원 부대를 보내시겠습니까？`, `현재 위임 중입니다\n해제하시겠습니까？`, `이 도시에는 의원이 없습니다`, `백성이 반란을 일으키지\n않으면 좋겠습니다만`, `무장의 충성도가 떨어졌습니다`, `그자의 몇백 배라도\n일해 보이겠습니다`다. `msgsec06 header[122]`는 잘린 duplicate source를 새로 작성하지 않고 정상 동일문장 header[121]로 redirect했고, `msgsec07 header[229]`의 `입니다<81>`은 분할 punctuation 의미를 복원해 **`입니다.`**로 정상화했다.
- **#111:** 이름표나 `fix_data.bin`을 건드리지 않았다. actual assembler owner는 `code.bin 0x15B894/0x15B8AC/0x15B8BC`이며 각각 **`(이)라는\n자가 있습니다` / `그대의 영지에 ` / `(이)라는\n자가 있소`**로 수정했다. 따라서 기대 조립은 **`그대의 영지에 고당륭(이)라는\n자가 있소`**다. Original ADR source association은 byte-exact로 유지한다.
- v123 대비 실제 변경 게임 파일은 정확히 **5개**다. SHA-256: `code=CE49C0CF0F40DB41B611A5C2CD60EF3B1446473509D6703B0EAB68E1FD65D09A`, `font=4E66EA4DB5F6AADA7226DEFDD0CB7EA5F4F8927FB8C22CA09512CD89ED68CE48`, `msgsec06=70427951F930A6F4B7F705AECC38C9DB685D22D0E43627DA2085B2153F300D7F`, `msgsec07=C3B4A62A3A7FAC6846436F1362555D4524739DFB4A41B03506BC6BE66179054E`, `msgsec08=E75EBAF82ABC1369B21BA666D841B8596B0B1C1C0002413FEE6E4284F161BAE1`. font 변경 cell은 정확히 `[3185,3463]`이다.
- 첫 빌드 후 독립 verifier가 PASS했고, 완성 v124 위에서 빌더를 다시 실행한 전후 **게임 파일 5개 + report SHA-256이 모두 완전히 동일**했다. report SHA-256은 `3229A844288E53E49937E4AE7FC3999C211CFFA33459079300D53F869E63FD82`. 이어 독립 verifier를 다시 실행해 `message rows=14`, `active orphan highbyte=0`, `event rows=17`, `issue111 rows=3`, old `곁/풍` Message/Scenario residue=0을 재확인했다. 정적/결정적 검증은 완료됐으며 **Citra 실화면 확인은 pending**이다.
- 권위 자료: `analysis\v124_issue101_102_109_110_111_targets.json`, `analysis\v124_issue101_102_109_110_111_report.json`, sealed baseline `analysis\v124_issue101_102_109_110_111_baseline\PatchSnapshot`, `tools\build_sangokushi2_v124_issue101_102_109_110_111.py`, `tools\verify_sangokushi2_v124_issue101_102_109_110_111.py`.

### 2026-09-02 v123-intermediate — 허자장 조립 대사 전수 재감사 + #095/#097/#100/#103 수정

- exact `v122-intermediate` 전체 Patch를 `analysis\v123_xuzijiang_issue095_097_100_103_baseline\PatchSnapshot`에 봉인하고 작업했다. 로컬 #095/#097/#100/#103 HTML과 PNG를 대조한 뒤 immutable Original `code.bin`의 원문, 23-word fragment pointer table, caller literal-pool xref를 함께 추적했다. Original/Rebuild/Backup은 수정하지 않았다.
- **v120 #095 source 판정 폐기:** `code.bin 0x159B04` Original은 **`我が遁甲天書三巻を\nお譲り致しましょう`**이며 좌자 본인이 자기 책을 주는 별도 증여 대사다. 따라서 이 문자열을 #095 허자장 스크린샷 source로 본 v120 판정은 잘못이었다. 최신 표기는 프로젝트 아이템 권위(v70)에 맞춰 **`제 둔갑천서3권을\n드리도록 하지요`**로 유지한다.
- 허자장 계열 실제 owner는 `code.bin 0x1E5DDC..0x1E5E34`의 **23개 pointer fragment table + 함수 내부 literal** 조립 경로다. Original 조립은 #095/#103 `<person> + の + <item> + を\nお求めなされ`, #097 `<item> + は + <person> + の\n手には余りましょう`, #100 `<person> + が + <item> + を\n持っているそうな`임을 확인했다. 테이블 owner를 증명하는 literal-pool xref 11개도 Original/v122/v123에서 byte-exact로 gate했다.
- **#095:** 기대 조립을 **`좌자에게 둔갑천서3권을(를)\n구하시오`**로 수정했다. **#103:** **`장각에게 태평요술서을(를)\n구하시오`**. 공용 일본어 `の@0x1D0508`를 전역 치환하지 않고, 기존 C-string 뒤 zero slack에 local helper **`에게 `**를 설치해 허자장 table의 해당 pointer만 redirect했다.
- **#097:** **`태평요술서, 장각에게는\n과한 것이오`**로 수정했다. 공용 `は@0x1D050C`는 그대로 보존하고 허자장 table에서만 local helper **`, `**로 redirect했다. 과거 Citra 실패 alias `찰=996F`를 쓰지 않는다.
- **#100:** 동적 인물명/아이템명 구조를 보존해 **`손견이(가) 맹덕신서을(를)\n가졌다는군`**으로 수정했다. 인물명 뒤 `が` table slot은 local **`이(가) `**, item suffix `を\n持っているそうな`는 **`을(를)\n가졌다는군`**으로 보정했다.
- 같은 오류 class 전체를 계속 감사해 23개 table row와 함수 내부 reviewed literal 9개를 모두 역읽기했다. 추가 보정은 ` 근처에\n전국의 보물이 있다더군`, `천명은 못 피하오\n` + 동적 인물 뒤 `조차`, `하루 천리 달릴 명마는\n` + ` 소유라오`, 동적 인물 위협 suffix **`이(가) 주군을\n노리는 듯하오`**, `유명한 <item>,\n<person> 소유 중이다` 계열이다. 최종 감사 범위의 일본어 kana 잔여는 **0건**이다.
- local helper 4개(`이(가) `, `에게 `, `, `, `의 `)는 기존 한국어 C-string의 **NUL 뒤 zero slack**에만 넣고 허자장 table pointer 4개만 redirect했다. 각 helper VA의 최종 raw pointer 참조는 정확히 1회다. shared global `の/は` 조각은 byte-exact 보존했다.
- v122 대비 실제 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다. SHA-256=`906F64FBB8A83DF19218C733B75C1A9B4DDF4246C6A01FECD8F19DC131F91A63`. 신규 glyph=0이며 `font.g1t`와 모든 비대상 Patch 파일은 v122 byte-exact다.
- 빌더 적용→독립 verifier→완성 v123 위 결정적 재빌드→verifier 재실행이 모두 PASS했다. 독립 verifier는 23 table row, embedded literal 9개, fixed string 13개, local helper 4개, 네 이슈의 실제 조립 예시, `0x159B04` 별도 좌자 대사, banned alias 부재를 다시 검증했다. **Citra 실화면 확인은 pending**이다.
- 권위 자료: `analysis\v123_xuzijiang_issue095_097_100_103_targets.json`, `analysis\v123_xuzijiang_issue095_097_100_103_report.json`, sealed baseline `analysis\v123_xuzijiang_issue095_097_100_103_baseline\PatchSnapshot`, `tools\build_sangokushi2_v123_xuzijiang_issue095_097_100_103.py`, `tools\verify_sangokushi2_v123_xuzijiang_issue095_097_100_103.py`.

### 2026-09-02 v122-intermediate — #075 문구 재검수 + #096 동맹 편집 동일 클래스 + #098 조건 확인 쉼표 보정

- exact `v121-intermediate` 전체 Patch를 `analysis\v122_issue075_096_098_baseline\PatchSnapshot`에 봉인하고 작업했다. 사용자 Citra 스크린샷으로 v121의 #075 source/출력 위치는 실제 화면에서 확인됐지만 문구가 직관적이지 않아 재번역했고, 로컬 `Github_Issue\Issue096.html`, `Issue098.html`과 assets PNG를 확인해 두 신규 이슈의 실제 화면을 대조했다. Original은 source/format 증거 확인에만 읽었고 Rebuild/Backup은 수정하지 않았다.
- **#075 후속:** 78B fixed span `msgsec01 0x458..0x4A6`에서 사용자 제안 1은 `값`을 포함해 84B라 6B 초과, 제안 2는 77B라 용량상 들어간다. 다만 `값`은 현재 map에 없고 엄격 donor probe의 유일 low-lead 후보 `96D7/physical3240`은 아래 physical+73 셀의 top edge에 4px 잉크가 있어 기존 세로 bleed 회귀를 배제할 수 없으므로 **신규 donor를 만들지 않았다.** 의미를 유지하면서 기존 glyph만 쓰는 **`사실무장 편집\n\n상성을 편집해도\n기존 장수의 상성 수치는\n초기 상태를 유지합니다`**를 77B + ASCII space 1B로 적용했다.
- **#096:** 제보 화면 `하진と他勢力で同盟を結んだり破棄します`의 실제 source는 `msgsec09 0x88E..0x8AB`이다. 한 줄만 고치지 않고 같은 세력/동맹 편집 fixed runtime class를 확장해 6개 span을 함께 한글화했다: `0x836` **`: 적대 편집 세력 선택`**, `0x858` **`: 동맹 편집 세력 선택`**, `0x88E` **`: 타세력 동맹 체결/파기`**, `0x8AE` **`: 세력 편집 확정`**, `0x8C4` **`적대 수치를 입력`**, `0x8DC` **`동맹 기간을 입력`**. 앞 네 개의 동적 세력명 prefix `02 2D`는 그대로 보존하며, 기존 #091 한국어 `0x874..0x88B`도 v121 byte-exact다. `msgsec09` 88-word header와 모든 `05 05 05` separator 위치는 유지했다.
- **#098:** `code.bin 0x180DB0`의 sibling `%s님, `이 이미 쉼표를 출력하는데 `0x180DB8` formatter가 `%s, 조건을 받습니까？`로 번역되어 두 번째 쉼표가 생긴 구조를 확인했다. Original 두 번째 formatter `%sこの条件を\nのみますか？`의 `%s`는 삭제하지 않고 **`%s이 조건을 받습니까？`**로 수정했다. 따라서 조립 기대 화면은 **`하진님, 이 조건을 받습니까？`**이며 sibling `%s님, `은 byte-exact다.
- v121 대비 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec09.dat` 3개다. `font.g1t`와 모든 비대상 Patch 파일은 v121 byte-exact다. SHA-256: `code=AD425EB39A97F08F9B090A87C1E3508EAF96099983DD5202C2FAF9AFF1DE7A6C`, `msgsec01=5F874EDDEBB45D7C0C562524024E80E20948832C50EB433EC69701640F67A785`, `msgsec09=7CE542E85F6B09306B6056EE6A52FED310F7E51F8108DAE0A8E0D2F9DC4D3BC3`.
- 빌더 첫 적용→독립 verifier→완성 v122 위 결정적 재빌드→verifier 재실행이 모두 PASS했다. 최종 gate는 #075 77B readback/78B fixed slot, #096 6개 visible 일본어·한자 잔여 0 및 msgsec09 header/separator 보존, #098 `%s` format token과 sibling honorific prefix 보존, `font changed=false`, 비대상 v121 byte-exact다. **v122의 Citra 실화면 확인은 pending**이며 사용자가 누적 수정사항과 함께 검수할 예정이다.
- 권위 자료: `analysis\v122_issue075_096_098_targets.json`, `analysis\v122_issue075_096_098_report.json`, sealed baseline `analysis\v122_issue075_096_098_baseline\PatchSnapshot`, `tools\build_sangokushi2_v122_issue075_096_098.py`, `tools\verify_sangokushi2_v122_issue075_096_098.py`.

### 2026-09-02 v121-intermediate — #069 저장/로드 슬롯 상태 + #075 사실무장편집 고정 UI 한글화

- exact `v120-intermediate` 전체 Patch를 `analysis\v121_issue069_075_baseline\PatchSnapshot`에 봉인하고 작업했다. `Sangokushi 2 Original`은 source/ADR 및 원 제어코드 확인에만 읽었고, `Sangokushi 2 Rebuild`와 `Backup`은 수정하지 않았다. 로컬 `Github_Issue\Issue069.html`, `Issue075.html`과 각각 2장/4장의 assets PNG를 먼저 확인했다.
- **#069:** 저장/로드 슬롯의 `新規 / 未使用`는 이미지가 아니라 `ExeFS/code.bin`의 runtime 텍스트임을 확정했다. `0xD565C 新規`는 **`신규`**로 exact 4B 교체했고 Original ARM `ADR@0xD5570`이 계속 같은 주소를 가리킨다. `0xD5664 未使用`는 **`미사용`**으로 exact 6B 교체했고 `ADR@0xD5574`를 byte-exact 보존했다. 이 이슈 때문에 이미지/G1T는 수정하지 않았다.
- **#075:** 사실무장편집의 제보 화면 4종은 `RomFS\Message\msgsec01.dat`의 fixed physical 문자열임을 확정했다. `0x458..0x4A6`은 **`사실무장 편집 / (빈 줄) / 상성 편집에서 정한 / 상성 좋은 장수의 수치는 / 초기 상태가 됩니다`**, `0x4A9..0x4B7`은 **`편집반영`**, `0x4BA..0x4E3`은 **`사실무장 편집을 / 게임에 반영합니다`**, `0x4E6..0x4F2`는 **`초기화`**, `0x4F5..0x511`은 **`사실무장 편집을 / 초기화합니다`**, 무장 선택 화면 상단 `0x1F5B..0x1F70`은 **`편집할 무장을 선택`**으로 수정했다.
- #075의 제목은 Original의 `ESC C9 ... ESC C1` 색상/스타일 wrapper를 그대로 보존했다. 편집반영 설명에 있던 `ESC K/H` 네 토글도 **`K,H,K,H` 순서와 개수**를 그대로 유지하면서 한국어 visible text는 최종 H/fullwidth 상태에서 렌더되도록 배치했다. `msgsec01.dat`는 **9057B 고정**, 전체 `05 05 05` separator 위치도 v120과 byte-exact이며 variable-length repack을 하지 않았다.
- `상성값` 표현도 검토했으나 현재 safe map에 `값` glyph가 없었다. 이번 문장은 의미 손실 없는 자연스러운 게임 용어 **`수치`**로 작성 가능해 신규 donor를 만들 필요가 없었다. 따라서 v121 신규 font glyph는 0개이고 `RomFS/Common/Font/font.g1t`는 v120과 byte-exact다.
- v120 대비 실제 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 2개다. SHA-256: `code=655C79B8B51D76DF5D7ECAD2E1A20F0D3781B4C6B47C74F5CEB8F8109A04997A`, `msgsec01=02808AA34E628504F508D41BDB9BBEF5B9C6E80822660C10F8892F93FB7D7F43`.
- 빌더 첫 적용→독립 verifier→완성 v121 위 결정적 재빌드→verifier 재실행이 모두 PASS했다. 최종 gate는 #069 두 ADR target/명령 byte-exact, #075 6개 target visible readback의 일본어 잔여 0, `msgsec01` file size/separator/control 보존, `font changed=false`, 비대상 Patch 파일 v120 byte-exact다. **Citra 실화면 확인은 pending**이며 사용자가 #069/#075를 포함해 누적 수정사항을 한꺼번에 검수할 예정이다.
- 권위 자료: `analysis\v121_issue069_075_targets.json`, `analysis\v121_issue069_075_report.json`, sealed baseline `analysis\v121_issue069_075_baseline\PatchSnapshot`, `tools\build_sangokushi2_v121_issue069_075.py`, `tools\verify_sangokushi2_v121_issue069_075.py`.

### 2026-09-02 v120-intermediate — #092~#095 편집/UI·동적 조사·ADR 직참조 일본어 대사 전수 보정

- exact `v119-intermediate` 전체 Patch를 `analysis\v120_issue092_095_baseline\PatchSnapshot`에 봉인하고 작업했다. `Sangokushi 2 Original`은 source/ADR 증거 확인에만 읽었고, `Sangokushi 2 Rebuild`와 `Backup`은 수정하지 않았다. 로컬 `Github_Issue\Issue092~095.html`과 각 assets PNG를 먼저 확인한 뒤 실제 화면 유형별 runtime source를 추적했다.
- **#092:** 편집 조건 미충족 문구는 `msgsec09.dat`의 fixed physical span을 **`조건 미충족\n편집할 수 없음`**으로 수정했다. 기존 88-word header와 모든 `05 05 05` separator 위치는 byte-exact로 유지했다.
- **#093:** `code.bin`의 `最小@0x1CFE48`, `最大@0x1CFFDC`를 각각 **`최소` / `최대`**로 동일 4B slot 안에서 교체했다.
- **#094:** 제보된 `허자장라는 자가` 한 문장만 고치지 않고 동적 인물명 뒤 조사가 받침에 따라 깨질 수 있는 동일 계열을 전수 감사했다. active Message `msgsec04/06/07/08`에서 **68개 row**를 `이(가) / 은(는) / 을(를) / (으)로 / 과(와) / (이)라는` 형태로 보정했고, 번역된 `code.bin` formatter에서도 동일 조사 패턴 **31개**를 함께 보정했다. verifier 기준 active dynamic-name 조사 잔여와 translated-code formatter 조사 잔여는 모두 0건이다.
- **#095 (당시 판정, v123에서 source 연결 폐기):** v120에서는 `code.bin 0x159B04`를 #095 스크린샷 source로 연결해 **`제 둔갑천서 3권을\n드리도록 하지요`**로 수정했다. 후속 v123 Original/조립 경로 재분석에서 이 C-string은 **좌자 본인의 별도 증여 대사**이고 #095 허자장 화면은 `0x1E5DDC..0x1E5E34` fragment table 조립 경로임이 확인됐다. 따라서 **#095에 대한 source association만 폐기**한다. 다만 v120에서 수행한 ARM ADR 직참조 일본어 C-string 감사 자체와 dialogue/event 16개 한글화, 비대사 9개 allowlist는 별도 감사 성과로 유지한다.
- 이번 target에 필요한 한글은 모두 기존 안전 alias로 인코딩 가능해 **신규 font donor는 0개**다. `RomFS/Common/Font/font.g1t`는 v119와 byte-exact다. 앞으로 필요한 한글이 없을 때는 문장을 어색하게 바꾸지 않고 runtime ref=0·현재 owner=0·historical collision=0·valid-JIS·bleed 안전을 통과한 미사용/저사용 일본어 한자 글리프를 donor로 재사용하는 사용자 규칙을 유지한다.
- v119 대비 실제 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat` 6개다. SHA-256: `code=1D37BF1746BC2146EE10C04F32E0897B13E26BF34E7B7A022D6511F7215C304C`, `msg04=57D8772E382497B706C005BD1AD3DFCEA2E3160E0482EFEAD5FD5917680DE5BC`, `msg06=0183B37DD4F204400108CC99363450A4AB54D16BCAE29619C1D76394A2E31BB8`, `msg07=A8DBF126B3CDF9B7BD073885F0821A6BA94ABA98F2814A0C1BFD8B694919F6E2`, `msg08=4D74553520F291701C56395F9A5B6D3E7367F6AF6CB85677FE3AEE7E02FA24FE`, `msg09=4668B3278D36130D4A293850766BE5826503A964BCDA7A2BA9E936DC6205C7BF`.
- 빌더 첫 적용→독립 verifier→완성 v120 위 결정적 재빌드→verifier 재실행이 모두 PASS했다. 최종 gate는 `active dynamic-name particle remaining=0`, `translated code particle remaining=0`, `ADR Japanese dialogue remaining=0`, `font changed=false`, `msgsec09 header/separator preserved=true`, 비대상 Patch 파일 v119 byte-exact다. **Citra 실화면 확인은 pending**이다.
- 권위 자료: `analysis\v120_issue092_095_targets.json`, `analysis\v120_issue092_095_report.json`, sealed baseline `analysis\v120_issue092_095_baseline\PatchSnapshot`, `tools\build_sangokushi2_v120_issue092_095.py`, `tools\verify_sangokushi2_v120_issue092_095.py`.

### 2026-09-02 v119-intermediate — 상단 계절 표시 + 시즌 전환 애니메이션 로고 한글화

- exact `v118-intermediate` 전체 Patch를 `analysis\v119_season_localization_baseline\PatchSnapshot`에 봉인하고 작업했다. Original은 v118 Patch에 존재하지 않던 시즌 ARC 4개의 최초 편입 source로만 읽었으며, Rebuild/Backup은 수정하지 않았다.
- 상단 단독 계절 문자열은 `code.bin`의 4바이트 고정 슬롯 `春@0x1CED0C / 夏@0x1CEE20 / 秋@0x1CED00 / 冬@0x1CED18`이다. 원 한자 글리프를 전역 덮어쓰지 않고 `봄=92F5/physical2518`, `여름=9474/2766`, `가을=95A1/2998`, `겨울=96CC/3229`의 fresh local-only alias를 각 슬롯 한 곳에서만 호출한다. `봄`은 Sam3 정상 한 음절 도트, 나머지 3개는 사용자가 만든 `Common\Font\season\*14x14.png` 합자 도트를 사용했다.
- 네 donor는 모두 low-lead valid CP932이며 v118 runtime 참조 0, prose/name map owner 0, 역사적 alias/physical 예약 0, physical ±73 세로 인접 예약 0을 증명했다. 완성본 raw 참조는 각 alias가 대응 `code.bin` 슬롯 한 곳에만 존재한다. packed font 변경 셀은 정확히 `[2518,2766,2998,3229]`, 그 밖의 셀 diff=0이고 PNG/Sam3 A4 역읽기는 pixel-exact다.
- 시즌 전환 화면은 단일 PNG가 아니라 `stg_season_effect_{spring,summer,autumn,winter}.arc` SARC 안의 BFLIM·BFLYT·BFLAN 합성이다. 각 runtime ARC에서 대응 `timg/season_{season}.bflim` 한 개만 사용자 68×68 RGBA PNG로 교체했다. 네 BFLIM은 format 9 RGBA8, orientation 4, padded 128×128이며 원본 decode→encode identity와 편집 PNG→packed BFLIM→PNG pixel-exact 역읽기를 통과했다. 꽃 조각, 다른 계절 texture, `.bflyt` 배치, `.bflan` 애니메이션을 포함한 모든 비대상 SARC member는 Original과 byte-exact다.
- v118 대비 Patch 변경/추가 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Stg/stg_season_effect_spring/summer/autumn/winter.arc` 6개다. SHA-256: `code=B45B7DC7E28EE495D1047D3D55C6615329A8F444E0F0905F5383B4521AC4B172`, `font=043666FE075B2B9A42CAFC6B39D8B09EC406E49FE4E100DCF14E5F7B5B6D6C02`, `spring=EBF3B26FA63F5407FF4F35D6A5EDE767CEE4E75942DE2E9982504F49BB81CD60`, `summer=C6BF4C9F8AB315B530367AFF5E72F5096D85479A3F40677715722C38FE97714F`, `autumn=710563F12E88A2BDD6783D1465C495A529414F7F42AC43248E0A3832342B9B22`, `winter=7638D32D74563577DCB730D96F8625CCD9B8A9E7B035815D106BB74966C89D04`.
- 빌더 첫 실행→독립 verifier→완성본 위 결정적 재빌드→verifier 재실행이 동일 SHA로 PASS했고, v103 dialogue audit도 `code residue=0 / active Message=0 / mixed body=0`이다. 정적 역검증은 완료됐지만 상단 네 계절과 시즌 전환 애니메이션의 **Citra 실화면 확인은 pending**이다.
- 권위 자료: `tools\build_sangokushi2_v119_season_localization.py`, `tools\verify_sangokushi2_v119_season_localization.py`, `analysis\v119_season_localization_targets.json`, `analysis\v119_season_localization_report.json`, `analysis\v119_season_localization_preview.png`, sealed baseline `analysis\v119_season_localization_baseline\PatchSnapshot`.

### 2026-09-02 v118-intermediate — #087 `몽/붙` alias 충돌 복구 + #091 실제 input-prompt runtime 보정

- exact `v117-intermediate` 전체 Patch를 `analysis\v118_issue087_091_runtime_repair_baseline\PatchSnapshot`에 봉인하고 작업했다. Original은 read-only 증거로만 사용했고 Rebuild/Backup은 수정하지 않았다.
- **#091 군주 선택 잔여 일본어 1글자:** v117은 `msgsec09 0x874`의 `02 2D C4`를 3바이트 동적 군주 토큰으로 오판해 `C4=반각 ﾄ`까지 보존했다. 사용자 Citra에서 바로 이 일본어 글자 하나가 `유선…의 타세력 적대 편집` 앞에 남는 것을 확인해 v117 token 판정을 폐기했다. 실제 동적 prefix는 **`02 2D` 2바이트**, `C4`는 일본어 조사 텍스트다. v118은 `02 2D`만 byte-exact 보존하고 **`의 타세력 적대 편집`**을 이어 붙였다.
- **#091 도시 편집 수치 입력 prompt 16종:** v117에서 번역한 `인구/금/병력…을 편집`은 버튼 hover/help 계층이었고, 버튼을 선택한 뒤 실제 입력 화면의 상단 prompt는 별도 `msgsec09 0x66A..0x7F5` physical 문자열 16개였다. 이 16개가 v117에서 immutable Original과 byte-exact임을 확인한 뒤 `인구를 입력하십시오`, `금을 입력하십시오`, `병력을 입력하십시오`, `군량을 입력하십시오`, `노/강노/군마/투함/몽충/주가를 입력하십시오`, `개발/경작/치수/관개/상업/민충 수치를 입력하십시오`로 fixed-position 치환했다. 88-word header와 모든 `05 05 05` separator 위치는 그대로다.
- **#087 `이붙/여붙` + `몽충→붙충` 원인:** runtime map은 이미 **`몽=94CE / physical2855`, `붙=969B / physical3180`**였지만, 실제 v117 `font.g1t`의 physical2855가 Sam3 `붙` 도트로 덮여 있어 physical2855와 physical3180 두 셀 모두 `붙`으로 중복돼 있었다. immutable Original `fix_data.bin`에서 `呂蒙 / 李蒙 / 蒙恬`을 확인했고 현재 번역 바이트는 `여몽 / 이몽 / 몽염`으로 정상이라 이름 텍스트는 수정하지 않았다.
- `94CE` baseline runtime 참조 14곳을 전수 역읽기했다. code의 `여몽` 대사 3건·`몽충건조`, Message의 몽충 관련 6건, `fix_data`의 여몽/이몽/몽염 3건은 모두 `몽` 의미였다. 단 **`msgsec15 0x36BD` 한 곳만 `사례금 등 조건을 붙일 수 있습니다.`의 `붙`을 잘못 94CE로 사용**하고 있었다. 이 한 곳만 정상 `붙=969B`로 재인코딩한 뒤 physical2855를 Sam3 `몽`으로 복원했다. v118에서 새 `몽충을 입력하십시오`가 94CE 참조를 1회 추가하므로 최종 94CE 참조는 다시 14회이며 **14회 전부 `몽` 의미**다.
- font packed readback에서 변경 atlas cell은 정확히 `[2855]` 하나뿐이다. `붙=969B/physical3180`, `떻=8A98/physical921`, #086 합자 physical948/1190, `챔=95AC/physical3009`는 pixel-exact 보존했다. `fix_data.bin`, `code.bin` 및 그 밖의 비대상 Patch 파일은 v117 byte-exact다.
- v117 대비 실제 변경 파일은 정확히 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec09.dat`, `RomFS/Message/msgsec15.dat` 3개다. SHA-256: `font=168CC87C370A6B8EEA765E2F6E52027B32779420AA6BCDA609FFABEA16DD1FD7`, `msgsec09=FD8DD4C05BA21D49D4FB8EB39C3BF6715EB6E9CA83EF7D37AE6AF10DAD3484AF`, `msgsec15=29DA1795A824EEED70980DF7D0BCE1C3896B3711C9D81DCE461E5BC0DEF917AE`.
- 빌더 첫 실행 → 독립 verifier → 완성 v118 위 결정적 재빌드 → verifier 재실행이 모두 동일 SHA로 PASS했다. v105 dialogue audit도 layout violation=0, bad alias code/message=0이며 기존 #084-1 후보 및 legacy mixed/active 수치는 v117과 동일하다. **Citra 실화면 확인은 pending**이다.
- 권위 자료: `tools\build_sangokushi2_v118_issue087_091_runtime_repair.py`, `tools\verify_sangokushi2_v118_issue087_091_runtime_repair.py`, `analysis\v118_issue087_091_runtime_repair_targets.json`, `analysis\v118_issue087_091_runtime_repair_report.json`, sealed baseline `analysis\v118_issue087_091_runtime_repair_baseline\PatchSnapshot`.

### 2026-09-02 v117-intermediate — #066 원본 C4 색상 복원 + #091 편집기능 잔여 runtime 문구

- exact `v116-intermediate` 전체 Patch를 `analysis\v117_issue066_color_issue091_baseline\PatchSnapshot`에 봉인하고 작업했다. Original/Rebuild/Backup은 수정·빌드 입력으로 사용하지 않았다.
- **#066 Citra 재검수:** v116의 잠금 안내 한국어 문구 자체는 정상이라고 사용자가 확인했으나, 원본은 초록색인데 v116은 흰색으로 출력됐다. Original 잠금 안내가 `ESC C4 = 1B 43 34` 색상 제어를 사용하고, 다른 Message 사례에서도 C4가 줄바꿈을 넘어 상태 유지되는 것을 확인했다. 따라서 세 히든 시나리오 잠금 안내 모두 고정 슬롯 시작에 **원본과 같은 C4를 복원**했다. 2번째 82B 슬롯은 기존 visible text 80B + C4 3B가 1B 초과하므로 의미 변화 없이 첫 `플레이 할`만 표준 표기 `플레이할`로 붙여 1B를 확보했다. 나머지 문구는 유지했다.
- **#091 로컬 이슈 8장 전부 확인:** 편집기능 관련 잔여 일본어는 한 파일에 모여 있지 않고 `msgsec18.dat` 상단 도움말 계층과 `msgsec09.dat` 실제 하위 편집/runtime 계층으로 분리되어 있었다. `msgsec18 0x2D2`의 공통 상단 설명은 **`도시, 세력, 특산아이템을 편집합니다.`**, `0xDA5`의 도시 선택 상단은 **`편집할 도시를 선택`**으로 수정했다.
- **세력 편집 동적 군주명:** #091 스크린샷의 `하진 他勢力...` source는 최초 분석 중 `msgsec18` 후보로 잘못 분류했지만 exact-byte gate가 Patch write 전에 실패해 오분류를 차단했다. 실제 source는 **`msgsec09 0x874..0x88B`**이며 시작 3B `02 2D C4`는 동적 군주 토큰이다. 이 토큰을 byte-exact 보존하고 suffix만 **`의 타세력 적대 편집`**으로 바꿔 예시 `하진의 타세력 적대 편집`을 목표로 한다.
- **도시 편집 하위 도움말 17종:** `msgsec09`의 기존 `05 05 05` separator 사이 물리 문자열을 fixed-position으로 직접 치환했다. 인구/금/병력/군량은 `~을 편집합니다`, 노/강노/군마/투함/몽충/주가는 `~ 수를 편집`, 개발/경작/치수/관개/상업/민충은 `~ 수치를 편집`, 마지막은 `도시편집을 확정`이다. 모든 separator 위치와 88-word header는 byte-exact다.
- **특산 편집:** 사용자의 1안 `개방할 도시를 선택해주세요`는 26B로 기존 22B 슬롯을 초과하므로 명시적으로 허용된 fallback **`개방할 도시를 선택`**을 적용했다. 특산 도시 선택 후 확인문 `msgsec09 0x91B..0x940`은 정확히 37B인 **`이 도시의 특산품을\n개방하시겠습니까？`**로 수정했다.
- v117은 새 폰트가 필요하지 않아 v116의 `챔=95AC/physical3009`를 포함한 `font.g1t`를 byte-exact 보존한다. `code.bin`, `msgsec11`과 그 밖의 비대상 Patch 파일도 v116 byte-exact다.
- v116 대비 실제 변경 파일은 정확히 `RomFS/Message/msgsec01.dat`, `msgsec09.dat`, `msgsec18.dat` 3개다. SHA-256: `msgsec01=FD33C4F8F003E8D3D94D2A01535C3FEFEE1B23C30524CF2650206C8EFB890D3C`, `msgsec09=8CCB38E5063D15B59A6CE327A50A4514D2605FDFCCFFEDF232A22064227E7E43`, `msgsec18=F7DBF909D3B4295F2447BBD47813B73C1CC44F2D108B8A376282904E30C58538`.
- 빌더 첫 실행 → 독립 verifier → 완성 v117 위 결정적 재빌드 → verifier 재실행이 모두 PASS했고 동일 SHA를 재현했다. 세 Message의 header와 전체 `05 05 05` separator 위치, `02 2D C4` 동적 토큰, 비대상 70파일은 모두 보존됐다. **Citra 실화면 확인은 pending**이다.
- 권위 자료: `tools\build_sangokushi2_v117_issue066_color_issue091.py`, `tools\verify_sangokushi2_v117_issue066_color_issue091.py`, `analysis\v117_issue066_color_issue091_targets.json`, `analysis\v117_issue066_color_issue091_report.json`, `analysis\v117_issue066_color_issue091_readback_report.json`.

### 2026-09-02 v116-intermediate — #066/#068 1차 수정 및 `챔` 글리프 추가

- 사용자 Citra에서 v115의 #082/신분/외교 후속 수정이 모두 정상임을 확인해 v115 변경을 승인 상태로 동결한 뒤 exact v115를 baseline으로 작업했다.
- #066의 두 잔여 잠금 안내를 한국어로 바꾸고, 해금 후 히든 시나리오 설명 3개는 이미 한글화되어 있어 byte-exact 보존했다. `명군 챔피언 도장`의 `챔`이 기존 runtime map에 없어 의미를 바꾸지 않고 **`챔=95AC / physical3009`**, stock `噴` donor를 사용했다. baseline runtime ref=0, Korean owner=0, historical Korean alias hit=0을 확인했고 font 변경 셀은 3009 하나뿐이다.
- #068은 `code.bin`의 `도시편집/세력편집` 버튼명과 `msgsec09`의 도시/세력/특산 편집 제목·설명·선택 프롬프트를 수정했다. Shift-JIS 경계 분할 `選=91|49`, `関=8A|D6`, `市=8E|73`은 전체 logical span으로 덮어 orphan byte가 남지 않게 했고 separator 위치를 보존했다.
- v115 대비 변경 파일은 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat`, `msgsec09.dat` 4개이며 독립 verifier PASS와 결정적 재빌드 동일 SHA를 확인했다.
- 이후 사용자 Citra에서 **#066 텍스트 자체는 정상이나 원본 초록색 C4가 사라져 흰색으로 보이는 문제**, 그리고 #091의 추가 편집기능 일본어 잔존이 확인됐다. 따라서 v116은 구조/문구 기준으로 계승하되 최종 화면 기준은 v117이 대체한다.

### 2026-09-02 v115-intermediate — #082 실제 hardcoded source 확정 + 후속 문구 3건 수정

- 시작 시 현재 Patch가 `v114_issue082_084_runtime_repair_report.json`의 73파일 manifest와 정확히 일치함을 `verify_sangokushi2_v114_issue082_084_runtime_repair.py`로 재확인했다. exact v114 전체 Patch를 `analysis\v115_issue082_followup_text_cleanup_baseline\PatchSnapshot`에 봉인했고 Original/Rebuild/Backup은 수정하지 않았다.
- **#082 재분석:** v114에서 `msgsec08 0x955` physical body까지 직접 수정했지만 사용자 Citra가 계속 `유선님, 영안で / 조광 휘하에 들어왔습니다`를 표시했다. 따라서 이 화면에 대한 v114 `msgsec08` source 판정도 실패로 폐기했다. 스크린샷의 고정 suffix `휘하에 들어왔습니다`를 기준으로 전체 Patch를 역검색한 결과, 실제 화면을 정확히 재현하는 조립 경로는 **`code.bin 0x14EBF4 = %s%sで\n%s%s` formatter + `0x1DBAFC -> 0x2CEEFC` 공용 suffix ` 휘하에 들어왔습니다`**였다. formatter의 `で`를 `에`로 바꾸고, proven suffix slot `0x1CEEFC..0x1CEF10`에는 긴 `이(가) 휘하에 들어왔습니다`가 들어가지 않아 사용자가 명시적으로 허용한 fallback **`이(가) 합류했습니다`**를 적용했다. 군주명/도시명/무장명 세 동적 정보는 모두 caller가 계속 공급하며 목표 예시는 **`유선님, 영안에 / 조광이(가) 합류했습니다`**다. v114의 `msgsec08` 파일 자체는 이번 revision에서 byte-exact 보존한다.
- **신분 선택:** 실제 source `code.bin 0xB8B08`의 `%s 신분을 선택하십시오`를 **`%s의 신분을 선택하십시오`**로 수정했다. `%s`는 동적 무장명이며 목표 예시는 `조광의 신분을 선택하십시오`다.
- **외교 부담 대사:** `code.bin 0x1D08A0`은 동적 이름이 본문 앞에 자동으로 붙는 proven suffix 구조다. 사용자의 요청 `그럼 비리의 부담이...`에서 `그럼`을 이름 앞으로 보내려면 실행 로직 재배치가 필요해 최소 수정 원칙을 벗어나므로, 동적 이름을 보존하면서 문법을 바로잡는 **`[이름]의 부담이 / 크지 않겠습니까..`**로 수정했다. pointer `0x1E504C -> 0x2D08A0`과 앞 `0xFF` guard는 byte-exact다.
- **외교 동맹 요구:** `code.bin 0x1D104C`의 `조건으로 `은 target `이 조건으로 `보다 1B 짧아 기존 바로 앞 `0x1D1049..0x1D104B` NUL padding을 검증한 뒤 문자열 시작을 `0x1D104B`로 1B 당겼고 pointer `0x1E509C`만 `0x2D104C -> 0x2D104B`로 이동했다. 기존 `0x1D1058 = 동맹을\n맺어`는 byte-exact 보존했다. 공용 외교 요청 suffix `0x1D0968`은 **`주십시오`**로 바꿔 최종 목표를 **`이 조건으로 동맹을 / 맺어주십시오`**로 만들었다. 같은 suffix를 쓰는 기존 `철수해/승낙해/쳐들어가` prefix와도 자연스럽게 조립된다.
- 이번 revision에는 새 한글 글리프가 필요하지 않아 `font.g1t`는 수정하지 않았다. 사용자 추가 규칙으로, 향후 필요한 한글 글리프가 없을 때 의미를 억지로 바꾸지 않고 runtime ref=0·owner=0·역사 alias 충돌 없음·bleed 안전을 증명한 저사용/미사용 일본어 한자 donor 셀을 사용한다는 원칙을 `PROJECT_RULES.md`에 추가했다.
- v114 대비 실제 Patch 변경 파일은 **`ExeFS/code.bin` 1개뿐**, 변경 byte는 94개다. 최종 code SHA-256=`C1F9A29F657763568196712B7A27CD030372B7A7EFF245502612A963CBE710E7`. 나머지 72개 Patch 파일과 v114 #084-1 `0x1D1184..0x1D11A3`, #084-3 `0x181286..0x18129F`는 byte-exact다.
- 빌더 첫 실행과 완성 v115 위 재실행이 동일 SHA를 재현했고 독립 verifier를 두 번 실행해 PASS했다. `tools/audit_sangokushi2_v105_dialogue.py`의 절대 결과는 v114에서 이미 존재한 #084-1 dynamic-honorific 후보 1건과 Message mixed/active 각 3건이 남지만, sealed v114 baseline과 v115 결과가 **완전히 동일**하여 v115 신규 layout/alias/Message 회귀는 0건이다. **Citra 실화면 확인은 pending**이다.
- 권위 자료: `tools\build_sangokushi2_v115_issue082_followup_text_cleanup.py`, `tools\verify_sangokushi2_v115_issue082_followup_text_cleanup.py`, `analysis\v115_issue082_followup_text_cleanup_targets.json`, `analysis\v115_issue082_followup_text_cleanup_report.json`.

### 2026-09-02 v114-intermediate — #082/#084 Citra runtime source 재수정 및 far-alias 재발 방지

- exact `v113-intermediate` 전체 Patch를 `analysis\v114_issue082_084_runtime_repair_baseline\PatchSnapshot`에 봉인하고 작업했다. Original/Rebuild/Backup은 수정하지 않았다.
- **#082:** v110의 `msgsec08 header[49] -> EOF 0x1B03` 수정은 정적 PASS였지만 사용자 Citra에서 `유선님, 영안... 조광 휘하에 들어왔습니다` 문제가 그대로 재현됐다. 실화면과 기존 body를 다시 대조해 Citra가 실제로 읽는 source를 **`msgsec08 0x955` physical body**로 확정했다. `0x955..0x978` 고정 36B span을 `%s에 <NAME1>이(가) 휘하에 들어왔습니다` 34B + `05 05`로 제자리 수정하고 header[49]도 `0x955`로 되돌려 source를 통일했다. header[50]=`0x979`, 파일 크기, 이후 body 주소, v110 EOF duplicate `0x1B03`은 byte-exact다.
- **#084-1:** 저장 PNG와 현재 폰트 패턴 대조로 실제 화면이 `군, 감언에 / 속지 마십시오`임을 확인했고 runtime source를 `code.bin 0x1D1184`로 확정했다. 사용자의 최종 문구 지시에 따라 동적 제어코드를 추가하지 않고 literal **`주군, 감언에 속지마십시오`**로 수정했다. 25B target은 `0x1D1184..0x1D11A3` 기존 NUL 슬롯 안에 들어가며 다음 문자열은 보존된다.
- **#084-2:** 두 번째 사진은 `code.bin 0x1D10D0 = 어떻게 해도 좋은 답은 / 받기 어렵겠군요`가 맞고, `떻=9955`만 Citra에서 stock 한자형 글리프로 렌더되는 far-alias 실패임을 확인했다. 현재 effective `9955` 사용은 정확히 5곳(`code 0xAB434/0x1D10D2`, `msgsec06 0x1314/0x133D`, `msgsec08 0x60B`)이며 모두 `떻` 용도였다. 새 local donor는 **`8A98 / physical921`**로, v113 runtime ref=0·current Korean owner=0·lead 0x8A·상하좌우 bleed edge=0을 증명했다. stock `釜` 셀은 사용자 허용에 따라 `떻` Sam3 glyph로 교체했고 5곳을 전부 `8A98`로 재인코딩했다. `9955`는 dialogue audit failed alias에 추가해 잔존 0을 강제한다.
- **#084-3:** 세 번째 사진은 `交渉 포기합니까？`이며, 분석 중 `0x181286`을 문자열 시작처럼 볼 위험이 있었지만 실제 C-string은 **`0x18128C`**였다. 앞 `0x181286..0x18128B = 8F E2 B4 B3 FD EA`는 실행/data guard이므로 byte-exact 보존하고, `0x18128C`부터 기존 17B 문자열만 **`교섭을 포기합니까？`** 19B로 수정했다. 뒤에 NUL 1B가 남고 `0x1812A0` 이후 코드는 불변이다.
- v113 대비 변경 Patch 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec06.dat`, `msgsec08.dat` 4개다. font 변경 cell은 `[921]` 하나뿐이며 v113의 #086 합자 physical 948/1190은 pixel-exact 보존된다.
- `tools\audit_sangokushi2_v103_dialogue.py`에 `떻_old=9955` failed alias와 local `떻=8A98`을 반영했다. 최종 audit는 `code residue=0 / active Message=0 / mixed body=0`이다.
- 빌더 첫 실행, 독립 verifier, 완성본 위 동일 빌더 재실행, verifier 재실행이 모두 PASS했다. 최종 정적 readback은 #082 `%s에 <NAME1>이(가) 휘하에 들어왔습니다`, #084-1 `주군, 감언에 속지마십시오`, #084-2 `어떻게 해도 좋은 답은 / 받기 어렵겠군요`, #084-3 `교섭을 포기합니까？`로 확인됐다. **Citra 재확인은 아직 pending**이다.
- 권위 자료: `tools\build_sangokushi2_v114_issue082_084_runtime_repair.py`, `tools\verify_sangokushi2_v114_issue082_084_runtime_repair.py`, `analysis\v114_issue082_084_runtime_repair_targets.json`, `analysis\v114_issue082_084_runtime_repair_report.json`.

### 2026-09-02 v113-intermediate — #086 나이 합자 타일 단독 재주입

- exact v112 전체 Patch를 `analysis\v113_issue086_age_tile_baseline\PatchSnapshot`에 봉인하고 재편집된 `age_8BE9_14x14.png`(SHA-256 `8963a205...032bcd`)만 physical 1190에 주입했다.
- v112 대비 변경 Patch 파일은 `RomFS/Common/Font/font.g1t` 하나, 변경 atlas cell은 `[1190]` 하나뿐이다. 이름 physical 948은 pixel-exact, `code.bin`과 나머지 Patch는 byte-exact다.
- 입력 PNG는 14×14 RGBA·alpha 0/255이며 packed G1T 역읽기가 pixel-exact다. 결정적 재빌드 2회와 독립 verifier PASS. output font SHA-256=`922C8A3AB36937DADA74948FFAC1DEB567BC59F96D43007562ED114D3A894A90`. Citra 확인 대기다.
- 권위 자료: `tools\build_sangokushi2_v113_issue086_age_tile.py`, `tools\verify_sangokushi2_v113_issue086_age_tile.py`, `analysis\v113_issue086_age_tile_targets.json`, `analysis\v113_issue086_age_tile_report.json`.

### 2026-09-01 v112-intermediate — #086 사용자 제작 이름/나이 합자 타일 주입

- exact `v111-intermediate` 전체 Patch를 `analysis\v112_issue086_manual_tiles_baseline\PatchSnapshot`에 봉인하고 `RomFS/Common/Font/font.g1t` 한 파일만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- 사용자 편집본 `Extracted_Image\RomFS\Common\Font\Issue086_Runtime_Label_Tiles\name_8AB3_14x14.png`(SHA-256 `341987c3...d1a33`)와 `age_8BE9_14x14.png`(`9dff2fa4...f7d7d`)를 권위 입력으로 사용했다. 두 파일 모두 정확히 14×14 RGBA, alpha 값은 0/255뿐이며 리사이즈하지 않았다.
- 이름 타일은 alias `8AB3`/physical 948, 나이 타일은 `8BE9`/physical 1190에 삽입했다. v111 대비 packed font 변경 cell은 정확히 `[948,1190]`, 두 cell 밖 diff=0이며 G1T 재디코딩 결과가 입력 PNG의 A4 alpha와 pixel-exact다.
- `ExeFS/code.bin` 및 나머지 Patch 전 파일은 v111 byte-exact다. 합자 alias 최종 참조는 `8AB3@code.bin 0x63CB8`, `8BE9@0x63CBC` 각 1회뿐이고 Message/Scenario/기타 code 위치 참조는 0이다.
- 완성본 위 결정적 재빌드 2회에서 font/report 해시가 동일했고 독립 verifier가 PASS했다. output font SHA-256=`D114430EA0B4F3EFF6329A486972DFAA980098028F8140F84D9B8255509895DF`.
- 권위 자료: `tools\build_sangokushi2_v112_issue086_manual_tiles.py`, `tools\verify_sangokushi2_v112_issue086_manual_tiles.py`, `analysis\v112_issue086_manual_tiles_targets.json`, `analysis\v112_issue086_manual_tiles_report.json`, `analysis\v112_issue086_manual_tiles_preview.png`. 새 도안은 **Citra 실화면 확인 대기**다.

### 2026-09-01 v111-intermediate — #086 장수 정보 runtime label·한 셀 합자 시험

- exact `v110-intermediate` 전체 Patch를 `analysis\v111_issue086_runtime_labels_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 두 파일만 수정했다. Rebuild/Original/Backup은 쓰지 않았다.
- 스크린샷의 빨간 영역과 동일한 순서로 `code.bin 0x63CB8~0x63CF7`에 `名 / 年 / 寿命 / 義理 / 野望 / 幸運 / 冷静 / 勇猛 / 相性` 9개 고정 문자열 슬롯이 연속 배치되어 있음을 확인했다. 이를 `이름 / 나이 / 수명 / 의리 / 야망 / 행운 / 냉정 / 용맹 / 상성`으로 바꿨다.
- `名`, `年` 슬롯은 각 4바이트라 정상 두 글자(4바이트)를 넣으면 NUL을 보존할 수 없다. 따라서 기존 runtime 참조 0·map owner 0·과거 배정 0인 정상 CP932/JIS alias `8AB3(physical 948)`, `8BE9(physical 1190)`를 골라 각각 14×14 한 셀 안에 `이름`, `나이` 두 음절을 6×11로 축소 배치했다. 이 합자 둘은 전역 문자 map에 등록하지 않고 해당 code offset에서만 직접 호출한다.
- 완성본 전수 raw-reference 검사에서 `8AB3`은 `code.bin 0x63CB8`, `8BE9`는 `0x63CBC`에서만 각각 1회 존재하며 Message/Scenario/다른 code 위치 참조는 0이다. 이미 번역된 텍스트가 축소 합자를 호출할 수 없는 구조다.
- 나머지 7개는 기존 정상 두 칸 글리프를 사용했다. `냉`은 far alias `98C1` 대신 v108 인명표에서 이미 검증된 low-lead `93FB`를 재사용했고 해당 font 셀은 변경하지 않았다.
- v110 대비 code changed byte=32, target 9슬롯 밖 code diff=0, font changed physical cell=`[948,1190]`, 두 셀 밖 font diff=0, packed font pixel-exact readback PASS다. 빌더를 완성 v111 위에서 다시 실행해 code/font/report 해시가 모두 동일함을 확인했고 독립 verifier도 PASS했다.
- 권위 자료: `tools\build_sangokushi2_v111_issue086_runtime_labels.py`, `tools\verify_sangokushi2_v111_issue086_runtime_labels.py`, `analysis\v111_issue086_runtime_labels_targets.json`, `analysis\v111_issue086_runtime_labels_report.json`, `analysis\v111_issue086_composite_glyph_preview.png`.
- 사용자 Citra Nightly 2104 실화면에서 9개 label의 한국어 출력과 `이름/나이` 합자가 각각 지정 위치에만 나타나는 것을 확인했다. 따라서 #086 대상이 runtime text라는 판정과 alias locality는 런타임 승인됐다. 다만 자동 6×11 축소 도안은 찌그러져 가독성 개선이 필요하다.
- Patch revision을 올리지 않고 v111 packed font에서 physical 948/1190을 **원/native 14×14 RGBA PNG**로 역추출했다. 편집 경로는 `Extracted_Image\RomFS\Common\Font\Issue086_Runtime_Label_Tiles\name_8AB3_14x14.png`, `age_8BE9_14x14.png`이며 완전 투명 `blank_14x14.png`도 제공한다. 사용자가 도안을 수정할 때 캔버스를 리사이즈하지 않는다. report=`analysis\v111_issue086_edit_tiles_report.json`, runtime 증거=`analysis\v111_issue086_citra_runtime_confirmation.json`.

### 2026-09-01 v110-intermediate — #082/#084/#085/#088 및 동적 이름 토큰 재발 방지

- 작업 시작 시 exact `v109-intermediate` Patch를 `analysis\v110_issue082_084_085_088_baseline\PatchSnapshot`으로 봉인했다. Original/Rebuild/Backup은 쓰지 않았다.
- #082는 최초 분석에서 `msgsec06 header[8]` 밀정 귀환 문구로 잘못 연결했으나 사용자 추가 설명(`유선님, 영안에 조광이(가) 휘하에 들어왔습니다`)을 기준으로 source ownership을 재추적해 **실제 source를 `msgsec08 header[49]`**로 확정했다. Original active row는 byte `0x69F = %sﾃﾞ + 02 01 C8 + ｶﾞ幕下ﾆ加ﾜﾘﾏｼﾀ`, v109 row는 `0x955 = %s에서 + 02 01 + 네가 + 휘하에 들어왔습니다`였다. v110은 `%s에 + 02 01 C8 + 이(가) 휘하에 들어왔습니다`로 수정했다. `이(가)`는 받침과 무관하게 안전한 literal 이중조사이며 군주명 prefix는 row 바깥 runtime 소유로 유지한다. target이 v109 row보다 1B 길어 msgsec08 EOF `6915`에 append하고 header[49]만 redirect했다.
- 같은 실패 패턴을 재발방지 목적으로 `msgsec04/06/07/08`의 active flat-header 전부를 immutable Original 같은 header index와 source-aware 대조했다. **105개 unique active row에서 NAME1 105개 + NAME2 12개 + NAME3 3개 = 총 120개 `02 xx C8` 토큰 누락**을 확인했고 전부 복구했다. 104개는 기존 capacity 안에서 제자리 수정/space padding, `msgsec07 header[318]`만 +3B라 EOF append relocation했다. `msgsec07 header[354]`의 동적 token 주변 고아 `0x81`도 explicit row rebuild로 제거했다.
- 구형 `tools\audit_sangokushi2_v103_dialogue.py`가 `02 03 C8`을 NAME3로 인식하지 않아 복구 후 3건을 일본어/CJK 혼입으로 오탐하는 것을 발견했다. Original 증거를 기준으로 decoder를 NAME1/2/3 모두 atomic 3B token으로 인식하도록 수정했으며 최종 audit는 **code=0 / active=0 / mixed=0 / failed alias=0**이다.
- #084는 Original `msgsec06 header[99] @0xA53`가 **`殿、甘言ニ...`로 시작하고 row 안에 이름 token이 없음**을 확인했다. 따라서 `주군,`을 hard-code하거나 NAME1을 중복 삽입하지 않고 runtime이 바깥에서 붙이는 군주명 뒤에 이어지는 suffix만 `님, 감언에 / 속지 마십시오`로 수정했다.
- #085는 `code.bin 0x1D104C`의 10B slot을 `조건으로 `로 수정해 기존 `0x1D1058=동맹을 / 맺어`와 조립했을 때 정확히 `조건으로 동맹을 / 맺어`가 되도록 검증했다. `msgsec21`은 `선비족의 가비능`, `군을 치게`, `촉에서 투항한`, `양평관으로 진군하게`, `방면에 대응책을`이 되도록 5개 fragment target을 수정했다. trailing space는 cross-fragment join을 위한 의도적 바이트이며 trim 금지다. `msgsec21`은 immutable Original identity rebuild 후 **37개 word pointer(EOF sentinel 포함) + 30개 absolute BYTE pointer**를 모두 다시 계산했고 deterministic rebuild를 확인했다.
- #088은 `msgsec09`가 혼합 포인터 구조임을 다시 확인했다. `header[0]=0x526`만 WORD pointer이고 `header[1]/[2]`는 absolute BYTE pointer다. v101에서 이미 append된 한국어 승리/패배 row가 각각 `0xA4E / 0xA61`에 있으므로 body를 새로 만들지 않고 header[1]/[2]만 그 위치로 redirect했다. header 이후 body는 v109와 byte-exact다.
- v110 변경 Patch 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat`, `msgsec21.dat` 7개다. `font.g1t`는 v109 byte-exact이며 신규 glyph/donor는 필요하지 않았다.
- 권위 자료: `tools\build_sangokushi2_v110_issue082_084_085_088.py`, `tools\verify_sangokushi2_v110_issue082_084_085_088.py`, `analysis\v110_issue082_084_085_088_targets.json`, `analysis\v110_issue082_084_085_088_report.json`.
- 빌더 2회 연속 deterministic 재실행, 독립 verifier, v103 dialogue audit 모두 PASS했다. **Citra 실화면 확인은 pending**이므로 #082/#084/#085/#088의 최종 성공 판정은 사용자 런타임 확인 후 승격한다.

### 이미지

- RomFS G1T 3,206개(엔트리 4,392개)를 분석했다.
- 3DS PICA200 텍스처 형식 `0x09`, `0x44`, `0x45`, `0x47`, `0x48`의 PNG 추출기를 작성했다.
- PICA200의 Y축 반전 문제를 수정해 전체 4,392개 PNG를 다시 추출했고, 오류는 0건이다.
- 결과 위치: `Extracted_Image\RomFS`
- 검사용 `_probe*`, `_orientation_verified` 폴더는 삭제했다.
- 2026-08-31 Hex UI 방향 재감사에서 공용 추출기의 Y축 변환과 Hex 전투 렌더러 UV가 다른 계열임을 확인했다. 글자를 방향 기준으로 판독해 `HexCommand_002~005`, `HexFontEffect_001~031`, `HexUpUI_000/004/005`, `HexDownUI_015~017` 총 41개가 편집 PNG에서 반전되어 있음을 확정했다. 첫 보정은 HexCommand에 `상하 반전 + 90도 반시계 회전`을 적용했으나 글자가 옆으로 누운다는 사용자 확인으로 잘못된 중간 변환임을 판정했다. 전 41개의 최종 권위 변환은 **상하 반전만 적용**하는 것이며 HexCommand의 세로 아틀라스 크기 `64×256 / 128×1024 / 64×256 / 128×128`도 그대로 유지한다. 사용자 요청에 따라 `Extracted_Image`의 해당 41개 PNG만 정상 편집 방향으로 교체했고, legacy 저장 배열 원본은 `analysis\hex_ui_orientation_audit\storage_layout_original`에 byte-exact 보존했다. 전체 Extracted_Image PNG manifest 비교로 비대상 PNG 변경이 없음을 확인했다. Original/Patch/Rebuild/Backup은 수정하지 않았고 `tools\sangokushi2_hex_image_orientation.py`에 정상 편집 방향/역방향 변환과 pixel-exact 왕복 gate를 추가했다. 비문자 Hex PNG는 방향을 단정하지 않는다. report=`analysis\hex_ui_orientation_audit\report.json`.

### 텍스트

- `RomFS\Message\msgsec00.dat`~`msgsec21.dat` 22개를 분석했다.
- 각 파일은 16비트 워드 오프셋 테이블과 문자열 데이터로 구성되어 있다.
- 원본 바이트·제어 코드·해시를 보존한 JSON 및 HTML 검수기를 생성했다.
- 결과 위치: `Extracted_Text`
  - `sangokushi2_text.json`: 보존용 원본 추출 데이터
  - `text_review.html`: 번역·검수 화면
  - `extraction_manifest.json`: 추출 통계
  - `README.md`: 사용 방법
- 3개 이상의 `0x05` 반복 구간을 논리적 항목 경계로 분리했다. 예: `msgsec00_0004`는 356개 인명/호칭 조각으로 분할된다.
- HTML은 제어 코드와 레이아웃 명령을 숨기며, 번역자가 한국어 번역·메모만 편집하도록 구성했다.
- 문장 앞의 `u/d/t/s/h/q/r/i + 연산자 + 숫자` 형식 레이아웃 명령과 연결 기호 `&`, `|`를 HTML 번역 화면에서 숨긴다. 원본 `source_text`와 `raw_hex`에는 보존한다.
- `msgsec00_0004_329`의 `t>40&s>4参ルゾ`를 삼국지 2 전체 24건 및 삼국지 1 참고 자료 35건과 재대조했다. `s>4` 전체가 서식 명령이며 실제 표시 문자열은 `参ルゾ`임을 확정했다.
- 필드 시작 직후의 `0x81`은 CP932 문자 선행 바이트가 아니라 독립 제어 바이트임을 확인했다. 잘못 표시되던 `⌒タシ`, `°CQリマシタ`를 각각 `ワタシ`, `参リマシタ`로 복원했다.
- 재추출 시 기존 JSON의 `translation_ko`와 `notes`를 ID 기준으로 승계하여 이미 입력한 번역이 사라지지 않도록 보강했다.
- `Se3`은 원시 바이트 `01 53 02 65 03`으로 시작하는 스타일/서식 접두사다. 값 `2/3/4`는 제어 파라미터이며 번역 화면에서는 `SeN` 전체를 숨긴다.

## 확인된 기술 사실

- `u<04>=122<06>&<02>d20` 형식은 UI/레이아웃 데이터로 보이며, 삼국지1 한글 패치에도 같은 형태·위치로 보존되어 있다.
- `&`, `h>60`, `q>10`처럼 남는 ASCII 조각은 엔진 서식 또는 참조 명령일 가능성이 높다. 의미가 확정되지 않은 항목은 번역 대상으로 사용하지 않고 원시 데이터에 보존한다.
- 제어 코드의 정확한 의미와 재삽입 규칙은 다음 단계에서 원본↔무변경 재구성 검증을 통과한 뒤 확정한다.

## 번역 진행

- 2026-08-27: `text_review.html`의 한국어 번역 작업을 재개했다.
- 기존 입력분은 134개였으며, 마지막 완료 지점은 `msgsec13_0046_001`이었다.
- 이번 재개 작업에서 `msgsec13_0046_002` ~ `msgsec13_0082_002`까지 107개 항목을 추가 번역해 HTML에 저장했다.
- 2026-08-27 재확인 결과, 위 241개 번역은 유실되지 않았으며 `text_review.html` 내부의 `const autoTranslations={...}`에 실제로 저장되어 있음을 확인했다. 브라우저 렌더링 모의 검증에서도 241개가 정상 로드되었다.
- 이어서 `msgsec13_0083_000` ~ `msgsec13_0106_001`까지 72개 항목을 추가 번역했다.
- 이어서 `msgsec13_0106_002` ~ `msgsec13_0125_001` 구간의 번역 대상 59개를 추가 번역했다. `msgsec13_0113_003`은 기술 데이터로 분류된 단일 조각이라 번역하지 않았다.
- 현재 누적 번역 적용 항목: 432개(기존 372 + 신규 60).
- 원문의 `<$0A>`, `<$8F>`, `<$1B>` 계열 등 제어 코드는 번역문에도 같은 순서로 보존한다.
- 이번에 추가한 131개 전체를 기계 검증해 원문 `control_codes`와 번역문의 제어 코드 순서/개수가 모두 일치함을 확인했다(불일치 0건).
- 원문 추출 과정에서 `A`, `G`, `N`, `R`, `s`, `U`, `q`, `~` 및 사설영역 글리프 등으로 남아 있는 미확정 조각은 임의 삭제하지 않고 그대로 남긴다.
- 2026-08-27: `msgsec13_0125_002` ~ `msgsec13_0145_002`의 번역 대상 60개를 추가 번역했다. HTML의 `autoTranslations`에 저장하며 페이지 로드시 비어 있는 `translation_ko`에도 같은 값이 자동 복사되므로 `한국어 번역`과 `한국어 번역 (AI)` 두 열에 동일한 번역이 표시된다.
- 이번 신규 60개도 원문 제어 코드와 번역문 제어 코드의 순서/개수를 기계 검증했으며 불일치 0건이다.
- 2026-08-27: 사용자의 요청으로 앞쪽 미번역 구간을 다시 채우기 시작했다. `msgsec00_0001_002`부터 기번역/기술 데이터를 건너뛰며 **실제 미번역 대상 300개**를 번역했고, 마지막 항목은 `msgsec01_0051_007`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 페이지 로드시 비어 있는 `translation_ko`에도 같은 값이 자동 병합되므로 `한국어 번역`과 `한국어 번역 (AI)` 양쪽에 동일하게 표시된다.
- 신규 300개 전체의 제어코드를 원문과 기계 비교한 결과 **불일치 0건**이며, 신규 번역문에 남은 일본어 가나도 **0건**임을 확인했다.
- 현재 HTML 기준 번역 데이터는 수동 `rows[].translation_ko` 134개 + `autoTranslations` 598개, 중복 0개로 **누적 732개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec01_0051_008`.
- 2026-08-27: `번역.md` 지침에 따라 위 지점부터 실제 미번역 대상 300개를 추가 번역했다. 마지막 완료 항목은 `msgsec02_0056_000`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 `한국어 번역`과 `한국어 번역 (AI)` 두 열에 동일하게 표시된다.
- 신규 300개 전체의 원문/번역 `<$xx>` 제어코드를 기계 비교한 결과 **불일치 0건**이다.
- 현재 HTML 기준 번역 데이터는 수동 `rows[].translation_ko` 134개 + `autoTranslations` 898개, 중복 0개로 **누적 1,032개**이다.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec02_0056_001`부터 실제 미번역 대상 **300개**를 추가 번역했다. 마지막 항목은 `msgsec06_0053_000`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 `한국어 번역`과 `한국어 번역 (AI)` 양쪽에 동일하게 표시된다.
- 신규 300개 전체의 제어코드를 원문과 단일 `<$xx>` 토큰 단위로 재검증한 결과 **불일치 0건**이다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 1,198개, 중복 0개로 **누적 1,332개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec06_0053_001`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec06_0053_001`부터 실제 미번역 대상 **300개**를 추가 번역했다. 마지막 항목은 `msgsec07_0081_003`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 페이지 로드시 비어 있는 `translation_ko`에도 같은 값이 병합되므로 `한국어 번역`과 `한국어 번역 (AI)` 양쪽에 동일하게 표시된다.
- 신규 300개 전체의 `<$xx>` 제어코드 순서/개수를 원문과 재검증한 결과 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**이다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 1,498개, 중복 0개로 **누적 1,632개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec07_0082_000`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec07_0082_000`부터 실제 미번역 대상 **300개**를 추가 번역했다. 마지막 항목은 `msgsec08_0012_001`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 `한국어 번역`과 `한국어 번역 (AI)` 양쪽에 동일하게 표시된다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 1,798개, 중복 0개로 **누적 1,932개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec08_0012_002`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec08_0012_002`부터 실제 미번역 대상 **300개**를 추가 번역했다. 마지막 항목은 `msgsec10_0019_001`이다.
- 신규 300개는 `autoTranslations`에 저장했으며 `한국어 번역`과 `한국어 번역 (AI)` 양쪽에 동일하게 표시된다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 2,098개, 중복 0개로 **누적 2,232개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec10_0020_000`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec10_0020_000`부터 **실제 미번역 대상 300개**를 추가 번역했다. 기존 수동 번역 83개와 겹치는 구간은 신규 번역 개수에서 제외하고 건너뛴 뒤 계속 진행했으며, 마지막 신규 번역 항목은 `msgsec14_0003_001`이다.
- 수동 번역과 겹친 83개는 `한국어 번역 (AI)`도 기존 수동 번역과 동일한 값으로 다시 동기화해 두 열의 표시가 서로 다르지 않게 했다.
- 신규 300개 전체를 단일 `<$xx>` 제어코드 토큰 기준으로 재검증한 결과 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**이다. 기존 번역 `msgsec13_0121_000`에 남아 있던 일본어 가나도 함께 정리했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 2,481개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 2,532개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec14_0004_000`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec14_0004_000`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec15_0070_000`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 2,781개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 2,832개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec15_0070_001`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec15_0070_001`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec15_0206_001`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 3,081개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 3,132개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec16_0000_000`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec16_0000_000`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec19_0029_000`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 3,381개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 3,432개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec19_0029_001`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec19_0029_001`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec19_0131_000`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 3,681개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 3,732개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec19_0131_001`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec19_0131_001`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec20_0005_000`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 3,981개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 4,032개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec20_0005_001`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec20_0005_001`부터 **실제 미번역 대상 300개**를 추가 번역했다. 마지막 신규 번역 항목은 `msgsec21_0024_001`이다.
- 신규 300개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 4,281개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 4,332개**이다.
- 앞쪽 미번역 채우기 기준 다음 시작 지점: `msgsec21_0025`.
- 2026-08-27: `번역.md` 지침에 따라 `msgsec21_0025`부터 남아 있던 **실제 미번역 대상 전부 28개**를 번역했다. 마지막 신규 번역 항목은 `msgsec21_0035_000`이다.
- 신규 28개 전체의 제어코드를 원문의 `control_codes`와 비교해 **불일치 0건**, 빈 번역 **0건**, 일본어 가나 잔존 **0건**을 확인했다.
- 현재 HTML 기준 수동 번역 134개 + `autoTranslations` 4,309개이며, 두 영역 중 83개는 동일 ID로 동기화되어 있다. 합집합 기준 **누적 번역 4,360개**이다.
- `is_translatable == true` 및 `is_technical == false`인 전체 번역 대상도 정확히 **4,360개**로 집계되어, 현재 실제 미번역 항목은 **0개**다. 앞쪽 순차 번역 작업은 완료 상태다.
- 전체 4,360개를 전수 재검증하면서 과거 번역분에 남아 있던 제어코드 불일치 22개와 일본어 가나/깨진 가나 잔존 6개를 추가 정리했다. 깨진 가나는 의미를 추측하지 않고 `[깨짐]`으로 명시했다.
- 최종 전수 검증 결과: **제어코드 불일치 0건 / 빈 번역 0건 / 일본어 가나 잔존 0건 / 실제 미번역 0건**.
- 기존 `msgsec13` 인물열전 순차 작업을 다시 이어갈 경우 시작 지점은 현재 앞쪽 순차 작업에 의해 대부분 메워졌으므로, 이후에는 반드시 실제 미번역 ID를 다시 검색해 결정한다.
- 반복 작업 지침을 작업 루트의 `번역.md`로 별도 저장했다. 앞으로 사용자가 `번역.md 보고 이어서 번역해줘`라고 하면 해당 문서의 절차와 규칙을 기준으로 진행한다.

## 검수 화면 표시 수정

- 2026-08-27: `text_review.html`에 `한국어 번역 (AI)` 열을 새로 추가했다.
- 기존 `한국어 번역` 열은 사용자 수동 편집용으로 유지하고, AI 번역은 `autoTranslations` 값을 별도 읽기 전용 열에 직접 표시하도록 분리했다.
- 검색 시 수동 번역과 AI 번역을 모두 검색 대상으로 포함한다.
- 따라서 수동 번역 필드가 비어 있어도 AI 번역 저장분은 화면에서 독립적으로 확인할 수 있다.
- 2026-08-27 추가 수정: 브라우저에서 기존 `한국어 번역` 입력칸이 비어 보이는 문제를 재현했다. 원인은 기존 134개는 `rows[].translation_ko`, 이후 238개는 `autoTranslations`에 따로 저장되어 있었기 때문이다.
- 페이지 로드시 비어 있는 `rows[].translation_ko`에 동일 ID의 `autoTranslations` 값을 병합하도록 수정했다. 현재 134 + 238 = 총 372개가 기존 `한국어 번역` 입력칸에도 표시되며, JSON 다운로드 시에도 `translation_ko`로 포함된다. `한국어 번역 (AI)` 열은 비교용으로 그대로 유지한다.

## 다음 작업

1. HTML 검수 화면에서 남은 서식 잔재를 추가 분류하고, 번역 대상만 깔끔하게 표시한다.
2. 삼국지1 한글 패치와 NDS 참고 자료를 비교해 메시지 명령과 문자 인코딩을 확정한다.
3. 한국어 글리프/폰트 삽입 방식을 분석한다.
4. 번역 JSON을 메시지 파일로 재구성하는 삽입기와 무변경 동일성 검증을 만든다.

## 3DS 한글 폰트 글리프 사전 조사 (2026-08-27)

### 현재 번역본의 필요 글자 수

- 기준 파일: `Extracted_Text\sangokushi2_translation.json`.
- 제어코드 `<$xx>`를 제외한 한국어 번역 전체에서 한글 완성형은 총 93,993회 사용되며, **고유 한글 완성형은 864자**다.
- 분리형 한글 자모(U+1100~U+11FF, U+3130~U+318F)는 0자다. 따라서 현재 스냅샷에서 새로 그려야 할 한글 글리프의 최소 수는 864개다.
- 이 수치는 사용자가 번역문을 추가 수정하면 바뀔 수 있으므로, 실제 폰트 제작 직전에 최종 JSON을 다시 내보내고 반드시 재집계한다.

### 원본 폰트 위치와 구조

- 원본 폰트: `Sangokushi 2 Original\RomFS\Common\Font\font.g1t`.
- 최종 수정본 배치 위치: `Sangokushi 2 Patch\RomFS\Common\Font\font.g1t`.
- G1T 안에는 A4(4비트 알파) 텍스처 2장이 있다. 런타임 스크린샷과 실제 글자열을 다시 대조하여 글리프 셀은 **12×16픽셀**, 한 행은 85셀임을 확정했다. 각 1024픽셀 행의 마지막 4픽셀은 패딩이다.
  - 시트 0: 1024×1024, 85×64 = 5,440셀.
  - 시트 1: 1024×512, 85×32 = 2,720셀.
- 셀 좌표 공식은 `x=(index % 85)*12`, `y=(index // 85)*16`이다. 이전의 16×16/64열 및 그 기준으로 계산한 빈 셀 수는 폐기한다.

### 필요한 추가 위치

- 사용자 결정에 따라 **일본어 글리프는 보존하지 않고 필요한 한글 글리프로 치환**한다. 빈 704셀만 쓰는 방식으로 제한하지 않는다.
- 단, 숫자·영문·공백·문장부호·UI 기호 및 제어 처리에 필요한 글리프는 일본어 문자와 구분하여 반드시 보존한다.
- 실제 치환 위치는 메시지 인코더의 문자 코드→셀 번호 매핑과 함께 확정해야 하며, 폰트 그림만 먼저 덮어쓰면 안 된다.
- 구조 참고용 삼국지1 패치 폰트 `Sangokushi 1 Patched\RomFS\UI\font.g1t`를 12×16 격자로 재검증했다. 실제 KS 완성형 한글 영역은 시트 0의 **셀 557~2906(2,350셀)**이며, 원래 `亜`에 해당하는 `0x889F`부터 `0x94FC`까지의 Shift-JIS 유효 코드 범위를 순서대로 치환한다. 이전의 16×16 격자로 계산한 셀 413~3106 및 셀 420~2769 결론은 사용하지 않는다.

### 다음 폰트 작업 방식

1. 사용자가 최종 폰트를 정하면 최종 번역 JSON에서 고유 한글을 다시 집계한다.
2. 글자 목록을 사용 빈도순으로 정렬한다.
3. 원본의 숫자·영문·공백·기호 글리프를 보호 목록으로 확정한 뒤, 나머지 일본어 글리프 슬롯 가운데 864개 이상을 한글 배치 영역으로 선정한다. 관리가 쉬운 연속 영역을 우선한다.
4. `한글 문자 → 게임 내부 코드 → G1T 시트/셀/PNG 좌표` 매핑표를 만든다.
5. 선택한 Sam3KRFont 16×16 원본을 게임의 12×16 셀에 최근접 이웃 방식으로 맞춘 뒤 A4 알파와 원본의 방향·타일 순서를 유지해 `font.g1t`를 재구성한다.
6. CMAP이 없는 G1T 이미지 아틀라스이므로 메시지 인코더 또는 실행 코드의 문자 코드→셀 계산을 반드시 함께 맞춘다.
7. 수정본을 패치 폴더의 동일 경로에만 저장하고, 원본 `font.g1t`는 변경하지 않는다.
8. 삽입 후 패치 폰트를 다시 읽어 864개 모든 한글이 실제 할당된 셀에 존재하는지 검증한다.

## Sam3KRFont 초반 설명문 테스트 패치 (2026-08-27)

- 사용자가 내려받은 `font\font-a862d172b0ac1931.zip`을 `font\Sam3KRFont`에 풀었다. TTF의 SHA-1은 `a0fa04f4364bdf4b9e572c8ff02131c34ef1816a`로 Font Share 배포본 및 공식 `hurss/fonts` 파일과 일치한다.
- 폰트는 Sam3KRFont(삼삼한글) 16×16 1bpp, KS 완성형 2,350자이며 BIN은 글리프당 32바이트다.
- 삼국지1 패치의 메시지와 폰트를 교차 검증하여 한글 별칭 코드 범위를 확정했다.
  - `가`: KS 인덱스 0 → `0x889F` → G1T 시트 0 셀 557.
  - `힝`: KS 인덱스 2349 → `0x94FC` → G1T 시트 0 셀 2906.
  - 이 범위는 첫 번째 JIS 한자 `亜`부터 시작하는 연속 Shift-JIS 유효 코드 2,350개와 정확히 대응한다.
- `tools\build_sangokushi2_font_test.py`를 추가해 원본을 읽고 패치 폴더의 `RomFS\Common\Font\font.g1t`만 생성하도록 했다. A4 알파의 세로 방향과 PICA200 8×8 스위즐을 역변환해 재패킹한다.
- Sam3의 16×16 글리프를 게임의 실제 12×16 셀에 최근접 이웃으로 맞춘다. 완성된 G1T를 다시 디코딩하여 2,350개 글리프와 비교한 결과 불일치 0개, 한글 셀 범위 밖 변경 픽셀 0개이며 파일 크기는 원본과 같은 786,492바이트다.
- 화면의 원본 모드 설명은 `Message\msgsec01.dat`의 `msgsec01_0041_003`으로 확인했다. 테스트 번역은 다음 두 줄이다.

  `『삼국지 III』를 기반으로 한`

  `게임입니다.`

- `tools\build_sangokushi2_intro_test.py`는 위 문장 영역만 고정 길이로 치환한다. 원본의 끝 `0x81`과 다음 엔트리 첫 `0x42`가 합쳐져 마침표 `0x8142`가 되는 경계를 유지했으며, DAT 크기는 원본과 같은 9,057바이트다.
- 생성 파일은 `Sangokushi 2 Patch\RomFS\Common\Font\font.g1t`와 `Sangokushi 2 Patch\RomFS\Message\msgsec01.dat`이다. 3DS 리패키징과 실제 화면의 크기·행간 확인은 사용자가 수행한다.
- 검증 자료는 `analysis\sam3krfont_test_report.json`, `analysis\intro_text_test_report.json`, `analysis\sam3krfont_test_preview.png`에 저장했다.

## ExeFS 글리프 매핑 및 하드코딩 UI 확인 (2026-08-27)

- 두 번째 Citra 테스트에서도 글리프가 다른 모양으로 깨졌다. 이 런타임 결과에 따라 12×16 셀 배치만으로는 부족하다는 사실을 확정했다.
- 원인은 `Sangokushi 2 Original\ExeFS\code.bin`의 JIS 코드→아틀라스 셀 조회표였다. 표 시작은 `0x1AC054`, 항목은 94×94개의 little-endian `uint16`이며 `0xFFFF`는 해당 글리프 부재를 뜻한다.
- 기준점 재검증: JIS `0x3021`/Shift-JIS `0x889F`의 `亜`는 셀 557, 다음 `唖`는 셀 558, KS 인덱스 1112에 해당하는 `誌`는 셀 1397을 가리켰다.
- 한글 별칭 2,350개가 셀 557~2906을 가리키도록 조회표를 수정했다. 원본에서 별칭 612개는 `0xFFFF`였고 총 2,233개 항목의 값이 바뀌었다. 수정본 전 항목 재읽기 불일치는 0건이다.
- `tools\patch_sangokushi2_code_ui_test.py`가 글리프 조회표와 현재 테스트 UI 문자열을 함께 수정하며, 출력은 `Sangokushi 2 Patch\ExeFS\code.bin`이다. 파일 크기는 원본과 같은 2,048,000바이트, 출력 SHA-256은 `B31479F004C66E56F960D911428AD117BF4ABAE46E89F5F4315E7F9D6C81EA7E`다.
- 따라서 다음 테스트 리빌드에는 폰트와 메시지 파일뿐 아니라 패치 폴더의 `ExeFS\code.bin`도 반드시 포함해야 한다. 실제 Citra 정상 출력 확인 전까지 런타임 검증은 미완료다.

## StartMenu 이미지 재추출 전수 점검 (2026-08-27)

- 원본 `RomFS\StartMenu`의 G1T 13개를 다시 전수 분석했다. 총 142개 텍스처가 모두 지원 형식(`0x09`, `0x47`)이며 `Extracted_Image\RomFS\StartMenu`의 PNG 수도 142개로 일치했다. 누락 0개, 미지원 0개다.
- 화면 상단의 `게임 선택`은 `Extracted_Image\RomFS\StartMenu\start_menu_up\start_menu_up_003.png` 이미지다.
- 금색 `オリジナルモード`는 이미지가 아니라 `ExeFS\code.bin`의 CP932 문자열이며 정확한 파일 오프셋은 `0x1CFB64`다. 원문 16바이트 슬롯에 한글 별칭 인코딩 `오리지널 모드` 13바이트와 NUL 패딩 3바이트를 넣었다.

## code.bin 전용 텍스트 검수 화면 (2026-08-27)

- `tools\extract_sangokushi2_code_text.py`를 추가했다. ARM 명령어 오탐을 피하기 위해 확인된 문자열 풀 `0x1CED00..0x1D39D4`에서 4바이트 정렬, 선행/종료 NUL, CP932 엄격 디코딩, 정렬 패딩 조건을 모두 만족하는 일본어 리터럴만 추출한다.
- `Extracted_Text\sangokushi2_code_text.json`과 별도 검수 화면 `Extracted_Text\code_text_review.html`을 생성했다. 총 776개이며 분류는 UI/용어 343개, 설명/대사 263개, 문자 입력/변환표 167개, 서식 포함 UI 3개다.
- 각 행은 ID, 정확한 code.bin 오프셋, 원문 CP932 바이트, 원본/최대 제자리 삽입 용량, `%s/%u` 계열 서식 토큰, `translation_ko`, 메모를 보존한다. JSON 내보내기는 `translation_ko`만 번역 권위 필드로 사용한다.
- 알려진 `code_001CFB64`는 `오리지널 모드`로 미리 채웠다. 문자 입력/변환표 167개는 일반 번역 대상이 아니라 구조 검수 대상으로 분류했으며 후속 삽입기에서 기본 제외한다.
- `Extracted_Text\code_text_manifest.json`의 검증 결과는 `pass: true`, 고유 오프셋 776개, 원본 code.bin SHA-256 `AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`이다.
- 현재 안전 기준은 원본 리터럴 바이트 길이 이내의 제자리 치환이다. 더 긴 번역은 포인터/참조 재배치가 검증되기 전까지 삽입하지 않는다. 정적 검증은 런타임 표시 성공을 대신하지 않는다.

### 런타임 실패 보고와 수정 (2026-08-27)

- 사용자가 Citra Nightly 2104의 게임 선택 화면에서 테스트 빌드를 실행한 결과, 한글 코드 자체는 호출됐지만 글리프 조각이 섞여 표시됐다. 실제 화면 증거가 정적 왕복 검증보다 우선하므로 최초 결과를 런타임 실패로 판정했다.
- 실패 가설: 글꼴 시트를 16×16/64열 격자로 간주해 `가`를 셀 420부터 배치했다. 재추출기가 같은 잘못된 격자로 다시 읽었기 때문에 왕복 비교는 통과했지만, 게임 렌더러는 12×16/85열 격자를 사용하므로 한 글리프가 여러 실제 셀에 걸쳤다.
- 실패 폰트 SHA-256은 `9900994971647cd1dbb38353f530b9be423f8a2a86abf374a30a1dbb61df0d14`다. 사용자 스크린샷은 이 테스트 산출물을 리패키징한 화면으로 보고되었으나 완성 ROM 해시는 별도로 받지 않았으므로, 화면과 파일의 결합 신뢰도는 중간으로 기록한다.
- 원본 시트에서 셀 550~556이 `①~⑥, Ⅲ`, 셀 557부터 `亜, 唖, 娃...` 순서임을 픽셀로 확인했다. 삼국지1 패치의 같은 위치는 셀 557부터 `가, 각, 간...` 순서다.
- `tools\build_sangokushi2_font_test.py`의 셀 규격을 12×16/85열로 수정하고 전체 한글을 셀 557~2906에 다시 배치했다. 새 `font.g1t`는 2,350개 글리프 왕복 불일치 0개, 범위 밖 변경 픽셀 0개다.
- 교정된 패치 폰트 SHA-256은 `37040122ec61f30159303077c8176812c11d19bcff7e6c6f8674729e670030d6`다. 실제 Citra 화면 재확인이 끝날 때까지 런타임 검증 상태는 미완료로 둔다.
- 스크린샷의 금색 `오리지널 모드` 및 흰색 `게임 선택` 같은 고정 UI 표기는 런타임 설명문과 별도의 이미지/정적 UI 자산이다. 사용자 방침에 따라 이미지 번역은 사용자가 담당하며, 이 수정에서는 흰색 설명문 텍스트만 처리한다.
- 실패 화면 확대본은 `analysis\runtime_text_failure_zoom.png`에 보존했다. 동일한 16×16 격자 가설은 다시 사용하지 않는다.

## NDS 참고 ROM 분해 및 추출 (2026-08-27)

- 원본 `Sangokushi DS\삼국지 DS.nds`는 읽기 전용으로 취급하고 `Sangokushi DS rom`에 작업용 분해본을 생성했다.
- 원본은 64 MiB, 타이틀 `SANGOKUSHIDS`, 게임 코드 `A3GK`이며 SHA-256은 `9b5250822d6c77a7dfef481fa4c16ffb6ef4b59f2c35f25240a476687f65f683`이다.
- NitroFS 15개 파일과 ARM9/ARM7/배너를 추출했다. 핵심 `romfile.bin`은 2,080개 논리 엔트리, 1,743개 고유 블록, 1,139개 LZ10 압축 블록으로 분해했다.

### NDS 이미지

- NDS 그래픽은 타일·팔레트·타일맵 구조를 조합하여 PNG로 추출 가능함을 확인했다.
- `Extracted_NDS_Image`에 RGBA PNG 981장을 생성했다.
  - `Mapped_Screens`: 타일맵까지 조합한 높은 신뢰도 화면 12장.
  - `Raw_Tile_Sheets`: 위 화면의 원시 타일 시트 12장.
  - `NitroFS_Icons`: 파일명이 확인된 아이콘 4장.
  - `Approximate_Tile_Sheets`: 공용 팔레트는 확인했으나 실제 배치가 미확정인 참고용 953장.
- 잘못 초상화로 추정했던 `Portraits`와 `_diagnostic`은 지정 폴더에서 제거하고 임시 보관 폴더로 이동했다. 현재 추출 폴더에는 용도별 결과만 남아 있다.
- 모든 PNG 981장을 Pillow로 다시 열어 디코딩 오류 0건을 확인했다. 정확한 분류와 원본 엔트리는 `image_manifest.json`, 주의사항은 `README.md`에 기록했다.

### NDS 한국어 텍스트

- ARM9의 실제 한국어 문자열 영역이 CP949임을 확인했다. 메뉴·시스템·전투·대사 계열 2,651개를 NUL 경계와 엄격한 CP949 디코딩으로 추출했다.
- `romfile.bin` 엔트리 1999에서 780개 포인터와 끝 포인터를 가진 인물 열전 테이블을 확인했다. 모든 780개 경계를 복원했으며 CP949 디코딩 오류는 0건이다.
- `Extracted_NDS_Text`에 총 3,431개 레코드를 저장했다.
  - `text_review.html`: 검색, 직접 편집, 편집 JSON 내보내기/불러오기.
  - `sangokushi_ds_text.json`: 원본 오프셋, 바이트, 제어코드, 변수, 참고문을 모두 보존.
  - `text_manifest.json`, `README.md`: 출처 해시, 범위와 사용 규칙.
- HTML 번역 칸에서는 ESC 제어코드를 숨기고 `%s/%d` 등은 `〔문자열〕/〔숫자〕`로 표시한다. 원시 제어코드와 변수는 JSON에 별도로 보존한다.
- HTML 내 JavaScript 구문 검사를 통과했고, 3,431개 전 레코드의 `raw_hex → CP949 → raw_text` 왕복 검증 오류는 0건이다.

### 무결성 검증

- `tools\verify_sangokushi_ds_extraction.py`로 원본 ROM 해시, 분해 영역 해시, PNG 디코딩, 텍스트 왕복, HTML 포함 여부를 통합 검증했다.
- 전체 결과는 `Sangokushi DS rom\verification_report.json`에 기록했으며 최종 상태는 `pass: true`이다.
- 원본 NDS ROM의 현재 SHA-256이 최초 분해 매니페스트와 동일하여 원본 미변경을 확인했다.

## 3DS 번역 HTML 최종 JSON 추출 검증 (2026-08-27)

- 사용자가 완료한 `Extracted_Text\text_review.html`에서 실제 브라우저 내보내기 동작을 재현하는 `tools\export_sangokushi2_translation.py`를 추가했다.
- 최종 권위 필드는 `한국어 번역` 열의 `translation_ko`로 확정했다. `한국어 번역 (AI)`는 별도 필드로 내보내지 않는다.
- HTML 내부는 초기 `autoTranslations` 객체와 43개 추가 병합 블록을 사용한다. 기존 수동 `translation_ko` 134개는 그대로 우선하고, 빈 항목 4,226개에만 AI 결과를 복사한다.
- `Extracted_Text\sangokushi2_translation.json`을 실제 생성했다. 전체 4,848개 행, 고유 ID 4,848개, 중복 ID 0개다.
- 비기술 번역 대상 4,360개 모두 `translation_ko`가 채워져 있으며 빈 번역은 0개다.
- 번역문의 `<$xx>` 제어코드 순서를 원문과 비교한 결과 불일치 0건이다. 제어코드는 추출 단계에서 수정하지 않고, 실제 삽입 시 원본 구조를 기준으로 중복·누락 여부를 재판단한다.
- 상세 수치와 원본/출력 SHA-256은 `Extracted_Text\translation_export_report.json`에 기록했다.

## 3DS 번역 잔존 일본어·문자 경계 오류 보정 (2026-08-27)

- 번역 완료본을 한자, 히라가나·가타카나, 반각 가나, Unicode 사설영역 문자, `[깨짐]` 표식까지 범위를 넓혀 전수 재검사했다.
- 원인은 일부 CP932 2바이트 문자가 추출 행 경계에서 갈라져 앞 행의 끝이 가짜 `<$91>` 같은 제어코드로, 다음 행의 시작이 한자·사설영역 문자나 깨진 문자열로 해석된 것이었다.
- `text_review.html`에 62개 ID의 `translationCorrections`와 16개 문자 치환 규칙을 추가했다. 페이지가 열릴 때 보정 결과를 비교용 AI 열이 아니라 실제 `rows[].translation_ko`, 즉 **한국어 번역 입력칸**에 적용한다.
- 사용자가 HTML에서 보정된 한국어 번역을 다시 수정하면 해당 입력값이 그대로 JSON 내보내기에 반영된다. JSON 불러오기 후에는 불러온 사용자 값이 우선한다.
- `tools\export_sangokushi2_translation.py`도 HTML과 동일한 보정·문자 치환 규칙을 적용하도록 갱신하고 최종 JSON을 다시 생성했다.
- 최종 전수 검사 결과: 번역 대상 빈칸 0건 / 일본어 가나 0건 / 한자 0건 / 사설영역 문자 0건 / `[깨짐]`·대체문자 0건 / 형식 자리표시자 불일치 0건이다.
- 제어코드 불일치 27건은 이번에 확인한 CP932 문자 경계의 가짜 제어 바이트를 번역 화면에서 제거한 결과다. 실제 삽입 단계에서는 원본 연속 바이트를 기준으로 다시 결합하며, 이 27건을 그대로 제어코드 누락으로 취급하지 않는다.

## 원클릭 3DS 리빌드 자동화 (2026-08-27)

- `Rebuild_San2.bat`와 내부 헬퍼 `Rebuild_San2.ps1`을 작업 루트에 추가했다.
- 더블클릭 시 `Sangokushi 2 Rebuild` 폴더를 입력으로 사용하고 `E:\Codex_01\Sangokushi2\san2.3ds`를 생성한다.
- GUI `ToolkitForm.exe` 자동 조작 대신 동일 툴킷의 `DotNet3dsToolkit.Converter.Build3DSDecrypted(source, output)` 공개 함수를 직접 호출한다.
- 다운로드 ZIP의 Windows Internet Zone 표식 때문에 PowerShell의 .NET 로드가 거부될 수 있어 실행 시 툴킷 파일에 `Unblock-File`을 적용한다.
- 기존 `san2.3ds`가 있으면 삭제 후 새로 빌드하며, 성공 시 파일 크기와 SHA-256을 표시한다.
- 실제 테스트 실행 결과 `san2.3ds`가 정상 생성되었고 크기는 536,870,912바이트(512 MiB)였다.

## 최소 폰트 위치/크기 프로브 (2026-08-27)

- 사용자 요청에 따라 전체 2,350자 치환과 한글 별칭 인코딩을 잠시 중단하고, 런타임에서 이미 정상 출력되는 원본 일본어 `三國志` 세 글자의 **기존 문자 코드와 기존 atlas 셀을 그대로 유지**하는 최소 프로브를 만들었다.
- `tools\build_sangokushi2_font_position_probe.py`를 추가했다. `Sangokushi 2 Original`을 읽고 수정 산출물은 규칙대로 `Sangokushi 2 Patch`에만 쓴다. `Sangokushi 2 Rebuild`는 사용자가 수동 관리하므로 작업 에이전트가 건드리지 않는다.
- 원본 `code.bin`의 JIS→atlas 조회표로 실제 셀을 확인했다: `三`(SJIS `8E4F`)→셀 1334, `國`(`9AA0`)→셀 3058, `志`(`8E75`)→셀 1372.
- 원본 `font.g1t`에서 세 글리프의 실제 ink bbox를 측정한 결과 모두 `[0,0,12,16]`, 즉 게임의 12×16 셀 전체를 사용한다. Sam3KRFont의 `삼/국/지`는 각 글리프의 빈 여백을 제거한 뒤 이 원본 bbox를 넘지 않도록 최근접 이웃으로 맞췄다.
- 이번 프로브 `Sangokushi 2 Patch\RomFS\Common\Font\font.g1t`는 **정확히 셀 1334, 1372, 3058 세 곳만 변경**되었고 그 밖의 셀 변경은 0개다. SHA-256: `59EBC04BC951155ACEF71E16B954882D75DE12AEE13A633E50734D9EA3EF624F`.
- `Sangokushi 2 Patch\RomFS\Message\msgsec01.dat`은 한글 인코딩 변수를 완전히 배제하기 위해 **원본 일본어 파일과 bit-exact 동일**하게 되돌렸다. SHA-256: `E4D5D89AEE1E07FFC3E6223834682A8839F1B098DC0D035938E11BAF0D4F06E1`.
- 따라서 이 테스트의 기대 화면은 원본 문장 구조를 그대로 유지하되 `『三國志Ⅲ』`의 세 글자만 `『삼국지Ⅲ』`처럼 보이는 것이다. 성공하면 G1T 셀 위치/크기/재패킹 경로를 확정하고, 실패하면 문자 인코딩이나 code.bin이 아니라 G1T 런타임 해석 자체를 다시 추적한다.
- 검증 자료: `analysis\font_position_probe_report.json`, `analysis\font_position_probe_preview.png`. 정적 검증 `pass: true`, 변경 셀 정확성 및 메시지 bit-exact 원본 여부 모두 통과했다.

## NDS 한국어판 대조 번역 검수 (2026-08-27)

- `Extracted_Text\text_review.html`의 3DS 번역본을 `Extracted_NDS_Text\text_review.html`의 NDS 한국어판 3,431개 레코드와 비교했다.
- 단순 문자열 유사도만으로 수정하지 않고, 3DS 쪽 fragment가 단어/문장 중간에서 갈린 경우에는 앞뒤 ID를 이어 실제 표시 문장을 확인했다. 예를 들어 `더` + `이상 건조할 수 없습니다`, `모` + `반을 일으킬 것입니다`처럼 개별 fragment만 보면 어색하지만 연결하면 정상인 항목은 수정하지 않았다.
- 실제 의미 오류 또는 어색한 표현으로 확인된 16개 ID를 `translationCorrections`에 추가 보정했다.
- 대표 보정: `그 부대로는 갈 수 없습니다`→`그 부대로는 할 수 없습니다`, `완전히 실패입니다`→`완전한 실패입니다`, `누구에게 모병시키시겠습니까?`→`누구에게 모병을 시키겠습니까?`, `누구에게 훈련시키시겠습니까?`→`누구에게 훈련을 시키겠습니까?`.
- NDS판 시나리오 제목을 참고해 `유비 자웅을 감추고 신야에 성을 빌리다`→`유비 자복하여 신야에 임시거처를 둔다`, `와룡 중원에 날아 천하를 바라보다`→`와룡 중원에 춤추며 천하를 바라본다` 등 시나리오 제목 8개 fragment도 자연스럽게 보정했다.
- 신규 보정 16개에 대해 원문 `source_text`의 `<$xx>` 제어코드와 보정문을 직접 비교한 결과 **제어코드 불일치 0건**이다.

## 폰트 런타임 재분석 및 12×16 최소 출력 시험 (2026-08-27)

- 사용자가 `Common\Font\font.g1t`의 두 A4 시트를 전부 투명화한 진단본을 Citra에서 실행한 결과, 화면의 메뉴/본문 글자가 실제로 모두 사라졌다. 따라서 `RomFS\Common\Font\font.g1t`가 해당 화면의 실제 런타임 폰트임을 확정했다.
- 과거 `16×16/64열` 가설을 다시 검증했으나 원본 일본어 글리프 형태와 맞지 않았다. 반면 원본 A4를 `12×16/85열`로 해석하고 Y축을 편집 방향으로 뒤집으면 셀 550~560 구간이 정상적인 일본어 글리프 연속 형태로 나타났다. 따라서 편집 논리 격자는 다시 **12×16/85열**로 확정하고 16×16 가설은 폐기한다.
- `build_sangokushi2_font_test.py`의 A4 디코더/인코더를 원본 두 시트에 대해 무수정 왕복 검증했다. 시트0 524,288바이트, 시트1 262,144바이트 모두 **바이트 차이 0건**으로 원본과 완전히 동일했다. 따라서 기존 깨짐 원인은 G1T 재패킹 자체가 아니라 글리프 매핑/대규모 치환 방식이었다.
- 원본 `ExeFS\code.bin`의 JIS→셀 조회표 `0x1AC054`를 다시 읽어 `0x889F~0x88A6`이 정확히 셀 `557~564`를 가리키는 것을 확인했다.
- 새 도구 `tools\build_sangokushi2_firstline_test.py`를 추가했다. 전체 2,350자를 건드리지 않고 셀 557~564 딱 8개만 `삼국지한글테스트`로 치환한다.
- 각 한글은 Sam3KRFont 원본 glyph의 빈 여백을 자른 뒤, 해당 일본어 셀의 **원래 ink bbox를 절대 넘지 않도록 비율 유지 리사이즈·중앙 정렬**한다. 예: 셀557 원본 bbox `(0,3)-(12,14)`, 셀558 `(0,3)-(12,15)` 등.
- 새 시험본은 `Sangokushi 2 Patch\RomFS\Common\Font\font.g1t`, `Sangokushi 2 Patch\RomFS\Message\msgsec01.dat`, `Sangokushi 2 Patch\ExeFS\code.bin`에 생성했다. `code.bin`은 원본과 바이트 단위 동일하다.
- 첫 줄 메시지는 원본 고정 슬롯을 유지한 채 `0x889F~0x88A6` 8개 별칭으로 구성했고 화면 기대값은 `삼국지한글테스트`, 둘째 줄은 원본 `ゲームです。` 그대로다.
- 검증 결과: A4 identity round-trip 통과, stock alias map `[557,558,559,560,561,562,563,564]`, 대상 8셀 밖 변경 픽셀 **0**, `code.bin` 원본 동일, 전체 테스트 report `pass: true`.

## 삼국지1 한글패치 폰트 계승 실험 — 런타임 실패 (2026-08-28)

- 런타임에서 한글은 나오기 시작했지만 Sam3KRFont를 임의 저번호 셀(558~)에 재축소해 넣는 방식은 글자 위치·베이스라인 품질이 불안정하고 본문/금색 UI 모두 잘못된 글리프를 참조하는 문제가 있었다.
- `Sangokushi 1 Patched\RomFS\UI\font.g1t`와 `Sangokushi 2 Original\RomFS\Common\Font\font.g1t`는 모두 786,492바이트이며 동일한 3DS A4 1024x1024 + 1024x512 구조를 사용한다.
- 삼국지1 패치 `ExeFS\code.bin`의 JIS→글리프 테이블 기준점은 `0x3C4A18`, 삼국지2 원본은 `0x1AC054`로 확인했다. 0x889F 부근은 양쪽 모두 셀 557,558,559...로 동일하게 이어지며, 현재 필요한 한국어 글자들의 실제 KS 셀에 대해 삼국지1/2에서 공통 JIS 별칭이 존재함을 검증했다.
- 따라서 Sam3를 다시 리사이즈하지 않고, 삼국지1에서 이미 게임용으로 조정된 한글 셀 557~2906(2,350셀)을 삼국지2 원본 `font.g1t`의 같은 12x16/85열 위치에 그대로 이식하는 방식으로 전환했다. 이 범위 밖 삼국지2 일본어/기호 글리프는 보존한다.
- 한글 문자열 인코딩은 각 글자의 Sam3 KS 인덱스로 목표 셀 `557 + index`를 구한 뒤, 삼국지2 원본 JIS 테이블을 역검색해서 그 셀을 가리키는 유효 JIS 코드의 Shift-JIS 별칭을 사용한다. 예: `삼`→셀1669/SJIS 90AE, `국`→셀675/899C, `지`→셀2240/94C5, `한`→셀2767/98B0.
- 본문은 ASCII 공백/III를 쓰지 않고 전각 경로만 사용하여 `『삼국지Ⅲ』를 기반으로 한\n게임입니다．`로 구성했다. 이 방식은 이전 화면의 `||||` 같은 반각/모드 전환 artefact를 피하기 위한 것이다.
- ROM 전체 검색 결과 금색 `オリジナルモード`는 `Sangokushi 2 Original\ExeFS\code.bin`의 `0x1CFB64`에 단 1회 존재한다. 해당 16바이트 슬롯을 동일 한글 별칭 방식의 `오리지널 모드`로 수정했다.
- 생성 도구: `tools\build_sangokushi2_s1_inherit.py`. 산출물은 규칙대로 `Sangokushi 2 Patch`에만 생성한다.
- 최종 산출물 SHA-256: `font.g1t` A2D1F0380DC0D75AE6BD5878B4F3B9C818F0122A269FA9654B149D3498E2BE76 / `msgsec01.dat` B89C46D10E08EC119F4884D45092DDA57B751FD1A60451FD4EC166A751EA2008 / `code.bin` 3B1FB51A95CDB3F59D0783487581C31BE93026CBCC6803E69268CB5587DB7707.
- 스크립트의 정적 검증은 `pass: true`였으나, 실제 Citra에서는 외계어가 출력되어 **글리프 의미 매핑 가정이 틀렸음**을 확인했다. 특히 삼국지1 셀 557~2906의 글자 순서를 Sam3KRFont `glyph_map` 순서와 동일하다고 본 가정은 폐기한다. 삼국지1 패치는 구조·시각 품질 참고용으로만 사용하며, 실제 글자 의미를 런타임/메시지 바이트로 증명하기 전에는 셀 전체를 이식하지 않는다.

## 금색 「오리지널 모드」 실제 화면 소스 확정 (2026-08-28)

- Citra 화면의 금색 `オリジナルモード`는 `code.bin 0x1CFB64`의 하드코딩 문자열이 아니라 **`RomFS\Message\msgsec01.dat`의 `msgsec01_0040_001`**이 실제 표시 소스임을 확인했다.
- 해당 fragment의 파일 오프셋은 **`0xEB0`**, 원본 raw는 `1B 43 39 1B 4B B5 D8 BC DE C5 D9 D3 1B 48 81 5B 1B 4B C4 DE`이며 추출 텍스트는 `<$1B>C9<$1B>Kｵﾘｼﾞﾅﾙﾓ<$1B>Hー<$1B>Kﾄﾞ`이다.
- 폰트 전체 투명화 시험에서 금색 문구도 같이 사라졌으므로 이 문구 역시 `Common\Font\font.g1t`를 사용하는 실제 텍스트 렌더링이다.
- 이후 이 화면의 금색 메뉴를 수정할 때는 `code.bin` 문자열이 아니라 **`msgsec01_0040_001 / 0xEB0`을 권위 위치로 사용한다.** `code.bin 0x1CFB64`는 별개의 하드코딩 문자열로 취급한다.

## 삼국지1 한글패치의 폰트 그리기 방식 비교 (2026-08-28)

- 삼국지1 패치의 `RomFS\UI\font.g1t`와 삼국지2 원본 `RomFS\Common\Font\font.g1t`의 12×16 논리 셀 557~2906을 동일 디코더로 측정했다.
- 삼국지1 한글패치 2,350셀의 bbox 중앙값은 **12×16**, 좌측/상단 여백 중앙값은 **0/0px**이다. 폭 12px가 1,834개, 높이 16px가 2,150개로, 한글을 작은 9×12·11×13 박스로 중앙 배치하는 방식이 아니라 **전각 셀을 거의 꽉 채우는 방식**임을 확인했다.
- 삼국지2 일본어 원본도 bbox 중앙값이 12×16이라 기본 시각 크기는 동일하다. 따라서 Sam3KRFont를 11×13으로 축소하는 기존 시험 방식은 폐기하고, 글리프의 빈 여백을 제거한 뒤 **12×16 전체로 리사이즈하는 방식**을 폰트 품질 캘리브레이션에 사용한다.
- 삼국지1/2 `code.bin`의 0x889F 부근 JIS→글리프 테이블은 양쪽 모두 557,558,559...로 동일하다. 다만 삼국지1의 실제 한글 글자 의미/셀 순서는 Sam3 `glyph_map`과 동일하다고 볼 수 없으므로 삼국지1 셀 전체 이식은 하지 않는다.

## 최초 한글 가시 성공본을 기준점으로 고정 (2026-08-28)

- 최근 캘리브레이션 과정에서 `12x16 강제 확대`, `삼국지 3글자만 교체`, 물리셀 +1 보정 등 여러 변수를 동시에 바꾸면서 최초 성공 화면보다 한글 가시성이 악화됐다.
- 따라서 이후 분석의 기준점을 **실제 Citra에서 `국지한글테스트`까지 한글이 읽혔던 `tools\build_sangokushi2_firstline_test.py` 산출물**로 고정한다.
- 2026-08-28에 해당 스크립트를 다시 실행하여 `Sangokushi 2 Patch`를 bit-exact 재생성했다. 기대 문자열은 `삼국지한글테스트 / ゲームです。`, 런타임 관측 이력은 첫 `삼`만 누락되고 나머지 `국지한글테스트`가 한글 형태로 보였던 상태다.
- 기준본 SHA-256: `font.g1t = 7D1C3EE2568538A54858FF7BB24F93B8017D2FE6845B3FC767433D17DED4142E`, `msgsec01.dat = A587761E2B3C34DE0A80DD3D37248BDED02DD6389FC83D36EC4B8308DC75E554`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98(원본)`.
- 이후에는 이 기준본에서 한 번에 **한 변수만** 변경한다. 기준본이 다시 런타임 재현되기 전에는 폰트 크기/셀 위치/문자모드/메시지 구조를 동시에 변경하지 않는다.

## 폰트 전용 캘리브레이션 v4 (2026-08-28)

- 의미 없는 텍스트 구조 변경을 배제하기 위해 `tools\build_sangokushi2_font_calibration_v4.py`를 생성했다.
- 원본 `msgsec01_0041_003`의 `『三國志Ⅲ』` 중 **`三國志` 6바이트만** `0x88A0/0x88A1/0x88A2` 별칭의 `삼국지`로 교체한다. 그 뒤 `Ⅲ』ｦ<$1B>K...`, 개행, 반각 가나, 제어코드, 슬롯 마지막 `0x81` 등은 원본과 bit-exact 유지한다.
- Sam3 `삼/국/지`는 빈 여백을 자른 뒤 12×16 전체로 비등방 최근접 리사이즈하여 각각 물리 셀 558/559/560에 넣었다. 새 bbox는 세 글자 모두 `(0,0,12,16)`이다.
- 금색 `オリジナルモード`는 이번 폰트 캘리브레이션에서는 원문 그대로 두며, 이후 폰트 품질이 확정된 뒤 실제 위치 `msgsec01_0040_001 / 0xEB0`에서 별도 번역한다.
- 캘리브레이션 산출물 SHA-256: `font.g1t = 2E1A4642A245E07F8D8BBF45A6C167949AFB5586F8B85C2D5D421F5A1D15EE41`, `msgsec01.dat = 8CA7705911CFFE4635D635B997E8382CB0DD8CEEC0C9D40027AFB42F67AA4C52`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98(원본)`. 정적 검증 `pass: true`.
- 실제 Citra 결과에서는 `三國志`를 교체한 3글자가 한글이 아니라 원래 일본어 계열 글리프로 보였다. 이후 일본어 문장은 캘리브레이션 의도대로 원본이므로 정상이다. 이 결과로 **이 화면 본문이 code.bin 문자열과 섞여 출력되는 것이 아니라 `msgsec01.dat`에서 정상 출력되고 있으며, 실패 지점은 문자 코드→글리프 ID 이후의 글리프 ID→font.g1t 실제 위치 해석**임을 확정했다.
- `code.bin`의 JIS 문자맵에서 `0x88A0` 등은 558,559,560...의 ID로 정상 변환되지만, 이 ID를 `12×16/85열` 또는 `16×16/64열` 고정격자 번호로 직접 해석한 어느 위치도 `三/一/二` 등 정답을 아는 일본어 글리프 모양과 일치하지 않았다. 따라서 **JIS 테이블 값은 물리 atlas 셀 번호가 아니라 중간 글리프 ID**이며, 뒤에 UV/메트릭 변환 단계가 존재한다.
- 향후에는 글리프 ID→실제 atlas 좌표 변환을 복원하기 전까지 새 한글 셀 위치를 추측해 패치하지 않는다. 단순 `+1 셀`, 삼국지1 셀 전체 이식, 12×16/16×16 직접 ID 대응 가설은 폐기 또는 보류한다.

## 한글 출력 성공 방식 복귀 + 슬롯 경계 복원 v2 (2026-08-28)

- Citra에서 `국지한글테스트` 등 실제 한글이 읽히기 시작했던 방식으로 복귀했다. `tools\build_sangokushi2_recovery_v2.py`는 Sam3KRFont를 삼국지2 원본의 저번호 12×16 셀 **558~576**에 직접 넣고, `0x88A0~0x88B2` 별칭을 사용하는 방식이다. 셀557/0x889F는 이전 런타임에서 첫 글자가 사라진 이력이 있어 계속 제외한다.
- 새 `font.g1t` SHA-256은 `6424257C4EF29D56A0A52C109A320642C0AB52672A4E14931C7C0E6095C4B0CA`이며, 이전에 한글이 읽혔던 `menu_korean_test`의 폰트와 **bit-exact 동일**하다. 즉 이번 시험에서는 폰트 배치 자체를 변경하지 않고 메시지/코드 문자열 구조만 바로잡았다.
- 이전 실제 번역문 시험에서 원본 47바이트 슬롯을 통째로 평문으로 덮어 원래 구조를 훼손했다. 특히 슬롯 마지막 `0x81`은 슬롯 직후 원본 바이트 `0x42`와 합쳐 `。`를 만드는 선행 바이트인데, 이를 없애면서 화면에 단독 ASCII `B`가 나타났다.
- 복구 v2는 첫 줄을 정확히 28바이트의 2바이트 문자들(`『삼국지Ⅲ』를　기반으로　한`)로 만들고 1바이트 패딩 뒤 **원래 개행 위치 29**를 유지한다. 둘째 줄 `게임입니다`는 10바이트로 넣고, 남은 6바이트를 뒤쪽 공백으로 패딩한 뒤 슬롯 마지막 `0x81`을 그대로 유지한다. 실제 readback은 슬롯 경계에서 `...20 81 42 05...`로 확인되어 원본 `。` 조합이 복원됐다.
- 금색 `オリジナルモード`(`code.bin 0x1CFB64`)도 1바이트 ASCII 공백/NUL을 문자열 중간에 넣지 않고, `오리지널` + 전각공백 `0x8140` + `모드` + 전각공백의 **정확히 16바이트/8개 2바이트 문자**로 치환했다.
- 복구 v2 출력 SHA-256: `font.g1t = 6424257C4EF29D56A0A52C109A320642C0AB52672A4E14931C7C0E6095C4B0CA`, `msgsec01.dat = 91260A56D42233FE14C91F832E5BDF56F9BAC4EB77005E7463DFAF79C9F80B10`, `code.bin = 5F2FEC4BD202E149823D1BCAD179C2D621DEFEAF0C9E78AFC440F007AB1A2150`.

## 원본 일본어 bbox + 물리셀 +1 보정 v3 (2026-08-28)

- 이전 Citra 결과에서 `삼국지한글테스트`가 `국지한글테스트`처럼 첫 글자만 빠지고 이후 문자열이 읽힌 현상을 다시 기준점으로 삼았다. 이 현상은 `code.bin`의 논리 글리프 ID N이 12×16 편집 격자의 **물리 셀 N+1**을 참조한다고 보면 정확히 설명된다.
- 새 도구 `tools\build_sangokushi2_offset_bbox_v3.py`를 추가했다. 한글 별칭은 다시 `0x889F`부터 사용하고 `code.bin`의 논리 매핑(`0x889F -> 557`)은 그대로 둔다. 실제 비트맵만 물리 셀 `558`부터 기록해 논리 N -> 물리 N+1 가설을 런타임 검증한다.
- 한글 크기는 공통 11×13 강제 크기를 쓰지 않는다. 각 대상 물리 셀에 **원래 존재하는 일본어 글리프의 alpha bbox**를 직접 측정하고, Sam3KRFont 글리프를 그 bbox 안에만 비율 유지 최근접 이웃으로 맞춘다. 예: `삼`은 논리557/물리558, 원본 bbox `(0,3)-(12,15)` 안에 배치한다.
- 3DS 게임도 반각을 사용한다. 이 게임 원문은 실제 CP932 반각 가나와 `<$1B>K`/`<$1B>H` 모드 전환을 사용한다. 한글 별칭은 2바이트이므로 v3에서는 전각 H 경로로 출력한다.
- 금색 `オリジナルモード`의 실제 화면 소스를 다시 추적해 `Message/msgsec01.dat`의 `msgsec01_0040_001`, 파일 오프셋 `0xEB0`, 원시 바이트 `1B43391B4BB5D8BCDEC5D9D31B48815B1B4BC4DE`임을 확인했다. 폰트 전체 투명화 테스트 때 금색 문구도 사라졌으므로 동일 `font.g1t` 렌더러를 사용한다. 이전 `code.bin 0x1CFB64` 문자열 수정은 이 화면의 실제 소스가 아니므로 v3 `code.bin`은 원본 bit-exact로 복원한다.
- 금색 문구는 원래 색상 제어 `<$1B>C9`를 보존하고 `<$1B>H` 전각 모드로 `오리지널 모드`를 삽입했다. 본문은 `『삼국지Ⅲ』를 기반으로 한 / 게임입니다。`로 구성하고 47바이트 슬롯 마지막 `0x81`을 유지해 슬롯 다음 `0x42`와 합쳐 `。`가 되도록 했다.
- 정적 검증 결과: 대상 물리 셀 외 alpha 변경 0픽셀 / 본문·금색 원문 각각 단일 위치 일치 / `code.bin` 원본 동일 / report `pass: true`.
- v3 SHA-256: `font.g1t` DE41535B678586CFF49E71DC3968DDBC48BAB9E741C087FF68A778AC5247C55D / `msgsec01.dat` C2BFD668D9D0D3A4A106131328DA43997084C4BC6DB73A7E4D6DDAF41E4C1E76 / `code.bin` AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98.
- 물리셀 +1 보정은 이후 v5 Citra 런타임에서 직접 재검증했다. 기준본(`557~564`)에서는 첫 `삼`이 누락되고 `국지한글테스트` 중심으로 보였으나, 동일 메시지/동일 code.bin 상태에서 비트맵만 `558~565`로 한 칸 이동한 v5에서는 `삼국지한글테스트` 8글자가 훨씬 더 온전하게 드러났다. 따라서 현재 작업에서는 **논리 글리프 N -> 12×16 편집 물리셀 N+1**을 Citra 실측 기반의 작업 규칙으로 사용한다.

## +1 물리셀 런타임 확정 v5 / 실제 첫 문장 v6 (2026-08-28)

- `tools\build_sangokushi2_shifted_probe_v5.py`는 기준본의 메시지와 `code.bin`을 bit-exact 유지하고 폰트 비트맵만 `557~564 -> 558~565`로 옮기는 단일 변수 테스트다. 겹치는 셀은 사전에 snapshot하여 연쇄 덮어쓰기 없이 이동한다.
- v5 Citra 결과에서 `삼국지한글테스트`가 이전보다 명확하게 출력되었다. 특히 `삼/국/지/한/글/테/스/트`의 한국어 도형이 확인되어 +1 보정 방향을 채택한다. 프로브 뒤쪽의 일본어처럼 보이는 잔여는 v5 첫 줄이 8개 별칭 뒤 13바이트 ASCII space로 채워진 캘리브레이션 문자열이라는 별도 변수이므로 실제 문장에서는 제거한다.
- v5 SHA-256: `font.g1t = BB093DA3A6C20AFAB5DF08A2521F107916978B0AD18796072739B5DF3FDCA224`, `msgsec01.dat = A587761E2B3C34DE0A80DD3D37248BDED02DD6389FC83D36EC4B8308DC75E554`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98(원본)`.
- 실제 첫 문장용 `tools\build_sangokushi2_intro_v6.py`를 생성했다. 기대 출력은 `『삼국지Ⅲ』를 기반으로 한` / `게임입니다。`이다. 금색 `オリジナルモード`는 이번 v6에서는 원본 그대로 두어 본문 검증과 분리한다.
- v6 첫 줄의 가시 문자열은 28바이트이며, 원본 개행 위치 29를 유지하기 위해 trailing ASCII space **1바이트만** 둔다. v5처럼 13바이트 패딩하지 않는다.
- v6 둘째 줄은 `게임입니다` 10바이트에 비가시 `<$1B>H` 제어 3개(6바이트)를 배치하고 마지막 `0x81`을 보존한다. 슬롯 직후 원본 `0x42`와 결합해 `。`가 출력된다. 전체 본문 슬롯은 원본과 동일한 47바이트다.
- v6은 한글 14자(`삼국지한를기반으로게임입니다`)를 원본 별칭 `0x889F~0x88AC`에 연결하고, 논리 셀 `557~570`의 Sam3 글리프를 물리 셀 `558~571`에 배치한다. 크기 산식은 v5와 동일하게 각 논리 원본 셀의 일본어 alpha bbox 안에 비율 유지 최근접으로 맞춘다.
- v6 정적 검증: 선택 셀 밖 alpha 변경 0픽셀 / `code.bin` 원본 bit-exact / 슬롯 길이 47 / 다음 바이트 `0x42` 확인 / report `pass: true`.
- v6 SHA-256: `font.g1t = 855554AD4CA70723CC1477F23512976F4C9691C03EE838EA94D57F5589E9CD5C`, `msgsec01.dat = 2D8CD57072D3732B71BA229825B9E5BB5965B1089EBABBAC1685C7C579B6F52`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98(원본)`.

## 원시 A4 폰트 셀 런타임 프로브 (2026-08-27)

- 최소 `三國志` 글리프 치환 테스트가 Citra에서 전혀 변하지 않아, 기존 `12×16/85열 + PNG Y축 반전 편집` 방식이 실제 런타임 셀 좌표와 일치하지 않음을 확정했다.
- 원본의 폰트 후보를 재검색한 결과 본문용 후보는 `RomFS\Common\Font\font.g1t`이며, `Debug\dbfont.g1t`는 256×128 RGBA 디버그 폰트, `Hex\UI\HexFontEffect.g1t`는 전투 효과용 텍스처 묶음이다.
- `code.bin`의 JIS 조회표는 기존 94×94 구간 뒤에도 글리프 ID 6082까지 연속 데이터가 이어진다. `font.g1t`의 1024×1024 + 1024×512 시트를 16×16/64열로 보면 총 6144셀이라 이 ID 범위와 구조적으로 부합한다. 따라서 과거 12×16/85열 가설은 폐기했다.
- 기존 PNG/A4 디코더 자체도 실제 한자 모양을 재현하지 못해 신뢰할 수 없으므로, 새 테스트는 PNG 변환을 완전히 우회한다.
- `tools\build_sangokushi2_raw_font_probe.py`를 추가했다. 원본 `code.bin`에서 확실히 유효한 Shift-JIS 별칭 `0x889F..0x88A6` 8개가 가리키는 글리프 ID `557..564`에 Sam3KRFont의 `삼국지한글테스트` 8글자를 **원시 PICA200 A4 16×16 타일 단위로 직접 기록**한다.
- `msgsec01_0041_003` 첫 줄은 같은 별칭 코드 8개로 고정 길이 치환하여 런타임 기대값을 `삼국지한글테스트`로 만들고, 둘째 줄 `ゲームです。`는 원본 그대로 둔다.
- 이번 프로브의 `Sangokushi 2 Patch\ExeFS\code.bin`은 원본과 바이트 단위로 동일하게 복원해 매핑표 수정 변수를 제거했다. SHA-256은 원본/출력 모두 `AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`이다.
- 프로브 출력 SHA-256: `font.g1t = DA16050A8F892254E99A7627AB300A6CBB427333AF25AC02DA82EA3260A6FB52`, `msgsec01.dat = A587761E2B3C34DE0A80DD3D37248BDED02DD6389FC83D36EC4B8308DC75E554`.
- 수정 산출물은 규칙대로 `Sangokushi 2 Patch`에만 생성하며 `Sangokushi 2 Rebuild`는 사용자가 직접 관리한다.

## 한글 런타임 2차 실패 원인 재분석 및 무-ExeFS 의존 방식 전환 (2026-08-27)

- 사용자 Citra 재시험에서도 금색 `オリジナルモード`가 일본어 그대로였고 흰색 본문은 동일한 글리프 조각으로 깨졌다. 이는 RomFS의 폰트/메시지는 적용되었지만 ExeFS `code.bin` 수정본은 실행 ROM에 반영되지 않았다는 강한 런타임 증거다.
- 원본 `font.g1t`의 셀 557~2906(2,350자)을 전수 측정했다. 빈 셀 0개, 글리프 잉크 폭 중앙값 12px, 높이 중앙값 16px이며 2,058자는 폭 12px, 1,976자는 높이 16px을 실제로 사용한다. 기존 Sam3 단순 16×16→12×16 축소본은 주요 한글이 대체로 9~10×12~13px만 사용해 원본보다 작았다.
- `build_sangokushi2_font_test.py`를 수정해 Sam3 글리프의 실제 잉크 bbox를 먼저 자른 뒤 원본 일본어와 동일한 **12×16 전체 셀**에 최근접 이웃으로 맞추도록 변경했다. 새 테스트 글리프 `삼국지게임입니다`는 모두 bbox `(0,0,12,16)`이며 재패킹 왕복 불일치 0건, 한글 영역 밖 변경 0픽셀이다.
- 원본 `code.bin`의 JIS 94×94 조회표(`0x1AC054`)를 전수 조사했다. 유효 항목은 5,583개이고 대응 셀도 5,583개로 전부 고유하며 셀 385~5967을 사용한다. 따라서 조회표 자체를 패치할 필요가 없음을 확인했다.
- 기존 실패 방식은 KS 인덱스에 대해 Shift-JIS 바이트를 단순 연속 생성해 원본 조회표의 미할당 JIS 위치 612개를 밟았고, 이를 보정하려고 `code.bin` 조회표를 수정하는 구조였다. 이 구조는 ExeFS가 빠지면 즉시 깨진다.
- 새 방식은 원본 조회표에서 **원래 셀 557~2906을 가리키는 유효 JIS 코드 2,350개를 셀 번호순으로 추출**하여 KS 한글 2,350자의 별칭 코드로 사용한다. 첫 별칭은 `0x889F`, 마지막 별칭은 `0x9980`이며 단순 연속 범위가 아니다.
- 새 매핑으로 `삼/국/지/게/임/입/니/다`를 원본 `code.bin`에 대조한 결과 각각 정확히 셀 `557 + KS 인덱스`를 가리켰다. 따라서 본문 한글은 `code.bin` 수정 없이 `font.g1t`+메시지 파일만으로 동작해야 한다.
- `build_sangokushi2_intro_test.py`를 이 원본-valid-JIS 별칭 방식으로 변경하고 `msgsec01.dat`를 다시 생성했다. 새 SHA-256은 `1D014CB1B7D9896A47C82024814F5FEA9868CF6FAB5F3F7F14BC3C1761BF2B8D`다.
- `patch_sangokushi2_code_ui_test.py`도 조회표를 더 이상 수정하지 않도록 변경했다. 현재 `code.bin` 수정은 하드코딩 문자열 `オリジナルモード`→`오리지널 모드` 16바이트 슬롯만 대상으로 하며 font-map 변경 수는 0개다. 새 SHA-256은 `54BC65DC2695166E1A60991B5E90B9F5020EBFED1B38440D262E52511F7FBD7D`다.
- 따라서 다음 테스트의 핵심 판정은 흰색 설명문이다. ExeFS가 계속 누락되어 금색 `オリジナルモード`가 일본어로 남더라도, 새 RomFS만 적용되면 흰색 설명문은 정상 한글로 보여야 한다. 금색 UI까지 한글화하려면 별도로 ExeFS `code.bin`을 리빌드에 포함시키는 경로를 해결해야 한다.

## Citra 한글 폰트 깨짐 재확인 및 테스트 세트 재생성 (2026-08-27)

- 사용자가 제공한 깨짐 화면에서 금색 `オリジナルモード`가 일본어 그대로 표시되는 것을 확인했다. 현재 `Sangokushi 2 Patch\ExeFS\code.bin` 테스트 수정본은 같은 문자열을 `오리지널 모드`로 바꾸므로, 해당 스크린샷의 테스트 ROM에는 수정 `code.bin`이 포함되지 않았다고 판단할 수 있다.
- 이 경우 한글 문자열은 `0x889F..0x94FC` 별칭 코드로 들어가지만 게임은 원본 `code.bin`의 JIS→아틀라스 셀 조회표를 사용한다. 따라서 Sam3 한글이 들어 있는 셀 557~2906이 아니라 기존 일본어/미등록 셀을 참조하여 글리프 조각이 섞여 보인다.
- `삼국지게임입니다`의 각 한글을 기준으로 `glyph_map → 별칭 Shift-JIS 코드 → JIS 94×94 조회표 → 패치 아틀라스 셀`을 교차검증했고 모두 `cell = 557 + KS index`로 일치했다.
- 최신 테스트 세트를 다시 생성했다: `Sangokushi 2 Patch\RomFS\Common\Font\font.g1t`, `Sangokushi 2 Patch\RomFS\Message\msgsec01.dat`, `Sangokushi 2 Patch\ExeFS\code.bin`.
- 재생성 검증: 폰트 2,350글리프 왕복 불일치 0 / 한글 영역 밖 변경 픽셀 0 / `code.bin` 2,350개 조회표 readback 불일치 0 / 세 파일 모두 원본과 동일 크기 유지.
- 현재 테스트 파일 SHA-256: `font.g1t = 37040122ec61f30159303077c8176812c11d19bcff7e6c6f8674729e670030d6`, `msgsec01.dat = cc0a84c9cf8e957051d5e2422e68a4669bf5b2f00552c548af519a918d2e3eeb`, `code.bin = b31479f004c66e56f960d911428ad117bf4abae46e89f5f4315e7f9d6c81ea7e`.
- 다음 Citra 테스트 ROM을 만들 때는 위 RomFS 2개뿐 아니라 **`ExeFS\code.bin`까지 반드시 함께 리빌드**해야 한다. 금색 메뉴 문구가 `오리지널 모드`로 바뀌지 않았다면 수정 `code.bin`이 적용되지 않은 빌드로 판정한다.

## 폰트 실제 물리 격자 14×14 확정 + v7 생성 (2026-08-28)

- 사용자 런타임 화면에서 12×16/+1셀 방식으로 한글 일부는 보였으나 `기`가 `ㄱ | ㅣ`처럼 벌어지고 옆 일본어 획이 섞이는 현상이 계속되어, 셀 크기 자체를 원본 아틀라스 전체 픽셀 주기로 재측정했다.
- `font.g1t` 시트0의 x/y 잉크량을 모든 후보 주기로 비교한 결과 **14px 주기에서만 경계 잉크가 거의 0**이었다. `x % 14 == 0` 평균 alpha는 약 3.4, `y % 14 == 13`은 약 0.57로 다른 주기 대비 압도적으로 낮았다. 1024px / 14 = 73셀(+2px tail padding)이다.
- 원본 셀 557~564를 14×14/73열로 직접 읽으면 각 셀마다 완전한 일본어 글리프가 독립적으로 들어 있고 마지막 행이 비어 있어 실제 물리 셀 정의를 확인했다. 기존 `12×16/85열`, `16×16/64열`, `12×16 +1셀` 해석은 폐기한다.
- 삼국지2 원본 2,350셀(557~2906)을 14×14로 재측정한 bbox 중앙값: left=1, top=0, width=13, height=13. 같은 방식으로 `Sangokushi 1 Patched\RomFS\UI\font.g1t`를 재측정한 한글 bbox 중앙값은 left=0, top=0, width=12, height=14였다. 따라서 한글은 14×14 셀을 넘기지 않고 최대 13×14 영역에 비율 유지 배치하는 것으로 확정했다.
- 새 도구 `tools\build_sangokushi2_intro_v7_14x14.py`를 작성했다. Sam3 글리프의 실제 ink bbox를 자른 뒤 최대 13×14로 최근접 비율 축소하고, JIS 글리프 ID N을 **동일한 14×14 물리 셀 N**에 직접 기록한다.
- v7은 실제 본문 `『삼국지Ⅲ』를 기반으로 한 / 게임입니다。`와 금색 `오리지널 모드`를 함께 적용한다. 금색 실제 소스는 `msgsec01.dat`의 `msgsec01_0040_001`, 오프셋 `0xEB0`이며 `<$1B>C9` 색상 제어를 유지한다. 본문은 `0xEFF` 슬롯과 마지막 `0x81 + 다음 0x42 = 。` 구조를 유지한다.
- 사용자 리빌드 방식에 맞춰 앞으로 Patch 변경 시 `font.g1t`, `msgsec01.dat`, `code.bin`을 항상 서로 일관된 한 세트로 전부 재생성한다. v7의 `code.bin`은 원본 bit-exact 파일이다.
- v7 정적 검증: 대상 14×14 셀 밖 alpha 변경 0픽셀 / 셀 round-trip mismatch 0 / `code.bin` 원본 동일 / report `pass: true`.
- v7 SHA-256: `font.g1t = 20AE75EA3E9BB693423AFD9B6905DA26F27D0DC01CF2313AE7CDA058C9741BE9`, `msgsec01.dat = 0F407AA5112CFA74C4F34CD4BEDC55D3A864CB3FA53BB04D0C6E2CC56B6B73B9`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`.

## v7 런타임 역매칭으로 physical = logical - 5 확정 + v8 생성 (2026-08-28)

- v7 Citra 결과에서 폰트 도트 품질은 크게 좋아졌지만, `삼국지` 자리에 `亜唖娃`, 금색 `오리지널 모드` 자리에는 `게임입니다` 계열 한글이 출력됐다. 따라서 14×14 셀 크기는 맞지만 `logical ID = physical cell` 가설은 틀렸음을 확인했다.
- 사용자 v7 스크린샷의 실제 `亜/唖/娃/阿/哀` 도트를 14×14 정규화 비트패턴으로 잘라 원본 `font.g1t` 전체 14×14 셀과 전수 Jaccard 매칭했다. 1위가 각각 물리셀 `552/553/554/555/556`으로 연속하며 점수도 0.689/0.787/0.699/0.908/0.675였다. 해당 문자 코드 `0x889F..0x88A3`의 `code.bin` 논리 ID는 `557..561`이므로 **physical = logical - 5**가 런타임으로 직접 확인됐다.
- v7 금색 제목도 같은 규칙을 독립적으로 확인한다. `오/리/널/모/드`의 논리 ID 571~575가 실제로 읽는 물리 566~570에 v7이 `게/임/입/니/다`를 넣어둔 탓에 금색 제목이 `게임입니다`처럼 출력됐다.
- v8 `tools\build_sangokushi2_intro_v8_fixedmap.py`는 시험 별칭 `0x889F..0x88B1`에 대해 물리 셀을 `logical-5`로 기록한다. 이 범위 밖 전체 폰트에 대해서는 아직 전역 규칙으로 확장하지 않는다.
- v7의 글자별 개별 crop/비율 resize는 글자마다 x/y 기준선이 달라지는 원인이었다. Sam3KRFont 현재 화면용 19자의 공통 원본 프레임은 모두 `(2,1)-(15,14)` 13×13 안에 들어가므로, v8은 이 **동일 13×13 프레임을 모든 글자에서 그대로 잘라 게임 14×14 셀 `(1,0)-(14,13)`에 리샘플링 없이 복사**한다. 따라서 Sam3 원본 도트 굵기와 공통 베이스라인을 그대로 유지한다.
- 본문 단어 사이에는 `0x8140` 전각공백을 쓰지 않고 1바이트 ASCII space를 사용하여 아직 검증되지 않은 전각공백 글리프 경로를 우회한다. 첫 줄은 원본 LF 위치 29바이트를 유지하고, 둘째 줄 뒤 남는 슬롯은 가시 텍스트 뒤의 `<$1B>H` 리셋으로 채우며 마지막 `0x81 + 다음 0x42 = 。`를 보존한다.
- 금색 `오리지널 모드`는 실제 소스 `msgsec01.dat / msgsec01_0040_001 / 0xEB0`에서 `<$1B>C9`를 유지하고 적용했다. `code.bin`은 원본 bit-exact이지만 사용자 복사 방식에 맞춰 Patch 세트에 항상 함께 다시 기록한다.
- v8 정적 검증: 대상 물리셀 밖 alpha 변경 0 / 셀 readback mismatch 0 / Sam3 공통 프레임 밖 획 0 / `code.bin` 원본 동일 / report `pass: true`.
- v8 SHA-256: `font.g1t = 6B9F34FCFC876496B223687A38F6743774FB97E916CCC4256A46FEF8B5FC7501`, `msgsec01.dat = 053648C441A0BDBCFD3FEFF4172CC0034D62AF58CF93BADEECDA8F70DCAF26A4`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`.

## v9 한국식 마침표 + 시나리오 설명문 첫 화면 적용 (2026-08-28)

- v8의 런타임 성공 방식(14×14/73열, 시험 별칭 `physical = logical - 5`, Sam3 공통 `(2,1)-(15,14)` 13×13 프레임을 `(1,0)`에 무리샘플링 복사)을 그대로 유지한다. 폰트 품질/기준선 방식은 변경하지 않았다.
- 한국어 문장 종결은 일본식 `。` 대신 ASCII `.`를 사용한다. `msgsec01_0041_003`의 원문 `。`는 fragment 경계에 `0x81 | 0x42`로 분할되어 있었으므로, v9은 `게임입니다.`의 `.`(0x2E)를 앞 47바이트 슬롯 안에 직접 넣고 독립 fragment `msgsec01_0042_000`의 `0x42`를 공백 `0x20`으로 바꿔 단독 `B` 출력 가능성을 제거했다.
- 2번 스크린샷의 상단 `シナリオ選択` 제목은 `Extracted_Text` 전체에서 문자열이 발견되지 않아 이미지 텍스처로 판단한다. 반면 아래 3줄 설명은 `RomFS\Message\msgsec11.dat`의 `msgsec11_0000_002 + msgsec11_0001 + msgsec11_0002_000`이며 파일에서 `0x79`부터 정확히 86바이트가 연속되고 직후 `05 05 05` separator가 있다.
- 번역본 `sangokushi2_translation.json`의 의미를 기준으로 슬롯에 맞게 압축한 런타임 문구는 `400년 번영을 누린 한 왕조에 / 쇠퇴의 조짐이 보였다. 장각이 / 이끈 황건군이 천하를 휩쓴다`이다. 가시 텍스트 84바이트 앞에 `<$1B>H` 2바이트를 붙여 정확히 86바이트로 맞췄고 패딩은 없다. 해당 3개 `translation_ko`도 이 최종 런타임 문구와 일치하도록 갱신했다.
- 시나리오용 신규 한글까지 포함해 현재 시험 별칭은 45글자(`0x889F..0x88CB`)를 사용하며 모두 원본 `code.bin`에서 연속 논리 ID `557..601`임을 정적 검증했다. 물리 셀은 `552..596`에 `logical-5`로 기록한다.
- 사용자 리빌드 방식에 맞춰 Patch 전체를 한 세트로 다시 생성했다. 현재 파일은 `ExeFS\code.bin`, `RomFS\Common\Font\font.g1t`, `RomFS\Message\msgsec01.dat`, `RomFS\Message\msgsec11.dat` 4개다.
- v9 정적 검증: 폰트 대상 셀 밖 alpha 변경 0 / 셀 readback mismatch 0 / 시나리오 슬롯 정확히 86바이트 / 직후 separator `050505` 보존 / `code.bin` 원본 bit-exact / report `pass: true`.
- v9 SHA-256: `font.g1t = 0F9A680854F6FD5242051C16EC4E38203062C36A6676606AF813F828C908205B`, `msgsec01.dat = 22EDF15393122594FDBEEA61918F0544603D44ED1F096E17F5D4EDDCDA233FF0`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`.

## v9 런타임 성공 확인 + 시나리오 1 시작 설정문 v10 확장 (2026-08-28)

- 사용자가 v9 Citra 화면에서 `400년 번영을 누린 한 왕조에 / 쇠퇴의 조짐이 보였다. 장각이 / 이끈 황건군이 천하를 휩쓴다`가 정상 한글로 출력되는 것을 확인했다. 따라서 **14×14 / 73열, logical ID → physical cell -5, Sam3 공통 13×13 무리샘플링 방식은 시나리오 본문에서도 런타임 검증 완료**로 본다.
- v9 화면에서 `쇠` 바로 왼쪽에 1px 세로 잔상이 보였다. 매핑상 `쇠`는 physical 580, 바로 이전 physical 579는 `에`이며 v9의 `에`는 셀 우측 x=13까지 도트를 사용한다. v10에서는 다른 글자는 그대로 두고 **`에`만 x=1→0으로 1px 왼쪽 이동**하여 physical 579의 우측 guard column x=13을 투명하게 만든다. 리샘플링/획 삭제는 하지 않는다.
- 사용자 추가 스크린샷과 `sangokushi2_translation.json`을 대조해 시나리오 1 시작 설정 구간을 `msgsec01.dat`에서 확인했다. v10 적용 문구: `설정 완료`, `O인 플레이로 진행할까요?`, `플레이 인원 설정`, `담당 군주 선택`, `난이도`, `게임 난이도를 선택합니다.`, `난이도가 높을수록 / 적 군주가 강해집니다`, `표시시간`, `표준 또는 정지를 선택합니다. / 표준은 메시지가 자동 진행됩니다. / 정지는 버튼을 누르면 진행됩니다.`.
- 해당 고정 오프셋: setup_done `0x1938`, player_confirm `0x1948`, player_count `0x1968`, ruler_select `0x1983`, difficulty_head `0x1A5B`, difficulty_prompt `0x1A6A`, difficulty_help `0x1A8C`, display_head `0x1AB8`, display_help `0x1AC9`. 원본 바이트를 각 위치에서 직접 assert한 뒤 같은 길이로만 치환한다.
- `難易度`의 다음 fragment 첫 `0x42`는 앞 fragment의 `0x81`과 합쳐 `。`를 만들던 분할 바이트다. v10은 앞 prompt를 한국식 `.`로 끝내므로, help 블록 첫 바이트를 공백으로 재구성하여 독립 `B`가 나오지 않도록 했다.
- `表示時間` 제목은 추출 JSON에서 `msgsec01_0069_001`의 끝 `0x1B` + `msgsec01_0070_000`의 `43 31`로 분할되어 있으므로 두 fragment를 합친 14바이트 슬롯을 `C9 + 표시시간 + C1`으로 정확히 치환한다. 이어지는 142바이트 설명 블록(`0070_001 + 0071 + 0072_000`)도 실제 파일에서 연속임을 확인 후 한글 도움말로 묶어 치환했다.
- 새 설정문을 포함한 한글 고유 글자는 89자이며 현재 별칭은 `0x889F..0x88F7`, 논리 ID `557..645`, 물리 셀 `552..640`을 사용한다. 전부 원본 `code.bin` 조회값이 기대 논리 ID와 일치함을 빌드 시 검증한다. 아직 `0x88` 행을 넘지 않는다.
- Sam3 신규 글리프 중 `세`, `때`는 공통 13×13 프레임을 벗어나는 14px 폭이므로 폰트 규칙을 깨는 예외 렌더링 대신 문구를 각각 `게임 난이도를 선택합니다.`, `정지는 버튼을 누르면 진행됩니다.`로 자연스럽게 조정했다.
- 버튼/선택지 이미지 자산은 사용자 포토샵 작업 대상이므로 v10에서 수정하지 않는다.
- v10 정적 검증: 대상 셀 밖 alpha 변경 0 / font roundtrip mismatch 0 / 모든 메뉴 고정 오프셋 원본 assert + 동일 길이 치환 / 기존 msgsec11 86바이트 및 separator 유지 / `code.bin` 원본 bit-exact / report `pass: true`.
- v10 SHA-256: `font.g1t = EC5E1C7A6C973B944F7F720A56D24C09B789A871B325EE2C12392132196F87E6`, `msgsec01.dat = EFA382ED7662430307C3966CAFBC0C3E5BA8B6943EF2D3A671191A886F195D31`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = AB7E06B261A742BC69DDCDBFEB827D944D3A6AE8D698F548A5642609A2D3BC98`.

## v10 런타임 잔상 보정 + code.bin 이벤트 대사 v11 (2026-08-28)

- 사용자가 v10 Citra에서 `쇠` 왼쪽 잔상이 완전히 사라졌음을 확인했다. 따라서 `에` physical 579의 우측 guard column을 비우는 국소 보정은 런타임 성공으로 확정한다.
- 같은 화면에서 `다` 바로 아래에 새 잔상이 생겼다. `다`는 physical 565이며 정확히 한 아틀라스 행 아래 `565+73=638`은 v10에서 신규 글자 `튼`이 들어간 셀이었다. 수직 인접 셀 샘플링으로 판단해 v11은 **logical slot 86 / physical 638을 완전 투명 guard 셀로 예약**하고 `튼` 이후 글리프를 한 슬롯씩 뒤로 이동한다.
- 지도 위 시나리오 1 대사가 `msgsec11.dat` 수정에도 일본어로 남은 원인을 원본 전체 바이트 검색으로 확인했다. 해당 문장들은 RomFS 메시지가 아니라 **`ExeFS\\code.bin` 하드코딩 CP932 문자열**이다. 확인 위치: 장각 설명문 `0x1D2C04` 64바이트, 하진 첫 명령 `0x55948` 45바이트, 인접 후속 명령 `0x55B14` 60바이트. 각 문자열 직후 NUL 종료를 검증하고 원본 바이트 길이 안에서만 치환한다.
- v11 적용 문구: `평원에 장각이란 자가 있었다 / 황제와 노자를 믿는 / 태평도의 교주였다`, `칙명이다! 각지에 퍼진 / 황건적을 소탕하라!`, `칙명대로 황건적을 소탕하라! / 조정에 맞서는 자는 / 용서 없다!`.
- v10의 `0x88` 행을 넘어서는 신규 한글 별칭은 바이트를 임의 증가시키지 않는다. 원본 `code.bin` JIS→논리셀 테이블에서 logical 557~2906에 실제로 존재하는 **2,350개 유효 JIS 별칭 목록**을 셀 순서대로 다시 사용한다. 따라서 `0x88FC` 다음은 실제 유효 코드 `0x8940`, `0x8941`…로 안전하게 이어진다.
- 신규 대사에 필요한 `와`는 Sam3에서 bbox `(1,1)-(15,14)` = **14×13**으로 공통 13×13 프레임보다 가로 1px 넓다. 열별 잉크량 측정 결과 오른쪽 끝열은 1픽셀뿐이므로, 전체 폰트나 기준선을 변경하지 않고 **`와`만 세로 13px은 그대로 유지하고 최우측 x=14 1열만 제거하여 13×13로 맞춘다**. 리샘플링/안티앨리어싱 없음. 폰트 교체는 현재 불필요하다.
- v11 한글 고유 글리프는 110자, 최고 사용 slot 110, 마지막 실제 유효 별칭 `0x8950`이다. physical=logical-5 방식으로 기록하되 slot 86은 guard로 비운다.
- `Extracted_Text\\sangokushi2_code_text.json`의 `code_001D2C04` 번역도 실제 적용 문구 `황제와 노자를 믿는`으로 동기화했다.
- v11 정적 검증: 폰트 대상/guard 셀 밖 alpha 변경 0 / roundtrip mismatch 0 / code.bin 3개 문자열 원본 위치·길이·NUL 종료 확인 / 모든 새 문자열 원본 슬롯 이하 / 기존 msgsec01·msgsec11 패치 유지 / report `pass: true`.
- v11 SHA-256: `font.g1t = 86EC4C20C87AA19F42C40F18E8BFD42ABD62640BFED7D2014751BA4CD96DC8D2`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = AA0A0114DBB158631F3B1120B16E89B15CCF5E60698EA54F3E155DD526D5BBA2`.

## v11 Citra 성공 확인 + 전체 글리프/32% 대량 삽입 v12 (2026-08-28)

- 사용자가 v11 Citra에서 장각 설명문 `평원에 장각이란 자가 있었다 / 황제와 노자를 믿는 / 태평도의 교주였다`와 하진 대사 `칙명이다! 각지에 퍼진 / 황건적을 소탕하라!`가 깨짐 없이 정상 출력되는 것을 확인했다. 폰트 품질도 만족스럽다고 확인했다. 따라서 **v11을 첫 실제 대사 런타임 성공 기준본**으로 고정한다.
- `Extracted_Text\sangokushi2_translation.json`의 실제 번역 대상은 4,360개이며 전부 번역 완료 상태다. 전체 번역문에서 필요한 고유 한글을 재집계한 결과 864자였으나, Sam3KRFont에 없는 유일한 글자 `슌`은 `甲相駿三国同盟` 문맥의 잘못된 표기 `고소슌`이어서 번역을 **`갑상준 삼국동맹`**으로 교정했다. 최종 필요한 한글은 **863자**, Sam3 누락 0자다. `text_review.html`의 동일 항목도 같이 교정했다.
- v12는 매 실행 시 `tools\build_sangokushi2_v11_code_dialogue.py`를 먼저 실행해 v11을 재생성한 뒤 확장한다. 따라서 다른 Agent가 Patch 상태를 몰라도 v11 기준을 자동 복구하고 시작할 수 있다.
- 기존 v11 한글 110자의 alias/slot/physical 위치는 **절대 재배치하지 않는다**. 신규 753자는 physical atlas row `11,13,15,...,37`처럼 한 행씩 띄운 후보 슬롯에 배치하여 한글 바로 아래에 또 다른 한글이 오는 수직 bleed를 줄인다. 중간 행은 stock 일본어 글리프를 그대로 유지한다.
- 전체 863자 Sam3 렌더 결과: 기존 v11 110자 + 신규 753자. 신규 753자 중 공통 13×13 무변형 684자, 가로 14→13 최근접 축소 45자, source frame 위치만 13×13로 보정 23자, 가로 15→13 최근접 축소 1자다. 높이는 모두 기존 13px 기준선을 유지한다. `font.g1t` 신규 셀 A4 round-trip mismatch 0.
- `Message\msgsecXX.dat`는 단순 offset 배열이 아니라 **혼합 헤더**임을 확정했다. 첫 word는 전체 헤더 word 수이고, 앞쪽의 EOF 내부 단조 증가 prefix만 실제 segment word-offset이다. 뒤쪽 값은 메타데이터다. 새 도구 `tools\sangokushi2_message_repacker.py`는 pointer prefix만 재계산하고 뒤 metadata word는 원본 그대로 보존한다. 번역을 0개 넣은 identity rebuild를 `msgsec00~21.dat` 22개 전부 수행해 **22/22 bit-exact 통과**했다.
- 첫 대량 삽입에서는 Citra 검증된 `msgsec01.dat`, `msgsec11.dat`는 v11 그대로 보호했다. 나머지 20개 DAT만 재패킹했다. 안전 게이트: 기술 데이터 제외 / 깨진 원문·PUA 제외 / 숨겨진 레이아웃 prefix 제외 / 원문·번역 `<$xx>` 제어코드 배열 동일 / 추정 최대 줄 폭이 원문 대비 **122% 이하(+1셀 허용)**.
- v12 실제 Message 적용: **1,412 / 4,360 = 32.385%**. 분류별: system 218, menu 296, battle 142, dialogue 756. skip 주요 원인: width gate 2,365 / bad source 176 / opaque segment 59 / control mismatch 22 / hidden layout prefix 18 / technical 3. 상세 ID와 파일별 적용/증가 크기는 `analysis\v12_bulk_report.json`에 저장했다.
- 파일별 대량 적용 수: msgsec00 125, 02 76, 03 17, 04 10, 05 12, 06 64, 07 107, 08 39, 09 39, 10 87, 12 16, 13 79, 14 101, 15 240, 16 38, 17 1, 18 93, 19 146, 20 97, 21 25. `msgsec01/11`은 v11 보호본.
- 무장 이름표의 실제 데이터는 `RomFS\Scenario\fix_data.bin` 9바이트 이름 필드임을 확인했다. v12에서 우선 적용: `0x6EA6 何進→하진`, `0x8212 張角→장각`, `0xBB4E 司馬徽→사마휘`. 이름 뒤 가나 독음 필드는 보존한다.
- v12 생성 도구: `tools\build_sangokushi2_v12_bulk.py`. 실행 결과 report `analysis\v12_bulk_report.json`, `pass: true`.
- v12 Patch는 사용자가 항상 전체 복사할 수 있도록 **25파일 한 세트**다: `ExeFS\code.bin`, `RomFS\Common\Font\font.g1t`, `RomFS\Scenario\fix_data.bin`, `RomFS\Message\msgsec00.dat`~`msgsec21.dat` 22개.
- v12 주요 SHA-256: `font.g1t = B9606F5F1721B0E082287C76D245193186F997E44BE47AE62CC64931DD992E08`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = AA0A0114DBB158631F3B1120B16E89B15CCF5E60698EA54F3E155DD526D5BBA2`, `fix_data.bin = 47CAC21C42D5ABD5BAD234D070C68C233634F6002144A1E31FB821194448B5F7`.
- v12 빌더를 완료 후 다시 한 번 처음부터 재실행했으며 **적용 수 1,412 / 글리프 863 / 주요 SHA-256이 모두 동일**하게 재생성되어 idempotent임을 확인했다. `py_compile`도 `sangokushi2_message_repacker.py`, `build_sangokushi2_v12_bulk.py`, `build_sangokushi2_v11_code_dialogue.py` 모두 통과했다.
- 22개 Patch Message DAT를 원본과 별도 재검증했다. 각 파일에서 원본 pointer prefix 뒤의 **metadata header word는 전부 bit-exact 동일**, 새 pointer prefix는 첫 값=전체 header word 수 / 단조 증가 / 전부 새 파일 내부 조건을 22/22 통과했다.
- **중요: v12의 863자 전체 원거리 alias 및 20개 DAT 가변 길이 재패킹은 정적 검증은 통과했지만 아직 사용자의 Citra 런타임 검증 전이다.** 다음 작업은 먼저 v12 ROM을 빌드해 여러 화면/대사/이름표에서 한글 출력, 줄 넘침, 텍스트 포인터 이상 여부를 확인해야 한다. 문제가 있으면 `analysis\v12_bulk_report.json`의 적용 ID를 기준으로 해당 파일/문장만 좁혀 롤백한다.

## v12 Citra donor 충돌 원인 확정 + safe alias v13 (2026-08-28)

- 사용자가 v12 Citra에서 위치/줄폭은 정상인데 일본어와 한글이 섞인 다수의 오표시를 확인했다. 대표적으로 원본 `移動` 버튼이 `당動`처럼 보였다. 패치 `msgsec15_0000_005`의 실제 바이트는 번역대로 `이동` alias 2개였으므로 Message 재패킹 오류가 아니었다.
- 원본 `移`의 Shift-JIS는 `88DA`, code.bin logical ID는 616, physical은 `616-5=611`이다. v11/v12에서 physical 611을 한글 `당`에 재사용했고 alias도 동일한 `88DA`였다. 따라서 아직 일본어로 남아 있던 원문 `移動`이 런타임에서 `당動`으로 보인 것이었다. **원인은 alias/셀 위치 오류가 아니라 미번역 일본어가 사용하는 donor 글리프를 한글로 덮어쓴 것**으로 확정한다.
- v12의 기존 donor 정책(일본어 사용 여부와 무관하게 원거리 셀을 한글에 배정)은 폐기한다. v13은 `msgsec01/11` 원본 전체, 나머지 Message 중 구조상 스킵되는 fragment, `sangokushi2_code_text.json`의 모든 하드코딩 문자열, Scenario 바이너리의 실제 byte pair를 donor 보호 목록으로 만든다.
- 원본-valid-JIS 2,350개 중 위 보호 목록에 등장하지 않는 safe donor는 **1,090개**, 필요한 한글은 **863개**, 여유 227개다. v13은 863자를 모두 이 safe donor에 새로 배정하며 `physical = logical - 5`로 기록한다. 보호 문자열과 donor 충돌은 빌드 시 **0건**을 assert한다.
- v13은 v11/v12의 기존 alias를 보존하지 않는다. `오리지널 모드`, 첫 화면/설정문, 첫 시나리오 3줄, v11 장각/하진 hardcoded 대사, fix_data 이름까지 **모두 새 safe alias map으로 원본에서 재인코딩**한다.
- v12의 폭 122% 게이트를 제거했다. `msgsec01.dat`, `msgsec11.dat`는 Citra 검증된 수동 레이아웃을 새 alias로 재생성해 보호하고, 나머지 20개 Message DAT는 혼합 헤더 재패커로 구조적 안전 항목을 전부 적용한다. v13 Message 적용은 **3,777 / 4,360 = 86.628%**다. skip: no_translation 460 / bad_source 176 / opaque 59 / control mismatch 22 / hidden layout 18 / technical 3.
- `msgsec15_0000`의 명령 메뉴 묶음은 구조 게이트를 전부 통과한다. `이동, 수송, 전쟁 준비, 전쟁, 징병, 모병, 훈련, 재편성, 동맹, 교환, 항복 권고` 등 사용자가 올린 명령 메뉴의 텍스트는 v13에서 실제 한국어 바이트로 재패킹된다. 대표 `이동`은 새 alias `88DF 8CD8`이며 원본 `移=88DA`는 donor에서 제외됐다.
- 스크린샷에서 원문 일본어로 남은 설명 중 `code.bin` 하드코딩을 추가 확인했다. v13 추가 패치: `張角は護符と霊水を用いて...`(0x1D26F8, 94B) → `장각은 부적과 영수를 써 / 병을 고치는 데 능했다 / 많은 백성이 장각에게 모였다`; `やがて信者たちは張角に...`(0x588C4, 57B) → `이윽고 신도들은 장각에게 / 악정에서 구원을 바라게 됐다`; `三國志２の / 遊び方を教えて進ぜよう` → `삼국지2의 / 플레이 방법을 알려주지`; `聞きますか？` → `들을까요?`. 기존 v11 hardcoded 3개까지 합쳐 code.bin 직접 패치는 현재 7개다.
- `fix_data.bin`은 Rebuild에서도 v12 해시가 실제 적용되어 있었지만 이전 화면의 `司馬徽`가 남았다. Rebuild 전체에서 원본 byte sequence `司馬徽`는 0건이었으므로 해당 이름은 save/cache 또는 별도 런타임 소스 가능성을 열어둔다. v13에서도 `하진/장각/사마휘` 이름 필드는 새 safe alias로 다시 생성하며 다음 Citra에서 재확인한다.
- v13 빌더: `tools\build_sangokushi2_v13_safe_alias.py`; report: `analysis\v13_safe_alias_report.json`; `py_compile` 및 빌드 PASS.
- v13 주요 SHA-256: `font.g1t = 348267033F90334A51C1B8059573CD178F16A3A5CA24E992D649890EEA10BFF0`, `msgsec01.dat = FA5E2A08B9761EA51666B51EE40E3BE1A97E10EF42F948D26D6D9616586FF938`, `msgsec11.dat = 107B460E664E1C23EDB38F97E33AFE52DC4A7174EB1468F40A43CF42DB8DFD7D`, `code.bin = FCB7455E111DFE39B1A82193A07A6553A80D299F014018045DA2F62430DBF0A0`, `fix_data.bin = 0C4A14A6FDAA5660A3F39EC8970A380DFADBB1110A3087319F43AD494AA1BBDB`.
- 다음 런타임 확인 우선순위: (1) `移動`이 더 이상 `당動`으로 나오지 않고 `이동`으로 표시되는지, (2) 명령 메뉴 전체 한글화, (3) 장각 설명 2페이지, (4) 사마휘 튜토리얼/이름표, (5) 신규 86% Message에서 줄 넘침/포인터 이상 여부.

## v13 런타임 회귀 확인 + v14 안정화 롤백 (2026-08-28)

- 사용자가 v13 Citra에서 **이전에 정상 출력되던 영역까지 다시 깨지는 회귀**를 확인했다. 금색 `오리지널 모드`, `『삼국지Ⅲ』를 기반으로 한`, 시나리오 첫 3줄, 군주 선택/설명 화면까지 다수 글자가 일본어/한자/엉뚱한 한글로 오표시됐다. 따라서 v13 safe-donor 전체 remap은 런타임 실패로 판정하고 배포 기준에서 폐기한다.
- v13 alias 자체를 Shift-JIS→JIS로 역변환해 원본 `code.bin` 조회표를 다시 읽었다. `오=899B` report logical 674 / actual 674, `삼=8A80` 834/834, `국=8A62` 805/805, `지=89B0` 695/695 등으로 **alias 생성 및 JIS→logical 조회는 정상**이었다. 실패점은 그 다음 단계다.
- 따라서 v11 Citra에서 직접 확인한 `physical = logical - 5`를 logical 557..667 밖 먼 범위까지 전역 적용한 것이 v13 회귀의 핵심 원인으로 좁혀진다. 현재는 **먼 logical ID에서 별도의 UV/physical 순열 또는 구간 규칙이 존재**한다고 취급한다.
- 원본 `font.g1t` physical 552..2901을 14×14로 전수 검사했으나 빈 셀은 0개였다. 단순 blank/guard 셀 누적 때문에 offset이 변하는 구조는 아니다. 별도 매핑을 찾기 전에는 먼 logical ID를 한글용으로 사용하지 않는다.
- 새 도구 `tools\build_sangokushi2_v14_stable_rollback.py`를 작성했다. 실행 시 Patch 25파일을 먼저 **전부 Original에서 재복사**한 뒤, 사용자가 실제 Citra에서 성공을 확인한 v11 빌더만 적용한다. 이로써 v12/v13 bulk Message/fix_data 잔재가 Patch 전체복사 방식에 섞이지 않는다.
- v14 Patch는 25파일 한 세트지만 수정 파일은 v11 검증 4개뿐이다: `font.g1t`, `msgsec01.dat`, `msgsec11.dat`, `code.bin`. `msgsec00/02..10/12..21`과 `fix_data.bin`은 Original bit-exact다.
- v14 빌드 `pass: true`, `py_compile` 통과. 주요 SHA-256은 v11 성공본과 정확히 동일: `font.g1t = 86EC4C20C87AA19F42C40F18E8BFD42ABD62640BFED7D2014751BA4CD96DC8D2`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = AA0A0114DBB158631F3B1120B16E89B15CCF5E60698EA54F3E155DD526D5BBA2`.
- 다음 확장 원칙: **v14/v11의 110자와 성공 화면은 절대 재배치하지 않는다.** 신규 글리프는 logical→physical 실제 매핑을 별도 검증한 구간만 추가한다. 번역률 확대보다 회귀 없는 런타임 검증을 우선한다.

## v14 안정본 기반 시나리오 1 대사 전용 v15 (2026-08-28)

- 사용자가 v14 Citra에서 v11의 정상 출력 상태로 복귀했음을 확인했다. 이후 작업은 **메뉴 / 대사 / 이름을 분리**하고 시나리오 1 대사부터 확장한다. v15에서는 메뉴 대량 번역과 `fix_data.bin` 이름표를 의도적으로 건드리지 않는다.
- v15 빌더 `tools\build_sangokushi2_v15_scenario1_dialogue.py`는 매 실행 시 먼저 v14를 재생성한다. 따라서 기존 110자, `msgsec01.dat`, `msgsec11.dat`, 기존 v11 장각/하진 대사 3개는 그대로 보존된다.
- 신규 대사에 필요한 한글은 **14자만** 추가한다: `써 병 고 쳤 백 성 복 양 량 허 창 식 숭 했`. 기존 110자 매핑은 변경하지 않는다.
- 신규 donor는 v11 다음 근거리 slot 중 실제 Message 원문, NUL-delimited `code.bin` 문자열, Scenario 문자열에 등장하지 않는 코드만 사용한다. 고정 slot: `111,113,114,115,116,122,124,125,127,128,129,130,134,135`. 최고 slot 135로 v13처럼 먼 영역을 사용하지 않는다. 이 구간의 `physical=logical-5`는 **정적 가설이며 이번 Citra에서 검증할 소규모 probe**로 취급한다.
- 신규 시나리오 1 code.bin 대사 4개: `0x1D26F8` → `장각은 영수를 써 / 병을 고쳤다 / 백성이 장각에게 모였다`; `0x5597C` → `장각은 평원에 있다 / 장보는 복양에 있다 / 장량은 허창에 있다`; `0x559B8` → `노식 황보숭 주준! / 황건적을 소탕하라!`; `0x55B54` → `황제가 하진을 대장군으로 임명했다.`. 원본 byte length와 직후 NUL은 유지한다.
- Sam3 신규 글리프는 v11과 같은 13×13 기준선을 사용한다. `써`만 bbox가 `(1,1)-(14,14)`라 source window를 1px 왼쪽으로 옮겨 획 손실 없이 13×13로 넣는다. 나머지 신규 글자는 기존 공통 프레임을 그대로 사용한다.
- v15 정적 검증 PASS: v14 font 대비 신규 14셀 밖 alpha 변경 0 / A4 round-trip mismatch 0 / `msgsec01.dat`, `msgsec11.dat` 해시는 v11과 동일 / `fix_data.bin` 및 나머지 20개 Message DAT는 Original bit-exact.
- v15 SHA-256: `font.g1t = CD07A27B9119E90368774F9BE4F5FEA1848ECABEC8616DE531B0A9868BBA1379`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = A3BFAEE9A692EE0F2928E75CFE195348FD39F309991EBD03535A90C7F71642AB`, `fix_data.bin = 2CB96EBB54BB47957EE3BB2AF4EF7653B590A77E44A3DCB5B7310A447DA179EC`.
- 다음 Citra 확인은 기존 v11 화면이 그대로 정상인지 먼저 보고, 신규 4대사만 확인한다. 신규 대사에서만 오표시가 난다면 v14로 즉시 되돌리고 slot 111~135의 logical→physical 대응만 재분석한다. 기존 화면까지 깨질 경우 v15 font diff 범위를 우선 점검한다.

## v15 런타임 경계 확인 + 시나리오 1 대사 정리 v16 (2026-08-28)

- v15 Citra에서 신규 한글 자체는 출력됐으나 일부 글자가 한자로 남았다. 확대 확인 결과 이는 14×14 셀 바깥 일본어 잔상이 아니라 **해당 alias가 원래 일본어 글리프를 그대로 읽은 것**이었다.
- 런타임 결과를 글자/slot로 대조하면 `써(slot111)`, `병(113)`, `고(114)`, `쳤(115)`, `백(116)`은 의도한 한글로 정상 출력됐다. 반면 첫 실패가 확인된 `성`은 slot122였고, `복/양/량/허/창`이 들어간 slot124/125/127/128/129도 원래 일본어 글리프가 출력됐다.
- 따라서 추가 검증 범위를 **slot116까지만** 인정한다. slot117 이후, 특히 Shift-JIS 유효 코드가 `8956` 뒤 `899B`로 점프하는 구간 이후는 별도 physical/UV 규칙이 있다고 보고 사용 금지한다. v15의 slot122+ probe는 실패 기록으로 남긴다.
- 새 빌더 `tools\build_sangokushi2_v16_dialogue_cleanup.py`를 추가했다. 매 실행 시 v14/v11 안정본부터 다시 생성한 뒤, Citra로 검증된 donor slot `111,113,114,115,116` **5개만** 사용한다. 신규 글자는 `써/병/고/쳤/많`이다. 기존 v11 110자는 그대로 고정한다.
- v16은 오표시를 피하기 위해 문장을 검증된 글자 집합에 맞게 다시 구성했다. `0x1D26F8`: `장각은 영수를 써 / 병을 고쳤다 / 많은 이가 장각에게 모였다`; `0x5597C`: `장각은 평원에 있다 / 황건군은 각지에 있다`; `0x559B8`: `황건적을 소탕하라!`; `0x55B54`: `황제는 하진을 대장군으로 임명.`.
- 추가로 새 글리프 없이 v11+5자만으로 가능한 시나리오1 설명 2개를 더 적용했다. `0x588C4`: `많은 이가 장각을 믿었다`; `0x1D1D24`: `황건군이 천하를 휩쓴다`. 따라서 v16 신규 `code.bin` 대사는 총 **6개**다.
- `Extracted_Text\sangokushi2_code_text.json`의 `code_001D26F8`, `code_001D1D24` 번역도 실제 런타임 적용 문구와 동기화했다.
- v16 정적 검증 PASS: 신규 5셀 밖 alpha 변경 0 / A4 round-trip mismatch 0 / 기존 `msgsec01.dat`, `msgsec11.dat`는 v11 해시 그대로 / `fix_data.bin`과 나머지 Message DAT는 Original bit-exact.
- v16 SHA-256: `font.g1t = F8ABB058B8C2E04A9FBD00CDA05468919811150EA1882900E05D2D4575A690E2`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `code.bin = 8A7CD434C19797ADEF0F7F963A9B308C37E9A1EA92DE19163EB391F93B4B1523`, `fix_data.bin = 2CB96EBB54BB47957EE3BB2AF4EF7653B590A77E44A3DCB5B7310A447DA179EC`.
- 다음 단계는 v16 Citra에서 한자 잔여가 사라지는지 확인하는 것이다. 성공 시 slot111~116 근거리 확장을 안정 범위로 고정하고, 시나리오1 전체 대사는 **기존 검증 문자만으로 먼저 적용 가능한 항목을 최대한 늘린 뒤**, 새 글자가 꼭 필요한 경우에는 slot117+ 실제 physical/UV 매핑을 별도 probe로 먼저 규명한다.

## v16 런타임 성공 + JIS 선형 physical 공식 확정 / 시나리오1·전쟁 메뉴 v17 (2026-08-28)

- 사용자가 v16 Citra에서 기존 화면과 v16 신규 시나리오1 대사가 **전부 정상 출력**됨을 확인했다. 따라서 v16을 현재 안정 fallback으로 승격한다.
- v15에서 slot116까지 정상, slot122 이후 stock 한자가 출력된 경계를 다시 분석했다. `build_codes()`의 유효 alias가 slot116 `8956(鰻)` 뒤 slot117 `899B(央)`로 크게 점프하며, 단순 logical 연속 번호와 실제 JIS 글리프 순서가 달라지는 것이 핵심이었다.
- Shift-JIS를 JIS 94×94 row/column으로 역산한 선형 index와 원본 14×14 atlas를 대조해 **`physical = JIS94_linear(alias) - 858`** 공식을 확정했다. 기존 성공 예: `889F(亜)` JIS index 1410→physical552, `8956(鰻)` 1526→668. 경계 뒤에는 `899B(央)` 1594→736, `89A0(旺)` 1599→741이다. v15는 이들을 잘못 669/674에 썼기 때문에 stock 한자가 출력됐다.
- 이 공식을 원본-valid-JIS 2,350개 전체에 적용한 결과 physical **552~3717**, 고유 셀 **2,350/2,350**, 중복 0으로 확인됐다. 또한 v11/v16의 모든 런타임 검증 row physical을 정확히 재현한다. 따라서 전역 `logical-5` 가설은 폐기하고 JIS 선형 공식을 새 권위 규칙으로 사용한다.
- 새 빌더 `tools\build_sangokushi2_v17_s1_war_menu.py`를 추가했다. 매 실행 시 먼저 v16을 재생성하고, 기존 한글 alias/셀은 하나도 이동하지 않는다. 신규 alias는 Original Message/code.bin/Scenario 실제 문자열에서 사용되지 않는 donor만 선택하며, 수직 bleed 방지를 위해 기존/신규 한글 physical의 ±73 셀을 피한다.
- v17 신규 한글은 **168자**이며 실제 신규 physical 셀은 743~1623 범위에서 168개 모두 고유하다. 신규 한글끼리 ±73 수직 인접 충돌은 0건, 신규 셀 밖 alpha 변경 0, A4 round-trip mismatch 0이다.
- 시나리오1 hardcoded `code.bin` 문자열을 **28개**로 확장했다. 범위는 장각의 봉기/영수 치료/태평도 선언, 황건적 확산, 장각·장보·장량 배치, 하진의 명령·임명·환관 숙청/암살, 황건적 진압 완료 등 현재 시나리오1 도입부에서 확인되는 장각·황건·하진 관련 문자열이다. 각 문자열은 원본 NUL-terminated 고정 바이트 슬롯을 넘지 않게 삽입한다.
- 전쟁 메뉴는 `msgsec15.dat`에서 **18개 fragment**를 선택 적용했다. 버튼: `이동 / 수송 / 전쟁 준비 / 전쟁 / 징병 / 모병 / 훈련 / 재편성 / 선박 건조`; 설명: 이동·수송·전쟁 준비·징병·모병·훈련·재편성·선박 건조 관련 검수 번역. 큰 `전쟁` 카테고리 아이콘은 이미지라 수정하지 않는다.
- `msgsec15.dat`는 혼합 헤더 재패커로 원본 20,373→20,613바이트(+240) 재구성했다. pointer prefix 207개를 갱신하고 metadata word 86개는 bit-exact 보존했다. opaque segment 3/16/205에는 replacement를 넣지 않았다.
- `fix_data.bin` 이름표는 이번에도 Original bit-exact로 유지한다. 메뉴/대사와 이름 작업은 계속 분리한다.
- v17 정적 검증 PASS. 주요 SHA-256: `font.g1t = B7E29E826774B1D1E0E880C7F1CE67BA921C8A9053E529C532C2ACC328A564C2`, `code.bin = 461D20C4BF1ACCA496154175EB8E0D75A2DA6D11730A230B6B5E85ADDF28A2C3`, `msgsec01.dat = 7B4570A0662028CCD0EB64EE5143D479ACDF72F7D0931A33517D35ECFD29E7FA`, `msgsec11.dat = 2F17CC139C71FE6F8D62A6956D7D49F7414C720997D0C8180B257B3647CC1CFB`, `msgsec15.dat = A237EB3D7E0A363DC37FF747472E662D11736FD6EF0241998AC485AFDB9726C1`, `fix_data.bin = 2CB96EBB54BB47957EE3BB2AF4EF7653B590A77E44A3DCB5B7310A447DA179EC`.
- 다음 Citra 우선 확인: (1) 기존 v16 성공 화면 유지, (2) 전쟁 메뉴 `이동`이 더 이상 `당動`이 아니라 `이동`인지, (3) 전쟁 메뉴 9개 버튼과 상단 이동 설명, (4) 시나리오1 신규 hardcoded 대사. v17에서 문제가 생기면 v16으로 즉시 fallback 가능하다.

## v17 메뉴 실소스 재추적 / 전쟁 메뉴 직접 패치 v18 (2026-08-28)

- 사용자가 v17 Citra에서 시나리오1 시작 대사는 정상 한글이지만 전쟁 메뉴 버튼과 상단 설명은 그대로 일본어이며 `移 動`의 `移`만 한글 donor에 오염돼 `당 動`처럼 보인다고 확인했다. Rebuild의 `font.g1t/code.bin/msgsec15.dat` 해시는 Patch v17과 완전히 같아 복사/리빌드 누락은 아니다.
- 원본 전체를 CP932 raw byte로 재검색한 결과 전쟁 메뉴 버튼의 실제 런타임 문자열 테이블을 `ExeFS/code.bin`에서 확정했다: `0x1CF59B 戦 争`, `0x1CF5A4 輸 送`, `0x1CF5AC 船建造`, `0x1CF5B4 再編成`, `0x1CF5BC 移 動`, `0x1CF5C4 徴 兵`, `0x1CF5CC 募 兵`, `0x1CF5D4 戦争準備`, `0x1CF5E0 訓 練`. v17의 `msgsec15_0000` 버튼 패치는 해당 화면의 실소스가 아니었다.
- 현재 선택된 `移動`의 상단 설명 `武将ｦ、自国ﾉ隣接都市ﾆ移動ｻｾﾏｽ。`은 `msgsec15_0133_002`가 아니라 **`RomFS/Message/msgsec10.dat`의 `msgsec10_0000_001`**임을 확정했다. `msgsec15_0133_002`는 별도 상세 튜토리얼 텍스트다.
- 새 빌더 `tools\build_sangokushi2_v18_war_menu_direct.py`를 추가했다. 매 실행 시 v17을 먼저 재생성한 뒤 실제 소스만 최소 패치한다. 버튼 9개는 `전쟁/수송/조선/재편성/이동/징병/모병/전쟁준비/훈련`으로 변경한다. `船建造`는 고정 6바이트 슬롯이라 `선박건조`(8B)를 넣지 않고 의미가 같은 2자 UI 표기 `조선`을 사용한다.
- code.bin 버튼 슬롯은 원본 C-string byte allocation을 유지하고, 짧아진 한국어 문자열 뒤를 NUL로 채워 다음 고정 문자열 위치를 움직이지 않는다. `0x1CF59B`의 선행 `0xFF` 비표시/padding 바이트는 보존한다. 9개 슬롯 모두 direct readback이 target alias bytes와 일치했다.
- 상단 이동 도움말은 `msgsec10_0000_001 -> 무장을 자국의 인접 도시로 이동시킵니다.` 한 항목만 먼저 적용했다. `msgsec10.dat`는 혼합 헤더 재패커로 6,884→6,892B(+8), pointer prefix 65개 재계산, metadata 71 word bit-exact 보존. 원본 fragment는 새 파일에서 0회, 한국어 encoded fragment는 1회 readback 확인했다.
- v18 정적 검증 PASS. SHA-256: `font.g1t = B7E29E826774B1D1E0E880C7F1CE67BA921C8A9053E529C532C2ACC328A564C2`(v17 동일), `code.bin = B8898940635FEF8889DDB11938E889E38C747F5B909E8F437B82632E2A6830A6`, `msgsec10.dat = C6CF2F24DC5ADD982DE0418E602005156EDC12370C6CF08C6AD7F84F29BF4C6D`, `msgsec15.dat = A237EB3D7E0A363DC37FF747472E662D11736FD6EF0241998AC485AFDB9726C1`.
- 다음 Citra는 **이 화면 하나만 먼저** 확인한다: 9개 버튼이 정확히 한글인지, 특히 `당 動 -> 이동`이 되었는지, 선택 상태의 상단 설명이 `무장을 자국의 인접 도시로 이동시킵니다.`로 바뀌었는지. 성공하면 `msgsec10_0000_002...`의 수송/전쟁준비/전쟁/징병/모병 등 상단 설명을 같은 방식으로 하나씩 확대한다.

## v18 런타임 확인 / 튜토리얼 실소스 복구 v19 (2026-08-28)

- 사용자가 v18 Citra에서 전쟁 메뉴 9개 버튼은 정상 한글 출력됨을 확인했다. 따라서 `code.bin 0x1CF59B..0x1CF5E0` 고정 테이블 패치는 런타임 성공으로 승격한다. 다만 이동 상단 설명은 `무장을 자국의 인접 도시로 이동시킵니다.`가 화면 폭보다 길어 왼쪽 `무장` 두 글자가 잘리고 `을 자국의...`부터 보였다.
- 같은 런타임에서 사마휘 튜토리얼 첫 대사 `三國志２の遊び方を教えて進ぜよう / 聞きますか？`는 일본어 그대로였고, `チュートリアル`의 `ゲームの説明` 페이지는 금색 제목이 `略`처럼 깨지고 본문이 사라졌다. 이는 v17이 `msgsec15_0000`을 전쟁 메뉴 실소스로 오판해 `msgsec15.dat`를 잘못 재패킹한 회귀다.
- 실제 사마휘 튜토리얼 첫 대사 소스는 `ExeFS/code.bin`: `0x5C040` 33B `三國志２の\n遊び方を教えて進ぜよう`, `0x5C068` 12B `聞きますか？`로 확정했다. v19에서 각각 `삼국지2의\n플레이 방법을 알려주지`, `들을까요?`로 고정 슬롯 이내 직접 치환한다.
- 튜토리얼 금색 제목은 `RomFS/Message/msgsec15.dat`의 `msgsec15_0000_002`이며 원문은 제어코드가 섞인 `ゲームの説明`이다. 게임 설명 본문은 `msgsec15_0025_002 ~ msgsec15_0035_000`의 연속 fragment로 확정했다.
- v19에서는 **v17의 잘못된 msgsec15 전쟁 메뉴 replacement를 전부 폐기**한다. `msgsec15.dat`를 Original에서 다시 재패킹한 뒤 `msgsec15_0000_002`와 게임 설명 본문 fragment 11개만 한글화한다. 본문은 `46개 도시를 지배해 대륙 통일을 목표로 합니다 / 군주가 사망했을 때 후계자로 삼을 수 있는 휘하 무장이 없으면 게임 오버입니다 / 여기서는 게임 설명을 볼 수 있습니다 / 아래 화면의 버튼으로 명령을 선택하면 위 화면에 설명이 표시됩니다` 의미를 원본 줄 구조에 맞춰 분할 삽입한다.
- 이동 도움말은 화면 clip 방지를 위해 `msgsec10_0000_001 -> 무장을 인접 도시로 이동시킵니다.`로 축약했다. `msgsec10.dat`는 Original에서 다시 재패킹하며 이 한 fragment만 수정한다.
- 신규 튜토리얼 문구에 필요한 한글은 기존 v17/v18 283자 외 **10자**: `알/려/륙/통/일/망/계/휘/볼/화`. JIS-linear physical 공식과 기존 ±73 수직 guard 규칙으로 safe donor에 추가했다. `화`만 Sam3 폭 14px라 기존 규칙대로 14→13px 가로 최소 보정, 나머지는 공통 13×13이다.
- 새 빌더 `tools\build_sangokushi2_v19_tutorial_restore.py`. 정적 검증 PASS. `msgsec10` 6,884→6,886B(+2), 원본 pointer prefix 65 이후 metadata bit-exact. `msgsec15` 20,373→20,411B(+38), 원본 pointer prefix 207 이후 metadata bit-exact. v19 SHA-256: `font.g1t = 040335256B2C4BC092313EA56EEDACE20F6E8C5664D87A591E61089AD37C472E`, `code.bin = ABBA1ECC6A1392A232279E0251136B6D492088E8499A1D7B600384B7CF2E13BF`, `msgsec10.dat = E894750829E68FA78B5BA9C7D00FC721D33287BF973EE3209F4F4D616DCF15B2`, `msgsec15.dat = 353B1F788A7E47C6FF2B93B1D7A4FEA6526A57DA820AD9D386656D2784411843`.
- 다음 Citra 확인: (1) 사마휘 대사 2개 한글, (2) 금색 `게임 설명`, (3) 게임 설명 6줄 본문 복구/한글, (4) 이동 상단 설명이 `무장을...`부터 잘리지 않고 표시되는지. 전쟁 메뉴 9개 버튼은 v18 성공 상태 유지가 필수다.

## v19 런타임 재분석 / fixed-position UI + 인사 메뉴 + 공통대사 v20 (2026-08-28)

- 사용자가 v19 Citra에서 `게임 설명`은 한글화되었지만 첫 `46개 도시...` 조각이 사라지고 다음 `대륙 통일...`부터 출력되며, 이동 상단 설명의 첫 `무`가 일본어 `ら/ﾗ`처럼 보인다고 확인했다. 동시에 `오`, `휘하` 아래에 1px 잔상이 관찰됐다.
- `무`는 alias `8BD7`, physical1172로 폰트 셀 자체를 재검증했고 정상 한글 도트가 들어 있다. 같은 v19의 `휘하 무장이 없으면`에서는 동일 `무`가 정상 출력되므로 glyph/alias 매핑 오류가 아니다. `msgsec10_0000_001`도 원본 fragment 시작 위치 자체는 맞았으나 v19에서 파일이 6,884→6,886B로 성장했다. `msgsec15` 역시 20,373→20,411B 성장 후 첫 fragment가 누락됐다. 두 현상을 함께 고려해 **msgsec10/msgsec15 해당 UI가 fragment 내부 위치를 별도로 고정 참조한다**고 판정하고 가변 길이 repack을 금지했다.
- v20 `msgsec10.dat`은 Original 6,884B를 그대로 사용해 `msgsec10_0000_001`을 `무장을 인접 도시로 이동합니다.` 30B + trailing space 1B로 원본 31B 슬롯에 in-place 적용한다. 인사 상단 설명 `msgsec10_0005_001`도 `도시에서 무장을 수색. 발견하면 재야 등록` 40B + space 4B로 원본 44B 슬롯에 in-place 적용한다. 최종 파일 크기 6,884B로 Original과 정확히 동일하다.
- v20 `msgsec15.dat`도 Original 20,373B를 그대로 사용한다. `msgsec15_0000_002` 금색 `게임 설명`과 `msgsec15_0025_002~0035_000` 게임 설명 본문만 각 원본 fragment 용량 안에서 fixed-position으로 다시 분할했다. 최종 파일 크기 20,373B, 모든 header/pointer/fragment start가 Original과 동일하다.
- 줄 길이 정책을 런타임 기준으로 추가했다. 상단 스크롤 도움말은 최대 18 fullwidth cell, 튜토리얼/일반 대사는 한 줄 최대 20 fullwidth cell을 권장한다. ASCII는 약 0.5 cell로 계산하며 초과 시 자동 줄바꿈에 의존하지 않고 문장을 압축하거나 `<$0A>`를 명시한다.
- 1px 잔상은 atlas 수직 bleed로 확인했다. `오` physical566 아래 physical639, `하` 594 아래667, `휘` 1681 아래1754가 대응한다. v20은 아래 세 셀의 기존 글리프를 셀 내부에서 1px 아래로 이동시켜 y=0을 투명 guard row로 만들며 13px 글리프 도트는 보존한다.
- 인사 메뉴 실제 버튼 테이블을 `code.bin 0x1CF5E8..0x1CF620`에서 확정하고 `위임/등용/시혜/임명/해고/수색/몰수/포상` 8개를 원본 고정 C-string 슬롯 안에 직접 패치했다. 전쟁 메뉴 9개 직접 패치와 사마휘 code.bin 튜토리얼 대사는 유지한다.
- 공통 대사는 `msgsec07.dat`의 명령/이벤트 대화군을 대상으로 했다. challenge/goal 성격의 parent 0~17, opaque segment, 제어코드 불일치, 한 줄 20 cell 초과 그룹은 제외하고 complete parent group만 선택했다. mixed-header repacker로 **44 fragment / 실제 표시 63줄**을 적용해 사용자의 최소 50줄 요청을 충족했다. 상세 ID는 `analysis/v20_fixed_ui_common_dialogue_report.json`이 권위다.
- v20 추가 문구에 필요한 신규 한글은 **43자**다. Original Message/code.bin/Scenario에서 실제 사용 중인 alias는 donor에서 제외하고 JIS-linear physical 공식, 기존 셀 및 ±73 guard 충돌 회피 규칙을 사용했다. A4 재디코딩 round-trip 검증 통과.
- 새 빌더 `tools\build_sangokushi2_v20_fixed_ui_common_dialogue.py`. 두 번 연속 실행하여 동일 해시가 재생성되었다. 정적 검증 PASS. SHA-256: `font.g1t = 83D3091B3BCC019E19EA51FFBD3A949360101766B3F138209917D07C1B03D708`, `code.bin = 654E71C02A307FF7CD6E273EF3AF2A4614BC59AC835850C38BB8907C511D6C8A`, `msgsec07.dat = 6ED6204649B8C24C910E750BF14B32F12E0740B5DE3D9DABBD37114344333E5B`, `msgsec10.dat = 03884BD713DB510AEA4D83C9B9208E59ED835D440C88A80FFAC1526A20F3332E`, `msgsec15.dat = BD61AC64925BDDA9D707E4DEC127A9D9D8B6E6A8ECFA2772E86709DAE0AB074F`.
- 현재 `Sangokushi 2 Patch`는 25파일 v20 세트이며, 비교 시 `Sangokushi 2 Rebuild`는 아직 v19 해시다. 사용자는 Patch 전체를 Rebuild에 다시 덮어쓴 후 리빌드해야 한다.

## v20 Citra 중간 확인 / 메뉴·군사 조언 확장 v21 (2026-08-28)

- 사용자가 v20 Citra에서 인사 버튼 8개가 정상 한글로 출력되는 것을 확인했다. 수색 상단 설명은 한글이 정상이나 `재야 등록` 뒤 원본 독립 `。` fragment가 떨어져 표시됐다.
- v21은 `msgsec10_0005_001 + msgsec10_0006_000`을 fixed-position 논리 문자열로 처리해 `무장을 수색해 재야 등록합니다.`로 바꾸고 일본식 마침표 `81 42`가 남지 않게 했다.
- `msgsec10.dat`에서 인사 8개·외교 7개·정보 7개·개발 4개·계략 5개, 총 **31개 상단 도움말**을 원본 raw slot 안에서 한글화했다. `msgsec10.dat`는 Original/v21 모두 **6,884B**이며 pointer/header 재패킹은 하지 않는다.
- 실제 `code.bin` 고정 문자열을 직접 수정해 정보 7개, 외교 7개, 개발 4개, 계략 5개 버튼을 한글화했다. 추가로 `軍師助言/軍 師/担当軍師`도 `군사조언/군사/담당군사`로 수정했다. 좌측 큰 카테고리 아이콘은 이미지이므로 건드리지 않았다.
- 군사 조언/판단 문자열로 확인된 `code.bin 0x1D1294~0x1D1650` 구간 중 **24개**를 고정 슬롯 길이 안에서 한글화했다. 정보 수집 기대치, 전력 판단, 병량 부족, 동맹 상태, 계략 성공 가능성/중지 권고 등이 포함된다.
- `msgsec07.dat` 공통 명령/이벤트 대사는 v20과 같은 complete-parent/control/20-cell 게이트를 유지하면서 **85 fragment / 121 표시줄**까지 확대했다. v20 대비 **추가 58줄**이다.
- v21 신규 한글은 **60자**만 추가했고 기존 검증 글리프는 재배치하지 않았다. JIS 94×94 linear physical 공식과 live-source donor 보호, ±73 충돌 회피를 유지했다. v20 bleed guard physical 639/667/1754의 top row도 모두 투명 상태를 재검증했다.
- 새 빌더: `tools\build_sangokushi2_v21_command_help_advice.py`; report: `analysis\v21_command_help_advice_report.json`. `py_compile` 통과 후 두 번 연속 재생성해 동일 해시가 나왔다.
- Patch는 계속 **25파일 전체 세트**다. `msgsec15.dat`는 v20의 20,373B/해시를 그대로 유지한다.
- v21 SHA-256: `font.g1t = 9DFF0741C66C135396EBAA7BE3C780A8BD93FBAB25D1A75C9F8820CE6DE7ED34`, `code.bin = BD2EE4A4ED7BE6B4CF119ED5F815AB6247F67FA359259303F5B9C90D294223CD`, `msgsec07.dat = E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat = E9BB16DF6168E214FEA8A3AD0CD34BDC2307930C25121D2B5C14A3DE07BB69F0`, `msgsec15.dat = BD61AC64925BDDA9D707E4DEC127A9D9D8B6E6A8ECFA2772E86709DAE0AB074F`.
- 다음 Citra 점검: 수색 마침표, 인사 설명 8개, 정보 7개, 외교 7개, 개발 4개, 계략 5개의 버튼/설명, 실제 플레이 중 군사 조언 24개와 msgsec07 신규 +58줄의 깨짐·줄넘침·크래시 여부를 확인한다.

## v21 Citra 성공 확인 / 튜토리얼·군사·상인·특별 v22 (2026-08-28)

- 사용자가 v21 Citra에서 인사/정보/외교/개발/계략 등 기존 추가 수정이 정상임을 확인했다. 따라서 v21의 `code.bin` 고정 C-string 직접 패치 + `msgsec10.dat` fixed-position in-place 도움말 방식은 성공 방식으로 유지한다.
- 튜토리얼 `게임 설명`은 v21에서 한글은 정상이나 fragment별 분할 때문에 줄바꿈이 부자연스러웠다. 일본어 원본 화면을 기준으로 **3줄 + 빈 줄 + 3줄**을 새 표준으로 정했다. 한 줄은 최대 20 fullwidth cell이며 자동 줄바꿈에 의존하지 않고 `<$0A>`를 명시한다.
- v22 게임 설명 본문은 `msgsec15_0025_002~0035_000`의 원본 11개 fragment를 각각 고정 길이로 유지하면서 재배치했다. 연속 191B를 한 번에 덮는 방식은 6개 fragment 시작이 한글 2바이트 중간에 걸리는 것을 검출해 폐기했다. 최종 줄 폭은 `[20, 20, 8, 빈줄, 14, 14, 7]` cell이다. 문구: `４６개 도시를 지배해 대륙을 통일합니다. / 군주 사망하면 후계자가 될 무장이 없으면 / 게임오버입니다. / (빈 줄) / 게임 설명을 볼 수 있습니다. / 아래 화면에서 명령을 고르면 / 위 화면에 표시`. 모든 fragment 시작은 완전한 글자/제어코드 경계다.
- `msgsec15`의 튜토리얼 하위 메뉴(일반 명령·전쟁 규칙·전투 기술 포함)를 **100 single slot + 15 split-SJIS group** fixed-position으로 한글화했다. `상업투자`, `적중작적`, `임시징수`, `전쟁규칙`, `기마돌격`, `일제사격`, `대기무장`, `대결`처럼 멀티바이트가 parent 경계에서 갈라진 항목은 인접 fragment를 한 묶음으로 처리한다.
- 튜토리얼 전용 `施し`는 원본 슬롯이 3B라 완전한 `시혜` 4B가 들어가지 않는다. separator와 다음 fragment를 지키기 위해 목록 표시만 `혜`로 축약했고, 메인 인사 메뉴 버튼은 기존의 완전한 `시혜`를 유지한다. `msgsec15_0024_004 + 0025_000`의 분할 항목도 4B 안에 맞는 `대결`로 처리했다.
- 군사 상단 설명은 이동 외 **9개 소스 그룹**을 추가 한글화했다: 수송 / 전쟁준비 / 전쟁 / 징병 / 모병 2경로 / 훈련 / 재편성 / 조선. 모두 상단 도움말 18-cell 규칙과 원본 byte slot을 동시에 만족하도록 압축했다.
- 상인 메뉴 실제 버튼은 `code.bin`의 `0x1CF6F0 병량매각`, `0x1CF71C 병량구입`, `0x1CF708 노구입`, `0x1CF6FC 강노구입`, `0x1CF710 군마구입`이며 v22에서 `병량매각/병량매입/노구입/강노구입/군마구입`으로 직접 수정했다. 상단 설명은 `msgsec10_0020_002~0021_000`을 fixed-position으로 `병량을 팝니다./삽니다.` 계열로 적용했다.
- 특별 메뉴 실제 버튼은 `0x1CF728 방랑`, `0x1CF73C 치료`, `0x1CF744 임시징수`, `0x1CF730 세율변경`이며 모두 한글화했다. 상단 설명은 `msgsec10_0021_001~0022_001` fixed-position으로 적용했다.
- v22에서도 `msgsec10.dat = 6,884B`, `msgsec15.dat = 20,373B`로 Original과 동일하며 header/pointer/fragment start를 움직이지 않는다. v20 잔상 guard physical 639/667/1754 top row도 0픽셀로 재검증했다.
- v22 신규 한글은 **23자**만 추가했으며 기존 v21까지의 alias/physical은 재배치하지 않는다. JIS94 linear physical 공식, live-source donor 보호, ±73 수직 충돌 회피 규칙을 그대로 유지한다.
- 빌더: `tools\build_sangokushi2_v22_tutorial_military_shop_special.py`; report: `analysis\v22_tutorial_military_shop_special_report.json`. 두 번 연속 빌드에서 동일 해시가 재생성되었고 Patch는 계속 25파일 세트다.
- v22 SHA-256: `font.g1t = D78AB0C6F508CD985B6EBC22FDC81976D9BE353F5CB5922D5A49EF83DE535668`, `code.bin = 9595F95E92DB35E4FBD76DC4A646DF8606C2AA8A8BBC63F20952FF71DECD3B23`, `msgsec07.dat = E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat = 13C116221AB8FAD51591DC76A5C8DC027065A0E4DA740F726B93E034B6ABD176`, `msgsec15.dat = 46A8F9C6982533FD405FE343359FEF928990A85A59E2DD995B723158616DF5EF`.
- 다음 Citra 점검 우선순위: (1) 게임 설명 3+빈줄+3줄 정렬, (2) 튜토리얼 일반 명령 하위 메뉴 한글, (3) 군사 9개 상단 설명, (4) 상인 5버튼+설명, (5) 특별 4버튼+설명, (6) v21 성공 화면의 회귀 없음.

## v22 런타임 회귀 수정 / 튜토리얼 설명·전투 버튼 v23 (2026-08-28)

- 사용자 Citra 화면에서 게임 설명 두 번째 줄의 마지막 `면`이 단독 줄로 자동 개행되고, 마지막 문장이 `위 화면에 표시`에서 끝나는 문제를 확인했다. 정적 20-cell PASS보다 실화면을 우선해 v22 배치를 회귀 사례로 기록했다.
- 게임 설명은 `군주 사망하면 후계자가 될 무장이 없으` / `면 게임 오버가 됩니다.`로 명시 분리했다. 하단은 `게임 설명을 볼 수 있습니다.` / `아래서 명령 선택.` / `위 화면에 설명이 표시됩니다.`로 완결했다. 줄 폭은 `[20,19,11,0,14,9,14]`이며 11개 원본 fragment 시작과 바이트 길이를 유지한다.
- 스크린샷에서 확인된 `전략` 설명, `전략 커맨드` 설명, `이동` 설명 1/4~4/4를 `msgsec15.dat` 원본 슬롯에 fixed-position으로 한글화했다. fragment 경계에서 한글 2바이트를 분할하지 않으며, 이동 2/4의 1바이트 여유는 다음 줄 들여쓰기가 되지 않도록 줄바꿈 직전에 배치한다.
- `code.bin` 고정 문자열에서 전투 메인 4개(`퇴각/출진/공격/계략`), 공격 6개(`일반/일제/기습/궁시/화시/돌격`), 계략 6개(`화계/복병/설득/상잔/위령/소화`), 퇴각 2개(`전군퇴각/대기무장`)를 한글화했다. 총 18개 모두 원본 C-string과 오프셋·슬롯 길이를 대조했고 성장 0이다.
- 신규 글리프는 `섭/확/깁/곳/킨/철/든` 7자만 추가했다. 기존 alias/physical은 재배치하지 않았고 JIS 94×94 linear physical 공식, live-source donor 보호, ±73 guard 규칙을 유지한다.
- 빌더: `tools\build_sangokushi2_v23_battle_tutorial_buttons.py`; report: `analysis\v23_battle_tutorial_buttons_report.json`. 두 번 연속 처음부터 재생성해 모든 핵심 해시가 동일했다. Patch는 25파일 전체 세트, `msgsec15.dat`는 Original과 같은 20,373B다. `Backup`, `Rebuild`, Original은 수정하지 않았다.
- v23 SHA-256: `font.g1t = 9ADD59DA70ED0996EF057EE9BF31A1CCE386887CC124EDE09E4A46CBB6FF2163`, `code.bin = D18A83A9F4020EF11689215C04BF03DA2F99BB5EF2066FF6CB193610F86982C2`, `msgsec07.dat = E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat = 13C116221AB8FAD51591DC76A5C8DC027065A0E4DA740F726B93E034B6ABD176`, `msgsec15.dat = 0CB27210923E8F864F905B2C21C7E9A1ED0CB4091FFAF7177A56EA441832F4E4`.
- 다음 Citra 점검: 게임 설명 7줄 → 전략 설명 → 전략 커맨드 → 이동 1/4~4/4 → 전투 메인 → 공격 → 계략 → 퇴각 순서. 정적 PASS는 런타임 성공 선언이 아니다.

## v23 누락 튜토리얼·동적 명령문 v24 (2026-08-28)

- 사용자 v23 Citra 화면에서 `이동 1/1`, `전쟁규칙 1/1`, `출진 1/1`, `위임 1/1` 본문이 일본어로 남은 것을 확인했다. 네 본문은 모두 `Extracted_Text\text_review.html`의 `autoTranslations`와 최종 번역 JSON에 이미 한국어가 있었지만 v23 실제 삽입 목록에서 빠져 있었다.
- 실소스는 각각 `msgsec15_0055_001~0057_000`, `0035_001~0045`, `0132_002`, `0132_003`이다. 화면 폭과 원본 고정 용량에 맞게 뜻을 보존해 압축했고, 모든 fragment 경계를 완전한 글자 단위로 맞췄다. 비가시 정렬 공백은 줄바꿈 직전에만 두며 한글 단어 중간 padding은 0건이다.
- 지도 상단 `何進様、洛陽にご命令を`는 `msgsec05_0008_002`다. 원본 시작 `02 01 C8` 전체가 군주명을 출력하는 동적 토큰이며 `%s`는 도시명 자리표시자임을 반복 패턴과 Citra 화면으로 확인했다. `02 01 C8 + 님,%s에 명령을`을 원본 17B 슬롯에 정확히 적용해 `[군주명]님,[도시명]에 명령을` 구조를 유지한다.
- HTML의 기존 AI 번역 `<$02><$01>네님, %s에게 명령을`은 C8 파라미터를 `네`로 오인하고 도시에 `에게`를 쓴 오류였다. `translationCorrections`에 `<$02><$01>님,%s에 명령을`을 추가하고 `sangokushi2_translation.json`을 다시 내보냈다. `%s` 불일치 0, 미번역 필수 행 0, 일본어 가나 잔존 0이다.
- 신규 글리프는 `침/밤/루/턴/옵/릅/컴/퓨/터` 9자다. 기존 alias/physical은 재배치하지 않았고 JIS-linear physical 공식, live-source 보호, ±73 guard를 유지한다.
- 빌더: `tools\build_sangokushi2_v24_tutorial_dynamic_prompt.py`; report: `analysis\v24_tutorial_dynamic_prompt_report.json`. 두 번 연속 재생성해 핵심 해시가 모두 동일했다. Patch는 25파일, `fix_data.bin`은 Original과 동일하다.
- v24 SHA-256: `font.g1t = 931BDAFB10BDB8836838EB780016EC6568B5DC0835291B87F23B5406810B8331`, `code.bin = D18A83A9F4020EF11689215C04BF03DA2F99BB5EF2066FF6CB193610F86982C2`, `msgsec05.dat = 1910DD23A09E871B6F20C905CF0B7A86E600217A61FC5F0CA638BB7AE37BEFE3`, `msgsec07.dat = E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat = 13C116221AB8FAD51591DC76A5C8DC027065A0E4DA740F726B93E034B6ABD176`, `msgsec15.dat = B5715A1016F33316219A1CFE6FCA6796DE636D13AEB94D2B13C6190036C2DEC6`.
- 다음 Citra 점검은 위 네 튜토리얼 본문과 동적 군주/도시 문구만 우선 확인한다. 정적 PASS는 런타임 성공 선언이 아니다.

## v24 설명 페이지 정리 v25 (2026-08-28)

- 사용자 Citra 확인을 바탕으로 게임 설명 하단을 `아래에서 게임 설명 버튼을 누르시면 / 위 화면에 설명이 표시 됩니다.`로 다듬고, 전략커맨드의 `특별: 세율 변경 등`을 `상인` 바로 다음 줄로 이동했다. `msgsec15`는 계속 20,373B fixed-position이다.
- HTML/최종 번역 JSON의 의미를 기준으로 `등용 1/9~9/9`, `시혜 1/1`, `특산 1/1`, `매복의독 1/4~4/4` 총 15페이지를 원본 슬롯 용량에 맞게 압축 한글화했다.
- `施ｼ` 3B와 `埋伏ﾉ毒` 7B는 정상 2바이트 한글로 각각 1바이트씩 부족해 v25에서 ASCII `Y/X`를 단일 글리프 별칭으로 쓰는 실험을 적용했다. 정적 lookup readback은 통과했으나 이후 Citra에서 `매복의X`가 그대로 출력되어 **ASCII는 해당 렌더 경로에서 lookup table을 거치지 않는 실패 방식**으로 판정한다.
- 빌더: `tools\build_sangokushi2_v25_tutorial_description_cleanup.py`; report: `analysis\v25_tutorial_description_cleanup_report.json`.
- v25 SHA-256: `font.g1t = CA10D9E4ED9EFE4A54B08955462815A18B8D9C2B5CDED6317ACDB57FF2C97D84`, `code.bin = D814B4C75AC713DE55139F5DFA9A342B4F1D1DF78F46258FDED0DAC1CF24A7C4`, `msgsec15.dat = 384B444C30204D1B123392731A5EF0512CF001427848A22687F43FCD364E88C8`.

## v25 런타임 피드백 / 전투기술·추가 설명 v26 (2026-08-28)

- Citra에서 `통상`의 `통` 주변 1px 잔상, 하단 전투 기술 목록의 `不意打ち/牽制/一騎討ち` 일본어 잔존, `매복의X` 실패를 확인했다. 또한 사용자가 `수송`, `전쟁준비`, `위서의심`, `상인` 설명 전체의 한글화를 요청했다.
- `통`은 alias `8E8F`, physical1664 자체가 아니라 바로 아래 atlas 셀 physical1737의 top scanline 6픽셀이 샘플링되는 v20과 동일한 수직 bleed 유형이었다. physical1737의 bottom row가 비어 있음을 확인한 뒤 셀 전체를 1px 아래로 이동해 top 6→0픽셀, 전체 ink 132→132로 보존했다. `통` 자체 글리프/alias는 재배치하지 않았다.
- 하단 전투 기술의 실제 소스는 별도 `code.bin` C-string 테이블 `0x1CEF64~0x1CEFA8`이었다. `기마돌격/견제/단기돌진/흘리기/튕겨내기/일기토/기습` 7개를 원본 슬롯 안에서 직접 한글화했다. v23의 일반 전투 메뉴 테이블과는 다른 소스다.
- `매복무장등용` 같은 긴 제목이 가능한데 `매복의독`이 안 됐던 이유는 **글자 수가 아니라 항목별 원본 byte slot 크기 차이**다. `埋伏ﾉ毒`은 정확히 7B라 보통 한글 4자(8B)가 1B 초과한다. `施ｼ`도 3B라 `시혜` 4B가 1B 초과한다.
- v25의 ASCII X/Y 방식은 폐기했다. v26은 실제 single-byte glyph lookup을 사용하는 CP932 halfwidth `0xA2/0xA3`을 각각 `독/혜`에 연결한다. 이 두 바이트는 Original 전체 Message에서 각각 1개 live occurrence만 있음을 확인했으며, 그 원래 위치 `msgsec19_0039_000`, `msgsec13_0094_000`은 같은 길이의 ASCII `[/]`로 보상 치환한다. 따라서 `매복의독`은 `2+2+2+1=7B`, `시혜`는 `2+1=3B`로 원본 슬롯을 정확히 유지한다. 이 새 halfwidth lookup 방식은 Citra 확인 전까지 런타임 시험 상태다.
- HTML/최종 번역 JSON을 의미 기준으로 삼아 `수송 1/4~4/4`, `전쟁준비 1/10~10/10`, `위서의심 1/4~4/4`, `상인 1/4~4/4` 총 **22페이지**를 추가 한글화했다. 모두 `v23.patch_lines_group` 기반 fixed-position이며 한 줄 20 cell 이하, 원본 writable byte 용량 이하를 검증했다.
- v26 신규 한글은 17자만 safe donor에 추가했다. 기존 검증 alias/physical은 이동하지 않았고 physical1737은 신규 donor에서 예약 제외했다.
- 빌더: `tools\build_sangokushi2_v26_tutorial_battle_more_help.py`; report: `analysis\v26_tutorial_battle_more_help_report.json`. `py_compile` 통과 후 두 번 연속 처음부터 재생성해 동일 해시를 확인했다. `msgsec15.dat`는 Original과 같은 **20,373B**, `fix_data.bin`은 Original bit-exact다.
- v26 SHA-256: `font.g1t = 5AB08A26CD7609873BF2031B023ED37C473E591530417F5A659168D936C43981`, `code.bin = B9043DD97834F0325420216C7E732670DD0CC46C7CA13E959017B26405AA387F`, `msgsec05.dat = 1910DD23A09E871B6F20C905CF0B7A86E600217A61FC5F0CA638BB7AE37BEFE3`, `msgsec07.dat = E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat = 13C116221AB8FAD51591DC76A5C8DC027065A0E4DA740F726B93E034B6ABD176`, `msgsec13.dat = 12B94BC4EB1E0C1AEB6C1511DF1454A0B1810249B8FD1B4200929B7D28C74D12`, `msgsec15.dat = 5107CB8F409DA1D93E272782E74674E9F9F7FD50B1577F90270ECF2892D9EEDC`, `msgsec19.dat = B6F825B8701F37DF27D79244FD2AC224CD36AADE219FAF2A76EBF2012E9394D9`.
- 다음 Citra 중간점검: (1) `통상`의 잔상 제거, (2) 하단 `기마돌격/견제/단기돌진/흘리기/튕겨내기/일기토/기습`, (3) `매복의독`과 `시혜` 완전 표기, (4) 수송 4페이지, (5) 전쟁준비 10페이지, (6) 위서의심 4페이지, (7) 상인 4페이지. 정적 PASS는 런타임 성공 선언이 아니다.

## v26~v27 Citra 회귀 / 실제 런타임 소스 v28 (2026-08-29)

- 사용자 Citra에서 v27의 `매복의독`/`시혜`는 **정상 완전 표기 성공**을 확인했다. 성공 방식은 원본 fragment 뒤 `05 05 05` separator에서 정확히 1바이트를 빌려 일반 2바이트 한글을 넣고 separator를 `05 05`로 남기는 방법이다. 전체 `msgsec15.dat` 크기와 다음 fragment 시작 위치는 그대로 유지된다. 앞으로 1바이트 부족 제목은 먼저 이 방식을 검토하며, v25 ASCII alias와 v26 halfwidth alias 방식은 사용하지 않는다.
- v27의 `오` 3px 하향 이동은 실패했다. Sam3 원본 `오` bbox는 `(2,2)-(15,11)`로 13x9이며 주변 일반 한글보다 짧지만 중심 y는 비슷하다. 단순 하향은 좌하단 쏠림을 만들었다. v28은 원본 ink만 `12x13`으로 nearest 확대해 셀 `(2,0)`에 배치한다. 최종 physical566 bbox는 `[2,0,14,13]`이며 Citra 재확인이 필요하다.
- `通常`은 의미 임의치환 없이 **`통상`**으로 복원했다. v27의 `일반`은 폐기한다.
- 튜토리얼 공격 설명이 일본어로 남은 원인은 `msgsec10.dat`에 동일 도움말이 두 벌 있기 때문이다. 예: `隣接ｽﾙ敵ﾉ部隊ｦ攻撃ｼﾏｽ。`가 `0x0FAF`와 `0x1448`에 모두 존재하며, Citra 튜토리얼은 앞쪽 `0x0FAF` 블록을 읽는다. v27은 뒤쪽만 수정했다. v28은 실제 튜토리얼 블록 `0x0FAF~0x12FE`의 통상/일제/기습/궁시/화시/돌격/일기토/화계/복병/설득/상잔/위령/소화 **13개 도움말**을 직접 fixed-position으로 한글화했다.
- 하단 버튼도 `msgsec15` 제목 목록만으로는 부족하다. 실제 런타임에는 `code.bin` 고정 슬롯 테이블 `0x1CEFxx`와 `0x1CF8xx`가 별도로 존재한다. v28은 공격/전투기술/계략 계열 **44개 슬롯**을 원본 stride 안에서 한글화했다. `통상`, `궁시`, `화시`, `돌격`, `견제`, `일기토`, `위협사격`, `일제사격`, `문사격`, `소화`, `화계`, `복병`, `위령`, `상잔`, `논파` 등이 포함된다. `성벽넘기`는 code C-string 슬롯이 8B total이라 NUL 포함 시 8B 한글을 넣을 수 없어 하단 버튼만 `성벽넘`으로 축약하고, msgsec15 제목은 separator-borrow 방식의 완전한 `성벽넘기`를 유지한다.
- 빌더: `tools\build_sangokushi2_v28_tutorial_battle_runtime_sources.py`; report: `analysis\v28_tutorial_battle_runtime_sources_report.json`. 두 번 연속 처음부터 재생성해 동일 해시 확인. v28 SHA-256: `font.g1t = 7202B2C37EA2194FAA2F4D12F08FF80DF0843CAED7D92DBE0E6001C46E8AE3B8`, `code.bin = 1F42BF7A4A03A47BA17003D5459240AAC8E730941970DE9A8271657AD9F07BB1`, `msgsec10.dat = F91C77775DE81CDEB063446E9C29C1F1CDB6D922B3E57580A55C9605DE53CD55`, `msgsec15.dat = A14B75CB1ADCC73E9D5D0F8292C4964208A7F4778E78189F45842A1FF2A828A9`.

## v28 Citra 피드백 / 전투 설명 일괄 정리 v29 (2026-08-29)

- `오`는 v27 하향 이동과 v28 강제 확대가 모두 런타임에서 어색했다. v29는 사용자 요청대로 별도 보정을 전부 폐기하고 `font\Sam3KRFont`의 원본을 다른 한글과 동일한 `v12.make_new_glyph()` common 13×13 경로로 다시 생성한다. physical566 bbox는 `[1,1,14,10]`; 추가 shift/scale/resize 없음.
- 하단 `성벽넘기` 누락은 실제 화면이 `code.bin 0x1D3A00`의 별도 `城壁越え` 8B C-string을 사용하기 때문이었다. `성벽넘기`도 8B라 NUL 위치를 움직이지 않고 exact-size 치환했다.
- `성문사격` 하단 버튼의 `code.bin 0x1CEFE4~0x1CEFEC` 슬롯은 총 8B, C-string payload 최대 7B다. `성문사격`은 8B + NUL = 9B가 필요해 **안전한 in-place 완전 표기는 불가**하며 하단 버튼은 `문사격`을 유지한다. 상단 노란 제목 `門射撃 05 05 | 05 火矢`는 8B `성문사격`으로 덮고 경계 05를 1개 남기는 2바이트 separator 차용 probe를 적용했다. Citra 확인 필요.
- `상잔` 왼쪽 일본어 잔상은 `상` physical704 자체가 아니라 왼쪽 physical703의 right edge 12픽셀이 샘플링되는 수평 bleed였다. physical703의 left edge는 0픽셀이므로 셀을 1px 왼쪽으로 이동해 right edge 12→0, 전체 ink 134→134로 보존했다.
- 스크린샷을 하나씩 받지 않도록 `msgsec10.dat` 고급 전투/계략 도움말 블록 `0x1448~0x1AAD`를 직접 스캔하고 **33개 설명 전체**를 fixed-position 한글화했다. `통상/궁시/기습/돌격/견제/일기토/받아넘기기/튕겨내기/단기돌진/기마돌격/성벽넘기/위협사격/일제/일제사격/성문사격/화시/저격/연노/소화/화계/복병/위령/상잔/논파/대화계/도발/설득/치료/고무/기도/충차/생존술` 등이 포함된다.
- `시혜`/`매복의독`의 v27 성공 방식(뒤 `05 05 05`에서 1B 차용, `05 05` 유지, 다음 fragment 시작 불변)은 그대로 보존한다.
- 빌더: `tools\build_sangokushi2_v29_tutorial_help_full_cleanup.py`; report: `analysis\v29_tutorial_help_full_cleanup_report.json`. 두 번 연속 처음부터 재생성해 동일 해시 확인. v29 SHA-256: `font.g1t = DECD27E59E7F25AF9F46861D9F8A17A647CE30783E84800F2AA93493EE8960ED`, `code.bin = 51FBBF1542ADBC790EE8393821CD97463AD702532E81F880C9929D800574D95F`, `msgsec10.dat = 2D63FA56AC947EB0D643445EB03F4899FA089D1DDE6547792AF65D2574153E55`, `msgsec15.dat = 1D15C738A49013419513CBF8019DFDF26F6856BA464B6BEF6719C4AB149CBFBD`.

## v29 Citra 피드백 / 실제 msgsec15 본문 정리 v30 (2026-08-29)

- v29에서 `msgsec10` 도움말 33개를 한글화했어도 Citra의 `통상/일기토/문사격/적중작적/상인 하위 설명`이 일본어로 남았다. 원인은 튜토리얼 상단 본문이 `msgsec10` 복제본이 아니라 **별도의 `msgsec15` 실제 본문 레코드**를 읽는 화면이 많기 때문이다. v30은 스크린샷과 HTML/`sangokushi2_translation.json`을 대조해 실제 런타임 레코드를 직접 패치한다.
- 실제 예: `통상 = msgsec15_0057_001 + 0058_000`, `일기토 = 0122_003 + 0123_000`, `문사격 = 0127_005`, `적중작적 1/2 = 0195_001 + 0196_000`, `병량구입 = 0202_001`.
- 전투/계략 실제 본문 **33개**를 fixed-position으로 한글화했다. 각 그룹은 원본 writable byte 이하, line unit 20 이하를 빌드 게이트로 검사하며 fragment 시작과 `msgsec15.dat` 전체 크기 **20,373B**를 유지한다.
- 상인 하위 복제본도 `병량매각/병량구입/노구입/강노구입/군마구입` **5개**를 각각 실제 레코드에 적용했다. 기존 상인 1/4 번역만으로는 각 하위 명령의 반복 설명까지 바뀌지 않는다.
- `적중작적`은 1/2~2/2 실제 `msgsec15` 본문을 한글화했다. 2/2는 48B 슬롯에 맞춰 `군주 매력·상대 충성·성격도 / 성패를 좌우합니다.`로 압축했다.
- `게임 오버`의 `오 버` 간격은 폰트가 아니라 `msgsec15_0029` 끝의 **ASCII padding 1B가 `오` 뒤에 놓인 것**이 원인이었다. 해당 1B를 같은 fragment 내부에서 이전 줄바꿈 직전으로 이동해 fragment 길이/시작을 그대로 유지했다. `오` 글리프 자체는 v29 표준 Sam3 상태를 유지한다.
- 상단 노란 제목은 하단 버튼과 맞춰 `성문사격` probe를 폐기하고 **`문사격`**으로 복원했다. 경계도 원래 `05 05 05`로 되돌렸다.
- `상잔` 왼쪽 bleed는 v29가 오래된 alias를 보고 잘못 physical703을 건드린 오류였다. 현재 `상 = alias 8BDD -> physical1178`; 실제 왼쪽 guard는 physical1177이다. v30은 v29의 physical703 변경을 원복하고 physical1177을 1px 왼쪽 이동해 right edge 11→0, ink 62→62로 보존한다.
- 사마휘 질문의 일본판 물음표는 ASCII `?`가 아니라 CP932 전각 `？ = 81 48`이다. `들을까요?`를 **`들을까요？`**로 바꾸어 원판 글리프를 그대로 사용한다.
- 시나리오1 hardcoded 번역 28개는 모두 유지됨을 재검증했다. 추출 데이터에서 번역 준비가 되어 있으나 아직 Original 상태로 남은 안전한 시나리오1 code 레코드는 0개였으므로, 인접한 다른 시나리오 문장을 추측해 추가하지 않았다.
- 빌더: `tools\build_sangokushi2_v30_actual_tutorial_body_cleanup.py`; report: `analysis\v30_actual_tutorial_body_cleanup_report.json`. 두 번 연속 처음부터 재생성해 동일 해시 확인. v30 SHA-256: `font.g1t = F6B4F685A2AB4AA146972D97695C170A565D5F9F6CC2F26A0EFF4BA8E8434202`, `code.bin = F088814B4E5FF21E40B4D79A46E85609D2282F9FF0C459348FB9D27136A1D1C8`, `msgsec10.dat = 2D63FA56AC947EB0D643445EB03F4899FA089D1DDE6547792AF65D2574153E55`, `msgsec15.dat = 54C1728144CB2F7448A45CA9EEC02860EBB87E0B5147EA5B8C947D6118E3A2B1`.

## v30 Citra 피드백 / 군사·후속 전투 조언 v31 (2026-08-29)

- 사용자가 v30에서 기존 수정 사항은 정상이라고 확인했고, 추가로 `전쟁/징병/모병/훈련/재편성/조선/설득` 설명과 `통상` 뒤쪽 전투 조언 다수가 일본어로 남아 있음을 확인했다. v31은 v30을 매번 처음부터 재생성한 뒤 이 실제 `msgsec15` 본문만 추가한다.
- 군사 실제 본문은 `전쟁=0144_001`, `징병=0144_002+0145_000`, 징병·모병 공통 설명 `0145_001`, `0145_002+0146_000`, `모병=0146_001+0147_000`, `훈련=0147_001`, `재편성=0147_002`, `조선=0147_003+0148_000` 및 `0148_001`이다. `설득`은 별도 실제 본문 `0130_000`을 처리한다.
- 일본어 제목 `落とし穴の数`는 의미상 `함정의 수`이며 UI 권위 표기는 **`함정 수`**로 결정했다. 기존 `함정수`는 띄어쓰기를 보정한다.
- `통상` 이후 아직 Original 상태인 전투 조언을 `0058_001~0120_000`에서 추적해 18그룹으로 추가 한글화했다. 함정 기본/함정 수·확률/함정 피해/함정 대책, 노·강노 활용/장기전 화살 보급, 수상전/화시 활용, 적 계략 주의/상잔·위령 대책, 적토마 활용이 포함된다.
- `적토마` 설명은 `0119_002` 뒤에 실제 `05 05 05` 페이지 경계가 있어 `0119_002`와 `0119_003+0120_000`을 별도 그룹으로 분리했다. 구조 게이트가 잘못된 연속 묶음을 차단했고, 분리 후 두 그룹 모두 원본 고정 슬롯 안에 들어갔다.
- v31 총 추가 그룹: 제목 1 + 군사 10 + 전투 조언 18 = **29그룹**. 신규 한글 글리프는 **21자**만 추가했고 기존 alias/physical은 재배치하지 않았다.
- `msgsec15.dat`는 Original과 같은 **20,373B**, 가변 repack 없음. `fix_data.bin`은 Original과 bit-exact. v30의 `code.bin/msgsec10.dat`도 동일 해시를 유지한다.
- 빌더: `tools\build_sangokushi2_v31_military_and_battle_advice.py`; report: `analysis\v31_military_and_battle_advice_report.json`. 두 번 연속 처음부터 재생성해 동일 해시 확인.
- v31 SHA-256: `font.g1t = 4D21EBB97CF304816B9FC5FC70A110A7E3571197F931DFCD5989A0255BC89037`, `code.bin = F088814B4E5FF21E40B4D79A46E85609D2282F9FF0C459348FB9D27136A1D1C8`, `msgsec10.dat = 2D63FA56AC947EB0D643445EB03F4899FA089D1DDE6547792AF65D2574153E55`, `msgsec15.dat = 6BE4849C0EB960BF55DB75AAF9FA0D5664231BF48C301EEBA0D616282862B5D8`.

## v31 Citra 피드백 / 3DS 전용 설명·폰트 bleed v32 (2026-08-29)

- v31에서 군사/오리지널모드 후속 설명은 정상화됐지만, 3DS 모드의 `수상전투법` 8~9/12와 `적계략주의` 10~11/12가 일본어로 남았다. 원인은 3DS 모드가 별도 복제본 `msgsec15_0123_002~0126_000`을 읽기 때문이다. 12/12만 공용 레코드를 사용해 v31 번역이 표시된다는 사용자 관찰과 바이너리 구조가 일치한다.
- v32는 3DS 전용 `수상전투법 8/12=0123_002`, `9/12=0123_003+0124_000`, `적계략주의 10/12=0124_001+0125_000`, `11/12=0125_001+0126_000`을 fixed-position으로 추가 한글화했다. 추가로 `0123_001`의 함정 탐색 조언도 한 페이지 더 번역했다.
- `설득` 일본어 혼입은 v31이 `0130_000`만 패치하고 앞의 `0129_003` prefix(`전략 커맨드 「계략」의`)를 놓친 것이 원인이다. v32는 `0129_003+0130_000` 전체 108B를 하나의 페이지로 처리한다.
- `전군퇴각` 실제 본문은 `0131_011+0132_000`, `대기무장`은 `0132_001`이며 v32에서 추가 한글화했다.
- `빠/변/없` 왼쪽의 가는 세로줄은 victim glyph 문제가 아니라 바로 왼쪽 atlas cell의 rightmost column bleed로 확인했다. victim physical은 각각 `1111/2366/662`, guard는 `1110/2365/661`. victim 자체를 이동·재생성하지 않고 guard의 x=13 픽셀만 최소 제거했다. right-edge nonzero는 각각 `1→0`, `8→0`, `1→0`이다.
- v32 추가: 3DS 전용 도움말 5그룹 + 기타 도움말 3그룹 = **8그룹**, 신규 한글 글리프 **11자**, bleed guard **3개**. `msgsec15.dat`는 Original과 동일한 **20,373B**, 가변 repack 없음. `fix_data.bin`은 Original bit-exact이며 v31의 `code.bin/msgsec10.dat` 해시는 그대로 유지한다.
- 빌더: `tools\build_sangokushi2_v32_3ds_help_and_font_bleed.py`; report: `analysis\v32_3ds_help_and_font_bleed_report.json`. 두 번 연속 처음부터 재생성해 동일 해시 확인.
- v32 SHA-256: `font.g1t = 5A51B537771AC386918E29B16B41347C048D67162EAA0C623C6F37290E4DACB5`, `code.bin = F088814B4E5FF21E40B4D79A46E85609D2282F9FF0C459348FB9D27136A1D1C8`, `msgsec10.dat = 2D63FA56AC947EB0D643445EB03F4899FA089D1DDE6547792AF65D2574153E55`, `msgsec15.dat = ED3715C05F03F7CB3D16061A4B7A82F25D0E007F43ABC4A08DA627C6E426D9B4`.

## v32 기반 StartMenu 이미지 5장 재삽입 v33 (2026-08-29)

- 사용자가 수정한 `Extracted_Image\RomFS\StartMenu\start_menu_down\start_menu_down_004.png`~`008.png` 다섯 장만 적용했다. 각 PNG는 RGBA 256×32이며 대응 G1T texture type은 `0x09 RGBA8`이다.
- 새 빌더 `tools\build_sangokushi2_v33_start_menu_images.py`는 v32 전체 세트를 먼저 결정적으로 재생성한 뒤, immutable Original의 `RomFS\StartMenu\start_menu_down.g1t`를 기반으로 texture index 4~8 payload만 교체한다. 결과는 `Sangokushi 2 Patch\RomFS\StartMenu\start_menu_down.g1t`에 추가했다.
- PNG의 Y축 방향과 PICA200 8×8 swizzle를 역변환해 삽입했다. 원본 PNG 무변경 왕복은 5/5 byte-exact, 패치 G1T 재추출 결과는 사용자 PNG와 5/5 pixel-exact로 일치했다.
- G1T 크기는 Original/패치 모두 2,589,404B이다. header 및 비대상 texture payload 변경 0, 허용 payload 밖 byte diff 0이다. Original G1T SHA-256은 작업 전후 `FA12313C62A1147D6D3BA7572BF41DC69156423B2A6884193E23B57C2337BFA4`로 동일하다.
- 텍스트·폰트·번역 자료는 변경하지 않았다. v33의 `font.g1t`, `code.bin`, `msgsec10.dat`, `msgsec15.dat` 해시는 v32와 모두 동일하다. 두 번 연속 재생성한 `start_menu_down.g1t`도 동일 해시를 확인했다.
- v33 `start_menu_down.g1t` SHA-256: `F8735663F04EC33F573928F994BA0C7520BBB7F90FAB62C28333884665A343D6`.
- 검증 report: `analysis\v33_start_menu_images_report.json`. 정적/픽셀 검증은 PASS이며 실제 메뉴 표시는 사용자의 Citra 리빌드 확인이 최종 권위다.

## 최종 v33 Patch 기반 지정 이미지 7장 갱신 v34 (2026-08-29)

- 새 규칙에 따라 Original에서 다시 만들지 않고, 최종 v33 Patch의 `RomFS\StartMenu\start_menu_down.g1t` 해시 `F8735663...A343D6`를 정확히 확인한 뒤 이를 입력 기반으로 사용했다.
- 사용자가 명시한 texture index `005/006/007/008/031/032/033`만 교체했다. 비지정 texture 004를 포함한 다른 모든 texture payload와 G1T header는 v33 byte-exact로 유지했다.
- 005~008은 RGBA 256×32, 031~033은 RGBA 512×32이며 모두 type `0x09 RGBA8`이다. 7개 모두 v33 payload 무변경 codec 왕복과 v34 PNG pixel-exact readback을 통과했다.
- v33→v34 조합을 같은 캡처 입력으로 두 번 계산해 동일 출력을 확인했다. 파일 크기는 2,589,404B로 동일하고, 지정 payload 밖 byte diff는 0이다.
- `code.bin`, `Message\*.dat`, `font.g1t` 및 번역 HTML/JSON은 건드리지 않았다. 핵심 해시는 v32/v33과 동일하다.
- 빌더: `tools\build_sangokushi2_v34_start_menu_images.py`; report: `analysis\v34_start_menu_images_report.json`.
- v34 `start_menu_down.g1t` SHA-256: `77BC3DEBBF73F14B356F9DC35BDFFBF3BFBD7AAAF8A6AE4E7A751FD9C528E173`.

## v34 기반 인사 도움말·`빠` 글리프 보정 v35 (2026-08-29)

- 사용자 완료본 v35를 검증했다. 인사 도움말 `수색 4쪽 / 포상 2쪽 / 위임 2쪽 / 해고 1쪽 / 몰수 1쪽`, 총 10쪽을 `msgsec15.dat` 원본 fragment 위치에 fixed-position으로 적용했다.
- `msgsec15.dat`는 20,373B로 유지하며 가변 repack하지 않았다. 신규 글리프는 `닌/응/종` 3자뿐이고, `빠`는 기존 physical 1111 셀 안의 반쪽 획 5픽셀만 보정했다.
- v34 이미지 컨테이너 해시 `77BC3D...28E173`과 `code.bin/msgsec10.dat`를 보존했다.
- 빌더: `tools\build_sangokushi2_v35_personnel_help_and_fast_glyph.py`; report: `analysis\v35_personnel_help_and_fast_glyph_report.json`.
- v35 SHA-256: `font.g1t=C4871695476D8292E13E7EA85FDF912402898CD353056074A0455855A04F7913`, `msgsec15.dat=B1EFC705879D8BE1510824CBFE14455A17599686FA6D0A26AD0626A467750B56`.

## v35 기반 신무장 화면 문자열 위치 시험 v36 (2026-08-29)

- `RomFS\Message\msgsec01.dat`의 `msgsec01_0006_006` 오프셋 `0x527`과 `msgsec01_0006_008` 오프셋 `0x54D`를 각각 `신무장 등록하기`, `사실무장 편집하기`로 교체했다. 당시 이를 하단 버튼 캡션으로 추정했으나, 이후 사용자 Citra 스크린샷으로 **버튼 선택 후 상단에 나타나는 설명문**임이 확인됐다. 하단 버튼 이미지는 v36에서 전혀 수정되지 않았다.
- 각 번역은 원문보다 1B 길어 바로 뒤 `05 05 05` 구분자에서 1B만 차용하고 `05 05`를 남겼다. 파일 크기 9,057B와 뒤 fragment 시작 위치는 변하지 않았다.
- v35 대비 변경 파일은 `RomFS\Message\msgsec01.dat` 하나뿐이다. 나머지 Patch 25개 파일과 v35의 폰트·code·msgsec10·msgsec15·StartMenu 이미지 해시는 byte-exact로 보존했다.
- 두 번 연속 전체 재빌드에서 Patch `msgsec01.dat`, report, 실제 폰트 셀 프리뷰 해시가 각각 동일했다. 정적 readback도 두 문구와 정확히 일치한다.
- 빌더: `tools\build_sangokushi2_v36_new_officer_menu_proof.py`; report: `analysis\v36_new_officer_menu_proof_report.json`; 프리뷰: `analysis\v36_new_officer_menu_text_preview.png`.
- v36 `msgsec01.dat` SHA-256: `F744420225FDE48937532E09E8495F08396ECC505C78A118EA0A27614ADE1C84`. 이 revision은 문자열 위치 판정 실패 사례로 보존하며, Citra 실화면이 정적 추정보다 우선한다.

## v36 상단 설명문 번역 정정 v37 (2026-08-29)

- v36에서 수정한 두 문자열의 실제 역할에 맞춰 `msgsec01_0006_006=신무장을 등록합니다`, `msgsec01_0006_008=사실무장을 편집합니다`로 정정했다.
- 두 새 문장은 v36보다 각각 4B 길다. 동일 `msgsec01_0006` parent 안의 다른 `05 05 05` 구분자 8곳에서 1B씩만 차용하고 모두 `05 05`를 남겼다. parent 전체 길이, 다음 parent 시작, `msgsec01.dat` 전체 9,057B는 유지했다.
- v36 대비 변경 파일은 `RomFS\Message\msgsec01.dat` 하나뿐이며 이미지 파일은 0개 변경했다. 하단의 `新武将を登録する / 史実武将を編集する` 버튼 이미지는 여전히 위치 미확인·미수정이다.
- 두 번 연속 전체 재빌드에서 Patch, report, 실제 폰트 셀 프리뷰 해시가 일치했다.
- 빌더: `tools\build_sangokushi2_v37_new_officer_description_correction.py`; report: `analysis\v37_new_officer_description_correction_report.json`; 프리뷰: `analysis\v37_new_officer_description_preview.png`.
- v37 `msgsec01.dat` SHA-256: `0968180CBA6397DF625DB227C95F9299873AE64E81C27B156FAFAE5E83DCF676`.

## v37 기반 외교 도움말·`맞` 잔상 수정 v38 (2026-08-29)

- 작업 시작 시 문서상 기준은 v37이었으나 실제 Patch `msgsec01.dat`가 v36 해시 `F7444202...ADE1C84`로 남아 있음을 확인했다. 과거 전체 빌드 체인을 다시 실행하지 않고 현재 Patch를 입력으로 사용해 `msgsec01_0006` parent만 v37 의미(`신무장을 등록합니다 / 사실무장을 편집합니다`)와 해시 `0968180C...DCF676`로 정상화했다. StartMenu 이미지 컨테이너는 v34/v37 해시 `77BC3DEB...C528E173` 그대로 보존했다.
- 외교 하단 버튼과 연결된 실제 `msgsec15.dat` 설명 페이지를 05 05 05 경계와 런타임 순서로 재확인했다. 총 페이지 수는 `동맹 4 / 공동작전 1 / 정전 1 / 교환 1 / 원조 3 / 항복권고 1 / 동맹파기 1 = 12페이지`이며 전부 fixed-position으로 한글화했다.
- 바로 뒤에 Original 상태로 남아 있던 외교 일반 조언 2페이지(`msgsec15_0185_001`, `0185_002`)도 추가 한글화했다. `msgsec15.dat` 전체 크기는 Original과 같은 20,373B이고 가변 repack은 하지 않았다.
- `맞`은 physical 1280 자체가 아니라 바로 왼쪽 physical 1279의 x=13 우측 경계에 남은 반투명 anti-alias 6픽셀(alpha 51/119/119/17/153/34)이 비치는 수평 bleed로 확인했다. `맞` 글리프 자체는 건드리지 않고 해당 6픽셀만 제거했다.
- 빌더: `tools\build_sangokushi2_v38_diplomacy_help_and_mat_bleed.py`; report: `analysis\v38_diplomacy_help_and_mat_bleed_report.json`.
- v38 SHA-256: `font.g1t=3EE538752C4A837CDB5EE8AA70EE4392BE04D3D53293C132FA8DD78F98BAEC29`, `msgsec01.dat=0968180CBA6397DF625DB227C95F9299873AE64E81C27B156FAFAE5E83DCF676`, `msgsec15.dat=712F39B36E980305D36994277D8283B75410F367CC521F2B6DE4ED8C26CEE877`. `code.bin`, `msgsec10.dat`, StartMenu G1T는 직전 기준과 byte-exact로 유지했다.

## 최종 v38 Patch 기반 지정 이미지 5장 갱신 v39 (2026-08-29)

- 이미지 업데이트 규칙에 따라 최종 v38 Patch를 입력 기준으로 사용했다. 사용자가 명시한 PNG 이외에는 읽거나 수정 대상으로 삼지 않았다.
- `RomFS\StartMenu\start_menu_down.g1t`: texture index `006`(256×32), `029/030`(512×32)만 갱신했다. 세 항목 모두 type `0x09 RGBA8`이며 비대상 payload와 G1T header는 v38 byte-exact로 유지했다.
- `RomFS\EditBushou\edit_bushou_base_menu_down.g1t`: 기존 Patch에 이 컨테이너가 없었으므로 유효 런타임 베이스인 immutable Original(`ED3466D8...4036C2`)에서 texture index `002/003`(각 512×32 RGBA8)만 갱신해 Patch에 추가했다. 000/001 및 header/다른 payload는 Original byte-exact다.
- 5개 texture 모두 소스 RGBA8 codec 무변경 왕복과 패치 후 PNG pixel-exact readback을 통과했다. 대상 payload 밖 byte diff는 0이다. 텍스트·폰트·code 파일은 v38 해시 그대로 보존했다.
- 빌더: `tools\build_sangokushi2_v39_requested_images.py`; report: `analysis\v39_requested_images_report.json`.
- v39 빌더를 두 번 연속 실행해 동일 최종 해시를 확인했다.
- v39 SHA-256: `start_menu_down.g1t=1DDF65F68F9E6F2156F913009B1E0C4BA67F344A44E4F470CA879ED7B0B057D4`, `edit_bushou_base_menu_down.g1t=3D3BF50E3D8D596D8B4104CCC3B81751B72073F309D151C2073DA57C916F6046`. 핵심 텍스트/폰트 해시는 v38과 동일하다.

## v39 기반 신무장 화면·정보 설명·`와` 글리프 수정 v40 (2026-08-29)

- 사용자 Citra 확인으로 v37의 `msgsec01_0006` separator 1B 차용 방식이 이 parent에서는 안전하지 않음을 확인했다. `新武将登録` 제목의 선두 글자 유실과 `C9` 문자 노출이 발생했으므로, v40에서는 **14개의 원래 `05 05 05` separator를 전부 복원**하고 separator borrowing을 0으로 만들었다.
- 원본 fragment `0006_005/007`의 `ESC C9 ... ESC C1` 제어를 그대로 유지해 노란 제목을 `신무장 등록 / 사실무장 편집`으로 교체하고, `0006_006/008` 본문을 `신무장을 등록합니다 / 사실무장을 편집합니다`로 교체했다. 늘어난 12B는 같은 parent의 일반 설명을 `편집 내용을\n초기화합니다`(−4B), `무장명 변경 불가`(−8B)로 자연스럽게 압축해 확보했다. parent는 `0x472~0x5FA`, 392B 그대로이며 parent 밖 변경 byte는 0이다.
- 정보 메뉴의 실제 도움말 페이지 수를 재확인했다. `밀정/무장/타국/속령일람/전장일람/세력지도/특산`은 모두 1/1이다. `특산`은 기존 정상 번역을 byte-exact로 유지하고 나머지 6페이지를 `msgsec15_0185_003~0187_002` 실제 fragment에 fixed-position으로 한글화했다. `msgsec15.dat`는 20,373B 그대로다.
- `와`는 physical 717, historical mode `v11_wa_crop`으로 확인됐다. Sam3 원본 bbox `(1,1)-(15,14)`의 맨 오른쪽 x=14에 실제 `ㅏ` 획이 있으나 v11에서 이 열을 통째로 버리고 있었다. v40은 x<9를 전혀 건드리지 않고 원본 모음 x=10~14만 1px 왼쪽으로 재배치해 x=14를 game x=13에 보존했다. v39 대비 font 셀 diff는 **physical 717 하나뿐**이며 총 31픽셀 변화다.
- 사용자는 `라` 옆 미세 잔상이 에뮬레이터 문제였다고 정정했다. v40에서 `라` 및 주변 셀은 수정하지 않았고, v39 font와 셀 단위 비교에서도 변경 셀은 717 하나뿐임을 확인했다.
- 첫 v40 preflight가 검증기 오류로 중단되며 Patch의 `font/msgsec01`에 부분 쓰기가 발생했으나, 사용자가 실제 테스트에 사용한 `Sangokushi 2 Rebuild`의 핵심 파일이 기록된 v39 해시와 100% 일치함을 확인했다. 이를 **읽기 전용 복구 기준**으로 사용해 v39 byte-exact 상태로 되돌린 후 다시 빌드했다. Rebuild 자체는 수정하지 않았다.
- v40 빌더는 실행 시 자신이 소유한 `font/msgsec01/msgsec15` 영역을 해시 검증된 Rebuild v39로 정상화한 뒤 재생성하므로, 두 번 연속 실행에서 동일 최종 해시를 확인했다. v39의 `code.bin/msgsec10.dat/StartMenu/EditBushou` 이미지 컨테이너는 byte-exact로 유지했다.
- 빌더: `tools\build_sangokushi2_v40_new_officer_info_wa.py`; report: `analysis\v40_new_officer_info_wa_report.json`.
- v40 SHA-256: `font.g1t=75F7D93C53BC3062ECE74940AF61E7037BF4F9C4BFB8BA1F27E9CBB5BF99CADD`, `msgsec01.dat=8312685E669485292FF2C88FE2878FE3CF14CD355BB6DC55CA49C0B8AF164C97`, `msgsec15.dat=3CF0890E81C13C3AA32D8958C87257D2B854EF6286F08B6C939BEE36C39C0EB9`. v39 이미지 해시와 `code.bin/msgsec10.dat`는 그대로다.

## 최종 v40 Patch 기반 Tutorial 지정 이미지 6장 갱신 v41 (2026-08-29)

- 이미지 업데이트 규칙에 따라 작업 시작 전 실제 `Sangokushi 2 Patch`의 v40 핵심 해시를 검증했다. `font.g1t`, `code.bin`, `msgsec01.dat`, `msgsec10.dat`, `msgsec15.dat`, StartMenu/EditBushou 이미지 컨테이너가 모두 `analysis\\v40_new_officer_info_wa_report.json`의 기록값과 일치했다.
- 사용자가 명시한 PNG만 반영했다: `Tutorial\\Tutorial_002/003/004/005/014.png`와 `Tutorial_sub\\Tutorial_sub_001.png`, 총 6장. 대응 texture index는 각각 `Tutorial.g1t`의 `2/3/4/5/14`, `Tutorial_sub.g1t`의 `1`이다.
- 6개 texture는 모두 `0x09 RGBA8`이다. `002~005`는 512×32, `014`는 128×128, `Tutorial_sub_001`은 256×32이며 입력 PNG의 RGBA 모드와 크기를 직접 검증했다.
- v40 Patch에는 `RomFS\\Tutorial\\Tutorial.g1t`와 `Tutorial_sub.g1t`가 아직 존재하지 않았다. 따라서 이 두 컨테이너에 한해서만 immutable `Sangokushi 2 Original`을 **이전 Patch와 동일한 유효 베이스**로 읽어 사용했다. Original 자체는 수정하지 않았으며, 첫 생성 이후의 결정적 재실행은 이미 생성된 v41 Patch 컨테이너를 입력으로 사용한다.
- 각 대상 texture는 소스 payload의 RGBA8 codec 무변경 왕복을 먼저 검증한 뒤 사용자 PNG로 교체했다. G1T header와 비지정 texture payload는 전부 byte-exact로 유지했고, 지정 payload 밖 byte diff는 0이다.
- 패치 후 6개 texture를 다시 디코딩해 사용자 PNG와 비교한 결과 **6/6 pixel-exact**였다. `Sangokushi 2 Patch`의 비대상 파일도 전부 byte-exact이며 v40의 텍스트·폰트·기존 StartMenu/EditBushou 이미지 해시는 변하지 않았다.
- 빌더: `tools\\build_sangokushi2_v41_tutorial_images.py`; report: `analysis\\v41_tutorial_images_report.json`. `py_compile` 통과 후 연속 재실행에서 G1T와 report 해시까지 동일하게 재생성됐다.
- v41 SHA-256: `Tutorial.g1t=0319358DB04F9BDE63A33FD8DC13B58D2EE7B2E16D6A9480045503CE287BE4F9`, `Tutorial_sub.g1t=4F3D9813E6B17D146744156376F555B2D9667D0CDE21596D8091F5AEB2CD466E`.
- v40 핵심 보존 SHA-256: `font.g1t=75F7D93C53BC3062ECE74940AF61E7037BF4F9C4BFB8BA1F27E9CBB5BF99CADD`, `code.bin=F088814B4E5FF21E40B4D79A46E85609D2282F9FF0C459348FB9D27136A1D1C8`, `msgsec01.dat=8312685E669485292FF2C88FE2878FE3CF14CD355BB6DC55CA49C0B8AF164C97`, `msgsec10.dat=2D63FA56AC947EB0D643445EB03F4899FA089D1DDE6547792AF65D2574153E55`, `msgsec15.dat=3CF0890E81C13C3AA32D8958C87257D2B854EF6286F08B6C939BEE36C39C0EB9`.
- `Sangokushi 2 Original`, `Sangokushi 2 Rebuild`, `Backup`은 수정하지 않았다. 최종 런타임 판정은 사용자가 Patch 전체를 Rebuild에 복사해 Citra에서 확인하는 것이 권위다.

## v41 런타임 회귀 수정 / 신무장 고정 슬롯 중간 시험 v42 (2026-08-29)

- 사용자 Citra에서 신무장 화면의 노란 제목과 상단 본문이 `刺ら장 등록`, `ら장을 등록합니다`처럼 깨지는 것을 확인했다. v41 자체는 이미지 전용이므로 원인은 v40의 `msgsec01_0006` 처리에 있다.
- v40은 parent 전체 392B와 `05 05 05` 경계 수는 유지했지만, fragment 4/9를 줄이고 5~8을 늘려 같은 parent 내부의 실제 fragment 시작 위치를 이동시켰다. 이 UI가 fragment 위치를 고정 참조하는 런타임 특성과 충돌한 것으로 판정했다.
- v42는 현재 최종 v41 Patch를 기준으로 **`RomFS\\Message\\msgsec01.dat` 하나만 변경**한다. `msgsec01_0006` parent `0x472~0x5FA`만 immutable Original의 pre-v36 바이트 배치로 복구하여 모든 원래 fragment 시작 위치를 되살린 뒤, 사용자 지정 4문구만 fixed-position으로 적용했다.
- 적용 문구: 제목 `신무장등록`, 본문 `신무장 등록하기`, 제목 `사실무장편집`, 본문 `사실무장 편집하기`.
- 제목 2개는 `ESC C9 ... ESC C1`을 포함해 각각 원본 16B/18B 슬롯과 **정확히 같은 길이**다. 본문 2개는 각각 원본 14B/16B보다 1B 길어 바로 뒤 `05 05 05` separator의 첫 1B만 차용하고 `05 05`를 남긴다. 다음 fragment의 절대 시작 위치와 `msgsec01.dat` 전체 9,057B 크기는 변하지 않는다.
- v40에서 용량 확보용으로 바꿨던 fragment 4 `편집 내용을/초기화합니다`, fragment 9 `무장명 변경 불가`는 parent 구조 복구 과정에서 pre-v36 원래 바이트로 되돌렸다. 이는 별도 번역 확장이 아니라 v40 내부 재배치의 collateral change 제거다.
- v41의 Tutorial 6개 이미지, StartMenu/EditBushou 이미지, `font.g1t`, `code.bin`, `msgsec10.dat`, `msgsec15.dat` 및 그 밖의 Patch 파일은 모두 byte-exact로 유지했다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더: `tools\\build_sangokushi2_v42_new_officer_fixed_slots.py`; report: `analysis\\v42_new_officer_fixed_slots_report.json`. `py_compile` 및 두 번 연속 재실행에서 동일 결과를 확인했다.
- v42 `msgsec01.dat` SHA-256: `A2AC5281AD8E5FFD1E73DF8280DB32C93709A0A70FA3B92C1162436BB0937A72`.
- 이 revision은 **Citra 중간 확인 전용**이다. 런타임 성공 확인 전에는 `text_review.html`/`sangokushi2_translation.json`의 권위 번역값을 v42 문구로 동기화하지 않는다.

## v42 기반 튜토리얼 문구·한글 alias 충돌 복구 v43 (2026-08-29)

- 사용자 Citra에서 포상/함정/노·강노/수상전/적계략주의 설명에 `지괴`, `탑 수 있는`, `떨어측`, `닌으면`, `응니다`, `종심`, `닌일`, `례는` 등 다수의 글리프 오표시와 일부 문장 축약 오류를 확인했다.
- 원인을 정적 report 체인과 현재 alias map으로 재분석했다. **v31에서 이미 사용한 글자 alias를 v32가 새 alias로 재할당했지만 v31의 기존 msgsec15 문장을 다시 인코딩하지 않은 것**이 첫 원인이다. 실제 load conflict 예: `붙 94F4→94CE`, `탑 94F8→94CF`, `핵 94FA→94D2`, `끄 9541→94D7`, `처 9542→94D9`, `럼 9544→94DC`, `측 94D2→94EA`, `괴 9545→94EE`, `롭 9547→94F3`.
- 두 번째 원인은 v32의 glyph donor 선택 입력에 v31 신규 `glyph_rows`가 전부 접히지 않아 이미 쓰던 alias를 다시 donor로 사용한 것이다. v42 기준 map에서 10개의 historical duplicate alias를 확인했다: `렴/빈`, `찾/춰`, `몽/붙`, `탑/팔`, `골/끄`, `처/칫`, `꺾/럼`, `질/측`, `괴/급`, `돼/롭`.
- 이번 요청 문장에 쓰이는 중복/누락 글자는 fresh safe alias로 재배정했다. 총 11자: `급/측/골/칫/꺾/희/붙/탑/끄/처/럼`. 새 alias는 각각 `9666/9667/966A/9675/967E/969A/969B/96A4/96A9/96AE/96B7`, physical `3128/3129/3132/3143/3152/3179/3180/3189/3194/3199/3208`이다. 모든 새 셀은 packed `font.g1t` 재디코딩 pixel-exact를 통과했다.
- 신규 donor는 Original live-source alias뿐 아니라 **모든 과거 `analysis/v*_report.json`에 한 번이라도 등장한 `alias/old_alias/new_alias`를 역사적 사용 alias로 영구 예약**해 재사용하지 않는다. 기존/신규 Korean physical, known bleed guard, ±73 수직 인접 셀도 donor에서 제외한다.
- 재발 방지용 `tools\\sangokushi2_alias_guard.py`를 추가했다. 앞으로 신규/수정 문장을 인코딩하기 전에 해당 문장의 모든 한글이 다른 글자와 alias를 공유하지 않는지 강제 검증한다. legacy 충돌 글자가 새 수정문에 등장하면 fresh alias로 분리한 뒤 그 문장을 읽는 공용/3DS 복제 레코드를 모두 최종 map으로 다시 인코딩해야 한다. 기존에 런타임에서 사용된 문자→alias를 임의 재할당하는 방식은 금지한다.
- `msgsec15.dat`는 가변 repack하지 않고 Original fragment start/전체 20,373B를 그대로 유지한 fixed-position 방식으로 아래 **12개 런타임 그룹**을 다시 인코딩했다: 포상 2/2, 함정 수, 함정 골칫거리, 함정 대책 2, 노·강노 활용 1/2, 수상전 기본 common/3DS, 수상전 화시 common/3DS, 적 계략 주의 common/3DS.
- 사용자 요청 문구를 적용했다. `여러 무장에게 지급 가능`, `설치 가능한 함정 수도...`, `공격 측에게 함정은 골칫거리...`, `선행 부대는 병력이 적어...후행 부대는...희생합시다`, `장기전에서는 화살이 부족할 수 있습니다`(끝 마침표 제거), `장기전에서는 화살을 보급할 수 있습니다...다음 달에 원군으로...화살이 보급됩니다`, `붙으면/탑니다/핵심이며`, `붙일 수/끄는`, `계략은 함정처럼 공격 측 행동을 방해합니다...`로 정리했다.
- `함정 수`는 primary `설치 가능한`이 writable 147B 중 124B로 충분히 들어가므로 fallback `쓸 수 있는`은 사용하지 않았다. 모든 그룹은 한 줄 최대 20 fullwidth cell 게이트를 통과했다. 가장 여유가 적은 `노·강노 활용 2`도 158B 중 155B로 3B 여유가 있다.
- 최종 요청 문장 alias 공유 충돌은 **0건**이다. 이번 범위 밖 historical collision은 `렴/빈`, `찾/춰`, `돼/롭` 3쌍만 legacy debt로 남겼고, 이후 이 글자가 수정문에 등장할 때 같은 fresh-alias 절차를 강제한다.
- 첫 v43 초안에서 과거 stale alias를 donor 후보로 다시 쓸 수 있는 위험을 추가 발견해 폐기했다. 사용자가 아직 복사하지 않은 `Sangokushi 2 Rebuild`의 `font.g1t/msgsec15.dat`가 v42 기록 해시와 정확히 일치함을 검증한 뒤 읽기 전용 복구 기준으로 Patch의 두 파일만 v42로 되돌리고, **과거 모든 alias 이력 영구 예약** 규칙을 추가한 최종 v43을 다시 생성했다. Rebuild 자체는 수정하지 않았다.
- 빌더: `tools\\build_sangokushi2_v43_tutorial_text_alias_repair.py`; alias gate: `tools\\sangokushi2_alias_guard.py`; report: `analysis\\v43_tutorial_text_alias_repair_report.json`. `py_compile` 통과 후 두 번 연속 재실행에서 동일 최종 해시를 확인했다.
- v43 SHA-256: `font.g1t=EA5D622FFD664F49EE17BB84D04026F6C1097D06D2D146C3C819B92C915A3A4E`, `msgsec15.dat=E62DECCE95F15E362E371C457BFC5E8718581E9436220528082A4AA112D8EBE9`.
- v42 `msgsec01.dat=A2AC5281...0937A72`, `code.bin=F088814B...6A1D1C8`, `msgsec10.dat=2D63FA56...E55`, v41 Tutorial G1T 및 기존 StartMenu/EditBushou 이미지는 byte-exact로 유지했다.
- 이 revision 역시 **Citra 중간 확인 전용**이다. 사용자 확인 전에는 `text_review.html`/`sangokushi2_translation.json`을 새 문구로 덮어쓰지 않는다.

## v43 기반 병사고용·위서의심·태수변경·임명 정리 v44 (2026-08-29)

- 사용자 Citra에서 `병사고용 2/3`의 `저빈하지만`, `사정에 맞찾`, `위서의심 1/4`의 `편 지를`, `태수변경 6/9`의 `다른 무장 이`, `임명 2/2`의 마지막 태수 설명 잘림을 확인했다.
- alias 검사 결과 `렴`은 legacy collision `렴/빈 -> 94C8`, `춰`는 `찾/춰 -> 94CC`임을 재확인했다. 병사고용 문구는 `징병은 저렴하지만... / 자금 사정에 맞게 선택하십시오.`로 수정해 `춰/찾` collision을 더 이상 사용하지 않으며, 실제로 필요한 `렴`은 fresh safe alias `96C7` / physical 3224로 분리했다. 새 태수 문구에 필요한 `됨`은 기존 map에 없어 fresh alias `96CB` / physical 3228로 추가했다. 두 글리프 모두 packed-font pixel-exact readback을 통과했고 이번 4페이지 target alias collision은 최종 0건이다.
- `위서의심 1/4`은 과거 `patch_lines_group`이 fragment 경계의 1B padding을 첫 줄 중간에 남겨 `편 지를`로 보인 문제다. v44에서는 `msgsec15_0196_002 + 0197_000` 전체를 하나의 fixed-position group으로 다시 인코딩해 `인접국 무장에게 거짓 편지를 보내`로 붙였다.
- `태수변경 6/9`(`msgsec15_0155_001 + 0156_000`)은 `다른 무장이 문·무관뿐이면`을 `남은 무장이 문·무관뿐이면`으로 수정했다. 페이지는 148B / 189B로 41B 여유다.
- `임명 2/2`은 기존 target이 **9줄**이었고 Citra 화면은 실제로 **8줄까지만 표시**되는 것이 원인이다. byte budget은 기존부터 충분했으므로 `↑` 기호 축약은 사용하지 않았다. 사용자 2안에 따라 무관 설명을 `무관 「개발」「외교」 불가` 한 줄로 합치고 문관은 `정치 높은 무장에 적합`으로 줄여 전체를 정확히 8줄로 재편했다. 마지막 두 줄은 `태수: 문관·무관을 태수 임명 시,` / `도시 위임 상태가 됨`이다. 새 본문은 **253B / 314B**, 61B 여유이며 최대 line width 19칸이다.
- 병사고용 2/3은 116B/121B, 위서의심 1/4은 145B/174B, 태수변경 6/9은 148B/189B, 임명 2/2는 253B/314B로 모두 fixed-position/no-repack 용량 게이트를 통과했다. 모든 줄은 20칸 이하다.
- v44 변경 파일은 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec15.dat` 두 개뿐이다. `msgsec01.dat`의 v42 신무장 수정, `code.bin`, `msgsec10.dat`, StartMenu/EditBushou 및 v41 Tutorial 이미지 등은 v43과 byte-exact로 유지했다.
- 첫 v44 실행 후 결정적 재실행 시 기존 v44 fresh alias를 다시 donor 후보에서 제외하면서 새 alias가 선택되는 문제를 발견했다. 빌더를 수정해 기존 v44 report의 glyph alias를 재실행 시 그대로 재사용하도록 했고, 이후 **두 번 연속 font/msgsec15/report 해시까지 동일**함을 확인했다.
- 빌더: `tools\\build_sangokushi2_v44_help_cleanup.py`; report: `analysis\\v44_help_cleanup_report.json`.
- v44 SHA-256: `font.g1t=23E187F43E10449B6396D469F06D804E6ECBFD35001FAAC8A9CD8A728655CAC0`, `msgsec15.dat=88FA7897CEB164799A06586CCE2587DFCCA74FBEEB132931F7BE79BE26A50FBB`.
- 이 revision도 **Citra 중간 확인 전용**이다. 사용자 확인 전에는 번역 HTML/JSON 권위값을 동기화하지 않는다.

## v44 기반 메뉴 상세 설명·alias 수정 v45 (2026-08-29)

- 사용자 Citra에서 `적대심낮추기 3/3`의 `낮찾야`, `공동작전 1/1`의 `지괴합니다`, 그리고 계략 `이호경식/구호탄랑`, 개발 4개, 특별 4개의 상단 상세 설명이 일본어로 남은 것을 확인했다.
- `낮찾야`는 legacy collision `찾/춰 -> 94CC`가 원인이다. v45는 `춰`를 fresh safe alias `96B8` / physical 3209로 분리하고 `msgsec15_0185_002` 전체를 현재 map으로 재인코딩했다. packed-font readback은 pixel-exact다.
- `지괴합니다`는 공동작전 페이지가 v43 이전의 `급/괴` stale alias 바이트를 유지한 것이 원인이다. 신규 glyph를 만들지 않고 v43에서 이미 검증한 safe `급` alias로 `msgsec15_0171_001 + 0172 + 0173 + 0174_000`을 다시 인코딩했다.
- 짧은 `msgsec10` 명령 설명은 Patch와 Rebuild가 동일 해시이고 이미 한글 바이트임을 확인했다. 런타임에서 일본어로 남은 부분은 별도 `msgsec15` command-detail page였다. 이번에 실제 detail page 10개만 추가 한글화했다: 계략 2(`이호경식/구호탄랑`), 개발 4(`토지개발/경작/치수/상업투자`), 특별 4(`방랑/치료/임시징수/세율변경`).
- 모든 detail page는 기존 `translation_ko` 의미를 기준으로 화면 용량에 맞게 압축했다. 특별 메뉴처럼 슬롯이 작은 항목은 핵심 기능만 보존했다. 12개 전체 그룹은 fixed-position/no-repack, 최대 20칸 이하, target alias collision 0건이다.
- 용량 예: 토지개발 83/89B, 경작 120/123B, 치수 92/107B, 상업투자 81/87B, 이호경식 115/126B, 구호탄랑 128/138B, 방랑 83/89B, 치료 67/73B, 임시징수 50/59B, 세율변경 32/37B.
- v45 변경 파일은 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec15.dat` 두 개뿐이다. `msgsec10.dat`, `msgsec01.dat`, `code.bin`, StartMenu/EditBushou, v41 Tutorial 이미지는 v44와 byte-exact로 유지했다.
- 빌더: `tools\\build_sangokushi2_v45_menu_help_alias_fix.py`; report: `analysis\\v45_menu_help_alias_fix_report.json`. 두 번 연속 실행에서 font/msgsec15/report가 동일하게 재생성됐다.
- v45 SHA-256: `font.g1t=AE40742C557167705018DD66C673D35795B225A82B6A056CB58DE7580013BE09`, `msgsec15.dat=E4120D36FF2D73C572B56548CD901BC6E9BD2C44C7FA0AEB49BF81A265777B91`.
- 2026-08-29 사용자 Citra 확인 결과 **v45까지의 튜토리얼 번역은 모두 정상이며 이상 없음**으로 확정했다. 따라서 v45의 튜토리얼/도움말 런타임 상태를 현재 안정 기준으로 동결하며, 이후 별도 회귀 제보가 없는 한 이 영역을 재번역·재패킹·전체 remap하지 않는다.
- 튜토리얼 작업의 표준 흐름은 `최신 Patch 베이스 검증 → 실제 런타임 소스 추적 → fixed-position/no-repack → 20칸/byte budget 검사 → alias collision/history guard 검사 → 필요한 글자만 fresh safe alias 분리 → packed-font pixel-exact readback → 비대상 파일 byte-exact 보존 → 빌더 2회 결정적 재실행 → Citra 화면 승인`으로 확정한다. 잘못된 한글이 보이면 단순 오타로 처리하기 전에 stale alias/legacy collision을 먼저 검사한다.

## v45 후속 미번역·단독 마침표 정리 v46 (2026-08-29)

- v45를 튜토리얼 완료로 기록한 직후 사용자 추가 확인에서 `치수/상업투자/세율변경`의 2~4페이지가 아직 일본어이고, 문장 끝 ASCII `.` 하나가 다음 시각 줄로 밀리는 사례가 남아 있음을 확인했다. 따라서 v45의 **완료 판정은 해제**하고 v46을 새 중간 시험본으로 만든다.
- 마침표 규칙은 전역 삭제가 아니다. Citra에서 문장 본문은 정상 줄에 끝나고 **종결 마침표 1개만 다음 줄로 밀리는 경우**에만 그 1B `.`를 제거한다. 사용자 확인 2곳은 `게임 설명`의 `대륙을 통일합니다.`와 `적중작적 1/2`의 `배신합니다.`이며, 현재 활성 번역 중 같은 경계 패턴인 `함정 기본`의 `조언할 때가 있습니다.`와 `노·강노 활용 2`의 `화살을 보급할 수 있습니다.`도 같은 규칙으로 정리했다. 다른 문장부호는 유지한다.
- 실제 미번역 런타임 소스는 `msgsec15` detail page 9개다: 치수 `0189_001`, `0189_002+0190_000`, `0190_001`; 상업투자 `0191_001+0192_000`, `0192_001`, `0192_002+0193_000`; 세율변경 `0204_002`, `0205+0206_000`, `0206_001`.
- 9페이지는 기존 `translation_ko`의 의미를 유지하면서 원본 fixed slot에 맞게 압축했다. 가장 빡빡한 슬롯은 세율변경 2/4가 **88/88B**, 세율변경 4/4가 **116/117B**다. 13개 전체 그룹은 최대 20칸 이하, fixed-position/no-repack, target alias collision 0건이다.
- 신규 글리프는 `잊=96C1/physical3218`, `밭=975B/3305`, `친=975F/3309`, `꿀=976D/3323` 총 4자이며 packed `font.g1t` 재디코딩 pixel-exact readback을 통과했다.
- v46 변경 파일은 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec15.dat` 두 개뿐이다. `msgsec10.dat`, `msgsec01.dat`, `code.bin`, StartMenu/EditBushou, v41 Tutorial 이미지 등은 v45와 byte-exact로 유지했다.
- 빌더: `tools\\build_sangokushi2_v46_tutorial_remaining_pages_punctuation.py`; report: `analysis\\v46_tutorial_remaining_pages_punctuation_report.json`. 두 번 연속 실행에서 font/msgsec15/report 해시가 동일하게 재생성됐다.
- v46 SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`, `msgsec15.dat=5F5578B4996733BD1225EC957D0932808A21146BDB4AC68591CA3EA4102B60F8`.
- v46은 **Citra 중간 확인 전용**이다. 사용자 확인 전에는 번역 HTML/JSON 권위값을 이번 압축 문구로 동기화하지 않는다.

## v46 문장부호 회귀 복구 v47 (2026-08-29)

- 사용자 Citra 확인에서 v46의 `게임 설명` 첫 페이지가 크게 재배치되어, 요청한 `.` 제거 외에 `군주 사망하면...` 줄배치와 padding까지 깨진 것을 확인했다. `관개도올리기 3/4`에서도 `오르지 않을 수 있습니다.` 뒤 `.` 하나가 단독 줄로 밀렸다.
- **원인 확정:** v46은 punctuation-only 요청을 `v21.patch_fixed_group()`으로 전체 group 재인코딩했다. 특히 `msgsec15_0026`은 원본 구조상 `대륙 통일` 문장 끝과 다음 문장 시작이 같은 fragment에 공존하는데, v46 target은 첫 문장만 넣고 나머지 writable bytes를 padding으로 채웠다. 이 때문에 `.`만 없어져야 할 작업이 인접 본문/줄바꿈까지 덮어썼다.
- v47에서는 punctuation-only 처리 방식을 전면 수정했다. `게임 설명`은 v23에서 검증된 `GAME_BODY_SLOTS`의 앞 2 slot을 그대로 재구성해 v45 레이아웃을 복원한다. `적중작적 1/2`은 v30 승인 target, `함정 기본`은 v31 승인 target, `노·강노 활용 2`는 v43 승인 target으로 각각 원래 fixed layout을 복원한다. v46에서 사용자 요청 없이 선제 수정했던 `함정 기본/노·강노 활용2`의 마침표 제거는 취소한다.
- 구조 복원 후 실제 punctuation 수정은 정확히 3개다: `통일합니다.` / `배신합니다.` / `오르지 않을 수 있습니다.`의 ASCII `0x2E`를 같은 위치의 `0x20`으로 치환한다. 문자열 길이, fragment start, `0x0A`, `0x05`, padding을 이동하지 않는다.
- 새 방지 게이트: 구조 복원 snapshot 대비 diff는 정확히 3바이트여야 하며, 세 byte 모두 `0x2E -> 0x20`이어야 한다. 지정 offset 외 차이가 하나라도 있으면 빌드 실패한다. punctuation-only 요청에는 generic group re-encode/reflow를 금지한다.
- v46의 새 번역 9페이지 중 `치수 3/4`은 위 마침표 1B만 변경하고, 나머지 8페이지는 v46 bytes와 fragment별 byte-exact 동일함을 검증한다. v46 신규 font glyph 4자도 그대로 유지하며 v47은 `msgsec15.dat`만 변경한다.
- 빌더: `tools\\build_sangokushi2_v47_punctuation_byte_only_repair.py`; report: `analysis\\v47_punctuation_byte_only_repair_report.json`. 두 번 연속 실행에서 동일 결과를 확인했다.
- v47 SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`(v46 동일), `msgsec15.dat=74A592B7794F94B3733CB00FF26E80FCE487329019B32D62ADA8C9F64E8F93E6`.
- v47은 **Citra 중간 확인 전용**이다. 이번에는 우선 게임 설명 첫 페이지의 줄배치가 v45 화면과 동일하게 복원되고 마침표만 사라졌는지, 적중작적 `배신합니다`, 치수 3/4 `오르지 않을 수 있습니다` 뒤 단독 마침표가 사라졌는지만 확인한다.

## v47 게임 설명 2차 회귀 복구 v48 (2026-08-29)

- 사용자 Citra 확인에서 v47 게임 설명이 여전히 잘못됐다. 첫 줄은 `도시를지배해`로 붙었고, 다음 줄의 `군주 사망`이 사라져 `하면 후계자가...`부터 시작했다.
- **v47의 추가 원인 확정:** v47은 v46의 broad-group 재인코딩 문제는 파악했지만, 정상 화면의 레이아웃 권위를 잘못 선택했다. `v23.GAME_BODY_SLOTS[:2]`만 재작성하면 과거 정상 화면이 복구된다고 가정했으나, 사용자 스크린샷의 정상 화면은 v25의 `GAME_LINES` 6줄을 `v23.patch_lines_group()`으로 전체 `GAME_IDS`에 연속 token-flow한 결과였다.
- 정상 v25 token-flow에서는 `msgsec15_0025_002`가 정확히 `４６개 도시를`에서 끝나고, 다음 fragment `msgsec15_0026`의 **첫 writable byte에 공백 토큰이 들어가** 화면상 `도시를 지배해`가 된다. v47의 per-slot 복원은 이 cross-fragment space를 만들지 못해 `도시를지배해`가 됐다.
- 더 중요한 점은 `msgsec15_0026`이 화면상 첫 문장만 담는 slot이 아니라, v25 whole-block 재배치에서는 첫 문장 뒤 남은 writable bytes에 다음 문장 `군주 사망...`의 시작도 들어간다는 것이다. v46/v47이 0026을 첫 문장 전용으로 덮어쓰면서 이 인접 번역 바이트를 padding으로 지워 `군주 사망`이 사라졌다.
- v48은 사용자 정상 스크린샷과 정확히 일치하는 v25 `GAME_LINES` 6줄 전체를 같은 `patch_lines_group()` 알고리즘으로 게임 설명 전체 block에 복원한다. 복원 후 snapshot을 기준으로 `통일합니다.`의 ASCII `.` 1바이트만 `0x20`으로 치환한다. 이후 token 이동/reflow는 없다.
- v48 강제 gate: (1) `msgsec15_0026` 첫 byte가 `0x20`이어야 함(`도시를 지배해`), (2) 게임 설명 block에 `군주 사망` encoded bytes가 정확히 1회 존재해야 함, (3) v25 승인 복원 snapshot 대비 최종 diff는 정확히 1바이트 `0x2E->0x20`, (4) v47 대비 변경은 게임 설명 source fragment 범위 밖으로 나가면 실패, (5) font/code/msg01/msg10/이미지는 v47과 byte-exact.
- 예상 Citra 문구는 `４６개 도시를 지배해 대륙을 통일합니다` / `군주 사망하면 후계자가 될 무장이 없으` / `면 게임 오버가 됩니다.` / 빈 줄 / `아래에서 게임 설명 버튼을 누르시면` / `위 화면에 설명이 표시 됩니다.`다.
- 빌더: `tools\\build_sangokushi2_v48_game_explanation_exact_restore.py`; report: `analysis\\v48_game_explanation_exact_restore_report.json`. 두 번 연속 실행에서 동일 결과를 확인했다.
- v48 SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`(v47 동일), `msgsec15.dat=D59BACD2A59EEEBD49A04235D0274C3B2B9365DBC9A3D3D365F823B8AD1282B2`.
- **재발방지 핵심:** screen-approved layout과 source fragment/slot 경계를 동일시하지 않는다. shared fragment가 있으면 정상 화면을 실제 생성한 whole-block writer/라인 목록을 권위로 삼고, punctuation-only는 그 승인 baseline을 재현한 뒤 byte-local edit만 수행한다.
- v48은 **Citra 중간 확인 전용**이다. 우선 게임 설명 첫 페이지가 사용자 제공 정상 스크린샷과 동일한 줄배치로 복원되고 첫 줄의 점만 사라졌는지 확인한다.

## v48 게임 오버 padding + 이미지 업데이트 v49 (2026-08-29)

- 사용자 Citra 확인에서 v48의 게임 설명 전체 레이아웃은 정상으로 복구됐다. 남은 텍스트 문제는 `면 게임 오 버가 됩니다.`의 `오 버` 1B 간격뿐이다.
- **원인 재확정:** 이 문제는 신규 문제가 아니라 v30에서 이미 해결했던 동일 fragment padding 회귀다. v25 whole-block token flow가 `msgsec15_0029` 끝에 1B를 남기고, 다음 2B 글자 `버`가 들어가지 못해 `오` 뒤에 ASCII `0x20`이 padding으로 들어간다. v48 whole-block 복원 과정에서 과거 v30 후속 보정을 다시 적용하지 않아 재등장했다.
- v49는 검증된 `v30.fix_game_over_padding()`을 그대로 사용한다. `msgsec15_0029`의 `... 오 20`을 `... 20 0A ... 오` 형태로 바꾸어 spare 1B를 이전 visual line의 줄바꿈 직전으로 이동한다. fragment 길이/시작은 불변이고 2B `오`/`버`를 fragment 경계에 걸치지 않는다. `오` 글리프/alias는 수정하지 않는다.
- 이미지 요청은 정확히 18개다: `StartMenu/start_menu_up`의 001~015, 017, 018 = 17개와 `Tutorial/Tutorial_sub`의 001 = 1개. 그 외 PNG는 읽거나 삽입하지 않는다.
- 이미지 베이스 규칙: v48 Patch가 권위다. v48 Patch에 `start_menu_up.g1t`가 없으므로 이 컨테이너만 immutable Original을 byte-identical effective prior-Patch base로 최초 생성한다. `Tutorial_sub.g1t`는 v48 Patch에 이미 존재하므로 현재 Patch 컨테이너에서 texture 1 payload만 교체한다.
- 두 G1T 모두 RGBA8 codec no-change round-trip을 먼저 확인했고, 지정 payload 외 header/non-target bytes가 immutable Original과 byte-exact임을 강제했다. 18개 target은 재추출 pixel-exact readback을 전부 통과했다.
- v49는 v48 대비 `msgsec15.dat`, 신규 `start_menu_up.g1t`, 기존 `Tutorial_sub.g1t`만 변경한다. `font.g1t`, `code.bin`, `msgsec01.dat`, `msgsec10.dat`, `start_menu_down.g1t`, `edit_bushou_base_menu_down.g1t`, `Tutorial.g1t`는 v48과 byte-exact다.
- 빌더: `tools\\build_sangokushi2_v49_game_over_and_images.py`; report: `analysis\\v49_game_over_and_images_report.json`. 두 번 연속 실행에서 동일 결과를 확인했다.
- v49 SHA-256: `msgsec15.dat=02688DC0513FA5EF2B627258EED610160F6FB37FA5E1439D5128D77C822DD39F`, `start_menu_up.g1t=0B80514DDB0BA620D6584945E7E322BD2AFD504774071C00326991826DA1E84C`, `Tutorial_sub.g1t=B2688A4303BB556737F521768748B4A9E59F61D0F5F1757764712D1752DF48E4`.
- v49는 Citra 중간 확인 전용이다. 우선 `게임 오버` 간격과 새 StartMenu 상단 이미지/`Tutorial_sub_001` 표시를 확인한다.

## 전 인물 이름·시나리오/인물 대사 한글화 — checkpoint 0 (2026-08-29)

- 사용자 요청에 따라 인물 이름을 먼저 처리하고, 이후 HTML의 `translation_ko`를 기준으로 미적용 시나리오 대사 및 인물 간 대사를 순차 적용한다.
- 이름 규칙을 확정했다. `何進 || カシン` 같은 레코드에서는 9바이트 한자 표시 이름만 한국어로 바꾸고 뒤 가나 독음은 원본 바이트 그대로 보존한다. 전 인물에 대해 독음 필드와 비대상 레코드 바이트의 byte-exact 검증을 강제한다.
- 현재 정식 기준은 v49이며 `Sangokushi 2 Patch\RomFS\Scenario\fix_data.bin`은 Original과 동일한 SHA-256 `2CB96EBB54BB47957EE3BB2AF4EF7653B590A77E44A3DCB5B7310A447DA179EC`임을 재확인했다.
- 전체 완료 전에는 Patch/Revision을 갱신하지 않는다. 작업물은 `analysis\dialogue_translation_work` 아래 staging 및 약 20% 단위 checkpoint로만 저장한다.
- checkpoint 0 현재 실제 게임 파일 변경: 0개. 다음 단계는 `fix_data.bin` 전 인물 레코드 구조와 중복을 전수 목록화하고, 한국어 이름 대응표의 출처를 확정하는 것이다.

## 전 인물 이름·시나리오/인물 대사 한글화 — checkpoint 20: 이름 완료 (2026-08-29)

- `fix_data.bin` 인물 테이블은 `0x0D78`부터 44바이트 레코드 1,020개이며, 각 레코드의 `+18`에 9바이트 표시 이름, `+27`에 17바이트 가나 독음이 있음을 전수 검증했다.
- 표시 이름 1,020건을 모두 한국어로 대응했다. 앞 770건은 `Sangokushi DS rom\arm9.bin`의 한국어판 동순서 이름 테이블(`0x101F50`, 40바이트 레코드, `+15` 9바이트 이름)을 권위로 사용했고, 후속 206건은 3DS 추가 무장·고대 무장·전국시대 무장·수호전 무장·특수 인물 대응표로 검토했으며, 더미 44건은 `더미`로 통일했다.
- 이름 규칙은 엄격히 적용했다. 한자 표시 이름만 바꾸고 `カシン` 같은 가나 독음 1,020건은 byte-exact로 보존했다. 이름 칸 이외 `fix_data.bin` 변경 offset은 0건이다.
- v49 font를 베이스로 필요한 한글 319자를 모두 확보했다. 기존 alias history를 예약한 상태에서 부족한 69자만 fresh safe alias로 배치했고, target alias 공유 충돌 0건 및 packed-font glyph pixel-exact readback을 통과했다.
- 작업 결과는 Patch가 아니라 `analysis\dialogue_translation_work\staging`에만 생성했다. 빌더를 두 번 연속 실행해 font/fix_data/report 해시가 완전히 동일함을 확인했다.
- staging SHA-256: `font.g1t=5051807A135201C15CF1D676414B275E6F22804543E45F3584A2FC81C6A941C1`, `fix_data.bin=595B84494349172EF927308D4462774B8062DA70EC857192B6C8185A704ED021`, report=`CEC37851A1557740B73FC547CE9D7C5280263C92B103A89EE5AEB28F12371A07`.
- checkpoint: `analysis\dialogue_translation_work\checkpoint_20_names`. checkpoint 0의 전체 Patch manifest와 다시 대조했으며 **정식 Patch 변경은 0개**다.
- 도구/자료: `tools\inventory_sangokushi2_character_names.py`, `tools\build_sangokushi2_character_names_staging.py`, `tools\checkpoint_sangokushi2_dialogue_work.py`, `analysis\dialogue_translation_work\character_name_inventory.tsv`, `character_name_overrides.json`, `character_names_staging_report.json`.
- 다음 단계는 v49 Patch의 실제 런타임 대사 소스를 전수 조사하여 HTML 한국어가 아직 적용되지 않은 `시나리오 대사`와 `인물 간 대사`만 분리하는 것이다. 튜토리얼/UI 텍스트는 이번 범위에 포함하지 않는다.

## 전 인물 이름 한글화 정식 revision v50 (2026-08-29)

- 사용자의 후속 승인에 따라 이름 단계에서 revision을 한 번 끊어 v50을 발행했다. v49 전체 Patch manifest를 권위 베이스로 사용했다.
- v50 변경 파일은 정확히 `RomFS\Common\Font\font.g1t`, `RomFS\Scenario\fix_data.bin` 두 개다. 그 밖의 Patch 28개 파일은 checkpoint 0의 v49 manifest와 byte-exact다.
- 전 인물 1,020건 표시 이름이 모두 한국어로 역변환되며, 표시 이름 9바이트 칸 밖 변경 0건, 가나 독음 1,020건 byte-exact, alias 공유 충돌 0건, 신규/재배치 glyph 69자 pixel-exact를 재확인했다.
- 빌더: `tools\build_sangokushi2_v50_character_names.py`; report: `analysis\v50_character_names_report.json`. 두 번 연속 실행에서 font/fix_data/report 해시가 모두 동일했다.
- v50 SHA-256: `font.g1t=5051807A135201C15CF1D676414B275E6F22804543E45F3584A2FC81C6A941C1`, `fix_data.bin=595B84494349172EF927308D4462774B8062DA70EC857192B6C8185A704ED021`, report=`6EF5FEB9E2A283CE05ABCBDA34A7C0BA5F24101892949A03A706876E503BAD9D`.
- 대사 작업의 새 immutable 기준점은 `analysis\dialogue_translation_work\checkpoint_20_v50_published`에 봉인했다. 이후 대사는 v50을 기준으로 staging/checkpoint에 누적한다. 단, 사용자가 직접 확인한 런타임 회귀는 별도 중간 revision으로 수정할 수 있다.

## v50 런타임 이름·수색 회귀 수정 v51-intermediate (2026-08-30)

- 사용자 Citra에서 `동탁`의 `탁`이 점/쉼표 같은 stock glyph로 표시되고, 인사→수색에서 일본판 `誰ﾆ捜索ｻｾﾏｽｶ？` 위치에 전혀 다른 번역 `노력이 될 듯합니다...`가 출력되는 두 회귀를 확인했다.
- **이름 1차 가설 교정:** `0x98xx` 전체가 이름 렌더러에서 금지된다는 가설은 폐기했다. Original `fix_data.bin`의 이름에도 0x98 이상 Shift-JIS pair가 다수 정상 사용된다. 또한 Patch와 Rebuild의 v50 `font.g1t/fix_data.bin` 해시가 서로 같아 font 복사 누락도 원인이 아니었다.
- `동탁` v50 필드는 `88F2 98A4`; `동=88F2`는 정상이고 `탁=98A4`만 실패했다. v50 font의 계산 physical 3565에는 `탁` 픽셀이 pixel-exact로 들어 있었으므로 정적 font readback만으로 이름 화면 런타임 성공을 보장하지 못한다는 반례로 기록한다.
- 이름 allocator의 별도 확정 버그도 발견했다. v35가 이미 `응=94F8`, `종=94FA`를 실제 font에 넣었지만, v35의 `load_alias_map()` helper는 자기 revision의 신규 `glyph_rows`를 반환 map에 접지 않았다. v40→v50 이름 staging이 이 helper chain을 사용하면서 기존 `응/종`을 놓쳐 `응=98A5`, `종=9842`를 중복 신규 배정했다. 현재 v50 font의 physical 2897/2899는 각각 v35 Sam3 `응/종`과 pixel-exact임을 재검증했다.
- v51은 `종/응`을 기존 v35 alias `94FA/94F8`로 되돌리고, 이를 포함하는 전 이름을 다시 인코딩했다. `탁`은 v50 effective runtime 전체에서 raw reference 0인 original-valid alias `8B45`로 이동했다. JIS-linear physical은 1027이며 v17 성공 구간 안이고, 위 physical954의 bottom row와 아래 physical1100의 top row가 모두 투명해 ±73 bleed guard도 깨끗하다. 새 `탁` glyph packed-font readback은 pixel-exact다.
- 이름 수정 대상은 총 **19레코드**다. `탁응/동탁` 2건, `응` 포함 이름 3건, `종` 포함 이름 15건이며 `탁응`이 중복되어 합계 19건이다. `fix_data.bin` 변경은 이 19개 9B 표시 이름 필드의 실제 40바이트뿐이고, 가나 독음과 이름 필드 밖 변경은 0이다. v50 이름 필드에서 `98A4/9842/98A5`는 0회가 됐다.
- v50 신규 name glyph 중 `>=98xx`는 46자/109레코드였다. v51에서 확정 문제/중복인 `탁/종/응` 3자를 정리한 뒤 **43자/90레코드**가 런타임 미검증 위험군으로 남는다. 이 숫자는 오류 확정 수가 아니며, 실화면 증거 없이 전부 remap하지 않는다.
- **수색 문구 원인 확정:** `msgsec07.dat` 원본 헤더는 혼합 word-offset prefix가 아니었다. word0=`388`, word1~387은 모두 absolute BYTE offset이다. 원본 387포인터를 extraction fragment 위치와 대조하면 `fragment start 345 / fragment 내부 41 / 다음 fragment 직전 separator 1`로 전수 매핑된다.
- v20/v21 repacker는 앞의 단조 증가 177값을 segment word-offset으로 잘못 판단해 파일을 9,688→10,448B로 늘리면서 그 177개를 다른 의미로 재작성했다. 결정적 증거는 수색 포인터 역할을 하는 **header[153]**이다: Original `0x10E2` = `msgsec07_0060_002 誰ﾆ捜索ｻｾﾏｽｶ？` 시작, v50/v21 `0x125E`. 현재 repacked body에서 `0x125E`는 `msgsec07_0053_001`의 한국어 `이 이상은 / 헛된 노력이 될 듯합니다...` 시작 `0x124F`에서 정확히 +15B 내부다. 사용자 화면과 일치한다.
- 새 `tools\sangokushi2_msgsec07_flat_header.py`는 repacked body를 건드리지 않고 원본 387개 byte pointer를 각 논리 fragment의 새 위치로 전부 재계산한다. v51에서 header[153]은 `0x13DA`로 복구되며 이는 현재 body의 `msgsec07_0060_002` 실제 시작과 정확히 같다.
- 번역 권위 값 `msgsec07_0060_002`는 `누구에게 수색을 시키겠습니까?<$05><$05>`로 정정해 `sangokushi2_translation.json`과 `text_review.html`에 동기화했다. 실제 원본 슬롯은 16B라 전체 문장은 들어가지 않으므로 런타임에는 의미를 유지한 압축 UI `수색할 무장？<$05><$05>`를 15B + control 앞 padding 1B로 fixed-slot 삽입했다. `05 05` 제어는 원위치에 보존한다.
- `msgsec07.dat`는 v50과 같은 10,448B다. v50 대비 차이는 776B header와 `0x13DA~0x13E9` 16B 수색 슬롯 안에서만 발생하며, 그 밖의 repacked body는 byte-exact다. 따라서 v21의 기존 121 표시줄 번역 body는 보존된다.
- v51 Patch 변경 파일은 정확히 `RomFS\Common\Font\font.g1t`, `RomFS\Scenario\fix_data.bin`, `RomFS\Message\msgsec07.dat` 3개다. 나머지 Patch 파일은 sealed v50 manifest와 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다. Rebuild는 작업 종료 시에도 v50의 세 대상 해시 그대로임을 읽기 전용으로 확인했다.
- 빌더: `tools\build_sangokushi2_v51_name_search_runtime_fix.py`; msg07 header 도구: `tools\sangokushi2_msgsec07_flat_header.py`; report: `analysis\v51_name_search_runtime_fix_report.json`. 빌더를 연속 재실행해 동일 출력 해시를 확인했다.
- v51 SHA-256: `font.g1t=6C7C13453E61EAFB03094F6BFBF0C840E1822A19CAD1A28CBF3388193A87ECCC`, `fix_data.bin=126279344D75454A7617EDE997ED8FE75B1D35B652B8E32CF7AC5DA4F75FA72E`, `msgsec07.dat=78A29470ADA09DE20E4A90D4D4B5CF48A8D2BF6D8AB7DBD8A95923F9780E2FB2`.
- **2026-08-30 사용자 Citra 확인:** `동탁`이 정상 한글로 표시되고, 인사→수색 진입 시 v51 compact prompt `수색할 무장?`가 정확히 출력됐다. 따라서 `탁=8B45/physical1027` 수정과 `msgsec07` flat-byte header[153] 복구는 런타임 성공으로 승격한다.

## 수색 자연문구 append-only 시험 v52-intermediate (2026-08-30)

- 사용자 요청에 따라 Citra 확인된 `수색할 무장?` 대신 자연스러운 권위 번역 `누구에게 수색을 시키겠습니까?`를 런타임에 그대로 표시할 수 있는지 재검토했다.
- 전체 문구는 control `05 05` 포함 31바이트, 최대 줄폭 15칸으로 UI 폭 20칸 이내다. 기존 16바이트 source slot에는 들어가지 않지만, v51에서 header[153]이 해당 fragment를 직접 가리킨다는 것이 Citra로 입증됐으므로 기존 body를 다시 repack할 필요는 없다.
- v52는 **append-only relocation**을 사용한다. v51 `msgsec07.dat`의 기존 10,448바이트는 header[153] 2바이트를 제외하고 byte-exact로 유지하고, EOF `0x28D0`에 전체 문구 31바이트를 추가한 뒤 header[153]만 `0x13DA -> 0x28D0`으로 변경한다. 기존 compact prompt와 다른 386개 header pointer 및 모든 기존 body byte는 그대로 남긴다.
- 이 방식은 후속 문자열 주소를 하나도 이동하지 않으므로 v21식 전역 repack 회귀 가능성을 최소화한다. 새 파일 크기는 10,479바이트(+31B)다.
- 빌더: `tools\build_sangokushi2_v52_search_full_prompt.py`; report: `analysis\v52_search_full_prompt_report.json`. sealed v50/v21 자료에서 v51 msgsec07을 내부 재생성한 뒤 v52를 만드는 방식으로 두 번 연속 실행해 동일 출력 해시를 확인했다.
- v52 SHA-256: `msgsec07.dat=E9C2A26162ADA768C54E8B45940BBD1B43B5D1F9329C408C68076C32697DD372`, report=`A6F1AB443F498EEF16AFFA0D9C5E387690423A4813FE5503B7DD43F9D3A111BF`. `font.g1t`와 `fix_data.bin`은 v51과 byte-exact다.
- **Citra 확인 완료:** v52에서 수색 화면 상단에 `누구에게 수색을 시키겠습니까?`가 정확히 한 줄로 정상 표시됐다. 따라서 `msgsec07`의 짧은 기존 slot을 억지로 확장하지 않고, EOF에 새 문구를 append한 뒤 해당 runtime header pointer 한 개만 새 absolute byte offset으로 전환하는 방식이 실제 런타임에서 성공한 것으로 확정한다. 기존 v51 body/다른 386개 포인터를 유지하는 최소 수정 방식이므로 향후 동일 구조의 `msgsec07` 단일 문구 확장 시 우선 검토한다.
- **다음 Citra 중간 확인:** (1) 군주 선택의 `동탁`이 정확히 표시되는지, 가능하면 `탁응`도 확인, (2) `종회/종진/진응/이응` 등 `종/응` 포함 이름이 정상인지 표본 확인, (3) 인사→수색 진입 시 상단 문구가 `수색할 무장？`으로 표시되고 `노력이 될 듯합니다...`가 사라졌는지, (4) 기존 msgsec07 공통 대사가 역으로 깨지지 않았는지 간단 확인. 성공 전에는 남은 43자 위험군을 일괄 수정하지 않는다.

## 시나리오·인물 간 대사 한글화 — 새 checkpoint 0 / v52 기준 동결 (2026-08-30)

- 직전 이름·수색 수정은 다른 작업에서 완료됐다는 사용자 확인에 따라, 대사 작업은 Citra 확인된 `v52-intermediate` Patch 전체 30파일을 새 불변 기준으로 시작한다.
- `msgsec07.dat=E9C2A26162ADA768C54E8B45940BBD1B43B5D1F9329C408C68076C32697DD372`, `font.g1t=6C7C13453E61EAFB03094F6BFBF0C840E1822A19CAD1A28CBF3388193A87ECCC`, `fix_data.bin=126279344D75454A7617EDE997ED8FE75B1D35B652B8E32CF7AC5DA4F75FA72E`가 v51/v52 보고서와 일치함을 확인했다.
- 전체 Patch byte-exact 사본은 `analysis\dialogue_translation_work\checkpoint_00_v52_dialogue_baseline\PatchSnapshot`에 봉인했다. 별도 누적 작업 트리는 `analysis\dialogue_translation_work\staging_v52_dialogue`이며 생성 직후 전체 manifest가 Patch와 동일하다.
- 인물 데이터는 완료 상태로 동결한다. `fix_data.bin`은 대사 작업 내내 변경 금지이며, 기존 이름 alias와 글리프가 깨지지 않도록 시작 해시와 최종 검증을 강제한다. 대사에 새 글리프가 필요할 때도 이름 필드나 기존 이름 글리프를 재할당하지 않는다.
- 범위는 아직 한글화되지 않은 **시나리오 대사와 인물 간 대사**다. 튜토리얼, 인물 열전, UI/버튼, 순수 인물 이름 테이블은 제외한다. HTML/JSON의 `translation_ko`를 번역 권위로 삼되 실제 런타임 소스 위치·제어코드·용량을 먼저 확인한다.
- 전체 번역 완료 전에는 `Sangokushi 2 Patch`를 갱신하거나 정식 Revision을 발행하지 않는다. 약 20%마다 staging 산출물, manifest, 대응표, 검증 결과를 별도 checkpoint로 백업하고 MD 기록을 누적한다.

## 시나리오·인물 간 대사 한글화 — checkpoint 20 (2026-08-30)

- Original `code.bin`의 CP932 NUL 문자열 2,575개를 전수 스캔하고, 실제 대사 후보를 현재 Patch와 HTML/JSON 번역에 대조했다. UI 용어 9건과 데이터 압축/쓰기 실패 시스템 오류 2건을 제외한 범위는 322건이며, v52 이전에 이미 수정된 32건을 뺀 시작 미번역 대상은 **290건**이다.
- 첫 묶음으로 58건(정확히 20.0%)을 `staging_v52_dialogue`에만 적용했다. HTML `translation_ko`와 정확히 대응하고 원 슬롯에 들어가는 50건, HTML 의미를 유지하면서 원 슬롯에 맞게 최소 압축한 8건이다.
- HTML의 명백한 띄어쓰기/오타(`업을이루어`, `자각오`, `깊이감복` 등)는 런타임 staging 문구에서만 바로잡았다. 최종 권위 JSON 동기화는 전체 대사 검토와 런타임 확인 뒤에 수행한다.
- 모든 문구는 original `code.bin` NUL 슬롯 안에 fixed-position으로 삽입했다. 대상 58개 슬롯 밖 code diff=0, `%s/%d` 서식 토큰 보존, 각 줄 20칸 이하, 슬롯 overflow=0을 확인했다.
- 대사에 필요한 신규 한글 17자는 historical alias·live source alias·기존 이름 alias·수직 인접 cell을 피한 fresh safe donor에 배치했다. target alias 공유 충돌=0, packed font readback pixel-exact, 새 17개 cell 밖 font pixel diff=0이다.
- 완료된 인물 데이터 `fix_data.bin=126279344D75454A7617EDE997ED8FE75B1D35B652B8E32CF7AC5DA4F75FA72E`와 v52 `msgsec07.dat=E9C2A26162ADA768C54E8B45940BBD1B43B5D1F9329C408C68076C32697DD372`는 시작점과 byte-exact다. `Sangokushi 2 Patch` 전체 manifest도 checkpoint 0과 동일하다.
- 결정적 재실행에서 staging/report 해시가 동일했다. staging `code.bin=0F49169D58D1F25405E44A6482A854A949ACAB7175FCE0E9789A2920FAA98605`, `font.g1t=49C39C311915D3CA5104A67754C32D5609F5921F75D01E493C290A8BF8BD24B7`, report=`FB6DEED895740B098C08E92635DD945575B6DEAFFE22AF0A3B2886A1DE0941D3`.
- 백업: `analysis\dialogue_translation_work\checkpoint_20_v52_dialogue`. 정식 Revision은 아직 발행하지 않았다.

## 시나리오·인물 간 대사 한글화 — checkpoint 40 (2026-08-30)

- checkpoint 20의 봉인본을 유일한 베이스로 사용해 HTML 대응 대사 58건을 추가 적용했다. 누적 116/290건, coverage 40.0%다.
- 두 번째 묶음은 HTML target이 원 슬롯을 2~6바이트 초과한 항목을 의미 보존형으로 압축했다. 명백한 HTML artifact `Se3덕...`은 `무덕한 자는 덕 있는 이에게 / 천하를 넘기시오`로 복구했다.
- 새 표현은 `%s/%d` 토큰을 원문과 정확히 보존하고 모든 줄을 20칸 이하로 제한했다. 58개 대상 슬롯 밖 `code.bin` diff=0, 모든 문자열 원 슬롯 이내, NUL 경계 유지다.
- 기존 map에서 target에 걸린 legacy alias 충돌은 표현을 바꾸거나 fresh-safe 분리했다. 이 묶음에서 실제 신규 glyph는 `찌` 1자뿐이며 packed readback pixel-exact, 해당 cell 밖 font pixel diff=0이다.
- 누적 staging SHA-256: `code.bin=936AE91A6E624406BDA4297532B782B782F51585C678B206911308D49AEB36BE`, `font.g1t=7E589042A6A692B15BC1040E097C2446F3B9CA5BFC883B952DE2740465FFD9F8`, report=`057E167B7B4A7ED8186466D23F312733AF4E9FEEBC0C7266889C97B30F8D86E6`.
- `fix_data.bin`과 v52 `msgsec07.dat`는 checkpoint 0과 byte-exact이며 실제 Patch 전체도 v52 manifest와 동일하다. 빌더/report 2회 결정적 재실행을 통과했다.
- 백업: `analysis\dialogue_translation_work\checkpoint_40_v52_dialogue`. 정식 Revision은 아직 발행하지 않았다.

## 시나리오·인물 간 대사 한글화 — checkpoint 60 (2026-08-30)

- checkpoint 40 봉인본에서 58건을 추가해 누적 174/290건(60.0%)을 staging에 적용했다.
- 이번 묶음은 남아 있던 HTML 정확 대응 대사 48건과, HTML에 대응 행이 없는 짧은 code-only 런타임 fragment 10건이다. code-only 항목은 일본어 원문과 앞뒤 결합 조사를 고려해 직접 번역했다.
- 수동 fragment 예: `동맹을 / 맺고`, `교환을 / 승낙해`, `옥새를 / 담보로`, `와룡을 / 또 찾아`, `와룡 초려를 / 찾아`, `에게 / 간파당해`.
- 원 슬롯 fixed-position, `%s` 보존, 20칸 이하, 대상 58개 슬롯 밖 code diff=0을 통과했다. 누적 이전 116개 대사 바이트는 checkpoint 40 베이스에서 그대로 보존된다.
- 신규 glyph는 `켰` 1자이며 packed readback pixel-exact, 신규 cell 밖 font pixel diff=0, target alias collision=0이다.
- staging SHA-256: `code.bin=54CD5454242A089019176C9BDD061E6611769FD420500EB0EF697FD397F3DE4C`, `font.g1t=0E1075C883D8874062E0DAF5A394CA4A945C7D3F061E62D69387161AC2897B1F`, report=`27F01276CAD9BC42A71A1A875D686381D9B2AA30EFA2E69905AF549FA16530EB`.
- `fix_data.bin`, v52 `msgsec07.dat`, 실제 Patch 전체는 checkpoint 0과 byte-exact다. 빌더/report 2회 결정적 재실행을 통과했다.
- 백업: `analysis\dialogue_translation_work\checkpoint_60_v52_dialogue`. 정식 Revision은 아직 발행하지 않았다.

### 범위 보정 — code.bin 단계와 direct Message 단계를 분리

- 위 checkpoint의 20/40/60%는 **`code.bin` 런타임 대사 풀 290건만의 coverage**다. 전체 시나리오·인물 간 대사 완료율로 해석하지 않는다.
- direct runtime Message 소스는 기존 v20/v21 빌더와 code 중복 대응을 근거로 `msgsec04/06/07/08.dat`를 별도 inventory했다. HTML 권위 번역이 있는 fragment는 957건(336 parent group)이다.
- v52 이전에 적용된 것은 v21 common dialogue 85 fragment와 v52 search prompt 1 fragment, 총 86건이다. 아직 direct Message에 남은 것은 **871 fragment / pending parent 302 + partial parent 1**이다.
- 파일별 권위 fragment: `msgsec04=70`, `msgsec06=212`, `msgsec07=493`, `msgsec08=182`. `msgsec04/06/08` Patch는 Original과 byte-exact이고, `msgsec07`만 v21 repack+v51 header repair+v52 append 상태다.
- inventory: `analysis\dialogue_translation_work\direct_message_dialogue_inventory.json/.tsv`. direct Message는 파일별 identity/구조 검증과 parent 단위 제어코드 재구성이 끝난 뒤 별도 staging checkpoint로 적용한다.

## 시나리오·인물 간 대사 한글화 — checkpoint 80 (2026-08-30)

- checkpoint 60 봉인본에서 code-only 원문 대사 58건을 직접 번역해 누적 **232/290건(80.0%)**을 staging에 적용했다. 이 수치는 `code.bin` 대사 풀만의 coverage이며 direct Message 완료율이 아니다.
- 사용자의 지적에 따라 donor 절약을 이유로 띄어쓰기나 문장을 부자연스럽게 훼손하지 않는 원칙을 확정했다. 자연스러운 번역을 우선하고, 필요한 신규 글리프는 아직 참조되는 일본어·historical alias·기존 이름 alias·수직 인접 cell을 제외한 **검증된 일본어 donor**에만 배치한다.
- 이번 신규 글리프 10자는 `벅/찰/났/슨/떠/곁/낳/폐/랍/넣`이며 alias는 각각 `996D/996F/9971/9972/9975/9978/997A/997B/997C/997D`다. target alias 공유 충돌=0, packed readback pixel-exact, 신규 10개 cell 밖 font pixel diff=0을 통과했다.
- 모든 58건은 원 슬롯 fixed-position, 20칸 이하, `%s` 보존, 대상 슬롯 밖 code diff=0이다. 앞선 checkpoint 20/40/60의 174개 대사 바이트도 byte-exact로 유지했다.
- 결정적 재빌드 및 봉인을 두 번 검증했다. staging SHA-256: `code.bin=BA368B7AB65FDA9473EB05B2B623F00355E9D01F0B9ACC6231DAE6584759E80A`, `font.g1t=36D77D87AA9C3B223A3B48F680E7C5739B2C170AAC996C78AE49542D300081AC`, report=`7E2D2C1200D1EBFF03221DEA639B447B274B2E9DFE0A06A18540685FA66667FD`.
- `fix_data.bin`, v52 `msgsec07.dat`, 실제 `Sangokushi 2 Patch` 전체는 checkpoint 0과 byte-exact다. 백업: `analysis\dialogue_translation_work\checkpoint_80_v52_dialogue`. 정식 Revision은 아직 발행하지 않았다.

## 시나리오·인물 간 대사 한글화 — corrected code.bin checkpoint 100 (2026-08-30)

- checkpoint 80 뒤에 남은 58건을 재분류했다. 이 중 `3DS 모드/사기/전기/특성/속성 레벨` 설명 8건은 사용자 지정 범위 밖의 **미적용 튜토리얼**이므로 원문 그대로 제외했다. 앞선 232건에는 이 8건이 섞이지 않았고, 이미 번역된 튜토리얼을 삭제하거나 되돌린 변경도 없다.
- 보정된 `code.bin` 시나리오·인물 간 대사 범위는 282건이며, 잔여 50건을 번역해 **282/282건(100.0%)**을 staging에 적용했다. direct Message 871건은 별도 축이므로 전체 대사 작업은 아직 완료가 아니다.
- 이번 50건은 신규 글리프 없이 checkpoint 80 font를 byte-exact로 유지했다. 모든 target은 원 슬롯 fixed-position, 20칸 이하, 서식 토큰 보존, 대상 슬롯 밖 diff=0이며 앞선 232건도 보존됐다.
- staging SHA-256: `code.bin=75319C9AAE3C92FE6B062600CA249D33257365F8CD6EC4EE10AE87035AF57BA4`, `font.g1t=36D77D87AA9C3B223A3B48F680E7C5739B2C170AAC996C78AE49542D300081AC`, report=`7853142D065EAE90C852827173AA7DAFBDD4B55079C4AACD10FE5CEAF5BB4DB4`.
- 첫 `checkpoint_100_v52_code_dialogue` 봉인 시 빌더가 두 번째 실행을 허용하지 않는 base-equality 검사를 갖고 있어 결정성 재실행이 실패했다. 산출물 검사는 통과했으나 정상 봉인으로 인정하지 않고 실패 이력으로 보존했다. 빌더를 idempotent하게 수정한 후 두 번 연속 동일 해시를 확인한 권위 백업은 `analysis\dialogue_translation_work\checkpoint_100_v52_code_dialogue_corrected`다.
- 실제 Patch, 완료 인물 `fix_data.bin`, v52 `msgsec07.dat`는 계속 불변이며 정식 Revision은 없다.

## direct Message identity gate + msgsec04 완료 (2026-08-30)

- `msgsec04/06/08.dat`는 번역 0건 상태로 기존 mixed-header repacker를 실행했을 때 Original·v52 Patch·corrected-code staging에서 모두 byte-exact identity rebuild를 통과했다. 각 파일의 모든 pointer segment가 reconstructible이고 opaque/padding 추가가 0임을 `analysis\dialogue_translation_work\direct_message_identity_report.json`에 기록했다. `msgsec07`은 387개 absolute-byte pointer 특수 구조이므로 이 identity 경로에서 의도적으로 제외했다.
- 첫 direct Message 묶음으로 `msgsec04.dat`의 미적용 **70 fragment / 24 parent group 전부**를 staging에 적용했다. HTML `translation_ko`를 기본 권위로 사용했고 미지원 글리프 5건은 의미와 자연스러움을 유지한 표현으로 바꿨다.
- 모든 fragment의 제어 토큰 순서 일치, 최대 20칸, parent 완결성, mixed-header metadata 보존, 새 pointer 기반 segment readback을 통과했다. 신규 glyph는 0이며 completed `code.bin`·font·`fix_data.bin`·v52 `msgsec07`은 byte-exact다.
- direct Message 축은 시작 871건 중 70건 적용, **801건 잔여**다. staging `msgsec04.dat=80DAC07EAD1BFD70C6244DE6F4666DF225A75E0BAA3D3139EB3A3C41E1AFA244`, report=`2CC5F0450B5A5C99D0C28D56B3A7D4D9C271D84836CC580046108ADD8551DD05`.
- 결정적 재빌드 2회 및 봉인 재검증 완료. 권위 백업: `analysis\dialogue_translation_work\checkpoint_direct_msg04_v52_dialogue`. 실제 Patch/정식 Revision은 아직 변경하지 않았다.
- 다음 `msgsec06`의 사전 검수에서 미지원 글리프 15건, 제어 토큰 누락 2건, 21칸 문장 1건을 식별하고 자연스러운 대체문을 `analysis\dialogue_translation_work\direct_msg06_review_overrides.json`에 저장했다. 아직 msgsec06 staging에는 적용하지 않았다.

## direct Message msgsec06 완료 (2026-08-30)

- `msgsec06.dat` 미적용 212 fragment / 75 parent group 전부를 staging에 적용했다. 사전 검수 18건은 제어 토큰 복원, 20칸 이내 압축, 기존 font로 자연스럽게 표시되는 문구로 교정했다.
- mixed-header metadata 보존, 새 pointer 기반 75개 segment readback, 제어 토큰, line width, alias 충돌, frozen file 검사를 통과했다. 신규 glyph는 0이다.
- direct Message 누적 282/871건, 잔여 589건이다. staging `msgsec06=069068FC4F2DD21DC13ADC8B558D6B0FFB719C16EA736BC1296273C3DA439060`, report=`FF26116CB636F13F15C5584D4E38CAF35126ADB305A3F799A8B79479C8CB7EAA`.
- 결정적 재빌드 2회와 봉인 재검증 완료. checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg06_v52_dialogue`. 실제 Patch/정식 Revision은 불변이다.

## direct Message msgsec08 완료 (2026-08-30)

- `msgsec08.dat` 미적용 182 fragment / 60 parent group 전부를 staging에 적용했다. 사전 검수에서 미지원 글리프·제어 토큰·문장 폭 문제가 있던 29건은 원문 의미와 자연스러움을 유지하면서 현재 font로 안전하게 표시되도록 교정했다.
- mixed-header metadata 보존, 새 pointer 기반 60개 segment readback, 원 제어 토큰 순서, 최대 20칸, alias 충돌 0, frozen file 검사를 통과했다. 신규 glyph는 0이다.
- direct Message 누적 464/871건, 잔여 407건이며 전부 특수 absolute-byte pointer 구조의 `msgsec07.dat`다. staging `msgsec08=18E4B7D027019961B26784C319CF73FB9E38AAA4A323BF8F8BB713157E39E4E2`, report=`6C5856CBE8291C1D63936008AD3E11BA8132C8C43E2BFA16EC3FF46D8BC649DF`.
- 결정적 재빌드 2회와 봉인 재검증 완료. checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg08_v52_dialogue`. 실제 Patch/정식 Revision은 계속 불변이다.

## direct Message msgsec07 완료 및 전체 봉인 (2026-08-30)

- 잔여 407건을 포함해 `msgsec07.dat` 권위 번역 493 fragment / 177 parent group 전체를 Original 구조에서 다시 만들었다. v52 시작 시 이미 적용됐던 86건도 현재 HTML/JSON 권위로 재생성했으며 direct Message 축은 **871/871 신규 대상 완료, 최종 권위 957/957**다.
- `msgsec07`은 generic mixed-header가 아니다. 387개 absolute byte pointer를 전수 분류한 결과 345개는 fragment 시작, 41개는 fragment 내부, 1개는 다음 fragment 직전이다. 내부 41개 중 11개는 Shift-JIS 글자의 둘째 바이트에 걸친 중첩 저장이라 원 delta 재사용을 폐기했다.
- 472개 권위 fragment는 재구성 body에 직접 반영했다. generic repacker가 opaque로 분류한 9개 segment의 권위 fragment 21개는 모두 non-start pointer 대상임을 증명하고, 총 42개 non-start 런타임 suffix를 독립 한국어 문자열로 분리해 각 header word를 새 문자열 시작으로 전환했다. 모든 387개 pointer가 새 파일 내부를 가리키고 42개 target readback이 정확하다.
- 원 제어 토큰, 최대 20칸, 177 parent 완결성, 수색 전체 문구 권위 재삽입을 통과했다. 자연스러운 번역을 우선해 검수 가능한 표현만 다듬었고, `수춘`·`청낭서` 등 고유명사는 유지했다.
- provisional 최종 런타임 전체에서 참조 0인 일본어 donor만 재사용했다. 신규/분리 글리프는 `춘=94A3/physical2812`, `돼=95DE/3059`, `엣=9941/3655`, `낭=9944/3658` 4자다. 다른 runtime 파일 참조 0, msg07 최종 참조 존재, alias 공유 0, packed pixel readback exact, target cell 밖 pixel diff 0이다.
- staging SHA-256: `msgsec07=49A4EC0B6269C302A34046523413E770F613A49EAB793C25204E67E577F200EC`, `font.g1t=5E753A7C0A33916BC6692C5A229DF454C2DF63E2605CC7AB22266989BD07C37F`, report=`BA48B8F7395C8442C39A16D215567D53FA0FFD1B9F9BF8B8EA0B4AB548661386`.
- 결정적 재빌드 2회와 봉인 검증 완료. 권위 checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg07_v52_dialogue`.
- `direct_message_msg07_preflight_report.json`의 `pass=false`는 최종 실패가 아니라 **신규 glyph 배정 전 진단 기록**이다. 당시 미지원 4문자·기존 `돼/롭` alias 충돌·opaque 21건을 의도적으로 검출했고, 이 세 항목은 최종 staging report와 v53 report에서 각각 zero-reference donor 4개, `돼` 분리, 42개 pointer detangle로 해소됐다. 최종 권위는 `direct_message_msg07_staging_report.json(pass=true)`와 checkpoint manifest다.

## 시나리오·인물 간 대사 완료 정식 revision v53 (2026-08-30)

- 전체 대사 축을 교차 검증한 뒤 sealed final staging을 `Sangokushi 2 Patch`에 발행했다. `code.bin` 대사 282/282, direct Message 권위 957/957(`msg04=70`, `msg06=212`, `msg07=493`, `msg08=182`) 완료다.
- v52 대비 변경 파일은 정확히 6개다: `ExeFS\code.bin`, `RomFS\Common\Font\font.g1t`, `RomFS\Message\msgsec04/06/07/08.dat`. 인물 데이터 `fix_data.bin`과 나머지 24개 Patch 파일은 v52와 byte-exact다.
- Patch 전체 30파일은 `Backup\Sangokushi 2 Patch - v53`에 백업했고 manifest가 Patch와 동일하다. `Sangokushi 2 Rebuild`는 수정하지 않았다.
- SHA-256: `code.bin=75319C9AAE3C92FE6B062600CA249D33257365F8CD6EC4EE10AE87035AF57BA4`, `font.g1t=5E753A7C0A33916BC6692C5A229DF454C2DF63E2605CC7AB22266989BD07C37F`, `msg04=80DAC07EAD1BFD70C6244DE6F4666DF225A75E0BAA3D3139EB3A3C41E1AFA244`, `msg06=069068FC4F2DD21DC13ADC8B558D6B0FFB719C16EA736BC1296273C3DA439060`, `msg07=49A4EC0B6269C302A34046523413E770F613A49EAB793C25204E67E577F200EC`, `msg08=18E4B7D027019961B26784C319CF73FB9E38AAA4A323BF8F8BB713157E39E4E2`.
- 발행 report: `analysis\v53_dialogue_complete_report.json`, SHA-256 `97D9BBCBC1CEF7F79D2392E3F89978E0C3BA3CF5376EA622C99D3524DB6BF821`. 빌더 연속 실행과 Patch/Backup 재검증에서 동일했다. 정적·결정적 검증은 완료됐으며 실제 화면 Citra 확인은 다음 단계다.

## v53 Citra 버그 수정 1차 — v54-intermediate (2026-08-30)

- 사용자 Citra 확인에서 v53 대규모 대사 적용 후 5개 문제를 확인했다: (1) `주시겠습니까?` 끝 ASCII 물음표 찌그러짐, (2) `民들도 매우 / 좋아할 것입니다`의 선행 한자 `民`, (3) `민충성도가 83이 되었습니다` 조사 표현, (4) `수색하시겠습니까?` 끝 물음표 찌그러짐, (5) 게임 시작 직후 무관한 초선 `…할 말이 있다 합니다 / 들어보시겠습니까?` 대사가 노출됨.
- 1·4번은 과거 v30과 동일한 원인이다. 원 일본어는 CP932 전각 `？=81 48`인데 v53 권위 번역이 ASCII `?=3F`를 사용해 Citra에서 찌그러졌다. `msgsec07` header[172]/[154]를 각각 EOF의 `주시겠습니까？`/`수색하시겠습니까？` 독립 문자열로 전환했다. 두 문자열 모두 packed bytes가 `81 48`로 끝나는 것을 gate로 확인했다.
- 2번은 `msgsec07_0072_002`의 기술 fragment `民`과 다음 `msgsec07_0073_000`의 `들도 매우…`가 런타임에서 연속 출력되는 split 구조다. source technical fragment를 억지로 늘리지 않고 header[176]을 EOF의 전체 문구 `백성들도 매우<$0A>좋아할 것입니다`로 append-only relocation했다.
- 3번은 header[178]을 EOF의 `민충성도가 %d 이(가) 되었습니다`로 옮겼다. `%d`는 그대로 보존되어 실제 수치가 들어가며, 최대 16칸으로 20칸 제한 이내다. `sangokushi2_translation.json`/`text_review.html` 권위값도 이 표현으로 동기화했다.
- 5번의 근본 원인은 `msgsec06.dat` 헤더 오판이다. 원본은 word0=161, word1~160이 모두 absolute byte pointer이며 156개는 fragment 시작, 4개는 fragment 내부다. v53 msg06 generic repacker는 앞 75개를 word-offset segment pointer로 재작성했다(`header[1] Original=0x0144`, v53=`0x0177`). 이 때문에 런타임 문자열 lookup이 번역 body의 엉뚱한 위치를 읽을 수 있었다.
- v54는 v53 msg06 translated body 5,724B를 byte-exact로 보존하고 156개 시작 포인터를 새 fragment 시작으로 모두 복구했다. 내부 4개(header 1/62/81/122)는 일본어 byte delta를 한글에 재사용하지 않고 의미상 runtime suffix를 EOF에 독립 append하여 각각 `사자를 보내시겠습니까?`, `분명 원조해/줄 것입니다`, `우리 나라를 원한다면/싸워서 빼앗아라`, `일<$91>`을 가리키게 했다. 새 msg06 크기는 5,828B다.
- v54의 msg07도 formal v53 body 15,171B를 byte-exact로 보존하며 4개 header entry만 새 EOF 문자열로 전환했다. 다른 383개 포인터와 기존 body는 그대로이며 새 크기는 15,283B다.
- Patch에서 실제 변경된 게임 파일은 정확히 `RomFS\Message\msgsec06.dat`, `RomFS\Message\msgsec07.dat` 두 개뿐이다. `code.bin`, `font.g1t`, `fix_data.bin`, msg04/msg08, 이미지 등은 formal v53과 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 도구: `tools\sangokushi2_msgsec06_flat_header.py`, `tools\build_sangokushi2_v54_runtime_bugfix_batch1.py`; report: `analysis\v54_runtime_bugfix_batch1_report.json`. 빌더 2회 연속 실행에서 동일 결과를 확인했다.
- v54 SHA-256: `msgsec06.dat=7E661C766AAF6152D6B9D533F0CC9CEA9C40BBFCE185F2E24B1277A80F1D1A9B`, `msgsec07.dat=9DA3264D349410B82EDF858DEB7D198CAC8D3C8129A3AA8516E71C62B5AC22BE`, report=`FADF7EDEC752854918C31634F632E6018FFAA802FB98494288B4602F6866A353`.
- **v54는 Citra 중간 확인 전용**이다. 우선 위 5개 화면만 재확인하고, 다음 버그 묶음은 이 v54 Patch를 베이스로 이어간다.

## 공통/기본 런타임 대사 정리 — v55-intermediate (2026-08-30)

- v54 이후 남은 기본/공통 일본어를 실제 런타임 기준으로 다시 점검했다. `sangokushi2_translation.json`의 `menu + translatable + translation_ko 빈 값`은 0건이었으므로, 잔존 일본어의 주원인은 번역 누락보다 **하드코딩 `code.bin` 공통 UI와 flat-byte pointer가 가리키는 technical/split fragment**였다.
- 강노 구입 화면의 `誰に取引させますか？`는 Message가 아니라 `ExeFS/code.bin` `0x1778E0`의 하드코딩 공통 UI임을 확정했다. 기존 code-text extractor pool 밖의 NUL-terminated CP932 문자열까지 재스캔해 선택/확인/거래/외교/정보/인사/수송/건조/결과 계열 **144개 공통 런타임 문자열**을 고정 슬롯에서 한글화했다. `%s/%d` 등 포맷 토큰과 NUL 경계는 자동 검증했다.
- `msgsec04.dat` 원본은 word0=55, word1~54 absolute BYTE pointer(51 fragment-start + 3 inside-fragment), `msgsec08.dat`는 word0=128, word1~127 absolute BYTE pointer(125 + 2)임을 새로 확정했다. v53 generic repacker가 이 두 파일도 잘못 재작성한 상태였으므로 formal-v53 translated body는 byte-exact로 보존하고 flat header를 복구했으며 inside-fragment pointer는 독립 EOF 한국어 문자열로 detangle했다.
- `msgsec06/msgsec07`도 v54 보정본을 재현한 뒤, 일본어 한 글자/접두 조각이 직접 노출될 수 있는 공통 runtime pointer만 append-only 한국어 target으로 추가 전환했다. 게임 파일 변경은 `code.bin`, `msgsec04/06/07/08.dat` 5개다.
- v55 SHA-256: `code.bin=4ABC4C23653969E85D62BBF973E62463E8D755E7E237397B0EADD67D01627627`, `msg04=EDDBF5C382097CE95C27808F12F6DED3E782F08A8010FF8C36C4C4B88D1928E6`, `msg06=6D82D4819AD0ABF91668F85E43C7D20A60D66EFEED22BB4F0AB0A4DA58D728C9`, `msg07=EBB50A5BB7B8476519737181ADF6CEF0F48B6FF8396E00967F665E9F7107A4D1`, `msg08=812F78EE6F0CF82125381CC7F72525201863D66592942793ECF6C4FDCC85D863`.
- 사용자가 Citra에서 시혜의 `누가 합니까?`와 상인 거래의 `누가 거래합니까?`가 정상 출력된다고 확인했다. 따라서 v55의 해당 공통 UI 경로는 런타임 성공 사례로 기록한다.

## UI 후속 보정 + 46개 도시명 — v56-intermediate (2026-08-30)

- 사용자 Citra 확인 후 남은 5개 묶음을 v55 기준으로 처리했다: (1) 시혜 쌀 수량, (2) 인사→임명 상단/버튼, (3) 게임 시작 문구, (4) 매복의 독 도시 선택 상단, (5) 46개 도시 표시명.
- `code.bin` 수량 문자열은 `0x154D52/0x154D68`의 `金をいくら%sますか？`, `0x154F48`의 `米をいくら%sますか？` 세 슬롯이다. 각각 20B 고정 슬롯 안에 `금을 얼마나 줍니까？` / `쌀을 얼마나 줍니까？`를 self-contained로 넣어 원 `%s` 동사 인자를 화면에 사용하지 않도록 했고, 슬롯 밖 diff=0을 검증했다.
- 인사→임명 상단 원문은 `msgsec10_0027_001`, file offset `0xE15`, `政治ﾏﾀﾊ知力ｶﾞ８０以上。`다. 자연 번역 권위는 `정치 또는 지력이 80 이상.`으로 유지하되, 23B fixed-position 슬롯에는 `정치 또는 지력 80 이상.`을 정확히 23B로 넣었다. `msgsec10`은 재패킹하지 않았고 파일 크기/header/다른 fragment는 byte-exact다. `code.bin` 버튼 `将軍/武官/文官/太守`는 `장군/무관/문관/태수`로 수정했다(`군사`는 기존 정상).
- 게임 시작 원본 구조는 `0x185068 それではゲーム%sです` + 인자 `0x18505C スタート`다. `スタート` 뒤 다음 문자열까지 12B 슬롯의 padding이 0임을 확인한 뒤 인자를 `시작합니다`, format을 `그럼 게임을 %s.`로 바꿔 최종 조합이 정확히 `그럼 게임을 시작합니다.`가 되도록 했다.
- 매복 도시 선택의 깨짐은 번역 오타가 아니라 **Shift-JIS 문자 경계 오판**이었다. `msgsec07_0109_001` 끝 `0x96`과 다음 fragment 시작 `0x84`가 합쳐져 `埋(0x9684)`인데 extractor가 `<$96>` control로 오인했다. 원문 의미는 `ドコヘ埋伏サセマスカ？`. v56은 header[249]만 EOF `어디에 매복시키겠습니까？`로 append-only relocation했고 기존 v55 msg07 body는 byte-exact로 보존했다. 새 pointer는 `0x3D97`(15767), msg07 크기는 15767→15797B다.
- 도시 표시명은 `RomFS/Scenario/fix_data.bin` offset `0x1C`, stride `0x34`(52B), 총 46레코드의 첫 9B 필드임을 확정했다. 양평~남해 46개를 한글화했으며 변경 바이트 173, 도시 필드 밖 diff=0, 46개 독음 필드 byte-exact, record46 이후 byte-exact다. 기존 1,020명 인물 이름/독음 영역도 보존됐다. `鄴/下邳/譙/梓潼`의 game-private glyph raw도 원본 필드 기준으로 검증했다.
- v56에서 새로 쓴 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `msgsec10.dat`, `RomFS/Scenario/fix_data.bin` 4개다. v55의 `msgsec04/06/08.dat`는 byte-exact로 계승했다. `font.g1t` 신규 변경/신규 글리프는 없다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v56_ui_city_cleanup.py`를 연속 2회 실행해 동일 SHA-256을 확인했다. report `analysis/v56_ui_city_cleanup_report.json`, target inventory `analysis/v56_ui_city_targets.json`.
- v56 SHA-256: `code.bin=461B477B4DED299F2F195FFF438626E9FDCCCE862A7086292A7F85603D7F3A86`, `msg04=EDDBF5C382097CE95C27808F12F6DED3E782F08A8010FF8C36C4C4B88D1928E6`, `msg06=6D82D4819AD0ABF91668F85E43C7D20A60D66EFEED22BB4F0AB0A4DA58D728C9`, `msg07=455874C0701A3BE4785B0ADCC18917EE8CC66B3415A2A8402BB876ECDD31DADC`, `msg08=812F78EE6F0CF82125381CC7F72525201863D66592942793ECF6C4FDCC85D863`, `msg10=784B740717ABD5CB034752A2E187862F91167159A7034CAE045EF207CFE8F251`, `fix_data=E1C386E4F27CE8D183F5D189FB19F3F9DC6874DE26D8D03BC340C7FC5141C828`.
- **v56은 Citra 중간 확인 대기 상태**다. 우선 `쌀을 얼마나 줍니까？`, 임명 상단/5버튼, `그럼 게임을 시작합니다.`, `어디에 매복시키겠습니까？`, 지도/화면의 46개 도시명을 확인한다.

## 외교/정보/특산/상인 부분 보정 — v57-intermediate (2026-08-30)

- 사용자 실화면 6개 묶음을 v56 위에 최소 수정했다: (1) 공동작전 대상 선택 줄넘김, (2) 외교→교환 `무엇을 내놓습니까？`의 `엇→첨` 런타임 글리프 오류, (3) 교환 노/강노/군마 수량의 일본어 잔존, (4) 정보→타국 상단, (5) 도시→특산품 공통 문구, (6) 상인 병량/노/강노/군마 구매·판매 수량 상단.
- 공동작전 `code.bin 0x17FAD8`은 기존 `어느 군주에게\n공동작전을 제안합니까?`에서 사용자 2안인 `어느 군주에게 제안합니까？`로 변경했다. 36B 고정 슬롯 안에서 처리해 강제 개행을 제거했다.
- 외교→교환 `0x1D08EC`의 바이트는 실제로 `무엇을 내놓습니까？`였지만, Citra 화면에서 `엇`이 정확히 `첨`으로 출력됐다. 기존 `엇=98FB`는 계산 physical3652의 packed-font readback이 Sam3 `엇`과 pixel-exact였으므로 **정적 폰트 readback만으로는 far alias 런타임을 보증할 수 없다는 두 번째 실증 사례**다(v50 `탁=98A4`와 같은 계열). v57은 98FB 전역 remap을 하지 않고 이 화면만 fresh fallback `8AF9/physical1018`로 재인코딩했다. 8AF9는 original-valid, clean-v56 runtime ref=0, 과거 report alias 사용=0, 현재 한글 physical/±73 비점유를 gate로 확인했고 packed 재디코딩 및 target cell 밖 pixel diff=0을 통과했다. 다른 98FB 사용처는 후속 실화면 감사 대상으로 남긴다.
- 교환 수량은 `0x15446C`의 `%s` 인자 `가져가기→가져갑니까`, `0x154FC8 弩をいくら%sますか？`, `0x151B60 強弩...`, `0x1554B0 軍馬...`를 각각 `노/강노/군마를 얼마나 %s？`로 바꿨다. 자동 조합 검증 결과 `노/강노/군마를 얼마나 가져갑니까？`가 정확히 생성된다. v56의 금/쌀 self-contained 수량 슬롯은 변경하지 않았다.
- 정보→타국 `0x16FA60`은 기존 `어디 정보입니까？`를 요청문 `어디의 정보를 볼까요？`로 변경했다. 인코딩 22B로 원 슬롯 용량 22B에 정확히 맞는다.
- 특산품은 `msgsec05.dat`가 word0=22이며 target runtime pointer가 header[19]=`0x01FC`, [20]=`0x020C`, [21]=`0x021F`임을 gate로 고정했다. 각 포인터를 EOF의 `이미 획득했습니다`, `먼저 조건을 만족시킵시다`, `수색해 봅시다`로 append-only 전환했고 기존 body는 byte-exact다. 크기 584→653B(+69).
- 상인 공통 수량은 v56 `msgsec07.dat`를 결정적으로 재현한 뒤 header[68,69,74,284,291,298]만 독립 한글 문자열로 전환했다. `쌀을 얼마나 파시겠습니까？`, `쌀을 얼마나 사시겠습니까？`, `노/강노/군마를 얼마나 사시겠습니까？`가 target이며 기존 body는 byte-exact다. 크기 15797→15989B(+192).
- v57에서 새로 쓴 게임 파일은 `RomFS/Common/Font/font.g1t`, `ExeFS/code.bin`, `RomFS/Message/msgsec05.dat`, `msgsec07.dat` 4개뿐이다. v56의 `msgsec04/06/08/10.dat`, `fix_data.bin`은 byte-exact 계승했다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v57_partial_ui_cleanup.py`와 target `analysis/v57_partial_ui_targets.json`을 작성했고, 빌더를 **연속 2회** 실행해 완전히 동일한 SHA-256을 확인했다. report는 `analysis/v57_partial_ui_cleanup_report.json`.
- v57 SHA-256: `font.g1t=F847D82788ABF1E5C4D764E484A41EFDF158AFE940C54478174236B1F356EFC3`, `code.bin=2388233CE850E67ADCC37EBCFFDE272BF0496D1A54BF1E22BC4A33CA99A66AFD`, `msg05=B408E5B6B101DDE3C6BC98C40E2B0023D006B3FA44AA9CEC234101FCB35AA3A3`, `msg07=33D3C58E8AF391985F5B0ABBAC5E49A4764D9A7401B5F997B09F0B7DCFB0701D`. 계승 해시: `msg04=EDDB...1928E6`, `msg06=6D82...728C9`, `msg08=812F...5D863`, `msg10=784B...F251`, `fix_data=E1C3...C828`.
- **v57은 Citra 중간 확인 대기 상태**다. 최우선은 `무엇을 내놓습니까？`의 `엇`이 더 이상 `첨`으로 보이지 않는지다. 이어 공동작전 1줄, 노/강노/군마 `가져갑니까`, `어디의 정보를 볼까요？`, 특산품 3문구, 상인 수량 문구를 확인한다.

## 도시/전장명 + 교환/요구 조립 + `엇` 전수 보정 — v58-intermediate (2026-08-30)

- 사용자 Citra 확인으로 v56 도시명은 단순 번역 누락이 아니라 **표시명에 쓰인 일부 far alias의 런타임 렌더 실패**가 섞인 문제임을 확인했다. `북평`은 `북=98DD`가 stock 글리프로 보였고, 추가 스크린샷에서 `홍농`도 `홍`만 한글이며 `농=98F2`가 한자로 출력되는 것이 명확히 확인됐다. 이전 `홍농 정상` 판정은 폐기했다.
- `fix_data.bin`의 46개 도시 표시명은 전부 다시 인코딩하되 전역 alias를 바꾸지 않고 표시명 전용 local fallback 7자를 사용한다: `북=89D0/physical789`, `릉=8BD3/1168`, `녕=8F7A/1832`, `농=956B/2945`, `곡=9391/2606`, `뢰=94D6/2863`, `새=9773/3329`. 후보는 historical alias=0, Message/Scenario live ref=0, code text ref=0, 현재 한글/±73 guard 비점유, packed glyph readback exact를 통과했다. `89D0/8BD3/956B`의 `code.bin` raw hit는 ARM 코드/데이터이며 NUL CP932 문자열이 아님을 별도 분류했다.
- 전장명 실테이블을 `fix_data.bin 0x970`, stride `0x30`(48B), 22개, 표시명 첫 7B로 확정했다. `백랑산/기산/서새산/역경/계교/관도/정도/오장원/가정/적도/회음/장판/이릉/합비/부성/유수구/적벽/호뢰관/함곡관/무관/호관/양평관`을 전부 한글화했다.
- 구조 재발방지: 46번째 도시 레코드의 명목상 끝은 `0x974`인데 전장 테이블이 `0x970`에서 시작해 4B 겹친다. 또한 22개 전장 레코드의 명목상 끝 `0xD90`은 인물 테이블 시작 `0xD78`을 지난다. 따라서 도시/전장 record 전체를 소유 영역으로 취급하지 않고 **확정된 표시명 필드만 writable**로 제한했다. v58 실제 `fix_data` 변경 115B는 도시 46×9B/전장 22×7B 허용 필드 안에만 존재하며 인물 표시명 영역은 byte-exact다.
- `엇=98FB`는 v57 한 화면의 예외가 아니라 한국어 런타임 여러 곳에 남아 있었다. v57 기준 `code.bin` 한국어 98FB 7곳, `msgsec06` 7곳, `msgsec07` 1곳, `msgsec08` 1곳을 모두 Citra 검증 fallback `8AF9`로 승격했다. v58 수정문 `무엇을 내놓습니까？`에도 8AF9가 사용되어 최종 `code.bin` 8AF9 한국어 위치는 8곳이다. Original `code.bin`에 원래 존재하던 raw 98FB 4곳(`0x1DB9/0x110BA8/0x16EB81/0x1C7349`)은 그대로 남겼고 effective Message/Scenario의 legacy 98FB는 0건이다.
- 외교/교환 `何を要求しますか？`는 proven allocation `0x1D0908..0x1D091B` 20B를 사용해 `무엇을 요구합니까？`(19B+NUL)로 완전하게 확장했다. `엇`은 새 runtime alias 8AF9를 사용한다.
- 자원 수량 오역의 원인은 v56에서 `金/米をいくら%sますか？`의 `%s` 동사 인자를 제거하고 `금을/쌀을 얼마나 줍니까？`라는 완성문으로 고정한 것이다. 원래 구조를 복구해 `금/쌀/노/강노/군마를 얼마나 %s니까？` + 동사 어간 `가져갑/줍/요구합/요청합`으로 조립하도록 했다. 5자원×4상황 20개를 자동 검증하여 `...가져갑니까？ / ...줍니까？ / ...요구합니까？ / ...요청합니까？`가 정확히 생성된다. v57에서 놓친 별도 `持って行き` 슬롯 `0x1D16A4`도 `가져갑`으로 함께 수정했다.
- v58에서 실제 새 write는 `font.g1t`, `code.bin`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `fix_data.bin` 6개다. `msgsec04/msgsec05/msgsec10` 및 그 밖의 v57 파일은 byte-exact 계승했다. Original/Rebuild/Backup은 수정하지 않았다.
- 실제 Patch readback: `북평@0x50=89D088F9`, `홍농@0x258=984C956B`, `건녕@0x8D8=88C68F7A`, `서새산@0x9D0=894E97738DDC`, `이릉@0xBB0=88BF8BD3`, `호뢰관@0xCA0=8E5894D68B6B`, `함곡관@0xCD0=8B9893918B6B`로 새 local fallback 사용을 확인했다.
- 빌더 `tools/build_sangokushi2_v58_city_battlefield_exchange.py`, inventory `analysis/v58_city_battlefield_exchange_targets.json`, report `analysis/v58_city_battlefield_exchange_report.json`. 게임 파일 빌드를 연속 실행해 동일 SHA-256을 확인했고 report도 재실행 전후 `2F2A7B77F6300CBDFB4B6395D05169EA58986702383C435A4FC7A92ED94C65EC`로 동일했다.
- v58 SHA-256: `code.bin=3B4A9D64AA61D0572C85D43386DF3CF16E00D525B32B2C15331C8856416BD3AF`, `font.g1t=B09CFFC42A3028AEE24908422224CF51F8D7388204654945FD8B56DBDBABC66C`, `msg06=4D6EBE87777990B720DFA37B3AAC8C0CAA56A64482BEE8A3148A90555068941A`, `msg07=4842C1A0B6CAE841337998603A8FCC04FDF6D851743BAC7DF3DEC88FABBE2CE1`, `msg08=3AC66CA36829DDF8016A132568E4F8244B66A0604D5DF2C9820B3C519D2E436E`, `fix_data=A00BB67145FA074EAC25E1195A12D65965233B196FA016A03FECE60478B940F5`.
- **v58은 Citra 중간 확인 대기 상태**다. 우선 `북평/홍농/강릉/무릉/영릉/건녕`과 전장 목록, `무엇을 내놓습니까？`, `무엇을 요구합니까？`, 금/쌀/노/강노/군마의 주기·가져가기·요구하기 문구를 집중 확인한다.

## 임명 화면 실제 runtime source + 경고/방랑 보정 — v59-intermediate (2026-08-30)

- 사용자 Citra에서 임명 화면 버튼은 `군사`만 한글이고 `将 軍 / 武 官 / 文 官 / 太 守`가 일본어로 남는 것을 확인했다. 이미지 버튼이 아니라 텍스트이며, 원인은 v56이 공백 없는 duplicate `将軍/武官/文官/太守`를 수정했던 반면 실제 임명 선택창은 **spaced C-string**을 참조한다는 점이었다.
- 실제 `code.bin` 역할명 pointer table `0x1E486C`를 역추적해 버튼 source를 확정했다: `軍 師@0x1D080C`, `将 軍@0x1D07E4`, `武 官@0x1D0834`, `文 官@0x1D083C`, `太 守@0x1D0844`. `군사`는 이미 한글 readback/Citra 성공 기준점이므로 gate-only로 보존하고 나머지 4개를 `장군/무관/문관/태수`로 수정했다.
- 신분 목록 `君主`는 `code.bin 0x1CEE2C`이며 status enum pointer table `0x1DB6C0`, `0x1DC2E4` 양쪽의 첫 항목이 이 위치를 가리키는 것을 확인해 `군주`로 수정했다.
- 기존 `누구를 %s 임명？`은 proven 24B allocation `0x16CE34..0x16CE4B` 안에서 사용자 요청 형식 **`%s 임명 대상을 선택`**으로 변경했다. 인코딩 19B + NUL로 relocation 없이 안전하며 장군 선택 시 `장군 임명 대상을 선택`로 조립된다.
- 임명 상단 자격 설명은 `msgsec10` header[67..71]의 실제 5개 fixed-position 블록을 모두 사용한다. 장군=`무력 85 이상. 또는 무력 70 이상, (육지휘+수지휘)÷2+매력=120 이상.`, 군사=`정치 또는 지력 80 이상.`, 무관=`무력 높은 무장에 적합. 개발·외교 불가.`, 문관=`지력·정치 높은 무장에 적합. 전쟁 불가.`, 태수=`문관·무관을 태수로 임명하면 위임 상태. 군주 통치국은 임명 불가`로 정리했다. header/fragment 위치와 05 separator는 보존한다.
- 태수가 있는 도시에서 태수를 다시 임명할 때의 이상한 `?`는 원본 `任=94 43`이 `msgsec07_0079_002 + 0080_000` 경계에서 `0x94|0x43`으로 갈라졌는데 이전 번역이 orphan `0x94`를 남긴 것이 원인이었다. header[190]을 EOF의 `군주가 있으므로<$0A>태수를 임명할 수 없습니다`로 append-only detangle했다.
- 방랑 확인의 이중 물음표는 번역 fragment `정말로 방랑하시겠습니까?`가 ASCII `?`를 넣고, 바로 다음 `msgsec07_0090_000`이 원본 전각 `？`를 별도로 공급한 구조였다. header[210]을 독립 `정말로 방랑하시겠습니까？`로 append-only 전환해 전각 물음표 하나만 남겼다.
- v59에서 새로 쓴 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec10.dat` 3개뿐이다. v58의 `font.g1t/msgsec04/05/06/08/fix_data.bin`은 byte-exact 계승하며 Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v59_appointment_runtime_cleanup.py`, inventory `analysis/v59_appointment_runtime_targets.json`, report `analysis/v59_appointment_runtime_cleanup_report.json`. 입력 lineage 기록도 report determinism을 깨지 않도록 gate 결과를 정규화했고, **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**함을 확인했다.
- v59 SHA-256: `code.bin=C313AABCECB46FBF8CA188B88A43EAFD6C59B2B99361ECB29233FEB0F113C865`, `msgsec07=29ADA1EB5B6E68C458C3BCB8BB226C6EEB31BBD77F7EFE7B7ADD8EBB38E8555D`, `msgsec10=29885C4741B8F8BCB7F63D00BD8C47C14207AA740766102F8A70B85985CA71F4`, report=`C1AA4D3A4A4538E08A25BD7A4EEBD0B23F424D9822F09FA067E6E6ED891BE75A`.
- **v59은 Citra 중간 확인 대기 상태**다. 우선 임명 화면 5버튼/상단 설명, `장군 임명 대상을 선택`, 태수 중복 임명 경고, 방랑 물음표 1개, 신분 `군주`를 확인하고 이어 v58 도시/전장/교환 항목 회귀 여부를 본다.

## 공통 침공/자원명/세율/매복/치수 보정 — v60-intermediate (2026-08-30)

- 사용자 Citra에서 `손견軍が徐州に / 攻め込んだ！`처럼 인물·도시명만 한글이고 공통 이벤트 본문은 일본어인 화면을 확인했다. `msgsec07_0041_001`은 이미 `네군이 %s에 / 쳐들어갔다!`로 한글화되어 있었으므로 Message 경로가 아니라 별도 hardcoded 경로를 재조사했다.
- 실제 source는 extractor pool 밖 `code.bin`의 `0x15789C %s%s軍が%s%s`, `0x157B20 %s%s軍が\n%s%s`, `0x1584A4 に\n攻め込んだ！`였다. 각각 `%s%s 군이 %s%s`, `%s%s 군이\n%s%s`, `에\n쳐들어갔다！`로 수정해 포맷 토큰을 그대로 보존했다. 같은 공통 이벤트 블록에서 추가로 `0x157D70 %s委任中の%sが...`→`%s 위임 중 %s가 / %s로 침공했습니다`, `0x158468 へ/増援部隊を派遣しました`→`로/증원군을 파견했습니다`도 함께 정리했다.
- 구매/교환 후 `%s의 수가 / %d이 되었습니다` formatter는 이미 한글이었다. 혼합 출력의 원인은 `code.bin 0x1AB3FC`의 실제 공용 자원명 table이 `弩@0x1CFAC0 / 強弩@0x1CFAB8 / 軍馬@0x1CFBFC`를 `%s`로 넘긴 데 있었다. 이를 `노/강노/군마`로 수정하고, 별도 spaced UI table `0x1E4824`의 `強 弩@0x1CFAB0 / 軍 馬@0x1CFBEC`도 같은 표기로 맞췄다.
- 세율 확인 화면에서 `세율`이 사라진 원인은 v53 `direct_msg07_internal_pointer_targets.json`의 header[228] detangle 규칙이 의도적으로 `세율`을 건너뛰어 `을 %d％로 합니다 / 괜찮으시겠습니까?`만 독립 문자열로 만든 것이었다. v60은 header[228]을 EOF의 전체 `세율을 %d％로 합니다<$0A>괜찮으시겠습니까？`로 append-only 전환하고 전각 물음표를 사용한다. 기존 msgsec07 body는 byte-exact다.
- 계략→매복의 독의 두 버튼은 이미지가 아니라 `code.bin` text이며 pointer table `0x1E52B0`이 `埋伏@0x1CFD98 / 撤退@0x1CFFC4`를 가리키는 것을 확인했다. 각각 `매복 / 철수`로 수정했다.
- 치수 실행 후 Citra에 단독으로 보인 `よろしいですか？`는 현재 v59 `code.bin`에 정확히 2곳 남아 있었고, `0x0B90DF`는 공통 명령 확인 suffix, `0x1CF4A1`은 세이브 데이터 포맷 UI였다. 치수 화면 대상인 `0x0B90DF`만 `괜찮으십니까？`로 바꾸고 세이브 쪽은 범위 밖으로 보존했다. 최종 exact 일본어 `よろしいですか？` 잔존은 `0x1CF4A1` 1곳뿐임을 gate로 확인한다.
- v60에서 새로 쓴 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개뿐이다. v59의 `font.g1t/msgsec04/05/06/08/10/fix_data.bin`은 byte-exact 계승했고 Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v60_common_event_resource_tax_ambush.py`, inventory `analysis/v60_common_event_resource_tax_ambush_targets.json`, report `analysis/v60_common_event_resource_tax_ambush_report.json`. 모든 대상 slot/pointer를 readback했고 **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**했다.
- v60 SHA-256: `code.bin=DB30779358799ECD21B45F35264FB1256A2D1B0EDED22152138C0F7749C53837`, `msgsec07=22C388FE0DAAF65FD839374E4FC9D0C0250DCAF2E50EBE51E41A35939B2C6331`, report=`1B1DEBD9BF044D7140BA9C941D8CCE179321B898259E12BDCD741195CC828320`. `msgsec10`은 v59의 `29885C47...A71F4`를 그대로 계승한다.
- **v60은 Citra 중간 확인 대기 상태**다. 우선 침공 공통문, 노/강노/군마 결과 표기, 세율 전체 문장+전각 물음표, 매복/철수 버튼, 치수 `괜찮으십니까？`를 확인하고 이후 v59 임명 항목과 v58 도시/전장/교환 항목의 회귀 여부를 본다.

## 임명 질문·임시징수·노 결과명·시작 화면 보정 — v61-intermediate (2026-08-30)

- 사용자 Citra에서 `어느 도시의 무장을 임명하시겠습니까?`의 마지막 ASCII `?`, 결과창의 `弩의 수가 52이 되었습니다`, 임시징수 경고의 잘린 `신뢰를 / 저버릴`, `임시 징수를 하시겠습니까?`, 시작 메뉴의 3DS 모드/챌린지 시나리오 일본어 설명을 확인했다.
- 임명 도시 선택은 `msgsec07` header[187]이 실제 runtime owner다. 기존 문구의 의미는 유지하고 **`어느 도시의 무장을 임명하시겠습니까？`**를 EOF에 append해 header[187]만 전환했다. append-only이므로 기존 19B fragment 용량에 전각 `？` 2B를 억지로 넣지 않으며 축약형은 사용하지 않았다.
- `노`는 v60에서 수정한 일반 C-string `弩@0x1CFAC0`과 별도로 결과창 전용 고정폭 필드 `code.bin 0x1CF9A8 = " 弩 "`가 존재했다. 실제 Citra 결과창은 이 필드를 사용하므로 해당 8B slot만 `" 노 "`로 수정했다. 강노/군마의 기존 정상 source는 보존한다.
- 임시징수 경고는 원문 `民ﾉ信頼ｦ / 裏切ﾙ` 계열의 inside-fragment pointer를 한글 fragment delta에 그대로 재사용해 앞뒤가 잘린 것이 원인이었다. NDS 한국어판 대응 문구를 권위로 사용해 header[221]을 **`백성들의 신뢰를 / 저버릴 생각입니까？`** 독립 문자열로 append-only 전환했다.
- 임시징수 실행 확인은 header[222]을 **`임시 징수를 하시겠습니까？`**로 append-only 전환해 ASCII `?`를 제거했다. 기존 msgsec07 body와 다른 header pointer는 그대로 유지한다.
- 시작 화면은 `msgsec01.dat`의 실제 3DS 모드/챌린지/잠긴 시나리오 설명 fragment가 현재 Patch에도 일본어로 남아 있음을 확인했다. `msgsec01_0042_001 / 0043_000 / 0043_001 / 0044_000 / 0044_001 / 0045_000` 6개만 fixed-position/no-repack으로 수정했다. 화면 권위 문구는 `3DS판 『삼국지2』에 / 다양한 요소를 추가해두었습니다.`, `목표를 달성하면 / 비교적 단기간에 즐기는 게임입니다. / 초보자에게도 추천합니다.`, `아직 플레이할 수 없는 / 시나리오입니다. / 영웅 모드를 진행하면 / 플레이할 수 있게 됩니다.`다.
- `msgsec01.dat`는 전체 **9,057B**, 201-word header, 6개 대상 fragment 시작 offset을 모두 byte-exact로 보존했다. 일부 문장은 fragment 경계에 맞춰 분할하며 generic repack/append relocation을 사용하지 않는다.
- v61에서 새로 쓴 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec07.dat` 3개뿐이다. v60의 `font.g1t/msgsec04/05/06/08/10/fix_data.bin`은 byte-exact 계승했고 Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v61_main_menu_temp_levy_bow.py`, inventory `analysis/v61_main_menu_temp_levy_bow_targets.json`, report `analysis/v61_main_menu_temp_levy_bow_report.json`. 대상 slot/pointer/fragment를 readback했고 **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**했다.
- v61 SHA-256: `code.bin=9FE8117DCC9C2FF1D7ED7BA364E867AD24DF040DB3CBAA8C5738917ECE862082`, `msgsec01=61BD12DDDC2E8C8E56DF1C72ABE45F68168B0C26F77A36843D1C54EC7FC1EF75`, `msgsec07=8F620A314B8E1CA588090966B2EC51E94E9257C4486C04E89E08EEEF7719B4BB`, report=`4582652DEF83F4DB58418D9635CA17CA67C0E09036B17559B906B64EF7D10727`.
- **v61은 Citra 중간 확인 대기 상태**다. 최우선 확인은 임명 질문의 전각 `？`, `노의 수가...`, 임시징수 경고/확인 2문구, 시작 메뉴 3DS 모드/챌린지/잠긴 시나리오 설명이다. 정상 확인 후 v60 이하 수정의 회귀 여부를 이어 확인한다.

## 시작 설정·환경설정 런타임 보정 — v62-intermediate (2026-08-30)

- 사용자 Citra에서 v61 시작 화면 본문은 한글이지만 금색 제목 `3DSモード / チャレンジシナリオ`가 일본어로 남고, 챌린지 본문의 `즐`이 stock 한자로 출력되는 것을 확인했다. 제목 실제 source는 `msgsec01_0041_001/002`이므로 각각 **`3DS 모드 / 도전 시나리오`**로 fixed-position 한글화했다. `챌린지 시나리오`는 원 20B 제목 슬롯을 넘으므로 의미를 유지한 `도전 시나리오`를 사용한다.
- `즐`은 Citra 실패 alias로 확인됐지만 이번 단계에서 font/전역 alias를 재배치하지 않고, 챌린지 문장을 **`비교적 단기간에 하는 게임입니다.`**로 자연스럽게 바꿔 문제 글자를 사용하지 않았다.
- 게임 시작/설정 단계의 `プレイ人数を設定してください`와 `君主を選択してください`는 `msgsec01_0064` duplicate가 이미 한글인데도 Citra에서는 일본어였다. 실제 별도 source를 추적해 `msgsec11_0008_002`와 `msgsec11_0008_003+0009_000`이 화면 owner임을 확인했고 **`플레이 인원 설정 / 군주 선택`**으로 수정했다. 같은 block의 `n번째 군주를 선택`, `표시할 군주를 선택`도 추가 한글화했다.
- 환경설정의 일본어 잔존은 `msgsec01_0064_003~0068_000`의 **모드**, `0072_001~0073_000`의 **무장편집**, `0074_002~0075`의 **수명**을 실제 source로 확인했다. 제목과 설명을 fixed-position/no-repack으로 번역했다. `무장 편집` 제목은 14B 슬롯 때문에 `무장편집`으로 압축했다.
- 사용자 요청의 "근처 일본어도 추가" 범위로 바로 다음 설정인 **BGM / 효과음** 제목과 설명(`0077_001~0079_000`)도 함께 한글화했다. 이번 범위를 지나가는 `타국의 전쟁` 이하 설정은 다음 실화면 검수 대상으로 남긴다.
- 여러 원 fragment에 target을 연속 배치할 때 한글 2바이트나 ESC 색상 제어가 경계에서 잘리지 않도록 새 gate를 추가했다. 초기 raw-byte lead 범위 휴리스틱은 CP932 trail byte도 같은 수치 범위에 들어갈 수 있어 false positive가 발생하므로 폐기하고, target을 **토큰/문자 단위로 재인코딩하여 실제 허용 cut offset 집합**을 계산하는 방식으로 교체했다.
- v62에서 새로 쓴 게임 파일은 `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec11.dat` 두 개뿐이다. v61의 `code.bin/font.g1t/msgsec04/05/06/07/08/10/fix_data.bin`은 byte-exact 계승하며 Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v62_start_setup_environment.py`, inventory `analysis/v62_start_setup_environment_targets.json`, report `analysis/v62_start_setup_environment_report.json`. `msgsec01` 13그룹과 `msgsec11` 4그룹을 readback했고 **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**했다.
- v62 SHA-256: `msgsec01=E82075B5AFB226CA5F74A9D1FC07F46CF6781F188EB780E861B57B65D8183C7E`, `msgsec11=7C999329DCAC81300DAEB9AC532DED0020DB1836BC16233818DF3F9B86764100`, report=`C450B00833FB5D03A40B2612D29BA523379F2BDED38828681495DD29B7CCC582`. v61 `code.bin=9FE8117D...62082`, `msgsec07=8F620A31...9B4BB`는 그대로 유지된다.
- **v62은 Citra 중간 확인 대기 상태**다. 최우선 확인은 `3DS 모드 / 도전 시나리오` 제목, `단기간에 하는 게임입니다`, 플레이 인원/군주 선택 상단 문구, 환경설정 `모드/무장편집/수명/BGM/효과음` 제목·설명이다.

## GitHub Issue001~002 보정 — v63-intermediate (2026-08-30)

- 공개 GitHub 저장소 `https://github.com/lovlince/sangokushi2`의 issue #1/#2를 GitHub API로 직접 읽어 작업 대상으로 사용했다. issue #1은 사마휘의 게임 시작 3DS 모드 가이드 미번역, issue #2는 임시징수에서 `백성들의 신엽를 / 저버릴 생각입니까？`처럼 `뢰`가 `엽`으로 렌더링되는 문제다.
- issue #1 첨부 화면 원문은 OCR 및 바이너리 교차검증으로 **`3DSモードの / 特徴を教えて進ぜよう`**임을 확인했다. 실제 source는 번역 DB가 아니라 `code.bin 0x5C078`부터의 hardcoded NUL C-string이며, `0x5C09C~0x5C24C`, `0x1D29A4~0x1D2AD4`에 후속 3DS 모드 가이드가 연속으로 남아 있어 총 **15개**를 같은 이슈 범위로 한글화했다. 첫 화면 권위 문구는 `3DS 모드의 / 특징을 알려드리지요`다.
- 15개 guide string은 모두 원 NUL allocation 안에서 수정했다. `전기/사기/특성/공격/계략` 용어를 기존 번역과 일치시키고, 현재 폰트에 없는 `턴/벨`은 각각 `차례/단계`로 표현해 신규 글리프를 만들지 않았다. 최대 줄 폭 20 units 이하를 gate로 검증했다.
- issue #2 원문은 `民の信頼を / 裏切る`로 의미상 `백성의 신뢰를 저버리다`가 맞다. 그러나 Citra에서 일반 Message의 `뢰` alias가 `엽`으로 출력되는 것이 실증됐으므로 fix_data 전용 `뢰=94D6`을 일반 대사로 승격하지 않고 **`백성들을 배신하시겠습니까？`**로 자연스럽게 재번역했다. `msgsec07` header[221]만 새 EOF 문자열로 append-only 전환하고 pre-existing body는 byte-exact로 보존했다. 문장 끝은 stock 전각 `？=8148`을 사용한다.
- v63에서 새로 쓴 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 두 개뿐이다. v62의 `msgsec01/msgsec11` 및 v61 이하 font/fix_data/Message 변경은 byte-exact 계승했고 Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v63_github_issue001_002.py`, inventory `analysis/v63_github_issue001_002_targets.json`, report `analysis/v63_github_issue001_002_report.json`. 15개 code slot과 header[221] readback을 통과했고 **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**했다.
- v63 SHA-256: `code.bin=F3CAD388874EAB359806580A2C8B933A0FE272A2296832CEE194F285B631C7F1`, `msgsec07=E9BFD2C0E2DCCB0277E682C55446ACE865F1D4751D8FCEEDECDFD67A31A4FF50`, report=`7D6C438FE545CC1E8EB4E124DFB12DE5824C8F2366FC4AC99AA87835760EF64F`.
- **v63은 Citra 중간 확인 대기 상태**다. GitHub issue #1의 첫 질문과 후속 3DS 모드 가이드가 모두 한글인지, issue #2가 `백성들을 배신하시겠습니까？`로 정상 출력되는지 우선 확인한다.

## GitHub Issue002 자연스러운 문구 복원 — v64-intermediate (2026-08-30)

- 사용자가 issue #2의 자연스러운 최종 문구로 **`백성들의 신뢰를 / 저버릴 생각입니까？`**를 선호한다고 재지정했다. 원문 `民の信頼を裏切る`와 NDS 대응 번역에도 더 가깝기 때문에 문구 권위를 이 표현으로 복원했다.
- 문제는 문구 의미가 아니라 일반 runtime map의 `뢰=98EA`가 Citra에서 `엽`처럼 보인다는 점이다. v58에서 `호뢰관` 표시명용으로 삽입한 **`뢰=94D6 / physical2863`** 셀이 현재 v63 `font.g1t`에서 Sam3KRFont `뢰`와 pixel-exact임을 다시 검증했고, v63 baseline의 `code/msg01/msg07/msg10/msg11`에서 94D6 runtime text ref=0, `fix_data.bin`에서만 1회임을 확인했다.
- v64는 global Korean alias map과 `font.g1t`를 전혀 바꾸지 않고, `msgsec07` header[221]의 새 EOF 문자열을 인코딩할 때만 `뢰→94D6` local override를 적용한다. 최종 target raw에는 `94D6`가 정확히 1회, 실패한 `98EA`는 0회이며 끝 물음표는 stock 전각 `？=8148`이다.
- 새 write는 `RomFS/Message/msgsec07.dat` **1개뿐**이다. v63의 사마휘 guide `code.bin`, v62 `msgsec01/msgsec11`, font/fix_data 및 기타 Message는 byte-exact 계승한다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v64_issue002_natural_wording.py`, inventory `analysis/v64_issue002_natural_wording_targets.json`, report `analysis/v64_issue002_natural_wording_report.json`. 재실행 gate도 mutable current Patch가 아니라 결정적으로 재구성한 v63 baseline에서 94D6 runtime ref=0을 검사하도록 보정했다.
- **연속 2회 실행에서 게임 파일과 report SHA-256이 모두 동일**했다. v64 SHA-256: `msgsec07=C2017425743021732D29A19CF064C84DF214B9CD9666A11B21BAED0B27ECCBD0`, `code.bin=F3CAD388874EAB359806580A2C8B933A0FE272A2296832CEE194F285B631C7F1`(v63 계승), `font.g1t=B09CFFC42A3028AEE24908422224CF51F8D7388204654945FD8B56DBDBABC66C`(불변), report=`3C51D3B563C4062D0F852EACA46AE8ED13EB5A9EC6570F275E996871626DB9E1`.
- **v64은 Citra 중간 확인 대기 상태**다. issue #2에서 `신뢰`의 `뢰`가 실제 한글로 보이고 전체 문장이 `백성들의 신뢰를 / 저버릴 생각입니까？`로 표시되는지 확인한다. 94D6을 다른 Message 문구로 전역 확대하는 것은 Citra 승인 전까지 금지한다.

## GitHub Issue003~009 일괄 보정 — v65-intermediate (2026-08-30)

- GitHub issue #3~#9의 본문과 첨부 화면을 기준으로 실제 런타임 source를 다시 추적했다. v64 전체를 기준으로 유지하고 새 write는 `font.g1t`, `msgsec01.dat`, `msgsec07.dat`, `msgsec11.dat` **4개만** 허용했다. `code.bin`, `fix_data.bin`, 기타 Message 및 이미지 컨테이너는 byte-exact 계승하며 Original/Rebuild/Backup은 수정하지 않았다.
- issue #3은 `善政ヲ永ク行ナ` + 다음 fragment가 런타임에서 갈라져 `오래 행하 면`처럼 보인 문제다. `msgsec07` header[224]를 EOF의 독립 문자열 **`선정을 오래 행하면 / 백성들도 마음을 다할 것입니다`**로 append-only 전환했다.
- issue #4는 문관 임명 경고를 header[196] 독립 문자열 **`문관으로 임명하면 병사를 거느릴 수 / 없게 됩니다. / 괜찮으시겠습니까？`**로 재구성했다. `없게 / 됩니다.`의 불필요한 분리를 없애고 질문 끝은 stock 전각 `？=8148`을 사용한다.
- issue #5는 `현재、<$02><$01>...` 계열이 실제 무장명을 동적으로 넣는 구조임을 확인했다. **`02 01 C8` 3바이트 동적 이름 토큰을 byte-exact 보존**한 채 header[202]를 **`현재 담당 군사는 [동적 무장명]입니다. / 변경하시겠습니까？`** 형태로 전환했다.
- issue #6은 제목을 `도전 시나리오`가 아니라 **`챌린지 시나리오`**로 복원했다. 원 제목 슬롯 20B에 target 21B가 필요하여 과거 Citra 성공 이력이 있는 separator-borrow 방식으로 뒤 `05 05 05`에서 1B만 차용하고 `05 05`를 남겼다. `챌`은 fresh safe alias **`9652 / physical3108`**에 추가했고 packed-font pixel readback을 통과했다. 본문은 **`목표 달성 시 완료됩니다. / 비교적 단기간에 플레이 가능합니다. / 초보자에게도 추천합니다.`**로 정리해 `추천      합니다` padding 문제도 제거했다.
- issue #7의 `B`는 수명 설명 뒤쪽 원문 `。`가 과거 fragment 경계에서 `0x81 | 0x42`로 갈라진 뒤 `0x42`가 고립된 흔적임을 확인했다. 수명 기본 설명을 fixed-position으로 다시 채워 orphan `B`를 제거하고, 바로 아래의 `일부 시나리오` 및 `현재 시나리오` 수명 제한 안내까지 한글화했다.
- issue #8은 `msgsec01_0079_001~0081_000`의 실제 환경설정 source를 fixed-position/no-repack으로 처리했다. 제목은 **`타국 전쟁`**, 설명은 **`타국 전쟁 표시를 설정합니다. / 「본다」이면 / 다른 군주의 전쟁을 봅니다`**로 적용했다.
- issue #9 실제 source는 v62에서 확인한 `msgsec11_0008_002`, `0008_003+0009_000`이다. 첫 문구는 원 24B 슬롯에 정확히 맞는 **`플레이 인원을 설정하세요`**를 사용했다. 두 번째는 `군주를 선택하세요`가 17B로 16B 슬롯을 넘고 10B fragment 경계도 자르므로, 의미를 유지하면서 exact 16B이고 경계가 안전한 **`군주: 선택하세요`**를 사용했다.
- 빌더 `tools/build_sangokushi2_v65_github_issue003_009.py`, inventory `analysis/v65_github_issue003_009_targets.json`, report `analysis/v65_github_issue003_009_report.json`. 첫 v65 생성 후 재실행 시 v64 helper가 mutable current Patch font를 직접 검사해 실패하는 **빌더 결정성 버그**를 발견했고, v65 빌더에서 sealed v63/v58 입력만으로 v64 Issue002를 재구성하도록 수정했다. 이후 **연속 2회 실행이 모두 PASS하고 4개 게임 파일 SHA-256이 완전히 동일**했다.
- v65 SHA-256: `font.g1t=3D6EEE4737F0C6ABF1FC0E70371871BEC30E5EB340E7315F0EBD17C67A399BD9`, `msgsec01=CF353E24820FFBE7916ACE19543B6A105A4E3F796E0EC9E17F12FB298BEE06BF`, `msgsec07=397FFFE19AC1CF044E67EABCDFF86F6250890E2B1E18C1460E2A066674B69F9E`, `msgsec11=0FCE42AF501C633F5204742C44BA0AE62DB1CFDB20B09462F604973E50C00802`. `code.bin=F3CAD388...31C7F1`, `fix_data.bin=A00BB671...940F5`는 v64와 동일하다.
- **v65은 Citra 중간 확인 대기 상태**다. 특히 `챌린지 시나리오`의 1B separator-borrow와 신규 `챌=9652`는 정적 검증만 완료된 상태이므로 Citra 출력이 최종 권위다. #3~#9 각 화면이 정상 확인되기 전에는 번역 HTML/JSON 권위값을 v65 런타임 압축 문구로 동기화하지 않는다.

## GitHub Issue009 군주 선택 문구 재보정 — v66-intermediate (2026-08-30)

- 사용자 Citra 실화면에서 v65의 **`군주: 선택하세요`**가 실제 출력됨을 확인했고, 원하는 문구는 `군주를 선택하세요`, 바이트가 부족하면 `군주를 선택`임을 재지정했다.
- 실제 v65 alias map으로 재계산한 결과 `군주를 선택하세요`는 **17B**라 16B 고정 영역을 1B 초과한다. 따라서 사용자 지정 fallback인 **`군주를 선택`**을 사용한다.
- 단순 ASCII space로 `군주를 선택`을 인코딩하면 11B이며 `msgsec11_0008_003 + 0009_000` 사이의 **10B fragment 경계가 `택` 2바이트를 반으로 자른다.** 이 상태는 Shift-JIS fragment 경계 규칙상 금지다.
- 화면 문구는 그대로 `군주를 선택`으로 유지하면서 경계를 안전하게 맞추기 위해 단어 사이 공백만 게임 원본에서 널리 쓰이는 stock CP932 전각 공백 **`0x8140`**으로 인코딩했다. 최종 raw는 12B이며 `군주를` 6B + `8140` 2B + `선` 2B = 정확히 10B에서 첫 fragment가 끝나고, 두 번째 fragment는 완전한 `택` 2B로 시작한다. 남은 4B만 ASCII space padding이다.
- v66은 **`RomFS/Message/msgsec11.dat` 한 파일만** v65 대비 변경한다. header와 파일 크기는 byte-exact이며 reviewed 16B 영역 밖 diff=0이다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v66_issue009_ruler_prompt.py`, inventory `analysis/v66_issue009_ruler_prompt_targets.json`, report `analysis/v66_issue009_ruler_prompt_report.json`. **연속 2회 실행 모두 PASS**, `msgsec11.dat` SHA-256은 두 번 모두 `04B77E10CD5295A8E4A650EBA2FCE296E4574758E91FB87C08C29CD40C1BC161`, report SHA-256은 `E069A510DDFF1DEDD5EDCB5346AC853D1576F43059CB062D9DD36A11445F3F69`다.
- **v66은 Citra 확인 대기 상태**다. 최우선 확인은 화면 상단이 콜론 없이 정확히 `군주를 선택`으로 보이는지, 전각 공백이 과도하게 넓거나 이상한 기호로 보이지 않는지다.

## GitHub Issue005 재보정 + Issue010 챌린지 1~7 전체 한글화 — v67-intermediate (2026-08-30)

- 사용자 규칙에 따라 GitHub issue는 본문만 보고 수정하지 않고 **첨부 스크린샷을 함께 확인한 뒤** 작업하도록 `PROJECT_RULES.md`에 필수 gate를 추가했다. 이번 v67은 사용자가 직접 올린 #5 Citra 화면과 #10의 챌린지 1/7 화면을 실제로 확인한 뒤에만 수정했다.
- issue #5의 v65 결과 화면은 실제로 **`現在、 [무장명]이 담당군사입니다 / 변경합니까?`**처럼 일본어 prefix가 남아 있었다. 정적 검색 결과 `code.bin 0x1CEF14`에 `現在、` 단독 C-string이 1회 존재하고, `msgsec07`에는 dynamic name token `02 01 C8` 직전에서 시작하는 별도 live suffix가 존재함을 확인했다. 따라서 `code.bin`의 prefix를 **`현재 `**로 바꾸고, `msgsec07 0x1D6A~0x1D94`의 43B live suffix를 **`02 01 C8 + 이(가) 담당군사입니다 / 변경합니까?`**로 재작성했다. header[202]도 v65 EOF target이 아니라 실제 in-body 시작 `0x1D6A`를 가리키도록 전환했다. 최종 목표 화면은 **`현재 [무장명]이(가) 담당군사입니다 / 변경합니까?`**다.
- issue #10은 사용자 스크린샷에서 챌린지 1과 7의 `플레이어/클리어/게임오버/평가` 값이 일본어인 것을 확인한 뒤 주변 데이터를 전수 조사했다. `msgsec16.dat`는 현재 Patch와 Original이 v66까지 byte-exact였고, 원본 SHA-256은 `8E58EFF8286D794744301E9675BCD75DAC4858CAD4DCA294E0EFD750BA6B1A7E`다.
- 구조를 다시 검증한 결과 `msgsec16.dat`는 word0=`49`, word1~48 **전부 absolute BYTE runtime pointer**다. v12의 generic repacker가 기록한 `26 pointer + 23 metadata` 판정은 이 런타임 화면 기준으로 폐기한다. 48개 pointer는 챌린지 1~6의 7개 항목씩과 챌린지 7의 6개 항목에 대응하며, 마지막 header[48]은 평가2와 평가3을 `05 05 05` separator로 연속 보관한다.
- v67은 기존 954B `msgsec16` body를 byte-exact로 보존하고 **header[1..48] 전체를 EOF의 독립 한글 runtime target으로 전환**했다. 챌린지 1~7의 제목, 플레이어, 클리어, 게임오버, 평가 항목을 모두 포함한다. 현재 폰트에 없는 신규 글리프를 만들지 않기 위해 `소지금`, `포로 석방`, `남만왕의 진격`, `맹획 일기토 승리 수` 등 기존 글리프로 자연스럽게 정리했다. 챌린지 7의 마지막 pointer는 `피해 없이 승리<$05><$05><$05>달성 연수`를 한 target으로 유지한다.
- v67 대비 새 write는 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 3개뿐**이다. `font.g1t`, `msgsec01`, `msgsec11`, `fix_data`, 이미지 및 기타 Message는 v66을 byte-exact 계승한다. Original/Rebuild/Backup은 수정하지 않았다.
- 첫 v67 생성 후 두 번째 실행에서 v65의 `챌` donor audit가 mutable 현재 Patch의 새 `msgsec16` 안 `챌` 7회를 과거 참조로 오인하는 결정성 문제가 드러났다. v66까지 `msgsec16`은 Original byte-exact였으므로 v65 재구성 audit에서만 sealed Original `msgsec16`을 사용하도록 v67 빌더를 보정했다. 이후 **완성된 v67 위에서 연속 2회 PASS 및 동일 SHA-256**을 확인했다.
- v67 SHA-256: `code.bin=D935B2B706BAA3D8CD0807079FA27502AA367B93B605DB0FA6A39CB17E1F9294`, `msgsec07=851F2A11DD807126972110162821EF48F3A090EE08917D8606DA795C9D089B43`, `msgsec16=E15D508FEAB1942541D78D26A4A33A1D1C71F2F9BE485BF14F6F570B3CDE7F12`, report=`554BC60327D577E79B89C8433D86BCCE7396CE17D81628FEEE188C2C9BD95BBE`.
- **v67은 Citra 확인 대기 상태**다. #5에서 일본어 `現在、`가 완전히 사라지고 조사 표기가 `이(가)`로 보이는지, #10에서 챌린지 1~7을 순회했을 때 제목/플레이어/클리어/게임오버/평가 값이 모두 한글이며 줄바꿈·별점 UI에 회귀가 없는지 확인한다.

## StartMenu 지정 이미지 43개 재삽입 — v68-intermediate (2026-08-31)

- 사용자 규칙에 따라 GPT 이미지 생성은 사용하지 않았고, 사용자가 직접 수정한 PNG 43개만 내부 RGBA8 texture payload와 1:1 교체했다. 대상은 `start_menu_up_017`, `title_down_001`, `title_up_000/001`, `start_menu_down_004~042`다.
- 기존 Patch 컨테이너인 `start_menu_up.g1t`와 `start_menu_down.g1t`는 **최종 v67 Patch를 sealed baseline으로 보존한 뒤** 그 파일에서 시작했다. v67 Patch에 존재하지 않던 `title_down.g1t`, `title_up.g1t`는 기존 이미지 수정 손실 가능성이 없는 최초 편입이므로 immutable Original을 effective prior-Patch base로 사용했다. Original/Rebuild/Backup은 수정하지 않았다.
- 각 대상은 source RGBA8 무변경 encode round-trip, PNG mode/dimension, 삽입 후 G1T decode pixel-exact readback을 통과했다. 각 컨테이너의 header와 비대상 payload는 선택한 base와 byte-exact다. 전체 Patch manifest 비교 결과 v67 대비 변경/추가 파일은 `RomFS/StartMenu/start_menu_up.g1t`, `start_menu_down.g1t`, `title_down.g1t`, `title_up.g1t` 네 개뿐이며 텍스트·폰트·Message·code·JSON·HTML은 변경되지 않았다.
- 완성된 v68 위에서 빌더를 다시 실행해 네 컨테이너와 report가 모두 동일함을 확인했다. SHA-256: `start_menu_up=708A8DA1C5CC79A5D937BF0402CE0E7891612605AB36F2E9AFEB720576C814CA`, `start_menu_down=3BB0903EA62434E8F7EC5DDA2FF70E15E169F70E2FCC4703CA9D8382E09CFC4E`, `title_down=133C28AB23C415E9D922E7901003B2BB50684D5ECB660F2659296678A38322A5`, `title_up=0C84D647F718771E5B66C2731CCC50522EE0435F4B4F24AFA5640C769FE646B4E`, report=`DD8BDB1156DFC56A3F4647529214B180602098923FCF90F7893FC2F96D129037`.
- 빌더=`tools/build_sangokushi2_v68_startmenu_images.py`, sealed v67 image baseline=`analysis/v68_startmenu_images_baseline/`, report=`analysis/v68_startmenu_images_report.json`. **v68은 Citra 실화면 확인 대기 상태**다.

## StartMenu 지정 이미지 4개 재삽입 — v69-intermediate (2026-08-31)

- 이미지 생성 없이 사용자가 수정한 `start_menu_down_036.png`, `039.png`, `040.png`, `041.png` 네 파일만 대응 RGBA8 payload와 1:1 교체했다.
- 입력 기반은 `analysis/v69_startmenu_down_images_baseline/`에 쓰기 전 봉인한 **최종 v68 Patch의 `start_menu_down.g1t`**다. 컨테이너 header와 네 대상 외 모든 payload는 v68과 byte-exact이며, 전체 Patch manifest 비교에서 변경 파일은 `RomFS/StartMenu/start_menu_down.g1t` 하나뿐이다. 텍스트·폰트·Message·code·JSON·HTML과 Original/Rebuild/Backup은 수정하지 않았다.
- 네 대상 모두 source codec round-trip과 삽입 후 pixel-exact readback을 통과했다. 완성된 v69 위에서 빌더를 다시 실행해 컨테이너와 report가 동일함도 확인했다.
- SHA-256: `start_menu_down.g1t=6DF767FD30CEBD2648FD9D6F5A2E22D7F56D266E36C2E86DD5A5E8550242DAB1`, report=`A1ADA792ABD52306000757A545A45D3AB213BB56DF8B062FDA8285F375A2BDE8`. 빌더=`tools/build_sangokushi2_v69_startmenu_down_images.py`, report=`analysis/v69_startmenu_down_images_report.json`. **Citra 실화면 확인 대기 상태**다.

## 아이템 전면 한글화 — v70-intermediate (2026-08-31)

- 작업 전 `Sangokushi 2 Patch` 32파일을 `analysis/v70_item_translation_baseline/PatchSnapshot/`에 봉인했다. v69 `start_menu_down.g1t=6DF767FD...0242DAB1`을 확인한 뒤 이 snapshot만 재구성 입력으로 사용했다.
- `fix_data.bin 0xDCA8`의 67개 아이템 표시명을 모두 한글화했다. 13B 표시명 필드만 수정하고 각 레코드 뒤 17B 반각 가타카나 읽기/정렬 키는 전부 byte-exact 보존했다. 주요 표기는 `청강검/의천검/칠성검/청룡언월도/적토마/조황비전/청낭서/옥새`, 특산품은 `참사검`부터 `오령수의 혼`까지다.
- `code.bin`에서 아이템 종류 5종(`무기/명마/의서/서적/보물`)과 목록/상세 효과 문자열 풀 28+28=56개를 함께 번역했다. 예: `무력+%d`, `기동력+%d 퇴각확실`, `병/부상 치료`, `수명=%d단계 야망-%d`.
- 몰수 선택 formatter는 기존 `어느 아이템을 %s합니까？`를 유지하고 일본어 동사 조각 `没収し@0x176E00`만 `몰수`로 바꿔 **`어느 아이템을 몰수합니까？`** 조립을 역읽기 검증했다.
- 몰수 확인 `msgsec07_0142_001`은 ASCII `?`와 고정 `로`를 폐기하고 **`%s(으)로 / 괜찮으시겠습니까？`**를 사용했다. 27B preferred 문구가 원 fragment보다 길어 parent 4개 fragment를 append-only 복제하고 header[142]만 `0x1569→0x202B`로 변경했다. 기존 header 이후 body는 byte-exact다. 이 header는 실제로 word-offset(`0x202B*2=0x4056`)을 사용함을 별도 기록했다.
- 목록 헤더 이미지 실제 위치는 `Common/Select/Select.g1t`의 `005=名前`, `021=種類`, `059=アイテム効果`였다. 세 payload만 `이름/종류/아이템 효과`로 결정적 로컬 래스터화했으며 비대상 payload/header diff=0, 3/3 pixel-exact readback이다. GPT 이미지 생성은 사용하지 않았다.
- 기존 map에 없던 글자는 `론/륜` 2자였다. provisional 번역 후 안전 donor `97E9/physical3446`, `97F5/physical3458`을 배정했고 packed readback 및 대상 셀 밖 diff=0을 통과했다. 나머지 이름은 기존 map으로 인코딩했다.
- v69 대비 Patch 변경 집합은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Common/Select/Select.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Scenario/fix_data.bin` 5개다. Original/Rebuild/Backup은 쓰지 않았다.
- 빌더를 완성본 위에서 두 번째 실행해 동일 SHA를 확인했다. SHA-256: `code=E1ABB4EF...3819CB`, `fix_data=507AA92D...1192F5`, `font=77E87AAD...37135A`, `msg07=B9151F29...6CB57B`, `Select=253972E3...D7986E`.
- 빌더=`tools/build_sangokushi2_v70_item_translation.py`, 권위 목록=`analysis/v70_item_translation_targets.json`, report=`analysis/v70_item_translation_report.json`. **Citra 실화면 확인 대기 상태**다.

## GitHub Issue #019~#022 런타임 수정 — v71-intermediate (2026-08-31)

- 사용자 v70 Citra 실화면에서 #19가 **`현재 순욱이 담당군사입니다 / 변경합니까?`**로 표시되고 #22의 `(으)로` 조사 표기도 요청대로 보이지 않는 것이 확인됐다. 따라서 v70에 해당 바이트가 정적으로 존재한다는 이유로 완료 판정했던 판단을 폐기하고, **Citra 출력 결과를 최종 권위**로 다시 추적했다.
- 작업 전 v70 Patch 33파일을 `analysis/v71_github_issue019_022_baseline/PatchSnapshot/`에 봉인하고 그 snapshot만 입력으로 사용했다. v71 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 4개이며 나머지 29개 Patch 파일은 v70과 byte-exact다. Original/Rebuild/Backup은 쓰지 않았다.
- **Issue #019:** v70의 ASCII 괄호 `(가)`가 Citra에서 사라져 동적 무장명 뒤 `이`만 보이는 런타임 현상을 기준으로 수정했다. `현재 ` prefix와 동적 이름 토큰 `02 01 C8`은 보존하고, `msgsec07` header[202]를 EOF `0x40D3`의 **`[무장명]이（가） 담당군사입니다 / 변경합니까?`**로 전환했다. 전각 괄호는 표시 안정화를 위한 런타임 저장형이며 화면 목표 표기는 `이(가)`다.
- **Issue #020:** 원본 챌린지7 `曹叡`를 직접 역읽기해 `조조/조비`가 아니라 **조예**임을 확정했다. 제목은 `챌린지 7 강유의 북벌`, 챌린지5 클리어는 `하변, 한중, 자동, 영안, / 강주, 성도, 건녕을 점령함`, 해당 평가의 `달성 연수`는 모두 `달성년수`로 변경했다. `혹`은 현재 글리프 map에 없어 의미가 같은 `강유 또는 유선의 죽음.`으로 적용했다. `msgsec16` 변경 runtime header는 `[7,14,28,31,35,43,45,46,48]` 9개뿐이다.
- `조예`의 `예=989D`는 이 화면에서 Citra 위험 far alias로 판단해 전역 map을 움직이지 않고 **local alias `927D / physical2399`**만 #20 target에 사용했다. 바로 위 physical2326의 `쳐` bleed를 막기 위해 glyph를 1px 아래로 이동하고 top scanline empty, packed-font pixel-exact, 대상 셀 밖 pixel diff=0을 확인했다. Citra 승인 전에는 이 alias를 전역 권위로 승격하지 않는다.
- **Issue #021:** 포상 금 수량 `0x154D52`를 **`금을 얼마나 줍니까？`**로 독립시켰고 공유 금 formatter `0x154D68`은 byte-exact 보존했다. 아이템 질문은 일본어의 `동사어간 + ますか` 구조가 한국어 `주기 + 합니까`로 조립된 것이 원인이었다. 공용 formatter를 **`어느 아이템을 %s`**로 바꾸고 확인된 8개 item-selector caller 전부에 완결형 동사를 공급했다: 포상=`줄까요？`, 보기=`볼까요？`, 내놓기=`내놓습니까？`, 몰수=`몰수합니까？`. 자원 수량용 `가져갑/줍/요구합/요청합` table은 byte-exact 보존했다. Message duplicate인 header[322]도 EOF `0x40FC`의 **`어느 아이템을 줄까요？`**로 전환했다.
- **Issue #022:** v70의 `%s(으)로`도 ASCII 괄호가 Citra에서 안정적으로 보이지 않는 동일 문제로 보고 parent 4-fragment를 다시 append했다. header[142]는 이 화면의 검증된 **word-offset** 규칙을 유지해 새 parent byte `0x4134`를 word `0x209A`(8346)로 저장했고 확인문은 **`%s（으）로 / 괜찮으시겠습니까？`**다. stock 전각 `？`를 유지한다.
- 아이템 수여 후 장수명 깨짐은 원본 3바이트 동적 이름 토큰 **`02 01 C8`**의 마지막 `C8`이 v70 결과문에서 누락된 것이 원인이었다. header[173]을 EOF `0x4115`의 `02 01 C8 + 의 충성이 / %d이 되었습니다`로 전환해 이름 토큰을 byte-exact 복구했다.
- 완성 v71 위에서 빌더를 두 번째 실행해 동일 SHA-256을 확인했고 별도 독립 역읽기도 PASS했다. SHA-256: `code=7EAE868E...EE772F`, `font=430ECA27...05CF9`, `msg07=E03846E3...55DB2`, `msg16=9C6C36DC...069F1`.
- 빌더=`tools/build_sangokushi2_v71_github_issue019_022.py`, targets=`analysis/v71_github_issue019_022_targets.json`, report=`analysis/v71_github_issue019_022_report.json`. **v71은 이후 Citra 재검수에서 #19/#23~#25 관련 런타임 source 누락/side-effect가 확인되어 v72로 대체됐다.**

## GitHub Issue #023~#025 + 담당군사 조사 재수정 — v72-intermediate (2026-08-31)

- 사용자 v71 Citra 재검수에서 (1) 담당군사 화면이 여전히 `현재 순욱이 담당군사입니다 / 변경합니까?`로 표시됨, (2) #23 챌린지 게임오버/`達成年数` 누락, (3) #24 아이템 확인 조사 표기 미반영, (4) #25 포상 금 질문이 `금을 얼마나 줄까요?니까?`로 조립되는 side-effect가 확인됐다. **이 실화면을 최종 권위로 삼아 v71의 정적 성공 판정을 해제**했다.
- 작업 전 현재 v71 Patch 33파일을 `analysis/v72_github_issue023_025_baseline/PatchSnapshot/`에 sealed baseline으로 봉인했다. v72 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 3개이며 `font.g1t`, `fix_data.bin`, `Select.g1t`, StartMenu/Tutorial 및 나머지 Message 파일은 v71과 byte-exact다. Original/Rebuild/Backup은 쓰지 않았다.
- **담당군사 조사:** v71에서 수정한 `msgsec07 header[202]`는 실제 Citra source가 아니었다. 실제 source는 `code.bin`의 formatter 복제본 **`0x16C9B8`, `0x16D1D8`** 두 곳이었고 둘 다 v71에서 `%s%s이 %s입니다 / 변경합니까？`였다. v72는 두 32B 슬롯을 직접 **`%s%s이(가) %s입니다 / 변경합니까?`**로 수정했다. 독립 역읽기에서 old formatter 0개/new formatter 2개를 확인했다.
- **Issue #023:** 챌린지7 게임오버는 요청한 의미와 줄폭에 맞춰 **`강유 또는 유선이 죽음. 조예나 / 사마의가 다른 세력에게 패배.`**로 header[46]을 새 EOF target으로 전환했다. 또한 `達成年数`는 header redirect만으로 끝나는 문자열이 아니며 원 `msgsec16` raw body에서 **`0x00BA/0x0139/0x01FB/0x028A/0x03AF` 5곳**이 별도 런타임 source로 남아 있었다. 각 8B를 동일 길이 **`달성년수`**로 제자리 치환했고 최종 `達成年数` 잔존은 0이다.
- **Issue #024:** 사용자 지정 표기를 그대로 사용해 확인문을 **`%s (으)로 / 괜찮으시겠습니까?`**로 변경했다. 즉 `%s` 뒤 공백 1칸 + ASCII `(으)` + `로`, 줄바꿈 뒤 ASCII `?`를 사용한다. header[142]의 검증된 **word-offset** 규칙을 유지하고 새 parent byte `0x41B4`, word pointer `0x20DA`로 전환했다. target fragment는 parent+37=`0x41D9`다. v71의 전각 괄호/전각 물음표 정책은 이 화면의 최신 권위가 아니다.
- **Issue #025:** v71 side-effect는 포상용 완결형 `줄까요？`가 자원 수량 공용 formatter `금을 얼마나 %s니까？`와 다시 결합하면서 `줄까요？니까？`가 된 것이었다. 금 포상 formatter 함수 `0x154CC0`의 호출처가 전체 code.bin에서 **`0x161B1C` 1곳뿐**임을 확인하고 포상 경로만 분리했다. 다수 대상은 **`한 사람당 금을 얼마나 줄까요?`**, 일반 포상은 **`금을 얼마나 줄까요?`**를 사용한다. 일반 포상 분기의 formatter 포인터는 `0x154D68→0x154D52`로 전환했고, 다른 거래/요구/수송이 공유하는 `0x154D68`은 v71 byte-exact 보존했다.
- 완성 v72 위에서 빌더를 두 번 연속 실행해 동일 SHA-256을 확인했다. SHA-256: `code=35706BD7...948DB`, `msg07=E0AD2B32...A231B`, `msg16=AA8F5C2B...4F9B4`. 별도 독립 역읽기에서도 담당군사 new formatter 2개/old 0개, #24 header[142] word=`0x20DA`, #23 raw `달성년수` 5곳, #25 shared `0x154D68` byte-exact, report hash 일치가 모두 PASS했다.
- 빌더=`tools/build_sangokushi2_v72_github_issue023_025.py`, targets=`analysis/v72_github_issue023_025_targets.json`, report=`analysis/v72_github_issue023_025_report.json`. **v72는 이후 Citra 재검수에서 담당군사 수정은 정상 확인됐으나 #023 `조예` local-alias 회귀와 #024 실제 runtime pointer 누락이 확인되어 v73으로 대체됐다.**

## Issue #023/#024 Citra 실제 source 재보정 — v73-intermediate (2026-08-31)

- 사용자 v72 Citra 실화면에서 담당군사 **`현재 공융이(가) 담당군사입니다 / 변경합니까?`**가 정상 출력됨을 확인했다. 따라서 v72의 `code.bin 0x16C9B8/0x16D1D8` 두 formatter 직접 수정 방식은 성공 기준점으로 기록하고 v73에서는 `code.bin`을 v72와 byte-exact 보존했다.
- 같은 Citra 검수에서 #023 챌린지7 게임오버의 `조예`가 다시 **`조?`**로 회귀했다. v72 header[46] target `0x0917`을 역읽기한 결과 `예`가 v71 Citra-safe local alias `927D`가 아니라 global alias **`989D`**로 재인코딩돼 있었다. 글자 수/슬롯 부족 문제가 아니므로 문구를 줄이지 않고 `989D→927D` **2바이트만 제자리 교체**했다. header[46], 파일 크기, 줄바꿈은 모두 v72와 동일하다.
- #024 로컬 이슈 문서와 HTML 번역본을 다시 대조했다. 논리 row는 `msgsec07_0142_001`, 원문 **`%sデ / ヨロシイデスカ?`**, 기존 번역 DB는 **`%s로 / 괜찮으시겠습니까?`**이며 사용자 목표는 **`%s (으)로 / 괜찮으시겠습니까?`**다. 아이템 이름(`청강검`)은 `%s`/item-name 제어 경로에서 공급되므로 이름 데이터는 수정하지 않는다.
- v72가 만든 올바른 #024 target은 byte `0x41D9`에 실제 존재했지만 Citra는 계속 **`청강검로 / 괜찮으시겠습니까?`**를 출력했다. 전체 `msgsec07` header를 역검색한 결과 그 화면과 정확히 일치하는 old target byte `0x2D43`을 **header[326] = 0x2D43 absolute BYTE pointer**가 직접 가리키고 있었다. 반면 v72가 바꾼 header[142]=`0x20DA`는 duplicate parent 경로였다.
- v73은 #024 body를 다시 append/repack하지 않고 **header[326]만 `0x2D43→0x41D9`로 redirect**했다. header[142], item-name payload, 기존 body는 byte-exact 보존했다. 이로써 실제 Citra source도 `%s (으)로 / 괜찮으시겠습니까?` target을 사용하도록 했다.
- 작업 전 v72 Patch 33파일을 `analysis/v73_issue023_024_runtime_source_fix_baseline/PatchSnapshot/`에 sealed baseline으로 봉인했다. v73 변경 파일은 정확히 `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` **2개뿐**이며 `code.bin/font.g1t/fix_data.bin/Select.g1t` 및 나머지 31개 파일은 v72와 byte-exact다. Original/Rebuild/Backup은 쓰지 않았다.
- 완성 v73 위에서 빌더를 두 번 연속 실행해 동일 SHA-256을 확인했다. SHA-256: `msg07=CF97A819...C4660E`, `msg16=17173923...ABCA6`. 별도 독립 역읽기에서도 header[326]=`0x41D9`, target byte 일치, header[46]=`0x0917`, `927D` 1회/`989D` 0회, `code.bin/font.g1t` v72 byte-exact, report hash 일치를 모두 확인했다.
- 빌더=`tools/build_sangokushi2_v73_issue023_024_runtime_source_fix.py`, targets=`analysis/v73_issue023_024_runtime_source_fix_targets.json`, report=`analysis/v73_issue023_024_runtime_source_fix_report.json`. **v73 Citra 재검수에서 #024 조사 표시는 정상 확인됐지만 마지막 ASCII 물음표가 어색하게 보였고, 챌린지7 제목의 `북`이 한자형 glyph로 출력되어 v74로 이어갔다.**

## Issue #023 제목 `북` + #024 물음표 표시 보정 — v74-intermediate (2026-08-31)

- 사용자 v73 Citra 실화면에서 챌린지7 제목 **`강유의 북벌`의 `북`이 佛 비슷한 한자형 glyph**로 보이는 것이 확인됐다. header[43]=`0x0868` title fragment를 역읽기한 결과 `북`이 과거 Citra 실패 이력이 있는 **`98DD`**로 인코딩돼 있었다.
- v58에서 `북평` renderer 문제 때문에 이미 확보한 fallback **`북=89D0 / physical789`**를 재사용했다. v58 report의 packed readback exact 이력을 확인했고, 현재 v73 `font.g1t`의 physical789 셀도 Sam3KRFont `북`과 pixel-exact임을 재검증했다. 따라서 font 자체는 수정하지 않고 header[43] title fragment 내부의 `98DD→89D0` **2바이트만 제자리 치환**했다. 제목 길이/포인터/제어코드는 그대로다.
- #024는 v73 Citra에서 **`청강검 (으)로 / 괜찮으시겠습니까?`**까지 조사 표시는 정상 확인됐으나 마지막 ASCII `?`가 게임 폰트에서 어색하게 보였다. 목표를 **`%s (으)로 / 괜찮으시겠습니까？`**로 변경했다. ASCII `?=3F` 대비 전각 `？=8148`은 1바이트 증가하지만 v73 `msgsec07` EOF가 `0x4231`이고 header[326]은 absolute BYTE pointer이므로 문구 축약이 필요하지 않았다.
- 기존 `0x41D9` target과 뒤 body를 밀지 않고 EOF byte **`0x4231`**에 28B 새 target + `05 05 05`를 append하고 **header[326]을 `0x4231`로 redirect**했다. 따라서 사용자가 제시한 `괜찮겠습니까？` 축약은 사용하지 않고 `괜찮으시겠습니까？`를 그대로 보존했다.
- 작업 전 v73 Patch 33파일을 `analysis/v74_issue023_024_display_fix_baseline/PatchSnapshot/`에 sealed baseline으로 봉인했다. v74 변경 파일은 정확히 `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` **2개뿐**이며 `code.bin/font.g1t` 및 나머지 31개 파일은 v73과 byte-exact다. 담당군사 `이(가)`와 게임오버 `조예=927D` 성공 수정도 그대로 유지된다.
- 완성 v74 위에서 빌더를 두 번 연속 실행해 동일 SHA-256을 확인했다. SHA-256: `msg07=D8A07E95...BC0737`, `msg16=3AC48D4A...E22E2A`. 독립 역읽기에서도 title `89D0` 1회/`98DD` 0회, 게임오버 `927D` 1회/`989D` 0회, header[326]=`0x4231`, 새 target 끝 `8148`, separator=`050505`, `code.bin/font.g1t` v73 byte-exact, report hash 일치를 모두 확인했다.
- 빌더=`tools/build_sangokushi2_v74_issue023_024_display_fix.py`, targets=`analysis/v74_issue023_024_display_fix_targets.json`, report=`analysis/v74_issue023_024_display_fix_report.json`. v74는 v75의 sealed baseline으로 계승됐다.

## GitHub Issue #016/#017/#018/#026 런타임 수정 — v75-intermediate (2026-08-31)

- 사용자 요청은 `#026, #018, #017, #016` 번호만으로 전달됐다. 다른 채팅에서도 같은 방식으로 처리할 수 있도록 **번호만 주어져도 `Github_Issue/IssueNNN.html`(3자리 0-padding)을 자동 확인하고 본문+embedded 스크린샷을 모두 읽는 규칙**을 `PROJECT_RULES.md`와 `PATCH_HANDOFF.md`에 추가했다. GitHub 직접 조회가 Cache miss/접근 실패이면 로컬 사본을 fallback 권위로 사용하고, 스크린샷을 실제 확인할 수 없으면 본문 텍스트만으로 추측 수정하지 않는다.
- 이번 작업 당시 GitHub 직접 조회는 Cache miss였고, 로컬 `Issue016/017/018/026.html`의 본문과 embedded 스크린샷을 추출·확인한 뒤 현재 v74 Patch의 바이너리 source와 교차검증했다. 스크린샷 기준 #026 1장, #018 1장, #017 3장, #016 1장으로 중복을 제거했다.
- **Issue #026:** 화면 상단 `誰に？`에 대응하는 실제 source는 `code.bin 0x161C0C`, 기존 NUL allocation 20B다. 사용자 preferred `누구에게 포상 합니까?`는 NUL 포함 22B라 슬롯을 넘으므로 사용자가 함께 제시한 fallback **`누구에게 줄까요?`**(NUL 포함 17B)를 원 슬롯 안에서 사용했다. relocation은 없다.
- **Issue #017:** 스크린샷 3장의 일본어와 일치하는 source를 `code.bin`에서 각각 `0x1D24CC`, `0x1D2D04`, `0x1D1EB4`로 확정했다. target은 `전략 명령을 실행하면 / 경험치가 모여 속성 단계가 / 오르고 전기를 배웁니다`, `「사기」는 전략 명령의 / 「훈련」「전쟁준비」로 / 미리 올릴 수 있습니다`, `영지의 재야 무장 중 「특성」을 / 가진 자가 있으면 적극적으로 / 등용하는 것이 좋습니다`다. 각 target은 원 allocation 82/72/84B 안에서 69/67/81B이며 최대 줄폭 13/11/15칸이다.
- **Issue #018:** 게임 시작 화면의 `確定`과 `設定ヲ完了シマス`는 `msgsec01.dat 0x1265/0x1272`의 fixed-position source로 확인했다. target은 **`확정` / `설정을 완료함`**이다. 파일 크기와 fragment 시작 위치를 유지하고 repack하지 않았다.
- **Issue #016:** 일반 모드 `msgsec15` 포상 도움말은 v35/v43에서 이미 한글화됐지만 3DS 모드가 사용하는 **`msgsec10` 복제본**이 일본어로 남아 있음을 확인했다. `0x0C65`=`최대 100, 여러 무장에게 지급 가능.`, `0x0C92`=`군주만 실행 가능. / 능력치·충성이 크게 상승.`, `0x0CCD`=`비용 없음. 금·아이템보다 / 효과가 적습니다.`로 fixed-position/no-repack 수정했다. 하단 `戻ル`는 이미지가 아니라 `msgsec03_0004_000`, 파일 offset `0x00C3`의 3B 텍스트다. 바로 뒤 `05 05 05` separator에서 1B만 차용해 **`뒤로`** 4B를 넣고 `05 05`를 남겼다.
- v74 전체를 `analysis/v75_github_issue016_018_026_baseline/PatchSnapshot`에 sealed baseline으로 사용했다. v75 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `msgsec03.dat`, `msgsec10.dat` 4개**이며 비대상 Patch 파일은 v74와 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- target 한글 alias는 전부 현재 runtime map에 존재하고 공유 충돌 0이다. #016/#017은 20칸 줄폭 gate, #018 fixed-position/file-size gate, #016 separator 보존, #026 slot capacity를 모두 통과했다.
- 완성된 v75 위에서 빌더를 다시 실행해 동일 SHA-256을 확인했고, 별도 독립 역읽기에서 #026/#017 3문장/#018 제목·설명/#016 설명 3개·뒤로 및 허용 범위 밖 diff=0, report hash 일치가 모두 PASS했다.
- SHA-256: `code.bin=433B255FB28B6E288978C95C27FCFE10A3D4B74F157BC1658BE53EEA997D2AB2`, `msgsec01=FAB633F748470D8C53C6144BF47942FA9A4E3EE087022F8FB4C9396CC9F8BA6C`, `msgsec03=887B4D6BB51B8E244704F21ED396231A592CEECE5B7C4E4A0375D5BAA1B8EC31`, `msgsec10=5653F131CD763F067DBBE7F6EEFF2683C1F5EAE6C333279A5D7497D2C713D590`.
- 빌더=`tools/build_sangokushi2_v75_github_issue016_018_026.py`, targets=`analysis/v75_github_issue016_018_026_targets.json`, report=`analysis/v75_github_issue016_018_026_report.json`. v75는 이후 #018 실제 runtime duplicate 오판이 확인되어 **v76에서 대체**됐다.

## GitHub Issue #018/#027/#028/#029 런타임 수정 — v76-intermediate (2026-08-31)

- 이번 작업은 사용자가 로컬 `Github_Issue`에 동기화한 `Issue018/027/028/029.html`과 각 `IssueNNN_assets/*.png`만 사용했다. 웹 GitHub는 조회하지 않았다. 앞으로 사용자가 URL 없이 `#018`처럼 이슈 번호만 전달하면 **웹을 조회하지 않고 로컬 HTML+PNG만 권위로 사용**하며, 웹 검증은 사용자가 명시적으로 요청한 경우에만 수행하도록 `PROJECT_RULES.md`에 규칙을 확정했다.
- **Issue #018 실제 source 재추적:** v75가 수정한 `msgsec01.dat 0x1265/0x1272`는 화면 의미가 같은 다른 duplicate였고, 사용자 Citra의 게임 시작 전 환경설정 화면에는 전혀 반영되지 않았다. 원본/현재 Patch occurrence와 fragment 구조를 다시 추적한 결과 실제 런타임 source는 `msgsec01_0073_001 + msgsec01_0074_000`에 걸친 split-SJIS `確定` 제목과 `msgsec01_0074_001`의 `設定ｦ完了ｼﾏｽ`로 확정했다. 실제 파일 위치는 **제목 `0x1BDA`, 설명 `0x1BE7`**이다. 제목은 fragment 경계 4B split을 보존한 채 `확정`, 설명은 **`설정 완료함`**으로 fixed-position/no-repack 수정했다. `msgsec01.dat` 크기 9,057B와 모든 fragment 시작 위치는 유지했다.
- **Issue #027 하단 버튼:** 스크린샷의 `金 / アイテム / 書物`은 이미지가 아니라 `code.bin`의 runtime text다. 포인터 테이블은 **`0x1E4890`**이며 포인터 자체는 변경하지 않았다. `금`은 `0x1CFFD0` shared string, 아이템 버튼은 기존 `特産アイテム` 공용 문자열을 **`특산아이템`**으로 재인코딩한 뒤 동일 interior pointer가 `아이템` 부분을 가리키도록 유지했고, 서적은 `0x1D0824`의 spaced `書 物`을 `서적`으로 수정했다. 독립 역읽기에서 버튼 결과 `금 / 아이템 / 서적`과 pointer table byte-exact를 확인했다.
- **Issue #027 설명 줄바꿈:** 3DS 포상 `msgsec10`의 아이템 설명 `0x0C92`를 **`군주만 실행 가능. 능력치·충성이 크게 상승`**, 서적 설명 `0x0CCD`를 **`비용 없음. 금·아이템보다 효과 적음`**으로 바꿔 명시적 줄바꿈을 0개로 만들었다. 두 문구는 가로 스크롤을 전제로 한 한 줄 문장이며 원 writable 56B/43B 안에서 각각 42B/35B로 들어간다. 파일 크기는 유지했다.
- **Issue #028 ASCII 물음표 전수 감사:** 현재 v75 Patch와 immutable Original을 비교해 Patch가 만든/유지한 한국어 ASCII `?` 후보를 전수 추출했다. binary header의 우연한 `0x3F`를 텍스트로 오인하지 않고 실제 C-string/Message 대사 범위만 대상으로 삼았다. 구조적으로 1B 확장을 흡수할 수 있는 **총 240개**를 stock 전각 `？=8148`로 정규화했다: `code.bin` 18개, `msgsec04` 15개, `msgsec06` 31개, `msgsec07` 164개, `msgsec08` 12개. Message는 다음 `05` separator 1B 차용, padding space 소비, ASCII+전각 중복 제거 등 **다음 fragment 시작을 움직이지 않는 방식만** 사용했고 flat header는 byte-exact다.
- #028에서 의도적으로 남긴 `0x3F`는 텍스트가 아닌 `msgsec07` binary header **5개(`0x184/0x189/0x1BB/0x1BC/0x1BD`)**다. 이미 사용자 Citra에서 정상 확인된 담당군사 `code.bin 0x16C9B8/0x16D1D8` formatter의 끝 `?` 두 곳은 바로 뒤 padding이 없어 1B 확장을 안전하게 증명할 수 없으므로 유지했다. `msgsec01 0x195F`의 시작 설정 질문도 이번 direct-message bulk 범위 밖이라 수정하지 않았다. 대표 #026 포상 질문 `누구에게 줄까요?`는 이번에 **`누구에게 줄까요？`**로 변경됐다.
- **Issue #029:** v75의 `영지의 재야 무장 중 「특성」을 / 가진 자가 있으면 적극적으로 / 등용하는 것이 좋습니다`는 실제 3줄 대화창에서 첫 줄 폭 때문에 자동 개행이 하나 더 발생해 후속 줄이 밀려 사라지는 문제가 있었다. 또한 `적극적으로`의 `극=98CA` far alias가 Citra에서 `겸`처럼 잘못 출력됐다. v76은 원 `code.bin 0x1D1EB4` 84B allocation 안에서 **`영지의 재야 무장 중 / 「특성」을 가진 자가 있다면 / 가급적 등용하십시오`**(67B)로 수정했다. 명시적 3줄, 시각 폭 `[10,14,10]`으로 16칸 이하이며 `98CA`를 사용하지 않는다.
- v76은 sealed v75 Patch snapshot을 기준으로 정확히 **7개 파일**만 변경했다: `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec10.dat`. `font.g1t`, `fix_data.bin`, 모든 이미지/G1T 및 기타 Patch 파일은 v75와 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 완성된 v76 위에서 빌더를 다시 실행해 **게임 파일 7개와 report SHA-256이 첫 실행과 완전히 동일**함을 확인했다. 별도 `tools/verify_sangokushi2_v76_runtime_readback.py` 독립 역읽기도 PASS했다. 검증 항목은 #018 실제 `0x1BDA/0x1BE7`, #027 버튼 pointer table 및 설명 newline=0, #028 direct Message 잔존 ASCII 질문=0(`msgsec07` header false-positive 5개 제외), #029 3줄 및 `98CA` 미사용이다.
- SHA-256: `code.bin=376EF36B35EF9DD360D23B469582EC452F7B04BC357E8FC6F398CDC0BCB577C7`, `msgsec01=AAF9E6225E59552D3798923F239E2E83591933710D078605D8A75C8BAC88731C`, `msgsec04=9D911DAA36B0DA4F09F4709DDD6AC067B7D11FC30E7B65A7214507144ADB95CD`, `msgsec06=F081DAFB97C274ECAFE2C15AE9BA16311E7458049CCA75433DA9B68768207030`, `msgsec07=451B803E87F8B6557FE5DEB8361AB14109A9D55B8D551D8A004E55F33A8339FA`, `msgsec08=99B8FD99701DDEAE1B84C6AB6655ECBE820A5FB58DA04CC42E7B82E09E15E460`, `msgsec10=9EDDC607024AC807E3FE04C17942297A9F3E0F3E2486B8629A627AF7EE0C94B8`.
- 빌더=`tools/build_sangokushi2_v76_github_issue018_027_028_029.py`, targets=`analysis/v76_github_issue018_027_029_questionmarks_targets.json`, report=`analysis/v76_github_issue018_027_028_029_report.json`, ASCII audit=`analysis/v75_ascii_question_audit.json`, 독립 검증기=`tools/verify_sangokushi2_v76_runtime_readback.py`.
- **현재 Patch는 v76-intermediate이며 다음 단계는 사용자 Citra 실화면 확인**이다. 우선 #018의 `확정 / 설정 완료함`이 실제 게임 시작 전 환경설정 화면에 반영됐는지 확인하고, 이어 #027 버튼/한 줄 설명, #028 전각 물음표, #029 3줄 대화를 검수한다.

## 지정 이미지 31개 재삽입 — v77-intermediate (2026-08-31)

- 사용자 규칙에 따라 GPT 이미지 생성 없이 `Extracted_Image`의 명시된 PNG 31개만 사용했다. 대상은 `start_menu_up` 2개, `saveload_u` 4개, `Tutorial` 4개, `saveload_d` 1개, `gallery_down` 5개, `option_down` 15개다.
- 시작 시 현재 Patch 33파일 manifest가 v76 report와 정확히 일치함을 확인하고 `analysis/v77_image_update_baseline/PatchSnapshot`에 전체를 봉인했다. 이미 Patch에 존재한 `start_menu_up.g1t/Tutorial.g1t`는 이 sealed v76을 기준으로 사용했다. v76에 없던 `saveload_u/d.g1t`, `gallery_down.g1t`, `option_down.g1t`만 immutable Original을 effective prior-Patch base로 최초 편입했다.
- 31개 PNG는 모두 RGBA이며 각 G1T 0x09 RGBA8 엔트리의 해상도와 일치했다. 각 source payload를 디코드→재인코드했을 때 byte-exact였고, 교체 뒤 header 및 비대상 payload diff=0, Patch 역디코드와 PNG pixel-exact를 확인했다.
- v76 대비 변경/추가 파일은 정확히 6개다: `RomFS/StartMenu/start_menu_up.g1t`, `saveload_u.g1t`, `saveload_d.g1t`, `gallery_down.g1t`, `option_down.g1t`, `RomFS/Tutorial/Tutorial.g1t`. 그 외 31개 Patch 파일은 v76과 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v77 위에서 빌더를 재실행해 6개 파일 해시와 report SHA-256 `C367059CBE4315861BA905407F28ADCC87DD8DB97F41BD88259F14DB919F37D8`이 동일함을 확인했다. 별도 `tools/verify_sangokushi2_v77_image_readback.py`도 6 containers / 31 textures / Patch 37 files PASS했다.
- 출력 SHA-256: `start_menu_up=8FAFEFEB...4066F8`, `saveload_u=A88F56BF...A5C4B2`, `Tutorial=87D40BD7...CE2165`, `saveload_d=A93A2A68...BCCAA3`, `gallery_down=3CC6B13F...33FD3`, `option_down=B7A4D3A1...9B765E`. 빌더=`tools/build_sangokushi2_v77_image_update.py`, report=`analysis/v77_image_update_report.json`. v77은 아래 v78의 sealed baseline으로 계승됐다.

## GitHub Issue #011/#030 + hardcoded 대사 길이 전수 점검 — v78-intermediate (2026-08-31)

- 작업 시작 전 `tools/verify_sangokushi2_v77_image_readback.py`를 다시 실행해 **v77 = 6 containers / 31 PNG / Patch 37 files PASS**를 확인했다. 현재 v77 전체를 `analysis/v78_github_issue011_030_baseline/PatchSnapshot`에 봉인했고, v78은 `ExeFS/code.bin` 하나만 수정한다. v77의 이미지 G1T 6개와 모든 Message/font/fix_data는 byte-exact 보존한다.
- **Issue #011:** 로컬 `Issue011.html` + PNG 2장을 확인했다. 첫 화면은 `무엇을 내놓습니까？`의 7개 버튼, 둘째는 `무엇을 요구합니까？`의 5개 버튼이다. 실제 pointer table은 요구=`code.bin 0x1DC450`, 내놓기=`0x1DC648`로 확인했다. v77 시점에 `금@0x1CFFD0`, `아이템@0x1D0818`, `노/강노/군마`는 이미 한글이라 유지하고, 실제 잔존 일본어 **`0x1CFEC8=兵 糧`, `0x1CFED0=兵糧`, `0x1CFBDC=兵士`** 3개만 **`병량 / 병량 / 병사`**로 변경했다. 두 pointer table은 byte-exact이며 비슷한 `兵 士@0x1D0804`는 이 화면 source가 아니므로 수정하지 않았다.
- **Issue #030 화면 규격 확정:** PNG 2장에서 `「전기」를 사용할 수 있습니다`(16글자)의 마지막 `다`가 잘리고, `우수한 무장은 「전기」 외에도`(16글자)의 마지막 `도`가 다음 줄로 밀리는 반면 앞 15글자는 한 줄에 남는 것을 확인했다. 따라서 이 `code.bin` portrait/hardcoded 대화창은 **한 줄 최대 15 visible characters / 최대 3 visual lines**로 확정했다.
- 기존 v53 code 대사 gate는 `<=20`이어서 이 15칸 UI에서 회귀가 남아 있었다. `Extracted_Text/sangokushi2_code_text.json`의 `설명/대사` 중 원 일본어가 같은 15칸 wrap에서 3 visual lines 이내인 현재 한국어 대사 **257개를 전수 감사**했고 v77에서 **20개 초과**를 확인했다. audit=`tools/audit_sangokushi2_dialogue_layout_v77.py`, report=`analysis/v77_dialogue_layout_audit.json`.
- 초과 20개 offset은 `0x1D17B8, 0x1D184C, 0x1D18A0, 0x1D1998, 0x1D19E0, 0x1D1AC4, 0x1D1B18, 0x1D1E5C, 0x1D1F0C, 0x1D1F64, 0x1D2050, 0x1D21BC, 0x1D2254, 0x1D22FC, 0x1D2390, 0x1D29EC, 0x1D2A80, 0x1D2AD4, 0x1D2B28, 0x1D2C48`이다. 신규 글리프를 만들지 않고 기존 runtime map에 존재하는 글자만 사용해 각 target을 **명시적 3줄 이하 / 각 줄 15글자 이하**로 재작성했다.
- #030 직접 문제 문구는 **`우수한 무장은 「전기」 / 외에도 본래 능력에 따른 / 「특성」을 지닙니다`**, **`3DS 모드에서는 전쟁 때 / 「공격」「계략」으로 다양한 / 「전기」를 쓸 수 있습니다`**, **`본래 능력과 무장 데이터의 / 4가지 속성 단계에 따라 / 사용할 수 있게 됩니다`**로 정리했다. 나머지 17개 이벤트 대사도 같은 원문 의미를 유지하며 15×3 안으로 축약했다.
- direct Message `msgsec04/06/07/08`은 portrait 15×3과 다른 레이아웃 계열이다. v53의 `<=20 fullwidth-cell` 권위를 유지하고, v78에서는 네 파일을 v77과 byte-exact로 보존했다. 15×3 규칙을 Message에 일괄 적용하지 않는다.
- v78 빌더 내부 pre-audit에서 257개 중 20 violations가 targets와 정확히 일치함을 강제하고, 수정 후 같은 257개를 다시 검사해 **violations=0**을 강제한다. 완성 v78 위에서 빌더를 재실행해 `code.bin`과 report SHA가 첫 실행과 동일했다. 독립 `tools/verify_sangokushi2_v78_runtime_readback.py`도 #011 pointer/label readback, #030 257/0 audit, v77 이미지 6개 hash 보존, direct Message byte-exact를 모두 PASS했다.
- SHA-256: `code.bin=58F979B9277E2A48E3016DD03C2CA47B500862F00B20F256A77638171E1D05EB`, `report=3B6805E5C473185D4E7DD3CA9000D6A7D02F3CFBE430EFB3ACD5CC3464730DF7`. 빌더=`tools/build_sangokushi2_v78_github_issue011_030.py`, targets=`analysis/v78_github_issue011_030_targets.json`, report=`analysis/v78_github_issue011_030_report.json`, 독립 검증기=`tools/verify_sangokushi2_v78_runtime_readback.py`.
- **현재 Patch는 v78-intermediate, Citra 실화면 확인 대기**다. 우선 #011의 두 교환 화면에서 `병량/병사`와 기존 `금/아이템`을 확인하고, #030의 두 사마휘 화면이 각각 정확히 3줄 안에 모두 표시되는지 확인한다. 이후 다른 hardcoded 이벤트 대사는 전수 audit상 15×3 초과가 0이므로 플레이 중 회귀 여부만 관찰한다.

## option_down 019~032 이미지 재삽입 — v79-intermediate (2026-08-31)

- 작업 시작 시 문서와 실제 Patch를 대조해 최신 기준이 v78-intermediate임을 확인했다. v78 report의 37파일 manifest가 현재 Patch와 완전히 일치했고, `PYTHONIOENCODING=utf-8` 환경에서 v78 독립 검증기도 PASS했다.
- v78 전체를 `analysis/v79_option_down_images_baseline/PatchSnapshot`에 봉인하고, `Extracted_Image/RomFS/StartMenu/option_down/option_down_019.png`~`032.png` 14개만 `RomFS/StartMenu/option_down.g1t`에 삽입했다. 이미지 생성은 사용하지 않았다.
- 14개 PNG는 전부 RGBA 128×32이며 대응 texture는 type `0x09`, payload 16,384B다. 원 v78 payload 디코드→재인코드 byte-exact, 대상 payload 외 diff=0, 완성 Patch 역디코드와 PNG pixel-exact를 모두 확인했다.
- v78 대비 Patch 변경은 정확히 `RomFS/StartMenu/option_down.g1t` 하나다. option_down의 019~032 외 texture/header와 다른 Patch 36파일은 v78과 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v79 위에서 빌더를 다시 실행해 G1T와 report가 동일함을 확인했다. 독립 검증기는 14 textures / Patch 37 files PASS했다. SHA-256: `option_down=6E154CB3DFB1FD46AFCD89142DDEF177F8FABA5BB4328B5F37AC114F591CFB09`, `report=AE5A8887A9938E49AA5D513D6A9A3B75FAAC1305ED724656AA5A5F3EC68EAC44`.
- 빌더=`tools/build_sangokushi2_v79_option_down_images.py`, report=`analysis/v79_option_down_images_report.json`, 독립 검증기=`tools/verify_sangokushi2_v79_option_down_readback.py`. v79는 아래 v80의 sealed baseline으로 계승됐다.

## GitHub Issue #012/#013/#014/#015 특산 조건 전수 수정 + #031 — v80-intermediate (2026-08-31)

- 작업 시작 전 `tools/verify_sangokushi2_v79_option_down_readback.py`를 재실행해 **v79 = option_down 14 textures / Patch 37 files PASS**를 확인했다. 현재 v79 전체를 `analysis/v80_github_issue012_015_031_baseline/PatchSnapshot`에 봉인했고, v80 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개**다. v79의 option_down 이미지와 나머지 35개 Patch 파일은 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- 로컬 `Github_Issue/Issue012~015.html`과 각 PNG 총 16장을 확인했다. 스크린샷에서 `심복하게`, 깨진 `옥새/씨/북/농`, `진애 성/손애 성/조애 성`, `모아 る`, `정치 90 이땅ン인`, `10만譜ﾀ유하라`, 태수 조건의 어색한 `~로 만들어라` 등이 반복됐다. 단순 제보 항목별 수정이 아니라 같은 화면 source를 추적했다.
- **특산 획득조건의 실제 runtime bank는 `msgsec07` flat header[20]~header[65], 정확히 46개**로 확인했다. Original 일본어 logical string을 header pointer 기준으로 읽으면 `魅力80以上ﾉ / 武将ｦ3人集ﾒ` 뒤의 `ﾙ`, `政治90以` 뒤의 `上`, `会`+`稽太守`, `江夏…10万ｦ`의 마지막 `保`가 fragment 경계를 넘어 저장된 사례가 다수다. v53의 fragment 단위 번역이 이 경계를 독립 문장으로 처리하면서 현재 화면의 대량 오역/일본어 잔존이 발생했다.
- v80은 **기존 v79 msgsec07 body를 한 바이트도 다시 repack하지 않고**, EOF에 완전한 한국어 획득조건 46개를 각각 `05 05 05`와 함께 append해 header[20..65]만 새 absolute byte pointer로 redirect했다. 나머지 341개 runtime header word와 기존 body는 byte-exact다. append 증가량은 1,577B이며 최종 포인터는 모두 16-bit 범위 안이다.
- 주요 문구는 다음처럼 정리했다: `여포를 복종시켜라`, `황제의 인장을 소지하고 / 성이 유인 무장을 6명 모아라`, `업에 금 5만, 군량 50만 / 성이 조인 무장을 5명 모아라`, `충성도 100인 왕쌍을 / 천수 태수로 임명하라`, `성이 사마인 무장을 / 수춘 태수로 임명하라`, `충성도 100인 관우를 / 재동 태수로 임명하라`, `도시를 20개 이상 보유하고 / 모든 민충을 100으로 만들어라`, `강하에 군량 10만을 / 보유하라` 등이다. `司馬姓`은 특정 `사마애`라는 인물이 아니라 **성이 사마인 무장**이라는 뜻으로 확정했다.
- Citra 스크린샷에서 `씨=98DE`가 `애`처럼 깨지는 패턴이 반복됐고 `옥새`도 `옥=98F5/새=9961` far alias 조합 때문에 읽을 수 없게 표시됐다. 따라서 특산 조건에서는 `~씨 성`을 **`성이 조인/유인/진인/하후인/손인/제갈인/사마인`**으로, `옥새`를 **`황제의 인장`**으로 자연스럽게 풀어써 위험 alias를 피했다. #012 북평은 이미 검증된 local `북=89D0`, #015 홍농은 local `농=956B`를 사용해 global `98DD/98F2`를 쓰지 않는다. `냉정`도 미검증 far `냉=98C1`을 피하려고 같은 의미의 `침착한 무장`으로 정리했다.
- 46개 target은 전부 현재 runtime map/local override로 인코딩 가능하며 alias 누락·공유 충돌 0, 최대 3줄 / 각 줄 20 units 이하를 통과했다. 특히 #014의 `매력 80 이상의 / 무장을 3명 모아라`, #015의 `정치 90 이상의 / 무장을 5명 모아라`, `강하에 군량 10만을 / 보유하라`처럼 fragment orphan이 있던 항목도 standalone 문장으로 완전히 분리했다.
- **Issue #031:** 로컬 PNG에서 교환 자원 선택의 spaced `兵 士`와 확인창의 `交換:`이 별도 일본어 source로 남아 있음을 확인했다. 실제 source는 `code.bin 0x1D0804=兵 士`, `0x1CEDB8=交換`이며 각각 원 C-string slot 안에서 **`병사 / 교환`**으로 제자리 치환했다. 버튼 이미지는 건드리지 않았다.
- 완성 v80 위에서 빌더를 다시 실행해 `code.bin`, `msgsec07.dat`, report SHA가 첫 실행과 완전히 동일했다. 독립 `tools/verify_sangokushi2_v80_runtime_readback.py`도 changed files=2, 특산 header[20..65] 46/46 byte readback, non-target header/body 보존, `북=89D0/농=956B`, #031 두 C-string, v79 option_down SHA 보존을 모두 PASS했다.
- SHA-256: `code.bin=6813816AE28BAB1630E3BFB7F38FE7B3485C4191ECE0BB2871AF4F7D25022F61`, `msgsec07=14CFCEA275ED98863D65B6A68BD9BFDB509D3BDE4801CD3B4FF5CBD1C2F1240D`, `report=FCBB10D8257A320D7D7E57EAB69D3785F2FE930EC3645F55BA6836B0B5BA9B00`. 빌더=`tools/build_sangokushi2_v80_github_issue012_015_031.py`, targets=`analysis/v80_github_issue012_015_031_targets.json`, report=`analysis/v80_github_issue012_015_031_report.json`, 독립 검증기=`tools/verify_sangokushi2_v80_runtime_readback.py`.
- **현재 Patch는 v80-intermediate, Citra 실화면 확인 대기**다. 우선 #012~#015의 제보 화면과 #031 `병사/교환`을 확인하고, 특산 조건을 좌우로 넘기며 46개 전체에서 일본어 잔존·깨진 성씨·잘못된 태수 표현이 없는지 확인한다.

## GitHub Issue #032/#033/#034 보고서·시나리오 제목·이름/특기 수정 — v81-intermediate (2026-08-31)

- 작업 시작 시 현재 `Sangokushi 2 Patch` 37파일 manifest가 v80 report와 **완전 일치**함을 다시 확인하고, 이를 `analysis/v81_github_issue032_034_baseline/PatchSnapshot`에 봉인했다. v81 변경 파일은 정확히 **7개**: `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Scenario/fix_data.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec11.dat`다. 그 외 v80 파일과 v79 `option_down.g1t`는 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- **#032 보고서 첫 문장:** v80 `msgsec06 header[21] -> 0x3FB`의 시작이 `02 01 + 네`로 깨져 있었고, 실제 동적 군주명 토큰 **`02 01 C8`**의 마지막 `C8`이 소실된 회귀였다. v81은 기존 msgsec06 body를 byte-exact로 유지하고 EOF `0x1725`에 `02 01 C8 + 님, %s의\n보고서가 도착했습니다 + 05 05 05`를 append한 뒤 **header[21] 하나만 redirect**했다. 독립 역읽기에서 raw 시작 `0201C8`을 재확인했다.
- **#032 보고 항목 10개:** 실제 pointer table `code.bin 0x1AB350` 순서를 유지한 채, 사용자의 추가 지시대로 정확히 **`전쟁준비 / 훈련 / 수색 / 토지개발 / 경작 / 치수 / 상업투자 / 투함건조 / 몽충건조 / 주가건조`**로 번역했다. source C-string은 `0x1CF9FC, 1CFA08, 1CF9F0, 1CF9C0, 1CF9E4, 1CF9CC, 1CF9D8, 1CFDE0, 1CFDEC, 1CFDD4`; 4글자 target은 원 8B와 정확히 같고 짧은 항목은 원 slot 안 NUL+0 padding으로 끝낸다. pointer table 자체는 byte-exact다.
- **#033 시나리오 제목:** 실제 source는 `msgsec11` segment9/10의 군주선택 제목군이다. v62가 segment9 앞의 군주 선택 3문구만 고정 위치 한글화해 generic repacker 기준 segment9가 opaque였기 때문에, 처음 generic replacement 시도는 안전하게 중단됐다. v81은 **무수정 identity rebuild가 byte-exact임을 먼저 확인한 뒤**, 기존 segment9/10 구조를 직접 보존하는 custom rebuild를 사용한다. 특히 6번째 `손권이 독립하여 삼국이 정립됨`은 원래처럼 header[10] 경계를 가로지르게 유지하고 header[10]만 word 701→724로 갱신했다. 앞 3개 군주 선택 prompt와 뒤 3개 비대상 시나리오는 byte-exact다.
- #033 최종 10개 제목은 **`황건이 흥하니 영웅이 청운을 품다 / 영제의 몰락, 동탁의 학정은 극에 / 천하가 어지러워 군웅이 할거하다 / 유비, 자복하여 신야성에 의탁하다 / 와룡은 천하를 바라본다 / 손권이 독립하여 삼국이 정립됨 / 강유, 떠난 공명의 뜻을 계승한다 / 조조, 헌제를 옹립하고 대두 / 유비, 봉추를 동반하여 입촉하다 / 사마의, 5로를 통해 촉을 침공하다`**다. 사용자가 “거의 그대로, 길이/글리프 문제가 있으면 축약”을 허용했으므로, 현재 안전 map에 없는 `폭/춤/잇` 신규 far donor를 만들지 않고 각각 `학정`, `와룡은 천하를 바라본다`, `계승한다`로 최소 우회했다. 전부 20 units 이하(최대 19)다.
- **#034 이름 문제:** Citra에서 실패한 기존 far alias는 `겸=989F`, `총=989B`. 이름 전수 검사 결과 `겸`은 **송겸·도겸·상삼겸신·직강겸속 4명**, `총`은 **향총·만총 2명**이 공유했다. 따라서 제보된 두 명만 부분 수정하지 않고 6개 표시 이름 필드를 모두 재인코딩했다. 가나 독음과 이름 필드 밖 바이트는 byte-exact다.
- 새 이름 alias는 과거 report의 `alias/old_alias/new_alias` 전체와 v80 effective `code + 모든 Message + 모든 Scenario` raw reference를 전수 검사해 **historical ref=0 / current runtime ref=0 / v80 map owner=0 / lead<0x98**인 fresh 후보만 사용했다: **`겸=965A / physical3116`, `총=9754 / physical3298`**. 이름 밖 실제 한국어 사용도 함께 바꿔 `code.bin 0x1D20A6`의 `도겸`, `msgsec04 0x525` 및 `msgsec07 0x3B18`의 `총대장`을 동일 신규 alias로 재인코딩했다. 반면 code의 `0x281D/0x1C628F/0x1C7647`에 우연히 존재하는 `989B` ARM/data 바이트는 byte-exact 보존했다.
- 이름 renderer의 수직 bleed 방지를 위해 신규 `겸/총` glyph는 셀 안에서 1px 아래로 이동해 top row를 투명화했고, 바로 아래 physical `3189/3371` 기존 셀도 bottom row가 비어 있음을 증명한 후 1px 아래로 옮겨 top guard를 만들었다. font readback에서 변경 physical은 정확히 **3116/3189/3298/3371 네 셀뿐**이며 Sam3 glyph/guard readback이 pixel-exact다.
- **#034 특기 버튼 44개:** 실제 compact-label pointer table은 `code.bin 0x1DC300..0x1DC3AC`(44×4B)이며 NDS 한국어판 ARM9 `0x136D94`, stride8의 동일 순서 자료와 전부 교차검증했다. 사용자가 승인한 예외로 NDS `낙혈`은 **`함정`**으로 사용한다. 추가 제보 `[誘면]`은 Original `code.bin 0x1CF090 = 97 55 88 F8`, CP932 **`誘引`**임을 직접 확인했고 NDS 같은 위치도 `유인`이므로 v81에서 **`유인`**으로 수정했다. 즉 추측 번역이 아니다. 나머지 버튼도 NDS compact 용어 순서대로 44/44 한글화했고 pointer table은 byte-exact다.
- 첫 font 빌드 시험은 투명 픽셀 RGB(`0,0,0,0` vs A4 decode의 `255,255,255,0`) 때문에 full-byte readback gate가 실패했으며, 실제 alpha/glyph 차이는 없었다. helper를 packed-font canonical transparent RGB에 맞춰 수정했다. 또 generic msg11 replacement는 v62 때문에 segment9가 opaque라 중단했고 custom 구조 보존 rebuild로 전환했다. 마지막으로 header[10]의 의도적 변경까지 금지하던 검증식을 header 제외 payload 비교로 정정했다. 이 실패들은 Patch write 전 단계에서 발생해 불완전 산출물은 발행되지 않았다.
- 완성 v81 빌더를 **연속 2회 실행하여 7개 산출물 SHA-256이 완전히 동일**함을 확인했다. 별도 `tools/verify_sangokushi2_v81_runtime_readback.py`도 changed files=7, #032 `0201C8`+10 labels, #033 10 titles/segment6 경계, #034 font 4셀/6 names/44 traits/`誘引→유인`/`낙혈→함정`, msg04/msg07 exact pair, v80 특산 append bank와 비대상 Patch 파일 보존을 전부 PASS했다.
- v81 SHA-256: `code.bin=BBB971E99B7E026041A310CD38E0AD545FD292DCC8F1EA877262736C94BD860A`, `font.g1t=0385F4CDEE621C060BF9A187C0EE26601226F48B3C0F167B5F27D5DFAF5E160E`, `fix_data.bin=F5EA6F24CD06837F99036217B50D634A320460E2105C0C2C7AC4E3510205E44F`, `msgsec04=94F141BEA412E8FC77E044EE1C10534564FD55B5227D46620112A814C10CE934`, `msgsec06=7FF1202FC1F82045233730A041AE545683F4E6EC818B2E7FAE53FF8AF90F2C1A`, `msgsec07=A9E78DA3313341BE28629DE63FB9B3403D2E0D2452742168B2CD20BDD1D1B36A`, `msgsec11=B6B2970C5CF74105634B207CB3562C48C99E661D0ED97F0CFAE02881BB23D03F`.
- 빌더=`tools/build_sangokushi2_v81_github_issue032_034.py`, targets=`analysis/v81_github_issue032_034_targets.json`, report=`analysis/v81_github_issue032_034_report.json`, 독립 검증기=`tools/verify_sangokushi2_v81_runtime_readback.py`. **현재 Patch는 v81-intermediate이며 Citra 실화면 확인 대기**다.

## 지정 이미지 35개 재삽입 — v82-intermediate (2026-08-31)

- 시작 시 MD 기준 최신 revision이 v81임을 확인하고, `analysis/v81_github_issue032_034_report.json`의 37파일 manifest와 현재 Patch가 완전히 일치함 및 v81 독립 검증 PASS를 확인했다. v81 전체는 `analysis/v82_image_update_baseline/PatchSnapshot`에 봉인했다.
- 사용자 지정 PNG는 총 35개/16컨테이너다: option_down 1, Opening 2, EditBushou 25, Duel 7. 모든 PNG가 RGBA이고 대응 G1T type `0x09` 및 해상도와 일치했다. GPT 이미지 생성은 사용하지 않았다.
- v81에 있던 `option_down.g1t`, `edit_bushou_base_menu_down.g1t`는 sealed v81을 기준으로 했다. 나머지 Opening/EditBushou/Duel 14개 컨테이너는 v81 Patch에 없었으므로 immutable Original을 effective prior-Patch base로 최초 편입했다.
- 모든 source payload의 디코드→재인코드가 byte-exact였고, 교체 뒤 각 컨테이너 header·비대상 payload diff=0, 완성 Patch 역디코드와 35개 PNG pixel-exact를 확인했다. v81 대비 diff는 정확히 요청 대상 16개 G1T이며 Patch 파일 수는 51개다.
- 완성 v82 위에서 빌더를 재실행해 16개 G1T와 report가 동일함을 확인했다. 독립 검증기는 16 containers / 35 textures / Patch 51 files PASS했다. report SHA-256=`E9374E30F3E0C8BF105CF44B7015C17D6739E3289ED42B7A7BD6729A17853999`.
- 빌더=`tools/build_sangokushi2_v82_image_update.py`, report=`analysis/v82_image_update_report.json`, 독립 검증기=`tools/verify_sangokushi2_v82_image_readback.py`. v82는 아래 v83의 sealed baseline으로 계승된다.

## GitHub Issue #035 대사 미번역/오번역 정리 — v83-intermediate (2026-08-31)

- 시작 시 `analysis/v82_image_update_report.json`의 Patch manifest와 현재 `Sangokushi 2 Patch` 51파일이 완전히 일치함을 확인한 뒤 전체를 `analysis/v83_github_issue035_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 쓰지 않았다.
- #035 첫 스크린샷의 깨진 기존 번역 `하? 으로 진출해 / 대부분을 손에 베었다`의 Original `code.bin 0x1D2758`은 **`官渡の戦いで勝利した曹操は\n河北に進出し、\nその大半を手中に収めた`**임을 직접 확인했다. 따라서 **`관도전에서 승리한 조조는\n하북에 진출해\n대부분을 차지했다`**로 수정했다. `북`은 기존 low-risk alias **`89D0 / physical789`**를 재사용했고 현재 font 셀이 Sam3 `북` glyph와 pixel-exact임을 확인했다. `넣=8A8A` 신규 사용은 셀 차이 리스크 때문에 폐기하고 자연어 `차지했다`로 우회하여 **font 변경 0**으로 만들었다.
- #035 두 번째 스크린샷의 `주창해`/깨진 `?려 했다`는 Original `code.bin 0x1D2520`의 **`天下三分の計を唱え / 三国時代を築こうとしていた`**를 확인한 뒤 **`제갈량은 삼분지계를 주장해 / 삼국시대를 세우려 했다`**로 수정했다. 사용자가 지적한 `주창해→주장해`를 반영했고 깨진 기존 `열` alias를 사용하지 않는다.
- 조사 과정에서 과거 v53 `code.bin 대사 282/282` 완료 집계가 `0x55E0C~0x57AA8`, `0x583A4~0x5AEF8`의 별도 fixed C-string 시나리오/이벤트 runtime bank를 포함하지 않았음을 확인했다. 해당 untouched bank는 v82에서 Original과 byte-exact였고, NDS 한국어판 ARM9 대응문을 기준으로 자연스럽게 축약하여 **기존 오번역 2개 + 시나리오 대사 bank + 시나리오 서술/목표 bank 총 169개 target**을 적용했다.
- 모든 169개 target은 Original NUL 고정 슬롯 길이 이내, hardcoded 대사 **15 visible cells × 최대 3 visual lines** 이내이며 현재 v81/v82 runtime alias map으로 전부 인코딩 가능하다. 초기 후보에서 미등록 한글 39개가 나왔으나 자연스러운 동의어/문장 재구성으로 최종 **신규 glyph/donor 0개**가 됐다.
- v83은 **`ExeFS/code.bin` 1개만 변경**하며 v82 이미지 35개/16 G1T, v81 font/fix_data/Message, 나머지 Patch 파일은 byte-exact다. code diff는 target slot 밖 0바이트, 실제 변경 code bytes=7,147이다.
- 완성 v83 빌더를 연속 2회 실행해 `code.bin` SHA-256 **`A2EC5187F91E5E984B3734BAECE2E514380A27120FA2B2CCF79A457188372F63`**, report SHA-256 **`A509C6F2CFCAFD375C8213204DD0CAE32FEA83CF22E7E91091ADAD7E7C27DCD2`**가 완전히 동일함을 확인했다. 독립 `tools/verify_sangokushi2_v83_runtime_readback.py`도 169/169 readback, 원 슬롯, 15×3, #035 두 원문/번역 교차검증, `북=89D0` 기존 font 셀 pixel-exact, changed files=`code.bin` only를 PASS했다.
- 빌더=`tools/build_sangokushi2_v83_github_issue035.py`, targets=`analysis/v83_github_issue035_targets.json`, report=`analysis/v83_github_issue035_report.json`, 독립 검증기=`tools/verify_sangokushi2_v83_runtime_readback.py`. v83은 아래 v84의 sealed baseline으로 계승된다.

## GitHub Issue #036 시나리오 제목 순서/년월 수정 — v84-intermediate (2026-08-31)

- 시작 시 `analysis/v83_github_issue035_report.json`의 51파일 manifest와 실제 Patch가 51/51 완전 일치함을 확인한 뒤 `analysis/v84_github_issue036_baseline/PatchSnapshot`에 v83 전체를 봉인했다. Original/Rebuild/Backup은 쓰지 않았다.
- Issue036의 10개 PNG/본문 순서를 다시 확인했다. 사용자 확인에 따라 선택 화면의 권위 순서는 스크린샷에 표시된 **184년1월 → 189년1월 → 194년1월 → 196년1월 → 201년1월 → 208년1월 → 213년1월 → 221년1월 → 223년1월 → 235년1월**이다. `image_008`의 221년 PNG는 사용자가 잘못 올린 캡처라고 확인했으므로 visual evidence에서는 제외하지만 본문 지정 제목은 권위로 사용한다.
- v83 `msgsec11`의 기존 logical title 순서는 **184→189→194→201→208→221→235→196→213→223**이어서 선택 화면이 순번대로 읽을 때 196년 이후 제목이 서로 다른 시나리오에 붙었다. v84는 visible 10개를 화면 연대순으로 **`황건 봉기와 영웅의 비상 / 영제 몰락 동탁의 폭정 / 난세의 군웅할거 / 헌제 옹립과 조조의 비상 / 유비, 신야에 의탁 / 와룡, 천하를 바라보다 / 유비, 봉추와 입촉 / 손권의 독립과 삼국정립 / 사마의, 5로로 촉 침공 / 강유, 공명의 뜻을 잇다`** 순으로 재구성했다.
- 숨은 3개도 실제 Original `msgsec11`에 `英雄集結し覇を争う / 反董卓連合瓦解す / 何進、簒奪し天下乱れる`가 존재함을 확인했고 각각 **`시대초월 영웅집결 / 반동탁 연합와해 / 하진, 찬탈과 천하혼란`**으로 적용했다. visible/hidden 13개 모두 한 줄 20 units 이하이다.
- 같은 시나리오 선택 화면의 날짜 포맷은 `code.bin 0xBA1C4`의 `1B 43 30 25 33 64 94 4E 20 25 73 20 25 32 64 8C 8E` = `<ESC>C0%3d年 %s %2d月`로 특정했다. v84는 길이를 바꾸지 않고 `年=944E→년=88B2`, `月=8C8E→월=8DEF` 4바이트만 수정했다.
- 요청 문구의 기존 map 미등록 글자는 `폭/잇/탈` 3개다. 현재 v83 runtime raw ref=0, 기존 map owner=0, 과거 alias 기록=0인 fresh Original-valid donor를 선정해 **`폭=8BDE/physical1179`, `잇=984A/physical3476`, `탈=98F4/physical3645`**로 추가했다. `폭`은 lead<0x98 저위험이고 `잇/탈`은 lead>=0x98 far alias라 정적 pixel-exact만으로 런타임 성공을 주장하지 않는다. #037의 교훈을 반영해 victim glyph 자체를 1px 아래로 내리는 처리는 하지 않았고 font 변경 physical은 정확히 세 셀뿐이다.
- v84 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec11.dat` 3개**다. v83의 #035 code 수정, v82 이미지 35개/16 G1T, 그 외 48개 Patch 파일은 byte-exact다.
- 완성 v84 빌더를 연속 2회 실행해 3개 게임 파일과 report 해시가 완전히 동일했다. 독립 `tools/verify_sangokushi2_v84_runtime_readback.py`도 changed files=3, 년/월 4바이트, visible 10개 화면순서, hidden 3개, 신규 alias가 msgsec11에서 각 1회만 참조됨, font 3셀 pixel-exact, 비대상 48파일 보존을 전부 PASS했다.
- v84 SHA-256: `code.bin=F1C36B58F7CB447C09536818617681E01731D5C8719333EC32902299B01527A7`, `font.g1t=2927335857E0F2846C48ACCD51BBF15964C9B6C2005B49A24E75ADC597C84664`, `msgsec11.dat=B1815A8FD99AA4AC9207D980177354FA4346F5EF8DE2CEF1A75C19359A7F6DF8`, report=`B472291DC92053275216AF65086E65FA603E349EB1CB69501FABB2B74700B44`.
- 빌더=`tools/build_sangokushi2_v84_github_issue036.py`, targets=`analysis/v84_github_issue036_targets.json`, report=`analysis/v84_github_issue036_report.json`, 독립 검증기=`tools/verify_sangokushi2_v84_runtime_readback.py`. 이후 Citra에서 #045/#046 회귀가 확인되어 v84 방식은 폐기되고 아래 v85로 대체됐다.

## GitHub Issue #045/#046 시나리오 제목 fixed-start 복구 + #035 Crash 방지 재인코딩 — v85-intermediate (2026-08-31)

- 사용자 Citra 제보 #046를 로컬 이슈/PNG와 대조했다. `image_002`는 `code.bin 0x56D28`의 `그만해라, 장비 / 공명과 나는 물과 물고기 / 하나와 같다` 출력 직후 100% Crash가 나는 상황이며, **다음 문자열 `0x56D64`의 `렇=994F` far alias**가 첫 직접 위험 후보였다. #035 v83의 169개 target 전체를 다시 감사한 결과 **42개 target/23종 문자가 lead>=0x98 far alias**를 사용하고 있었다. 정적 slot/readback PASS만으로 런타임 안전을 주장한 v83 검증은 불충분했다.
- #046는 한 글자만 땜질하지 않고 #035 169개 target을 전체 재인코딩했다. 자연어 치환으로 위험 글자를 제거하고, 역사명/시나리오 제목에 꼭 필요한 글자만 신규 저위험 donor로 분리했다. 최종 신규 alias는 **`헌=8972/physical696`, `촉=8D6F/1445`, `폭=92F9/2522`, `잇=8ED1/1730`, `탈=95A9/3006`, `찌=9179/2207`, `익=92CB/2476`, `룡=92DE/2495`**다. 8개 모두 v84 effective code+모든 Message+Scenario raw ref=0, 기존 map owner=0, lead<0x98, 바로 아래 atlas 셀 top row ink=0을 검증했다. glyph는 정상 baseline 그대로 쓰고 victim/guard cell을 이동시키지 않는다.
- #046 Crash 다음 문구 `0x56D64`은 **`그 뜻이 확고하다면 / 우린 더 말하지 않겠습니다`**로 수정했다. 문제 제보의 `남팽으로 향해 남정을 시작했다`는 `0x58DC8`의 기존 `쪽=9945` far alias 때문에 `남쪽`이 깨진 것이 핵심이었고, Original `曹操は次の目標を南に向け、南征を開始する`를 다시 확인해 **`조조는 남방을 목표로 정해 / 남정을 시작했다`**로 정리했다. 최종 169개 target은 **far alias 사용 0, encode error 0, Original fixed slot 초과 0, 15×3 위반 0**이다.
- #045의 9개 새 Citra PNG를 OCR/본문과 대조했다. 189/194 제목 누락, 196 화면에 221 제목 일부, 201 화면에 196 제목, 208 화면에 201 제목, 213 화면에 223 제목, 221 화면에 208 제목, 223 제목 소실, 235 화면에 213 제목이 보였다. 이는 v81/v84처럼 title 길이에 따라 `msgsec11` segment를 재조립하면 **게임이 계속 참조하는 원래 물리 시작 주소와 문자열 위치가 어긋나는 것**으로 확정했다.
- #045는 `analysis/v81_github_issue032_034_baseline/PatchSnapshot/RomFS/Message/msgsec11.dat`의 **v80 fixed layout**을 구조 권위로 복원했다: file size **1560B**, header[10]=**701**, 모든 `05 05 05` separator 위치 byte-exact. visible storage index는 `184,189,194,201,208,221,235,196,213,223`이며 fixed starts는 **`0x4FA,0x511,0x528,0x540,0x558,0x56F,0x586,0x59E,0x5B2,0x5C7`**이다. 221 title은 원래처럼 segment9/10 경계를 가로지르는 20B slot을 유지한다. 각 한국어 제목은 원 slot 안에만 쓰고 남는 바이트는 ASCII space로 채워 뒤 start를 절대 이동시키지 않는다.
- v85 visible compact 제목은 storage 순서로 **`황건봉기와 영웅비상 / 영제몰락 동탁의폭정 / 난세의 군웅할거 / 유비, 신야에 의탁 / 와룡,천하를 바라보다 / 손권독립과 삼국정립 / 강유,공명의 뜻을잇다 / 헌제옹립 조조비상 / 유비, 봉추와 입촉 / 사마의,5로로 촉침공`**이다. 화면 연대순은 이 storage order와 다르므로 다시 순서대로 재배열하지 않는다. hidden은 **`초월 영웅집결 / 반동탁 연합와해 / 하진,찬탈 천하혼란`**으로 원 15/15/19B slot에 맞췄다.
- v84가 수정한 `code.bin 0xBA1C4` formatter는 이 화면 source가 아니었다. Citra #045에서 `189年 1月` 등이 그대로 보였고, 실제 scenario-select string table 후보를 추적해 **`0x1CFA6C=%3u年`, `0x1CFD50=%2u月`, standalone `0x1CFA74=年`, `0x1CFD58=月`**을 확인했다. v85는 단위 2바이트만 각각 `년=88B2`, `월=8DEF`로 치환한다. v84의 0xBA1C4 수정은 base에서 그대로 보존한다.
- v84 임시 title donor `폭=8BDE`, `잇=984A`, `탈=98F4`은 폐기했다. v85 font는 sealed v83 font를 구조 기준으로 사용해 physical **1179/3476/3645**를 v83 상태로 복원하고 위 신규 8셀만 추가한다. 독립 검증에서 변경 셀은 정확히 `696/1445/1683/1730/1867/2207/2476/2495`만이며 모두 Sam3 glyph pixel-exact, 아래 guard top row=0이다.
- 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec11.dat` 3개**이고 Patch 파일 수는 51개다. 완성 빌더를 연속 2회 실행해 3개 게임 파일과 report SHA가 완전히 동일했다. 독립 `tools/verify_sangokushi2_v85_runtime_readback.py`도 #035 169개 far=0, #046 두 제보 offset, #045 date units, 신규 font 8셀, retired v84 donor 복원, msgsec11 fixed size/header/separator/start/13 title slot을 전부 PASS했다.
- v85 SHA-256: `code.bin=4C932F1F6C3D72806DB4377A1B0DDCA33E371F13DAF67218036C2CA4B501CF26`, `font.g1t=251F6D8E002F894FB5FE8FA0497CC62F9C9F14AD565DAAAEB87DCD741FE9AC70`, `msgsec11.dat=7F83A6A5ED43F945C59F49B7716991E21AB71E8980742C83C6EF6DFDCC9054E5`, report=`8975F3E5DBC72A76FA170CB4B5D28889284AF7F939EA2354D01D2346A11A3EE1`.
- 빌더=`tools/build_sangokushi2_v85_github_issue045_046.py`, targets=`analysis/v85_github_issue045_046_targets.json`, report=`analysis/v85_github_issue045_046_report.json`, 독립 검증기=`tools/verify_sangokushi2_v85_runtime_readback.py`. v85는 아래 #047 분석에서 Crash 원인 가설이 잘못됐음이 확인되어 v86으로 대체된다.

## GitHub Issue #047 ARM branch 1바이트 침범 복구 — v86-intermediate (2026-08-31)

- 사용자 Citra에서 v85도 `그만해라, 장비 / 공명과 나는 물과 물고기 / 하나와 같다` 출력 직후 100% Crash가 계속됨을 확인했다. 로컬 `Github_Issue/Issue047.html`과 제보 내용을 기준으로 v85의 `0x56D64` far-alias 가설을 폐기하고 실제 ARM 제어 흐름을 역추적했다.
- Original `code.bin`에서 `0x56C48 = ADR r1,0x56D28`, `0x56C4C = BL 0xEF8D8`, `0x56C50 = ADR r1,0x56D64`, `0x56C54 = MOV r0,#0x47`, `0x56C58 = B 0x57D38`, 그리고 `0x57D38 = BL 0xEF8D8`임을 확인했다. 그러나 **현재 v85의 `0x56C58` word는 `89000436`**이었고 Original은 **`EA000436`**이다. 즉 분기 opcode의 최상위 `EA` 1바이트가 한국어 문자열 바이트 `89`로 덮여 있었고, 이 때문에 첫 대사 뒤 다음 분기로 진행할 때 CPU 실행 코드가 깨져 Crash하는 것이 직접 원인이다.
- 근본 원인은 v83 #035 target inventory의 **1바이트 시작점 오판**이다. 문제 target `0x56C5B`는 실제 문자열 시작이 아니며, `0x56C58` ARM `B` 명령의 마지막 바이트 위치다. 실제 runtime ADR은 **`0x56C5C`**를 가리키고 Original을 그 위치에서 디코딩하면 정확히 `劉琦と劉琮 / どちらを後継者とするべきか…`가 나온다. v83은 `0x56C5B`부터 한국어를 써 `EA→89`로 branch opcode를 파괴했다.
- 같은 오류를 169개 전체에서 감사한 결과 **정확히 10개**가 동일 패턴이었다. 잘못된 시작/실제 ADR 시작은 `55F73→55F74`, `5641F→56420`, `5673F→56740`, `56923→56924`, `56B03→56B04`, `56C5B→56C5C`, `56E1B→56E1C`, `56FA7→56FA8`, `57383→57384`, `58B1F→58B20`이다. 10개 모두 bad offset은 `branch_offset+3`, Original word는 `EAxxxxxx`, 실제 runtime ADR은 bad+1을 직접 가리킨다.
- v86은 10개의 ARM branch 4바이트를 Original과 byte-exact로 복구하고, 각 v85 한국어 문구는 **문구 변경 없이** 실제 ADR 시작인 `bad+1`의 Original C-string slot에 다시 기록했다. 10개 모두 수정된 1바이트 짧은 실제 slot에 여전히 들어가며 overflow=0이다. #047의 `0x56D28` 표시문과 `0x56D64` 다음 문구는 v85와 byte-exact로 보존했다. font/msgsec11/이미지/Scenario/기타 Message도 전부 v85 byte-exact다.
- 변경 Patch 파일은 정확히 **`ExeFS/code.bin` 1개**다. 완성 v86 빌더를 연속 2회 실행해 동일 SHA-256을 확인했고 독립 readback도 PASS했다. #047 branch는 **`89000436 → EA000436`**, 복구 후 branch target=`0x57D38`, 10개 branch 모두 Original word와 일치, 허용 repair range 밖 code diff=0이다.
- v86 `code.bin` SHA-256=`EC803F46FFCF11D1BA20D46290FA6F76A10C86007B345AE2C5D7CEFD0A1DAF2A`. 빌더=`tools/build_sangokushi2_v86_github_issue047_crash_safe.py`, targets=`analysis/v86_github_issue047_crash_safe_targets.json`, report=`analysis/v86_github_issue047_crash_safe_report.json`, 독립 검증기=`tools/verify_sangokushi2_v86_runtime_readback.py`.
- **사용자 Citra 확인 완료:** #047의 동일 대사 뒤 강제 종료 없이 다음 대사/이벤트로 정상 진행됐다. 따라서 v86의 ARM branch 복구 방식과 10개 동일 overlap target 보정은 런타임 성공으로 승인한다.

## 용어 통일 `병량` → `군량` — v87-intermediate (2026-08-31)

- 사용자 요청에 따라 게임 내 한국어 용어 **`병량`을 전부 `군량`으로 통일**했다. 의미상 같은 `兵糧`에 대해 한국어 현지화 용어는 앞으로 `군량`을 권위로 사용하며 `병량`은 신규 번역/수정에서 사용하지 않는다.
- v86 Patch 51파일 전체를 현재 한글 alias로 전수 스캔한 결과 실제 연속 `병량` 인코딩은 **정확히 50회**였다. 파일별로 `ExeFS/code.bin` 7회, `msgsec07.dat` 2회, `msgsec10.dat` 10회, `msgsec15.dat` 31회이며 font/G1T/Scenario/그 밖의 Patch 파일에서는 0회였다. 상인 `병량매각/병량매입`, 교환 자원 버튼, 군사/개발/치수/시혜/세율/전투 도움말, 이벤트 대사 등 실제 텍스트 경로가 모두 포함된다.
- 현재 alias는 `병=8953`, `군=88C7`, `량=89E9`이므로 `병량=8953 89E9` → `군량=88C7 89E9`의 **동일 4바이트 제자리 치환**만 수행했다. 각 occurrence에서 실제 diff는 첫 글자 2바이트뿐이며 총 runtime 변경 바이트는 100B다. 파일 크기/포인터/fragment 시작/제어코드/폰트/이미지는 움직이지 않는다.
- v87 변경 Patch 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `msgsec10.dat`, `msgsec15.dat` 4개**다. 완성 빌더를 두 번 연속 실행해 동일 결과를 확인했고 Patch 전체 재검색에서 `병량` runtime sequence는 **0회**다.
- 직전 Citra 승인된 #047은 회귀 방지를 위해 `code.bin 0x56C58` ARM branch와 `0x56D28/0x56D64` 대사 구간을 v86과 byte-exact로 보호했고 PASS했다. 따라서 이번 용어 변경은 #047 실행 코드 복구를 건드리지 않는다.
- 빌더=`tools/build_sangokushi2_v87_bingliang_to_gunryang.py`, report=`analysis/v87_bingliang_to_gunryang_report.json`. **현재 Patch는 v87-intermediate**다. Citra에서는 대표적으로 상인 `군량매각/군량매입`, 외교 교환 `군량`, 도움말/대사의 `군량` 표기를 확인한다.

## 지정 이미지 36개 재삽입 — v88-intermediate (2026-08-31)

- 시작 시 MD 기준 최신 revision이 v87임을 확인하고 `analysis/v87_bingliang_to_gunryang_report.json`의 51파일 manifest와 현재 Patch가 완전히 일치함을 확인했다. v87 전체는 `analysis/v88_image_update_baseline/PatchSnapshot`에 봉인했다.
- 사용자 지정 PNG는 총 36개/8컨테이너다: `ktlogo` 1, `staff_roll` 1, `stg_monarch_edit` 2, `stg_map` 8, `Tutorial` 1, `stg_foreign_diplomacy` 6, `stg_city_edit_sub` 1, `stg_city_edit` 16. 이미지 생성은 사용하지 않았다.
- v87에 있던 `ktlogo.g1t/Tutorial.g1t`는 sealed v87을 기준으로 했다. v87에 없던 StaffRoll/Stg 6개 컨테이너는 immutable Original을 effective prior-Patch base로 최초 편입했다.
- 모든 PNG가 RGBA이고 대응 G1T type `0x09` 및 해상도와 일치했다. source payload 디코드→재인코드 byte-exact, 대상 payload 외 diff=0, 완성 Patch 역디코드와 36개 PNG pixel-exact를 확인했다.
- v87 대비 diff는 정확히 요청 대상 8개 G1T이며 Patch 파일 수는 57개다. v87의 `병량→군량` 50회, ARM branch 복구와 다른 Patch 파일은 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v88 위에서 빌더를 재실행해 8개 G1T와 report가 동일함을 확인했다. 독립 검증기는 8 containers / 36 textures / Patch 57 files PASS했다. report SHA-256=`A1D05421198834540537255D0D540D12AC8A483CDC66F0A474CC77A75647D2EE`.
- 빌더=`tools/build_sangokushi2_v88_image_update.py`, report=`analysis/v88_image_update_report.json`, 독립 검증기=`tools/verify_sangokushi2_v88_image_readback.py`. **현재 Patch는 v88-intermediate, Citra 실화면 확인 대기**다.

## 특기 `単騎→단기` + GitHub Issue #037 인물명 글리프 수정 — v89-intermediate (2026-08-31)

- 시작 시 `analysis/v88_image_update_report.json`의 57파일 manifest와 현재 Patch가 완전히 일치함을 확인하고 v88 전체를 `analysis/v89_trait_issue037_baseline/PatchSnapshot`에 봉인했다. v89 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Scenario/fix_data.bin` 3개**이며, 나머지 54개 Patch 파일과 v88 이미지 36개/8컨테이너는 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- compact 특기 pointer table `code.bin 0x1DC300`의 index7은 Original `単騎`, 실제 문자열 위치 `0x1CFA8C`임을 재확인했다. v81의 `일기=8E90 88A3`를 사용자 지정 **`단기=92AD 88A3`**로 동일 4바이트 제자리 치환했으며 pointer table과 NUL은 보존했다. 별도 장문 특기 `単騎駆け`(`0x1CEF78`)는 이미 **`단기돌진`**이어서 byte-exact 보존했다.
- #037 조예 화면의 `조?`는 안전 `예` glyph가 v71에서 `927D/physical2399`에 만들어졌지만 인물명 테이블은 여전히 깨지는 전역 alias `989D`를 사용한 것이 원인이었다. 재발 방지를 위해 `예`가 포함된 표시 이름을 전수 조사해 **조예(曹叡)·조예(趙叡)·예형·전예·두예 5명** 모두를 `927D`로 재인코딩했다. 1,020개 표시 이름에서 `989D` 잔존 0회, `927D` 정확히 5회이며 가나 독음 17바이트와 다른 이름 필드는 byte-exact다.
- #037 도겸 화면에서 `겸`이 낮아진 직접 원인은 v81 빌더가 `겸=965A/physical3116` glyph를 의도적으로 1px 아래로 이동한 처리였다. 이번에는 작업 폴더의 Sam3KRFont 원본 `겸`을 **이동 없이 정상 기준선으로 재생성**했고, v81에서 함께 아래로 이동했던 바로 아래 physical3189 셀은 sealed v80 상태로 복원했다. 같은 이유로 `예=927D/physical2399`도 Sam3KRFont 정상 기준선으로 재생성했다. font diff는 정확히 physical **2399/3116/3189** 안에만 존재하며 packed readback이 pixel-exact다.
- 완성 빌더를 연속 2회 실행해 3개 게임 파일 SHA가 동일했다. 독립 `tools/verify_sangokushi2_v89_runtime_readback.py`는 `単騎→단기`, `単騎駆け→단기돌진` 보존, 특기 pointer table, 5개 `예` 이름, 정상 기준선 `예/겸`, 겸 아래 셀 복원, 비대상 v88 파일 보존을 모두 PASS했다. Citra 실화면 확인은 아직 대기다.
- SHA-256: `code.bin=2855FEEBC17C375AD951570847609E9EE9E2126420BA01A0667FDACDCA343C3E`, `font.g1t=148FAE23663DBDDEC3D9FCE504D2F012AF359EE440B24E656C8CABC29E46D368`, `fix_data.bin=8C38C65A167380CFDE12A36375468FB2A9855E667DAE38973D041E13D5FDC78E`, report=`710D3B7A4B6635B22382B3BE59F131603FE361D9F2CDB69944B5949C0308105B`.
- 빌더=`tools/build_sangokushi2_v89_trait_issue037.py`, targets=`analysis/v89_trait_issue037_targets.json`, report=`analysis/v89_trait_issue037_report.json`, 독립 검증기=`tools/verify_sangokushi2_v89_runtime_readback.py`. **현재 Patch는 v89-intermediate**다.

## GitHub Issue #038 / #040 / #041 수정 — v90-intermediate (2026-08-31)

- 작업 시작 전에 `tools/verify_sangokushi2_v89_runtime_readback.py`를 다시 실행해 현재 Patch가 v89 report와 일치함을 확인했다. 정확한 v89 전체 Patch 57파일은 `analysis/v90_github_issue038_040_041_baseline/PatchSnapshot`에 봉인했다. v90 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 2개**이며, font/fix_data/이미지/다른 Message를 포함한 나머지 55파일은 v89와 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- **#038 모집 글자 혼합 표시:** 수량 질문 formatter `code.bin 0x16084C = 몇백 명을 %s합니까？`는 이미 정상 한글이므로 byte-exact 보존했다. 실제 `%s` 인자는 별도 공용 명칭 `0x1CFE88=徴兵`, `0x1CFE98=募兵`임을 확인해 각각 동일 4B **`징병=8DAD8953`, `모집=88B088E8`**으로 제자리 치환했다. formatter나 다른 징병/모병 UI 문자열은 수정하지 않았다.
- **#040 무장편집 상단 설명:** 로컬 Issue040 HTML과 PNG 3장을 확인한 뒤 `msgsec01.dat`의 fixed upper block이 v89에서도 Original 일본어 그대로임을 확인했다. `0x3CD~0x471` 안의 8개 슬롯만 **`신규등록 / 신무장 만들기 / 내용변경 / 작성한 신무장의 설정을\n변경합니다 / 삭제 / 신무장 삭제 / 무장편집 / 사실무장의 내용 편집`**으로 치환했다. colored title의 `<ESC>C9/C1`, 원 fragment 시작, separator, 파일 크기 **9057B**를 보존했다. 마지막 설명 슬롯의 `0A0A918A90`은 다음 `相性...` 문장의 fragment-boundary tail이므로 byte-exact 보존했고, **0x472의 `msgsec01_0006` 이후는 v89와 완전 동일**하다.
- **#041 상업투자 실행 확인:** 스크린샷에 실제 표시된 `よろしいですか？`와 동일한 CP932 raw 문자열은 현재 v89 `code.bin`에서 **`0x1CF4A1` 1곳뿐**임을 재확인했다. 16B 기존 suffix slot 안에서 **`괜찮으십니까？`**(14B)+NUL+zero padding으로 교체했다. 바로 뒤 `0x1CF4B4=セーブしますか？`와 주변 bytes는 v89와 byte-exact다. v90 역읽기에서 해당 일본어 raw 잔존은 0회다.
- target alias gate는 전체 v90 문구에 대해 missing=0 / shared-alias=0으로 통과했다. 완성 빌더를 연속 2회 실행했고 두 결과 SHA가 동일했다. 독립 `tools/verify_sangokushi2_v90_runtime_readback.py`는 변경 파일 2개, #038 formatter/동적 명칭, #040 fixed 8슬롯/9057B/0x472 이후 보존, #041 unique raw 제거/인접 문자열 보존, 허용 범위 밖 diff **0바이트**를 모두 PASS했다.
- v89 대비 실제 diff는 `code.bin` **24 bytes**, `msgsec01.dat` **113 bytes**이며 허용된 범위 밖 diff는 0이다. SHA-256: `code.bin=69512B1449C7401D510284BC7D10D14D41B36974BEAA1246DC5DCBEAAA9083EF`, `msgsec01.dat=E34BE5778D41BDAD1247B2C467C1D62D804BFC008C200978F6905CF1338C16F7`.
- 빌더=`tools/build_sangokushi2_v90_github_issue038_040_041.py`, targets=`analysis/v90_github_issue038_040_041_targets.json`, report=`analysis/v90_github_issue038_040_041_report.json`, 독립 검증기=`tools/verify_sangokushi2_v90_runtime_readback.py`. v90은 아래 v91의 sealed baseline으로 계승되며, #041 source 판정은 후속 Citra 결과로 폐기됐다.

## Issue #040 / #041 Citra 후속 수정 — v91-intermediate (2026-09-01)

- 사용자 v90 Citra 실화면에서 #040 제목 `삭제`의 첫 글자가 **`곡`으로 표시되어 `곡제`**가 되었고, #041 상업투자 확인창은 여전히 **`よろしいですか？`** 그대로임을 확인했다. 따라서 정적 readback보다 Citra를 우선해 v90의 #041 `code.bin 0x1CF4A1` source 판정을 실패로 취소했다. 정확한 v90 전체 Patch 57파일을 `analysis/v91_issue040_041_runtime_fix_baseline/PatchSnapshot`에 봉인했다.
- **#040 `삭` 런타임 alias:** v90의 `삭=98CD/physical3606` font cell 자체는 Sam3KRFont와 pixel-exact였으나 Citra에서 `곡`으로 표시되어 far-alias 런타임 실패로 확정했다. 역사적 alias/현재 runtime ref/global owner를 모두 제외한 fresh original-valid 후보를 재탐색했고 `9956/physical3676`을 #040 전용 local override로 선택했다. 바로 아래 physical3749의 top-row ink=0이라 별도 guard 이동 없이 Sam3 `삭` 정상 기준선 glyph만 physical3676에 삽입했다. font diff는 해당 셀 내부 **122 pixels**, 셀 밖 diff=0이다. Citra 승인 전까지 `9956`은 전역 map에 승격하지 않는다.
- **#040 설명 바이트:** 사용자 희망 `신무장을 삭제합니다.`는 한글 인코딩 **20B**라 원 14B fixed slot에는 들어가지 않는다. 다음 fixed title 시작 `0x447`을 유지하면서 3B separator 중 검증된 방식대로 1B만 차용하면 안전 상한은 **15B**다. 따라서 설명을 **`신무장 삭제하기`(15B)**로 확장하고 `05 05` separator를 남겼다. 제목은 `삭제`를 local `삭=9956`으로 재인코딩했으며 `0x447` 이후는 v90과 byte-exact다.
- **#041 실제 runtime 후보 재추적:** `RomFS/Message/msgsec17.dat`는 71B, header words `[4,10,23,40]`인 4-entry absolute-byte-pointer 테이블이며 **header[1]=10이 독립 `ﾖﾛｼｲﾃﾞｽｶ？`를 직접 가리킨다.** 앞 문구 없이 질문만 표시되는 Citra 화면 형태와 일치한다. 기존 body는 이동하지 않고 EOF byte71에 사용자 1안 **`진행하시겠습니까？`(18B)+`05 05 05`**를 append하고 header[1]만 `10→71`로 변경했다. 새 파일 크기는 **92B**, 다른 header pointer `23/40`과 기존 body는 byte-exact다. v90에서 잘못 바꾼 `code.bin 0x1CF4A1` 16B는 sealed v89 원문으로 정확히 복원했으며 #038 `징병/모집` 변경은 그대로 유지했다.
- v91 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec17.dat` 4개**다. 나머지 53개 Patch 파일은 v90과 byte-exact이고 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 빌더를 연속 2회 실행해 4파일 SHA가 동일했다. 독립 `tools/verify_sangokushi2_v91_runtime_readback.py`는 #040 local `삭`, physical3676 pixel readback, 15B 설명/`0x447` 보존, #041 v90 wrong-source 복원, msgsec17 header `[4,10,23,40]→[4,71,23,40]`, append target, #038 보존, 허용 범위 밖 diff **0**을 모두 PASS했다.
- SHA-256: `code.bin=62EC99B0C2B76B8C5A3E3BF4B1CAD089989A62F4CC89F62D865FAD8B24A4519B`, `font.g1t=4F58C519BAB1F224C06FCE47A77476522AE051CB584E560536A44CB5385C7D13`, `msgsec01.dat=96B15D3C4334A678CE4BB3F0D28D4BE14CCF88C0AB5E50EB3374CC7F9AE0E32F`, `msgsec17.dat=1FF3E57AFD814C4C0A2D8ECF475D614D4E9C7A07A928C4DE39CBEC44A68931FA`.
- 빌더=`tools/build_sangokushi2_v91_issue040_041_runtime_fix.py`, targets=`analysis/v91_issue040_041_runtime_fix_targets.json`, report=`analysis/v91_issue040_041_runtime_fix_report.json`, 독립 검증기=`tools/verify_sangokushi2_v91_runtime_readback.py`. v91은 아래 v92의 sealed baseline으로 계승된다.

## GitHub Issue #040 / #042 / #043 수정 — v92-intermediate (2026-09-01)

- 사용자 v91 Citra 실화면에서 #040 `삭제`의 `삭=9956`도 한자형으로 표시되어 **fresh far-alias 방식이 다시 실패**했음을 확인했다. 시작 전에 `tools/verify_sangokushi2_v91_runtime_readback.py`를 실행해 현재 Patch가 v91 report와 정확히 일치함을 확인하고, 전체 57파일을 `analysis/v92_github_issue040_042_043_baseline/PatchSnapshot`에 봉인했다.
- **#040 low-lead alias 재선정:** 첫 후보 `9645`는 current runtime ref=0 / owner=0이었지만 바로 아래 physical3168 cell의 top-row ink=7이라 font bleed gate가 Patch write 전에 빌드를 중단했다. 따라서 실제 게임 파일에는 `9645`가 한 번도 적용되지 않았다. 재탐색 후 `9554/physical2922`, below physical2995 top-row ink=0을 선택했다. `9554`는 과거 v13-era `밭` alias 이력이 있으나 exact v91 runtime raw ref=0, 현재 effective alias owner=0, Original-valid이다. Citra에서 성공한 low-lead `농=956B`와 같은 0x95xx 계열이라는 점도 보조 근거로 사용했다. v91에서 실패한 physical3676은 sealed v90 셀로 pixel-exact 복원하고 physical2922에 Sam3 `삭` glyph를 삽입했다. packed font diff는 허용 physical3676/2922 두 셀 밖 **0 pixels**다.
- **#040 msgsec01:** 제목/설명 문구는 `삭제 / 신무장 삭제하기`를 유지하고 alias만 `9956→9554`로 바꿨다. title `0x429`, description `0x436..0x444`만 변경하며 `05 05` separator와 다음 fixed start `0x447`, 이후 전체는 v91 byte-exact다. `msgsec01.dat` 크기는 9,057B 유지다.
- **#042 로컬 이슈/스크린샷 확인:** `Github_Issue/Issue042.html`과 `Issue042_assets/image_001.png`를 실제 확인하고 Windows OCR 결과를 Original/current 바이너리와 교차검증했다. 실제 hardcoded source는 `code.bin 0x1801DC="%s様、"`, `0x1801E4="%s副軍師の%sが\n意見があるそうです"`였다. 각각 **`%s님, `**(6B), **`%s부군사 %s,\n의견이 있다고 합니다`**(33B)로 원 슬롯과 동일 길이 치환했다. 바로 뒤 `0x180208`은 v55에서 이미 `들으시겠습니까？`로 정상 번역된 20B slot이라 byte-exact 보존했다.
- **#043 외교 조립문:** `Issue043.html`과 PNG에서 상단 `어찌합니까？`, 아래 `쳐들어가받고 싶다`를 확인했다. 상단 source `0xAB42C=%s様、どうしますか？`를 **`%s님, 어떻게할까요？`**로 정확히 20B 치환했다. `받고 싶다@0x1D0968`는 Original `いただきたい`를 동맹/공동공격/철수/교환 네 prefix 뒤에 공통으로 붙이는 suffix임을 pointer table `0x1E5030`과 주변 pool 전체로 확인했다. suffix는 **` 주길 바란다`**로 동일 12B 치환했고, 연결 문법 때문에 `0x1D1058 동맹을\n맺고→동맹을\n맺어`, `0x1D102C 서 병을\n물리고→서 병을\n철수해`를 함께 수정했다. `교환을\n승낙해@0x1D103C`, `로 함께\n쳐들어가@0x1D1068`는 byte-exact 보존했고 관련 pointer entries `0x1E5030/0x1E519C/0x1E51A4/0x1E51A8/0x1E51A0`도 전부 byte-exact다.
- v92 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat` 3개**다. 나머지 54개 Patch 파일, 특히 v91 #041 `msgsec17.dat`는 v91과 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 빌더를 연속 2회 실행해 3파일 SHA가 동일했다. 독립 `tools/verify_sangokushi2_v92_runtime_readback.py`는 sealed v91 manifest, #042/#043 원문/target/preserved strings, pointer table 보존, #040 alias occurrence, font packed readback, `0x447` 이후 보존, 허용 범위 밖 diff **0**을 모두 PASS했다. v91 대비 code diff=61 bytes, msgsec01 diff=4 bytes이며 font outside-cell diff=0 pixels다.
- SHA-256: `code.bin=BD34F746F220EC86F9F1692FC0AC6F04A9294865C77CEDCE42EE1AF154ACE52E`, `font.g1t=A20F8A02D4EFE526ED7EC6C0AE086EAF8698F3FAA6DE2A1897D78665E2E74000`, `msgsec01.dat=FA9612A8FC2B60B1D5ECA5A526BE7594F6F90FDA2406FE370BBC69EC0D267A3A`.
- 빌더=`tools/build_sangokushi2_v92_github_issue040_042_043.py`, targets=`analysis/v92_github_issue040_042_043_targets.json`, report=`analysis/v92_github_issue040_042_043_report.json`, 독립 검증기=`tools/verify_sangokushi2_v92_runtime_readback.py`. **현재 Patch는 v92-intermediate이며 Citra 실화면 확인 대기**다.

## 특기 오역 + GitHub Issue #048/#049/#050 수정 — v93-intermediate (2026-09-01)

- 시작 시 `analysis/v92_github_issue040_042_043_report.json`의 57파일 manifest와 현재 Patch가 완전 일치함을 확인하고 전체를 `analysis/v93_trait_issue048_050_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- compact 특기 pointer table을 재확인해 Original `受流`의 `code.bin 0x1CFE54`를 **`수반→수류`**, Original `軍学`의 `0x1CED10`을 **`병법→군학`**으로 동일 4B 치환했다. pointer entries `0x1DC314/0x1DC348`은 byte-exact다. 이미지 편집 참고용 `특기.html`도 v93 실제 readback/SHA에 맞춰 갱신했다.
- #048은 `0x154F48`의 공유 formatter **`쌀을 얼마나 %s니까？`**와 action pointer `0x1E5250→0x1D1500`을 보존했다. 4B action `주기`를 `줍+NUL`로 바꿔 사용자가 허용한 2안 **`쌀을 얼마나 줍니까？`**로 조립한다.
- #049는 글꼴 문제가 아니었다. `msgsec07_0142_003`의 Original `報告=8D90`이 header[142]/[143] 경계에서 `8D|90`으로 분할됐고, v72 EOF parent가 dangling `8D`로 끝나 후행 일본어 글자가 노출됐다. v93은 header[142]만 새 word-offset `0x243D`(byte `0x487A`)로 전환하고 마지막 행을 **`*개월 후에 보고할 수 있을 / 것 같습니다`**까지 완결한 뒤 separator로 끝냈다. 기존 18,553B body와 비대상 header는 byte-exact다.
- #050은 사용자 확인대로 이전 빌드에서 수정된 `조예` 이름을 포함한 군주명 payload를 건드리지 않았다. Original `msgsec09_0000_001=軍 / ﾊ敗ﾚﾏｼﾀ`만 **`군은 / 패배했습니다`**로 번역했다. parent0 마지막 `%lu` 문구도 header[1]까지 이어지는 원문을 완결해 독립 재배치했고 header[0]만 `88→1216` word로 바꿨다. 기존 msgsec09 2,432B body와 header[1..87]은 byte-exact다.
- v93 변경 Patch 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec09.dat` 3개**다. 나머지 54파일은 v92 byte-exact다. 완성본 위에서 빌더를 다시 실행해 report/파일 SHA가 동일했고 독립 verifier도 PASS했다.
- SHA-256: `code.bin=ACD609479091984A471A14B27DE71EB4039617DE48AD01C8AC27D89387521E0D`, `msgsec07.dat=A8FA99305CE08DD8EC20EE4B4AF724783CE1033337DD9411DD3AD289C6B9F327`, `msgsec09.dat=76266B3B55152B881B35CA402C9F33DF99532841A3247FA41AC2D35B27EA53A5`, report=`8DA1094E22001AD9A96D8966FDDB277F7D61EF93577926FBEC8D9A510EFD44EC`.
- 빌더=`tools/build_sangokushi2_v93_trait_issue048_050.py`, targets=`analysis/v93_trait_issue048_050_targets.json`, report=`analysis/v93_trait_issue048_050_report.json`, 독립 검증기=`tools/verify_sangokushi2_v93_runtime_readback.py`. v93의 #049 header[142] 수정은 사용자 Citra에서 반영되지 않아 아래 v94에서 실패 source 판정으로 대체된다.

## GitHub Issue #049 재수정 + #051/#052 — v94-intermediate (2026-09-01)

- 사용자 Citra 확인에서 **v93의 #049가 전혀 수정되지 않았음**을 최우선 런타임 증거로 삼았다. 로컬 `Github_Issue/Issue049.html`, `Issue051.html`, `Issue052.html`과 각 `IssueNNN_assets/image_001.png`를 실제 확인했다. 시작 시 exact v93 Patch 57파일 manifest를 `analysis/v94_issue049_051_052_baseline/PatchSnapshot`에 봉인했으며 Original/Rebuild/Backup은 수정하지 않았다.
- **#049 실제 runtime source 재확정:** Citra 화면의 `3개월 후에 보[한자]할 수 있을 / 것 같습니다`가 현재 v93 `msgsec07.dat` 기존 body **`0x2D83`**의 바이트열과 정확히 일치했다. 이 위치는 `...월 후에 보 + 0x8D + 고할 수 있을...`로 저장되어, 고립된 Shift-JIS lead `0x8D`가 다음 한글 `고=8954`의 첫 바이트와 잘못 결합하는 상태였다. 따라서 v93의 `header[142]→EOF 0x487A` 재지정은 실제 화면 source가 아니었던 것으로 폐기한다.
- #049는 **header를 하나도 바꾸지 않고**, 실제 Citra가 읽은 `0x2D83`의 46B 물리 span을 제자리 수정했다. 고립 `0x8D`를 제거해 `*개월 후에 보고할 수 있을 / 것 같습니다`로 완결하고, 1B 짧아진 만큼 기존 `05 05 05` separator 직전에 ASCII space 1B를 넣어 뒤 물리 주소를 전혀 이동시키지 않았다. `msgsec07.dat` 파일 크기 **18,705B**와 388-word header 전체, v93의 나머지 body/EOF append는 byte-exact이며 실제 diff는 이 span 내부 **25 bytes**뿐이다.
- **#051 월 보고서 우측 결과:** PNG의 `兵士士気…상승`과 직접 일치하는 hardcoded formatter `code.bin 0x152328="兵士士気%s"`를 **`병사사기%s`**로 동일 10B 치환했다. 같은 보고서 계열 공용 label `0x1CFA1C="兵士士気"`도 **`병사사기`**로 동일 8B 치환했고 pointer table entry `0x1AB378`은 byte-exact 보존했다. 따라서 formatter/label 어느 duplicate 경로를 사용해도 한글이 되도록 같은 의미 범위만 최소 수정했다.
- **#052 외교 동맹 조건:** PNG의 `同盟:금 2401`에서 실제 공용 C-string `code.bin 0x1CEE18="同盟"`을 **`동맹`**으로 동일 4B 치환했다. 이 문자열을 가리키는 pointer entries **`0x1AB394 / 0x1DBAC0 / 0x1E50C8`**는 모두 `0x2CEE18`을 유지하며 byte-exact다.
- v94 변경 Patch 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개**다. code의 허용 span 밖 diff=0, msgsec07의 `0x2D83` span 밖 diff=0, 나머지 55개 Patch 파일은 v93과 byte-exact다. code 실제 변경 바이트는 20B, msgsec07은 25B다.
- 완성 v94 위에서 빌더를 다시 실행해 `code.bin`, `msgsec07.dat`, report SHA-256이 첫 실행과 완전히 동일했다. 독립 `tools/verify_sangokushi2_v94_runtime_readback.py`도 sealed v93 manifest, #049 Citra-matching stale body readback/전체 header 보존, #051 formatter+label/pointer, #052 3개 pointer, 비대상 55파일 보존을 모두 PASS했다.
- SHA-256: `code.bin=7E04CE4E9B7E7C47C1E5BCD06C26070DEF416C5FEDDD357EA7719D16E2B9FB39`, `msgsec07.dat=E831421B13AB1AE16AA6588DE07C2CE463720AD93A636AEBCCE232F26BB8E630`, report=`491591CA8ECA2A12BC4098C1765636889F53E7AF556557F895615200C80C685F`.
- 빌더=`tools/build_sangokushi2_v94_issue049_051_052.py`, targets=`analysis/v94_issue049_051_052_targets.json`, report=`analysis/v94_issue049_051_052_report.json`, 독립 검증기=`tools/verify_sangokushi2_v94_runtime_readback.py`. v94는 아래 v95의 sealed baseline으로 계승된다.

## GitHub Issue #056/#057/#058/#059 수정 — v95-intermediate (2026-09-01)

- 작업 시작 전에 `tools/verify_sangokushi2_v94_runtime_readback.py`를 재실행해 현재 Patch가 v94 report와 정확히 일치함을 확인하고, exact v94 전체 Patch 57파일을 `analysis/v95_issue056_059_baseline/PatchSnapshot`에 봉인했다. 로컬 `Issue056~059.html`과 첨부 PNG를 직접 확인했으며 Original/Rebuild/Backup은 수정하지 않았다.
- **#056 계략 실패:** Citra 화면의 `失敗です, [무장명]에게 / 간파당했습니다`는 `msgsec07 header[262]`이 소유한 group의 네 번째 fragment와 정확히 일치했다. 앞 3개 fragment는 byte-exact로 복사하고 마지막만 **`실패했습니다. [동적 무장명]에게 / 간파당했습니다`**로 재구성했다. 동적 무장명 토큰은 불완전한 `02 01 + 네`가 아니라 원래 3B **`02 01 C8`** 전체를 보존했다. 새 group을 EOF에 append하고 header[262]만 redirect했다.
- **#057 타국 동맹:** 기존 `header[372]` target은 `02 01 + 네와 / 02 02 + 네가`처럼 동적 군주명 토큰의 마지막 `C8`이 빠진 상태여서 스크린샷의 깨진 글자가 발생했다. 사용자 지정 문구대로 **`[군주명]군과 [군주명]군이 동맹을 / 맺은 모양입니다`**를 사용하고, 두 이름 토큰을 각각 **`02 01 C8 / 02 02 C8`**로 완전 복구한 standalone을 EOF에 append한 뒤 header[372]만 redirect했다.
- **#058 방랑/거병:** 사용자 후속 요청에 따라 군주명 뒤 이중 조사 처리를 피하기 위해 양쪽 모두 **`군은`** 구조를 사용한다. `header[374]` 방랑은 **`[군주명]군은 방랑길에 나섰습니다`**, `header[369]` 거병은 **`[군주명]군은 %s에서 / 거병했습니다`**로 수정했다. 두 기존 target 모두 `02 01` 뒤에 `네는/네가`를 붙여 `C8`이 빠져 있었으므로 새 target은 **`02 01 C8`**을 먼저 넣고 suffix를 붙인다.
- **#059 밀담 보고:** Citra 화면과 Original을 대조해 `msgsec05 header[15] @0x197`의 `02 01 C8 様、%sﾉ 02 02 C8 ｶﾞ / 何者ｶﾄ密談...`가 실제 source임을 확정했다. 새 문구는 **`[군주명]님, %s의 [무장명]이(가) / 누군가와 밀담 중인 듯합니다`**이며 동적 토큰 `02 01 C8 / 02 02 C8`을 모두 보존했다. 기존 body는 이동시키지 않고 EOF append + header[15] redirect만 수행했다.
- #059 요청에 따라 exact 미번역 duplicate도 Patch 전체를 감사했다. CP932 `何者`와 `密談`은 v94 Patch에서 각각 **정확히 1회**, 모두 `RomFS/Message/msgsec05.dat 0x1A7 / 0x1AD`의 같은 물리 fragment에만 존재한다. v95는 안정성을 위해 그 stale physical copy 자체는 byte-exact로 남기되 **live header[15]은 새 한국어 target을 가리켜 런타임에서는 해당 일본어를 읽지 않는다.** 동일 exact phrase의 다른 Patch duplicate는 없다.
- v95 변경 Patch 파일은 정확히 **`RomFS/Message/msgsec05.dat`, `RomFS/Message/msgsec07.dat` 2개**다. msgsec07은 기존 18,705B body를 byte-exact 보존하고 header **`262/369/372/374`**만 변경했으며, msgsec05는 기존 653B body를 byte-exact 보존하고 header **`15`**만 변경했다. 나머지 55개 Patch 파일은 v94와 byte-exact다.
- 완성본 위에서 빌더를 2회 실행해 두 Message 파일과 report SHA가 완전히 동일했고, 독립 `tools/verify_sangokushi2_v95_runtime_readback.py`도 changed-file set, header diff, 기존 body 보존, 5개 live target, 완전한 동적 토큰, #059 exact duplicate audit를 모두 PASS했다.
- SHA-256: `msgsec05.dat=25C29E9E32B25E9960A042601D62B03FC3F609602C7B8DEA7E9CD7531A213854`, `msgsec07.dat=BF864B1333775F87DF764612BD604E172900C4AAE2105D261347A0E28FA6C5E0`, report=`F4805168D5D6C56012B8EA82699E7BD4A4AE52334F395D0340A56C6584BF1E54`.
- 빌더=`tools/build_sangokushi2_v95_issue056_059.py`, targets=`analysis/v95_issue056_059_targets.json`, report=`analysis/v95_issue056_059_report.json`, 독립 검증기=`tools/verify_sangokushi2_v95_runtime_readback.py`. 사용자 Citra 후속 확인에서 **거병은 정상 반영됐지만 #056과 방랑은 전혀 반영되지 않아 header[262]/header[374] source 판정은 실패**했다. #057은 아직 실화면 확인 불가, #059는 이번 후속에서 별도 확인하지 않았다. 실패한 두 source는 아래 v96 권위로 대체한다.

## #056 / #058 방랑 Citra 실제 source 재수정 — v96-intermediate (2026-09-01)

- 사용자 Citra 스크린샷에서 #056은 여전히 `失敗です, [무장명]에게 / 간파당했습니다`, 방랑은 여전히 깨진 `[군주명]는 방 랑의 길을 떠났습니다` 계열로 출력됐고, 거병만 **`[군주명]군은 낙양에서 / 거병했습니다`**로 정상 반영됐다. 따라서 v95의 append + header redirect가 #056/방랑에는 실제 runtime source가 아니었음을 확정하고, 거병 수정은 성공 상태로 동결했다.
- exact v95 Patch 57파일을 `analysis/v96_issue056_wandering_runtime_fix_baseline/PatchSnapshot`에 봉인했다. v96은 **`RomFS/Message/msgsec07.dat` 1개만** 수정하며 Original/Rebuild/Backup은 건드리지 않았다.
- **#056 실제 runtime source:** 사용자 Citra 혼합 출력이 현재 v95 `msgsec07.dat` 물리 body **`0x2597`**의 35B와 byte-exact로 일치했다. 해당 span은 `失敗ﾃﾞｽ, + 02 01 + 네에게 / 간파당했습니다`로 저장되어 있었다. v96은 header를 전혀 바꾸지 않고 이 35B 자체를 **`실패했습니다. / [동적 무장명]에게 들켰습니다`**로 정확히 35B 제자리 치환했다. 동적 이름은 완전한 **`02 01 C8`** 토큰을 사용한다.
- **#058 방랑 실제 runtime source:** 사용자 Citra 출력이 v95 `msgsec07.dat` 물리 body **`0x33E8`**의 33B `02 01 + 네는 방 랑의 길을 떠났습니다`와 일치했다. v96은 이 span을 **`02 01 C8 + 군은 방랑길에 나섰습니다.`**로 바꾸고 2B 짧아진 만큼 separator 직전 ASCII space 2B로 보충했다. 결과적으로 파일 크기와 모든 후속 물리 주소를 유지한다.
- `msgsec07`의 **388-word header 전체는 v95와 byte-exact**다. 즉 v95가 만든 header[262]/[374] EOF target은 남아 있으나 Citra가 사용하지 않는 stale 경로로 취급하며, 성공한 거병 header[369], 미확인 #057 header[372]도 그대로 보존했다. #059가 들어 있는 `msgsec05.dat`와 나머지 55개 파일도 v95와 byte-exact다.
- 완성본 위에서 v96 빌더를 다시 실행해 동일 SHA를 확인했고, 독립 `tools/verify_sangokushi2_v96_runtime_readback.py`는 sealed v95 preimage, `0x2597/0x33E8` 실제 물리 source, 전체 header 보존, v95 거병/#057/#059 보존, 비대상 파일 보존을 모두 PASS했다.
- SHA-256: `msgsec07.dat=356250780042973FCC23168FF7BBC24E42419E28C73FF9503C8ADC3EC3033472`.
- 빌더=`tools/build_sangokushi2_v96_issue056_wandering_runtime_fix.py`, targets=`analysis/v96_issue056_wandering_runtime_fix_targets.json`, report=`analysis/v96_issue056_wandering_runtime_fix_report.json`, 독립 검증기=`tools/verify_sangokushi2_v96_runtime_readback.py`. v96은 이후 v97/v98의 sealed lineage로 계승됐다.

## #056 direct group 보정 + 방랑 fragment 경계 수정 — v97-intermediate (2026-09-01)

- v96 후속 Citra에서 #056은 여전히 일본어 `成功です、/失敗です、` prefix가 남았지만 **방랑은 `[군주명]군은 방랑길에 나섰습니다.`로 정상 출력**됐다. 따라서 방랑은 사용자 Citra 승인 상태로 동결하고 이후 revision에서 해당 물리 span과 관련 header를 byte-exact 보존한다.
- v97은 v96의 #056 direct-message 내부 fragment 분류를 재검토해 header[263..265]의 잘못된 `inside-fragment +3` 판정을 폐기하고 각각 의미상 완전한 standalone target으로 전환했다. 동시에 방랑의 실제 row boundary에서 `02 01 C8` full dynamic token을 복구했다. 완성본 위 결정적 재빌드와 독립 readback을 통과했다.
- 그러나 v97 Citra에서 `msgsec07.dat` 안의 `成功ﾃﾞｽ/失敗ﾃﾞｽ`를 제거했음에도 화면의 `成功です、/失敗です、`가 남아, #056 prefix가 **Message가 아니라 별도 code.bin formatter 경로**임이 확정됐다. 이 실패 source 판정은 v98에서 ARM/table까지 역추적해 교체한다.
- report=`analysis/v97_issue056_wandering_fragment_boundary_report.json`, 빌더=`tools/build_sangokushi2_v97_issue056_wandering_fragment_boundary.py`, 독립 검증기=`tools/verify_sangokushi2_v97_runtime_readback.py`.

## #054 / #055 / #056 실제 runtime source 수정 — v98-intermediate (2026-09-01)

- 작업 시작 시 `tools/verify_sangokushi2_v97_runtime_readback.py`를 재실행해 현재 Patch가 exact v97임을 확인하고 전체 Patch를 `analysis/v98_issue054_055_056_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- **#054 상단 동적 무장명:** Original `msgsec07 header[316]`은 `02 01 C8 + [무장명] + どの部隊にしますか？`를 가리키지만 v97 target은 `02 01` 뒤에 한국어 alias가 들어가 **동적 이름 토큰 마지막 `C8`이 유실**돼 있었다. 기존 body는 이동시키지 않고 EOF에 full token **`02 01 C8 + 의 소속 부대를 고르세요 + 05 05 05`**를 append하고 header[316]만 `0x2BE3→0x4A2D`로 전환했다. 예상 화면은 **`[무장명]의 소속 부대를 고르세요`**다.
- **#054 전투 편성 UI 4항목:** 현재 v97 `code.bin`에는 일본어 `兵糧`이 0회인데 Citra #054 화면에 `兵糧`이 남아 있었고 `msgsec03.dat`에는 정확히 1회 `兵糧@0x9C`가 존재했다. 같은 compact UI block의 `騎馬@0x118 / 弩@0x11F / 強弩@0x124`까지 실제 source로 묶어 각각 **`군량 / 기마 / 노 / 강노`**로 동일 길이 제자리 치환했다. `msgsec03.dat` 크기는 320B 그대로이며 해당 네 span 밖 diff=0이다. `弩=노`, `強弩=강노`는 기존 프로젝트 권위 용어를 그대로 사용했다.
- **#055 총대장 선택:** Original runtime `msgsec07 header[319]`은 `誰を総大将にしますか？` 전체를 가리키지만 과거 internal-pointer detangle이 앞 `誰を`를 기계적으로 생략해 v97 h319이 고립된 **`총대장`**만 가리키고 있었다. EOF에 **`총대장은 누구로 하시겠습니까？`**를 append하고 h319만 `0x3B18→0x4A4A`로 전환했다. 기존 `총대장` old target은 과거 승인 local alias 바이트를 사용하므로 최신 map으로 재인코딩해 preimage를 판정하지 않고 exact v97 6B를 seal한다.
- **#056 실제 hardcoded formatter:** ARM `code.bin 0x16B014`가 결과 index에 따라 VA **`0x2E52D0`** pointer table을 읽고, table index0=`0x2D0064→file 0x1D0064 成功`, index1=`0x2D1538→file 0x1D1538 失敗`를 선택한다. 바로 이어 `0x16B024`의 ADR이 **`file 0x16B168 = です、`**를 직접 참조한다. 따라서 Citra의 `成功です、/失敗です、`는 정확히 **라벨 + 공통 suffix 조립**이며 Message/세이브/VRAM 가설은 필요 없다. 동일 길이로 `成功→성공` 4B, `失敗→실패` 4B, `です、→했다. ` 6B만 제자리 치환했다. 결과 pointer table과 formatter ARM block은 byte-exact 유지했다.
- #056 인접 `0x16B174=ました`, 기존 정상 `0x16A760=성공했습니다.`, **`0x16A790=간파당했습니다`**는 byte-exact 보존한다. 예상 최종 prefix는 `성공했다. ... / 실패했다. ...`이며 실패 화면의 `간파당했습니다`는 유지된다.
- **방랑은 v97 사용자 Citra 승인 상태를 동결:** `msgsec07 0x1EDA` 30B span의 SHA/readback을 v98 verifier에서 직접 비교하고 h262/h263/h264/h265/h369/h372/h374도 v97과 동일함을 강제한다. 새 msgsec07 header 변경은 정확히 **[316,319]**뿐이며 기존 body는 이 두 16-bit header word 외 byte-exact다.
- v98 변경 Patch 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec03.dat`, `RomFS/Message/msgsec07.dat` 3개**다. `font.g1t`와 나머지 Patch 파일은 v97 byte-exact다. `sangokushi2_alias_guard.py`로 v98 새 문구의 모든 한글 alias 존재/비공유를 확인해 PASS했으며 font 수정은 없다.
- 빌더와 verifier를 완성본 위에서 다시 실행해 두 번째 실행도 동일 SHA-256으로 PASS했다. SHA-256: `code.bin=E58FA4D1B6416C53E5AB2D6968E444977CF5852849CCDC71EAC52EF077758123`, `msgsec03.dat=E50E56A176D7AA0400D7014B381A2CA26FC758ADF68ADA879CE77BF16B3C33C6`, `msgsec07.dat=CB07F9AD187EFF1B3B2C41BDF157BD7FC17605180F12D6D7F035253177891C33`.
- 빌더=`tools/build_sangokushi2_v98_issue054_055_056.py`, targets=`analysis/v98_issue054_055_056_targets.json`, report=`analysis/v98_issue054_055_056_report.json`, 독립 검증기=`tools/verify_sangokushi2_v98_runtime_readback.py`. v98은 아래 v99의 sealed baseline으로 계승됐다.

## #054 / #056 / #060 / #062 Citra 후속 보정 — v99-intermediate (2026-09-01)

- 작업 시작 시 `tools/verify_sangokushi2_v98_runtime_readback.py`를 재실행해 현재 Patch가 exact v98임을 확인하고, 전체 Patch를 `analysis/v99_issue054_056_060_062_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- **#054:** 사용자 후속 Citra에서 상단 **`[무장명]의 소속 부대를 고르세요`는 정상 수정됨**을 확인했다. 따라서 `msgsec07 header[316]`과 해당 appended body는 동결하고 더 이상 수정하지 않는다. 하단 `msgsec03.dat`에서 v98의 `군량/기마/노/강노`는 그대로 보존하면서 남아 있던 고정 한자 라벨만 동일 길이 제자리 치환했다: **`兵士→병사`, `軍資金→군자금`, `病気→질병`, `負傷→부상`, `[동적 이름]軍→[동적 이름]군`, `歩兵→보병`, `闘艦→투함`, `蒙衝→몽충`, `走舸→주가`**. `02 01 C8` 동적 이름 토큰, msgsec03 header/파일 크기는 보존했다.
- **#056:** v98이 `成功/失敗 + です、`만 `성공/실패 + 했다. `로 바꿔도 끝에 일본어가 남은 원인을 ARM 조립 경로에서 재확인했다. 실제 런타임은 `결과 prefix + 했다. + 동적 이름 + (성공 stem 또는 실패 stem) + 공통 ました`를 순서대로 strcat한다. v98의 성공 stem **`은\n꾀에 걸려`**를 **`은\n꾀에 걸렸`**, 실패 stem **`에게\n간파당해`**를 **`에게\n간파당했`**, 공통 `ました` 6B를 동일 길이 **`습니다`** 6B로 바꿨다. 최종 목표는 **`성공했다. [대상무장]은 / 꾀에 걸렸습니다`**, **`실패했다. [대상무장]에게 / 간파당했습니다`**다. executable formatter block 자체는 byte-exact다.
- **#060:** 월 보고서 `병사사기? 상승`의 실제 source는 `msgsec06.dat header[23] = 0x42C`이다. v98 body에는 `%s` 뒤에 **고립된 `0x8F` 1바이트**가 남아 `%s + 8F + 공백 + 상승`으로 저장돼 있었고, 이는 완성 문자/제어코드가 아니라 Shift-JIS fragment 경계에서 떨어진 lead byte라 Citra의 `?`와 일치한다. `0x42C..0x436` 11B span을 **`%s 상승 + trailing ASCII space + 05 05 05`**로 제자리 복구해 모든 161-word header와 파일 크기를 유지했다. 화면 목표는 **`병사 사기 상승`**이다.
- **#062:** v98 `msgsec07 header[319]=0x4A4A`이 가리키는 **`총대장은 누구로 하시겠습니까？`**에서 첫 `총=989B`가 이 renderer에서 `?`로 표시됐다. body를 다시 쓰지 않고 header[319]만 유효 2바이트 문자 경계 **`0x4A4A→0x4A4C`**로 이동해 기존 문자열의 `대장`부터 읽도록 했다. 최신 화면 목표는 **`대장은 누구로 하시겠습니까？`**다. v98 body, #054 header[316], 사용자 승인 방랑 30B는 byte-exact 동결했다.
- v99 변경 Patch 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec03.dat`, `RomFS/Message/msgsec06.dat`, `RomFS/Message/msgsec07.dat` 4개**다. `font.g1t` 및 비대상 Patch 파일은 v98 byte-exact다.
- 완성본 위에서 빌더를 두 번 실행해 두 실행의 SHA-256이 동일했고, 독립 `tools/verify_sangokushi2_v99_runtime_readback.py`도 PASS했다. SHA-256: `code.bin=8C39FAD6A9FC89FC508AB03C5B3EF227191138EFD69018B8F5A59297B006E321`, `msgsec03.dat=B723FB056423379A6E6690A138A16F5C3B5EE6C0B419351D6D47D03B24D3E0BC`, `msgsec06.dat=5C5319BE2CBCA7D5268F64A11D044E6C33F96377032B7E63C84634F6628B0AD6`, `msgsec07.dat=D5F137414EBE21B53CFE40C74471A1C46B45928DFBEDB8A4219C4F94EDD1B6E1`.
- 빌더=`tools/build_sangokushi2_v99_issue054_056_060_062.py`, targets=`analysis/v99_issue054_056_060_062_targets.json`, report=`analysis/v99_issue054_056_060_062_report.json`, 독립 검증기=`tools/verify_sangokushi2_v99_runtime_readback.py`. **현재 Patch는 v99-intermediate이며 #054/#056/#060/#062 Citra 실화면 확인 대기**다.

## 2026-09-01 — v100-intermediate 이미지 9종 1:1 리빌드

- MD와 v99 report를 대조해 실제 직전 최종본이 v99-intermediate임을 확인하고, 현재 Patch 57개 전 파일이 v99 manifest와 일치하는 상태를 analysis/v100_image_update_baseline/PatchSnapshot에 봉인했다.
- 지정된 PNG 9개 외에는 입력 이미지로 사용하지 않았다. start_menu_up_015.png는 봉인 v99 start_menu_up.g1t의 texture[15]에, Common/Info 8개 PNG는 직전 Patch에 컨테이너가 없으므로 immutable Original 각 G1T의 texture[0]에 반영했다.
- 출력 codec은 0x09 RGBA8 512x128 1개와 0x45 RGB8 512x256 8개다. RGB8 입력 8개는 alpha 255 gate를 통과했다. 각 원본의 decode→encode identity, 변경 후 header/비대상 payload byte-exact, PNG 대비 pixel-exact readback이 모두 9/9 PASS했다.
- v99 대비 변경 파일은 정확히 9개 G1T이며, 기존 v99 57개 파일은 모두 byte-exact다. 요청하지 않은 BG_InfoB_C.g1t, BG_InfoK.g1t는 Patch에 추가되지 않았다. Original, Rebuild, Backup은 쓰지 않았고 이미지 생성도 사용하지 않았다.
- 완성본 위에서 빌더를 두 번 실행해 동일한 manifest와 report SHA-256 60DF827A42D877092DF02C1AC6EE4A000D011EE9E9BEDB107E75E75FEB279ECD를 얻었고, 독립 tools/verify_sangokushi2_v100_image_readback.py도 PASS했다.
- 빌더=tools/build_sangokushi2_v100_image_update.py, report=analysis/v100_image_update_report.json, 독립 검증기=tools/verify_sangokushi2_v100_image_readback.py. **v100-intermediate는 v101의 sealed base로 보존된다.**

## 2026-09-01 — v101-intermediate #054 / #061 / #063 runtime 정리

- exact v100 전체 Patch 65파일이 `analysis/v101_issue054_061_063_baseline/PatchSnapshot`에 봉인됐고, 실제 변경 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat` 6개다. font/msgsec03/v100 이미지 9종 및 비대상 파일은 v100 byte-exact다.
- #054 하단 전투 편성 UI의 실제 runtime source를 재추적했다. v99 `msgsec03.dat`에 이미 `군량/기마/노/강노`가 있었는데도 Citra에서 `兵数/騎馬/歩兵`가 남았던 이유는 이 화면이 `code.bin` 공통 UI 문자열 테이블을 읽기 때문이다. v101은 `0x1CFD78 兵数→병력`, `0x1CFE80 歩 兵→보 병`, `0x1CFBE4 騎 馬→기 마`, `0x1CFBF4 騎馬→기마` 4개 slot만 exact-size 수정했다. #054 상단 `msgsec07 header[316]`과 기존 성공 문구는 동결했다.
- #061 전체대사 감사에서 과거 Citra 실패 가능성이 확인된 far/old alias를 다시 찾았다. code 대사 282개에는 `북/헌/촉/찌/룡` old alias가 16회, 활성 direct Message에는 물리 10개(msg08)+1개(msg06)가 남아 있었다. 현재 v100 font에서 safe cell을 pixel-exact 재검증한 뒤 `북 98DD→89D0`, `헌 98A0→8972`, `촉 989A→8D6F`, `찌 9968→9179`, `룡 98B5→92DE`로 byte-local 치환했다. 빌드 후 동일 감사에서 old alias 잔존 0건을 확인했다.
- #061의 핵심은 단순 alias뿐 아니라 Shift-JIS 2바이트 문자가 fragment 경계에서 잘린 채 한국어 사이에 실제 한자/가나가 남는 문제였다. 원본 header 의미와 현재 runtime pointer를 대조해 명확한 33개만 선별했다: msg04 2개, msg06 6개, msg07 15개, msg08 10개. 기존 body를 repack하지 않고 각각 EOF에 완결 한국어 문자열 + `05` guard를 append하고 해당 absolute-byte header만 redirect했다. 동적 이름은 `02 01 C8` raw 3B token을 그대로 삽입했다.
- 대표 복구 문구: `대기 무장을 어디로 / 퇴각시키겠습니까？`, `[군주명]군의 성을 함락시켰습니다`, `의술에 국경은 없습니다`, `건조 중`, `잠시 기다려 주십시오`, `죄송합니다, / 산적에게 빼앗겼습니다`, `지금 상태로는 / 승산이 없습니다`, `누구를 등용하시겠습니까？`, `[무장명]의 충성도가 떨어졌습니다`, `죄송합니다 / 완전히 실패했습니다`, `황제가 될 수도 / 있었을 것인데`, `형님이 가는데 나 혼자 / 남아 있을 수는 없지`, `부디 선생의 가르침을 / 내려 주시옵소서` 등이다.
- `msgsec06 header[121]=0x1157`은 `$` literal과 중복 cross-fragment 문장이 함께 있어 의미/제어 역할이 확정되지 않았다. 이번 revision에서는 의도적으로 보류했다. 또한 `농=956B`, `뢰=94D6`처럼 정적 CP932 해석 시 한자로 보여도 실제 Citra-safe glyph인 local alias가 있으므로 raw 검색만으로 일괄 치환하지 않는다.
- #063 `msgsec09`는 v93 이후 header[0]이 EOF parent0 word pointer 역할을 하고 있었다. v100 active parent0(`header[0]=0x4C0`, byte 0x980)은 승리 row만 `a<09>軍 / ﾉ勝利ﾃﾞｽ` 일본어이고 패배 row는 이미 `군은 / 패배했습니다`였다. 기존 rows 2~7을 byte-exact 보존한 새 parent0을 EOF에 append하고 header[0]만 `0x526`(byte 0xA4C)으로 redirect했다. 최신 목표는 `[군주명]군의 승리입니다.` / `[군주명]군은 패배했습니다.`다.
- 완성본 위 빌더를 두 번 재실행해 6개 SHA가 완전히 동일했고 독립 `tools/verify_sangokushi2_v101_runtime_readback.py`도 두 번 PASS했다. SHA-256: code=`91DEA206DD82ED401F6849BA5EE96438AD90C7E68DDA3EF9A8A90D22E4D8211A`, msg04=`1F6D036D1269D3E84F0F78E46B2B0D73E80E93EBA7A768CF1CEB60A78C5F0493`, msg06=`BDD2E93619CF3A819BD05379907AD3C85CC08B69B1F35E40266AD0FC14457BC1`, msg07=`FC4710D0DAA564C45AA03967D5462C4BF2FAAB4A3982B5D2ECE0C3597F0A3B6D`, msg08=`455765664178C2DEB7DC57A1BD800807945788A3C22D912DE6DDB9AA4C5885A8`, msg09=`02A8EDA51509A041D9665CAE62672DBFD326599ECEF2DAB5433ABF6E8CD7DE9D`.
- 빌더=`tools/build_sangokushi2_v101_issue054_061_063.py`, targets=`analysis/v101_issue054_061_063_targets.json`, report=`analysis/v101_issue054_061_063_report.json`, verifier=`tools/verify_sangokushi2_v101_runtime_readback.py`.
- **2026-09-01 사용자 Citra 확인 완료:** v101 적용 후 사용자가 `잘 수정되었다`고 확인했다. 따라서 v101의 #054 하단 전투 편성 UI, #061 safe-alias/fragment-boundary 복구, #063 타국 전쟁 결과 문구는 런타임 승인 상태로 동결한다.

## 2026-09-01 — v102-intermediate #053 / #064 시나리오 설명·설정 메뉴

- Citra 승인된 exact v101 전체 Patch 65파일을 `analysis/v102_issue053_064_baseline/PatchSnapshot`에 봉인했다. 실제 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec10.dat`, `RomFS/Message/msgsec11.dat` 4개다. 그 밖의 Patch 61파일, v100 이미지 9종, v101 #054/#061/#063 변경은 byte-exact로 유지한다.
- **#053 실제 source:** 시나리오 선택 화면의 상단 설명은 `RomFS/Message/msgsec11.dat`이다. 이미 한글로 정상인 184년 첫 설명 physical block `0x79~0xD1`은 byte-exact 동결하고, 나머지 12개 설명만 번역했다. 189/194/201/208/221/235/196/213/223년 9개와 숨겨진 시나리오 3개를 모두 포함한다.
- #053은 `msgsec11` header, `05 05 05` separator, 파일 크기, 기존 fragment 시작 위치를 전부 유지하는 fixed-position/no-repack 방식이다. 12개 그룹 모두 원본 byte capacity 이내, 한 줄 최대 20칸 이내이며, 2바이트 한글·제어 단위가 원래 fragment 경계에서 갈라지지 않는 것을 검증했다. 기존 번역 DB의 의미를 유지하되 원본 반각 일본어 슬롯에 맞도록 3줄 요약형 문장으로 압축했다.
- **#064 실제 source:** 설정 메뉴의 `세이브 / 로드 / 환경설정 / 편집기능 / 튜토리얼 / 게임종료` 버튼과 상단 설명은 baked image가 아니라 text source다. 따라서 `Extracted_Image`/G1T 이미지는 전혀 수정하지 않았다. `세이브/로드/환경설정/편집기능/게임종료`는 `code.bin` 공통 C-string 슬롯을 직접 수정했고, 상단 도움말 6개는 `msgsec10.dat` fixed-position 슬롯에 `데이터를 세이브합니다. / 데이터를 로드합니다. / 환경을 설정합니다. / 도시·세력·특산품을 편집합니다. / 튜토리얼을 볼 수 있습니다. / 게임을 종료합니다.`로 반영했다.
- 원본 `ﾁｭｰﾄﾘｱﾙ` 슬롯 `0x1CF784`는 visible 7B라 한국어 `튜토리얼` 8B(+NUL)를 담을 수 없다. 원본 문자열을 억지로 확장하지 않고 `code.bin 0x1D1886`의 26B 연속 NUL slack에 `튜토리얼\0`을 넣은 뒤, 원래 tutorial 문자열을 가리키던 **5개 포인터** `0x1DC12C/0x1DC148/0x1DC160/0x1DC17C/0x1DC278`을 모두 새 VA로 redirect했다.
- #064에 새로 필요한 글자는 `브`, `튜` 두 자뿐이다. 현재 v101 font/runtime/history를 다시 감사해 `브=96AC/physical3197`, `튜=96B0/physical3201`을 fresh low-risk alias로 할당했다. 두 alias 모두 기존 한국어 owner 0, Message/Scenario 참조 0, code text 참조 0이며, 바로 아래 physical 3270/3274는 JIS로 주소화되지 않는 gap이다. 두 donor glyph를 정상 baseline으로 넣고 아래 gap의 top-row만 bleed guard로 비운 뒤 packed `font.g1t` pixel-exact readback을 통과했다.
- 완성본 위에서 v102 빌더를 두 번 실행해 동일 SHA가 재현됐고 독립 `tools/verify_sangokushi2_v102_runtime_readback.py`도 두 번 PASS했다. 최종 SHA-256: `code.bin=ACD5DB756794D9FEFAEB7CA5C930580CE632605306059D0511AF51850260954E`, `font.g1t=1C93C72B555F6B0B32EB3A86EC0675534EF688A3D5B6911C567EE2FC84840CD6`, `msgsec10=D3C3C9436C09519AE247DD4E642C237B56D400DC119C535B8929468DD6D0EE9C`, `msgsec11=8BAA68AC6F2DF554D08A12456AE20012E936629475F3FD50DE8A3948AD4D5550`.
- 빌더=`tools/build_sangokushi2_v102_issue053_064.py`, targets=`analysis/v102_issue053_064_targets.json`, report=`analysis/v102_issue053_064_report.json`, verifier=`tools/verify_sangokushi2_v102_runtime_readback.py`.
- **현재 Patch는 v102-intermediate다. 정적/구조/결정적 빌드 검증은 완료됐지만 #053/#064의 Citra 실화면 승인은 아직 받지 않았다. 다음 채팅에서는 v102를 현재 기준으로 유지한 채 사용자의 #053/#064 테스트 결과를 먼저 기다린다.**

## v103-intermediate #065 / #067 / #070 대사 전수 감사·수정 (2026-09-01)

- 사용자가 #065/#067/#070에서 미번역 일본어와 한자 혼입을 추가 제보해 v102의 `대사 완료` 판정을 즉시 해제하고 재감사했다. exact v102 전체 Patch를 `analysis/v103_issue065_067_070_baseline/PatchSnapshot`에 봉인한 뒤 작업했으며 Original/Rebuild/Backup은 수정하지 않았다.
- **#065:** 설정 메뉴의 `게임종료` 버튼 자체는 v102에서 한글화됐지만 실제 종료 확인 popup duplicate가 남아 있었다. `code.bin`의 종료/세이브/로드/데이터 오류 popup 계열을 다시 추적해 대사·확인문을 정리했다.
- **#067:** 기존 282개 hardcoded 대사 inventory 밖의 `code.bin` 공용/이벤트 bank에 일본어가 남아 있음을 확인했다. 대표적으로 `0x1D171D` 공명 장성/촉군/전군출격, `0x1D089F` 부담이 무겁다는 대사, `0x17F314` 주군의 서찰 대사가 별도 runtime source였다. 기존 `번역 완료` 플래그를 신뢰하지 않고 실제 v102 NUL C-string bank를 다시 스캔했다.
- **#070:** `저팽`은 번역문 오타가 아니라 `쪽=9945` Citra-failing far alias 재사용 문제였다. `조예`/`새`/`옥새`/`씨` 등 과거 실패 alias도 함께 감사했으며 필요한 문장은 안전 alias 또는 자연스러운 표현으로 재인코딩했다. `쪽`은 fresh low-lead `97DD / physical3434`를 신규 배정하고 아래 physical3507의 top scanline만 bleed guard로 비웠다. packed font readback 결과 변경 physical은 정확히 `3434, 3507` 두 셀뿐이다.
- `code.bin`은 실제 대사/팝업 **120개 고정 슬롯**을 수정했다. ARM branch 마지막 1바이트를 문자열 시작으로 오인하는 v83 실패 패턴을 gate로 재검사했고 target slot 밖 code diff는 0이다.
- `msgsec06/07/08`의 **active runtime 문제 20개**는 완전한 standalone 한국어 문자열을 EOF append하고 해당 flat absolute-byte header만 redirect했다. `02 01 C8` 동적 이름 token은 3바이트 전체를 raw 보존한다.
- v96처럼 header를 우회해 old physical body를 직접 읽는 Citra 경로에 대비해 v101에서 증명된 stale mirror 46개를 유지했고, 이번 전수 감사에서 추가로 발견한 **legacy stale fragment 41개(msg06 3 / msg07 33 / msg08 5)**를 제자리 정리했다. 모든 cleanup 대상은 verified body fragment start이며 기존 `05` separator를 이동하지 않고 원 fragment capacity 이내, 최대 20칸 이내로 치환했다.
- 감사 도구 `tools/audit_sangokushi2_v103_dialogue.py`는 flat header 자체를 body로 오인하지 않도록 `msgsec04/06/07/08`의 검증된 header byte range를 제외한다. 또한 `code.bin`의 메뉴/theme 라벨 10개와 ARM/data pseudo-string 1개는 대사 scope 밖으로 명시 분리했다.
- 최종 감사 결과: **code 대사 잔존 0 / failed alias 0 / active Message 문제 0 / Message body 일본어·한자 혼입 0**.
- 변경 Patch 파일은 정확히 6개: `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`. 나머지 v102 파일은 byte-exact다.
- 독립 verifier=`tools/verify_sangokushi2_v103_runtime_readback.py`, readback report=`analysis/v103_issue065_067_070_readback_report.json`. code 120개 슬롯 밖 diff 0, Message 승인 범위 밖 diff 0, font 두 physical 셀 밖 diff 0을 확인했다.
- 완성본 위에서 v103 빌더+verifier를 **연속 2회** 실행해 동일 SHA가 재현됐다. SHA-256: `code.bin=51FC2772C8F581A75EBEC4BF1D92F1864C07D4A41F95DF0B38F30DA4FBD51A57`, `font.g1t=E047558103DDF309BD9228750036EE074F8A56107B8E61BDA167EDD0BC1CADD6`, `msgsec04=DBF0A8B7FEA4EA955FDA8013E5C14EAA7DFBD3BC95D2BDEFBB223260BAA2172E`, `msgsec06=79F722DEB2DE4D04F22DAFFC0F6C826A3EC4004667796034537E75B7E6899B25`, `msgsec07=52D758D7CD1750F25D81FA93C8742FCA9502F17C87A9F32E626FB37ABAB5EA2C`, `msgsec08=CA7AE92F94CE3A7F12735651C288E1D888040998BE0B6AD7AD70336031796023`.
- 빌더=`tools/build_sangokushi2_v103_issue065_067_070.py`, targets=`analysis/v103_issue065_067_070_targets.json`, report=`analysis/v103_issue065_067_070_report.json`.
- **현재 Patch 권위는 v103-intermediate다. 정적/구조/결정적 검증은 완료됐고 Citra 실화면 승인만 남았다. 다음 확인은 #065/#067/#070 제보 화면을 우선한다.**

## 2026-09-01 — v104-intermediate 이미지 20종 1:1 리빌드

- 최초 확인에서 v100 표기만 좁게 검색한 오류를 사용자 지적으로 바로잡았다. 전체 MD 이력과 analysis/v103_issue065_067_070_report.json의 complete manifest, tools/verify_sangokushi2_v103_runtime_readback.py PASS를 교차 확인해 exact v103을 실제 최신 기준으로 확정했다.
- exact v103 전체 Patch 65파일을 analysis/v104_image_update_baseline/PatchSnapshot에 봉인했다. 요청된 5개 G1T는 v103 Patch에 없으므로 Sangokushi 2 Original의 대응 컨테이너를 immutable first-inclusion base로 사용했다.
- 지정 PNG는 정확히 20개다: InfoB 005/007~015, InfoI 000/001, InfoK 000/002/005, InfoT 000/001/002, InpCalc 002/003. 모두 RGBA 모드와 원 texture 치수가 일치했고 0x09 RGBA8로 삽입했다.
- 다중 texture 컨테이너마다 허용 payload span 밖 diff 0을 확인했다. source codec identity 20/20, PNG 대비 출력 pixel-exact 20/20, v103 기존 65파일 byte-exact, Original before/after SHA 동일을 통과했다. Original/Rebuild/Backup은 쓰지 않았고 이미지 생성도 사용하지 않았다.
- 완성된 v104 위에서 빌더를 다시 실행해 두 실행의 manifest와 report SHA-256 80091A1443B6A7BD1E9E891B07A543A24AD553D513C6336F50086E81962121C4가 동일했으며 독립 verifier도 두 번 PASS했다.
- 빌더=tools/build_sangokushi2_v104_image_update.py, report=analysis/v104_image_update_report.json, verifier=tools/verify_sangokushi2_v104_image_readback.py. **v104-intermediate는 v105의 sealed baseline으로 계승됐다.**

## 2026-09-01 — v105-intermediate #071 / #072 / #073 / #074 대사 재감사·수정

- exact v104 전체 Patch 70개 파일을 `analysis/v105_issue071_074_baseline/PatchSnapshot`에 봉인한 뒤 작업했다. Original/Rebuild/Backup은 수정하지 않았다. 실제 게임 파일 변경은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat` 5개뿐이며 `font.g1t`, v104 이미지 컨테이너 5개와 그 밖의 Patch 65파일은 v104 byte-exact다.
- 로컬 `Github_Issue/Issue071~074.html`과 저장된 PNG를 함께 확인한 뒤 runtime source를 다시 추적했다. #071은 `code.bin 0x1D1168`의 단독 조사 suffix `가 응하리라`가 동적 군주명 뒤에 붙어 `손권가`가 되는 문제였고, 최신 target은 **`군이 응하리라 / 믿지 않소`**다.
- #072는 번역문 오타가 아니라 Citra에서 잘못 렌더링되는 far alias 문제였다. `렇=994F`, `찰=996F`가 신고 화면뿐 아니라 실제 live dialogue 여러 곳에 남아 있음을 확인해, 폰트를 다시 배정하지 않고 문장을 자연스럽게 우회했다. 대표적으로 `그렇다면→그러면`, `서찰→서신`, `벅찰→무리`, `진찰→확인`으로 바꾸어 **현재 live code/Message 대사에서 994F/996F 사용을 0건**으로 만들었다.
- #073은 세 종류의 조립 오류를 함께 수정했다. `0x1D089F`는 동적 인물명 직후 본문이 붙어 `장제그럼...`이 되므로 **`, 그럼 부담이 / 크지 않겠습니까...`**로 구분자를 넣었다. `0x17F314`의 `서찰`은 `찰=996F` 때문에 화면에서 다른 글자로 보이므로 **`우리 주군의 서신을 / 가지고 왔습니다`**로 변경했다. `0x1D0FF0`의 `주군이 바로 / 승낙했습니다`는 동적 군주명과 중복되어 `손권주군이`가 되므로 **`군이 바로 / 승낙했습니다`**로 변경했다.
- #073과 같은 재발을 막기 위해 hardcoded 대사 전체에서 Original이 `殿...`으로 시작하면서 한국어가 `주군...`으로 시작하는 후보, 일본어가 단독 `が/は/を/に...` 조사 fragment로 시작하면서 한국어도 단일 조사로 시작하는 후보를 전수 감사했다. 실제 동적 이름 suffix로 판정된 항목은 `군/군이/군의/군이라면`, `이(가)`, `은(는)` 등으로 정리해 **post-audit 후보 0건**으로 만들었다.
- #074는 `code.bin 0x1D171D`가 원문 3줄 `[14,14,7]`인데 v104 한국어 첫 줄이 17칸이라 자동 wrap으로 4 visual line이 되어 `공명 / 이 / 없는` 식으로 밀리는 문제였다. 최신 target은 **`공명의 장성이 떨어졌다! / 공명 없는 촉군은 안 무섭다 / 전군 출격하라!!`**이며 명시적 3줄 모두 15칸 이하이다.
- 기존 `Extracted_Text` inventory만 이용한 layout 감사가 `0x1D17xx` 부근 실제 Original C-string 경계를 놓칠 수 있음을 확인했다. 새 `tools/audit_sangokushi2_v105_dialogue.py`는 **Original `code.bin`의 실제 NUL C-string 시작/끝을 직접 기준으로** hardcoded portrait 대화 15×3을 재감사한다. 이 감사에서 별도로 발견한 `0x1D121C`의 16칸 대사도 **`그 군주와는 / 교섭 불가입니다`**로 수정했다.
- v105 code fixed-slot target은 총 23개이며 모든 target은 Original NUL slot capacity 이내, ARM branch final-byte overlap 없음, `%s/%d` 등 format token 보존을 통과했다. Message는 5개 physical fragment만 제자리 수정했고 각 원 fragment 시작, `05` separator, 전체 flat header word는 byte-exact로 유지했다.
- 최종 post-audit 결과: **hardcoded 15×3 위반 0 / 동적 이름 조사·`주군` 중복 후보 0 / `994F/996F` live dialogue 0 / Message 일본어·한자 혼입 0 / active Message 문제 0**.
- 독립 verifier `tools/verify_sangokushi2_v105_runtime_readback.py`를 두 번 실행해 PASS했고, 완성본 위에서 v105 빌더를 다시 실행해 결정성을 확인했다. `code.bin` SHA-256은 두 실행 모두 `3972A742A09612CB7594765AC2BBE64D2D485496B9F65370E93298F56BED4203`이다. 나머지 변경 파일 SHA-256은 `msgsec04=7BB69A028AB969267DF2DC13BA9E16A8B8C5054D48767B25F222DFCF7D236186`, `msgsec06=DF01F1712D6ED4CEA7F49372CC60F5F07ECFF15799D180DE4AE79AD5D7036C29`, `msgsec07=7BFD900046411E96C0B6BAB1FB9879DB786DEDE154517F3A215067FAE4F58137`, `msgsec08=EF78F6E68AD3D1CF6652AB34D24D1ED62C27289AE772B504C02BA79C99205B3B`이다.
- 빌더=`tools/build_sangokushi2_v105_issue071_074.py`, targets=`analysis/v105_issue071_074_targets.json`, report=`analysis/v105_issue071_074_report.json`, 감사기=`tools/audit_sangokushi2_v105_dialogue.py`, 독립 verifier=`tools/verify_sangokushi2_v105_runtime_readback.py`.
- **이 문단 작성 당시 Patch 권위는 v105-intermediate였다.** 이후 v106~v109에 계승·수정됐으며 현재 권위는 문서 하단의 v109-intermediate다.

## 2026-09-01 — v106-intermediate #076 / #077 / #078 / #079

- 사용자가 지정한 최신 기준 `v105-intermediate`의 complete manifest를 검증해 `analysis/v106_issue076_079_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- #076 스크린샷 17장을 이름 inventory와 대조해 문제 글자를 `온/준/풍/륭/열/촉/욱/유/휴/겸/수/교/각`으로 확정했다. 이 글자들이 포함된 표시 이름 123레코드를 최신 인명 전용 map으로 재인코딩했다. 표시 이름은 1,020건 모두 inventory와 의미가 일치하고, 뒤 가타카나 읽기 17바이트는 1,020건 모두 v105 byte-exact다.
- 인명 12자와 대사에 새로 필요한 `섯/떤`에 original-valid·effective runtime 참조 0인 일본어 donor 14개를 배정했다. 각 donor 바로 아래 unmapped 셀을 bleed guard로 함께 이동했다. packed font 독립 역읽기에서 변경 셀은 허용 28/28, 범위 밖 0이다. `촉`은 v101에서 검증된 `8D6F`를 재사용했다.
- #077 실제 source가 기존 감사가 제외한 `msgsec21.dat`임을 확인했다. 223년 유선 플레이 오로 침공 사건의 연속 20 fragment(`0018_001~0028_000`)를 모두 번역했다. fragment별 `05` 및 `1B K/H` 흐름 제어를 원문과 byte-exact로 보존했다. **당시 tail 30word를 metadata로 본 판정은 v107 #081에서 absolute BYTE runtime pointer로 정정됐으므로 재사용하지 않는다.**
- #078은 한 C-string 내부 혼입이 아니라 런타임 조립형 문자열이었다. `%s様、` 21곳과 확인된 짧은 일본어/조사/조건 조각 16개를 고쳤다. #079는 첫 조각 뒤 공백을 추가해 최종 조립문 `우리와 동맹을 / 맺어 주길 바란다`를 역읽기했다.
- 변경 파일은 정확히 4개: `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec21.dat`, `RomFS/Scenario/fix_data.bin`. 그 밖의 v105 Patch 파일과 v104 이미지 컨테이너 5개는 byte-exact다.
- 완성본 위 빌더를 재실행해 전체 Patch manifest와 report SHA가 동일했고, 독립 `tools/verify_sangokushi2_v106_runtime_readback.py`가 이름 1,020/읽기 1,020/font 28셀/code 38 target/msgsec21 20 fragment를 다시 검증해 PASS했다. inherited audit는 hardcoded layout 0 / dynamic suffix 0 / Message mixed 0 / active Message 0이다.
- 권위 자료는 `analysis/v106_issue076_079_targets.json`, `analysis/v106_issue076_079_report.json`, `tools/build_sangokushi2_v106_issue076_079.py`, `tools/verify_sangokushi2_v106_runtime_readback.py`다. v106은 #080~#083 재검증에서 `msgsec21` 구조/일부 alias 판정이 실패한 선행 revision으로 남기며 현재 권위는 아니다.

## 2026-09-01 — v107-intermediate #080~#083 구조 재수정

- exact v106을 `analysis/v107_issue080_083_baseline/PatchSnapshot`에 봉인했다. 변경은 `code.bin`, `msgsec21.dat`, `fix_data.bin` 3개뿐이고 font는 v106 byte-exact다.
- #081에서 `msgsec21` 67-word header를 **37 word pointer(0..36, 마지막 EOF) + 30 absolute byte runtime pointer(37..66)**로 재확정했다. `tools/sangokushi2_msgsec21_dual_header.py`의 Original 3708B identity rebuild가 byte-exact PASS했고 번역 후 두 pointer 계열을 모두 재계산한다.
- `msgsec21`은 검수 91개 + split Shift-JIS 기술 fragment 4개 제거, 총 95개를 재구성했다. 가짜 고바이트 제어 토큰 0, target far alias 0, 20칸 초과 0이다. 223년 유선 사건은 Original 문맥 기준으로 유비 사망→사마의 오로 계책→가비능/맹획/손권/맹달/조진→제갈량 대응으로 정리했다.
- #082/#083 `code.bin`: `0x180DDC=다른 `, `0x180DEC=%s조건을\\n제시합니까？`, `0x180F94=%s이(가) 안 돌아왔습니다`, `0x181290= 포기합니까？`. Original exact source signature residue gate를 추가했고 실패 alias `떤=9963`을 제거했다.
- #080 phase1은 `オウセイ=区星=구성`이 맞음을 재확인했다. `헌=8972, 익=92CB, 룡=92DE, 삭=9554, 북=89D0, 뢰=94D6, 농=956B`를 적용해 13개 이름을 재인코딩했으며 1,020명 의미/읽기를 모두 검증했다.
- 결정성 SHA: code `0A7EC7E3C7EAF7CDE28245D3623A3FCBF4FF21A112E4BEBEE300F9AA1E96F493`, msg21 `7A83F293EA519DB43A69E23967480C84417F7AC833C16340A3D30DCFC2E6110D`, fix `14E5416EAAA3198B344258038E7D035056661464BE05E90C7204A1EABC8BB0EB`. 독립 `tools/verify_sangokushi2_v107_issue080_083.py` PASS.

## 2026-09-01 — v108-intermediate #080 인명 phase2 완료

- exact v107을 `analysis/v108_issue080_name_phase2_baseline/PatchSnapshot`에 봉인하고 남은 far-alias 인명 27자를 전부 low-lead로 이전했다. 변경은 `font.g1t`, `fix_data.bin` 2개뿐이며 v107 code/msg21 및 나머지는 byte-exact다.
- 22 donor는 v107 code+모든 Message+Scenario 참조 0 / name owner 0이다. `9555/96A5/9777/9787/97B7`은 `fix_data`의 인물 레코드 **비텍스트 field +4**에서만 우연히 일치하며 표시 이름(+18)/읽기(+27)와 겹치지 않는다.
- font diff는 정확히 27 donor cell, 범위 밖 0, packed readback pixel-exact다. v107 대비 이름 46레코드가 변경됐고 **1,020/1,020 의미 PASS, 1,020/1,020 가타카나 읽기 byte-exact, 이름 far alias 0건**이다.
- 결정성 SHA: font `6A91C3FBD8CB9A976FA443961F41D24AB7B484172C4DAA767F19F17D511CF735`, fix `5C40E354C3723F7C92F7A1DE943BE27DF9A07F891E82D45679A1A2DB4E14F0A4`. 독립 `tools/verify_sangokushi2_v108_issue080_name_phase2.py` PASS, 요약 `analysis/v108_issue080_name_phase2_readback_report.json`.
- **v108은 #080~#083 텍스트·폰트·인명 권위로 후속 v109에 byte-exact 계승됐다.**

## 2026-09-01 — v109-intermediate Common UI 이미지 106종

- exact v108 Patch 70파일 manifest를 확인하고 `analysis/v109_image_update_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- 지정 PNG 106개만 반영했다: Select 55개(`005~059`), GuidBtn 35개(`012~046`), Dialog 14개(`007~012`, `015~022`), Common 2개(`009~010`). AI 이미지 생성은 사용하지 않았다.
- 기존 `Select.g1t`는 sealed v108 Patch를 기반으로 했고, v108에 없던 `GuidBtn/Dialog/Common.g1t`만 immutable Original을 최초 편입 base로 사용했다.
- 대상은 모두 `0x09 RGBA8`이다. 106개 base codec identity, PNG mode/치수, packed pixel-exact readback을 통과했고 네 컨테이너의 header·비대상 payload diff는 0이다.
- v108 대비 변경 파일은 `RomFS/Common/Select/Select.g1t`, `GuidBtn/GuidBtn.g1t`, `Dialog/Dialog.g1t`, `Common/Common.g1t` 4개뿐이다. v108 code/font/Message/fix_data와 그 밖의 Patch 파일은 byte-exact다.
- 완성본 위 2회 결정적 재빌드와 독립 `tools/verify_sangokushi2_v109_image_readback.py`가 PASS했다. report SHA-256은 `8B7E2CD23560EF3021902850BF55905D77DA5877C9419640C58B537E0264992E`다.
- 빌더=`tools/build_sangokushi2_v109_image_update.py`, report=`analysis/v109_image_update_report.json`, verifier=`tools/verify_sangokushi2_v109_image_readback.py`. **이 문단 작성 당시 Patch 기준은 v109-intermediate였으며, 이후 v110~v125를 거쳐 아래 v126-intermediate가 현재 권위다.**

## 2026-09-02 — v126-intermediate #099 / #104 / #105 / #112

- 작업 시작 전 `PROJECT_RULES.md / WORK_LOG.md / PATCH_HANDOFF.md / 번역.md`와 `analysis/v125_issue106_107_108_report.json`을 대조하고 v125 독립 verifier를 다시 실행해 **실제 현재 Patch 기준이 v125-intermediate**임을 확인했다. WORK_LOG의 물리적 하단 v109 표기는 최신 계보를 반영하지 못한 역사 기록으로 정정한다.
- 로컬 `Github_Issue/Issue099/104/105/112.html`과 저장된 첨부 PNG를 함께 확인했다. 스크린샷은 프로젝트 파일을 수정하지 않고 로컬 Windows OCR로 표시 문자열을 보조 확인했으며, 최종 source 판정은 현재 Patch/Original binary와 포인터 구조를 교차검증해 확정했다.
- **#099:** 상단 `内容 / 条件`과 `援助`는 이미지가 아니라 `code.bin`의 고정 텍스트다. 각각 `0x1CFB40=내용`, `0x1CFD5C=조건`, `0x1CED30=원조`로 **4B 동일 길이** 치환했다. 대응 포인터 `0x1DC5B8`, `0x1AB2FC`, `0x1AB3A4/0x1DBAD0/0x1E50D8`은 byte-exact다. #099 때문에 G1T/PNG는 전혀 수정하지 않았다.
- **#104:** 실제 source는 `msgsec09.dat 0x88E..0x8AB`, 29B fixed span이며 앞 `02 2D`는 동적 군주명 prefix다. 기존 suffix `: 타세력 동맹 체결/파기`를 사용자 1안 **`의 타세력 동맹 체결/파기`**로 변경했다. prefix 포함 26B라 29B 안에 들어가므로 fallback `의 타세력 동맹 편집`은 사용하지 않았다. 88-word header와 `05 05 05` separator 위치는 모두 보존했다.
- **#105:** 실제 runtime source는 `msgsec06.dat header[63] -> 0x936`의 38B standalone target이다. 사용자 1안은 49B, 2안은 47B로 초과하고 3안은 29B라 **`그리 쉽지는 않을 것 같습니다.`**를 선택했다. 남는 9B는 ASCII space padding으로 유지했고, 161-word flat absolute-byte header 전체와 header[63], 바로 뒤 `05 05 05` separator 및 전체 파일 크기는 byte-exact다.
- **#112:** `プレイ時間`은 `code.bin 0x1CFF6C`의 10B source이고 **`플레이시간`**도 10B라 exact-size 치환했다. 세이브 완료 화면은 실제 pointer `0x1AB2D4 -> 0x2CF4DC`가 가리키는 `code.bin 0x1CF4DC` visible 20B C-string이다. 사용자 1안 `세이브를 완료했습니다.`는 22B라 초과하여 허용 2안 **`세이브 완료`**를 사용했고 나머지는 NUL padding으로 채웠다. 해당 포인터는 변경하지 않았다.
- exact v125 전체 Patch를 `analysis/v126_issue099_104_105_112_baseline/PatchSnapshot`에 봉인했다. v126 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec06.dat`, `RomFS/Message/msgsec09.dat` 3개**다. `font.g1t`, `fix_data.bin`, 모든 이미지/G1T와 그 밖의 Patch 파일은 v125와 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 완성본 위에서 빌더를 다시 실행하고 독립 verifier를 다시 실행해 **2회 결정적 재생성 + 독립 readback PASS**를 확인했다. code target 5개, code diff 35B, #104 dynamic prefix `022D`, #105 header[63] 및 모든 구조 gate가 정상이다.
- SHA-256: `code.bin=D4CF43C0A89381AB33572AD3176AAA196731354A39361B67D6AE70C3E55DDBF4`, `msgsec06.dat=07D472269B436FCDB519E7CD86F3CE3E95A6711B2D4862E15EA7391FBDCFBEF2`, `msgsec09.dat=6BB0E83367EAF9CB590AE905C4C36D7A347B4F0D7D04F220D7FB41FBAA174EC4`.
- 권위 자료: `analysis/v126_issue099_104_105_112_targets.json`, `analysis/v126_issue099_104_105_112_report.json`, `tools/build_sangokushi2_v126_issue099_104_105_112.py`, `tools/verify_sangokushi2_v126_issue099_104_105_112.py`.
- **이 절 작성 당시 Patch 권위는 v126-intermediate였으며, 현재는 아래 v127-intermediate가 계승한다.** 정적/구조/결정적 빌드는 PASS했지만 Citra 실화면 확인은 아직 pending이므로 최종 승인으로 표현하지 않는다.

## 2026-09-02 — v127-intermediate Hex 전투 시간·군 표기

- exact v126 전체 Patch를 `analysis/v127_hex_battle_time_army_baseline/PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 두 파일만 수정했다. Original/Rebuild/Backup 및 `Extracted_Image/.../HexUpUI_000.png`는 수정하지 않았다.
- `code.bin` 시간 슬롯 `朝@0x1D07DC / 昼@0x1D07D8 / 夜@0x1D07E0`을 `아침 / 낮 / 밤`으로 바꾸고, 공격·수비 전용 `%s軍@0xC9174/0xC9314`를 `%s군`으로 동일 길이 치환했다. 시간 pointer table `0x1B2E24`, literal pool `0x10E758/0x10E8A8`, 양측 ADR `0xC9030/0xC91D4`는 byte-exact다.
- 사용자 `Extracted_Image/RomFS/Common/Font/morning/아침1414.png`(14×14 RGBA, SHA-256 `6B78EAAAA2B0F40184B581F3D9CD6A05B5A4AFC6C43997668FFEDBE1B6A53E12`)를 fresh local-only donor `96D7/physical3240` 한 셀에 넣었다. font 변경 cell은 `[3240]` 하나뿐이고 packed readback pixel-exact다. `낮=9155`, `밤=9371`, `군=88C7`의 기존 Sam3 셀은 byte/pixel-exact로 유지했다.
- v126 대비 게임 파일 변경은 정확히 code/font 2개다. SHA-256은 `code=0EB9AEB86720634E5B0E9B8CEC50D89D479DDA06A50064CEC93C6630FF17A4DF`, `font=32FF5F7A66D16FDA8F176F3F8C5E2F32AC402B41D8359606B94591E1D62E4EA6`다. 완성본 위 결정적 재빌드와 독립 verifier가 PASS했다.
- v103 dialogue audit code residue는 0이며 active 7/mixed 10은 v126 baseline과 동일한 후속 alias decode 결과다. v127이 Message를 바꾸지 않았고 모든 비대상 파일이 v126과 byte-exact이므로 신규 대사 회귀는 없다.
- 권위 자료: `analysis/v127_hex_battle_time_army_targets.json`, `analysis/v127_hex_battle_time_army_report.json`, `analysis/v127_hex_battle_time_army_preview.png`, `tools/build_sangokushi2_v127_hex_battle_time_army.py`, `tools/verify_sangokushi2_v127_hex_battle_time_army.py`. Citra 실화면 확인 전까지 intermediate다.

## 2026-09-02 — v128-intermediate 지정 UI PNG 40개 리빌드

- exact v127 전체 Patch를 `analysis/v128_image_update_baseline/PatchSnapshot`에 봉인하고 사용자가 지정한 PNG 40개만 반영했다. 범위는 `edit_bushou_edit_009` 1개, `start_menu_up_017` 1개, `HexCommand_002~005` 4개, `HexFontEffect_001~031` 31개, `HexUpUI_000/004/005` 3개다. 이미지 생성은 사용하지 않았다.
- `RomFS/EditBushou/edit_bushou_edit.g1t`와 `RomFS/StartMenu/start_menu_up.g1t`는 이미 Patch에 있으므로 sealed v127 컨테이너를 base로 사용했다. v127 Patch에 없던 `RomFS/Hex/UI/HexCommand.g1t`, `HexFontEffect.g1t`, `HexUpUI.g1t`만 immutable Original에서 최초 편입했으며 Original/Rebuild/Backup은 수정하지 않았다.
- 40개 모두 RGBA8·원 치수·입력 SHA를 고정했다. 모든 base texture의 decode→encode identity, 모든 출력 texture의 pixel-exact readback, 각 G1T header·비대상 payload byte-exact를 통과했다.
- Hex 38개는 당시 정상 편집 방향 PNG에 `from_edit_view(..., flip_y)`를 적용한 뒤 표준 G1T encoder에 넣었다. 정적 왕복 검사는 통과했지만 사용자 v129 Citra에서 상하 반전 실패가 확인됐으므로 **이 방식은 역사적 실패 기록이며 v130에서 폐기·교정됐다.** HexCommand를 회전하지 않은 판단은 유지한다.
- v127 대비 변경/추가 Patch 파일은 정확히 5개다. SHA-256: `edit_bushou_edit=44F91BB8322240BB5CE3C0F586C39C0B442B882A529F522E1DB344459514346E`, `start_menu_up=D1AD13D9E40F367243FD8DE2FB0377798F7D2E4BE611C3C007A741DB36B7AA75`, `HexCommand=31CB553B160C3AD39C03537FF95457CBBC09C85FEC24B5D39ABEE4607201E865`, `HexFontEffect=DFCC05CC8D52720B0B6EEC200009B0046F74BB08C96006CEABEC645564CA12CE`, `HexUpUI=A614A2D2CD4E733422B8AECF70E7454C5D67FECD1B662E555CB42A70B8254B9D`.
- 권위 자료: `analysis/v128_image_update_targets.json`, `analysis/v128_image_update_report.json`, `tools/build_sangokushi2_v128_image_update.py`, `tools/verify_sangokushi2_v128_image_update.py`. 결정적 재빌드와 독립 verifier PASS, Citra 실화면 확인 전까지 intermediate다.

## 2026-09-02 — v129-intermediate #099 / #105 재추적 + #113 / #114

- 사용자가 v128 Citra에서 **#104와 #112는 정상**, 반면 **#099의 `条件`과 #105 원조 대사는 그대로**라고 실화면으로 재확인했다. 따라서 v126의 해당 source 판정을 정적 분석보다 낮은 권위로 취급하고 다시 추적했다. 작업 베이스는 `analysis/v128_image_update_report.json`의 complete manifest와 현재 Patch를 독립 verifier로 대조해 exact v128임을 확인한 뒤 `analysis/v129_issue099_105_113_114_baseline/PatchSnapshot`에 봉인했다.
- **#099 source 판정 교정:** v126에서 고친 `code.bin 0x1CFD5C=条件`은 이 패널의 Citra source가 아니었다. 실제 화면 owner는 **`code.bin 0x0B465C=条件`**, ADR `0x0B4430/0x0B4464`가 직접 참조한다. v129은 이 실제 4B slot만 **`조건`**으로 동일 길이 치환한다. 이미 정상 확인된 `내용@0x1CFB40`은 보존한다. v126의 duplicate `0x1CFD5C=조건`은 다른 기존 수정과 함께 그대로 남기되 #099 화면 source로는 더 이상 간주하지 않는다.
- **#105 source 판정 교정:** v126에서 `msgsec06 header[63] -> 0x936`을 수정했지만 Citra 화면은 그대로였다. 실제 제보 화면과 byte-exact로 일치하는 hardcoded source는 **`code.bin 0x1D08C4` Original `そうたやすく援助が\n受けられるとは・・・`**, literal pointer owner는 `0x1E5074`다. 실제 capacity는 39B이며 사용자 1안 49B, 2안 47B는 overflow하고 3안 29B만 들어가므로 최종 **`그리 쉽지는 않을 것 같습니다.`**를 이 실제 slot에 쓴다. v126의 msgsec06 duplicate는 v129에서 다시 쓰거나 되돌리지 않고 v128 byte-exact로 보존한다.
- **#113 root cause:** `code.bin 0x185068` formatter는 이미 한국어 `그럼 게임을 %s.`였지만 별도 argument `0x185054=再開`가 그대로여서 화면 뒤쪽에 일본어/한자가 섞였다. 세 ADR owner `0x18501C/0x185020/0x185024`를 그대로 두고 같은 고정 영역 안에서 `0x185054=재개`, `0x18505C=시작`, `0x185068=그럼 게임을 %s합니다.`로 바꿔 기대 조립을 **`그럼 게임을 재개합니다.` / `그럼 게임을 시작합니다.`**로 만든다.
- #113 요청에 따라 같은 runtime class를 전수 감사했다. **한국어 `%s` formatter와 ±16B 이내 ADR로 함께 조립되는 짧은 CJK argument**에서 `曹操@0x5839C`, `劉玄徳@0xB01C8`, `太守@0x15D8F8`, `再開@0x185054` 4건을 찾았고 각각 `조조 / 유현덕 / 태수 / 재개`로 정리했다. 또한 **이미 일부 한국어로 바뀐 ADR direct dialogue인데 CJK/kana가 남은 경우**는 dialogue/popup bank로 범위를 제한해 `0x10E118=中断세이브 없이...`, `0x1671FC=%s님, %sが...` 2건을 확정했다. 각각 **`중단 저장 없이\n게임을 종료하시겠습니까？`**, **`%s님, %s이(가)\n합류했습니다`**로 fixed-slot 수정했다. 아이템 능력치처럼 legacy alias raw bytes가 CP932 CJK로 보일 수 있는 비대사 UI는 이 감사에서 제외한다. 최종 same-class residue는 short argument 0 / partial mixed dialogue 0이다.
- **#114 병과 table:** 실제 출진 목록 source는 `code.bin 0x1B2E30`의 7-pointer table이다. v128 상태는 `歩兵 / 기마 / 노 / 강노 / 闘艦 / 蒙衝 / 走舸`였으며 포인터 자체는 유지하고 미번역 4종만 `0x1CFE90=보병`, `0x1CFF2C=투함`, `0x1CFFA8=몽충`, `0x1D07D0=주가`로 바꿨다. 최종 역읽기 목록은 **`보병 / 기마 / 노 / 강노 / 투함 / 몽충 / 주가`**다.
- v129은 **code-only Revision**이다. exact v128 대비 변경 파일은 `ExeFS/code.bin` 하나뿐이고 diff는 135B다. v128의 40개 이미지/G1T, v127 font, 모든 Message/Scenario/fix_data는 byte-exact다. 특히 사용자가 정상 확인한 #104 `msgsec09`와 #112 `세이브 완료 / 플레이시간`은 그대로 보존한다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 첫 감사 단계에서 비문자열 ADR의 NUL 부재와 주소 표기 zero-padding 때문에 두 차례 사전 gate가 멈췄으나 **Patch 쓰기 전 단계**였고 게임 파일 변경은 없었다. 감사기는 비문자열 ADR skip, numeric address 비교, dialogue-only partial-mixed scope로 보정했다. 이후 완성본 위에서 빌더를 다시 실행해 동일 결과를 재생성했고, 독립 verifier를 두 차례 실행해 모두 PASS했다.
- 최종 SHA-256: `ExeFS/code.bin=796F5C351866D0A9508008B6C4810F1FE70644E73105356F94BD0FCC16A005A5`. 권위 자료는 `analysis/v129_issue099_105_113_114_targets.json`, `analysis/v129_issue099_105_113_114_report.json`, `tools/build_sangokushi2_v129_issue099_105_113_114.py`, `tools/verify_sangokushi2_v129_issue099_105_113_114.py`다.
- **현재 Patch 최종 기준은 v129-intermediate**다. 정적 구조/manifest/결정적 재빌드/독립 readback은 PASS했지만 #099/#105/#113/#114의 새 화면은 Citra 실화면 재확인 전까지 최종 승인으로 표현하지 않는다.

## 2026-09-02 — v130-intermediate Hex UI 상하 반전 교정

- 사용자 v129 Citra 화면에서 `HexCommand`, `HexFontEffect`, `HexUpUI`의 적용 이미지가 위아래 반전된 것을 확인했다. 반면 `edit_bushou_edit`와 `start_menu_up`은 정상이라고 명시적으로 확인됐으므로 두 컨테이너는 수정 대상에서 제외하고 exact 보존했다.
- 원인은 v128 pack 경로의 중복 방향 변환이다. 정상 방향 `Extracted_Image` PNG에 `from_edit_view(flip_y)`를 먼저 적용하고 다시 표준 RGBA8 encoder를 호출해, runtime에서 결과가 거꾸로 표시됐다. v128/v129 결과를 decode하면 사용자 PNG의 `FLIP_TOP_BOTTOM`과 pixel-exact라는 실패형을 38개 전부에서 재현했다.
- exact v129 전체 Patch를 `analysis/v130_hex_orientation_repair_baseline/PatchSnapshot`에 봉인했다. 수정 대상은 `HexCommand_002~005` 4개, `HexFontEffect_001~031` 31개, `HexUpUI_000/004/005` 3개로 정확히 38개다. 정상 방향 PNG를 **추가 반전 없이 표준 encoder에 직접 입력**했고, 출력 표준 decode가 각 PNG와 pixel-exact임을 확인했다. HexCommand는 원 치수·세로 atlas를 유지했으며 90도 회전하지 않았다.
- v129 대비 변경 파일은 정확히 `RomFS/Hex/UI/HexCommand.g1t`, `RomFS/Hex/UI/HexFontEffect.g1t`, `RomFS/Hex/UI/HexUpUI.g1t` 3개다. 각 G1T header와 비대상 texture payload는 byte-exact이고, `RomFS/EditBushou/edit_bushou_edit.g1t`, `RomFS/StartMenu/start_menu_up.g1t`, `ExeFS/code.bin`, font, Message 및 그 밖의 v129 파일도 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 최종 SHA-256: `HexCommand=F01C6DFC4789ED5F82F4409115AE2787567409CD81567F0C669273CD91A930F6`, `HexFontEffect=69B1C30829C22B9ACBF1DA902E17BDC6F724BC19E58F968E721145A5DDDCDEF1`, `HexUpUI=22D7E4417D5BC7DBE96AC8A901C196D5F281DF78352D68B46451642549B3E33E`.
- 재발 방지 gate는 `v129 decode == FLIP_TOP_BOTTOM(PNG)` 실패형 재현, `v130 decode == PNG` 교정형 확인, header/비대상 payload/비대상 Patch manifest exact, 회전 금지를 함께 검사한다. 권위 자료는 `analysis/v130_hex_orientation_repair_targets.json`, `analysis/v130_hex_orientation_repair_report.json`, `tools/build_sangokushi2_v130_hex_orientation_repair.py`, `tools/verify_sangokushi2_v130_hex_orientation_repair.py`다.
- **v130은 v131의 exact sealed baseline**이다. 결정적 재빌드와 독립 verifier는 PASS했으며 Hex UI 방향 규칙은 v131에 byte-exact 계승한다.

## 2026-09-02 — v131-intermediate #117 / #118 / #119 / #120 + msgsec09 공통 runtime 전수 보정

- 시작 전 `tools/verify_sangokushi2_v130_hex_orientation_repair.py`를 `py -3`로 재실행해 현재 Patch가 exact v130-intermediate임을 확인하고 `analysis/v131_issue117_120_baseline/PatchSnapshot`에 전체 manifest를 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- **#117 사관 조사:** 실제 hardcoded formatter `code.bin 0x15B0C0`은 v130에서 `%s%s에서 %s\n라는 자가 사관했습니다`였다. `(이)`를 추가하면 원 `에서` 유지 시 allocation을 넘으므로 사용자 요구 의미를 유지하면서 `에서→에`로 줄여 최신 target을 **`%s%s에 %s\n(이)라는 자가 사관했습니다`**로 했다. 기대 화면은 `진류에 전풍(이)라는 자가 사관했습니다` 계열이다.
- **#118 황충→메뚜기 떼:** Original formatter는 `0x15E524 = いなごが大%s`, 공통 suffix는 `0x1D3004 = 発生しました` 계열이다. formatter를 **`메뚜기 떼가 %s`**로 바꾸고 event-name pointer `0x1DC428`을 code slack `0x1F3F00`의 **`메뚜기 떼`**로, common 발생 pointer `0x1E5ECC`를 `0x1F3F20`의 **`발생하였습니다.`**로 relocation했다. 독립 조립 readback은 정확히 **`메뚜기 떼가 발생하였습니다.`**다. 신규 글리프는 `뚜=8D56/physical1420`, `떼=97CF/physical3420`; 두 alias는 v130 Message/Scenario ref 0, code text ref 0, historical owner 0이며 raw code 우연 hit만 존재한다. directly-below unmapped guard `1493/3493`은 bottom row가 비어 있어 한 줄 아래로 이동해 top-edge bleed를 차단했고 font 변경 cell은 정확히 `[1420,1493,3420,3493]`이다.
- **#119 이동:** `msgsec07 header[79]`은 과거 detangle 시 의도적으로 `누구를`을 건너뛰고 `이동시키시겠습니까？`만 가리키던 상태였다. 기존 msg07 body를 byte-exact 보존하고 EOF에 **`누구를 이동시키시겠습니까?`**를 append한 뒤 header[79] 하나만 새 absolute byte pointer로 전환했다. 같은 화면의 `code.bin 0x183554 = 輸送量入力 輸送先:%s`는 **`수송량 입력 수송지:%s`**, 별도 spaced duplicate `0x1CFF24=闘 艦 / 0x1CFFA0=蒙 衝 / 0x1CFC28=走 舸`는 각각 **`투함 / 몽충 / 주가`**로 정리했다. 기존 붙여 쓴 병과 문자열과 pointer table은 보존한다.
- **#120 v131 역사적 실패 기록:** 하단 깨진 `x払いが出来ず`를 `msgsec09.dat`의 미번역 active pointer 문제로 판단해 32개 runtime pointer를 EOF 한국어 standalone으로 전환했으나, 이후 사용자가 **일본판 동일 세이브·동일 시점**을 직접 비교해 정상 원문이 `都市の規模が / 中規模になりました！`임을 확인했다. 따라서 이 화면을 `msgsec09` 지급 실패 이벤트로 본 v131 진단은 **폐기**한다. v131의 32-pointer #120 변경도 v132에서 제거한다. `msgsec09`의 개별 번역 필요성 자체와 #120 화면 source는 별개의 문제이며, Citra가 다른 메시지를 보여 주면 정적 residue 감사보다 실제 이벤트 routing을 우선한다.
- exact v130 대비 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec09.dat`** 네 개뿐이다. 최종 SHA-256: `code.bin=D3490D38D60F2D27EDEFF9EE6C805617DAA9E4948D244BD60E10BDB7E2F241F1`, `font.g1t=C5AD71FE7B5E6F5C013FE849A66E48DB2EB6531D9333F084DBF7F5253482EA4B`, `msgsec07=12F51E85B18A692EE06265EE6AF0C6AC59E1CDFDBB8453252578C72C97ABF966`, `msgsec09=A1235A1DFCE4AE57B23B2986BEF890C5018B19FDB89DA1563B679E369E014B9F`.
- 1차 builder PASS 후 독립 verifier에서 verifier 자체의 LF 표현(`\\n` vs 실제 newline) 비교 버그가 한 번 검출됐다. 실제 Patch 바이트는 target과 exact였고 verifier만 수정했다. 수정 후 독립 verifier PASS. 이어 sealed v130에서 builder를 다시 실행한 결과 네 파일 SHA가 1차와 완전히 동일했으며 두 번째 verifier도 PASS했다. `msgsec09 relocation=32 / active Japanese residue=0 / all other v130 files byte-exact`를 재검증했다.
- 권위 자료: `analysis/v131_issue117_120_targets.json`, `analysis/v131_issue117_120_report.json`, `analysis/v131_issue117_120_baseline/PatchSnapshot`, `tools/build_sangokushi2_v131_issue117_120.py`, `tools/verify_sangokushi2_v131_issue117_120.py`.
- v131은 #117~#119의 직접 수정 권위로 v132에 계승된다. **#120 진단과 msgsec09 32-pointer 변경은 v132에서 폐기·제거됐다. 현재 Patch 전체 권위는 아래 v132-intermediate다.**

## 2026-09-02 — v132-intermediate #120 msgsec06 mixed-header 구조 복구

- 사용자 일본판 Citra에서 한글판과 **동일 세이브·동일 시점**을 비교한 결과 정상 원문은 `都市の規模が / 中規模になりました！`였다. 따라서 한글판의 `協力してくれた… / 約束通りの支払いが出来ず` 출력은 번역 문제가 아니라 **다른 메시지를 읽는 포인터/인덱스 구조 오류**로 확정했다. 정상 한국어 target은 기존 body에 이미 존재하는 **`도시 규모가 / %s 규모가 되었습니다!`**다.
- immutable Original `msgsec06.dat`와 `Extracted_Text/sangokushi2_translation.json`의 75개 parent를 전수 대조해 실제 헤더 모델을 재확정했다. **word[0..74]은 75개 parent의 WORD offset table**이며 모든 값이 각 parent `offset_words`와 75/75 exact 일치한다. **word[75..160]은 absolute BYTE runtime pointer 86개**이고, Original 분류는 84 fragment-start + 2 inside-fragment(`header[81]→msgsec06_0036_000+2`, `header[122]→msgsec06_0054_001+29`)다. 과거 `sangokushi2_msgsec06_flat_header.py`의 all-byte 모델은 잘못됐다.
- 최초 손상 revision도 재현했다. formal v53 `msgsec06` SHA-256은 `069068FC4F2DD21DC13ADC8B558D6B0FFB719C16EA736BC1296273C3DA439060`이며 parent[74]는 **`0xB19`→byte `0x1632`**다. v54 `repair_msg06()`가 first 75를 byte pointer처럼 다시 쓰면서 **75개 중 74개를 변경**, parent[74]도 `0xA94`로 바꿨다. 즉 #120 계열의 근본 오류는 v54의 구조 오판에서 시작됐다.
- 현재 v131 body를 직접 확인하면 `0x1632=까？`, 이어지는 separator 뒤 **`0x1638=도시 규모가 / %s 규모가 되었습니다!`**가 그대로 살아 있다. 따라서 v132은 body 재패킹이나 번역 재작성 없이 `msgsec06` first 75 parent WORD table만 formal v53의 정상 값으로 복구했다. **word[75..160] 86개와 byte 322 이후 body는 v131 byte-exact**다. 복구 후 first 75는 단조 증가하며 `word*2` OOB가 0이다.
- v131에서 #120을 잘못 진단해 추가했던 `msgsec09` 32-pointer EOF redirect는 이번 revision에서 제거했다. `msgsec09.dat`는 exact pre-v131/v130 상태(`size=2845`, SHA-256 `6BB0E83367EAF9CB590AE905C4C36D7A347B4F0D7D04F220D7FB41FBAA174EC4`)로 되돌렸다. **이는 전체 v130 롤백이 아니라 v131의 잘못된 #120 변경만 제거한 것**이며 #117~#119의 `code.bin/font/msgsec07`은 byte-exact 보존한다.
- 사용자 측에서 추가된 `ExeFS/icon.bin`은 v131 manifest 외 extra였지만 기존 v131 파일 drift는 없었다. v132 baseline에 함께 봉인해 byte-exact 보존했고 삭제/수정하지 않았다.
- 동일 Original 구조 감사에서 `msgsec04/07/08/09`도 각각 **24/177/60/41개 parent WORD prefix**가 100% `offset_words`와 일치함을 확인했다. 현재 파일을 이 모델로 검사하면 OOB parent entry가 각각 **3/86/30/31개** 존재한다. 매우 중요한 동일 구조 위험이지만 사용자 요청대로 v132에서는 **msgsec06만 수정**하고 나머지는 감사 결과만 기록했다. #120 Citra 확인 뒤 별도 revision으로 다룬다.
- v132 변경 게임 파일은 정확히 `RomFS/Message/msgsec06.dat`, `RomFS/Message/msgsec09.dat` 2개다. 최종 SHA-256은 `msgsec06=CF6BEDFA252880E415413D0A88CC663B4B5CDB8413EB1744A1DBDAB9F1877E02`, `msgsec09=6BB0E83367EAF9CB590AE905C4C36D7A347B4F0D7D04F220D7FB41FBAA174EC4`다.
- 독립 verifier가 Original 구조를 별도 재분류해 75 WORD + 84 BYTE-start + 2 BYTE-inside를 확인했고, #120 경로 `parent[74]=0xB19 → 0x1632 → 0x1638 도시 규모`를 재검증했다. builder idempotency gate의 재실행 순서 문제를 도구에서 한 차례 수정한 뒤, 완성본 재빌드 전후 두 파일 SHA가 완전히 동일하고 두 번째 verifier도 PASS했다.
- 권위 자료: `analysis/v132_issue120_msgsec06_mixed_header_targets.json`, `analysis/v132_issue120_msgsec06_mixed_header_report.json`, `analysis/v132_issue120_msgsec06_mixed_header_baseline/PatchSnapshot`, `tools/build_sangokushi2_v132_issue120_msgsec06_mixed_header.py`, `tools/verify_sangokushi2_v132_issue120_msgsec06_mixed_header.py`.
- **v132-intermediate는 사용자 Citra에서 실패가 확인됐다.** 로드 직후 `~할 말이 있다 합니다 / 들어보시겠습니까?` 계열의 일본어 혼입 팝업이 새로 나타났고, 여러 화면의 텍스트 위치가 한꺼번에 어긋났다. 따라서 v132를 현재 권위/새 빌드 베이스로 사용하지 않는다.

## 2026-09-02 — #120 후속: dual-use message table 전수 감사 (새 게임 revision 없음)

- v132 Citra 회귀를 계기로 `msgsec06` first-75를 다시 Original과 1:1 분석했다. 핵심은 이 영역이 단순 parent WORD table도, 단순 direct BYTE table도 아니라 **한 16-bit 값이 두 lookup 경로에 동시 재사용되는 dual-use 구조**라는 점이다. Original `word[1..74]`을 BYTE offset으로 다시 분류하면 **72 fragment-start + 2 inside-fragment**에 74/74 모두 정확히 매핑된다. 동시에 같은 값은 WORD offset으로 parent[1..74] 시작을 가리킨다.
- #120의 대표 증거: Original `word[74]=0x07D3`. parent-route에서는 `0x07D3*2=0x0FA6`으로 parent74 `スカ? / 都市ﾉ規模ｶﾞ...`를 가리키고, direct-route에서는 byte `0x07D3`이 **`その君主とは / 同盟関係がありません`**를 가리킨다. 즉 v133 초안처럼 `word[74]`만 `0x0B19`로 바꾸면 도시 규모는 살릴 수 있어도 외교 direct 문구를 깨뜨릴 수 있으므로 **단일 2-byte fix도 폐기**했다.
- formal v53 번역 repack은 parent start가 이동하면서 이 숫자 하나의 dual-use 불변식을 깨뜨렸다. v54의 `repair_msg06()`는 direct-route를 살리려 first75를 absolute BYTE pointer처럼 바꿨고 **75개 중 74개를 변경**했다. 그 결과 parent-route는 구조적으로 틀어졌다. 이후 first75 중 **12개(6,15,21,28,39,40,48,53,56,65,69,71)**는 v55/v81/v101/v120에서 실제 화면 수정용 EOF direct redirect로 다시 변경됐다. 이것이 v132에서 old parent table을 일괄 복구했을 때 여러 정상 화면까지 즉시 회귀한 이유다.
- `msgsec06` 75 parent를 일본판 Original ↔ formal v53 한국어 ↔ v131 table로 전수 감사했다. 결과는 **정상 parent word 1 / v54 잘못된 값 잔존 62 / 후속 direct redirect 12**. current word를 parent WORD offset으로 해석하면 **14개는 OOB**, **60개는 파일 안의 전혀 다른 문장**을 가리킨다. 다만 모든 74개가 반드시 현재 플레이에서 즉시 노출된다고 단정하지 않는다. 각 엔트리가 parent-route로 호출되는지 direct-route로만 호출되는지는 runtime caller 증거/Citra로 구분한다.
- 같은 dual-use 구조는 Original의 다른 Message bank에도 존재한다. `msgsec04`: shared 23/23 = start20+inside3, `msgsec07`: 176개 중 start161+inside14+특수1, `msgsec08`: shared59/59 = start58+inside1, `msgsec09`: shared40/40 = start36+inside4. formal v53↔v131 비교에서도 `msgsec04 parent word 23개 변경/OOB3`, `msgsec06 74/OOB14`, `msgsec08 59/OOB30`을 확인했다. `msgsec07`은 v53 단계부터 all-byte 모델을 사용했기 때문에 v53 자체가 clean parent authority가 아니며 Original 기반 재구성이 필요하다.
- **새 수정 방향:** shared first-prefix word 자체를 더 이상 직접 고치지 않는다. currently-working direct-route pointer는 보존하고, **parent-route lookup을 별도 shadow parent table 또는 공통 parent getter 분기로 분리**하는 것을 우선한다. 공통 getter를 찾지 못하면 일본판/Citra로 실제 parent caller가 확인된 이벤트만 caller 단위로 우회한다. 텍스트 전체 재배치나 old prefix 일괄 복구는 금지한다.
- 메시지 로더는 `Message/msgsec%02u.dat` 22개를 연속 버퍼에 적재하고 section별 시작 오프셋을 별도 테이블에 보관하는 구조까지 확인했다. 따라서 공통 `(section,parent)` lookup 함수를 찾아 shadow table로 분리할 가능성이 있다. 이 low-level 추적은 다음 단계에서 계속한다.
- 권위 감사 자료: `analysis/v133_msgsec06_parent_route_audit.json`, `analysis/v133_dual_use_message_format_audit.json`, `tools/audit_sangokushi2_v133_msgsec06_parent_routes.py`. 이 감사 결과를 바탕으로 아래 v133 dual-mirror 최소 복구를 만들었다.

## 2026-09-02 — v133-intermediate #120 dual-mirror 최소 구조 복구

- v132의 broad first-75 복구는 사용하지 않는다. `analysis/v132_issue120_msgsec06_mixed_header_baseline/PatchSnapshot/RomFS/Message/msgsec06.dat`의 sealed v131 파일(SHA-256 `07D472269B436FCDB519E7CD86F3CE3E95A6711B2D4862E15EA7391FBDCFBEF2`)을 다시 권위 source로 사용하고, v132에서 정상적으로 되돌린 `msgsec09`는 그대로 보존한다.
- low-level 추적에서 direct 메시지 getter `code.bin 0x1BF0F4`를 확인했다. 메시지 ID를 `/1000`으로 section/index로 나눈 뒤 section base의 첫 word를 count로 읽고 `word[index+1]`을 **BYTE offset 그대로** 더한다. 따라서 `msgsec06 word[74]`는 direct index73의 pointer이면서 parent74의 WORD offset을 동시에 담당하는 shared 값이라는 dual-use 해석이 실제 런타임 코드와 일치한다.
- #120용 shared pair의 현재 권위는 direct source `0x0A94..0x0AB7 = 그 군주와는 / 동맹 관계가 아닙니다`, parent source `0x1632..0x165C = 까？ + 도시 규모가 / %s 규모가 되었습니다!`다. `0x1528=0x0A94*2`는 실제로 다른 활성 조건 prompt fragment 중간이므로 in-place overwrite는 금지한다.
- **dual-mirror 방식:** fresh shared value `V=0x1B00`을 사용한다. `word[74]=0x1B00`; byte `0x1B00`에는 기존 direct fragment+separator `0x0A94..0x0AB7`을 byte-exact mirror하고, byte `0x3600(=2*0x1B00)`에는 parent74 두 fragment+separator `0x1632..0x165C`를 byte-exact mirror한다. old v131 body byte322~EOF는 byte-exact이고 기존 header 161 words 중 diff는 **word[74] 하나뿐**이다.
- 이 방식은 direct-route와 parent-route를 모두 보존한다. 독립 runtime simulation은 direct index73→`0x1B00`→`그 군주와는 / 동맹 관계가 아닙니다`, parent74→`0x3600`→`까？`→`도시 규모가 / %s 규모가 되었습니다!`를 확인했다. v132처럼 다른 first-prefix entry는 건드리지 않는다.
- v133에서 v132 대비 실제 변경 게임 파일은 정확히 **`RomFS/Message/msgsec06.dat` 하나**다. #117~#119 `code.bin/font/msg07`, v132의 pre-v131/v130 `msgsec09`, 사용자 `ExeFS/icon.bin`, 그 밖의 Patch는 v132 manifest와 byte-exact다. msgsec06 크기는 `6834→13866` bytes이며 append gap은 `0x05`로 채운다. append-only Message 확장은 기존 프로젝트에서 이미 사용된 패턴이고 section별 base offset은 loader가 파일 크기로 동적 계산한다.
- 최종 SHA-256: `msgsec06=6B0FA61A74E81FCE3F4D025E3D3A6487B031ADEF34B8A3C16A2F6FDFB0FEB20F`. builder 실행→독립 verifier PASS 후 동일 완성본에서 builder를 다시 실행해 SHA가 완전히 동일했고 두 번째 verifier도 PASS했다.
- 권위 자료: `analysis/v133_issue120_dual_mirror_targets.json`, `analysis/v133_issue120_dual_mirror_report.json`, `tools/build_sangokushi2_v133_issue120_dual_mirror.py`, `tools/verify_sangokushi2_v133_issue120_dual_mirror.py`, 구조 감사 `analysis/v133_msgsec06_parent_route_audit.json`, `analysis/v133_dual_use_message_format_audit.json`.
- **v133 Citra 결과: 실패.** 사용자 최종 빌드에서 #120 도시 규모 상승 시 정상 도시 발전 문구 대신 여전히 깨진 일본어/한자 혼합 문자열이 출력됐다. 따라서 `word[74]` dual-mirror가 #120의 실제 owner라는 판정은 폐기한다. v133은 현재 기준본이 아니다.

## 2026-09-02 — v134-intermediate #120 실제 마지막 direct pointer 복구

- 사용자 Citra 실패 화면과 일본판 동일 시점 `都市の規模が / 中規模になりました！`를 다시 권위로 삼아 `code.bin`의 실제 message getter부터 재추적했다. `VA 0x001BF0F4` getter는 message ID를 `/1000`으로 section/index로 나눈 뒤 section base의 `word0`을 count로 읽고, **`u16(section_base + 2 + index*2)`를 BYTE offset 그대로** section base에 더한다. 즉 `word0=161`은 "헤더 word 수"가 아니라 뒤에 오는 direct entry의 수다.
- 이 코드 의미를 immutable Original `msgsec06.dat`에 대입하면 실제 direct table은 **`word[1]..word[161]` = 161개**다. 기존 extractor/repacker는 `word[0]..word[160]`까지만 헤더로 잘라 **마지막 direct pointer를 헤더에서 누락**했다. Original의 `word[161]=0x0FAD`는 정확히 `都市ﾉ規模ｶﾞ\n%s規模ﾆﾅﾘﾏｼﾀ!` 시작을 가리킨다. 따라서 #120은 parent74가 아니라 **section6 direct index160, message ID 6160**이다.
- v131/v133 `msgsec06`의 byte `0x142..0x143`은 잘못된 header 길이 때문에 번역 body 시작으로 사용돼 `word[161]=0x8D0F`가 되었다. v131 파일 크기 6834B보다 큰 OOB pointer이며, Citra가 여기서 무관한 메모리/깨진 글자를 읽은 것이 제보 화면과 일치한다.
- **최소 수정:** v133의 speculative dual-mirror/padding을 상속하지 않고 sealed v131 `msgsec06`를 source로 사용했다. direct index0~159에 해당하는 `word[1..160]`과 v131 기존 body는 byte-exact로 유지하고, 구조적으로 헤더인 byte `0x142..0x143`의 **`word[161]` 한 곳만** EOF standalone target `0x1AB2`로 변경했다. target 바이트는 v131 `0x1638..0x1659`에 이미 존재하는 한국어 **`도시 규모가\n%s 규모가 되었습니다!`**를 그대로 복제했다. `word[74]`는 v131 정상 direct 값 `0x0A94`로 복원되어 별도 mirror 없이 기존 외교 경로를 유지한다.
- **인접/전체 direct 감사:** v131 direct index0~159는 **160/160 전부 파일 내부**였고, #120 인접 index145~159도 모두 유효하다. v134에서는 새 index160까지 합쳐 **161/161 전부 파일 내부**다. 따라서 `msgsec06` 안에서 이번에 확인된 동일 원인의 OOB pointer는 마지막 index160 한 곳뿐이며 주변 포인터는 수정하지 않았다.
- 공통 getter의 같은 마지막-entry 규칙을 다른 Message bank에도 읽기 전용으로 적용했다. 대부분은 마지막 entry가 정상 in-file이지만 **`msgsec07 word[388]=0xEA88`(file 21007B), `msgsec21 word[67]=0x8C0E`(file 4816B)** 두 곳은 Original의 마지막 pointer가 정상인데 현재 값이 OOB인 동일 형태의 **잠복 후보**로 확인됐다. Original 마지막 target은 각각 `長期戦中ﾉ都市ﾍﾊ移動ﾃﾞｷﾏｾﾝ`, 3줄짜리 `ｻﾗﾆﾊｺﾉ混乱ﾆ乗ｼﾞﾃ...` 사건문이다. 다만 실제 Citra caller/노출이 아직 증명되지 않았으므로 **v134에서는 수정하지 않는다.** `msgsec09`는 후속 revision에서 word0 자체가 다른 용도로 변경된 이력이 있어 이 단순 count 감사로 일반화하지 않는다. 기록=`analysis/v134_last_direct_entry_crossbank_audit.json`.
- 변경 게임 파일은 `RomFS/Message/msgsec06.dat` 하나뿐이다. v134 msgsec06 크기 `6834→6871B`, SHA-256=`FDE90AF5E7ED0F10756B71909768BA91221C039AAB391AE2A717FBECF51288D1`. `code.bin`, font, msg07, msg09, `ExeFS/icon.bin` 및 나머지 Patch는 v133과 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- builder와 독립 verifier를 완성본 위에서 재실행해 두 번 모두 PASS했고 동일 SHA를 확인했다. verifier는 Original `word[161]=0x0FAD`, getter machine-code region Original exact, v131 direct0~159 byte-exact, v134 direct0~160 161/161 in-file, 인접145~160 유효성을 독립 재검증한다.
- 권위 자료: `analysis/v134_issue120_last_direct_pointer_targets.json`, `analysis/v134_issue120_last_direct_pointer_report.json`, `tools/build_sangokushi2_v134_issue120_last_direct_pointer.py`, `tools/verify_sangokushi2_v134_issue120_last_direct_pointer.py`.
- **v134 Citra 결과: 포인터 복구 성공.** 사용자 동일 세이브에서 깨진 문자열 대신 `도시 규모가 / 中 규모가 되었습니다!`가 정상 위치에 출력됐다. 따라서 message ID 6160의 마지막 direct pointer 복구는 runtime 성공으로 확정한다. 남은 `中`은 `%s` 인자 자체의 별도 일본어 소스 문제다.

## 2026-09-02 — v135-intermediate #120 규모명 인자 大/中/小 한글화

- v134 실화면에서 `%s` 자리에 `中`이 그대로 나왔고 사용자 추가 제보로 `大`도 같은 방식으로 노출되는 것을 확인했다. #120 formatter/포인터는 더 수정하지 않고 `%s`를 공급하는 실제 인자 selector를 추적했다.
- `code.bin VA 0x0015B2C0`는 입력 enum이 0~2이면 `VA 0x002DC444`의 3-entry pointer table에서 문자열 포인터를 반환한다. table은 enum0=`0x002CFFE4`→`大`, enum1=`0x002CFCDC`→`中`, enum2=`0x002CFE50`→`小`를 가리킨다. #120 caller `VA 0x00248CF8`가 도시 규모 값을 이 selector에 넘기고 반환 문자열을 message ID 6160의 `%s` 인자로 사용한다.
- **최소 수정:** standalone 2B 문자열 세 곳만 same-width 교체했다: `大 91E5→대 894C`, `中 9286→중 8A49`, `小 8FAC→소 8949`. 각 뒤의 `00 00`, selector instruction, pointer table 3개, 다른 code 영역은 유지한다. 기존 폰트 map의 검증된 alias를 재사용하므로 font 변경은 없다.
- v134 `msgsec06.dat`는 byte-exact 유지되어 SHA-256=`FDE90AF5E7ED0F10756B71909768BA91221C039AAB391AE2A717FBECF51288D1`. v134 대비 변경 게임 파일은 `ExeFS/code.bin` 하나뿐이며 새 SHA-256=`91ADE4D3A12D7DB8A0AAC140073560612DF232A7BA4A44D46430B017C4CF3959`다.
- builder 실행 후 독립 verifier PASS, 다시 builder→verifier를 실행해 동일 SHA와 동일 3-entry pointer table을 확인했다. 기대 화면은 `도시 규모가 / 소 규모가 되었습니다!`, `... 중 규모 ...`, `... 대 규모 ...`다.
- 잠복 OOB 후보 `msgsec07 index387`과 `msgsec21 index66`은 이번 v135에서도 **수정하지 않았다.** 실제 caller/Citra 노출이 확인되지 않았기 때문이다. 사용자 식별용 원문은 각각 `長期戦中ノ都市ヘハ移動デキマセン`(장기전 중인 도시로는 이동할 수 없습니다), `サラニハコノ混乱ニ乗ジテ / 黄巾ノ残党モ再興ノ兆シヲ見セ / 新皇帝ノ治世ハ波乱ノ幕開ケトナッタ`(혼란을 틈탄 황건 잔당 재흥 및 새 황제 치세 개막 서술)다.
- 권위 자료: `analysis/v135_issue120_scale_labels_targets.json`, `analysis/v135_issue120_scale_labels_report.json`, `tools/build_sangokushi2_v135_issue120_scale_labels.py`, `tools/verify_sangokushi2_v135_issue120_scale_labels.py`.
- **직전 Patch 권위는 v135-intermediate**다. #120의 포인터 오류 자체는 v134 Citra에서 해결 확인됐고, v135는 규모명 3종의 runtime 표시 확인만 남았다.

## 2026-09-02 — v136-intermediate #118/#121/#122 runtime 재수정 및 위임/외교 잔여 한글화

- **베이스:** exact v135 Patch를 `analysis/v136_issue118_121_122_baseline/PatchSnapshot`으로 봉인한 뒤 작업했다. `Original / Rebuild / Backup`은 수정하지 않았다.
- **#118 v131 실패 원인:** v131은 `메뚜기 떼`와 `발생하였습니다.`를 `code.bin 0x1F3F00 / 0x1F3F20`의 NUL tail slack으로 relocation하고 각각 `0x1DC428 / 0x1E5ECC` pointer를 연결했다. 정적 readback은 문자열/포인터가 모두 맞았지만 사용자 Citra에서는 첫 popup이 `메뚜기 떼가`에서 끊기고 결과 화면의 이벤트명도 사라졌다. ARM caller를 다시 추적한 결과 `0x15E408` formatter가 `0x1E5ECC`를 실제 `%s` 인자로 읽으므로 pointer 위치 자체는 맞았고, **tail slack 문자열이 runtime에서 유효하게 읽히지 않는 방식**이 실패 원인으로 좁혀졌다.
- **#118 최신 구현:** tail relocation을 전부 폐기했다. 기존 runtime string pool `0x1CFE5C..0x1CFE6B`의 `いなご / なし` 두 슬롯을 한 16B span으로 사용해 `메뚜기 떼\0없음\0`으로 재배치하고 event pointer `0x1DC428`은 `0x002CFE5C`, sibling `なし` pointer `0x1DC58C`은 `0x002CFE66`으로 맞췄다. 기존 `発生しました`가 있던 `0x1D3004..0x1D3013` 16B에는 **`발생하였습니다.`**를 직접 넣고 `0x1E5ECC`는 `0x002D3004`로 복귀시켰다. v131 tail `0x1F3F00/0x1F3F20`은 전부 0으로 되돌렸다. formatter `0x15E524=메뚜기 떼가 %s`는 유지한다. 기대 화면은 **`메뚜기 떼가 발생하였습니다.` / `메뚜기 떼`**다.
- **#121 외교 실제 source:** 제보 화면의 `対象`은 기존에 번역된 다른 duplicate가 아니라 `code.bin 0x0B4654`가 실제 source였고, 같은 UI block의 `条件@0x0B465C`와 나란히 있다. `0x0B4654`를 **`대상`**으로 exact-size 치환했다. 외교 label pointer table `0x1E50C8..0x1E50E0`을 전수 역읽기한 결과 현재 메뉴의 유일한 잔존 일본어 label은 `停戦@0x1CEE4C`이어서 **`정전`**으로 교체했다. 최종 label은 `동맹 / 공동작전 / 정전 / 교환 / 원조 / 항복권고 / 동맹파기`다.
- #121 상단 `%s님, 어떻게할까요？`는 원 allocation 21B 때문에 공백을 넣고 전각 `？`까지 유지하면 1B 초과한다. 따라서 같은 21B 안에서 **`%s님, 어떻게 할까요?`**로 수정해 사용자 요청의 띄어쓰기를 반영했다. reviewed diplomacy pointer range `0x1E501C..0x1E51AC`는 현재 target들을 제외한 일본어 잔여 0건이다.
- **#122 위임 code UI(v136 구현 및 후속 오류):** 실제 pointer table `0x1E48BC/0x1E48C0/0x1E48C4`는 `全委任 / 全直轄 / 都市単位`를 가리켰다. `전체위임/전체직할`은 원 6B 슬롯에 8B 한국어가 들어가지 않아 v136에서 `0x1D188E..0x1D189F`에 각각 NUL 포함 9B씩 배치했으나, 후속 Citra에서 **`0x1D188E`가 바로 앞 live string `튜토리얼@0x1D1886..0x1D188D`의 종료 NUL이었다는 사실이 확인됐다.** 따라서 v136 relocation 위치 판정은 실패다. `都市単位→도시단위`, `軍事型→군사형`, `生産型→생산형`, `適応型→적응형`, `直轄→직할`, `위임을 해제합니까?` 및 stale fallback `위임/직할` 자체는 유지한다.
- **#122 상단 설명:** 실제 `msgsec10.dat`의 fixed physical span 6개가 v135에서 Original 일본어 그대로였음을 확인했다. header/separator/file size를 움직이지 않고 ASCII space padding으로 `msgsec10_0025_001=군비를 강화해 영지를 늘립니다.`, `_002=군량 수입과 상업을 발전시킵니다.`, `_003=상황에 맞게 대응합니다`, `_004=모든 속령을 같은 정책으로 위임`, `msgsec10_0026_000=군사·장군 태수의 위임을 모두 해제합니다.`, `_001=도시별 정책을 설정합니다.`로 fixed-position 치환했다. `msgsec10` size는 6884B 그대로이고 reviewed span 밖 diff는 0이다. `msgsec15`의 기존 위임 상세 도움말은 이미 한국어라 v135 byte-exact로 보존했다.
- **변경 범위:** v135 대비 게임 파일 diff는 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 두 개다. `font.g1t`, `msgsec06/07/15/21.dat`, `fix_data.bin`과 그 밖의 Patch는 v135 byte-exact다.
- **최종 SHA-256:** `code.bin=444BDDEFB84ECA64C38359F86BB12FD183448B49E0FE0CCD28CE9966E5D7BA02`, `msgsec10.dat=344532CCF45FBE6FD944F6D1A1DCC2AF8655654A8CFE09E26C42E43D4509B50C`.
- 빌더 1차 PASS 후 독립 verifier PASS, 완성본에서 다시 builder→verifier를 실행해 **동일 SHA와 동일 readback으로 2회 결정적 검증 PASS**했다. verifier는 #118 live pointer가 모두 proven pool/fixed span으로 복귀했고 tail slack이 0인지, #121 외교 label/prompt와 reviewed pointer range 일본어 잔여 0, #122 action/policy/direct label·fixed msgsec10 6개·보존 파일을 독립 검사한다.
- 권위 자료: `analysis/v136_issue118_121_122_targets.json`, `analysis/v136_issue118_121_122_report.json`, `tools/build_sangokushi2_v136_issue118_121_122.py`, `tools/verify_sangokushi2_v136_issue118_121_122.py`.
- **v136 Citra 결과:** 사용자가 #121 외교 텍스트 개선과 #122 위임 화면 한글화는 정상 확인했다. 다만 메인 메뉴의 `튜토리얼` 버튼이 `튜토리얼전체위임`으로 출력되는 회귀가 발견됐고, 이는 위 relocation이 `튜토리얼` 종료 NUL을 덮은 직접 결과다. #118 메뚜기 이벤트는 아직 별도 runtime 확인 기록이 없다. 따라서 v136은 현행 기준본이 아니며 아래 v137 hotfix가 대체한다.

## 2026-09-03 — v137-intermediate 튜토리얼 종료 NUL hotfix

- **베이스:** 사용자 Citra에서 #121 외교와 #122 위임 화면의 내용 자체가 정상 확인된 exact v136을 `analysis/v137_tutorial_terminator_hotfix_baseline/PatchSnapshot`으로 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다.
- **원인 확정:** v135 baseline에서 `튜토리얼`은 `code.bin 0x1D1886..0x1D188D`의 8B 한글 문자열이고 바로 다음 `0x1D188E`가 C-string 종료 NUL이다. v136은 `전체위임`을 정확히 `0x1D188E`부터 써서 이 NUL을 덮었고, 따라서 런타임이 `튜토리얼전체위임`을 한 문자열로 읽었다. 이 문제는 #122 번역 내용이 아니라 relocation 경계 선택 오류다.
- **새 relocation:** 문자열 pool 내부의 `0x1CF143`부터 28B 연속 NUL run을 확인했다. 첫 NUL `0x1CF143`은 앞 live C-string terminator일 수 있으므로 guard로 보존하고, payload는 **`0x1CF144..0x1CF155` 18B**만 사용한다. `전체위임`은 `0x1CF144`, `전체직할`은 `0x1CF14D`; 각 8B 뒤 NUL 1B를 둔다. 뒤 `0x1CF156`도 guard NUL로 보존한다. 이 18B payload에는 v136 기준 absolute pointer ref 0, ARM `ADR` ref 0을 확인했다.
- **pointer 수정:** `0x1AB3D4/0x1E48BC`는 `0x002CF144`, `0x1AB3D8/0x1E48C0`은 `0x002CF14D`로 redirect했다. v136의 잘못된 `0x1D188E..0x1D189F` 18B는 전부 0으로 복원해 `튜토리얼` 종료 NUL을 되살렸다.
- **보존 범위:** v136에서 Citra 정상 확인된 #121/#122의 나머지 code 변경과 `RomFS/Message/msgsec10.dat`는 byte-exact다. v136 대비 변경 게임 파일은 **`ExeFS/code.bin` 하나뿐**이다.
- **검증:** final `code.bin SHA-256=271C4D0E847B470DD2589E6DDA4758DE81AE8EB845182002ABD88D1CAF439667`. builder→독립 verifier→builder→verifier 2회 동일 SHA/PASS. verifier는 exact v136+hotfix composition, `튜토리얼` readback과 `0x1D188E` 종료 NUL, 새 relocation 양쪽 guard NUL, 4개 pointer target, v136 `msgsec10` 및 비대상 파일 보존을 독립 확인한다.
- 권위 자료: `analysis/v137_tutorial_terminator_hotfix_targets.json`, `analysis/v137_tutorial_terminator_hotfix_report.json`, `tools/build_sangokushi2_v137_tutorial_terminator_hotfix.py`, `tools/verify_sangokushi2_v137_tutorial_terminator_hotfix.py`.
- **직전 Patch 권위는 v137-intermediate**다. Citra에서 `튜토리얼` 단독 표기와 `전체위임/전체직할`이 계속 정상인지 재확인하면 이 hotfix를 runtime 성공으로 승격할 수 있다.

## 2026-09-03 — v138-intermediate #123/#124/#125/#126 + 동일 오류 class 감사

- **베이스/범위:** exact v137 Patch를 `analysis/v138_issue123_126_baseline/PatchSnapshot`으로 봉인했다. 로컬 `Github_Issue/Issue123~126.html`과 첨부 PNG를 확인하고 immutable Original/current Patch를 대조했다. `Original / Rebuild / Backup`은 수정하지 않았다. 최종 변경 게임 파일은 정확히 **6개**: `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec11.dat`, `msgsec21.dat`다.
- **#123 다중 군주 선택:** `msgsec11_0009_001` Original raw 시작은 **`02 28`**인데 v62 계열에서 `28`이 빠져 `02`만 남으면서 Citra에 `(0う)` 같은 깨진 조각이 혼입됐다. `msgsec11 0x4C7` fixed 23B span에서 **`<$02>(번째 군주를 선택`**으로 복원했다. `02 28`은 동적 숫자 토큰으로 보존되므로 화면은 `1번째 / 2번째 / ...` 형식으로 조립되는 것이 목표다. `msgsec11` header/file size 1560B는 유지한다.
- **#124 항복권고 + 띄어쓰기 동일 class:** `code.bin 0x17F350/0x17F35C`의 `이제는 / 이제` 뒤 spare NUL을 1B ASCII space로 사용해 `이제는 [군주명]...`, `이제 [군주명]...` 조립을 복구했다. 호위 장수 확인 `0x181264`는 **`%s(으)로 할까요？`**로 교체했다. 같은 공백 누락 class를 current runtime 전체에서 감사해 byte allocation, line width, interior pointer가 모두 안전한 것만 추가 수정했다: `0x157738=%s 태수 %s이(가) / 공동작전을 거절함`, `0x159DE8=%s의 %s이(가) / 모반했습니다!`, `0x1801E4=%s 부군사 %s / 의견이 있다고 합니다`, `msgsec04 0x5F9/0x99D`의 동적 이름 앞 공백 2건. 반대로 `msgsec06 0x1A58..`, `msgsec07 0x1D66..`처럼 다른 pointer 해석이 span 내부를 가리키거나, 동적 이름 2개 때문에 최대 줄폭을 확정할 수 없는 후보는 **의도적으로 수정하지 않았다.**
- **#124 `치욕` 렌더:** 제보 화면의 `욕=98A5`가 Citra에서 깨지는 far-alias 실패로 판단했다. 신규 donor를 추가하지 않고 같은 길이의 자연스러운 **`수치`**로 실제 live 사용 3곳만 same-width 교체했다: `code.bin 0x180995`, `msgsec06 0xC1C`, `msgsec07 0x1D2C`. 정적 readback에서 세 위치 모두 `수치=88E3 8DF9`이고 기존 `치욕=8DF9 98A5`는 제거됐다.
- **#125 숨김 시나리오 설명:** `천하는 더?`의 `?`는 실제 물음표가 아니라 **`욱=989C`**가 해당 renderer에서 깨진 결과였다. `msgsec11 0x3F4..0x443` fixed 80B span을 **`동탁타도에 모인 연합은 / 뜻을 이루지 못하고 해산했다. / 천하는 더 혼란해진다...`**로 바꿨다. 기존 fragment boundary `0x42C`가 한글 2바이트 중간을 자르지 않는 것을 별도 gate했고, 해당 span의 `989C`는 0건이다.
- **#126 첫 화면 — 시나리오 대사 잘림:** v110/v137 `msgsec21` visible-line 폭을 제어 토큰 제외 기준으로 재감사했다. 사용자 화면에서 **19.0 units** 문장의 마지막 `다`가 잘린 근거를 바탕으로 `>18.5`를 수정 gate로 잡았고 실제 초과는 2행이었다. `msgsec21_0034_000`은 **`거병하면서 / 사태는 군웅할거 양상으로 변했다`**(최대 15.5), `msgsec21_0028_001` 마지막 줄은 **`영웅들이 패권을 놓고 싸운 난세였다`**(17.0)로 줄였다. 기존 18.5-unit 행은 유지했다. 두 fixed span 내부에는 direct/word 해석 pointer가 없음을 확인했다.
- **#126 두 번째 화면 — 마지막 direct entry 구조 오류:** v134에서 잠복 후보로 기록했지만 caller가 미확정이라 보류했던 `msgsec21 word[67]=0x8C0E`가 이번 제보 화면의 실제 실패와 일치했다. 이 u16은 이미 body 첫 바이트와 겹쳐 값을 덮으면 기존 문장을 손상시키므로 **현재 0x8C0E 값을 그대로 보존하고 파일을 그 주소까지 0 padding한 뒤** `게다가 이 혼란을 이용해 / 황건 잔당도 재기의 조짐을 보였고 / 새 황제의 치세는 파란으로 시작됐다`를 append했다. `msgsec21`은 4816B→35949B이며 pointer 0x8C0E가 이제 파일 내부 target이다.
- **#126 동일 구조 전수:** v134 cross-bank 감사에서 남은 유일한 다른 동일 class는 `msgsec07 word[388]=0xEA88`이었다. 사용자 요청에 따라 같은 append-only 방식으로 21007B→60082B까지 확장하고 **`장기전 중인 도시로는 이동할 수 없습니다`**를 0xEA88에 배치했다. 기존 prefix와 겹치는 u16은 byte-exact 보존했다. v138 완성본에서 Original-valid last-entry가 current OOB인 mechanical candidate는 **0건**이다.
- **검증:** 첫 builder PASS 후 독립 verifier가 exact v137+v138 composition, #123 `02 28`, #124 6 code target/2 msg04 spacing/3 `수치`, #125 `989C=0`, #126 `>18.5` 잔여 0 및 두 last direct pointer in-file을 재검증했다. 이어 완성본에서 builder→verifier를 다시 실행해 동일 결과/PASS를 확인했다. v103 범용 dialogue audit은 v138에서 `active 7 / mixed 11`을 출력했으나 sealed v137 baseline에도 **정확히 동일한 7/11**이므로 v138 신규 회귀는 아니다.
- **최종 SHA-256:** `code.bin=17885F886943972D2F9E3FAC5625E315B10AC0BFBC857787D6CE75C65E9C36AA`, `msgsec04=4A54E6EEAEB34935C155E651283587FC1961BB5894161C15682EE040BDFAD003`, `msgsec06=B159DC7A815E14641EB58DEEE334C7A75B184CC04D6A97789318CD5472CE0615`, `msgsec07=4A897D3A5BB06106B779F5685097658B36C8E7659C9FB0EABA76D5ED77AF4314`, `msgsec11=CA86107E6E8876EADC47534BE3D2F7C6BB3A4523AF90FD1D874C7B47C6BDF8D9`, `msgsec21=6F7B2132E07DEE58E9BFD2FD4C877E465AEF69ABADB4D000B7A29416FAB3BDE9`.
- **권위 자료:** `analysis/v138_issue123_126_targets.json`, `analysis/v138_issue123_126_report.json`, sealed baseline `analysis/v138_issue123_126_baseline/PatchSnapshot`, `tools/build_sangokushi2_v138_issue123_126.py`, `tools/verify_sangokushi2_v138_issue123_126.py`.
- **직전 Patch 권위는 v138-intermediate**다. 정적/결정적 검증은 완료됐으며 #123~#126 및 v137 튜토리얼 hotfix의 Citra 실화면 재확인은 pending이다.

## 2026-09-03 — v139-intermediate 지정 PNG 19개 이미지 전용 리빌드

- **베이스/규칙:** 작업 시작 전 `tools/verify_sangokushi2_v138_issue123_126.py`를 재실행해 현재 Patch가 exact v138-intermediate임을 확인했다. v138 전체 Patch **81파일**을 `analysis/v139_image_update_baseline/PatchSnapshot`으로 봉인했다. 사용자가 명시한 PNG 19개만 사용했고 GPT/AI 이미지 생성은 사용하지 않았다. Original/Rebuild/Backup은 수정하지 않았다.
- **대상 19개:** `Extracted_Image/RomFS/Common/BGM/ChangeBGM/ChangeBGM_000.png` 1개, `Extracted_Image/RomFS/StartMenu/option_down/option_down_017/019/021/023/025/027/029/031.png` 8개, `Extracted_Image/RomFS/Common/BGM/BGMDown/BGMDown_002~011.png` 10개다. 전부 RGBA이며 대응 G1T texture는 type `0x09 RGBA8`; `ChangeBGM_000`은 256×32, 나머지는 128×32로 source texture와 정확히 일치했다.
- **최신 Patch base 우선:** `RomFS/StartMenu/option_down.g1t`는 v138 Patch에 이미 존재하므로 sealed v138 SHA-256 `B974ED2602BA3A94D5A46B28977A828346CEF193E51B753AD6BEFAB2BEE9D256`을 base로 사용했다. `RomFS/Common/BGM/ChangeBGM.g1t`와 `BGMDown.g1t`는 v138 Patch에 존재하지 않아 이번 최초 편입에 한해서만 immutable Original을 base로 사용했다. Original base SHA는 각각 `072B64EA442B97865E16B7645061F1333F0F6360A0133922234AA0BFE009F779`, `6250F89BA29AAA99CAD22C27D7DE26847D33716CB38037DE91DAAFBA37508D6C`다.
- **1:1 payload 교체:** 각 source RGBA8 payload를 decode→encode하여 byte-exact identity를 먼저 증명하고, 명시된 index payload만 사용자 PNG로 교체했다. 각 G1T header와 요청하지 않은 texture payload는 authoritative base와 byte-exact다. 빌드 후 Patch G1T를 다시 decode해 **19/19 PNG pixel-exact readback**을 확인했다.
- **변경 범위:** v138 대비 변경/추가 게임 파일은 정확히 **3개**다: `RomFS/Common/BGM/ChangeBGM.g1t`, `RomFS/StartMenu/option_down.g1t`, `RomFS/Common/BGM/BGMDown.g1t`. 그 밖의 v138 80개 기존 파일은 byte-exact이며 `code.bin`, `font.g1t`, `msgsec04/06/07/11/21.dat`의 v138 SHA도 그대로다. BGM 두 G1T가 새로 추가되어 Patch 파일 수는 **81→83**이다.
- **최종 SHA-256:** `ChangeBGM.g1t=CDB093D6A8E109E0AC29BA5FE71388BD8CF758DE5E77C425691B67B38F274A38`, `option_down.g1t=B7C56B0EDE8F4E8C630B6029A02091537CD3B5156E91386AD8B988298482E676`, `BGMDown.g1t=F5016473EAE2BC69DFAB89314F63178AB4608C6CF4D385DE670FBBC00F377ED6`. `analysis/v139_image_update_report.json` SHA-256은 `A84374B0B4B8B5A82ADC724F9913DD30CD1D9F8B3F20B5157C5503A117B06FDD`다.
- **검증:** builder 1차 PASS → 독립 verifier PASS → 완성 v139 위 builder 2차 → verifier 2차가 모두 PASS했다. 두 번의 빌드에서 위 3개 G1T와 report SHA까지 완전히 동일했다. verifier는 exact v138+3 G1T 조성, 19/19 pixel-exact, header/비대상 payload byte-exact, 비대상 v138 manifest byte-exact, Original first-inclusion hash 불변을 독립 확인했다.
- **재발 방지:** 이번 revision 이후 `ChangeBGM.g1t`와 `BGMDown.g1t`는 Patch에 존재한다. 따라서 다음 BGM 이미지 갱신 시에는 Original을 다시 base로 사용하지 말고 **최신 Patch의 v139 이후 컨테이너**에서 명시된 payload만 교체해야 한다.
- **권위 자료:** `analysis/v139_image_update_targets.json`, `analysis/v139_image_update_report.json`, sealed baseline `analysis/v139_image_update_baseline/PatchSnapshot`, `tools/build_sangokushi2_v139_image_update.py`, `tools/verify_sangokushi2_v139_image_update.py`.
- **현재 Patch 권위는 v139-intermediate**다. 정적/결정적 이미지 검증은 완료됐으며 지정 19개 이미지와 v138 누적 텍스트의 Citra 실화면 확인은 pending이다.

## 2026-09-03 — v140-intermediate #128/#129/#130/#132 + 동일 오류 class 전수 감사

- **베이스/범위:** exact v139 Patch 83파일을 `analysis/v140_issue128_130_132_baseline/PatchSnapshot`에 봉인한 뒤 로컬 `Github_Issue/Issue128/129/130/132.html`과 첨부 PNG를 대조했다. `Original / Rebuild / Backup`은 수정하지 않았다. 최종 변경 게임 파일은 정확히 **5개**: `ExeFS/code.bin`, `RomFS/Message/msgsec05.dat`, `msgsec06.dat`, `msgsec08.dat`, `RomFS/Scenario/fix_data.bin`이다. `font.g1t`와 v139 이미지 3컨테이너를 포함한 나머지 78파일은 byte-exact다.
- **#128 항복 수락 대사:** `code.bin 0x180990` Original `この上は / 生きて恥は曝すまいぞ`를 다시 대조해 기존 `이제 수치을 / 안고 살지 않겠다`를 **`이제 수모를 / 참으며 살지 않겠다`**로 교체했다. `욕=98A5`를 다시 쓰지 않고 자연스러운 의미를 유지한다. 같은 화면의 `조조는마등님이...`는 `0x17DF50=는` 뒤의 spare NUL 때문에 다음 동적 이름 문자열과 붙던 구조였으며 **`는 `**으로 1B ASCII space를 추가했다. 같은 standalone 조사+다음 C-string class를 전수 감사해 `0x1D2EF0/0x159948/0x15A20C`도 검토했으나 runtime 조립/폭 안전성이 확정되지 않아 그대로 보존했다.
- **#129 사자 포박/혼입 한자:** 제보 화면의 `네焉들이`, `傷...`, `무決...` 등은 일본어 원문 조각이 아니라 **Citra에서 잘못 렌더되는 far alias `놈=9951 / 베=9952 / 슨=9972`**가 원인이었다. 제보 지점만 고치지 않고 hardcoded 대사 + active `msgsec06/08` 전체에서 세 alias의 live 사용을 전수 추적했다. `code.bin` 8개 문자열을 자연스럽게 재작성했고(`너희들이 / 나를 죽일건가`, `저들 따위는 없애 / 보여드리겠습니다`, `손책, 동오의 덕왕인...`, `그자를 붙잡아 죽여라!`, `이, 이게 대체!` 등), `msgsec06` 6개 fixed span과 `msgsec08`의 `베어→잘라` 2곳을 함께 수정했다. ARM/data의 우연 raw hit는 건드리지 않으며 최종 **활성 대사 내 9951/9952/9972 hit=0**이다.
- **#129 이중 쉼표:** `code.bin 0x1806C4` suffix가 `%s, 아쉽지만`인데 앞 formatter가 이미 `님, `으로 끝나 `손견님, , 아쉽지만`이 조립됐다. suffix를 **`%s아쉽지만 / 놓쳤습니다`**로 바꿔 두 번째 쉼표를 제거했다.
- **#130 아이템명:** `릉성검`이라는 번역 항목은 존재하지 않았고, 67종 표시는 원래 **`칠성검`**이었다. `칠=98F3`의 렌더 실패 때문에 다른 글자로 보인 것으로 확정했다. 67개 표시명 필드를 전수 감사해 기존 인명/폰트에서 검증된 low-lead alias를 재사용할 수 있는 `둔=95F5, 칠=9787, 룡=92DE, 옥=97B7, 새=9773, 극=94D9, 첨=95CB, 씨=9555, 홍=8BA3`을 해당 item display-name 토큰에만 재인코딩했다. 총 **15/67 레코드**가 변경됐고, `칠성검`은 `칠=9787`로 정상화됐다. 아이템 독음 17B와 표시명 이외 `fix_data.bin`은 byte-exact다. `낭=9944`는 이번 Citra 실패 증거가 없어 high-lead라는 이유만으로 임의 이동하지 않았다.
- **#132 관전 종료 팝업:** Citra 제보 source는 `msgsec05.dat 0x22E..0x244`의 23B `０人プレイを終了しますか？` 물리 span으로 확정했다. **`관전종료하시겠습니까？`**(22B)로 fixed-position 교체하고 1B space padding했다. 기존 6B fragment 경계는 완전한 한글 경계이며 `msgsec05` header와 앞/뒤 `05 05 05` separator는 byte-exact다.
- **미번역 팝업 전수 감사:** 단순 CP932 디코딩은 한국어 alias를 일본어로 오인해 대량 오탐을 만들 수 있으므로, 번역 DB의 **원본 일본어 question/confirmation raw가 현재 section<20 system/menu 파일에 실제로 남아 있고, 현재 direct pointer가 그 raw 시작 또는 제어 prefix 내부를 실제로 가리키는 경우**만 활성 후보로 승격했다. 물리 raw 후보는 71개, direct-pointer 소유권까지 확인된 **추가 활성 미번역 후보는 34개**다: `msgsec00=1, msgsec01=9, msgsec02=20, msgsec05=3, msgsec17=1`. #132 원 `msgsec05_0009_002/msgsec05_0010`은 교체 후 활성 후보에서 사라졌다. 이 34건은 대부분 한국어 번역이 원 span보다 길고 일부는 `<$05>`, `<$1B>`, 동적 토큰/fragment 분할을 포함하므로, 과거 `msgsec06` 회귀를 반복하지 않기 위해 **이번 revision에서 bulk relocation하지 않고 구조별 후속 수정 대상으로 기록**했다. hardcoded dialogue/popup bank의 일본어 residue는 0건이다.
- **검증:** builder 1차 PASS → 독립 `tools/verify_sangokushi2_v140_issue128_130_132.py` PASS → 완성본 위 builder 2차 → verifier 2차가 모두 PASS했다. 두 번의 출력 SHA가 완전히 동일하다. verifier는 exact v139+5파일 조성, code 10 target byte-exact 재구성, msg06 6 fixed span, msg08 2 same-width replacement, #132 header/separator/fragment boundary, item 67 display field 독립 재구성 및 15 changed-record/독음 byte-exact, failed alias live hit 0, hardcoded 일본어 popup 0, physical popup 71 / active 34 후보 ID set을 독립 확인한다.
- **최종 SHA-256:** `code.bin=327AC0B4D98E2B63C675272EDF660697C50C86B78AF9801139A842BBEB79E040`, `msgsec05=28795EE83C42F9B240B0488881D09E4BCDF31C69A7B87E17BEE9272D2F199CFD`, `msgsec06=F0CCBFDC9493229BDBD8D739F1EB60EF5E8A8F619089C9F5FD7115CD6BCA7A14`, `msgsec08=C2D1BB876EDA2838518040FA27EA82E45CA324E041C1E2DF5AC174376E5ED717`, `fix_data.bin=46385E08468561CB002EBBD3EBDA2FA5FD3710E63272BEEC3BBC6C641392664C`. report SHA-256=`27F21EFF24B7FF2145159CB16186BA75CA646FA6AAC246B7CD89734D2BDD44E9`.
- **권위 자료:** `analysis/v140_issue128_130_132_targets.json`, `analysis/v140_issue128_130_132_report.json`, sealed baseline `analysis/v140_issue128_130_132_baseline/PatchSnapshot`, `tools/build_sangokushi2_v140_issue128_130_132.py`, `tools/verify_sangokushi2_v140_issue128_130_132.py`.
- **현재 Patch 권위는 v140-intermediate**다. 정적/구조/결정적 검증은 완료됐으며 #128/#129/#130/#132의 새 화면은 Citra 실화면 확인 전까지 최종 승인으로 표현하지 않는다. 추가 활성 미번역 팝업 34건은 다음 구조별 팝업 revision의 우선 작업 목록이다.

## 2026-09-03 — v141-intermediate direct-owned 미번역 팝업 34건 전수 정리

- **베이스/목적:** v140 popup audit에서 번역 DB의 일본어 질문/확인 raw가 현재 파일에 남아 있고 **current direct pointer가 실제로 그 raw 시작 또는 보존 가능한 control-prefix 내부를 가리키는 34건**을 후속 처리했다. exact v140 전체 Patch를 `analysis/v141_live_popup_sweep_baseline/PatchSnapshot`에 봉인했고 `Original / Rebuild / Backup`은 수정하지 않았다.
- **처리 원칙:** 과거 `msgsec06`처럼 pointer/header를 재배치하지 않는다. 34건 모두 기존 원문 raw span 안에 들어가는 짧은 한국어를 새로 정해 **in-place fixed-span**으로 교체했다. 남는 바이트는 ASCII space로 padding했고 파일 크기와 direct pointer table은 byte-exact다. source raw 자체가 trailing `05` separator를 포함하는 경우 padding을 separator 앞에 넣어 `05`의 물리 offset을 그대로 유지했다. `msgsec01_0037_000`, `msgsec02_0033_000`처럼 pointer가 source start+2를 가리키는 항목은 앞 `05 05`를 그대로 보존했다. `msgsec05_0003_001`은 **동적 이름 토큰 `02 01 C8` 3바이트를 원자 단위로 보존**하고 suffix만 ` 병력 몇 명?`으로 바꿨다.
- **수정 범위:** v140 대비 변경 파일은 정확히 **5개**다: `msgsec00.dat` 1건, `msgsec01.dat` 9건, `msgsec02.dat` 20건, `msgsec05.dat` 3건, `msgsec17.dat` 1건. 예시는 `편집 내용을 초기화할까요?`, `편집 저장할까요?` 계열, `응전할까?`, `공격할까?`, `방향? 기동력:<$02>(`, `어디로 돌격?`, `소화할까?`, `%lu명 주민복귀?`, `%s로 할까요?` 등이다. v140 #132 `관전종료하시겠습니까？` 23B span은 v141에서 byte-exact 보존한다.
- **완료 gate:** 번역 DB의 section<20 system/menu 질문·확인 raw를 다시 전수 감사해, 현재 direct pointer가 원 일본어 raw를 소유하는 **활성 미번역 후보를 0건**으로 확인했다. stale/duplicate 원문 raw는 파일 안에 남을 수 있으나 direct ownership이 없으면 이번 runtime popup gate의 실패로 보지 않는다.
- **검증:** builder 1차 PASS → 독립 `tools/verify_sangokushi2_v141_live_popup_sweep.py` PASS → 완성본 위 builder 2차 → verifier 2차 모두 PASS. 독립 verifier는 sealed v140에서 34 span을 다시 찾고 current pointer ownership을 재확인한 뒤 각 compact target을 별도 재인코딩해 5개 파일 전체를 byte-exact 재구성한다. v140 비대상 파일 전부 byte-exact, v140 #132 span byte-exact, 파일 크기/header/pointer table 불변, trailing `05` 물리 위치 보존, 최종 active untranslated popup candidate=0을 재검증한다.
- **최종 SHA-256:** `msgsec00=6FA599DF13D18678E97B59E211A90F46FD6875B2E2A2DDB4B0A661AB84C1FF4D`, `msgsec01=EE3C0321FAE122B8AED3B42B8BDE07A365077C34424EE993BAFD1534788584E2`, `msgsec02=CCEF9B8DEB61F12E818B76623BFA9EE9D335DDF6E9C169C4B5CD3A5D158A69D3`, `msgsec05=BD1F682D11EC698181995A6A41D099EEB67A0D3589CE0B52F183847609FF1AE1`, `msgsec17=9AEDEDA74541EDD0435EBBDD15A7CBDAE10AFF2A5D0F247FC5133C425FEF0B34`. report SHA-256=`C1258C4EE96A58E3ECB2B3F638C4A2323DBD9F7652CEB6EE6E7A362D3D43873C`.
- **권위 자료:** `analysis/v141_live_popup_sweep_targets.json`, `analysis/v141_live_popup_sweep_report.json`, sealed baseline `analysis/v141_live_popup_sweep_baseline/PatchSnapshot`, `tools/build_sangokushi2_v141_live_popup_sweep.py`, `tools/verify_sangokushi2_v141_live_popup_sweep.py`.
- **현재 Patch 권위는 v141-intermediate**다. v140의 #128/#129/#130/#132 수정과 v139 이미지 변경을 모두 계승하며, 정적/구조/결정적 검증은 완료됐다. 새/추가 팝업 문구는 Citra 실화면 확인 전까지 최종 승인으로 표현하지 않는다.

## 2026-09-03 — v142-intermediate #135 항복/사자 팝업/동적 공백 + `98B3` 동일 오류 class 전수 정리

- **베이스/범위:** exact `v141-intermediate` 전체 Patch를 `analysis/v142_issue135_baseline/PatchSnapshot`으로 봉인하고 로컬 `Github_Issue/Issue135.html`과 첨부 4장 PNG를 대조했다. OCR/원문/runtime owner를 함께 재추적했고 `Original / Rebuild / Backup`은 수정하지 않았다. v141 대비 변경 게임 파일은 정확히 **3개**: `ExeFS/code.bin`, `RomFS/Message/msgsec06.dat`, `RomFS/Message/msgsec08.dat`다. `font.g1t`, Scenario, 이미지, 그 밖의 모든 Patch 파일은 v141 byte-exact다.
- **#135-1 `않겠다』/ュ` 끝 혼입:** 제보 화면은 `이제 수모를 / 참으며 살지 않겠다』`로 OCR됐다. Original `この上は / 生きて恥は曝すまいぞ`를 다시 확인했고, `code.bin 0x180990..0x1809AF`의 visible 문구는 **`이제 수모를\n참으며 살지 않겠다`**로 유지하되 마지막 spare byte를 `space + NUL` guard로 사용해 다음 glyph 잔상을 차단했다. 같은 원문이 별도 runtime copy로 존재하는 `msgsec06 0xC0E..0xC2F`도 동일 visible 문구로 맞추고 남는 4B를 space padding했다. active `않겠다` 인코딩을 `code/msg06/msg21`에서 전수 감사한 결과 7개 occurrence를 기록했고, 제보 지점 외에 실제 일본어 tail이 붙은 동일 종료 class는 추가 발견되지 않았다.
- **#135-2 사자 무장 선택 팝업:** 실제 direct-owned 고정 span은 `msgsec06 0x1594..0x15A3`의 **16B**다. 사용자 제안 바이트는 `사자 무장은 선택할 수 없습니다=30B`, `사자 무장은 불가능 합니다.=26B`, `사자로 선택된 무장입니다.=25B`, `사자 무장 입니다=16B`로 대조됐다. relocation/pointer 이동 없이 들어가는 안은 4안뿐이므로 문법만 다듬은 정확히 16B **`사자 무장입니다.`**를 적용했다. 뒤 `05 05 05` separator는 원 위치 그대로다.
- **#135-3 항복 거절 대사:** 첨부 화면은 화자 `장훈`, 본문 `…들 따위는 없… / 보여드리겠습니다`로 OCR됐고 실제 owner는 `code.bin 0x1D0E58` 및 `msgsec06 0x1459` 복제본이다. immutable Original은 **`奴らなど討ち取って / ご覧にいれます`**이므로 의미를 정확히 살린 **`저들을 토벌해\n보여드리겠습니다`**로 수정했다. 깨짐의 직접 원인은 `없애`의 `애=98B3` far-alias 실패였다.
- **`98B3` 동일 오류 class 전수:** v141 active text에서 `98B3`은 `code.bin=2`, `msgsec06=1`, `msgsec08=5`, 총 **8회**였고 v142에서는 세 파일 모두 **0회**다. 같은 alias를 쓰던 다른 live 문장도 함께 보정했다. `code.bin 0x1670D4`는 Original `を討ち / 朕の苦しみを除いてくれ`에 맞춰 **`를 토벌해\n짐의 고통을 끝내라`**, `msgsec08 0xDA6`은 **`아쉽구나, 아쉬워\n와룡은 하늘로 오를 때를 얻지 못했도다`**, 동적 역적명 3 copy는 `짐의 고통을 끝내라`로 통일했다. 신규 font donor는 만들지 않았다.
- **#135-4 `조조는마등님이...` 띄어쓰기:** v140에서 `0x17DF50`의 `は→는 `에 ASCII 1B 공백을 넣은 source 판정 자체는 맞았고 Original ARM `ADR@0x17DE0C -> 0x17DF50`, `ADR@0x17DE50 -> 0x17DF54`가 실제 호출 owner임을 다시 확인했다. 다만 반각 공백 폭이 Citra에서 사실상 붙어 보이므로 두 ADR instruction은 byte-exact 보존한 채 두 번째 C-string 시작에 stock **전각 공백 `81 40`**을 넣어 `조조는　마등님이...`처럼 시각적으로 확실히 분리되게 했다. `msgsec06 0x147D`, `0x1A85`의 동일 항복 조립 copy도 동적 NAME token을 그대로 보존하면서 같은 전각 공백으로 맞췄다.
- **구조/독립 검증:** `tools/verify_sangokushi2_v142_issue135.py`를 독립 실행해 변경 파일 집합 3개, `code/msg06/msg08` reviewed span 밖 diff=0, `msgsec06/msg08` file size/header 불변, 사자 팝업 16B, 두 Original ADR instruction/target 불변, `98B3=0`, 동적 NAME token 및 항복 spacing copy를 재검증해 PASS했다. 이어 builder에 **read-only `--check` 모드**를 추가해 sealed v141에서 v142 세 파일을 메모리상 재계산하고 현재 Patch와 byte-for-byte 비교했으며 `deterministic_match=true`; 그 뒤 verifier를 다시 실행해 PASS했다. 이 방식은 완성 Patch를 다시 쓰지 않고 결정성을 확인한다.
- **최종 SHA-256:** `code.bin=F99BD7F2EFF51EEA86D2CFDA901AC14D495F8005CDF57FB1427B46C0045B8660`, `msgsec06=419DD006446BBAD4E637CA225A72A252D3572427C1EAE7D00749EC945E0CE62C`, `msgsec08=56B70B0B2279E7A15843762E9D617168999487F0850A6A09E8588FBC730A358E`. report SHA-256=`7BB65D2E0247C51732381BE12B4839186E4566A22AEF61C69F0C3E4FBB959349`.
- **권위 자료:** `analysis/v142_issue135_targets.json`, `analysis/v142_issue135_report.json`, sealed baseline `analysis/v142_issue135_baseline/PatchSnapshot`, `tools/build_sangokushi2_v142_issue135.py`, `tools/verify_sangokushi2_v142_issue135.py`.
- **현재 Patch 권위는 v142-intermediate**다. 정적/구조/결정적 검증은 완료됐으며 #135의 네 화면은 **Citra 실화면 재확인 pending**이다. 실제 화면 확인 전에는 런타임 최종 승인으로 표현하지 않는다.

## 2026-09-03 — v143-intermediate #134 항복 대사 `똑=9953` 한자형 렌더 실패 및 동일 alias class 정리

- **베이스/화면 확인:** exact `v142-intermediate` Patch를 `analysis/v143_issue134_baseline/PatchSnapshot`으로 봉인하고 로컬 `Github_Issue/Issue134.html` 및 첨부 PNG `92b82eb8-d90c-4f84-b5a1-c586395c0231.png`를 확인했다. 스크린샷 OCR은 `귀공의 본성 ... 알았습니다` 계열로 잡혔고, 현재 v142 항복 응답 **`귀공의 본성 / 똑똑히 알았습니다`**와 정확히 대응했다. immutable Original `code.bin 0x1D0FD0`은 **`貴殿の本性 / しかと承知しました`**이며 의미는 “귀공의 본성을 확실히 알았습니다”다.
- **원인/수정:** 혼입 한자는 실제 일본어 잔여가 아니라 `똑=9953` far-alias가 Citra에서 stock 한자형 글리프로 표시되는 런타임 실패였다. v142 live text에서 `9953`은 정확히 `code.bin` 2회 + `msgsec06.dat` 2회이며 모두 같은 문장의 두 `똑`이다. 실제 runtime copy는 `code.bin 0x1D0FD0..0x1D0FEC`와 `msgsec06 direct header[115] -> 0x107F..0x109B` 두 곳이다. 두 copy 모두 원 29B와 정확히 같은 29B **`귀공의 본성\n확실히 알았습니다`**로 fixed-span 교체했다. `확=9355 / 실=8DF3 / 히=8F6E` 기존 low-risk alias만 사용하며 신규 font donor는 없다.
- **구조 보존:** `code.bin 0x1E517C` literal pointer는 Original/v142/v143 모두 `0x002D0FD0`으로 byte-exact다. `msgsec06`은 161-word flat header 전체, header[115]=`0x107F`, 파일 크기, 뒤 `0x109C`의 `05 05 05` separator를 전부 보존했다. v142 대비 게임 파일 변경은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec06.dat` 2개뿐**이며 각 reviewed 29B span 밖 diff=0이다. `font.g1t`, `msgsec08`, Scenario, 이미지 등 나머지 Patch는 v142 byte-exact다.
- **동일 사례 전수/재발 방지:** `tools/audit_sangokushi2_v103_dialogue.py`의 Citra-proven failed alias 목록에 후속 이슈에서 확정된 `놈=9951`, `베=9952`, `슨=9972`, `애=98B3`, `똑=9953`을 추가했다. 또한 과거 안전 local alias `풍=9559`, `곁=96A0`을 decoder에 반영해 `漂/蔓` 정적 false positive를 제거했다. v143 최종 audit는 **`code_count=0 / code_failed_alias=0 / active_message_count=0 / active_message_failed_alias=0 / active_message_japanese_or_cjk=0`**이다. `message_mixed_count=1`은 `msgsec06 0x142`의 기존 control/meta fragment 한 건뿐이며 direct header가 가리키지 않고 v142 대비 byte-exact라 runtime 오류로 승격하지 않는다.
- **번역 권위 동기화:** `Extracted_Text/sangokushi2_translation.json`의 `msgsec06_0051_002.translation_ko`와 현재 `Extracted_Text/text_review.html`을 **`귀공의 본성<$0A>확실히 알았습니다`**로 갱신했다. `text_review - 백업.html`은 백업 자료이므로 수정하지 않았다.
- **검증:** 작업 직전 `verify_sangokushi2_v142_issue135.py`로 exact v142를 재확인했다. v143 builder 1차 PASS 후 독립 `tools/verify_sangokushi2_v143_issue134.py`가 변경 파일 집합, 두 29B runtime copy, Original source, literal/direct pointer, header/separator, span 밖 diff=0, `9953=0`, 최신 failed-alias 전체 live audit를 검증해 PASS했다. builder `--check`가 sealed v142에서 두 출력을 메모리상 재계산해 현재 Patch와 **`deterministic_match=true`**를 확인했고 verifier 재실행도 PASS했다.
- **최종 SHA-256:** `code.bin=BF9A914363E188C12C1C5B2CE5027C17C4AFD395C31AFA621C8BC6799F75F99C`, `msgsec06=B47C3E67199FB90AD32F98548214BC87CDB41AE6B5D99947901D4CB9F25B2096`. report SHA-256=`1EF2354390955824B1DD4778D0F9B4EA645CD6D86218DB9254B247E9D2B32B41`.
- **권위 자료:** `analysis/v143_issue134_targets.json`, `analysis/v143_issue134_report.json`, sealed baseline `analysis/v143_issue134_baseline/PatchSnapshot`, `tools/build_sangokushi2_v143_issue134.py`, `tools/verify_sangokushi2_v143_issue134.py`, 최신 `tools/audit_sangokushi2_v103_dialogue.py`.
- **현재 Patch 권위는 v143-intermediate**다. 정적/구조/결정적 검증은 완료됐으며 #134 수정 화면은 **Citra 실화면 재확인 pending**이다. 실제 화면 확인 전에는 런타임 최종 승인으로 표현하지 않는다.

## 2026-09-03 — v144-intermediate #135 Citra 재제보 3건 실제 runtime 구조 수정

- **베이스/재현:** 사용자가 v143 적용 후 Citra Nightly 2104 실화면 3장을 다시 제공했다. exact `v143-intermediate` Patch를 `analysis/v144_issue135_citra_runtime_repair_baseline/PatchSnapshot`으로 봉인했고 `Original / Rebuild / Backup`은 수정하지 않았다. v143 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec06.dat` 2개**뿐이다.
- **사자 무장 팝업 — v142 source 판정 수정:** Citra는 계속 `사자 무장`만 표시했다. 전체 Patch 검색 결과 동일 한국어 source는 `msgsec06 0x1594` 외에 **`code.bin 0x181254`**에도 있었고, immutable Original의 실제 C-string은 정확히 **`使者武将です`**였다. ARM `ADR@0x181160 -> 0x181254`가 실제 Citra 호출 owner다. 과거 v55가 이 code source를 `사자 무장`으로 축약했고 v142는 Message duplicate만 고쳐 화면에 반영되지 않았다. v144는 16B allocation `0x181254..0x181263`에 **`사자 무장입니다` 15B + NUL 1B**를 정확히 넣고 다음 C-string `0x181264`는 byte-exact 보존했다. `msgsec06 0x1594..0x15A3`도 `사자 무장입니다` + trailing space로 동기화하고 뒤 `05 05 05` separator를 보존했다.
- **`않겠다` 뒤 가나 — 고정 memcpy 길이 초과가 원인:** 사용자 확대 화면에서 `않겠다` 뒤에 별도 가나형 글리프가 계속 보였지만 v143의 code/msg raw에는 일본어 tail이 없었다. 실제 caller를 ARM 추적한 결과 `code.bin 0x1808EC = mov r2,#0x1E`, `0x1808F0 = ADR r1,0x180990`, `0x1808F4 = B 0x201184`이며 `0x201184`는 **r2 바이트를 정확히 복사하는 memcpy**다. Original은 visible 29B + NUL = 30B였으나 v143 `이제 수모를 / 참으며 살지 않겠다`는 visible만 **30B**라 NUL이 31번째로 밀려 고정 복사에서 빠졌다. v144는 원 의미를 유지하면서 **`이제 수모를\n참고 살지 않겠다.`**로 바꿔 visible 29B + NUL 1B = 정확히 30B로 맞췄고, 물리 NUL 위치도 Original과 같은 `0x1809AD`로 복원했다. `msgsec06 0xC0E..0xC2F` duplicate도 같은 문구로 동기화했다. 번역 권위 `msgsec06_0037_002.translation_ko`와 현재 `text_review.html`도 동일 문구로 갱신했다.
- **`<NAME1>는<NAME2>님이` 띄어쓰기 — v140/v142 가설 폐기:** 실제 caller를 디스어셈블한 결과 `0x17DE0C`는 `ADR` 후 **`LDRH`로 `0x17DF50`의 조사 한 글자만** 읽고, `0x17DE20`에서 SP에 2B 저장한 뒤 `0x17DE24`가 byte NUL을 붙여 임시 C-string을 만든다. 따라서 v140처럼 `0x17DF50` 뒤에 ASCII space를 써도 caller는 그 space를 읽지 않으며, v142가 `0x17DF54` suffix 앞에 전각 공백을 넣은 것은 실제 조립상 `<NAME2>　님이`가 되어 위치가 틀렸다. v144는 `0x17DE10: mov r1,#0 -> mov r1,#0x20`, `0x17DE24: strb r1,[sp,#2] -> strh r1,[sp,#2]`로 바꿔 SP를 **`는 + 0x20 + NUL`**로 직접 만든다. 직후 `0x1FE7E0` getter는 r0만 건드리고 r1을 보존하는 3-instruction body임을 gate로 증명했다. `0x17DF54`의 잘못된 전각 공백은 제거해 suffix를 다시 `님이 두려워 / 항복했습니다`로 시작시켰다. 결과 조립 목표는 **`<NAME1>는 <NAME2>님이 두려워\n항복했습니다`**다. `msgsec06 0x147D`, `0x1A85` duplicate도 fullwidth space를 제거하고 NAME2 앞 **ASCII 1B space**로 통일했으며 NAME1/NAME2 token은 각각 1회 byte-exact 보존한다.
- **검증:** builder 1차 PASS 후 독립 `tools/verify_sangokushi2_v144_issue135_citra_runtime_repair.py`가 reviewed span 밖 diff=0, messenger 실제 ADR/source, 30B memcpy의 마지막 바이트 NUL, memcpy branch target `0x201184`, SP 조립 명령 2개, particle/suffix ADR, dynamic NAME token, msgsec06 161-word header/file size/separator, 번역 DB 동기화를 재검증해 PASS했다. 이어 builder `--check`가 sealed v143에서 두 출력 파일을 메모리상 재계산해 **`deterministic_match=true`**, verifier 재실행도 PASS했다. 최신 dialogue audit는 `code_count=0 / code_failed_alias=0 / active_message_count=0 / active_message_failed_alias=0 / active_message_japanese_or_cjk=0`; `message_mixed_count=1`은 기존 `msgsec06 0x142` 비직접 control/meta fragment로 v143와 byte-exact다.
- **최종 SHA-256:** `code.bin=A95C76CD584C8D41A7F393D70E662AE08DD3C123CE8FB781D48AC6C747C6BF22`, `msgsec06=000C675BD42EAA8FDE9314CC7DBFA70D4D2BD83CE2932ED7EBF4A5AAED98C63C`. report SHA-256=`766CE8BEA583A87BCB5D11086A8E2BA17F31397FEE0CFBFC6EF8624E3B80215A`.
- **권위 자료:** `analysis/v144_issue135_citra_runtime_repair_targets.json`, `analysis/v144_issue135_citra_runtime_repair_report.json`, sealed baseline `analysis/v144_issue135_citra_runtime_repair_baseline/PatchSnapshot`, `tools/build_sangokushi2_v144_issue135_citra_runtime_repair.py`, `tools/verify_sangokushi2_v144_issue135_citra_runtime_repair.py`.
- **현재 Patch 권위는 v144-intermediate**다. 세 화면은 실제 Citra 재제보를 근거로 runtime source/caller 구조를 다시 잡아 수정했으며 정적/구조/결정적 검증은 완료됐다. **v144 실화면 재확인은 pending**이다.

## 2026-09-03 — v145-intermediate #140 무장 열전 798건 전수 한글화

- **베이스/범위:** exact `v144-intermediate` 전체 Patch를 `analysis/v145_issue140_officer_biographies_baseline/PatchSnapshot`에 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다. #140의 실제 runtime 열전 bank를 재조사한 결과 `msgsec13.dat=336명`, `msgsec19.dat=462명`으로 **총 798명**이며, v144에서는 두 파일 모두 Original과 byte-exact라 열전 번역이 런타임에 전혀 적용되지 않은 상태였다. 이번 revision의 변경 게임 파일은 정확히 **`RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec13.dat`, `msgsec19.dat` 3개**뿐이다. #137 아이템 설명은 사용자 요청대로 이번 revision에서 **전혀 수정하지 않았다.**
- **3DS 추가 무장 확인:** `fix_data.bin` record 0~769는 기존 프로젝트에서 NDS 한국어판 770명 이름표와 1:1 정렬된 것이 검증돼 있다. 3DS 열전은 record 0~797까지 존재하므로 **3DS 추가 무장은 28명(record 770~797)**이다: `금환삼결/두기/기람/대원/견초/고당륭/최림/장소/아하소과/곽석/주조/장거/장순/해니/토안/유주/비잔/월길/아단/철리길/주삭/왕진/위풍/공경/교현/황승언/장절/두원`. 공통 770명은 NDS 한국어판 열전을 의미/용어 참고 권위로 사용하되, 3DS 화면 규격과 3DS 원문 차이를 반영했다. 추가 28명은 실제 3DS 일본어 열전을 기준으로 별도 검수했다.
- **fragment 경계 복구/번역 방식:** 기존 `sangokushi2_translation.json`의 `msgsec13/msgsec19` 번역값은 비어 있지 않았지만 Shift-JIS 2바이트 문자가 extractor fragment 경계에서 잘린 사례가 다수 있어 `<$8F>B`, `<$97>...` 같은 깨진 조각을 개별 재패킹하면 안 된다. #140은 **무장 1명 = 완전한 열전 1개** 단위로 798개를 다시 구성하고 `05 05 05` 구분자를 새로 배치했다. 일본어 kana/CJK 잔여 0, unsupported Hangul 0을 release gate로 고정했다.
- **레이아웃:** Original 열전 실측 규격을 **한 줄 19.5 cells 이하 / 최대 4줄**로 잡았다. NDS 공식 문장을 그대로 복사하면 긴 항목이 있어, 관직·전투·사망 원인·정사/연의 차이 같은 핵심 의미는 유지하면서 중복 수식어만 자연스럽게 압축했다. 최종 **798/798 모두 4줄 이내, 각 줄 19.5 cells 이하**다.
- **`msgsec13` 구조:** `word0=337`, 뒤 336개 absolute BYTE pointer가 336개 열전을 가리킨다. 첫 pointer만 첫 열전 시작+2, 나머지 335개는 각 열전 시작과 정확히 일치한다. 새 본문 뒤에는 Original의 최종 sentinel **`05 05 05` 3B**를 byte-exact 보존했다. 파일 크기는 35,858B→44,996B, 최종 SHA-256=`156ECFC4932EC68D5DB5D36823225AEE67700EA68A940E05580EB829624AAFE0`.
- **`msgsec19` 구조:** `word0=505`. 첫 463개 `05 05 05` block 중 block0 `5ト`는 기술 데이터라 byte-exact 보존하고 block1~462만 열전으로 교체했다. 뒤 **126B technical tail**은 byte-exact 보존(SHA-256=`DFFD0CDCAD431952C6264EAA99C6062CF5FE2D165920C82B84B6D1E21EE84D35`)했고, header[464..504]의 41개 tail pointer는 새 tail 시작이 12,222B 이동한 만큼 absolute 값만 이동시켜 **tail 내부 상대 offset 41/41을 그대로 유지**했다. 파일 크기는 50,232B→62,454B, 최종 SHA-256=`4F5F589691213FD5116D48FE600083CC876B29AA8FC125057F723CD0FAE18EAC`.
- **폰트:** 자연스러운 최종문에 필요한 글자를 줄이기 위해 의미 손실 없는 표현 교정을 먼저 했지만, 흔한 `해` 등을 억지로 제거하지 않고 사용자 승인 규칙대로 안전 donor를 사용했다. 신규 low-lead donor는 **`해=8B6C/physical1066(stock 詰)`, `던=8F5B/1801(stock 充)`, `맥=909C/2053(stock 頗)`, `머=93AD/2634(stock 働)`, `코=9772/3328(stock 羊)`** 5자다. 모두 historical alias=0, target bank 밖 runtime ref=0, current Korean owner=0, Original-valid JIS, 아래 셀 top-edge=0을 통과했다. `8F5B/909C/93AD/9772`는 상하좌우 edge까지 0이며 `8B6C`은 수직 edge 0, 왼쪽 이웃 right-edge 1px만 기록했다. 기존 packed font에서 이미 Sam3 픽셀이 정확히 존재하던 **`론=97E9/3446`, `륜=97F5/3458`, `흉=97FA/3463`**은 셀을 수정하지 않고 같은 글자로 재사용했다. v144 대비 실제 font diff cell은 정확히 `[1066,1801,2053,2634,3328]` 5개이고 target 밖 cell diff=0, 8개 alias 모두 packed readback pixel-exact다. 최종 `font.g1t` SHA-256=`E80BCB95B887C37ABC7B3CDC5B796BFCA7792C1716FF1792D8FE6F4DA344C275`.
- **검증:** `tools/build_sangokushi2_v145_issue140_officer_biographies.py --check`가 sealed v144에서 세 출력을 메모리상 재계산해 현재 Patch와 byte-for-byte 동일(`deterministic_match=true`)임을 확인했다. 독립 `tools/verify_sangokushi2_v145_issue140_officer_biographies.py`는 exact v144+3파일 조성, 798/798 raw 본문 readback, 19.5×4 layout, msgsec13 final sentinel, msgsec19 block0/tail/41 pointer 상대 위치, font 5-cell diff/8 glyph pixel-exact, manifest를 재검증해 PASS했다. v105 범용 dialogue audit은 current v145와 sealed v144에서 **완전히 동일**하게 `layout_violation=1 / dynamic_suffix_candidate=1 / bad_alias_code=0 / bad_alias_message=0 / v103_message_mixed=14 / active=10`을 출력해 #140 신규 회귀가 아님을 확인했다.
- **최종 해시/권위 자료:** `text_sha256=7F5E0C34D4BE15C1C2FF20E841D0A6D1E95B6B8F4A6221467D2C1E70BE50CAB5`, encoded biography aggregate=`4775AE69A7CCCF985F2A85D1CDEFAD51AB9277D806BFF10C5C2C4CDB1A604004`, targets SHA-256=`5618C4DF54FBB0B9FF6CB071495F9913809DF2085BE7119BA6CAADDA1E5F80AE`, report SHA-256=`6E004B60F0A4AC2D27BBEEFACA2961A7F3C6D4AF5A28054A7ECD335D122E4DDE`. 권위 자료는 `analysis/v145_issue140_officer_biographies_targets.json`, `analysis/v145_issue140_officer_biographies_report.json`, sealed baseline `analysis/v145_issue140_officer_biographies_baseline/PatchSnapshot`, 위 builder/verifier다.
- **현재 Patch 권위는 v145-intermediate**다. #140 정적/구조/결정적 검증은 완료됐다. 실제 무장 정보 화면의 줄바꿈·신규 donor 5자 렌더를 포함한 **Citra 실화면 확인은 pending**이며, 실화면 확인 전에는 runtime 최종 승인으로 표현하지 않는다. #137 아이템 설명은 다음 사용자 요청에서 별도 revision으로 진행한다.

## 2026-09-03 — v146-intermediate #140 Citra 무장 열전 한자 혼입/alias 전수 복구

- **베이스/재현:** 사용자가 v145 적용 후 Citra Nightly 2104의 무장 열전 화면에서 `성문을 ?고`, `극새`, `?후`, `방?`, `조?의 참모`, `양위시?다` 등 한자/이상 글리프 혼입을 실화면으로 제보했다. exact `v145-intermediate` 전체 Patch를 `analysis/v146_issue140_citra_alias_repair_baseline/PatchSnapshot`으로 봉인하고 v145 builder/verifier를 다시 실행해 기준 SHA가 일치함을 확인한 뒤 작업했다. `Original / Rebuild / Backup`은 수정하지 않았고, v145 대비 변경 게임 파일은 정확히 **`RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec13.dat`, `msgsec19.dat` 3개**뿐이다. #137 아이템 설명은 이번 revision에서도 수정하지 않았다.
- **원인/전수 감사:** v145 열전 798건을 실제 인코딩 기준으로 전수 감사한 결과 `lead>=0x98` alias를 사용하는 한글이 **42종, 총 450회** 있었고, 이번 Citra 화면에서 `덕=97A1`도 같은 열전 renderer에서 실패가 확인됐다. 제보 7건은 각각 `열=98CC`, `옥=98F5`, `열=98CC`, `덕=97A1`, `휴=989E`, `켰=996B`의 렌더 실패와 문장 `마초를 한중으로 몰아 넣는다`의 어색한 번역이 결합된 사례였다. 이 실패는 다른 화면 전체에 대한 전역 alias 금지로 확대하지 않고 **무장 열전 screen-class 전용 실패 규칙**으로 제한했다.
- **제보 문구 수정:** release gate를 `성문을 열고`, `마초를 한중으로 내몰았다`, `옥새를 담보로`, `열후에 봉해졌다`, `방덕`, `조휴의 참모로서`, `양위시켰다`로 고정했다. 특히 #6은 스크린샷상 `조?`가 조예처럼 보였지만 실제 원문/열전은 **조휴**이며 `휴` glyph 실패였다. `몰아 넣는다→내몰았다` 외에도 `궁중에서 축출되어났다→궁중에서 축출되었다` 등 위험 음절을 피하면서 의미를 유지할 수 있는 기존 어색한 문장까지 함께 정리해 **40개 열전 row**가 문장상 변경됐다.
- **위험 alias 제거 정책:** `깊/났/넣/돕/딸/떠/옛/욕/찰` 9자는 자연스러운 문장 재작성으로 열전에서 제거했다. 기존 packed font에 동일 한글의 정상 픽셀이 이미 존재하는 **23자**는 font를 수정하지 않고 안전 low-lead alias로 재인코딩했다: `홍=8BA3, 곡=8BDE, 횡=8CD9, 억=8DAF, 굉=8F9E, 애=9078, 빙=91BF, 즐=91F8, 뇌=92B8, 엽=939C, 냉=93FB, 릉=8BD3, 극=94D9, 씨=9555, 첨=95CB, 궐=95F4, 둔=95F5, 륭=9645, 굴=9679, 촌=96A5, 흑=9777, 칠=9787, 옥=97B7`. 신규 donor는 **11자** `욱=89BC/769, 온=89F8/829, 낭=8F83/1840, 휴=937E/2588, 덕=9399/2614, 켰=94DA/2867, 롱=95EF/3076, 베=964A/3100, 름=966C/3134, 폐=969D/3182, 열=96A8/3193`다. v145 대비 실제 font diff physical cell은 정확히 `[769,829,1840,2588,2614,2867,3076,3100,3134,3182,3193]` 11개이며 모두 packed readback pixel-exact다.
- **bank/레이아웃 보존:** v145에서 확정한 `msgsec13=336명`, `msgsec19=462명`, 총 798명 구조는 그대로 유지했다. 최종 **798/798 raw 본문 readback**, 한 줄 19.5 cells 이하/최대 4줄, Japanese kana/CJK 잔여 0, unsupported Hangul 0, 열전 전용 위험 alias 0을 확인했다. `msgsec13 word0=337`, 첫 pointer start+2, 나머지 absolute BYTE pointer, 마지막 `05 05 05` sentinel을 보존했다. `msgsec19 word0=505`, technical block0 2B, 뒤 **126B tail SHA-256=`DFFD0CDCAD431952C6264EAA99C6062CF5FE2D165920C82B84B6D1E21EE84D35`**, header[464..504] 41개 tail pointer의 내부 상대 offset을 모두 보존했다.
- **검증:** `tools/build_sangokushi2_v146_issue140_citra_alias_repair.py --check`가 sealed v145에서 세 출력을 재계산해 현재 Patch와 byte-for-byte 동일(`deterministic_match=true`)임을 확인했다. 독립 `tools/verify_sangokushi2_v146_issue140_citra_alias_repair.py`는 exact v145+3파일 조성, 798/798 본문, 40개 수정 row, screenshot phrase 7종, 위험 alias=0, font 11-cell diff/신규+재사용 glyph pixel-exact, msgsec13 sentinel, msgsec19 block0/tail/41 pointer 상대 위치를 재검증해 PASS했다. 실제 빌드 후 verifier, `--check`, verifier, 재빌드, verifier를 반복해 동일 SHA를 확인했다.
- **최종 SHA-256/권위 자료:** `font.g1t=64D7EB5D352598DC191CC93AB90A1A5701F7F9301A109299083175999120D4FA`, `msgsec13=E02D037B7DBDC55313D30A3006AE109B0E7527CCDA48F325B7EAFF489FAF7613`, `msgsec19=BC0D5B6488BA7789C12ABE428B010D8991453000C6FD37735D7B81FC9A2038F6`; `text_sha256=C951910EE77E1BCE5F9646266151EBF960979AA7FB2FBC408D8E5A83AC0EB353`, encoded biography aggregate=`40775121AD334D828BE3651C6DBD40BA0DF4F8B1AC7CEBA39ABF9AA0064A5AA7`, targets SHA-256=`153479374A330086FFC83462A5D754A52E04864FD243B060373C7B101DAF5579`, report SHA-256=`348838420C022CA52559AF7EAD1CB8D314D17194481C05E5061B406FA6EA3F45`. 권위 자료는 `analysis/v146_issue140_citra_alias_repair_targets.json`, `analysis/v146_issue140_citra_alias_repair_report.json`, sealed baseline `analysis/v146_issue140_citra_alias_repair_baseline/PatchSnapshot`, 위 builder/verifier다.
- **v146은 #140 Citra alias 복구 기준본**이다. 정적/구조/결정적 검증은 완료됐고, 사용자가 제보한 7개 문구 및 신규 donor(`욱/온/낭/휴/덕/켰/롱/베/름/폐/열`)의 **Citra 실화면 재확인은 pending**이다. 실화면 확인 전에는 #140 runtime 최종 승인으로 표현하지 않는다.

## 2026-09-03 — v147-intermediate #141 왕윤 열전 문구/줄바꿈 최소 수정

- **베이스/범위:** exact v146 전체 Patch를 `analysis/v147_issue141_biography_wording_baseline/PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec13.dat` 한 파일만 수정했다. `font.g1t`, `msgsec19.dat` 및 나머지 Patch 파일은 v146과 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- **#141 실제 대상:** 열전 index 20(왕윤)의 v146 문구 `동탁 사후,이 / 각을 등 ...`이 사용자 화면과 일치했다. 최신 문구는 **`동탁 사후,이각 등 동탁의 반란군 잔당들을 막지 못하고 살해당했다.`**이며 잘못된 조사 `을`을 삭제했다. 전체 열전은 의미를 바꾸지 않고 줄바꿈만 재조정해 **`사후,이각 등`이 한 줄에 함께 표시**되게 했다.
- **레이아웃/구조:** 최종 4줄 폭은 `[16.0, 18.5, 17.5, 11.5]`, v145/v146의 19.5×4 gate를 통과한다. `msgsec13 word0=337`, 336개 pointer, 첫 pointer start+2, 마지막 `05 05 05` sentinel 구조는 그대로 유지한다.
- **검증:** builder→독립 verifier→builder `--check`→verifier가 모두 PASS. v146 대비 changed file은 `msgsec13.dat` 하나뿐이다. 최종 `msgsec13 SHA-256=433AC05D3745B46249CF63D05CCA4099F0CA8141AD847D07B3BD0A3075B5FDC3`, report SHA-256=`488E38230477319533BFE17376E54C8FE8399DF4736B4359E01576D58590149B`.
- **권위 자료:** `analysis/v147_issue141_biography_wording_targets.json`, `analysis/v147_issue141_biography_wording_report.json`, `analysis/v147_issue141_biography_wording_baseline/PatchSnapshot`, `tools/build_sangokushi2_v147_issue141_biography_wording.py`, `tools/verify_sangokushi2_v147_issue141_biography_wording.py`. Citra 실화면 확인 전까지 intermediate다.

## 2026-09-03 — v148-intermediate #137 아이템 설명 67종 전수 한글화

- **원인/번역 DB 대조:** `RomFS/Message/msgsec14.dat`가 아이템 설명 runtime bank다. v147 Patch의 `msgsec14.dat` SHA-256=`527A5F658F9CE57620AD085B45A50A41976C7781980B585EDD412A4AA6782A6D`는 immutable Original과 byte-exact라, 번역 데이터가 있어도 실제 게임에는 주입되지 않은 상태였다. `Extracted_Text/sangokushi2_translation.json`의 msgsec14는 185행 중 번역 가능 168행이며 **빈 `translation_ko` 0건**, `Extracted_Text/text_review.html`에서도 168/168 id가 존재해 번역 DB 누락이 아니라 runtime injection 누락으로 확정했다.
- **runtime 구조:** `msgsec14 word0=121`; 실제 아이템 설명은 **direct index 54~120 = 67개**이며 v70 아이템 67종 순서와 1:1 일치한다. `word[121]`은 byte `0xF2..0xF3`에서 parent0 첫 2바이트와 물리적으로 겹치므로 generic repack/header 재작성은 금지한다. v148은 121개 direct pointer와 파일 크기 4282B를 전부 보존하고, 각 direct span 내부 visible payload만 완전한 한국어 설명으로 재작성한 뒤 남는 바이트를 ASCII space로 채우고 기존 `05 05 05` separator 위치를 유지했다.
- **번역/레이아웃:** 손자병법서~오령수의 혼 **67/67 전부 한국어**, 미번역 runtime item=0. 기존 fragment 초안은 의미 참고용으로만 사용하고, 잘린 Shift-JIS fragment를 다시 연결하지 않도록 아이템 1개=완전한 설명 1개로 재구성했다. 모든 설명은 **최대 3줄 / 한 줄 19.5 cells 이하 / 각 기존 direct span capacity 이하**다. 대완마처럼 글리프 때문에 의미를 심하게 바꾸지 않고 `달리면 피 같은 물이 나온다고 한다`처럼 슬롯 내 자연어로 정리했다.
- **폰트:** 최종 설명 전체에서 v147 map에 없는 한글은 **`쥐` 1자뿐**이다. fresh low-lead donor **`쥐=97D8/physical3429(stock 鱗)`**을 사용했다. Original-valid JIS, historical Korean alias 0, v147 code/Message/Scenario runtime ref 0, 현재 owner 0, 상·하 vertical edge 0을 확인했고 packed `font.g1t` readback은 Sam3 `쥐`와 pixel-exact다. v147 대비 실제 font diff는 physical3429 한 셀뿐이다.
- **검증/해시:** 독립 verifier가 exact v147+2파일 조성, JSON/HTML 번역 coverage 168/168, direct 54~120 67/67 raw readback, header 244B/121 pointer/file size/separator 보존, 19.5×3 layout, font 1-cell diff를 PASS했다. 이어 builder `--check`가 `deterministic_match=true`, verifier 재실행도 PASS했다. 최종 `font.g1t SHA-256=02A56951685DB7514A725FB05671A2162568EA15B0E12FFEAFBBADE389C66440`, `msgsec14.dat SHA-256=08093B5C508C21EF6730589F403CB89849ACD566E9DDA37AFA75861B7FF6E801`; targets SHA-256=`F3BFDD803B15F02A76980B0F82671E4CA544695FA3F1230CDA338F956806CFD0`, report SHA-256=`E1C4149ECF4630FF2F6A68DF803BCEAFEEA9B59E5617030C1398261257E20ED0`.
- **권위 자료/현재 상태:** `analysis/v148_issue137_item_descriptions_targets.json`, `analysis/v148_issue137_item_descriptions_report.json`, `analysis/v148_issue137_item_descriptions_baseline/PatchSnapshot`, `tools/prepare_sangokushi2_v148_issue137_item_descriptions.py`, `tools/build_sangokushi2_v148_issue137_item_descriptions.py`, `tools/verify_sangokushi2_v148_issue137_item_descriptions.py`. v149가 이 상태를 exact baseline으로 계승한다.

## 2026-09-03 — v149-intermediate #144/#145 열전 로드 상한·후반 특수 열전·옥새 종류 수정

- **베이스/변경 범위:** exact `v148-intermediate` 전체 Patch를 `analysis/v149_issue144_145_baseline/PatchSnapshot`에 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다. v148 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec20.dat` 2개**뿐이며 `msgsec19.dat`, `font.g1t`, v148 `msgsec14.dat`와 나머지 Patch 파일은 byte-exact다.
- **#144 방회 이후 깨짐의 실제 원인:** 열전 UI의 ID 생성 함수 `VA 0x1D78C8`과 generic Message getter `0x1EF888→0x1BF024→0x1BF0F4`를 디스어셈블해 record730 방회가 `message ID 19395 = msgsec19 direct index395`를 사용하며 현재 direct pointer **`0xD09E`**가 정확함을 확인했다. 문제는 파일 포인터가 아니라 실제 Message 파일 로더 `VA 0x10A94C`였다. 이 루틴은 각 `msgsecXX.dat`를 읽을 때 `VA 0x10A994: mov r2,#0xD000`으로 **최대 53,248B만 read 요청**한다. Original 최대 `msgsec19=50,232B`에서는 문제없었으나 현재 Patch는 `msgsec07=60,082B`, `msgsec19=62,465B`라 상한을 초과한다. `msgsec19` record730은 `0xD09E`, record797은 `0xF2F9`라 정적 포인터가 정상이어도 런타임 메모리의 미로드 영역을 읽게 된다. v149는 이 한 ARM 명령만 **`mov r2,#0x10000` (`E3A02801`)**으로 변경했다. 기존 루틴이 작은 파일에도 항상 0xD000을 요청하고 실제 읽은 길이를 반환받는 구조이므로 EOF 초과 요청은 기존 설계와 동일하며, 현재 22개 Message 파일 모두 새 65,536B 상한 미만임을 검증했다. 따라서 #144를 이유로 `msgsec19`를 재패킹하지 않는다.
- **#144 관로/자허상인 및 동일 누락 클래스:** `fix_data.bin` record1010~1019의 내부 ID가 각각 770~779이고, 이는 NDS 한국어판 `officer_biography` 마지막 10개와 정확히 1:1이다: **화타, 사마휘, 허자장, 우길, 좌자, 관로, 자허상인, 이의, 길평, 헌제**. 3DS runtime owner는 `msgsec20 word0=180`, direct index170~179(message20170~20179)다. 기존 `msgsec20` 21,360B와 body/header는 그대로 두고 EOF에 10개 완문 한국어 열전을 `05 05 05`와 함께 append한 뒤 header word171~180만 새 주소로 redirect했다. 최종 크기 22,699B. NDS 문구 중 현재 안전 map에 없는 `혐/흐`는 신규 donor 없이 의미 보존 문장으로만 최소 수정했다: 화타 `조조 암살을 의심받아 옥사했다`, 헌제 `시대에 농락당한다`. 10건 모두 한 줄19.5 cells 이하/최대4줄, 미지원 한글·일본어/CJK 잔여 0이다.
- **#145 옥새 종류 `特別`:** 실제 source는 `code.bin file+0x1CFEFC`, Original/현재 v148 모두 `93C1 95CA` 4B fixed C-string이며 absolute pointer는 `file+0x1DC48C -> VA 0x2CFEFC`다. 기존 packed font에서 **`특=8F61/physical1807`, `별=8F67/physical1813`**이 Sam3 기대 도트와 pixel-exact임을 확인해 새 font donor 없이 `8F61 8F67` 4B **`특별`**로 same-width 치환했다. 종료 NUL과 absolute pointer는 byte-exact다.
- **검증/결정성:** 독립 verifier가 v148+2파일 조성, code 변경이 loader 4B span과 #145 4B span 안에만 존재함(실제 diff byte offset `0xA994,0xA995,0x1CFEFC..0x1CFEFF`), loader instruction old/new 디코드, 22개 Message 크기, `msgsec19` v148 byte-exact 및 방회/record797 포인터, `msgsec20` 기존 body byte-exact/word171~180만 변경/10개 append raw readback, #145 NUL/pointer 보존을 모두 PASS했다. 이어 `tools/build_sangokushi2_v149_issue144_145.py --check`에서 `deterministic_match=true`, verifier 재실행도 PASS했다.
- **최종 해시/권위:** `ExeFS/code.bin SHA-256=E27EF9E4145EFF4E7EA2855AAFD03D1D442061E038E807C578A945A1810EA537`, `RomFS/Message/msgsec20.dat SHA-256=712C0B526F1B7836C8D9D24678A40362B1D05E371C78719E88AFD810DED99920`. 권위 자료는 `analysis/v149_issue144_145_targets.json`, `analysis/v149_issue144_145_report.json`, sealed baseline `analysis/v149_issue144_145_baseline/PatchSnapshot`, `tools/build_sangokushi2_v149_issue144_145.py`, `tools/verify_sangokushi2_v149_issue144_145.py`다. **v149는 #144/#145 Citra 승인 기준본**이다. 이후 사용자가 Citra에서 수정이 잘 되었다고 확인했으므로 #144/#145는 runtime 승인 상태로 승격한다. 확인 기록은 `analysis/v149_issue144_145_citra_runtime_confirmation.json`. 향후 동일한 `정적 포인터는 정상인데 특정 파일의 후반부부터 런타임 문자열이 깨지는` 증상에서는 ① runtime message ID/직접 포인터를 먼저 검증하고 ② 해당 `msgsecXX.dat`의 실제 파일 크기가 loader read cap을 넘는지 확인하며 ③ 포인터가 정상이라면 Message bank를 재패킹하지 않고 loader cap/읽기 경로를 먼저 수정한다. 이번 성공 방식은 `0xD000→0x10000` read-cap 확장 + `msgsec19` byte-exact 유지이며, 후반 특수 열전은 `msgsec20` EOF append + direct pointer redirect, fixed `特別`은 same-width code slot 치환이다.

## 2026-09-03 — v150-intermediate #136/#138/#139/#143 + 동일 popup/alias 잔존 보정

- **베이스/변경 범위:** 사용자 Citra 승인 상태인 exact `v149-intermediate` 전체 Patch를 `analysis/v150_issue136_138_139_143_dialogue_sweep_baseline/PatchSnapshot`에 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다. v149 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec08.dat`, `msgsec09.dat`, `msgsec18.dat` 5개**뿐이며 나머지 Patch 파일은 byte-exact다.
- **#136 포로 처리 버튼/같은 고정 라벨:** 포로 처리 선택지는 **`석방 / 처단 / 등용`**으로 통일했다. `code.bin 0x1D1714`에 남아 있던 `登用する`를 `등용`으로 바꿨고, 같은 ARM/absolute-ref 고정 라벨 묶음을 추가 감사해 `0x1D082C 赴く→방문`, `0x1D084C 在野→재야`도 함께 한글화했다. `赴く`는 인재 등용 방식 중 군주가 직접 찾아가는 선택지라는 문맥과 인접 `병사/군사/특산아이템/서적/무관/문관/태수` 고정 라벨군을 함께 확인했다.
- **#138/#139 popup:** #138 특산품 계열은 `msgsec09 direct85`의 이미 개방된 안내와 `msgsec18 direct110~112`의 상대 세력 없음/특산품 개방 완료/태수 없음 안내까지 같은 runtime class를 함께 수정했다. `ESC K/H`와 `NAME1` 동적 토큰은 보존했다. #139는 `msgsec09 direct76/87/88`의 편집 취소, 동맹 체결, 동맹 파기 확인문을 fixed span 안에서 수정했다.
- **`msgsec09` 동일 잔존 전수 + dual-use 경계:** 첫 40 shared word가 parent-route에서는 WORD offset(×2), direct-route에서는 BYTE pointer로 동시에 소비되는 구조를 다시 확인했다. direct 3~31의 협력 보상/금·병량/매복·내응/아이템/포로/후계자/사망 계열 일본어 잔존을 모두 제자리 한글화하되 shared numeric word는 **한 개도 변경하지 않았다.** 특히 direct10/12/15/19/23/27/28/31 안에는 다른 parent-route 시작점이 각각 delta `7/10/5/11/10/0/10/14`로 들어 있으므로 단순 한국어+공백 패딩을 금지하고, 그 바이트 위치에서도 의미 있는 한국어 suffix가 정확히 시작하도록 각 문자열을 구성했다. verifier에서 direct1~31과 제보 direct76/85/87/88의 일본어 잔여는 0이다.
- **#143 글리프/문구:** 하진 설명은 `하진은 환관을 조정 부패 원인으로\n보고 제후를 낙양에 불러 숙청했다.`로 줄바꿈을 재배치했다. Citra에서 `빙=98B8`이 실패하는 class를 확인해 `code.bin 0x1D2664`와 `msgsec08 0xB6A`의 두 proven-live `초빙`만 기존 packed safe alias **`빙=91BF/physical2276`**으로 재인코딩했다. `팔이 근질거리는군!!`의 `탑/측` 오표시는 문자열이 아니라 font physical collision이 원인이었다. 의미상 `팔=94CF/physical2856`, `질=94EA/physical2883`인데 실제 셀이 각각 `탑/측` 도트였으므로 참조 감사 후 **두 셀만** Sam3 `팔/질`로 복원했다.
- **추가 레이아웃/짧은 direct 문자열 감사:** `그리 쉽지는 않을 것 같습니다.`는 의미를 바꾸지 않고 `그리 쉽지는\n않을 것 같습니다.`로 줄바꿈만 보정했다. ARM 직접 참조에서 기존 가나 중심 scanner가 놓치던 `武将の%s%s→무장의%s%s`, `民の%s%s→민의%s%s`, `孫策殿が会見→손책이 회견`도 같은 revision에 포함했다. broad residue scanner는 후보 발굴용일 뿐이며 `msgsec09`에서 index>88을 body word를 pointer처럼 오해한 23건은 실제 direct table이 아니다. 반대로 다른 Message section에 남아 있는 broad 일본어 후보는 v150 범위 밖이므로 전체 게임 미번역 0이라고 주장하지 않는다. 분류 기록=`analysis/v150_dialogue_popup_residue_classification.json`.
- **도구 재현성 보정:** 첫 v150 적용 뒤 target preparer를 다시 돌리자 현재 Patch를 source preimage로 읽어 builder의 sealed-v149 source gate와 불일치했다. 안전 게이트가 실제 쓰기 전에 중단했고, preparer를 **항상 sealed v149 baseline에서 source를 읽도록** 수정했다. 이후 완성본 위 target 재생성/빌드가 다시 결정적으로 일치한다.
- **검증/결정성:** `tools/verify_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py` 독립 verifier PASS, 이어 builder `--check`에서 `deterministic_match=true` PASS. 검증 결과 v150 code target 10건, msgsec09 target 33건(그중 interior parent-boundary 8건), msgsec18 target 3건, msgsec08 target 1건이 모두 raw readback과 token/경계를 통과했다. reviewed `msgsec09/msgsec18/code` 일본어 잔여=0, 두 proven-live target의 old `빙=98B8`=0, font diff cell=`[2856,2883]`, v149 비대상 Patch 파일 byte-exact다. **정적/구조/결정성 검증은 완료했으나 Citra 실화면 확인은 아직 pending**이다.
- **최종 해시/권위:** `code.bin SHA-256=432C3D131E0C082130BB23E9FB58451D0D422F5BA1CDD7D049546E24C3FEACA8`, `font.g1t=7CF47000EF82EB0D93CF1009C7F5005C237A890BE03BE6AD7BB89BA47649C228`, `msgsec08.dat=0A7CF1E51E4FE5987F2C6B959935192E35490759EED2BEC181402ED26E33F8D7`, `msgsec09.dat=57716A241F2D7990C6790B9625A4C334746A8F2ABF3ACA588D105EB04B33A55F`, `msgsec18.dat=E70AAB427142025F38CC7AB265FC8B17A6AF9DE3A0FC961E7D62E74779C5BF9E`. 권위 자료는 `analysis/v150_issue136_138_139_143_dialogue_sweep_targets.json`, `analysis/v150_issue136_138_139_143_dialogue_sweep_report.json`, sealed baseline, residue classification, `tools/prepare_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py`, builder, 독립 verifier다. **v150은 v151의 exact baseline이며 runtime 최종 승인은 Citra 재확인 후에만 한다.**

## 2026-09-04 — v151-intermediate 편집취소 문구 + #146/#147/#148 UI 최소 수정

- **베이스/변경 범위:** exact `v150-intermediate` 전체 Patch를 `analysis/v151_issue146_148_ui_text_repair_baseline/PatchSnapshot`에 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다. v150 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec09.dat`, `RomFS/Message/msgsec18.dat` 3개**뿐이고 `font.g1t`를 포함한 비대상 Patch 파일은 byte-exact다.
- **편집 취소 문구:** 사용자 요청 `편집을 취소하시겠습니까？`의 어휘를 반영하되 Original/v150의 newline control을 유지해 `msgsec09 direct76`을 **`편집을 취소\n하시겠습니까？`**로 수정했다. direct pointer, 30B physical span, 파일 크기, `05 05 05` separator는 유지하고 남는 4B는 ASCII space다.
- **#146 특산품 완료 안내:** 실제 owner는 v150에서 확인된 `msgsec18 direct111`, capacity **39B**다. 사용자 제안 1안 `특산품 개방 완료 / <NAME1>의 소지품이 되었습니다.`는 47B, 2안 `... / <NAME1> 소유가 되었습니다.`는 43B라 둘 다 fixed span을 초과한다. pointer relocation/repack 없이 첫 줄과 의미를 보존하는 compact 1안 **`특산품 개방 완료\n<NAME1>의 소지품이 됨`** 38B를 적용했다. `NAME1`, `ESC K/H`, newline, direct header, file size, separator를 모두 보존했다.
- **#146 아이템 효과 `?정`:** code item-effect label table의 실제 row는 `0x1CFEB0 冷静`, pointer `0x1DC3F4 -> VA 0x2CFEB0`이다. v146에서 이미 packed-font pixel-exact로 검증된 **`냉=93FB/physical2712`**과 기존 `정=88CD`를 사용해 same-width `냉정=93FB88CD`로만 재인코딩했다. Citra에서 정상 출력된 `義理/野望/幸運/勇猛` sibling은 raw CJK라는 이유만으로 건드리지 않고 v150 byte-exact로 유지했다. `font.g1t` 자체도 v150과 byte-exact다.
- **#147 건강 상태 공용 table:** 사용자 확정대로 `0x1CFA14 病気→질병`, `0x1CFDBC 負傷→부상`, `0x1CFA64 健康→건강`으로 각각 4B same-width 치환했다. absolute pointer `0x1DC580/584/588`, 각 종료 NUL은 그대로다.
- **#148 도시 정보:** standalone `code.bin 0x1D0054 都市情報`가 pointer `0x1AB304 -> VA 0x2D0054`로 사용됨을 확인하고 **`도시정보=88DF88EA88CD88C0`** 8B same-width로 교체했다. pointer/NUL은 byte-exact다.
- **검증/결정성:** `tools/verify_sangokushi2_v151_issue146_148_ui_text_repair.py` → `tools/build_sangokushi2_v151_issue146_148_ui_text_repair.py --check` → verifier 재실행이 모두 PASS했다. `deterministic_match=true`, code target 5개, msgsec09 row76, msgsec18 row111 raw readback, control-token skeleton, msgsec18 113-pointer direct header/order, v149 loader cap/#145 특별 보존, #146 sibling 보존, safe `냉` packed glyph pixel-exact, v150 비대상 파일 byte-exact를 확인했다. Citra 실화면 확인은 pending이다.
- **최종 해시/권위:** `ExeFS/code.bin SHA-256=5F29F7C2E0A51C5C48FFA0B85FE064448A6578956A7B03F4BBA253BC1C3C08E0`, `msgsec09.dat=8392238A8D595F617AD11C53A728E76BB77726C912EEAE30DA61FA0A682EC7BE`, `msgsec18.dat=31E47E0F40311E248A8839AB4C7CAD4A24D25A03F32BF5594F846CB509EE78F7`. 권위 자료는 `analysis/v151_issue146_148_ui_text_repair_targets.json`, `analysis/v151_issue146_148_ui_text_repair_report.json`, sealed baseline, builder, 독립 verifier다. v151 Citra 재검수에서 #146 `냉=93FB`과 #148 실제 버튼 source 오판이 확인되어 최신 권위는 아래 v152가 대체한다.

## 2026-09-04 — v152-intermediate #146 item-detail alias + #148 실제 버튼 runtime 재수정

- **베이스/변경 범위:** exact `v151-intermediate` 전체 Patch를 `analysis/v152_issue146_148_runtime_repair_baseline/PatchSnapshot`에 봉인했다. `Original / Rebuild / Backup`은 수정하지 않았다. v151 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 2개**뿐이고 `msgsec09.dat`, `msgsec18.dat` 및 나머지 v151 Patch 파일은 byte-exact다.
- **#146 `?정=15` 재분석:** v151의 `code.bin 0x1CFEB0`은 실제로 `냉정=93FB 88CD`로 들어가 있었고 pointer `0x1DC3F4 -> VA 0x2CFEB0`도 정상이다. Citra에서는 두 번째 글자 `정`은 정상인데 첫 글자만 한자형으로 출력됐으므로 source 오판이 아니라 **아이템 상세 renderer에서 `냉=93FB`가 실패하는 screen-class alias 문제**로 판정했다. 기존 93FB는 다른 화면을 위해 건드리지 않는다. v151 전체 code/Message/Scenario에서 runtime ref=0, current Hangul owner=0인 controlled historical alias **`냉=8B9C/physical1113`**을 이 row 전용으로 사용했다. physical1113에 Sam3 `냉`을 주입했고 상위 physical1040 bottom edge=0, 하위1186 top edge=0, packed readback pixel-exact, font 변경 셀은 정확히 `[1113]` 하나다. `0x1CFEB0`만 `8B9C 88CD`로 same-width 재인코딩하고 NUL/pointer는 보존했다.
- **#148 실제 하단 버튼:** 사용자 Citra 스크린샷의 하단 `武将 / 都市`는 v151에서 수정한 `都市情報@0x1D0054`와 다른 source였다. 실제 공용 버튼 C-string은 **`code.bin 0x1CF904=武将`, `0x1CFC14=都市`**다. 각각 `무장=8BD7 88C2`, `도시=88DF 88EA`로 4B same-width 치환했다. `武将` absolute refs는 `0x1AB31C/0x1DC5DC/0x1E51C0`, `都市` refs는 `0x1AB320/0x1E51C4`이며 모두 동일 target을 유지한다. 종료 NUL도 byte-exact다. v151의 별도 `도시정보` 수정은 롤백하지 않고 계승한다.
- **검증/결정성:** 독립 verifier → builder `--check` → verifier 재실행이 모두 PASS했다. `deterministic_match=true`, code diff는 `0x1CFEB0..B3`, `0x1CF904..07`, `0x1CFC14..17`의 세 4B slot에만 존재하고 font diff는 physical1113 한 셀뿐이다. v151 비대상 파일은 모두 byte-exact다.
- **최종 해시/권위:** `ExeFS/code.bin SHA-256=478669A22E41015F5F4851705DCE2B9709D365E8742EA151BDEF9EA4BC19CA56`, `RomFS/Common/Font/font.g1t SHA-256=98B895A661DB83D8F9E1D68815C71E3A54C2AE89E33A33576212C266BADC0C4F`. 권위 자료는 `analysis/v152_issue146_148_runtime_repair_targets.json`, `analysis/v152_issue146_148_runtime_repair_report.json`, sealed baseline, `tools/build_sangokushi2_v152_issue146_148_runtime_repair.py`, `tools/verify_sangokushi2_v152_issue146_148_runtime_repair.py`다. 후속 Citra에서 #148 `무장/도시`는 성공했지만 #146 `价정=15`는 그대로였으므로 **#146의 `0x1CFEB0 + 8B9C` 원인 판정은 v153에서 폐기**됐다.

## 2026-09-04 — v153-intermediate #146 방천화극 실제 formatter + 동일 item alias 전수 보정

- **실제 원인 확정:** 사용자 스크린샷의 첫 글자 `价`는 CP932 **`98C1`**과 정확히 일치했다. v70 item-effect 권위와 current `code.bin`을 대조한 결과 방천화극 상세 화면은 `0x1CFEB0` standalone label이 아니라 **`0x195F5C = 무력+%d\n98C1 88CD=%d`** 포맷 문자열을 직접 사용한다. 따라서 화면의 `무력+30 / 价정=15`가 raw bytes와 1:1로 일치한다.
- **#146 실제 runtime formatter 8건:** v70 `effect_pool_list/detail`에서 `냉정`을 포함하는 8개 C-string `0x142EBC/0x142EC4/0x143238/0x14327C/0x195F54/0x195F5C/0x1962D0/0x196314`의 **`냉=98C1`을 모두 low-lead `93FB`로 same-width 교체**했다. `%d`, 공백/줄바꿈, NUL, 각 C-string 길이는 그대로다. 방천화극 상세 최종 raw는 `8BD78C972B25640A93FB88CD3D2564` = `무력+%d\n냉정=%d`다.
- **동일 사례 추가 감사:** `msgsec07 0x63C`의 **`냉정한 무장을\n5명 모아라`**도 동일 `98C1`을 사용하므로 `93FB`로 교체했다. v70 item UI의 effect formatter + item type 전체를 재감사한 결과 lead `>=0x98` alias는 0건이다. 67개 item 표시명에는 `청낭서`, `나기향낭` 두 곳의 `낭=9944`만 남아 있어 기존 v146 packed low-lead **`낭=8F83/physical1840`**으로 same-width 재인코딩했다. 그 결과 item code UI와 67개 item 표시명 모두 lead `>=0x98` 잔여 0건이다.
- **v152 임시 donor 제거:** v152에서 원인 오판으로 추가한 `냉=8B9C/physical1113`은 더 이상 runtime text ref가 없으므로 제거했다. `0x1CFEB0` standalone duplicate도 `냉=93FB`로 복원하고 `font.g1t`는 **v151과 byte-exact**로 복귀했다. v152에서 성공한 #148 `무장/도시`와 v151의 `도시정보`, 편집취소/#147/특산품 수정은 그대로 보존한다.
- **변경 범위/검증:** exact v152 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Scenario/fix_data.bin` 4개다. 나머지 v152 Patch 파일은 byte-exact. 독립 verifier → builder `--check` → verifier가 모두 PASS했고 `deterministic_match=true`다. code의 `98C1 88CD` text pattern은 8→0, msgsec07은 1→0이며 code에 남은 raw `98C1` 4곳(`0x1E93/0x1EA3/0xDC04C/0x116C7C`)은 모두 실행코드/데이터로 분류해 보존했다.
- **최종 해시/권위:** `code.bin=3328341B6C6534D199D517CEA2A4BC2AF71995899D565863D0CA72FFECF3382B`, `font.g1t=7CF47000EF82EB0D93CF1009C7F5005C237A890BE03BE6AD7BB89BA47649C228`, `msgsec07.dat=4D8D8F34965F7BDD41204CF0A55E2EE2D8502E45B17D5A5D631FB71C0FD8E010`, `fix_data.bin=BE1B131231A3896FA910EA39CB3054F78590F90A599C5B4AF157A9896C8514A1`. 권위 자료는 `analysis/v153_issue146_item_effect_runtime_fix_targets.json`, `analysis/v153_issue146_item_effect_runtime_fix_report.json`, sealed baseline, `tools/build_sangokushi2_v153_issue146_item_effect_runtime_fix.py`, `tools/verify_sangokushi2_v153_issue146_item_effect_runtime_fix.py`다. **v154에서 byte-exact 계승되며, v153 자체의 Citra 확인 상태는 pending이다.**

## 2026-09-04 — v154-intermediate #127/#131/#133/#142 에디터·갤러리·BGM 전수 한글화

- **베이스/범위:** exact `v153-intermediate` 전체 Patch를 `analysis/v154_issue127_131_133_142_baseline/PatchSnapshot`에 봉인했다. 사용자가 지정한 #127/#131/#133/#142 로컬 이슈 HTML/스크린샷과 실제 runtime source를 대조하고, 스크린샷에 없는 같은 화면군의 하위 메뉴까지 포함해 감사했다. Original/Rebuild/Backup은 수정하지 않았다. v153 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat`, `msgsec14.dat`, `msgsec18.dat` 5개**다.
- **#127 BGM 변경:** `code.bin 0x1CF8B0`의 `BGM変更`을 same-width **`BGM변경`**으로 수정했다. `msgsec18 direct33~58`의 BGM 곡명/설명과 direct60 확인문을 전부 한국어화하고, 동적 제목 formatter인 direct32는 byte-exact 보존했다. 각 direct span은 원 pointer/header/`05 05 05` separator와 control signature를 유지한 fixed-slot in-place 방식이다.
- **#131 무장편집:** `msgsec01`의 미번역 direct **6,7,24~26,29~34,72,74,76,77** 총 15개와 direct73의 두 동적 삭제 확인문을 수정했다. 이미 정상 한국어인 sibling 22개는 byte-exact 보존했고, direct70의 기존 ASCII `?`도 그대로 유지했다. 추가 runtime 감사에서 공용 code pointer table `0x1DC5D0/0x1DC5D4/0x1DC5D8`이 각각 `新武将/ＳＰ武将/年齢`을 가리키는 것을 확인해 **`신무장 / SP 무장 / 나이`**로 same-width 수정했다. 인접 `新君主` 등 편집기 범위 밖 공용 용어는 수정하지 않았다.
- **#142 갤러리:** 실제 owner는 `msgsec01 direct37~51` 15개다. 갤러리 제목/설명, 무장 파일, 이벤트 CG, 기본 물품, 특산품, SP 무장 파일, 명군 챔피언 도장과 각 설명을 모두 한글화했다. 사용자 요청대로 `イベントCG`는 **`이벤트 CG`**로 유지했으며 `장면 CG` 우회안은 폐기했다. 모든 15개 span은 원 control-code 순서와 capacity gate를 통과했다.
- **#133 이벤트 CG:** 실제 owner는 `msgsec14 direct0~26` 27개다. 기존 짧은 일본어 슬롯을 억지로 늘리지 않고 **현재 4282B body는 그대로 두고 EOF에 272B를 append한 뒤 direct0~26만 새 문자열로 redirect**했다. direct27~120 pointer는 보존했고 특히 v148 아이템 설명 direct54~120의 pointer/body가 byte-exact임을 검증했다. 첫 항목은 사용자 지정 **`메뚜기 떼`**, 그 밖에는 `태풍/홍수/역병/.../손책의 첫 출진/.../도겸의 죽음` 등 27개 전체가 한국어다.
- **폰트 donor:** 신규 4자는 **`벤=8A43/physical837`, `트=97B6/physical3395`, `갤=98BF/physical3592`, `첫=997E/physical3716`**이다. 반복 표시되는 `이벤트`의 `벤/트`는 low-lead donor에 배정하고, 한 번만 노출되는 `갤/첫`만 high-lead로 격리했다. `첫`은 cell 내부 `(x=1,y=1)`로 배치해 상/좌/우 guard를 확보했다. #133의 `뚜=8D56/physical1420`, `떼=97CF/physical3420`은 v131에서 이미 `메뚜기 떼가 발생하였습니다.`를 위해 만든 glyph가 현재 v153에서도 Sam3와 pixel-exact임을 재확인해 그대로 재사용했다. 최종 font diff는 physical **[837,3395,3592,3716]** 네 셀뿐이다.
- **검증/결정성:** builder preflight `--check` → 실제 v154 build → 독립 verifier → sealed v153에서 결과를 다시 계산한 deterministic `--check` → verifier 재실행이 모두 PASS했다. 첫 postbuild 결정성 검사에서 `historical_aliases_before_v148()`가 새 v154 report 자체를 다시 읽어 `98BF`를 과거 예약 alias로 오인하는 self-reference가 발견됐으나, Patch 문제가 아니라 감사 함수 범위 문제였다. history 스캔을 **v1~v153 report만** 읽도록 수정한 뒤 `deterministic-postbuild`가 PASS했다. `msgsec14` 최종 크기는 4554B다.
- **최종 SHA-256:** `code.bin=B2C830400600AA50874DDC189DD806B557D2B60604D723A07AF1EDEEFC729391`, `font.g1t=E1E623A1633CB97129B6A75E920EDB68E8116A775D93A796EE949AF02C50673A`, `msgsec01.dat=ACE675C8B00FA017CBC3D20823F41C3A20D23C29EA4A312CE1FCB410B38ABF53`, `msgsec14.dat=9EFF71269A4FE815F0EE049BD6C2F1F7D27793760DB7371E0855958305B2FE85`, `msgsec18.dat=6DD8DADD6DC4349948F0006DC84052DB882D0A808D2952AE0D31E1B6B03D1642`.
- **권위 자료/Citra:** `analysis/v154_issue127_131_133_142_targets.json`, `analysis/v154_issue127_131_133_142_report.json`, sealed baseline, `tools/build_sangokushi2_v154_issue127_131_133_142.py`, `tools/verify_sangokushi2_v154_issue127_131_133_142.py`가 v154 권위다. Citra 우선 확인은 `갤러리`의 `갤=98BF`, `손책의 첫 출진`의 `첫=997E`, `이벤트`의 `트=97B6`, #131 hidden editor menu/popup, #127 BGM 하위 설명이다. v154는 아래 v155의 exact baseline으로 계승한다.

## 2026-09-04 — v155-intermediate #153/#156 BGM·갤러리 실제 누락 source 재추적

- **베이스/범위:** exact `v154-intermediate` 전체 Patch를 `analysis/v155_issue153_156_baseline/PatchSnapshot`에 봉인하고, 새 로컬 이슈 `Issue153.html/4장`, `Issue156.html/1장`을 다시 기준으로 실제 화면 source를 재추적했다. v154 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec18.dat` 4개**뿐이다. Original/Rebuild/Backup과 BGM G1T는 수정하지 않았다.
- **#153 BGM 화면 split source — v156에서 일부 source 판정 폐기:** v154의 `msgsec18 direct33~58`만으로 화면 전체가 끝나지 않았고, 상위 시작메뉴 설명 `msgsec18 direct7`, 현재값 prefix `direct31`, 동적 값 formatter `direct32`, 곡 설명 `direct33~58`, 하단 라벨/26곡 code pool이 서로 분리된다는 판정은 유지한다. 다만 v155에서 BGM 서브화면 상단 안내의 실제 화면 source를 **`code.bin 0x86D84`**로 본 것은 #157 Citra 재제보 후 폐기됐다. `0x86D84`는 의미가 같은 duplicate이고, 실제 표시 copy는 v156에서 **`code.bin 0xEC5C0=ゲーム中に再生するＢＧＭを設定してください`**로 재확정했다. v155의 `0x86D84` 한국어 수정은 롤백하지 않고 보존한다.
- **#153 하단 라벨/26곡 목록:** `戦 略`은 `code.bin 0x1CFE2C`의 독립 5B C-string이므로 사용자 요청대로 **`전 략`** same-width로 수정했다. `デフォルト` 및 26곡 선택 목록은 `code.bin` 별도 C-string pool이며, 상단 `msgsec18 direct33~58` 제목과 **26/26 동일 표기**가 되도록 맞췄다. `디폴트, 영걸결기, 와룡자복, 질풍신뢰, 공수유구, 황궁연회, 창천황토, 방황, 파도 공방, ...`을 적용했고 fixed code slot 때문에 긴 항목은 상·하단을 함께 `경국미희 / 긴급상황 / 위 주제곡 / 촉 주제곡 / 오 주제곡 / 몇 성상 지나 / 호기도래`로 통일했다. `디폴트`의 색상/선택 스타일은 문자열 밖 caller/UI 상태가 담당하므로 해당 code bytes를 건드리지 않았다.
- **#153 이미지 버튼 판정 — v156에서 교정:** `ChangeBGM_000.png`는 사용자가 #157에서 확인한 대로 **메뉴 진입 후 상단 타이틀 이미지**이며, 메인 메뉴의 `ＢＧＭ変更` 버튼과는 무관하다. v155의 `raw BGM変更=0` 감사는 ASCII `BGM` 형태만 확인해 전각 `ＢＧＭ` source를 놓친 잘못된 판정이었다. 실제 메인 메뉴 버튼은 v156에서 `code.bin 0x1CF750=ＢＧＭ変更` 및 pointer refs `0x1DC128/0x1DC15C`로 확정했다. `ChangeBGM.g1t`/`BGMDown.g1t`는 v155/v156 모두 byte-exact 보존하며, 사용자 제공 PNG 없이 임의 이미지 생성·합성을 하지 않는다.
- **#156 갤러리 내부 누락 class:** v154에서 고친 `msgsec01 direct37~51`은 **갤러리 진입 메뉴**였고, Issue156 상단 1줄은 별도 내부 hover/help class였다. 실제 owner는 `msgsec01 direct174~182`: `閲覧ｽﾙ勢力...`, `閲覧ｽﾙ項目...`, 미획득/미발생 안내, 무장 카드/아이템/역사 이벤트 표시 안내 9개다. 최신 문구는 `세력을 선택하세요`, `항목을 선택하세요`, `해당 세력의 카드가 없습니다`, `1장도 없습니다`, `아직 발생 없음`, `미발생 이벤트입니다`, `무장 카드를 표시`, `아이템 정보 표시`, `역사 이벤트 정보 표시`이며, 원 `ESC K/H`, pointer/header, 9057B file size, `05 05 05` separator를 모두 보존했다. v154 direct37~51은 byte-exact다.
- **폰트 donor:** `디폴트`의 `폴`만 기존 alias map에 없어 의미를 회피하지 않고 신규 low-lead **`폴=8C98/physical1297 (stock 堅)`**을 추가했다. strict donor 감사에서 current owner=0, runtime text ref=0, historical alias reservation=0, known failed alias=0, 아래 셀 top-edge=0, 왼쪽 셀 right-edge=0, ±73 Hangul owner=0을 통과했다. Sam3 공통 13×13 frame을 `(x=0,y=0)`에 배치했고 packed readback은 pixel-exact이며 v154 대비 font diff는 physical **[1297] 한 셀뿐**이다.
- **검증/결정성:** builder `--check` prebuild → 실제 v155 build → 독립 verifier → builder `--check` deterministic-postbuild → verifier 재실행이 모두 PASS했다. verifier는 Patch manifest/targets digest, code 허용 C-string allocation 밖 diff=0, `ARM 0x86CD0` instruction exact, msgsec01 direct174~182 외 diff=0, msgsec18 대상 direct + direct31 visible prefix 외 diff=0/direct32 byte-exact, 26곡 상·하단 제목 exact match, font physical1297 단독 diff, `ChangeBGM.g1t/BGMDown.g1t` byte-exact를 독립 확인했다.
- **최종 SHA-256:** `code.bin=694311E7C65EC1FC8AE6F9CF2A15D4E0623ADA02CDE7191019407947052E8F22`, `font.g1t=4A02FEC231721B54DF560CC370CA42904277960CD511FE72EB1A60EB1B630C4D`, `msgsec01.dat=8DA7A54338A2872F51C9A3CA2B1BB8981CFFC4E6FED2664E2F2E864982B93842`, `msgsec18.dat=63A7524F1F10076C9A7EE6E6D5B65237102F7EA7469AE6BE0AAFF0558A05A287`.
- **권위 자료/Citra:** v155 자체 권위는 `analysis/v155_issue153_156_targets.json`, `analysis/v155_issue153_156_report.json`, sealed v154 baseline, `tools/build_sangokushi2_v155_issue153_156.py`, `tools/verify_sangokushi2_v155_issue153_156.py`다. 다만 #153의 메인 메뉴 버튼/상단 hardcoded 안내 source 판정과 `전 략` 표기는 아래 **v156 #157**이 대체한다. v155는 v156의 exact baseline으로만 유지한다.

## 2026-09-04 — v156-intermediate #157 BGM 실제 메인메뉴/runtime source 재수정

- **베이스/범위:** exact `v155-intermediate` 전체 Patch를 `analysis/v156_issue157_baseline/PatchSnapshot`에 봉인하고 로컬 `Github_Issue/Issue157.html`과 첨부 3장을 기준으로 재추적했다. v155 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 2개**뿐이다. `font.g1t`, `ChangeBGM.g1t`, `BGMDown.g1t`, Original/Rebuild/Backup은 수정하지 않았다. 이미지 생성/합성도 하지 않았다.
- **메인 메뉴 `ＢＧＭ変更` 실제 owner 확정:** v102에서 이미 Citra용으로 고친 `세이브/로드/환경설정/편집기능/게임종료`와 같은 공용 문자열 테이블을 다시 추적했다. 실제 BGM 버튼은 **`code.bin 0x1CF750=ＢＧＭ変更`**이며 absolute pointer refs **`0x1DC128`, `0x1DC15C` → VA `0x2CF750`**이다. immutable Original에서 인접 refs가 `セーブ/ロード/環境設定/編集機能/ＢＧＭ変更/ゲーム終了`를 가리키는 것을 확인했다. v154의 `0x1CF8B0`은 같은 의미의 다른 duplicate였다. `0x1CF750` 10B allocation을 **`BGM변경` 7B + trailing space 3B**로 교체하고 종료 NUL과 두 pointer를 byte-exact 보존했다. 전각 `ＢＧＭ` 때문에 보이던 넓은 자간도 ASCII `BGM`으로 자연스럽게 정리된다.
- **메인 메뉴 상단 설명:** `ＢＧＭｦ変更ｼﾏｽ。`의 실제 owner는 v102 도움말 목록에서 빠졌던 **`msgsec10_0030_000 @ 0xF0C`**다. fixed span은 정확히 16B이고 바로 뒤 `05 05 05` separator를 움직일 수 없다. `BGM을 변경합니다.`는 17B라 1B overflow하므로 의미를 유지한 **`BGM을 변경합니다` 16B**를 사용했다. msgsec10 header, separator, 6884B 파일 크기, 비대상 바이트는 모두 byte-exact다.
- **BGM 설정 화면 상단 설명:** v155에서 고친 `code.bin 0x86D84`는 displayed copy가 아니었다. current v155 전체에서 스크린샷과 완전히 동일한 일본어 `ゲーム中に再生するＢＧＭを設定してください`가 **`code.bin 0xEC5C0`에 유일하게 1건** 남아 있었고 이를 실제 source로 재확정했다. 42B allocation 안에 **`게임 중 재생할 BGM을 설정하세요` 31B + trailing space 11B**로 넣고 기존 NUL을 보존했다. v155의 0x86D84 한국어 duplicate는 byte-exact 계승한다.
- **`전 략` 띄어쓰기 교정:** 실제 하단 label `code.bin 0x1CFE2C`는 v155에서 `전 략=8A83 20 90DE`였으나 #157 사용자 요청대로 **`전략=8A83 90DE + trailing space 1B`**로 수정했다. pointer `0x1DC6CC → VA 0x2CFE2C`와 종료 NUL은 byte-exact다.
- **검증/결정성:** builder preflight `--check` → 실제 build → 독립 verifier → deterministic-postbuild `--check` → verifier 재실행이 모두 PASS했다. 최종 감사에서 actual `ＢＧＭ変更` source count=0, actual `ゲーム中に再生するＢＧＭを設定してください` count=0, msgsec10 `ＢＧＭｦ変更ｼﾏｽ。` count=0이다. code target allocation 밖 diff=0, msgsec10 target 16B 밖 diff=0, refs `0x1DC128/0x1DC15C/0x1DC6CC` byte-exact, `font.g1t/ChangeBGM.g1t/BGMDown.g1t` v155 byte-exact를 확인했다.
- **최종 SHA-256:** `code.bin=13FC157798FE80ECDBBEA1E169EBF478B4ECC165F2720291BC20861A970E71DC`, `msgsec10.dat=B90F242A122FB75593E879EC01A0E661757A46361DFF9E4F7CA65A2150C0B2AF`.
- **권위 자료/Citra:** v156 권위는 `analysis/v156_issue157_targets.json`, `analysis/v156_issue157_report.json`, sealed v155 baseline, `tools/build_sangokushi2_v156_issue157.py`, `tools/verify_sangokushi2_v156_issue157.py`다. 후속 사용자 Citra에서 두 상단 설명과 실제 `BGM변경`/`전략` source 교정은 화면에 반영됐지만, **고정 슬롯의 trailing ASCII space가 중앙정렬 폭에 포함되어 `BGM변경`은 약 9.5px, `전략`은 약 5px 왼쪽으로 밀리는 레이아웃 회귀**가 확인됐다. 따라서 content source 판정은 유지하되 최종 레이아웃 권위는 아래 v157이 대체한다.

## 2026-09-04 — v157-intermediate #157 중앙정렬 trailing-space 보정

- **Citra 픽셀 계측:** 사용자 v156 실화면을 직접 계측한 결과 메인 메뉴의 정상 버튼 `세이브/환경설정/게임종료` visible center는 X≈`158.5~159.0`, `BGM변경`은 X=`149.0`으로 약 **9.5px 왼쪽**이었다. BGM 설정의 `방랑/전쟁` center는 X=`73.0`, `전략`은 X=`68.0`으로 정확히 **5px 왼쪽**이었다. 두 차이는 각각 v156이 남긴 trailing ASCII space 3B/1B 폭의 절반과 일치하므로, 이 renderer는 C-string 끝의 공백도 중앙정렬 폭 계산에 포함한다고 판정했다.
- **메인 메뉴 BGM 버튼:** `code.bin 0x1CF750`의 v156 preimage `BGM변경 + ASCII space×3`을 폐기하고, 사용자가 제안한 대로 원본 전각 **`ＢＧＭ` 6B는 그대로 유지**하면서 `変更`만 한글 `변경`으로 교체했다. 최종 visible bytes는 정확히 10B **`82 61 82 66 82 6C 92 5C 8D D8` = `ＢＧＭ변경`**이며 trailing padding은 **0B**다. pointer refs `0x1DC128/0x1DC15C → VA 0x2CF750`과 기존 종료 NUL은 byte-exact다.
- **`전략` 중앙정렬:** v156 `0x1CFE2C = 전략 + ASCII space`에서 trailing space를 단순 leading space로 옮기면 visible text가 약 10px 오른쪽으로 과보정될 수 있다. 따라서 `전략` 4B 직후인 **`0x1CFE30`을 NUL로 조기 종료**해 렌더러가 공백 폭을 계산하지 않게 했다. 최종 span 시작은 `8A 83 90 DE 00`; 다음 `0x1CFE31`도 v156부터 이미 NUL이므로 인접 문자열 침범이 없다. pointer `0x1DC6CC → VA 0x2CFE2C`는 그대로다. 기대 center는 `방랑/전쟁`과 같은 X≈73이다.
- **변경 범위:** exact v156을 `analysis/v157_issue157_alignment_baseline/PatchSnapshot`에 봉인했고, v156 대비 게임 파일 변경은 **`ExeFS/code.bin` 1개뿐**이다. `msgsec10.dat`, `font.g1t`, `ChangeBGM.g1t`, `BGMDown.g1t`와 나머지 v156 Patch는 byte-exact이며 이미지 생성/수정은 하지 않았다.
- **검증/결정성:** builder preflight → 실제 build → 독립 verifier → deterministic-postbuild → verifier 재실행 모두 PASS. verifier는 두 alignment region 밖 code diff=0, `ＢＧＭ변경` 10B exact/no padding, `전략` immediate NUL + following pre-existing NUL, 세 runtime pointer refs byte-exact, 모든 비-code v156 파일 byte-exact, Patch manifest/targets digest 일치를 독립 확인했다.
- **권위 자료/Citra:** v157 권위는 `analysis/v157_issue157_alignment_targets.json`, `analysis/v157_issue157_alignment_report.json`, sealed v156 baseline, `tools/build_sangokushi2_v157_issue157_alignment.py`, `tools/verify_sangokushi2_v157_issue157_alignment.py`다. v157의 #157 정렬 수정은 아래 v158에 byte-exact 계승되며, Citra에서는 메인 메뉴 `ＢＧＭ변경` center와 `전략/방랑/전쟁` X 정렬을 계속 확인한다.

## 2026-09-04 — v158-intermediate #152 재야장수 `방문` 제한 팝업 최소 복구

- **베이스/실제 source 추적:** 현재 Patch 83파일이 `analysis/v157_issue157_alignment_report.json` manifest와 83/83 exact 일치함을 확인한 뒤 exact v157을 `analysis/v158_issue152_visit_popup_baseline/PatchSnapshot`에 봉인했다. 일본판 `君主しかできません`은 전체 Original에서 **`ExeFS/code.bin 0x1D1524` 한 곳에만 존재**한다. 등용 방식 메뉴는 `code.bin 0x16E7C4`에서 menu record index `1`(`금/설득/방문/아이템`)로 생성되며 `방문`은 selection index `2`, 분기 시작은 `0x16EA98`, 제한 팝업 호출은 `0x16EA90 -> VA 0x001EDE74`다. 이 분기가 읽는 pointer는 **`0x1E5254 -> VA 0x002D1524`**이고 v157에서도 Original과 byte-exact이므로, #152는 source 위치/포인터가 사라진 문제가 아니다.
- **v157 상태와 최소 수정:** v103부터 `0x1D1524`에는 `군주만 가능합니다`가 들어 있었지만 visible payload가 **17B**라 Original 일본어 18B보다 1B 짧고 NUL이 `0x1D1535`에서 한 바이트 일찍 시작했다. 사용자가 허용한 2안 **`군주만 가능합니다.`**는 정확히 **18B**이며 기존 한글 17B는 그대로 둔 채 **`0x1D1535` 한 바이트만 `00 -> 2E`**로 바꿔 Original과 동일한 `0x1D1536` terminator 위치를 복원한다. relocation, pointer 변경, font/Message/image 변경은 전혀 없다.
- **동일 class 감사:** 같은 전역 popup pointer cluster `0x1E524C..0x1E5270`의 8개 code C-string을 Original/v157로 비교했다. #152 외에도 원문보다 terminator가 앞당겨진 구조 후보가 4개(`0x1D1500 与え`, `0x1D14A4 委任状態...`, `0x1D14D8 かの者は...`, `0x1D1508 一筋縄では...`) 존재한다. 다만 이 네 문자열에는 현재 Citra 실패 증거가 없으므로 **감사만 기록하고 수정하지 않는다.** #152가 v158 Citra에서 정상화되기 전에는 “terminator 위치가 원인”을 전역 규칙으로 승격하지 않는다.
- **검증/결정성:** v157 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개**, 실제 code diff는 **1바이트(0x1D1535)**뿐이다. pointer cluster, 방문 menu call/branch/popup call 영역, 모든 비-code 82파일은 v157 byte-exact다. builder → 독립 verifier → builder 재실행 → verifier 재실행을 모두 PASS했고 두 번 모두 `code.bin SHA-256=E879A88D18ED53D36B47689521A4144933F98EFB730993416AB52AD5BA61F989`로 동일했다.
- **권위 자료/Citra:** `analysis/v158_issue152_visit_popup_targets.json`, `analysis/v158_issue152_visit_popup_report.json`, sealed v157 baseline, `tools/build_sangokushi2_v158_issue152_visit_popup.py`, `tools/verify_sangokushi2_v158_issue152_visit_popup.py`가 정적 빌드 권위다. 그러나 사용자 Citra Nightly 2104 실화면에서 v158도 **팝업 프레임만 뜨고 텍스트 영역은 완전히 빈 상태**임이 재확인됐다(`analysis/v158_issue152_citra_runtime_failure.json`). 따라서 **“Original terminator 위치 복원으로 해결된다” 가설은 폐기**한다. live RAM에서 `VA 0x002D1524`의 한국어 문자열과 `VA 0x002E5254 -> 0x002D1524`는 정상 적재됨을 확인했고, RAM 문자열을 immutable Original `君主しかできません`으로 바꿔도 빈칸이 유지됐다. 이후 GDB 준비 과정에서 source-path를 재감사하다 아래 v159의 shared-literal 회귀를 발견했다.

## 2026-09-04 — v159-intermediate #152 v71 shared-literal 회귀 복구

- **실제 원인 확정:** immutable Original의 `code.bin 0x16ED18`은 **`0x002E524C`** shared table base다. 그러나 v71 #021 빌더가 이를 `REWARD_TABLE_LITERAL_2`로 보고 reward mini-table **`0x002D6560`**으로 리다이렉트했다. 문제는 이 literal이 reward 전용이 아니었다는 점이다. PC-relative LDR xref를 전수하면 정확히 두 군데가 나온다: `0x16E9D4`는 table **+4**를 읽어 #021 `줄까요?` selector에 쓰고, `0x16EA44`는 같은 literal의 table **+8**을 읽어 #152 `군주만...` 제한 팝업에 쓴다. v71 mini-table은 +0/+4만 채우고 **+8을 0으로 남겼기 때문에**, `방문` 제한 경로가 `r0=NULL`인 상태로 `0x1EDE74`를 호출하여 프레임만 뜨고 본문은 비었다. `0x1E5254 -> 0x002D1524` 자체가 정상이어도 그 shared table이 더 이상 참조되지 않아 소용없었던 것이다.
- **최소 수정:** exact v158을 `analysis/v159_issue152_shared_literal_fix_baseline/PatchSnapshot`에 봉인하고, v71 mini-table의 비어 있던 **`code.bin 0x1D6568`(+8)** 한 워드만 `0x00000000 -> 0x002D1524`로 채웠다(raw `00 00 00 00 -> 24 15 2D 00`). `0x16ED18=0x002D6560` 리다이렉트 자체와 `0x1D6564=0x002D6580`(#021 reward +4)는 그대로 유지하므로 #021 동작은 보존한다. #152 target `0x1D1524=군주만 가능합니다.`와 모든 Message/font/fix_data/G1T도 byte-exact 계승한다.
- **동일 class 감사:** v71의 다른 reward literal `0x161C64`도 xref가 두 군데지만 `0x161B0C`, `0x161B68` 모두 **+4만 읽으므로** 현재 mini-table 설계와 충돌하지 않는다. 따라서 이번에 확인된 shared-member 회귀는 `0x16ED18` 한 건이다. 앞으로 table-base literal을 "전용"으로 리다이렉트할 때는 literal xref 수뿐 아니라 **각 xref의 table member offset(+0/+4/+8...)까지 전수**해야 한다.
- **검증/결정성:** v158 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개**뿐이고 실제 changed byte offset은 `0x1D6568/69/6A` 3바이트다(마지막 00은 동일). preflight → build → 독립 verifier → deterministic check → verifier 재실행 모두 PASS. 최종 `code.bin SHA-256=913BED0A95C7AE0F60086A3BB7D500782CE01234A3B3B55A463640E037F99B63`.
- **권위 자료/Citra 승인:** `analysis/v159_issue152_shared_literal_fix_targets.json`, `analysis/v159_issue152_shared_literal_fix_report.json`, sealed v158 baseline, `tools/build_sangokushi2_v159_issue152_shared_literal_fix.py`, `tools/verify_sangokushi2_v159_issue152_shared_literal_fix.py`가 정적 권위이며, 사용자 Citra Nightly 2104 실화면에서 재야장수 등용→`방문` 시 **`군주만 가능합니다.`가 정상 표시되는 것을 확인**했다. runtime 승인 증거는 `analysis/v159_issue152_citra_runtime_confirmation.json`이다. 따라서 **현재 Patch 권위는 Citra 승인된 v159-intermediate**로 올리고, `PROJECT_RULES.md`에 shared table/literal 재배치 시 모든 xref와 member offset(+0/+4/+8...)을 전수 확인하는 규칙을 확정 반영했다. 향후 팝업 프레임만 나오고 본문이 비는 동일 증상에서는 문자열/NUL/폰트보다 이 shared-literal/member 누락을 우선 점검한다.

## 2026-09-04 — v160-intermediate #150/#151/#154 무장 편집·신분·해고 조립 전수 수정

- **베이스/변경 범위:** Citra 승인된 exact v159 전체 Patch 83파일을 `analysis/v160_issue150_151_154_baseline/PatchSnapshot`에 봉인하고 #150/#151/#154만 수정했다. v159 대비 실제 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 2개**이며, 나머지 **81파일은 byte-exact**다. `font.g1t`, 모든 PNG/G1T, Scenario 데이터는 변경하지 않았다.
- **#150 해고 확인문 actual source:** 화면 문구는 standalone 고정문이 아니라 `code.bin 0x1696A8` formatter와 동적 argument 조립이다. caller `0x169404`가 formatter를 사용하며 첫 `%s`는 pointer `0x1DBAE8 -> 0x1CEE7C`의 기존 `정말`, 두 번째 `%s`는 선택한 무장명이다. 기존 formatter는 `%s%s 해고합니까？`였으므로 이름과 prefix가 붙고 조사가 없었다. 최신 target은 **`%s %s을(를) 해고합니까?`**다. 이 슬롯은 다음 문자열까지가 아니라 **`0x1696A8..0x1696BF` 24B(terminating NUL 포함)**만 안전하며 `0x1696C0`부터 ARM literal pool이 시작한다. 따라서 기존 정상 legacy `해고합니까` 바이트는 보존하고 공백/`을(를)`만 삽입했으며, 마지막 물음표는 1B ASCII `?`를 사용해 target 23B+NUL=24B로 정확히 맞췄다. literal pool `0x1696C0..`는 byte-exact다.
- **#151 신분 table 전수 감사:** 신분 pointer table의 실제 7종은 **`군주 / 군사 / 장군 / 무관 / 문관 / 在野 / 태수`**였고, 다른 6종은 이미 한국어였다. 따라서 `code.bin 0x1CEE44`의 4B `在野`만 same-width **`재야`**로 교체했다. refs `0x1DB6D4`, `0x1DC2F8` 및 주변 pointer table은 변경하지 않았다. 제보 한 항목만 보고 다른 신분까지 재인코딩하지 않았다.
- **#154 split-source 전수 수정:** 스크린샷 9장을 기준으로 한 화면군 안에서도 source가 분리됨을 재확인했다. 상단 도움말/확인문은 `msgsec01.dat` direct, 능력치·성격·전기 카테고리 짧은 라벨은 `code.bin` fixed C-string table이다. `msgsec01`은 **direct29 + direct149~173 중 이미 정상인 direct164를 제외한 총 25개**를 fixed span 안에서 한국어화했다. direct29는 **`무장 편집을 중지 / 할까요?`**, direct149~173에는 변경 항목, 얼굴/이름/독음, 능력치, 진형, 특수능력, 나이, 부모 무장, 상성, 선악·수명, 성격, 삭제, 성별 특수능력 안내, 전략 레벨, 전기 설정/초과 안내가 포함된다. direct70 `작성을 중지할까요?`는 디스크상 마지막 문자가 이미 ASCII `0x3F`임을 확인해 **byte-exact 보존**했다.
- **#154 code label table:** 능력치 6종 `武力/知力/政治/魅力/陸指/水指`를 **`무력/지력/정치/매력/육지/수지`**, 성격은 이미 정상인 `냉정`을 보존하고 `義理/野望/幸運/勇猛`만 **`의리/야망/행운/용맹`**, 전기 카테고리 `軍学戦技/掌握戦技/戦闘戦技/用兵戦技`는 **`군학전기/장악전기/전투전기/용병전기`**로 same-width 치환했다. 각 label pointer는 그대로이며 스크린샷의 빨간 `戦闘戦技`는 문자열에 색상 코드가 내장된 구조가 아니라 UI/caller style이므로 **문자열만 교체하고 색상/style 경로는 건드리지 않았다.**
- **검증/결정성:** 최초 build 후 독립 verifier PASS → builder `--check`에서 `deterministic_match=true` → verifier 재실행 PASS. verifier는 `non_target_files_byte_exact=81`, `issue150_literal_pool_preserved=true`, `direct70_ascii_question_mark=true`, `v159_issue152_preserved=true`를 확인했다. 최종 SHA-256은 `code.bin=58AE393D68F0702CC8A59CCC9DA836DB0A16DC60FC02FF7BCFE582F292A0792A`, `msgsec01.dat=1699F9677CAB54ABFE4D2154A4D87249210C785F7F1E14C62CACD864888E6B24`. v159 대비 changed byte count는 code 95B, msgsec01 558B다.
- **권위/런타임 상태:** 최신 정적 Patch 권위는 **v160-intermediate**이며 자료는 `analysis/v160_issue150_151_154_targets.json`, `analysis/v160_issue150_151_154_report.json`, sealed v159 baseline, `tools/build_sangokushi2_v160_issue150_151_154.py`, `tools/verify_sangokushi2_v160_issue150_151_154.py`다. 이후 사용자 실화면 재검수에서 **대부분 수정됨**을 확인했고, 남은 편집기 문제는 #158로 재등록했다. 따라서 v160의 다수 수정은 실화면에서 유효했지만 revision 전체를 blanket runtime 승인으로 승격하지 않고 아래 v161에서 잔여를 보정한다.

## 2026-09-04 — v161-intermediate #155/#158 이벤트 CG 두 번째 목록·편집기 이름 인자 수정

- **베이스/변경 범위:** exact v160 전체 Patch 83파일을 `analysis/v161_issue155_158_baseline/PatchSnapshot`에 봉인하고 #155/#158만 수정했다. v160 대비 실제 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec14.dat` 2개**이며 나머지 **81파일은 byte-exact**다. `RomFS/Message/msgsec01.dat`, `font.g1t`, 모든 PNG/G1T, Scenario 데이터는 변경하지 않았다.
- **#155 실제 누락 source:** v154 #133에서 번역한 이벤트 CG 이름은 `msgsec14 direct0~26` 27개였지만, 사용자 #155 스크린샷의 `任命/南蛮/議論/張角` 등은 같은 파일의 **별도 live 목록 direct27~49**였다. current v160에서 direct27~49가 Original 일본어 body/pointer를 그대로 유지하고 있음을 확인한 뒤 23개를 `조운/관우/일기토/논의/임명/공명의 죽음·1/공명의 죽음·2/통일/장각/불타는 낙양/오군/촉군/남만/훈련/기원/조위의 군신/동오의 군신/대승리/촉한의 군신/선인/북진 성공/삼국지Ⅲ/삼국지 2`로 **EOF append + direct27~49 u16 pointer만 redirect**했다. 기존 일본어 physical body는 byte-exact로 남겨 구조 회귀를 피했다. 잠금 placeholder direct50~53과 v148 item 설명 direct54~120은 pointer/body 모두 보존했다.
- **#155 사용자 문구/상단 처리:** v154 direct22의 `유성 지다`는 사용자 지정대로 **`별이 지다`**로 같은 visible byte 길이 안에서 in-place 교체했으며 pointer/separator를 유지했다. #155 스크린샷 상단 `閲覧する項目を選んでください`는 current v160의 `msgsec01 direct176`에서 이미 **`항목을 선택하세요`**로 수정된 상태였고 Original 일본어 raw도 current Patch에 없었다. 따라서 오래된 스크린샷을 이유로 정상 source를 다시 쓰지 않고 `msgsec01` 전체를 v160 byte-exact로 보존했다.
- **#158 조립형 popup actual source:** 첫 화면 `名前 설정 중지할까요?`는 공용 문장 전체가 한 C-string이 아니라 **`code.bin 0x1CFA78=名前` standalone label + `msgsec01`의 `%s 설정 중지할까요?` generic template** 조립이다. generic template은 이미 한국어이고 마지막도 ASCII `0x3F`였다. 따라서 shared template을 건드리지 않고 `名前` 4B만 same-width **`이름`**으로 바꿨다. pointer `0x1DC578 -> VA 0x2CFA78`, 종료 NUL, 주변 label table은 byte-exact이며 기대 조립은 사용자 2안 **`이름 설정 중지할까요?`**다.
- **#158 물음표 동일 class 감사 — v162에서 폐기/보정:** v161에서는 편집기 질문 direct `[27,28,29,69,70,71,72,73,75]`와 generic short/long template의 ASCII `?=0x3F`를 정적으로 확인하고 정상으로 보존했다. 그러나 사용자 Citra Nightly 2104 실화면에서 이 ASCII 물음표가 작고 낮게 보여 요구사항을 충족하지 못함이 확인됐다. 따라서 **"raw가 0x3F이므로 정상"이라는 v161 판정은 punctuation 표시 기준으로 폐기**하고, 실제 source/class 판정만 유지한다. 후속 v162에서 같은 class direct `27,28,29,69,70,71,72,73,75,96,97`을 stock 전각 `？=81 48`로 보정한다.
- **검증/결정성:** preflight에서 신규/수정 문구의 high-lead 위험 alias 0, 신규 glyph 필요 0을 확인했다. build 후 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. verifier는 `non_target_files_byte_exact=81`, `issue155_direct27_49_count=23`, `placeholders_50_53_preserved=true`, `items_54_120_preserved=true`, `issue155_top_help_prior_fix_preserved=true`, `issue158_name_pointer_preserved=true`, `issue158_editor_ascii_question_directs=9`, `msgsec01_v160_byte_exact=true`, `font_and_images_unchanged=true`를 확인했다. 최종 SHA-256은 `code.bin=AAEE539256C5410A9071F133106AF33161F4CD649C45D77D968D00D4EF0ED832`, `msgsec14.dat=B88405B699A0C257D641F800679956B4DF3B4F4C75554FBBFDC4954EC84E293D`.
- **권위/런타임 상태:** v161의 #155 목록 수정과 `名前→이름` source 수정은 정적으로 유지되며 v162에 byte-exact 계승된다. 다만 ASCII `?` 보존 정책은 사용자 Citra 실화면에서 실패했고 `analysis/v161_issue158_ascii_question_runtime_failure.json`에 기록했다. 따라서 최신 Patch 권위는 아래 **v162-intermediate**로 이동한다.

## 2026-09-04 — v162-intermediate #158 편집기 물음표 runtime 표시 보정

- **사용자 Citra 증거:** Citra Nightly 2104 실화면 3장에서 `무장 편집을 중지할까요?`, `작성을 중지할까요?`, `이름 설정 중지할까요?`의 끝 ASCII `?`가 다른 게임 문장부호보다 작고 낮게 보이는 것을 확인했다. 첨부 스크린샷은 **runtime 증거로만 사용했고 이미지 편집/생성은 수행하지 않았다.**
- **실제 source class:** screenshot-critical source는 각각 `msgsec01 direct29`, `direct70`, generic `%s 설정 중지할까요?`인 `direct97`이다. 동일 편집기 확인문 class를 전수해 direct **`27,28,29,69,70,71,72,73,75,96,97` 11개**, ASCII 물음표 **12개**를 대상으로 확정했다. unrelated direct126과 code.bin의 다른 질문문은 이번 범위에서 제외했다.
- **최소 수정 방식:** 모든 대상 ASCII 물음표가 바로 뒤에 padding space를 갖는 `3F 20` 형태임을 preflight로 증명했다. 이를 **stock Shift-JIS 전각 `？=81 48`로 정확히 2B same-width 치환**했다. 따라서 각 물음표 뒤의 모든 바이트가 같은 물리 위치에 남고, direct73 내부의 중간 `05 05 05` 경계, 201개 direct pointer table, 파일 크기, 모든 separator physical offset도 그대로다. 최종 기대 문구는 `무장 편집을 중지할까요？`, `작성을 중지할까요？`, `이름 설정 중지할까요？`다.
- **변경 범위:** exact v161 전체 Patch 83파일을 `analysis/v162_issue158_questionmark_baseline/PatchSnapshot`에 봉인하고 **`RomFS/Message/msgsec01.dat` 1개만** 변경했다. `code.bin`, `msgsec14.dat`, `font.g1t`, 모든 PNG/G1T와 나머지 **82파일은 v161 byte-exact**다.
- **검증/결정성:** preflight PASS(`target_directs=11`, `question_marks=12`) → build → 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. verifier는 `pointer_table_preserved=true`, `separator_offsets_preserved=true`, `direct73_internal_boundary_preserved=true`, `unrelated_direct126_preserved=true`, `font_and_images_unchanged=true`를 확인했다. 최종 `msgsec01.dat SHA-256=7B3AC1E35D8A8810384199F4F6D8F32A35E8018D3A1B52F13B33FAE8614E0BC2`.
- **권위/런타임 상태:** v162의 편집기 전각 물음표 수정은 정적으로 유지되며 v163에 byte-exact 계승된다. v162 자체의 Citra 재확인은 아직 pending이다.

## 2026-09-04 — v163-intermediate 챌린지 시나리오 목표확인 메뉴/상단 도움말 한글화

- **사용자 Citra 증거:** 챌린지 시나리오에서만 메인 메뉴에 `目標確認` 버튼이 추가되고, 선택 시 상단에 `シナリオの目標を確認します。`가 표시되는 것을 Citra Nightly 2104 실화면으로 확인했다. 첨부 스크린샷은 runtime 증거로만 사용했고 이미지 편집/생성은 수행하지 않았다.
- **버튼 actual source:** 사용자 추정대로 기존 메인 메뉴의 `세이브/로드/환경설정/편집기능/BGM변경/게임종료` fixed label pool 바로 옆 `code.bin 0x1CF7A4`가 `目標確認`의 실제 source다. current v162에서도 raw `96DA95578A6D9446`이 그대로 남아 있었고 refs `0x1DC168`, `0x1DC184`가 모두 `VA 0x2CF7A4`를 가리킨다. 이를 기존 안전 alias로 **`목표확인=8BC388E9935588D0`** 8B same-width 치환했으며 종료 NUL과 두 pointer는 byte-exact 보존했다.
- **상단 도움말 split-source:** `シナリオの目標を確認します。`는 하나의 독립 row가 아니라 `msgsec10_0028_003 @ 0xECD`의 9B `ESC K + ｼﾅﾘｵ + ESC H + ﾉ`와 `msgsec10_0029_000 @ 0xED6`의 14B `目標ｦ確認ｼﾏｽ。`가 **separator 없이 물리적으로 연속**되어 한 줄을 만드는 구조다. #v20 이후 msgsec10 fixed-position 규칙 때문에 repack/redirect를 하지 않고, 첫 fragment는 정확히 9B **`시나리오 `**, 둘째는 13B **`목표 확인하기`** + padding 1B로 in-place 교체했다. 따라서 combined 기대 표시는 **`시나리오 목표 확인하기`**이며, direct 시작점 `0xED6`, separator `0xEE4`, msgsec10 header/file size를 전부 보존한다.
- **동일 class/글리프 감사:** `目標確認` 버튼은 Original `code.bin`에서 해당 위치 한 곳이며 상단 suffix `目標ｦ確認ｼﾏｽ。`도 Original code+Message 전체에서 `msgsec10 0xED6` 한 곳뿐임을 확인했다. 새 문자열은 신규 glyph 0, known-failed alias 0, lead>=0x98 alias 0이다. 주변 메인 메뉴 한국어 label은 재인코딩하지 않았다.
- **변경 범위:** exact v162 전체 Patch 83파일을 `analysis/v163_challenge_goal_menu_baseline/PatchSnapshot`에 봉인하고 **`ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 2개만** 변경했다. v162의 `msgsec01` 전각 물음표, `msgsec14` 이벤트 목록, `font.g1t`, 모든 PNG/G1T 및 나머지 **81파일은 byte-exact**다. Original/Rebuild/Backup은 수정하지 않았다.
- **검증/결정성:** preflight PASS → build → 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. verifier는 `button_pointer_refs_preserved=true`, `msgsec10_header_preserved=true`, `msgsec10_fixed_boundary_0xED6_preserved=true`, `msgsec10_separator_0xEE4_preserved=true`, `v162_questionmark_revision_preserved=true`, `font_and_images_unchanged=true`를 확인했다. 최종 SHA-256은 `code.bin=1CACBA04D4D0CFAC8F7492CF7A2F87E30DEFBA4945FDDA0682EF2720716C7E7A`, `msgsec10.dat=545B5F8B0E6D93C82785E3437E12289FC0C577FBB38B93474BC8569261EEE6AA`.
- **권위/런타임 상태:** v163은 v164의 exact baseline이다. 권위 자료는 `analysis/v163_challenge_goal_menu_preflight.json`, `analysis/v163_challenge_goal_menu_targets.json`, `analysis/v163_challenge_goal_menu_report.json`, sealed v162 baseline, `tools/prepare_sangokushi2_v163_challenge_goal_menu.py`, `tools/build_sangokushi2_v163_challenge_goal_menu.py`, `tools/verify_sangokushi2_v163_challenge_goal_menu.py`다. **v163 자체의 Citra 실화면 확인은 pending**이다.

## 2026-09-04 — v164-intermediate #115/#116 전쟁 runtime·인물 전투 대사 전수 한글화

- **요청/범위 확장:** 로컬 `Github_Issue\Issue115.html`, `Issue116.html`과 첨부 스크린샷을 runtime 증거로 확인했다. 사용자는 스크린샷에 나온 문구만 찾지 말고 주변 전쟁 대사를 전부 확인하고 특히 인물들의 전투 시 대사를 전체 점검하도록 요청했다. 따라서 `대기/이동/전술/퇴각/설득/기도` 몇 건만 핀포인트하지 않고 current live pointer 기준으로 전쟁 관련 Message bank와 code fixed-label table을 광역 감사했다. 이미지 자체 수정 요청은 없었으므로 PNG/G1T/이미지 편집은 전혀 하지 않았다.
- **`msgsec02` 전쟁 본체 구조:** exact v163의 `RomFS/Message/msgsec02.dat`는 `word0=157`, header+pointer 영역 뒤 첫 body가 정확히 `0x013C=2*(157+1)`이며 157개 absolute BYTE direct pointer를 가진다. v163 실제 direct readback에서 **137개가 일본어/CJK live 문구, 20개가 이미 한국어**였다. 퇴각/포획/부상/사망/추격, 일기토 신청/응답, 화재/함정, 전술, 이동, 기습, 돌격/궁시/연노, 받아넘기기/튕겨내기, 성벽넘기, 정보, 화계/복병/위령/상잔/논파/도발/설득/고무/유인/기도/생존술/치료/충차, 출진/전군퇴각까지 전쟁 본체가 거의 전부 포함되어 있었다.
- **fragment 번역값 직접 주입 금지 확인:** 기존 `sangokushi2_translation.json`의 section2 번역은 physical fragment 단위라 실제 direct 문장을 재조립하면 `조심하십시오요`, `同士→동사`, Shift-JIS 반쪽 byte/고립 lead가 섞이는 사례가 확인됐다. 따라서 기존 `translation_ko`를 기계적으로 연결하지 않고 **immutable Original의 direct pointer 원문을 완전한 1문장으로 먼저 복원한 뒤 자연스러운 한국어를 다시 확정**했다. NAME1/NAME2/NAME3, `%d/%w/%s`, `01 S 02 e2/e3/e4` 등 special/control skeleton은 source와 target 개수를 gate로 비교했다.
- **`msgsec02` 안전 구현:** 137개 한국어 완문은 기존 v163 body를 수정/재패킹하지 않고 EOF에 `05 05 05` separator와 함께 append한 뒤 해당 **137개 u16 BYTE pointer만 새 문자열로 redirect**했다. 20개 기존 한국어 direct는 pointer와 payload를 byte-exact 보존했다. 신규 글리프가 필요한 표현은 의미를 유지하는 자연스러운 기존 글리프 표현으로 재작성했고 최종 신규 glyph=0, 모든 줄은 20 cells 이하이다. 최종 size는 `3690→7747B`.
- **인물 전투 대사 추가 bank 발견:** 작은 전쟁 관련 bank를 direct table 전체로 재감사한 결과 `msgsec12.dat`의 **25/25 direct entry 전부가 일기토/무장 전투 대사**였다. `나와 싸워라`, `내가 바로 <NAME>이다`, `이런 식으로 싸우게 될 줄이야`, `장난은 끝이다`, `적이지만 대단하군`, `내 검을 피하다니 제법이군`, `함께 죽게 되더라도 네놈만은 베겠다`, `적에게 등을 보이다니 그래도 무인인가` 등 25개를 전부 완문 한글화했다. flat absolute-BYTE table을 유지하고 old body byte-exact + EOF append + 25 pointer redirect를 사용했다. 최종 size `860→1560B`; 독립 verifier에서 **25/25 coverage**를 확인했다.
- **추가 live 전쟁 source:** `msgsec03`은 배치 prompt·전투 시간/날씨·전장 제목 등 **12개**, `msgsec05`는 매복 무장 정체/전투 재개 **3개**, `msgsec10`은 #115 상단 설명과 직접 연결되는 이동/전군퇴각/성내무장퇴각/출진 help **4개**, `msgsec15`는 안개 전투 운용 설명 **3개**를 같은 flat-table append+redirect 방식으로 한글화했다. `msgsec03`의 복잡한 ESC K/H 게임종료 문자열은 전쟁 범위에 불필요하고 구조 위험이 있어 억지로 포함하지 않았다.
- **#115 퇴각문/벽넘기:** 잘린 `퇴각시키겠습니까?` 실제 owner는 `msgsec04 direct22 @ 0x08D6`이었다. `msgsec04`는 mixed/dual-use 이력이 있으므로 table을 재패킹/redirect하지 않고 기존 물리 span 안에서만 사용자 허용 축약안 **`<NAME1>을(를) 어디로\n퇴각할까요？`**로 in-place 수정하고 남는 공간만 padding했다. header/pointer/separator/file size는 그대로다. 하단 `성벽넘`은 `code.bin 0x1CEFC4` fixed label이며 exact-width **`벽넘기`**로 교체했다. 인접 `0x1CEFBC 用兵術`도 **`용병술`**로 수정했다.
- **전쟁 자원/지형 fixed-label 감사:** 같은 runtime pointer table에서 `軍資金/援軍/増援`만 미번역/혼합인 것을 확인해 **`군자금/원군/증원`**으로 same-width 교체했다. 전투 지형 table도 `성벽`만 한국어이고 `湿地/湖水/建物/道/関/門/城/川`가 남아 있어 **`습지/호수/건물/길/관/문/성/강`**으로 exact visible-byte 치환했다. 각 terminating NUL과 pointer table은 보존했다. `埋伏武将@0x1CF8E0`은 `HEX ON/OFF`와 같은 편집/디버그 분류 table, `他国の戦争@0x1CFCF0`은 환경설정, `使者捕縛@0x1CFDB0`은 외교 결과로 확인되어 이번 in-battle 범위에서는 제외했다.
- **1차 v164 중간본에서 얻은 실패/재발 방지:** 최초 137개 msgsec02만 반영한 중간 Patch 위에서 preflight를 다시 돌리자 `msgsec02_first_pointer_is_body_start/strictly_increasing` gate가 실패했다. 이는 Patch 회귀가 아니라 이미 EOF redirect된 1차 v164를 v163 구조 권위로 잘못 읽은 것이 원인이었다. 이후 모든 v164 구조 preflight/빌드는 **sealed exact v163 baseline을 source authority**로 사용하도록 고쳤다. 현재 Patch가 이미 중간 v164일 때는 비대상 파일만 baseline과 비교하고 최종 산출물은 sealed v163에서 결정적으로 재생성한다.
- **광역 residue 감사와 과탐지 분류:** `tools/audit_sangokushi2_v164_battle_residue.py`로 Message 전체의 conservative live BYTE pointer와 code text 후보를 광역 감사했다. 최종 v164 재감사에서 후보는 Message 110 + code 3으로 남았지만, `msgsec01` 1건은 챌린지/영웅배틀 설명, `msgsec10` 1건은 신무장 편집 배치 help, `msgsec18` 4건은 편집 help, `msgsec20` 104건은 무장 열전이며, code 3건도 편집/옵션/외교 table이다. 따라서 **scoped in-battle runtime 일본어 잔존=0**으로 `analysis/v164_battle_residue_classification.json`에 명시했다. 단 battle vocabulary가 있다는 이유만으로 전쟁 runtime으로 단정하지 않고 active pointer + UI/domain 문맥을 함께 요구한다.
- **최종 변경 범위/검증:** exact v163 83파일 대비 변경 게임 파일은 정확히 **8개**: `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec03.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec10.dat`, `msgsec12.dat`, `msgsec15.dat`. `font.g1t`, 모든 PNG/G1T, Original/Rebuild/Backup 및 비대상 75파일은 byte-exact다. `build_sangokushi2_v164_issue115_116_war_sweep.py --check`가 `deterministic_match=true`로 PASS했고, 독립 `verify_sangokushi2_v164_issue115_116_war_sweep.py`도 PASS했다. verifier coverage는 msg02 `137 target + 20 preserved`, msg03 `12+20`, msg05 `3+19`, msg10 `4+132`, msg12 `25+0`, msg15 `3+290`; code fixed 13개, font/images unchanged다.
- **최종 SHA-256:** `code=DA7AED5F12AC36703C8876D98DCD06919FAA92B9F1AD12E4DA284E9589F03F4A`, `msg02=8D796318738E0E2240C9B6BD936F2A0254AC76454B310C00582A3F386395FB5A`, `msg03=A12ED4089D118C6C396BA32C434D298EDB35721877735B323BBF368A9318AEE7`, `msg04=56BACCFC8360A14DD64498F4E21763E286FE08FF5C1CB88CC827D40DFC9248EA`, `msg05=080167D8FFA20178E2806E47A315E83FA0F6F01CABADBDE05695903F533F0D3F`, `msg10=D9E096BEF9D242357C7B733E0C8E148076B5AC5C48829A2E1DED76CB480D4E22`, `msg12=6DDF329424C33A5DB8C13556EEA3049D85823F1E8888E94D24DDF2DD6050C963`, `msg15=EC4BEA114C7E6B2F0D21984A9B4DE3FB56BD589B89CECA0D38C237C1F066124C`.
- **권위/런타임 상태:** v164는 v165의 exact baseline이다. 권위 자료는 `analysis/v164_issue115_116_war_sweep_targets.json`, `analysis/v164_issue115_116_war_sweep_report.json`, `analysis/v164_battle_residue_audit.json`, `analysis/v164_battle_residue_classification.json`, sealed v163 baseline, `tools/prepare_sangokushi2_v164_issue115_116_war_sweep.py`, `tools/build_sangokushi2_v164_issue115_116_war_sweep.py`, `tools/verify_sangokushi2_v164_issue115_116_war_sweep.py`다. **v164 자체의 Citra 실화면 확인은 pending**이다.

## 2026-09-04 — v165-intermediate #160/#161 상태명 혼입·조선 메뉴 한글화

- **베이스/이슈 확인:** current v164 manifest를 `build_sangokushi2_v164_issue115_116_war_sweep.py --check`로 다시 확인한 뒤 exact 83파일을 `analysis/v165_issue160_161_baseline/PatchSnapshot`에 봉인했다. 로컬 `Github_Issue/Issue160.html`, `Issue161.html`과 assets 존재를 확인하고 이슈 본문/스크린샷을 runtime 증거로 사용했다. 이미지 자체 수정 요청은 없으므로 PNG/G1T/이미지 편집은 수행하지 않았다.
- **#160 actual source:** `msgsec07`의 generic 상태 template `현재 %s에서 %s 중입니다 / 변경하시겠습니까？`는 이미 정상 한국어였다. 혼입 원인은 두 번째 `%s`에 공급되는 **`code.bin 0x1DB6DC..0x1DB704` current-command pointer table**이었다. v164 table은 `준비 / 訓練 / 捜索 / 개발 / 耕作 / 治水 / 투자 / 密偵 / 건조 / 건조 / 건조`였고, 일본어 5개만 exact 4B slot에서 **`훈련 / 수색 / 경작 / 치수 / 밀정`**으로 same-width 치환했다. pointer word와 종료 NUL은 byte-exact다.
- **#160 동일 상황 감사:** `msgsec04/06/07/08`의 active direct를 다시 순회해 `현재`가 포함된 live 문자열을 전부 역읽기했다. `현재 공동작전 중입니다`, `현재 건조 중입니다`, `현재 %s에서 %s 중입니다`, `현재 위임/직할 중입니다`, `현재 %s에 종사하고 있습니다만` 등에서 일본어 kana/CJK 혼입은 **0건**이다. 이 결과는 `analysis/v165_issue160_161_residue_classification.json`에 기록했다.
- **#161 조선 상단 설명:** current `msgsec10 direct54/55/56`의 실제 pointer는 **`0xBE4 / 0xC0F / 0xC3A`**이며 각각 `建造期間2/4/6...金1000/2000/3000...` 일본어 40B span이었다. 이를 **`건조 기간 2개월, 금 1000 필요` / `건조 기간 4개월, 금 2000 필요` / `건조 기간 6개월, 금 3000 필요`**로 각 29B 인코딩하고 남는 11B만 ASCII space padding했다. 136개 msgsec10 direct pointer, 파일 크기, 각 `05 05 05` separator는 전부 v164와 동일하다.
- **#161 실행 확인문:** user Citra 화면의 `시키시겠습니까？`는 current `msgsec07 header[142]=0x243D`가 다른 한국어 문장 중간의 suffix를 가리키는 상태와 정확히 일치했다. immutable Original의 header[142]는 본래 조선 비용/기간 확인문 계열이었으나 과거 repack 뒤 현재 pointer가 interior suffix로 변해 있었다. 사용자 허용안 중 1안 **`군선을 건조하시겠습니까？`**는 25B/13 visible units로 안전하므로 v164 EOF **`0xEAB2`**에 target+`05 05 05`를 append하고 **header[142] 두 바이트만** 새 pointer로 redirect했다. 다른 387-word header와 기존 body는 v164 byte-exact다. 과거 #024에서 header[142]가 duplicate/non-live였던 사실을 이번 화면에 일반화하지 않고, #161 Citra 실제 출력과 current pointer가 일치하므로 screen-specific live source로 판정했다.
- **변경 범위/글리프:** v164 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `msgsec10.dat` 3개**다. 신규 glyph=0이며 `font.g1t`, 모든 PNG/G1T/이미지 자산, 나머지 80파일, Original/Rebuild/Backup은 byte-exact/무수정이다.
- **검증/결정성:** build 성공 → 독립 `verify_sangokushi2_v165_issue160_161.py` PASS → builder `--check`에서 `deterministic_match=true` PASS. verifier는 status table 11종 readback, 모든 `현재...` 동일-class 일본어/CJK 혼입 0, msg10 direct54~56 in-place/pointer/header/file-size/separator 보존, msg07 header[142] 단독 redirect/old body 보존, font/images unchanged를 확인했다.
- **최종 SHA-256/권위:** `code=708661AC02E552242BE6B035835F2824D62CAAA96C463C5F6A7F41D1E1FFB3B8`, `msg07=F6AED0E0A791515D57FC9745754BE416415694A47A57B2DAEAC288F09F541E4B`, `msg10=71F43D337380E4A23E9935B54EDA881883C1B13C2CCBB3FE9EC57AE90EFF2ADF`. v165는 v166의 exact baseline이다. 자료는 `analysis/v165_issue160_161_targets.json`, `analysis/v165_issue160_161_report.json`, `analysis/v165_issue160_161_residue_classification.json`, sealed v164 baseline, `tools/build_sangokushi2_v165_issue160_161.py`, `tools/verify_sangokushi2_v165_issue160_161.py`다. **v165 자체의 Citra 실화면 확인은 pending**이다.

## 2026-09-04 — v166-intermediate #160 후속 이중조사·무장정보 상단 종사표시 수정

- **사용자 Citra 재제보:** v165 실화면에서 `양의은 현재 훈련 중입니다 / 계속합니까？`로 이름 뒤 조사가 고정 `은`으로 붙었고, 무장정보 상단에는 `훈련に1カ月従事`가 일본어로 남았다. 첨부 스크린샷은 runtime 증거로만 사용했으며 이미지 편집/PNG/G1T 수정은 하지 않았다.
- **이중조사 actual source:** project history의 v55 공용 formatter를 현재 v165에서 다시 확인해 `code.bin 0xB623C`와 `0x114790` 두 copy가 실제 source임을 확정했다. 현재 target은 **`%s은 %s%s 중입니다\n계속합니까？`** 31B이고, 첫 `%s`가 무장명이며 뒤 `%s%s`는 이미 `현재 ` + 명령명을 공급한다. 따라서 사용자가 요청한 최신 target은 **`%s은(는) %s%s 중입니다\n계속합니까？`** 35B다. 두 slot 모두 immutable Original에서 visible allocation이 45B + terminating NUL이며 current target 뒤부터 Original terminator까지 0-padding이므로 fallback `은(는) 현재 OO 중인데...`는 필요 없다. 두 formatter 외 code/data는 그대로다.
- **무장정보 상단 actual source 재추적:** `msgsec07` current body `0x48B`에는 이미 `ESC C4 %s ESC C0 에 %d ESC K 개 ESC H 월 종사`라는 한국어 duplicate가 존재하지만 Citra는 `훈련に1カ月従事`를 표시했다. Patch 전체에서 Citra raw와 일치하는 Japanese month-duty pattern을 재검색해 **`code.bin 0x641F4` inline formatter**를 actual owner로 확정했다. 이 구간은 `0x641F0`의 ARM branch가 data block을 건너는 inline-string 구조이며 다음 code/data를 문자열 여유로 사용하면 안 된다.
- **사용자 우선안 바이트 판정:** control/format bytes까지 포함하면 1안 `OO에 X개월 종사 중`은 **25B**, 2안 `OO에 X개월 종사`는 **22B**, 3안 `OO X개월 진행`은 **20B**다. Original allocation이 정확히 20B이므로 **3안만 안전**하며 `0x641F4`를 `ESC C4 %s ESC C1 %u개월 진행`으로 exact 20B same-width 치환했다. 바로 옆 같은 inline class의 `0x64220=ESC C4 %s ESC C1 に従事`도 14B allocation 안에서 **`ESC C4 %s ESC C1 진행`** 13B + early NUL로 정리해 무개월 sibling의 일본어 잔존을 제거했다.
- **변경 범위:** exact v165 전체 83파일을 `analysis/v166_issue160_followup_baseline/PatchSnapshot`에 봉인했고 v165 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다. 신규 glyph=0, `font.g1t`, 모든 Message, 모든 PNG/G1T, Scenario, 나머지 82파일은 v165 byte-exact다. Original/Rebuild/Backup은 무수정이다.
- **검증/결정성:** 독립 `verify_sangokushi2_v166_issue160_followup.py` PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. verifier는 두 prompt target이 정확히 known copy 2곳에만 존재하고 각 45B allocation 밖 diff=0, month formatter 20B exact, plain sibling 14B allocation 준수, code 전체 diff가 reviewed slot 밖으로 나가지 않음, font/images unchanged를 확인했다.
- **최종 SHA-256/권위:** `code.bin=82A38F1F98E31FA29D51F46185E0F7F0C8D4BBC08BCB59D5DBC904374CF861FA`. 최신 정적 Patch 권위는 **v166-intermediate**다. 권위 자료는 `analysis/v166_issue160_followup_targets.json`, `analysis/v166_issue160_followup_report.json`, sealed v165 baseline, `tools/build_sangokushi2_v166_issue160_followup.py`, `tools/verify_sangokushi2_v166_issue160_followup.py`다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**

## 2026-09-04 — v167-intermediate #162 전쟁 오역·위임 무출력·fixed label 보정

- exact v166 전체 Patch 83파일을 `analysis\v167_issue162_battle_text_baseline\PatchSnapshot`에 봉인했다. 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec04.dat`, `msgsec07.dat`, `msgsec12.dat` 5개다. Original/Rebuild/Backup, `font.g1t`, PNG/G1T/ARC와 비대상 78파일은 수정하지 않았다.
- 사용자 제공 일본판 화면으로 위임 원문을 `解除できませんが / よろしいですか？`로 확정했다. 실제 raw는 `msgsec04.dat 0x583`의 옛 `解除ﾃﾞｷﾏｾﾝｶﾞ\nﾖﾛｼｲﾃﾞｽｶ？`였으나 current direct54가 body 변경 후에도 `0x583`에 남아, 현재는 direct37 `0x587` 바로 앞의 `？`만 가리켰다. 최신 완문 `해제할 수 없습니다만 / 괜찮으시겠습니까？`를 EOF `0x9CF`에 append하고 header direct54만 바꿨다. direct37과 나머지 header word는 보존했다.
- `msgsec02`는 v164에서 이미 한국어라 보존했던 20개 압축 번역도 원문과 다시 대조했다. 여기에 기동력 변화 2개와 `났=9971` 1개를 더해 총 23 direct를 EOF standalone append+redirect했다. NAME1, `02 28`, `%w` token 수는 source와 동일하고, 한 줄은 동적 이름 4글자를 포함해 20 cells 이하이다. `공격할까요？`, `일기토에 응할까요？`, `흘리기 대기할까요？`, `튕겨내기로 대기할까요？`, `소화할까요？` 등으로 고쳤으며 ASCII `?`는 전각 `？=81 48`로 정리했다.
- 퇴각 direct22는 mixed/dual-use 안전 규칙을 유지해 기존 `0x8D6` span 안에서만 `<무장명>는(은) 어디로 / 퇴각할까요？`로 수정했다. 위임 direct54만 append 방식이다.
- `code.bin`의 `作敵武将/埋伏武将/一般武将`은 `작적무장/매복무장/일반무장`, 남은 지형 `高山/低山/森/草地/平地/橋`은 `고산/낮은산/숲/초지/평지/다리`로 수정했다. `橋`는 2B라 4B `다리`를 in-place로 넣지 않고 ref=0 all-zero `0x1CF96E..0x1CF972`에 C-string을 두고 `0x1DC2D8` 한 pointer만 `0x2CF94C→0x2CF96E`로 redirect했다.
- `났=9971`은 새 donor 후보의 실제 인접 edge가 안전하지 않아 폰트를 바꾸지 않았다. active 문장만 `출현했습니다/마주했군/도주했습니다/대면했습니다/마쳤습니다`로 자연스럽게 재작성했다. 최종 Message active direct의 `9971` hit는 0, code에는 비문자 allowlist `0x405CC/0xBBEDC/0x1C91DE`만 남는다.
- builder 실행, 독립 verifier, deterministic `--check`가 모두 PASS했다. 최종 SHA-256은 `code=61902CE069F3E2A2DE97F3812E4F1BDA0E28A575FC74734948DDE79F6DB93566`, `msg02=8C91B79ADDBE54AC366867C8083EAEC43AFED25654DE85C11303D9C9BFED7B69`, `msg04=26692C41B560C1B3C1559F48A4393FD238B2B5D3F9529653F1C98DCF79CA73AF`, `msg07=C9C5AD55F6CBA486F925C99683AF3880A3153C75FCF23284AF227DA748C9F13E`, `msg12=2E400DEF0E41DA6AAEF72F2D237A7F467C96956516F5C6316C2867D2924C57B8`다. 최신 정적 Patch 권위는 **v167-intermediate**이며 Citra 실화면 재확인은 pending이다.

## 2026-09-04 — v168-intermediate #162 후속 퇴각 도움말 중복·`놈` 글리프 복구

- exact v167 전체 Patch 83파일을 `analysis\v168_retreat_help_nom_glyph_baseline\PatchSnapshot`에 봉인했다. 변경 게임 파일은 정확히 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec02.dat`, `msgsec10.dat`, `msgsec12.dat` 4개다. `code.bin`, 이미지/ARC/G1T(폰트 제외), Original/Rebuild/Backup과 비대상 79파일은 수정하지 않았다.
- 퇴각 도움말 `msgsec10 direct93/94`는 문자열 안 수동 `0A` 때문에 스크롤 renderer에서 `자국 도시·공백지로 퇴각시킵니다.`가 두 행에 중복 표시됐다. 두 target을 각각 **`아군 전체 부대를 인접한 자국 도시·공백지로 퇴각시킵니다.`**, **`성내 무장을 인접한 자국 도시·공백지로 퇴각시킵니다.`**인 줄바꿈 없는 standalone 문자열로 EOF append하고 해당 두 pointer만 redirect했다. verifier는 두 active raw의 `0A=0`, 공통 후반 문구 출현=각 1회, 기존 body와 비대상 pointer byte-exact를 확인했다.
- `놈`은 이미 `KNOWN_FAILED_ALIASES`에 `놈_old=9951`로 등록되어 있었으나 v164가 v155 `effective_map()`을 그대로 사용하면서 `msgsec02` 5건, `msgsec12` 3건에 다시 들어왔다. inherited map의 존재를 안전 판정으로 오인한 것이 재발 원인이다. active direct를 전수해 8건 모두 새 `놈=89B8`로 완문 재인코딩하고 EOF append+pointer redirect했다. 최종 active `9951=0`, active `89B8=8`; control prefix `01 S 02 e3/e4`와 줄바꿈 skeleton은 보존했다.
- donor `89B8/physical765`는 stock 한자 `穏`이다. 폐기된 v13 bulk 실험에서 `않`에 배정된 역사 이력은 있으나 exact v167에서 current Hangul owner=0, code text ref=0, 모든 Message/Scenario raw ref=0, known-failed 아님, valid-JIS임을 다시 증명해 사용자가 허용한 미사용 한자 재활용 예외로 회수했다. Sam3 `놈`을 x=0으로 넣어 edge를 top2/bottom0/left1/right0으로 만들었고, live right/below neighbor 방향은 0 edge다. packed readback은 pixel-exact, font diff는 physical765 한 셀뿐이다.
- 재발 방지로 신규/수정 target 인코딩 전 inherited map의 모든 사용 글자를 `KNOWN_FAILED_ALIASES`와 대조하며, 실패 alias 문자가 필요하면 새 안전 alias를 확보해 current active 사용 전체를 함께 재인코딩한다. 폐기 실험 alias를 회수할 때는 직전 sealed 기준의 owner/ref/valid-JIS/인접-edge와 예외 이력을 모두 기록한다.
- builder → 독립 verifier → deterministic `--check`가 PASS했다. 최종 SHA-256은 `font=444F30EF09F863F38F74F3AE32A75218D4594CBA22BBFD56123C2E7572046A18`, `msg02=22463A80B6E221F4C0F0ECC525867EE4F74BCDF201552FDAC61FEBFD0F5FDCC1`, `msg10=F7D101181C830E740A58213A35FFD916C285B505648117621878DA31A6100F71`, `msg12=6C8B87085F339C32C4111CE23D6947263C86463E9EA6E131EC1ED9371246E1F0`다. 권위 자료는 `analysis/v168_nom_donor_audit.json`, v168 targets/report/sealed baseline, v168 builder/verifier다. v168 당시 정적 Patch 권위는 **v168-intermediate**이며 Citra 실화면 확인은 pending이었다.

## 2026-09-04 — v169-intermediate #163 일기토 한자·포로 처리·등용 일본어 및 대사 잔존 전수 보정

- exact v168 전체 Patch 83파일을 `analysis\v169_issue163_dialogue_residue_baseline\PatchSnapshot`에 봉인했다. 실제 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat`, `msgsec10.dat`, `msgsec12.dat`, `msgsec15.dat`, `msgsec21.dat` **12개**다. `font.g1t`, 모든 PNG/G1T/ARC, Original/Rebuild/Backup 및 나머지 Patch 파일은 수정하지 않았다.
- #163 Citra 스크린샷에서 v168의 신규 `놈=89B8/physical765`가 stock 한자 `穏`으로 출력된 것을 확인했다. 정적 donor audit와 packed pixel-exact가 Citra 성공을 보장하지 않는 사례로 기록하고 `89B8`도 failed alias로 취급한다. `msgsec02` 5건 + `msgsec12` 3건의 active 일기토/전투 대사를 `네 목을 베어도`, `너를 상대하려면`, `너 따위는`, `너희에게`, `겁쟁이로군`, `너만은`, `네 이름`처럼 자연스럽게 다시 써서 **8개 target의 `9951=0`, `89B8=0`**으로 만들었다. v169은 폰트를 재수정하지 않는다.
- 포로 처리 상단 actual source는 `msgsec09 0x2AA..0x2BD`의 **20B dual-use fixed span**이다. 사용자 1안 **`<NAME1>님, <NAME2>의 처분은?`**을 exact 20B로 적용했고 interior `+10` route가 `의 처분은?`로 독립 decode되는 것을 검증했다. comma를 유지한 채 전각 `？`를 쓰면 21B가 되어 overflow하므로 이 span의 ASCII `?`는 용량상 의도된 예외다.
- 포로 처리 두 일본어 버튼은 과거 공용 `석방/처단/등용` label과 다른 실제 runtime owner였다. `code.bin 0x1D39C8=解き放す`, `0x1D39D4=首を斬る`를 각각 **`석방` / `처단`** 4B + NUL padding으로 바꾸고 refs/pointer는 byte-exact 보존했다. 조홍 등용 거절은 `msgsec09 0x30B` 24B 일본어 `逆賊ﾆﾅﾄﾞ力ﾊ貸ｻﾇ / 早ｸ斬ﾚ！`가 그대로 남아 있던 것이며 사용자 후속 검토에서 **`역적을 도울 수는 없다 / 베어라!`**는 29B라 fixed 24B fragment를 5B 초과하는 것을 확인했으며, 최종 사용자 선택에 따라 처음 제안한 **`역적은 못 돕는다 / 베어라!`**를 exact 24B로 적용했다. `베`는 실패 `9952`가 아니라 기존 안전 `964A`를 사용한다.
- #163 요청대로 동일 source class를 넓혀 실제 일본어 대사를 추가 감사했다. `msgsec05`에는 `%sﾉ...野ﾆ下ｯﾃ...`, `金ｶﾞ足ﾘﾏｾﾝ`, 초선 자결/주군 곁 대사 등 genuine Japanese direct가 남아 있어 **12 direct**를 Original 완문 기준으로 standalone append+redirect했다. `msgsec07`은 번역된 parent 뒤에 `誰ｦ...`, `国ﾉﾀﾒ...`, `自国ﾉ都市ﾃﾞｽ`가 공유 chain으로 남은 actual Original direct **8건**을 각각 complete Korean direct로 redirect했다.
- 추가로 `msgsec08 direct127`은 current pointer가 한국어 2바이트의 두 번째 바이트를 가리켜 `ﾍ...`처럼 보이는 상태였고 immutable Original은 `<NAME1> / 王允ｶﾞ参内ｼﾃｲﾏｽ`였다. 이를 `<NAME1> / 왕윤이 입궐해 있습니다`로 복구했다. `msgsec21 direct15/23/34/35`도 같은 pointer-mid-glyph 계열로 `ﾇ`, `ｳ비를...`, `ｪ니다`, `ﾖ 때입니다`처럼 깨져 있어 Original 완문에 맞춰 `원소 공과 협력해 / 조조를 막아야겠군`, `조조에게 / 타격을 줄 방법은 없을까...`, `지금이 천하삼분의 길로 / 나아갈 때입니다`, `음, / 모두의 활약을 기대하겠다`로 복구했다.
- 한자 혼입은 일본어 문장뿐 아니라 과거 Korean alias가 stock CJK로 렌더되는 잔존이 광범위했다. `해 88E7→8B6C`, `덕 97A1→9399`, `켰 996B→94DA`, `홍 984C→8BA3`, `름 9957→966C`, `굉 98AF→8F9E`, `롱 9943→95EF`, `억 98AD→8DAF` 등을 포함한 확정 old→current safe alias를 **한국어 direct/code dialogue 문맥에서만 2B→2B**로 재인코딩했다. targets 감사 기록은 Message 145 reviewed rows + code dialogue 80 reviewed rows이고, 독립 verifier의 scoped active stale-alias 최종 hit는 **0**이다. binary 전체 raw replace는 하지 않았다.
- 광역 residue scanner는 수정 후 `msgsec05/07/08/21`의 flat dialogue Japanese candidate를 0으로 만들었다. `msgsec09`는 구조상 generic flat scanner가 유효하지 않아 큰 index 후보가 false pointer로 발생하므로 exact #163 span으로 검증했고, 별도 `msgsec03/10/18`에는 system/editor/help raw Japanese 후보가 남아 있다. 따라서 **v169에서 게임 전체 일본어 0이라고 선언하지 않으며**, 남은 UI/system 후보는 실제 화면 owner를 확인한 뒤 별도 revision에서 처리한다.
- builder 실행 → deterministic `--check` → 독립 verifier가 모두 PASS했다. verifier 결과는 Patch 83파일 manifest exact, direct rewrite 33건 검증, msgsec09 fixed span 2건 검증, 실제 `석방/처단` button source 검증, 8개 duel line failed-놈 alias 0, scoped active stale alias 0, non-flat msgsec09 제외 flat dialogue 일본어 잔존 0이다. 최종 SHA-256은 `code=F92F5DD3DEEAE674A798BAA274CD5CF26BF9CA4648AA0D4796ABA7A6FD035FD4`, `msg02=22B6A5D7C67A08EFDED3FDC1A7EC18ECF85FA59FC5F5914B9BF2E758DA1589AB`, `msg04=6F8547004D98AEA66B9D341AA56A4013DE60F9FDB81D02EF8DDE9C87E5641C0F`, `msg05=DDE846928BF8C7F8B774284D97AFC1C79081AC238ABF85C42E2562153D86B401`, `msg06=FA696A78E48E6DF13121C4E2F1C8766468E75658A3C4245AA0B9ECE429273EC4`, `msg07=A3C067A699869045774D61244E44EF2ED07F577B2C518FF4F6C6A5B8C3C80890`, `msg08=D79CC8CF9B0E623497C14A01FDE485468AD0B1AB454938CE548FC6D647B35BC8`, `msg09=5B5D9EBBCCE6691B928262C690BA915B2A5BB7A678F109B3B82F508D2B7542AC`, `msg10=1AB20E347AC8C98DC1F9F4790E04F5A41898AFBD374EC75D05A79D1498F9B9D5`, `msg12=0EA43B8C10B214AFF4A1C9BA228990442CAB10F44ED2B3FBE4204DF7385B6341`, `msg15=A7479EE6E2EAE88D34AA3B0EFB2860CDC32306B727813DC9CB2F7F64A77DC6B0`, `msg21=3BECDA9B87AA5BE083B56042AA31A8A4C6621D191B1221ABCDFC6FD163C6A897`. 권위 자료는 v169 targets/report/sealed baseline과 v169 builder/verifier다. v169 당시 정적 Patch 권위는 **v169-intermediate**였으며 #163 후속 Citra 검수에서 `msgsec12` 구조 회귀와 포로 처리 ASCII 물음표가 확인되어 아래 v170에서 교정했다.

## 2026-09-04 — v170-intermediate #163 일기토 mixed pointer 구조 복구·포로 처리 전각 물음표

- exact v169 전체 Patch 83파일을 `analysis\v170_issue163_duel_pointer_repair_baseline\PatchSnapshot`에 봉인했다. 실제 변경 게임 파일은 `RomFS/Message/msgsec09.dat`, `msgsec12.dat` **2개뿐**이며 `font.g1t`, 모든 PNG/G1T/ARC, code.bin, 나머지 Message, Original/Rebuild/Backup은 v169 byte-exact다.
- 사용자 Citra 후속 화면에서 일기토 `네[한자]의 이름...`과 `Q0??V` garbage가 확인되어 `msgsec12` 구조를 immutable Original과 다시 비교했다. v164의 **25 flat BYTE direct** 판정이 오판이었다. Original은 860B, 25 u16 words를 가지지만 실제 사용은 앞 15개 값의 **WORD offset×2 parent route**와 `word1..word24`의 **BYTE direct route**가 겹치며, 25번째 direct pointer `0x034A`는 첫 body word `0x32`에 저장된다. parent 15개 안에는 physical fragment 49개가 있고 `05 05 05` separator 시작 위치는 36개다.
- v164 이후 `word1..word24`를 EOF BYTE 주소로 redirect한 것은 direct scanner에서는 정상처럼 보였지만 parent route에서는 같은 값을 ×2해 전혀 다른 주소를 읽게 만들었다. 이것이 Citra의 한자 혼입/garbage의 구조적 원인이다. v170은 immutable Original의 **860B 골격, 25 words, hidden direct24, 36 separator 위치를 exact 복원**하고 49 physical fragment를 각 원래 capacity 안에서만 한글로 다시 구성한다. generic append+redirect는 `msgsec12`에 더 이상 사용하지 않는다.
- 25 direct의 정적 readback을 전수 확인했다. Citra 제보 direct17은 **`네 이름, / 기억하마`**, hidden direct24는 `간신히 도망쳤군`으로 읽히며 25개 모두 일본어/CJK와 known-failed alias가 0이다. `놈=9951/89B8`은 사용하지 않는다.
- 포로 처리 `msgsec09 0x2AA..0x2BD`는 기존 `<NAME1>님, <NAME2>의 처분은?` 20B/ASCII fallback을 폐기하고 **`<NAME1>님, <NAME2> 처분은？`** 19B + padding1B로 바꿨다. `？=81 48` 전각 1회, ASCII `3F` 0회이며 내부 `+10` parent suffix도 ` 처분은？`로 독립 decode된다. 물음표 정책은 **전각 우선**, 검증된 fixed slot에서 전각이 실제로 초과하고 축약/안전 relocation이 적절하지 않을 때에만 ASCII를 최후 fallback으로 허용한다.
- builder 실행 → deterministic `--check` → 독립 verifier가 모두 PASS했다. verifier 결과: Patch 83파일 manifest exact, changed files 2, msgsec12 size 860B, header words 25, parent groups 15, physical fragments 49, direct 25, hidden direct24 true, separator positions 36, direct17=`네 이름, / 기억하마`, msg09 fullwidth question=true / ASCII=false, font/images unchanged. 최종 SHA-256은 `msg09=8F2C2A076891661B29245572C835C24F7B5EFD20F9BAF4AFC0EC539F29427098`, `msg12=C582CE1EAC5E866A6486097874CBAECA931C031263A3E3434BBE002331C3CA90`. 권위 자료는 v170 targets/report/sealed baseline과 v170 builder/verifier다. v170 당시 최신 정적 Patch 권위였으며 #165 문구 개선은 아래 v171이 계승한다.

## 2026-09-04 — v171-intermediate #165 일기토 문구 개선·`놈/벼` stock 한자 donor 재배정

- exact v170 전체 Patch 83파일을 `analysis\v171_issue165_duel_text_baseline\PatchSnapshot`에 봉인했다. #165 로컬 HTML과 assets를 확인한 뒤 v170 `msgsec12`의 parent/direct mixed 구조와 현재 문구를 대조했다. 실제 변경 게임 파일은 **`RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec12.dat` 2개뿐**이며 `code.bin`, msgsec09를 포함한 나머지 81파일, PNG/UI G1T/ARC, Original/Rebuild/Backup은 v170 byte-exact다.
- #165 첫 화면은 v170 mixed parent 고정 span `0x1E2..0x1F1`(총 16B)에 들어간 `너론 상대 / 안 돼` 계열이 어색한 문제였다. 사용자 제안 `넌 내 상대가 안된다`=19B, `넌 내 상대가 안돼`=17B라 exact 16B span을 각각 3B/1B 초과한다. separator/header를 움직이지 않고 자연스럽게 맞는 **`너는 상대 못돼`**를 적용했다. physical split은 `0x1E2..0x1EB="너는 상대" + padding1`, `0x1EC..0x1F1="못돼" + padding2`이며 두 span 사이에는 원래 separator가 없어 runtime 결합은 `너는 상대 못돼`다.
- 두 번째 화면의 parent-only `너 상대론 / 시간 낭비다`는 사용자 1안대로 **`네놈 상대론 / 시간낭비다`**로 수정했다. 25B fixed span에 22B로 들어가며 3B padding만 남긴다. `놈`을 피하기 위해 문장을 바꾸지 않고 신규 safe donor를 사용한다.
- 세 번째 direct0 Original `...을 知らぬとあれば / かかって参られよ` 계열은 기존 `OO을 모르면 / 싸워라`에서 사용자 1안 **`<NAME1>을(를) 모르면 / 덤벼라`**로 수정했다. control `01 S 02 e2`와 3B NAME1 token을 그대로 보존하며 direct0 payload 28B를 정확히 채운다. `덤`은 기존 safe alias, `벼`만 신규 donor가 필요했다.
- **한글 셀 보호 donor 감사:** 사용자 지시에 따라 기존 한글 glyph를 한 칸도 지우지 않았다. `analysis\v171_issue165_donor_audit.json`에서 current Hangul owner=0, code text ref=0, Message/Scenario ref=0인 stock CJK 셀만 후보로 제한했다. `놈`은 **`8F4B/physical1785`, stock `習`**, `벼`는 **`8FD4/physical1921`, stock `蕉`**를 사용한다. `놈`은 x=0/y=0, `벼`는 위쪽 live `땅` 셀 bleed를 막기 위해 x=0/y=+1로 배치해 top edge=0으로 만들었다. verifier 기준 **existing Hangul cells removed=0**, font changed physical cells=`[1785,1921]` 정확히 두 셀이고 packed readback pixel-exact다.
- `msgsec12`는 v170 구조를 그대로 유지한다. 파일 크기 **860B**, 25 header words, hidden direct24 `0x034A@0x32`, separator 36개 위치가 전부 byte-exact이며 #165 허용 span `0x34..0x4F`, `0x1E2..0x1F1`, `0x208..0x220` 밖 diff=0이다. direct0 외 **나머지 24개 direct route는 v170과 byte-exact**이고 일본어/CJK/known-failed alias 잔존이 없다.
- 빌드 → 독립 verifier → deterministic `--check` → verifier 재실행이 모두 PASS했다. 최종 SHA-256은 `font=6F933CF73AF9B4C8F91B61C71512130B910B652253A57BB368AE71610755326D`, `msg12=04DC7656E62A6DA04CC97B25CD4444DC0106540BC6F4D55783DA1C25FF96CA47`. 권위 자료는 `analysis\v171_issue165_donor_audit.json`, `analysis\v171_issue165_duel_text_targets.json`, `analysis\v171_issue165_duel_text_report.json`, sealed v170 baseline, `tools\audit_sangokushi2_v171_issue165_donors.py`, `tools\build_sangokushi2_v171_issue165_duel_text.py`, `tools\verify_sangokushi2_v171_issue165_duel_text.py`다. v171 당시 최신 정적 Patch 권위였으며 #166은 아래 v172가 계승한다.

## 2026-09-04 — v172-intermediate #166 일기토 번역 문구 개선

- exact v171 전체 Patch 83파일을 `analysis\v172_issue166_duel_text_baseline\PatchSnapshot`에 봉인했다. 실제 변경 게임 파일은 **`RomFS/Message/msgsec12.dat` 1개뿐**이다. `font.g1t`, code.bin, 나머지 82개 게임 파일, PNG/UI G1T/ARC, Original/Rebuild/Backup은 v171 byte-exact다.
- 사용자 검수 HTML `Extracted_Text\duel_text_review_v171.html` 기준 개선안을 적용했다. 최종 적용은 **#16 `과소평가했군 / 진심으로 간다!` (27/31B), #17 `과연, / 쉽지않군!` (15/27B), #18 `[e4]그 실력으론 / 어림도 없다!` (29/29B), #20 `벌써 지쳤는가？` (15/16B), #21 `네놈 따위는 / 시간 낭비다` (23/25B), #25 `이런 자가 / 있었다니` (18/18B), #29 `[e2]더 강한 / 장수는 없나!` (25/26B), #31 `이런` (4/4B), #32 `등을 보이다니 / 겁쟁이군!` (23/23B)**다. #18/direct14, #29/direct19, #31/direct20, #32/direct21은 동일 physical span이라 함께 갱신된다.
- #16 최초안 `네놈을 얕봤군 / 진심으로 간다!`은 `얕/봤`이 current safe map에 없고 신규 donor audit에서 기존 한글·live ref·edge bleed를 모두 피하는 donor가 없어 사용자 후속 대체안 **`과소평가했군 / 진심으로 간다!`**로 변경했다. 대체안은 신규 glyph 없이 27B/31B로 안전하다.
- #26/direct16 요청안 **`오랜만에 / 만난 호적수!`은 21B로 immutable 20B span을 1B 초과**한다. 또한 `랜`은 current safe map에 없고 저위/전체 유효 JIS stock CJK donor를 확장 감사해도 기존 한글 owner/live ref/edge bleed 조건을 모두 통과하는 donor가 없었다. 사용자에게 초과 시 알리라는 지시를 우선해 임의 축약하지 않고 v171 **`드디어 / 참 사내군!`**을 byte-exact 보존했다.
- #20은 프로젝트 질문부호 정책에 따라 ASCII `?` 대신 stock 전각 **`？=8148`**을 사용했다. 15B/16B라 1B 여유가 있다.
- 독립 verifier는 Patch 83파일 manifest exact, msgsec12 860B, 25 mixed header words, hidden direct24, separator 36개, 요청 9개 span 역읽기/바이트 수, #26/direct16 보존, 허용 span 밖 diff=0을 확인했다. deterministic `--check`도 PASS했다. 최종 `msgsec12 SHA-256=F2620FF1C6F2A3DB35E8E0BDE41A9346AF0A64AC2E12DB0F097C108CC4EEDBFB`다. 권위 자료는 `analysis\v172_issue166_duel_text_targets.json`, `analysis\v172_issue166_duel_text_report.json`, sealed v171 baseline, `tools\build_sangokushi2_v172_issue166_duel_text.py`, `tools\verify_sangokushi2_v172_issue166_duel_text.py`다. 최신 일기토 검수 HTML은 `Extracted_Text\duel_text_review_v172.html`이며 **Citra 실화면 재확인은 pending**이다.

## 2026-09-05 — v173-intermediate #167 시나리오 시작 대사 dual-route 전수 복구·리뷰 HTML 생성

- `Github_Issue\Issue167.html`과 첨부 스크린샷 7장을 직접 확인했다. 196년 조조 대두 시작 화면에서 `E이라는 강한 바람은`, `? 모습이 변해...`, 단독 `고`, `양을 장악했다`, `다스리려면`처럼 문두가 잘린 현상이 실제로 보였고, 이를 한 시나리오의 번역문 오타로 처리하지 않고 **시나리오 시작 대사 전체 runtime class**로 확대 감사했다.
- 원인은 `RomFS/Message/msgsec21.dat`의 dual-use header 구조였다. immutable Original은 `word[0]=67`이 parent0 시작(134B)을 겸하고, `word[1..35]`는 parent WORD start와 direct0..34 BYTE pointer를 동시에, `word[36]`은 parent EOF와 direct35를 동시에, `word[37..66]`은 direct36..65 BYTE pointer로 사용한다. body 첫 `1B 48`도 parent0 ESC H control이면서 hidden direct66 pointer `0x481B`로 해석된다. v172에서 first shared direct를 BYTE로 읽으면 번역 후 늘어난 body 중간에 착지해 #167 현상이 정확히 재현됐다.
- immutable Original의 67개 direct 범위를 fragment 경계와 무관하게 완문 단위로 다시 복구했다. legacy `translation_ko` fragment를 그대로 이어 붙이지 않고, 각 Original direct 문장의 의미와 기존 reviewed 번역을 대조한 뒤 자연스러운 한국어 완문으로 재구성했다. #167 핵심 direct0~7은 각각 `후한 제국에 몰아친...`, `그러나 동탁이 만든...`, `하북에서 세력을 떨치는...`, `그런 군웅과 영걸이...`, `조조는 동탁의 잔당을 토벌하고...`, `천하의 쟁란은 더 거세졌고...`, `대의를 얻어 낙양을 장악했다...`, `천하를 다스리려면...`으로 문두를 복원했다.
- shared u16의 두 runtime 해석을 동시에 살리기 위해 v133에서 검증했던 dual-mirror 원리를 `msgsec21` 전체에 확장했다. shared `word[1..36]`은 `V=0x5000+i*0x80`의 standalone direct 완문과 `2V`의 parent compact blob을 별도 mirror하고, `word[37..66]` 30개는 `0x6400+i*0x80`에 standalone direct를 둔다. hidden direct66 `0x481B`는 body 첫 `ESC H=1B48`와 양립하도록 보존했다. parent mirror는 `0xA000`부터 **0x100-byte 슬롯**으로 배치했고 `word[36]*2 = 0xC300`이 최종 EOF가 되도록 했다.
- 최종 `msgsec21.dat`는 v172 **36,096B → v173 49,920B(0xC300)**이며 과거 확인된 Message loader 상한 `0xD000` 미만이다. 실제 변경 게임 파일은 **`RomFS/Message/msgsec21.dat` 1개뿐**이고 `font.g1t`, `code.bin`, 나머지 82개 게임 파일, PNG/G1T/ARC, Original/Rebuild/Backup은 v172 byte-exact다. 이미지 편집/생성 및 G1T 수정은 수행하지 않았다.
- 시나리오 시작 대사 67개 전체에 **한 줄 18.5 fullwidth units 이하 / 최대 4줄** gate를 적용했다. 질문부호는 전각 `？` 2회, ASCII `?` 0회다. 독립 verifier는 Patch 83파일 manifest exact, direct 67/67, shared BYTE/WORD mirror 36/36, tail direct 30/30, hidden direct66, parent 36/36, layout 67/67, 비대상 파일 byte-exact를 확인했다. builder deterministic `--check`도 `deterministic_match=true`로 PASS했다. 최종 `msgsec21 SHA-256=04995CE03ABF203A51D332B4B117401A5925D8DA19B37765F2EAFE93A7824B98`이다.
- 사용자가 전체 시나리오 시작 대사를 직접 검수할 수 있도록 **`Extracted_Text\scenario_text_review_v173.html`**을 생성했다. 67개 runtime direct 각각에 ID, 일본어 Original 완문, v172 실제 direct 포인터로 읽히던 문구, v173 번역, 사용 바이트, 구조상 허용 바이트, 여유, 줄별 폭, source fragment를 표시하며 #167 핵심 항목은 별도 강조한다. 이후 사용자가 ID별 수정안을 제안하면 이 HTML을 검수 권위로 사용한다.
- 권위 자료는 `analysis\v173_issue167_scenario_dialogue_targets.json`, `analysis\v173_issue167_scenario_dialogue_report.json`, `analysis\v173_issue167_scenario_dialogue_baseline\PatchSnapshot`, `tools\build_sangokushi2_v173_issue167_scenario_dialogue.py`, `tools\verify_sangokushi2_v173_issue167_scenario_dialogue.py`, `Extracted_Text\scenario_text_review_v173.html`이다. **정적 검증은 완료됐지만 Citra 실화면 재확인은 pending**이므로 runtime 승인으로 표현하지 않는다. #168 실화면에서 18.5-unit wrap 및 code.bin 시나리오 bank 누락이 확인되어 현재 권위는 아래 v174가 대체한다.

## 2026-09-05 — v174-intermediate #168 시나리오 전체 source 재감사·줄바꿈 안전폭 교정

- `Github_Issue\Issue168.html`과 첨부 2장을 직접 확인했다. 첫 화면은 `그런 군웅과 영걸이 수없이 모인 가운 / 데 / 천하에... / 한때...`로, 두 번째는 `영웅들의 기마 소리가 울리는 큰 소리 / 에`로 자동 wrap됐다. 두 경우 모두 v173 정적 폭 18.5 units가 실제 renderer에서 한 글자를 다음 줄로 넘겼고 첫 문장은 5 visual lines가 되어 마지막 `평가받은 조조였다`가 보이지 않았다. 따라서 `msgsec21` screen-class의 기존 18.5×4 gate를 **17.5×4**로 교정했다.
- v173 67 direct 전부를 새 gate로 재감사한 결과 실제 초과는 **#3/#4/#5 3건**이었다. #3은 `수많은 군웅과 영걸이 모인 가운데 / 천하에 한발 앞선 이는 / 한때 「난세의 간웅」이라 / 평가받은 조조였다`, #4는 마지막 줄을 `중원에 확고한 기반을 다졌다`, #5는 `천하의 쟁란은 더 거세지고 / 영웅들의 기마 소리가 울리며 / 대륙은 그 굉음에 휩싸이려 했다`로 정리했다. parent fragment도 동일 17.5 gate로 다시 만들고 36 parent mirror를 전부 역검증했다.
- `scenario_text_review_v173.html`이 `msgsec21` 67개만 담았다는 범위 누락을 확인했다. 사용자 첨부의 **`원소와 조조 / 두 대국이 관도에서 격돌했다`**는 실제 `code.bin 0x58BE4`의 hardcoded 시나리오 서술이며, 과거 #035에서 확인한 별도 fixed C-string bank에 속한다. #035 기준 169개 target을 v85 문구/v86 실제 시작점으로 복원해 다시 감사했고, immutable Original `code.bin 0x55D00..0x5AF80`의 ARM ADR 참조를 별도 전수 수집했다.
- ADR 감사에서 historical inventory 밖 **17개**가 나왔고, `0x5839C=曹操→조조`는 standalone 동적 이름 인자라 review 대사에서 제외했다. 나머지 **16개는 실제 시나리오/이벤트 문장**으로 새 inventory에 편입했다. 그중 v173에 일본어가 남아 있던 `0x5A9A0=「小覇王孫策」...`, `0x5ABCC=「天下三分の計」...`, `0x5AE58=「姜維の北進」...`을 각각 **`소패왕 손책 / 목표를 달성했습니다!`**, **`천하삼분지계 / 목표를 달성했습니다!`**, **`강유의 북진 / 목표를 달성했습니다!`**로 수정했다. 기존 inventory 밖에서 15×3을 넘던 `0x589B0`과 `0x5A788`도 각각 `그러나 하진은 환관의 함정에 / 빠져 암살되고 말았다`, `황건적 진압 / 목표를 달성했습니다!`로 교정했다.
- 따라서 v174의 proven 시나리오/이벤트 검수 범위는 **`msgsec21 67 + code.bin 185 = 총 252개`**다. code 185는 historical 169 + recovered 16이며 모두 Original NUL fixed slot을 유지하고 15×3을 통과한다. Original main scenario/event 영역의 Japanese-like ADR target 184개는 `기존 main-region 167 + recovered 16 + standalone name 1`로 모두 accounting되어 미분류 target이 0이다.
- v173 HTML #41의 `[K]가하하[H]!`를 원문 raw와 다시 대조했다. Original은 실제로 **`1B 4B ｶﾞﾊﾊ 1B 48 ！`**, 즉 `ESC K ... ESC H`를 갖고 있으므로 K/H는 추출 오류가 아니라 문자폭 모드 제어다. K는 반각 일본어 가나에 쓰인 상태, H는 전각 상태 복귀로 취급한다. v173은 한국어 `가하하`를 K 상태 안에 넣은 것이 부적절했으므로 #41/#53 모두 **`ESC K → ESC H → 가하하!`**로 바꾸고, 새 HTML에서는 K/H를 visible text가 아니라 control 설명으로 표기한다.
- exact v173 Patch 83파일을 `analysis\v174_issue168_scenario_full_review_baseline\PatchSnapshot`에 봉인했다. 실제 변경 게임 파일은 **`ExeFS/code.bin`, `RomFS/Message/msgsec21.dat` 2개뿐**이다. `font.g1t`, 모든 PNG/G1T/ARC, 나머지 81개 게임 파일, Original/Rebuild/Backup은 v173 byte-exact이며 이미지 편집/생성이나 G1T 수정은 하지 않았다.
- 새 검수 HTML **`Extracted_Text\scenario_text_review_v174.html`**을 생성했다. 총 252개에 대해 source class/group/offset, Original, v173 readback, v174 문구, 사용/허용/여유 바이트, 줄별 폭과 제한, recovered inventory 여부, ESC K/H 설명을 표시하고 검색·source filter·v174 변경만·누락 복구만 필터를 제공한다.
- 최종 SHA-256은 `code=320F2C72770F186F1C4369B92B81A7222EC6D02DABA6F0869E26AE5718167333`, `msg21=D52E53ABB6022F440A0653955ECBAB7A186FF50C16D056A88FF11307B9740149`. builder 실행 후 deterministic `--check`가 PASS했고 독립 verifier도 Patch 83파일 exact manifest, changed files 2, msgsec21 direct 67/67, parent 36/36, code scenario 185/185, recovered 16, hardcoded 일본어 잔존 0, hardcoded layout 위반 0, review 252행을 PASS했다. 권위 자료는 `analysis\v174_issue168_scenario_full_review_targets.json`, `analysis\v174_issue168_scenario_full_review_report.json`, sealed v173 baseline, `tools\build_sangokushi2_v174_issue168_scenario_full_review.py`, `tools\verify_sangokushi2_v174_issue168_scenario_full_review.py`, `Extracted_Text\scenario_text_review_v174.html`이다. **Citra 실화면 재확인은 pending**이다.

## 2026-09-05 — v175-intermediate #169 사마휘 가이드 존댓말 통일

- `Github_Issue\Issue169.html`과 첨부 2장을 확인했다. 동일 화자 사마휘가 첫 화면에서는 `삼국지2의 / 플레이 방법을 알려주지`, 다음 화면에서는 `3DS 모드의 / 특징을 알려드리지요`라고 말해 말투가 반말/존댓말로 섞여 있었다. actual source는 각각 **`code.bin 0x5C040`**, **`0x5C078`**이며 공통 질문은 `0x5C068=들을까요？`다.
- 사용자 1안인 존댓말 통일을 우선 적용했다. `0x5C040` immutable Original visible slot은 **33B**이고 직역 `삼국지2의 / 플레이 방법을 알려드리지요`는 **36B로 3B 초과**한다. relocation이나 code/data 침범 없이 의미를 유지하도록 **`삼국지2 / 게임 방법을 알려드리지요`**로 정리했으며 32B/33B라 1B 여유가 있다. 질문 `들을까요？`와 두 번째 prompt `3DS 모드의 / 특징을 알려드리지요`는 이미 존댓말이라 byte-exact 보존했다.
- 과거 v63의 사마휘 3DS guide hardcoded 15개도 현재 의미 readback을 tone-audit했다. 후속 대사는 `-지요 / -니다 / -십시오` 계열의 공손체이며 **반말 판정 0건**이라 #169를 이유로 추가 문구 수정은 하지 않았다. 일부 historical raw alias/후속 자연어 변경으로 v63 byte-exact와 다른 항목은 있으나 이번 작업의 말투 scope와는 분리했다.
- exact v174 Patch 83파일을 `analysis\v175_issue169_sima_hui_tone_baseline\PatchSnapshot`에 봉인하고 실제 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다. 허용 diff는 `0x5C040`의 33B fixed slot 내부뿐이며 그 밖의 code.bin, `msgsec21`, font, PNG/G1T/ARC와 나머지 82파일은 v174 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- builder deterministic `--check`와 독립 verifier가 PASS했다. verifier는 Patch 83파일 exact manifest, changed files=`code.bin` only, 첫 prompt 32/33B, 직역안 36B/3B overflow, 질문/비교 prompt 보존, 후속 사마휘 guide 15개 공손체/반말 0, font/images unchanged를 확인했다. 최종 `code.bin SHA-256=DBB3F5D1B56ED9D62753ED3E93FAED7D6D724D04AAD29677C028B6145925D2F7`. 권위 자료는 `analysis\v175_issue169_sima_hui_tone_targets.json`, `analysis\v175_issue169_sima_hui_tone_report.json`, sealed v174 baseline, `tools\audit_sangokushi2_v175_issue169_sima_hui_tone.py`, `tools\build_sangokushi2_v175_issue169_sima_hui_tone.py`, `tools\verify_sangokushi2_v175_issue169_sima_hui_tone.py`다. **Citra 실화면 재확인은 pending**이다.

## 2026-09-05 — v176-intermediate #170 시나리오 문구 개선 + #171 renderer별 줄잘림 전수 보정

- `Github_Issue\Issue170.html`, `Issue171.html`과 #171 첨부 Citra 화면을 직접 확인했다. #170은 review ID C026/M26의 자연어 개선 요청이고, #171은 초상화가 붙는 M41에서 마지막 줄 `한인 따위에게 넘겨주지 않겠다!`의 끝 `다!`가 화면 우측에서 잘리는 실증이다. #171을 M41 한 건으로 처리하지 않고 v174의 252개 시나리오 inventory 전체를 renderer별로 다시 감사했다.
- #171의 핵심은 v174에서 `msgsec21` 전체에 적용한 `17.5×4`가 **무초상 서술창에는 맞지만 초상 대화창에는 너무 넓은 규칙**이었다는 점이다. M41의 15.0-unit 줄이 실제로 잘렸으므로 안전 여유를 두어 `msgsec21`을 **초상 대화 36개(M06~M25, M32~M41, M48~M53)=13.5×3**, **무초상 서술 31개(M00~M05, M26~M31, M42~M47, M54~M66)=17.5×4**로 분리했다. `code.bin` hardcoded 시나리오/이벤트 185개도 초상 화면에 사용될 가능성을 고려해 **13.5×3**으로 전수 gate를 낮췄다.
- v175 기준 새 초상 gate 초과는 정확히 **M09/M17/M24/M32/M33/M41 6건**이었다. M09=`진정해라, 장비 / 지금 우리 힘으론 부족하다`, M17=`조조가 황제를 옹립하다니... / 하지만 황제의 인장만 / 있다면...`, M24=`저 착한 유비를 이용해 / 재기하겠다!`, M32=`다음 통일 기회에 대비하라! / 병사를 기르고 / 인재를 모아라!`, M33=`당분간은 / 조조도 움직이지 / 못할 것입니다`, M41=`가하하! / 이 땅은 우리 것이다! / 한인 따위는 못 넘겨준다!`로 재행갈이·축약했다. M41의 `ESC K→ESC H` 제어 구조는 v174 그대로 보존했다.
- `code.bin` 185개를 13.5×3으로 다시 감사한 결과 초과는 **0x562B0, 0x57040 두 건**뿐이었다. 각각 `촉 백성을 편안히 하는 것이 / 군주의 역할이다 / 명심해 두거라`(13.0/7.5/6.5), `그 말대로 유비님뿐 / 그분이 천하의 영웅입니다`(9.0/12.0)로 보정했다. 이 두 건을 포함해 v176 final hardcoded 185개는 새 gate 위반 0이다.
- #170 C026 `code.bin 0x56364` Original `義は公孫殿にあります / 私の剣で袁紹を退けてみせましょう`를 확인하고 사용자 1안 **`대의는 공손찬님께 있다 / 내 검으로 원소를 물리치겠다`**를 적용했다. `께`는 현재 effective map에서 기존 **`8DBC / physical1521`**로 이미 존재하므로 새 donor/font 변경이 필요하지 않았다. target은 50B/Original 55B, 폭 11.0/13.5로 안전하다. #170 M26은 사용자 지정대로 **`적벽 회전`→`적벽 대전`**만 교정했다.
- exact v175 Patch 83파일을 `analysis\v176_issue170_171_scenario_wording_layout_baseline\PatchSnapshot`에 봉인하고 변경 게임 파일은 **`ExeFS/code.bin`, `RomFS/Message/msgsec21.dat` 2개뿐**이다. font/PNG/G1T/ARC와 나머지 81개 게임 파일, Original/Rebuild/Backup은 v175 byte-exact다. v175 #169 `삼국지2 / 게임 방법을 알려드리지요`도 regression gate로 보존했다.
- 전체 검수 HTML을 **`Extracted_Text\scenario_text_review_v176.html`**로 갱신했다. 252개 행 수는 유지하면서 각 항목에 renderer class와 `13.5×3` 또는 `17.5×4` 실제 제한을 표시하고, #170/#171 관련 행을 따로 필터링할 수 있게 했다.
- 최종 SHA-256은 `code=87093D7FA442A95553D198383D26EAC112F91811964D24EB78AD02B17438DADE`, `msg21=7AA7048EEB995D099452ACA0C6AA86C6FCD76CE3DAD25A3FD9E26FDBFE976C11`. builder deterministic `--check`와 독립 verifier를 연속 실행해 모두 PASS했다. verifier는 Patch 83파일 exact manifest, changed files 2, msgsec21 direct 67/67, parent mirror 35(+parent0 특수 control/hidden direct 별도), 초상 36/서술 31, code scenario 185/185, 새 layout 위반 0, C026/M26/M41 문구와 review 252행을 확인했다. **Citra 실화면 재확인은 pending**이다.

## 2026-09-05 — v177-intermediate Opening/StartMenu 지정 이미지 3개 1:1 리빌드

- 작업 시작 전 `tools\verify_sangokushi2_v176_issue170_171_scenario_wording_layout.py`를 실행해 현재 Patch가 exact v176-intermediate 83파일임을 확인했다. 전체 v176 Patch를 `analysis\v177_image_update_baseline\PatchSnapshot`에 봉인했으며, Original/Rebuild/Backup은 수정하지 않았다.
- 사용자 지정 PNG는 `Opening/ktlogo/ktlogo_000.png`, `ktlogo_001.png`, `StartMenu/title_up/title_up_000.png` 정확히 3개다. GPT/AI 이미지 생성 없이 해당 PNG를 그대로 사용했다. 세 파일 모두 RGBA 512×256이며 대응 texture는 `0x09 RGBA8`다.
- 두 컨테이너가 모두 v176 Patch에 존재하므로 `ktlogo.g1t`, `title_up.g1t`는 sealed v176 컨테이너에서 시작했다. `ktlogo` texture 0/1과 `title_up` texture 0 payload만 교체했고, G1T header와 비대상 payload는 v176과 byte-exact다. built Patch를 다시 decode해 3/3 source PNG pixel-exact를 확인했다.
- v176 대비 변경 게임 파일은 정확히 **`RomFS/Opening/ktlogo.g1t`, `RomFS/StartMenu/title_up.g1t` 2개뿐**이다. 최종 SHA-256은 `ktlogo=CC6B464A89DE3D1C4032EC6CC11E760FAB0107E69E9A0B45C3FABCC7EFDA7594`, `title_up=49DBB4FE02342F08BB0923712AE2DE983D66CE4A35AFD65FA2985E523DEFD272`다. Patch 파일 수는 83으로 유지되며 v176 code/font/Message/Scenario와 비대상 이미지 81파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier 재실행이 모두 PASS했다. 권위 자료는 `analysis\v177_image_update_targets.json`, `analysis\v177_image_update_report.json`, `analysis\v177_image_update_baseline\PatchSnapshot`, `tools\build_sangokushi2_v177_image_update.py`, `tools\verify_sangokushi2_v177_image_update.py`다. 최신 정적 Patch 권위는 **v177-intermediate**이며 Citra 실화면 확인은 pending이다.

## 2026-09-05 — v178-intermediate Opening/StartMenu 지정 이미지 2개 재리빌드

- v177 report의 complete 83파일 manifest와 현재 Patch가 exact 일치함을 확인한 뒤 전체를 `analysis\v178_image_update_baseline\PatchSnapshot`에 봉인했다. v177 verifier는 사용자가 새로 편집한 source PNG와의 readback 비교 때문에 예상대로 mismatch가 났지만, Patch 자체는 v177 manifest와 **83/83 exact, drift 0**이었다.
- 이번 입력은 `Opening/ktlogo/ktlogo_000.png`, `StartMenu/title_up/title_up_000.png` 정확히 2개다. 둘 다 RGBA 512×256이고 대응 texture 0은 `0x09 RGBA8`이다. 이미지 생성은 사용하지 않았다.
- 두 G1T 모두 sealed v177 컨테이너에서 시작해 texture 0 payload만 교체했다. `ktlogo_001` texture 1을 포함한 비대상 payload와 G1T header는 v177과 byte-exact이며 built Patch 재디코딩은 2/2 source PNG pixel-exact다.
- v177 대비 변경 게임 파일은 `RomFS/Opening/ktlogo.g1t`, `RomFS/StartMenu/title_up.g1t` 두 개뿐이다. 최종 SHA-256은 `ktlogo=18E91D3AF612C9EE28A6F3870366381C6045366D60FCB2CAA6EC75A8430D1D3B`, `title_up=114DA3AF5F344DA8ECD5782508DC381A408CE875C540BC6F1506DF5CAAB2C38D`. Patch 83파일 유지, v177 code/font/Message/Scenario와 나머지 81파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier 재실행을 모두 통과했다. 권위 자료는 `analysis\v178_image_update_targets.json`, `analysis\v178_image_update_report.json`, sealed v177 baseline, v178 builder/verifier다. 최신 정적 Patch 권위는 **v178-intermediate**이며 Citra 실화면 확인은 pending이다.

## 2026-09-05 — v179-intermediate Opening/StartMenu 이미지 2개 미세 조정 재리빌드

- `analysis\v178_image_update_report.json`의 complete 83파일 manifest와 현재 Patch가 exact 일치함을 확인하고 v178 전체를 `analysis\v179_image_update_baseline\PatchSnapshot`에 봉인했다.
- 미세 조정된 `Opening/ktlogo/ktlogo_000.png`, `StartMenu/title_up/title_up_000.png` 두 파일만 입력으로 사용했다. 두 PNG는 RGBA 512×256이며 대응 texture 0은 `0x09 RGBA8`이다. 이미지 생성은 사용하지 않았다.
- 각 sealed v178 G1T의 texture 0 payload만 교체했다. built Patch 재디코딩은 2/2 source PNG pixel-exact이며 G1T header, 비대상 payload, 특히 미언급 `ktlogo_001` texture 1은 v178과 byte-exact다.
- v178 대비 변경 게임 파일은 정확히 `RomFS/Opening/ktlogo.g1t`, `RomFS/StartMenu/title_up.g1t` 두 개뿐이다. 최종 SHA-256은 `ktlogo=84A2EB9B5403032EB438AC6AF2A06C104C237A9928D390DD4079BEF876547C4F`, `title_up=37ABEA5197AA05840C1F66415B31ED696FA60B9D2A3949F5C605AD316264B6C0`. Patch 83파일 유지, v178 code/font/Message/Scenario와 나머지 81파일은 byte-exact다.
- builder → 독립 verifier → deterministic `--check` → verifier 재실행이 모두 PASS했다. 권위 자료는 v179 targets/report/sealed baseline과 v179 builder/verifier다. 최신 정적 Patch 권위는 **v179-intermediate**이며 Citra 실화면 확인은 pending이다.

## 2026-09-05 — Opening `Title.arc` 진입 전 타이틀/LA8 credit 추출 분석 (Revision 변경 없음)

- 오프닝 영상 뒤, 시작 메뉴 진입 전에 표시되는 일본어 로고는 동영상 프레임이 아니라 `RomFS/Opening/Title.arc` SARC의 `timg/title_logo_2.bflim`이다. 배경 `shoki_up_00_title_bg.bflim`, 저작권 `shoki_up_00_title_credit.bflim`, 배치 `shoki_title_2.bflyt`, 애니메이션 `shoki_title_2_Tag_00/01.bflan`이 분리되어 있어 로고 member만 교체하면 애니메이션은 보존 가능하다. 실제 영상 파일 `RomFS/Movie/series.moflex`와는 별개다.
- 최초 credit 추출기의 format `0x03` 채널을 `L,A`로 해석해 검은 잡픽셀이 보인 오류를 확인했다. 실제 CTR LA8 저장 순서는 **`A,L`**이므로 `RGBA=(L,L,L,A)`로 고쳤다. `shoki_up_00_title_credit.png`는 320×24 RGBA이며 투명 배경과 원래 외곽선/안티앨리어싱을 그대로 보존한다.
- 교정된 credit PNG를 다시 `A,L`로 인코딩한 결과 Original `shoki_up_00_title_credit.bflim` member와 byte-exact였다. source member SHA-256=`B958885F285091D64CB46E6661DF6B72D6FF421FB03234B6B1696642B1B0C647`, corrected PNG SHA-256=`3C90A461B63C9A9671277B4A31F1D76465879C91A349C995593424008AB3A301`. 도구=`tools\extract_sangokushi2_title_arc.py`, 증거=`Extracted_Image\RomFS\Opening\Title\shoki_up_00_title_credit_manifest.json`.
- 이번 작업은 추출기와 편집용 PNG만 교정했으며 `Sangokushi 2 Patch`, Original/Rebuild/Backup, Revision은 변경하지 않았다. 현재 Patch 권위는 계속 **v179-intermediate**다.

## 2026-09-05 — v180-intermediate #172 M41 시나리오 대사 수정

- 작업 시작 전에 `analysis\v179_image_update_report.json` complete manifest와 현재 Patch를 대조해 **83/83 exact v179-intermediate**임을 확인했다. v178/v179이 v177 이후 `ktlogo.g1t/title_up.g1t` 이미지 컨테이너만 바꾼 image-only revision이라는 것도 report로 재확인했다. exact v179 전체 Patch는 `analysis\v180_issue172_scenario_m41_baseline\PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- `Github_Issue\Issue172.html`의 M41 요청을 확인했다. v176에서 Citra로 확정한 초상 대화 안전폭 **13.5×3**을 적용하면 사용자 1안 `한인 따위에게는 못 넘겨준다!`는 **14.0칸**이라 초과한다. 2안 `한인 따위에겐...`은 13.0칸, 3안 `한인놈들에겐...`은 12.5칸이지만 두 안 모두 current effective font map에 없는 **`겐`**이 필요하다.
- 이번 요청에서는 v179 이후 이미지/G1T를 건드리지 않는 조건을 우선해 font donor를 새로 만들지 않았다. current map에 이미 존재하는 `엔`을 사용한 가장 가까운 자연어 **`한인 따위엔 못 넘겨준다!`**를 적용했다. visible 폭은 **12.0칸**이다. 최종 M41은 `ESC K → ESC H → 가하하! / 이 땅은 우리 것이다! / 한인 따위엔 못 넘겨준다!`이며 기존 control 흐름은 보존한다.
- v173~v176에서 확정한 `msgsec21` dual-route 구조를 그대로 유지했다. `RomFS/Message/msgsec21.dat` 크기 **0xC300(49,920B)**와 67개 direct pointer geometry는 byte-exact이며, v179 대비 의미상 변경 direct는 **41 하나**, parent mirror는 **18 하나**다. 실제 changed bytes는 4B다.
- v179 대비 변경 게임 파일은 정확히 **`RomFS/Message/msgsec21.dat` 1개**다. `code.bin`, `font.g1t`, v179의 `ktlogo.g1t/title_up.g1t`, 모든 PNG/G1T/ARC와 나머지 82파일은 byte-exact다. 최종 `msgsec21 SHA-256=37689EB1B3AFF04C848473E74EB1C5AA20C2FDA6F8EB75F8EFC2994EC43A69F6`.
- 최초 preflight에서 `<$1B>` 표기와 actual ESC byte 비교 방식, 이어 v176 width 함수의 K/H 반각 계측, parent17/18 예상 change set을 차례로 과도하게 엄격하게 잡아 세 번 중단됐지만 모두 **Patch 쓰기 전 gate**였다. 실제 구조를 확인해 verifier 기대값만 교정한 뒤 build 성공. 독립 verifier → deterministic `--check` → verifier 재실행이 모두 PASS했다.
- 권위 자료는 `analysis\v180_issue172_scenario_m41_targets.json`, `analysis\v180_issue172_scenario_m41_report.json`, sealed v179 baseline, `tools\build_sangokushi2_v180_issue172_scenario_m41.py`, `tools\verify_sangokushi2_v180_issue172_scenario_m41.py`다. 최신 정적 Patch 권위는 **v180-intermediate**이며 **Citra 실화면 재확인은 pending**이다.

## 2026-09-05 — 전쟁 텍스트 전체 검수 DB 생성 (Revision 변경 없음)

- `scenario_text_review_v176.html`과 유사한 검색·source/category/status 필터 형식으로 `Extracted_Text\battle_text_review_v180.html`을 생성했다. 단순 전쟁 키워드 검색이 아니라 과거 runtime source 감사로 확정한 전투 범위만 사용했다.
- 수록 범위는 **270행**이다: `msgsec02` direct 157/157, `msgsec03` 12, `msgsec04` 2, `msgsec05` 3, `msgsec07` 1, `msgsec10` 5, `msgsec15` 전투 도움말 direct130~183 54, `code.bin` 전쟁 메뉴·전투 지형/자원/시간/군 표기 36이다. 일기토 전용 `msgsec12` mixed-pointer 본문은 중복하지 않고 기존 정본 `Extracted_Text\duel_text_review_v172.html`로 연결했다.
- 사용자 첨부 화면은 `M02-120=敵部隊ﾆ偽ﾉ伝令...→적 부대에 거짓 전령...`, `M02-100=成功ﾃﾞｽ...→성공입니다...`, `M02-136=祈祷ｼﾏｽｶ？→기도할까요？`로 각각 실제 direct pointer에 대응시켰다.
- immutable Original은 CP932로 별도 복호화하고 current v180은 effective Hangul alias map으로 reverse readback했다. 270행의 현재 일본어 kana 잔존 의심 0, ASCII `?` 0, ID 중복 0을 확인했다. generator 재실행 HTML SHA-256 결정성도 PASS했다.
- 증거 JSON은 `analysis\battle_text_review_v180_report.json`, 재생성 도구는 `tools\build_sangokushi2_battle_text_review_v180.py`다. 생성 전 exact v180 report manifest와 현재 Patch 83파일을 대조했으며 `Sangokushi 2 Patch`, Original, Rebuild, Backup은 수정하지 않았다. 최신 Patch 권위는 계속 **v180-intermediate**다.
- 사용자 후속 요청으로 각 행의 바이트 열을 `현재 사용 / 현재 span·slot B`, `여유 B` 형식으로 확장했다. 고정 code/string span은 실제 슬롯 여유를 표시하고, pointer 재지정형 Message는 현재 span 여유와 함께 초과 시 재배치 가능 여부 및 해당 `msgsecXX.dat`의 **0x10000 로더 상한 기준 공용 여유**를 별도로 표시한다. 바이트 여유가 화면 줄폭 여유를 의미하지 않는다는 주의도 HTML에 명시했다. 전체 270행은 음수 여유 0, 고정 88행/재배치형 182행이며 재생성 결정성을 통과했다.

## 2026-09-05 — 무장 열전 전체 검수 DB 생성 (Revision 변경 없음)

- 사용자 검수용 **`Extracted_Text\biography_text_review_v180.html`**을 생성했다. 실제 runtime 열전 source를 구조별로 전수 수록했으며 총 **978행**이다: `msgsec13` record 0~335 **336개**, `msgsec19` record 336~797 **462개**, `msgsec20` record 840~1019 **180개**. 열전 route가 없는 record 798~839는 상단 설명에 명시하고 행에서는 제외했다.
- 각 행은 `B####` ID, 한글/일본어 무장명과 읽기, source/direct index/Original·v180 pointer, immutable 일본어 원문, 현재 effective font alias를 반영한 실제 pointer readback, `현재 사용 / 현재 physical span B`, 행 내부 여유, Message 파일 공용 안전 추가 여유, 줄별 폭과 **19.5칸×4줄** 판정을 표시한다. 현재 repack된 열전은 padding 없는 연속 span이므로 개별 행 여유는 0B이며, 더 긴 수정은 구조 인식 rebuild/pointer redirect가 필요하다. `< 0x10000` loader gate를 반영한 파일 공용 안전 추가 여유는 `msgsec13=20,548B`, `msgsec19=3,070B`, `msgsec20=42,836B`다.
- 검색 및 source/번역 상태/첨부 화면/폭 초과 필터와 raw hex 토글을 제공한다. 첨부 화면의 **이적=B0003**, **자허상인=B1016**으로 대응시켰다. 정적 결과는 한글 readback **808**, 일본어/혼입 **166**, 의도적 빈 entry **4**(`msgsec20` direct166~169: 병사/무장/더미/더미), layout 초과 0, 음수 바이트 여유 0, ASCII `?` 0, ID 중복 0이다. 일본어/혼입 166개는 번역 완료로 숨기지 않고 향후 검수 대상으로 그대로 노출했다.
- 구조 감사에서 `msgsec13` 첫 열전 **B0000 아회남**은 physical block에 `남만왕…`이 저장되어 있지만 runtime pointer가 block 시작+2를 가리켜 실제 readback은 첫 글자 `남`이 빠진 **`만왕…`**부터 시작함을 확인했다. 이번 요청은 검수 DB 생성이므로 Patch는 수정하지 않고 해당 행에 경고만 기록했다.
- 증거 JSON은 `analysis\biography_text_review_v180_report.json`, 재생성 도구는 `tools\build_sangokushi2_biography_text_review_v180.py`다. HTML SHA-256=`C6BB56DF796FA096A0BEAA76F1C1933CA3D073E5D19149538360550986DC5F4A`, report SHA-256=`3145303CD14D992EF6759F98CF00AE36095CBAA64E1C4135B961C010E2F35207`; 연속 2회 재생성 결과가 byte-exact라 결정성을 통과했다. 생성 전후 v180 complete manifest와 Patch 83파일의 exact 일치를 확인했으며 Patch/Original/Rebuild/Backup은 수정하지 않았다. 최신 Patch 권위는 계속 **v180-intermediate**다.

## 2026-09-05 — 전쟁 텍스트 DB 누락 재감사·448행 재구성 (Revision 변경 없음)

- 사용자 Citra 화면의 `매복한 조흥이(가) / 배반했습니다`는 `msgsec02`가 아니라 **`code.bin 0x1D05C8` 독립 C-string**이다. immutable Original은 `埋伏していた%sは / 寝返りました`, v180 actual readback은 `매복한 %s이(가) / 배반했습니다`이며 **28/29B, 여유 1B**다. 이 항목은 과거 v52 runtime inventory에 이미 있었지만 최초 270행 전쟁 DB가 v164 등 “수정 target 목록”을 중심으로 구성되어 누락됐다.
- 같은 실패 class를 전체 source에 적용해 재감사했다. 복구분은 `msgsec03` 이미 번역된 전투 sibling **18**, `msgsec09` 전투 결과·보상·포로 처리 **32**, `msgsec10` 전쟁/전투 help sibling **58**, `code.bin` 전투 중 고정 대사 **19**, 전쟁 준비·침공·증원 **19**, 명령·특기·자원 label **32**로 총 **178행**이다. 따라서 `battle_text_review_v180.html`은 **270→448행**으로 재구성됐다.
- `msgsec09`는 flat count table이 아니라 88-word dual-use header이므로 direct N을 `word[N+1]`의 BYTE pointer로 읽었다. direct0..30과, direct가 가리키지 않지만 포로 거부 분기에 속한 physical battle block26을 포함했다. editor UI가 시작되는 direct31+는 제외했다. `msgsec10 direct115..120`은 실제 문장이 아니라 연속 `05 05 05` separator alias라 제외했고 direct121부터 실제 특기 설명을 다시 포함했다. `msgsec11`은 시나리오 선택/서술, `msgsec12`는 별도 `duel_text_review_v172.html` 정본이라 중복하지 않았다.
- 확장된 current v180 readback에서 일본어/CJK 잔존 의심은 **8행**이다: `M03-012~015`의 `年/月/日目`, `M09-D029`의 깨진 `儻`, `M10-091/092/096`의 일본어 전투 정보·위임 help. 이번 요청은 DB 누락 확인이므로 이를 숨기거나 Patch에서 수정하지 않고 HTML 필터에 노출했다. ASCII `?` 0, ID 중복 0, 음수 바이트 여유 0이다.
- 재생성 도구는 `tools\build_sangokushi2_battle_text_review_v180.py`, 증거는 `analysis\battle_text_review_v180_report.json`, 출력은 `Extracted_Text\battle_text_review_v180.html`이다. HTML SHA-256=`B5A58693A5FE241AC7C5D6E9A02A65B19A3AD06B4030986504D2ED160C34C40A`; 연속 2회 재생성 결과 HTML/JSON 모두 byte-exact였다. generator가 작업 전 exact v180 manifest 83/83을 확인했고, Patch/Original/Rebuild/Backup은 수정하지 않았다. 최신 Patch 권위는 계속 **v180-intermediate**다.

## 2026-09-05 — v181-intermediate #173 일반 공통 대사 문구 개선·안전 donor 재감사

- `Extracted_Text\common_dialogue_review_v180.html`의 고정 Cxxx ID와 로컬 `Github_Issue\Issue173.html`을 대조해 C000/C002/C003/C015/C021/C186/C187/C196/C202/C208/C243 **11건**을 수정 대상으로 확정했다. 모든 항목은 immutable Original C-string allocation 안에 들어가 relocation 없이 fixed-position으로 처리 가능했다. 최종 사용량/용량은 C000 25/28B, C002 25/27B, C003 20/23B, C015 28/29B, C021 29/29B, C186 32/35B, C187 41/43B, C196 35/35B, C202 27/27B, C208 25/31B, C243 30/31B다.
- 의미 보존을 위해 새 글리프가 필요한 문자는 `풀/랄/냐/렇` 4자였다. 기존 `렇=994F`는 과거 Citra 실패 alias라 재사용하지 않았다. 첫 donor 감사 뒤 과거 분석 JSON의 단순 후보 목록까지 역사 배정으로 잘못 간주하면 `냐` strict candidate가 0이 되는 문제를 발견해, 역사 예약은 **실제 applied `*report.json` 배정**만 수집하고 donor audit/plan/targets의 rejected candidate는 제외하도록 감사기를 보정했다.
- 최종 donor는 `풀=8D5A/physical1424(stock 校)`, `랄=97E4/physical3441(stock 嶺)`, `냐=8B5D/physical1051(stock 犠)`, `렇=914E/physical2164(stock 鮮)`이다. `풀/랄/렇`은 applied historical assignment 0인 fresh donor다. `냐=8B5D`는 strict-fresh 후보가 없어 v168 폐기 실험 재사용 예외를 적용했다. grep/구조 감사상 역사 배정은 **Citra 회귀로 폐기된 v13 safe-alias report의 `경=8B5D` 한 번뿐**이고 이후 성공 revision에는 계승되지 않았다. exact v180에서 owner/code-ref/Message·Scenario-ref 모두 0, known-failed 아님을 독립 verifier가 재확인한다.
- atlas bleed 방지는 runtime-unreferenced 이웃 edge만 최소 수정했다: physical1497 top 2px(`풀`), 3514 top 4px(`랄`), 1050 right 5px(`냐`), 2237 top 5px(`렇`). 최종 font changed cells는 정확히 `[1050,1051,1424,1497,2164,2237,3441,3514]`다. 다른 font cell은 v180 byte-exact다.
- C005는 게임 데이터 결함이 아니었다. v180 game raw를 byte-exact 보존했고 `해=8B6C/physical1066` current font cell이 Sam3 `해` x=1,y=0과 pixel-exact임을 확인했다. 사용자 Citra에서도 `기간을 입력해 주십시오`가 정상 출력된 사실을 반영해 review generator/API에 **runtime-confirmed review override**만 추가했다.
- exact v180 83파일은 `analysis\v181_issue173_common_dialogue_baseline\PatchSnapshot`에 봉인했다. v181 실제 Patch diff는 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 2개뿐**이며 PNG/기타 G1T/Message/Scenario/Original/Rebuild/Backup은 수정하지 않았다. 최종 SHA-256은 `code=C3DBB60CC42295F65808D2CA5453DA5C26BC11144A83F80A0E7D98F31BC320F4`, `font=E375D9D9BDCCB9B19B2226879205DD836C97482BA1E782F91773B91FB53EBD16`이다.
- 검증 순서는 donor audit → builder 재생성 → deterministic `--check` → 독립 verifier였고 모두 PASS했다. 독립 verifier는 83-file manifest, 비대상 파일 byte-exact, 11 target readback/format/slot, donor valid-JIS·stock identity·current owner/ref=0·historical exception, packed glyph pixel-exact, guard changed-cell exact set, C005 game byte-exact를 확인했다. 최신 정적 Patch 권위는 **v181-intermediate**, Citra 실화면 재확인은 pending이다.

## 2026-09-05 — v182-intermediate #174 실패 `욱=989C`·공통 대사 제어코드 전수 감사

- #174의 `더<98><9C> 업무에 / 힘쓰겠습니다` actual source는 `code.bin 0x16C9F0`이며 `<98><9C>`는 제어코드가 아니라 과거 Citra 실패 alias `욱=989C`였다. 요청대로 `더욱 열심히 / 임하겠습니다` 24B를 27B fixed C-string allocation에 넣었다.
- 같은 오류 class를 공통 대사 전체에서 감사해 msgsec07 live `header166=우리 진용이 더욱/강화되었군`, `header171=더욱 충성을 다해/모시겠습니다`, `header198=더욱 열심히/임하겠습니다`로 보정했다. 사용자 지적 `M07-B124`는 absolute-BYTE header가 직접 읽지 않는 stale physical copy지만 same-width `989C→89BC`로 정리했다. 최종 msgsec07 `989C` 잔존은 0이다.
- `M07-B123`의 `02 01 + 네`는 NAME1 atomic token `02 01 C8`의 과거 절단 흔적이다. 그러나 absolute-BYTE active fragment 전수 대조에서 이 malformed sequence를 읽는 active header는 0이었다. msgsec07의 같은 stale remnant 8곳도 전부 비활성이라 geometry를 움직여 청소하지 않았다.
- active msgsec04/06/07/08에서 NAME1/2/3, `02 28`, `02 2D`, ESC K/H/Cx를 Original과 source-aware 비교했다. active high-byte placeholder=0, malformed NAME prefix=0. control skeleton mismatch는 의도적 standalone redirect인 msgsec07 header142(v165 군선 건조 확인)와 header322(v71 아이템 지급 확인) 두 건만 allowlist했다. 리뷰 generator도 msgsec07 runtime 참조를 absolute BYTE 기준으로 바로잡아 M07-B124를 stale physical로 표시한다.
- exact v181 83파일을 sealed baseline으로 사용했고 v182 Patch diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개뿐이다. builder `--check`와 독립 verifier를 반복 실행해 PASS했다. 최종 SHA-256은 `code=275AA00778D17626F7C776EFF014332B0B7A066C88B14FB0E25D9884E8F138EA`, `msg07=5709978916F2386CABA63AD4B90F6B032C634411E45C92E3CB6991B71C4A01AD`. 최신 정적 Patch 권위는 **v182-intermediate**, Citra 재확인 pending이다.

## 2026-09-05 — v183-intermediate #174 header166 `진용→세력` 후속

- 사용자 지적대로 header166 원문 `我ガ陣容ガサラニ / 強化サレタゾ`와 현재 번역을 다시 대조했다. immutable Original header166은 `0x11F6`, body 23B지만 current v182 header166은 이미 `0x3CAE`의 relocated 27B body를 가리키므로 Original 23B 초과 자체는 현재 allocation overflow가 아니다. v182에서 이 row에 한 변경은 `욱=989C→89BC` 2B뿐이었다.
- 사용자 문구 **`우리 세력이 더욱 / 강화되었군`**은 current mapping으로 27B라 현재 27B body에 exact fit한다. pointer/header/파일 크기를 움직이지 않고 live `0x3CB3`의 `진용=88D3894F`만 `세력=8BDC8C97`로 same-width 4B 치환했다. 리뷰 혼동 방지를 위해 stale B124 `0x188C`도 같은 4B를 교체했다.
- exact v182 83파일을 `analysis\\v183_issue174_header166_wording_baseline\\PatchSnapshot`에 봉인했고 v183 Patch diff는 `RomFS/Message/msgsec07.dat` 1개뿐이다. 실제 byte diff 8B, 388-word header byte-exact, size 60406B 유지, Original body 23B / v182 body 27B / v183 target 27B를 독립 verifier가 확인했다.
- builder deterministic `--check` + 독립 verifier PASS. 최종 `msgsec07 SHA-256=48F2ECD6A244898C5938C32EA2B33D588343D0712A358EF7BE00A03DCEAFA8A8`. 최신 정적 Patch 권위는 **v183-intermediate**, Citra 확인 pending이다.

## 2026-09-06 — v184-intermediate #175/#176 등용 조언·동적 도시명 조사 전수 개선

- 로컬 `Github_Issue\\Issue175.html`/`Issue176.html`과 각 assets PNG를 확인했다. #175 Citra 화면의 C264 `쉽게는 / 통하지 않습니다` actual source는 `code.bin 0x1D1508`; 사용자 1안 **`쉽지는 않을 / 것 같습니다`**를 23B로 26B fixed slot 안에 적용했다.
- #176 Citra 화면의 `운남로 침공했습니다` actual source는 `code.bin 0x158488`의 독립 suffix `へ攻めて来ました`; **`에 침공했습니다.`** 16B로 exact-width 교체해 동적 도시명 받침과 무관하게 만들었다. 이 source가 `common_dialogue_review_v180.html`에서 빠졌던 원인은 짧은 assembler suffix가 기존 later-target keyword 수집기에 포함되지 않았기 때문이며, 기존 C000~C431 ID를 유지한 채 **C432** 보충 행으로 추가했다. C264는 C264 그대로다.
- 같은 오류 class를 공통 runtime에서 전수 감사해 도시 목적지/도시 주제에 고정 `로/(으)로/은(는)/가/를`이 붙는 proven source를 함께 정리했다. code 9개 sibling은 요격(`0x157228/0x15724C`), 위임 공격 prompt(`0x156A6C`), 영지 전환(`0x157264`), 위임 무장 침공(`0x157D70`), 증원 suffix(`0x158468`), 공백지(`0x15D920`), 풍작(`0x15F0B4`) 등이며 `에/에서` 또는 조사 없는 `도시명, ...` 구조로 바꿨다. msgsec07 active/stale city 관련 physical copy도 13개 op로 정리했고 msgsec08 공백지 active row 1개도 수정했다. 인물/아이템/직책/위임 방식 변수의 `%s로/%s를/%s는`는 도시 class가 아니므로 보존했다.
- exact v183 전체 Patch 83파일을 `analysis\\v184_issue175_176_city_particles_baseline\\PatchSnapshot`에 봉인했고 실제 게임 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec08.dat` 3개**다. font/PNG/G1T 및 나머지 80개 게임 파일, Original/Rebuild/Backup은 v183 byte-exact다.
- builder 실행 → deterministic `--check` → 독립 verifier 모두 PASS했다. verifier는 Patch 83파일, code target 10개, Message op 14개, C264 ID 유지, #176 review C432 보충, font/PNG/G1T 무변경을 확인했다. 최종 SHA-256은 `code=081E50FCA175D4EF41EF221308349D23880C9A5C9AC2FE7ADABCF74B1DB75487`, `msg07=38BA305AA69EBC5CD45063D73F99ED5AE5F9781C22511107784BC4D8C7B83CA4`, `msg08=413B4D12A3E0423B53D5096BBB4C70A23FCBEADA09652B6EE31B5C26B692C578`. 최신 정적 Patch 권위는 **v184-intermediate**, Citra 확인 pending이다.

## 2026-09-06 — v185-intermediate #177 C250 복원·C366 review anchor 교정

- 업데이트된 `Github_Issue\\Issue177.html`을 다시 읽고 현재 v184에서 재검증했다. C250 `code.bin 0x1D12D0`의 formal authority는 원래 **`그럭저럭 성과는 / 얻을 수 있을 듯합니다`**인데 current만 `정보는`으로 바뀌어 있어 사용자 요청대로 `성과는`으로 복원했다. `성과` target은 37B/37B exact-width이며 existing glyph **`성=8BCC`**를 사용하므로 신규 donor/font 수정은 없다.
- C366의 review `ｬ.`는 게임 번역 손상이 아니었다. historical target offset `0x1D3011`은 immutable Original의 `FF FF FF` padding을 포함하는 slot 시작이고 visible string은 `0x1D3014`부터다. 후대의 앞 문자열 `발생하였습니다.`가 그 3B padding을 사용해 current `0x1D3011`에서 읽으면 앞 문자열 tail `AC 2E`가 CP932 `ｬ.`처럼 보였다.
- 실제 runtime pointer도 정적 증명했다. `code.bin 0x1E6020`의 32-bit pointer는 Original/current 모두 **`0x2D3014`**(file `0x1D3014`)이며 `0x2D3011` literal pointer는 0개다. 따라서 C366 actual current는 **`제가 참된 주군을 / 알려드리지요……`**로 정상이고 게임 바이트 수정은 불필요하다. review generator만 C366 ID를 유지한 채 canonical offset `0x1D3014`를 표시하도록 수정했다.
- `common_dialogue_review_v180.html`의 Message formal 957행은 v53 historical authority라 후속 wording/alias/pointer/repack 때문에 current와 exact byte가 다를 수 있다. 기존 `(exact formal bytes 미검출)`을 오류처럼 표시하지 않고 **`v53 formal differs from current Patch` / `후속 revision에서 변경/재배치됨 — current binary view 확인`**으로 의미를 명확히 했다. 현재 통계는 byte-identical 553 / changed-or-repacked 404이며 current binary 840행은 별도 실제 바이너리 view로 유지한다.
- exact v184 전체 Patch 83파일을 `analysis\\v185_issue177_common_review_baseline\\PatchSnapshot`에 봉인했고 실제 게임 변경은 **`ExeFS/code.bin` 1개뿐**이다. v184 대비 C250 slot 내 실제 byte diff는 4B이며 font/PNG/G1T/Message/Scenario와 나머지 82개 게임 파일, Original/Rebuild/Backup은 v184 byte-exact다.
- builder → deterministic `--check` → stable common review verifier → v185 독립 verifier 모두 PASS했다. 최종 `code.bin SHA-256=5208F8F98240EF304CC0397ADFCE801648CB3555E225743D656DC24FB49EB465`; 최신 정적 Patch 권위는 **v185-intermediate**, Citra 실화면 재확인은 pending이다.
