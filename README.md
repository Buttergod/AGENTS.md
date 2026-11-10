# AGENTS.md

A practical, opinionated playbook for building high-quality, production-ready websites and web products using AI-assisted development.

This repository captures a strict set of engineering and design standards that help ensure the output of an AI coding assistant is not just functional, but also polished, accessible, secure, scalable, and aligned with real business goals.

---

## Why this repository exists

Modern AI-generated code can be fast, but it often defaults to generic patterns:

- repetitive UI layouts
- low-contrast design choices
- weak accessibility practices
- incomplete edge-case handling
- shallow security assumptions
- marketing copy that feels template-like instead of product-specific

This file is designed to counter those behaviors. It acts as a quality gate and delivery standard for website implementation.

The goal is simple:

> Build websites that are not just visually acceptable, but genuinely excellent, resilient, and production-ready.

---

## What is included

This repository documents a complete engineering ethos across five major areas:

1. User Experience (UX)
2. User Interface (UI)
3. Security
4. Product/Marketing Functionality
5. Code and implementation standards

It includes strategic guidance for:

- diagnosing UX maturity
- building structured UX strategy
- aligning UX metrics with business outcomes
- creating better product design systems
- avoiding generic AI design slop
- hardening apps for production security
- improving conversion, customer trust, and performance
- integrating open-source UX components such as DitherVeil

---

## Core principles

### 1. Build like a senior web developer

The instructions emphasize engineering standards that would be expected from a world-class senior web developer:

- clean and maintainable code
- robust logic and edge-case coverage
- modern accessibility standards
- scalable architecture
- performance-aware implementation
- security-first thinking

### 2. Quality is not cosmetic only

The repository rejects the idea that design quality is just about decoration. It treats UX and UI as functional business systems that affect:

- conversion
- retention
- usability
- brand trust
- product clarity
- long-term maintainability

### 3. Security is a default requirement

The guidelines explicitly require:

- HTTPS enforcement and TLS standards
- secure headers and server hardening
- proper input validation
- safe authentication and session handling
- secure secret management
- data protection and access control
- dependency and infrastructure auditing

### 4. AI should be guided, not left to default patterns

This repo helps prevent AI from producing generic or shallow work by enforcing:

- specific design constraints
- named aesthetic directions
- explicit content briefs
- accessibility checklists
- measurable product outcomes

---

## Repository structure

```text
AGENTS.md/
├── AGENTS.md   # Core project instructions and standards
├── README.md   # Repository overview and usage guidance
└── .gitignore  # optional repo metadata (if present in future)
```

At the moment, the main source of content is the `AGENTS.md` file, which acts as a blueprint for AI-driven website generation and review.

---

## What the AGENTS instructions cover

### UX maturity and strategy

The guidance explains how organizations evolve in UX maturity and how to move from a low-maturity state to a structured, measurable UX practice.

It includes:

- six stages of UX maturity
- diagnosis of status quo failings
- strategic planning across diagnosis, policy, and action
- business-to-UX KPI alignment
- stakeholder persuasion and design buy-in

### UI and design quality

The UI section is especially detailed. It calls out common patterns of generic AI-generated design, including:

- gradient-heavy default visuals
- repetitive card arrangements
- weak typography decisions
- overuse of stock imagery and fake dashboards
- low-contrast dark themes
- shallow motion and visual noise

It teaches how to design intentionally with:

- named visual directions
- constrained color palettes
- stronger typography hierarchy
- spacing systems
- edge-case UI states
- accessibility compliance

### Security and production hardening

The security section covers practical production safeguards, including:

- sitewide HTTPS
- HSTS and mixed-content elimination
- CSP and anti-clickjacking controls
- secure cookie configuration
- password hashing (Argon2id/Scrypt)
- server-side validation
- MFA and access controls
- dependency scanning and infrastructure protection

### Marketing and conversion considerations

The repository also includes product and growth guidance, such as:

- mapping user journeys
- reducing friction in checkout and onboarding
- using social proof and CTAs intelligently
- improving site search and customer engagement
- improving trust and legal clarity
- optimizing performance and SEO foundations

### Code and component integration

It includes a concrete example: integrating the React Bits `DitherVeil` component using `ogl`.

This is a good example of the repository's intent: it doesn't just talk at a high level; it includes implementation specifics and production-quality UI code patterns.

---

## Example of the included component guidance

The repo includes a `DitherVeil` usage example and configuration breakdown:

```jsx
import DitherVeil from './DitherVeil';

<div style={{ width: '100%', height: '600px', position: 'relative' }}>
  <DitherVeil
    src="https://images.unsplash.com/photo-1737071371043-761e02b1ef95?q=80&w=1400&auto=format&fit=crop"
    pattern="floyd"
    pixelSize={2}
    inkColor="#120f17"
    paperColor="#f4f1ea"
    revealRadius={200}
    softness={0.6}
    linger={1}
  />
</div>
```

This demonstrates the repository's emphasis on:

- visual motion and polish
- modern interactive experiences
- thoughtful component APIs
- production-grade front-end implementation

---

## How to use this repository

This repository is best used as a project rules file for AI coding agents, especially when generating or refining a website.

### Recommended approach

1. Keep `AGENTS.md` at the root of the project.
2. Make sure AI tools or coding assistants can access it.
3. Use it as a baseline for design, UX, security, and implementation reviews.
4. Re-check generated code against the standards in this document.

### Example use case

If you are asking an AI assistant to build a landing page, this file helps ensure the output includes:

- strong information hierarchy
- a clear CTA strategy
- accessible navigation
- responsive layout
- keyboard-friendly controls
- secure implementation details
- refined visual styling rather than generic template output

---

## Quality checklist for new projects

Before shipping a website built from this guidance, verify the following:

- The landing page has a clear primary action and hierarchy.
- The layout is not generic or copy-paste-like.
- Typography and spacing create intentional visual rhythm.
- Colors meet contrast requirements.
- Keyboard navigation works across the app.
- All major UI states are considered: empty, loading, validation, error, permission denied.
- Forms are validated on the server as well as the client.
- Traffic is served over HTTPS only.
- Security headers are implemented.
- Performance is measured via real-world metrics.
- Accessibility requirements meet WCAG-level expectations.

---

## Best practices derived from the file

Some of the most valuable recommendations in this repo are:

- design with real product context, not generic SaaS clichés
- align design choices to real user journeys and business goals
- reserve time for quality and design debt reduction
- audit user flows instead of only visual mockups
- test with real users early and often
- make accessibility a design requirement, not an afterthought
- secure the app by default, not as a late-stage patch

---

## Summary

This repository is a compact but high-value blueprint for building better digital experiences.

It is not a loose style guide. It is a quality framework that helps produce sites and apps that are:

- intentional
- user-centric
- technically sound
- secure
- accessible
- conversion-aware
- visually differentiated
- production-ready

If you are using AI to generate or refine a website, this repo provides the operational guardrails needed to turn mediocre output into excellent product work.

---

## License

This repository is a project instruction and guidance file. Use it as a practical standard for internal development, product design, and AI-assisted implementation workflows.

If you intend to reuse or adapt the content for your own project, keep the original intent intact and preserve the quality standards described here.

---

## Final note

This repository exists to help bridge the gap between:

- raw AI code generation
- product quality expectations
- user trust and usability
- real-world engineering discipline

It is best treated as a professional operating standard, not just a reference file.

---

For a full implementation reference, see the `AGENTS.md` file in this repository.
