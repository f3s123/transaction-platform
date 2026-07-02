# Secure Coding

## Tiny Secondhand Shopping Platform.


```
git clone https://github.com/f3s123/secure-coding
pip install -r requirements.txt
```

## usage

run the server process.

```
python app.py
```

### Login

- 관리자 계정:  
  **ID**: `123`  
  **PW**: `123`

- 일반 유저 계정:  
  **ID**: `qwer`  
  **PW**: `qwer11!!`

# Secure Transaction Platform

Flask 기반 중고거래 플랫폼으로, **시큐어코딩 관점에서 웹 서비스의 보안 취약점을 진단하고 대응**하는 데 중점을 두었습니다.
빠르게 구현한 초기 버전에서 발견되는 보안 허점을 체크리스트로 정리하고, 소스코드 수준에서 하나씩 점검, 보완했습니다.

<br>

## 목차
- [주요 기능](#주요-기능)
- [시큐어코딩 적용 사항](#시큐어코딩-적용-사항)
- [기술 스택](#기술-스택)
- [실행 방법](#실행-방법)
- [프로젝트 구조](#프로젝트-구조)

<br>

## 주요 기능

| 영역 | 기능 |
|------|------|
| 회원 | 회원가입, 로그인, 프로필(소개글/비밀번호 수정), 사용자 조회 |
| 상품 | 상품 등록/조회/상세보기/수정/삭제, 내가 등록한 상품 관리 |
| 검색 | 상품명/설명 기반 검색 |
| 소통 | 실시간 전체 채팅, 1:1 채팅 |
| 신고 | 상품/사용자 신고, 신고 누적 시 상품 차단/사용자 휴면 전환 |
| 송금 | 판매자 계좌 기반 구매/송금 흐름 |
| 관리자 | 상품 삭제, 사용자 계정 삭제, 휴면 계정 설정/해제 |

<br>

## 시큐어코딩 적용 사항

초기 코드의 취약점을 영역별로 점검하고 아래와 같이 보완했습니다.

### 인증 / 세션
- **비밀번호 암호화 저장** — 평문 저장을 `bcrypt` 해싱으로 전환
- **세션 쿠키 보안** — `HttpOnly`, `Secure` 플래그 적용
- **세션 만료 및 재인증** — 세션 타임아웃 및 민감 작업 전 재확인
- **로그인 실패 제한** — 무차별 대입(brute force) 방지를 위한 시도 횟수 제한

### 입력 검증 / XSS
- **서버측 입력 검증** — 사용자명/비밀번호 등에 길이/허용 문자/형식 검증 (클라이언트 검증 우회 대비)
- **XSS 방지** — 출력되는 사용자 입력값에 `escape()` 적용 (프로필·상품·채팅·신고 전반)

### 인젝션 / 데이터 무결성
- **SQL Injection 방지** — 모든 쿼리에 파라미터 바인딩(`?` placeholder) 사용
- **데이터 무결성 검증** — DB 저장 전 필수 항목·형식 검증

### 접근 제어
- **인증 사용자 제한** — 상품 등록/수정/삭제, 신고 등 기능을 로그인 사용자로 제한
- **관리자 권한 분리** — 관리자 기능은 `role=admin` 계정만 접근 가능
- **CSRF 보호** — `flask-wtf` 기반 CSRF 토큰으로 폼 위조 요청 차단

### 신고 · 남용 방지
- **신고 남용 방지** — 동일 대상 중복 신고 제한
- **감사 로그** — 신고 내역(신고자/대상·사유)을 DB에 기록

### 전체 시스템
- **오류 정보 노출 방지** — 디버그 모드 비활성화, 403/404/500 커스텀 에러 페이지 제공
- **HTTPS 전송** — `SESSION_COOKIE_SECURE`로 HTTPS 환경 쿠키 전송 제한

<br>

> 각 항목의 상세 점검 내용은 [`secure_coding_checklist.csv`](./secure_coding_checklist.csv) 참고

<br>

<br>

## 실행 방법

WSL 환경에서 miniconda를 사용해 실행합니다.

```bash
# 저장소 클론
git clone https://github.com/f3s123/transaction-platform.git
cd transaction-platform

# conda 가상환경 생성 및 활성화
conda env create -f environments.yaml
conda activate secure_coding

# 의존성 설치
pip install -r requirements.txt

# 서버 실행
python app.py
```

실행 후 브라우저에서 `http://127.0.0.1:5000` 접속


**관리자 계정** : `id: 123 / pw: 123`

<br>

## 프로젝트 구조

```
transaction-platform/
├── app.py                        # 메인 애플리케이션
├── templates/                    # HTML 템플릿
├── static/                       # 정적 리소스
├── secure_coding_checklist.csv   # 보안 점검 체크리스트
├── enviroments.yaml              # conda 가상환경 정의
├── requirements.txt
└── README.md
```

<br>

