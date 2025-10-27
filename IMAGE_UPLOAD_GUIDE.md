# 실습 인증 이미지 업로드 가이드

## 개요
실습 인증 시스템은 학생들이 실습 완료를 증명하기 위해 스크린샷을 제출할 수 있는 기능을 제공합니다. 총 **5가지 방법**으로 이미지를 업로드할 수 있습니다.

---

## 이미지 업로드 방법

### 1. 📁 파일 선택 (클릭)
**가장 기본적인 방법**

- **사용 방법:**
  1. "실습 인증하기" 버튼 클릭
  2. 모달 창의 업로드 영역 클릭
  3. 파일 탐색기에서 이미지 선택

- **구현 코드:**
```javascript
function triggerFileInput() {
    document.getElementById('fileInput').click();
}

function handleFileSelect(event) {
    const file = event.target.files[0];
    if (file) handleFile(file);
}
```

- **특징:**
  - 가장 직관적이고 익숙한 방법
  - 모든 운영체제에서 동일하게 작동
  - 기존에 저장된 스크린샷을 업로드할 때 유용

---

### 2. 🖱️ 드래그 앤 드롭 (Drag & Drop)
**빠르고 편리한 방법**

- **사용 방법:**
  1. 파일 탐색기에서 이미지 파일 선택
  2. 모달 창의 업로드 영역으로 드래그
  3. 마우스를 놓으면 자동 업로드

- **구현 코드:**
```javascript
uploadWrapper.addEventListener('dragover', (e) => {
    e.preventDefault();
    e.stopPropagation();
});

uploadWrapper.addEventListener('drop', (e) => {
    e.preventDefault();
    e.stopPropagation();
    const files = e.dataTransfer.files;
    if (files.length > 0) handleFile(files[0]);
});
```

- **특징:**
  - 클릭 없이 빠르게 업로드 가능
  - 여러 이미지 중 하나를 선택할 때 편리
  - 현대적이고 직관적인 UX

---

### 3. 📋 클립보드 붙여넣기 (Ctrl/Cmd + V)
**스크린샷을 찍은 직후 가장 빠른 방법**

- **사용 방법:**
  1. 스크린샷 도구로 화면 캡처
     - **Windows:** `Windows + Shift + S`
     - **macOS:** `Cmd + Shift + 4` (영역 선택)
  2. 모달 창이 열린 상태에서 `Ctrl + V` (Windows) 또는 `Cmd + V` (Mac)
  3. 클립보드의 이미지가 자동으로 붙여넣어짐

- **구현 코드:**
```javascript
document.addEventListener('paste', (e) => {
    // 모달이 열려있을 때만 작동
    if (!document.getElementById('modalOverlay').classList.contains('active')) return;

    const items = e.clipboardData.items;
    for (let item of items) {
        if (item.type.indexOf('image') !== -1) {
            handleFile(item.getAsFile());
            showToast('클립보드에서 이미지를 붙여넣었습니다!');
            break;
        }
    }
});
```

- **특징:**
  - 스크린샷 찍고 바로 붙여넣기 가능
  - 파일로 저장할 필요 없음
  - 가장 빠른 워크플로우
  - **토스트 메시지**로 성공 피드백 제공

---

### 4. 📸 브라우저 내장 스크린샷 캡처
**별도 도구 없이 브라우저에서 바로 캡처**

- **사용 방법:**
  1. 모달 창에서 "스크린샷 찍기" 버튼 클릭
  2. 브라우저가 화면 공유 권한 요청
  3. 캡처할 화면/창/탭 선택
  4. 자동으로 이미지 업로드

- **구현 코드:**
```javascript
async function captureScreenshot() {
    try {
        // 화면 캡처 권한 요청
        const stream = await navigator.mediaDevices.getDisplayMedia({
            video: { mediaSource: 'screen' }
        });

        const video = document.createElement('video');
        video.srcObject = stream;
        video.play();

        video.onloadedmetadata = () => {
            // Canvas로 비디오 프레임 캡처
            const canvas = document.createElement('canvas');
            canvas.width = video.videoWidth;
            canvas.height = video.videoHeight;
            canvas.getContext('2d').drawImage(video, 0, 0);

            // 스트림 종료
            stream.getTracks().forEach(track => track.stop());

            // Canvas를 Blob으로 변환하여 업로드
            canvas.toBlob(blob => {
                const file = new File([blob], 'screenshot.png', { type: 'image/png' });
                handleFile(file);
                showToast('화면을 캡처했습니다!');
            }, 'image/png');
        };
    } catch (err) {
        if (err.name === 'NotAllowedError') {
            showError('화면 캡처 권한이 거부되었습니다.');
        } else {
            showError('화면 캡처 중 오류가 발생했습니다.');
        }
    }
}
```

