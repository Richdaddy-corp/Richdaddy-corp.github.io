---
title: 로컬 폴더 ↔ GitHub 자동 동기화 Python 프로그램
date: 2025-08-01 13:00:00 +/-TTTT
categories: [Study]
tags: [Python, Study]
math: true
toc: true
author: 
img_path: /assets/img/for_post/
image: 
  path: 2025-08-01-Auto-Github-Sync-Title.png #썸네일
  alt: "GitHub 자동 동기화 Python"
pin: false
description: Python, GitPython, watchdog, schedule을 활용하여 로컬 폴더와 GitHub 저장소를 동기화하는 방법을 단계별로 설명합니다.
---

# 로컬 ↔ GitHub 폴더 자동 동기화 Python 프로그램

--------------
> **<u>KEYWORDS</u>**    
> Python GitHub 동기화, folder sync Python, 자동화, GitPython tutorial, watchdog 사용법
{: .prompt-info }
----------------------

## 1. 개요
이 문서는 로컬 PC(Windows/macOS)에서 특정 폴더를 GitHub 저장소의 지정 폴더와 주기적으로 동기화하는 Python 프로그램 개발 및 배포 가이드입니다. 초보자도 쉽게 따라 할 수 있도록 설치부터 실행, 백그라운드 서비스 등록까지 단계별로 설명합니다.

## 2. 요구사항
- Python 3.8 이상
- Git CLI 설치 및 GitHub 계정 권한(토큰 또는 SSH 키)
- 인터넷 연결

## 3. 설치 및 설정
### 3.1 필수 프로그램
- Git : [`깃허브`](https://git-scm.com/)
- Python: [`파이썬`](https://python.org/)

### 3.2 Python 가상환경(venv) 설정
#### 프로젝트 폴더 생성
``` 
mkdir folder_sync && cd folder_sync
```
#### venv 생성
``` 
python3 -m venv venv
```

#### venv 활성화 (Windows)
``` 
venv\Scripts\activate
``` 

#### venv 활성화 (macOS/Linux)
```
source venv/bin/activate
```


### 3.3 필요한 Python 모듈 설치
```  
pip install PyYAML schedule gitpython watchdog pillow
```
- PyYAML: 설정파일(config.yaml) 파싱
- schedule: 주기적 동기화
- GitPython: Git CLI 래퍼
- watchdog: 로컬 파일 변경 감지
- pillow: 이미지 처리 (타이틀/설명 이미지)


## 4. 프로그램 구조 및 주요 기능
```
folder_sync/
├── venv/                   
├── images/                 # 생성된 이미지 파일
│   ├── title.png
│   └── sync_flow.png
├── config.yaml             # 설정 파일
├── sync.py                 # 메인 실행 파일
├── git_sync.py             # GitHub 동기화 로직
├── watcher.py              # 폴더 변경 감지 로직
└── README.md               # 이 문서
```
- config.yaml: 로컬 폴더, 원격 GitHub 저장소 URL, 동기화 간격 설정
- sync.py: 스케줄러와 감시자 초기화
- git_sync.py: Git clone, fetch, pull, commit, push 구현
- watcher.py: watchdog 기반 로컬 변경 감지

## 5. 핵심 코드 설명
### 5.1 config.yaml 예시
``` yaml
local_path: "/Users/jino/project/local_folder"
remote:
  repo_url: "https://github.com/username/repo.git"
  branch: "main"
  remote_path: "target_folder"
interval_minutes: 5
```

### 5.2 GitSync 클래스 (git_sync.py)
``` python
import os
from git import Repo

class GitSync:
    def __init__(self, repo_url, local_path, branch="main"):
        self.local_path = local_path
        if not os.path.isdir(local_path):
            self.repo = Repo.clone_from(repo_url, local_path, branch=branch)
        else:
            self.repo = Repo(local_path)

    def pull(self):
        origin = self.repo.remotes.origin
        origin.pull()

    def push(self, message="Auto-sync commit"):
        self.repo.git.add(A=True)
        if self.repo.is_dirty():
            self.repo.index.commit(message)
            origin = self.repo.remotes.origin
            origin.push()
```
설치된 리포지토리가 없으면 클론, 있으면 pull 실행 후 변경사항 발생 시 commit & push 수행합니다.


### 5.3 Watcher (watcher.py)
``` python
import time
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler

class ChangeHandler(FileSystemEventHandler):
    def __init__(self, sync_callback):
        self.sync_callback = sync_callback

    def on_any_event(self, event):
        self.sync_callback()

def start_watch(path, sync_callback):
    event_handler = ChangeHandler(sync_callback)
    observer = Observer()
    observer.schedule(event_handler, path, recursive=True)
    observer.start()
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        observer.stop()
    observer.join()
```

로컬 폴더 변화를 감지하면 즉시 동기화 콜백을 호출합니다.

### 5.4 스케줄러 및 실행 (sync.py)
``` python
import yaml
import schedule
import time
from git_sync import GitSync
from watcher import start_watch

def load_config():
    with open("config.yaml") as f:
        return yaml.safe_load(f)

def sync_job():
    config = load_config()
    gs = GitSync(config['remote']['repo_url'], config['local_path'], config['remote']['branch'])
    gs.pull()
    gs.push()

if __name__ == "__main__":
    config = load_config()
    schedule.every(config['interval_minutes']).minutes.do(sync_job)
    # 백그라운드 워처 동작
    from threading import Thread
    watcher_thread = Thread(target=start_watch, args=(config['local_path'], sync_job))
    watcher_thread.daemon = True
    watcher_thread.start()

    while True:
        schedule.run_pending()
        time.sleep(1)
```

## 6. 실행 및 백그라운드 서비스 등록
### 6.1 Windows 서비스 등록 (NSSM 사용)
``` 
- NSSM 다운로드 및 설치: https://nssm.cc/
- 서비스 생성
nssm install FolderSyncService C:\path\to\venv\Scripts\python.exe C:\path\to\folder_sync\sync.py
- 서비스 시작
nssm start FolderSyncService
```

### 6.2 macOS Launch Agent
``` bash
- ~/Library/LaunchAgents/com.user.foldersync.plist 생성
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"...>
<plist version="1.0">
  <dict>
    <key>Label</key><string>com.user.foldersync</string>
    <key>ProgramArguments</key>
    <array>
      <string>/Users/jino/project/folder_sync/venv/bin/python</string>
      <string>/Users/jino/project/folder_sync/sync.py</string>
    </array>
    <key>RunAtLoad</key><true/>
    <key>KeepAlive</key><true/>
  </dict>
</plist>
- 로드
launchctl load ~/Library/LaunchAgents/com.user.foldersync.plist
```


## 7. 사용 방법
- config.yaml에 설정 입력
- 가상환경 활성화 및 sync.py 실행
- 백그라운드 서비스 상태 확인

## 8. 주의사항
- repository URL 및 권한 설정 확인
- 원격 폴더 권한 오류 시 SSH 키 사용 권장
- 대용량 파일 동기화 시 성능 저하 우려

