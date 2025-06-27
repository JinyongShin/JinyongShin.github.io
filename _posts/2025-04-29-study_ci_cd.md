---
title:  "CI/CD 공부! (Github Actions에서 블로그 포스팅을 배포하는 구조(?))"

categories:
  - 개발
tags:
  - CI
  - CD

author: JinyongShin
toc: true
toc_sticky: true
 
date: 2025-04-29 18:00:00 +0900
---
## CI/CD 공부! (Github Actions에서 블로그 포스팅을 배포하는 구조(?))

개발 업무를 하다 보면 "CI/CD"라는 용어를 심심찮게 듣게 됩니다. 저 또한 그저 그런 것이 있나 보다 하고 넘어갔었는데요. 최근 이 깃허브 블로그를 개설하면서 CI/CD로 포스팅이 배포된다는 사실을 알게 되었고, 대체 CI/CD가 무엇인가 궁금해져서 공부하고 그 내용을 바탕으로 이번 포스팅을 작성하게 되었습니다. CI/CD의 개념을 알아보겠습니다. 아직 매끄러운 이해를 하지 못하여 설명이 다소 부족할 수 있는 점 양해 부탁드리며, 추가로 공부할 만한 자료를 댓글로 추천해 주시면 감사하겠습니다.

### 1. CI/CD란 무엇인가?

CI/CD는 **Continuous Integration (지속적인 통합)**과 **Continuous Delivery (지속적인 제공)** 또는 **Continuous Deployment (지속적인 배포)**의 약자입니다. 현대 소프트웨어 개발에서 핵심적인 부분을 차지하며, 개발팀이 더 빠르고 안정적으로 사용자에게 새로운 기능과 업데이트를 제공할 수 있도록 돕는 일련의 자동화된 프로세스를 의미합니다.

* **Continuous Integration (CI, 지속적인 통합):** 개발자들이 작성한 코드를 **정기적으로 공유 저장소(Repository)에 통합**하고, **자동화된 빌드 및 테스트**를 수행하여 코드 변경 사항을 빠르게 검증하는 практика입니다. 이를 통해 통합 과정에서 발생하는 충돌을 줄이고, 버그를 조기에 발견하여 수정할 수 있습니다.

* **Continuous Delivery (CD, 지속적인 제공):** CI 단계를 거쳐 빌드 및 테스트를 통과한 소프트웨어 변경 사항들을 **자동으로 준비하여 배포할 수 있는 상태**로 만드는 практика입니다. 배포 자체는 수동으로 트리거될 수 있습니다.

* **Continuous Deployment (CD, 지속적인 배포):** Continuous Delivery의 확장된 개념으로, 준비된 소프트웨어 변경 사항들을 **테스트 환경을 넘어 실제 운영 환경까지 자동으로 배포**하는 практика입니다. 사용자에게 더욱 빠르게 새로운 기능을 제공할 수 있지만, 높은 수준의 자동화와 신뢰성이 요구됩니다.

**핵심 목표:** 개발부터 배포까지의 과정을 자동화하여 효율성을 높이고, 위험을 줄이며, 사용자에게 더 나은 가치를 빠르게 제공하는 것입니다.

### 2. CI/CD는 어떻게 동작하는가?

CI/CD 파이프라인은 일반적으로 다음과 같은 단계들을 거치며 동작합니다. 각 단계는 자동화된 스크립트나 도구를 통해 실행됩니다.

1.  **코드 변경 (Code Change):** 개발자가 코드 변경 사항을 저장소(예: Git)에 푸시(Push)합니다.

2.  **빌드 (Build):** 저장소의 코드를 가져와 컴파일, 패키징 등의 빌드 과정을 거칩니다. (예: Java의 Maven 또는 Gradle, JavaScript의 npm 또는 yarn, Jekyll)

3.  **테스트 (Test):** 빌드된 결과물에 대해 다양한 자동화된 테스트(유닛 테스트, 통합 테스트, UI 테스트 등)를 실행하여 코드의 품질을 검증합니다.

4.  **릴리스 (Release):** 테스트를 통과한 빌드 결과물을 배포 준비 단계로 옮깁니다. (Continuous Delivery)

