# skskelwl.github.io

[Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마를 사용하는 Jekyll 기반 GitHub 블로그입니다.

## 로컬 실행

Ruby 3.1 이상과 Bundler를 설치한 뒤 다음 명령을 실행합니다.

```bash
bundle install
bundle exec jekyll serve
```

브라우저에서 <http://localhost:4000>으로 접속합니다.

## 글 작성

`_posts` 폴더에 `YYYY-MM-DD-title.md` 형식으로 파일을 추가합니다.

```yaml
---
title: 글 제목
date: 2026-09-11 12:00:00 +0900
categories: [카테고리]
tags: [태그]
---
```

`main` 브랜치에 푸시하면 GitHub Actions가 사이트를 빌드하고 GitHub Pages에 배포합니다.
