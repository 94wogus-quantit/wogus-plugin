# MCP 도구 참조 문서

workflow-skills 플러그인에서 제공하는 8개 MCP 서버의 도구 목록과 Permission 패턴입니다.

---

## Permission 패턴 규칙

### 기본 문법

| 패턴 | 설명 | 예시 |
|------|------|------|
| `mcp__<server>` | 서버의 **모든 도구** 허용/거부 | `mcp__plugin_workflow-skills_sequential-thinking` |
| `mcp__<server>__<tool>` | **특정 도구만** 허용/거부 | `mcp__plugin_workflow-skills_serena__find_symbol` |

### 중요 규칙

1. **와일드카드 미지원**: `mcp__server__*` 같은 패턴 사용 불가
2. **우선순위**: Deny > Ask > Allow
3. **서버 이름 형식**: `plugin_workflow-skills_{mcp_id}` (언더스코어로 연결)

### 서버 이름 구조

```
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking
│    │      │                 │                  │
│    │      │                 │                  └─ 도구 이름
│    │      │                 └─ MCP 서버 ID
│    │      └─ 플러그인 이름
│    └─ 접두사
└─ MCP 접두사
```

---

## sequential-thinking

**설명**: 체계적 사고를 위한 Sequential Thinking MCP
**환경 변수**: 없음

### 도구 목록 (1개)

| 도구 이름 | 설명 |
|-----------|------|
| `sequentialthinking` | 다단계 문제 해결을 위한 순차적 사고 프로세스 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_sequential-thinking

# 개별 도구
mcp__plugin_workflow-skills_sequential-thinking__sequentialthinking
```

---

## context7

**설명**: 라이브러리 문서 조회 (Context7 API)
**환경 변수**: `CONTEXT7_API_KEY`

### 도구 목록 (2개)

| 도구 이름 | 설명 |
|-----------|------|
| `resolve-library-id` | 라이브러리 이름을 Context7 호환 ID로 변환 |
| `get-library-docs` | 라이브러리 문서 및 코드 예시 조회 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_context7

# 개별 도구
mcp__plugin_workflow-skills_context7__resolve-library-id
mcp__plugin_workflow-skills_context7__get-library-docs
```

---

## serena

**설명**: 코드 심볼 분석 및 검색
**환경 변수**: 없음

### 도구 목록 (24개)

#### 파일 탐색

| 도구 이름 | 설명 |
|-----------|------|
| `list_dir` | 디렉토리 내용 조회 (재귀 옵션) |
| `find_file` | 파일 마스크로 파일 검색 |
| `search_for_pattern` | 코드베이스에서 정규식 패턴 검색 |

#### 심볼 분석

| 도구 이름 | 설명 |
|-----------|------|
| `get_symbols_overview` | 파일의 최상위 심볼 개요 조회 |
| `find_symbol` | 이름 경로 패턴으로 심볼 검색 |
| `find_referencing_symbols` | 심볼을 참조하는 코드 검색 |

#### 심볼 편집

| 도구 이름 | 설명 |
|-----------|------|
| `replace_symbol_body` | 심볼 본문 교체 |
| `insert_after_symbol` | 심볼 뒤에 코드 삽입 |
| `insert_before_symbol` | 심볼 앞에 코드 삽입 |
| `rename_symbol` | 코드베이스 전체에서 심볼 이름 변경 |

#### 메모리 관리

| 도구 이름 | 설명 |
|-----------|------|
| `list_memories` | 사용 가능한 메모리 목록 조회 |
| `read_memory` | 메모리 파일 내용 읽기 |
| `write_memory` | 프로젝트 정보를 메모리에 저장 |
| `edit_memory` | 메모리 내용 수정 |
| `delete_memory` | 메모리 파일 삭제 |

#### 프로젝트 관리

| 도구 이름 | 설명 |
|-----------|------|
| `activate_project` | 프로젝트 활성화 |
| `get_current_config` | 현재 설정 조회 |
| `check_onboarding_performed` | 온보딩 완료 여부 확인 |
| `onboarding` | 프로젝트 온보딩 수행 |
| `initial_instructions` | Serena 사용 매뉴얼 조회 |

#### 사고 도구

| 도구 이름 | 설명 |
|-----------|------|
| `think_about_collected_information` | 수집 정보 충분성 검토 |
| `think_about_task_adherence` | 작업 진행 상태 점검 |
| `think_about_whether_you_are_done` | 작업 완료 여부 확인 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_serena

