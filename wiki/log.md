# Operations Log

Append-only. Never edit past entries. One entry per ingestion or structural change.

Format:
```
## YYYY-MM-DD — <action type>
- **Source**: raw/file.md or external
- **Action**: ingested | created | updated | merged | generated-output
- **Affected**: wiki/entry.md (or list)
- **Notes**: brief description of what changed and why
```

---
