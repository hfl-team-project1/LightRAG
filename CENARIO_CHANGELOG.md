# Cenario LightRAG Fork — Changelog

This file tracks all Cenario-specific changes to LightRAG. The goal is
minimal, surgical modifications that stay close to upstream.

**Upstream:** https://github.com/HKUDS/LightRAG
**Base version:** v1.4.9.10

---

## shards-v1 (2026-02-15)

Based on upstream `v1.4.9.10`.

### Change 1: `relation_types` support in `addon_params`

**Files:** `lightrag/operate.py` (lines ~2789-2811), `lightrag/prompt.py` (lines 31, 73-77)

**What:** When `addon_params` includes a `relation_types` list, the extraction
prompt instructs the LLM to use canonical type labels (e.g., `employee_of`,
`founder_of`) instead of free-form keyword phrases.

**Why:** LightRAG's default prompt asks for "high-level keywords summarizing the
overarching nature" of relationships, producing outputs like
`"founding,leadership"` or `"competition,rivalry"`. These cannot be reliably
mapped to a domain ontology. By passing explicit relation types, the LLM outputs
structured labels that match our gold standard, directly improving Relation F1.

**Backwards compatible:** Yes. When `relation_types` is not provided, behavior
is identical to upstream (free-form keywords).

**Diff summary:**
- `operate.py`: Read `relation_types` from `addon_params`, build instruction and
  section strings, pass to prompt context (same pattern as `entity_types`)
- `prompt.py`: Replace hardcoded keyword instruction with `{relation_type_instruction}`
  template variable; add `{relation_types_section}` to user prompt alongside
  `<Entity_types>`
