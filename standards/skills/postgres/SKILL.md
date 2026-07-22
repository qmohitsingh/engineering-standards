---
name: postgres
description: PostgreSQL schema & naming standard. Use whenever designing or reviewing a Postgres database schema — naming tables, columns, primary/foreign keys, constraints, or indexes; choosing column types; or writing DDL / migrations.
---

# PostgreSQL naming & schema standard

Apply these rules when designing, reviewing, or generating any PostgreSQL schema, DDL, or migration. Based on Tom Christ's [SQL naming conventions](https://launchbylunch.com/posts/2014/Feb/16/sql-naming-conventions/) plus agreed additions. Keep the rules product-agnostic.

## Identifiers
- `lowercase`, `snake_case`; multi-word separated by underscores.
- No quoted identifiers — if you'd have to quote it, rename it.
- No whitespace. Full English words; avoid abbreviations (standard acronyms like `url`, `id`, `i18n` are fine).
- Max 63 chars (Postgres limit).
- Avoid reserved words (`user`, `order`, `table`, `lock`, `end`, …) — rename (e.g. a users table becomes `account`).

## Tables (relations)
- **Singular** names (`person`, `team`, `order_item`).
- No `tbl_`/type prefixes — group with **schemas**, not name prefixes.
- Link tables named for the concept when one exists (`membership`), otherwise both singulars (`person_team`).

## Columns
- Singular `snake_case`.
- Order: `id` → foreign keys → attributes → flags → timestamps.
- Prefer `text` over `varchar(n)`; add a `CHECK` if a bound is genuinely needed. Use `citext` for case-insensitive uniqueness.
- No data-type prefixes/suffixes (`_tx`, `_dt`).

## Keys
- **Primary key:** a single column named `id` (no table prefix). Default type `bigint GENERATED ALWAYS AS IDENTITY`. Use `uuid` only when you need distributed or client-offline id generation.
- **Foreign key:** `<referenced_table>_id` (`person_id`, `team_id`).
- **Role-qualified FKs** when two or more FKs reference the same table → `manager_id`, `report_id` (comment which table each targets). *This intentionally extends the base convention.*
- Pure junction tables may use a composite PK of their two FKs.

## Booleans
- `is_` / `has_` prefix, `NOT NULL`, with a default → `is_active boolean not null default true`. Never nullable booleans.

## Enums / status
- Lowercase `snake_case` values; columns named `*_type` / `*_status` / `*_kind`.
- Represent as a native `enum` or `text` + `CHECK`; promote to a lookup table only if it needs metadata or frequent change.

## Timestamps
- Always `timestamptz` (store UTC), never `timestamp`.
- Every table gets `created_at` and `updated_at`, both `not null default now()`, plus a trigger to maintain `updated_at`.
- Optional per table: `deleted_at timestamptz null` for soft/recoverable deletes.

## Constraints & indexes — always explicit names
| kind | pattern | example |
|---|---|---|
| primary key | `<table>_pk` | `person_pk` |
| unique | `<table>_uq_<cols>` | `person_uq_email` |
| foreign key | `<table>_fk_<col>` | `order_item_fk_order_id` |
| check | `<table>_ck_<desc>` | `person_ck_email_format` |
| index | `<table>_ix_<cols>` | `person_ix_last_name` |

*Deviations from the source article, on purpose: role-qualified FKs, and the constraint-naming scheme (the article specified only indexes).*
