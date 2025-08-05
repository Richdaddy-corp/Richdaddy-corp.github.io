---
title: "원격서버의 파이썬 스크립트 실행하기."
date: 2025-08-06 07:00:00 +/-TTTT
categories: [Work, Study, Python, Pyside6]
tags: [Work, Study, Python, Pyside6, Remote, 원격지 스크립트 실행, 파이썬 실행, 업무자동화]
math: true
toc: true
author: 
img_path: /assets/img/for_post/
image:
  path: 2025-08-06-Remote_Script_run.png #썸네일
  alt: "python remote_ui_with_selection.py 코드"
pin: false
description: 너무 나를 귀찮게 해서 자동화 프로그램 하나 만들어 던져줌, 원격서버의 파이썬 실행하는 스크립트
---
#

업무시간에 너~~~ 무 나를 귀찮게 한다.  
원래의 파이썬 스크립트는 매 시간 자동으로 실행 되도록 백그라운드 처리가 되어있는데,  실무 부서의 입장에서는 바로바로 확인이 되었으면 하나보다.  
하루에도 5~6회 요청을 한다.

--------------------
> **<u>KEYWORDS</u>**         
> paramiko, python, 파이썬 스크립트, 원격실행, 업무자동화, 귀차니즘
{: .prompt-info }
--------------------
# 나좀 내버려둬
`원격지`에 있는 거래처에서 `FTP`를 통해서 회사 NAS의 `업체폴더`로 파일을 전송한다. 보안상 직원들이 보는 `메인폴더`를 바로 열어줄 수 없으니...  
`업체폴더`에 들어와 있는 파일을 주기적 (1시간 간격)으로 `메인폴더`로 신규파일을 복사한다.  
파일을 확인해야 하는 담당부서는 자기가 보고싶을때 확인을 해야하니 나한테 `파일 확인 및 복사`를 요청함.

## 귀찮음이 자동화를 만든다.
개발자는 뭔가 불편하고 귀찮을때 꼼지락거리면서 뭔가를 만든다.  
눈으로 확인하고 복사하는게 귀찮아서 1시간 간격으로 스케쥴을 걸어 처리를 해왔다.  

요청할때마다 스크립트 실행하는게 귀찮아서   
담당자가 할수 있게 파이썬으로 된 `UI`프로그램을 만들기로 했다.  
콘솔열어서 로그인해서 스크립트 실행하라고 하면 못하니  
`UI`로 만들어 줘야 쓴다.

# PYTHON 
## 뭘 어떻게?
1. `UI`로 만들어야 한다. 배포를 해야하니 Pyside6로 한다.
2. 실행해야할 스크립트는 3종류가 있다. `선택해서 실행` 할 수 있어야 한다.
3. 실행 `결과`(원래 있던 스크립트가 뿌려주는 print)를 확인 할 수 있어야 한다.

## 파이썬 라이브러리 설치
```bash
pip install PySide6 paramiko
```

## 파이썬 코드
```python
# remote_ui_with_selection.py

import sys
import paramiko
from PySide6.QtCore import QThread, Signal
from PySide6.QtWidgets import (
    QApplication, QMainWindow, QWidget, QVBoxLayout, QHBoxLayout,
    QPushButton, QPlainTextEdit, QRadioButton, QButtonGroup
)


class SSHWorker(QThread):
    output_received = Signal(str)
    finished = Signal()

    def __init__(self, host, port, username, password, remote_script):
        super().__init__()
        self.host = host
        self.port = port
        self.username = username
        self.password = password
        self.remote_script = remote_script

    def run(self):
        try:
            client = paramiko.SSHClient()
            client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
            client.connect(
                hostname=self.host,
                port=self.port,
                username=self.username,
                password=self.password,
                timeout=10
            )

            stdin, stdout, stderr = client.exec_command(f'python3 {self.remote_script}')

            for line in iter(stdout.readline, ""):
                self.output_received.emit(line.rstrip())
            for line in iter(stderr.readline, ""):
                self.output_received.emit(f"[ERROR] {line.rstrip()}")

            client.close()

        except Exception as e:
            self.output_received.emit(f"[EXCEPTION] {e}")
        finally:
            self.finished.emit()


class MainWindow(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Remote Script Runner")
        self.resize(700, 500)

        # 라디오 버튼 구성
        self.radio1 = QRadioButton("스크립트 A (/home/you/script_a.py)")
        self.radio2 = QRadioButton("스크립트 B (/home/you/script_b.py)")
        self.radio3 = QRadioButton("스크립트 C (/home/you/script_c.py)")
        self.radio1.setChecked(True)

        self.button_group = QButtonGroup()
        self.button_group.addButton(self.radio1, id=1)
        self.button_group.addButton(self.radio2, id=2)
        self.button_group.addButton(self.radio3, id=3)

        radio_layout = QVBoxLayout()
        radio_layout.addWidget(self.radio1)
        radio_layout.addWidget(self.radio2)
        radio_layout.addWidget(self.radio3)

        # 실행 버튼과 콘솔창
        self.run_button = QPushButton("원격 스크립트 실행")
        self.console = QPlainTextEdit()
        self.console.setReadOnly(True)

        # 레이아웃 병합
        top_layout = QHBoxLayout()
        top_layout.addLayout(radio_layout)
        top_layout.addWidget(self.run_button)

        main_layout = QVBoxLayout()
        main_layout.addLayout(top_layout)
        main_layout.addWidget(self.console)

        container = QWidget()
        container.setLayout(main_layout)
        self.setCentralWidget(container)

        # 서버 정보 설정 (본인 환경에 맞게 수정)
        self.host = "192.168.0.10"
        self.port = 22
        self.username = "your_user"
        self.password = "your_password"
        self.scripts = {
            1: "/home/you/script_a.py",
            2: "/home/you/script_b.py",
            3: "/home/you/script_c.py"
        }

        self.worker = None
        self.run_button.clicked.connect(self.start_remote)

    def start_remote(self):
        self.run_button.setEnabled(False)
        self.console.clear()

        selected_id = self.button_group.checkedId()
        script_path = self.scripts.get(selected_id, "")
        self.worker = SSHWorker(
            self.host, self.port, self.username, self.password, script_path
        )
        self.worker.output_received.connect(self.append_output)
        self.worker.finished.connect(self.on_finished)
        self.worker.start()

    def append_output(self, text: str):
        self.console.appendPlainText(text)

    def on_finished(self):
        self.console.appendPlainText("\n[실행 종료]")
        self.run_button.setEnabled(True)


if __name__ == "__main__":
    app = QApplication(sys.argv)
    window = MainWindow()
    window.show()
    sys.exit(app.exec())
```
## 사용법
- 위 코드를 `remote_ui_with_selection.py`로 저장합니다.
- `host`, `username`, `password`와 scripts 딕셔너리 내 경로를 본인 서버 환경에 맞게 수정합니다.
- 터미널에서 `python remote_ui_with_selection.py`를 실행합니다.
- GUI에서 원하는 스크립트를 라디오 버튼으로 선택한 뒤 “`원격 스크립트 실행`”을 클릭하면 선택된 스크립트가 실행되며 로그가 콘솔에 표시됩니다.

#

간단한 프로그램이다.
물론. 실사용에는 좀더 이쁘게 아이콘 같은 걸 넣기도 하고, 버튼이랑 프로그램 타이틀도 바꿨다.

![HEROPY](/assets/img/for_post/2025-08-05-remote_py.png)


