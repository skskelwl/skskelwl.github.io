---
title: 글 제목
date: 2026-09-12 12:00:00 +0900
categories: [로보틱스, Unitree Go2]
tags: [ROS2, Python]
description: 글의 내용을 한 문장으로 설명합니다.
---

## 시작하며

여기에 본문을 Markdown으로 작성합니다.

## 사진 넣기

사진 파일을 `assets/img/posts` 폴더에 넣고 아래처럼 작성합니다.

```markdown
![사진을 설명하는 문장](/assets/img/posts/example.jpg){: width="1200" height="800" }
_사진 아래에 표시할 설명_
```

대표 사진으로 사용할 때는 문서 맨 위의 `---` 안에 다음 내용도 추가합니다.

```yaml
image:
  path: /assets/img/posts/example.jpg
  alt: 사진을 설명하는 문장
```

## YouTube 영상 넣기

YouTube 주소에서 `v=` 뒤의 영상 ID만 넣습니다.

{% raw %}
```liquid
{% include embed/youtube.html id='영상_ID' %}
```
{% endraw %}

예를 들어 `https://www.youtube.com/watch?v=abcdefghijk`의 영상 ID는 `abcdefghijk`입니다.

## 직접 올린 동영상 넣기

MP4 파일을 `assets/video` 폴더에 넣고 아래처럼 작성합니다.

{% raw %}
```liquid
{% include embed/video.html src='/assets/video/example.mp4' title='영상 설명' %}
```
{% endraw %}

용량이 큰 동영상은 GitHub 저장소에 직접 넣기보다 YouTube에 올려 삽입하는 방식을 권장합니다.