- **특징:**
  - OS 스크린샷 단축키를 몰라도 사용 가능
  - 브라우저 API(`getDisplayMedia`) 사용
  - 전체 화면, 특정 창, 특정 탭 선택 가능
  - 캡처 후 자동으로 파일 생성 및 업로드
  - **Chrome, Edge, Firefox 지원** (Safari는 제한적)

---

### 5. 🔁 재업로드 버튼
**이미지를 업로드한 후 다시 선택**

- **사용 방법:**
  1. 이미지를 한 번 업로드한 후
  2. "이미지 업로드 다시하기" 버튼 클릭
  3. 새로운 이미지 선택

- **구현 코드:**
```javascript
<button class="reupload-btn" id="reuploadBtn" onclick="triggerFileInput()">
    <svg>...</svg>
    이미지 업로드 다시하기
</button>
```

- **특징:**
  - 잘못된 이미지를 선택했을 때 유용
  - 기존 이미지 미리보기 유지
  - 파일 탐색기를 다시 열어줌

---

## 공통 검증 로직

모든 업로드 방법은 동일한 `handleFile()` 함수를 거쳐 검증됩니다:

```javascript
function handleFile(file) {
    hideError();

    // 1. 파일 형식 검증
    if (!['image/jpeg', 'image/png'].includes(file.type)) {
        showError('JPEG 또는 PNG 형식의 이미지만 업로드 가능합니다.');
        return;
    }

    // 2. 파일 크기 검증 (5MB 제한)
    if (file.size > MAX_FILE_SIZE) {
        showError('파일 크기는 5MB 이하여야 합니다.');
        return;
    }

    // 3. 업로드 진행 시뮬레이션
    currentFile = file;
    simulateUpload();

    // 4. 이미지 미리보기
    const reader = new FileReader();
    reader.onload = (e) => {
        document.getElementById('previewImg').src = e.target.result;
        document.getElementById('imagePreview').classList.add('active');
        document.getElementById('uploadContent').style.display = 'none';
        document.getElementById('uploadWrapper').classList.add('has-image');
        document.getElementById('reuploadBtn').classList.add('active');
        document.getElementById('submitBtn').disabled = false;

        // 개인정보 보호 경고 표시
        const warningBox = document.querySelector('.warning-box');
        if (warningBox) {
            warningBox.classList.add('active');
        }
    };
    reader.readAsDataURL(file);
}
```

---

## 제약 사항 및 보안

### 파일 제약
- **허용 형식:** JPEG, PNG만 가능
- **최대 용량:** 5MB
- **개수 제한:** 한 번에 1개만 업로드

### 보안 기능
1. **개인정보 보호 경고:**
   - 이미지 업로드 시 경고 박스 표시
   - API 키, 이메일 등 민감 정보 확인 유도

```html
<div class="warning-box">
    <svg class="warning-icon">...</svg>
    <div class="warning-text">
        <strong>제출 전 확인:</strong> 스크린샷에 개인정보(이메일, API 키 등)가
        포함되지 않았는지 확인하세요.
    </div>
</div>
```

2. **이미지 삭제 확인:**
   - 이미지 제거 시 confirm 대화상자로 재확인

```javascript
function removeImage() {
    if (confirm('이미지를 삭제하시겠습니까?')) {
        resetModal();
    }
}
```

---

## UX 개선 요소

### 1. 진행 상태 표시
업로드 중 프로그레스 바로 시각적 피드백 제공:

```javascript
function simulateUpload() {
    document.getElementById('uploadProgress').classList.add('active');
    let progress = 0;
    const interval = setInterval(() => {
        progress += 10;
        document.getElementById('progressFill').style.width = progress + '%';
        if (progress >= 100) {
            clearInterval(interval);
            setTimeout(() => {
                document.getElementById('uploadProgress').classList.remove('active');
            }, 500);
        }
    }, 100);
}
```

### 2. 이미지 미리보기
업로드된 이미지를 바로 확인:
```html
<div class="image-preview" id="imagePreview">
    <img id="previewImg" class="preview-img" alt="미리보기">
    <button class="remove-btn" onclick="removeImage()">×</button>
</div>
```

