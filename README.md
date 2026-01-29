# QRGenerator-Front

QR 코드 대량 생성 및 배치 업로드 웹 애플리케이션입니다. 동적 URL을 기반으로 일련번호에 따른 QR 코드를 생성하고, 암호화된 토큰과 함께 백엔드에 업로드하는 기능을 제공합니다.

## 개요

본 프로젝트는 다음과 같은 핵심 기능을 갖추고 있습니다:

- **QR 코드 일괄 생성**: 지정된 범위의 일련번호에 대해 QR 코드를 동적으로 생성합니다
- **AES 암호화 토큰**: 각 QR 코드에 암호화된 URL-safe 토큰을 포함시킵니다
- **배치 업로드**: 생성된 QR 코드(Base64 이미지)를 청크 단위로 백엔드에 전송합니다
- **진행률 추적**: 대량 업로드 시 진행 상황을 실시간으로 표시합니다
- **인증 및 라우팅**: Redux Saga를 이용한 상태 관리 및 보호된 라우트를 구현합니다

## 기술 스택

### 핵심 라이브러리
- **React 19**: UI 렌더링
- **Vite 7**: 고속 번들링 및 개발 서버
- **Redux Toolkit + Redux Saga**: 상태 관리 및 비동기 작업
- **React Router 7**: 페이지 라우팅
- **Ant Design 5**: UI 컴포넌트
- **Styled Components**: CSS-in-JS
- **QRCode**: QR 코드 생성 라이브러리
- **Axios**: HTTP 클라이언트
- **Crypto-JS**: AES 암호화
- **jszip & file-saver**: 파일 다운로드 지원

### 개발 도구
- **ESLint 9**: 코드 린팅
- **Babel**: JavaScript 변환 (Vite 플러그인 제공)

## 디렉토리 구조

```
src/
├── components/              # 재사용 가능한 컴포넌트
│   ├── QRGenerator.jsx     # QR 생성 및 업로드 로직
│   ├── QRList.jsx          # QR 목록 조회
│   ├── QRDateSearchModal.jsx   # 날짜 검색 모달
│   ├── QRSerialSearchModal.jsx # 일련번호 검색 모달
│   └── ProtectedRoute.jsx   # 인증 필요 라우트
├── pages/                  # 페이지 컴포넌트
│   ├── LoginPage.jsx       # 로그인 페이지
│   ├── MainPage.jsx        # 메인 페이지
│   └── QRGeneratorPage.jsx # QR 생성 페이지
├── features/               # Redux 상태 관리 (Feature-based)
│   ├── qr/
│   │   ├── qrSlice.js      # QR 액션 및 슬라이스
│   │   └── qrSaga.js       # QR API 비동기 작업
│   └── user/
│       ├── userSlice.js    # 사용자 액션 및 슬라이스
│       └── userSaga.js     # 사용자 API 비동기 작업
├── store/                  # Redux 스토어 설정
│   ├── index.js           # 스토어 초기화
│   ├── rootReducer.js     # 루트 리듀서
│   └── rootSaga.js        # 루트 사가
├── lib/                    # API 및 유틸리티
│   ├── api.js             # API 엔드포인트 정의
│   └── image.js           # 이미지 처리 함수
├── utils/                  # 헬퍼 함수
│   └── crypto.js          # AES 암호화 유틸
├── ui/                     # 레이아웃 컴포넌트
│   └── AppLayout.jsx      # 애플리케이션 레이아웃
└── assets/                 # 정적 리소스
    └── react.svg
```

## 주요 기능 상세

### QR 생성 로직
`QRGenerator.jsx`에서 구현된 핵심 로직은 다음과 같습니다:

1. **동적 URL 생성**: 입력된 베이스 URL과 일련번호 범위(start~end)로부터 URL을 생성합니다
2. **토큰 암호화**: 일련번호를 AES로 암호화하여 URL-safe 토큰을 생성합니다
3. **QR 코드 인코딩**: 생성된 URL을 QRCode 라이브러리로 QR 코드로 변환합니다
4. **Base64 변환**: QR 이미지를 Base64로 인코딩하여 용량을 축소합니다

