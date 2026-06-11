# my-stock-team

단기 모멘텀(시간축 1주일) 관점으로 한국·미국 대형주를 리서치하는 Claude Code 플러그인입니다. 역할별 분석가 서브에이전트가 병렬로 분석하고, 검증 애널리스트가 품질을 점검한 뒤, 디자인된 PPTX 리포트로 내보냅니다.

> 학습·연구 목적의 분석 도구이며 투자 권유가 아닙니다. 매수·매도·목표가 단정을 만들지 않습니다.

## 구성 요소

### 커맨드 (`commands/`)
- **`/my-stock-team:analyze <종목>`** — 전체 파이프라인: 분석가 병렬 분석 → 헤드 종합(`reports/{종목}.md`) → 검증 게이트 → `reports/{종목}.pptx`.
- **`/my-stock-team:verify <종목>`** — 완성 리포트만 품질 점검(통과/보류).

### 서브에이전트 (`agents/`)
| 에이전트 | 역할 | 비중 | 데이터 | 커버리지 |
|---|---|---|---|---|
| `market-technical-analyst` | 추세·거래량·돌파 | 핵심 | FinanceDataReader | 한국+미국 |
| `news-sentiment-analyst` | 뉴스·수급·임박 이벤트 | 핵심 | 웹서치 | 한국+미국 |
| `risk-manager-synthesizer` | 급락·고점추격·변동성·유동성 | 견제 | pykrx | 한국 전용 |
| `fundamental-analyst-dart` | 실적쇼크·재무위험 필터 | 보조 | DART | 한국 전용 |
| `verification-analyst` | 리포트 품질 검증(읽기 전용) | 게이트 | — | — |

### 스킬 (`skills/`)
- **`report-pptx`** — `reports/{종목}.md`를 읽어 디자인된 `reports/{종목}.pptx`로 변환. KB 옐로우(#FFBC00) 포인트색, 맑은 고딕, 증권사 리서치 톤, 출처 자동 수집, 표 넘침 방지.

## 설치

마켓플레이스로 추가한 뒤 설치합니다(이 폴더가 곧 단일 플러그인 마켓플레이스입니다).

```
/plugin marketplace add <이 저장소 경로 또는 URL>
/plugin install my-stock-team@my-stock-team
```

## 사전 준비 (사용자 각자)

이 플러그인은 **비밀값을 포함하지 않습니다.** 펀더멘털(DART) 분석을 쓰려면 각자 키를 준비하세요.

1. Python 패키지 설치:
   ```
   pip install -r requirements.txt
   ```
   (또는 `python-pptx FinanceDataReader pykrx OpenDartReader python-dotenv` 개별 설치)
2. 프로젝트 루트에 `.env` 생성 후 본인 DART OpenAPI 키 입력 — **저장소에 커밋 금지**:
   ```
   DART_KEY=발급받은_키
   ```
   DART 키 발급: https://opendart.fss.or.kr (무료)
3. 한글 폰트 **맑은 고딕**: Windows 기본 탑재. 비Windows에서 PPTX의 한글이 깨지면 맑은 고딕(또는 대체 한글 폰트)을 설치.

## 사용 예

```
/my-stock-team:analyze 삼성전자
/my-stock-team:analyze SK하이닉스
/my-stock-team:verify 삼성전자
```

산출물은 현재 프로젝트의 `reports/` 폴더에 `{종목}.md`, `{종목}.pptx`로 저장됩니다.

## 데이터 출처

FinanceDataReader · pykrx · DART OpenAPI · 공개 웹 뉴스. 모두 무료 공개 데이터이며, 리포트의 모든 수치에 출처·기준일을 병기합니다.
