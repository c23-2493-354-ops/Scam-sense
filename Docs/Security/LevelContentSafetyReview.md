# Level Content Safety Review

**Project:** Scam Sense: Think Before You Click
**Version reviewed:** 0.1.0 (main branch, tag `v0.1.0`)
**Reviewer:** Chester Guile P. Castillo, Group G17
**Date:** 14 Nov 2026
**Related OWASP Top 10:2025 category:** A06 Insecure Design

## Why this check matters

Scam Sense shows players fake scam messages on purpose. If any link, email
address or phone number in those messages were real, a player could copy it
and contact a real person or company, or open a real (possibly malicious)
website. Every contact detail in the game content must be fictional.

## Scope

All five level data files in `Assets/Levels/`:

| File | Lines with links, emails or phone numbers |
|---|---|
| `Level1_TheFirstCase.asset` | 8 |
| `Level2_TheInvestigation.asset` | 13 |
| `Level3_TheDigitalTrap.asset` | 8 |
| `Level4_DigitalForensicsLab.asset` | 13 |
| `Level5_MacLab.asset` | 18 |

## Results

| Content checked | Found (unique) | Rule | Result |
|---|---|---|---|
| Web links | 7 | Must use the reserved `.example` or `.invalid` domains | Pass: all 7 do |
| Email addresses | 30 | Must use the reserved `.example` domain | Pass: all 30 do |
| Phone numbers | 7 | Must use the fictional `555-01xx` range | Pass: all 7 do |
| Bank account numbers | 1 (`Level5_MacLab.asset`) | Must be clearly fictional | Flagged for review |

The reserved domains `.example` and `.invalid` (RFC 2606 / RFC 6761) can never
be registered, so these links and addresses can never reach a real site or
inbox. Look-alike names such as `upshl-binan.example` are intentional red flags
for the player to spot and are still safe.

## Follow-up

1. Confirm the account number in Level 5 is made up, or change it to an
   obviously fake pattern (for example `0000-0000-0000`).
2. Repeat this check whenever a new scam case is added or level text changes.

## How to repeat the check

Run from the repository root (Git Bash):

```bash
L=Assets/Levels/Level*.asset
grep -ohE 'https?://[^ "\\<]+' $L | sort -u          # links
grep -ohE '[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}' $L | sort -u   # emails
grep -ohE '\+?[0-9][0-9 ()-]{8,}[0-9]' $L | sort -u  # phone / account numbers
```

Every link and email should end in `.example` or `.invalid`, and every phone
number should be in the `555-01xx` range.