### 3. 토스트 알림
사용자 행동에 대한 즉각적인 피드백:
- "클립보드에서 이미지를 붙여넣었습니다!"
- "화면을 캡처했습니다!"
- "인증이 완료되었습니다!"

### 4. 에러 메시지
명확한 에러 표시:
```javascript
function showError(message) {
    document.getElementById('errorText').textContent = message;
    document.getElementById('errorBox').classList.add('active');
}
```

---

## 추천 워크플로우

### 📊 사용 빈도 예상
1. **클립보드 붙여넣기** (60%) - 가장 빠르고 편리
2. **파일 선택** (20%) - 기존 스크린샷 사용 시
3. **드래그 앤 드롭** (10%) - 탐색기에서 직접 작업 시
4. **브라우저 캡처** (8%) - 단축키를 모르는 사용자
5. **재업로드** (2%) - 실수로 잘못 선택 시

### 🎯 최적의 사용법
**초급 사용자:**
1. "스크린샷 찍기" 버튼 클릭
2. 화면 선택 후 자동 업로드

**중급 사용자:**
1. OS 단축키로 스크린샷 캡처
2. `Ctrl/Cmd + V`로 바로 붙여넣기

**고급 사용자:**
1. 스크린샷을 미리 여러 장 준비
2. 드래그 앤 드롭으로 빠르게 제출

---

## 브라우저 호환성

| 기능 | Chrome | Edge | Firefox | Safari |
|------|--------|------|---------|--------|
| 파일 선택 | ✅ | ✅ | ✅ | ✅ |
| 드래그 앤 드롭 | ✅ | ✅ | ✅ | ✅ |
| 클립보드 붙여넣기 | ✅ | ✅ | ✅ | ✅ |
| 브라우저 캡처 | ✅ | ✅ | ✅ | ⚠️ 제한적 |
| 재업로드 | ✅ | ✅ | ✅ | ✅ |

**Note:** Safari는 `getDisplayMedia` API 지원이 제한적이므로 브라우저 캡처 기능이 일부 버전에서 작동하지 않을 수 있습니다.

---

## 코드 구조

### 핵심 함수
- `triggerFileInput()` - 파일 선택 대화상자 열기
- `handleFileSelect()` - 파일 input onChange 핸들러
- `handleFile()` - 파일 검증 및 처리 (공통 로직)
- `captureScreenshot()` - 브라우저 화면 캡처
- `simulateUpload()` - 업로드 진행 상태 시뮬레이션
- `removeImage()` - 이미지 삭제
- `submitImage()` - 최종 제출

### 이벤트 리스너
- `dragover` - 드래그 오버 시 기본 동작 방지
- `drop` - 파일 드롭 처리
- `paste` - 클립보드 이미지 붙여넣기

---

## 참고 자료

### OS별 스크린샷 단축키
**Windows:**
- `Windows + Shift + S` - 영역 선택 후 클립보드에 복사
- `PrtScn` - 전체 화면 클립보드 복사
- `Windows + PrtScn` - 전체 화면 자동 저장

**macOS:**
- `Cmd + Shift + 3` - 전체 화면 파일 저장
- `Cmd + Shift + 4` - 영역 선택 후 파일 저장
- `Cmd + Shift + 4 + Space` - 창 선택 후 파일 저장
- `Ctrl` 추가 시 - 파일 대신 클립보드에 복사

**Linux (GNOME):**
- `PrtScn` - 전체 화면
- `Alt + PrtScn` - 현재 창
- `Shift + PrtScn` - 영역 선택

---

## 향후 개선 가능 사항

### 1. 다중 이미지 업로드
현재는 1개만 가능하지만, 여러 스크린샷을 한 번에 제출할 수 있도록 확장

### 2. 이미지 편집 기능
- 간단한 주석 추가 (화살표, 텍스트)
- 민감 정보 모자이크 처리
- 이미지 크롭

### 3. 클라우드 스토리지 통합
- Google Drive에서 직접 선택
- Dropbox 연동

### 4. OCR (텍스트 인식)
- 스크린샷에서 자동으로 텍스트 추출
- 특정 키워드 존재 여부 자동 검증

### 5. AI 자동 검증
- 스크린샷이 실습 내용과 일치하는지 자동 확인
- 개인정보 자동 감지 및 경고

---

**문서 작성일:** 2025-01-25
**버전:** 1.0
**작성자:** Claude Code