# 개별 도구 예시
mcp__plugin_workflow-skills_serena__find_symbol
mcp__plugin_workflow-skills_serena__search_for_pattern
mcp__plugin_workflow-skills_serena__write_memory
mcp__plugin_workflow-skills_serena__replace_symbol_body
```

---

## sentry

**설명**: 에러 트래킹 (Sentry)
**환경 변수**: `SENTRY_ACCESS_TOKEN`, `SENTRY_HOST`, `OPENAI_API_KEY`

### 도구 목록 (주요 도구)

| 도구 이름 | 설명 |
|-----------|------|
| `search_events` | Sentry 이벤트 검색 (AI 지원) |
| `search_issues` | Sentry 이슈 검색 (AI 지원) |
| `get_issue_details` | 이슈 상세 정보 조회 |
| `get_event_details` | 이벤트 상세 정보 분석 |
| `list_organizations` | 조직 목록 조회 |
| `list_projects` | 프로젝트 목록 조회 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_sentry

# 개별 도구 예시
mcp__plugin_workflow-skills_sentry__search_issues
mcp__plugin_workflow-skills_sentry__get_issue_details
```

---

## atlassian

**설명**: JIRA/Confluence 연동
**환경 변수**: `ATLASSIAN_URL`, `ATLASSIAN_USERNAME`, `ATLASSIAN_API_TOKEN`

### 도구 목록 (41개)

#### JIRA 도구 (30개)

| 도구 이름 | 설명 |
|-----------|------|
| `jira_get_issue` | 이슈 상세 정보 조회 |
| `jira_search` | JQL로 이슈 검색 |
| `jira_create_issue` | 새 이슈 생성 |
| `jira_update_issue` | 이슈 업데이트 |
| `jira_delete_issue` | 이슈 삭제 |
| `jira_transition_issue` | 이슈 상태 전환 |
| `jira_add_comment` | 이슈에 댓글 추가 |
| `jira_get_all_projects` | 모든 프로젝트 조회 |
| `jira_get_project_issues` | 프로젝트 이슈 목록 |
| `jira_get_worklog` | 작업 로그 조회 |
| `jira_get_transitions` | 가능한 상태 전환 조회 |
| `jira_search_fields` | 필드 검색 |
| `jira_get_agile_boards` | 애자일 보드 목록 |
| `jira_get_board_issues` | 보드의 이슈 목록 |
| `jira_get_sprints_from_board` | 보드의 스프린트 목록 |
| `jira_get_sprint_issues` | 스프린트 이슈 목록 |
| `jira_get_issue_link_types` | 이슈 링크 유형 조회 |
| `jira_batch_get_changelogs` | 변경 이력 일괄 조회 |
| `jira_get_user_profile` | 사용자 프로필 조회 |
| `jira_download_attachments` | 첨부파일 다운로드 |
| `jira_get_project_versions` | 프로젝트 버전 조회 |
| `jira_batch_create_issues` | 이슈 일괄 생성 |
| `jira_add_worklog` | 작업 로그 추가 |
| `jira_link_to_epic` | 에픽에 이슈 연결 |
| `jira_create_sprint` | 스프린트 생성 |
| `jira_update_sprint` | 스프린트 업데이트 |
| `jira_create_issue_link` | 이슈 링크 생성 |
| `jira_remove_issue_link` | 이슈 링크 제거 |
| `jira_create_version` | 버전 생성 |
| `jira_batch_create_versions` | 버전 일괄 생성 |

#### Confluence 도구 (11개)

| 도구 이름 | 설명 |
|-----------|------|
| `confluence_search` | 콘텐츠 검색 |
| `confluence_get_page` | 페이지 조회 |
| `confluence_create_page` | 페이지 생성 |
| `confluence_update_page` | 페이지 업데이트 |
| `confluence_delete_page` | 페이지 삭제 |
| `confluence_get_page_children` | 하위 페이지 조회 |
| `confluence_get_comments` | 댓글 조회 |
| `confluence_get_labels` | 라벨 조회 |
| `confluence_search_user` | 사용자 검색 |
| `confluence_add_label` | 라벨 추가 |
| `confluence_add_comment` | 댓글 추가 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_atlassian

