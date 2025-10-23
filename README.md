# Practice Verification System

실습 인증 시스템 프로토타입 - 스크린샷 기반 실습 검증 및 단체 채팅 기능

## 주요 기능

### 1. 실습 검증 시스템
- 스크린샷 업로드를 통한 실습 인증
- 드래그 앤 드롭, 클립보드 붙여넣기, 화면 캡처 지원
- 실시간 프로그레스 바 업데이트
- 체크리스트 기반 학습 진행도 관리

### 2. 단체 채팅
- 더미 유저들의 자동 응답 시뮬레이션
- AI 튜터 1:1 채팅 탭 분리
- 실시간 메시지 타임스탬프
- 이미지 업로드 기능

### 3. UI/UX 특징
- Figma 디자인 기반 구현
- 체크 가능한 Step Indicator
- 토스트 알림 시스템
- 반응형 레이아웃

## 파일 구조

```
.
├── practice-verification-full.html    # 최종 완성 버전 (메인 파일)
├── figma-screenshot-verification.html # Figma 디자인 기반 초기 버전
├── improved-practice-ui.html          # 개선된 UI 버전
├── screenshot-verification.html       # 스크린샷 검증 초기 버전
└── README.md
```

## 사용 방법

1. `practice-verification-full.html` 파일을 브라우저에서 열기
2. 체크리스트의 "실습 인증하기" 버튼 클릭
3. 스크린샷 업로드 (드래그 앤 드롭, 붙여넣기, 또는 파일 선택)
4. 제출하여 실습 인증 완료
5. Step Indicator 클릭으로 수동 체크 가능
6. 채팅 탭에서 다른 학습자들과 소통

## 기술 스택

- HTML5
- CSS3 (Flexbox, Animations)
- Vanilla JavaScript
- Figma Design Integration

## 주요 개선 사항

- ✅ Figma 디자인 시스템 완벽 구현
- ✅ 체크 가능한 Step Indicator
- ✅ 자동 프로그레스 바 업데이트
- ✅ 단체 채팅 + AI 튜터 탭 분리
- ✅ Empty State 구현
- ✅ 토스트 알림 디자인 개선
- ✅ 엣지 케이스 처리 (파일 크기, 형식 검증 등)

## 브라우저 호환성

- Chrome (권장)
- Firefox
- Safari
- Edge

---

🤖 Generated with [Claude Code](https://claude.com/claude-code)