### 배치 업로드 프로세스
```javascript
// 청크 단위: 1개씩
// 간격: 500ms
// DTO 구조:
{
  image: "Base64 인코딩된 QR 이미지",
  qrUrl: "생성된 URL",
  serial: "일련번호(4자리)",
  createdDate: "",
  itemName: "maruon"
}
```

안전장치로서 1000건 이상의 배치는 확인 다이얼로그를 표시합니다.

### 상태 관리 구조
Redux Saga를 통해 다음을 관리합니다:
- 사용자 인증 상태(`userSlice`)
- QR 생성/업로드 상태(`qrSlice`)
- API 요청/응답 처리(`userSaga`, `qrSaga`)

## 설치 및 실행

### 의존성 설치
```bash
npm install
```

### 개발 서버 실행
```bash
npm run dev
```
포트 5173에서 HMR(Hot Module Replacement)을 지원하며 실행됩니다.

### 빌드
```bash
npm run build
```
`dist/` 디렉토리에 최적화된 번들이 생성됩니다.

### 린팅
```bash
npm run lint
```

### 미리보기
```bash
npm run preview
```

## 환경 변수

모든 환경 변수는 Vercel의 "Settings > Environment Variables"에서 관리됩니다. 다음은 각 변수의 역할입니다:

| 변수명 | 설명 | 비고 |
|--------|------|------|
| `VITE_QR_BASE_URL` | QR 코드의 기본 베이스 URL | 공개 가능 |
| `VITE_API_BASE_URL` | 백엔드 API 서버 주소 | 공개 가능 |
| `VITE_QR_DEBUG` | 디버그 모드 활성화 여부 | 공개 가능 |
| `VITE_QR_ZERO_BASED` | 일련번호 0부터 시작 여부 | 공개 가능 |
| `VITE_QR_PAGING_MODE` | QR 목록 페이징 모드 | 공개 가능 |
| `VITE_DEVELOP_MODE` | 개발 모드 활성화 여부 | 공개 가능 |
| `VITE_AES_IV` | AES 암호화 초기화 벡터(16자) | ⚠️ 민감 정보 |
| `VITE_AES_KEY` | AES 암호화 키(16자) | ⚠️ 민감 정보 |
| `VITE_AES_IV_FORMAT` | AES IV 포맷 | 공개 가능 |
| `VITE_AES_KEY_FORMAT` | AES 키 포맷 | 공개 가능 |

### 로컬 개발 환경 설정

로컬 개발 시 프로젝트 루트에 `.env` 파일을 생성합니다. 

```env
VITE_QR_BASE_URL=www.team-koas.com
VITE_API_BASE_URL=http://13.211.211.70:8080
VITE_QR_DEBUG=false
VITE_QR_ZERO_BASED=true
VITE_QR_PAGING_MODE=page
VITE_DEVELOP_MODE=false
VITE_AES_IV=YOUR_KEY
VITE_AES_KEY=YOUR_KEY
VITE_AES_IV_FORMAT=base64
VITE_AES_KEY_FORMAT=base64
```


## API 프록시 설정

`vite.config.js`의 프록시 설정으로 개발 중 CORS 이슈를 해결합니다:

```javascript
proxy: {
  '/api': {
    target: 'http://13.211.211.70:8080',
    changeOrigin: true,
    secure: false,
  },
}
```

실제 백엔드 엔드포인트가 `http://13.211.211.70:8080`에 위치합니다.

## 배포

Vercel에 배포 설정이 되어 있습니다. `vercel.json`을 통해 배포 구성을 정의합니다.

## 주의사항

1. **청크 크기**: 현재 1개씩 업로드하도록 설정되어 있으므로, 필요시 `CHUNK` 값을 조정하여 네트워크 성능을 최적화할 수 있으나, 백엔드에서도 조정이 필요합니다.
2. **갭(간격)**: 백엔드 부하 관리를 위해 500ms 간격으로 전송합니다
3. **토큰 보안**: 암호화 토큰은 서버 측 비밀키와 동일하게 설정되어야 정상 작동합니다
4. **메모리 사용**: 대량(1000+)의 QR 생성 시 메모리 사용량이 증가할 수 있으니 주의하시기 바랍니다

## 라이선스

KOAS 공식 웹사이트 소스코드입니다.

---

**작성일**: 2025-01-29  
**작성자**: 윤소현