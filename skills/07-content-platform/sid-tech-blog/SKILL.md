---
name: sid-tech-blog
description: Write a new blog post in Siddhesh Lakhani's tech blog voice (sidlakhani.in/blog) — first-person incident write-ups about security, Linux, and sysadmin work, with the site's exact frontmatter, tag style, and section structure. Use this whenever the user asks to write, draft, or outline a blog post for "my blog," "the blog," or sidlakhani.in, or describes a security incident / technical investigation / build they want turned into a post. Also use if the user asks to match their existing blog's tone, format a post like their other posts, or generate the YAML frontmatter for a new post on their site. Trigger even if they just paste in raw notes/logs from an incident and say "turn this into a blog post."
---

# Sid's Tech Blog Writer

Writes new posts for sidlakhani.in/blog in Sid's established voice: first-person (or "we" when a colleague was involved) technical incident write-ups — mostly security/malware analysis and Linux/sysadmin investigations — that walk from "here's what happened" to "here's exactly what we found" to a one-line takeaway.

This skill is for **drafting new posts**, not for redesigning the site. Output is a single Markdown file with the site's YAML frontmatter, ready to drop into the blog's content folder.

## Voice & style rules

Derived from the two live posts ("Anatomy of a Fake CAPTCHA Infostealer," "Hunting a Cryptominer on My VPS"). Follow these closely — they're what makes a draft sound like Sid and not like generic AI blog filler:

- **POV**: First person. "I" when Sid worked alone, "we" when a colleague/teammate was involved in the incident.
- **Opening**: Always starts mid-scene with a concrete, small, human detail — noticing sluggishness, a colleague pinging him, a weird process in `htop`. Never opens with a definition, a "In today's digital landscape..." throat-clear, or an abstract intro paragraph. First sentence should be something that actually happened.
- **Second beat**: A short 1–2 sentence framing line that states the stakes or the general pattern this incident fits into (e.g. "Most compromises don't crash your system anymore — they silently monetize it.") This usually closes out the intro, right before the first `---` section break.
- **Sentence style**: Short to medium sentences. Active voice. Plain technical language — no hedging, no filler adjectives ("truly," "incredibly," "cutting-edge"). Occasional em-dash for a beat or aside. Dry, matter-of-fact tone even when describing something alarming — no fear-mongering, no hype.
- **No sign-off, no CTA, no "thanks for reading," no bio blurb at the end.** The post just ends on the takeaway.
- **Humor/personality**: Understated. It shows through precise word choice and dry observations, not jokes.

## Structure template

Use this shape unless the user's incident clearly needs something different. Sections are separated by a horizontal rule (`---`).

1. **Title** — short, concrete, noun-phrase style. Patterns Sid uses: "Anatomy of a [Thing]," "Hunting a [Thing] on [Where]," "[Verb-ing] a [Thing]." Avoid clickbait, avoid colons-with-subtitle unless it reads naturally.
2. **Description** (one sentence, goes in frontmatter AND as the sub-headline under the title) — states what happened and what the post covers. Pattern: "How/What [X], and how we detected/analyzed/mitigated it."
3. **Intro** (before the first `---`) — the human-scale hook + the framing line. 2–4 short paragraphs max.
4. **Body sections**, each with an `## H2` heading naming the phase of the investigation, e.g.:
   - Detection / how the problem was noticed
   - Analysis (what the payload/process/log actually showed — include real commands and code blocks)
   - Mitigation / response (numbered list of concrete steps taken, bolded lead-in per step)
   - Hardening / prevention (bullet list of what was changed afterward)
   Use as many of these as the incident actually has — don't pad. The infostealer post used 4 body sections; the cryptominer post used 3. Match section count to how much there actually is to say.
5. **Code/commands**: Any command, script, or log line the user gives you goes in a fenced code block, verbatim. Don't paraphrase actual commands or file paths.
6. **Numbered steps get bold lead-ins**: `1. **Terminated the Miner:** Killed the processes immediately using \`kill -9\`.` — bold phrase names the action, rest of the sentence is the detail.
7. **Closing section** — `## Takeaway` or `## Summary & Post-Incident Clean-Up` (name it to fit the post). 1–3 sentences: state the general lesson plainly. Often ends on a bolded one-line rule of thumb, e.g. **"never paste commands from a website into your terminal."**

## Frontmatter — match exactly

Every post on the site uses this Astro YAML frontmatter block. Fill in every field; don't drop any, and don't invent a different schema.

```yaml
---
title: "<Post Title>"
slug: "<url-slug>"
description: "<same one-sentence description as the sub-headline>"
date: "<YYYY-MM-DD>"
tags: ["<tag1>", "<tag2>"]
cover: "/images/blogs/<image-file>.png"
featured: false
---
```

- `<url-slug>`: lowercase, hyphenated, 3–6 words, derived from the title (e.g. `anatomy-fake-captcha-infostealer`).
- `<image-file>`: propose a lowercase-hyphenated filename that describes the hero image (e.g. `malware.png`, `htop-server.png`); flag to the user that they still need to add the actual image at that path.
- `tags`: array of lowercase, hyphenated strings. Use existing vocabulary when it fits — `cybersecurity`, `linux`, `malware`, `malware-analysis`, `powershell`, `security`, `social-engineering`, `sysadmin` — plus new ones as needed.

## Workflow

1. **Get the raw material.** Ask the user what happened — or if they've already pasted logs, commands, screenshots, or a rough account, use that directly. You need: what tipped them off, what they found when they dug in (actual commands/output if available), what they did about it, and what they'd tell someone else to prevent it. If any of these are missing and can't be reasonably inferred, ask — don't invent technical specifics (fake IPs, fake file paths, fake command output) to fill gaps. It's fine to leave a bracketed placeholder like `[paste the actual iptables rule you used]` rather than fabricate one.
2. **Pick title, slug, tags, and which body sections apply** based on the structure template above.
3. **Draft the full post** in Markdown, frontmatter included, following the voice rules above. Keep it as tight as the two reference posts — these run roughly 500–800 words of actual prose plus code blocks, not padded 1500-word "content."
4. **Save it** as a single `.md` file (filename = the slug) to `/mnt/user-data/outputs/` and present it to the user.
5. **Call out anything you couldn't verify**: placeholder image filenames, any technical detail you weren't given and left as a bracketed placeholder, and the fact that they'll need to drop the real hero image at the path referenced in frontmatter.

Don't use the `docx` or other document-format skills for this — output is a plain `.md` file matching this static site's content format, not a Word doc.
