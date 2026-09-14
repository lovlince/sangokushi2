# 삼국지 2 3DS 한글패치 — 현재 인수인계

마지막 갱신: 2026-09-14

새 채팅/새 Agent는 이 문서와 `PROJECT_RULES.md`, `WORK_LOG.md`, `번역.md`를 먼저 읽는다. 사용자가 `#018, #027`처럼 **이슈 번호만 말하면 웹페이지를 조회하지 말고 URL도 다시 묻지 않는다.** 작업 루트의 `Github_Issue\IssueNNN.html`(3자리 0-padding)과 `Github_Issue\IssueNNN_assets\*.png`만 열어 본문과 실제 스크린샷을 함께 확인한다. 로컬 HTML/PNG를 확인하지 못한 상태에서는 본문만으로 추측 수정하지 않는다. **웹 확인은 사용자가 해당 턴에서 명시적으로 웹 검증을 요청한 경우에만 수행한다.**

- **현재 Patch 최종 기준본: v252-intermediate (Issue #224 매복 문구 4안, 정적 검증 완료·Citra 확인 pending)** — exact v251 Patch 84파일을 `analysis\v252_issue224_ambush_one_line_baseline\PatchSnapshot`에 봉인했다. actual owner는 `RomFS/Message/msgsec09.dat` **M09-D016 / mixed header word[17] BYTE=0x0217**, fixed 20B span `0x0217..0x022A`, separator `0x022B..0x022D`, 다음 direct `word[18]=0x022E`다. 사용자 확정 4안 **`<NAME1>군 <NAME2>,매복이었다`**를 적용했으며 기존 줄바꿈을 쉼표로 바꾸는 **`0x0220: 0A→2C` 단 1바이트 수정**이다. 88-word header, word[17]/[18], 모든 `05 05 05` separator, file size 2845B를 보존했고 parent-WORD target landing은 0건이다. 군주3자/장수4자 가정의 정적 폭은 **14.0/15.0 units**다. v251 대비 game diff는 `msgsec09.dat` 1개뿐, SHA-256=`63CE1A0F5BC296003AAD1848799DB6603AD9B4DFFA07400AD2C639FF94F8E377`; 나머지 83파일은 v251 byte-exact다. builder/독립 verifier/deterministic check/common review 2280행/battle review 520행 모두 v252 authority로 PASS. report=`analysis\v252_issue224_ambush_one_line_report.json`.
- **직전 Patch 최종 기준본: v251-intermediate (Issue #226 원군 요청 문구 개선, 정적 검증 완료·Citra 확인 pending)** — actual owner는 `msgsec07.dat` M07-B208 / `word[330] BYTE=0x2DD4`, 49B fixed span이며 **`<NAME1>님, <NAME2>군이 %s에\n원군을 요청해 왔습니다`** 42B+padding7을 적용했다. `해`는 stale `88E7`이 아니라 current 실사용으로 검증된 **`8B6C`**가 권위다. v250 대비 game diff는 `RomFS/Message/msgsec07.dat` 1개, SHA-256=`D76274E18310AE626D6BF27C16DDDF6C4A30B73FD87FC915DAC4D761BBFCF87C`; header/pointer/separator/file size 보존, relocation 없음. report=`analysis\v251_issue226_reinforcement_request_report.json`.
- **직전 Patch 최종 기준본: v250-intermediate (Issue #222/#225 대사 개선, 정적 검증 완료·Citra 확인 pending)** — #222는 32B local code slot을 넘는 `두려워` 안을 배제하고 **`<NAME1>은/는 <NAME2>님에게\n항복했습니다`**를 적용했다. #225 actual owner는 `msgsec04 direct20 / word[20] BYTE=0x0365`, 40B span이며 **`함정을 파두면\n적은 자멸할 것입니다`** 34B+padding6을 적용했다. v249 대비 game diff는 `ExeFS/code.bin`, `msgsec04.dat`, `msgsec06.dat` 3개이고 SHA-256=`code 4B7FE99F166795369AB45E6CC63023AF6C247E68D4E97B1D044C0B56F12021C6`, `msg04 BC8BB5F4B2B19B15D9542D4D016F141CFBC98797C0CA0B5E59D32D82E69FE9AF`, `msg06 F21AA9D96FA67E61CFF957EEEBAC80A0054805A3BD170DA5FF9F7E504758D68C`. report=`analysis\v250_issue222_225_text_cleanup_report.json`.
- **직전 Patch/이미지 기준본: v249-intermediate (`title_up_002.png` 이미지 미세 조정, 정적 검증 완료·Citra 확인 pending)** — exact v248 Patch 84파일을 `analysis\v249_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 명시한 64×16 RGBA `title_up_002.png`(SHA-256=`113831FD024D550DBC7F8EDE121FA500D61CA3776E6ADFE7ED3E2429B238E690`)만 `RomFS/StartMenu/title_up.g1t` texture index 2에 1:1 재삽입했다. 공식 v1.1 3-texture 구조, header와 index 0/1 payload는 v248 byte-exact이고 target pixel readback은 PNG와 exact다. v248 대비 game diff는 `RomFS/StartMenu/title_up.g1t` 1개뿐, SHA-256=`4DBA69202B1CC028610E3DDFCCB1CC7EBADAE2910BFCA7FE657EF32273B6D364`; code/font/Message/Scenario와 나머지 83파일은 v248 byte-exact다. 이미지 생성 없음, builder/독립 verifier/deterministic check PASS. Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 미수정. report=`analysis\v249_title_up_002_image_update_report.json`.
- **직전 Patch 최종 기준본: v248-intermediate (Issue #223 물자 도착 문구 띄어쓰기, 정적 검증 완료·Citra 확인 pending)** — exact v247 Patch 84파일을 `analysis\v248_issue223_spacing_baseline\PatchSnapshot`에 봉인했다. actual owner는 `RomFS/Message/msgsec07.dat` **M07-B234 / word[371] BYTE=0x3378**, fixed 41B span `0x3378..0x33A0`, separator `0x33A1..0x33A3`다. 사용자 요청대로 **`<NAME1> 님, %s에서 물자가 / %s 에 도착했습니다` → `<NAME1>님, %s에서 물자가 / %s에 도착했습니다`**로 두 내부 공백만 제거했다. visible payload는 38B이고 3B tail padding으로 41B span을 유지한다. msgsec07 388-word header 전체, `word[371]=0x3378`, 모든 separator/file size 60406B를 보존했고 target span 내부 BYTE header landing=0, legacy parent-WORD 177개 `*2` landing=0이다. v247 대비 game diff는 `RomFS/Message/msgsec07.dat` 1개뿐, SHA-256=`9ECC9E6F73254DDA02B85BA5D0213014270F17FDF2BCC87CE219A5FA34A133B8`; 나머지 83파일은 v247 byte-exact다. builder/독립 verifier/deterministic check PASS, common review 2280행/battle review 520행 v248 PASS. report=`analysis\v248_issue223_spacing_report.json`.
- **직전 Patch 최종 기준본: v247-intermediate (Issue #221 군주 계승 문구 쉼표/축약, 정적 검증 완료·Citra 확인 pending)** — exact v246 Patch 84파일을 `analysis\v247_issue221_ruler_succession_baseline\PatchSnapshot`에 봉인했다. actual owner는 `RomFS/Message/msgsec09.dat` **M09-D028 / word[29] BYTE=0x0359**, fixed 29B span `0x0359..0x0375`, separator `0x0376..0x0378`다. 사용자 1안 **`<NAME1>, 뜻을 이어 / 군주가 됐다`**를 채택했다. `됐=89A7`은 current safe map에 이미 존재하고 known-failed가 아니므로 font 수정은 없다. visible payload는 26B이며 padding 3B를 포함해 기존 29B span을 그대로 유지한다. 88-word mixed header, `word[29]=0x0359`, 다음 `word[30]=0x0379`, 모든 separator/file size 2845B를 보존했고 parent-WORD prefix 41개 중 target span 내부 착지는 0건이다. v246 대비 game diff는 `RomFS/Message/msgsec09.dat` 1개뿐, SHA-256=`531580C7A67AC1F3B3CB1188D21EFC9E35F39530A7C4402F60A6EDFBA1713F20`; 나머지 83파일은 v246 byte-exact다. builder/독립 verifier/deterministic check PASS, common review 2280행/battle review 520행 v247 PASS. report=`analysis\v247_issue221_ruler_succession_report.json`.
- **직전 Patch 최종 기준본: v246-intermediate (Issue #220 장수 자연사 `武将` 혼입·조사 문구 개선, 정적 검증 완료·Citra 확인 pending)** — exact v245 Patch 84파일을 `analysis\v246_issue220_natural_death_text_baseline\PatchSnapshot`에 봉인했다. 실제 자연사 owner는 `code.bin 0x15DC10=武将`, `0x15DC18=태수`, C055 `0x15DC20`이며 각각 ARM ADR `0x15DA9C/0x15DAA0/0x15DAB0` 단 1개씩으로 같은 함수 전용이다. local 8B label slots는 **` 무장` / ` 태수`** 5B+NUL로 바꾸고 C055는 **`%s%s의%s\n%s, 사망했습니다`**를 25/25B exact-fit으로 적용했다. NUL/zero guard 2B/`FF FF` sentinel, ADR/caller/code size를 보존하며 relocation과 file growth는 없다. `msgsec08`의 M08-B048 `0x8C4`는 BYTE/WORD ref 0인 stale copy라 보존하고 active M08-B169/B170/B171만 같은 `, 사망했습니다` 규칙으로 fixed 25/36/35B 안에서 수정했다. 128-word header, BYTE pointers 43/44/45, separator/file size를 보존했고 B171 dual-use `word[84]*2=0x1EC6` landing 및 그 이후 route는 v245 byte-exact다. v245 대비 game diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec08.dat` 2개뿐, SHA-256=`code FBEBE5BE231FCD58EC1677062C0B35B4136C67CF2D43CA3FF2A4CEE192A27D78`, `msg08 56A52D986E849449D1A4E833473D46045FAB1047D00783F7D2696BD637668CEA`. builder/독립 verifier/deterministic check PASS, common review 2280행/battle review 520행 PASS. report=`analysis\v246_issue220_natural_death_text_report.json`.
- **직전 Patch 최종 기준본: v245-intermediate (M02-032 `슨` 안전 donor 재배정, 정적 검증 완료·Citra 확인 pending)** — exact v244 Patch 84파일을 `analysis\v245_m02_032_seun_donor_baseline\PatchSnapshot`에 봉인했다. M02-032 `대체 무슨 일인가 / 이래서는 움직일 수 없다`의 actual runtime은 `msgsec02.dat direct32 / pointer 0x129B`, 40B span이며 `슨`이 과거 Citra-failing alias `9972/physical3704`를 다시 사용하고 있었다. current v244 Message/Scenario 전수에서 live `9972`는 이 한 곳뿐이고 code raw 2건은 ARM 명령 바이트라 비대상이다. 새 donor는 **`95D7` / stock `勉` / physical3052**로, current code text ref=0, Message/Scenario ref=0, current Hangul owner=0, known-failed 아님, stock pixel-exact, 인접 live neighbor 없음이 검증됐다. physical3704의 기존 정상 `슨` 도트를 physical3052로 pixel-exact 복사하고 비활성 아래 guard physical3125 top row만 투명화했다. M02-032은 `9972→95D7` 2B만 바꾸며 pointer/header/separator/file size를 보존한다. v244 대비 game diff는 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec02.dat` 2개뿐이고 SHA-256=`font 4FAF492F93E0B46F36EA4DB7BE6C1DE041CE812CBD63751F5BFA2AB0803C9C03`, `msg02 20467714E66FC113450CC6CEC9B9DADB8306C7AB13ABA089E88164286F8A9B2E`. builder/독립 verifier/deterministic check PASS, battle review v180 520행 verifier PASS. `9972` live Message/Scenario ref=0, `95D7` ref는 msgsec02 한 곳뿐이다. 이후 effective map은 **`슨=95D7`**가 권위이며 `9972` 재사용 금지. report=`analysis\v245_m02_032_seun_donor_report.json`.
- **직전 Patch 기준본: v244-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra 확인 pending)** — exact v243 Patch 84파일을 `analysis\v244_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자 지정 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v243 `RomFS\StartMenu\title_up.g1t` texture index 2에 1:1 삽입했다. source PNG SHA-256=`9482E70D0B33AF8393B8D1F529193F32731D54BC53D5FC1B9D6635808619B078`. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v243과 byte-exact이고 index 2 readback은 pixel-exact다. v243 대비 변경 게임 파일은 이 G1T 하나뿐이며 최종 SHA-256=`59BCD30A51F760633F34AE22F70C5AAB4D484220FE40C47B8F36C23772F545D5`; v243 code/font/Message와 나머지 83파일은 동일하다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v244_title_up_002_image_update_report.json`.
- **현재 Patch 최종 기준본: v243-intermediate (Issue #218/#219 징병 상단 안내 + 시혜 결과 띄어쓰기, 정적 검증 완료·Citra 확인 pending)** — exact v242 Patch 84파일을 `analysis\v243_issue218_219_text_fix_baseline\PatchSnapshot`에 봉인했다. #218 Citra 화면의 `これ以上同時に選択できません` actual owner는 `RomFS/Message/msgsec17.dat 0x2F..0x43`의 21B physical runtime span이다. 번역 DB formal `더 이상 동시에 선택할 수 없습니다`는 33B라 overflow하므로 의미 보존 compact **`더 선택할 수 없습니다`**를 21/21B exact-fit으로 적용하고 current header `[4,71,23,40]`, `0x44` separator, v91 append `0x47..`, 92B file size를 보존했다. 과거 v141 질문/확인 popup sweep과 common review current-binary scope에 msgsec17 비질문형 상단 안내가 포함되지 않아 누락됐으며, 같은 인접 active direct `word[3] BYTE=0x28`의 `%s軍`도 4B exact-fit **`%s군`**으로 함께 정리한다. v243부터 common review에 proven msgsec17 runtime 4행을 추가하고 #218을 **M17-P003**, 인접 sibling을 **M17-D003**으로 노출한다. #219 actual owner는 `msgsec07 header[179] BYTE=0x1A28`의 18B active block이며 `효과가 없었 습니다`를 **`효과가 없었습니다`** 17B+pad1로 교정했다. 동일 정상 sibling `header[175] BYTE=0x19AA`는 byte-exact 보존한다. v242 대비 game diff는 `RomFS/Message/msgsec07.dat`, `msgsec17.dat` 2개뿐이고 SHA-256=`msg07 301BCFBDF99944FB79486C07DAF805EDA561CC58112B4EB3AA9ADB734F4573C6`, `msg17 6A11A710DA1FB2017494FF3EBCB775E85AAB4EB68D76409044494E71BBF326D3`; code/font/title_up 및 나머지 82파일은 v242 byte-exact다. builder/독립 verifier/deterministic check/common verifier가 모두 PASS했고 common review는 **2280행(code441/formal957/binary882)**으로 확장됐다. report=`analysis\v243_issue218_219_text_fix_report.json`.
- **직전 Patch 기준본: v242-intermediate (Issue #216 우길 대사 14B exact-fit 개선, 정적 검증 완료·Citra 확인 pending)** — exact v241 Patch 84파일을 `analysis\v242_issue216_ugil_wording_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin 0x15B6D4`의 owner suffix만 수정했다. 기존 ` 소유 중이다` 12B 대신 사용자 확정 1안 **` 손에 있소이다`**가 현재 effective map에서 정확히 14B라 fixed 14B allocation에 exact-fit한다. 기존 NUL 뒤 같은 allocation의 zero slack 2B를 사용해 NUL을 새 끝으로 옮겼고 pointer/code size/new glyph 변경은 없다. 기대 조립은 **`유명한 <ITEM>, / <PERSON> 손에 있소이다`**. v241 대비 game diff는 `ExeFS/code.bin` 1개뿐이고 SHA-256=`4735E5D55B412DEFB685B3DE1CA75C1EE073FF7FDA506B6B945B63C673033382`; v241 `msgsec09.dat`, v240 `title_up.g1t`, 나머지 83파일은 byte-exact다. builder/독립 verifier/deterministic check PASS, common review는 v242 기준 C440=` 손에 있소이다` 14/14B·여유0으로 읽히며 2276행 PASS. report=`analysis\v242_issue216_ugil_wording_report.json`.
- **직전 Patch 기준본: v241-intermediate (Issue #215/#216/#217 공통 대사 개선 + review coverage 보완, 정적 검증 완료·#215/#217 Citra 확인 pending)** — exact v240 Patch 84파일을 `analysis\v241_issue215_217_text_cleanup_baseline\PatchSnapshot`에 봉인했다. #215 actual owner는 C232, historical `0x1D1068` / current `0x1D1078`, capacity 21B이며 `로 함께\n쳐들어가`를 **`에 함께\n쳐들어가 `**로 바꿔 별도 `주십시오` suffix와 조립 시 `<대상>에 함께 / 쳐들어가 주십시오`가 되게 했다. #216 우길 대사는 v123 embedded literal 3조각(`유명한 ` / `,\n` / ` 소유 중이다`) 조립형이라 기존 common review에서 누락돼 있었고, suffix capacity가 14B인데 사용자 1안/2안이 16B/15B이며 `갖` glyph도 current map에 없어 **게임 바이트는 유지**했다. 대신 stable ID를 보존한 채 C438/C439/C440으로 review에 후첨했다. #217 actual owner는 `msgsec09.dat` M09-D030 / word[31] BYTE=`0x0398`, 25B fixed span이며 `<NAME1>` token을 보존해 **`<NAME1> 일족 / 자취를 감추다`** 22B+pad3으로 수정했다. v240 대비 game diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec09.dat` 2개뿐이고 SHA-256=`code 2D1583EDE8A10A45A84EA5E492D47D2C201408D84B4396F4459B742233802344`, `msg09 8D9D58FCF426E713E4C722BD63CF734EB8C87ACE9D5E53A616262D1E6844A5DD`; v240 `title_up.g1t`는 byte-exact다. builder/독립 verifier/deterministic check PASS, common review는 v241 기준 **2276행(code441/formal957/binary878)** PASS. report=`analysis\v241_issue215_217_text_cleanup_report.json`.
- **직전 Patch/이미지 기준본: v240-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra 확인 pending)** — exact v239 Patch 84파일을 `analysis\v240_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자 지정 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v239 `RomFS\StartMenu\title_up.g1t` texture index 2에 1:1 삽입했다. source PNG SHA-256=`F52C200F10E690A498DE215EB34B6FA4557FB604F66A35B89997AC522FF27877`. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v239와 byte-exact이고 index 2 readback은 pixel-exact다. v239 대비 변경 게임 파일은 이 G1T 하나뿐이며 최종 SHA-256=`4A216AA795469686931B6317C58A26DCEC3E2C81E3D37C2BA703DBAC117E7063`; v239 code/font/Message와 나머지 83파일은 동일하다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v240_title_up_002_image_update_report.json`.
- **현재 Patch 최종 기준본: v239-intermediate (Issue #209/#214 헌제 밀사 대사 개선, 정적 검증 완료·Citra runtime PASS)** — exact v238 Patch 84파일을 `analysis\v239_issue209_214_xiandi_envoy_baseline\PatchSnapshot`에 봉인했다. #209 actual runtime C437은 `역적` prefix + 동적 `<NAME1>` + C064 suffix 조립이며, 제안 1/2/3안의 suffix는 각각 33/31/26B, C064 capacity는 29B라 **3안만 안전**하다. 과거 v211의 copy5 NUL 누락 문제를 재발시키지 않도록 호출 `0x1671E4`만 기존 ABI-compatible copy6 helper `0xF2370`로 바꿔 `역적 ` 5B+NUL을 안전하게 복사하고, suffix를 `을/를 토벌해\n짐을 구해다오`로 교체해 **`역적 <NAME1>을/를 토벌해 / 짐을 구해다오`**로 출력한다. `M08-B112`는 header 비참조 42B 물리 sibling이지만 사용자 요청대로 같은 3안으로 동기화했다. #214 C066 actual owner `code.bin 0x167488`, capacity37B의 `%s%s황제에게서\n%s(으)로 밀사 도착`을 **`%s%s 황제로부터\n%s에게 밀사 도착`** 32B로 교체했다. v238 대비 game diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec08.dat` 2개뿐이며 SHA-256=`code 3F94AC73EA5373CACA5EDAF09265FEF24066DD59DF02D0A6B6C15FE069957979`, `msg08 270B88AA3663CA188DA7561D9D5C73ACB1FC38FFED003058625980344785C6C7`. builder/독립 verifier/deterministic check PASS. common 2273 / battle 520 / Xiandi 39행 read-only review가 v239를 인식하며 C437/C064/C066/M08-B112 새 표기가 정상이다. 사용자 Citra Nightly 2104 실화면에서 #214 `허창 황제로부터 / 장오에게 밀사 도착`, #209 `역적 조조을/를 토벌해 / 짐을 구해다오` 정상 출력을 확인해 runtime PASS. report=`analysis\v239_issue209_214_xiandi_envoy_report.json`.
- **직전 Patch 기준본: v238-intermediate (Issue #211 자연재해 텍스트 점검, 정적 검증 완료·Citra 확인 pending)** — exact v237 Patch 84파일을 봉인하고 `ExeFS/code.bin 0x15EB88`의 역병 확산용 포인터만 기존 한글 `역병` pool `0x2CFB1C`로 redirect했다. 기대 runtime은 **`역병 피해가 확산 중입니다`**다. 메뚜기 떼 확산 포인터 `0x15EB84`는 v210 수정 그대로 정상이며, 홍수 `0x15F074`/태풍 `0x15F244` 실제 formatter와 header-referenced `msgsec08` 복수 지역 대사는 이미 한글이다. `msgsec14.dat`의 `台風/洪水/疫病`은 header 비참조 물리 잔재라 수정하지 않는다. v237 대비 game diff는 `ExeFS/code.bin` 1개뿐, SHA-256=`F5E9D709568F2DFA2808A097CA92429EAE5E89C5ACDD96C11D3C13AF0E7A5E0D`. report=`analysis\v238_issue211_disaster_spread_report.json`.
- **직전 Patch 기준본: v237-intermediate (Issue #212/#213 문장·띄어쓰기 개선, 정적 검증 완료·Citra 확인 pending)** — exact v236 Patch 84파일을 `analysis\v237_issue212_213_text_cleanup_baseline\PatchSnapshot`에 봉인했다. #212 actual owner는 official-update 이후 `code.bin 0x157538`(historical `0x157228`)이며 `%s, %s에\n요격한 듯 합니다`를 **`%s, %s에\n요격한 듯합니다`**로 fixed C-string 수정했다. current Patch 전체 exact `듯 합니다`는 이 1곳뿐이었다. #213 actual owner는 `msgsec07.dat header[151] BYTE=0x168C`; `우리 나라의 힘이 될 자가\n재야에 있어`와 `있을 것입니다`가 separator 없이 붙어 `있어있을 것입니다`가 되던 49B live span을 **`우리나라에 힘이 될 자가\n재야에 있을 것입니다`** 44B+pad5로 교체했다. 388-word header/header[151]/`0x16BD` separator/file size를 보존하며 다른 `우리 나라` 문구는 범위 밖으로 보존했다. v236 대비 game diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개뿐이고 SHA-256=`code 6B6E13A14CAB8D2FA26E8AE37462F0D11379CFE70EFE2CE205A470E105A1AEF7`, `msg07 91558824F55D5353B0541BC4574E2A1F3D7E56715B55FED26B82E074A7D9C03E`; 나머지 82파일/이미지/font는 v236 byte-exact다. builder→독립 verifier→deterministic check PASS. common/battle/Xiandi review resolver는 v237 manifest와 code relocation을 인식하며 read-only 검증에서 2273/520/39행을 유지, C021/M07-B114 새 표기를 정확히 읽는다. Original/Rebuild/`0004000000174D00`/Dummy update/Backup/패키징/이미지는 미수정. report=`analysis\v237_issue212_213_text_cleanup_report.json`.
- **직전 Patch 기준본: v236-intermediate (Issue #208/#210 띄어쓰기 통일, 정적 검증 완료·Citra 확인 pending)** — exact v235 Patch 84파일을 `analysis\v236_issue208_210_spacing_baseline\PatchSnapshot`에 봉인했다. #208은 공식 v1.1 이후 live 공통 침공 formatter `code.bin 0x157BAC=%s%s 군이 %s%s`, `0x157E30=%s%s 군이\n%s%s`의 세력명-호칭 사이 1B 공백만 제거해 **`%s%s군이 %s%s` / `%s%s군이\n%s%s`**로 통일했다. Patch 전체 감사에서 동적 NAME token + ` 군` residue는 0건이고, 이미 정상인 `<NAME1>군이...`, HEX `%s군`은 보존했으며 `%s 군주`는 별도 단어라 제외했다. #210은 `RomFS/Message/msgsec06.dat` header[35] BYTE `0x546`, 20B fixed span의 **`도시가 번영 하겠군요→도시가 번영하겠군요`**를 19B+pad1로 적용했다. msgsec06 header/pointer/separator/file size와 code pointer/instruction은 보존한다. v235 대비 game diff는 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec06.dat` 2개이며 SHA-256=`code D131D1E0511E2313E924BEAA0C8331B6ACB837298929363B6B29D95A567B486E`, `msgsec06 B87A232E88CB2F5FC4B8B8CDEE8879580113FCFF7D744CD5DF2F0CCB5EB8CB21`. `title_up.g1t`와 나머지 82파일은 v235 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. common review resolver/post-v192 relocation authority도 v236까지 확장했고 read-only build에서 2273행, C026/C027/M06-B030 새 표기가 정상 확인됐다. Citra 확인은 pending. Original/Rebuild/`0004000000174D00`/Dummy update/Backup/패키징/이미지는 미수정. report=`analysis\v236_issue208_210_spacing_report.json`.
- **직전 Patch 기준본: v235-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra 확인 pending)** — exact v234 Patch 84파일을 `analysis\v235_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자 지정 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v234 `RomFS\StartMenu\title_up.g1t` texture index 2에 1:1 삽입했다. source PNG SHA-256=`9BF8D3B821B15BFE627F5B3655AA75764C25426BC23E1C18A19892A278B22374`. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v234와 byte-exact이고 index 2 readback은 pixel-exact다. v234 대비 변경 게임 파일은 이 G1T 하나뿐이며 최종 SHA-256=`D82BED0C792ACD5F9372051B1DDDF0D1A8880986F6BB5A041FD4664E199DB791`; v234 code/font/Message와 나머지 83파일은 동일하다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v235_title_up_002_image_update_report.json`.
- **현재 Patch 최종 기준본: v234-intermediate (Issue #205 군주 사망 대사 + #207 띄어쓰기/줄바꿈, 정적 검증 완료·Citra pending)** — exact v233 Patch 84파일을 `analysis\v234_issue205_207_text_spacing_baseline\PatchSnapshot`에 봉인했다. #205 actual owner는 `RomFS/Message/msgsec09.dat` mixed direct29 / word[30] BYTE `0x0379`, fixed span **28B**다. 옛 위험 alias `름=9957` 때문에 `이儻난`으로 보이던 문구를 사용자 확정 **`이름 떨친 <NAME1> / 여기서 죽다...`**로 28/28B exact-fit 치환했으며 현재 안전 alias `름=966C`, `떨=94B3`, `친=975F`, `죽=8CBC`를 사용한다. 신규 font donor는 없다. #207은 `msgsec04 0x031E`의 `함정 을→함정을`(40B span, target39+pad1), `msgsec07 0x1485`의 `성과 는→성과는`(39B, target38+pad1), `msgsec07 0x2E5A`의 `모양 입니다→모양입니다`(32B, target31+pad1), `msgsec02 direct21 0x10E7`의 대사를 **`대장에게 필요한 건 / 무력이 아니라 / 사람을 움직이는 힘이다`** 3줄(55/55B)로 정리했다. 네 Message 파일의 크기/header/pointer/모든 `05 05 05` separator 위치를 보존하며 v233 대비 game diff는 정확히 `msgsec02/04/07/09.dat` 4개다. SHA-256=`msg02 D7A99554ECCE062D95514C6E013E113FEDCB9E439641A01BC0EC244E8EAFDE41`, `msg04 15DDE3C66654FE5F65FE62CF37F3C1C78EA37FE5A62A5493A764A7F04D8D466C`, `msg07 58A5FBFA5CF2429570721D102D65FA312EEA89A26FCC5CA8FFF8CB18D8D944C1`, `msg09 B55C55C8AE5EA6C1F79FABF05D94632531B8A79EFFADFAD2E6804B372CD32692`; 나머지 80파일과 code/font는 v233 byte-exact다. #205로 드러난 common review 누락 원인은 msgsec09 mixed-header class가 원래 범위 밖이었던 것이며, 검증된 `M09-D000~D030 + M09-P026` 32행을 추가해 common review는 **2273행**으로 확장했다. `open_common_dialogue_review_v180.bat`/generator/server/verifier는 v234 authority 대응, battle review는 520행 PASS이며 residue는 6건으로 감소했다. builder/check/독립 verifier/common review/battle review 모두 PASS. report=`analysis\v234_issue205_207_text_spacing_report.json`.
- **직전 Patch 기준본: v233-intermediate (Issue #204/#206 전투 정보·연합 UI 및 주변 누락 보완, 정적 검증 완료·#204 `적무장/아군무장` Citra PASS·#206 pending)** — exact v232 Patch 84파일을 `analysis\v233_issue204_206_ui_text_cleanup_baseline\PatchSnapshot`에 봉인했다. #204 actual code UI owner `0x1CF908=敵武将`, `0x1CF910=味方무장`을 **`적무장 / 아군무장`**으로 바꾸고, 인접 live sibling `0x1CF91C=外`도 **`외`**로 보완했다. 같은 화면 계열의 active `msgsec10` direct91/92/96도 각각 **`아군 무장 정보. 1턴에 여러 번 확인.` / `주변 적 무장 정보. 지력이 높을수록 범위 증가. 실행 시 금 필요.` / `모두 컴퓨터에 위임.`**으로 fixed-span 교체했으며 원 ESC K/H skeleton, 136개 direct pointer, separator, 7237B file size를 보존한다. #206 actual owner는 `0x1CFCF8=連合`, ref `0x1DC5B0`이며 **`연합`**으로 same-slot 수정했다. 같은 연속 pointer table 주변의 live untranslated/mixed sibling `米買 / 米売 / 廊수 / 他国の戦争`도 사용자 지정 포함 **`쌀삼 / 쌀팜 / 홍수 / 타국 전쟁`**으로 정리했다. `팜`은 current map에 없어 donor audit 16개 후보 중 **`0x929B / 兆 / physical 2428`**을 신규 alias로 사용했다. 이 alias는 v232 code text refs 0, Message refs 0, Scenario refs 0, 기존 한글 owner 0, historical alias 0이며 font changed cells는 2428과 비활성 아래 guard 2501뿐이다. `名/読` 같은 별도 1-cell label은 같은 table에 있으나 제보 화면 owner로 증명되지 않아 예방 수정하지 않았다. v232 대비 game diff는 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec10.dat` 3개이고 SHA-256=`code B74E93AC64CE7F4F1B57209809430C5F30B194F2113F540E558FDF799135BA09`, `font B84FFAAD2337FE9968347755B059BD90699C8FB675FCEF0C8BC7DC3377D3D3F0`, `msg10 AD76838FB2A249B5118331262496C8E3845DFD98804E440E95040ECF1775D5A6`. 나머지 81개는 v232 byte-exact이고 v231 금 포상 `/4`, v219 아이템 포상 행동 미소모 로직도 보존한다. builder/check/독립 verifier + common review live v233 2241행 + battle review live v233 520행 verifier PASS. Original/Rebuild/Backup/Dummy update/패키징 미수정. report=`analysis\v233_issue204_206_ui_text_cleanup_report.json`, donor=`analysis\v233_pam_donor_audit.json`.
- **직전 Patch 기준본: v232-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra 확인 pending)** — exact v231 Patch 84파일을 `analysis\v232_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 명시한 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v231 `RomFS\StartMenu\title_up.g1t` texture index 2에 1:1 삽입했다. source PNG SHA-256=`982D2F1232F77F0BC8FD094F4624A40A2582BCF6A45CA3404230BEB568962E24`. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v231과 byte-exact이고 index 2 readback은 pixel-exact다. v231 대비 변경 게임 파일은 이 G1T 하나뿐이며 최종 SHA-256=`75914C28A704277CFFA6215A8B4FBA7BA0D251FA0152C0E14F12CE9D301252AD`; v231 `code.bin`과 나머지 83파일은 동일하다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v232_title_up_002_image_update_report.json`.
- **현재 Patch 최종 기준본: v231-intermediate (금 포상 충성도 `/6→/4` 추가 강화, 정적 검증 완료·Citra 확인 pending)** — exact v230 Patch 84파일을 `analysis\v231_gold_reward_loyalty_boost_div4_baseline\PatchSnapshot`에 봉인했다. v230의 `/6` 계산은 `0xB1A3C=0xAAAAAAAB` + `0xB19C0 LSR #2`였고, v231은 금(type0) 전용 literal을 **`0x80000000`**, shift를 **`LSR #1`**로 바꿔 `floor(isqrt(매력×금액)/4)`를 정확히 만든다. `0xB1A3C`의 PC-relative LDR xref는 current code 전체에서 `0xB19B8` 단 1곳이고, `n=0..65535` 전수 산술 검증에서 신규 `/4`가 정수 나눗셈과 일치한다. 상성 보너스 +0..5, 현재 충성도 비참여, 최종 상한 100, item/book 분기, v219 아이템 포상 행동 미소모 로직은 보존한다. v230 대비 game diff는 `ExeFS/code.bin` 1개/6B(`0xB19C0/1`, `0xB1A3C..3F`)뿐이며 SHA-256=`226FAA2C10C94F388A2B00EE48EEC90943C4D5A23DB815E197D3E35AEA28EE76`; 비-code 83파일과 PNG/G1T/font/Message/Scenario, Original/Rebuild/Backup/Dummy update/패키징은 미수정이다. builder→독립 verifier→deterministic check→verifier PASS. report=`analysis\v231_gold_reward_loyalty_boost_div4_report.json`.
- **후속 divisor 변경 인수인계:** `/N` 요청이 다시 오면 `0xB1960` 금(type0) 경로를 그대로 기준으로 한다. `0xB1A3C` literal 주소의 PC-relative xref를 먼저 전역 감사하고, “동일 숫자값의 다른 상수 존재”와 “동일 literal 주소의 공유 참조”를 구분한다. shift만으로 정확한 배율이 되면 1명령 최소 수정, 아니면 magic literal+shift를 함께 설계한다. 변경 전/후 산술은 최소 `0..65535` 전수로 `n//N`과 일치시킨다. v219 아이템 포상 행동 미소모와 item/book 분기, 상성 보너스, 충성도 100 clamp는 반드시 보존한다.
- **직전 Patch 최종 기준본: v230-intermediate (금 포상 충성도 `/12→/6` 강화, 정적 검증 완료·Citra runtime PASS)** — exact v229 Patch 84파일을 `analysis\v230_gold_reward_loyalty_boost_baseline\PatchSnapshot`에 봉인했다. actual 금 포상 delta 계산은 `code.bin 0xB1960`의 type0 분기이며, `SMULBB(매력×금액) → 0xEF8E4 정수 제곱근 → 0xAAAAAAAB UMULL → LSR #3`가 `floor(isqrt(매력×금액)/12)`를 만들고 이후 군주-무장 상성 보너스 `+0..5`를 더한다. v230은 **`0xB19C0`의 `LSR #3` 한 명령만 `LSR #2`로 변경**해 같은 magic multiplier를 정확한 `/6`으로 사용한다. `n=0..65535` 전수 산술 검증에서 기존 `/12`와 신규 `/6`이 모두 정수 나눗셈과 일치한다. 현재 충성도는 delta 산식 입력이 아니며 적용 단계에서 결과를 더한 뒤 100으로 clamp한다. v219의 아이템 포상 행동 미소모 명령(`0x162190/194/19C/1C4`)은 byte-exact 보존했다. v229 대비 game diff는 `ExeFS/code.bin` 1개/1B(`0xB19C0`)뿐이며 최종 SHA-256=`9917522DD201E0157462597673F4ED779AB0A5384AE29502484DC3454CBE529C`; 비-code 83파일, PNG/G1T/font/Message/Scenario, Original/Rebuild/Backup/Dummy update/패키징은 미수정이다. builder→독립 verifier→deterministic check→verifier PASS. 이후 사용자가 실제 플레이에서 `/6` 상승량이 정상 동작함을 확인해 **runtime PASS**로 승격했다. report=`analysis\v230_gold_reward_loyalty_boost_report.json`.
- **직전 Patch 최종 기준본: v229-intermediate (Issue #203 신군주/신무장 설정 후속 + 주변 일본어 잔존 완료, 정적 검증 완료·Citra 재확인 pending)** — #203은 v226 이후 source class별로 v227→v228→v229 세 단계로 진행했다. v227에서 신군주/신무장 선택 prompt, 16세 미만 등장/직책 반영 안내, 연령 증감 popup, `초기화 할까요？` 등을 `msgsec01` fixed span/physical gap 안에서 처리했고, v228에서 SP무장 direct115~124와 direct197을 한글화했다. 사용자 Citra 확인에서 대부분 정상 적용됐으나 `연령 변경 없음 / 진행할까요？`가 어색하고 `新君主 설정 중지할까요？`의 선두 label이 일본어로 남은 것을 확인했다. v229는 `0x15FA`를 **`연령 변경 없이 / 진행할까요？`**로 교정한다. `新君主` 문제는 `msgsec01 direct97=%s 설정 중지할까요？`를 수정하지 않고, current `code.bin 0x1CFFE4`의 unique label `新君主` 6B를 **`신군주` 6B**로 exact-width 치환한다. pointer owner는 `0x1DC5CC→0x002CFFE4`; immutable Original은 string-pool relocation 전 `0x1CFFD4`, 같은 entry가 `0x002CFFD4`를 가리킨다. 따라서 기대 runtime은 **`신군주 설정 중지할까요？`**다. 함께 남아 있던 direct110/148/183/184/195/196도 마무리한다. direct110은 opaque dynamic prefix `02 14 CA 66 C8`를 byte-exact로 두고 literal suffix만 `의 / 휘하로？`; 나머지는 `볼 장을 선택 / 확인할 항목을 선택 / 확인할 내용을 선택 / 열람할 순위를 선택 / 영웅전 미진행으로 열람 불가`다. `랭`·`틀`은 current effective font map에 없어 신규 glyph 없이 기존 글자 표현으로 우회한다. audited msgsec01 direct87~197의 Original-exact residue는 **0건**이고 관련 raw 일본어 제보/인접 문구도 재검색 0건이다. exact v228 84파일을 `analysis\v229_issue203_completion_baseline\PatchSnapshot`에 봉인했고, v228 대비 game diff는 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 2개뿐이다. 최종 SHA-256=`code.bin A78DA44F99519B93E5F2D7D7ECF40E9BFE280C01595029EFE072DA35A1A72839`, `msgsec01.dat EEDF10022398203D69A5B59A13F30AE193BA0F19DC01F4292A1BF7FE83E9F0CE`; msgsec01 9183B, 201개 pointer, 대상 separator는 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. font/PNG/G1T/다른 Message/Scenario, Original/Rebuild/Backup/Dummy update/패키징은 미수정. report=`analysis\v229_issue203_completion_report.json`.
- **직전 Patch 최종 기준본: v226-intermediate (Issue #202 신군주/신무장 등장 설정 상단 도움말 한글화, 정적 검증 완료·Citra pending)** — 로컬 `Github_Issue\Issue202.html`과 번역 HTML/JSON을 current v225와 대조했다. 대응 번역은 이미 존재했지만 `msgsec01_0049~0063` 일부는 legacy fragment 경계가 깨져 있고 실제 live direct는 Original 일본어 body를 그대로 읽고 있었다. actual source는 `RomFS/Message/msgsec01.dat` direct **87~93, 98~109 중 19개**다. 제목은 `신군주 등장 / 도시 설정 / 초기화 / 신무장 등장 / 소재·신분 변경 / 연령 모두 변경 / 확정`으로 정리하고 원 `ESC C9...C1` 색상 제어를 그대로 보존했다. 본문의 `ESC K/H`도 순서·개수를 유지하고, 201개 direct pointer와 모든 대상 `05 05 05` separator 위치 및 파일 크기 **9183B**를 바꾸지 않는 fixed-position 방식으로 각 span만 교체했다. direct94~97의 기존 한국어와 #201 direct198~200은 byte-exact 보존했다. `괄`은 과거 v12 alias 이력이 있으나 current 안전 map에는 없어 신규 font donor 없이 `연령 모두 변경`으로 처리했다. exact v225 84파일을 `analysis\v226_issue202_new_ruler_officer_help_baseline\PatchSnapshot`에 봉인했고 v225 대비 game diff는 `RomFS/Message/msgsec01.dat` 1개뿐, 최종 SHA-256=`A04993E99B251CC071C5258D5EEC1624635D842886F4563B9F4B50731AAE6A29`. builder→독립 verifier→deterministic check→verifier PASS. code/font/PNG/G1T/다른 Message/Scenario, Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 미수정. report=`analysis\v226_issue202_new_ruler_officer_help_report.json`.
- **직전 Patch 최종 기준본: v225-intermediate (Issue #201 등용 직후 장군/군사 자격 부족 direct 보완, 정적 검증 완료·Citra pending)** — 로컬 `Github_Issue\Issue201.html`과 current v224를 대조했다. 기존 번역 DB `msgsec01_0092_000~002`에는 이미 유사 한국어 번역이 있었지만 full Korean payload는 current Patch 어느 파일에도 존재하지 않았고, `msgsec01.dat`의 마지막 live direct 198/199/200이 각각 `0x2309/0x2321/0x2340`의 Original 일본어 `将軍...武力不足 / 将軍...指揮能力不足 / 軍師...政治または知力不足` body를 직접 가리키고 있었다. 즉 #201은 번역문 자체 부재보다 **translation DB materialization 누락 / 별도 live tail-direct class 미처리**가 원인이다. 사용자 Issue wording을 반영해 `장군이 되기에는 무력이 부족합니다`, sibling `장군이 되기에는 지휘 능력 등이 부족합니다`, `군사가 되기에는 정치 또는 지력이 부족합니다` 3문장을 EOF에 append하고 direct198/199/200만 `0x2361/0x2385/0x23B1`로 redirect했다. old Japanese physical body와 direct0~197 pointer/body는 byte-exact, msgsec01 9057→9183B(+126B), SHA-256=`288DFB271ACC21614676F7ED48E91FF36097DFA283354822C31A43FE16535F04`. v224 `code.bin` 건강상태 수정, v223 `title_up.g1t`, font/PNG/G1T와 그 밖의 83파일은 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. Rebuild·`0004000000174D00`·Dummy update·Backup·패키징·이미지는 미수정. report=`analysis\v225_issue201_recruit_appointment_report.json`.
- **직전 Patch 최종 기준본: v224-intermediate (전투 장수 건강상태 `負傷/病気` hardcoded duplicate 수정, 정적 검증 완료·Citra pending)** — exact v223 Patch 84파일을 `analysis\v224_battle_health_status_baseline\PatchSnapshot`에 봉인했다. 사용자 Citra Nightly 2104 전투 장수 정보창에서 초상화 아래 붉은 `負傷`이 실제 출력됐다. 기존 v151 계보의 일반 상태 pointer table `0x1DC580/584/588=질병/부상/건강`과 `msgsec03 direct19/20=질병/부상`은 이미 정상이라 보존하고, 별도 hardcoded duplicate `code.bin 0x6420C=病気`, `0x64214=負傷`, `0x10EE58=負傷`, `0x10EE60=病気`만 same-width 4B `질병/부상/부상/질병`으로 치환했다. 각 문자열 뒤 NUL/alignment와 code size를 보존했고 standalone health-state raw residue `病気/負傷/健康`은 `2/2/0→0/0/0`이다. `msgsec02` old physical sentence body의 `負傷` 1건은 current direct가 아니며 이번 단독 상태 라벨 owner로 증명되지 않아 수정하지 않았다. v223 대비 game diff는 `ExeFS/code.bin` 1개/16B, SHA-256=`B2B615422B2F68B133EFAC23CDA582EB95A2BBBBE856F96394C60A7069556644`; v223 `title_up.g1t` SHA-256=`17C8DEC766178EEB505088CE749216874F9DDBFCB6A275C91A281713A052D3C7`와 나머지 83파일은 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. PNG/G1T/font/Message/Scenario, Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 미수정. report=`analysis\v224_battle_health_status_report.json`.
- **직전 Patch 최종 기준본: v223-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra pending)** — exact v222 Patch 84파일을 `analysis\v223_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 명시한 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v222 `RomFS\StartMenu\title_up.g1t` texture index 2에 1:1 삽입했다. source PNG SHA-256=`10C55948D7B6AD9C23F8FA0493ACF03D6B95DECAD540EA411DDC3433D982D63B`. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v222와 byte-exact이고 index 2 readback은 source PNG와 pixel-exact다. 변경 게임 파일은 `RomFS/StartMenu/title_up.g1t` 하나뿐이며 최종 SHA-256=`17C8DEC766178EEB505088CE749216874F9DDBFCB6A275C91A281713A052D3C7`, Patch는 84파일이다. v222의 #199 runtime-PASS `code.bin`과 font/Message/Scenario/Hex 등 나머지 83파일은 byte-exact다. builder→독립 verifier→deterministic check PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v223_title_up_002_image_update_report.json`.
- **직전 Patch 기준본: v222-intermediate (Issue #199 actual `code.bin` formatter 수정, 정적 검증 완료·Citra runtime PASS)** — 사용자 Citra 화면의 `조조님, 북해の원담が / 野に下ってしまいました`를 current v221 `code.bin`과 대조한 결과 actual runtime 조립은 `0x14A030=%s님, ` + `0x14A038=%s%sの%sが\n野に下ってしまいました`로 확정했다. ADR은 `0x149FC8→0x14A030`, `0x149FFC→0x14A038`이며 해당 range를 가리키는 ARM ADR은 이 두 exact start뿐이다. v221이 수정한 `msgsec05 0x2E/0x47`은 Patch/Rebuild에 실제로 적용돼 있었지만 화면이 전혀 바뀌지 않았으므로 #199 actual-source 판정으로는 실패다. `0x14A038` 33B source를 사용자 목표 **`%s%s의 %s(이)가 / 하야했습니다`** 28B로 same-allocation 치환했다. `(이)가`를 유지해도 5B 여유가 있어 `이/가` fallback은 불필요하다. 같은 fullwidth tail sibling `0x14FB40=%sの配下%sが野に下ってしまいました`(ADR `0x14FA94`)도 **`%s의 %s(이)가 / 하야했습니다`** 26/34B로 정리했다. current code의 fullwidth `野に下ってしまいました` raw residue는 0건, `%s` format-token 수와 두 outer `%s님, ` prefix/ADR, 원 C-string allocation은 보존된다. exact v221 84파일을 `analysis\v222_issue199_runtime_source_fix_baseline\PatchSnapshot`에 봉인했고 변경 게임 파일은 `ExeFS/code.bin` 1개/59B뿐이다. 최종 code SHA-256=`B0BBAB197068068175F5D23D8AF86934CB12540DAED1340172EDCF4E7A11F706`; v221 `msgsec05.dat` SHA-256=`F71E81773A64F4D96623DB2E32826574A9871420ECD781A99F2BB2D6CDC7D147` 및 나머지 83파일은 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. 이후 사용자 Citra Nightly 2104 실화면에서 **`조조님, 북해의 원담(이)가 / 하야했습니다`가 정상 출력되는 것을 확인해 #199 actual formatter route는 runtime PASS**로 승격했다. Rebuild·`0004000000174D00`·Dummy update·Backup·패키징·이미지는 미수정. report=`analysis\v222_issue199_runtime_source_fix_report.json`.
- **폐기된 #199 actual-source 판정: v221-intermediate (Issue #199 하야 물리 경로·매복 배반 미러, 정적 검증 PASS·Citra runtime 실패)** — exact v220 Patch 84파일을 `analysis\v221_issue199_departure_betrayal_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec05.dat` 1개만 수정했다. direct 0/1은 이미 `%s의 <NAME1>(이)가 / 재야로 내려갔습니다`, `%s 휘하 <NAME1>(이)가 재야로 내려갔습니다`라는 완전한 한글 EOF target을 가리키지만 Citra #199가 읽은 Original 물리 본문 `0x2E/0x47`만 일본어로 남아 있었다. 장소/소속 `%s` 생략안은 사용자 요구에 따라 폐기했다. 물리 fallback은 `0x2E=%s의<NAME1>(이)가 / 하야했다`(22/22B), `0x47=%s의 <NAME1>(이)가 / 하야했다`(23/25B)로 모든 동적 문맥을 유지한다. 원하는 존댓말 `%s의 <NAME1>(이)가 / 하야했습니다`는 27B라 첫 span을 5B 초과하며, 정확히 적용하려면 사용자 제작 `하야/했습/니다` 14×14 합자 3개와 fresh local-only 안전 한자 alias 3개로 21B화하는 후속 작업이 필요하다. 카나/기존 번역 글리프는 사용하지 않는다. 인접 복귀 `0x63=%s / <NAME1>(이)가 복귀`(17/20B), 매복 배반 발각 `0xE3=<NAME1>님,<NAME2>(이)가 / <NAME3>의 매복자였음`(32/33B)도 동일 방식으로 처리했다. `code.bin 0x1841C0=%s 재야로 이동`은 1인자 별도 경로라 #199에 재사용하지 않았다. 남는 바이트는 space padding이며 `05 05 05` separator, 파일 크기 1231B, 22개 direct pointer/46B header와 current direct target 전체를 보존한다. 전투·모반·귀순 관련 code 4 + Message direct 21 = 25개 route는 모두 한글/일본어 잔존 0으로 감사했다. 사용자가 정상 출력을 확인한 `밀담 중` direct14와 physical `0x197..0x1BC`는 v220 byte-exact로 보존했으므로 수정 대상이 아니다. 최종 SHA-256=`F71E81773A64F4D96623DB2E32826574A9871420ECD781A99F2BB2D6CDC7D147`; 비대상 83파일은 v220 byte-exact다. builder/check/독립 verifier PASS. Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 미수정. report=`analysis\v221_issue199_departure_betrayal_report.json`.
- **현재 Patch 최종 기준본: v220-intermediate (수정된 `title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra pending)** — exact v219 Patch 84파일을 `analysis\v220_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 명시한 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v219 `RomFS\StartMenu\title_up.g1t`의 texture index 2에 1:1 삽입했다. 공식 v1.1 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v219와 byte-exact이고 index 2 readback은 source PNG와 pixel-exact다. 변경 게임 파일은 `RomFS/StartMenu/title_up.g1t` 하나뿐이며 최종 SHA-256=`F78E2BD15E5FA15059175D7AFAB716D3ECB3AB510989AEDED3E0C6CF10FD4451`, Patch는 84파일이다. v219 포상 아이템 행동 유지 code와 font/Message/Scenario/Hex 등 나머지 83파일은 byte-exact다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v220_title_up_002_image_update_report.json`.
- **현재 Patch 최종 기준본: v219-intermediate (포상 아이템 수여만 군주 행동완료 예외, 정적 검증 완료·Citra runtime PASS)** — exact v218 Patch 84파일을 `analysis\v219_reward_item_no_action_baseline\PatchSnapshot`에 봉인했다. 포상 전용 후처리는 current `code.bin 0x16215C`, caller `0x162110` 1곳뿐이며 `0x16216C mov sb,r1`의 `sb/r9`가 포상 종류(`0=금`, `1=아이템`, 그 외=서적)를 보존한다. 기존 `0x162190 bl 0xF0B24` 행동완료 setter를 code cave 없이 `0x162190=cmp sb,#1`, `0x162194=blne 0xF0B24`로 조건화했다. 원래 `r4` literal load는 `0x16219C`로 이동해 같은 `0x1622AC`을 가리키고, `r6=0` 초기화는 기존 NOP `0x1621C4`로 이동하여 `0x1621C0 subs`→`0x1621C8 ble` flags를 보존한다. 따라서 **금/서적은 기존처럼 행동완료, 아이템만 행동완료 setter를 skip**하며 아이템 지급·충성도 후처리는 유지된다. v218 대비 game diff=`ExeFS/code.bin` 1개/14B, SHA-256=`F92B07D75722C84022A06489AAF4A2E7002243FB51CBA96D918066898A8C913B`; v218 `title_up.g1t` 포함 비-code 83파일은 byte-exact다. 독립 verifier + deterministic check PASS. 이후 사용자가 실제 Citra에서 **아이템 포상 후 군주 행동이 유지되는 것을 확인**해 핵심 목표 경로는 runtime PASS다. 다음에 특정 하위 명령만 행동 소모를 면제하는 요청이 오면, v219처럼 하위 종류 register를 조건으로 공통 행동완료 setter 호출만 skip하고 실제 효과/후처리는 그대로 유지하는 패턴을 우선 검토한다. Rebuild/`0004000000174D00`/Dummy update/Backup/패키징/이미지는 미수정. report=`analysis\v219_reward_item_no_action_report.json`.
- **직전 Patch 기준본: v218-intermediate (`title_up_002.png` 단일 이미지 갱신, 정적 검증 완료·Citra pending)** — exact v217 Patch 84파일을 `analysis\v218_title_up_002_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 명시한 RGBA 64×16 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`만 최신 v217 `RomFS\StartMenu\title_up.g1t`의 texture index 2에 1:1 삽입했다. 공식 v1.1의 3-texture 구조, G1T header, 미지정 index 0·1 payload는 v217과 byte-exact이고 index 2 readback은 source PNG와 pixel-exact다. 변경 게임 파일은 `RomFS/StartMenu/title_up.g1t` 하나뿐이며 최종 SHA-256=`1DE07427A7108AE9E6AFD51B88D7CA6BD02D08BBC78DA79B60D91A858AEEE270`, Patch는 84파일이다. v217 code/font/Message/Scenario/Hex 및 그 밖의 83파일은 byte-exact 보존했다. builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았고 Rebuild·`0004000000174D00`·Dummy update·Backup·패키징은 수정하지 않았다. report=`analysis\v218_title_up_002_image_update_report.json`.
- **이전 Patch 기준본: v217-intermediate (Issue #198 습득용 장문 특기명 `戦闘術`→`전투술`, 정적 검증 완료·Citra runtime PASS)** — 작업 전 `작업지시폴더\\이슈198_작업의견.md`를 최우선 지시로 읽고 exact v216 Patch 84파일을 `analysis\\v217_issue198_skill_name_baseline\\PatchSnapshot`에 봉인했다. #198은 v81의 44개 compact 특기명과 별개인 **장문/습득 표시용 특기명 source class**다. actual current source는 `code.bin 0x1CEF6C=戦闘術`, pointer `0x1DBE14→VA 0x002CEF6C`; `0x1CEF6B=FF` marker와 `0x1CEF72=NUL`을 보존하면서 6B `90ED93AC8F70`을 **`전투술=8A838E5F934A` 6B**로 exact-width 치환했다. v216 대비 변경 게임 파일은 `ExeFS/code.bin` 1개/6B이고 최종 SHA-256=`4EB0EB0E1559CB53BEA2D5A2EE0F5CAF817371F01E87E5527085BDBFB737052B`. 같은 contiguous 장문 특기명 블록의 sibling 32개(`기마돌격/견제/단기돌진/흘리기/튕겨내기/일기토/기습/용병술/.../생존술/.../유인`)는 이미 v216에서 한국어이며 byte-exact 보존, `0x1DC300..0x1DC3AF` 44개 compact 특기 pointer table도 byte-exact다. font/Message/Scenario/PNG/G1T 및 모든 비-code Patch 파일은 v216과 동일하다. 독립 verifier→deterministic check→verifier PASS, raw `戦闘術` residue 0. 이후 사용자가 실제 습득 화면에서 **`전투술`과 `충차`가 모두 정상 표시되는 것을 확인**했으므로 #198 장문/습득 특기명 route는 runtime PASS다. Rebuild·`0004000000174D00`·Dummy update·패키징·이미지는 수정하지 않았다. report=`analysis\\v217_issue198_skill_name_report.json`.
- **직전 Patch 기준본: v216-intermediate (독음 키보드 `ブ` shared-pointer 복원, 정적 검증 완료·Citra 확인 pending)** — exact v215 Patch 84파일을 봉인했다. Original은 키보드 `ブ` pointer `code.bin 0x1E3FB4`가 메인 메뉴 `セーブ`의 마지막 `ブ\0`을 재사용했는데, v102 `セーブ→세이브` 후 이 pointer가 한국어 `브`를 읽은 것이 실제 원인이다. 종료된 `로드` 뒤 미참조 padding `0x1CF77D..0x1CF77F`에 독립 `ブ\0=83 75 00`을 쓰고 pointer를 `0x002CF784→0x002CF77D`로 redirect했다. v215 대비 actual game diff는 `ExeFS/code.bin` 1개/3B이며 최종 SHA-256=`C8082CF824CF9FFD3A39C4D1E39CF5E86DF4DF2AA65880D6FE440D6C92F876BA`. 한국어 `브=9546/physical2908`, `세이브`, font/fix_data/Message/Scenario/이미지는 byte-exact다. builder/check/독립 verifier PASS. 사용자 지시에 따라 Rebuild·`0004000000174D00`·Dummy update·패키징 산출물은 건드리지 않으며 사용자가 리빌드한다. report=`analysis\v216_beu_kana_pointer_restore_report.json`.
- **v215 판정 정정:** `브`를 안전 alias 9546로 옮기고 old physical3197을 Original로 되돌린 것은 유지하지만, 이것만으로 키보드 `ブ`가 복원된다는 expected-runtime 문구는 폐기한다. `edit_bushou_input_name.g1t`는 Original/Rebuild byte-exact이고 실제 owner는 위 shared substring pointer다.

### Hex UI 이미지 방향 주의 — v130 교정

- `Extracted_Image\RomFS\Hex\UI`의 대상 PNG는 사람이 읽을 수 있는 정상 편집 방향이다. v128에서 `from_edit_view(flip_y)`를 선행한 뒤 표준 encoder에 넣은 결과가 v129 Citra에서 위아래 반전으로 확인됐으므로 그 pack 규칙은 폐기했다.
- v130 이후 runtime pack은 **정상 편집 PNG를 추가 반전 없이 표준 RGBA8 encoder에 직접 입력**한다. 표준 decode 결과가 원 PNG와 pixel-exact여야 하며, `FLIP_TOP_BOTTOM(PNG)`와 같아지는 알려진 실패형은 빌드를 거부한다. `to_edit_view()/from_edit_view()`는 legacy 추출물 감사·이관 전용이다. HexCommand는 세로 아틀라스를 유지하고 90도 회전하지 않는다.
- legacy 저장 배열 PNG는 `analysis\hex_ui_orientation_audit\storage_layout_original`에 보존했다. 전체 `Extracted_Image` manifest 기준 변경 파일은 정확히 위 41개다. Patch, Original, Rebuild, Backup은 수정하지 않았다. 방향 판독 기준이 없는 지도/이펙트/유닛까지 “Hex 355장 전부 반전”이라고 일반화하지 않는다. report=`analysis\hex_ui_orientation_audit\report.json`.

## 1. 현재 기준

- 작업 루트: `E:\Codex_01\Sangokushi2`
- 원본: `Sangokushi 2 Original` — 절대 수정 금지
- 출력: `Sangokushi 2 Patch`
- 사용자는 Patch 폴더 **전체**를 `Sangokushi 2 Rebuild`에 덮어쓴 뒤 3DS를 리빌드한다.
- `Backup` 아래 버전별 백업은 사용자 관리 영역이다. 빌드 입력·현재 버전 판정에 사용하거나 수정하지 않는다.
- **v51-intermediate Citra 확인 완료:** `동탁` 정상 출력과 인사→수색 `수색할 무장?` 출력을 사용자 실화면으로 확인했다. 따라서 `탁=8B45/physical1027` 및 `msgsec07` flat-byte header[153] 복구는 런타임 성공 상태다.
- **현재 정식 기준: v53** — `code.bin` 대사 282/282, direct Message 957/957을 발행한 전체 대사 완료 revision이다.
- **현재 Patch 최종 기준본: v214-intermediate (Issue #197 무장 편집 `読み`→`독음`, 정적 검증 완료·Citra 확인 pending)** — exact v213 전체 Patch 84파일을 `analysis\v214_issue197_reading_label_baseline\PatchSnapshot`에 봉인했다. actual owner는 `code.bin 0x1CFFCC=読み`, pointer `0x1DC57C→VA 0x2CFFCC`이며 바로 앞 `0x1DC578→VA 0x2CFA88`은 #158에서 이미 한글화된 `이름` sibling이다. `読み=93C782DD` 4B를 사용자 요청 **`독음=90A48EF7`** 4B로 exact-width 제자리 치환해 pointer/NUL/code size를 모두 보존한다. `msgsec01`의 v162 공용 `%s 설정 중지할까요？`는 byte-exact로 재사용하므로 기대 runtime은 **`독음 설정 중지할까요？`**다. v213 대비 변경 게임 파일은 `ExeFS/code.bin` 1개/4B이고 최종 SHA-256=`B99FB679951A262C91366ABC86DBA38CEB31527DA7C61F42B3DE21BCBD2D8E1A`; `Dummy update\ExeFS\code.bin`도 동일 SHA로 동기화했다. v213 `title_up.g1t`, 모든 Message/font/Scenario/PNG/G1T/ARC는 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier PASS. Rebuild/3DS/CIA 패키징과 이미지 편집은 수행하지 않았다. report=`analysis\v214_issue197_reading_label_report.json`.
- **직전 이미지 기준본: v213-intermediate (`title_up_002` 단일 이미지 리빌드, 정적 검증 완료·Citra 확인 pending)** — exact v212 84파일을 봉인하고 최신 Patch의 3-texture `RomFS/StartMenu/title_up.g1t`를 base로 사용했다. 사용자 지정 index 2 `title_up_002.png`(64×16 RGBA8)만 교체했고 index 0·1, header, 파일 크기 및 지정 payload 밖 바이트는 v212와 exact다. packed readback은 입력 PNG와 pixel-exact다. v212 대비 변경 게임 파일은 이 G1T 1개뿐이고 최종 SHA-256=`F4F228B1559E0137220B29CFE959B0F38855FA1731042F9D26AC4E6EB08443DF`다. `Dummy update\RomFS\StartMenu\title_up.g1t` 입력도 Patch와 같은 SHA로 동기화했으며 기존 CIA/CXI/update.romfs는 사용자 재빌드 전까지 보존했다. 이미지 생성은 사용하지 않았다. report=`analysis\v213_title_up_002_image_update_report.json`.
- **직전 Patch 기준본: v212-intermediate (Issue #195 v211 fixed-copy NUL side-effect hotfix, 정적 검증 완료·Citra 재확인 pending)** — v211의 split-source 판정(`0x167414` prefix + 동적 군주명 + `0x167420` suffix)은 유지하되, 사용자 Citra에서 `역적 + 이전 shared-buffer 잔여문 + 조조를...`이 삽입되는 side-effect가 확인됐다. `0x1671E4`가 호출하는 `0x0F55B4`는 `ldr/str` 4B + `ldrb/strb` 1B로 정확히 5B만 복사하므로 v211의 `역적 ` 5B는 terminating NUL을 destination에 복사하지 못했다. v212는 `역적=8AFB88E5` 4B를 유지하고 `0x167418` 1B를 `20→00`으로 복원해 NUL을 copy5 범위 안에 넣었다. v211 대비 game diff는 `ExeFS/code.bin` 1개/1B, 최종 SHA-256=`AD59A97FA70C005E8EAB7D577DCCA02A48AD285B5117D40C02FEFCA6AAC27D67`; Dummy update code도 동일 SHA다. 기대 runtime=`역적<NAME1>를 토벌해 / 짐의 고통을 끝내라`. 공백 1B는 fixed-copy 계약을 넘기므로 이번 hotfix에서는 넣지 않는다. common review live v212 2241행 / Xiandi review 39행 verifier PASS. report=`analysis\\v212_issue195_fixedcopy_hotfix_report.json`.
- **폐기된 Patch 기준본: v211-intermediate (Issue #195 헌제 밀사 `逆賊` split-source 수정, 정적 검증 PASS·Citra runtime 실패)** — 로컬 `Github_Issue\\Issue195.html` 실화면의 `逆賊조조를 토벌해 / 짐의 고통을 끝내라`를 current v210과 대조했다. 헌제 리뷰에서 정상으로 보인 `M08-B112=역적 <NAME1>를 토벌하여...`는 별도 Message duplicate이고, C064도 actual full runtime이 아니라 suffix `를 토벌해 / 짐의 고통을 끝내라`만 표시한 행이었다. actual owner는 같은 code function의 **`0x167414=逆賊` prefix + 동적 군주명 + `0x167420=C064 suffix`** 조립 route이며 ADR `0x1671E0→0x167414`, `0x167204→0x167420`으로 증명했다. source `逆賊=8B7491AF` 4B를 current map `역적=8AFB88E5`로 바꾸되 자연스러운 `역적 조조를` 띄어쓰기를 위해 `0x167418..0x16741B`의 4B zero alignment padding 중 1B를 `20`으로 사용하고 NUL을 `0x167419`로 1B 이동했다. 다음 literal word `0x16741C`, 남은 padding, 두 ADR와 suffix는 byte-exact다. exact v210 Patch 84파일을 `analysis\\v211_issue195_xiandi_rebel_prefix_baseline\\PatchSnapshot`에 봉인했고 v210 대비 변경 게임 파일은 정확히 `ExeFS/code.bin` 1개/5B. 최종 code SHA-256=`88898285B50F1D2910A946DFF8B485D0F0DF3C67A8B8FD3B3E1751DBB822A3F2`; `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. Message/font/PNG/G1T/ARC는 v210 byte-exact다. 전체 `逆賊` 감사에서 active code residue는 0건. `msgsec00@0x19BC`는 direct header가 없는 embedded command/data block, `msgsec16@0xD3`는 49-word runtime table 미참조 old physical body라 runtime 증거 없이 수정하지 않는다. common review는 **C437** composed route를 추가해 live v211 2241행(code438/formal957/binary846) PASS, Xiandi review는 C437을 강한 후보로 추가하고 C064를 suffix fragment로 재분류해 live v211 39행 PASS다. 두 launcher는 current Patch 기반으로 v211까지 sync 확인했다. Rebuild/3DS 빌드 및 이미지 편집은 수행하지 않는다. report=`analysis\\v211_issue195_xiandi_rebel_prefix_report.json`.
- **직전 Patch 기준본: v210-intermediate (Issue #194 메뚜기 피해 확산 팝업의 일본어 `いなご` 잔존 수정, Citra 실화면 PASS)** — 로컬 `Github_Issue\\Issue194.html`과 첨부 Citra 화면에서 `いなご 피해가 확산 중입니다`를 확인했다. v124 #102에서 한글화한 damage formatter는 공식 v1.1 병합 후 current `code.bin 0x0B269C`에서 이미 **`%s 피해가 확산 중입니다`**로 정상이고 caller `0x0B25FC`도 이를 참조한다. 실제 잔존 owner는 별도 `%s` 인자 경로로, current `0x15EB84` literal-pool pointer가 `0x0025D720`의 일본어 **`いなご`**를 가리키고 있었다. `0x15D720`은 6B visible 뒤 NUL/align 후 `0x15D728`부터 ARM code라 9B `메뚜기 떼`를 in-place 확장할 수 없다. v210은 이미 current string pool `0x1CFE6C`에 존재하는 **`메뚜기 떼`**를 재사용해 pointer word만 `0x0025D720→0x002CFE6C`로 redirect했다. 실제 diff는 pointer word 내부 3B뿐이고 formatter, old Japanese literal, target Korean pool, code size를 모두 보존했다. exact v209 전체 Patch 84파일을 `analysis\\v210_issue194_locust_damage_baseline\\PatchSnapshot`에 봉인했고 v209 대비 변경 게임 파일은 정확히 `ExeFS/code.bin` 1개뿐이다. 최종 code SHA-256=`6C64CF63FFC8A29FEF233776FC24B8F408FC4F9AFDC1C308C387AC53E64FE063`; `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. font/Message/PNG/G1T/ARC는 v209 byte-exact다. builder→독립 verifier→deterministic `--check` PASS. `common_dialogue_review_v180`은 이 조립형 source가 기존 일반 C-string inventory에서 누락됐던 문제를 보완해 **C436=`메뚜기 떼 피해가 확산 중입니다`**를 후첨했고 live v210 **2240행(code437/formal957/binary846)** verifier PASS다. 기존 C000~C435 ID는 유지한다. Rebuild/3DS 빌드는 사용자 전용이며 이미지 편집은 수행하지 않는다. report=`analysis\\v210_issue194_locust_damage_report.json`.
- **직전 Patch 기준본: v209-intermediate (Issue #193 상단 모드명 한글화, 정적 검증 완료·Citra 재확인 pending)** — 로컬 `Github_Issue\\Issue193.html`과 첨부 asset 2개를 기준으로 상단 모드명 3개를 추적했다. actual owner는 `code.bin`의 연속 pointer table `0x1AB3F0 / 0x1AB3F4 / 0x1AB3F8`이며 각각 current VA `0x2CFD38 / 0x2CFAB4 / 0x2CFE7C`를 가리킨다. source=`オリジナル` 10B / `3DS` 3B / `ﾁｬﾚﾝｼﾞｼﾅﾘｵ` 10B, target=`오리지널` 8B / `3DS` 3B / `챌린지` 6B다. 3DS는 byte-exact 유지하고 나머지 두 slot만 shorter C-string으로 same-slot 교체해 pointer와 code file size를 움직이지 않았다. 별도 fullwidth `チャレンジシナリオ` at `0x1CFA4C`는 해당 table owner가 아니므로 보존했다. exact v208 전체 Patch 84파일을 `analysis\\v209_issue193_mode_names_baseline\\PatchSnapshot`에 봉인했고 v208 대비 변경 게임 파일은 정확히 `ExeFS/code.bin` 1개뿐이다. 최종 code SHA-256=`A9FCAB0775C6AB3C56CA74B7406B004729B6C14931460000BA95DF4BEE142794`; `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. font/Message/PNG/G1T/ARC는 v208 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier PASS. common dialogue review는 live v209 **2239행**이며 Issue193 rows `C433=오리지널`, `C434=3DS`, `C435=챌린지`를 추가했고, battle review는 live v209 **520행** verifier PASS다. Rebuild/3DS 빌드는 사용자 전용이며 이미지 편집은 수행하지 않는다. report=`analysis\\v209_issue193_mode_names_report.json`.
- **직전 Patch 기준본: v208-intermediate (상대군 전멸 대사 띄어쓰기 개선, 정적 검증 완료·Citra 재확인 pending)** — `common_dialogue_review_v180.html` 기준 실제 live source는 `RomFS/Message/msgsec04.dat`의 **`M04-B032 / BYTE header[47] / pointer 0x6EA`**이고 source fragment 권위는 `msgsec04_0020_000`이다. v207 current body는 `<NAME1>군을전멸시켰습니다 ` 22B이며 마지막 ASCII space 1B가 padding으로 남아 있었다. 사용자 제안 1안 `<NAME1>군을 전멸 시켰습니다.`는 24B, 2안 `<NAME1>군을 전멸 시켰습니다`는 23B로 각각 2B/1B overflow하고, 3안 **`<NAME1>군을 전멸시켰습니다`**는 22/22B exact fit이라 적용했다. 끝 padding 1B를 `군을` 뒤로 이동하는 same-span 수정만 수행해 header[47]=`0x6EA`, header[48]=`0x703`, separator `0x700`, file size 2553B와 다음 physical block을 모두 보존했다. exact v207 전체 Patch 84파일을 `analysis\\v208_annihilation_spacing_baseline\\PatchSnapshot`에 봉인했고 v207 대비 변경 게임 파일은 정확히 `RomFS/Message/msgsec04.dat` 1개뿐이다. 최종 SHA-256=`861EE0A11402ABC5BCDFE047A92A8256AF73E19107612C8C5E421DF1EDB0FF0A`; code/font/PNG/G1T/ARC와 Dummy update code는 v207 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier PASS, common dialogue review는 live v208 **2236행**, battle review는 live v208 **520행** verifier PASS이며 `M04-B032` readback도 새 문구와 일치한다. `Extracted_Text/sangokushi2_translation.json`과 `text_review.html`의 `msgsec04_0020_000`은 조립용 선두 공백을 포함한 ` 전멸시켰습니다`로 갱신했다. Rebuild/3DS 빌드는 사용자 전용이고 이미지 편집은 하지 않는다. report=`analysis\\v208_annihilation_spacing_report.json`.
- **직전 Patch 기준본: v207-intermediate (M02-120 줄바꿈 개선, 정적 검증 완료·Citra 재확인 pending)** — 사용자가 Citra에서 `적 부대에 거짓 전령을 보내라!`의 마지막 느낌표만 다음 줄로 밀리는 것을 확인했다. actual live source는 `msgsec02 direct120 / pointer 0x1A72`, separator `0x1AA7`, fixed visible span 53B다. 첫 구만 `적에게`로 줄여 **`적에게 거짓 전령을 보내라! / 적을 혼란시키는 것이다!`** 50B + padding 3B로 적용했고 pointer/header/separator/file size와 다른 156 direct payload, v205 `M02-P120` physical mirror를 보존했다. msgsec02 SHA-256=`4327C0EE9D7AD7B2BB54E33902793B59B7A0F1986FE88ACF82B257B1E5FE8D68`; report=`analysis\\v207_m02_120_layout_report.json`.
- **직전 Patch 기준본: v206-intermediate (#190 위협사격 상태문 조사 표기 개선, 정적 검증 완료·Citra 재확인 pending)** — 사용자가 v205 Citra에서 `OO / 움직일 수 없습니다!!`가 정상 출력되는 것을 확인해 v205 source 판정은 runtime PASS로 유지한다. v206은 같은 `code.bin 0x1257F4 / VA 0x2257F4` formatter를 **`%s은/는\n움직일 수 없습니다!`**로 개선했다. 요청안 `%s는(은)\n...`은 28B로 visible 27B allocation을 1B 초과하므로 사용하지 않았고, 최종안은 **27B/27B exact fit**이다. 기존 NUL `0x12580F`, refs `0x1257AC/0x1264A0`, 직후 ARM code는 byte-exact 보존한다. exact v205 전체 Patch 84파일을 `analysis\\v206_issue190_status_particle_baseline\\PatchSnapshot`에 봉인했고 v205 대비 변경 게임 파일은 정확히 `ExeFS/code.bin` 1개뿐이다. 최종 SHA-256=`BA9C6C398BB9D0B6A75ADE0F77112E909238A871A0A525496DAE2F5F1FFD08E1`; `msgsec02.dat`는 v205 SHA `EB0806E8975446A726CB4D77401DE14C9B8A0A2402D7AAB1FA4912AFC74401EC` 그대로다. Dummy update code도 동일 SHA로 동기화했다. builder→독립 verifier→deterministic `--check`→verifier PASS. battle review live v206 520행 / common review live v206 2236행 PASS. font/PNG/G1T/ARC, Original/Rebuild/Backup은 미변경이며 Rebuild/3DS 빌드는 사용자 전용이다. report=`analysis\\v206_issue190_status_particle_report.json`.
- **직전 Patch 기준본: v205-intermediate (#190 전투 중 일본어 잔존 / runtime source 분리 복구, 정적 검증 완료·위협사격 상태문 Citra PASS)** — exact v204 전체 Patch 84파일을 `analysis\\v205_issue190_battle_runtime_residue_baseline\\PatchSnapshot`에 봉인했다. 첫 #190 `奮ｲ立ﾃ！ / ｺﾉ戦ｲ、勝ﾃﾙｿﾞ！！`는 `msgsec02 direct127` pointer가 이미 EOF 한국어 `분발하라! / 이 싸움은 이길 수 있다!!`를 가리키므로 direct 오지정으로 단정하지 않는다. Citra가 old physical sequence를 소비하는 동일 class를 감사해 direct/header를 전혀 움직이지 않고 **26개 old physical span**을 original capacity 안의 compact Korean mirror로 교체했다. 대표는 `M02-P127=분발하라! / 이길 수 있다!!`, `M02-P100=성공! / 적이 동요했다`, `M02-P089=<NAME1> / 분노 상태!`다. 추가 위협사격 성공 일본판 화면 `馬超は / 身動きが取れません！！`의 actual owner는 Message가 아니라 current `code.bin 0x1257F4 / VA 0x2257F4` shared formatter `%sは\n身動きが取れません！！`이며 refs `0x1257AC`, `0x1264A0` 두 곳이 같은 문자열을 참조한다. 이를 `%s\n움직일 수 없습니다!!` 23B로 바꾸고 즉시 NUL 종료했다. v204 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec02.dat` 2개**이며 SHA-256은 `code=239070217EA84E611999D72F7208C18C5FAE10E0D0D37791C99370593D24153F`, `msg02=EB0806E8975446A726CB4D77401DE14C9B8A0A2402D7AAB1FA4912AFC74401EC`. 157 direct/header, relocated EOF payload, target separator, msgsec02 size는 보존되고 font/PNG/G1T/ARC는 비대상이다. `Dummy update\\ExeFS\\code.bin`도 동일 code SHA로 동기화했다. builder→독립 verifier→deterministic `--check`→verifier PASS. `battle_text_review_v180.html`은 live v205 **520행**(신규 `msgsec02-physical` 26 + `C190-STATUS` 1)으로 재생성/검증 PASS, common review도 live v205 **2236행** PASS. **주의:** 이 작업 중 에이전트가 규칙과 달리 `Rebuild_San2.bat`을 실행한 적이 있으나 이는 정식 절차가 아니며 재발 금지다. `Sangokushi 2 Rebuild` 동기화와 `san2.3ds`/CIA 리패키징은 사용자 전용 작업으로 유지한다. 에이전트는 Patch/검증/HTML/MD까지만 수행한다. report=`analysis\\v205_issue190_battle_runtime_residue_report.json`.
- **직전 Patch 기준본: v204-intermediate (#189 전투 중 기능 메뉴 `중단 / 게임종료`, 정적 검증 완료·Citra 재확인 pending)** — 로컬 `Github_Issue\\Issue189.html`과 첨부 Citra 화면을 current v203과 대조해 두 버튼이 이미지가 아니라 `code.bin` 공통 UI C-string table임을 확인했다. actual source는 `0x1CF8C8=中 断`, `0x1CF8D0=halfwidth ゲーム終了`, literal owner는 `0x1DC268->0x2CF8C8`, `0x1DC27C->0x2CF8D0`이다. 최신 표기는 **`중단 / 게임종료`**다. `중단`은 8B local slot에서 4B target 직후 즉시 NUL 종료해 중앙정렬에 영향을 주는 trailing space를 넣지 않고, `게임종료`는 원 visible 8B와 target 8B가 exact-width라 기존 terminator `0x1CF8D8`을 그대로 보존한다. exact v203 전체 Patch 84파일을 sealed baseline으로 봉인했고 v203 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개 / 13B**뿐이다. 최종 code SHA-256=`86966C881B28377428F4F3201C0E868A03364567A112490ABF1AF558A3D2C43C`. font/모든 Message/PNG/G1T/ARC와 Original/Rebuild/Backup은 v203 byte-exact이며 이미지 편집/생성은 하지 않았다. code 변경에 따라 `Dummy update\\ExeFS\\code.bin`도 동일 SHA로 동기화했다. builder→독립 verifier→deterministic `--check`→verifier PASS, common review live v204 2236행 / battle review live v204 493행 PASS. report=`analysis\\v204_issue189_battle_function_menu_report.json`.
- **직전 Patch 기준본: v203-intermediate (#188 통상 공격 `성문이 열렸습니다!` 오출력 + e2/e3/e4 복수대사 구조 복구, 정적 검증 완료·핵심 경로 Citra PASS)** — 사용자 일본어판/패치판 동일 조건 비교로 통상 공격의 `くらえ！ / 邪魔だ！`가 Patch에서 모두 `성문이 열렸습니다!`로 나오는 것을 확인했다. 실제 원인은 `msgsec02 direct8`이 단일 문자열이 아니라 **`01 S 02 e2` 뒤 05×3 separator로 이어지는 2-variant chain**인데, v164 standalone repack이 첫 `くらえ！`만 EOF로 옮겨 variant2 `邪魔だ！`를 소실시킨 것이다. runtime이 두 번째 변형을 선택하면 물리적으로 다음 direct9 `門が開きました！`를 읽었다. v203은 direct8을 완전한 **`받아라! / 비켜라!`** e2 chain으로 append+direct8만 redirect하고, 독립 direct9 `성문이 열렸습니다!` pointer/payload는 v202 byte-exact 보존했다. 같은 class 전수 감사로 총 **11 eN 그룹**을 복구했다: msgsec00 direct59~62, msgsec02 direct8, msgsec07 direct76, msgsec08 direct6/11/13/14/15. flat 5그룹은 group 전체 append, mixed 6그룹은 shared BYTE-direct/WORD-parent header를 byte-exact 유지한 채 active direct/parent overlap 0인 proven span 안에서만 in-place 수정했다. v202 대비 변경 게임 파일은 정확히 `msgsec00/02/07/08.dat` 4개이며 code/font/모든 이미지/Dummy update는 v202 byte-exact다. SHA: `msg00=140E1FDD...A46C2A`, `msg02=82B86CAB...6C4AC`, `msg07=892C0584...68C33`, `msg08=7E759D66...EE20`. builder→독립 verifier→deterministic `--check`→verifier PASS. `battle_text_review_v180.html`은 hidden variant 14행을 추가해 **493행**(`M02-008=받아라!`, `M02-008-V2=비켜라!`, `M02-009=성문이 열렸습니다!`), common review는 live v203 **2236행(code433/formal957/binary846)** verifier PASS. report=`analysis\v203_issue188_special_group_repair_report.json`.
- **직전 Patch 기준본: v202-intermediate (#185 Citra runtime 실패 복구, 정적 검증 완료·Citra 재확인 pending)** — v200/v201 #185 조립형 감소문은 사용자 Citra에서 `???`, 감소문 중복, 메시지 직후 freeze가 확인되어 **runtime 실패본**으로 처리한다. 실제 원인은 `code.bin 0x0CACE0` 함수가 prefix를 `0x1F55B4` helper로 정확히 5B만 복사하는데 v200의 `%s의 `는 NUL이 6번째 바이트라 stack buffer가 종료되지 않았던 것이다. v202는 prefix를 `%s의\0`로 복원하고, ` 체력이`/` 사기가`/` 병력이` slot과 ARM 6-instruction local routing으로 첫 항목만 선행 공백을 사용하고 후속 항목은 +1 pointer로 공백을 건너뛴다. separator는 local `/`이며 기대 출력은 **`관평의 체력이/사기가/병력이`** 계열이다. 7개 감소 조합 전부 simulation PASS, 최장 NUL 포함 37/40B. v201 대비 변경 게임 파일은 `ExeFS/code.bin` 1개/34B, #186/#187 Message와 font/PNG/G1T는 byte-exact다. Dummy update code 동기화, 독립 verifier PASS, code SHA-256=`436181F129DFBDEB747096431DA043AD041E76289B6224E3BC750DF67C6906CE`. report=`analysis\v202_issue185_runtime_repair_report.json`.
- **직전 Patch 기준본: v201-intermediate (#186 아이템 정보 확인문 + #187 `(이)가` 조사 순서 전수 보정, 정적 검증 완료·#185 때문에 전체 runtime 확인 중단)** — exact v200 84파일을 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat` 정확히 8개만 수정했다. #186 actual owner는 `msgsec09 direct19 / 0x266..0x27A` 21B mixed fixed span이며 최종 문구는 **`아이템 정보를 볼까요?`** 21/21B다. 전각 `？`는 22B로 overflow하므로 header/WORD-parent를 움직이지 않고 ASCII `?`를 용량 예외로 사용했다. #187은 active runtime `이(가)` 62곳을 same-width **`(이)가`** 6B→6B로 치환했다(code22 + msg02 10 + msg05 5 + referenced msg04/06/07/08 25). 참조 없는 stale physical copy 2곳은 보존했다. code SHA-256=`9275D1A89B27AC5B8BCA134A6BA3975BB4221AB91D91ACBDDEE8155AD0BADCC7`. report=`analysis\v201_issue186_187_report.json`.
- **v200-intermediate (#185 최초 조립형 메시지 보완 — Citra runtime 실패 확정)** — `%s의 ` + 항목 + local `, ` 방식은 정적 verifier는 통과했지만, prefix 5B-copy helper의 NUL 경계를 잘못 해석해 Citra에서 `???`, 감소문 중복, freeze를 일으켰다. 이 revision의 #185 구현은 재사용 금지이며 v202가 교정 권위다. report=`analysis\v200_issue185_battle_common_report.json`.
- **직전 이미지 기준본: v199-intermediate (`HexFontEffect_007` 단일 이미지 리빌드, 정적 검증 완료·Citra 확인 pending)** — exact v198 84파일을 봉인하고 최신 Patch의 `RomFS/Hex/UI/HexFontEffect.g1t`를 base로 사용했다. 사용자 지정 texture index 7 `HexFontEffect_007.png`(256×64 RGBA8)만 교체했다. v130 runtime-proven 방향 규칙대로 정상 편집 PNG를 **추가 반전·회전 없이** 표준 encoder에 직접 입력했으며 packed readback은 원 PNG와 pixel-exact, 상하반전 실패형과는 불일치한다. header·32-texture 구조·비대상 31 payload는 v198과 exact이고 변경 게임 파일은 이 G1T 1개뿐이다. 최종 SHA-256=`BF250F4AE8CFB92F56F1C7A3E615E5689CC5F1FCEE07927722B73ABFCD83B341`; 이미지 생성은 사용하지 않았다. report=`analysis\v199_hexfonteffect_007_image_update_report.json`.
- **직전 Patch 기준본: v198-intermediate (공통 대사 이중 물음표 제거 + v180 공통대사 리뷰 v198 live 지원, 정적 검증 완료·Citra 확인 pending)** — exact v197 84파일을 기준으로 `RomFS/Message/msgsec07.dat` 1개만 수정했다. 사용자 화면 `누구에게 명하시겠습니까？？`의 actual runtime source는 **header[375] = 0x3409**이며 block tail `0x3420..0x3423 = 81 48 81 48` 중 두 번째 `？`(`0x3422..0x3423`)만 `20 20`으로 바꿔 기대 문구를 **`누구에게 명하시겠습니까？`**로 만들었다. 388-word header, header[375], `0x3424`의 `05 05 05`, 파일 크기 60406B는 byte-exact, 실제 diff 2B, 최종 msgsec07 SHA-256=`E500E27FD08803A6AA24D2BF9308CCDE0330BB1CDE6DE5B64961593053453F11`. raw `？？` sibling `0x1EB0`은 active BYTE header가 문장 끝을 직접 가리키지 않는 stale physical body이므로 증거 없이 수정하지 않는다. report=`analysis\\v198_double_question_cleanup_report.json`. `Extracted_Text\\open_common_dialogue_review_v180.bat`은 v180 stable ID를 유지하면서 exact v198 Patch를 live-read하도록 generator/server/verifier를 갱신했다. 최신 review verifier PASS: 2231행(code433/formal957/binary841), 이번 문구=`M07-B238 / 0x3409 / BYTE=375`, v197 `뤄=89AA` 대사도 정상 decode. review report=`analysis\\v180_common_dialogue_review_report.json`.
- **직전 Patch 기준본: v197-intermediate (철수 문구 자연화 + #183 `뤄` runtime alias 보완, 정적 검증 + Citra 실화면 확인 완료)** — exact v196 84파일을 기준으로 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec04.dat` 3개만 수정했다. 동적 도시명 뒤 철수 prefix는 `서 병을\n철수해\0`→`의 군을\n철수해\0`로 바꿔 기대 화면을 **`한중의 군을 철수해주십시오`**로 만들었으며 둘 다 NUL 포함 15B라 `0x1E51A4` pointer와 다음 문자열 `0x1D104C`를 이동하지 않았다. #183의 `미앵두겠다!`는 `뤄=9949` far alias가 Citra에서 `앵`으로 표시되는 문제로 확인했다. current semantic runtime source는 `code.bin 0x1D0692`와 `msgsec04.dat 0x111` 정확히 2곳이며 모두 **`뤄=89AA / physical751`**로 재인코딩했다. 새 donor는 current owner/ref 0, Original stock-cell exact, low-lead이며 font 변경 셀은 751 하나뿐이다. msgsec04 header/separator/file size와 모든 비대상 파일은 v196 byte-exact. SHA-256: `code=FFC0CB9C2FA793FA72D9CF103872109E8F76591D914329603630BB86A7663A26`, `font=F5D102E9B1A08FA4127BF346278897E97BF9DAF9D1DCDF572F83C0204DECA924`, `msgsec04=1F5FDDB0A50C080FB4491D8C60A1FAC09D7579CEF59D8D3EDCCE571EFC01CEA8`. 사용자 Citra 실화면에서 **`한중의 군을 철수해주십시오`와 `미뤄두겠다!` 모두 정상 출력**을 확인했다. report=`analysis\v197_issue183_ryeo_and_retreat_report.json`.
- **직전 Patch 기준본: v196-intermediate (동적 도시명 뒤 띄어쓰기 보완, 정적 검증 완료·Citra 확인 pending)** — exact v195 84파일을 기준으로 `ExeFS/code.bin`만 수정했다. `대현에관구검(이)라는 자가 있습니다`의 조립 구조가 `[도시명] + "에" + [무장명] + suffix`임을 확인하고 current `0x15BBAC` 4B slot의 `에`를 `에 `로 바꿨다. 실제 변경은 1B이며 기대 화면은 `대현에 관구검(이)라는 자가 있습니다`다. 함께 요청된 당시 문구 `한중서 병을 철수해주십시오`→`한중에서 병을 철수해주십시오`는 current `0x1D103C` prefix가 15B slot인데 요청안은 17B이고 다음 문자열 전 물리 공간도 16B라 overflow로 보류했다. 이 보류 건은 v197에서 동일 길이 대안 `한중의 군을...`로 해결했다. 최종 code SHA-256=`722806ADB27C69C2BD1FDA581A7F2D20E5535CDB243AB94534F0752CC2B72FA5`; 모든 비-code Patch 파일은 v195 byte-exact. report=`analysis\v196_dynamic_city_spacing_report.json`.
- **직전 Patch 기준본: v195-intermediate (외교 조건값 `なし` runtime owner 회귀 보완, 정적 검증 완료·Citra 확인 pending)** — exact v194 84파일을 기준으로 `ExeFS/code.bin`만 수정했다. v125의 설정값 `なし→없음` target은 공식 v1.1에서 `0xD6308→0xD6458`로 정상 이식되어 있었고, 문제는 별도 standalone runtime source였다. current `0x0B4734`(ADR `0x0B4678/0x0B46E0`)와 `0x17A93C`(ADR `0x17A904`)의 `なし\0` 두 곳을 동일 길이 4B `없음`으로 치환했다. NUL/ADR은 byte-exact 보존, 변경 바이트 8B, standalone `なし\0` 잔여 0개다. v191→v194 historical code target 313개 감사에서는 공식 v1.1 통합으로 새로 재도입되거나 count가 증가한 strong residue가 0건이었다. 최종 code SHA-256=`29EC6238393F440DBA64FFE78A43316DFB566BE5692068944DE37B985A125CA1`; builder→독립 verifier PASS, idempotency PASS. 모든 비-code Patch 파일은 v194 byte-exact. report=`analysis\v195_none_owner_regression_report.json`.
- **직전 Patch 기준본: v194-intermediate (#180 치수 상단 스크롤 설명 보완, 정적 검증 완료·Citra 확인 pending)** — exact v193 84파일을 기준으로 `RomFS/Message/msgsec10.dat`의 fixed span `0x846..0x87D`만 수정했다. v193 runtime 문구 **`치수를 올려 군량 수입을 늘립니다.`**를 사용자 요청안 **`치수・관개를 올려 군량 수입을 늘립니다.`**로 변경했다. 새 문구는 39B/55B라 16B 여유가 있고, 기존 마지막 `0x87D=05` 경계와 파일 크기 7237B는 그대로다. 스크롤형 상단 도움말이므로 수동 줄바꿈은 넣지 않는다. `관=8B6B`, `개=8DEE` 기존 글리프를 사용해 font 수정은 없으며 v193 대비 변경 게임 파일은 `RomFS/Message/msgsec10.dat` 1개뿐이다. 최종 SHA-256=`138ABCB4B575E71119A0ABFE83F5D19CA804ED62DF43E2DB146FEFC2BF9FF920`; code/font/title_up/모든 PNG·G1T·ARC는 v193 byte-exact, builder idempotency + 독립 verifier PASS. report=`analysis\v194_issue180_flood_control_help_report.json`.
- **직전 Patch 기준본: v193-intermediate (`title_up` 지정 PNG 2개 이미지 리빌드, 정적 검증 완료·Citra 확인 pending)** — exact v192 84파일을 봉인하고 최신 Patch의 3-texture `RomFS/StartMenu/title_up.g1t`를 base로 사용했다. 사용자 지정 index 0 `title_up_000.png`(512×256 RGBA8)와 index 2 `title_up_002.png`(64×16 RGBA8)만 교체했다. index 1, header, 파일 크기 및 지정 payload 밖 바이트는 v192와 exact이며 2/2 packed readback pixel-exact다. v192 대비 변경 게임 파일은 이 G1T 1개뿐이고 최종 SHA-256=`FAF199F252DEE6CB1E7D49FBA7CE763836DF0F9BD0DD1B99A5047BF2FD06FD63`다. 이미지 생성은 사용하지 않았다. report=`analysis\v193_title_up_image_update_report.json`.
- **현재 Patch 최종 기준본: v192-intermediate (공식 v1.1 업데이트 병합, 정적 검증 완료·Citra 확인 pending)** — `analysis\v192_official_update_merge_report.json`의 84파일 manifest가 현재 Patch와 exact 일치한다. v191 대비 게임 파일 변경은 `ExeFS/code.bin`, `RomFS/StartMenu/title_up.g1t` 2개이며, `title_up.g1t`는 공식 업데이트의 3-texture 구조와 신규 index 2 `Ver.1.1` 텍스처(64×16 RGBA8)를 보존한다. 최종 `title_up.g1t SHA-256=CF690660196F283F51EAA580CADF2FE2E88016416F176DD29F288528FC8671A0`이다. 신규 항목은 편집용 `Extracted_Image\RomFS\StartMenu\title_up\title_up_002.png`로 무손실 추출했으며 이는 게임 빌드 변경이 아니므로 Revision은 v192 그대로다. extraction report=`analysis\v192_title_up_002_extraction_report.json`.
- **현재 Patch 최종 기준본: v191-intermediate (일기토 #26/direct16 문구 개선, 정적 검증 완료·Citra 확인 pending)** — exact v190 Patch에서 `RomFS/Message/msgsec12.dat` 1개만 변경했다. logical #26 `0x28F..0x2A3` / direct16 `0x28F`를 **`간만의 호걸이로군!`**으로 적용했다. 18B/20B이며 mixed 내부 경계 `0x298`의 9B+11B 구조에 정확히 맞아 header/direct pointer/parent boundary/separator 위치를 전혀 움직이지 않았다. 신규 glyph/font/이미지 변경은 없고 v190 `word10=0xF4` 및 #179 구조도 byte-exact 보존된다. 최종 msgsec12 SHA-256=`1C8BADDE2693E627DB2ACD4673317941EA82AEE88E34178FECF6C643F6B2D439`; 독립 verifier PASS. `duel_text_review_v172.html`은 v172 고정 ID를 유지하면서 현재 v191 데이터를 표시한다. report=`analysis\v191_duel26_report.json`.
- **직전 Patch 기준본: v190-intermediate (#179 일기토 문구/혼합경계 조정, 정적 검증 완료·Citra 확인 pending)** — exact v189 Patch를 `analysis\v190_issue179_duel_text_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec12.dat` 1개만 변경했다. logical #19 `0x1E2..0x1F2`를 사용자 최종안 **`네놈은 어림없다`** 15B/16B로 적용했다. 기존 mixed 내부 parent 경계 `0x1EC`에서는 `림` alias가 2바이트 중간 절단되므로 shared `word10=0x00F6`을 **`0x00F4`**로 옮겨 parent start를 **`0x1E8`**로 조정했다. 같은 word가 BYTE direct9 pointer도 겸하므로 `크윽` direct를 `0xF6→0xF4`로 2B 이동했고, direct8의 trailing padding 4B 중 2B를 소비해 separator 하나만 `0xF3→0xF1`로 이동했다. 25개 direct visible text는 v189와 모두 동일, separator 36개/각 3B, hidden direct24, file size 860B는 유지한다. 최종 msgsec12 SHA-256=`F281A8293E1C3F9F5D8ECB1A42831F7E4359F5746EA4733EDD6A54218E4F15AE`; deterministic builder + 독립 verifier PASS. `duel_text_review_v172.html`은 v172 고정 ID를 유지하면서 현재 v190 데이터로 최신화했다.
- **직전 Patch 기준본: v189-intermediate (#178 아이템 발견 이중조사, 정적 검증 완료·Citra 확인 pending)** — v188은 `Dialog.g1t`만 바뀐 이미지 전용 revision이라 text는 그대로 계승한다. Issue178 실제 source는 `msgsec06` direct index17/header word18이며 기존 pointer는 `0x0396`이다. `s를 찾았습니다`→`s을(를) 찾았습니다`는 14B→18B(+4B)라 shared body를 늘리지 않고 완성 runtime 문자열을 EOF `0x1AD7`에 append+separator한 뒤 direct17만 redirect했다. 기존 msgsec06 body는 byte-exact, 기존 영역 diff는 header `0x24/0x25` 두 바이트뿐, 새 glyph 0, v188 `Dialog.g1t` byte-exact다. msgsec06 크기 6871→6912B, SHA-256 `BE9D327FC75EF6E0648634F5B1E33406C50CFA50EFAE2A2A6A41421682F5A0CC`. report=`analysis\v189_issue178_double_particle_report.json`.
- **직전 이미지 기준본: v188-intermediate (Common Dialog 지정 이미지 5개, 정적 검증 완료·Citra 확인 pending)** — exact v187 Patch 84파일을 `analysis\v188_dialog_image_update_baseline\PatchSnapshot`에 봉인하고 현재 Patch의 `RomFS/Common/Dialog/Dialog.g1t`를 base로 사용했다. 사용자 지정 `Dialog_015/016/017/018/021.png`에 대응하는 index 15/16/17/18/21만 교체했으며 모두 128×64 RGBA8, source codec identity 및 built readback 5/5 pixel-exact다. G1T header/entry geometry/파일 크기와 비대상 payload는 v187 byte-exact이고 Patch 전체 변경은 Dialog.g1t 1개뿐이다. 최종 SHA-256 `2B0DAECD7B8C48E17C678D5632FF0917B7F0D8A2D2A598915FF4BBAFC543D292`; builder→독립 verifier→deterministic check→verifier PASS. 이미지 생성은 사용하지 않았다.
- **현재 Patch 최종 기준본: v187-intermediate (Opening 진입 전 로고·credit 이미지, 정적 검증 완료·Citra 확인 pending)** — exact v186 전체 Patch 83파일을 `analysis\v187_title_arc_image_update_baseline\PatchSnapshot`에 봉인했다. v186에 없던 `RomFS/Opening/Title.arc`를 immutable Original에서 최초 편입하고 사용자가 지정한 `title_logo_2.png`, `shoki_up_00_title_credit.png` 대응 member 2개만 교체했다. `title_logo_2`는 302×144 RGBA4라 native 4비트 nearest quantization(max channel error 8)을 적용하고 그 결과의 재추출 pixel-exact를 확인했다. credit은 320×24 LA8, 저장 `A,L`, PNG grayscale 및 원 PNG pixel-exact readback을 확인했다. 비대상 SARC member·offset·크기는 byte-exact, archive 크기 1741352B 유지, Patch 84파일이다. 최종 `Title.arc SHA-256=7D49110E481DA2E2675A339E60E62DA87881D1D637523F60AB95D5C5E2AB9736`; builder→독립 verifier→deterministic check→verifier PASS. 다음 Title.arc 수정부터는 Original이 아니라 최신 Patch 컨테이너를 base로 한다.
- **현재 Patch 최종 기준본: v186-intermediate (남만왕 복원·특수 열전 166건 한글화, 정적 검증 완료·Citra 확인 pending)** — exact v185 전체 Patch 83파일을 `analysis\v186_biography_translation_baseline\PatchSnapshot`에 봉인하고 `font.g1t`, `msgsec13.dat`, `msgsec20.dat` 3개만 변경했다. msgsec13 direct0은 EOF `0xAFBB`의 완전한 `남만왕…` payload로 redirect했고 숨은 direct336은 빈 SEP `0xAFB8`로 복원해 record1~335를 보존했다. msgsec20 direct0~165(record840~1005) 166건을 한국어 append redirect했으며 direct166~169 빈 항목과 direct170~179 기존 한국어를 보존했다. 신규 donor 21자는 exact v185 current owner/code ref/Message·Scenario ref 0, stock pixel exact, low-lead 조건을 통과했다. 최종 DB는 978행 중 한글 974 / 일본어·혼입 0 / 의도된 빈 항목 4, 19.5×4 위반 0이다. builder→독립 verifier→deterministic check→verifier PASS. SHA: `font=FFC1D34D...CE2AC8A`, `msg13=50AAC856...A127D8`, `msg20=8194660D...B995D7`.
- **직전 Patch 기준본: v138-intermediate** — exact v137을 봉인하고 #123~#126 및 동일 오류 class를 수정했다. 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec11.dat`, `msgsec21.dat` 6개다. #123 `02 28` 동적 서수 토큰 복구, #124 안전 띄어쓰기/호위 확인/`치욕→수치`, #125 `욱=989C` 회피, #126 `msgsec21 >18.5 units` 두 행 축약과 `msgsec07/msgsec21` 마지막 direct OOB 2건 append-only 복구가 최신 권위다. builder→독립 verifier→builder→verifier PASS, Citra 재확인 pending이다.
- **직전 이미지 기준본: v139-intermediate** — exact v138 전체 Patch 81파일을 봉인하고 사용자가 지정한 PNG 19개만 1:1 재삽입한 이미지 전용 revision이다. 변경/추가 게임 파일은 `RomFS/Common/BGM/ChangeBGM.g1t`, `RomFS/StartMenu/option_down.g1t`, `RomFS/Common/BGM/BGMDown.g1t` 3개뿐이며 Patch 파일 수는 81→83이다. `option_down.g1t`는 sealed v138 Patch를 base로, v138에 없던 BGM 두 컨테이너는 이번 최초 편입에 한해 immutable Original을 base로 사용했다. 19/19 pixel-exact, source RGBA8 codec identity, G1T header/비대상 payload byte-exact, 비대상 v138 Patch byte-exact, builder→verifier 2회 결정성 PASS. 이미지 생성은 사용하지 않았고 v140에 byte-exact 계승된다.
- **직전 텍스트 기준본: v140-intermediate** — exact v139 83파일을 봉인하고 #128/#129/#130/#132 및 같은 오류 class 감사를 반영했다. 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec05.dat`, `msgsec06.dat`, `msgsec08.dat`, `RomFS/Scenario/fix_data.bin` 5개다. #128 항복문/동적 이름 공백, #129 `놈=9951 / 베=9952 / 슨=9972` live 대사 0건 + 이중 쉼표, #130 67종 item 표시명 중 15레코드 safe alias 재인코딩(`칠=9787`), #132 `관전종료하시겠습니까？`를 수정했다. 이 revision에서 direct-owned 추가 미번역 popup 34건을 확인했고 v141이 이를 후속 처리한다.
- **현재 Patch 최종 기준본: v185-intermediate (#177 C250 복원·C366 review anchor 교정, 정적 검증 완료·Citra 재확인 pending)** — exact v184 전체 Patch 83파일을 `analysis\v185_issue177_common_review_baseline\PatchSnapshot`에 봉인하고 실제 게임 파일은 **`ExeFS/code.bin` 1개만** 수정했다. C250 `0x1D12D0`은 formal authority대로 **`그럭저럭 성과는 / 얻을 수 있을 듯합니다`**로 복원했으며 37B/37B exact-width, existing glyph `성=8BCC`, 실제 byte diff 4B라 font/G1T 변경이 없다. C366 review의 `ｬ.`는 게임 손상이 아니라 legacy `0x1D3011`이 Original FF×3 padding을 포함한 잘못된 review anchor였기 때문이다. actual pointer table `code.bin 0x1E6020`은 Original/current 모두 **VA `0x2D3014` = file `0x1D3014`**를 가리키고 `0x2D3011` pointer는 0개이며, actual current text는 **`제가 참된 주군을 / 알려드리지요……`**로 정상이다. `common_dialogue_review_v180.html`은 C366 ID를 유지하고 canonical `0x1D3014`를 표시하며, formal 957행의 exact mismatch는 오류 대신 v53 historical authority와 current Patch의 변경/repack 상태로 표시한다(553 byte-identical / 404 changed-or-repacked). builder `--check`, common review verifier, v185 독립 verifier PASS. 최종 `code SHA-256=5208F8F98240EF304CC0397ADFCE801648CB3555E225743D656DC24FB49EB465`.
- **직전 Patch 기준본: v184-intermediate (#175/#176 등용 조언·동적 도시명 조사 전수 개선, 정적 검증 완료·Citra 재확인 pending)** — exact v183 전체 Patch 83파일을 `analysis\v184_issue175_176_city_particles_baseline\PatchSnapshot`에 봉인하고 실제 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec08.dat` 3개만** 수정했다. #175 C264 `쉽게는 / 통하지 않습니다`는 `code.bin 0x1D1508`에서 **`쉽지는 않을 / 것 같습니다`**로 개선했고, #176의 `운남로 침공했습니다` actual source `code.bin 0x158488=へ攻めて来ました`는 **`에 침공했습니다.`**로 exact-width 교체했다. 같은 오류 class를 전수 감사해 proven 동적 도시명 뒤 `로/(으)로/은(는)/가/를`을 요격·증원·위임·영지 전환·공백지·풍작·방랑 도시 문맥까지 `에/에서` 또는 조사 없는 구조로 정리했다. 비도시 `%s`(인물/아이템/직책/위임 방식 등)는 보존했다. common dialogue review는 기존 C000~C431을 재번호화하지 않고 누락된 `0x158488`을 **C432** supplemental row로 추가했으며 C264는 그대로 유지된다. builder → deterministic `--check` → 독립 verifier PASS, Patch 83파일, code target 10개, Message op 14개, font/PNG/G1T 무변경을 확인했다. SHA-256: `code=081E50FCA175D4EF41EF221308349D23880C9A5C9AC2FE7ADABCF74B1DB75487`, `msg07=38BA305AA69EBC5CD45063D73F99ED5AE5F9781C22511107784BC4D8C7B83CA4`, `msg08=413B4D12A3E0423B53D5096BBB4C70A23FCBEADA09652B6EE31B5C26B692C578`.
- **직전 Patch 기준본: v183-intermediate (#174 header166 `진용→세력` 후속, 정적 검증 완료·Citra 재확인 pending)** — exact v182 전체 Patch 83파일을 `analysis\v183_issue174_header166_wording_baseline\PatchSnapshot`에 봉인하고 **`RomFS/Message/msgsec07.dat` 1개만** 수정했다. immutable Original header166은 `0x11F6`의 23B body지만 현재 runtime header166은 이미 이전 revision에서 relocated된 `0x3CAE`의 27B body를 가리킨다. 따라서 v183은 pointer/header/파일 크기를 움직이지 않고 `우리 진용이 더욱 / 강화되었군`을 **`우리 세력이 더욱 / 강화되었군`**으로 same-width 4B `진용=88D3894F→세력=8BDC8C97` 치환했다. 새 문구는 27/27B exact fit이며 stale `M07-B124` copy도 리뷰 일관성을 위해 같은 4B만 치환했다. v182 대비 실제 diff는 8B, msgsec07 size 60406B·388-word header byte-exact, 최종 SHA-256=`48F2ECD6A244898C5938C32EA2B33D588343D0712A358EF7BE00A03DCEAFA8A8`. builder `--check` + 독립 verifier PASS.
- **직전 Patch 기준본: v182-intermediate (#174 실패 `욱=989C`·공통 대사 제어코드 감사, 정적 검증 완료·Citra 재확인 pending)** — exact v181에서 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개만 변경했다. `욱=989C` live/stale 잔존을 safe `욱=89BC`로 제거하고 code/header198은 `더욱 열심히 / 임하겠습니다`로 수정했다. active malformed NAME prefix=0, active high-byte placeholder=0을 감사했으며 `M07-B123`의 `02 01 + 네`는 active가 아닌 stale physical 절단 흔적으로 분류해 repack하지 않았다. v182 최종 SHA-256은 `code=275AA00778D17626F7C776EFF014332B0B7A066C88B14FB0E25D9884E8F138EA`, `msg07=5709978916F2386CABA63AD4B90F6B032C634411E45C92E3CB6991B71C4A01AD`다.
- **직전 Patch 기준본: v181-intermediate (#173 일반 공통 대사 문구 개선·안전 donor 적용, 정적 검증 완료·Citra 재확인 pending)** — exact v180 전체 Patch 83파일을 `analysis\v181_issue173_common_dialogue_baseline\PatchSnapshot`에 봉인하고 실제 게임 파일은 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 2개만** 수정했다. `common_dialogue_review_v180.html`의 고정 Cxxx ID 중 C000/C002/C003/C015/C021/C186/C187/C196/C202/C208/C243 11건을 기존 C-string allocation 안에서 fixed-position으로 교체했다. 신규 글리프는 `풀=8D5A/physical1424`, `랄=97E4/physical3441`, `냐=8B5D/physical1051`, `렇=914E/physical2164`; 이 중 `풀/랄/렇`은 역사 배정 0 fresh donor이고 `냐=8B5D`만 v13 폐기 실험 `경=8B5D` 단독 이력을 v168 예외로 재사용한다. exact v180에서 네 donor 모두 current owner/ref=0·known-failed 아님을 감사했고, runtime-unreferenced guard edge만 physical1497 top/3514 top/1050 right/2237 top에서 최소 투명화했다. C005는 게임 바이트를 건드리지 않고 Citra 정상 출력 및 `해=8B6C/physical1066` pixel-exact 증거에 따라 리뷰 표시만 `기간을 입력해 주십시오`로 보정했다. final changed cells=`[1050,1051,1424,1497,2164,2237,3441,3514]`; SHA-256은 `code=C3DBB60CC42295F65808D2CA5453DA5C26BC11144A83F80A0E7D98F31BC320F4`, `font=E375D9D9BDCCB9B19B2226879205DD836C97482BA1E782F91773B91FB53EBD16`. donor audit→builder→deterministic `--check`→독립 verifier PASS. PNG/기타 G1T/Message/Scenario/Original/Rebuild/Backup은 v180 byte-exact다. 권위 자료는 v181 targets/report/sealed baseline, donor audit, builder/verifier다.
- **직전 Patch 기준본: v180-intermediate (#172 M41 시나리오 대사 수정, 정적 검증 완료·Citra 재확인 pending)** — exact v179 전체 Patch 83파일을 `analysis\v180_issue172_scenario_m41_baseline\PatchSnapshot`에 봉인하고 **`RomFS/Message/msgsec21.dat` 1개만** 수정했다. v176에서 Citra로 확정한 초상 대화 `13.5×3` 규칙을 그대로 적용했다. 사용자 1안 `한인 따위에게는 못 넘겨준다!`는 14.0칸으로 초과했고, 2·3안은 모두 current font map에 없는 `겐`이 필요해 이번 G1T/font 무수정 범위와 충돌한다. 최종 M41은 **`가하하! / 이 땅은 우리 것이다! / 한인 따위엔 못 넘겨준다!`**이며 마지막 줄 12.0칸이다. `ESC K→ESC H` 제어, 0xC300 파일 크기, direct pointer geometry는 유지되고 실제 내용 변경은 direct41 + parent mirror18뿐이다. `msgsec21 SHA-256=37689EB1B3AFF04C848473E74EB1C5AA20C2FDA6F8EB75F8EFC2994EC43A69F6`. v179의 모든 font/PNG/G1T 및 나머지 82파일은 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 권위 자료는 v180 targets/report/sealed baseline과 v180 builder/verifier다.
- **직전 이미지 기준본: v179-intermediate (Opening/StartMenu 지정 PNG 2개 미세 조정 재리빌드, 정적 검증 완료·Citra 재확인 pending)** — exact v178 전체 Patch 83파일을 `analysis\v179_image_update_baseline\PatchSnapshot`에 봉인하고 미세 조정된 `Opening/ktlogo/ktlogo_000.png`, `StartMenu/title_up/title_up_000.png` 두 파일만 다시 1:1 삽입했다. 두 G1T 모두 sealed v178 컨테이너에서 texture 0만 교체했으며, 미언급 `ktlogo_001` texture 1은 v178과 byte-exact다. 변경 게임 파일은 정확히 **`RomFS/Opening/ktlogo.g1t`, `RomFS/StartMenu/title_up.g1t` 2개뿐**이다. 두 target은 `0x09 RGBA8 / 512×256`, source codec identity PASS, built readback 2/2 pixel-exact, header와 비대상 payload byte-exact다. 최종 SHA-256은 `ktlogo=84A2EB9B5403032EB438AC6AF2A06C104C237A9928D390DD4079BEF876547C4F`, `title_up=37ABEA5197AA05840C1F66415B31ED696FA60B9D2A3949F5C605AD316264B6C0`. v178 code/font/Message/Scenario와 나머지 81파일은 byte-exact이고 Original/Rebuild/Backup은 수정하지 않았다. 이미지 생성은 사용하지 않았다. builder→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 권위 자료는 v179 targets/report/sealed baseline과 v179 builder/verifier다.
- **직전 이미지 기준본: v178-intermediate** — v177 위에서 같은 texture 0 두 개를 갱신한 기준이며, v179은 해당 두 texture만 다시 미세 조정했다.
- **직전 이미지 기준본: v177-intermediate** — exact v176 위에서 `ktlogo_000/001`, `title_up_000` 세 texture를 갱신한 기준이다. v178은 그중 texture 0 두 개만 다시 갱신하며 v177 `ktlogo_001`은 byte-exact 계승한다.
- **직전 Patch 기준본: v176-intermediate (#170 시나리오 문구 개선 + #171 초상 대화창 줄잘림 전수 보정, 정적 검증 완료·Citra 재확인 pending)** — exact v175 전체 Patch 83파일을 `analysis\v176_issue170_171_scenario_wording_layout_baseline\PatchSnapshot`에 봉인하고 **`ExeFS/code.bin`, `RomFS/Message/msgsec21.dat` 2개만** 수정했다. #170은 C026을 사용자 1안 **`대의는 공손찬님께 있다 / 내 검으로 원소를 물리치겠다`**로 적용했다. `께`는 신규 글리프가 아니라 current effective map의 기존 low alias **`8DBC / physical1521`**라 font/G1T 변경 없이 사용 가능하다. M26은 **`적벽 회전`→`적벽 대전`**으로 교정했다. #171 Citra에서 M41의 15.0-unit 마지막 줄 `한인 따위에게 넘겨주지 않겠다!`가 `다!`까지 표시되지 않는 것이 확인되어 v174의 단일 `17.5×4` 규칙을 폐기하고 renderer를 분리했다: **msgsec21 초상 대화 M06~M25/M32~M41/M48~M53 = 13.5×3**, **무초상 서술 M00~M05/M26~M31/M42~M47/M54~M66 = 17.5×4**, hardcoded 시나리오/이벤트 185개는 보수적으로 **13.5×3**이다. v175 기준 초상 direct 초과 6건(M09/M17/M24/M32/M33/M41)과 code 2건(0x562B0/0x57040)을 전수 보정해 v176 위반 0으로 만들었다. M41은 **`한인 따위는 못 넘겨준다!`**, M09 `지금 우리 힘으론 부족하다`, M17은 3줄 재행갈이, M24/M32/M33도 13.5×3 안에서 재행갈이했다. code 0x562B0은 `촉 백성을 편안히 하는 것이...`, 0x57040은 `그분이 천하의 영웅입니다`로 안전폭을 맞췄다. 새 검수 권위는 **`Extracted_Text\scenario_text_review_v176.html`**이며 기존과 동일한 252개 전체를 renderer별 제한과 함께 표시한다. 최종 SHA-256은 `code=87093D7FA442A95553D198383D26EAC112F91811964D24EB78AD02B17438DADE`, `msg21=7AA7048EEB995D099452ACA0C6AA86C6FCD76CE3DAD25A3FD9E26FDBFE976C11`. builder deterministic `--check` + 독립 verifier PASS: Patch 83파일, msgsec21 direct 67/67, parent mirror 35개, 초상 36/서술 31, hardcoded 185/185, layout 위반 0, review 252행, v175 #169 사마휘 문구 보존을 확인했다. font/PNG/G1T/ARC와 나머지 81개 파일은 v175 byte-exact이며 **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.** 권위 자료는 v176 targets/report/sealed baseline, v176 builder/verifier, `scenario_text_review_v176.html`이다.
- **직전 Patch 기준본: v175-intermediate (#169 사마휘 가이드 존댓말 통일, 정적 검증 완료·Citra 재확인 pending)** — exact v174 전체 Patch 83파일을 `analysis\v175_issue169_sima_hui_tone_baseline\PatchSnapshot`에 봉인하고 **`ExeFS/code.bin` 1개만** 수정했다. #169 첫 화면 `삼국지2의 / 플레이 방법을 알려주지`의 실제 source는 `code.bin 0x5C040`, immutable Original visible capacity는 **33B**다. 요청 직역 존댓말 `삼국지2의 / 플레이 방법을 알려드리지요`는 **36B로 3B 초과**하므로 relocation 없이 자연스럽게 **`삼국지2 / 게임 방법을 알려드리지요` 32B/33B**로 적용했다. 뒤 질문 `들을까요？ @ 0x5C068`과 두 번째 화면 `3DS 모드의 / 특징을 알려드리지요 @ 0x5C078`은 그대로 보존했다. 사마휘 3DS 가이드 후속 hardcoded 15개도 말투를 감사해 반말 잔존 0으로 확인했다. 변경 허용 범위는 `0x5C040`의 33B fixed slot뿐이며 그 밖의 code.bin과 나머지 82개 Patch 파일, font/PNG/G1T/ARC는 v174 byte-exact다. 최종 `code.bin SHA-256=DBB3F5D1B56ED9D62753ED3E93FAED7D6D724D04AAD29677C028B6145925D2F7`. builder deterministic `--check`와 독립 verifier PASS, **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.** 권위 자료는 `analysis\v175_issue169_sima_hui_tone_targets.json`, `analysis\v175_issue169_sima_hui_tone_report.json`, sealed v174 baseline, `tools\audit_sangokushi2_v175_issue169_sima_hui_tone.py`, `tools\build_sangokushi2_v175_issue169_sima_hui_tone.py`, `tools\verify_sangokushi2_v175_issue169_sima_hui_tone.py`다.
- **직전 Patch 기준본: v174-intermediate (#168 시나리오 전체 source 재감사·줄바꿈 안전폭 교정·검수 범위 252개 확장, 정적 검증 완료·Citra 재확인 pending)** — exact v173 전체 Patch 83파일을 `analysis\v174_issue168_scenario_full_review_baseline\PatchSnapshot`에 봉인하고 **`ExeFS/code.bin`, `RomFS/Message/msgsec21.dat` 2개만** 수정했다. #168 Citra 실화면에서 18.5-unit 줄이 `가운/데`, `소리/에`로 강제 wrap되어 5번째 줄이 사라지는 것이 확인되어 v173의 msgsec21 `18.5×4` 정적 gate를 이 화면 class에서 폐기하고 **17.5×4**로 낮췄다. v173 67개 direct를 전수 재검사해 실제 초과 #3/#4/#5를 자연스럽게 재행갈이·축약했고 parent 조립 fragment도 같은 17.5-unit gate로 재검증했다. 또한 v173 review가 `msgsec21`만 포함해 불완전했음을 인정하고, 과거 #035~#047에서 확인된 **`code.bin` hardcoded 시나리오/이벤트 169개**를 다시 추적한 뒤 Original ADR 참조를 전수 감사해 **기존 inventory 누락 16개**를 추가 복구했다. 따라서 현재 검수 범위는 `msgsec21 67 + code.bin 185 = 총 252개`다. 사용자 제보 `원소와 조조 / 두 대국이 관도에서 격돌했다`는 `code.bin 0x58BE4`의 정상 시나리오 서술로 확인되어 새 HTML에 포함된다. 누락 16개 가운데 `0x5A9A0/0x5ABCC/0x5AE58`의 일본어 목표 달성 문구를 각각 `소패왕 손책 / 목표를 달성했습니다!`, `천하삼분지계 / 목표를 달성했습니다!`, `강유의 북진 / 목표를 달성했습니다!`로 한글화했고, hardcoded 15×3 gate를 넘던 `0x589B0`, `0x5A788`도 줄바꿈을 교정했다. `msgsec21 #41/#53`의 `ESC K/H`는 추출 오류가 아니라 immutable Original에 실제 존재하는 **문자폭 모드 제어코드**이며, v173처럼 Korean glyph를 K(반각) 상태에 두지 않고 v174는 `ESC K → ESC H → 한국어` 순서로 정규화했다. 최종 SHA-256은 `code=320F2C72770F186F1C4369B92B81A7222EC6D02DABA6F0869E26AE5718167333`, `msg21=D52E53ABB6022F440A0653955ECBAB7A186FF50C16D056A88FF11307B9740149`. 새 검수 권위는 `Extracted_Text\scenario_text_review_v174.html`이며 Original/v173/v174/바이트/폭/제어코드와 누락 복구 여부를 252개 전부 표시한다. builder deterministic `--check` + 독립 verifier PASS: msgsec21 direct 67/67, parent 36/36, hardcoded 185/185, recovered 16, Original main scenario ADR Japanese-like target 184개 전부 accounting, hardcoded 일본어 잔존 0, layout 위반 0. `font.g1t`, 모든 PNG/G1T/ARC와 나머지 81개 게임 파일은 v173 byte-exact이며 **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.** 권위 자료는 v174 targets/report/sealed baseline, v174 builder/verifier 및 `scenario_text_review_v174.html`이다.
- **직전 Patch 기준본: v173-intermediate (#167 시나리오 시작 대사 dual-route 전수 복구·리뷰 HTML 생성, 정적 검증 완료·Citra 재확인 pending)** — exact v172 전체 Patch 83파일을 `analysis\v173_issue167_scenario_dialogue_baseline\PatchSnapshot`에 봉인하고 **`RomFS/Message/msgsec21.dat` 1개만** 수정했다. #167 Citra 화면의 `E이라는 강한 바람은`, `? 모습이 변해`, 단독 `고`, `양을 장악했다`, `다스리려면` 같은 문두 손실은 `msgsec21`의 같은 u16 값이 WORD parent와 BYTE direct 양쪽에서 해석되는 dual-use 구조에서 direct route가 번역 후 늘어난 body 중간을 가리킨 것이 원인이었다. v173은 immutable Original의 완문을 기준으로 **67개 runtime direct route 전부**를 재구성했다. shared `word[1..36]`은 `V=0x5000+i*0x80`에 direct 완문, `2V`에 parent blob을 따로 mirror하고, `word[37..66]` 30개는 `0x6400+i*0x80`의 독립 direct mirror, 마지막 hidden direct66은 body 첫 `ESC H=1B48`를 pointer `0x481B`로 동시에 보존한다. 최종 `msgsec21`은 **49,920B(0xC300)**로 과거 Message loader 상한 `0xD000` 미만이며 67/67 direct, 36/36 parent, 36 shared BYTE/WORD mirror, 30 tail direct, hidden direct66을 독립 verifier로 확인했다. 모든 시나리오 시작 대사는 **한 줄 18.5 units 이하 / 최대 4줄**, 질문부호는 전각 `？` 2회·ASCII `?` 0회다. `font.g1t`, `code.bin`, 모든 PNG/G1T/ARC와 나머지 82개 게임 파일은 v172 byte-exact다. 최종 `msgsec21 SHA-256=04995CE03ABF203A51D332B4B117401A5925D8DA19B37765F2EAFE93A7824B98`. `Extracted_Text\scenario_text_review_v173.html`에 **67개 전체의 일본어 원문 / v172 runtime 표시 / v173 번역 / 사용 바이트 / 구조상 허용 바이트 / 줄 폭 / source fragment**를 수록했다. builder deterministic `--check`와 독립 verifier 모두 PASS했으며 **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.** 권위 자료는 v173 targets/report/sealed baseline, v173 builder/verifier, 위 review HTML이다.
- **직전 Patch 기준본: v172-intermediate (#166 일기토 문구 전수 개선, 정적 검증 완료·Citra 재확인 pending)** — exact v171 전체 Patch 83파일을 `analysis\v172_issue166_duel_text_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec12.dat` **1개만** 수정했다. 적용 ID는 #16/#17/#18/#20/#21/#25/#29/#31/#32이며 direct14/direct19/direct20/direct21은 같은 물리 문자열로 함께 갱신됐다. #16은 사용자 대체안 `과소평가했군 / 진심으로 간다!` 27B/31B를 적용했다. #20은 전각 `？`를 사용해 15B/16B다. #26/direct16 요청안 `오랜만에 / 만난 호적수!`는 21B로 20B를 1B 초과하고 `랜` 안전 donor도 없어 v171을 byte-exact 보존했다. font/이미지는 변경하지 않았고 msgsec12 860B/25 words/hidden direct24/36 separator 구조를 유지했다. 독립 verifier와 deterministic `--check` PASS, `msg12=F2620FF1C6F2A3DB35E8E0BDE41A9346AF0A64AC2E12DB0F097C108CC4EEDBFB`. 권위 자료는 v172 targets/report/sealed baseline과 v172 builder/verifier다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v171-intermediate (#165 일기토 문구 개선·기존 한글 보호 donor 재배정, 정적 검증 완료·Citra 재확인 pending)** — exact v170 전체 Patch 83파일을 `analysis\v171_issue165_duel_text_baseline\PatchSnapshot`에 봉인하고 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec12.dat` **2개만** 수정했다. #165 parent-only 어색한 문구는 `너는 상대 못돼`, `네놈 상대론 / 시간낭비다`로 보정했고 direct0은 `<NAME1>을(를) 모르면 / 덤벼라`로 수정했다. 첫 사용자안 `넌 내 상대가 안된다/안돼`는 exact mixed-parent span 16B에 각각 19B/17B로 초과하여, 구조를 움직이지 않는 14B `너는 상대 못돼`를 적용했다. 사용자 지시에 따라 기존 한글 physical 셀은 **0개 제거**했으며, current Hangul owner/code-text/Message/Scenario ref=0인 stock CJK만 donor로 사용했다: `놈=8F4B/physical1785(stock 習)`, `벼=8FD4/physical1921(stock 蕉, y=+1)`. font diff는 정확히 `[1785,1921]` 두 셀, packed readback pixel-exact다. `msgsec12`는 v170의 860B/25 words/15 WORD-parent+25 BYTE-direct/hidden direct24/36 separator 구조를 그대로 유지하며 #165 허용 span 밖 diff=0, direct0 외 나머지 24 direct는 v170 byte-exact다. 빌드→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 최종 SHA-256은 `font=6F933CF73AF9B4C8F91B61C71512130B910B652253A57BB368AE71610755326D`, `msg12=04DC7656E62A6DA04CC97B25CD4444DC0106540BC6F4D55783DA1C25FF96CA47`. 권위 자료는 v171 donor audit/targets/report/sealed baseline과 v171 audit/builder/verifier다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v170-intermediate (#163 일기토 mixed pointer 구조 복구·포로 처리 전각 물음표 후속, 정적 검증 완료·Citra 재확인 pending)** — exact v169 전체 Patch 83파일을 `analysis\v170_issue163_duel_pointer_repair_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec09.dat`, `msgsec12.dat` **2개만** 수정했다. #163 후속 Citra 화면의 한자 혼입과 `Q0??V` garbage 원인은 v164가 `msgsec12`를 25-entry flat BYTE table로 오판하여 `word1..word24`를 EOF BYTE 주소로 redirect한 데 있었다. 실제 immutable `msgsec12`는 860B이며 **15 WORD-offset parent + 25 BYTE direct가 겹치는 mixed 구조**, hidden direct24 `0x034A @ body 0x32`, 49 physical fragments, 36 separator를 가진다. v170은 Original의 25 words/hidden direct24/file size/separator geometry를 exact 복원하고 49 fragment를 각 fixed capacity 안에서 한글화하여 두 runtime route를 동시에 살렸다. Citra 제보 direct17은 **`네 이름, / 기억하마`**, direct24는 `간신히 도망쳤군`으로 static readback 된다. 포로 처리 `msgsec09 0x2AA`는 **`<NAME1>님, <NAME2> 처분은？`** 19B + padding1B로 바꿔 stock 전각 `？=8148`을 사용한다. 물음표 정책은 전각 우선이며, 전각이 실제 고정 슬롯을 초과하고 축약/안전 relocation이 적절하지 않을 때만 ASCII `?`를 최후 fallback으로 허용한다. builder deterministic `--check`와 독립 verifier PASS: Patch 83파일 exact manifest, msgsec12 860B/25 words/15 parent/49 fragments/25 direct/hidden direct24/36 separator 검증, msg09 전각 1·ASCII 0, font/이미지 불변. 최종 SHA-256은 `msg09=8F2C2A076891661B29245572C835C24F7B5EFD20F9BAF4AFC0EC539F29427098`, `msg12=C582CE1EAC5E866A6486097874CBAECA931C031263A3E3434BBE002331C3CA90`. 권위 자료는 v170 targets/report/sealed baseline과 v170 builder/verifier다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v169-intermediate (#163 일기토 한자·포로 처리·등용 일본어 및 대사 잔존 전수 보정, 정적 검증 완료·Citra 재확인 pending)** — exact v168 전체 Patch 83파일을 `analysis\v169_issue163_dialogue_residue_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat`, `msgsec10.dat`, `msgsec12.dat`, `msgsec15.dat`, `msgsec21.dat` **12개만** 수정했다. #163 Citra 실화면에서 v168 `놈=89B8`이 stock 한자 `穏`으로 출력된 것이 확인되어 `89B8`도 runtime 실패 alias로 판정했고, 일기토/전투 active 8문장을 `네 목을 베어도`, `너를 상대하려면`, `너 따위는`, `겁쟁이로군` 등 자연스러운 표현으로 재작성해 8문장 내 `9951/89B8` hit를 모두 0으로 만들었다. 포로 처리 상단은 `msgsec09 0x2AA`의 20B dual-use span에 **`<NAME1>님, <NAME2>의 처분은?`**을 exact-width로 넣고 +10 interior suffix `의 처분은?`를 보존했다. 이 span은 전각 `？` 사용 시 21B가 되어 overflow하므로 ASCII `?`가 의도된 용량 예외다. 화면의 별도 일본어 버튼 actual owner `code.bin 0x1D39C8=解き放す`, `0x1D39D4=首を斬る`는 각각 NUL-terminated **`석방/처단`**으로 수정했고 pointer는 건드리지 않았다. 조홍 등용 거절 `msgsec09 0x30B=逆賊ﾆﾅﾄﾞ力ﾊ貸ｻﾇ / 早ｸ斬ﾚ！`는 사용자 최종 선택에 따라 exact 24B **`역적은 못 돕는다 / 베어라!`**로 적용했다. 후속 검토안 `역적을 도울 수는 없다 / 베어라!`는 29B로 fixed 24B fragment를 5B 초과해 적용하지 않았다. 광역 대사 감사에서 `msgsec05` 일본어 direct 12건, `msgsec07` shared-chain 잔존 8건, 한글 2바이트 중간을 가리키던 `msgsec08 direct127`과 `msgsec21 direct15/23/34/35`도 immutable Original 완문 기준으로 EOF standalone append+redirect했다. 또한 `育/裡/冖/廊/儻/弍/僂/亊`처럼 보이는 과거 한글 alias 잔존을 Korean runtime 문맥에 한정해 current safe alias로 2B→2B 전수 재인코딩했으며 targets 기준 Message 145 reviewed rows + code dialogue 80 reviewed rows를 교정하고 scoped active stale-alias hit=0을 확인했다. `font.g1t`는 v168 SHA `444F30EF09F863F38F74F3AE32A75218D4594CBA22BBFD56123C2E7572046A18` 그대로이며 모든 PNG/G1T/ARC와 Original/Rebuild/Backup은 무수정이다. builder → deterministic `--check` → 독립 verifier PASS, Patch 83파일 manifest exact match. verifier는 direct rewrite 33건, msgsec09 fixed span 2건, 실제 석방/처단 버튼, 8개 duel line의 실패 놈 alias 0, scoped stale alias 0, non-flat msgsec09를 제외한 flat dialogue 일본어 잔존 0을 확인했다. 다만 generic residue scan에는 `msgsec03/10/18`의 별도 system/editor/help raw 일본어 후보와 non-flat `msgsec09` parser noise가 남아 있으므로 **게임 전체 일본어 0이라고 표현하지 않는다.** 권위 자료는 `analysis\v169_issue163_dialogue_residue_targets.json`, `analysis\v169_issue163_dialogue_residue_report.json`, sealed v168 baseline, `tools\build_sangokushi2_v169_issue163_dialogue_residue.py`, `tools\verify_sangokushi2_v169_issue163_dialogue_residue.py`다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v168-intermediate (#162 후속 퇴각 도움말 중복·`놈=9951` 글리프 복구, 정적 검증 완료·Citra 재확인 pending)** — exact v167 전체 Patch 83파일을 `analysis\v168_retreat_help_nom_glyph_baseline\PatchSnapshot`에 봉인하고 `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec02.dat`, `msgsec10.dat`, `msgsec12.dat` **4개만** 수정했다. `msgsec10 direct93/94`의 수동 `0A`가 스크롤 도움말에서 후반 문장을 다음 행에 중복 그리게 하므로 각각 **`아군 전체 부대를 인접한 자국 도시·공백지로 퇴각시킵니다.`**, **`성내 무장을 인접한 자국 도시·공백지로 퇴각시킵니다.`**인 단일 standalone 문자열로 다시 append+redirect했다. 두 active target은 `0A=0`, `자국 도시·공백지로 퇴각시킵니다.` 출현이 각 1회다. `놈`은 과거 Citra 실패 alias `9951`이 v155 map에 남아 v164 전투 대사에 재도입된 것이 원인이며, current active 전체를 감사해 `msgsec02` 5건 + `msgsec12` 3건을 새 **`놈=89B8/physical765`**로 함께 재인코딩했다. `89B8`은 폐기 v13 bulk에서 `않`에 사용된 이력은 있으나 exact v167 current owner/code text/Message/Scenario ref=0이고 known-failed가 아닌 저위 valid-JIS 한자 `穏`이다. Sam3 `놈`을 x=0에 배치해 right/bottom edge=0으로 만들었고 font diff는 physical765 한 셀뿐이다. 최종 active `9951=0`, active `89B8=8`, code/이미지/비대상 파일은 v167 byte-exact다. builder→독립 verifier→deterministic `--check` PASS. SHA-256은 `font=444F30EF09F863F38F74F3AE32A75218D4594CBA22BBFD56123C2E7572046A18`, `msg02=22463A80B6E221F4C0F0ECC525867EE4F74BCDF201552FDAC61FEBFD0F5FDCC1`, `msg10=F7D101181C830E740A58213A35FFD916C285B505648117621878DA31A6100F71`, `msg12=6C8B87085F339C32C4111CE23D6947263C86463E9EA6E131EC1ED9371246E1F0`. 권위 자료는 v168 donor audit/targets/report/sealed baseline과 v168 builder/verifier다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v167-intermediate (#162 전쟁 오역·위임 무출력·fixed label 보정, 정적 검증 완료·Citra 재확인 pending)** — exact v166 전체 Patch 83파일을 `analysis\v167_issue162_battle_text_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec04.dat`, `msgsec07.dat`, `msgsec12.dat` **5개만** 수정했다. 위임 무출력은 `msgsec04 word0=55`의 마지막 direct54가 과거 body 위치 `0x583`에 남아 direct37 `0x587` 앞 전각 `？`만 읽은 포인터 오류였다. 일본판 원문 `解除ﾃﾞｷﾏｾﾝｶﾞ / ﾖﾛｼｲﾃﾞｽｶ？`를 사용자 제공 화면과 대조해 **`해제할 수 없습니다만 / 괜찮으시겠습니까？`**로 만들고 EOF `0x9CF`에 standalone append한 뒤 direct54만 redirect했다. direct37과 나머지 pointer는 보존했다. `msgsec02`는 v164에서 이미 한국어라 보존했던 압축/오역 20건에 기동력 문장 2건과 `났=9971` 1건을 더한 **23 direct**를 원문 단위로 재번역했다. 핵심 표기는 `일기토에 응할까요？`, `공격할까요？`, `흘리기 대기할까요？`, `튕겨내기로 대기할까요？`, `누구에게 할까요？`, `소화할까요？`, `<무장명>의 기동력이 / N 이(가) 되었습니다.`이며 모든 질문은 전각 `？`다. 퇴각은 사용자 지정대로 `<무장명>는(은) 어디로 / 퇴각할까요？`로 direct22 기존 span 안에서 수정했다. `code.bin` fixed label은 `작적무장/매복무장/일반무장`, `고산/낮은산/숲/초지/평지/다리`로 정리했다. `다리`는 2B `橋` 슬롯을 넘기지 않고 ref=0 zero-padding `0x1CF96E`에 5B C-string으로 두고 pointer table `0x1DC2D8` 한 곳만 redirect했다. Citra screen-class 위험 alias `났=9971`의 active code/Message 문장은 의미를 유지한 자연어로 재작성해 active Message hit=0, code는 비문자 allowlist 3곳만 남겼다. 신규 glyph=0, `font.g1t`, 모든 이미지/G1T/ARC 및 비대상 78파일은 v166 byte-exact다. builder→독립 verifier→deterministic `--check`가 PASS했다. 최종 SHA-256은 `code=61902CE069F3E2A2DE97F3812E4F1BDA0E28A575FC74734948DDE79F6DB93566`, `msg02=8C91B79ADDBE54AC366867C8083EAEC43AFED25654DE85C11303D9C9BFED7B69`, `msg04=26692C41B560C1B3C1559F48A4393FD238B2B5D3F9529653F1C98DCF79CA73AF`, `msg07=C9C5AD55F6CBA486F925C99683AF3880A3153C75FCF23284AF227DA748C9F13E`, `msg12=2E400DEF0E41DA6AAEF72F2D237A7F467C96956516F5C6316C2867D2924C57B8`. 권위 자료는 v167 targets/report/sealed baseline과 v167 builder/verifier다. **Citra 실화면 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v166-intermediate (#160 후속 이중조사·무장정보 상단 종사표시 수정, 정적 검증 완료·Citra 재확인 pending)** — exact v165 전체 Patch 83파일을 `analysis\v166_issue160_followup_baseline\PatchSnapshot`에 봉인하고 **`ExeFS/code.bin` 1개만** 수정했다. Citra 실화면 `양의은 현재 훈련 중입니다 / 계속합니까？`의 actual owner는 `code.bin 0xB623C`, `0x114790`의 동일 formatter 두 copy이며 기존 `%s은 %s%s 중입니다\n계속합니까？` 31B를 **`%s은(는) %s%s 중입니다\n계속합니까？`** 35B로 교체했다. 각 Original allocation은 45B visible + NUL이라 fallback 없이 사용자 1안이 안전하게 들어가며 뒤 code/data는 byte-exact다. 무장정보 상단 `훈련に1カ月従事`는 이미 한국어인 `msgsec07 0x48B` duplicate가 아니라 **`code.bin 0x641F4`의 20B inline formatter**가 actual source임을 Citra 문구와 raw가 일치해 확정했다. 사용자 우선안은 1안 25B, 2안 22B로 overflow하고 3안 **`OO X개월 진행`**이 20B exact라 `ESC C4 %s ESC C1 %u개월 진행`으로 same-width 치환했다. 같은 inline class의 무개월 `OOに従事 @ 0x64220`도 14B allocation 안에서 **`OO 진행`**으로 정리했다. 신규 glyph=0, `font.g1t`, 모든 Message/PNG/G1T 및 비대상 82파일은 v165 byte-exact, Original/Rebuild/Backup은 무수정이다. 독립 verifier → deterministic `--check` → verifier 재실행 PASS. 최종 `code.bin SHA-256=82A38F1F98E31FA29D51F46185E0F7F0C8D4BBC08BCB59D5DBC904374CF861FA`. 권위 자료는 `analysis\v166_issue160_followup_targets.json`, `analysis\v166_issue160_followup_report.json`, sealed v165 baseline, `tools\build_sangokushi2_v166_issue160_followup.py`, `tools\verify_sangokushi2_v166_issue160_followup.py`다. **Citra 재확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v165-intermediate (#160/#161 상태명·조선 메뉴 한글화, 정적 검증 완료·Citra 재확인 pending)** — exact v164 전체 Patch 83파일을 `analysis\v165_issue160_161_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `msgsec10.dat` **3개만** 수정했다. #160의 `현재 %s에서 %s 중입니다` template 자체는 이미 한국어였고 실제 혼입 원인은 `code.bin 0x1DB6DC..0x1DB704` current-command pointer table의 일본어 dynamic label 5개였다. `訓練/捜索/耕作/治水/密偵`을 각각 same-width **`훈련/수색/경작/치수/밀정`**으로 바꿔 최종 table을 `준비/훈련/수색/개발/경작/치수/투자/밀정/건조/건조/건조`로 만들었고, `msgsec04/06/07/08`의 모든 live `현재 ...` direct를 추가 감사해 일본어/CJK 혼입 0건을 확인했다. #161 조선 상단 설명 actual source는 `msgsec10 direct54~56 @ 0xBE4/0xC0F/0xC3A`; 각각 **`건조 기간 2개월, 금 1000 필요` / `4개월, 금 2000 필요` / `6개월, 금 3000 필요`**로 원 40B span 안에서 in-place + space padding하여 pointer/header/file size/separator를 보존했다. 실행 시 보이던 `시키시겠습니까？`는 current `msgsec07 header[142] -> 0x243D`가 다른 문장의 interior suffix를 가리키는 실제 Citra-visible source이며, 사용자 1안 **`군선을 건조하시겠습니까？`** 25B를 기존 EOF `0xEAB2`에 append + `05 05 05` 후 header[142]만 redirect했다. 나머지 387-word header/body는 v164 byte-exact다. 신규 glyph=0, `font.g1t`와 모든 PNG/G1T 및 비대상 80파일은 v164 byte-exact, Original/Rebuild/Backup은 수정하지 않았다. builder → 독립 verifier → deterministic `--check` PASS. 최종 SHA-256은 `code=708661AC02E552242BE6B035835F2824D62CAAA96C463C5F6A7F41D1E1FFB3B8`, `msg07=F6AED0E0A791515D57FC9745754BE416415694A47A57B2DAEAC288F09F541E4B`, `msg10=71F43D337380E4A23E9935B54EDA881883C1B13C2CCBB3FE9EC57AE90EFF2ADF`. 권위 자료는 `analysis\v165_issue160_161_targets.json`, `analysis\v165_issue160_161_report.json`, `analysis\v165_issue160_161_residue_classification.json`, sealed v164 baseline, `tools\build_sangokushi2_v165_issue160_161.py`, `tools\verify_sangokushi2_v165_issue160_161.py`다.
- **직전 Patch 기준본: v164-intermediate (#115/#116 전쟁 runtime 전수 감사·한글화, 정적 검증 완료·Citra 재확인 pending)** — exact v163 전체 Patch 83파일을 `analysis\v164_issue115_116_war_sweep_baseline\PatchSnapshot`에 봉인하고, 실제 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec02.dat`, `msgsec03.dat`, `msgsec04.dat`, `msgsec05.dat`, `msgsec10.dat`, `msgsec12.dat`, `msgsec15.dat` 8개**다. `font.g1t`, 모든 PNG/G1T, Original/Rebuild/Backup 및 나머지 75파일은 v163과 byte-exact다. #115/#116 스크린샷만 핀포인트하지 않고 live-pointer 기준으로 전쟁 source를 확장 감사했다. 핵심 `msgsec02.dat`는 `word0=157`, 첫 body=`0x013C`인 absolute-BYTE direct table로 확정했고 **157/157 entry를 감사해 일본어 137개를 EOF standalone 완문 append + 해당 137 pointer만 redirect**, 이미 한국어인 20개는 pointer/payload byte-exact 보존했다. 포획·부상·사망·퇴각·추격·전술·화재·함정·이동·공격·궁시·성벽넘기와 화계/복병/위령/상잔/논파/도발/설득/고무/유인/기도/치료/충차/생존술 등 전쟁 본체를 포함한다. 추가 전수 감사에서 `msgsec12.dat` **25/25 direct 전부가 일기토/인물 전투 대사**로 확인되어 전부 한글화했고, `msgsec03` 전투 배치/시간/날씨/전장명 12건, `msgsec05` 매복무장/전투 재개 3건, `msgsec10` 이동·전군퇴각·성내퇴각·출진 상단 도움말 4건, `msgsec15` 안개 전투 도움말 3건도 같은 append+pointer 방식으로 처리했다. #115 잘린 퇴각문은 mixed/dual-use 위험이 있는 `msgsec04 direct22 @ 0x08D6`의 기존 span 안에서만 **`<무장명>을(를) 어디로 / 퇴각할까요？`**로 in-place 수정해 header/pointer를 움직이지 않았다. `code.bin`은 `0x1CEFC4 성벽넘→벽넘기`, `0x1CEFBC 用兵術→용병술`, 전쟁 자원 `軍資金/援軍/増援→군자금/원군/증원`, 전투 지형 `湿地/湖水/建物/道/関/門/城/川→습지/호수/건물/길/관/문/성/강`을 exact-width/NUL-preserved 치환했다. 기존 fragment `translation_ko`에는 `조심하십시오요`, `同士→동사`, Shift-JIS 반쪽바이트처럼 완문 조립 시 깨지는 항목이 있어 **fragment 값을 직접 runtime에 넣지 않고 실제 direct pointer 원문을 완문 단위로 복원한 뒤 번역**했다. 신규 glyph 0, 모든 target 한 줄 20 cells 이하다. builder `--check` deterministic PASS 후 독립 verifier PASS. 최종 광역 live-pointer 감사에서 scoped 전쟁 runtime 일본어 잔존은 **0건**이며 남은 후보는 `msgsec01` 챌린지 설명 1, `msgsec10` 신무장 편집 도움말 1, `msgsec18` 편집 도움말 4, `msgsec20` 열전 104, code 편집/옵션/외교 3건으로 전쟁 범위 밖임을 `analysis\v164_battle_residue_classification.json`에 명시했다. 최종 SHA-256은 `code=DA7AED5F12AC36703C8876D98DCD06919FAA92B9F1AD12E4DA284E9589F03F4A`, `msg02=8D796318738E0E2240C9B6BD936F2A0254AC76454B310C00582A3F386395FB5A`, `msg03=A12ED4089D118C6C396BA32C434D298EDB35721877735B323BBF368A9318AEE7`, `msg04=56BACCFC8360A14DD64498F4E21763E286FE08FF5C1CB88CC827D40DFC9248EA`, `msg05=080167D8FFA20178E2806E47A315E83FA0F6F01CABADBDE05695903F533F0D3F`, `msg10=D9E096BEF9D242357C7B733E0C8E148076B5AC5C48829A2E1DED76CB480D4E22`, `msg12=6DDF329424C33A5DB8C13556EEA3049D85823F1E8888E94D24DDF2DD6050C963`, `msg15=EC4BEA114C7E6B2F0D21984A9B4DE3FB56BD589B89CECA0D38C237C1F066124C`. **Citra 실화면 검수 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v163-intermediate (챌린지 시나리오 `목표확인` 메뉴/상단 도움말 한글화, 정적 검증 완료·Citra 재확인 pending)** — exact v162 전체 Patch 83파일을 `analysis\v163_challenge_goal_menu_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` **2개만** 수정했다. 챌린지 시나리오에서만 추가되는 `目標確認` 버튼의 실제 owner는 기존 `세이브/로드/환경설정/편집기능/BGM변경/게임종료`와 같은 fixed-label pool의 `code.bin 0x1CF7A4`이며 refs `0x1DC168/0x1DC184 -> VA 0x2CF7A4`; 8B `目標確認`을 정확히 8B **`목표확인`**으로 same-width 치환하고 NUL/pointer를 보존했다. 상단 `シナリオの目標を確認します。`는 `msgsec10_0028_003 @ 0xECD` 9B와 `msgsec10_0029_000 @ 0xED6` 14B가 separator 없이 이어지는 split-source이며, fixed start `0xED6`과 separator `0xEE4`를 움직이지 않고 각각 **`시나리오 `**(9B exact) + **`목표 확인하기`**(13B+padding1B)로 in-place 교체해 기대 표시는 **`시나리오 목표 확인하기`**다. `msgsec10` header/file size/separator, v162 `msgsec01` 전각 물음표 수정, font, 모든 PNG/G1T와 비대상 81파일은 byte-exact다. preflight→build→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 최종 SHA-256은 `code=1CACBA04D4D0CFAC8F7492CF7A2F87E30DEFBA4945FDDA0682EF2720716C7E7A`, `msgsec10=545B5F8B0E6D93C82785E3437E12289FC0C577FBB38B93474BC8569261EEE6AA`. 첨부 스크린샷은 runtime source 확인용으로만 사용했고 이미지 편집/생성은 하지 않았다.
- **직전 Patch 기준본: v162-intermediate (#158 물음표 표시 후속, 정적 검증 완료·Citra 재확인 pending)** — exact v161 전체 Patch 83파일을 `analysis\v162_issue158_questionmark_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec01.dat` **1개만** 수정했다. 사용자 Citra Nightly 2104 실화면에서 `무장 편집을 중지할까요?`, `작성을 중지할까요?`, `이름 설정 중지할까요?`의 끝 ASCII `?=0x3F`가 작고 낮게 표시되는 것이 확인되어 v161의 "ASCII ? 보존" 정책은 이 편집기 화면 class에서 폐기했다. 동일 계열 direct `27,28,29,69,70,71,72,73,75,96,97` **11개 / 물음표 12개**를 전수해 각 `3F 20`(`?`+직후 padding)을 stock 전각 `？=81 48`로 same-width 교체했다. 따라서 파일 크기, 201개 pointer table, 모든 `05 05 05` separator 물리 offset, direct73 내부 경계가 전부 byte-exact다. #155의 `code.bin/msgsec14`, font, 모든 PNG/G1T 및 나머지 82파일은 v161과 byte-exact다. preflight→build→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 최종 `msgsec01 SHA-256=7B3AC1E35D8A8810384199F4F6D8F32A35E8018D3A1B52F13B33FAE8614E0BC2`. v161 실패 증거=`analysis\v161_issue158_ascii_question_runtime_failure.json`. **이미지 편집은 수행하지 않았으며, 첨부 스크린샷은 runtime 증거로만 사용했다.**
- **직전 Patch 기준본: v161-intermediate (#155/#158 정적 검증 완료, Citra 재확인 pending)** — exact v160 전체 Patch 83파일을 `analysis\v161_issue155_158_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec14.dat` **2개만** 수정했다. #155는 v154에서 처리한 이벤트 CG `msgsec14 direct0~26`과 별개로 실제 목록에 남아 있던 **두 번째 목록 class direct27~49 23개**를 전수 확인해 `조운/관우/일기토/논의/임명/공명의 죽음·1·2/통일/장각/불타는 낙양/오군/촉군/남만/훈련/기원/조위의 군신/동오의 군신/대승리/촉한의 군신/선인/북진 성공/삼국지Ⅲ/삼국지 2`로 EOF append + 해당 u16 pointer만 redirect했다. direct22 `유성 지다`는 사용자 지정 **`별이 지다`**로 same-width in-place 교체했고, 잠금 placeholder direct50~53과 item 설명 direct54~120은 byte-exact 보존했다. Issue155 스크린샷의 상단 `閲覧する項目...`은 current v160 디스크에서는 이미 `항목을 선택하세요`로 고쳐져 있어 `msgsec01`을 다시 쓰지 않았다. #158은 공용 `%s 설정 중지할까요?` 템플릿이 이미 한국어 + ASCII `?`임을 확인하고, 동적 `%s` 인자인 `code.bin 0x1CFA78=名前`만 same-width **`이름`**으로 바꿔 기대 조립을 `이름 설정 중지할까요?`로 만들었다. editor 질문 direct 9개도 전부 ASCII `?`임을 verifier로 고정했다. `font.g1t`, PNG/G1T 및 비대상 81파일은 v160과 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier 재실행 PASS. 최종 SHA-256은 `code=AAEE539256C5410A9071F133106AF33161F4CD649C45D77D968D00D4EF0ED832`, `msgsec14=B88405B699A0C257D641F800679956B4DF3B4F4C75554FBBFDC4954EC84E293D`. **사용자는 v160에서 대부분 수정되었다고 확인했으나 #158 잔여를 제보했으므로 v160 전체를 blanket runtime 승인으로 표현하지 않는다. v161은 Citra 재확인 pending이다.**
- **직전 Patch 기준본: v160-intermediate (#150/#151/#154 정적 검증 완료, 사용자 실화면에서 대부분 정상·#158 잔여 확인)** — Citra 승인된 exact v159 전체 Patch 83파일을 `analysis\v160_issue150_151_154_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` **2개만** 수정했다. #150은 `code.bin 0x1696A8` 동적 formatter를 `%s %s을(를) 해고합니까?`로 교정하되 `0x1696C0`부터 시작하는 ARM literal pool을 침범하지 않도록 23B+NUL=24B allocation에 정확히 맞췄다. #151은 신분 7종 전체를 감사해 유일한 일본어 `在野@0x1CEE44`만 same-width `재야`로 바꿨다. #154는 `msgsec01` direct29 + direct149~173 중 정상 direct164 제외 **25개**와 `code.bin` 능력치/성격/전기 카테고리 **14개** fixed label을 각각 실제 source class에서 수정했다. direct70의 ASCII `?`는 이미 정상이라 byte-exact 보존했고, 전기 화면 빨간 라벨은 문자열만 same-width 치환하여 caller/UI style을 보존했다. 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재PASS, 비대상 81파일 byte-exact, v159 #152 경로 보존. 최종 SHA-256은 `code=58AE393D68F0702CC8A59CCC9DA836DB0A16DC60FC02FF7BCFE582F292A0792A`, `msgsec01=1699F9677CAB54ABFE4D2154A4D87249210C785F7F1E14C62CACD864888E6B24`. **정적 권위는 v160이지만 Citra runtime 승인 전이므로 마지막 실화면 승인본은 아래 v159다.**
- **직전 Citra 승인본: v159-intermediate (#152 shared-literal 회귀 수정, Citra 승인 완료)** — exact v158을 `analysis\v159_issue152_shared_literal_fix_baseline\PatchSnapshot`에 봉인했다. #152의 실제 원인은 문자열/NUL/폰트가 아니라 v71 #021에서 `code.bin 0x16ED18` shared table literal을 reward mini-table `0x002D6560`으로 리다이렉트하면서 **+8 member를 비워 둔 회귀**다. 이 literal은 `0x16E9D4`에서 +4(#021 `줄까요?`)를 읽는 동시에 `0x16EA44`에서 +8(#152 `군주만...`)도 읽는다. v159는 mini-table **`0x1D6568`(+8)만 `0x00000000 -> 0x002D1524`**로 채웠고 `0x16ED18=0x002D6560`, `0x1D6564=0x002D6580`의 #021 경로는 byte-exact 보존했다. v158 대비 변경 파일은 `ExeFS/code.bin` 1개, changed byte offsets는 `0x1D6568/69/6A`뿐이다. 다른 reward literal `0x161C64`는 두 xref 모두 +4만 사용해 동일 회귀가 없다. preflight→build→독립 verifier→deterministic check→verifier PASS, `code.bin SHA-256=913BED0A95C7AE0F60086A3BB7D500782CE01234A3B3B55A463640E037F99B63`. 사용자 Citra Nightly 2104 실화면에서 **`군주만 가능합니다.` 정상 출력이 확인되어 v159의 shared-literal/member-offset 진단과 수정법이 runtime 승인됐다.** 승인 증거=`analysis\v159_issue152_citra_runtime_confirmation.json`. 향후 팝업 프레임만 뜨고 본문이 비는 경우에는 table-base relocation 이력과 모든 xref/member offset을 먼저 확인한다.
- **직전 Patch 기준본: v158-intermediate (#152 Citra 실패 확인, source-path 판정 폐기)** — exact v157 83파일을 `analysis\v158_issue152_visit_popup_baseline\PatchSnapshot`에 봉인하고 #152 `방문` 제한 팝업만 최소 수정했다. 일본판 `君主しかできません`의 정적 source는 `code.bin 0x1D1524` 한 곳뿐이며, 등용 메뉴는 `0x16E7C4`에서 record index 1로 생성되고 `방문` selection index 2가 `0x16EA98` 분기를 거쳐 `0x16EA90 -> VA 0x001EDE74` 팝업을 호출하는 것으로 분석됐다. pointer `0x1E5254 -> VA 0x002D1524`는 Original/v157/v158 모두 byte-exact다. v158은 `군주만 가능합니다.`를 Original과 같은 18B visible 길이로 맞추기 위해 `0x1D1535` 한 바이트만 `00→2E`로 바꿨고, 정적 검증은 모두 PASS했다. **그러나 사용자 Citra Nightly 2104 실화면에서 v158도 팝업 프레임만 뜨고 텍스트는 완전히 비었다.** 따라서 조기 NUL/terminator 가설은 폐기한다. 실패 기록=`analysis\v158_issue152_citra_runtime_failure.json`. 현재 Patch 파일은 더 수정하지 않는다. 다음 단계는 Citra 내장 RPC(게임 실행 시 자동 시작)를 이용해 live RAM `0x002D1524`와 pointer `0x002E5254`를 읽고, 같은 RAM 슬롯을 Original 일본어 `君主しかできません`으로 임시 치환한 뒤 `방문`을 재실행하는 differential test다. 일본어가 보이면 source/branch는 맞고 한국어 alias/decoder/render-path 문제, 일본어도 비면 source 경로 추적이 틀린 것으로 보고 GDB stub 또는 임시 instrumentation으로 실제 r0를 잡는다.
- **직전 Patch 기준본: v157-intermediate (Citra 정렬 재확인 pending)** — exact v156을 `analysis\v157_issue157_alignment_baseline\PatchSnapshot`에 봉인하고, 사용자 v156 Citra 실화면에서 확인된 중앙정렬 회귀만 최소 보정했다. v156 대비 변경 게임 파일은 **`ExeFS/code.bin` 1개뿐**이다. 메인 메뉴 `BGM변경`의 visible center가 정상 버튼보다 약 9.5px 왼쪽인 원인은 v156의 trailing ASCII space 3B였으며, `0x1CF750`을 **원본 전각 `ＢＧＭ` 6B + 한글 `변경` 4B = 정확히 10B**인 `ＢＧＭ변경`으로 만들어 trailing padding을 0B로 했다. BGM 설정의 `전략`은 `방랑/전쟁`보다 5px 왼쪽이었고 v156 trailing space 1B의 절반과 일치했다. leading space로 옮기면 과보정되므로 `0x1CFE30`에서 즉시 NUL 종료하여 표시 폭을 두 한글 글자만 계산하게 했다. 다음 `0x1CFE31`도 원래 NUL이며 refs `0x1DC128/0x1DC15C/0x1DC6CC`는 byte-exact다. `msgsec10.dat`, font, BGM G1T와 나머지 v156 파일은 모두 byte-exact다. builder→독립 verifier→deterministic `--check`→verifier 재실행 PASS. **현재 Patch 권위는 v157-intermediate**다.
- **직전 Patch 기준본: v156-intermediate (source 수정 성공, 정렬은 v157에서 보정)** — exact v155를 `analysis\v156_issue157_baseline\PatchSnapshot`에 봉인하고 #157의 BGM 실제 runtime source를 다시 추적했다. v155 대비 변경 게임 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 2개**뿐이다. 메인 메뉴 `ＢＧＭ変更`의 실제 owner는 v102에서 한글화한 `세이브/로드/환경설정/편집기능/게임종료`와 같은 공용 table의 **`code.bin 0x1CF750`**이며 refs `0x1DC128/0x1DC15C -> VA 0x2CF750`; 이를 `BGM변경`으로 수정했다. 메인 메뉴 상단 `ＢＧＭｦ変更ｼﾏｽ。`는 **`msgsec10_0030_000 @ 0xF0C`**이며 16B fixed span 때문에 `BGM을 변경합니다`로 적용했다. BGM 설정 화면 상단 `ゲーム中に再生するＢＧＭを設定してください`의 displayed copy는 v155의 `0x86D84`가 아니라 **`code.bin 0xEC5C0`**이며 `게임 중 재생할 BGM을 설정하세요`로 수정했다. 하단 `전 략`은 `0x1CFE2C`에서 **`전략`**으로 붙였다. v154 `0x1CF8B0`과 v155 `0x86D84`는 의미가 같은 duplicate로 판정하며 기존 한국어 수정은 보존한다. `font.g1t`, `ChangeBGM.g1t`, `BGMDown.g1t`는 v155와 byte-exact이고 이미지 생성/수정은 하지 않았다. builder preflight → build → 독립 verifier → deterministic `--check` → verifier 재실행 모두 PASS. 최종 SHA는 `code=13FC157798FE80ECDBBEA1E169EBF478B4ECC165F2720291BC20861A970E71DC`, `msgsec10=B90F242A122FB75593E879EC01A0E661757A46361DFF9E4F7CA65A2150C0B2AF`. **Citra 실화면 확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v155-intermediate** — #153/#156의 BGM 26곡/갤러리 내부 direct174~182 및 `폴=8C98` 기준이며 v156의 exact baseline이다. 단 #153의 `0x86D84` 실제 상단설명 source 판정, `전 략` 표기, 메인 메뉴 BGM 버튼 이미지 추정은 v156에서 폐기/교정됐다.
- **직전 Patch 기준본: v154-intermediate** — #127/#131/#133/#142의 에디터·갤러리·BGM 1차 전수 수정 기준이며 v155의 exact baseline이다. v154의 `msgsec14` 이벤트 CG 27개, `이벤트 CG`, editor 수정, `벤/트/갤/첫` glyph는 v155에서 비대상 영역으로 그대로 계승한다.
- **직전 Patch 기준본: v153-intermediate (Citra 재확인 pending)** — exact v152를 `analysis\v153_issue146_item_effect_runtime_fix_baseline\PatchSnapshot`에 봉인하고 #146의 실제 item-effect runtime formatter와 동일 위험 class를 재수정했다. 사용자 스크린샷의 첫 글자는 CP932 `98C1=价`와 정확히 일치했고, 방천화극 상세 source는 `code.bin 0x195F5C = 무력+%d\n98C1 88CD=%d`임을 확인했다. v70 `effect_pool_list/detail`의 냉정 formatter 8개(`0x142EBC/0x142EC4/0x143238/0x14327C/0x195F54/0x195F5C/0x1962D0/0x196314`)를 모두 `냉=98C1→93FB`로 same-width 재인코딩했고, `msgsec07 0x63C`의 `냉정한 무장을 / 5명 모아라`도 같은 방식으로 수정했다. 아이템 UI 전체를 추가 감사해 item type/effect formatter의 lead>=0x98 잔여는 0건이며, 67개 item 표시명에서 남은 `청낭서/나기향낭`의 `낭=9944` 두 건도 기존 low-lead `낭=8F83/physical1840`으로 교체했다. v152의 임시 `냉=8B9C/physical1113` donor는 제거해 font를 v151과 byte-exact로 복원했다. v152에서 성공한 `무장/도시` 버튼과 v151의 `도시정보`, 편집취소/#147/특산품 수정은 그대로 계승한다. v152 대비 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Scenario/fix_data.bin` 4개다. 독립 verifier → builder `--check` → verifier 모두 PASS, `deterministic_match=true`. 최종 SHA-256은 `code=3328341B6C6534D199D517CEA2A4BC2AF71995899D565863D0CA72FFECF3382B`, `font=7CF47000EF82EB0D93CF1009C7F5005C237A890BE03BE6AD7BB89BA47649C228`, `msg07=4D8D8F34965F7BDD41204CF0A55E2EE2D8502E45B17D5A5D631FB71C0FD8E010`, `fix_data=BE1B131231A3896FA910EA39CB3054F78590F90A599C5B4AF157A9896C8514A1`. **Citra 실화면 확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 v152-intermediate — #146 원인 판정은 v153에서 폐기, #148 무장/도시는 유지:** exact v151을 `analysis\v152_issue146_148_runtime_repair_baseline\PatchSnapshot`에 봉인하고 사용자 Citra 재제보 2건을 실제 runtime source 기준으로 재수정했다. v151 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 2개다. #146의 `냉정`은 v151 source `0x1CFEB0` 자체는 올바르게 `93FB 88CD`였으나 아이템 상세 renderer에서 `냉=93FB`만 한자형으로 실패했다. v152는 기존 93FB를 보존하고 이 한 화면용으로 current v151 runtime ref=0 / current Hangul owner=0인 controlled historical alias `냉=8B9C/physical1113`을 주입해 `0x1CFEB0`만 `8B9C 88CD`로 재인코딩했다. font diff는 physical1113 한 셀뿐이며 packed Sam3 glyph가 pixel-exact다. #148은 v151의 `都市情報→도시정보` source 판정이 사용자 의도와 달랐고 실제 하단 버튼 source가 `code.bin 0x1CF904=武将`, `0x1CFC14=都市`임을 재확정했다. 이를 same-width `무장=8BD7 88C2`, `도시=88DF 88EA`로 교체하면서 `武将` 3개 / `都市` 2개의 absolute pointer와 종료 NUL을 보존했다. v151의 별도 `도시정보` 변경은 롤백하지 않고 계승한다. 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재PASS. 최종 SHA-256은 `code=478669A22E41015F5F4851705DCE2B9709D365E8742EA151BDEF9EA4BC19CA56`, `font=98B895A661DB83D8F9E1D68815C71E3A54C2AE89E33A33576212C266BADC0C4F`. **Citra 실화면 확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v151-intermediate (Citra 재확인 pending)** — exact v150을 `analysis\v151_issue146_148_ui_text_repair_baseline\PatchSnapshot`에 봉인하고, 사용자 후속 편집취소 문구와 #146/#147/#148을 최소 수정했다. v150 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec09.dat`, `RomFS/Message/msgsec18.dat` 3개뿐이며 `font.g1t` 및 나머지 Patch 파일은 byte-exact다. 편집 취소는 `msgsec09 direct76`을 **`편집을 취소 / 하시겠습니까？`**로 수정하면서 기존 newline 1개, pointer, 파일 크기, `05 05 05` separator를 보존했다. #146 특산품 완료 안내 `msgsec18 direct111`은 39B fixed span에서 제안 1안 47B/2안 43B가 모두 overflow하므로, 첫 줄을 유지한 compact 1안 **`특산품 개방 완료 / <NAME1>의 소지품이 됨`** 38B를 적용했다. NAME1, ESC K/H, newline, direct header, 파일 크기, separator는 모두 보존했다. #146 아이템 효과는 Citra에서 이미 정상인 sibling까지 raw CJK라는 이유로 일괄 재인코딩하지 않고, 실제 깨진 `冷静`만 `냉정=93FB 88CD`로 same-width 교체했다. `냉=93FB/physical2712`는 기존 v146 safe packed glyph가 Sam3와 pixel-exact이고 font 자체는 v150과 byte-exact다. #147 health-state 공용 table은 사용자 확정대로 `病気→질병`, `負傷→부상`, `健康→건강`; #148은 standalone `都市情報→도시정보`를 각 원 code slot과 absolute pointer를 유지해 same-width 치환했다. 독립 verifier → builder `--check` → 독립 verifier가 모두 PASS했고 `deterministic_match=true`다. 최종 SHA-256은 `code=5F29F7C2E0A51C5C48FFA0B85FE064448A6578956A7B03F4BBA253BC1C3C08E0`, `msg09=8392238A8D595F617AD11C53A728E76BB77726C912EEAE30DA61FA0A682EC7BE`, `msg18=31E47E0F40311E248A8839AB4C7CAD4A24D25A03F32BF5594F846CB509EE78F7`. 권위 자료는 `analysis\v151_issue146_148_ui_text_repair_targets.json`, `analysis\v151_issue146_148_ui_text_repair_report.json`, sealed baseline, `tools\build_sangokushi2_v151_issue146_148_ui_text_repair.py`, `tools\verify_sangokushi2_v151_issue146_148_ui_text_repair.py`다. **Citra 실화면 확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Patch 기준본: v150-intermediate (Citra 재확인 pending)** — 사용자 승인 상태의 exact v149를 `analysis\v150_issue136_138_139_143_dialogue_sweep_baseline\PatchSnapshot`에 봉인하고 #136/#138/#139/#143 및 동일 오류 class를 수정했다. v149 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec08.dat`, `msgsec09.dat`, `msgsec18.dat` 5개다. #136은 포로 처리 버튼을 `석방/처단/등용`으로 통일하고 같은 고정 라벨의 `赴く→방문`, `在野→재야`를 추가 수정했다. #138은 특산품 popup의 `msgsec09 direct85`와 `msgsec18 direct110~112`, #139는 `msgsec09 direct76/87/88`을 한글화했다. 같은 잔존 class 감사에서 `msgsec09 direct3~31`의 협력 보상·매복/내응·아이템·포로·후계자/사망 계열을 함께 수정했다. `msgsec09` shared numeric pointer는 변경하지 않고, 다른 parent-route가 문자열 중간에서 시작하는 8개 direct span은 정확한 interior byte 경계에서도 의미 있는 한국어 suffix가 시작되도록 구성했다. #143은 하진 줄바꿈을 보정하고, proven-live `빙=98B8` 두 곳만 `빙=91BF/physical2276`으로 재인코딩했으며 font physical2856/2883을 각각 `팔/질`로 복원했다. ARM 직접참조 짧은 문자열 `武将の%s%s`, `民の%s%s`, `孫策殿が会見`과 레이아웃 한 건도 함께 정리했다. 독립 verifier PASS, builder `--check` `deterministic_match=true`, reviewed msgsec09/msgsec18/code 일본어 잔여 0, 비대상 v149 파일 byte-exact다. 최종 SHA는 `code=432C3D131E0C082130BB23E9FB58451D0D422F5BA1CDD7D049546E24C3FEACA8`, `font=7CF47000EF82EB0D93CF1009C7F5005C237A890BE03BE6AD7BB89BA47649C228`, `msg08=0A7CF1E51E4FE5987F2C6B959935192E35490759EED2BEC181402ED26E33F8D7`, `msg09=57716A241F2D7990C6790B9625A4C334746A8F2ABF3ACA588D105EB04B33A55F`, `msg18=E70AAB427142025F38CC7AB265FC8B17A6AF9DE3A0FC961E7D62E74779C5BF9E`. **Citra 실화면 확인 전까지 runtime 승인으로 표현하지 않는다.**
- **직전 Citra 승인본: v149-intermediate (#144/#145)** — exact v148을 봉인하고 #144/#145를 최소 수정했다. #144의 실제 원인은 `msgsec19` 포인터가 아니라 `code.bin VA 0x10A994`의 Message 파일 read 상한 `0xD000(53,248B)`이었다. 현재 `msgsec07=60,082B`, `msgsec19=62,465B`라 상한을 넘고, 방회 record730/message19395의 정상 direct pointer가 `0xD09E`부터라 런타임에서 미로드 영역을 읽어 깨졌다. v149는 해당 ARM 한 명령만 `mov r2,#0x10000`으로 올렸으며 22개 Message 파일 모두 새 상한 미만임을 독립 검증했다. `msgsec19`은 v148과 byte-exact다. 별도 누락 클래스인 `msgsec20` 마지막 10명은 NDS 열전 770~779와 3DS record1010~1019가 1:1임을 확인해 화타·사마휘·허자장·우길·좌자·관로·자허상인·이의·길평·헌제를 EOF append + direct index170~179 redirect로 한글화했다. #145는 `code.bin file+0x1CFEFC`의 4B `特別`을 기존 안전 글리프 `특별=8F61 8F67`로 same-width 교체하고 NUL/pointer를 보존했다. v148 대비 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec20.dat` 두 개뿐이다. 최종 SHA-256은 `code=E27EF9E4145EFF4E7EA2855AAFD03D1D442061E038E807C578A945A1810EA537`, `msgsec20=712C0B526F1B7836C8D9D24678A40362B1D05E371C78719E88AFD810DED99920`. builder → 독립 verifier → `--check` → verifier가 모두 PASS했고, 이후 사용자가 Citra 실화면에서 수정이 잘 되었다고 확인했다. runtime 확인 증거는 `analysis\v149_issue144_145_citra_runtime_confirmation.json`이다.
- **직전 Patch 기준본: v148-intermediate** — v146을 기반으로 #141을 v147에서 먼저 최소 수정한 뒤 #137을 v148에서 전수 적용했다. #141은 왕윤 열전 index20의 `동탁 사후,이각을 등`을 **`동탁 사후,이각 등`**으로 고치고 줄바꿈을 재배치해 `이각 등`을 한 줄에 표시한다. v147 변경 파일은 `msgsec13.dat` 하나뿐이며 19.5×4 gate와 msgsec13 pointer/sentinel 구조를 유지한다. #137의 실제 runtime bank는 `msgsec14.dat`; `sangokushi2_translation.json`에는 msgsec14 번역 가능 168행의 `translation_ko`가 모두 채워져 있고 `text_review.html`에도 168/168 id가 존재했지만, v147 Patch의 msgsec14는 Original과 byte-exact라 **번역 데이터가 런타임에 주입되지 않은 상태**였다. v148은 direct index **54~120 = 아이템 67종**을 완전한 설명 단위로 한글화하고 기존 121 direct pointer, 4282B 파일 크기, 각 `05 05 05` separator 위치를 그대로 보존했다. 모든 설명은 최대 3줄/19.5 cells 이하/기존 direct span capacity 이하이며 미번역 runtime item=0. 신규 글리프는 `쥐=97D8/physical3429(stock 鱗)` 한 자뿐이고 v147 대비 font diff도 이 한 셀뿐이다. v147 대비 v148 변경 파일은 `font.g1t`, `msgsec14.dat` 두 개다. 최종 SHA는 `font=02A56951...66440`, `msg14=08093B5C...6E801`; builder `--check` + 독립 verifier 2회 PASS. Patch 파일 수는 83개이며 #141/#137 Citra 실화면 확인 전까지 intermediate다.
- **직전 #140 기준본: v145-intermediate** — #140 무장 열전을 실제 runtime 기준으로 전수 적용했다. 열전은 `msgsec13=336명 + msgsec19=462명 = 798명`이며 NDS 이름표와 1:1 정렬되는 공통 무장 770명 외에 3DS 추가 무장 **28명(record 770~797)**도 모두 포함한다. 기존 번역 JSON의 Shift-JIS fragment 잘림을 그대로 재패킹하지 않고 무장별 완문으로 재구성했으며 모든 열전은 **19.5 cells×최대4줄** gate를 통과했다. `msgsec13`은 336 absolute BYTE pointer + 마지막 `05 05 05` sentinel을 보존하고, `msgsec19`은 기술 block0과 뒤 126B tail을 byte-exact 보존하면서 41개 tail pointer의 상대 offset을 유지했다. 신규 glyph는 `해=8B6C/1066`, `던=8F5B/1801`, `맥=909C/2053`, `머=93AD/2634`, `코=9772/3328` 5개 low-lead donor이며 target bank 밖 runtime ref=0/history=0/current owner=0/vertical bleed gate를 통과했다. packed font에 이미 정확히 있던 `론=97E9`, `륜=97F5`, `흉=97FA`는 셀 수정 없이 재사용했다. v144 대비 변경 게임 파일은 **`font.g1t`, `msgsec13.dat`, `msgsec19.dat` 3개뿐**이고 builder `--check` + 독립 verifier PASS. 최종 SHA는 `font=E80BCB95...4C275`, `msg13=156ECFC4...AAFE0`, `msg19=4F5F5896...E18EAC`. **#137 아이템 설명은 미수정**이며 Citra 실화면 확인 pending이다.
- **직전 Patch 기준본: v144-intermediate** — 사용자가 v143 Citra 실화면에서 재제보한 #135 세 건의 **실제 runtime 구조**를 다시 수정했다. `사자 무장`은 실제 owner가 `code.bin 0x181254`의 Original `使者武将です`임을 확인해 **`사자 무장입니다` 15B + NUL**로 16B allocation에 정확히 맞췄고 msgsec06 duplicate도 동기화했다. `않겠다` 뒤 가나는 `0x1808EC`가 `r2=0x1E`로 `0x180990`을 **정확히 30B memcpy**하는데 v143 visible text가 30B라 NUL이 복사 밖으로 밀린 것이 원인이었다. 최신 문구는 **`이제 수모를 / 참고 살지 않겠다.`** 29B + NUL=30B이며 Original과 같은 NUL 위치 `0x1809AD`를 복원한다. `조조는조비 님이...` 문제는 `0x17DE0C`가 조사 한 글자만 LDRH로 읽는 구조라 v140의 trailing-space data 수정이 무효였고, v142의 suffix-leading 전각 공백은 `<NAME2>　님`을 만든 잘못된 위치였다. v144는 `0x17DE10=mov r1,#0x20`, `0x17DE24=strh r1,[sp,#2]`로 SP C-string을 직접 `는 + ASCII space + NUL`로 만들고 suffix 전각 공백을 제거해 목표 조립을 **`<NAME1>는 <NAME2>님이 두려워 / 항복했습니다`**로 고정한다. 변경 게임 파일은 `code.bin`, `msgsec06.dat` 두 개뿐이고 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. 최종 SHA는 `code=A95C76CD...6BF22`, `msg06=000C675B...8C63C`; Citra v144 실화면 확인 pending이다.
- **직전 Patch 기준본: v143-intermediate** — exact v142를 봉인하고 #134의 항복 대사 한자형 렌더 실패를 수정했다. 스크린샷의 `귀공의 본성 ... 알았습니다`는 `code.bin 0x1D0FD0` 및 `msgsec06 header[115] -> 0x107F`의 **`귀공의 본성 / 똑똑히 알았습니다`** 두 runtime copy이며, immutable Original은 `貴殿の本性 / しかと承知しました`다. 원인은 `똑=9953` far-alias 실패로 확정했고 두 29B span을 정확히 같은 길이 **`귀공의 본성 / 확실히 알았습니다`**로 교체했다. 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec06.dat` 2개뿐이며 literal pointer `0x1E517C -> 0x002D0FD0`, msgsec06 161-word header/header[115]/`05 05 05` separator/file size는 byte-exact다. `9953` live text 4회→0회. 최신 dialogue audit에는 Citra-proven `9951/9952/9972/98B3/9953`을 failed alias로 추가하고 안전 local `풍=9559`, `곁=96A0`을 decoder에 반영했다. 최종 `code_failed_alias=0 / active_message_failed_alias=0 / active_message_japanese_or_cjk=0`; direct pointer가 없는 legacy control/meta `msgsec06 0x142` mixed false-positive 1건만 v142 byte-exact로 남는다. 독립 verifier PASS → builder `--check` `deterministic_match=true` → verifier 재실행 PASS. 최종 SHA는 `code=BF9A9143...5F99C`, `msg06=B47C3E67...B2096`; Citra 실화면 재확인 pending이다.
- **직전 Patch 기준본: v142-intermediate** — exact v141을 봉인하고 #135 네 화면과 동일 오류 class를 수정했다. 변경 게임 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec06.dat`, `msgsec08.dat` 3개뿐이다. `이제 수모를 / 참으며 살지 않겠다`는 code/msg06 runtime copy를 동기화하고 줄 끝 blank guard를 추가했다. 사자 선택 팝업은 실제 16B fixed span에 맞춰 **`사자 무장입니다.`**를 적용했다. 항복 거절 Original `奴らなど討ち取って / ご覧にいれます`는 **`저들을 토벌해 / 보여드리겠습니다`**로 수정했고, 원인이던 failed far alias `애=98B3`을 active `code=2/msg06=1/msg08=5` 총 8회에서 **0회**로 전수 정리했다. `조조는마등님이...`는 실제 ARM ADR owner를 유지하면서 stock 전각 공백 `81 40`으로 동적 이름 간 간격을 확실히 만들고 msg06 동일 copy도 맞췄다. 독립 verifier PASS → builder `--check` 메모리 재계산이 현재 Patch와 byte-for-byte 일치(`deterministic_match=true`) → verifier 재실행 PASS. 최종 SHA는 `code=F99BD7F2...B8660`, `msg06=419DD006...E62C`, `msg08=56B70B0B...358E`; #135 Citra 실화면 재확인 pending이다.
- **직전 Patch 기준본: v141-intermediate** — exact v140을 봉인하고 위 34개 direct-owned system/menu 질문·확인 원문을 전부 **원 span 안에서만 in-place 한국어화**했다. 변경 파일은 정확히 `RomFS/Message/msgsec00.dat` 1건, `msgsec01.dat` 9건, `msgsec02.dat` 20건, `msgsec05.dat` 3건, `msgsec17.dat` 1건이다. pointer/header/file size를 전혀 움직이지 않고 남는 바이트는 space padding했다. trailing `05` separator는 원 물리 offset에 유지하고, interior pointer의 leading `05 05` 및 `msgsec05_0003_001`의 `02 01 C8` NAME1 토큰을 그대로 보존했다. v140 #132 span과 v140 비대상 파일은 byte-exact다. 최종 section<20 system/menu 번역 DB raw × current direct-pointer 교차감사에서 **활성 미번역 팝업 후보=0건**. builder→독립 verifier→builder→verifier 2회 동일 SHA/PASS, Citra 실화면 확인 pending이다.
- **직전 기준본: v124-intermediate** — #101/#110 `곁`, #102 자연재해/event, #109 고립 Shift-JIS lead 전수, #111 `(이)라는` 조립 수정의 기준이며 v125의 sealed baseline이다. v124의 변경 5파일과 alias/font/Message 구조는 v125에서 `code.bin`의 v125 reviewed spans 외 모두 byte-exact 계승된다.
- **직전 기준본: v123-intermediate** — 허자장 #095/#097/#100/#103 조립 대사의 직전 권위이며 v124의 sealed baseline이다. `0x159B04` 좌자 별도 증여 대사, 허자장 23-word table/local helper/shared `の/は` 보존 규칙은 v124에 그대로 계승된다.
- **직전 기준본: v122-intermediate** — #075 후속, #096, #098의 기준이다. #075 최신 문구는 `사실무장 편집\n\n상성을 편집해도\n기존 장수의 상성 수치는\n초기 상태를 유지합니다`; #096은 세력/동맹 편집 fixed class 6개와 `02 2D` prefix/header/separator 보존; #098은 `하진님, 이 조건을 받습니까？` 조립이다. v123은 이 파일들 중 `code.bin`의 허자장 관련 reviewed span만 추가 수정하며 `msgsec01.dat`, `msgsec09.dat`, font 및 나머지 파일은 v122 byte-exact다.
- **직전 기준본: v121-intermediate** — #069 `신규/미사용`과 #075 사실무장편집의 fixed source 6개를 한글화한 기준이다. #069의 ADR source와 #075의 제목/반영/초기화/선택 prompt, C9/C1 및 K/H control, msgsec01 9057B/separator 규칙은 후속 revision에 그대로 계승된다. 단 #075의 `0x458..0x4A6` 설명 문구만 사용자 Citra 재검수 의견에 따라 v122가 대체한다.
- **과거 기준본: v120-intermediate** — #092/#093/#094와 ARM ADR 직참조 일본어 C-string 감사의 역사적 기준이다. #094의 active Message 68개 row + translated formatter 31개 조사 보정과 ADR dialogue/event 16개 한글화/비대사 9개 allowlist는 유지한다. 단 **`0x159B04`를 #095 허자장 스크린샷 source로 연결한 판정은 v123에서 폐기**됐다. `0x159B04`는 좌자 본인의 별도 증여 대사이고, 실제 #095는 허자장 23-pointer fragment table 조립 경로다.
- **직전 기준본: v119-intermediate** — exact v118 전체 Patch를 `analysis\v119_season_localization_baseline\PatchSnapshot`에 봉인하고 상단 계절 문자열과 시즌 전환 로고만 한글화했다. `code.bin`의 독립 슬롯은 `봄=92F5/physical2518@0x1CED0C`, `여름=9474/2766@0x1CEE20`, `가을=95A1/2998@0x1CED00`, `겨울=96CC/3229@0x1CED18`의 fresh local-only alias를 사용하며 각 alias의 최종 runtime 참조는 해당 슬롯 한 곳뿐이다. font 변경 cell은 `[2518,2766,2998,3229]`; `봄`은 Sam3, 나머지 3개는 사용자 14×14 PNG의 A4 도트다. 시즌 애니메이션은 `RomFS/Stg/stg_season_effect_{spring,summer,autumn,winter}.arc`의 대응 `timg/season_*.bflim` 68×68 로고 한 개만 교체했으며 모든 꽃 texture, BFLYT 배치, BFLAN 애니메이션 및 비대상 member는 byte-exact다. v118 대비 변경/추가 파일은 `code.bin`, `font.g1t`, 시즌 ARC 4개로 정확히 6개다. 결정적 재빌드·독립 verifier·dialogue audit 0건 PASS, **Citra 실화면 확인 pending**이다.
- **직전 기준본: v118-intermediate** — #087 `몽/붙` alias 충돌과 #091 실제 input-prompt/C4 잔존을 보정한 기준이다. v119는 v118의 `font.g1t`, Message, Scenario 및 기존 이미지 자산을 exact baseline으로 계승하며, v118의 `몽=94CE/physical2855`, `붙=969B/physical3180`과 16개 입력 prompt를 변경하지 않는다.
- **직전 중간본: v117-intermediate** — exact v116 전체 Patch를 `analysis\v117_issue066_color_issue091_baseline\PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec01.dat`, `msgsec09.dat`, `msgsec18.dat` **3개만** 수정했다. 사용자 Citra에서 v116 #066 한국어 문구는 정상이나 Original의 초록색과 달리 흰색으로 출력된 것을 확인해, 세 히든 시나리오 잠금 안내 시작에 Original과 동일한 **`ESC C4 = 1B4334`** 색상 제어를 복원했다. 두 번째 82B 슬롯만 C4 포함 시 1B 초과해 의미 변화 없이 첫 `플레이 할`을 `플레이할`로 붙였다. #091은 로컬 HTML과 8장 PNG를 확인해 편집기능 source가 `msgsec18` 상단 도움말과 `msgsec09` 하위/runtime 문자열로 분리됨을 확정했다. 공통 상단은 `도시, 세력, 특산아이템을 편집합니다.`, 도시 선택 상단은 `편집할 도시를 선택`; `msgsec09 0x874`의 **동적 토큰 `02 2D C4`를 그대로 보존**하고 suffix를 `의 타세력 적대 편집`으로 수정했다. 도시 하위 도움말 17종을 한글화하고, `개방할 도시를 선택해주세요`는 26B>22B라 사용자가 허용한 **`개방할 도시를 선택`**을 적용했으며 특산 확인문은 `이 도시의 특산품을 / 개방하시겠습니까？`다. 세 Message header와 모든 `05 05 05` separator는 byte-exact, v116 `code.bin/font.g1t/msgsec11` 및 비대상 파일도 byte-exact다. SHA-256: `msgsec01=FD33C4F8F003E8D3D94D2A01535C3FEFEE1B23C30524CF2650206C8EFB890D3C`, `msgsec09=8CCB38E5063D15B59A6CE327A50A4514D2605FDFCCFFEDF232A22064227E7E43`, `msgsec18=F7DBF909D3B4295F2447BBD47813B73C1CC44F2D108B8A376282904E30C58538`. 결정적 재빌드와 독립 verifier 재실행 모두 PASS, **Citra 실화면 확인은 pending**이다.
- **직전 중간본: v116-intermediate** — v115 사용자 Citra 승인 후 #066/#068을 1차 수정했다. `챔`이 없어 의미를 바꾸지 않고 `챔=95AC/physical3009`(stock `噴`) donor를 추가했으며 font 변경 셀은 3009 하나뿐이다. #068 `code.bin/msgsec09` 편집기능 기본 제목·설명·선택 문구도 번역했다. 다만 후속 Citra에서 #066 C4 색상 누락과 #091 추가 일본어 잔존이 확인되어 화면 최종 기준은 v117이 대체한다.
- **직전 Citra 승인본: v115-intermediate** — 사용자 실화면에서 #082 `유선님, 영안에 / 조광이(가) 합류했습니다`, `조광의 신분을 선택하십시오`, 외교 부담/동맹 요구 수정까지 모두 정상임을 확인했다. 따라서 v115의 `code.bin` 변경은 v116/v117에 승인 상태로 계승한다. #082 실제 source는 `code.bin 0x14EBF4` formatter + `0x1CEEFC` suffix이며, v114의 `msgsec08 0x955` source 판정은 폐기된 상태다.
- **직전 전체 기준본: v114-intermediate** — #084-1 `주군, 감언에 속지마십시오`, #084-2 `떻=8A98/physical921`, #084-3 `교섭을 포기합니까？`는 후속 revision에 byte-exact 계승됐다. 단 #082의 `msgsec08 0x955` source 판정은 v115에서 폐기됐다.
- **직전 v113-intermediate** — #086 나이 합자 physical1190 단독 재주입본이며 v114의 exact baseline이다.
- **직전 폰트 기준본: v112-intermediate** — 사용자 제작 이름/나이 두 타일을 physical 948/1190에 반영한 revision이며, v113은 이름 타일을 byte/pixel-exact로 계승하고 나이만 갱신했다.
- **직전 runtime 확인본: v111-intermediate** — #086 장수 정보창의 9개 label을 runtime text로 번역했고, 사용자 Citra에서 9개 한국어 출력과 두 합자의 위치 한정 출력이 확인됐다. 단 자동 6×11 합자 도안은 지나치게 찌그러져 v112에서 사용자 제작 14×14 도안으로 교체했다. 증거=`analysis\v111_issue086_citra_runtime_confirmation.json`.
- **과거 텍스트 중간본: v110-intermediate** — #085/#088과 105개 row/120개 NAME token 복구의 역사적 기준이다. **#082의 EOF/header redirect와 #084의 msgsec06 source 판정은 사용자 Citra에서 실패가 확인되어 v114에서 폐기했다. #082는 v114의 `msgsec08 0x955` physical-body 판정도 다시 Citra에서 실패했고, v115에서 `code.bin 0x14EBF4 + 0x1CEEFC` 조립 경로로 최종 재분석했다.** exact v109 전체 Patch를 봉인한 뒤 #082/#084/#085/#088과 같은 계열의 동적 이름 토큰 재발을 함께 수정했다. 변경 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec04/06/07/08/09/21.dat` 7개다. v109 active direct-message를 immutable Original과 header별 source-aware 대조해 **105개 active row / 120개 atomic name token(NAME1 105, NAME2 12, NAME3 3)** 누락을 전부 복구했다. #082는 `msgsec08 header[49]`가 `%s에 <NAME1>이(가) 휘하에 들어왔습니다`이며 기대 화면 예시는 `유선님, 영안에 조광이(가) 휘하에 들어왔습니다`; 도시 `%s`와 장수 `02 01 C8`은 runtime token, `이(가)`는 literal 이중조사다. #084는 Original이 `殿、甘言ニ...`로 시작해 이름 token이 row 안에 없으므로 런타임 군주명 prefix는 그대로 두고 `님, 감언에 / 속지 마십시오`만 넣는다. #085는 `조건으로 동맹을`, `선비족의 가비능`, `군을 치게/촉에서 투항한/양평관으로 진군하게/방면에 대응책을`을 수정하고 msgsec21 dual-header 37+30 pointer를 모두 재계산했다. #088은 msgsec09 header[1]/[2]를 기존 한국어 승리/패배 row `0xA4E/0xA61`로 redirect했으며 body는 byte-exact다. `font.g1t`는 v109와 byte-exact이고 신규 donor 없음. 결정적 빌드 2회, 독립 verifier, v103 dialogue audit 0건 PASS. v111에 byte-exact 계승됐다.
- **직전 이미지 기준본: v109-intermediate** — exact v108 전체 Patch 70파일을 봉인하고 Common UI PNG 106개를 네 G1T에 1:1 반영한 이미지 전용 revision이다. 변경 파일은 `RomFS/Common/Select/Select.g1t`, `GuidBtn/GuidBtn.g1t`, `Dialog/Dialog.g1t`, `Common/Common.g1t`뿐이다. 106/106 pixel-exact, 결정적 재빌드 2회와 독립 verifier PASS이며 Citra 확인 대기 상태로 v110에 byte-exact 계승됐다.
- **직전 이미지 중간본: v104-intermediate** — exact v103 전체 Patch 65개 파일을 기준으로 지정 PNG 20개를 `InfoB/InfoI/InfoK/InfoT/InpCalc.g1t` 5개에 반영한 이미지 전용 revision이다. 이 20종과 5개 G1T는 v105에서 byte-exact 계승되며, v105 대사 수정 때문에 다시 생성하거나 Original에서 덮어쓰지 않는다.
- **직전 대사 중간본: v103-intermediate** — exact v102 전체 Patch를 `analysis/v103_issue065_067_070_baseline/PatchSnapshot`에 봉인한 뒤 #065/#067/#070을 계기로 대사 runtime source를 전수 재감사했다. `code.bin` 대사/팝업 120개, active Message 20개, legacy stale body 41개를 정리했고 신규 glyph는 `쪽=97DD/physical3434` 1자다. 변경 파일은 `code.bin`, `font.g1t`, `msgsec04/06/07/08.dat` 6개뿐이며 독립 verifier 및 완성본 위 결정적 재빌드 2회 PASS, 대사 잔존 일본어/혼합 한자/failed alias 감사 0건이다. **#065/#067/#070과 선행 #053/#064 모두 Citra 확인 대기**다.
- **직전 Citra 승인본: v101-intermediate** — 사용자가 v101 적용 후 `잘 수정되었다`고 확인했다. 따라서 #054 하단 전투 편성 UI, #061 safe-alias/fragment-boundary 복구, #063 타국 전쟁 결과 문구는 승인 상태로 동결한다. `msgsec06 header[121]`의 `$`+중복 cross-fragment 구조는 v101에서도 의도적으로 보류한 상태다.
- **직전 이미지 중간본: v100-intermediate** — exact v99 전체 Patch 57개 파일을 `analysis\v100_image_update_baseline\PatchSnapshot`에 봉인하고, 사용자가 지정한 PNG 9개만 대응 G1T에 1:1 반영했다. 기존 `RomFS/StartMenu/start_menu_up.g1t`는 봉인 v99를 기반으로 texture[15](`0x09 RGBA8`, 512x128)만 교체했다. v99 Patch에 없던 `RomFS/Common/Info` 8개 G1T(`BG_InfoB_NEW`, `BG_InfoB`, `BG_InfoBHex`, `BG_InfoBHex_NEW`, `BG_InfoI`, `BG_InfoT`, `BG_InfoT_HOUROU`, `BG_InfoT_KOUSEN`)는 immutable Original을 기반으로 texture[0](`0x45 RGB8`, 512x256)만 교체해 최초 편입했다. 요청 외 `BG_InfoB_C.g1t`와 `BG_InfoK.g1t`는 추가하지 않았다. 이미지 생성 없이 source PNG를 그대로 사용했으며, 9/9 codec identity·header/비대상 payload byte-exact·pixel-exact 역읽기, v99 비대상 57파일 byte-exact, 완성본 위 2회 결정적 빌드와 독립 verifier가 모두 PASS했다. Patch 파일 수는 57→65이며 Citra 실화면 확인 대기다.
- **직전 Patch 중간본: v98-intermediate** — exact v97 전체 Patch를 `analysis\v98_issue054_055_056_baseline\PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec03.dat`, `RomFS/Message/msgsec07.dat` 3개만 수정했다. #054 상단과 #056 prefix 분석은 v99에 계승됐고, #054 하단 잔존 한자/#056 끝의 `ました`/#062의 `총` 표시 문제는 v99에서 추가 보정됐다.
- **v86 #047 Citra 승인 완료:** 동일 대사 뒤 강제종료 없이 정상 진행됨을 사용자가 확인했다. 직전 v86 분석 기록: — v85에서도 GitHub #047 Crash가 그대로 재현되어 ARM 제어 흐름을 다시 추적한 revision이다. 원인은 `0x56D64` 문구/far alias가 아니라 v83 #035 inventory의 **문자열 시작 offset 1바이트 오판**이었다. 문제 사례 `0x56C5B`는 실제 문자열 시작이 아니라 `0x56C58`의 ARM `B` 명령 마지막 바이트이며, 실제 ADR은 `0x56C5C`를 가리킨다. v83~v85가 이 위치부터 한국어를 써 Original branch word `EA000436`을 `89000436`으로 파괴했고, `0x56D28` 대사 출력 뒤 해당 분기를 실행하면서 강제종료가 발생했다. 169개 전체 감사에서 같은 패턴이 **10개** 발견되어 모두 branch word를 Original로 복구하고 번역문을 실제 ADR 시작(`bad+1`)으로 1바이트 이동했다. #047의 `0x56D28`/`0x56D64` 문구, v85 font/msgsec11/#045 수정, 나머지 Patch 파일은 byte-exact다. 변경 파일은 `ExeFS/code.bin` 1개이며 두 번 연속 결정적 빌드와 독립 readback PASS 상태다. Citra 실화면 승인 전까지 중간본이다.
- **v73 Citra 재검수 결과:** 담당군사 `이(가)`, #023 게임오버 `조예`, #024의 `청강검 (으)로` 조사 표시는 정상화됐다. 다만 챌린지7 제목 `강유의 북벌`의 `북`이 한자형 glyph로 보였고 #024 마지막 ASCII `?`가 어색하게 표시됐다. 따라서 v72/v73의 성공 수정은 유지하면서 이 두 표시 문제만 v74에서 최소 보정한다.
- **v55 Citra 부분 확인 완료:** 시혜의 `누가 합니까?`와 상인 거래의 `누가 거래합니까?`가 정상 출력됐다. 따라서 해당 `code.bin` 공통 UI 경로는 성공 기준점으로 유지한다.
- **안정 fallback: v16** — 사용자가 기존 화면과 시나리오1 신규 대사 정상 출력을 직접 확인했다.
- v17~v19의 시나리오1/code.bin/전쟁 메뉴 성공 변경은 v20에 유지된다.
- 메뉴 / 대사 / 이름은 별도 작업으로 분리한다. `fix_data.bin`은 v50에서 전 인물 1,020명 표시 이름이 한글화됐고, v51에서 `탁/종/응` 관련 19레코드의 alias만 재인코딩했다.

## 2. 빌드 명령

```powershell
py -3 tools\build_sangokushi2_v179_image_update.py --check
py -3 tools\verify_sangokushi2_v179_image_update.py
```

v179 최신 정적 권위 자료는 `analysis\v179_image_update_targets.json`, `analysis\v179_image_update_report.json`, sealed baseline `analysis\v179_image_update_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v179_image_update.py`, `tools\verify_sangokushi2_v179_image_update.py`다. v178 대비 두 G1T의 texture 0만 바뀌었고 `ktlogo_001`과 모든 비대상 파일은 보존됐다. **Citra에서는 두 미세 조정 이미지의 실제 표시를 확인하면 된다.**

이전 v178 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v178_image_update.py --check
py -3 tools\verify_sangokushi2_v178_image_update.py
```

v178 최신 정적 권위 자료는 `analysis\v178_image_update_targets.json`, `analysis\v178_image_update_report.json`, sealed baseline `analysis\v178_image_update_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v178_image_update.py`, `tools\verify_sangokushi2_v178_image_update.py`다. v177 대비 두 G1T의 texture 0만 바뀌었고 `ktlogo_001`과 모든 비대상 파일은 보존됐다. **Citra에서는 Opening `ktlogo_000`과 StartMenu `title_up_000` 표시를 확인하면 된다.**

이전 v177 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v177_image_update.py --check
py -3 tools\verify_sangokushi2_v177_image_update.py
```

v177 최신 정적 권위 자료는 `analysis\v177_image_update_targets.json`, `analysis\v177_image_update_report.json`, sealed baseline `analysis\v177_image_update_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v177_image_update.py`, `tools\verify_sangokushi2_v177_image_update.py`다. v176 대비 변경 게임 파일은 `RomFS/Opening/ktlogo.g1t`, `RomFS/StartMenu/title_up.g1t` 두 개뿐이다. 지정 PNG 3개 readback은 pixel-exact이며 header/비대상 payload, v176 code/font/Message/Scenario, Original/Rebuild/Backup은 보존됐다. 이미지 생성은 사용하지 않았다. **Citra에서는 Opening ktlogo 2장과 StartMenu title_up 1장의 실제 표시를 확인하면 된다.**

이전 v163 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v163_challenge_goal_menu.py --check
py -3 tools\verify_sangokushi2_v163_challenge_goal_menu.py
```

v163 최신 정적 권위 자료는 `analysis\v163_challenge_goal_menu_preflight.json`, `analysis\v163_challenge_goal_menu_targets.json`, `analysis\v163_challenge_goal_menu_report.json`, sealed baseline `analysis\v163_challenge_goal_menu_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v163_challenge_goal_menu.py`, `tools\verify_sangokushi2_v163_challenge_goal_menu.py`다. v162 대비 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 두 개뿐이다. `목표확인@0x1CF7A4`는 refs `0x1DC168/0x1DC184`를 유지한 same-width 8B 치환이며, 상단 도움말은 `0xECD` 9B + `0xED6` 14B fixed fragment를 각각 `시나리오 ` / `목표 확인하기`로 in-place 수정해 `0xED6` 시작점과 `0xEE4` separator를 보존한다. 비대상 81파일과 v162 `msgsec01` 전각 물음표, font/PNG/G1T는 byte-exact다. **Citra에서는 챌린지 시나리오 메인 메뉴의 버튼 `목표확인`과 상단 `시나리오 목표 확인하기`를 확인하면 된다.**

이전 v162 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v162_issue158_questionmark.py --check
py -3 tools\verify_sangokushi2_v162_issue158_questionmark.py
```

v162 정적 권위 자료는 `analysis\v162_issue158_questionmark_preflight.json`, `analysis\v162_issue158_questionmark_targets.json`, `analysis\v162_issue158_questionmark_report.json`, sealed baseline `analysis\v162_issue158_questionmark_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v162_issue158_questionmark.py`, `tools\verify_sangokushi2_v162_issue158_questionmark.py`다. v161 대비 변경 게임 파일은 `RomFS/Message/msgsec01.dat` 하나뿐이다. editor direct `27,28,29,69,70,71,72,73,75,96,97`의 ASCII `?` 12개를 각각 직후 padding과 함께 `3F20→8148`로 same-width 교체하여 pointer/file-size/separator/internal boundary를 모두 보존한다. 비대상 82파일, font, PNG/G1T는 v161과 byte-exact다. 독립 verifier PASS → deterministic `--check` PASS → verifier 재PASS 완료. **Citra에서 `무장 편집을 중지할까요？ / 작성을 중지할까요？ / 이름 설정 중지할까요？`의 표시 모양을 재확인하면 된다.**

이전 v161 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v161_issue155_158.py --check
py -3 tools\verify_sangokushi2_v161_issue155_158.py
```

v161의 #155/direct-list와 `名前→이름` source 수정은 v162에 byte-exact 계승된다. 단 **ASCII `?`를 정상으로 보고 보존한 v161 punctuation 정책은 사용자 Citra 실화면에서 실패가 확인되어 폐기**되었고 `analysis\v161_issue158_ascii_question_runtime_failure.json`에 기록했다.

이전 v160 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v160_issue150_151_154.py --check
py -3 tools\verify_sangokushi2_v160_issue150_151_154.py
```

v160 정적 권위 자료는 `analysis\v160_issue150_151_154_targets.json`, `analysis\v160_issue150_151_154_report.json`, sealed baseline `analysis\v160_issue150_151_154_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v160_issue150_151_154.py`, `tools\verify_sangokushi2_v160_issue150_151_154.py`다. 변경 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 두 개뿐이며 비대상 81파일은 v159와 byte-exact다. #150 formatter는 `0x1696A8..0x1696BF` 24B allocation 안에서 끝나야 하고 `0x1696C0`부터의 ARM literal pool은 절대 침범하지 않는다. #151은 신분 7종 중 `在野`만 `재야`, #154는 msgsec01 direct 25개와 code label 14개를 실제 split-source별로 수정한다. direct70 ASCII `?`, v159 #152 shared-literal fix, pointer/control/style은 보존된다. 독립 verifier PASS → deterministic `--check` PASS → verifier 재PASS 완료. **Citra 실화면 확인은 아직 pending**이다.

이전 v159 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v159_issue152_shared_literal_fix.py --check
py -3 tools\verify_sangokushi2_v159_issue152_shared_literal_fix.py
```

v159은 마지막 Citra 승인본이다. 권위 자료는 `analysis\v159_issue152_shared_literal_fix_targets.json`, `analysis\v159_issue152_shared_literal_fix_report.json`, sealed baseline, builder/verifier, runtime 승인 `analysis\v159_issue152_citra_runtime_confirmation.json`이며 `방문` 팝업의 `군주만 가능합니다.` 정상 출력이 확인됐다. shared table-base literal을 재배치할 때 모든 xref와 +0/+4/+8 member를 전수 확인하는 규칙은 v160에도 그대로 적용한다.

이전 v158 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v158_issue152_visit_popup.py --check
py -3 tools\verify_sangokushi2_v158_issue152_visit_popup.py
```

v158의 terminator/NUL 가설은 Citra 실패와 live-RAM 일본어 치환 실패로 폐기됐고, v159의 shared-literal 진단이 이를 대체한다.

이전 v157 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v157_issue157_alignment.py --check
py -3 tools\verify_sangokushi2_v157_issue157_alignment.py
```

v157 권위 자료는 `analysis\v157_issue157_alignment_targets.json`, `analysis\v157_issue157_alignment_report.json`, sealed baseline `analysis\v157_issue157_alignment_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v157_issue157_alignment.py`, `tools\verify_sangokushi2_v157_issue157_alignment.py`다. #157 actual owners는 v156에서 확정된 값을 유지하고, v157은 정렬만 보정한다. `0x1CF750=ＢＧＭ변경`은 exact 10B/no trailing padding, `0x1CFE2C=전략`은 4B 직후 immediate NUL이다. refs `0x1DC128/0x1DC15C/0x1DC6CC`는 byte-exact이며 모든 비-code v156 파일도 byte-exact다. **Citra에서 두 center만 재확인하면 된다.**

이전 v156 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v156_issue157.py --check
py -3 tools\verify_sangokushi2_v156_issue157.py
```

이전 v155 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v155_issue153_156.py --check
py -3 tools\verify_sangokushi2_v155_issue153_156.py
```

v155는 v156의 exact baseline이다. #153의 BGM 26곡/`디폴트` 및 #156 `msgsec01 direct174~182`, `폴=8C98`은 유지하지만 `0x86D84` 실제 상단설명 source 판정과 `전 략` 표기, 메인 메뉴 BGM 버튼 이미지 추정은 v156에서 폐기됐다.

이전 v154 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v154_issue127_131_133_142.py --check
py -3 tools\verify_sangokushi2_v154_issue127_131_133_142.py
```

v154 권위 자료는 `analysis\v154_issue127_131_133_142_targets.json`, `analysis\v154_issue127_131_133_142_report.json`, sealed baseline `analysis\v154_issue127_131_133_142_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v154_issue127_131_133_142.py`, `tools\verify_sangokushi2_v154_issue127_131_133_142.py`다. v155가 이를 exact baseline으로 계승한다.

이전 v153 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v153_issue146_item_effect_runtime_fix.py --check
py -3 tools\verify_sangokushi2_v153_issue146_item_effect_runtime_fix.py
```

v153 권위 자료는 `analysis\v153_issue146_item_effect_runtime_fix_targets.json`, `analysis\v153_issue146_item_effect_runtime_fix_report.json`, sealed baseline `analysis\v153_issue146_item_effect_runtime_fix_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v153_issue146_item_effect_runtime_fix.py`, `tools\verify_sangokushi2_v153_issue146_item_effect_runtime_fix.py`다. #146 권위는 v154에서도 byte-exact 계승한다. 방천화극 상세 `0x195F5C`를 포함한 effect formatter 8개와 `msgsec07 0x63C`의 `냉=93FB`, item 표시명 `청낭서/나기향낭`의 `낭=8F83`이 최신 값이며 v152의 `8B9C` donor는 폐기 상태다.

이전 v152 권위 자료는 `analysis\v152_issue146_148_runtime_repair_targets.json`, `analysis\v152_issue146_148_runtime_repair_report.json`, sealed baseline `analysis\v152_issue146_148_runtime_repair_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v152_issue146_148_runtime_repair.py`, `tools\verify_sangokushi2_v152_issue146_148_runtime_repair.py`다. **#148 `武将/都市→무장/도시`는 사용자 Citra에서 성공하여 v153에 계승한다. 반면 #146 `0x1CFEB0 + 냉=8B9C` 원인 판정은 v153에서 폐기됐다.**

이전 v151 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v151_issue146_148_ui_text_repair.py --check
py -3 tools\verify_sangokushi2_v151_issue146_148_ui_text_repair.py
```

v151 권위 자료는 `analysis\v151_issue146_148_ui_text_repair_targets.json`, `analysis\v151_issue146_148_ui_text_repair_report.json`, sealed baseline `analysis\v151_issue146_148_ui_text_repair_baseline\PatchSnapshot`, builder/verifier `tools\build_sangokushi2_v151_issue146_148_ui_text_repair.py`, `tools\verify_sangokushi2_v151_issue146_148_ui_text_repair.py`다. `--check`는 sealed v150에서 code/msgsec09/msgsec18 세 출력을 재계산해 현재 Patch와 byte-for-byte 비교한다. verifier는 code same-width slot/NUL/absolute pointer, msgsec09 direct76, msgsec18 direct111 39B span과 NAME1/K/H/newline skeleton, v150 비대상 파일 및 font byte-exact를 독립 확인한다. **Citra runtime 확인 pending**이다.

이전 v150 검증 명령:

```powershell
py -3.12 tools\build_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py --check
py -3.12 tools\verify_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py
```

v150 권위 자료는 `analysis\v150_issue136_138_139_143_dialogue_sweep_targets.json`, `analysis\v150_issue136_138_139_143_dialogue_sweep_report.json`, sealed baseline `analysis\v150_issue136_138_139_143_dialogue_sweep_baseline\PatchSnapshot`, residue 분류 `analysis\v150_dialogue_popup_residue_classification.json`, preparer/builder/verifier `tools\prepare_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py`, `tools\build_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py`, `tools\verify_sangokushi2_v150_issue136_138_139_143_dialogue_sweep.py`다. preparer는 현재 Patch가 아니라 sealed v149 baseline을 source preimage로 읽어야 한다. v150은 구조/결정성 검증 완료, **Citra runtime 확인 pending**이다.

이전 v149 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v149_issue144_145.py --check
py -3 tools\verify_sangokushi2_v149_issue144_145.py
```

v149 권위 자료는 `analysis\v149_issue144_145_targets.json`, `analysis\v149_issue144_145_report.json`, sealed baseline `analysis\v149_issue144_145_baseline\PatchSnapshot`, runtime 확인 `analysis\v149_issue144_145_citra_runtime_confirmation.json`, 빌더/검증기 `tools\build_sangokushi2_v149_issue144_145.py`, `tools\verify_sangokushi2_v149_issue144_145.py`다. #144는 `msgsec19` 재구성이 아니라 Message per-file read 상한을 `0xD000→0x10000`으로 올리는 runtime fix이며, #145는 code fixed slot same-width 치환이다. **#144/#145는 사용자 Citra 승인 상태로 동결**하며, 동일 문제가 다시 보일 때는 먼저 Message 파일 크기와 loader read cap을 확인하고 `msgsec19` pointer/header를 임의 재패킹하지 않는다.

이전 v148 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v148_issue137_item_descriptions.py --check
py -3 tools\verify_sangokushi2_v148_issue137_item_descriptions.py
```

v148 권위 자료는 `analysis\v148_issue137_item_descriptions_targets.json`, `analysis\v148_issue137_item_descriptions_report.json`, sealed baseline `analysis\v148_issue137_item_descriptions_baseline\PatchSnapshot`, `tools\prepare_sangokushi2_v148_issue137_item_descriptions.py`, 빌더/검증기 `tools\build_sangokushi2_v148_issue137_item_descriptions.py`, `tools\verify_sangokushi2_v148_issue137_item_descriptions.py`다. `--check`는 sealed v147에서 `font.g1t/msgsec14.dat`를 재계산해 현재 Patch와 byte-for-byte 비교한다. verifier는 JSON/HTML 번역 coverage, 67개 direct span raw readback, 121-pointer/header/file-size/separator 보존, 19.5×3 layout, 신규 `쥐` 글리프 1셀 pixel-exact, v147 대비 변경 파일 2개를 독립 확인한다.

이전 v147 #141 검증 명령:

```powershell
py -3 tools\build_sangokushi2_v147_issue141_biography_wording.py --check
py -3 tools\verify_sangokushi2_v147_issue141_biography_wording.py
```

v147은 v146 열전 구조를 그대로 계승해 왕윤 열전 index20 한 건만 수정한 중간 기준본이다.

이전 v145 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v145_issue140_officer_biographies.py --check
py -3 tools\verify_sangokushi2_v145_issue140_officer_biographies.py
```

v145 권위 자료는 `analysis\v145_issue140_officer_biographies_targets.json`, `analysis\v145_issue140_officer_biographies_report.json`, sealed baseline `analysis\v145_issue140_officer_biographies_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v145_issue140_officer_biographies.py`, `tools\verify_sangokushi2_v145_issue140_officer_biographies.py`다. v145는 #140의 bank 구조/798개 완문 재구성 기준본이며, v146은 이 구조를 그대로 계승해 Citra alias 실패만 보정한다.

이전 v144 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v144_issue135_citra_runtime_repair.py --check
py -3 tools\verify_sangokushi2_v144_issue135_citra_runtime_repair.py
```

v144 권위 자료는 `analysis\v144_issue135_citra_runtime_repair_targets.json`, `analysis\v144_issue135_citra_runtime_repair_report.json`, sealed baseline `analysis\v144_issue135_citra_runtime_repair_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v144_issue135_citra_runtime_repair.py`, `tools\verify_sangokushi2_v144_issue135_citra_runtime_repair.py`다. `--check`는 sealed v143에서 `code.bin/msgsec06` 두 출력을 메모리상 재계산해 현재 Patch와 byte-for-byte 비교한다. 특히 verifier는 `0x180990`의 30B memcpy 마지막 바이트가 NUL인지, `0x181254`의 실제 messenger code source가 `사자 무장입니다\0`인지, `0x17DE10/0x17DE24`가 SP에 `는 + ASCII space + NUL`을 만드는지 직접 확인한다. 번역 DB `msgsec06_0037_002`도 `이제 수모를<$0A>참고 살지 않겠다.`가 최신 권위다.

이전 v143 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v143_issue134.py --check
py -3 tools\verify_sangokushi2_v143_issue134.py
```

v143 권위 자료는 `analysis\v143_issue134_targets.json`, `analysis\v143_issue134_report.json`, sealed baseline `analysis\v143_issue134_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v143_issue134.py`, `tools\verify_sangokushi2_v143_issue134.py`다. v144의 exact baseline이며 #134 `똑=9953` 렌더 실패와 두 29B runtime copy 수정의 직전 증거로 유지한다.

이전 v142 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v142_issue135.py --check
py -3 tools\verify_sangokushi2_v142_issue135.py
```

v142 권위 자료는 `analysis\v142_issue135_targets.json`, `analysis\v142_issue135_report.json`, sealed baseline `analysis\v142_issue135_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v142_issue135.py`, `tools\verify_sangokushi2_v142_issue135.py`다. v143의 exact baseline이며 #135 네 화면/`98B3`/동적 공백 수정의 직전 증거로 유지한다.

이전 v141 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v141_live_popup_sweep.py
py -3 tools\verify_sangokushi2_v141_live_popup_sweep.py
```

v141 권위 자료는 `analysis\v141_live_popup_sweep_targets.json`, `analysis\v141_live_popup_sweep_report.json`, sealed baseline `analysis\v141_live_popup_sweep_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v141_live_popup_sweep.py`, `tools\verify_sangokushi2_v141_live_popup_sweep.py`다. exact v140의 direct-owned popup 34개를 원 raw span 안에서만 교체하며 verifier는 각 v140 source raw/직접 pointer ownership을 독립 재추적해 5개 Message 파일 전체를 byte-exact 재구성하고 최종 active untranslated popup candidate=0을 확인한다.

이전 v140 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v140_issue128_130_132.py
py -3 tools\verify_sangokushi2_v140_issue128_130_132.py
```

v140 권위 자료는 `analysis\v140_issue128_130_132_targets.json`, `analysis\v140_issue128_130_132_report.json`, sealed baseline `analysis\v140_issue128_130_132_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v140_issue128_130_132.py`, `tools\verify_sangokushi2_v140_issue128_130_132.py`다. v141의 exact baseline이며 #128/#129/#130/#132 수정과 popup inventory의 직전 증거로 유지한다.

이전 v139 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v139_image_update.py
py -3 tools\verify_sangokushi2_v139_image_update.py
```

v139 권위 자료는 `analysis\v139_image_update_targets.json`, `analysis\v139_image_update_report.json`, sealed baseline `analysis\v139_image_update_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v139_image_update.py`, `tools\verify_sangokushi2_v139_image_update.py`다. exact v138을 기준으로 지정 PNG 19개만 세 G1T에 1:1 반영하며, verifier는 exact v138+3 containers 조성, 19/19 pixel-exact, header/비대상 payload byte-exact, v138 비대상 Patch byte-exact를 독립 확인한다. `ChangeBGM.g1t`와 `BGMDown.g1t`는 v139부터 Patch에 존재하므로 이후에는 최신 Patch 컨테이너를 base로 사용한다.

이전 v138 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v138_issue123_126.py
py -3 tools\verify_sangokushi2_v138_issue123_126.py
```

v138 권위 자료는 `analysis\v138_issue123_126_targets.json`, `analysis\v138_issue123_126_report.json`, sealed baseline `analysis\v138_issue123_126_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v138_issue123_126.py`, `tools\verify_sangokushi2_v138_issue123_126.py`다.

이전 v130 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v130_hex_orientation_repair.py
py -3 tools\verify_sangokushi2_v130_hex_orientation_repair.py
```

v130 권위 자료는 `analysis\v130_hex_orientation_repair_targets.json`, `analysis\v130_hex_orientation_repair_report.json`, sealed baseline `analysis\v130_hex_orientation_repair_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v130_hex_orientation_repair.py`, `tools\verify_sangokushi2_v130_hex_orientation_repair.py`다. exact v129를 기준으로 Hex 38개만 정상 편집 PNG에서 표준 encoder로 직접 다시 pack하며, v129 실패 반전형 재현·v130 direct readback·header/비대상 payload·전체 비대상 manifest byte-exact를 gate로 고정한다.

이전 v129 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v129_issue099_105_113_114.py
py -3 tools\verify_sangokushi2_v129_issue099_105_113_114.py
```

v128 이미지 빌더는 역사적 재현용일 뿐이다. Hex 38개에 사용한 선행 `from_edit_view(flip_y)`가 Citra에서 실패했으므로 v130 이후 runtime 이미지 pack에 재사용하지 않는다.

이전 v127 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v127_hex_battle_time_army.py
py -3 tools\verify_sangokushi2_v127_hex_battle_time_army.py
```

이전 v126 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v126_issue099_104_105_112.py
py -3 tools\verify_sangokushi2_v126_issue099_104_105_112.py
```

이전 v125 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v125_issue106_107_108.py
py -3 tools\verify_sangokushi2_v125_issue106_107_108.py
```

v125 권위 자료는 `analysis\v125_issue106_107_108_targets.json`, `analysis\v125_issue106_107_108_report.json`, sealed baseline `analysis\v125_issue106_107_108_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v125_issue106_107_108.py`, `tools\verify_sangokushi2_v125_issue106_107_108.py`다. v126의 exact baseline이며 #106~#108의 직전 증거로 유지한다.

이전 v124 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v124_issue101_102_109_110_111.py
py -3 tools\verify_sangokushi2_v124_issue101_102_109_110_111.py
```

v124 권위 자료는 `analysis\v124_issue101_102_109_110_111_targets.json`, `analysis\v124_issue101_102_109_110_111_report.json`, sealed baseline `analysis\v124_issue101_102_109_110_111_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v124_issue101_102_109_110_111.py`, `tools\verify_sangokushi2_v124_issue101_102_109_110_111.py`다. v125의 exact baseline이며 #101/#102/#109/#110/#111 규칙의 직전 증거로 유지한다.

이전 v123 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v123_xuzijiang_issue095_097_100_103.py
py -3 tools\verify_sangokushi2_v123_xuzijiang_issue095_097_100_103.py
```

v123 권위 자료는 `analysis\v123_xuzijiang_issue095_097_100_103_targets.json`, `analysis\v123_xuzijiang_issue095_097_100_103_report.json`, sealed baseline `analysis\v123_xuzijiang_issue095_097_100_103_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v123_xuzijiang_issue095_097_100_103.py`, `tools\verify_sangokushi2_v123_xuzijiang_issue095_097_100_103.py`다. v124의 exact baseline이며 #095/#097/#100/#103 조립 대사 규칙의 직전 증거로 유지한다.

이전 v122 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v122_issue075_096_098.py
py -3 tools\verify_sangokushi2_v122_issue075_096_098.py
```

v122 권위 자료는 `analysis\v122_issue075_096_098_targets.json`, `analysis\v122_issue075_096_098_report.json`, sealed baseline `analysis\v122_issue075_096_098_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v122_issue075_096_098.py`, `tools\verify_sangokushi2_v122_issue075_096_098.py`다. v123의 exact baseline이며 #075/#096/#098의 직전 증거로 유지한다.

이전 v121 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v121_issue069_075.py
py -3 tools\verify_sangokushi2_v121_issue069_075.py
```

v121 권위 자료는 `analysis\v121_issue069_075_targets.json`, `analysis\v121_issue069_075_report.json`, sealed baseline `analysis\v121_issue069_075_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v121_issue069_075.py`, `tools\verify_sangokushi2_v121_issue069_075.py`다. #075의 첫 상성 설명 문구는 v122에서 대체됐지만, #069과 나머지 #075 fixed-source/control 구조의 직전 증거로 유지한다.

이전 v120 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v120_issue092_095.py
py -3 tools\verify_sangokushi2_v120_issue092_095.py
```

v120 권위 자료는 `analysis\v120_issue092_095_targets.json`, `analysis\v120_issue092_095_report.json`, sealed baseline `analysis\v120_issue092_095_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v120_issue092_095.py`, `tools\verify_sangokushi2_v120_issue092_095.py`다. 빌더는 exact v119를 기준으로 `code.bin`과 `msgsec04/06/07/08/09.dat`만 수정한다. active dynamic-name 조사, translated code formatter 조사, ARM ADR 직참조 일본어 dialogue를 각각 독립 gate로 0건까지 감사하고, `font.g1t` 및 비대상 Patch 파일이 v119 byte-exact인지 확인한다. 완성본 위 재실행에서 동일 SHA가 재현되어야 하며 실제 화면 승인 전에는 정적 PASS를 Citra 성공으로 승격하지 않는다.

이전 v119 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v119_season_localization.py
py -3 tools\verify_sangokushi2_v119_season_localization.py
```

v119 권위 자료는 `analysis\v119_season_localization_targets.json`, `analysis\v119_season_localization_report.json`, sealed baseline `analysis\v119_season_localization_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v119_season_localization.py`, `tools\verify_sangokushi2_v119_season_localization.py`다. 빌더는 exact v118을 기준으로 `code.bin`, `font.g1t`, 계절별 runtime ARC 4개만 수정한다. 상단 계절 합자는 local-only alias로 제한하고, 각 ARC에서는 대응 `timg/season_*.bflim`만 교체한다. 꽃 texture·BFLYT·BFLAN·비대상 member는 byte-exact여야 한다. 완성본 위 재실행에서 동일 SHA가 재현되어야 하며 실제 화면 승인 전에는 정적 PASS를 Citra 성공으로 승격하지 않는다.

이전 v118 복원/검증 명령:

```powershell
py -3 tools\build_sangokushi2_v118_issue087_091_runtime_repair.py
py -3 tools\verify_sangokushi2_v118_issue087_091_runtime_repair.py
```

v118 권위 자료는 `analysis\v118_issue087_091_runtime_repair_targets.json`, `analysis\v118_issue087_091_runtime_repair_report.json`, sealed baseline `analysis\v118_issue087_091_runtime_repair_baseline\PatchSnapshot`, 빌더/검증기 `tools\build_sangokushi2_v118_issue087_091_runtime_repair.py`, `tools\verify_sangokushi2_v118_issue087_091_runtime_repair.py`다. 빌더는 exact v117을 기준으로 font/msgsec09/msgsec15만 수정하며 `02 2D` dynamic prefix, C4 제거, 16개 input prompt fixed spans, 94CE/969B alias 분리, font cell2855 단독 변경, msgsec09 header/separator를 독립 gate로 검증한다. 완성본 위 재실행에서 동일 SHA가 재현되어야 하며 실제 화면 승인 전에는 정적 PASS를 Citra 성공으로 승격하지 않는다.

v117 권위 자료는 이전 화면/source 실패 이력 복원용으로 `analysis\v117_issue066_color_issue091_targets.json`, `analysis\v117_issue066_color_issue091_report.json`, sealed baseline `analysis\v117_issue066_color_issue091_baseline\PatchSnapshot`에 유지한다. 특히 **`02 2D C4` 3B 전체를 동적 군주 토큰으로 본 판정은 v118에서 폐기**되었으므로 신규 작업에서 재사용하지 않는다.

v116 권위 자료는 `analysis\v116_issue066_068_targets.json`, `analysis\v116_issue066_068_report.json`, sealed baseline `analysis\v116_issue066_068_baseline\PatchSnapshot`에 유지한다. `챔=95AC/physical3009`와 #068 기본 번역은 v117이 byte-exact 계승한다. v115/v114 자료는 이전 Citra 승인 및 실패 source 이력 복원용으로 그대로 보존한다.

v86 빌더는 `analysis\v86_github_issue047_crash_safe_baseline\PatchSnapshot`에 sealed한 **정확한 v85-intermediate 전체 Patch 51파일**을 기준으로 `ExeFS/code.bin` 하나만 수정한다. v83 #035 target 169개 중 `offset % 4 == 3`이고 바로 앞 aligned word가 ARM `B(EAxxxxxx)`인 항목은 정확히 10개이며, 10개 모두 Original ADR이 `bad offset + 1`을 가리키는 것을 gate로 증명한다. 각 branch 4바이트를 Original로 복구하고 v85 한국어 문구를 실제 ADR 시작으로 옮긴다. #047 핵심은 `0x56C58: 89000436 -> EA000436`, 복구 branch target은 `0x57D38`이다. `0x56D28` 표시문과 `0x56D64` 다음 문구는 v85 byte-exact로 유지해야 하며, 완성 v86 위 재실행과 독립 readback 모두 동일해야 한다.

alias 재발 방지 게이트는 `tools\sangokushi2_alias_guard.py`다. v43 튜토리얼/alias 수정본만 다시 생성하려면 `tools\build_sangokushi2_v43_tutorial_text_alias_repair.py`, v42 신무장 화면은 `tools\build_sangokushi2_v42_new_officer_fixed_slots.py`를 사용한다.

## 3. 폰트 권위 규칙

- 원본 폰트: `RomFS\Common\Font\font.g1t`
- 한글 원본: `font\Sam3KRFont`
- atlas: **14×14 / 73열**
- 기본 Sam3 프레임: `(2,1)-(15,14)` 13×13 → 게임 셀 `(1,0)`에 복사
- 넓은 글자는 해당 글자만 최소 가로 보정한다. 전체 폰트 크기는 바꾸지 않는다.
- 전역 `physical = logical-5`는 폐기한다.
- 현재 권위 공식: **`physical = JIS 94×94 선형 index(alias) - 858`**
- 원본 valid-JIS 2,350개 전체에서 physical 552~3717, 고유 2,350개, 중복 0으로 검증했다.
- 단, **정적 physical 계산/packed-font readback이 Citra 런타임 성공을 보증하지는 않는다.** v50 `탁=98A4`에 이어 v57에서 `엇=98FB`도 정적 readback은 Sam3 원본과 pixel-exact였지만 Citra 교환 화면에서는 `첨`으로 표시됐다. 따라서 lead `>=0x98` far alias는 런타임 미검증 위험군으로 유지하고, 사용자 실화면 실패가 확인된 문자는 전역 remap보다 먼저 low-risk donor로 최소 우회한다.
- **v115 사용자 확정:** 필요한 한글 글리프가 없다는 이유로 번역 의미를 바꾸거나 어색하게 회피하지 않는다. 기존 safe alias가 없으면 runtime ref=0, Korean owner=0, historical alias 충돌 없음, valid-JIS, bleed/인접 셀 안전을 증명한 미사용·저사용 일본어 한자 donor 셀에 필요한 한글을 넣고 packed font pixel-exact readback을 거친다. 문자열 슬롯 용량 때문에 사용자가 직접 축약안을 허용한 경우만 그 축약을 사용한다.

### 1px 수직 잔상 규칙

Citra에서 글자 아래 1px 잔상이 보이면 해당 글자 자체를 다시 그리지 않는다. 게임이 바로 아래 atlas 행(+73)의 첫 scanline을 샘플링하는 현상부터 확인한다.

v20에서 런타임/atlas로 확정한 사례:
- `오` physical566 아래 physical639
- `하` physical594 아래 physical667
- `휘` physical1681 아래 physical1754

v20은 physical639/667/1754의 기존 글리프를 **셀 안에서 1px 아래로 이동**시켜 top y=0을 투명 guard row로 만든다. 13px 원본 도트는 잃지 않는다.

## 4. 시나리오1 / code.bin 대사

v17에서 장각·황건·하진 관련 hardcoded 문자열 28개를 적용했다. v18/v19/v20은 이 영역을 유지한다.

- 장각 봉기 / 태평도 / 영수 치료 / 신도 집결
- 장각·장보·장량 배치
- 황건적 확산 / 황건의 난
- 하진의 황건 토벌 명령 / 대장군 임명
- 환관과 하진의 대립·암살
- 황건적 진압 완료

`code.bin` 번역은 원본 NUL-terminated 고정 슬롯 길이를 절대 넘기지 않는다.

## 5. 전쟁 메뉴

큰 `전쟁` 카테고리 아이콘은 이미지이며 사용자 포토샵 작업 대상이다.

실제 중앙 버튼은 `ExeFS\code.bin` 고정 C-string 테이블:

- `0x1CF59B` → `전쟁`
- `0x1CF5A4` → `수송`
- `0x1CF5AC` → `조선`
- `0x1CF5B4` → `재편성`
- `0x1CF5BC` → `이동`
- `0x1CF5C4` → `징병`
- `0x1CF5CC` → `모병`
- `0x1CF5D4` → `전쟁준비`
- `0x1CF5E0` → `훈련`

## 6. 인사 메뉴 — v20 추가

실제 버튼은 전쟁 메뉴 바로 뒤의 `code.bin` 고정 C-string 테이블이다.

- `0x1CF5E8` `委 任` → `위임`
- `0x1CF5F0` `登 用` → `등용`
- `0x1CF5F8` `施 し` → `시혜`
- `0x1CF600` `任 命` → `임명`
- `0x1CF608` `解 雇` → `해고`
- `0x1CF610` `捜 索` → `수색`
- `0x1CF618` `没 収` → `몰수`
- `0x1CF620` `褒 美` → `포상`

상단 스크롤 설명은 `msgsec10_0005_001`이다. v20 적용문:

`도시에서 무장을 수색. 발견하면 재야 등록`

원본 44B 슬롯 안에 40B로 고정 삽입하고 뒤 4B만 ASCII space로 패딩한다.

## 7. msgsec10/msgsec15 고정 위치 규칙 — 매우 중요

v19 Citra에서 다음 두 회귀가 확인됐다.

1. `무장을...`의 첫 `무`가 일본어 `ら/ﾗ`처럼 보임.
2. 튜토리얼 `게임 설명`에서 첫 `46개 도시...` fragment가 사라지고 다음 조각부터 출력됨.

`무` alias `8BD7`/physical1172 자체는 정상이며 같은 v19의 `휘하 무장이 없으면`에서는 정상 출력된다. 따라서 글리프 문제가 아니다.

원인 결론: `msgsec10/msgsec15` UI 일부는 ordinary pointer 외에 fragment 내부 위치를 고정 참조하는 정황이 강하다. v19에서 파일을 늘려 재패킹한 것이 fragment 시작 위치를 움직였다.

**v20부터 이 두 UI 영역은 가변 길이 재패킹 금지.**

- `msgsec10.dat`: Original 6,884B와 v20 6,884B 동일
- `msgsec15.dat`: Original 20,373B와 v20 20,373B 동일
- fragment 시작 위치·header·pointer·metadata를 전부 그대로 두고 raw fragment 바이트만 같은 길이 안에서 덮어쓴다.
- 부족한 남는 바이트는 NUL이 아니라 trailing ASCII space로 채운다.

### 이동 도움말

실제 ID: `msgsec10_0000_001`, 원본 31B.

v20 문구: `무장을 인접 도시로 이동합니다.` = 30B + trailing space 1B.

### 게임 설명 — v22 권위 레이아웃

- 금색 제목: `msgsec15_0000_002` → `게임 설명`
- 본문: `msgsec15_0025_002 ~ msgsec15_0035_000`, 원본 총 **191B**.
- 일본어 원본 화면을 기준으로 **3줄 + 빈 줄 + 3줄**을 표준으로 한다. 한 줄 최대 20 fullwidth cell.
- v22 본문:
  - `４６개 도시를 지배해 대륙을 통일합니다.`
  - `군주 사망하면 후계자가 될 무장이 없으면`
  - `게임오버입니다.`
  - `(빈 줄)`
  - `게임 설명을 볼 수 있습니다.`
  - `아래 화면에서 명령을 고르면`
  - `위 화면에 표시`
- 줄 폭은 `[20, 20, 8, 0, 14, 14, 7]`. 원본 11개 fragment를 각각 고정 길이로 패치하며, 모든 시작은 완전한 글자/제어코드 경계다. 한글 2바이트를 fragment 경계에 걸치지 않는다.
- 튜토리얼 하위 메뉴는 일반 명령·전쟁 규칙·전투 기술을 포함해 **100 single slot + 15 split-SJIS group**을 fixed-position 한글화했다. parent 경계에서 SJIS가 갈라진 항목은 인접 fragment를 묶되 위치는 이동하지 않는다.
- `시혜`와 `매복의독`은 v27 Citra에서 성공한 **뒤 `05 05 05` separator의 1바이트 차용 방식**을 현재 권위로 사용한다. 정상 2바이트 한글을 넣고 `05 05`를 남기며 다음 fragment 시작과 전체 파일 크기는 유지한다. 과거 `혜` 축약/ASCII·halfwidth alias 우회는 폐기했다.

## 8. 한 줄 표시 길이 규칙

현재 런타임 화면을 기준으로 보수적으로 다음을 사용한다.

- 상단 스크롤 도움말: **최대 18 fullwidth cell** 권장
- 튜토리얼/일반 대사: **한 줄 최대 20 fullwidth cell** 권장
- ASCII는 대략 반각 0.5 cell로 계산한다.
- 넘길 경우 엔진 자동 줄바꿈을 기대하지 말고 번역을 먼저 압축하거나 명시적 `<$0A>`를 사용한다.

## 9. 공통 대사 v20

`RomFS\Message\msgsec07.dat`에서 명령/이벤트 공통 대화를 확장했다.

정책:
- challenge/goal 성격이 강한 앞쪽 0~17 parent는 제외
- opaque segment 제외
- parent group은 중간 fragment만 섞지 않고 완전한 묶음으로 선택
- 원문/번역 제어코드 순서 동일
- 번역 한 줄 최대 20 fullwidth cell
- `sangokushi2_message_repacker.py`의 mixed-header repacker 사용

v20 적용:
- fragment 44개
- 실제 표시 line 기준 **63줄**
- 최소 50줄 요청 충족
- 상세 ID는 `analysis\v20_fixed_ui_common_dialogue_report.json`의 `common_dialogue.ids`가 권위 자료다.

이 영역은 v20에서 첫 런타임 시험이므로 Citra에서 명령/이벤트 대사 진행 중 회귀 여부를 확인한다.

## 10. v20 신규 폰트

- v19 기존 글리프는 재배치하지 않는다.
- v20에 필요한 신규 한글만 추가: **43자**
- Original Message/code.bin/Scenario에서 실제 사용 중인 JIS alias는 donor로 쓰지 않는다.
- 신규 physical은 기존/신규 한글과 ±73 충돌이 없는 셀만 사용한다.
- Sam3KRFont에 없는 글자는 빌드 중단한다.
- A4 재디코딩 round-trip을 통과해야 한다.
- **v43 alias 불변/충돌 게이트:** 런타임에서 한 번 사용된 `문자→alias`는 임의 재할당하지 않는다. 신규/수정 문장에 쓰는 한글은 `tools\sangokushi2_alias_guard.py`로 alias 공유 여부를 검사하고, legacy collision이면 fresh alias로 분리한 뒤 해당 공용/3DS 런타임 복제본을 모두 최종 map으로 재인코딩한다. 과거 `analysis/v*_report.json`의 `glyph_rows/alias/old_alias/new_alias`에 한 번이라도 등장한 2바이트 alias는 stale message가 참조할 가능성이 있으므로 신규 donor로 재사용 금지다.

## 11. 현재 주요 SHA-256 (v67-intermediate)

- `font.g1t`  `3D6EEE4737F0C6ABF1FC0E70371871BEC30E5EB340E7315F0EBD17C67A399BD9`
- `fix_data.bin` `A00BB67145FA074EAC25E1195A12D65965233B196FA016A03FECE60478B940F5`
- `msgsec04.dat` `EDDBF5C382097CE95C27808F12F6DED3E782F08A8010FF8C36C4C4B88D1928E6`
- `msgsec05.dat` `B408E5B6B101DDE3C6BC98C40E2B0023D006B3FA44AA9CEC234101FCB35AA3A3`
- `msgsec06.dat` `4D6EBE87777990B720DFA37B3AAC8C0CAA56A64482BEE8A3148A90555068941A`
- `msgsec07.dat` `851F2A11DD807126972110162821EF48F3A090EE08917D8606DA795C9D089B43`
- `msgsec08.dat` `3AC66CA36829DDF8016A132568E4F8244B66A0604D5DF2C9820B3C519D2E436E`
- `code.bin`  `D935B2B706BAA3D8CD0807079FA27502AA367B93B605DB0FA6A39CB17E1F9294`
- `msgsec01.dat` `CF353E24820FFBE7916ACE19543B6A105A4E3F796E0EC9E17F12FB298BEE06BF`
- `msgsec11.dat` `04B77E10CD5295A8E4A650EBA2FCE296E4574758E91FB87C08C29CD40C1BC161`
- `msgsec16.dat` `E15D508FEAB1942541D78D26A4A33A1D1C71F2F9BE485BF14F6F570B3CDE7F12`
- `msgsec10.dat` `29885C4741B8F8BCB7F63D00BD8C47C14207AA740766102F8A70B85985CA71F4`
- `msgsec15.dat` `02688DC0513FA5EF2B627258EED610160F6FB37FA5E1439D5128D77C822DD39F`
- `start_menu_down.g1t` `1DDF65F68F9E6F2156F913009B1E0C4BA67F344A44E4F470CA879ED7B0B057D4`
- `edit_bushou_base_menu_down.g1t` `3D3BF50E3D8D596D8B4104CCC3B81751B72073F309D151C2073DA57C916F6046`
- `Tutorial.g1t` `0319358DB04F9BDE63A33FD8DC13B58D2EE7B2E16D6A9480045503CE287BE4F9`
- `Tutorial_sub.g1t` `B2688A4303BB556737F521768748B4A9E59F61D0F5F1757764712D1752DF48E4`
- `start_menu_up.g1t` `0B80514DDB0BA620D6584945E7E322BD2AFD504774071C00326991826DA1E84C`

현재 빌더: `tools\build_sangokushi2_v67_github_issue005_010.py`

현재 alias gate: `tools\sangokushi2_alias_guard.py`

현재 msgsec07 header gate: `tools\sangokushi2_msgsec07_flat_header.py`

현재 msgsec06 header gate: `tools\sangokushi2_msgsec06_flat_header.py`

현재 report: `analysis\v67_github_issue005_010_report.json`

현재 v67 target inventory: `analysis\v67_github_issue005_010_targets.json`

## 12. 다음 Citra 확인 순서

1. **v67 GitHub issue #5 최우선:** 담당 군사 변경 화면에서 일본어 `現在、`가 완전히 사라지고 **`현재 [무장명]이(가) 담당군사입니다 / 변경합니까?`**로 표시되는지 확인한다. 실제 선택 무장명이 동적으로 바뀌어야 한다.
2. **v67 GitHub issue #10 최우선:** 챌린지 1~7을 모두 순회해 제목, 플레이어, 클리어, 게임오버, 평가 값이 전부 한글인지 확인한다. 챌린지 7의 마지막 평가 두 항목 `피해 없이 승리 / 달성 연수`도 각각 정상 분리되어야 하며 별점 UI/줄바꿈 회귀가 없어야 한다.
3. **v66 GitHub issue #9:** 화면 상단이 콜론 없이 정확히 **`군주를 선택`**으로 표시되는지 확인한다. 단어 사이에는 binary `8140` 전각 공백을 사용했으므로 공백 폭이 과도하거나 기호로 보이지 않는지도 확인한다.
4. **v65 GitHub issue #3~#8 + #9 첫 문구:** `선정을 오래 행하면 / 백성들도...`, 문관 임명 경고의 `없게 됩니다.`와 전각 `？`, `챌린지 시나리오`, 수명 `B` 제거, `타국 전쟁`, `플레이 인원을 설정하세요`를 확인한다.
3. **v64/v63 회귀 확인:** 임시징수 경고 `백성들의 신뢰를 / 저버릴 생각입니까？`의 `뢰=94D6`와 사마휘 3DS 모드 가이드 15개가 그대로 정상인지 확인한다.
4. **v61 회귀 확인:** 임명 도시 선택 전각 `？`, `노의 수가...`, 임시징수 실행 확인, 3DS/챌린지/잠긴 시나리오 본문을 확인한다.
8. **v60 회귀 확인:** `손견 군이 서주에 / 쳐들어갔다！`, 세율 전체 문장+전각 `？`, `매복/철수`, 치수 `괜찮으십니까？`, 강노/군마 결과 표기를 확인한다.
9. **v59/v58 회귀 확인:** 임명 5버튼/상단 역할 설명/`장군 임명 대상을 선택`, 태수 경고, 방랑, 신분 `군주`, `북평/홍농` 포함 도시명과 전장 22개를 확인한다.
10. 외교→교환 `무엇을 내놓습니까？ / 무엇을 요구합니까？`와 금/쌀/노/강노/군마 수량 동사 구분, 공동작전/타국 정보/특산품/상인 공통 문구 및 기존 46개 도시명을 회귀 기준점으로 유지한다.
6. v55에서 확인된 `누가 합니까?`, `누가 거래합니까?`, v52 `누구에게 수색을 시키겠습니까?`, v51 `동탁`은 회귀 기준점으로 유지한다.
7. v54 항목(`주시겠습니까？`, `백성들도 매우 / 좋아할 것입니다`, `민충성도가 %d 이(가) 되었습니다`, `수색하시겠습니까？`, 시작 직후 잘못된 초선 prompt 없음)도 회귀가 없는지 확인한다.
4. v47에서 이미 수정한 적중작적 `배신합니다`와 치수 3/4 `오르지 않을 수 있습니다`의 점 제거가 그대로 유지되는지 확인.
5. v46에서 번역한 치수/상업투자/세율변경의 나머지 8페이지가 회귀 없이 그대로인지 확인. 세율변경 2/4는 88/88B, 4/4는 116/117B이므로 Citra 확인이 중요하다.
6. **v44 회귀 확인:** 병사고용 2/3 `저렴하지만 / 자금 사정에 맞게`, 위서의심 `편지를`, 태수변경 `남은 무장이`, 임명 2/2의 마지막 태수 설명이 그대로 정상인지 확인.
7. **v43/v42 회귀 확인:** 포상/함정/노·강노/수상전/적계략주의 및 신무장 fixed-slot 화면이 그대로 정상인지 확인.
8. 정보 메뉴 `밀정 / 무장 / 타국 / 속령일람 / 전장일람 / 세력지도` 진입 시 설명이 모두 한글인지 확인. `특산`은 기존 정상 상태를 유지해야 한다.
9. 외교 도움말의 `나라와` 같은 문장에서 `와`가 `외`처럼 보이지 않고 오른쪽 `ㅏ` 획이 정상인지 확인. `라`는 v40에서 수정하지 않았다.
10. v39에서 갱신한 StartMenu 006/029/030 및 EditBushou 002/003 이미지가 회귀하지 않았는지 확인.
11. v41의 Tutorial `002/003/004/005/014` 및 Tutorial_sub 계열 이미지가 방향·색상·투명도·배치 이상 없이 표시되는지 확인.
12. `함정 수` 계열에서 새로 추가한 3DS 전용 함정 안내 페이지가 한글인지 확인.
13. 3DS 모드 `수상전투법` 8/12~9/12가 한글인지 확인. 실제 런타임 소스는 `0123_002`, `0123_003+0124_000`이다.
14. 3DS 모드 `적계략주의` 10/12~11/12가 한글인지 확인. 실제 런타임 소스는 `0124_001+0125_000`, `0125_001+0126_000`; 12/12는 공용 레코드라 v31에서 이미 한글이다.
15. `설득` 첫 줄의 일본어 prefix가 사라지고 전체 페이지가 한글인지 확인. v32는 `0129_003+0130_000`을 한 그룹으로 처리한다.
16. `전군퇴각`, `대기무장` 설명이 한글인지 확인.
17. `빠`, `변`, `없` 왼쪽의 가는 세로줄이 사라졌는지 확인. victim 자체는 건드리지 않고 왼쪽 guard physical `1110/2365/661`의 rightmost column만 제거했다.
18. v31에서 정상 확인된 군사 설명/함정 수/노·강노/적토마 및 v30의 게임오버 간격·문사격·전각 물음표가 회귀하지 않았는지 간단히 확인.
19. `매복의독/시혜`의 v27 separator-borrow 성공 방식은 그대로 유지한다.

## 13. v21 메뉴/도움말 + 군사 조언 중간 시험본

- 빌더: `tools\build_sangokushi2_v21_command_help_advice.py`
- report: `analysis\v21_command_help_advice_report.json`
- v20을 매번 먼저 재생성한 뒤 확장하므로 v20의 fixed-position 튜토리얼, 전쟁/인사 성공 변경, 수직 bleed guard를 유지한다.
- 수색 도움말은 `msgsec10_0005_001 + msgsec10_0006_000`을 fixed-position으로 처리해 `무장을 수색해 재야 등록합니다.`로 수정했다. 뒤에 떨어져 보이던 일본식 `。`는 제거한다.
- `msgsec10.dat`에서 인사 8 / 외교 7 / 정보 7 / 개발 4 / 계략 5 = **31개 상단 도움말**을 한글화했다. 파일은 Original과 같은 **6,884B**이며 pointer/header 재패킹 금지 원칙을 유지한다.
- `code.bin` 실소스 버튼은 정보 7 / 외교 7 / 개발 4 / 계략 5를 직접 수정했다. 추가로 `군사조언 / 군사 / 담당군사` 문자열도 적용했다. 좌측 큰 카테고리 아이콘은 이미지이므로 미수정이다.
- 군사 조언/판단 하드코딩 문자열 **24개**를 추가 한글화했다. 정보 수집 결과, 전력 판단, 승산, 병량, 동맹, 계략 성공 가능성 관련 문구가 중심이다.
- `msgsec07.dat` 공통대사는 **85 fragment / 121 표시줄**로 확대되었으며 v20보다 **58줄 추가**다. complete parent / 제어코드 일치 / 20-cell 이하 게이트는 그대로 적용한다.
- 신규 한글은 **60자**만 추가하며 기존 검증 글리프는 재배치하지 않는다. JIS 94×94 linear physical 공식과 live-source donor 보호, ±73 수직 충돌 회피를 유지한다.
- v20 bleed guard physical `639/667/1754`는 v21에서도 top row nonzero=0으로 재검증됐다.
- Patch는 계속 **25파일 전체 세트**이며 `msgsec15.dat`는 v20의 20,373B 및 해시를 그대로 유지한다.
- 두 번 연속 처음부터 재빌드해 동일 결과를 확인했다. SHA-256: `font.g1t 9DFF0741C66C135396EBAA7BE3C780A8BD93FBAB25D1A75C9F8820CE6DE7ED34`, `code.bin BD2EE4A4ED7BE6B4CF119ED5F815AB6247F67FA359259303F5B9C90D294223CD`, `msgsec07.dat E512DBD5D0094863E2EAE0E26B8D002BF01FC5CE3CD1FF3768D94FA56A415E06`, `msgsec10.dat E9BB16DF6168E214FEA8A3AD0CD34BDC2307930C25121D2B5C14A3DE07BB69F0`, `msgsec15.dat BD61AC64925BDDA9D707E4DEC127A9D9D8B6E6A8ECFA2772E86709DAE0AB074F`.
- 다음 Citra는 수색 마침표 제거 → 인사 설명 → 정보 → 외교 → 개발 → 계략 → 군사 조언/신규 공통대사 순서로 중간 점검한다. 정적 PASS는 런타임 성공 선언이 아니다.

## 14. 금지 사항

- Original 수정 금지.
- 전역 `logical-5`로 돌아가지 말 것.
- v12/v13 bulk font를 현재 Patch에 섞지 말 것.
- 기존 검증 글리프를 재배치하지 말 것.
- `msgsec10/msgsec15` 문제 UI를 가변 길이 repack하지 말 것.
- 이미지 버튼/카테고리 아이콘은 별도 요청 없이 수정하지 말 것.
- 정적 PASS만으로 런타임 성공을 선언하지 말 것. Citra 확인이 최종 권위다.

## 15. 현재 이미지 전용 revision — v33

- v33은 v32 텍스트/폰트 세트를 그대로 유지한 이미지 전용 revision이다. 번역 HTML/JSON, `code.bin`, `Message\*.dat`, `font.g1t`에는 신규 변경이 없다.
- 적용 PNG는 `Extracted_Image\RomFS\StartMenu\start_menu_down\start_menu_down_004.png`~`008.png`이며, 출력 컨테이너는 `Sangokushi 2 Patch\RomFS\StartMenu\start_menu_down.g1t`이다.
- 다섯 texture는 `0x09 RGBA8`, 256×32이다. v33 빌더는 Original G1T의 index 4~8 payload만 교체하며 header·비대상 payload·전체 파일 크기를 보존한다.
- 검증 결과: RGBA8 무변경 왕복 5/5 byte-exact, 출력 재추출 5/5 PNG pixel-exact, 대상 payload 밖 diff 0, 두 번 연속 동일 G1T 해시.
- 다음 Citra에서는 시작 메뉴의 `데이터를 불러온다 / 새로운 게임을 시작 / 갤러리를 감상한다 / 무장을 편집한다 / 튜토리얼을 본다` 이미지가 정상 방향·색상·투명도·배치로 표시되는지만 우선 확인한다. 정적 PASS만으로 런타임 성공을 확정하지 않는다.

## 16. 현재 이미지 전용 revision — v34

- 현재 최종 revision은 v34다. v33 Patch G1T를 입력으로 사용했으며 Original 기반 재생성은 하지 않았다.
- 이번에 갱신한 항목은 `start_menu_down_005/006/007/008/031/032/033.png`에 대응하는 7개 payload뿐이다. 비지정 texture 004와 나머지 전 항목은 v33 그대로다.
- G1T header·크기와 지정 payload 밖 모든 바이트를 보존했다. 7개 재추출 이미지는 입력 PNG와 pixel-exact이고, 비대상 Patch 파일 변경은 0이다.
- 후속 이미지 revision도 반드시 **현재 최종 Patch G1T를 기반으로** 하고, 사용자가 명시한 PNG index만 교체한다.
- Citra 확인 대상: `새로운 게임을 시작 / 갤러리를 감상한다 / 무장을 편집한다 / 튜토리얼을 본다 / 오리지널 모드 / 3DS 모드 / 챌린지 시나리오`의 방향·색상·투명도·배치.

## 17. v35 인사 도움말·폰트 보정

- v35는 v34 이미지를 보존하고 인사 도움말 10쪽을 `msgsec15.dat` fixed-position으로 적용했다. 신규 글리프 `닌/응/종`과 physical 1111의 `빠` 5픽셀 보정만 추가했다.
- 빌더: `tools\build_sangokushi2_v35_personnel_help_and_fast_glyph.py`; report: `analysis\v35_personnel_help_and_fast_glyph_report.json`.

## 18. 실패 기록 — v36 신무장 화면 문자열 위치 오판

- v36에서 고친 `msgsec01.dat`의 `msgsec01_0006_006/008`은 하단 버튼 캡션이 아니라, 선택 후 상단에 나오는 설명문이었다. 하단 버튼 글자는 이미지이며 v36에서 수정되지 않았다.
- v35 대비 변경 파일은 `RomFS\Message\msgsec01.dat` 하나이며 SHA-256은 `F744420225FDE48937532E09E8495F08396ECC505C78A118EA0A27614ADE1C84`이다. 나머지 Patch 25개 파일은 byte-exact다.
- 정적 프리뷰와 readback은 문자열이 존재하고 렌더 가능하다는 사실만 증명한다. 화면에서의 실제 역할은 Citra 관찰이 우선한다.

## 19. v40 revision — 신무장 화면·정보 설명·`와` 보정

### v37 상단 설명 정정 기록

- `msgsec01_0006_006/008`을 각각 `신무장을 등록합니다 / 사실무장을 편집합니다`로 정정했다.
- v36 대비 변경 파일은 `RomFS\Message\msgsec01.dat` 하나뿐이며 SHA-256은 `0968180CBA6397DF625DB227C95F9299873AE64E81C27B156FAFAE5E83DCF676`이다. 이미지 변경은 없다.
- 상세 구조 검증: `analysis\v37_new_officer_description_correction_report.json`. 빌더: `tools\build_sangokushi2_v37_new_officer_description_correction.py`.

### v38 외교 도움말 / `맞` 잔상

- 실제 Patch가 문서와 달리 v36 `msgsec01.dat` 해시로 남아 있었으므로 과거 이미지 빌드 체인을 재실행하지 않고 해당 parent만 v37 해시 `0968180C...DCF676`으로 정상화했다.
- 외교 버튼 설명 총 12페이지를 실제 `msgsec15` 경계대로 한글화했다: `동맹 4 / 공동작전 1 / 정전 1 / 교환 1 / 원조 3 / 항복권고 1 / 동맹파기 1`. 바로 뒤 외교 조언 2페이지도 추가 번역했다.
- `맞` physical 1280 왼쪽 잔상은 physical 1279 우측의 반투명 6픽셀 bleed였다. `맞` 글리프 자체는 건드리지 않고 해당 픽셀만 제거했다.
- v38 핵심 SHA-256: `font.g1t=3EE538752C4A837CDB5EE8AA70EE4392BE04D3D53293C132FA8DD78F98BAEC29`, `msgsec01.dat=0968180CBA6397DF625DB227C95F9299873AE64E81C27B156FAFAE5E83DCF676`, `msgsec15.dat=712F39B36E980305D36994277D8283B75410F367CC521F2B6DE4ED8C26CEE877`.
- 빌더: `tools\build_sangokushi2_v38_diplomacy_help_and_mat_bleed.py`; report: `analysis\v38_diplomacy_help_and_mat_bleed_report.json`.

### v39 지정 이미지 5장

- 반드시 최종 v38 Patch를 입력으로 사용한다. `StartMenu/start_menu_down.g1t`는 Patch 컨테이너 위에서 006/029/030만 교체했고, `EditBushou/edit_bushou_base_menu_down.g1t`는 Patch에 기존 컨테이너가 없어서 immutable Original을 유효 베이스로 삼아 002/003만 교체했다.
- 지정된 5개 PNG만 읽었고, 각 G1T의 target payload 밖 byte diff는 0이다. 5/5 PNG pixel-exact readback을 통과했다. 텍스트/폰트/code는 v38 해시 그대로다.
- 최종 v39 SHA-256: `start_menu_down.g1t=1DDF65F68F9E6F2156F913009B1E0C4BA67F344A44E4F470CA879ED7B0B057D4`, `edit_bushou_base_menu_down.g1t=3D3BF50E3D8D596D8B4104CCC3B81751B72073F309D151C2073DA57C916F6046`.
- v39 빌더는 두 번 연속 실행해 동일 해시를 확인했다. 빌더: `tools\build_sangokushi2_v39_requested_images.py`; report: `analysis\v39_requested_images_report.json`.
- 하단 두 버튼 이미지 소스는 이후 `EditBushou\edit_bushou_base_menu_down`에서 확인했고, v39에서 사용자가 지정한 002/003을 실제 Patch에 반영했다.

### v40 신무장 화면 / 정보 설명 / `와`

- v37의 `msgsec01_0006` separator 1B 차용은 Citra에서 제목 선두 글자 유실 및 `C9` 노출을 일으킨 실패 방식으로 재분류했다. v40에서는 원래 `05 05 05` separator 14개를 모두 복원하고 separator borrowing을 사용하지 않는다.
- 제목은 원본과 동일한 `ESC C9 ... ESC C1` 구조로 `신무장 등록 / 사실무장 편집`, 본문은 `신무장을 등록합니다 / 사실무장을 편집합니다`로 넣었다. 늘어난 12B는 같은 parent의 `편집 내용을\n초기화합니다` 및 `무장명 변경 불가`로 확보하며 parent 392B와 parent 밖 바이트를 유지한다.
- 정보 도움말은 `밀정/무장/타국/속령일람/전장일람/세력지도` 각 1/1을 실제 `msgsec15_0185_003~0187_002`에 fixed-position으로 한글화했다. `특산 1/1`은 기존 정상 번역을 유지한다.
- `와`는 physical 717의 historical `v11_wa_crop`을 수정했다. 왼쪽 본문 x<9는 그대로 두고 Sam3 원본 오른쪽 모음 x=10~14만 1px 왼쪽으로 옮겨, 과거 잘려 나가던 source x=14를 game x=13에 보존한다. v39 대비 font 변경 셀은 physical 717 하나뿐이다.
- 사용자는 `라` 옆 미세 잔상을 에뮬레이터 표시 문제로 확인했다. v40은 `라`와 주변 셀을 수정하지 않는다.
- 첫 preflight 실패로 생긴 부분 쓰기는 실제 테스트용 `Sangokushi 2 Rebuild`의 핵심 파일이 기록된 v39 해시와 모두 일치함을 검증한 뒤, 해당 파일들을 읽기 전용 복구 기준으로 사용해 v39로 되돌렸다. Rebuild 자체는 수정하지 않았다. 이후 v40 빌더를 두 번 연속 실행해 동일 해시를 확인했다.
- v40 SHA-256: `font.g1t=75F7D93C53BC3062ECE74940AF61E7037BF4F9C4BFB8BA1F27E9CBB5BF99CADD`, `msgsec01.dat=8312685E669485292FF2C88FE2878FE3CF14CD355BB6DC55CA49C0B8AF164C97`, `msgsec15.dat=3CF0890E81C13C3AA32D8958C87257D2B854EF6286F08B6C939BEE36C39C0EB9`.
- 빌더: `tools\build_sangokushi2_v40_new_officer_info_wa.py`; report: `analysis\v40_new_officer_info_wa_report.json`.

## 20. 현재 revision — v41 Tutorial 이미지 6장

- v41은 **이미지 전용 revision**이다. v40의 `font.g1t`, `code.bin`, `msgsec01.dat`, `msgsec10.dat`, `msgsec15.dat`와 v39의 StartMenu/EditBushou 이미지 컨테이너를 byte-exact로 유지한다.
- 적용 대상은 사용자가 명시한 `Extracted_Image\RomFS\Tutorial\Tutorial\Tutorial_002.png`, `003`, `004`, `005`, `014`와 `Extracted_Image\RomFS\Tutorial\Tutorial_sub\Tutorial_sub_001.png`뿐이다.
- 대응 G1T는 `RomFS\Tutorial\Tutorial.g1t` texture `2/3/4/5/14`와 `RomFS\Tutorial\Tutorial_sub.g1t` texture `1`이다. 모두 `0x09 RGBA8`이며 6/6 pixel-exact readback을 통과했다.
- v40 Patch에는 두 Tutorial G1T가 존재하지 않았으므로 이 두 컨테이너만 immutable Original을 유효 베이스로 사용했다. Original은 읽기 전용이며 수정하지 않았다. 후속 이미지 revision은 이제 **v41 Patch의 Tutorial G1T를 기반으로** 지정 payload만 교체해야 한다.
- G1T header 및 비지정 texture payload 변경은 0이며, 비대상 Patch 파일도 전부 byte-exact다. 두 번 연속 빌드에서 동일 G1T/report 해시를 확인했다.
- SHA-256: `Tutorial.g1t=0319358DB04F9BDE63A33FD8DC13B58D2EE7B2E16D6A9480045503CE287BE4F9`, `Tutorial_sub.g1t=4F3D9813E6B17D146744156376F555B2D9667D0CDE21596D8091F5AEB2CD466E`.
- 빌더: `tools\build_sangokushi2_v41_tutorial_images.py`; report: `analysis\v41_tutorial_images_report.json`.

## 21. 현재 revision — v42 신무장 화면 고정 슬롯 중간 시험

- v42는 사용자 Citra에서 확인된 `刺ら장 등록 / ら장을 등록합니다` 형태의 깨짐을 수정하기 위한 **중간 확인용 text-only revision**이다.
- 원인은 v40이 `msgsec01_0006` parent 전체 크기는 유지했지만 fragment 4/9의 용량을 다른 fragment에 재분배하여 parent 내부 시작 위치를 움직인 점으로 판단한다. 이 화면은 fragment 시작 위치를 고정 참조하는 런타임 정황이 강하므로 v42에서는 내부 재배치를 폐기한다.
- `msgsec01_0006` parent `0x472~0x5FA`를 immutable Original의 pre-v36 byte layout으로 복구한 뒤, 오직 4개 표시 문구만 삽입한다: `신무장등록 / 신무장 등록하기 / 사실무장편집 / 사실무장 편집하기`.
- 제목 `msgsec01_0006_005/007`은 `ESC C9 ... ESC C1`을 포함해 원본 16B/18B 슬롯에 exact-size로 들어간다. 본문 `0006_006/008`은 각각 원본보다 1B 길어 바로 뒤 `05 05 05`의 첫 바이트만 사용하고 `05 05`를 남긴다. 따라서 다음 fragment의 절대 시작 위치와 `msgsec01.dat` 전체 9,057B는 유지된다.
- v40에서 용량 확보를 위해 줄였던 fragment 4/9는 pre-v36 원문 바이트로 복구한다. 이는 새로운 번역 확장이 아니라 깨짐을 만든 parent 내부 재배치를 제거하기 위한 구조 복구다.
- v41 Tutorial 이미지 6장, StartMenu/EditBushou 이미지, `font.g1t`, `code.bin`, `msgsec10.dat`, `msgsec15.dat` 및 나머지 Patch 파일은 byte-exact 유지한다. `Sangokushi 2 Original`, `Sangokushi 2 Rebuild`, `Backup`은 수정하지 않는다.
- v42 `msgsec01.dat` SHA-256: `A2AC5281AD8E5FFD1E73DF8280DB32C93709A0A70FA3B92C1162436BB0937A72`.
- 빌더: `tools\build_sangokushi2_v42_new_officer_fixed_slots.py`; report: `analysis\v42_new_officer_fixed_slots_report.json`. 성공 확인 전에는 번역 HTML/JSON을 v42 문구로 동기화하지 않는다.

## 22. 현재 revision — v43 튜토리얼 문구 / alias 충돌 복구 중간 시험

- v43은 사용자 Citra에서 확인된 `지괴/탑/떨어측/닌으면/응니다/종심/닌일/례는` 등의 오표시와 함정·노/강노 설명 문구를 함께 정리하는 **중간 확인용 revision**이다.
- 근본 원인은 v31→v32의 alias 관리 회귀다. v32가 `붙/탑/핵/끄/처/럼/측/괴/롭`의 alias를 바꾸고도 이전 v31 `msgsec15` 페이지를 재인코딩하지 않았고, v31 신규 `glyph_rows`를 donor map에 완전히 병합하지 않아 일부 alias를 다른 글자에 재사용했다.
- v42 map에서 확인된 historical duplicate alias 10쌍: `렴/빈`, `찾/춰`, `몽/붙`, `탑/팔`, `골/끄`, `처/칫`, `꺾/럼`, `질/측`, `괴/급`, `돼/롭`. 이번 요청 문장에서 사용되는 충돌 글자는 모두 fresh alias로 분리했다. 요청 범위 밖 `렴/빈`, `찾/춰`, `돼/롭` 3쌍은 legacy debt로 기록하고 향후 해당 글자가 수정문에 등장하면 반드시 같은 절차로 분리한다.
- fresh alias 11자: `급=9666`, `측=9667`, `골=966A`, `칫=9675`, `꺾=967E`, `희=969A`, `붙=969B`, `탑=96A4`, `끄=96A9`, `처=96AE`, `럼=96B7`. physical은 각각 `3128/3129/3132/3143/3152/3179/3180/3189/3194/3199/3208`이며 packed font readback 11/11 pixel-exact다.
- donor는 Original live-source alias뿐 아니라 **과거 모든 revision report의 `alias/old_alias/new_alias` 이력 전체**를 영구 예약한다. 기존/신규 Korean physical, known bleed guard, ±73 수직 인접 셀도 제외한다. 이를 `tools\sangokushi2_alias_guard.py`와 v43 빌더가 강제한다.
- `msgsec15.dat`는 20,373B fixed-position/no-repack을 유지하면서 12개 런타임 그룹을 최종 collision-free map으로 재인코딩했다: 포상 2/2, 함정 수, 함정 골칫거리, 함정 대책 2, 노·강노 활용 1/2, 수상전 기본 common/3DS, 수상전 화시 common/3DS, 적 계략 주의 common/3DS.
- `함정 수`는 `설치 가능한`을 사용한다. writable 147B / 실제 124B이며 fallback `쓸 수 있는`은 사용하지 않았다. 모든 줄은 20 cell 이하, 가장 빡빡한 `노·강노 활용 2`도 158B 중 155B로 3B 여유가 있다.
- v43 변경 파일은 `RomFS/Common/Font/font.g1t`와 `RomFS/Message/msgsec15.dat` 두 개뿐이다. v42 `msgsec01`, `code.bin`, `msgsec10`, v41 Tutorial 이미지와 기존 StartMenu/EditBushou는 byte-exact로 유지한다.
- SHA-256: `font.g1t=EA5D622FFD664F49EE17BB84D04026F6C1097D06D2D146C3C819B92C915A3A4E`, `msgsec15.dat=E62DECCE95F15E362E371C457BFC5E8718581E9436220528082A4AA112D8EBE9`.
- 빌더: `tools\build_sangokushi2_v43_tutorial_text_alias_repair.py`; alias gate: `tools\sangokushi2_alias_guard.py`; report: `analysis\v43_tutorial_text_alias_repair_report.json`. 두 번 연속 동일 결과를 확인했다.
- Citra 성공 확인 전에는 `text_review.html`/`sangokushi2_translation.json`을 이번 런타임 문구로 동기화하지 않는다.

## 23. 현재 revision — v44 병사고용·위서의심·태수변경·임명 중간 시험

- v44는 v43 전체 결과를 보존하고 `font.g1t`와 `msgsec15.dat` 두 파일만 변경한다. 대상은 `병사고용 2/3`, `위서의심 1/4`, `태수변경 6/9`, `임명 2/2`다.
- `렴`은 `빈`과 alias `94C8`을 공유해 `저빈하지만`으로 출력되던 legacy collision이다. v44에서 `렴=96C7` / physical 3224로 fresh-safe 분리했다. 새 태수 문장의 `됨`은 기존 map에 없어서 `96CB` / physical 3228로 추가했다. 두 새 셀 모두 packed-font pixel-exact readback을 통과했다.
- `맞찾`은 의도한 `맞춰`의 `춰`가 `찾`과 alias `94CC`를 공유한 결과다. 사용자가 원하는 문구가 `자금 사정에 맞게`이므로 이번 페이지에서는 `춰/찾` collision을 참조하지 않는다. alias gate는 향후 두 글자 중 하나가 수정문에 등장하면 fresh alias 분리를 강제한다.
- 병사고용 2/3은 `징병은 저렴하지만... / 자금 사정에 맞게 선택하십시오.`로 재인코딩했다. 116B/121B, 4줄, 최대 19칸이다.
- 위서의심 1/4은 기존 line 단위 삽입이 fragment 경계에 1B padding을 남겨 `편 지`가 된 문제다. `msgsec15_0196_002 + 0197_000`을 한 fixed group으로 다시 써 `인접국 무장에게 거짓 편지를 보내`로 붙였다. 145B/174B다.
- 태수변경 6/9은 `다른 무장이 문·무관뿐이면`을 `남은 무장이 문·무관뿐이면`으로 수정했다. 148B/189B다.
- 임명 2/2는 byte 부족이 아니라 **9줄 target 중 Citra가 8줄까지만 표시하는 세로 높이 문제**로 판정했다. `↑` 기호 축약은 사용하지 않고 사용자 2안을 적용해 8줄로 줄였다: 무관은 `「개발」「외교」 불가`를 같은 줄에 합치고, 문관은 `정치 높은 무장에 적합`으로 축약했다. 마지막은 `태수: 문관·무관을 태수 임명 시,` / `도시 위임 상태가 됨`. 최종 253B/314B, 61B 여유, 최대 19칸이다.
- 네 페이지 모두 fixed-position/no-repack, 20칸 이하, target alias collision 0건이다. v43의 12개 수정 페이지와 v42 `msgsec01`, v41 Tutorial 이미지, code/msg10/기존 이미지 파일은 byte-exact로 유지한다.
- 빌더: `tools\build_sangokushi2_v44_help_cleanup.py`; report: `analysis\v44_help_cleanup_report.json`. 첫 실행 후 fresh alias 재할당으로 재실행 해시가 달라지는 빌더 문제를 발견해 수정했고, 최종 빌더는 기존 v44 report의 fresh alias를 재사용한다. 그 뒤 두 번 연속 **font/msgsec15/report 해시까지 동일**함을 확인했다.
- SHA-256: `font.g1t=23E187F43E10449B6396D469F06D804E6ECBFD35001FAAC8A9CD8A728655CAC0`, `msgsec15.dat=88FA7897CEB164799A06586CCE2587DFCCA74FBEEB132931F7BE79BE26A50FBB`.
- Citra 성공 확인 전에는 HTML/JSON 권위 번역을 v44 문구로 동기화하지 않는다.

## 24. 현재 revision — v45 튜토리얼 번역 완료·안정 기준

- v45는 v44 전체 결과를 보존하고 `font.g1t`와 `msgsec15.dat` 두 파일만 변경한다. 대상은 `적대심낮추기 3/3`, `공동작전 1/1`, 계략 2개, 개발 4개, 특별 4개 상세 설명으로 총 12개 fixed-position 그룹이다.
- `낮찾야`는 `찾/춰 -> 94CC` legacy collision이 원인이다. `춰`를 fresh safe alias `96B8` / physical 3209로 분리하고 해당 외교 조언 페이지를 현재 map으로 재인코딩했다. 새 셀은 packed-font pixel-exact readback을 통과했다.
- `지괴합니다`는 공동작전 페이지가 v43 이전 `급/괴` stale alias 바이트를 유지한 문제다. 신규 glyph 없이 v43의 safe `급` mapping으로 페이지 전체를 다시 인코딩했다.
- 런타임 소스 조사 결과 짧은 `msgsec10` 설명 10개는 이미 Patch/Rebuild에서 한글 바이트이고 동일 해시였다. 사용자가 일본어로 확인한 설명은 별도의 `msgsec15` command-detail page로 판정해 그 10개만 한글화했다: `이호경식/구호탄랑`, `토지개발/경작/치수/상업투자`, `방랑/치료/임시징수/세율변경`.
- 상세 설명은 기존 `translation_ko` 의미를 기준으로 원본 슬롯에 맞게 압축했다. 용량은 토지개발 83/89B, 경작 120/123B, 치수 92/107B, 상업투자 81/87B, 이호경식 115/126B, 구호탄랑 128/138B, 방랑 83/89B, 치료 67/73B, 임시징수 50/59B, 세율변경 32/37B다.
- 12개 그룹 모두 fixed-position/no-repack, 최대 line width 20칸 이하, target alias collision 0건이다. `msgsec10.dat`, `msgsec01.dat`, `code.bin`, StartMenu/EditBushou, v41 Tutorial 이미지는 v44와 byte-exact로 유지한다.
- 빌더: `tools\build_sangokushi2_v45_menu_help_alias_fix.py`; report: `analysis\v45_menu_help_alias_fix_report.json`. 두 번 연속 실행에서 font/msgsec15/report 해시가 동일하게 재생성됐다.
- SHA-256: `font.g1t=AE40742C557167705018DD66C673D35795B225A82B6A056CB58DE7580013BE09`, `msgsec15.dat=E4120D36FF2D73C572B56548CD901BC6E9BD2C44C7FA0AEB49BF81A265777B91`.
- 2026-08-29 사용자 Citra 확인에서 당시 확인 범위는 정상으로 승인됐으나, 후속 확인에서 미번역 9페이지와 단독 마침표 문제가 발견되어 **v45 완료 판정은 v46에서 해제**됐다.
- 후속 작업자는 표준 절차 `최신 Patch 확인 → 실제 런타임 소스 추적 → fixed-position/no-repack → 20칸/byte budget 검사 → alias history/collision 검사 → 필요한 글자만 fresh safe alias → packed-font readback → 비대상 byte-exact 보존 → 빌더 2회 동일 해시 → Citra 승인`을 유지한다.

## 25. 현재 revision — v46 잔여 튜토리얼 9페이지·단독 마침표 정리

- v46은 v45 전체 상태를 베이스로 `font.g1t`와 `msgsec15.dat`만 변경한다. `msgsec01.dat`, `msgsec10.dat`, `code.bin`, 기존 이미지 컨테이너는 v45와 byte-exact다.
- 미번역 detail page 9개를 fixed-position으로 한글화했다: 치수 2/4~4/4, 상업투자 2/4~4/4, 세율변경 2/4~4/4.
- 종결 `.` 하나만 다음 시각 줄로 밀리는 경우에 한해 마침표 1B만 제거하는 규칙을 확정했다. 사용자 확인 대상 `게임 설명 대륙통일`, `적중작적 1/2 배신합니다`와 동일 활성 패턴인 `함정 기본`, `노·강노 활용 2`까지 총 4곳을 정리했다. 전역 문장부호 삭제는 금지한다.
- 신규 safe glyph는 `잊=96C1/physical3218`, `밭=975B/3305`, `친=975F/3309`, `꿀=976D/3323`이며 packed-font pixel-exact readback을 통과했다.
- 13개 그룹 모두 fixed-position/no-repack, 최대 line width 20칸 이하, target alias collision 0건이다. 세율변경 2/4는 88/88B, 세율변경 4/4는 116/117B로 특히 Citra 확인이 필요하다.
- 빌더: `tools\\build_sangokushi2_v46_tutorial_remaining_pages_punctuation.py`; report: `analysis\\v46_tutorial_remaining_pages_punctuation_report.json`. 두 번 연속 실행에서 font/msgsec15/report 해시가 동일하게 재생성됐다.
- SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`, `msgsec15.dat=5F5578B4996733BD1225EC957D0932808A21146BDB4AC68591CA3EA4102B60F8`.
- v46은 **Citra 중간 확인 전용**이다. 확인 전에는 튜토리얼 완료 판정을 다시 내리거나 HTML/JSON 권위 번역을 동기화하지 않는다.

## 26. 현재 revision — v47 punctuation-only 바이트 복구

- v46에서 `.`만 제거하라는 요청을 generic `patch_fixed_group`으로 처리하면서 전체 fragment를 다시 인코딩한 것이 회귀 원인이다. `msgsec15_0026`은 `대륙 통일` 문장 끝뿐 아니라 다음 문장 시작도 같은 fragment에 포함되어 있어, 첫 문장만 target으로 재작성하면서 인접 본문/padding까지 덮어썼다.
- v47은 v46의 잔여 번역 9페이지와 `font.g1t`를 유지하고 `msgsec15.dat`만 변경한다.
- 게임 설명은 v23 `GAME_BODY_SLOTS` 앞 2 slot의 승인된 per-slot 방식으로 복원한다. 적중작적 1/2은 v30 target, 함정 기본은 v31 target, 노·강노 활용2는 v43 target으로 원래 배치를 복원한다. v46에서 사용자 지정 없이 선제적으로 제거했던 함정 기본/노·강노 활용2 마침표는 원복한다.
- 복원 후 punctuation 수정은 정확히 3개뿐이다: `통일합니다.` / `배신합니다.` / `오르지 않을 수 있습니다.`의 `0x2E`를 같은 위치 `0x20`으로 치환한다. 파일 길이, fragment start, linefeed, `0x05`, padding을 이동하지 않는다.
- **재발방지 필수 규칙:** punctuation-only 요청에는 전체 문자열/group 재인코딩 금지. 정확한 punctuation byte만 제자리 치환하고, 구조 복원 snapshot 대비 허용 offset 외 diff가 1바이트라도 있으면 실패시킨다. 비슷해 보인다는 이유로 사용자 미지정 문장부호를 선제 수정하지 않는다.
- v46의 나머지 신규 detail 8페이지는 fragment별 byte-exact v46 보존을 확인한다. 치수 3/4만 승인된 마침표 1B 차이가 있다.
- 빌더: `tools\\build_sangokushi2_v47_punctuation_byte_only_repair.py`; report: `analysis\\v47_punctuation_byte_only_repair_report.json`. 두 번 연속 동일 해시를 확인했다.
- SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`(v46 동일), `msgsec15.dat=74A592B7794F94B3733CB00FF26E80FCE487329019B32D62ADA8C9F64E8F93E6`.
- v47은 **Citra 중간 확인 전용**이다. 이번 확인에서는 게임 설명 줄배치 복원 + 세 곳의 단독 마침표 제거만 우선 판정한다.

## 27. 현재 revision — v48 게임 설명 정확 복원

- v47 Citra 결과에서 `도시를지배해`, `군주 사망` 누락이 남아 있음을 확인했다. v47은 v46의 broad-group 문제는 맞게 잡았지만 **정상 화면의 레이아웃 권위 자체를 잘못 선택**했다.
- 사용자 제공 정상 스크린샷과 과거 코드 이력을 대조한 결과, 정상 게임 설명은 v23의 앞 2 per-slot 문자열이 아니라 v25 `GAME_LINES` 6줄을 `v23.patch_lines_group()`으로 전체 `GAME_IDS`에 token-flow한 화면이다.
- 이 구조에서는 `msgsec15_0025_002`가 `４６개 도시를`에서 정확히 끝난 뒤 다음 fragment `msgsec15_0026`의 첫 byte에 공백 토큰이 들어가 `도시를 지배해`가 된다. 또한 `msgsec15_0026`의 남는 writable bytes는 다음 화면 문장 `군주 사망...` 시작에도 사용된다. 따라서 source fragment 이름만 보고 `0026=첫 문장 전용`이라고 간주하면 안 된다.
- v48은 게임 설명 전체 `GAME_IDS`를 v25 승인 라인/동일 writer로 복원하고, 그 복원 snapshot에서 `통일합니다.`의 `.` 한 바이트만 `0x20`으로 바꾼다. 적중작적/치수 마침표 수정 및 v46 잔여 번역 9페이지는 v47 상태를 그대로 유지한다.
- 강제 gate: `msgsec15_0026` 첫 byte=`0x20`, 게임 설명 block 내 `군주 사망` 정확히 1회, 승인 복원 snapshot 대비 최종 diff는 정확히 1바이트 `0x2E->0x20`, v47 대비 변경은 게임 설명 source fragment 범위 내부만 허용, non-msg15 파일 byte-exact.
- 예상 화면: `４６개 도시를 지배해 대륙을 통일합니다` / `군주 사망하면 후계자가 될 무장이 없으` / `면 게임 오버가 됩니다.` / 빈 줄 / `아래에서 게임 설명 버튼을 누르시면` / `위 화면에 설명이 표시 됩니다.`
- 빌더: `tools\\build_sangokushi2_v48_game_explanation_exact_restore.py`; report: `analysis\\v48_game_explanation_exact_restore_report.json`. 두 번 연속 동일 해시를 확인했다.
- SHA-256: `font.g1t=E15CCDA300CCD2B5786539B9418B30D223D810656750211119945BF0DFF611BD`(v47 동일), `msgsec15.dat=D59BACD2A59EEEBD49A04235D0274C3B2B9365DBC9A3D3D365F823B8AD1282B2`.
- **재발방지:** screen-approved layout을 source fragment/slot 경계와 동일시하지 않는다. shared fragment가 있으면 실제 정상 화면을 만든 whole-block writer를 권위로 사용하고, punctuation-only는 승인 baseline snapshot 이후 byte-local edit만 수행한다.
- v48은 **Citra 중간 확인 전용**이다. 우선 게임 설명 첫 페이지를 사용자 제공 정상 스크린샷과 직접 비교한다.

## 28. 현재 revision — v49 게임 오버 padding + StartMenu/Tutorial_sub 이미지

- v48 Citra에서 게임 설명의 문장/줄배치는 정상으로 복구됐다. 남은 `게임 오 버` 간격은 v25 whole-block 배치가 `msgsec15_0029` 끝에 남긴 1B padding이 `오` 뒤에 보이는 과거 v30 동일 이슈다.
- v49는 과거 검증된 `v30.fix_game_over_padding()`을 그대로 재사용한다. `msgsec15_0029`의 끝 `오 + 0x20`에서 spare 1B를 같은 fragment의 이전 `0x0A` 직전으로 옮긴다. fragment 시작/길이는 그대로이며 `오`와 `버`의 2B alias를 fragment 경계에 걸치지 않는다.
- 이미지 대상은 정확히 18개: `StartMenu/start_menu_up` 001~015,017,018 및 `Tutorial/Tutorial_sub` 001. 명시되지 않은 texture는 수정하지 않는다.
- v48 Patch에는 `start_menu_up.g1t`가 없으므로 해당 컨테이너만 immutable Original을 effective prior-Patch base로 최초 생성했다. `Tutorial_sub.g1t`는 v48 Patch 컨테이너를 직접 베이스로 texture 1 payload만 교체했다.
- 두 컨테이너 모두 대상 RGBA8 payload의 codec round-trip을 통과했고, header 및 비대상 payload는 immutable Original과 byte-exact, 대상 18개는 재추출 pixel-exact readback을 통과했다.
- v49 변경 범위는 `msgsec15.dat`, `start_menu_up.g1t`, `Tutorial_sub.g1t`뿐이다. font/code/msg01/msg10/start_menu_down/edit_bushou/Tutorial.g1t는 v48과 byte-exact다.
- 빌더: `tools\\build_sangokushi2_v49_game_over_and_images.py`; report: `analysis\\v49_game_over_and_images_report.json`. 두 번 연속 동일 해시를 확인했다.
- SHA-256: `msgsec15.dat=02688DC0513FA5EF2B627258EED610160F6FB37FA5E1439D5128D77C822DD39F`, `start_menu_up.g1t=0B80514DDB0BA620D6584945E7E322BD2AFD504774071C00326991826DA1E84C`, `Tutorial_sub.g1t=B2688A4303BB556737F521768748B4A9E59F61D0F5F1757764712D1752DF48E4`.
- v49는 **Citra 중간 확인 전용**이다. 우선 `게임 오버` 간격, StartMenu 상단 이미지 17개, `Tutorial_sub_001`을 확인한다.

## 29. 진행 중 작업 — 이름/대사 한글화 checkpoint 20 (정식 revision 아님)

- 권위 Patch는 계속 v49이며 현재 전체 Patch manifest는 작업 시작 checkpoint 0과 동일하다. 이 작업이 전부 끝나기 전에는 Patch나 정식 revision을 갱신하지 않는다.
- 전 인물 이름 1,020건은 별도 staging에 적용 완료했다. `fix_data.bin`은 44바이트 레코드 1,020개, 표시 이름 `+18/9B`, 가나 독음 `+27/17B` 구조다.
- `何進 || カシン`은 `하진 || カシン`처럼 표시 이름만 번역한다. 실제 결과에서도 가나 독음 1,020건과 이름 외 모든 바이트가 원본과 동일하다.
- staging 결과: `analysis\dialogue_translation_work\checkpoint_20_names`. `font.g1t=5051807A...A941C1`, `fix_data.bin=595B8449...ED021`; 두 번 결정적 재실행, alias 비공유, 1,020건 이름 readback, 69개 신규 glyph pixel-exact를 모두 통과했다.
- 다음 작업은 HTML/JSON의 기존 번역을 권위로 삼아 실제 v49 런타임에서 일본어로 남은 시나리오 대사와 인물 간 대사만 inventory한 뒤 staging에 순차 적용하는 것이다. 튜토리얼·메뉴·설명 텍스트는 이번 범위 밖이다.

## 30. 현재 정식 revision — v50 전 인물 이름 한글화

- 사용자 승인으로 checkpoint 20 이름 완료본을 v50 Patch에 발행했다. 기준은 v49 전체 manifest이며 변경 파일은 `RomFS\Common\Font\font.g1t`, `RomFS\Scenario\fix_data.bin`뿐이다.
- 1,020개 44바이트 레코드의 `+18/9B` 표시 이름만 한글화했다. `+27/17B` 가나 독음은 1,020건 모두 byte-exact이며 이름 칸 밖 `fix_data.bin` 차이는 0건이다.
- 앞 770명은 NDS 한국어판 동순서 이름표, 뒤 206명은 검토 대응표, 더미 44건은 `더미`를 사용했다. 1,020건 전부 alias 역변환 일치한다.
- v50 font는 한글 319자를 포함하며 v49 대비 fresh safe glyph 69자를 추가했다. target alias collision=0, packed glyph readback pixel-exact다.
- 빌더 `tools\build_sangokushi2_v50_character_names.py`, report `analysis\v50_character_names_report.json`; 두 번 연속 결정적 재실행을 통과했다.
- SHA-256: `font.g1t=5051807A...A941C1`, `fix_data.bin=595B8449...ED021`, report=`6EF5FEB9...BAD9D`.
- v50 전체 Patch와 이름 자료는 `analysis\dialogue_translation_work\checkpoint_20_v50_published`에 기준점으로 봉인했다. 다음 대사 작업은 v50을 변경하지 않고 staging에 누적한다.

## 31. v51-intermediate — 이름 alias + msgsec07 포인터 회귀 수정 (Citra 확인 완료)

- 사용자 Citra 화면에서 v50 `동탁`의 둘째 글자가 점/쉼표처럼 깨지고, 인사→수색 prompt 대신 다른 문장 `노력이 될 듯합니다...`가 표시되는 회귀가 확인됐다.
- `0x98xx` 전체 금지 가설은 폐기했다. Original 이름에도 0x98 이상 pair가 다수 존재한다. 실제 이름 allocator 누락으로 v35에서 이미 존재한 `종=94FA`, `응=94F8`을 v50이 다시 `9842/98A5`로 배정한 사실을 확인했고 관련 이름 19건을 재인코딩했다.
- `탁=98A4`는 Citra 확정 실패이므로 runtime ref 0인 `8B45 / physical1027`로 최소 이동했다. packed glyph readback과 ±73 guard를 통과했다.
- `msgsec07.dat`는 word0=388, word1~387=absolute byte pointer 구조다. v20/v21 repacker가 앞 177개를 word-offset segment pointer로 잘못 고친 것이 수색 오문장 원인이었다. v51은 387개 pointer를 현재 body fragment 위치에 맞게 복구하고 16B source slot에 `수색할 무장?`을 넣었다.
- **사용자 Citra 확인 완료:** `동탁` 정상, `수색할 무장?` 정상. 따라서 이름 alias 수정과 header[153]=`0x13DA`는 런타임 검증 성공으로 취급한다.
- SHA-256: `font.g1t=6C7C13453E61EAFB03094F6BFBF0C840E1822A19CAD1A28CBF3388193A87ECCC`, `fix_data.bin=126279344D75454A7617EDE997ED8FE75B1D35B652B8E32CF7AC5DA4F75FA72E`, `msgsec07.dat=78A29470ADA09DE20E4A90D4D4B5CF48A8D2BF6D8AB7DBD8A95923F9780E2FB2`.

## 32. v52 자연 수색 prompt append-only — Citra 성공 확인

- 권위 번역 `누구에게 수색을 시키겠습니까?<$05><$05>`는 31바이트/15칸이라 화면 폭에는 맞지만 기존 16B source slot에는 들어가지 않는다.
- v51 Citra가 header[153]이 이 prompt를 직접 가리킨다는 것을 입증했으므로, v52는 기존 body 재패킹을 하지 않는다. v51 파일 EOF `0x28D0`에 전체 31바이트를 append하고 **header[153] 두 바이트만 `0x13DA -> 0x28D0`**으로 변경한다.
- 기존 10,448바이트 영역에서 허용 diff는 header[153] 2바이트뿐이다. 다른 386개 pointer, 기존 compact prompt, 기존 body는 byte-exact. 새 파일 크기 10,479B.
- 빌더 `tools\build_sangokushi2_v52_search_full_prompt.py`; report `analysis\v52_search_full_prompt_report.json`; 두 번 연속 결정적 실행 동일 해시 확인.
- SHA-256: `msgsec07.dat=E9C2A26162ADA768C54E8B45940BBD1B43B5D1F9329C408C68076C32697DD372`, report=`A6F1AB443F498EEF16AFFA0D9C5E387690423A4813FE5503B7DD43F9D3A111BF`.
- **사용자 Citra 확인 성공:** `누구에게 수색을 시키겠습니까?`가 수색 화면 상단에 정확히 한 줄로 정상 출력됐다. 따라서 `msgsec07`에서 단일 runtime pointer의 소유가 먼저 입증된 짧은 문구를 확장해야 할 때는 **기존 body 재패킹 대신 EOF append + 해당 pointer 1개 전환**을 우선하는 것을 검증된 방식으로 기록한다.

## 33. 진행 중 — v52 기준 시나리오·인물 간 대사 전수 한글화

- 새 작업 기준은 Citra 확인된 `v52-intermediate`다. 전체 Patch 30파일은 `analysis\dialogue_translation_work\checkpoint_00_v52_dialogue_baseline\PatchSnapshot`에 byte-exact로 봉인했고, 실제 작업은 `staging_v52_dialogue`에만 누적한다.
- 정식 Patch/Revision은 모든 대상 대사가 완료되고 구조·제어코드·용량·alias·결정적 재빌드 검증을 통과할 때까지 갱신하지 않는다.
- 완료된 인물 데이터는 작업 범위 밖이다. `RomFS\Scenario\fix_data.bin`은 시작 SHA-256 `126279344D75454A7617EDE997ED8FE75B1D35B652B8E32CF7AC5DA4F75FA72E`에서 한 바이트도 바꾸지 않는다.
- 대상은 아직 일본어로 남은 시나리오 대사와 인물 간 대사다. HTML/JSON `translation_ko`를 우선 참고하되, 실제 runtime 소스가 `code.bin`인지 각 `msgsecXX.dat`인지 먼저 증명하고 원본 제어코드와 출력 구조를 보존한다.
- 튜토리얼, 열전, 메뉴/UI/버튼, 순수 인물 이름표는 이번 범위에서 제외한다. 약 20% 단위로 staging 전체 manifest와 대응표·검증 보고서를 checkpoint에 저장한다.

### checkpoint 20 완료

- 시작 미번역 대사 범위는 290건이며 58건(20.0%)을 staging에 적용했다. 50건은 HTML 번역의 원 슬롯 direct fit, 8건은 동일 의미의 최소 슬롯 압축이다.
- 저장 위치: `analysis\dialogue_translation_work\checkpoint_20_v52_dialogue`; 실제 누적 작업본: `analysis\dialogue_translation_work\staging_v52_dialogue`.
- 변경된 staging 게임 파일은 `ExeFS\code.bin`과 `RomFS\Common\Font\font.g1t`뿐이다. `fix_data.bin`, `msgsec07.dat`, 그 밖의 staging 파일 및 실제 Patch는 v52 기준과 byte-exact다.
- 모든 58건은 fixed-position, 원 슬롯 이내, 20칸 이내, 서식 토큰 보존이다. 신규 glyph 17자에 대해 alias 충돌 0, packed readback pixel-exact, 지정 cell 밖 font pixel diff 0을 확인했다.
- staging SHA-256: `code.bin=0F49169D58D1F25405E44A6482A854A949ACAB7175FCE0E9789A2920FAA98605`, `font.g1t=49C39C311915D3CA5104A67754C32D5609F5921F75D01E493C290A8BF8BD24B7`. 정식 Revision은 아직 없다.

### checkpoint 40 완료

- checkpoint 20 봉인본에서 대사 58건을 추가해 누적 116/290건(40.0%)을 staging에 적용했다.
- 모든 추가 target은 HTML 번역 의미를 유지한 fixed-position 압축이며 슬롯 overflow 0, 20칸 초과 0, 서식 토큰 변경 0, 대상 슬롯 밖 code diff 0이다.
- 신규 glyph는 `찌` 1자다. target alias 충돌 0, packed readback pixel-exact, 신규 cell 밖 font pixel diff 0이다.
- 백업: `analysis\dialogue_translation_work\checkpoint_40_v52_dialogue`. staging 해시는 `code.bin=936AE91A...B36BE`, `font.g1t=7E589042...FD9F8`이다.
- 실제 Patch, 완료 인물 `fix_data.bin`, v52 `msgsec07.dat`는 계속 불변이다. 정식 Revision은 없다.

### checkpoint 60 완료

- 대사 58건을 더 적용해 누적 174/290건(60.0%)이다. HTML 대응 48건과 code-only 원문 직역 fragment 10건으로 구성된다.
- 신규 glyph는 `켰` 1자다. 모든 문자열은 원 code slot 이내이고 20칸/서식 토큰/대상 외 diff/packed readback gate를 통과했다.
- 백업: `analysis\dialogue_translation_work\checkpoint_60_v52_dialogue`; staging `code.bin=54CD5454...3DE4C`, `font.g1t=0E1075C8...97B1F`.
- 실제 Patch와 인물 데이터, v52 `msgsec07`은 불변이며 아직 정식 Revision은 없다.

### 대사 coverage 해석 주의

- 60%는 `code.bin` 대사 풀 290건 중 174건을 뜻한다. direct Message까지 포함한 전체 완료율이 아니다.
- 별도 direct Message inventory 결과 `msgsec04/06/07/08`에 HTML 번역 fragment 957건이 있고, v52까지 적용된 86건을 제외하면 871건이 남아 있다.
- 상세 목록: `analysis\dialogue_translation_work\direct_message_dialogue_inventory.json/.tsv`. 이 단계는 parent/제어코드/파일별 헤더 구조 검증 후 진행해야 한다.

### checkpoint 80 완료

- checkpoint 60에서 code-only 대사 58건을 더 적용해 `code.bin` 대사 풀은 누적 **232/290건(80.0%)**이다. direct Message의 871개 미적용 fragment는 별도 단계다.
- 자연스러운 번역을 우선한다. 신규 글리프가 필요하면 현재 일본어 참조·기존/과거 alias·이름 글리프·수직 인접 cell을 전수 제외한 검증된 일본어 donor만 사용하며, donor 절약을 위해 비문이나 잘못된 띄어쓰기를 만들지 않는다.
- 신규 글리프는 `벅/찰/났/슨/떠/곁/낳/폐/랍/넣` 10자다. alias 충돌 0, packed readback exact, 지정 cell 밖 pixel diff 0을 통과했다.
- 백업: `analysis\dialogue_translation_work\checkpoint_80_v52_dialogue`; staging `code.bin=BA368B7A...9E80A`, `font.g1t=36D77D87...081AC`.
- 실제 Patch, 완료 인물 `fix_data.bin`, v52 `msgsec07.dat`는 불변이다. 정식 Revision은 없다.

### corrected code.bin checkpoint 100 완료

- 잔여 58건 중 범위 밖 미적용 튜토리얼 8건은 원문 그대로 제외했고, 실제 시나리오·인물 간 대사 50건을 추가했다. 보정된 `code.bin` 범위는 **282/282건 완료**다.
- 이미 번역된 튜토리얼은 삭제·복원하지 않는다. 이번 제외 8건은 원래부터 일본어였으며 앞선 232건에도 해당 항목이 없다.
- 권위 백업은 `analysis\dialogue_translation_work\checkpoint_100_v52_code_dialogue_corrected`다. `checkpoint_100_v52_code_dialogue`는 첫 봉인 빌더의 비-idempotent base 검사 때문에 결정성 2회 게이트를 통과하지 못한 실패 이력이며 사용하지 않는다.
- staging `code.bin=75319C9A...57BA4`, `font.g1t=36D77D87...081AC`; 실제 Patch, `fix_data.bin`, v52 `msgsec07`은 불변이다.
- 다음 단계는 direct Message 871개 fragment다. 이 단계가 끝나기 전에는 전체 대사 완료나 정식 Revision 발행으로 간주하지 않는다.

### direct Message msgsec04 완료

- `msgsec04/06/08` 무번역 identity rebuild가 Original·Patch·staging에서 모두 byte-exact임을 확인했다. report: `analysis\dialogue_translation_work\direct_message_identity_report.json`.
- `msgsec04` 미적용 70 fragment(24 parent)를 전부 staging에 적용했다. 제어코드/20칸/header metadata/new-pointer segment readback/결정적 2회 재빌드를 통과했다.
- checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg04_v52_dialogue`; `msgsec04=80DAC07E...FA244`.
- direct Message는 871건 중 70건 적용, 801건 남음. 다음은 `msgsec06`이며, control mismatch 2건·21칸 1건·미지원 글리프 문장 15건을 먼저 자연스럽게 교정해야 한다.

### direct Message msgsec06 완료

- `msgsec06` 212 fragment / 75 parent 전부 적용 및 봉인 완료. direct Message 누적 282/871, 잔여 589건(`msgsec08` 182 + 특수 `msgsec07` 407)이다.
- checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg06_v52_dialogue`; `msgsec06=069068FC...39060`.
- 실제 Patch, code/font, 인물 데이터, msg04, v52 msg07은 보존됐으며 정식 Revision은 아직 없다.

### direct Message msgsec08 완료

- `msgsec08` 182 fragment / 60 parent 전부 적용 및 봉인 완료. direct Message 누적 464/871, 잔여 407건은 모두 특수 `msgsec07`이다.
- checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg08_v52_dialogue`; `msgsec08=18E4B7D0...E4E2`, report=`6C5856CB...49DF`.
- 실제 Patch와 완료된 code/font·인물 데이터·msg04/msg06·v52 msg07은 보존됐고 정식 Revision은 아직 없다.

### direct Message msgsec07 및 전체 대사 완료 — v53 발행

- `msgsec07` 잔여 407건을 모두 적용했다. 파일 권위 493 fragment / 177 parent는 전부 현재 번역으로 재구성됐고 direct Message 최종 범위는 957/957이다.
- 387개 flat byte pointer 중 345개 시작 포인터는 새 body 위치로 재계산했다. fragment 내부/직전 42개는 원 delta를 재사용하지 않고 독립 한국어 런타임 문자열로 분리했다. opaque segment에 속한 권위 21건도 전부 이 경로로 표시된다.
- 신규 글리프는 effective runtime 참조 0을 입증한 일본어 donor 4개만 사용했다: `춘=94A3`, `돼=95DE`, `엣=9941`, `낭=9944`. 기존 이름·과거 alias는 보존됐다.
- 최종 checkpoint: `analysis\dialogue_translation_work\checkpoint_direct_msg07_v52_dialogue`; `msgsec07=49A4EC0B...F200EC`, `font=5E753A7C...07C37F`.
- 주의: `direct_message_msg07_preflight_report.json`은 donor 배정 전 문제를 보존한 진단 산출물이라 `pass=false`가 정상이다. 최종 판정에는 `direct_message_msg07_staging_report.json`과 checkpoint `manifest.json`의 `pass=true`를 사용한다.
- 정식 `v53` Patch와 `Backup\Sangokushi 2 Patch - v53` 발행 완료. 변경 파일은 `code.bin`, `font.g1t`, `msgsec04/06/07/08.dat` 6개뿐이다. `fix_data.bin`과 나머지 24개 파일은 v52와 byte-exact다.
- 전체 범위: `code.bin 282/282`, direct Message `957/957`. report: `analysis\v53_dialogue_complete_report.json`. 다음 작업은 Citra 실화면에서 대표 시나리오/대화와 msgsec07 포인터 화면을 확인하는 것이다.

### v53 Citra 버그 수정 1차 — v54-intermediate

- 사용자 실화면에서 확인된 첫 5건만 수정했다. 게임 파일 변경은 `RomFS\Message\msgsec06.dat`, `msgsec07.dat` 두 개뿐이다.
- `msgsec07` header[154/172]의 `수색하시겠습니까?`/`주시겠습니까?`는 ASCII `3F` 대신 원판 전각 `？=81 48`을 쓰는 독립 EOF 문자열로 전환했다.
- header[176]은 split source `民` + `들도 매우…`를 피하고 `백성들도 매우<$0A>좋아할 것입니다` 전체 문구를 EOF에 append했다. header[178]은 `민충성도가 %d 이(가) 되었습니다`로 전환했다. formal v53 msg07 body와 다른 383개 header pointer는 byte-exact다.
- `msgsec06`은 원본 header가 word0=161, word1~160 absolute byte pointer임을 확정했다. v53 generic repacker가 앞 75개를 word-offset으로 바꾼 것이 게임 시작 직후 무관한 초선 대사가 뜨는 원인이다. v54는 156개 fragment-start pointer를 복구하고 내부 4개 pointer를 EOF 독립 suffix로 detangle했다. v53 translated body는 byte-exact다.
- 도구: `tools\sangokushi2_msgsec06_flat_header.py`, `tools\build_sangokushi2_v54_runtime_bugfix_batch1.py`; report `analysis\v54_runtime_bugfix_batch1_report.json`.
- SHA-256: `msgsec06=7E661C766AAF6152D6B9D533F0CC9CEA9C40BBFCE185F2E24B1277A80F1D1A9B`, `msgsec07=9DA3264D349410B82EDF858DEB7D198CAC8D3C8129A3AA8516E71C62B5AC22BE`.
- **다음 확인:** (1) `주시겠습니까？`, (2) `백성들도 매우 / 좋아할 것입니다`, (3) `민충성도가 83 이(가) 되었습니다`, (4) `수색하시겠습니까？`, (5) 게임 시작 직후 초선 prompt가 더 이상 잘못 뜨지 않는지. 이 5건 확인 후 다음 버그 묶음을 v54 기준으로 이어간다.

### 기본/공통 런타임 정리 — v55-intermediate

- `code.bin` extractor pool 밖까지 NUL-terminated CP932 문장형 문자열을 재점검해 공통 UI 144건을 fixed-slot으로 반영했다. 강노 구입의 `誰に取引させますか？`도 `0x1778E0`의 hardcoded common UI였다.
- `msgsec04`는 54 absolute byte pointer(51 start+3 inside), `msgsec08`은 127 pointer(125+2) 구조임을 확정해 v53 generic repack 회귀를 복구했다. inside pointer는 독립 EOF 한국어 문자열로 detangle한다.
- v54의 `msgsec06/07` 구조 보정도 재현한 뒤 공통 technical-prefix pointer만 추가 append-only 전환했다. v55 게임 파일은 `code.bin`, `msgsec04/06/07/08.dat` 5개가 formal v53과 달라진다.
- 사용자 Citra에서 시혜 `누가 합니까?`, 상인 거래 `누가 거래합니까?` 정상 출력을 확인했다. 이 두 화면은 v55 런타임 성공 기준이다.
- 권위 목록 `analysis/v55_common_runtime_targets.json`, report `analysis/v55_common_runtime_cleanup_report.json`.

### UI 후속 + 도시명 — v56-intermediate

- 시혜 수량: `0x154F48 米をいくら%sますか？`를 20B 고정 슬롯에서 `쌀을 얼마나 줍니까？`로 처리. 동일 구조의 금 2슬롯도 `금을 얼마나 줍니까？`로 보정했다.
- 임명: `msgsec10_0027_001`은 natural authority `정치 또는 지력이 80 이상.`을 유지하고 runtime 23B 슬롯에는 `정치 또는 지력 80 이상.`을 fixed-position 삽입. `将軍/武官/文官/太守`는 `장군/무관/문관/태수`로 수정했다.
- 게임 시작: `それではゲーム%sです` + `スタート` 구조를 `그럼 게임을 %s.` + `시작합니다`로 바꿨다. `スタート` 뒤 다음 문자열까지 12B padding이 모두 0임을 먼저 검증했고 최종 조합은 `그럼 게임을 시작합니다.`다.
- 매복 도시 선택: `msgsec07_0109_001`의 끝 `0x96`은 control이 아니라 다음 fragment `0x84`와 합쳐 `埋(0x9684)`가 되는 split Shift-JIS lead byte였다. header[249]를 EOF의 `어디에 매복시키겠습니까？`로 append-only 전환했다.
- 도시명: `fix_data.bin` `0x1C`, stride `0x34`, 총 46레코드의 첫 9B 표시명만 한글화. 도시명 필드 밖 diff=0, 독음 전부 byte-exact, record46 이후 byte-exact, 기존 인물 이름/독음 영역 보존. 도시 목록/원 raw는 `analysis/v56_ui_city_targets.json`이 권위다.
- v56 새 write는 `code.bin`, `msgsec07.dat`, `msgsec10.dat`, `fix_data.bin` 4개뿐이고, v55 `msgsec04/06/08.dat`는 byte-exact 계승했다. 신규 font/glyph 없음. 빌더를 2회 연속 실행해 동일 해시 확인.
- 빌더 `tools/build_sangokushi2_v56_ui_city_cleanup.py`, report `analysis/v56_ui_city_cleanup_report.json`. v57의 결정적 입력 기준으로 유지한다.

### 외교/정보/특산/상인 부분 보정 — v57-intermediate

- 공동작전 대상 선택 `0x17FAD8`은 강제 개행이 있던 `어느 군주에게\n공동작전을 제안합니까?`를 사용자 2안인 `어느 군주에게 제안합니까？`로 축약했다. 기존 36B 슬롯 안에서 fixed-position 처리했다.
- 외교→교환의 `무엇을 내놓습니까？`는 문구 바이트 자체가 틀린 것이 아니라 `엇=98FB` **far alias 런타임 실패**였다. 98FB 계산 physical3652의 packed font는 Sam3 `엇`과 pixel-exact였지만 Citra에서는 실제로 `첨`으로 보였다. v57은 전역 98FB를 건드리지 않고 이 화면 한 곳만 original-valid/live-ref=0/non-historical `8AF9/physical1018`에 `엇`을 추가해 재인코딩했다. 8AF9는 현재 한글 physical 및 ±73과 겹치지 않으며 packed readback/outside-cell diff gate를 통과했다. 다른 98FB 사용처는 후속 실화면 검수 대상으로 남긴다.
- 교환 수량은 `0x15446C 가져가기`를 `가져갑니까`, `0x154FC8 弩をいくら%sますか？` / `0x151B60 強弩...` / `0x1554B0 軍馬...`를 각각 `노/강노/군마를 얼마나 %s？`로 바꿔 최종 조합을 `...얼마나 가져갑니까？`로 만들었다. 금/쌀의 기존 self-contained 수량 문구는 건드리지 않았다.
- 정보→타국 `0x16FA60`은 22B 슬롯에 정확히 들어가는 `어디의 정보를 볼까요？`로 교체했다.
- 특산품 `msgsec05.dat`는 word0=22 flat absolute-byte header 중 실제 runtime header[19]=0x01FC, [20]=0x020C, [21]=0x021F를 EOF의 `이미 획득했습니다` / `먼저 조건을 만족시킵시다` / `수색해 봅시다`로 append-only 전환했다. 기존 body는 byte-exact, 파일 584→653B(+69).
- 상인/구입 공통 `msgsec07`은 v56 body를 그대로 재현한 뒤 header[68,69,74,284,291,298]만 `쌀 판매/구매`, `노/강노/군마 구매` 전체 한글 문구로 append-only 전환했다. 파일 15797→15989B(+192), 기존 body와 나머지 header는 byte-exact다.
- v57 새 write는 `font.g1t`, `code.bin`, `msgsec05.dat`, `msgsec07.dat` 4개뿐이다. v56의 `msgsec04/06/08/10.dat`, `fix_data.bin`은 byte-exact 계승했다. Original/Rebuild/Backup은 수정하지 않았다.
- 빌더 `tools/build_sangokushi2_v57_partial_ui_cleanup.py`를 연속 2회 실행해 동일 SHA-256을 확인했다. report `analysis/v57_partial_ui_cleanup_report.json`, target inventory `analysis/v57_partial_ui_targets.json`.

### 도시/전장명 + 자원 동사 조립 보정 — v58-intermediate

- **v58 시점 Patch는 v58-intermediate**였다. v57을 결정적으로 재현한 뒤 그 위에만 수정했고, 이 계층은 현재 v59의 베이스로 그대로 계승된다. Original/Rebuild/Backup은 쓰지 않았다.
- Citra 실화면 권위로 도시명 far-alias 실패를 확정했다. `북평`의 `북=98DD`, `홍농`의 `농=98F2`가 stock/한자 글리프로 보였다. 특히 `홍농`은 `홍`만 한글이므로 과거 `홍농 정상` 판독은 잘못된 것으로 폐기한다.
- 46개 도시명은 모두 다시 인코딩하되 `fix_data` 표시명에서만 local fallback 7자를 사용한다: `북=89D0`, `릉=8BD3`, `녕=8F7A`, `농=956B`, `곡=9391`, `뢰=94D6`, `새=9773`. 전역 대사/이름 alias를 일괄 변경하지 않는다.
- 전장명 테이블은 `fix_data.bin 0x970`, stride `0x30`, 22개, 첫 7B 표시명이다. 전부 한글화했다: `백랑산, 기산, 서새산, 역경, 계교, 관도, 정도, 오장원, 가정, 적도, 회음, 장판, 이릉, 합비, 부성, 유수구, 적벽, 호뢰관, 함곡관, 무관, 호관, 양평관`.
- 구조 주의: 도시 46번째 record의 명목 끝 `0x974`보다 전장 테이블 시작 `0x970`이 4B 앞이다. 전장 22개 record의 명목 끝 `0xD90`도 인물 테이블 `0xD78`을 지난다. 따라서 도시/전장 모두 record 전체가 아니라 확인된 표시명 필드만 수정한다. v58 gate는 도시 46×9B + 전장 22×7B 밖 diff=0과 인물 표시명 영역 byte-exact를 강제한다.
- `엇=98FB`는 더 이상 화면 하나짜리 예외가 아니다. v57 기준 한국어 런타임 98FB를 전수 감사해 `code.bin` 한국어 7곳 + `msgsec06` 7곳 + `msgsec07` 1곳 + `msgsec08` 1곳을 `8AF9`로 승격했다. Original `code.bin` 원래 raw 98FB 4곳은 그대로 보존한다. 최종 effective Message/Scenario legacy 98FB는 0건이다.
- 외교/교환 `何を要求しますか？`의 20B allocation `0x1D0908..0x1D091B`에 `무엇을 요구합니까？`를 넣었다. `엇`은 8AF9를 사용한다.
- 자원 수량은 원본의 `%s` 동사 조립 구조를 복원했다. formatter=`금을/쌀을/노를/강노를/군마를 얼마나 %s니까？`, action stem=`가져갑/줍/요구합/요청합`. 따라서 5개 자원 모두 상황별로 `...가져갑니까？`, `...줍니까？`, `...요구합니까？`, `...요청합니까？`가 된다. v57에서 빠졌던 별도 `持って行き` 슬롯 `0x1D16A4`도 함께 `가져갑`으로 보정했다.
- v58 write: `font.g1t`, `code.bin`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `fix_data.bin`. `msgsec04/msgsec05/msgsec10`은 v57과 byte-exact다.
- 빌더 `tools/build_sangokushi2_v58_city_battlefield_exchange.py`; inventory `analysis/v58_city_battlefield_exchange_targets.json`; report `analysis/v58_city_battlefield_exchange_report.json`. 반복 실행에서 게임 파일 SHA 동일, report SHA=`2F2A7B77F6300CBDFB4B6395D05169EA58986702383C435A4FC7A92ED94C65EC` 동일.
- 핵심 SHA: `code=3B4A9D64...BD3AF`, `font=B09CFFC4...C66C`, `msg06=4D6EBE87...941A`, `msg07=4842C1A0...2CE1`, `msg08=3AC66CA3...436E`, `fix_data=A00BB671...40F5`.
- **다음 Citra 우선 확인:** `북평`, `홍농`, `강릉/무릉/영릉`, `건녕`, 전장 22개 목록/표시, `무엇을 내놓습니까？`, `무엇을 요구합니까？`, 그리고 금/쌀/노/강노/군마의 `가져갑니까/줍니까/요구합니까` 상황별 문구. 정상 확인 전에는 v58을 정식 revision으로 올리지 않는다.

### 임명 화면 실제 source + 경고/방랑 보정 — v59-intermediate

- **v59 단계의 Patch는 v59-intermediate**였다. v58을 결정적으로 재현한 뒤 `code.bin/msgsec07/msgsec10`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- 임명 버튼의 실제 source는 공백 없는 `将軍/武官/文官/太守`가 아니라 `軍 師 / 将 軍 / 武 官 / 文 官 / 太 守` spaced 문자열이다. pointer table `0x1E486C`를 gate로 고정하고 `0x1D080C/0x1D07E4/0x1D0834/0x1D083C/0x1D0844`를 `군사/장군/무관/문관/태수`로 사용한다. 군사는 이미 Citra 성공이므로 gate-only다.
- 신분 `君主@0x1CEE2C`는 status enum tables `0x1DB6C0/0x1DC2E4`의 첫 항목임을 확인해 `군주`로 수정했다.
- 임명 대상 prompt `0x16CE34`는 proven 24B allocation 안에서 **`%s 임명 대상을 선택`**으로 변경했다. 장군 선택 시 `장군 임명 대상을 선택`가 된다.
- `msgsec10` header[67..71]의 장군/군사/무관/문관/태수 설명을 모두 fixed-position 한글화했다. header와 05 separator는 이동하지 않는다.
- 태수 중복 임명 경고는 Original `任=94 43` split 경계가 이전 번역에서 orphan `0x94`로 남아 `?`처럼 보인 문제다. `msgsec07` header[190]을 EOF의 `군주가 있으므로<$0A>태수를 임명할 수 없습니다`로 append-only detangle했다.
- 방랑 확인은 번역 fragment의 ASCII `?`와 다음 원본 fragment의 전각 `？`가 중복 출력된 문제다. header[210]을 `정말로 방랑하시겠습니까？` 독립 문자열로 전환해 전각 물음표 하나만 남긴다.
- 빌더 `tools/build_sangokushi2_v59_appointment_runtime_cleanup.py`; inventory `analysis/v59_appointment_runtime_targets.json`; report `analysis/v59_appointment_runtime_cleanup_report.json`. 게임 파일과 report를 연속 2회 실행해 모두 동일 SHA-256을 확인했다.
- 핵심 SHA: `code=C313AABC...3C865`, `msg07=29ADA1EB...8555D`, `msg10=29885C47...A71F4`, report=`C1AA4D3A...BE75A`. v58 `font/fix_data/msg04/05/06/08`은 byte-exact 계승한다.
- **다음 Citra 최우선:** 임명 5버튼과 5개 상단 설명, `장군 임명 대상을 선택`, 태수 중복 임명 경고, 방랑 물음표 하나, 신분 `군주`. 정상 확인 전에는 v59를 정식 revision으로 올리지 않는다.

### 공통 침공/자원명/세율/매복/치수 보정 — v60-intermediate

- **현재 Patch는 v60-intermediate**다. v59를 결정적으로 재현한 뒤 `code.bin/msgsec07`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- Citra의 `손견軍が徐州に / 攻め込んだ！`는 이미 번역된 `msgsec07`이 아니라 extractor pool 밖 hardcoded 조립 경로였다. `0x15789C/0x157B20`의 `%s%s軍が...`를 `%s%s 군이...`, `0x1584A4 に/攻め込んだ！`를 `에/쳐들어갔다！`로 수정했다. 같은 block의 위임 중 침공(`0x157D70`)과 증원군 파견(`0x158468`)도 추가 한글화했다.
- `%s의 수가 / %d이 되었습니다` 혼합 출력은 formatter가 아니라 `%s` 인자 table 문제였다. `0x1AB3FC`가 가리키는 `弩/強弩/軍馬@0x1CFAC0/0x1CFAB8/0x1CFBFC`를 `노/강노/군마`로 바꾸고 `0x1E4824` spaced UI table의 `強 弩/軍 馬`도 동일 표기로 맞췄다.
- 세율 확인 header[228]은 v53 detangle가 앞의 `세율`을 의도적으로 건너뛰어 실제 Citra도 `을 %d％로 합니다...`로 시작했다. v60은 전체 `세율을 %d％로 합니다<$0A>괜찮으시겠습니까？`를 EOF에 append하고 header[228]만 전환했다. pre-existing body는 byte-exact다.
- 매복의 독의 실제 텍스트 버튼 table `0x1E52B0`은 `埋伏@0x1CFD98 / 撤退@0x1CFFC4`를 가리킨다. 각각 `매복/철수`로 수정했다.
- 치수의 단독 `よろしいですか？`는 `code.bin 0x0B90DF`의 generic command-confirmation suffix로 확인해 `괜찮으십니까？`로 수정했다. 다른 exact occurrence `0x1CF4A1`은 세이브 포맷 UI이므로 범위 밖으로 보존하며 최종 일본어 exact occurrence가 그 1곳뿐임을 gate로 검사한다.
- 빌더 `tools/build_sangokushi2_v60_common_event_resource_tax_ambush.py`; inventory `analysis/v60_common_event_resource_tax_ambush_targets.json`; report `analysis/v60_common_event_resource_tax_ambush_report.json`. code/msg07 및 report를 연속 2회 실행해 완전히 동일한 SHA-256을 확인했다.
- 핵심 SHA: `code=DB307793...C53837`, `msg07=22C388FE...2C6331`, report=`1B1DEBD9...828320`. v59의 `font/fix_data/msg04/05/06/08/10`은 byte-exact 계승한다.
- **다음 Citra 최우선:** 침공 공통문, `노/강노/군마` 결과 표기, 세율 전체 문장과 전각 물음표, `매복/철수` 버튼, 치수 `괜찮으십니까？`. 정상 확인 전에는 v60을 정식 revision으로 올리지 않는다.

### 임명 질문·임시징수·노 결과명·시작 화면 보정 — v61-intermediate

- **현재 Patch는 v61-intermediate**다. v60을 결정적으로 재현한 뒤 `code.bin/msgsec01/msgsec07`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- 임명 도시 선택은 `msgsec07` header[187]을 EOF의 `어느 도시의 무장을 임명하시겠습니까？`로 append-only 전환했다. 기존 slot 용량을 늘리지 않으므로 전각 물음표 때문에 문구를 축약할 필요가 없다.
- 임시징수 경고 header[221]은 NDS 한국어판 권위 문구 **`백성들의 신뢰를 / 저버릴 생각입니까？`**로 독립시켰다. 원 fragment의 inside-pointer delta를 한글에 재사용해 `신뢰를 / 저버릴`만 잘리는 방식은 금지한다. header[222]의 실행 확인도 `임시 징수를 하시겠습니까？`로 독립시켜 ASCII `?`를 제거했다.
- v60에서 `弩@0x1CFAC0` C-string을 번역했음에도 결과창만 `弩`가 남은 이유는 별도 고정폭 source `code.bin 0x1CF9A8=" 弩 "`였다. 이 8B slot만 `" 노 "`로 수정했고 강노/군마의 정상 source는 보존한다.
- 시작 메뉴 설명은 `msgsec01.dat`의 6개 실제 fragment `0042_001/0043_000/0043_001/0044_000/0044_001/0045_000`만 fixed-position/no-repack으로 수정했다. 권위 문구는 3DS 모드=`3DS판 『삼국지2』에 / 다양한 요소를 추가해두었습니다.`, 챌린지=`목표를 달성하면 / 비교적 단기간에 즐기는 게임입니다. / 초보자에게도 추천합니다.`, 잠긴 시나리오=`아직 플레이할 수 없는 / 시나리오입니다. / 영웅 모드를 진행하면 / 플레이할 수 있게 됩니다.`다.
- `msgsec01.dat` 전체 9,057B와 201-word header, 6개 fragment 시작 offset은 모두 보존했다. 화면 문장이 여러 fragment에 걸리므로 generic repack이나 임의 fragment 병합을 하지 않는다.
- 빌더 `tools/build_sangokushi2_v61_main_menu_temp_levy_bow.py`; inventory `analysis/v61_main_menu_temp_levy_bow_targets.json`; report `analysis/v61_main_menu_temp_levy_bow_report.json`. code/msg01/msg07 및 report를 연속 2회 실행해 완전히 동일한 SHA-256을 확인했다.
- 핵심 SHA: `code=9FE8117D...62082`, `msg01=61BD12DD...1EF75`, `msg07=8F620A31...9B4BB`, report=`4582652D...10727`. v60의 `font/fix_data/msg04/05/06/08/10`은 byte-exact 계승한다.
- **다음 Citra 최우선:** 임명 질문 전각 `？`, `노의 수가...`, 임시징수 경고/확인, 3DS 모드/챌린지/잠긴 시나리오 시작 화면 설명. 정상 확인 전에는 v61을 정식 revision으로 올리지 않는다.

### 시작 설정·환경설정 런타임 보정 — v62-intermediate

- **현재 Patch는 v62-intermediate**다. v61을 결정적으로 재현한 뒤 `msgsec01.dat/msgsec11.dat`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- 메인 금색 제목의 실제 source `msgsec01_0041_001/002`를 각각 `3DS 모드 / 도전 시나리오`로 번역했다. `챌린지 시나리오`는 20B 슬롯을 넘기 때문에 의미를 보존한 `도전 시나리오`를 사용한다. 챌린지 본문의 Citra 실패 `즐`은 font/전역 alias를 움직이지 않고 **`비교적 단기간에 하는 게임입니다.`**로 문장을 바꿔 회피했다.
- 시작 설정의 `プレイ人数を設定してください / 君主を選択してください`는 `msgsec01` duplicate가 아니라 `msgsec11_0008_002`, `0008_003+0009_000`이 실제 Citra source다. 각각 `플레이 인원 설정 / 군주 선택`으로 수정했고 같은 block의 `n번째 군주를 선택 / 표시할 군주를 선택`도 추가 정리했다.
- 환경설정은 `모드`, `무장편집`, `수명` 제목·설명을 실제 `msgsec01_0064_003~0075` 범위에서 fixed-position으로 한글화했다. 인접 일본어 추가 정리로 `BGM / 효과음` 제목·설명도 `0077_001~0079_000`에서 함께 처리했다.
- 여러 fragment를 한 target으로 채울 때 한글 2바이트/ESC 색상 제어가 원 경계에서 잘리지 않는지 검증한다. CP932 trail byte가 lead 범위 숫자와 겹칠 수 있으므로 raw-byte 휴리스틱은 사용하지 않고 **target 토큰/문자를 재인코딩해 허용 cut offset 집합**을 계산한다.
- 빌더 `tools/build_sangokushi2_v62_start_setup_environment.py`; inventory `analysis/v62_start_setup_environment_targets.json`; report `analysis/v62_start_setup_environment_report.json`. `msgsec01` 13그룹, `msgsec11` 4그룹을 readback했고 게임 파일과 report를 연속 2회 실행해 동일 SHA-256을 확인했다.
- 핵심 SHA: `msg01=E82075B5...183C7E`, `msg11=7C999329...64100`, report=`C450B008...CC582`. v61의 `code=9FE8117D...62082`, `msg07=8F620A31...9B4BB`, font/fix_data 및 기타 Message는 byte-exact 계승한다.
- **다음 Citra 최우선:** `3DS 모드 / 도전 시나리오` 제목, `단기간에 하는 게임입니다`, 플레이 인원/군주 선택, 환경설정 `모드/무장편집/수명/BGM/효과음` 제목·설명. 정상 확인 전에는 v62를 정식 revision으로 올리지 않는다.

### GitHub Issue001~002 런타임 보정 — v63-intermediate

- **현재 Patch는 v63-intermediate**다. v62를 결정적으로 재현한 뒤 `code.bin/msgsec07.dat`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- GitHub issue #1(`https://github.com/lovlince/sangokushi2/issues/1`)의 첨부 화면은 `3DSモードの / 特徴を教えて進ぜよう`이며 실제 source는 `code.bin 0x5C078`의 hardcoded NUL C-string이다. 같은 3DS 모드 사마휘 가이드가 `0x5C078~0x5C24C`, `0x1D29A4~0x1D2AD4`에 연속으로 남아 있어 총 15개를 원 allocation 안에서 한글화했다. 첫 화면 권위는 **`3DS 모드의 / 특징을 알려드리지요`**다.
- 후속 가이드는 `전기 / 사기 / 특성 / 공격 / 계략` 용어를 사용한다. 현재 폰트에 없는 `턴/벨`을 새로 만들지 않고 각각 `차례/단계`로 표현했다. 모든 target은 원 C-string 슬롯보다 짧거나 같고 최대 줄 폭 20 units 이하임을 gate로 검증했다.
- GitHub issue #2(`https://github.com/lovlince/sangokushi2/issues/2`)의 원문은 `民の信頼を裏切る`로 의미상 `백성의 신뢰를 저버리다`가 맞다. 그러나 일반 Message에서 `뢰`가 Citra 실화면상 `엽`으로 출력됐으므로 fix_data 전용 `뢰=94D6`을 전역 승격하지 않고 **`백성들을 배신하시겠습니까？`**로 재번역했다. `msgsec07` header[221]만 새 EOF 문자열로 append-only 전환하고 기존 body는 byte-exact로 보존한다.
- 빌더 `tools/build_sangokushi2_v63_github_issue001_002.py`; inventory `analysis/v63_github_issue001_002_targets.json`; report `analysis/v63_github_issue001_002_report.json`. 15개 code slot과 header[221]을 readback했고 게임 파일과 report를 연속 2회 실행해 동일 SHA-256을 확인했다.
- 핵심 SHA: `code=F3CAD388...31C7F1`, `msg07=E9BFD2C0...A4FF50`, report=`7D6C438F...0EF64F`. v62의 `msg01/msg11` 및 기타 v61 이하 파일은 byte-exact 계승한다.
- **다음 Citra 최우선:** issue #1의 첫 질문에서 `예`를 선택해 사마휘 3DS 모드 가이드를 끝까지 확인하고 일본어 잔존이 없는지 본다. issue #2 임시징수 경고는 `백성들을 배신하시겠습니까？`가 정상 출력되는지 확인한다.

### GitHub Issue002 자연스러운 문구 복원 — v64-intermediate

- **현재 Patch는 v64-intermediate**다. v63을 결정적으로 재현한 뒤 `msgsec07.dat`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- 사용자 최종 선호 및 원문/NDS 의미에 맞춰 issue #2의 권위 문구를 **`백성들의 신뢰를 / 저버릴 생각입니까？`**로 복원했다.
- 전역 `뢰=98EA`는 Citra에서 `엽`처럼 보였으므로 그대로 사용하지 않는다. v58부터 `font.g1t` physical 2863에 존재하는 **`뢰=94D6`** 셀이 현재 v63 font에서도 pixel-exact임을 재검증했고, v63 baseline runtime text에는 94D6 참조가 0이며 `fix_data`의 `호뢰관`에만 1회임을 확인했다.
- v64는 global Korean alias map과 `font.g1t`를 변경하지 않고 header[221]의 새 EOF 문자열에서만 `뢰→94D6` local override를 적용한다. 최종 target에는 `94D6` 정확히 1회, 실패 `98EA` 0회, 문장 끝 `？=8148`을 gate로 검증했다.
- 빌더 `tools/build_sangokushi2_v64_issue002_natural_wording.py`; inventory `analysis/v64_issue002_natural_wording_targets.json`; report `analysis/v64_issue002_natural_wording_report.json`. 게임 파일과 report를 연속 2회 실행해 동일 SHA-256을 확인했다.
- 핵심 SHA: `msg07=C2017425...ECCBD0`, `code=F3CAD388...31C7F1`(v63 계승), `font=B09CFFC4...ABC66C`(불변), report=`3C51D3B5...6DB9E1`.
- **다음 Citra 최우선:** 임시징수에서 `백성들의 신뢰를 / 저버릴 생각입니까？`가 완전하게 나오고 특히 `뢰`가 `엽` 또는 한자로 깨지지 않는지 확인한다. Citra 승인 전까지 94D6을 다른 Message 문자열로 확대하지 않는다.

### GitHub Issue003~009 일괄 보정 — v65-intermediate

- **현재 Patch는 v65-intermediate**다. v64를 sealed chain에서 결정적으로 재구성한 뒤 `font.g1t`, `msgsec01.dat`, `msgsec07.dat`, `msgsec11.dat`만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- issue #3: `msgsec07` header[224] → **`선정을 오래 행하면 / 백성들도 마음을 다할 것입니다`**. fragment 경계 때문에 생긴 `행하 면` 공백을 독립 runtime 문자열로 제거했다.
- issue #4: header[196] → **`문관으로 임명하면 병사를 거느릴 수 / 없게 됩니다. / 괜찮으시겠습니까？`**. `없게/됩니다` 불필요 분리와 ASCII 물음표를 정리했다.
- issue #5: header[202] → **`현재 담당 군사는 [동적 무장명]입니다. / 변경하시겠습니까？`**. 무장명은 원본 3바이트 token `02 01 C8`을 그대로 보존한다.
- issue #6: 금색 제목 권위를 **`챌린지 시나리오`**로 복원했다. 원 20B 슬롯에 21B가 필요해 바로 뒤 `05 05 05`에서 1B만 차용하고 `05 05`를 유지한다. 신규 glyph는 `챌=9652/physical3108` 1개이며 packed readback pixel-exact다. 설명 권위는 **`목표 달성 시 완료됩니다. / 비교적 단기간에 플레이 가능합니다. / 초보자에게도 추천합니다.`**다.
- issue #7: 수명 설명의 고립 `B(0x42)`는 원 `。=81 42` split 흔적에서 생긴 것으로 보고 fixed-position 전체 문구로 덮어 제거했다. 기본 수명 설명과 `일부 시나리오/현재 시나리오` 제한 안내까지 한글화했다.
- issue #8: 환경설정 실제 `msgsec01_0079_001~0081_000`을 fixed-position으로 처리해 제목 **`타국 전쟁`**, 설명 **`타국 전쟁 표시를 설정합니다. / 「본다」이면 / 다른 군주의 전쟁을 봅니다`**를 적용했다.
- issue #9: 실제 source `msgsec11_0008_002`, `0008_003+0009_000`에 각각 **`플레이 인원을 설정하세요`**(24/24B), **`군주: 선택하세요`**(16/16B)를 적용했다. `군주를 선택하세요`는 17B로 슬롯 초과 및 10B fragment 경계 분할 때문에 사용하지 않는다.
- 빌더 `tools/build_sangokushi2_v65_github_issue003_009.py`; inventory `analysis/v65_github_issue003_009_targets.json`; report `analysis/v65_github_issue003_009_report.json`. v64 helper의 mutable Patch font 의존으로 두 번째 실행이 실패하는 문제를 v65 빌더에서 sealed v63/v58 기반 재구성으로 제거한 뒤 **연속 2회 PASS 및 동일 게임 파일 SHA-256**을 확인했다.
- 핵심 SHA: `font=3D6EEE47...99BD9`, `msg01=CF353E24...E06BF`, `msg07=397FFFE1...69F9E`, `msg11=0FCE42AF...00802`; `code=F3CAD388...31C7F1`, `fix_data=A00BB671...940F5`는 v64와 동일하다.
- **Citra 확인 전까지 v65는 intermediate**다. 특히 `챌=9652`와 제목 1B separator-borrow는 정적 검증만으로 성공을 선언하지 않는다. #3~#9 화면 확인 후 성공 문구만 번역 HTML/JSON 권위값과 동기화한다.

### GitHub Issue009 군주 선택 문구 재보정 — v66-intermediate

- 사용자 Citra에서 v65의 `군주: 선택하세요`가 실제 출력됨을 확인했고, 사용자 권위는 `군주를 선택하세요`, 바이트 부족 시 `군주를 선택`으로 확정됐다.
- `군주를 선택하세요`는 현재 alias 인코딩 기준 **17B**라 16B 영역을 1B 초과한다. 따라서 최신 권위 문구는 **`군주를 선택`**이다.
- ASCII space로 인코딩한 `군주를 선택`은 11B지만 원 `msgsec11_0008_003 + 0009_000`의 10B 경계가 마지막 `택` 2바이트를 분할한다. 이를 피하기 위해 화면상 동일한 단어 공백을 stock CP932 전각 공백 **`8140`**으로 인코딩했다. raw 12B는 `군주를` 6B + `8140` 2B + `선` 2B에서 정확히 10B 경계를 끝내고, 다음 fragment가 완전한 `택`으로 시작한다.
- v66은 v65 대비 `RomFS/Message/msgsec11.dat`만 변경한다. header/file size 보존, 16B reviewed 영역 밖 diff=0, 비대상 v65 파일 byte-exact를 gate로 검증했다. Original/Rebuild/Backup은 쓰지 않았다.
- 빌더 `tools/build_sangokushi2_v66_issue009_ruler_prompt.py`; inventory `analysis/v66_issue009_ruler_prompt_targets.json`; report `analysis/v66_issue009_ruler_prompt_report.json`. **연속 2회 PASS**했고 `msg11=04B77E10CD5295A8E4A650EBA2FCE296E4574758E91FB87C08C29CD40C1BC161`, report=`E069A510DDFF1DEDD5EDCB5346AC853D1576F43059CB062D9DD36A11445F3F69`다.
- **다음 Citra 최우선:** 상단 문구가 콜론 없이 `군주를 선택`으로 보이고, `8140` 공백이 이상한 기호나 과도한 간격으로 렌더링되지 않는지 확인한다.

### GitHub Issue005 재보정 + Issue010 챌린지 전체 한글화 — v67-intermediate

- 사용자 제공 스크린샷을 실제 확인한 뒤 작업했다. issue #5 화면은 v65 이후에도 `現在、`가 일본어로 남고 동적 무장명 뒤 조사가 `이(가)` 형태가 아니었다. 실제 조립 경로는 `code.bin 0x1CEF14`의 유일한 `現在、` C-string과 `msgsec07` dynamic-name suffix의 결합으로 판단되며, 두 곳을 동시에 최소 수정했다.
- #5 최종 목표는 **`현재 [무장명]이(가) 담당군사입니다 / 변경합니까?`**다. v67 당시에는 `code.bin` prefix + `msgsec07 header[202]` 경로로 판단했지만, **v72 Citra 재분석에서 실제 화면 source가 `code.bin 0x16C9B8/0x16D1D8` formatter 두 곳임이 확인됐다.** 이 문단의 v67 source 판정은 역사 기록이며 최신 구현 권위로 사용하지 않는다.
- issue #10 스크린샷의 챌린지 1·7과 주변 데이터를 대조해 `msgsec16.dat` 전체를 재분석했다. 원본은 954B, SHA `8E58EFF8...B1A7E`, word0=`49`이며 **word1~48이 전부 absolute BYTE runtime pointer**다. 기존 v12의 `26 pointer + 23 metadata` generic 판정은 이 파일의 런타임 권위로 폐기한다.
- 48개 pointer는 챌린지 1~6의 7항목씩과 챌린지 7의 6 pointer에 대응한다. 마지막 header[48]은 원본에서도 `無傷デ勝利 + 05 05 05 + 達成年数`를 한 body에서 처리한다. v67 당시 표기는 `달성 연수`였으나 **최신 v72 권위는 `달성년수`**이며, header target뿐 아니라 raw body의 `達成年数` 5곳도 모두 제자리 치환한다.
- 기존 `msgsec16` 954B body는 byte-exact 보존하고 header[1..48]을 EOF의 독립 한글 target으로 전환했다. 제목/플레이어/클리어/게임오버/평가를 챌린지 1~7 모두 포함한다. 신규 glyph 추가 없이 현재 map으로만 작성했다.
- v67 새 write는 `code.bin`, `msgsec07.dat`, `msgsec16.dat` 3개뿐이다. 첫 생성 뒤 mutable `msgsec16`을 v65 `챌` donor audit가 오인하는 재실행 문제를 발견했으며, v66까지 byte-exact였던 sealed Original `msgsec16`을 재구성 audit에만 사용하도록 보정했다. 이후 **연속 2회 PASS**했다.
- SHA: `code=D935B2B7...1F9294`, `msg07=851F2A11...D089B43`, `msg16=E15D508F...CDE7F12`, report=`554BC603...D95BBE`.
- **다음 Citra 최우선:** #5 일본어 prefix 제거와 `이(가)` 조사 표시, #10 챌린지 1~7의 모든 값 및 챌린지 7 마지막 두 평가 항목 분리 표시를 확인한다.

### StartMenu 지정 이미지 43개 재삽입 — v68-intermediate

- **현재 Patch는 v68-intermediate**다. v67의 모든 텍스트·폰트·Message·code 상태를 byte-exact로 계승하고 StartMenu G1T 네 개만 변경/추가했다.
- 교체 texture는 `start_menu_up_017`, `title_down_001`, `title_up_000/001`, `start_menu_down_004~042`의 정확히 43개다. 그 밖의 PNG는 읽거나 수정하지 않았다.
- `start_menu_up/down.g1t`는 sealed 최종 v67 Patch 컨테이너가 base다. v67 Patch에 없던 `title_up/down.g1t`만 immutable Original을 최초 편입 effective base로 사용했다. 각 container header와 비대상 payload는 해당 base와 byte-exact이며 43개 PNG readback은 모두 pixel-exact다.
- v67 전체 Patch manifest와 비교한 변경 집합은 `RomFS/StartMenu/start_menu_up.g1t`, `start_menu_down.g1t`, `title_down.g1t`, `title_up.g1t` 네 개뿐이다. Original/Rebuild/Backup은 쓰지 않았다.
- 빌더 `tools/build_sangokushi2_v68_startmenu_images.py`를 완성된 출력 위에서 재실행했으며 네 게임 파일과 report가 동일했다. report=`analysis/v68_startmenu_images_report.json`, report SHA=`DD8BDB1156DFC56A3F4647529214B180602098923FCF90F7893FC2F96D129037`.
- 컨테이너 SHA: `start_menu_up=708A8DA1...6C814CA`, `start_menu_down=3BB0903E...09CFC4E`, `title_down=133C28AB...38322A5`, `title_up=0C84D647...FE646B4E`. 다음 검증은 Patch 전체를 Rebuild에 복사한 뒤 네 화면군의 버튼/제목 이미지와 기존 비대상 이미지 회귀를 Citra에서 확인하는 것이다.

### StartMenu 지정 이미지 4개 재삽입 — v69-intermediate

- **현재 Patch는 v69-intermediate**다. 최종 v68 Patch의 `start_menu_down.g1t`를 sealed base로 삼고 `036/039/040/041` 네 texture payload만 교체했다.
- 비대상 payload와 G1T header는 v68 byte-exact이며 Patch의 나머지 31개 파일도 v68과 동일하다. 지정되지 않은 PNG는 열거나 수정하지 않았고 이미지 생성도 사용하지 않았다.
- 네 PNG readback은 모두 pixel-exact다. 완성 출력 위 재실행도 동일했으며 `start_menu_down.g1t=6DF767FD...0242DAB1`, report=`A1ADA792...75A2BDE8`이다.
- 빌더=`tools/build_sangokushi2_v69_startmenu_down_images.py`, report=`analysis/v69_startmenu_down_images_report.json`. 다음 단계는 Patch 전체를 Rebuild에 복사하고 해당 네 버튼 이미지와 주변 비대상 버튼의 회귀 여부를 Citra에서 확인하는 것이다.

### 아이템 전면 한글화 — v70-intermediate

- **v70 당시 Patch는 v70-intermediate**였다. sealed v69 32파일을 기반으로 만들었고 Patch 파일 수는 `Select.g1t` 최초 편입으로 33개다.
- 변경 파일은 정확히 5개다: `ExeFS/code.bin`, `RomFS/Scenario/fix_data.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Common/Select/Select.g1t`.
- 아이템 표시명 67/67, 종류 5/5, 효과 문자열 56/56을 한글화했다. `fix_data`의 표시명 13B만 수정했으며 읽기/정렬용 반각 가타카나 17B는 보존했다.
- 화면 목표: 상단 **`어느 아이템을 몰수합니까？`**, 확인창 **`[아이템명](으)로 / 괜찮으시겠습니까？`**, 목록 헤더 **`이름 / 종류 / 아이템 효과`**다. 효과 표기는 `무력+10`, `기동력+1 퇴각확실`처럼 표시된다.
- 신규 글리프는 `론=97E9/physical3446`, `륜=97F5/physical3458` 두 자다. 정적 font 검증은 통과했지만 Citra에서 `상한잡병론`, `사륜차`가 정확히 보이는지 우선 확인한다.
- `msgsec07` header[142]는 이 화면에서 word-offset이다. v70은 새 parent byte `0x4056`을 `0x202B`로 저장했다. 다른 absolute-byte header 규칙을 이 항목에 재사용하지 않는다.
- 아이템 헤더 3개는 이미지 source로 확인되어 `Select.g1t` texture `005/021/059`만 결정적으로 수정했다. AI 이미지 생성은 사용하지 않았고 다른 texture는 byte-exact다.
- 두 번 연속 빌드와 독립 역읽기 PASS. 빌더=`tools/build_sangokushi2_v70_item_translation.py`, targets=`analysis/v70_item_translation_targets.json`, report=`analysis/v70_item_translation_report.json`.
- 다음 Citra 검수: (1) 일반/특산/SP 아이템 이름 목록 전체, (2) 종류/효과 두 화면, (3) 몰수 상단 질문, (4) 받침 유무가 다른 아이템의 `(으)로` 표시, (5) 전각 물음표, (6) `론/륜` 글리프, (7) 헤더 이미지 위치/크기.

### GitHub Issue #019~#022 런타임 수정 — v71-intermediate

- **v71 당시 Patch는 v71-intermediate**였다. sealed v70 Patch 33파일을 기준으로 만들었으며 v70 대비 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 4개다. 나머지 29개는 v70과 byte-exact다.
- **#19 담당 군사:** v70 정적 데이터에는 `(가)`가 있었지만 Citra에서는 `현재 순욱이 담당군사입니다`처럼 ASCII 괄호가 사라졌다. 동적 이름 토큰 `02 01 C8`을 보존하고 header[202]를 새 absolute-byte target `0x40D3`의 **`[무장명]이（가） 담당군사입니다 / 변경합니까?`**로 전환했다. 저장은 stock 전각 괄호를 쓰고 화면 목표 표기는 `이(가)`다.
- **#20 챌린지:** 원문 `曹叡`는 **조예**가 맞다. `챌린지 7 강유의 북벌`, 챌린지5의 `하변, 한중, 자동, 영안, / 강주, 성도, 건녕을 점령함`, `달성 연수→달성년수`를 적용했다. 챌린지7은 `사마의, 조예를 참수`, `강유 또는 유선의 죽음. 조예나 사마의가 / 다른 세력으로 넘어감`으로 정리했다. `msgsec16`에서 바뀐 runtime header는 `[7,14,28,31,35,43,45,46,48]`뿐이다.
- #20의 `예=989D`는 이 화면에서 안전하다고 간주하지 않는다. v71은 **`예=927D / physical2399` local override**만 챌린지7 두 target에 사용한다. 바로 위 `쳐/physical2326`과의 bleed를 막기 위해 glyph를 1px 아래로 배치했고 top scanline empty, packed readback exact, target cell 밖 diff=0이다. **Citra 확인 전에는 전역 map으로 승격 금지**다.
- **#21 포상:** 금 수량 질문은 `금을 얼마나 줍니까？`로 수정했다. 아이템 질문은 공용 formatter를 **`어느 아이템을 %s`**로 바꾸고 8개 확인된 caller 각각에 완결형 동사를 공급한다. 포상=`줄까요？`, 보기=`볼까요？`, 내놓기=`내놓습니까？`, 몰수=`몰수합니까？`. 자원 수량용 action table(`가져갑/줍/요구합/요청합`)과 공유 금 formatter `0x154D68`은 v70 byte-exact다. Message duplicate header[322]도 `어느 아이템을 줄까요？`로 EOF 전환했다.
- **#22 조사/장수명:** 확인문은 ASCII `(으)` 대신 stock 전각 괄호를 사용해 **`[아이템명]（으）로 / 괜찮으시겠습니까？`**로 저장한다. header[142]의 특수 word-offset 규칙은 유지되며 새 parent byte `0x4134`, word pointer `0x209A`다. 수여 결과는 v70에서 동적 이름 토큰 `02 01 C8`의 `C8`이 누락된 것이 원인이므로 header[173]을 `0x4115`의 **`02 01 C8 + 의 충성이 / %d이 되었습니다`**로 복구했다.
- `msgsec07`에서 v71이 바꾼 header는 정확히 `[142,173,202,322]`다. 기존 body는 byte-exact이고 새 runtime 문구만 EOF append한다. 단, header[142]만 검증된 word-offset이며 173/202/322는 absolute-byte pointer다.
- 두 번 연속 결정적 빌드 및 독립 역읽기 PASS. SHA-256: `code=7EAE868E...EE772F`, `font=430ECA27...05CF9`, `msg07=E03846E3...55DB2`, `msg16=9C6C36DC...069F1`. 빌더=`tools/build_sangokushi2_v71_github_issue019_022.py`, report=`analysis/v71_github_issue019_022_report.json`.
- **v71 상태는 v72에서 대체됐다.** v71의 #19/#22 조사 관련 Message header 접근과 #21 포상 금 문구는 Citra 재검수에서 실제 source 누락/side-effect가 확인됐으므로 최신 권위로 사용하지 않는다.

### GitHub Issue #023~#025 + 담당군사 조사 재수정 — v72-intermediate

- **현재 Patch는 v72-intermediate**다. sealed v71 Patch 33파일을 기준으로 만들었으며 v71 대비 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 3개다. 나머지 30개는 v71과 byte-exact다.
- **담당군사:** 실제 Citra source는 `msgsec07 header[202]`가 아니라 `code.bin 0x16C9B8/0x16D1D8`의 두 formatter였다. 둘 모두 **`%s%s이(가) %s입니다 / 변경합니까?`**로 직접 수정했다. v72 검증에서 이전 `%s%s이 %s입니다 / 변경합니까？` formatter는 0개, 신규 formatter는 2개다.
- **#23 챌린지:** 게임오버 최신 문구는 **`강유 또는 유선이 죽음. 조예나 / 사마의가 다른 세력에게 패배.`**다. `달성년수`는 header EOF target만 고치는 것으로 충분하지 않았고 raw body의 `達成年数` 5곳(`0x00BA/0x0139/0x01FB/0x028A/0x03AF`)을 모두 같은 길이 8B로 제자리 치환했다. Citra가 header와 raw body 어느 경로를 쓰더라도 일본어 `達成年数`가 남지 않도록 한다.
- **#24 아이템 확인:** 사용자 지정 화면 권위는 **`[아이템명] (으)로 / 괜찮으시겠습니까?`**다. `%s` 뒤 공백 1칸, ASCII `(으)`, ASCII `?`를 사용한다. `msgsec07 header[142]`의 word-offset 규칙은 유지하며 v72 parent byte=`0x41B4`, word=`0x20DA`, target fragment=`0x41D9`다. v71의 전각 괄호/전각 `？` 저장형은 이 화면에서 폐기한다.
- **#25 금 포상:** v71의 `줄까요？`가 공용 `...%s니까？`와 결합해 `줄까요？니까？`가 된 side-effect를 수정했다. 포상 금 입력 함수 `0x154CC0`의 호출처는 `0x161B1C` 하나뿐이다. 일반 포상은 **`금을 얼마나 줄까요?`**, 다수 대상은 **`한 사람당 금을 얼마나 줄까요?`**로 고정한다. 다른 거래가 쓰는 shared formatter `0x154D68`은 v71 byte-exact 보존한다.
- 두 번 연속 결정적 빌드와 독립 역읽기 PASS. SHA-256: `code=35706BD7...948DB`, `msg07=E0AD2B32...A231B`, `msg16=AA8F5C2B...4F9B4`. 빌더=`tools/build_sangokushi2_v72_github_issue023_025.py`, targets=`analysis/v72_github_issue023_025_targets.json`, report=`analysis/v72_github_issue023_025_report.json`.
- **v72 Citra 결과:** 담당군사 `이(가)`는 정상 확인됐지만 #023 `조예`가 `조?`로 회귀했고 #024는 `청강검로`로 old source를 계속 사용했다. 따라서 v72의 담당군사/달성년수/#25 수정은 유지하되 #023/#024는 아래 v73 권위로 대체한다.

### Issue #023/#024 실제 runtime source 재보정 — v73-intermediate

- **현재 Patch는 v73-intermediate**다. sealed v72 Patch 33파일을 기준으로 만들었으며 v72 대비 변경 파일은 정확히 `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 2개다. `code.bin/font.g1t` 및 나머지 31개는 v72와 byte-exact다.
- **담당군사 성공 기준점:** 사용자 Citra에서 `현재 공융이(가) 담당군사입니다 / 변경합니까?`가 정상 표시됨을 확인했다. 실제 source인 `code.bin 0x16C9B8/0x16D1D8` formatter 직접 수정 방식은 앞으로도 유지하며, v73에서는 `code.bin`을 건드리지 않는다.
- **#023 `조예`:** v72 header[46] target `0x0917`에 `예=989D`가 1회 다시 들어간 것이 회귀 원인이었다. v71에서 확보한 Citra-safe local alias **`예=927D / physical2399`**를 재사용해 `989D→927D` 2바이트만 제자리 치환했다. 문구/줄바꿈/header[46]/파일 크기는 그대로이므로 사용자가 제시한 `강유나 유선이 죽음` 축약은 필요하지 않았다.
- **#024 HTML 대조:** 논리 row는 `msgsec07_0142_001`, 원문 `%sデ / ヨロシイデスカ?`, 기존 HTML 번역은 `%s로 / 괜찮으시겠습니까?`, 사용자 최신 목표는 **`%s (으)로 / 괜찮으시겠습니까?`**다. 아이템명 `청강검`은 `%s`/item-name 제어 경로가 공급하므로 이름 payload는 수정하지 않는다.
- **#024 실제 source:** v72의 올바른 target은 이미 byte `0x41D9`에 있었지만 Citra는 old byte `0x2D43`의 `%s로 / 괜찮으시겠습니까?`를 사용했다. 전체 header 역검색으로 **header[326]=0x2D43 absolute BYTE pointer**가 이 old target을 직접 가리키는 것을 확인했다. v73은 body append 없이 **header[326]만 `0x2D43→0x41D9`**로 변경한다. v72 header[142]=`0x20DA` duplicate parent는 그대로 둔다.
- 두 번 연속 결정적 빌드 및 독립 역읽기 PASS. SHA-256: `msg07=CF97A819...C4660E`, `msg16=17173923...ABCA6`. 독립 확인값: header[326]=`0x41D9`, header[142]=`0x20DA`, #024 target bytes exact, header[46]=`0x0917`, 게임오버 target의 `927D` 1회/`989D` 0회, `code.bin/font.g1t` v72 byte-exact.
- 빌더=`tools/build_sangokushi2_v73_issue023_024_runtime_source_fix.py`, targets=`analysis/v73_issue023_024_runtime_source_fix_targets.json`, report=`analysis/v73_issue023_024_runtime_source_fix_report.json`.
- **v73 Citra 결과:** #023 게임오버 `조예`와 #024 조사 `청강검 (으)로`는 정상화됐다. 다만 챌린지7 제목의 `북`이 한자형 glyph로 출력되고 #024 마지막 ASCII `?`가 어색하게 보여 아래 v74로 보정한다.

### Issue #023 제목 `북` + #024 물음표 표시 보정 — v74-intermediate

- **현재 Patch는 v74-intermediate**다. sealed v73 Patch 33파일을 기준으로 만들었으며 v73 대비 변경 파일은 정확히 `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec16.dat` 2개다. `code.bin/font.g1t` 및 나머지 31개는 v73과 byte-exact다.
- **챌린지7 제목 `북`:** header[43]=`0x0868` target에서 과거 Citra 실패 alias `북=98DD`가 1회 사용되고 있었다. v58에서 확보한 **`북=89D0 / physical789`** fallback을 이 title에만 local override로 재사용했다. 현재 v73 font physical789도 Sam3KRFont `북`과 pixel-exact이므로 font는 수정하지 않고 `98DD→89D0` 2바이트만 제자리 치환했다.
- **#024 최종 물음표:** 조사 표시는 v73에서 정상 확인됐고 실제 source는 header[326]이다. 최신 화면 목표는 **`[아이템명] (으)로 / 괜찮으시겠습니까？`**다. ASCII `?`를 CP932 전각 **`？=8148`**로 바꾸면 1바이트 늘어나지만 EOF append가 가능하므로 축약하지 않는다. 새 standalone target은 byte **`0x4231`**에 append하고 header[326]을 `0x4231`로 전환했다. `괜찮겠습니까？` fallback은 사용하지 않았다.
- 독립 역읽기: title `89D0` 1회/`98DD` 0회, 게임오버 `927D` 1회/`989D` 0회, header[326]=`0x4231`, 새 target 끝=`8148`, separator=`050505`, `code.bin/font.g1t` v73 byte-exact. 두 번 연속 결정적 빌드 SHA도 동일하다.
- SHA-256: `msg07=D8A07E95...BC0737`, `msg16=3AC48D4A...E22E2A`. 빌더=`tools/build_sangokushi2_v74_issue023_024_display_fix.py`, targets=`analysis/v74_issue023_024_display_fix_targets.json`, report=`analysis/v74_issue023_024_display_fix_report.json`.
- **다음 Citra 우선 검수:** (1) 상단 제목이 `챌린지 7 강유의 북벌`로 정확히 보이는지, (2) #024가 `청강검 (으)로 / 괜찮으시겠습니까？`로 보이며 마지막 물음표 모양이 정상인지 확인한다. 담당군사 `이(가)`와 게임오버 `조예`는 회귀 여부만 확인한다.

### GitHub Issue #016/#017/#018/#026 — v75-intermediate

- **현재 Patch는 v75-intermediate**다. 작업 당시 GitHub 직접 조회는 Cache miss였으므로 로컬 `Github_Issue\Issue016.html`, `Issue017.html`, `Issue018.html`, `Issue026.html`의 본문과 embedded 스크린샷을 실제 화면 권위로 사용했고, 각 화면을 현재 v74 바이너리 source와 교차검증했다.
- **#026 포상 상단:** 실제 source는 `code.bin 0x161C0C`, 기존 allocation은 20B다. preferred `누구에게 포상 합니까?`는 NUL 포함 22B라 들어가지 않아 사용자 제시 fallback **`누구에게 줄까요?`**를 사용한다. relocation 없이 원 슬롯 안에서 수정했다.
- **#017 사마휘 3DS 설명 3장:** 실제 source는 `code.bin 0x1D24CC / 0x1D2D04 / 0x1D1EB4`다. 각각 **`전략 명령을 실행하면 / 경험치가 모여 속성 단계가 / 오르고 전기를 배웁니다`**, **`「사기」는 전략 명령의 / 「훈련」「전쟁준비」로 / 미리 올릴 수 있습니다`**, **`영지의 재야 무장 중 「특성」을 / 가진 자가 있으면 적극적으로 / 등용하는 것이 좋습니다`**로 원 C-string allocation 안에서 제자리 수정했다.
- **#018 게임 시작 확정:** `msgsec01.dat` fixed-position source `0x1265/0x1272`를 **`확정` / `설정을 완료함`**으로 변경했다. 파일 크기와 fragment 시작 위치를 보존하며 no-repack이다.
- **#016 3DS 포상 도움말:** 일반 모드 `msgsec15`은 이미 번역돼 있었지만 3DS가 쓰는 `msgsec10` 복제본이 일본어로 남아 있었다. `0x0C65`=`최대 100, 여러 무장에게 지급 가능.`, `0x0C92`=`군주만 실행 가능. / 능력치·충성이 크게 상승.`, `0x0CCD`=`비용 없음. 금·아이템보다 / 효과가 적습니다.`로 fixed-position/no-repack 수정했다. 하단 버튼은 이미지가 아니라 `msgsec03.dat 0x00C3`의 `戻ル` 텍스트였고, 뒤 `05 05 05` separator에서 1B만 차용해 **`뒤로`**를 넣고 `05 05`를 보존했다.
- v74 대비 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `msgsec03.dat`, `msgsec10.dat` 4개**다. 비대상 Patch 파일은 v74와 byte-exact다. target 한글 alias 누락/공유 충돌 0, 20칸 줄폭 gate, fixed-slot readback, 허용 범위 밖 diff=0을 통과했다.
- 완성된 v75 위에서 빌더를 다시 실행해 동일 SHA-256을 확인했고 독립 역읽기도 전 항목 PASS했다. SHA-256: `code=433B255F...7D2AB2`, `msg01=FAB633F7...F8BA6C`, `msg03=887B4D6B...B8EC31`, `msg10=5653F131...13D590`.
- 빌더=`tools/build_sangokushi2_v75_github_issue016_018_026.py`, targets=`analysis/v75_github_issue016_018_026_targets.json`, report=`analysis/v75_github_issue016_018_026_report.json`, sealed baseline=`analysis/v75_github_issue016_018_026_baseline/PatchSnapshot`.
- **v75 후속 판정:** #018의 `0x1265/0x1272` source 가정은 사용자 Citra에서 전혀 반영되지 않아 **잘못된 duplicate 수정으로 판정**됐다. v75의 나머지 #016/#017/#026 수정 이력은 유지하되, #018과 #017 세 번째 특성 설명 및 #026 물음표는 아래 v76 권위를 우선한다.

### GitHub Issue #018/#027/#028/#029 — v76-intermediate

- **현재 Patch는 v76-intermediate**다. 이번 작업은 웹 GitHub를 조회하지 않고 로컬 `Github_Issue\Issue018/027/028/029.html`과 각 `IssueNNN_assets\*.png`만 실제 화면 권위로 사용했다.
- **#018 게임 시작 전 환경설정:** v75의 `0x1265/0x1272`는 다른 duplicate였다. 실제 화면 source는 `msgsec01_0073_001 + 0074_000`의 split `確定`과 `0074_001`의 `設定ｦ完了ｼﾏｽ`, 파일 위치 **`0x1BDA / 0x1BE7`**다. 최신 목표는 **`확정 / 설정 완료함`**이다. split fragment 경계, 다음 fragment 시작, 파일 크기 9,057B를 모두 유지한다.
- **#027 포상 하단 버튼:** `code.bin` pointer table **`0x1E4890`**은 그대로 둔다. 공용 문자열을 `특산아이템`으로 재인코딩하여 기존 interior pointer가 `아이템`을 계속 가리키게 하고, 나머지를 `금 / 서적`으로 수정했다. 독립 역읽기 결과 실제 세 버튼은 **`금 / 아이템 / 서적`**이다. 아이템 도움말=`군주만 실행 가능. 능력치·충성이 크게 상승`, 서적 도움말=`비용 없음. 금·아이템보다 효과 적음`; 둘 다 명시적 줄바꿈 0개다.
- **#028 물음표:** 대표 포상 질문은 **`누구에게 줄까요？`**다. Patch가 만든 한국어 ASCII 질문부를 전수 감사해 구조적으로 1B 증가를 흡수할 수 있는 240개(`code` 18 + direct Message 222)를 stock **`？=8148`**로 정규화했다. `msgsec07` binary header의 `0x3F` 5개는 텍스트가 아니므로 보존한다. 담당군사 `0x16C9B8/0x16D1D8`의 마지막 ASCII `?`는 Citra 승인 상태이고 뒤 padding이 없어 무리하게 확장하지 않는다. `msgsec01 0x195F` 시작 설정 질문도 direct-message bulk 범위 밖이라 유지한다. 따라서 **raw `0x3F` 전역 치환은 금지**다.
- **#029 사마휘 특성 설명:** 최신 목표는 **`영지의 재야 무장 중 / 「특성」을 가진 자가 있다면 / 가급적 등용하십시오`**다. 원 `code.bin 0x1D1EB4` 84B allocation 안에 67B로 들어가며 시각 폭 `[10,14,10]`로 3줄 모두 16칸 이하이다. v75의 `적극적으로`에 쓰인 위험 alias `극=98CA`는 사용하지 않는다.
- v75 대비 변경 파일은 정확히 **7개**: `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat`, `msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec10.dat`. `font.g1t`, `fix_data.bin`, 이미지/G1T와 나머지 Patch 파일은 v75와 byte-exact다.
- 빌더를 완성 v76 위에서 재실행해 7개 게임 파일과 report SHA가 완전히 동일했고, `tools/verify_sangokushi2_v76_runtime_readback.py`도 PASS했다. SHA-256: `code=376EF36B...B577C7`, `msg01=AAF9E622...88731C`, `msg04=9D911DAA...B95CD`, `msg06=F081DAFB...07030`, `msg07=451B803E...339FA`, `msg08=99B8FD99...5E460`, `msg10=9EDDC607...C94B8`.
- 빌더=`tools/build_sangokushi2_v76_github_issue018_027_028_029.py`, targets=`analysis/v76_github_issue018_027_029_questionmarks_targets.json`, report=`analysis/v76_github_issue018_027_028_029_report.json`, audit=`analysis/v75_ascii_question_audit.json`.
- **다음 Citra 우선 검수:** (1) 게임 시작 전 환경설정의 `확정 / 설정 완료함`, (2) 포상 하단 `금 / 아이템 / 서적`과 아이템·서적 설명 한 줄 스크롤, (3) `누구에게 줄까요？` 및 다른 질문의 전각 물음표 회귀 여부, (4) 사마휘 특성 설명이 3줄 모두 보이고 `적겸적으로` 오표시가 사라졌는지 확인한다.

### 지정 이미지 31개 재삽입 — v77-intermediate

- **현재 Patch는 v77-intermediate**다. v76 Patch 33파일을 `analysis/v77_image_update_baseline/PatchSnapshot`에 먼저 봉인하고 전체 세트를 그대로 재생성한 뒤, 요청한 31개 PNG의 payload만 교체했다.
- v76에 이미 있던 `RomFS/StartMenu/start_menu_up.g1t`와 `RomFS/Tutorial/Tutorial.g1t`는 sealed v76 컨테이너가 기준이다. v76에 없던 `saveload_u.g1t`, `saveload_d.g1t`, `gallery_down.g1t`, `option_down.g1t`만 immutable Original을 최초 편입 기준으로 사용했다.
- 변경/추가 Patch 파일은 정확히 6개다: `RomFS/StartMenu/start_menu_up.g1t`, `saveload_u.g1t`, `saveload_d.g1t`, `gallery_down.g1t`, `option_down.g1t`, `RomFS/Tutorial/Tutorial.g1t`. 명시되지 않은 PNG/texture와 G1T header·비대상 payload, v76의 `code.bin/Message/font.g1t/fix_data.bin`은 byte-exact다.
- 모든 대상은 RGBA 0x09이며 원 코덱 무변경 왕복, 대상 payload 외 diff=0, 31개 PNG pixel-exact 역읽기를 통과했다. 완성 v77 위에서 재실행한 두 번째 빌드의 6개 파일 및 report SHA도 동일했고 독립 검증기도 PASS했다. GPT 이미지 생성은 사용하지 않았다.
- 빌더=`tools/build_sangokushi2_v77_image_update.py`, report=`analysis/v77_image_update_report.json`, sealed baseline=`analysis/v77_image_update_baseline/PatchSnapshot`, 독립 검증기=`tools/verify_sangokushi2_v77_image_readback.py`. v77은 아래 v78의 sealed baseline으로 계승됐다.

### GitHub Issue #011/#030 + hardcoded 대사 15×3 전수 점검 — v78-intermediate

- **현재 Patch는 v78-intermediate**다. 시작 전 v77 독립 검증기 PASS를 다시 확인한 뒤 v77 Patch 37파일 전체를 `analysis/v78_github_issue011_030_baseline/PatchSnapshot`에 봉인했다. v78 변경 파일은 정확히 **`ExeFS/code.bin` 1개**이며 v77의 이미지 6개 G1T, 모든 Message, `font.g1t`, `fix_data.bin`은 byte-exact다.
- **#011 실제 runtime source:** 요구 화면 pointer table=`0x1DC450` (`금 / 兵 糧 / 노 / 강노 / 군마`), 내놓기 화면 pointer table=`0x1DC648` (`금 / 兵糧 / 노 / 강노 / 군마 / 兵士 / 아이템`). v77에서 `금/아이템/노/강노/군마`는 이미 한글이므로 보존하고 **`0x1CFEC8=병량`, `0x1CFED0=병량`, `0x1CFBDC=병사`**만 수정했다. pointer table은 byte-exact이고 `兵 士@0x1D0804`는 이 두 화면 source가 아니므로 건드리지 않는다.
- **#030 대화창 규격:** Citra PNG에서 16글자 줄의 마지막 한 글자만 다음 줄로 밀리는 것을 근거로 `code.bin` portrait/hardcoded 대화창은 **15 visible characters/line, max 3 visual lines**로 확정했다. 동일 레이아웃으로 분류되는 현재 한국어 대사 257개를 감사했고 v77 violations=20, v78 violations=0이다.
- #030 화면의 최신 문구는 **`우수한 무장은 「전기」 / 외에도 본래 능력에 따른 / 「특성」을 지닙니다`**, **`3DS 모드에서는 전쟁 때 / 「공격」「계략」으로 다양한 / 「전기」를 쓸 수 있습니다`**, 다음 설명은 **`본래 능력과 무장 데이터의 / 4가지 속성 단계에 따라 / 사용할 수 있게 됩니다`**다. 이 3개를 포함해 초과 20개 event dialogue를 전부 각 줄 15글자 이하·3줄 이하로 재작성했다.
- direct Message `msgsec04/06/07/08`은 별도 레이아웃 계열이므로 15×3을 적용하지 않는다. v53의 `<=20 fullwidth-cell` 권위를 유지하며 v78에서는 네 파일이 v77과 byte-exact다.
- 빌더를 완성 v78 위에서 다시 실행해 `code.bin` SHA-256 `58F979B9277E2A48E3016DD03C2CA47B500862F00B20F256A77638171E1D05EB`와 report SHA-256 `3B6805E5C473185D4E7DD3CA9000D6A7D02F3CFBE430EFB3ACD5CC3464730DF7`이 동일함을 확인했다. 독립 `tools/verify_sangokushi2_v78_runtime_readback.py`도 #011 pointer/label, #030 257개/0 violation, v77 이미지 6개 hash 보존, direct Message byte-exact를 모두 PASS했다.
- 빌더=`tools/build_sangokushi2_v78_github_issue011_030.py`, targets=`analysis/v78_github_issue011_030_targets.json`, report=`analysis/v78_github_issue011_030_report.json`, audit=`analysis/v77_dialogue_layout_audit.json`, 독립 검증기=`tools/verify_sangokushi2_v78_runtime_readback.py`.
- **다음 Citra 우선 검수:** (1) #011 요구/내놓기 화면에서 `금 / 병량 / 노 / 강노 / 군마 / 병사 / 아이템`이 의도대로 보이는지, (2) #030 첫 화면의 마지막 `다`가 보이는지, (3) 두 번째 화면에서 `도` 한 글자만 별도 줄로 떨어지지 않고 3줄 전체가 표시되는지 확인한다.

### option_down 019~032 이미지 재삽입 — v79-intermediate

- **현재 Patch는 v79-intermediate**다. v78 Patch 37파일 manifest와 독립 검증을 확인하고 전체를 `analysis/v79_option_down_images_baseline/PatchSnapshot`에 봉인한 뒤 `RomFS/StartMenu/option_down.g1t` 하나만 갱신했다.
- 사용자가 명시한 `option_down_019.png`~`option_down_032.png` 14개만 사용했다. 모두 RGBA 128×32, G1T type `0x09`이며 원 source codec identity round-trip, 대상 payload 외 diff=0, Patch 역디코드 pixel-exact를 통과했다. GPT 이미지 생성은 사용하지 않았다.
- v78 대비 변경 파일은 정확히 `RomFS/StartMenu/option_down.g1t` 하나다. v78의 `ExeFS/code.bin`과 나머지 Patch 36파일, option_down header 및 비대상 payload는 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v79 위에서 재실행한 빌드의 G1T 및 report 해시가 동일했고, 독립 검증기도 14 textures / Patch 37 files PASS했다. `option_down.g1t` SHA-256=`6E154CB3DFB1FD46AFCD89142DDEF177F8FABA5BB4328B5F37AC114F591CFB09`, report SHA-256=`AE5A8887A9938E49AA5D513D6A9A3B75FAAC1305ED724656AA5A5F3EC68EAC44`.
- 빌더=`tools/build_sangokushi2_v79_option_down_images.py`, report=`analysis/v79_option_down_images_report.json`, 독립 검증기=`tools/verify_sangokushi2_v79_option_down_readback.py`. v79는 아래 v80의 sealed baseline으로 계승됐다.

### GitHub Issue #012/#013/#014/#015 특산 조건 전수 수정 + #031 — v80-intermediate

- **현재 Patch는 v80-intermediate**다. 시작 전 v79 독립 검증기 PASS를 다시 확인하고 v79 Patch 37파일 전체를 `analysis/v80_github_issue012_015_031_baseline/PatchSnapshot`에 봉인했다. v80 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개**이며 v79의 `option_down.g1t`와 다른 35개 Patch 파일은 byte-exact다.
- **특산 runtime source:** 획득조건은 `msgsec07`의 **header[20]~header[65] 46개**가 실제 화면 문구를 직접 가리킨다. Original pointer 기준 logical string을 재구성하니 `魅力80...集メ`+`ル`, `政治90以`+`上`, `会`+`稽太守`, `江夏...保`+`有セヨ`처럼 extractor fragment 경계를 가로지른 원문이 다수였다. 따라서 기존 fragment별 `translation_ko`를 특산 화면 권위로 사용하지 않는다.
- v80은 기존 v79 msgsec07 body와 비대상 header 341개를 그대로 보존하고 EOF에 46개 완전한 한국어 standalone 문장을 `05 05 05`와 함께 append한 뒤 **header[20..65]만 redirect**한다. append 증가량은 1,577B이며 모든 pointer는 16-bit 범위 안이다. target은 모두 최대 3줄/한 줄 20 units 이하, 현재 alias map/local override로 byte-exact 인코딩·역읽기된다.
- 대표 수정: `여포를 복종시켜라`; `황제의 인장을 소지하고 / 성이 유인 무장을 6명 모아라`; `업에 금 5만, 군량 50만 / 성이 조인 무장을 5명 모아라`; `충성도 100인 왕쌍을 / 천수 태수로 임명하라`; `성이 사마인 무장을 / 수춘 태수로 임명하라`; `도시를 20개 이상 보유하고 / 모든 민충을 100으로 만들어라`; `충성도 100인 관우를 / 재동 태수로 임명하라`; `강하에 군량 10만을 / 보유하라` 등이다. `司馬姓`은 특정 인물명이 아니라 **사마 성씨** 조건이다.
- **깨진 글리프 회피:** 이 특산 화면에서 `씨=98DE`가 `애`처럼 보인 실화면이 반복되므로 `~씨 성` 대신 `성이 조인/유인/진인/하후인/손인/제갈인/사마인`으로 쓴다. `옥새`의 `옥=98F5/새=9961`도 실화면에서 깨져 `황제의 인장`으로 풀어썼다. `북평`은 local `북=89D0`, `홍농`은 local `농=956B`를 사용하며 `냉정`은 far `냉=98C1`을 피한 `침착한 무장`으로 정리했다.
- **#031:** 교환 자원 버튼의 spaced `兵 士`는 `code.bin 0x1D0804`, 확인창의 `交換:`은 `0x1CEDB8`가 실제 source다. 원 C-string slot 안에서 각각 **`병사` / `교환`**으로 제자리 치환했고 다른 code.bin byte는 v79와 동일하다.
- 결정성: 완성 v80 위에서 빌더를 재실행해 `code.bin`, `msgsec07.dat`, report SHA가 동일했다. 독립 `tools/verify_sangokushi2_v80_runtime_readback.py`도 46/46 pointer/target, old body, non-target header, `북=89D0/농=956B`, #031 두 C-string, v79 option_down SHA를 모두 PASS했다. SHA-256: `code=6813816A...022F61`, `msg07=14CFCEA2...F1240D`, `report=FCBB10D8...BA9B00`.
- 빌더=`tools/build_sangokushi2_v80_github_issue012_015_031.py`, targets=`analysis/v80_github_issue012_015_031_targets.json`, report=`analysis/v80_github_issue012_015_031_report.json`, 독립 검증기=`tools/verify_sangokushi2_v80_runtime_readback.py`.
- **다음 Citra 우선 검수:** #012의 여포/황제의 인장/북평, #013의 성씨·왕쌍/관우 태수 조건, #014의 업·조 성씨/매력80/사마 성씨, #015의 홍농/20도시·민충/정치90/강하 군량, #031의 `병사`와 `교환`을 확인한다. 그 다음 특산 조건을 전부 넘겨보며 46개 전체에 일본어/깨진 글자/문장 연결 오류가 없는지 확인한다.

### GitHub Issue #032/#033/#034 보고서·시나리오 제목·인물/특기 — v81-intermediate

- **현재 Patch는 v81-intermediate**다. 시작 전 v80 report manifest와 실제 Patch 37파일이 완전히 일치함을 확인하고 `analysis/v81_github_issue032_034_baseline/PatchSnapshot`에 v80 전체를 봉인했다. v81 대비 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Scenario/fix_data.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec11.dat` 7개**다. v80의 특산 획득조건 46개 bank, v79 option_down 이미지, 기타 30개 Patch 파일은 byte-exact다.
- **#032 보고서 첫 문장:** `msgsec06`은 flat-header 파일이며 실제 source는 header[21]이다. v80 target에는 동적 군주명 토큰 `02 01 C8` 중 `C8`이 빠지고 `네님`이 들어가 있었으므로 화면 앞부분이 깨졌다. v81은 기존 body를 보존한 채 EOF에 **`02 01 C8 + 님, %s의\n보고서가 도착했습니다`**를 append하고 header[21]만 새 pointer로 전환했다. 비대상 header word와 old body는 byte-exact다.
- **#032 보고 항목 순서 권위:** 사용자가 지정한 순서를 그대로 **`전쟁준비 / 훈련 / 수색 / 토지개발 / 경작 / 치수 / 상업투자 / 투함건조 / 몽충건조 / 주가건조`**로 적용했다. 실제 code source는 `0x1CF9FC`, `0x1CFA08`, `0x1CF9F0`, `0x1CF9C0`, `0x1CF9E4`, `0x1CF9CC`, `0x1CF9D8`, `0x1CFDE0`, `0x1CFDEC`, `0x1CFDD4`이며, 첫 항목은 `준비`가 아니라 반드시 **`전쟁준비`**다.
- **#033 시나리오 제목:** 실제 군주선택 source는 `msgsec11`의 segment9/10이다. v62 fixed-fragment 패치로 이 구간이 ordinary row identity와 달라졌으므로 v81은 현재 v80의 segment payload와 separator를 권위로 삼아 **기존 segment capacity를 유지하는 구조 보존 재구성**을 사용했다. 현재 10개 제목은 `황건이 흥하니 영웅이 청운을 품다`, `영제의 몰락, 동탁의 학정은 극에`, `천하가 어지러워 군웅이 할거하다`, `유비, 자복하여 신야성에 의탁하다`, `와룡은 천하를 바라본다`, `손권이 독립하여 삼국이 정립됨`, `강유, 떠난 공명의 뜻을 계승한다`, `조조, 헌제를 옹립하고 대두`, `유비, 봉추를 동반하여 입촉하다`, `사마의, 5로를 통해 촉을 침공하다`다. 시나리오6은 원래 `msgsec11_0009_008 + 0010_000`으로 경계를 가로지르므로 그 경계만 새 길이에 맞게 조정했고, 앞의 군주선택 문구와 뒤의 다른 시나리오는 byte-exact다.
- **#034 이름 alias:** `겸=989F`, `총=989B`는 정적 font readback은 가능했지만 Citra 이름 renderer에서 실패한 far alias다. 실제 이름 테이블 전수 감사 결과 `겸`은 **송겸·도겸·상삼겸신·직강겸속 4명**, `총`은 **향총·만총 2명**이 공유한다. v81은 v80 effective runtime 전체에서 참조 0인 low-risk alias **`겸=965A/physical3116`, `총=9754/physical3298`**를 local runtime alias로 사용하며 6명 이름 모두를 재인코딩했다. 아래쪽 인접 physical 3189/3371은 top guard row를 비워 vertical bleed를 막았고 packed font alpha readback을 통과했다. old `989F/989B`는 이름 필드에서 0회다.
- 이름 밖 실제 한국어 문자열도 일관성을 위해 최소 수정했다. `code.bin`의 `도겸 공` 한 곳, `msgsec04/msgsec07`의 `총대장` 두 곳만 새 alias로 재인코딩했으며, `code.bin`에서 우연히 명령/데이터 바이트로 나타나는 `989B` 3곳(`0x281D/0x1C628F/0x1C7647`)은 byte-exact 보존했다.
- **#034 특기 버튼:** 실제 pointer table은 `code.bin 0x1DC300~0x1DC3AC`, 총 **44개**다. Original source와 NDS 한국어판 동일 순서를 대조하여 전부 한글화했다. 특히 화면의 `[誘면]`은 Original `0x1CF090 = 97 55 88 F8 = 誘引`이고 NDS 대응도 **`유인`**이므로 `유인`으로 확정했다. `落穴`의 NDS 번역 `낙혈`은 사용자가 승인한 자연스러운 **`함정`**으로 적용했다. 나머지는 `전투/불의/돌격/견제/일기/수반/탄반/일기/기돌/용병/월벽/위협/일제/제사/문사/화시/저격/연노/병법/진화/화계/복병/위령/동토/논파/대화/장악/도발/설득/치료/고무/유인/기도/충차/생존/당천/선독/연사/수신/회심/인내/가호/반격` 순이며 index25만 `함정`이다.
- 빌더를 완성 v81 위에서 다시 실행해 7개 게임 파일과 report 결과가 동일했다. 독립 `tools/verify_sangokushi2_v81_runtime_readback.py`도 v80 baseline 보존, #032 `02 01 C8` 및 10개 순서, #033 10개 제목과 경계, #034 font/fix_data/6명 이름/44개 특기/`誘引→유인`/`落穴→함정`, accidental code pairs 보존을 전부 PASS했다.
- 주요 SHA-256: `code.bin=BBB971E99B7E026041A310CD38E0AD545FD292DCC8F1EA877262736C94BD860A`, `font.g1t=0385F4CDEE621C060BF9A187C0EE26601226F48B3C0F167B5F27D5DFAF5E160E`, `fix_data.bin=F5EA6F24CD06837F99036217B50D634A320460E2105C0C2C7AC4E3510205E44F`, `msgsec04=94F141BEA412E8FC77E044EE1C10534564FD55B5227D46620112A814C10CE934`, `msgsec06=7FF1202FC1F82045233730A041AE545683F4E6EC818B2E7FAE53FF8AF90F2C1A`, `msgsec07=A9E78DA3313341BE28629DE63FB9B3403D2E0D2452742168B2CD20BDD1D1B36A`, `msgsec11=B6B2970C5CF74105634B207CB3562C48C99E661D0ED97F0CFAE02881BB23D03F`.
- 빌더=`tools/build_sangokushi2_v81_github_issue032_034.py`, targets=`analysis/v81_github_issue032_034_targets.json`, report=`analysis/v81_github_issue032_034_report.json`, 독립 검증기=`tools/verify_sangokushi2_v81_runtime_readback.py`.
- **다음 Citra 우선 검수:** #032 보고서 첫 문장에 군주명이 정상 표시되는지와 10개 항목 순서/폭, #033 군주선택 화면의 10개 시나리오 제목이 한 줄 버튼 안에 표시되는지, #034 `도겸/향총` 및 나머지 `겸/총` 공유 이름과 특기 44개(특히 `유인`, `함정`)를 확인한다.

### 지정 이미지 35개 재삽입 — v82-intermediate

- **현재 Patch는 v82-intermediate**다. v81 report와 독립 검증을 확인하고 v81 Patch 37파일 전체를 `analysis/v82_image_update_baseline/PatchSnapshot`에 봉인한 뒤 요청한 35개 PNG를 16개 G1T에 반영했다.
- sealed v81 기준 컨테이너는 `RomFS/StartMenu/option_down.g1t`, `RomFS/EditBushou/edit_bushou_base_menu_down.g1t` 2개다. v81에 없던 `Opening/Caution`, `Opening/ktlogo`, 나머지 EditBushou 5개, Duel 7개 등 14개 컨테이너만 immutable Original을 최초 편입 기준으로 사용했다.
- 대상은 모두 RGBA type `0x09`이며 source codec identity round-trip, 대상 payload 외 diff=0, Patch 역디코드와 35개 PNG pixel-exact를 통과했다. GPT 이미지 생성은 사용하지 않았다.
- v81 대비 변경/추가 Patch 파일은 정확히 16개 G1T이며 Patch 총 파일 수는 37→51이다. v81의 `code.bin/font.g1t/fix_data.bin/Message`와 비대상 G1T/texture/header/payload는 byte-exact이고 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v82 위에서 빌더를 재실행해 16개 컨테이너와 report 해시가 동일했다. report SHA-256=`E9374E30F3E0C8BF105CF44B7015C17D6739E3289ED42B7A7BD6729A17853999`; 독립 검증기는 16 containers / 35 textures / Patch 51 files PASS했다.
- 빌더=`tools/build_sangokushi2_v82_image_update.py`, report=`analysis/v82_image_update_report.json`, 독립 검증기=`tools/verify_sangokushi2_v82_image_readback.py`. 다음 단계는 Patch 전체 51파일을 Rebuild에 복사한 뒤 Citra 실화면 확인이다.

### v83~v87 후속 runtime 기준 계승

- v83~v87은 v82의 이미지 16개 G1T를 보존하면서 GitHub #035/#036/#045/#046/#047의 대사·시나리오 제목·날짜·alias·Crash를 순차 수정했다. 최신 구조 권위는 v86의 hardcoded 대사 실제 ADR 시작주소와 ARM branch 10개 byte-exact 복구다.
- v87은 v86 전체 Patch에서 runtime `병량` 50회를 동일 길이 `군량`으로 제자리 치환했다. 변경 파일은 `code.bin`, `msgsec07.dat`, `msgsec10.dat`, `msgsec15.dat`이며 최종 Patch에서 `병량` 잔존은 0회다. 빌더=`tools/build_sangokushi2_v87_bingliang_to_gunryang.py`, report=`analysis/v87_bingliang_to_gunryang_report.json`이다.

### 지정 이미지 36개 재삽입 — v88-intermediate

- **현재 Patch는 v88-intermediate**다. v87 report manifest와 실제 Patch 51파일 일치를 확인하고 전체를 `analysis/v88_image_update_baseline/PatchSnapshot`에 봉인한 뒤 요청한 36개 PNG를 8개 G1T에 반영했다.
- sealed v87 기준 컨테이너는 `RomFS/Opening/ktlogo.g1t`, `RomFS/Tutorial/Tutorial.g1t` 2개다. v87에 없던 `StaffRoll/staff_roll.g1t` 및 Stg 5개 컨테이너는 immutable Original을 최초 편입 기준으로 사용했다.
- 대상은 모두 RGBA type `0x09`이며 source codec identity round-trip, 대상 payload 외 diff=0, Patch 역디코드와 36개 PNG pixel-exact를 통과했다. GPT 이미지 생성은 사용하지 않았다.
- v87 대비 변경/추가 파일은 정확히 8개 G1T이며 Patch 파일 수는 51→57이다. v87의 `code.bin/font.g1t/fix_data.bin/Message`와 비대상 G1T/texture/header/payload는 byte-exact이고 Original/Rebuild/Backup은 수정하지 않았다.
- 완성 v88 위에서 빌더를 재실행해 8개 컨테이너와 report 해시가 동일했다. report SHA-256=`A1D05421198834540537255D0D540D12AC8A483CDC66F0A474CC77A75647D2EE`; 독립 검증기는 8 containers / 36 textures / Patch 57 files PASS했다.
- 빌더=`tools/build_sangokushi2_v88_image_update.py`, report=`analysis/v88_image_update_report.json`, 독립 검증기=`tools/verify_sangokushi2_v88_image_readback.py`. 다음 단계는 Patch 전체 57파일을 Rebuild에 복사한 뒤 Citra 실화면 확인이다.

### 특기 `単騎→단기` + GitHub Issue #037 — v89-intermediate

- **현재 Patch는 v89-intermediate**다. 정확한 v88 Patch 57파일을 `analysis/v89_trait_issue037_baseline/PatchSnapshot`에 봉인한 뒤 세 파일만 수정했다: `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Scenario/fix_data.bin`. 나머지 v88 파일은 byte-exact다.
- compact 특기 index7의 Original `単騎`는 최신 권위 **`단기`**다. `code.bin 0x1CFA8C`에서 동일 4바이트로 치환하며 pointer table `0x1DC300..0x1DC3AC`은 바꾸지 않는다. `一騎`는 계속 **`일기`**, 장문 `単騎駆け`는 이미 적용된 **`단기돌진`**을 유지한다.
- #037 이름 renderer에서 `예=989D`는 사용하지 않는다. 표시 이름 중 `예`가 들어간 **record 246/306/697/700/712**(조예·조예·예형·전예·두예)는 모두 안전 local alias **`예=927D/physical2399`**로 인코딩한다. 가나 독음은 절대 번역하거나 수정하지 않는다.
- #037 `겸=965A/physical3116`과 `예=927D/physical2399` glyph는 Sam3KRFont의 정상 기준선을 그대로 사용하며 1px 아래로 이동하지 않는다. v81에서 `겸`과 함께 이동한 physical3189는 sealed v80 셀로 복원한다. 이 규칙은 v81의 “top guard를 위해 donor를 1px 아래 이동” 기록을 **대체**한다.
- 빌더를 2회 실행해 결정성을 확인했고 독립 역읽기 PASS다. SHA-256은 `code=2855FEEB...343C3E`, `font=148FAE23...46D368`, `fix_data=8C38C65A...5FDC78E`다. 빌더=`tools/build_sangokushi2_v89_trait_issue037.py`, targets=`analysis/v89_trait_issue037_targets.json`, report=`analysis/v89_trait_issue037_report.json`, 검증기=`tools/verify_sangokushi2_v89_runtime_readback.py`.
- 다음 Citra 검수는 특기 배열의 `一騎=일기 / 単騎=단기`, 군주선택의 조예, 도겸 및 다른 `예/겸` 공유 이름의 글자 형태와 기준선이다.

### GitHub Issue #038 / #040 / #041 — v90-intermediate

- **현재 Patch는 v90-intermediate**다. exact v89 Patch 57파일을 `analysis/v90_github_issue038_040_041_baseline/PatchSnapshot`에 봉인한 뒤 `ExeFS/code.bin`, `RomFS/Message/msgsec01.dat` 두 파일만 수정했다. 나머지 55개 Patch 파일은 v89와 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- #038의 수량 formatter `0x16084C = 몇백 명을 %s합니까？`는 이미 정상 한글이므로 절대 다시 번역하지 않는다. `%s`에 들어가는 별도 runtime 명칭은 **`0x1CFE88 徴兵→징병`, `0x1CFE98 募兵→모집`**이며 두 슬롯 모두 4B 동일 길이 제자리 치환이다.
- #040 무장편집 상단 설명은 `msgsec01.dat`의 **fixed byte range `0x3CD~0x471`**이 실제 source다. v90의 8개 권위 문구는 `신규등록 / 신무장 만들기 / 내용변경 / 작성한 신무장의 설정을\n변경합니다 / 삭제 / 신무장 삭제 / 무장편집 / 사실무장의 내용 편집`이다. 파일 크기 9057B와 fixed start/separator를 유지하고, 마지막 슬롯 tail `0A0A918A90` 및 **`0x472` 이후 전체는 v89 byte-exact**로 보존한다.
- #041의 v90 `code.bin 0x1CF4A1` source 판정은 **Citra에서 상업투자 화면이 전혀 바뀌지 않아 실패로 폐기**됐다. 해당 16B는 v91에서 sealed v89 원문으로 복원한다. 실제 후속 후보는 아래 v91 `msgsec17.dat` header[1] 경로다.
- v90 빌더를 완성본 위에서 2회 실행해 동일 SHA를 확인했다. 독립 검증기는 허용 범위 밖 diff=0을 PASS했다. SHA-256: `code=69512B14...9083EF`, `msgsec01=E34BE577...8C16F7`. 빌더=`tools/build_sangokushi2_v90_github_issue038_040_041.py`, targets=`analysis/v90_github_issue038_040_041_targets.json`, report=`analysis/v90_github_issue038_040_041_report.json`, verifier=`tools/verify_sangokushi2_v90_runtime_readback.py`.
- v90 Citra 후속 결과: #040의 `삭제`는 `삭=98CD`가 `곡`으로 출력되어 **`곡제`**, 설명은 `신무장 곡제`로 나타났다. #041은 일본어 `よろしいですか？`가 그대로였다. 따라서 아래 v91로 후속 수정한다.

### Issue #040 / #041 Citra 후속 — v91-intermediate

- **현재 Patch는 v91-intermediate**다. exact v90 Patch 57파일을 `analysis/v91_issue040_041_runtime_fix_baseline/PatchSnapshot`에 봉인했고, 변경 파일은 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat`, `RomFS/Message/msgsec17.dat` 4개다. 나머지 53파일은 v90 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- #040 `삭=98CD/physical3606`은 폰트 셀 자체는 정상인데 Citra에서 `곡`으로 렌더링되는 far-alias 실패다. fresh original-valid alias **`삭=9956/physical3676`**을 이 fixed block에서만 local override로 사용한다. 과거 alias 기록 0, v90 effective runtime raw ref 0, global owner 0이고 바로 아래 physical3749 top-row ink=0이다. font는 physical3676만 수정하며 Citra 승인 전까지 전역 alias로 승격하지 않는다.
- #040 제목은 local alias로 **`삭제`**를 유지한다. 사용자 희망 `신무장을 삭제합니다.`는 20B라 원 14B slot 및 안전 1B separator 차용 상한 15B를 넘는다. 따라서 설명은 **`신무장 삭제하기` = 15B**로 적용하고 원 `05 05 05` 중 첫 1B만 차용해 `05 05`를 남긴다. 다음 fixed start **`0x447`과 이후 bytes는 v90 byte-exact**다.
- #041의 실제 후속 후보 `RomFS/Message/msgsec17.dat`는 71B이고 header words가 **`[4,10,23,40]`**이다. header[1]=10이 독립 `ﾖﾛｼｲﾃﾞｽｶ？`를 직접 가리킨다. 기존 body는 유지하고 EOF byte71에 **`진행하시겠습니까？`(18B)+`05 05 05`**를 append한 뒤 header[1]만 `10→71`로 redirect했다. 새 header는 `[4,71,23,40]`, 파일 크기는 92B다. v90의 잘못된 `code.bin 0x1CF4A1` 수정은 sealed v89 원문으로 복원했다.
- 빌더 2회 결정성 및 독립 readback PASS. SHA-256: `code=62EC99B0...A4519B`, `font=4F58C519...5C7D13`, `msgsec01=96B15D3C...E0E32F`, `msgsec17=1FF3E57A...8931FA`. 빌더=`tools/build_sangokushi2_v91_issue040_041_runtime_fix.py`, targets=`analysis/v91_issue040_041_runtime_fix_targets.json`, report=`analysis/v91_issue040_041_runtime_fix_report.json`, verifier=`tools/verify_sangokushi2_v91_runtime_readback.py`.
- v91 Citra 후속 결과: #040의 `삭=9956`도 한자형으로 표시되어 fresh far-alias 방식이 다시 실패했다. 이 실패를 기준으로 아래 v92에서 low-lead alias로 재보정한다. #041은 v92 비대상으로 그대로 계승하며 아직 Citra 재확인 대상이다.

### GitHub Issue #040 / #042 / #043 — v92-intermediate

- **현재 Patch는 v92-intermediate**다. exact v91 Patch 57파일을 `analysis/v92_github_issue040_042_043_baseline/PatchSnapshot`에 봉인했고, 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec01.dat` 3개다. 나머지 54파일은 v91 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- #040: v91 `삭=9956`이 Citra에서 한자형으로 표시되어 실패 확정. 첫 low-lead 후보 `9645`는 바로 아래 atlas cell top-row ink가 있어 **Patch write 전에 gate가 중단**했다. 최종 local alias는 **`삭=9554/physical2922`**로 선택했다. exact v91 runtime raw ref=0, 현재 effective map owner=0, Original-valid이며 바로 아래 physical2995 top-row ink=0이다. 실패한 physical3676은 sealed v90 상태로 pixel-exact 복원했다. `msgsec01`은 `삭제 / 신무장 삭제하기` 문구를 유지하며 `0x447` 이후는 v91 byte-exact다.
- #042: 로컬 `Issue042.html`과 저장 PNG를 확인해 미번역 hardcoded source를 `code.bin 0x1801DC="%s様、"`, `0x1801E4="%s副軍師の%sが\n意見があるそうです"`로 확정했다. 각각 **`%s님, `**, **`%s부군사 %s,\n의견이 있다고 합니다`**로 원 6B/33B 슬롯에 동일 길이 치환했다. 뒤 `0x180208`의 기존 **`들으시겠습니까？`**는 v55 정상 번역이므로 보존했다.
- #043: `code.bin 0xAB42C`의 `%s님, 어찌합니까？`를 원 20B에 정확히 맞는 **`%s님, 어떻게할까요？`**로 수정했다. 공통 `いただきたい`의 잘못된 `받고 싶다@0x1D0968`는 네 외교 요구에 공통으로 붙는 종결어이므로 **` 주길 바란다`**로 동일 12B 치환했다. 문법 연결을 위해 `동맹을\n맺고→동맹을\n맺어`, `서 병을\n물리고→서 병을\n철수해`만 함께 수정했고 `교환을\n승낙해`, `로 함께\n쳐들어가`는 보존했다. 관련 pointer table entries는 전부 byte-exact다.
- v92 빌더를 완성본 위에서 2회 실행해 SHA가 동일했고, 독립 `tools/verify_sangokushi2_v92_runtime_readback.py`는 v91 baseline manifest, code 허용 span, #043 pointer tables, #040 font physical2922/3676 두 셀, `msgsec01 0x447` 이후 보존, 비대상 54파일 보존을 모두 PASS했다. 허용 범위 밖 diff=0이다.
- SHA-256: `code.bin=BD34F746F220EC86F9F1692FC0AC6F04A9294865C77CEDCE42EE1AF154ACE52E`, `font.g1t=A20F8A02D4EFE526ED7EC6C0AE086EAF8698F3FAA6DE2A1897D78665E2E74000`, `msgsec01.dat=FA9612A8FC2B60B1D5ECA5A526BE7594F6F90FDA2406FE370BBC69EC0D267A3A`.
- 빌더=`tools/build_sangokushi2_v92_github_issue040_042_043.py`, targets=`analysis/v92_github_issue040_042_043_targets.json`, report=`analysis/v92_github_issue040_042_043_report.json`, verifier=`tools/verify_sangokushi2_v92_runtime_readback.py`.

### 특기 오역 / GitHub Issue #048/#049/#050 — v93-intermediate

- **현재 Patch는 v93-intermediate**다. exact v92 Patch 57파일을 `analysis/v93_trait_issue048_050_baseline/PatchSnapshot`에 봉인했고, 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec09.dat` 3개다. 비대상 54파일과 Original/Rebuild/Backup은 수정하지 않았다.
- 특기 최신 권위는 **`受流=수류`, `軍学=군학`**이다. 기존 `수반/병법`은 폐기하며 `특기.html`도 v93 Patch 실제 code SHA와 일치한다.
- #048 최종 표시는 사용자가 허용한 2안 **`쌀을 얼마나 줍니까？`**다. formatter `0x154F48`과 pointer `0x1E5250`은 보존하고 action slot `0x1D1500`만 `주기→줍`으로 수정했다.
- #049의 `보持` 형태는 font/glyph 오류가 아니라 `報告=8D90`의 포인터 경계 분할 오류다. live header[142]은 완결된 **`*개월 후에 보고할 수 있을 / 것 같습니다`** parent를 가리키며 dangling lead byte가 없다. 기존 msgsec07 body와 비대상 header는 byte-exact다.
- #050은 **군주명에 손대지 않았다.** live msgsec09 header[0] parent 안의 `軍 / ﾊ敗ﾚﾏｼﾀ`만 **`군은 / 패배했습니다`**로 바꿨다. 따라서 조립 결과는 `[기존 군주명]군은 / 패배했습니다`다. 기존 body와 header[1..87]은 byte-exact다.
- 결정성 재빌드와 독립 역읽기 PASS. SHA: `code=ACD60947...7521E0D`, `msg07=A8FA9930...B9F327`, `msg09=76266B3B...EA53A5`, report=`8DA1094E...FD44EC`.
- 빌더=`tools/build_sangokushi2_v93_trait_issue048_050.py`, targets=`analysis/v93_trait_issue048_050_targets.json`, report=`analysis/v93_trait_issue048_050_report.json`, verifier=`tools/verify_sangokushi2_v93_runtime_readback.py`. **#049의 header[142] source 판정은 사용자 Citra에서 실패해 아래 v94 권위로 대체한다.**

### GitHub Issue #049 재수정 + #051/#052 — v94-intermediate

- **현재 Patch는 v94-intermediate**다. exact v93 Patch 57파일을 `analysis/v94_issue049_051_052_baseline/PatchSnapshot`에 봉인했고, 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec07.dat` 2개다. 나머지 55파일은 v93 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- **#049:** 사용자 Citra 화면의 `3개월 후에 보[한자]할 수 있을 / 것 같습니다`가 v93 `msgsec07.dat 0x2D83`의 기존 body 바이트와 정확히 일치했다. 이 span은 `보 + 0x8D + 고할...` 형태라 `0x8D`가 다음 한글과 잘못 결합한다. v94는 header를 전혀 움직이지 않고 **0x2D83의 46B span만** `*개월 후에 보고할 수 있을 / 것 같습니다`로 제자리 재작성한다. 1B 감소분은 separator 직전 ASCII space로 보충해 파일 크기 18,705B와 모든 후속 주소를 유지한다. msgsec07 388-word header 전체와 이 span 밖 body는 v93 byte-exact다.
- **#051:** 월 보고서 결과의 hardcoded `code.bin 0x152328="兵士士気%s"`를 **`병사사기%s`**로 동일 10B 치환하고, 같은 보고서 계열 공용 label `0x1CFA1C="兵士士気"`도 **`병사사기`**로 동일 8B 치환한다. label pointer `0x1AB378`은 byte-exact다.
- **#052:** 외교 조건 화면의 `同盟:금 ...`에서 공용 C-string `code.bin 0x1CEE18="同盟"`을 **`동맹`**으로 동일 4B 치환한다. pointer entries `0x1AB394 / 0x1DBAC0 / 0x1E50C8`은 모두 byte-exact다.
- 결정적 빌드 2회와 독립 verifier PASS. SHA-256: `code.bin=7E04CE4E9B7E7C47C1E5BCD06C26070DEF416C5FEDDD357EA7719D16E2B9FB39`, `msgsec07.dat=E831421B13AB1AE16AA6588DE07C2CE463720AD93A636AEBCCE232F26BB8E630`, report=`491591CA8ECA2A12BC4098C1765636889F53E7AF556557F895615200C80C685F`.
- 빌더=`tools/build_sangokushi2_v94_issue049_051_052.py`, targets=`analysis/v94_issue049_051_052_targets.json`, report=`analysis/v94_issue049_051_052_report.json`, verifier=`tools/verify_sangokushi2_v94_runtime_readback.py`.
- v94는 아래 v95의 sealed baseline으로 계승된다.

### GitHub Issue #056/#057/#058/#059 — v95-intermediate

- **현재 Patch는 v95-intermediate**다. exact v94 Patch 57파일을 `analysis/v95_issue056_059_baseline/PatchSnapshot`에 봉인했고, 변경 파일은 정확히 `RomFS/Message/msgsec05.dat`, `RomFS/Message/msgsec07.dat` 2개다. 나머지 55파일은 v94와 byte-exact이며 Original/Rebuild/Backup은 수정하지 않았다.
- **#056:** `msgsec07 header[262]`의 네 번째 결과 fragment가 Citra의 `失敗です, ...` 화면과 일치했다. 앞 3개 fragment는 byte-exact로 유지하고 마지막만 **`실패했습니다. [동적 무장명]에게 / 간파당했습니다`**로 바꾼 새 group을 EOF에 append해 header[262]만 redirect했다. 동적 이름은 `02 01 C8` 전체를 보존한다.
- **#057:** 기존 header[372] target은 `02 01 + 네와 / 02 02 + 네가`로 `C8`이 누락돼 있었다. 최신 target은 사용자 지정 **`[군주명]군과 [군주명]군이 동맹을 / 맺은 모양입니다`**이며, 두 토큰을 `02 01 C8 / 02 02 C8`로 복구해 EOF standalone + header[372] redirect로 적용했다.
- **#058:** 사용자 후속 요청에 따라 조사 회피를 위해 군주명 뒤에 `군은`을 붙인다. 방랑은 **`[군주명]군은 방랑길에 나섰습니다`**(header[374]), 거병은 **`[군주명]군은 %s에서 / 거병했습니다`**(header[369])가 최신 권위다. 두 target 모두 `02 01 C8`을 완전 보존한다.
- **#059:** `msgsec05 header[15] @0x197`의 `...何者...密談...`이 실제 Citra source다. 최신 target은 **`[군주명]님, %s의 [무장명]이(가) / 누군가와 밀담 중인 듯합니다`**이며 `02 01 C8 / 02 02 C8`을 보존한다. EOF standalone + header[15] redirect를 사용한다. 요청된 exact 중복 감사 결과 `何者`/`密談`은 v94 Patch 전체에서 각각 1회, 모두 이 같은 old physical fragment에만 존재한다. old body는 안전을 위해 byte-exact로 남기지만 live header는 더 이상 그 일본어를 가리키지 않는다.
- msgsec07 기존 18,705B body는 byte-exact이고 바뀐 header는 **262/369/372/374**뿐이다. msgsec05 기존 653B body도 byte-exact이고 바뀐 header는 **15**뿐이다. 결정적 빌드 2회와 독립 verifier PASS.
- SHA-256: `msgsec05.dat=25C29E9E32B25E9960A042601D62B03FC3F609602C7B8DEA7E9CD7531A213854`, `msgsec07.dat=BF864B1333775F87DF764612BD604E172900C4AAE2105D261347A0E28FA6C5E0`, report=`F4805168D5D6C56012B8EA82699E7BD4A4AE52334F395D0340A56C6584BF1E54`.
- 빌더=`tools/build_sangokushi2_v95_issue056_059.py`, targets=`analysis/v95_issue056_059_targets.json`, report=`analysis/v95_issue056_059_report.json`, verifier=`tools/verify_sangokushi2_v95_runtime_readback.py`.
- 사용자 Citra 후속 결과: **거병은 `[군주명]군은 낙양에서 / 거병했습니다`로 정상 반영**, #056과 방랑은 기존 혼합 출력 그대로여서 v95의 두 header redirect는 실패 source로 판정한다. #057은 현재 재현/확인 불가이며 재발 시 새 Report를 기준으로 다시 추적한다. #059는 이 후속에서 별도 확인하지 않았다.

### #056 / #058 방랑 Citra 실제 source — v96-intermediate

- **현재 Patch는 v96-intermediate**다. exact v95 Patch 57파일을 `analysis/v96_issue056_wandering_runtime_fix_baseline/PatchSnapshot`에 봉인하고 `RomFS/Message/msgsec07.dat` 1개만 수정했다. 나머지 56파일은 v95와 byte-exact다.
- #056은 실제 Citra 출력이 v95 물리 body **`0x2597`**의 35B `失敗ﾃﾞｽ, + 02 01 + 네에게 / 간파당했습니다`와 정확히 일치했다. header[262]의 v95 EOF target은 런타임에서 사용되지 않는 것으로 보고, **0x2597 자체를 정확히 35B `실패했습니다. / 02 01 C8 + 에게 들켰습니다`로 제자리 치환**했다. 파일 크기와 모든 header를 유지한다.
- #058 방랑은 실제 Citra 출력이 v95 물리 body **`0x33E8`**의 33B `02 01 + 네는 방 랑의 길을 떠났습니다`와 일치했다. 이를 **`02 01 C8 + 군은 방랑길에 나섰습니다.`**로 제자리 치환하고 2B 감소분은 separator 직전 ASCII space 2B로 보충했다. 따라서 뒤 주소는 이동하지 않는다.
- `msgsec07` 388-word header 전체는 v95와 byte-exact다. 성공한 거병 header[369], 미확인 #057 header[372], 실패했지만 남겨둔 stale header[262]/[374]도 값 자체는 v95 그대로다. `msgsec05`의 #059와 다른 모든 Patch 파일 역시 v95 byte-exact다.
- SHA-256: `msgsec07.dat=356250780042973FCC23168FF7BBC24E42419E28C73FF9503C8ADC3EC3033472`.
- 빌더=`tools/build_sangokushi2_v96_issue056_wandering_runtime_fix.py`, targets=`analysis/v96_issue056_wandering_runtime_fix_targets.json`, report=`analysis/v96_issue056_wandering_runtime_fix_report.json`, verifier=`tools/verify_sangokushi2_v96_runtime_readback.py`.
- **다음 Citra 최우선 확인은 #056과 방랑 두 화면뿐이다.** 거병은 v95 Citra 성공 상태로 동결한다. #057은 재현 시 그때 새 Report를 기준으로 추적한다.

## v100-intermediate 이미지 9종 리빌드 (2026-09-01)

- 기준은 exact v99 전체 Patch 57개 파일이며 analysis/v100_image_update_baseline/PatchSnapshot에 봉인했다. start_menu_up.g1t는 봉인 v99에서 갱신하고, v99에 없던 Common/Info 8개 G1T는 immutable Original에서 최초 편입했다.
- 반영 대상은 정확히 start_menu_up_015.png, BG_InfoB_NEW_000.png, BG_InfoB_000.png, BG_InfoBHex_000.png, BG_InfoBHex_NEW_000.png, BG_InfoI_000.png, BG_InfoT_000.png, BG_InfoT_HOUROU_000.png, BG_InfoT_KOUSEN_000.png 9개다.
- Patch 변경 G1T는 정확히 RomFS/StartMenu/start_menu_up.g1t와 RomFS/Common/Info/{BG_InfoB_NEW,BG_InfoB,BG_InfoBHex,BG_InfoBHex_NEW,BG_InfoI,BG_InfoT,BG_InfoT_HOUROU,BG_InfoT_KOUSEN}.g1t다. 요청 외 BG_InfoB_C.g1t, BG_InfoK.g1t는 추가하지 않았다. Patch 파일 수는 57→65다.
- start_menu_up_015는 texture[15] 0x09 RGBA8 512x128; Info 8종은 texture[0] 0x45 RGB8 512x256이다. 9/9 codec identity, header/비대상 payload byte-exact, pixel-exact 역읽기, v99 비대상 57파일 byte-exact를 통과했다.
- 완성본 위 빌더 2회 결과가 동일했고 독립 verifier도 PASS했다. 빌더=tools/build_sangokushi2_v100_image_update.py, report=analysis/v100_image_update_report.json (SHA-256 60DF827A42D877092DF02C1AC6EE4A000D011EE9E9BEDB107E75E75FEB279ECD), verifier=tools/verify_sangokushi2_v100_image_readback.py.
- 텍스트, code.bin, Message, font, fix_data는 수정하지 않았다. 이미지 생성은 사용하지 않았다. **v100-intermediate는 v101의 sealed base이며 이미지 9종 권위로 그대로 보존된다.**

## v101-intermediate #054 / #061 / #063 runtime 정리 (2026-09-01)

- exact v100 전체 Patch 65개 파일을 `analysis/v101_issue054_061_063_baseline/PatchSnapshot`에 봉인했다. 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat`, `msgsec09.dat` 6개다. `font.g1t`, `msgsec03.dat`, v100 이미지 9종과 그 밖의 파일은 byte-exact다.
- **#054 실제 하단 source 확정:** 사용자 스크린샷에 남은 `兵数 / 騎馬 / 歩兵` 계열은 v99가 고친 `msgsec03.dat`가 아니라 `code.bin` 공통 UI 문자열 테이블을 읽는다. v101은 `0x1CFD78 兵数→병력`, `0x1CFE80 歩 兵→보 병`, `0x1CFBE4 騎 馬→기 마`, `0x1CFBF4 騎馬→기마` 네 slot만 exact-size 치환한다. 사용자 승인 #054 상단 `msgsec07 header[316]`은 동결한다.
- **#061 safe-alias 감사:** code 대사 282개와 활성 direct Message를 다시 검사해 과거 Citra 실패 이력이 있는 old alias를 현재 검증된 셀로 바꿨다. 현재 v100 font에서 pixel-exact를 다시 확인한 값은 `북 98DD→89D0(physical789)`, `헌 98A0→8972(696)`, `촉 989A→8D6F(1445)`, `찌 9968→9179(2207)`, `룡 98B5→92DE(2495)`다. code 16개 위치와 Message 10개 물리 위치(+msg06 1개)를 byte-local 치환했으며 빌드 후 audited 영역의 old alias 잔존은 0건이다.
- **#061 Shift-JIS fragment 경계 감사:** 실제 한국어 사이에 일본어/한자/반쪽 Shift-JIS가 끼어 있는 것이 명확한 active runtime string 33개를 `msgsec04/06/07/08`에서 선별했다. 기존 body를 repack하지 않고 각각 EOF에 완전한 한국어 문자열 + `05` guard를 append한 뒤 해당 absolute-byte header만 redirect했다. `02 01 C8` 동적 이름 token이 필요한 문자열은 텍스트 재인코딩이 아니라 raw 3B token을 그대로 삽입했다. 예: `대기 무장을 어디로 / 퇴각시키겠습니까？`, `[군주명]군의 성을 함락시켰습니다`, `의술에 국경은 없습니다`, `잠시 기다려 주십시오`, `죄송합니다 / 산적에게 빼앗겼습니다`, `누구를 등용하시겠습니까？`, `[무장명]의 충성도가 떨어졌습니다`, `황제가 될 수도 / 있었을 것인데`, `부디 선생의 가르침을 / 내려 주시옵소서` 등이다.
- `msgsec06 header[121]=0x1157`의 `$` + 중복 cross-fragment 문구는 의미/제어 역할이 아직 불명확하다. v101에서는 **의도적으로 수정하지 않고 deferred**로 남긴다. 정적 CP932 스캔에서 `농=956B`, `뢰=94D6`처럼 이미 Citra-safe인 local alias가 한자로 보이는 false positive도 확인했으므로 raw CP932 판독만으로 추가 일괄 수정하지 않는다.
- **#063:** v93 이후 `msgsec09` header[0]은 원래 count가 아니라 EOF parent0의 word pointer로 사용 중이었다. v100 `header[0]=0x4C0`(byte `0x980`) active parent0에서 승리 row만 `a<09>軍 / ﾉ勝利ﾃﾞｽ` 일본어로 남고 패배 row는 이미 `군은 / 패배했습니다`였다. v101은 기존 parent rows 2~7을 byte-exact 보존한 새 parent0을 append하고 header[0]만 `0x526`(byte `0xA4C`)으로 이동했다. 최신 화면 목표는 **`[군주명]군의 승리입니다.` / `[군주명]군은 패배했습니다.`**다.
- 완성본 위에서 빌더를 두 번 실행해 동일 SHA가 재현됐고 `tools/verify_sangokushi2_v101_runtime_readback.py`도 두 번 PASS했다. SHA-256: `code.bin=91DEA206DD82ED401F6849BA5EE96438AD90C7E68DDA3EF9A8A90D22E4D8211A`, `msgsec04=1F6D036D1269D3E84F0F78E46B2B0D73E80E93EBA7A768CF1CEB60A78C5F0493`, `msgsec06=BDD2E93619CF3A819BD05379907AD3C85CC08B69B1F35E40266AD0FC14457BC1`, `msgsec07=FC4710D0DAA564C45AA03967D5462C4BF2FAAB4A3982B5D2ECE0C3597F0A3B6D`, `msgsec08=455765664178C2DEB7DC57A1BD800807945788A3C22D912DE6DDB9AA4C5885A8`, `msgsec09=02A8EDA51509A041D9665CAE62672DBFD326599ECEF2DAB5433ABF6E8CD7DE9D`.
- 빌더=`tools/build_sangokushi2_v101_issue054_061_063.py`, targets=`analysis/v101_issue054_061_063_targets.json`, report=`analysis/v101_issue054_061_063_report.json`, verifier=`tools/verify_sangokushi2_v101_runtime_readback.py`.
- **2026-09-01 사용자 Citra 승인 완료:** v101 적용 후 사용자가 `잘 수정되었다`고 확인했다. #054/#061/#063 수정은 이후 회귀 제보가 없는 한 승인 기준으로 동결한다.

## v102-intermediate #053 / #064 시나리오 설명·설정 메뉴 (2026-09-01)

- exact v101 전체 Patch 65개 파일을 `analysis/v102_issue053_064_baseline/PatchSnapshot`에 봉인했다. 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec10.dat`, `RomFS/Message/msgsec11.dat` 4개다. 나머지 61개 파일과 v101 승인 수정은 byte-exact다.
- **#053:** 시나리오 선택 화면 상단 설명의 실제 source는 `msgsec11.dat`이다. 184년 첫 설명 physical block `0x79~0xD1`은 이미 한국어 정상 상태이므로 byte-exact 동결했다. 나머지 12개 설명은 189/194/201/208/221/235/196/213/223년 9개 + 숨겨진 3개 전부를 번역했다. header/`05 05 05` separator/파일 크기/fragment start를 그대로 유지하는 fixed-position/no-repack이며, 각 그룹은 원본 capacity, 최대 20칸, fragment cut safety를 통과했다.
- **#064:** 설정 메뉴는 baked image가 아니라 `code.bin` + `msgsec10.dat` text source다. 따라서 이미지/G1T는 수정하지 않았다. 버튼 최신 표기는 **`세이브 / 로드 / 환경설정 / 편집기능 / 튜토리얼 / 게임종료`**이며 상단 설명은 **`데이터를 세이브합니다.` / `데이터를 로드합니다.` / `환경을 설정합니다.` / `도시·세력·특산품을 편집합니다.` / `튜토리얼을 볼 수 있습니다.` / `게임을 종료합니다.`**다.
- `세이브/로드/환경설정/편집기능/게임종료`는 `code.bin` 고정 C-string 슬롯에 직접 반영했다. `ﾁｭｰﾄﾘｱﾙ` 원본 슬롯 `0x1CF784`는 7B라 한국어 `튜토리얼` 8B+NUL을 담을 수 없어 `0x1D1886`의 26B NUL slack으로 relocation했다. 관련 pointer는 예상 4개가 아니라 **5개**였으며 `0x1DC12C`, `0x1DC148`, `0x1DC160`, `0x1DC17C`, `0x1DC278` 전부를 새 문자열 VA로 redirect했다.
- 신규 폰트 글자는 `브=96AC/physical3197`, `튜=96B0/physical3201` 두 자다. 두 donor 모두 historically fresh, 기존 Korean owner 0, Message/Scenario refs 0, code text refs 0을 확인했다. 바로 아래 physical 3270/3274는 JIS로 주소화되지 않는 gap이므로 top-row bleed guard를 적용했고 packed font pixel-exact readback을 통과했다.
- 완성본 위 빌더 2회와 독립 verifier 2회 모두 동일 결과로 PASS했다. SHA-256: `code.bin=ACD5DB756794D9FEFAEB7CA5C930580CE632605306059D0511AF51850260954E`, `font.g1t=1C93C72B555F6B0B32EB3A86EC0675534EF688A3D5B6911C567EE2FC84840CD6`, `msgsec10=D3C3C9436C09519AE247DD4E642C237B56D400DC119C535B8929468DD6D0EE9C`, `msgsec11=8BAA68AC6F2DF554D08A12456AE20012E936629475F3FD50DE8A3948AD4D5550`.
- 빌더=`tools/build_sangokushi2_v102_issue053_064.py`, targets=`analysis/v102_issue053_064_targets.json`, report=`analysis/v102_issue053_064_report.json`, verifier=`tools/verify_sangokushi2_v102_runtime_readback.py`.
- **다음 채팅 시작점:** 현재 Patch 권위는 `v102-intermediate`. 정적/구조/결정적 빌드는 완료됐다. **#053/#064 Citra 실화면 성공 여부는 아직 미확인**이므로, 다음 Agent는 v102를 다시 만들거나 롤백하지 말고 사용자의 테스트 결과를 먼저 기다린다. 문제가 있으면 해당 화면 source만 재추적하고, v101 승인 영역은 건드리지 않는다.

## v103-intermediate — #065 / #067 / #070 대사 전수 재감사 (2026-09-01)

- **현재 Patch 권위는 v103-intermediate**다. exact v102 전체 Patch를 `analysis/v103_issue065_067_070_baseline/PatchSnapshot`에 봉인한 뒤 `code.bin`, `font.g1t`, `msgsec04/06/07/08.dat` 6개만 수정했다. 그 외 v102 파일은 byte-exact다.
- #065 게임 종료 popup, #067 inventory 밖 hardcoded 대사, #070 혼합 한자/잘못된 suffix를 계기로 기존 282/957 완료 집계를 다시 신뢰하지 않고 현재 v102의 실제 NUL C-string bank + active flat-header target + body fragment를 전수 감사했다.
- `code.bin` 실제 대사/팝업 120개를 고정 슬롯 안에서 한글화했다. 포맷 토큰과 ARM instruction 경계를 보존하며 slot 밖 diff는 0이다.
- `msgsec06/07/08` active 문제 20개는 EOF standalone target + 해당 absolute-byte header redirect로 정리했다. dynamic name token은 `02 01 C8` 전체를 보존한다.
- 과거 Citra가 header가 아닌 physical body를 직접 읽은 사례를 반영해 v101 stale mirror 46개에 더해 **추가 stale body 41개(msg06 3 / msg07 33 / msg08 5)**를 fragment 시작점에서 제자리 정리했다. separator/후속 주소는 이동하지 않는다.
- `저팽` 원인은 `쪽=9945` 실패 alias였다. v103은 `쪽=97DD / physical3434`를 fresh low-lead donor로 사용하고 physical3507 top row만 guard 처리한다. 이번 font의 변경 cell은 정확히 3434/3507 두 개다. 사용자가 허용한 일본어 glyph donor 추가는 필요 시 가능하지만, 이번 최종본은 `쪽` 1자 외 신규 donor를 늘리지 않고 기존 safe alias/표현 정리로 해결했다.
- `tools/audit_sangokushi2_v103_dialogue.py` 최종 결과는 **code dialogue 0 / failed alias 0 / active Message 0 / mixed Message body 0**이다. flat header 내부 binary와 비대사 menu/theme C-string은 대사 감사 scope에서 명시 분리한다.
- 독립 verifier `tools/verify_sangokushi2_v103_runtime_readback.py` PASS. readback report=`analysis/v103_issue065_067_070_readback_report.json`.
- 완성본 위 v103 builder+verifier 2회 반복 결과 SHA가 동일했다. 최종 SHA: `code.bin 51fc2772c8f581a75ebec4bf1d92f1864c07d4a41f95df0b38f30da4fbd51a57`, `font.g1t e047558103ddf309bd9228750036ee074f8a56107b8e61bda167edd0bc1cadd6`, `msgsec04 dbf0a8b7fea4ea955fda8013e5c14eaa7dfbd3bc95d2bdefbb223260baa2172e`, `msgsec06 79f722deb2de4d04f22daffc0f6c826a3ec4004667796034537e75b7e6899b25`, `msgsec07 52d758d7cd1750f25d81fa93c8742fca9502f17c87a9f32e626fb37abab5ea2c`, `msgsec08 ca7ae92f94ce3a7f12735651c288e1d888040998be0b6ad7ad70336031796023`.
- **Citra 실화면 확인 전까지 intermediate 상태다. 다음 Agent는 v103을 재생성/롤백하지 말고 #065/#067/#070 제보 화면의 사용자 테스트 결과를 먼저 받는다.** #053/#064도 아직 Citra 미승인이므로 함께 회귀 확인 대상이다.

## v104-intermediate 이미지 20종 리빌드 (2026-09-01)

- 전체 MD와 v103 report/manifest 및 독립 verifier를 교차 확인해 실제 최신 기준이 v103-intermediate임을 확정했다. exact v103 Patch 65개 파일을 analysis/v104_image_update_baseline/PatchSnapshot에 봉인했다.
- 요청 대상은 InfoB texture[5,7,8,9,10,11,12,13,14,15] 10개, InfoI texture[0,1] 2개, InfoK texture[0,2,5] 3개, InfoT texture[0,1,2] 3개, InpCalc texture[2,3] 2개로 총 20개다.
- 변경 Patch 파일은 정확히 RomFS/Common/Info/InfoB.g1t, InfoI.g1t, InfoK.g1t, InfoT.g1t, RomFS/Common/InpCalc/InpCalc.g1t 5개다. 모두 v103에 없던 컨테이너라 immutable Original에서 최초 편입했고 Patch 파일 수는 65→70이다.
- 전 대상은 0x09 RGBA8이다. 20/20 source codec identity, pixel-exact 출력 역읽기, 각 컨테이너 header/비대상 payload byte-exact, v103 기존 65파일 byte-exact를 통과했다.
- 완성본 위 빌더 2회와 독립 verifier가 동일 결과로 PASS했다. report SHA-256은 80091A1443B6A7BD1E9E891B07A543A24AD553D513C6336F50086E81962121C4다. 빌더=tools/build_sangokushi2_v104_image_update.py, report=analysis/v104_image_update_report.json, verifier=tools/verify_sangokushi2_v104_image_readback.py.
- 텍스트, code.bin, Message, font, fix_data, 기존 v100 이미지 9종과 요청 외 PNG는 수정하지 않았다. 이미지 생성은 사용하지 않았다. **v104-intermediate는 v105의 sealed baseline으로 계승됐다.**

## v105-intermediate #071 / #072 / #073 / #074 대사 재감사·수정 (2026-09-01)

- exact v104 전체 Patch 70파일을 `analysis/v105_issue071_074_baseline/PatchSnapshot`에 봉인했다. 이 항목은 v105 당시의 계보 기록이며 현재 후속 작업 기준은 v109이다. v103/v104/Original로 롤백해 재생성하지 않는다.
- 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec08.dat` 5개다. `font.g1t`, fix_data, v104의 `InfoB/InfoI/InfoK/InfoT/InpCalc.g1t` 5개와 그 안의 PNG 20종은 모두 byte-exact 보존됐다.
- **#071:** `code.bin 0x1D1168`의 suffix가 `가 응하리라 / 믿지 않소`여서 동적 군주명과 합쳐 `손권가`가 됐다. 최신 target은 **`군이 응하리라 / 믿지 않소`**이며 화면 조립 목표는 `[군주명]군이 응하리라 / 믿지 않소`다.
- **#072:** 스크린샷의 한자형 혼입은 `렇=994F`, `찰=996F` far alias 런타임 실패다. 한 곳만 remap하지 않고 code/Message live dialogue를 전수 검색해 두 alias를 사용하는 실제 대사를 모두 제거했다. 최신 표현은 `그렇다면→그러면`, `서찰→서신`, `벅찰→무리`, `진찰→확인` 계열이며 post-audit에서 994F/996F live dialogue는 0건이다. 이번 revision은 font를 수정하지 않는다.
- **#073:** `0x1D089F`는 동적 인물명 뒤에 본문이 바로 붙어 `장제그럼...`이 되므로 본문을 **`, 그럼 부담이 / 크지 않겠습니까...`**로 수정했다. `0x17F314`는 **`우리 주군의 서신을 / 가지고 왔습니다`**로 바꿔 `찰` 실패 alias를 제거했다. `0x1D0FF0`은 **`군이 바로 / 승낙했습니다`**로 바꿔 `[군주명]주군이` 중복을 제거했다.
- 같은 유형의 재발 방지를 위해 Original hardcoded C-string 전체에서 (1) `殿...` 시작 + 한국어 `주군...` 시작, (2) 일본어 단독 조사 `が/は/を/に...` 시작 + 한국어 단일 조사 시작을 후보로 수집했다. 실제 동적 이름 suffix로 판정된 항목을 `군/군이/군의/군이라면`, `이(가)`, `은(는)` 등으로 정리했고 최종 후보는 0건이다. 단, 향후 새 이슈에서는 caller/스크린샷 확인 없이 모든 `주군`을 일괄 치환하지 않는다.
- **#074:** `code.bin 0x1D171D` v104 문장은 원문 15×3 창에서 첫 줄 17칸으로 넘쳐 4 visual line이 됐다. 최신 target은 **`공명의 장성이 떨어졌다! / 공명 없는 촉군은 안 무섭다 / 전군 출격하라!!`**다. 새 감사기가 같은 hardcoded pool을 전수 검사해 별도 16칸 항목 `0x1D121C`도 발견했고 **`그 군주와는 / 교섭 불가입니다`**로 함께 수정했다.
- 기존 추출 inventory만으로 hardcoded layout을 감사하면 `0x1D17xx` 같은 실제 C-string 경계를 놓칠 수 있다. v105부터 `tools/audit_sangokushi2_v105_dialogue.py`가 **immutable Original `code.bin`의 실제 NUL C-string boundary를 직접 순회**하고, Original이 15×3 portrait class에 들어가는 항목의 현재 Patch 번역을 같은 기준으로 검사한다. 이 gate 결과 layout violation은 0건이어야 한다.
- code target은 총 23개다. Original slot capacity, ARM branch final-byte overlap, format token 보존, 새 target 15×3을 모두 검증한다. Message target은 physical fragment 5개이며 header/fragment start/`05` separator를 움직이지 않는 fixed-position 수정이다.
- 최종 감사: **hardcoded layout violation 0 / dynamic suffix candidate 0 / 994F·996F live dialogue 0 / v103 mixed Message 0 / active Message problem 0**. 독립 verifier도 2회 PASS했다.
- SHA-256: `code.bin=3972A742A09612CB7594765AC2BBE64D2D485496B9F65370E93298F56BED4203`, `msgsec04=7BB69A028AB969267DF2DC13BA9E16A8B8C5054D48767B25F222DFCF7D236186`, `msgsec06=DF01F1712D6ED4CEA7F49372CC60F5F07ECFF15799D180DE4AE79AD5D7036C29`, `msgsec07=7BFD900046411E96C0B6BAB1FB9879DB786DEDE154517F3A215067FAE4F58137`, `msgsec08=EF78F6E68AD3D1CF6652AB34D24D1ED62C27289AE772B504C02BA79C99205B3B`.
- 빌더=`tools/build_sangokushi2_v105_issue071_074.py`, targets=`analysis/v105_issue071_074_targets.json`, report=`analysis/v105_issue071_074_report.json`, audit=`tools/audit_sangokushi2_v105_dialogue.py`, verifier=`tools/verify_sangokushi2_v105_runtime_readback.py`.
- **이 문단 작성 당시 Patch 권위는 v105-intermediate였다.** 이후 v106~v109에 계승됐으며 현재 권위는 문서 하단의 v109-intermediate다.

## v106-intermediate #076 / #077 / #078 / #079 재발 문제 수정 (2026-09-01)

- exact v105-intermediate 전체 Patch manifest를 먼저 검증하고 `analysis/v106_issue076_079_baseline/PatchSnapshot`으로 봉인했다. Original/Rebuild/Backup은 쓰지 않았다. 실제 변경은 `code.bin`, `font.g1t`, `msgsec21.dat`, `fix_data.bin` 4개뿐이다.
- **#076 인명 글리프:** 신고된 `온/준/풍/륭/열/촉/욱/유/휴/겸/수/교/각`을 포함하는 표시 이름을 전체 inventory에서 다시 찾아 123레코드를 재인코딩했다. `촉`은 이미 검증된 `8D6F`를 사용하고 나머지 12자는 비참조 original-valid donor를 새로 배정했다. 1,020개 표시 이름을 inventory와 의미 역읽기했고, 1,020개 가타카나 읽기는 v105와 byte-exact다. 신고된 old alias는 이름 필드에서 0건이다.
- **#077 미번역 시나리오:** 실제 source는 기존 감사 범위 밖 `RomFS/Message/msgsec21.dat`였다. 223년 유선 플레이의 오로 침공 사건을 신고 화면만 점 수정하지 않고 `msgsec21_0018_001~0028_000`의 연속 20 fragment 전체로 번역했다. `05 05 05`가 레코드 경계에 분산된 구조를 보존했다. **v106 당시 tail 30word를 metadata로 본 판정은 v107 #081에서 absolute BYTE runtime pointer로 정정됐으므로 폐기한다.** 자연스러운 `다섯`을 위해 `섯=98F4` donor를 사용했다.
- **#078 일어 혼입:** `%s様、` 21개를 `%s님, `으로 전수 치환했다. 같은 화면군에서 런타임이 별도 C-string으로 합성하는 `何か/新しい/は/の/のみ/に/もはや/あとは/て下さい/が/とて`와 조건 질문을 확인된 16개 slot에서 한국어화했다. `어떤`을 위해 `떤=9963` donor를 사용했다. 기존 감사가 놓친 원인은 한 문자열 내부 혼입만 검사하고 짧은 조립형 일본어 조각과 msgsec21 bank를 범위에 넣지 않았기 때문이다.
- **#079:** 조립형 세 조각의 첫 부분 `우리와`를 `우리와 `로 바꿔 최종 역읽기 **`우리와 동맹을 / 맺어 주길 바란다`**를 확인했다.
- font는 donor 14셀과 각 바로 아래 unmapped guard 14셀, 총 28셀만 변경됐다. hardcoded layout 위반 0, 동적 suffix 후보 0, Message mixed 0, active Message 문제 0이다. report SHA-256은 `AC7D6F641D6546CD9BE063F4754E6B072CD9A4A3BC2FBC9455ADBE9B8B8FDC5F`; 빌더 2회에서 report와 전체 Patch manifest가 동일했다.
- SHA-256: `code.bin=0D336549317A682ED611FE864CE8339B0E59F3A309610C96DC166CD2F8951CE1`, `font.g1t=5F7B0CB647BA56A14337B35A2CF80688EDAE0BC623AE949E07B4937E4B62ABB7`, `msgsec21.dat=C135E643197FC65FCE7E26D2FECFDDEAAC437BF0E1A14640CC84A3EB9F4365EC`, `fix_data.bin=AF8E664551A14DC52D6BB0EE296F8F885811F548C8DD53AC7290BFCDCF6799F8`.
- 권위 자료: `analysis/v106_issue076_079_targets.json`, `analysis/v106_issue076_079_report.json`, `tools/build_sangokushi2_v106_issue076_079.py`, `tools/verify_sangokushi2_v106_runtime_readback.py`. **v106은 후속 v107/v108에 계승·정정된 선행 revision이며 현재 권위가 아니다.**

## v107-intermediate #080~#083 구조 수정 및 재발 방지 (2026-09-01)

- exact v106을 `analysis/v107_issue080_083_baseline/PatchSnapshot`에 봉인하고 `ExeFS/code.bin`, `RomFS/Message/msgsec21.dat`, `RomFS/Scenario/fix_data.bin` 3개만 수정했다.
- **#081 dual-header 재확정:** `msgsec21.dat`의 67-word header는 `word[0..36]`의 37개 word-offset entry(36은 EOF sentinel)와 `word[37..66]`의 30개 absolute BYTE runtime pointer다. v106의 tail metadata 판정을 폐기하고, `tools/sangokushi2_msgsec21_dual_header.py`로 immutable Original zero-translation identity rebuild가 byte-exact임을 먼저 증명한 뒤 두 pointer class를 모두 재계산했다.
- 검수 번역 91개와 Shift-JIS 경계에서 갈라진 기술 fragment 4개를 합쳐 `msgsec21` runtime target **95개**를 재구성했다. 223년 유선 사건은 유비 사망→사마의 오로 계책→가비능/맹획/손권/맹달/조진→제갈량 대응의 원문 문맥으로 정리했다.
- **#082/#083 hardcoded 수정:** `0x180DDC="다른 "`, `0x180DEC="%s조건을\n제시합니까？"`, `0x180F94="%s이(가) 안 돌아왔습니다"`, `0x181290=" 포기합니까？"`가 최신 target이다. 한국어 alias 역디코딩과 충돌해 가려질 수 있는 일본어는 immutable Original exact source signature로 직접 검사하며, 두 Original signature와 잘못된 `떤=9963` counteroffer target의 잔존은 0건이다.
- #080 phase1은 `オウセイ=区星=구성`을 유지하고 `헌=8972 / 익=92CB / 룡=92DE / 삭=9554 / 북=89D0 / 뢰=94D6 / 농=956B`을 적용했다. 이름 1,020건 의미와 가타카나 읽기 1,020건을 검증했으며 이 시점의 remaining far alias 문자는 27자였다.
- SHA-256: `code.bin=0A7EC7E3C7EAF7CDE28245D3623A3FCBF4FF21A112E4BEBEE300F9AA1E96F493`, `msgsec21.dat=7A83F293EA519DB43A69E23967480C84417F7AC833C16340A3D30DCFC2E6110D`, `fix_data.bin=14E5416EAAA3198B344258038E7D035056661464BE05E90C7204A1EABC8BB0EB`. 결정적 재빌드와 독립 verifier가 PASS했다.
- 권위 자료: `analysis/v107_issue080_083_targets.json`, `analysis/v107_issue080_083_report.json`, `tools/sangokushi2_msgsec21_dual_header.py`, `tools/build_sangokushi2_v107_issue080_083.py`, `tools/verify_sangokushi2_v107_issue080_083.py`.

## v108-intermediate #080 인명 far-alias phase2 완료 (2026-09-01)

- exact v107을 `analysis/v108_issue080_name_phase2_baseline/PatchSnapshot`에 봉인하고 `RomFS/Common/Font/font.g1t`, `RomFS/Scenario/fix_data.bin` 2개만 수정했다. v107 `code.bin`/`msgsec21.dat`와 나머지 비대상 Patch 파일은 byte-exact다.
- 남은 far alias 27자를 모두 lead `<0x98` low-lead alias로 이전했다. donor 22개는 v107 code+모든 Message+Scenario raw ref 0 / name owner 0이며, `9555/96A5/9777/9787/97B7` 5개는 `fix_data` 인물 레코드의 비텍스트 field `+4`에서만 우연히 일치함을 증명했다.
- 27 target glyph는 normal 13x13 render, bottom row ink 0, packed cell pixel-exact를 통과했다. font diff는 정확히 27 donor cell이고 허용 셀 밖 diff는 0이다.
- v107 대비 표시 이름 46레코드가 바뀌었다. 최종 검증은 **1,020/1,020 이름 의미 일치 / 1,020/1,020 가타카나 읽기 byte-exact / 이름 map lead `>=0x98` alias 0**이다.
- SHA-256: `font.g1t=6A91C3FBD8CB9A976FA443961F41D24AB7B484172C4DAA767F19F17D511CF735`, `fix_data.bin=5C40E354C3723F7C92F7A1DE943BE27DF9A07F891E82D45679A1A2DB4E14F0A4`. 결정적 재빌드와 독립 verifier가 PASS했다.
- 권위 자료: `analysis/v108_issue080_name_phase2_targets.json`, `analysis/v108_issue080_name_phase2_report.json`, `analysis/v108_issue080_name_phase2_readback_report.json`, `tools/build_sangokushi2_v108_issue080_name_phase2.py`, `tools/verify_sangokushi2_v108_issue080_name_phase2.py`.
- **v108은 #080~#083 텍스트·폰트·인명 권위로 v109에 byte-exact 계승됐으며, 현재 Patch 전체 기준은 아래 v109-intermediate다.**

## v109-intermediate Common UI 이미지 106종 리빌드 (2026-09-01)

- 시작 전 `analysis/v108_issue080_name_phase2_report.json`의 complete manifest와 현재 Patch 70파일이 일치함을 확인하고 exact v108을 `analysis/v109_image_update_baseline/PatchSnapshot`에 봉인했다. Original/Rebuild/Backup은 수정하지 않았다.
- 사용자가 지정한 PNG는 정확히 106개다: `Select_005~059` 55개, `GuidBtn_012~046` 35개, `Dialog_007~012/015~022` 14개, `Common_009~010` 2개다. 지정하지 않은 PNG/texture는 수정하지 않았다.
- `Select.g1t`는 v108 Patch에 이미 존재하므로 sealed v108 컨테이너를 base로 사용했다. `GuidBtn.g1t`, `Dialog.g1t`, `Common.g1t`는 v108에 없으므로 immutable Original 컨테이너를 최초 편입 base로 사용했다.
- 대상 106개는 모두 `0x09 RGBA8`이며 base codec decode→encode identity, PNG RGBA mode·원 texture 치수, packed G1T pixel-exact readback을 모두 통과했다. 네 컨테이너의 header 및 비대상 payload diff는 0이다.
- v108 대비 변경 파일은 정확히 `RomFS/Common/Select/Select.g1t`, `RomFS/Common/GuidBtn/GuidBtn.g1t`, `RomFS/Common/Dialog/Dialog.g1t`, `RomFS/Common/Common/Common.g1t` 4개다. v108의 code/font/Message/fix_data 및 다른 이미지 파일은 byte-exact다. Patch 파일 수는 70→73이다.
- 완성본 위 빌더를 다시 실행해 report SHA-256 `8B7E2CD23560EF3021902850BF55905D77DA5877C9419640C58B537E0264992E`와 전체 Patch manifest가 동일했고 독립 verifier도 PASS했다. 이미지 생성은 사용하지 않았다.
- SHA-256: `Select.g1t=5662A6EBE13DD0259174049AEDEB0FA87F00164BE991CB83C38E199CE9966DAC`, `GuidBtn.g1t=2CCC37F01207C49E57D30B69AF35D1650A157A7258595B42295C1B23FE9D16F8`, `Dialog.g1t=20F1C4E8D6F4317567CA2B334AE1F65DBD28F2E7765950F4ACBC438B4B0F87C5`, `Common.g1t=7A65248CD0909C593677BF24F5FF11227F13AF3BADB7845C14D610924F540B17`.
- 권위 자료: `analysis/v109_image_update_report.json`, `tools/build_sangokushi2_v109_image_update.py`, `tools/verify_sangokushi2_v109_image_readback.py`. **이 문단 작성 당시의 기준은 v109-intermediate이며 이후 계보는 후속 revision 기록과 아래 v126을 우선한다.**

## 직전 revision — v126-intermediate #099 / #104 / #105 / #112 (2026-09-02)

- **현재 Patch 전체 권위는 v126-intermediate**다. exact v125 전체 manifest를 먼저 검증하고 `analysis/v126_issue099_104_105_112_baseline/PatchSnapshot`에 봉인한 뒤 그 위에만 수정했다. Original/Rebuild/Backup은 쓰지 않았다.
- **#099 역사 기록:** v126에서는 `内容 / 条件 / 援助`를 `0x1CFB40 / 0x1CFD5C / 0x1CED30`에서 수정했다. 이후 사용자 v128 Citra에서 `내용`은 정상이나 화면의 `条件`이 그대로인 것이 확인되어 **`0x1CFD5C`를 해당 패널 source로 본 판정은 폐기**됐다. 현재 권위는 아래 v129의 **`0x0B465C=조건`**이다. `내용@0x1CFB40`, `원조@0x1CED30` 및 duplicate `0x1CFD5C=조건`은 후속 revision에 보존된다.
- **#104:** 동맹 편집 상단의 실제 source는 `msgsec09.dat 0x88E..0x8AB`, 29B fixed span이다. 선두 `02 2D`는 동적 군주명 prefix이므로 반드시 보존한다. 최신 suffix는 사용자 1안 **`의 타세력 동맹 체결/파기`**이고 prefix 포함 26B라 원 slot에 들어간다. 따라서 `하진의 타세력 동맹 체결/파기`처럼 조립되며 fallback `의 타세력 동맹 편집`은 사용하지 않는다. msgsec09 88-word header와 separator 위치는 이동하지 않는다.
- **#105 역사 기록:** v126에서는 `msgsec06 header[63] -> 0x936`을 실제 owner로 보고 38B standalone target에 3안 **`그리 쉽지는 않을 것 같습니다.`**를 넣었다. 이후 사용자 v128 Citra에서 제보 대사가 그대로인 것이 확인되어 **이 header/source 판정은 폐기**됐다. 현재 권위는 아래 v129의 hardcoded **`code.bin 0x1D08C4`**, literal pointer `0x1E5074`다. v126의 msgsec06 duplicate는 후속 revision에서 그대로 보존하지만 #105 화면 source로 취급하지 않는다.
- **#112:** `プレイ時間@code.bin 0x1CFF6C`는 **`플레이시간`**으로 10B exact-size 치환한다. 세이브 완료의 실제 visible source는 pointer `0x1AB2D4 -> 0x2CF4DC`, 즉 `code.bin 0x1CF4DC`의 20B C-string이다. 사용자 1안 `세이브를 완료했습니다.`는 22B라 초과하므로 허용 2안 **`세이브 완료`**를 사용한다. 이 pointer와 주변 save/load table은 바꾸지 않는다.
- v125 대비 변경 파일은 정확히 **`ExeFS/code.bin`, `RomFS/Message/msgsec06.dat`, `RomFS/Message/msgsec09.dat`** 3개뿐이다. `font.g1t`, `fix_data.bin`, 이미지/G1T와 모든 비대상 Patch 파일은 byte-exact다.
- 완성 v126 위 builder 재실행과 독립 verifier 재실행까지 모두 PASS했다. SHA-256: `code.bin=D4CF43C0A89381AB33572AD3176AAA196731354A39361B67D6AE70C3E55DDBF4`, `msgsec06=07D472269B436FCDB519E7CD86F3CE3E95A6711B2D4862E15EA7391FBDCFBEF2`, `msgsec09=6BB0E83367EAF9CB590AE905C4C36D7A347B4F0D7D04F220D7FB41FBAA174EC4`.
- 빌더=`tools/build_sangokushi2_v126_issue099_104_105_112.py`, targets=`analysis/v126_issue099_104_105_112_targets.json`, report=`analysis/v126_issue099_104_105_112_report.json`, verifier=`tools/verify_sangokushi2_v126_issue099_104_105_112.py`.
- **Citra 다음 확인:** (1) #099 동맹 화면의 `내용/조건/원조`, (2) #104 `하진의 타세력 동맹 체결/파기`, (3) #105 `그리 쉽지는 않을 것 같습니다.`, (4) #112 `세이브 완료`와 `플레이시간`. 실화면 승인 전까지 intermediate다.

## 직전 revision — v127-intermediate Hex 전투 시간·군 표기 (2026-09-02)

- exact v126 전체 Patch를 `analysis/v127_hex_battle_time_army_baseline/PatchSnapshot`에 봉인했다. v126 대비 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t` 두 개이며 Original/Rebuild/Backup과 `HexUpUI_000.png`는 수정하지 않았다.
- 시간 문자열은 `0x1D07DC 朝→아침`, `0x1D07D8 昼→낮`, `0x1D07E0 夜→밤`이다. `0x1B2E24`의 `[아침, 낮, 밤]` pointer table과 이를 읽는 literal pool `0x10E758/0x10E8A8`은 byte-exact다.
- `아침`은 사용자 14×14 RGBA 타일 SHA-256 `6B78EAAAA2B0F40184B581F3D9CD6A05B5A4AFC6C43997668FFEDBE1B6A53E12`를 fresh donor `96D7/physical3240`에 A4로 넣었다. v126 runtime reference 0, prose/이름 owner 0, historical reservation 0, 수직 인접 `3167/3313` 예약 0을 확인했고 최종 참조는 `code.bin 0x1D07DC` 한 곳뿐이다. `낮=9155`, `밤=9371`, `군=88C7`은 기존 Sam3 셀을 변경하지 않는다.
- 공격/수비 전용 포맷 `0xC9174/0xC9314`의 `%s軍`만 `%s군`으로 동일 길이 치환했다. 각 owner ADR `0xC9030/0xC91D4`는 byte-exact다.
- SHA-256: `code.bin=0EB9AEB86720634E5B0E9B8CEC50D89D479DDA06A50064CEC93C6630FF17A4DF`, `font.g1t=32FF5F7A66D16FDA8F176F3F8C5E2F32AC402B41D8359606B94591E1D62E4EA6`. 완성본 위 빌더 재실행과 독립 verifier가 PASS했다.
- v103 dialogue audit의 code residue는 0이다. active 7/mixed 10은 v126 baseline에도 동일한 후속 alias decode 항목이라 v127 변화가 아니며, v127 비대상 Message는 v126과 byte-exact다.
- 빌더=`tools/build_sangokushi2_v127_hex_battle_time_army.py`, targets=`analysis/v127_hex_battle_time_army_targets.json`, report=`analysis/v127_hex_battle_time_army_report.json`, verifier=`tools/verify_sangokushi2_v127_hex_battle_time_army.py`. Citra에서 시간 세 상태와 공격·수비 양측 `군`을 확인하기 전까지 intermediate다.

## 직전 revision — v128-intermediate 지정 UI PNG 40개 리빌드 (2026-09-02)

- exact v127 전체 Patch를 `analysis/v128_image_update_baseline/PatchSnapshot`에 봉인하고 지정 PNG 40개만 5개 G1T에 반영했다. `edit_bushou_edit_009`, `start_menu_up_017`, `HexCommand_002~005`, `HexFontEffect_001~031`, `HexUpUI_000/004/005`가 정확한 대상이다.
- `edit_bushou_edit.g1t`, `start_menu_up.g1t`는 sealed v127 컨테이너를 사용했다. v127에 없던 `HexCommand.g1t`, `HexFontEffect.g1t`, `HexUpUI.g1t`만 immutable Original을 최초 편입 기준으로 사용했으며 Original/Rebuild/Backup은 쓰지 않았다.
- Hex 38개는 당시 `from_edit_view(flip_y)` 후 표준 RGBA8 encoder에 넣었다. 정적 변환 왕복은 통과했지만 사용자 v129 Citra에서 위아래 반전으로 판명됐으므로 **이 runtime pack 방식은 실패 기록이며 v130에서 폐기·교정됐다.** HexCommand를 회전하지 않은 판단은 유지한다.
- v127 대비 변경/추가 파일은 정확히 위 G1T 5개다. 모든 container header와 비대상 texture payload, v127의 code/font/Message/Scenario/기존 비대상 이미지는 byte-exact다. 이미지 생성은 사용하지 않았다.
- SHA-256: `edit_bushou_edit=44F91BB8322240BB5CE3C0F586C39C0B442B882A529F522E1DB344459514346E`, `start_menu_up=D1AD13D9E40F367243FD8DE2FB0377798F7D2E4BE611C3C007A741DB36B7AA75`, `HexCommand=31CB553B160C3AD39C03537FF95457CBBC09C85FEC24B5D39ABEE4607201E865`, `HexFontEffect=DFCC05CC8D52720B0B6EEC200009B0046F74BB08C96006CEABEC645564CA12CE`, `HexUpUI=A614A2D2CD4E733422B8AECF70E7454C5D67FECD1B662E555CB42A70B8254B9D`.
- 권위 자료: `analysis/v128_image_update_targets.json`, `analysis/v128_image_update_report.json`, `tools/build_sangokushi2_v128_image_update.py`, `tools/verify_sangokushi2_v128_image_update.py`. v129은 이 전체 manifest를 exact baseline으로 계승한다.

## 직전 revision — v129-intermediate #099 / #105 / #113 / #114 (2026-09-02)

- **베이스:** `analysis/v128_image_update_report.json` complete manifest와 실제 Patch를 대조해 exact v128임을 확인하고 `analysis/v129_issue099_105_113_114_baseline/PatchSnapshot`에 봉인했다. v129은 `ExeFS/code.bin`만 수정하는 code-only revision이다. Original/Rebuild/Backup은 수정하지 않았다.
- **#099:** 사용자 Citra에서 `내용`은 정상인데 바로 아래 `条件`만 남는 것을 권위로 삼아 v126의 `0x1CFD5C` source 판정을 폐기했다. 실제 owner는 **`code.bin 0x0B465C`**, ADR `0x0B4430/0x0B4464`이며 **`조건`**으로 4B 동일 길이 치환한다. 이미 정상인 `내용@0x1CFB40`은 byte-exact 보존한다.
- **#105:** v126 `msgsec06 header[63]` 수정이 화면에 반영되지 않았으므로 폐기했다. 실제 Citra 화면의 원문과 일치하는 hardcoded owner는 **`code.bin 0x1D08C4`**, pointer `0x1E5074`다. capacity 39B에 사용자 1안 49B/2안 47B는 들어가지 않고 3안 29B만 맞아 **`그리 쉽지는 않을 것 같습니다.`**를 적용한다. 기존 msgsec06 duplicate는 v128 byte-exact로 둔다.
- **#113:** `0x185068`의 한국어 formatter와 별도 `0x185054=再開` argument가 조립되어 뒤쪽 한자가 섞였다. ADR `0x18501C/20/24`를 변경하지 않고 같은 fixed cluster를 `재개 / 시작 / 그럼 게임을 %s합니다.`로 정리해 **`그럼 게임을 재개합니다.` / `그럼 게임을 시작합니다.`**로 조립한다.
- **#113 동일 class 감사:** 한국어 `%s` formatter와 가까운 ADR short argument에서 `曹操 / 劉玄徳 / 太守 / 再開` 4건을 `조조 / 유현덕 / 태수 / 재개`로 수정했다. 이미 일부 한국어인 ADR dialogue 중 CJK/kana 잔존은 dialogue/popup scope에서 `中断세이브 없이...@0x10E118`, `%s님, %sが...@0x1671FC` 2건을 확정해 각각 **`중단 저장 없이\n게임을 종료하시겠습니까？`**, **`%s님, %s이(가)\n합류했습니다`**로 수정했다. 최종 감사 결과 short CJK argument 0 / partial mixed dialogue 0이다. 비대사 UI의 legacy alias raw CJK는 이 class에서 제외한다.
- **#114:** 출진 목록 병과 pointer table은 **`0x1B2E30` 7-entry**다. 기존 `기마/노/강노`는 유지하고 `歩兵/闘艦/蒙衝/走舸`만 `보병/투함/몽충/주가`로 바꾼다. 최종 목록은 **`보병 / 기마 / 노 / 강노 / 투함 / 몽충 / 주가`**, table pointer 7개는 byte-exact다.
- **보존:** 사용자가 정상 확인한 #104 `msgsec09` 전체와 #112 `세이브 완료@0x1CF4DC / 플레이시간@0x1CFF6C`, v128 이미지 40개/5개 G1T, 모든 Message/Scenario/font/fix_data는 exact v128과 byte-exact다.
- **검증:** v128 대비 code diff 135B, 변경 파일 1개. 완성본 위에서 builder를 다시 실행해 동일 결과를 재생성했고 독립 verifier를 재실행해 PASS했다. #113 same-class residue 0/0, #114 7종 역읽기 PASS다. 최종 `code.bin` SHA-256=`796F5C351866D0A9508008B6C4810F1FE70644E73105356F94BD0FCC16A005A5`.
- 권위 자료: `analysis/v129_issue099_105_113_114_targets.json`, `analysis/v129_issue099_105_113_114_report.json`, `tools/build_sangokushi2_v129_issue099_105_113_114.py`, `tools/verify_sangokushi2_v129_issue099_105_113_114.py`.
- **Citra 다음 확인:** (1) #099 `내용/조건/원조`, (2) #105 `그리 쉽지는 않을 것 같습니다.`, (3) #113 `그럼 게임을 재개합니다.`와 가능하면 시작 쪽 sibling, (4) #114 출진 목록 7종 병과. #104/#112는 사용자 승인 상태를 유지한다. 새 화면 승인 전까지 v129-intermediate다.

## 현재 revision — v130-intermediate Hex UI 상하 반전 교정 (2026-09-02)

- **베이스:** 현재 Patch가 v129 complete manifest와 일치하고 v129 독립 verifier가 PASS함을 확인한 뒤 exact v129 전체를 `analysis/v130_hex_orientation_repair_baseline/PatchSnapshot`에 봉인했다.
- **Citra 증거와 원인:** 사용자 화면에서 Hex 3개 그룹만 위아래가 거꾸로였고 `edit_bushou_edit`, `start_menu_up`은 정상 확인됐다. v128의 `정상 편집 PNG → from_edit_view(flip_y) → 표준 encoder`가 중복 반전이었다. v129 세 컨테이너를 표준 decode한 결과가 각 사용자 PNG의 `FLIP_TOP_BOTTOM`과 38/38 pixel-exact임을 재현했다.
- **교정:** `HexCommand_002~005`, `HexFontEffect_001~031`, `HexUpUI_000/004/005`만 정상 방향 PNG에서 **추가 변환 없이 표준 RGBA8 encoder로 직접** 다시 pack했다. 출력 표준 decode는 입력 PNG와 38/38 pixel-exact다. HexCommand는 세로 atlas와 원 치수를 유지하고 회전하지 않았다.
- **변경 파일:** `RomFS/Hex/UI/HexCommand.g1t`, `RomFS/Hex/UI/HexFontEffect.g1t`, `RomFS/Hex/UI/HexUpUI.g1t` 3개뿐이다. G1T header·비대상 payload는 v129 byte-exact다. `RomFS/EditBushou/edit_bushou_edit.g1t`, `RomFS/StartMenu/start_menu_up.g1t`, code/font/Message/Scenario/fix_data와 나머지 Patch 파일도 v129 byte-exact다. Original/Rebuild/Backup은 수정하지 않았다.
- **SHA-256:** `HexCommand=F01C6DFC4789ED5F82F4409115AE2787567409CD81567F0C669273CD91A930F6`, `HexFontEffect=69B1C30829C22B9ACBF1DA902E17BDC6F724BC19E58F968E721145A5DDDCDEF1`, `HexUpUI=22D7E4417D5BC7DBE96AC8A901C196D5F281DF78352D68B46451642549B3E33E`.
- **권위 자료:** `analysis/v130_hex_orientation_repair_targets.json`, `analysis/v130_hex_orientation_repair_report.json`, `tools/build_sangokushi2_v130_hex_orientation_repair.py`, `tools/verify_sangokushi2_v130_hex_orientation_repair.py`.
- **다음 확인:** Citra에서 HexCommand 메뉴, HexFontEffect 글자, HexUpUI 상단 UI가 정상 방향인지 확인한다. 정적 구조·결정적 재빌드·독립 readback은 PASS했고 v131에 byte-exact 계승됐다.

## 현재 revision — v131-intermediate #117~#120 + msgsec09 공통 runtime 전수 보정 (2026-09-02)

- **베이스/변경 범위:** exact v130 manifest를 `analysis/v131_issue117_120_baseline/PatchSnapshot`에 봉인했다. 변경 파일은 정확히 `ExeFS/code.bin`, `RomFS/Common/Font/font.g1t`, `RomFS/Message/msgsec07.dat`, `RomFS/Message/msgsec09.dat` 4개다. 그 외 v130 Patch 파일과 Original/Rebuild/Backup은 byte-exact/미수정이다.
- **#117:** 사관 formatter `0x15B0C0` 최신 target은 **`%s%s에 %s\n(이)라는 자가 사관했습니다`**다. `(이)` 추가분을 확보하기 위해 `에서`를 자연스러운 `에`로 줄였고, 동적 지역/무장 `%s`는 유지한다.
- **#118:** `0x15E524` formatter는 **`메뚜기 떼가 %s`**. event-name pointer `0x1DC428 -> 0x2F3F00`은 **`메뚜기 떼`**, common 발생 pointer `0x1E5ECC -> 0x2F3F20`은 **`발생하였습니다.`**다. 독립 조립 readback은 **`메뚜기 떼가 발생하였습니다.`**. 신규 font alias는 `뚜=8D56/physical1420`, `떼=97CF/physical3420`; guard `1493/3493`만 함께 shift-down 처리한다. 다른 font cell은 변경 금지다.
- **#119:** `msgsec07 header[79]`만 append-only **`누구를 이동시키시겠습니까?`**로 redirect한다. 기존 msg07 body와 다른 387 header word는 보존한다. `code.bin 0x183554`은 **`수송량 입력 수송지:%s`**, spaced duplicate `闘 艦/蒙 衝/走 舸` 실제 slots `0x1CFF24/0x1CFFA0/0x1CFC28`은 **`투함/몽충/주가`**다.
- **#120 v131 진단은 폐기:** v131은 `msgsec09`의 `約束通リノ支払イガ出来ズ`를 #120 실제 source로 보고 32 active pointer를 번역/redirect했으나, 사용자가 일본판 동일시점에서 **`都市の規模が / 中規模になりました！`**가 나오는 것을 확인했다. 따라서 #120을 지급 실패/협력 메시지로 본 판정과 32-pointer 변경은 잘못된 fix이며 v132에서 제거했다. 이 화면은 msgsec09 번역 문제가 아니라 msgsec06 parent routing 구조 오류다.
- **검증/SHA:** builder 2회 결과가 동일했다. `code=D3490D38D60F2D27EDEFF9EE6C805617DAA9E4948D244BD60E10BDB7E2F241F1`, `font=C5AD71FE7B5E6F5C013FE849A66E48DB2EB6531D9333F084DBF7F5253482EA4B`, `msg07=12F51E85B18A692EE06265EE6AF0C6AC59E1CDFDBB8453252578C72C97ABF966`, `msg09=A1235A1DFCE4AE57B23B2986BEF890C5018B19FDB89DA1563B679E369E014B9F`. 독립 verifier 2회 PASS, 32 msg09 relocation 및 active Japanese residue 0을 재확인했다.
- **권위 자료:** `analysis/v131_issue117_120_targets.json`, `analysis/v131_issue117_120_report.json`, `tools/build_sangokushi2_v131_issue117_120.py`, `tools/verify_sangokushi2_v131_issue117_120.py`.
- v131의 #117~#119는 v132에 그대로 계승한다. #120은 아래 v132 구조 복구가 최신 권위다.

## 현재 revision — v132-intermediate #120 msgsec06 mixed-header 구조 복구 (2026-09-02)

- **증거:** 사용자가 일본어판에서 한글판과 동일한 시점에 `都市の規模が / 中規模になりました！`가 출력되는 것을 확인했다. 한글판의 `協力してくれた… / 約束通りの支払いが出来ず`는 잘못된 메시지다. 정상 한국어는 현재 msgsec06 body에 이미 있는 **`도시 규모가 / %s 규모가 되었습니다!`**다.
- **근본 원인:** immutable Original `msgsec06.dat`를 75개 parent `offset_words`와 대조하면 **header word[0..74] = 75개 parent WORD offset**, **word[75..160] = 86개 absolute BYTE pointer(84 fragment-start + 2 inside-fragment)**다. v54의 `sangokushi2_msgsec06_flat_header.py`/`repair_msg06()`는 first 75도 BYTE pointer라고 잘못 해석해 75개 중 74개를 덮어썼다. formal v53 parent[74]는 `0xB19`인데 v54에서 `0xA94`로 변했다. 이 v54가 현재까지 확인된 최초 손상 revision이다.
- **#120 실제 경로:** 정상 parent[74] `0xB19` ×2 = byte `0x1632`; 여기에는 최신 body의 `까？`가 있고, `05 05` 뒤 `0x1638`에 **`도시 규모가 / %s 규모가 되었습니다!`**가 있다. v132은 이 구조를 복구한다.
- **수정:** `msgsec06` first 75 parent WORD table만 formal v53 정상 값으로 복구한다. 후반 86 BYTE pointer와 byte 322 이후 body는 v131 byte-exact다. v131의 잘못된 #120 `msgsec09` 32-pointer 변경만 제거하고 `msgsec09`를 exact pre-v131/v130 상태로 복원한다. 전체 v130 롤백이 아니다.
- **보존:** #117~#119의 `ExeFS/code.bin`, `font.g1t`, `msgsec07.dat`는 v131 byte-exact다. 사용자 추가 `ExeFS/icon.bin`도 baseline에 봉인하고 byte-exact 보존한다. Original/Rebuild/Backup은 미수정이다.
- **동일 구조 감사:** Original `msgsec04/07/08/09`도 각각 parent WORD prefix **24/177/60/41개**가 100% parent `offset_words`와 일치한다. 현재 Patch에서 WORD offset으로 볼 때 OOB parent entry는 각각 **3/86/30/31개**다. v132에서는 사용자 요청대로 msgsec06만 고쳤다. 나머지는 #120 Citra 성공 확인 후 별도 revision에서 다룬다.
- **SHA/검증:** `msgsec06=CF6BEDFA252880E415413D0A88CC663B4B5CDB8413EB1744A1DBDAB9F1877E02`, `msgsec09=6BB0E83367EAF9CB590AE905C4C36D7A347B4F0D7D04F220D7FB41FBAA174EC4`. 독립 verifier가 Original 75 WORD + 84 BYTE-start + 2 BYTE-inside 모델, parent[74]→`0x1632`→`0x1638` 경로, body/aux pointer 보존을 재검증했다. 결정적 재빌드 전후 SHA 동일.
- **권위 자료:** `analysis/v132_issue120_msgsec06_mixed_header_targets.json`, `analysis/v132_issue120_msgsec06_mixed_header_report.json`, `analysis/v132_issue120_msgsec06_mixed_header_baseline/PatchSnapshot`, `tools/build_sangokushi2_v132_issue120_msgsec06_mixed_header.py`, `tools/verify_sangokushi2_v132_issue120_msgsec06_mixed_header.py`.
- **Citra 결과: v132 실패.** 로드 직후 일본어 혼입 `~할 말이 있다 합니다 / 들어보시겠습니까?` 계열 팝업이 새로 발생했고 여러 텍스트 위치가 동시에 어긋났다. 따라서 **v132는 승인본/새 작업 베이스가 아니다.**

## #120 후속 handoff — dual-use parent/direct table 감사 (새 revision 미발행)

- **핵심 구조:** `msgsec06` first-75는 한 값이 두 lookup에 재사용된다. parent-route에서는 WORD offset(`value*2`), direct-route에서는 같은 값을 BYTE offset으로 사용한다. Original word[1..74]의 BYTE 해석은 72 fragment-start + 2 inside-fragment로 74/74 정확히 매핑된다.
- **#120 word[74] dual owner:** Original `0x07D3`은 parent-route에서 parent74 `スカ? / 都市ﾉ規模ｶﾞ...`, direct-route에서 `その君主とは / 同盟関係ガアリマセン`을 소유한다. 따라서 **whole-prefix restore도, header[74] 2-byte 단독 수정도 금지**한다. 후자는 도시 이벤트와 외교 direct 문구를 서로 맞바꾸는 부작용이 생길 수 있다.
- **왜 v132가 무너졌는가:** v54가 번역 repack 후 깨진 dual-use invariant를 direct-route 쪽만 맞추기 위해 first75 중 74개를 BYTE pointer처럼 덮어썼고, 이후 first75 중 12개(6,15,21,28,39,40,48,53,56,65,69,71)가 v55/v81/v101/v120에서 EOF direct redirect로 추가 변경됐다. v132의 old parent 일괄 복구가 이 direct 의존성을 한꺼번에 제거해 회귀했다.
- **msgsec06 전수 결과:** 75 parent 중 formal-v53 parent word가 현재 유지된 것은 1개, v54 값 잔존 62개, 후속 direct redirect 12개다. current word를 parent offset으로 사용하면 OOB 14개, 다른 in-bounds 문장 60개다. 이는 **잠복 위험 inventory**이며 실제 노출 여부는 caller/Citra로 판정한다.
- **다른 bank:** Original dual-use 구조는 `msgsec04 23/23`, `msgsec07 175/176(+특수1)`, `msgsec08 59/59`, `msgsec09 40/40`에서도 확인됐다. 과거 all-byte 규칙을 이 bank들에도 그대로 적용하지 않는다.
- **다음 구현 방향:** 현재 동작 중인 direct pointer table을 보존하고 **parent lookup만 별도 shadow table/common getter로 분리**하는 방식을 최우선으로 추적한다. 공통 getter를 찾지 못하면 일본판/Citra에서 parent-route가 증명된 이벤트 caller만 개별 우회한다. 전체 텍스트 재배치 금지.
- 메시지 로더가 `Message/msgsec%02u.dat` 22개를 연속 버퍼에 올리고 section별 시작 오프셋을 관리하는 것까지 code.bin에서 확인했다. 다음 작업자는 이 객체의 `(section,parent)` getter를 우선 역추적한다.
- 권위 감사: `analysis/v133_msgsec06_parent_route_audit.json`, `analysis/v133_dual_use_message_format_audit.json`, `tools/audit_sangokushi2_v133_msgsec06_parent_routes.py`. 이 감사 결과를 바탕으로 아래 v133 dual-mirror 최소 복구를 발행했다.

## 현재 revision — v133-intermediate #120 dual-mirror 최소 구조 복구 (2026-09-02)

- **베이스:** v132의 broad msgsec06 rewrite는 상속하지 않는다. sealed v131 `msgsec06.dat`(SHA `07D472269B436FCDB519E7CD86F3CE3E95A6711B2D4862E15EA7391FBDCFBEF2`)를 다시 source로 사용하고, v132에서 되돌린 pre-v131/v130 `msgsec09.dat`는 그대로 유지한다.
- **runtime 증거:** direct getter `code.bin 0x1BF0F4`는 message ID를 section/index로 나눈 뒤 `word[index+1]` 값을 BYTE offset 그대로 더한다. 따라서 shared prefix는 실제로 direct BYTE pointer로 사용된다. parent 쪽은 같은 shared 값을 WORD offset으로 사용하는 별도 경로가 존재하며 #120 Original `word[74]=0x07D3`이 두 의미를 동시에 만족한다.
- **#120 dual-mirror:** v131 direct source `0x0A94..0x0AB7 = 그 군주와는 / 동맹 관계가 아닙니다`; parent source `0x1632..0x165C = 까？ + 도시 규모가 / %s 규모가 되었습니다!`. fresh `V=0x1B00`을 사용해 `word[74]=0x1B00`; byte `0x1B00`에 direct source를 exact mirror하고 byte `0x3600=2V`에 parent source를 exact mirror한다. 기존 v131 body byte322~EOF는 byte-exact, header 161 words 중 기존 영역 diff는 word74 하나뿐이다.
- **왜 안전한가:** v132 broad restore처럼 다른 shared entry를 건드리지 않는다. `0x1528=old 0x0A94*2`는 다른 활성 condition-prompt fragment 내부이므로 overwrite하지 않는다. 기존 direct route도 `0x1B00` mirror로 보존하고 parent74도 `0x3600` mirror로 분리해 동일 word의 두 runtime 의미를 모두 살린다.
- **변경 범위:** v132 대비 실제 게임 파일 변경은 `RomFS/Message/msgsec06.dat` 하나뿐이다. `code.bin/font/msg07`의 #117~#119, v132의 restored `msgsec09`, 사용자 `ExeFS/icon.bin`, 기타 Patch는 byte-exact. msgsec06 size `6834→13866`.
- **검증:** 독립 runtime simulation은 direct index73→`그 군주와는 / 동맹 관계가 아닙니다`, parent74→`까？`→`도시 규모가 / %s 규모가 되었습니다!`를 확인했다. builder 2회 동일 SHA, verifier 2회 PASS. 최종 `msgsec06 SHA-256=6B0FA61A74E81FCE3F4D025E3D3A6487B031ADEF34B8A3C16A2F6FDFB0FEB20F`.
- **권위 자료:** `analysis/v133_issue120_dual_mirror_targets.json`, `analysis/v133_issue120_dual_mirror_report.json`, `tools/build_sangokushi2_v133_issue120_dual_mirror.py`, `tools/verify_sangokushi2_v133_issue120_dual_mirror.py`.
- **Citra 결과: 실패.** 최종 v133에서도 #120 도시 발전 시 정상 문구 대신 깨진 일본어/한자 혼합 문자열이 출력됐다. 따라서 `word[74]`를 도시 이벤트의 parent owner로 본 v133 dual-mirror 판정은 폐기한다. v133은 현재 Patch 권위가 아니다.

## 직전 revision — v134-intermediate #120 마지막 direct pointer 복구 (2026-09-02)

- **실제 runtime 구조 재확정:** immutable Original `code.bin`의 getter `VA 0x001BF0F4`를 ARM 단위로 재분석했다. message ID는 `section=id//1000`, `index=id%1000`으로 나뉘며, getter는 section base에서 `word0`을 count로 읽은 뒤 `u16(section_base + 2 + index*2)`를 가져와 **BYTE offset 그대로** section base에 더한다. `msgsec06 word0=161`은 header 전체 word 수가 아니라 뒤에 오는 direct entry 수다.
- **#120 실제 owner:** 따라서 direct table은 `word[1..161]`의 161개다. immutable Original의 `word[161]=0x0FAD`는 정확히 `都市ﾉ規模ｶﾞ\n%s規模ﾆﾅﾘﾏｼﾀ!` 시작을 가리킨다. #120은 parent74가 아니라 **section6 direct index160(message ID 6160)**이다. 과거 extractor/repacker가 `word[0..160]`까지만 header로 잘라 마지막 entry를 빠뜨린 것이 근본 원인이다.
- **왜 깨졌는가:** sealed v131/v133 `msgsec06`의 byte `0x142..0x143`은 번역 body의 첫 2바이트 `0F 8D`로 덮여 `word[161]=0x8D0F`가 됐다. v131 size 6834B보다 큰 OOB pointer다. 런타임이 direct index160을 조회하면 파일 밖을 읽으므로 제보된 무관한 일본어/한자 혼합 출력이 발생할 수 있다.
- **최소 수정 구현:** v133의 speculative `0x1B00/0x3600` dual-mirror와 큰 padding은 상속하지 않는다. sealed v131 `msgsec06`를 source로 사용해 direct index0~159(`word[1..160]`)과 기존 body를 byte-exact로 보존하고, 실제 header 마지막 `word[161]` 한 곳만 EOF `0x1AB2`로 변경했다. 새 target은 v131 `0x1638..0x1659`에 이미 존재하는 한글 바이트 **`도시 규모가\n%s 규모가 되었습니다!`**를 그대로 복제했다. `word[74]`는 v131 direct 값 `0x0A94`로 돌아간다.
- **주변 감사:** v131 direct index0~159는 160/160 전부 파일 내부였고, 인접 145~159도 모두 유효했다. v134에서는 index160까지 포함해 161/161 in-file이다. `msgsec06` 안에서는 동일한 OOB 문제가 index160 한 곳뿐이므로 다른 포인터는 수정하지 않았다.
- **다른 bank 동일형 후보(읽기 전용):** 공통 getter 규칙으로 마지막 entry만 대조했을 때 `msgsec07 word[388]=0xEA88`과 `msgsec21 word[67]=0x8C0E`가 각각 현재 파일 밖을 가리키며, immutable Original의 같은 word는 정상 마지막 문자열을 가리킨다. 구조상 동일 off-by-one의 잠복 후보지만 Citra/runtime caller가 아직 증명되지 않았으므로 **이번 v134에서 수정하지 않는다.** `msgsec09`는 후속 작업에서 word0 자체가 repurpose된 특수 상태라 이 단순 감사에서 제외한다. 상세=`analysis/v134_last_direct_entry_crossbank_audit.json`.
- **변경 범위/검증:** v133 대비 변경 게임 파일은 `RomFS/Message/msgsec06.dat` 하나뿐이다. `code.bin`, font, msg07, msg09, `ExeFS/icon.bin`과 나머지는 v133 byte-exact다. msgsec06 size `6834→6871`, SHA-256=`FDE90AF5E7ED0F10756B71909768BA91221C039AAB391AE2A717FBECF51288D1`. builder + 독립 verifier를 완성본 위에서 재실행해 동일 SHA/PASS를 확인했다. verifier는 Original getter region byte-exact, Original `word161=0x0FAD`, v131 direct0~159 exact, v134 direct0~160 161/161 in-file, 인접145~160 유효성을 다시 검사한다.
- **권위 자료:** `analysis/v134_issue120_last_direct_pointer_targets.json`, `analysis/v134_issue120_last_direct_pointer_report.json`, `tools/build_sangokushi2_v134_issue120_last_direct_pointer.py`, `tools/verify_sangokushi2_v134_issue120_last_direct_pointer.py`.
- **Citra 결과:** 사용자 동일 세이브에서 깨진 문자열 대신 `도시 규모가 / 中 규모가 되었습니다!`가 정상 위치에 출력됐다. 따라서 message ID 6160의 마지막 direct pointer 복구는 runtime 성공으로 확정한다. 남은 `中/大/小`는 `%s`에 들어가는 별도 규모명 인자 문제이며 아래 v135에서 처리한다.

## 직전 revision — v135-intermediate #120 규모명 인자 대/중/소 한글화 (2026-09-02)

- **실제 owner:** `code.bin VA 0x0015B2C0`가 도시 규모 enum 0~2를 받아 `VA 0x002DC444`의 3-entry pointer table에서 문자열을 반환한다. enum0=`大@file+0x1CFFE4`, enum1=`中@+0x1CFCDC`, enum2=`小@+0x1CFE50`. #120 caller가 이 반환값을 message ID 6160의 `%s` 인자로 넘긴다.
- **수정:** standalone 2B 문자열만 same-width로 `大 91E5→대 894C`, `中 9286→중 8A49`, `小 8FAC→소 8949`로 교체했다. 각 NUL padding, selector instruction, pointer table 자체는 byte-exact다. 기존 검증된 한글 alias를 재사용하므로 `font.g1t`는 변경하지 않는다.
- **보존:** v134 `msgsec06.dat`는 byte-exact이며 SHA-256=`FDE90AF5E7ED0F10756B71909768BA91221C039AAB391AE2A717FBECF51288D1`. v134 대비 게임 파일 diff는 `ExeFS/code.bin` 하나뿐이고 새 SHA-256=`91ADE4D3A12D7DB8A0AAC140073560612DF232A7BA4A44D46430B017C4CF3959`다.
- **검증:** builder→독립 verifier→builder→verifier를 실행해 동일 SHA, 3개 alias readback, pointer table `[0x2CFFE4,0x2CFCDC,0x2CFE50]`, selector machine-code, v134 msgsec06 hash 보존을 모두 PASS했다.
- **잠복 후보:** `msgsec07` 마지막 direct index387의 Original 원문은 `長期戦中ノ都市ヘハ移動デキマセン`(장기전 중인 도시로는 이동할 수 없습니다), `msgsec21` index66은 `サラニハコノ混乱ニ乗ジテ / 黄巾ノ残党モ再興ノ兆シヲ見セ / 新皇帝ノ治世ハ波乱ノ幕開ケトナッタ`(혼란을 틈탄 황건 잔당의 재흥 조짐과 새 황제 치세의 파란 개막 서술)다. 둘 다 현재 pointer는 OOB지만 실제 runtime caller는 아직 확인하지 못했으므로 수정하지 않는다. 이 문구 또는 깨진 변형이 실화면에 나오면 우선 해당 후보를 의심한다.
- **권위 자료:** `analysis/v135_issue120_scale_labels_targets.json`, `analysis/v135_issue120_scale_labels_report.json`, `tools/build_sangokushi2_v135_issue120_scale_labels.py`, `tools/verify_sangokushi2_v135_issue120_scale_labels.py`.
- **직전 Patch 권위는 v135-intermediate.** 기대 화면은 `도시 규모가 / 소 규모가 되었습니다!`, `... 중 규모 ...`, `... 대 규모 ...`다. 규모명 3종의 실화면 확인만 남았다.

## 직전 revision — v136-intermediate #118/#121/#122 runtime 재수정 (2026-09-02)

- **베이스/변경 범위:** exact v135 Patch를 sealed baseline으로 사용한다. 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec10.dat` 두 개뿐이다. `font.g1t`, `msgsec06/07/15/21.dat`, `fix_data.bin`과 나머지 Patch는 v135 byte-exact이며 `Original / Rebuild / Backup`은 수정하지 않았다.
- **#118 v131 방식 폐기:** v131의 `0x1F3F00/0x1F3F20` code tail NUL slack relocation은 정적 verifier가 PASS했지만 사용자 Citra에서 `%s`가 비고 이벤트명도 사라져 실패가 확정됐다. 실제 ARM caller에서 `0x1E5ECC`가 formatter `%s` 인자로 쓰이는 것은 맞았으므로, pointer 위치보다 **tail slack의 runtime 유효성**이 문제였다. v136은 tail 두 영역을 0으로 되돌리고 모든 live pointer를 기존 runtime pool/fixed span으로 복귀시킨다.
- **#118 최신 target:** `0x1CFE5C..0x1CFE6B`에 `메뚜기 떼\0없음\0`, event pointer `0x1DC428→0x002CFE5C`, sibling pointer `0x1DC58C→0x002CFE66`; `0x1D3004..0x1D3013`에 `발생하였습니다.`, `0x1E5ECC→0x002D3004`. formatter는 `메뚜기 떼가 %s`. 기대 화면은 **`메뚜기 떼가 발생하였습니다.` / `메뚜기 떼`**다. 앞으로 Citra/loader로 증명되지 않은 `code.bin` tail NUL slack은 새 문자열 relocation에 사용하지 않는다.
- **#121 외교:** 스크린샷의 `対象` 실제 source는 `code.bin 0x0B4654`이며 **`대상`**으로 same-size 치환했다. 외교 label pointer table 전수 역읽기에서 남은 일본어 `停戦@0x1CEE4C`만 **`정전`**으로 바꿨다. 최종 7개는 `동맹 / 공동작전 / 정전 / 교환 / 원조 / 항복권고 / 동맹파기`. 상단 prompt는 allocation을 지키기 위해 전각 `？` 대신 ASCII `?`를 사용한 **`%s님, 어떻게 할까요?`**다. reviewed pointer range `0x1E501C..0x1E51AC`의 일본어 잔여는 0건이다.
- **#122 위임 메뉴:** `도시단위`, `군사형 / 생산형 / 적응형`, `직할`, **`위임을 해제합니까?`**, stale fallback `위임/직할`은 정상 적용됐다. 다만 `전체위임 / 전체직할`을 두기 위해 잡았던 `0x1D188E..0x1D189F`는 후속 Citra에서 **앞의 live string `튜토리얼@0x1D1886..0x1D188D`의 종료 NUL `0x1D188E`부터 시작하는 잘못된 span**임이 확인됐다. 그래서 v136은 `튜토리얼전체위임` 회귀를 만들었으며, 이 relocation 위치만 아래 v137에서 폐기/이동한다.
- **#122 상단 설명 `msgsec10`:** fixed-position/no-repack으로 6개를 `군비를 강화해 영지를 늘립니다. / 군량 수입과 상업을 발전시킵니다. / 상황에 맞게 대응합니다 / 모든 속령을 같은 정책으로 위임 / 군사·장군 태수의 위임을 모두 해제합니다. / 도시별 정책을 설정합니다.`로 교체했다. 파일 크기 6884B와 reviewed span 밖 바이트는 보존한다. `msgsec15` 위임 상세 도움말은 이미 한국어이므로 그대로 둔다.
- **검증:** builder→독립 verifier→builder→verifier를 완성본에서 실행해 동일 SHA/PASS를 2회 확인했다. `code.bin SHA-256=444BDDEFB84ECA64C38359F86BB12FD183448B49E0FE0CCD28CE9966E5D7BA02`, `msgsec10.dat SHA-256=344532CCF45FBE6FD944F6D1A1DCC2AF8655654A8CFE09E26C42E43D4509B50C`.
- **권위 자료:** `analysis/v136_issue118_121_122_targets.json`, `analysis/v136_issue118_121_122_report.json`, `tools/build_sangokushi2_v136_issue118_121_122.py`, `tools/verify_sangokushi2_v136_issue118_121_122.py`.
- **v136 Citra 결과:** 사용자가 #121 외교 텍스트와 #122 위임 화면의 한글화 자체는 정상 확인했다. 단 메인 메뉴의 `튜토리얼` 버튼에 `전체위임`이 붙는 회귀가 발견되어 v136은 현재 기준본이 아니다. #118 메뚜기 이벤트는 아직 별도 runtime 확인 기록이 없다.

## 현재 revision — v137-intermediate 튜토리얼 종료 NUL hotfix (2026-09-03)

- **베이스/목표:** exact v136을 sealed baseline으로 삼고 `ExeFS/code.bin` 한 파일만 수정한다. v136에서 Citra 성공한 #121 외교, #122 위임 메뉴/상단 설명의 나머지 변경은 그대로 보존한다.
- **원인:** v135/v136 baseline의 `튜토리얼`은 `0x1D1886..0x1D188D` 8B이며 `0x1D188E`가 정확한 C-string 종료 NUL이다. v136은 `전체위임` payload를 `0x1D188E`부터 기록해 이 종료자를 지웠고, 따라서 Citra가 `튜토리얼전체위임`을 하나의 문자열로 읽었다.
- **새 안전 위치:** `code.bin` 내부 string pool의 28B NUL run `0x1CF143..0x1CF15E`를 사용하되 첫 NUL `0x1CF143`은 직전 문자열 terminator guard로 남긴다. `전체위임`=`0x1CF144`, `전체직할`=`0x1CF14D`, payload 끝 `0x1CF155`; 뒤 `0x1CF156`도 NUL guard로 유지한다. payload 18B에는 v136 기준 absolute pointer ref 0, ARM ADR ref 0을 확인했다.
- **pointer/복구:** `0x1AB3D4/0x1E48BC→0x002CF144`, `0x1AB3D8/0x1E48C0→0x002CF14D`. v136의 `0x1D188E..0x1D189F`는 18B 모두 0으로 복원해 `튜토리얼` 종료 NUL을 되살린다.
- **검증:** v136 대비 changed file은 `ExeFS/code.bin` 하나뿐. final SHA-256=`271C4D0E847B470DD2589E6DDA4758DE81AE8EB845182002ABD88D1CAF439667`. builder→독립 verifier→builder→verifier 2회 동일 SHA/PASS. verifier는 exact v136+hotfix composition, `튜토리얼` C-string readback 및 `0x1D188E` terminator, 새 relocation 양쪽 guard NUL, 4개 redirect, v136 `msgsec10.dat` 보존을 확인한다.
- **권위 자료:** `analysis/v137_tutorial_terminator_hotfix_targets.json`, `analysis/v137_tutorial_terminator_hotfix_report.json`, `tools/build_sangokushi2_v137_tutorial_terminator_hotfix.py`, `tools/verify_sangokushi2_v137_tutorial_terminator_hotfix.py`.
- **직전 Patch 권위는 v137-intermediate.** Citra에서 메인 메뉴가 `튜토리얼` 단독으로 돌아오고 `전체위임/전체직할`도 계속 정상인지 확인하면 hotfix를 runtime 성공으로 승격한다.

## 직전 revision — v138-intermediate #123/#124/#125/#126 + 동일 오류 class 감사 (2026-09-03)

- **베이스/변경 파일:** exact v137을 `analysis/v138_issue123_126_baseline/PatchSnapshot`으로 봉인했다. 변경 게임 파일은 `ExeFS/code.bin`, `RomFS/Message/msgsec04.dat`, `msgsec06.dat`, `msgsec07.dat`, `msgsec11.dat`, `msgsec21.dat` 6개뿐이다. Original/Rebuild/Backup은 수정하지 않는다.
- **#123:** `msgsec11 0x4C7`의 v137 시작 `02`는 잘린 동적 숫자 토큰이었다. Original의 **`02 28`**을 복원하고 뒤를 `번째 군주를 선택`으로 유지한다. 이 화면은 고정 한글 서수를 저장하는 것이 아니라 runtime 숫자가 앞에 붙어 `1번째/2번째...`로 조립되는 구조다.
- **#124:** `0x17F350=이제는 `, `0x17F35C=이제 `처럼 spare NUL을 이용해 동적 이름 앞 공백을 복구했다. 호위 장수 확인은 `0x181264=%s(으)로 할까요？`. 동일 공백 class는 **실제 padding + interior pointer 0 + 줄바꿈 안전**이 모두 증명된 경우만 수정한다. 추가 안전 수정은 `%s 태수 %s이(가)`, `%s의 %s이(가)`, `%s 부군사 %s`, msgsec04 동적 이름 앞 공백 2건이다. 동적 이름 2개 때문에 최대 폭이 불명확하거나 다른 포인터 해석이 span 내부에 들어오는 후보는 건드리지 않는다. `치욕`의 `욕=98A5`는 제보 renderer에서 실패하므로 live 3곳을 same-width **`수치`**로 바꿨다.
- **#125:** `천하는 더?`의 `?`는 물음표가 아니라 `욱=989C` 실패 alias다. 최신 fixed target은 **`동탁타도에 모인 연합은 / 뜻을 이루지 못하고 해산했다. / 천하는 더 혼란해진다...`**이며 `msgsec11 0x42C` fragment boundary는 문자 경계와 일치한다.
- **#126 폭:** 시나리오 narrative에서 19.0-unit 줄 마지막 글자가 잘린 Citra 증거를 기준으로 `>18.5 units`를 감사했다. 실제 두 행을 **`영웅들이 패권을 놓고 싸운 난세였다`**, **`사태는 군웅할거 양상으로 변했다`**로 줄여 v138 기준 초과 0건이다. 제어 토큰 `<$05>` 등은 visible width에 포함하지 않는다.
- **#126 마지막 direct entry:** `msgsec21 word[67]=0x8C0E`는 v134에서 latent로 기록됐던 OOB 후보이며 이번 제보로 실제 runtime 노출 근거가 생겼다. 같은 mechanical class의 유일한 다른 잔여는 `msgsec07 word[388]=0xEA88`. 두 u16은 body와 겹치므로 값을 바꾸지 않고 기존 prefix를 byte-exact 보존한 채 해당 주소까지 zero padding하고 target을 append했다. 최종 `msgsec21 size=35949`, `0x8C0E -> 게다가 이 혼란을 이용해...`; `msgsec07 size=60082`, `0xEA88 -> 장기전 중인 도시로는 이동할 수 없습니다`. v138 완성본의 same-class last direct OOB는 0건이다.
- **최종 SHA-256:** `code=17885F886943972D2F9E3FAC5625E315B10AC0BFBC857787D6CE75C65E9C36AA`, `msg04=4A54E6EEAEB34935C155E651283587FC1961BB5894161C15682EE040BDFAD003`, `msg06=B159DC7A815E14641EB58DEEE334C7A75B184CC04D6A97789318CD5472CE0615`, `msg07=4A897D3A5BB06106B779F5685097658B36C8E7659C9FB0EABA76D5ED77AF4314`, `msg11=CA86107E6E8876EADC47534BE3D2F7C6BB3A4523AF90FD1D874C7B47C6BDF8D9`, `msg21=6F7B2132E07DEE58E9BFD2FD4C877E465AEF69ABADB4D000B7A29416FAB3BDE9`.
- **검증:** builder→독립 verifier→builder→verifier PASS. verifier는 exact v137+v138 조성, #123 `02 28`, #124 spacing/수치, #125 `989C=0`, #126 over-width=0, 두 last direct pointer in-file, non-target Patch byte-exact를 검사한다. v103 legacy audit의 `active 7 / mixed 11`은 sealed v137과 동일해 신규 회귀가 아니다.
- **권위 자료:** `analysis/v138_issue123_126_targets.json`, `analysis/v138_issue123_126_report.json`, `tools/build_sangokushi2_v138_issue123_126.py`, `tools/verify_sangokushi2_v138_issue123_126.py`.
- **직전 Patch 권위는 v138-intermediate.** 다음 Citra 검수 우선순위는 #123 다중 군주 선택, #124 항복권고/공백/수치, #125 숨김 시나리오 설명, #126 연속 시나리오 대사 및 `장기전 중인 도시` 메시지, 그리고 v137의 `튜토리얼/전체위임/전체직할` 회귀 여부다.

## 현재 revision — v139-intermediate 지정 PNG 19개 이미지 전용 리빌드 (2026-09-03)

- **베이스:** exact v138 Patch 81파일을 `analysis/v139_image_update_baseline/PatchSnapshot`으로 봉인했다. `tools/verify_sangokushi2_v138_issue123_126.py` 재실행 PASS 후 작업했고 Original/Rebuild/Backup은 수정하지 않았다.
- **지정 PNG만 반영:** `ChangeBGM_000` 1개, `option_down_017/019/021/023/025/027/029/031` 8개, `BGMDown_002~011` 10개 = 정확히 **19개**다. 사용자가 수정한 PNG를 그대로 사용했으며 이미지 생성은 하지 않았다.
- **base 선택:** `option_down.g1t`는 최신 v138 Patch 컨테이너 SHA `B974ED2602BA3A94D5A46B28977A828346CEF193E51B753AD6BEFAB2BEE9D256`에서 시작했다. `ChangeBGM.g1t`/`BGMDown.g1t`는 v138 Patch에 없었으므로 이번 최초 편입에 한해 immutable Original을 사용했다. v139 이후 두 BGM 컨테이너도 Patch에 존재하므로 다음 수정부터는 반드시 최신 Patch를 base로 한다.
- **구조/픽셀 검증:** 대상 전부 type `0x09 RGBA8`; source decode→encode identity PASS. 각 G1T header와 비대상 texture payload는 base와 byte-exact이고, built Patch를 다시 decode한 결과 **19/19 사용자 PNG pixel-exact**다. v138의 `code.bin`, `font.g1t`, 모든 Message 및 다른 이미지 컨테이너는 byte-exact다.
- **변경 파일/해시:** v138 대비 정확히 `RomFS/Common/BGM/ChangeBGM.g1t`, `RomFS/StartMenu/option_down.g1t`, `RomFS/Common/BGM/BGMDown.g1t` 3개만 변경/추가됐다. Patch 파일 수 81→83. SHA-256은 `ChangeBGM=CDB093D6A8E109E0AC29BA5FE71388BD8CF758DE5E77C425691B67B38F274A38`, `option_down=B7C56B0EDE8F4E8C630B6029A02091537CD3B5156E91386AD8B988298482E676`, `BGMDown=F5016473EAE2BC69DFAB89314F63178AB4608C6CF4D385DE670FBBC00F377ED6`다.
- **결정성:** builder→독립 verifier→builder→verifier 2회 PASS했고 세 G1T와 `v139_image_update_report.json` SHA까지 동일했다. report SHA=`A84374B0B4B8B5A82ADC724F9913DD30CD1D9F8B3F20B5157C5503A117B06FDD`.
- **권위 자료:** `analysis/v139_image_update_targets.json`, `analysis/v139_image_update_report.json`, `analysis/v139_image_update_baseline/PatchSnapshot`, `tools/build_sangokushi2_v139_image_update.py`, `tools/verify_sangokushi2_v139_image_update.py`.
- **현재 Patch 권위는 v139-intermediate.** Citra에서 BGM 변경 화면/하단 BGM 항목/옵션 하단 지정 버튼과 v138 누적 텍스트 영역의 회귀 여부를 확인한다.