# JIRA 개별 도구
mcp__plugin_workflow-skills_atlassian__jira_get_issue
mcp__plugin_workflow-skills_atlassian__jira_search
mcp__plugin_workflow-skills_atlassian__jira_create_issue

# Confluence 개별 도구
mcp__plugin_workflow-skills_atlassian__confluence_search
mcp__plugin_workflow-skills_atlassian__confluence_get_page
```

---

## terraform

**설명**: Terraform IaC 자동화 (HashiCorp)
**환경 변수**: 없음 (Docker 필요)

### 도구 목록 (33개)

#### Provider/Module/Policy (8개)

| 도구 이름 | 설명 |
|-----------|------|
| `search_providers` | 프로바이더 검색 |
| `get_provider_details` | 프로바이더 상세 정보 |
| `get_latest_provider_version` | 최신 프로바이더 버전 |
| `search_modules` | 모듈 검색 |
| `get_module_details` | 모듈 상세 정보 |
| `get_latest_module_version` | 최신 모듈 버전 |
| `search_policies` | 정책 검색 |
| `get_policy_details` | 정책 상세 정보 |

#### Terraform Cloud/Enterprise (17개)

| 도구 이름 | 설명 |
|-----------|------|
| `list_terraform_orgs` | 조직 목록 |
| `list_terraform_projects` | 프로젝트 목록 |
| `list_workspaces` | 워크스페이스 목록 |
| `get_workspace_details` | 워크스페이스 상세 |
| `create_workspace` | 워크스페이스 생성 |
| `update_workspace` | 워크스페이스 업데이트 |
| `delete_workspace_safely` | 워크스페이스 안전 삭제 |
| `list_runs` | 실행 목록 |
| `get_run_details` | 실행 상세 |
| `create_run` | 실행 생성 |
| `action_run` | 실행 작업 (apply, cancel 등) |
| `search_private_modules` | 프라이빗 모듈 검색 |
| `get_private_module_details` | 프라이빗 모듈 상세 |
| `search_private_providers` | 프라이빗 프로바이더 검색 |
| `get_private_provider_details` | 프라이빗 프로바이더 상세 |
| `create_workspace_tags` | 워크스페이스 태그 생성 |
| `read_workspace_tags` | 워크스페이스 태그 조회 |

#### Variable 관리 (8개)

| 도구 이름 | 설명 |
|-----------|------|
| `list_variable_sets` | 변수 세트 목록 |
| `create_variable_set` | 변수 세트 생성 |
| `create_variable_in_variable_set` | 변수 세트에 변수 추가 |
| `delete_variable_in_variable_set` | 변수 세트에서 변수 삭제 |
| `attach_variable_set_to_workspaces` | 변수 세트 연결 |
| `detach_variable_set_from_workspaces` | 변수 세트 해제 |
| `list_workspace_variables` | 워크스페이스 변수 목록 |
| `create_workspace_variable` | 워크스페이스 변수 생성 |
| `update_workspace_variable` | 워크스페이스 변수 업데이트 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_terraform

# 개별 도구 예시
mcp__plugin_workflow-skills_terraform__search_modules
mcp__plugin_workflow-skills_terraform__create_workspace
```

---

## amplitude

**설명**: 사용자 행동 분석 (Amplitude Analytics)
**환경 변수**: `AMPLITUDE_API_KEY`

### 도구 목록 (주요 도구)

| 도구 이름 | 설명 |
|-----------|------|
| `track_event` | 커스텀 이벤트 추적 |
| `track_page_view` | 페이지 뷰 추적 |
| `track_signup` | 회원가입 추적 및 사용자 프로필 생성 |
| `update_user_properties` | 사용자 속성 업데이트 |
| `track_revenue` | 매출/구매 추적 |
| `get_user_activity` | 사용자 활동 데이터 조회 |
| `batch_track_events` | 이벤트 일괄 추적 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_amplitude

