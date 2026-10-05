# Kubernetes의 이해와 실습 — 실습 자료

『Kubernetes의 이해와 실습』(John Bae 지음, AIDevOps Cloud Native Series #04)의 공식 실습 자료입니다.
책에서 `companion/...`으로 가리키는 파일이 이 저장소의 `companion/` 폴더에 같은 경로로 들어 있습니다.

- 도서 안내: <https://www.aidevops.kr/books/kubernetes-understanding-practice/>
- 같은 자료의 ZIP: <https://www.aidevops.kr/downloads/books/kubernetes-understanding-practice/companion.zip>

## 받는 방법

```bash
git clone https://github.com/aidevops-books/kubernetes-understanding-practice-ko.git
cd kubernetes-understanding-practice-ko/companion
```

Git을 쓰지 않는다면 위의 ZIP을 받아 압축을 풀어도 됩니다.

## 폴더 구성

```text
companion/
├── k8s/
├── LEARNING_RECORD.md
└── README.md
```

실습 순서와 준비물, 각 파일의 쓰임은 [`companion/README.md`](companion/README.md)에 있습니다. 먼저 그 문서를 읽으세요.

## Image 빌드 소스

이 책의 매니페스트는 앞 권 『Docker & Kubernetes 최신 입문 - 2026』에서 만든 `intro-api:1.0`, `intro-web:1.0` Image를 사용합니다.
두 Image의 소스(`companion/api`, `companion/web`)는 앞 권의 저장소에 있습니다: <https://github.com/aidevops-books/docker-kubernetes-intro-ko>

```bash
git clone https://github.com/aidevops-books/docker-kubernetes-intro-ko.git
```

위 ZIP에는 같은 소스가 `03-docker-kubernetes-intro/companion/` 폴더로 함께 들어 있습니다.

## 사용할 때

- 학습용 환경에서만 사용하세요. 예제에 들어 있는 비밀번호와 토큰은 실습용 값이며 실제 서비스에 쓰면 안 됩니다.
- 책과 다른 버전의 도구에서는 출력이나 옵션이 조금 다를 수 있습니다.
- 오류를 발견하면 책 제목과 판본, 장 번호, 실행 환경, 재현 절차를 함께 Issue로 알려 주세요.
