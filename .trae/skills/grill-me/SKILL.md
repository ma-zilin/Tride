---
name: grill-me
description: "Use only when the user explicitly invokes grill-me or requests this interview skill. Not for ordinary questions, implementation, or routine stage checks."
metadata:
  invocation: explicit-only
---

Call the available Skill tool with "grilling". If this TRAE session has no compatible Skill tool, read `.trae/skills/grilling/SKILL.md` in full and follow it in the current conversation. Report a missing dependency; do not pretend it loaded.
