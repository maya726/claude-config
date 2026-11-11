# Claude Config

Claude Code 전역 설정과 skill.

## 연결

컴퓨터마다 한 번.

```bash
cd ~/.claude
git clone https://github.com/maya726/claude-config.git
ln -sf claude-config/CLAUDE.md CLAUDE.md
# 이미 skills 폴더가 있으면 ln 이 그 안에 링크를 만든다. 먼저 치운다. synced/ 는 Claude Code 가 다시 받는다
[ -d skills ] && [ ! -L skills ] && mv skills skills.old-$(date +%y%m%d)
ln -sfn claude-config/skills skills
ls skills/git-workflow/SKILL.md   # 보이면 연결된 것
```

## 최신 내용 받기

```bash
cd ~/.claude/claude-config
git pull
```

링크는 다시 걸지 않는다. Claude Code 의 새 대화부터 적용된다.

## Plugins

### Figma
Figma 파일을 읽어 화면 코드로 옮길 때 쓴다.

```
/plugin install figma@claude-plugins-official
```
