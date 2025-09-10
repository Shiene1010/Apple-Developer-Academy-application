# 두 대의 Mac, 두 개의 GitHub 계정, 하나의 프로젝트: 실전 인증 문제와 해결 사례

## 들어가며

최근 한 프로젝트를 두 대의 Mac(맥북프로, 맥미니)에서 각각 다른 GitHub 계정(신1010, 신허브)으로 관리하면서 여러 인증 문제를 겪었습니다. 이 글에서는 실제로 겪은 실수와, 이를 어떻게 해결했는지 정리합니다.

---

## 1. 상황 설명

- **맥북프로**: GitHub 계정 A(신허브) 사용
- **맥미니**: GitHub 계정 B(신1010) 사용
- **프로젝트**: 동일한 원격 저장소(`Shiene1010/Apple-Developer-Academy-application`)를 두 기기에서 관리

---

## 2. 자주 겪는 실수

### 2-1. SSH 키와 계정 불일치

- 맥미니에서 SSH 키를 만들고 신허브 계정에만 등록
- 원격 저장소는 신1010 계정 소유
- `git push` 시 "Permission denied to shienehub" 에러 발생

### 2-2. HTTPS 원격 주소 사용

- 원격 저장소 주소가 `https://github.com/...`로 되어 있음
- SSH 키를 등록해도 계속 토큰/비밀번호를 요구

### 2-3. 여러 계정/키 혼용 시 인증 충돌

- 여러 SSH 키를 만들어도, 어떤 키가 사용될지 명확하지 않음
- `ssh -T git@github.com` 명령으로 인증 계정 확인 필요

---

## 3. 해결 방법

### 3-1. SSH 키를 각 계정에 맞게 등록

- 각 계정별로 SSH 키를 생성
- 해당 계정의 GitHub > Settings > SSH and GPG keys에 공개키 등록

### 3-2. SSH 설정 파일(`~/.ssh/config`) 활용

```ssh
# 신1010 계정용
Host github-shiene1010
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_shiene1010

# 신허브 계정용
Host github-shienehub
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_shienehub
```

### 3-3. 원격 저장소 주소에 Host 별칭 사용

```sh
git remote set-url origin git@github-shiene1010:Shiene1010/Apple-Developer-Academy-application.git
```

- 이렇게 하면 push/pull 시 반드시 지정한 SSH 키(=계정)로 인증됨

### 3-4. 인증 계정 확인

```sh
ssh -T git@github-shiene1010
```
- "Hi Shiene1010!" 메시지가 나오면 정상

---

## 4. 추가로 알게 된 점

- **VS Code 오른쪽 하단 계정 아이콘**은 GitHub 인증과 무관(확장 기능용)
- **`git config user.email`**은 커밋 작성자 정보일 뿐, 인증과 무관
- 여러 계정/키를 쓸 때는 Host 별칭 방식이 가장 안전

---

## 5. 결론

여러 대의 컴퓨터와 여러 GitHub 계정을 쓸 때는  
- SSH 키를 계정별로 만들고  
- `~/.ssh/config`에서 Host 별칭을 지정  
- 원격 저장소 주소에 Host 별칭을 사용  
하는 것이 인증 문제를 깔끔하게 해결하는 방법입니다.