# 개별 도구 예시
mcp__plugin_workflow-skills_amplitude__track_event
mcp__plugin_workflow-skills_amplitude__get_user_activity
```

---

## chrome-devtools

**설명**: Chrome DevTools 연동
**환경 변수**: 없음

### 도구 목록 (26개)

#### Input Automation (8개)

| 도구 이름 | 설명 |
|-----------|------|
| `click` | 요소 클릭 |
| `drag` | 드래그 앤 드롭 |
| `fill` | 입력 필드 채우기 |
| `fill_form` | 폼 자동 채우기 |
| `handle_dialog` | 대화상자 처리 |
| `hover` | 요소 호버 |
| `press_key` | 키 입력 |
| `upload_file` | 파일 업로드 |

#### Navigation (6개)

| 도구 이름 | 설명 |
|-----------|------|
| `navigate_page` | 페이지 이동 |
| `new_page` | 새 페이지 열기 |
| `close_page` | 페이지 닫기 |
| `list_pages` | 열린 페이지 목록 |
| `select_page` | 페이지 선택 |
| `wait_for` | 특정 조건 대기 |

#### Emulation (2개)

| 도구 이름 | 설명 |
|-----------|------|
| `emulate` | 기기 에뮬레이션 |
| `resize_page` | 페이지 크기 조절 |

#### Performance (3개)

| 도구 이름 | 설명 |
|-----------|------|
| `performance_start_trace` | 성능 트레이스 시작 |
| `performance_stop_trace` | 성능 트레이스 종료 |
| `performance_analyze_insight` | 성능 인사이트 분석 |

#### Network (2개)

| 도구 이름 | 설명 |
|-----------|------|
| `list_network_requests` | 네트워크 요청 목록 |
| `get_network_request` | 네트워크 요청 상세 |

#### Debugging (5개)

| 도구 이름 | 설명 |
|-----------|------|
| `take_screenshot` | 스크린샷 캡처 |
| `take_snapshot` | DOM 스냅샷 |
| `list_console_messages` | 콘솔 메시지 목록 |
| `get_console_message` | 콘솔 메시지 상세 |
| `evaluate_script` | JavaScript 실행 |

### Permission 패턴

```
# 전체 서버 (권장)
mcp__plugin_workflow-skills_chrome-devtools

# 개별 도구 예시
mcp__plugin_workflow-skills_chrome-devtools__take_screenshot
mcp__plugin_workflow-skills_chrome-devtools__list_console_messages
mcp__plugin_workflow-skills_chrome-devtools__navigate_page
```

---

## 사용 예시

### 1. 전체 MCP 서버 allow 설정

```json
{
  "permissions": {
    "allow": [
      "mcp__plugin_workflow-skills_sequential-thinking",
      "mcp__plugin_workflow-skills_serena"
    ]
  }
}
```

### 2. 특정 도구만 deny 설정

민감한 도구를 제외하고 나머지는 허용:

```json
{
  "permissions": {
    "allow": [
      "mcp__plugin_workflow-skills_atlassian"
    ],
    "deny": [
      "mcp__plugin_workflow-skills_atlassian__jira_delete_issue",
      "mcp__plugin_workflow-skills_atlassian__confluence_delete_page"
    ]
  }
}
```

### 3. 특정 도구만 ask 설정

생성/수정 작업은 매번 확인:

```json
{
  "permissions": {
    "allow": [
      "mcp__plugin_workflow-skills_atlassian__jira_get_issue",
      "mcp__plugin_workflow-skills_atlassian__jira_search"
    ],
    "ask": [
      "mcp__plugin_workflow-skills_atlassian__jira_create_issue",
      "mcp__plugin_workflow-skills_atlassian__jira_update_issue"
    ]
  }
}
```

---

## 요약 테이블

| MCP 서버 | 도구 수 | 권장 패턴 |
|----------|--------|-----------|
| sequential-thinking | 1 | `mcp__plugin_workflow-skills_sequential-thinking` |
| context7 | 2 | `mcp__plugin_workflow-skills_context7` |
| serena | 24 | `mcp__plugin_workflow-skills_serena` |
| sentry | 6+ | `mcp__plugin_workflow-skills_sentry` |
| atlassian | 41 | `mcp__plugin_workflow-skills_atlassian` |
| terraform | 33 | `mcp__plugin_workflow-skills_terraform` |
| amplitude | 7+ | `mcp__plugin_workflow-skills_amplitude` |
| chrome-devtools | 26 | `mcp__plugin_workflow-skills_chrome-devtools` |

**총 도구 수**: 140+ 개

---

## 참고

- [Claude Code Tool Use Documentation](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)
- [sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian)
- [HashiCorp Terraform MCP Server](https://developer.hashicorp.com/terraform/docs/tools/mcp-server/reference)
- [Chrome DevTools MCP](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [Sentry MCP](https://github.com/getsentry/sentry-mcp)
- [Serena](https://github.com/oraios/serena)
