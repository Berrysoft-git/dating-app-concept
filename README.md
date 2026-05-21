# 소개팅 서비스 기획안

프리미엄 검증 기반 소개팅 서비스와 AI 정밀 분석 매칭 아이디어를 정리하는 저장소입니다.

핵심 방향은 단순 프로필 매칭이 아니라, 사람의 성향과 맥락을 AI가 분석하고 "왜 이 사람이 잘 맞는지"를 설명해주는 큐레이션형 매칭 서비스입니다.

## 문서

- [서비스 개요서](docs/service-overview.md)
- [PRD 제품 요구사항 문서](docs/prd.md)
- [MVP 범위 정의서](docs/mvp-scope.md)
- [회원 등급 정책서](docs/member-tier-policy.md)
- [셀렉트/프리미엄 검증 기준표](docs/select-premium-verification-criteria.md)
- [인증자료 수집/검증/보관 명세서](docs/verification-evidence-lifecycle-spec.md)
- [노출 정책서](docs/exposure-policy.md)
- [매칭 정책서](docs/matching-policy.md)
- [문서 작업 분담 및 크로스 체크 계획](docs/work-division-and-cross-check.md)
- [제품 콘셉트 정리](docs/product-concept.md)
- [Asteria 역기획서](docs/asteria-reverse-engineering.md)
- [협업 로그](docs/collaboration-log.md)
- [개인정보 및 인증자료 처리 정책 초안](docs/privacy-verification-data-policy.md)

## Coordination Notes

Use this short task split before the next cross-check.

### Agent A: Codex Runtime

Scope:
- `agent/transports/codex_app_server.py`
- `tests/agent/transports/test_codex_app_server_runtime.py`

Tasks:
- Document why Codex binary resolution must handle Windows npm shims such as
  `codex.CMD`, `.bat`, and `.exe`.
- Confirm `CodexAppServerClient` and `check_codex_binary()` use the same
  resolver contract.
- Keep tests portable by checking the resolved executable basename instead of
  requiring `cmd[0] == "codex"`.

### Agent B: Gateway and Discord

Scope:
- `gateway/platforms/base.py`
- `gateway/platforms/discord.py`
- Discord auto-thread and background task cancellation/drain tests.

Tasks:
- Document why fire-and-forget message tasks need a done callback that logs
  unexpected failures.
- Confirm expected cancellations are not logged as task failures.
- Confirm Discord auto-thread creation starts a typing indicator without
  changing the response routing target.
- Confirm auto-thread creation failure falls back to the original channel and
  emits a useful warning.

### Cross-Check Criteria

- `git diff --check` passes.
- `ruff check` passes on the touched Python files.
- Targeted gateway and Codex tests pass.
- No change should treat normal cancellation as an error.
- No change should route a Discord auto-thread response back to the parent
  channel after a thread was created.
