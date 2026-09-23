# Tomcat 10.1 설치 정보 + Eclipse(eGovFrame) 서버 등록 가이드

> 작성: 2026-08-08(토). Tomcat 설치·검증은 완료 상태이며, 이 문서는 **Eclipse GUI에서 서버를 등록하는 사용자 작업**(5분)만 안내한다.

## 1. 설치된 것 (완료, 손댈 것 없음)
| 항목 | 값 |
|---|---|
| Tomcat | **10.1.57** (SHA512 무결성 검증 완료) |
| 위치 | `D:\tomcat\apache-tomcat-10.1.57` |
| JVM | JDK21 Temurin — `bin\setenv.bat`에 자동 지정됨 |
| Maven | 3.9.16 — `D:\tools\apache-maven-3.9.16` |
| 포트 | 8080 (HTTP) / 8005 (shutdown) |
| 배포 확인 | univa-survey WAR가 Oracle(FREEPDB1) 연결로 구동 검증 완료 |

## 2. 명령줄 구동/정지 (Eclipse 없이 확인할 때)
`run`은 그 창을 점유하므로 **정지는 반드시 새 PowerShell 창에서** (환경변수는 창마다 다시 설정):
```powershell
# [창 1] 구동
$env:CATALINA_HOME="D:\tomcat\apache-tomcat-10.1.57"
& "$env:CATALINA_HOME\bin\catalina.bat" run
```
```powershell
# [창 2] 정지
$env:CATALINA_HOME="D:\tomcat\apache-tomcat-10.1.57"
& "$env:CATALINA_HOME\bin\catalina.bat" stop
```
브라우저: http://localhost:8080/univa-survey/

## 2-1. ★코드 수정 후 재빌드·재배포 (명령줄 개발 사이클 — 매번 이걸 씀)
소스(java/jsp/xml) 수정 후 아래를 **한 번에** 실행하면 정지→빌드→재배포→기동까지 됨 (PowerShell):
```powershell
$env:CATALINA_HOME="D:\tomcat\apache-tomcat-10.1.57"
$env:JAVA_HOME="C:\Program Files\Eclipse Adoptium\jdk-XXX.XXX.XXX.XXX-hotspot"
& "$env:CATALINA_HOME\bin\catalina.bat" stop
Start-Sleep -Seconds 6
Set-Location "D:\egov\workspace-egov\univa-survey"
& "D:\tools\apache-maven-3.9.16\bin\mvn.cmd" clean package -DskipTests -B -q
if ($LASTEXITCODE -eq 0) {
  Remove-Item "$env:CATALINA_HOME\webapps\univa-survey" -Recurse -Force -ErrorAction SilentlyContinue
  Remove-Item "$env:CATALINA_HOME\webapps\univa-survey.war" -Force -ErrorAction SilentlyContinue
  Copy-Item "target\univa-survey-1.0.0.war" "$env:CATALINA_HOME\webapps\univa-survey.war"
  & "$env:CATALINA_HOME\bin\catalina.bat" start
}
```
- 기동 완료는 30초~1분 후 http://localhost:8080/univa-survey/login.do 가 200이면 됨.
- **한글 DB 작업(sqlplus)**: `chcp 65001` + `$env:NLS_LANG="KOREAN_KOREA.AL32UTF8"` 먼저(안 하면 INSERT 한글 깨짐). sqlplus 경로 `C:\app\user\product\26ai\dbhomeFree\bin\sqlplus.exe`, 접속 `univa_survey/"Survey#2026"@localhost:1521/FREEPDB1`.
- **리터럴 ID INSERT(검증/시드) 후엔** `db/util_sync_sequences.sql` 실행(시퀀스 정렬, ORA-00001 방지).

## 3. Eclipse 서버 등록 (사용자 GUI 작업, 1회)
1. eGovFrame Eclipse 실행 (D:\egov, workspace = `D:\egov\workspace-egov`)
2. 메뉴 **Window → Preferences → Server → Runtime Environments → Add...**
3. **Apache → Apache Tomcat v10.1** 선택 → Next
4. Tomcat installation directory = `D:\tomcat\apache-tomcat-10.1.57`
   JRE = **jdk-XXX.XXX.XXX.XXX-hotspot** (Installed JREs에 없으면 Installed JREs... → Add → Standard VM → `C:\Program Files\Eclipse Adoptium\jdk-XXX.XXX.XXX.XXX-hotspot`)
5. Finish → Apply and Close
6. 하단 **Servers 뷰**(없으면 Window → Show View → Servers) → 링크 클릭해 새 서버 생성 → Tomcat v10.1 → `univa-survey` 프로젝트를 Add → Finish
7. Servers 뷰에서 서버 우클릭 → **Start** → 브라우저에서 http://localhost:8080/univa-survey/ 확인

### 주의
- **명령줄 Tomcat과 Eclipse Tomcat을 동시에 켜면 포트 충돌**(8080/8005). 하나만 켤 것.
- Eclipse가 소스 수정 → 자동 재배포하므로, 개발 중에는 Eclipse 쪽으로 구동하는 게 편함.
- Oracle 서비스(`OracleServiceFREE`)가 켜져 있어야 앱이 뜬다. 안 쓸 때 껐다면:
  `Start-Service OracleServiceFREE` (관리자 PowerShell)
- **Eclipse는 setenv.bat을 무시한다**: Eclipse가 띄우는 Tomcat은 §3-4에서 고른 JRE(JDK21)와 자체 launch 설정을 쓴다. heap 등 JVM 옵션이 필요하면 Servers 뷰에서 서버 더블클릭 → *Open launch configuration* → VM arguments에 입력.
- **이중 배포 주의**: 명령줄용으로 `webapps\univa-survey.war`가 이미 배포돼 있다. Eclipse 서버 생성 시 기본값(workspace metadata에 별도 사본)을 그대로 쓰면 충돌 없음. "Use Tomcat installation (takes control)"을 고르면 기존 WAR와 같은 컨텍스트로 충돌하므로 그 경우 webapps의 univa-survey.war·폴더를 지울 것.
- **JDK 업데이트 시**: Temurin이 업데이트되면 폴더명이 바뀐다(`jdk-21.0.13...`). 그러면 `bin\setenv.bat`의 JAVA_HOME 경로도 같이 고쳐야 명령줄 구동이 된다.
- **보안(2026-08-08 적용)**: server.xml의 8080 커넥터를 `address="127.0.0.1"`로 localhost 전용 바인딩(외부 접근 원천 차단), 기본 웹앱(docs/examples/manager/host-manager) 제거됨. LAN의 다른 기기(폰 등)에서 테스트하려면 address 줄을 지우고 재기동.

## 4. 현재 앱 상태 (2026-08-08 기준)
- DataSource = **Oracle 26ai Free FREEPDB1**, 계정 `univa_survey` (`context-datasource.xml`)
- pom.xml에 ojdbc11(XXX.XXX.XXX.XXX.04) + commons-dbcp2(2.13.0) 추가됨
- 샘플 게시판 SQL의 페이징을 Oracle 문법(OFFSET…FETCH)으로 수정함
- 검증 완료: 목록 조회(READ)·글 등록(INSERT+채번 SAMPLE-00115)·페이징 전부 Oracle에서 정상
- SAMPLE/IDS 테이블은 eGov 예제 검증용 — 실개발 시작 후 삭제 예정 (42테이블 본 스키마와 별개)
