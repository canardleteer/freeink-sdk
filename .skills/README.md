# FreeInk SDK: project skills

On-demand Agent Skills for this repository. Compatible agents load one when the
task matches its `description`; you do not invoke them by hand.

These are written for capable agents, not beginners. They are principle- and
decision-focused on purpose. They deliberately avoid line-number citations,
which drift; they anchor on durable names (APIs, types, macros, files).

This is separate from `AGENTS.md`, the always-loaded repository guide. Skills
are the applied decision procedures that load on demand and add the judgment
layer `AGENTS.md` does not carry.

Each skill is a directory containing `SKILL.md` whose `name` matches the directory.

| Skill | Loads when you are... |
|---|---|
| `platform-targets` | asking what is true on a board/env, or adding / correcting / removing a device, or changing that skill's schema |

Each skill ends with a self-review checklist the agent runs against its own
diff before handing it back.

## Maintaining these

Edit the `SKILL.md` under each directory. Keep them tight. Do not restate
`AGENTS.md`; add the judgment that file cannot afford to carry. Trigger quality
lives in the `description` field: it must name the situations that should pull
the skill in, in the words a contributor's task would use.
