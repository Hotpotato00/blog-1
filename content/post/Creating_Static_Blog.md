---
draft: false
date: 2018-08-12T20:48:55+09:00
title: "Creating_Static_Blog"
slug: "Creating_Static_Blog"
categories: ["ETC"]
tags: ["Hugo","Static Site Generator","github","휴고","블로그","Jekyll","블로그 만들기"]
---
# Static Site Generator (정적 사이트 생성기)
>주소가 github.io인 개발 블로그들이 눈에 많이 띄었다.  
>그래서 찾아 헤매던 도중 Jekyll, Hexo, Hugo 등 Static Site Generator의 존재를 알게 되었다.  
>놀다 지친 여름휴가 막바지에 집중공략을 시작했다. (블로그도 다시 시작해볼겸)

## 1. Static Site Generator 란?

## 2. Static Site Generator 종류
[이 곳](https://www.staticgen.com/)에서 모든(?) Static Site Generator들을 한눈에 볼 수 있었다.


## 3. Hugo! 너로 정했다!


## 4. Hugo + Github Page 만들어가는 과정

### 4-1. Hugo 설치
>나도 멋깔나게 `$ brew install hugo`를 mac 터미널에 입력해서 설치하고 싶었다.  
>하지만 현실은 WINDOWS...  
>Giraffe Academy의 [Windows에서 Hugo설치하기](https://gohugo.io/getting-started/installing#windows) 이 영상 하나면 설치는 쉽다. (젊은 형아가 영어로 설명해줌)
- [hugo 공식 깃헙](https://github.com/gohugoio/hugo/releases)에서 운영체제에 맞는 최신버전 다운로드
- `C:\Hugo\bin` 디렉토리 생성해서 다운받은 압축파일 해제
- 어느 위치에서나 Hugo가 실행될 수 있도록`$ set PATH=%PATH%;C:\Hugo\bin` 명령으로 환경변수에 `C:\Hugo\bin`추가
- 명령 프롬프트에 `$ hugo version` 혹은 `$ hugo help`로 동작 확인

### 4-2. Github 저장소 2개 만들기
- 하나는 Hugo의 컨텐츠와 소스파일들을 포함할 `<YOUR-PROJECT>` 저장소 생성 (나의 경우 `blog`라는 이름으로 생성)
- 다른 하나는 렌더링된 버전의 Hugo 웹사이트를 포함할 `<USERNAME>.github.io` 저장소 생성 (`integerous.github.io`)

### 4-3. Directory Structure 구성
- `$ hugo new site blog` 명령으로 로컬에서 컨텐츠를 관리하기 위한 장소(Hugo/blog) 생성
- `C:\Hugo\blog`에서 `$ dir`로 directory structure를 확인할 수 있다.

### 4-4. 테마 다운로드 및 설정
- https://themes.gohugo.io/ 에서 원하는 테마를 선택한다.
- 선택한 테마의 github에서 저장소 주소를 복사한다.
- `C:\Hugo\blog\themes`에 선택한 테마를 clone한다. `$ git clone https://github.com/선택한/테마`
- `config.toml` 파일을 선택한 테마의 설명서에 따라 수정한다.

### 4-5. Remote와 Submodule 설정
- 깃헙에 만든 `blog 저장소`를 local의 blog 디렉토리의 remote로 등록한다.
  - `C:\Hugo\blog` 로 이동
  - `$ git init`
  - `$ git remote add origin git@github.com:integerous/blog.git`
- `integerous.github.io 저장소`를 blog의 submodule로 등록한다.
  - `$ git submodule add -b masater git@github.com:integerous/integerous.github.io.git public`
  - 이렇게 함으로써 `hugo` 명령으로 `public`에 웹사이트를 만들 때, 만들어진 `public` 디렉토리는 다른 remote origin을 가질 것이다.

### 4-6. 컨텐츠 생성
- `$ hugo new post/test1.md` 명령으로 파일을 생성하면 `\content\post\test1.md`

### 4-7. 컨텐츠 업로드 (블로그에)
- Linux는 http://gohugo.io/tutorials/github-pages-blog/ 의 deploy.sh 파일을 사용
- 


## *Reference
- [Jekyll, Hexo, Hugo 간단 비교 글](http://tadakichi.tistory.com/188)

