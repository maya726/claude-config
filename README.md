# Claude Code 설정 — 디자이너용 (v261118)

이전 패키지(Git 규칙)에 **윤문 규칙**을 더한 업데이트입니다. Claude가 쓰는 답변·커밋·PR 글에서
AI 티(번역투, `결론적으로`류 관용구, 연결어미 뒤 쉼표 등)를 처음부터 빼고 씁니다.

## 들어 있는 것

| 파일 | 역할 |
|---|---|
| `CLAUDE.md` | 전역 기준 — 언어, 응답 형식, 규칙 목록 |
| `rules/git.md` | 브랜치·커밋·PR·버전 규칙 (항상 적용) |
| `rules/writing.md` | 윤문 규칙 (항상 적용, 새로 추가) |
| `skills/git-workflow/` | 릴리즈·핫픽스 절차, PR 템플릿 |
| `settings.json` | 간결한 응답 스타일 |

## 설치

압축을 푼 폴더에서 실행합니다. 기존 `CLAUDE.md`·`rules/`·`skills/git-workflow`는 덮어씁니다 —
직접 고친 내용이 있으면 먼저 백업하세요.

```bash
mkdir -p ~/.claude/rules ~/.claude/skills
cp CLAUDE.md ~/.claude/CLAUDE.md
cp rules/*.md ~/.claude/rules/
cp -r skills/git-workflow ~/.claude/skills/
# settings.json 은 덮어쓰지 않고 병합 (jq 필요)
[ -f ~/.claude/settings.json ] || echo '{}' > ~/.claude/settings.json
jq -s '.[0] * .[1]' ~/.claude/settings.json settings.json > /tmp/s.json && mv /tmp/s.json ~/.claude/settings.json
```

남이 쓴 글을 따로 윤문하고 싶으면 Claude Code에서 플러그인을 설치합니다.

```
/plugin marketplace add epoko77-ai/im-not-ai
/plugin install humanize-korean@im-not-ai
```

## 확인

Claude Code를 새로 열고 `/memory`를 입력하면 `writing.md`·`git.md`가 목록에 보입니다.
