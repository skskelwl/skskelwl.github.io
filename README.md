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

본문은 두 번째 `---` 아래부터 Markdown으로 작성합니다. 작성 중인 글은 `_drafts` 폴더에 두고, 공개할 때 `_posts`로 옮길 수 있습니다.

## 사이트 편집

- 블로그 제목, 설명, 주소, SNS: `_config.yml`
- 소개 페이지: `_tabs/about.md`
- 카테고리·태그·보관함 탭: `_tabs` 폴더
- 프로필 및 본문 이미지: `assets/img` 폴더
- 배경색과 디자인: `assets/css/jekyll-theme-chirpy.scss`

설정 파일을 바꾼 뒤에는 로컬 서버를 다시 시작해야 변경 내용이 정확히 반영됩니다.

## 배포

`main` 브랜치에 푸시하면 GitHub Actions가 사이트를 빌드하고 GitHub Pages에 배포합니다.
