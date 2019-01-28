---
layout : post
title : Linux/리눅스 자주 쓰는 명령어 정리
---

&nbsp;실무에서 자주 쓰는 Linux/리눅스 명령어를 설명과 함께 정리한다.<br/>
&nbsp;리눅스에는 명령어와 옵션이 이것말고도 아주 많이 있지만, 내 기준으로 주로 사용하는 명령어를 기준으로 꾸준히 업데이트 한다.

- <span class="accent bold">pwd</span> - 현재 위치한 경로 표시
- <span class="accent bold">cd</span> - 현재 위치한 경로 이동
- <span class="accent bold">ls</span> - 현재 폴더의 폴더/파일들의 이름 표시
- <span class="accent bold">ls -l</span> - 현재 폴더내 폴더/파일들의 이름, 퍼미션, 파일수 등 상세정보 표시
- <span class="accent bold">ls -al</span> - 현재 폴더내 폴더/파일 (숨김파일 포함) 들의 이름, 퍼미션 등 상세정보표시
- <span class="accent bold">cp</span> - 폴더/파일 복사
- <span class="accent bold">cp -r</span> - 폴더/파일 및 하위경로까지 복사
- <span class="accent bold">mv</span> - 폴더/파일 이동
- <span class="accent bold">rm</span> - 폴더/파일 삭제 (삭제전 확인)
- <span class="accent bold">rm -r</span> - 폴더/파일 및 하위경로까지 삭제
- <span class="accent bold">mkdir</span> - 폴더 생성
- <span class="accent bold">cat</span> - 파일의 내용을 화면에 출력
- <span class="accent bold">more</span> - 화면 단위로 파일의 내용을 출력
- <span class="accent bold">find</span> - 폴더/파일 검색
- <span class="accent bold">find -name</span> - 특정 이름의 폴더/파일 검색
- <span class="accent bold">touch</span> - 크기가 0인 파일 생성
- <span class="accent bold">chown</span> [소유자:소유그룹] [폴더/파일명] - 폴더/파일의 소유자 변경
- <span class="accent bold">chmod</span> [옵션] [퍼미션] [폴더/파일명] - 폴더/파일의 퍼미션 변경
- <span class="accent bold">chgrp</span> [옵션] [그룹] [폴더/파일명] - 폴더/파일의 소유 그룹 수정
- <span class="accent bold">tar -cvf</span> [압축파일.tar] [폴더/파일명] - tar로 압축하기
- <span class="accent bold">tar -xvf</span> [압축파일.tar] - tar로 압축풀기
- <span class="accent bold">tar -zcvf</span> [압축파일.tar.gz] [폴더/파일명] - tar.gz로 압축하기
- <span class="accent bold">tar -zxvf</span> [압축파일.tar.gz] - tar.gz로 압축풀기
- <span class="accent bold">gzip</span> [폴더/파일명] - gz로 압축하기
- <span class="accent bold">gzip -d</span> [압축파일.gz] - gz로 압축풀기
- <span class="accent bold">yum</span> - 인터넷을 연결해서 rpm 패키지 설치
- <span class="accent bold">yum install nano -y</span> - Nano 에디터 설치
- <span class="accent bold">crontab</span> - 반복작업 관리
- <span class="accent bold">free</span> - 시스템 메모리 정보 출력
- <span class="accent bold">ps</span> - 현재 실행되고 있는 프로세스 목록 출력
- <span class="accent bold">pstree</span> - 프로세스의 정보를 트리 형식으로 출력
- <span class="accent bold">top</span> - 실시간으로 시스템 상황 모니터링
- <span class="accent bold">killall</span> [프로세스명] - 특정 이름의 프로세스를 모두 종료
- <span class="accent bold">kill -9</span> [PID] - 특정 PID를 가진 프로세스 종료
- <span class="accent bold">shutdown</span> - 시스템 종료
- <span class="accent bold">poweroff</span> - 시스템 종료
- <span class="accent bold">reboot</span> - 시스템 재부팅
- <span class="accent bold">shutdown -r now</span> - 시스템 재부팅
- <span class="accent bold">ping</span> - IP 네트워크를 통해 특정한 호스트가 도달할 수 있는지의 여부를 테스트
- <span class="accent bold">ifconfig</span> - 리눅스 네트워크 인터페이스 설정 확인/관리
- <span class="accent bold">netstat</span> - 네트워크 접속, 라우팅 테이블, 네트워크 인터페이스의 통계 정보를 보여주는 도구
- <span class="accent bold">traceroute</span> - 네트워크를 통해 목적지에 도달하는 경로를 수집하는 리눅스 명령어
- <span class="accent bold">route</span> - 리눅스에서 route 설정 상태 확인하는 명령어
- <span class="accent bold">clock</span> - 하드웨어 시계 조회/설정하는 리눅스 명령어
- <span class="accent bold">date</span> - 시스템 날짜, 시간을 출력 또는 변경하는 리눅스 명령어
