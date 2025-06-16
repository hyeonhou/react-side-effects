# React Side Effects 학습 프로젝트 📍

![React Side Effects 1](./public/react-side-effects-1.png)

![React Side Effects 2](./public/react-side-effects-2.png)

## 프로젝트 개요

이 프로젝트는 React의 **Side Effects**를 학습하기 위한 PlacePicker 애플리케이션입니다. 사용자가 방문하고 싶은 장소를 선택하고 관리할 수 있으며, 현재 위치를 기반으로 장소들을 거리순으로 정렬해서 보여줍니다.

## 학습한 Side Effects 개념들

### 1. 🌍 지리적 위치 정보 가져오기 (Geolocation API)

```javascript
useEffect(() => {
  navigator.geolocation.getCurrentPosition((position) => {
    const sortedPlaces = sortPlacesByDistance(
      AVAILABLE_PLACES,
      position.coords.latitude,
      position.coords.longitude
    );
    setAvailablePlaces(sortedPlaces);
  });
}, []);
```

**학습 포인트:**

- 브라우저 API 호출은 side effect
- 위치 정보를 비동기적으로 가져와서 상태 업데이트
- 의존성 배열이 빈 배열 `[]`로 컴포넌트 마운트 시에만 실행

### 2. 💾 LocalStorage를 통한 데이터 지속성

```javascript
// 초기 로드 시 저장된 데이터 불러오기
useEffect(() => {
  const storedIds = JSON.parse(localStorage.getItem("selectedPlaces")) || [];
  const storedPlaces = storedIds.map((id) =>
    AVAILABLE_PLACES.find((place) => place.id === id)
  );
  setPickedPlaces(storedPlaces);
}, []);

// 장소 선택 시 localStorage에 저장
function handleSelectPlace(id) {
  // ... 상태 업데이트 로직

  const storedIds = JSON.parse(localStorage.getItem("selectedPlaces")) || [];
  if (storedIds.indexOf(id) === -1) {
    localStorage.setItem("selectedPlaces", JSON.stringify([id, ...storedIds]));
  }
}
```

**학습 포인트:**

- localStorage 읽기/쓰기는 side effect
- 애플리케이션 재시작 후에도 데이터 유지
- JSON 직렬화/역직렬화를 통한 객체 저장

### 3. ⏰ 타이머와 자동 실행 기능

```javascript
// DeleteConfirmation.jsx
useEffect(() => {
  const timer = setTimeout(() => {
    onConfirm();
  }, TIMER);

  return () => {
    clearTimeout(timer); // 클린업 함수
  };
}, [onConfirm]);
```

**학습 포인트:**

- `setTimeout`은 side effect
- **클린업 함수**를 사용해 메모리 누수 방지
- 컴포넌트 언마운트 시 타이머 정리

### 4. 📊 실시간 진행률 표시

```javascript
// ProgressBar.jsx
useEffect(() => {
  const interval = setInterval(() => {
    setRemainingTime((prevTime) => prevTime - 10);
  }, 10);

  return () => {
    clearInterval(interval); // 클린업 함수
  };
}, []);
```

**학습 포인트:**

- `setInterval`을 사용한 주기적 상태 업데이트
- 클린업 함수로 인터벌 정리
- 함수형 상태 업데이트 패턴 사용

### 5. 🎛️ Modal 상태 관리 (React 19 방식)

```javascript
// Modal.jsx - React 19의 새로운 방식
function Modal({ open, children, onClose }) {
  const dialog = useRef();

  useEffect(() => {
    if (open) {
      dialog.current.showModal();
    } else {
      dialog.current.close();
    }
  }, [open]);

  return createPortal(
    <dialog className="modal" ref={dialog} onClose={onClose}>
      {open ? children : null}
    </dialog>,
    document.getElementById("modal")
  );
}
```

**학습 포인트:**

- React 19에서 `forwardRef` 없이 `ref` prop 직접 사용
- `useImperativeHandle` 대신 `useEffect`로 DOM 조작
- `createPortal`을 사용한 모달 렌더링

## 주요 Side Effects 패턴들

### 1. 의존성 배열 관리

- `[]`: 컴포넌트 마운트 시에만 실행
- `[dependency]`: 특정 값 변경 시 실행
- 의존성 없음: 매 렌더링마다 실행

### 2. 클린업 함수 사용

```javascript
useEffect(() => {
  // side effect 설정
  return () => {
    // 클린업 로직
  };
}, []);
```

### 3. 조건부 Side Effects

```javascript
useEffect(() => {
  if (condition) {
    // side effect 실행
  }
}, [condition]);
```

## 프로젝트 구조

```
src/
├── components/
│   ├── Places.jsx              # 장소 목록 표시
│   ├── Modal.jsx              # 모달 컴포넌트 (React 19 방식)
│   ├── DeleteConfirmation.jsx # 삭제 확인 + 자동 타이머
│   └── ProgressBar.jsx        # 실시간 진행률 표시
├── App.jsx                    # 메인 컴포넌트
├── data.js                    # 장소 데이터
└── loc.js                     # 거리 계산 유틸리티
```

## 실행 방법

1. 의존성 설치

```bash
npm install
```

2. 개발 서버 실행

```bash
npm run dev
```

3. 브라우저에서 위치 권한 허용

## 학습 성과

이 프로젝트를 통해 다음과 같은 React Side Effects 개념들을 실습했습니다:

- ✅ **비동기 API 호출** (Geolocation)
- ✅ **브라우저 스토리지 활용** (LocalStorage)
- ✅ **타이머 관리** (setTimeout/setInterval)
- ✅ **DOM 조작** (Modal show/hide)
- ✅ **클린업 함수** 사용
- ✅ **의존성 배열** 관리
- ✅ **React 19 새로운 기능** 활용

각 side effect는 적절한 의존성 배열과 클린업 함수를 통해 올바르게 관리되었습니다.
