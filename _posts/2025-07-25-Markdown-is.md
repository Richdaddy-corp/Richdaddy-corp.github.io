---
title : 마크다운 기초 사용방법
description : 마크다운 문서의 작성법을 공부해 봅니다.
img_path: /assets/img/for_post/
image:
  path: markdown.jpg #썸네일
  alt: "마크다운(Markdown) 로고"
tag : [마크다운,markdown,문서]
categories : [Study]
author : Richdaddy
date : 2025-07-28 00:00:00 +/-TTTT
pin : false
math : false
mermaid : true
last_modified_at: 2025-07-29 23:00:00 +/-TTTT
#shortTitle: 아아아아아

---

--------------
> **<u>KEYWORDS</u>**    
> 마크다운의 기초 사용방법, 마크다운 문법을 공부해 봅니다.
{: .prompt-info }
----------------------
&nbsp;
# 마크다운이란?
[마크다운(Markdown)](https://ko.wikipedia.org/wiki/%EB%A7%88%ED%81%AC%EB%8B%A4%EC%9A%B4)은 일반 텍스트 서식을 저장하는 쉬운 방법의 경량 ```마크업 언어``` 입니다.\
텍스트 기반이며 마크다운만의 문법을 사용하여 제목, 링크, 강조, 표, 이미지삽입 등을 표현할 수 있습니다.\
HTML로 쉽게 변환이 가능하며 주로 Github 등에서 README 파일, 온라인문서, 텍스트 편집 등에서 편리하게 사용됩니다.
&nbsp;
&nbsp;
&nbsp;
# 마크다운 주요문법

## 제목(Header)

```markdown
# 제목 중요도 1
## 제목 중요도 2
### 제목 중요도 3
#### 제목 중요도 4
##### 제목 중요도 5
###### 제목 중요도 6
```

# 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6

## 일반 문장
```markdown
동해물과 백두산이 마르고 닳도록
하느님이 보우하사 우리나라 만세
```
동해물과 백두산이 마르고 닳도록
하느님이 보우하사 우리나라 만세

## 줄바꿈(Line Breaks)

```markdown
동해물과 백두산이 마르고 닳도록 
하느님이 보우하사 우리나라 만세   <!--띄어쓰기 2번-->

무궁화 삼천리 화려 강산<br/>
대한 사람 대한으로 길이 보전하세
```

동해물과 백두산이 마르고 닳도록 
하느님이 보우하사 우리나라 만세   <!--띄어쓰기 2번-->

무궁화 삼천리 화려 강산<br/>
대한 사람 대한으로 길이 보전하세

## 강조(Emphasis)

```markdown
_이텔릭_
**두껍게**
**_이텔릭 + 두껍게_**
~~취소선~~
<u>밑줄</u>
```

_이텔릭_  
**두껍게**  
**_이텔릭 + 두껍게_**  
~~취소선~~  
<u>밑줄</u>  

## 목록(List)
```markdown
1. 순서가 필요한 목록
1. 순서가 필요한 목록
    1. 순서가 필요한 목록(서브)
    1. 순서가 필요한 목록(서브)
1. 순서가 필요한 목록

- 순서가 필요하지 않은 목록
    - 순서가 필요하지 않은 목록(서브)
    - 순서가 필요하지 않은 목록(서브)
      - 순서가 필요 없는 목록 (서서브)
- 순서가 필요하지 않은 목록
- 순서가 필요하지 않은 목록
```
1. dkdkdk
- dkslfjlskfjlsdkf
+ selfjsleifjlsiejf
* sdljflskdjflsdj
2. sldkjflsakjdflak
- lsdkjflskjdfalsd
    - sldjflaksjfld

1. 순서가 필요한 목록
1. 순서가 필요한 목록
    1. 순서가 필요한 목록(서브)
    1. 순서가 필요한 목록(서브)
1. 순서가 필요한 목록

- 순서가 필요하지 않은 목록
    - 순서가 필요하지 않은 목록(서브)
    - 순서가 필요하지 않은 목록(서브)
      - 순서가 필요 없는 목록 (서서브)
- 순서가 필요하지 않은 목록
- 순서가 필요하지 않은 목록

## 링크(Links)
```markdown
[GOOGLE](https://google.com)

[NAVER](https://naver.com "NAVER로 이동!")

동해물과 [백두산](https://namu.wiki/w/%EB%B0%B1%EB%91%90%EC%82%B0)이 마르고 닳도록
```
[GOOGLE](https://google.com)

[NAVER](https://naver.com "NAVER로 이동!")

동해물과 [백두산](https://namu.wiki/w/%EB%B0%B1%EB%91%90%EC%82%B0)이 마르고 닳도록

## 이미지(Images)
### 단순 이미지 삽입
``` markdown
![HEROPY](https://avatars.githubusercontent.com/u/126153180?v=4)
```
![HEROPY](https://avatars.githubusercontent.com/u/126153180?v=4)

### 링크있는 이미지 삽입
``` markdown
[![HEROPY](https://avatars.githubusercontent.com/u/126153180?v=4)](https://github.com/cito-pes/)
```
[![HEROPY](https://avatars.githubusercontent.com/u/126153180?v=4)](https://github.com/cito-pes/)

## 코드(Code) 강조

### 인라인(inline) 코드 강조
```markdown
`background`혹은 `background-image` 속성으로 요소에 배경 이미지를 삽입할 수 있습니다.
```
`background`혹은 `background-image` 속성으로 요소에 배경 이미지를 삽입할 수 있습니다.

### 블록(block) 코드 강조

````markdown
```html
<a href="https://www.google.co.kr/" target="_blank">GOOGLE</a>
```
````

```html
<a href="https://www.google.co.kr/" target="_blank">GOOGLE</a>
```
````markdown
```css
.list > li {
  position: absolute;
  top: 40px;
}
```
````
```css
.list > li {
  position: absolute;
  top: 40px;
}
```
````markdown
```javascript
function func() {
  var a = 'AAA';
  return a;
}
```
````

```javascript
function func() {
  var a = 'AAA';
  return a;
}
```
````markdown
```bash
$ git commit -m 'Study Markdown'
```
````
```bash
$ git commit -m 'Study Markdown'
```
````markdown
```plaintext
동해물과 백두산이 마르고 닳도록
하느님이 보우하사 우리나라 만세
```
````
```plaintext
동해물과 백두산이 마르고 닳도록
하느님이 보우하사 우리나라 만세
```

## 표(Table)
```markdown
값 | 의미 | 기본값
---|:---:|---:
`static` | 유형(기준) 없음 / 배치 불가능 | `static`
`relative` | 요소 **자신**을 기준으로 배치 |
`absolute` | 위치 상 **_부모_(조상)요소**를 기준으로 배치 |
`fixed` | **브라우저 창**을 기준으로 배치 |
```

값 | 의미 | 기본값
---|:---:|---:
`static` | 유형(기준) 없음 / 배치 불가능 | `static`
`relative` | 요소 **자신**을 기준으로 배치 |
`absolute` | 위치 상 **_부모_(조상)요소**를 기준으로 배치 |
`fixed` | **브라우저 창**을 기준으로 배치 |

## 인용문(BlockQuote)
```markdown
인용문(blockQuote)

> 남의 말이나 글에서 직접 또는 간접으로 따온 문장.
> (네이버 국어 사전)

> 인용문을 작성하세요!
>> 중첩된 인용문
>>> 중중첩된 인용문 1  
>>> 중중첩된 인용문 2  
>>> 중중첩된 인용문 3
```
인용문(blockQuote)

> 남의 말이나 글에서 직접 또는 간접으로 따온 문장.
> (네이버 국어 사전)

> 인용문을 작성하세요!
>> 중첩된 인용문
>>> 중중첩된 인용문 1  
>>> 중중첩된 인용문 2  
>>> 중중첩된 인용문 3

## 원시 HTML(Raw HTML)
```markdown
<strong>동해물</strong>과 <u>백두산</u>이 마르고 닳도록<br/>
하느님이 보우하사 우리나라 만세

<img width="100" src="https://avatars.githubusercontent.com/u/126153180?v=4" alt="cito" />
```
<strong>동해물</strong>과 <u>백두산</u>이 마르고 닳도록<br/>
하느님이 보우하사 우리나라 만세

<img width="100" src="https://avatars.githubusercontent.com/u/126153180?v=4" alt="cito" />

## 수평선(Horizontal Rule)
```markdown
---

***

___
```
---

***

___
