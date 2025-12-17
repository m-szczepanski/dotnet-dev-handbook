# Design Template

As a Chapter we refer to a folder that holds subchapters - pages inside that chapter.

This template **must be used for every subchapter** in this repository to ensure consistency, readability, and long-term maintainability.

---

## Subchapter File Structure

Each subchapter file should follow this exact structure:

```md
# <Subchapter Number>. <Subchapter Title>

## 1. What problem does this solve?

## 2. Core concept (simple explanation)

## 3. How it works (under the hood)

## 4. Practical examples

## 5. Common mistakes & pitfalls

## 6. Best practices & recommendations

## 7. When NOT to use this

## 8. Summary
```

---

## Writing Rules (MANDATORY)

- Use **plain, simple English**
- Assume the reader is **smart but new to the topic**
- Avoid academic or marketing language
- Prefer **short paragraphs** (1–4 lines)
- Each section starts with Heading level 2, no additional horizontal rules
- Follow good markdown formatting
- Always explain **why**, not just **what**
- Examples must be **realistic**, not contrived

---

## Section Guidelines

### 1. What problem does this solve?

- Start with a **real-world pain point**
- Avoid technical terms unless necessary

### 2. Core concept

- One main idea only
- Use analogies if helpful

### 3. How it works

- Explain only what matters for daily work
- Skip internal implementation details unless relevant

### 4. Practical examples

- Always include **code examples**
- Explain the code below the snippet

### 5. Common mistakes & pitfalls

- List **real mistakes developers make**
- Explain consequences

### 6. Best practices

- Opinionated, but justified
- Use bullet points

### 7. When NOT to use this

- Explicitly call out misuse scenarios

### 8. Summary

- Bullet points
- Maximum 6 bullets

---

# LM Studio – Global Prompt Configuration

This file defines **constant rules** that must be applied when generating content using LM Studio (openai/gpt-oss-20b).

---

## Repository Naming Conventions

### Chapter folders

```
01-Fundamentals
02-CSharp
03-DotNet-Runtime
04-AspNet-Core
05-Data-Access
06-Testing
07-Architecture
08-Tooling
09-Performance
10-Security
11-Deployment
12-Common-Pitfalls
13-Cheat-Sheets
```

Rules:

- Always two-digit prefix
- PascalCase words
- Hyphen-separated

---

### Chapter file naming

```
01-what-is-dotnet.md
02-dotnet-sdk-vs-runtime.md
03-project-structure.md
```

Rules:

- Lowercase
- Hyphen-separated
- Numeric prefix for ordering
- File name must describe **one concept only**

---

## LM Studio Prompt (USE AS SYSTEM PROMPT)

```
You are writing content for a repository called "dotnet-dev-handbook".

Your role:
- Senior .NET developer
- Mentor mindset
- Explains concepts clearly and practically

Writing rules:
- Simple, direct language
- No fluff, no marketing tone
- Explain WHY something exists
- Prefer examples over theory
- Be opinionated when necessary and explain why

Audience:
- Beginner to mid-level .NET developers
- Developers switching from other ecosystems

Formatting rules:
- Use Markdown
- Short paragraphs
- Clear section headers
- Code examples must be explained

Strictly follow the provided chapter template.
Do NOT invent features or APIs.
Prefer modern .NET (6+, preferably 10).
```

---

## Consistency Check (Before committing)

Before committing a chapter, verify:

- [ ] Folder name matches convention
- [ ] File name matches convention
- [ ] Chapter template is followed exactly
- [ ] Examples compile on modern .NET
- [ ] No unexplained jargon

---

_End of template & prompt configuration._
