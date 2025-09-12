---
title: K-pop 데몬헌터스 OST 유튜브 다운로드 방법 및 파이썬 자동화 가이드
description: 유튜브에서 K-pop 데몬헌터스 OST를 개인 감상용으로 다운로드하는 이야기와 파이썬 스크립트 가이드. 저작권 준수와 FFmpeg·yt-dlp 활용법까지 자세히 설명합니다.
author:
date: 2025-08-04
tags:
  - K-pop OST 다운로드
  - 유튜브 오디오 추출
  - 파이썬 yt-dlp 스크립트
  - 데몬헌터스 OST 저장
  - FFmpeg 음악 변환
keywords:
  - K-pop 데몬헌터스 OST
  - 유튜브 음악 다운로드
  - yt-dlp 사용법
  - 파이썬 음악 저장
  - FFmpeg mp3 변환
  - OST 자동 저장
  - 무료 음악 저장 방법
  - 개인 감상용 유튜브 다운로드
  - 게임 OST 저장하기
  - Python으로 유튜브 다운로드
canonical: https://richdaddy-corp.github.io/kpop-demonhunters-ost-download-guide
robots: index, follow
language: ko-KR
image:
  path: https://img.hankyung.com/photo/202508/01.40930753.1.jpg
  alt: 한경
pin : false
math : false
mermaid : true
sitemap :
  changefreq : daily
  priority : 1.0
---

# K-pop 데몬헌터스 OST 다운로드 & 이해하기 쉬운 파이썬 자동화 가이드
----------------------
> **<u> 📌 KEYWORDS</u>**   
- K-pop 데몬헌터스 OST 다운로드
- 유튜브에서 게임 음악 저장하기
- 파이썬으로 음원 추출하는 법
- yt-dlp와 FFmpeg을 활용한 오디오 변환
- 저작권 걱정 없는 개인 음악 저장
{: .prompt-info }
----------------------

