---
description: "Implement a Discover Jesus story using its acceptance criteria and the owning repository"
name: "Work on a story"
argument-hint: "Story ID or path, for example STORY-004"
agent: "agent"
---

Work on the Discover Jesus story identified in the user's request or prompt
arguments. If no story can be identified unambiguously, ask which story to use.

Follow the repository's [story implementation workflow](../../ai/developer-guide.md#story-implementation-workflow).
Read the story, parent epic, dependencies, relevant architecture decision
records, and shared contracts before editing. Implement code and run tests in
the repository that owns the implementation. Preserve unrelated worktree
changes. Update shared contracts or documentation only when the change requires
it, and report exactly what was validated. Do not deploy or mark the story
complete without authorization and verified acceptance criteria.
