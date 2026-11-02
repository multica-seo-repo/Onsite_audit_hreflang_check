# Report and findings contract

Filename: `15.1-15.3_{name}_onsite_hreflang_audit_{date}.xlsx`. Prefix the covered checklist item range; all three rows are included, so N/A, Human check and unavailable evidence do not shorten it. Map name to findings.site_name and date to findings.audit_date (YYYY-MM-DD). Sanitize invalid filename characters and reject excessively long names. Use a unique writable run directory; never overwrite prior reports.

## Worksheets

- Always create `Checklist`: `Item No. | Item Name | Check Result | Findings | Instruction`.
- Create `15. HREFLANG` only when at least one check is X: `Address | Issue | Instruction`.
- There is **no Coverage column**. Archive source details, counts, scope and timestamps in evidence/manifest instead. Keep material limitations in Findings, so an incomplete √ cannot be mistaken for verified success.
- Include 15.1–15.3 exactly once, in order. Results are √ / X / N/A / Human check; never Yes/No or Y/N. N/A and Human check retain their previously agreed meanings.
- Every X requires one or more detail issues; other results have none. Prefix detail Issue with the item number. Store customer strings as literal text, never formulas. No screenshots column or extra worksheets.

## Findings versus Instruction

**Findings = what was found. Instruction = the next thing a person needs to do.** Use English for report narratives, short sentences and familiar words. Do not put next steps in Findings or repeat the whole finding in Instruction.

- Findings: one short conclusion; include a brief missing-data reminder when needed. Issue: one specific defect, plus the target URL if known.
- Instruction: usually one short, concrete action. Begin with a verb: “Provide…”, “Add…”, “Correct…”. A pass with nothing to do uses “No action needed.” N/A also uses this when no follow-up is needed.
- Human check and any unverified 15.3 √ must give a real next step. X uses a summary action in Checklist and the specific fix on each detail row.
- Keep essential URL/count/limitations; avoid jargon, background paragraphs, broad “optimize hreflang” advice and long chains of actions. Do not invent causes or claim a homepage 429 proves switching is broken.
- Parameter URL advice stays gentle and brief: “Consider language subdirectories during a redesign and assess migration effects.”

| Case | Findings | Instruction |
| --- | --- | --- |
| Confirmed working versions | Chinese and English versions switch correctly. | No action needed. |
| Missing return link | The English page lacks a return hreflang link to the Chinese page. | Add a hreflang link to the corresponding Chinese page. |
| Missing SF results | The six SF results are unavailable; provisionally marked √ under the initial-review convention, with verification incomplete. | Provide the six SF exports. |
| Homepage HTTP 429 | The homepage request was rate-limited; language switching was not checked. | Provide saved homepage HTML or SF results. |
| Parameter URLs | Language versions use the ?lang=en parameter. | Consider language subdirectories during a redesign and assess migration effects. |
| Missing >100 distinct URLs (Human check) | Found 126 URLs missing hreflang; review is required. | Check for excessive parameter pages or Screaming Frog crawl anomalies. |

## Findings input

The agent reviews evidence and writes JSON. The generator validates and formats it; it does not crawl or independently judge SEO.

```json
{
  "site_name": "Example",
  "site_url": "https://example.com/",
  "audit_date": "2026-10-12",
  "checks": [
    {
      "id": "15.1",
      "item_name": "Multi-language / country?",
      "result": "√",
      "finding": "Chinese and English versions switch correctly.",
      "instruction": "No action needed.",
      "evidence_state": "available",
      "evidence": ["raw/homepage-observation.json"],
      "issues": []
    }
  ]
}
```

The fragment shows one row; real input requires all three. Every finding/instruction must be nonempty. evidence_state is `available`, `partial`, `unavailable` or `not_applicable`. evidence is a list of actual archived references, which may be empty for unavailable or skipped checks.

For large-Missing 15.3 Human check, use `issues: []` and an evidence_state matching export availability, not `not_applicable`. Retain the actual distinct Missing count and review reason in Findings; save the complete export/count summary in evidence. The agent applies the >100 rule from audit-rules.md; the generator formats reviewed results and does not count SF rows. If other filters establish X, include only confirmed defects in issues and keep large unreviewed Missing rows in raw evidence.

X issue object: `{"address":"https://example.com/","issue":"Language versions use the ?lang=en parameter.","instruction":"Consider language subdirectories during a redesign and assess migration effects."}`. Use real source addresses; retain known target URLs/codes in Issue.

For 15.3 √ with partial/unavailable evidence, require nonempty `missing_evidence_note` describing what is missing and that verification is incomplete. The generator appends this fact to Findings; put the user's action in `instruction`. Do not add a second mixed-language notice. Other passes require available evidence. N/A requires not_applicable; skipping 15.3 is an explicit user instruction, not automatically inherited from 15.1.

**Migration:** older findings.json must add `instruction` to every check and separate any existing next steps from `finding`. `coverage` is no longer required or exported; legacy extra fields are ignored. Do not move old coverage paragraphs into Instruction. Keep full provenance in the run archive.

Archive findings.json, manifest.json, complete raw exports and final workbook under ignored runs/. The validator checks types, IDs, statuses, issue inclusion and missing-evidence disclosure, not evidence truth or wording quality. The generator reopens the workbook to check text, sheet names and row counts. Inspect layout, preserve complete raw evidence and avoid silently truncating long text.