## 1. 이야기를 먼저 들어볼래요?
당신이 흠뻑 빠져 있는 애니메이션 “[**``K-pop 데몬헌터스``**](https://www.google.com/search?gs_ssp=eJzj4tVP1zc0zC5IMUgySDc2YPQSyi7IL1BISc3Nz1PIKM0rSS0qBgC9DQt9&q=kpop+demon+hunters&oq=%E3%85%8F%E3%85%94%E3%85%90%E3%85%94&gs_lcrp=EgZjaHJvbWUqDQgBEC4YgwEYsQMYgAQyBggAEEUYOTINCAEQLhiDARixAxiABDINCAIQABiDARixAxiABDIGCAMQABgDMgcIBBAAGIAEMgoIBRAAGLEDGIAEMgcIBhAAGIAEMgYIBxAAGAMyBwgIEAAYgATSAQgyODQ1ajBqN6gCALACAA&sourceid=chrome&ie=UTF-8)”는 사방에서 쿵쿵대는 EDM 베이스와 케이팝 풍 멜로디가 어우러진 OST로 유명하죠.
퇴근길 지하철에서, 혹은 산책길 이어폰 속에서 그 음악이 재생되면 플레이하던 전투 장면이 한눈에 떠올라 피로가 스르르 풀려요. 문제는 모바일 데이터. 유튜브 스트리밍만 믿고 있다가 지하 구간에 들어서면 “로딩…”. 음악은 끊기고 감동은 반 토막.
“아예 오프라인으로 저장해 두면 어떨까?”
이 질문이 오늘 가이드를 시작하게 된 출발점입니다.

## 2. 저작권 & 유튜브 약관, 꼭 짚고 가요
- 유튜브 서비스 약관은 기본적으로 상업적 용도 (재배포·재가공 포함) 다운로드를 금지합니다. 개인 감상을 위한 범위라도, 체류 국가의 저작권법이 우선이에요.
- 제작사(혹은 퍼블리셔)가 “다운로드 OK”나 “Creative Commons” 표시를 명시하지 않았다면, 개인 소장만 하더라도 법적 논쟁 여지는 존재합니다.
- 결론: 아래 스크립트는 ① 완전히 개인 감상 목적 ② 정식 구매가 불가능하거나 사라진 음원인 경우에 한해 참고용 예시로만 사용해 주세요.

## 3. 준비물 한눈에 보기

 구분 | 이유 | 설치 명령 | 비고
 -------|------------|-----------------------|----------
 `Python 3.9+` | 스크립트 실행 환경 |   | Windows 11·macOS 모두 OK 
 `yt-dlp` | 유튜브 다운로드 핵심 라이브러리<br>(유튜브-DL 후속) | pip install yt-dlp | 유지보수 활발 
 `FFmpeg` | 오디오 추출·변환 | Windows: choco install ffmpeg<br>macOS: brew install ffmpeg | PATH 등록 필요 



## 4. 30줄 파이썬 스크립트
``` python
"""
kpop_demonhunters_downloader.py
다운로드 URL  ➜ mp3 추출 ➜ 메타데이터 정리까지 one-shot
테스트 환경: Python 3.11 / yt-dlp 2024.4.x / FFmpeg 6.x
"""

from pathlib import Path
from datetime import datetime
import subprocess
import sys
import yt_dlp

# ---- 1) 설정값만 바꾸면 끝! ---------------------------------
VIDEO_URL   = "https://www.youtube.com/watch?v=XXXXXXXXXXX"  # OST 영상 주소
SAVE_FOLDER = Path.home() / "Music" / "Kpop_DemonHunters"
FILE_TITLE  = "Kpop_DemonHunters_OST"
AUDIO_FMT   = "mp3"        # wav, m4a 등도 가능
BITRATE     = "192"        # kbps
# -----------------------------------------------------------

def ensure_dir(path: Path):
    path.mkdir(parents=True, exist_ok=True)

def download_audio(video_url: str, out_dir: Path, fname: str):
    ydl_opts = {
        "format": "bestaudio/best",
        "outtmpl": str(out_dir / f"{fname}.%(ext)s"),
        "postprocessors": [
            {
                "key": "FFmpegExtractAudio",
                "preferredcodec": AUDIO_FMT,
                "preferredquality": BITRATE,
            },
            {
                "key": "FFmpegMetadata",
            },
        ],
        # 잔잔한 콘솔 로그
        "quiet": False,
        "noplaylist": True,
    }

    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        ydl.download([video_url])

def tag_metadata(file_path: Path):
    """
    ID3 태그 정리 예시 (ffmpeg 이용)
    """
    date_str = datetime.now().strftime("%Y")
    cmd = [
        "ffmpeg",
        "-i", str(file_path),
        "-metadata", f"title={FILE_TITLE}",
        "-metadata", "artist=Various Artists",
        "-metadata", f"album=K-pop Demonhunters OST",
        "-metadata", f"date={date_str}",
        "-codec", "copy",
        str(file_path.with_suffix(".tmp")),
    ]
    subprocess.run(cmd, check=True)
    file_path.with_suffix(".tmp").replace(file_path)

def main():
    ensure_dir(SAVE_FOLDER)
    print("▶  다운로드 시작...")
    download_audio(VIDEO_URL, SAVE_FOLDER, FILE_TITLE)
    downloaded = SAVE_FOLDER / f"{FILE_TITLE}.{AUDIO_FMT}"
    tag_metadata(downloaded)
    print(f"✓  완료! 파일 위치: {downloaded}")

if __name__ == "__main__":
    try:
        main()
    except Exception as e:
        print("⚠️  오류 발생:", e)
        sys.exit(1)
```


## 5. 코드, 줄줄이 해설
- yt_dlp: 유튜브 영상에서 가장 고음질 오디오 스트림만 골라 내려받아요.
- postprocessors:
- FFmpegExtractAudio → 다운로드된 webm/mp4에서 mp3 추출
- FFmpegMetadata → 파일 내부에 title, artist 같은 태그 삽입
- 메타데이터를 한 번 더 다듬고 싶다면 tag_metadata()처럼 FFmpeg 소환 → 무손실 복사(-codec copy)로 헤더만 업데이트.
- 디렉터리가 없다면 ensure_dir()가 자동 생성.
- 예외 발생 시 sys.exit(1)로 깔끔히 종료, CI 나 배치 작업에도 유용.


[![Video Label](http://img.youtube.com/vi/xQnsFqn44uo/0.jpg)](https://youtu.be/xQnsFqn44uo)

## 6. 실행 전·후 체크리스트
- Windows: FFmpeg 실행 파일(ffmpeg.exe)이 시스템 PATH에 잡혀 있는지 ffmpeg -version으로 확인.
- macOS/Linux: brew/apt 설치 후 터미널에서 동일하게 버전 확인.
- 다운로드 대상 영상이 지역 차단·연령 제한인 경우 yt-dlp가 추가 로그인·쿠키를 요구할 수 있어요. (공식 Wiki “FAQ” 참고)
- BITRATE=192는 휴대용 기기 감상에 무난합니다. 하이파이를 원한다면 320 kbps나 flac 선택.

## 7. 자주 묻는 Q&A
### `“영상이 통째로 mp3로 변환돼요❓”`
- 네, 영상·자막은 버리고 오디오 트랙만 추출합니다.
### `“플레이리스트 주소 넣어도 돼요❓”`
- noplaylist=True이므로 한 곡만 저장합니다. 여러 곡이면 옵션 False.
### `“에러: unsupported URL 무엇인가요❓”`
- 유튜브 shorts 등 특수 유형은 개별 영상 URL로 변환해 주세요. 
### `“클레임 뜨나요❓”`
- 다운로드 자체는 비공개 단말 내부라 감지되지 않습니다. 다만 배포·업로드 순간 저작권 클레임 발생. 

![HEROPY](https://upload.wikimedia.org/wikipedia/commons/0/05/Minhwa-Tiger_and_magpie-02.jpg "출처:KPop Demon Hunters WIKI (https://en.wikipedia.org/wiki/KPop_Demon_Hunters)")

🎧 이제 데이터 끊김 없이 K-pop 데몬헌터스 OST로 하루를 버닝해 보세요!
다른 자동화 아이디어나 스크립트 튜닝이 필요하면 언제든 불러주세요. 항상 코드와 이야기, 두 마리 토끼를 동시에 잡아드릴 준비가 되어 있답니다. 🐰💻
