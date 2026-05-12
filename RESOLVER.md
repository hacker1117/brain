# Brain Resolver — Decision Tree for Filing Pages

Walk this tree before creating any new page. The answer is always unambiguous.

## Step 1: Is it a person?
→ YES → `people/` (see people/README.md)
→ NO → Step 2

## Step 2: Is it a company, organization, or institution?
→ YES → `companies/` (see companies/README.md)
→ NO → Step 3

## Step 3: Is it a place?
→ YES → `places/` (see places/README.md)
→ NO → Step 4

## Step 4: Is it a concept, theory, or framework?
→ YES → `concepts/` (see concepts/README.md)
→ NO → Step 5

## Step 5: Is it a project or active work?
→ YES → `projects/` (see projects/README.md)
→ NO → Step 6

## Step 6: Is it a raw idea, not yet synthesized?
→ YES → `ideas/` (see ideas/README.md)
→ NO → Step 7

## Step 7: Everything else
→ `inbox/` — this is a signal the schema needs to evolve

---

When two directories both seem to apply, use the primary home. Add cross-links to other relevant directories.
Never create duplicate pages for the same entity.
