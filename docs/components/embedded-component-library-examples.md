# Embedded component library examples

- **Created:** 2026-10-31
- **Source:** The existing page-owned components on `TestPage01`–`TestPage06`
  and `Welcome`
- **Purpose:** Published English examples for testing the reusable-component
  store; these are copies, not replacements or page references.

All 24 source components were copied into the reusable-component store and
published as revision 1 in locale `en`. The original page-owned components
remain unchanged. The backend has no list-components endpoint, so load an entry
in the Content Studio by its UUID, or retrieve it with
`GET /api/reusable-components/{componentId}?locale=en`.

The duplicate pages `TestPage07`–`TestPage12` are published in locale `en`.
Each uses the same component UUIDs and ordering as its corresponding source
page, through page-content `componentReference` nodes; their legacy page
component lists are empty.

| Duplicate page | Source page | Public page UUID | Component references |
|---|---|---|---:|
| `TestPage07` | `TestPage01` | `2da326d4-14eb-47b6-92c1-a7f26f8e1837` | 6 |
| `TestPage08` | `TestPage02` | `e93dfbd4-1253-4ca6-9f51-9b7ce538a083` | 6 |
| `TestPage09` | `TestPage03` | `a18d2c4a-3bac-4eac-accd-2a5d358686a0` | 4 |
| `TestPage10` | `TestPage04` | `85d8ef87-1d5a-4cbb-835d-f87587158e16` | 1 |
| `TestPage11` | `TestPage05` | `b6231f2b-2bcd-49f4-9a03-209b9996c72a` | 3 |
| `TestPage12` | `TestPage06` | `7892f4cc-af0c-4c5a-9edc-aee969a35323` | 3 |

The source type and properties are retained in each component's `source`
metadata. Legacy headings and text use the current `text` node; lists use a
`stack` of text nodes; the navigation bar uses a `stack` of button samples.
The six `TestPage01` buttons now have explicit `navigate` actions targeting
`TestPage07`–`TestPage12`, respectively. The `TestPage05` Home and Community
buttons target `TestPage07` and `TestPage12`. These eight reusable definitions
are at revision 2; other examples remain at revision 1. Original target slugs
remain in source metadata. The scripture sample retains the John 3:16 / ESV
reference but omits the original verse text. The media sample retains the
original `fottol.com/image/image.jpg` source as-is.

## TestPage01

| Embedded component | Reusable component UUID |
|---|---|
| `ButtonTB01` | `81c33f92-b434-4000-96fc-7abb40f46ebe` |
| `ButtonTB02` | `7810a38c-2e30-408c-8d63-16053c18fe67` |
| `ButtonTB03` | `b5732d36-8c1f-486d-97c4-66b115b66860` |
| `ButtonTB04` | `d597bea4-bf56-491e-9705-f5087dedfc13` |
| `ButtonTB05` | `f4a6b8f3-dca0-4e6c-8fef-8d845eb728cf` |
| `ButtonTB06` | `4e7e41e2-c71a-4f8c-a798-3dac79a46f7d` |

## TestPage02

| Embedded component | Reusable component UUID |
|---|---|
| `Header01` | `f13f5052-08b5-4fc4-af59-920d4d0441ce` |
| `Text01` | `d94a64eb-7608-415c-ade7-e6383d693a8a` |
| `Header02` | `54ab1c50-d748-45e2-afae-fd50b4bc197a` |
| `Text02` | `6d51ce72-bc12-4a06-a645-041a47ecd804` |
| `Header03` | `d402f244-cf58-4322-bf45-49750257c5e3` |
| `Text03` | `99a27c6e-b17c-4f96-b17d-488fa50ff102` |

## TestPage03

| Embedded component | Reusable component UUID |
|---|---|
| `Header01` | `034a18f6-69d4-4cce-9777-d2dca15e5843` |
| `UL01` | `4ab53066-25d1-4c04-87fe-b54bcc0f693d` |
| `Header02` | `63db7c2f-abe2-488d-8298-6c1c4a42a15f` |
| `OL01` | `c5c79b0c-3a27-45a2-ad92-15d2b4952f7d` |

## TestPage04

| Embedded component | Reusable component UUID |
|---|---|
| `BV01` | `4c09f9ba-8bd2-480a-8293-caae12f31c2a` |

## TestPage05

| Embedded component | Reusable component UUID |
|---|---|
| `NavBar01` | `4a924d85-859f-4a23-8fcf-3f302973f805` |
| `NavBarBtn01` | `08a015c2-5c60-4160-93b9-36852d0a4963` |
| `NavBarBtn02` | `1de31bf8-882b-43d5-a2db-0a9adf16340f` |

## TestPage06

| Embedded component | Reusable component UUID |
|---|---|
| `Header01` | `8d404fec-201f-408c-9d79-2f93ab8d5a41` |
| `Media01` | `4dcf6d27-69b8-4c52-85aa-103f43d96736` |
| `Text01` | `bc6a593f-dae2-4485-b113-6c47e338d1d6` |

## Welcome

| Embedded component | Reusable component UUID |
|---|---|
| `title` | `a195f6d4-f7c7-468f-bb4e-eba9647fe98f` |
