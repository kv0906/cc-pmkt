# Consistency Checklist (Manual Review)

Before merging changes to skills or docs, verify:

1. **Paths**: All folder references match `_core/config.yaml` paths section
2. **Commands**: Every `/command` mentioned in docs has a matching `.claude/skills/*/SKILL.md`; `handbook/HELP_GUIDE.md` stays in sync with skill inventory
3. **Branding**: All files use "PM-Kit" (not MindLoom)
4. **QMD references**: `/ask` docs and `handbook/QMD_INTEGRATION.md` stay in sync
5. **Templates**: Wikilinks in `_templates/*.md` use correct index path