5.  **배포 (Deploy):** 준비된 릴리스를 실제 운영 환경에 배포합니다. (Continuous Deployment)

6.  **모니터링 (Monitor):** 배포된 애플리케이션의 성능과 안정성을 지속적으로 모니터링하고, 문제가 발생하면 빠르게 대응합니다.

이러한 각 단계를 자동화하는 데 다양한 도구들이 사용됩니다. (예: Jenkins, GitLab CI/CD, GitHub Actions, CircleCI 등)

### 3. GitHub Pages 배포의 예시 (현재 블로그에서 사용중인 Chirpy 테마 뜯어보기)

이번에는 GitHub Pages를 사용하여 Jekyll 기반의 Chirpy 블로그 테마를 자동으로 배포하는 CI/CD 파이프라인 설정을 살펴보겠습니다. 아래의 `pages-deploy.yml` 파일은 GitHub Actions 워크플로우 설정 파일입니다. 'main' 혹은 'master' branch에 변경사항이 push 될 경우 동작하며, .gitignore, README.md, LICENSE 항목에 대한 push는 무시하도록 되어있네요. 그외에도 어떤 설정이 되어있는지 주석을 보시면 쉽게 파악할 수 있으리라 생각됩니다.

**.github/workflows/pages-deploy.yml:**

```yaml
name: "Build and Deploy" # 워크플로우 이름
on: # 워크플로우 트리거 조건
  push: # 푸시 이벤트 발생 시
    branches: # 특정 브랜치에 푸시될 때만 실행
      - main
      - master
    paths-ignore: # 특정 파일 경로는 무시
      - .gitignore
      - README.md
      - LICENSE

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch: # 수동으로 워크플로우를 실행할 수 있도록 허용

permissions: # GitHub Pages 관련 권한 설정
  contents: read # 저장소 내용 읽기 권한
  pages: write # GitHub Pages 배포 권한
  id-token: write # OIDC 토큰 발급 권한

# Allow one concurrent deployment
concurrency: # 동시 배포 설정
  group: "pages" # 그룹 이름
  cancel-in-progress: true # 진행 중인 배포 취소

jobs: # 작업 정의
  build: # 빌드 작업
    runs-on: ubuntu-latest # 실행 환경 설정

    steps: # 작업 단계 정의
      - name: Checkout # 코드 체크아웃
        uses: actions/checkout@v4 # 사용할 액션
        with:
          fetch-depth: 0 # 전체 커밋 내역 가져오기
          # submodules: true
          # If using the 'assets' git submodule from Chirpy Starter, uncomment above
          # (See: [https://github.com/cotes2020/chirpy-starter/tree/main/assets](https://github.com/cotes2020/chirpy-starter/tree/main/assets))

      - name: Setup Pages # GitHub Pages 설정
        id: pages
        uses: actions/configure-pages@v4

      - name: Setup Ruby # Ruby 환경 설정 (Jekyll 실행에 필요)
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.3 # Ruby 버전 지정
          bundler-cache: true # Bundler 캐시 사용

      - name: Build site # 사이트 빌드 (Jekyll 실행)
        run: bundle exec jekyll b -d "_site${{ steps.pages.outputs.base_path }}" # 빌드 명령어
        env:
          JEKYLL_ENV: "production" # Jekyll 환경 변수 설정

      - name: Test site # 사이트 테스트 (htmlproofer 실행)
        run: |
          bundle exec htmlproofer _site \
            --disable-external \
            --ignore-urls "/^http:\/\/127.0.0.1/,/^http:\/\/0.0.0.0/,/^http:\/\/localhost/"

      - name: Upload site artifact # 빌드 결과물 업로드
        uses: actions/upload-pages-artifact@v3
        with:
          path: "_site${{ steps.pages.outputs.base_path }}" # 업로드 경로

  deploy: # 배포 작업
    environment: # 배포 환경 설정
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }} # 배포된 페이지 URL
    runs-on: ubuntu-latest # 실행 환경
    needs: build # 빌드 작업 완료 후 실행
    steps:
      - name: Deploy to GitHub Pages # GitHub Pages에 배포
        id: deployment
        uses: actions/deploy-pages@v4
```
