# GDSC 홍익 오픈 커뮤니티 웹사이트

GDSC 홍익 오픈 커뮤니티 회원가입을 위해 firebase hosting에 배포된 정적 SvelteKit 앱입니다.

## 세팅

1. [Node.JS](https://nodejs.org) 설치

2. 종속 항목 설치

   ```
   npm install
   ```

3. Firebase 셋업

   1. [Firebase console](https://console.firebase.google.com)에서 프로젝트를 생성

   2. Firebase CLI 설치 및 로그인

      ```
      npm install -g firebase-tools
      ```

      ```
      firebase login
      ```

   3. 프로젝트 루트 디렉토리에서 다음을 실행하여 사용할 Firebase 프로젝트를 선택

      ```
      firebase use --add
      ```

   4. Firebase project settings에서 web app을 생성 후 `firebaseConfig`를 `src/lib/firebaseConfig.json`에 저장

      - [이 링크](https://console.firebase.google.com/u/0/project/_/settings/general)를 클릭 후 프로젝트를 선택하면 해당 페이지로 이동합니다
      - firebase config 구조:
        ```json
        {
        	"apiKey": "...",
        	"authDomain": "...",
        	"projectId": "...",
        	"storageBucket": "...",
        	"messagingSenderId": "...",
        	"appId": "...",
        	"measurementId": "..."
        }
        ```

4. [http://localhost:5173](http://localhost:5173)에서 실행

   ```
   npm run dev
   ```

5. Production build 생성

   ```
   npm run build
   ```

6. Production build 실행

   ```
   npm run preview
   ```
