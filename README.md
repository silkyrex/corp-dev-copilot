# Corp Dev Copilot

A Claude Code skill for reading **investment term sheets** and **merger agreements** through a corporate development lens — not a legal lens.

Built for corp dev professionals who don't own the legal craft but own the **business terms**. Use it to move faster, sound sharper, and never miss a pro-rata clause again.

---

## Why this exists

If you're in corp dev, most of your week is reading documents someone else drafted — term sheets from investors, merger agreements from counterparty's counsel, redlined markups coming back four versions deep. Legal owns the doc. **You own the business items.**

The problem: every doc is 40+ pages of dense boilerplate wrapping a dozen business terms that actually matter. You scan for the same things every time — pro-rata, info rights, marketing rights, earnout mechanics, closing conditions — but it's easy to miss something when the counterparty buried it, or to spend an hour hunting for something that isn't in the doc at all.

This skill automates the first pass. It gives you:

1. **A structured pull of every business term you care about** — with the actual quoted language, a plain-English translation, and a status (standard / non-standard / missing / needs attention).
2. **Redline flags** if the doc has track changes — what moved, which direction it favors, and what you should think about it.
3. **A prioritized issues list** — the 3-7 things that actually need your attention, with suggested pushback language ready to drop into an email.

It does **not** pretend to be a lawyer. If something is reps & warranties, indemnity mechanics, or governing law, it tags it `[legal-owned, FYI]` and moves on. Your lawyer is still your lawyer.

---

## What you get after one run

You point it at a term sheet PDF. Thirty seconds later you have a markdown file that looks like this:

```markdown
# Acme Series B — term sheet review
Date: 2026-04-17 | Source: ~/Downloads/acme-series-b-ts.pdf

## 1. Key Terms

### Pro-rata rights
- Language: "Major Investors shall have the right to participate pro rata in
  subsequent financings..." (§4.2)
- Plain English: You keep your ownership percentage in future rounds — but only if
  you qualify as a Major Investor (≥$2M in this round).
- Status: standard

### Marketing rights / logo usage
- Language: NOT FOUND
- Plain English: The doc is silent on consent rights for use of your firm's logo
  or name.
- Status: missing

### Purchase price / valuation
- Language: "$40M pre-money, $50M post-money, with a 10% post-money option pool."
- Plain English: The option pool is being taken from the existing shareholders
  before the round, not from the new money. This reduces effective pre-money
  to ~$36M.
- Status: needs attention

...

## 3. Issues & Pushback

1. **Option pool shuffle reduces effective pre-money by ~$4M.**
   Suggested pushback: "Given the 10% post-money pool, we'd propose moving to a
   pre-money pool or adjusting pre-money to $44M to hold economics."

2. **No marketing rights / logo clause.**
   Suggested pushback: "We'd like to add a clause requiring written consent
   before any use of [Firm]'s name or logo in marketing materials."

3. [legal-owned, FYI] Investor rights agreement references unusual 5-year
   survival on info rights — flag to legal.
```

You skim it in two minutes. You're ready for the call.

---

## How to use it — a strategy guide

### Step 1: Install Claude Code

Claude Code is Anthropic's official CLI and desktop app. Download it: https://claude.com/claude-code

You need a Claude subscription (Pro or Max). The skill itself is free — the cost is the Claude account.

### Step 2: Install this skill

```bash
# Clone this repo
git clone https://github.com/silkyrex/corp-dev-copilot.git

# Copy the skill into your Claude skills directory
mkdir -p ~/.claude/skills/read-deal-doc
cp corp-dev-copilot/SKILL.md ~/.claude/skills/read-deal-doc/
```

Restart Claude Code. The skill is now available.

### Step 3: Run it on a deal doc

In any Claude Code session:

```
read this term sheet at ~/Downloads/acme-series-b-ts.pdf
```

Or explicitly:

```
/read-deal-doc ~/Downloads/acme-series-b-ts.pdf
```

Claude will extract the text, walk the checklist, and write a markdown file to `./deals/YYYY-MM-DD-<deal-slug>.md`. You can override the output path by saying so ("write the output to `~/work/acme/review.md`").

### Step 4: Read the output like this

Don't read the output top-to-bottom. Read it in this order:

1. **Section 3 first — Issues & Pushback.** This is what you act on. If nothing here surprises you, the rest is a sanity check.
2. **Section 1 — any row with status `missing` or `non-standard`.** These are the terms you should have thoughts about before your next touchpoint.
3. **Section 2 — Redline Flags.** Only if the doc was redlined. Tells you where the counterparty is pushing.
4. **Everything else.** Skim to confirm no surprises.

