# SLA, Pendency & Priority (C3)

## Purpose
Show how long each case has been in its stage, whether it is late, and which cases deserve attention first, with a transparent explanation.

## Definitions
| Term | Definition |
|---|---|
| `due_at` | `entered_at + sla_days` (calendar days by default; working-day mode is configurable) |
| Pendency / ageing | `now − entered_at` for the open stage; also total case age |
| SLA ratio | `days_in_stage / sla_days` |
| Breach | `now > due_at` |
| Near-breach | `days_remaining <= near_breach_days` |

SLA durations are **placeholders** until verified; they are shown as such in the UI.

## Priority score
```
priority = w_sla * min(sla_ratio, cap)
         + w_find * (open_major_findings / max_findings)
         + w_wait * (institution_waiting_days / max_wait)
```
The API returns the total **and** each term so the UI can show "why this priority".

## Scan
The daily/hourly job queries open stage rows, computes ratios and creates `SLA_NEAR` / `SLA_BREACH` alerts (idempotent). Breach must appear within one scheduler cycle.

## Reporting views
Funnel (cases per stage), ageing buckets (0–7, 8–15, 16–30, 30+ days), breach counts by region / stage / officer → `/analytics/funnel`, `/analytics/ageing`.

## Edge cases
- Cases waiting on the **institution** (deficiency response) should not count against officer SLA; model with a `waiting_on` field (officer vs institution).
- Config change affects only new stage entries.
- Timezones: compute in UTC; display IST.

## Tests
- SLA math for boundary values (exactly at due, one second after).
- Priority breakdown sums to the total.
- Breach alert appears once, not repeatedly (dedupe).