### Step 5: Use the pushback language — but rewrite it

The suggested pushback in Section 3 is a **starting draft**, not a finished email. It's phrased to be technically correct but tonally neutral. Rewrite it in your voice, with your relationship context, before sending.

### Step 6: Keep a deal folder

The default output path is `./deals/YYYY-MM-DD-<deal-slug>.md`. Keep running the skill on each new draft — you'll end up with a dated trail of reviews that makes it easy to see what the counterparty changed between v3 and v4, and to brief a new team member on the deal history in one read.

---

## What this skill is good at

- **First-pass triage.** Turning 40 pages into a 2-page "here's what matters" brief.
- **Consistency.** The same checklist runs every time. You'll never forget to look for marketing rights again.
- **Verifiability.** The skill is forced to emit a row for every checklist item, even if the term isn't in the doc. "Silence on pro-rata" is a row, not an omission.
- **Surfacing asymmetry.** Non-standard terms and missing terms are the two most common ways counterparty screws you quietly. This is explicitly designed to catch both.

## What this skill is not good at

- **Replacing legal review.** It tags legal-owned items and moves on. You still need your lawyer on reps, indemnity, survival, governing law, and anything structural.
- **Reading Excel models.** It's scoped to deal docs only. Financial models are a different skill (and frankly a different discipline).
- **Heavy redline comparison.** If the doc has hundreds of track changes across a 60-page merger agreement, the skill will miss things. Use a real diff tool for serious redline work.
- **Novel terms.** The "standard vs. non-standard" judgment is based on what Claude has seen in training. For exotic structures (SPVs, sidecar funds, unusual earnout geometries) it'll hedge. Don't trust the status field blindly on weird deals.

## Verification discipline

Because the skill forces a row for every checklist item, you can quickly verify it did its job:

- **Did every checklist item get a row?** If any are missing from the output, rerun.
- **Did it quote the actual language?** If a row has "Plain English" but no "Language" field, the skill guessed. Rerun or check manually.
- **Does the Status field make sense?** If a term you know is unusual is marked "standard," push back on the skill — sometimes it defaults to standard when it hasn't seen enough examples.

---

## The meta-lesson for anyone job-hunting in corp dev

The reason this skill exists is because **the corp dev role is compressing.** A lot of what a junior corp dev person did five years ago — first-pass doc review, checklist extraction, summary memos — is work an LLM can now do in thirty seconds with the right scaffolding.

This is not a threat. It's a tell.

The people who will thrive in corp dev over the next five years are the ones who:

1. **Automate their own first-pass work.** Build skills like this one for every repeatable review task you do. The goal is not to work faster — it's to free up the hours you used to spend on triage for the work that actually compounds (building relationships, shaping strategy, closing deals).
2. **Sharpen their judgment.** The output of this skill is a draft. The value you add is deciding what to fight on, what to let go, and how to sequence pushback. That's a skill you build by doing, not by reading — but now you can do it ten times as often because the triage is automated.
3. **Ship their playbooks.** Every corp dev person has a personal "here's what I always look for" list. Before, it lived in your head. Now, with skills like this, you can make it explicit, version it, improve it, and share it. A documented playbook is a career asset. A vibe is not.

If you're between roles or watching your seat consolidate: build three or four of these skills for the work you used to do manually. Use them in interviews. Bring them to your next job on day one. It's the clearest signal you can give that you're not the person being automated away — you're the person doing the automating.

---

## Customizing the skill

The checklist in `SKILL.md` is opinionated. It reflects what **one** corp dev person cares about. Yours will be different.

Edit the file directly. Add terms you care about, remove terms you don't. Common customizations:

- **If you do a lot of secondaries:** add ROFR, co-sale, tag-along mechanics to the term sheet checklist.
- **If you do a lot of minority growth:** add board observer rights, consent rights, and protective provisions.
- **If you do a lot of M&A:** add termination fees, go-shop/no-shop, interim operating covenants, and financing-out conditions.
- **If you're buy-side on LBOs:** add debt commitment letter terms, equity commitment letters, and reverse termination triggers.

The skill is a template, not a prescription. Make it yours.

---

## Questions, feedback, improvements

Open an issue or a PR. If you build a variant of this skill for a different domain (private credit, real estate, venture debt), share it back.

## License

MIT. Use it, fork it, sell the output, build a consultancy around it. No attribution required.
