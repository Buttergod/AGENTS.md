# LEGALITIES FOR VIBECODED WEBSITES
## AS AN AI YOU MUST MAKE SURE THAT THE WEBSITE IS 100% LEGAL

Here is a comprehensive breakdown of **100 key legal risks, mandatory requirements, and risk-mitigation strategies** for websites and AI-built ("vibe-coded") applications, categorized into three distinct pillars:

---

### Pillar A: Vibe-Coding Specific Legal Risks & Vulnerabilities (1–40)

1. **Lack of Age Verification (COPPA Violations):** AI-generated sign-up forms routinely omit age verification, exposing operators to FTC civil penalties up to \$53,088 per child for collecting data under 13 without verifiable parental consent ``.
2. **CDN-Hosted Fonts (GDPR Data Leaks):** Loading Google Fonts via public CDNs leaks visitor IP addresses to third parties without consent, which European courts have ruled violates the GDPR ``.
3. **Session Replay Wiretapping Claims (CIPA):** Integrating unconsented session-replay analytics can trigger California Invasion of Privacy Act wiretapping lawsuits carrying \$5,000 statutory damages per visitor ``.
4. **Disabled Row Level Security (RLS):** Vibe-coded database configurations (such as Supabase) frequently leave Row Level Security disabled, exposing private databases and API tokens to mass leaks ``.
5. **Copyleft Open-Source Ingestion (GPL Breaches):** AI models often reproduce GPL-licensed code snippets; incorporating them can legally force you to open-source your entire proprietary codebase ``.
6. **Stripped Open-Source Notices (MIT/Apache):** AI code generation regularly strips required copyright and attribution notices from permissive libraries, causing direct copyright infringement ``.
7. **Loss of Copyright Ownership Over Pure AI Code:** Code generated purely by AI without human authorship cannot be copyrighted under U.S. and EU law, placing the codebase in the public domain ``.
8. **Insufficient Prompt Copyrightability:** Simply typing natural language prompts into an AI generator does not meet the legal threshold for human authorship required to copyright the output ``.
9. **Exposure to Statutory Damages:** Failing to establish copyrightable human contributions deprives software owners of statutory damages and attorney fees in legal disputes ``.
10. **Unchecked Prompt Input Data Leaks:** Inputting personal or customer data into public AI prompts without a Data Processing Agreement (DPA) constitutes an illegal data transfer under GDPR Art. 28/44 ``.
11. **Destruction of Trade Secret Protection:** Submitting proprietary source code to AI tools that retain inputs for model training destroys trade secret legal protections ``.
12. **Missing Software Bill of Materials (SBOM):** AI-generated dependencies are obscured as in-house code, preventing accurate vulnerability tracking required by the EU Cyber Resilience Act (CRA) ``.
13. **Strict Product Liability for Code Flaws:** Updated EU Product Liability Directives classify software as a product, making creators strictly liable for defects and security vulnerabilities ``.
14. **Lack of Privacy by Design (GDPR Art. 25):** Shipping unreviewed AI code containing hardcoded credentials or unprotected interfaces violates statutory "Privacy by Design" mandates ``.
15. **Unbounded Serverless Recursive Billing Loops:** AI-generated serverless functions can enter recursive execution loops, causing unexpected five-figure cloud hosting invoices overnight ``.
16. **Lack of Rate Limiting & Financial Exposure:** Deploying AI web applications without DDoS protection or spend limits can result in runaway bandwidth bills ``.
17. **Breaching Third-Party API Terms of Service:** Stitching together external APIs via AI without reviewing usage rules can lead to abrupt account termination or breach-of-contract claims ``.
18. **Missing DMCA Designated Agent Registration:** Accepting user-uploaded files without registering a DMCA agent with the Copyright Office (\$6 fee) strips statutory safe harbor protections ``.
19. **Unconsented Marketing Emails (CAN-SPAM):** Sending launch emails without a valid physical address or unsubscribe mechanism exposes businesses to FTC fines up to \$53,088 per email ``.
20. **Unlawful SMS Marketing (TCPA Lawsuits):** Texting user waitlists without documented prior express written consent triggers TCPA statutory damages of \$500 to \$1,500 per text message ``.
21. **Deceptive Auto-Renewals (California ARL):** Failing to display subscription renewal terms directly adjacent to the sign-up button allows consumers to legally claim full refunds ``.
22. **Over-reliance on Accessibility Overlays:** Adding third-party overlay widgets to fix AI-generated UI fails to prevent ADA lawsuits, as courts reject overlays as legally insufficient ``.
23. **Missing Keyboard Focus & Traps:** AI-built UI components routinely omit visible focus states or trap keyboard users in navigation loops, triggering ADA Title III litigation ``.
24. **Missing Form Field Labels:** AI-generated forms frequently rely on visual placeholders rather than accessible `<label>` elements, creating immediate ADA lawsuit targets ``.
25. **Missing Image Alt Text:** AI-generated graphics and photos missing descriptive `alt` tags violate WCAG standards and rank among the top causes of accessibility lawsuits ``.
26. **High OWASP Security Failure Rates:** Security research shows 45% of AI-generated code samples fail basic OWASP security tests, exposing apps to SQL injection and XSS ``.
27. **AI Chatbot Deception:** Deploying customer-facing AI chatbots without disclosing that users are interacting with an AI rather than a human constitutes a deceptive business practice ``.
28. **Deceptive AI Testimonials:** Using AI to fabricate customer reviews or testimonials violates FTC endorsement guidelines ``.
29. **Disregarded State Privacy Thresholds:** Operating a vibe-coded SaaS across state lines without tracking data thresholds (e.g., CCPA 50k+ residents/devices) leads to unexpected state non-compliance ``.
30. **Unprotected API Endpoints:** AI code generators often expose backend routes without proper session authentication checks ``.
31. **Hardcoded Credentials in Client Bundles:** AI tools frequently hardcode private keys or secrets directly into client-side JavaScript bundles ``.
32. **Missing Terms of Service:** Launching web apps without binding Terms of Service leaves the business without contractual liability limits ``.
33. **Personal Liability from Unincorporated Launching:** Operating vibe-coded apps as sole proprietorships exposes personal assets to software breach liabilities ``.
34. **Insurance Coverage Exclusions:** Standard general business insurance policies typically exclude software bugs, privacy breaches, and tech liabilities ``.
35. **Misclassification of Independent Contributors:** Collaborating on vibe-coded projects without written IP assignment agreements creates cloudiness over code ownership ``.
36. **Neglecting Staff AI Literacy (EU AI Act Art. 4):** Failing to ensure employees understand AI security and legal risks violates mandatory literacy obligations under the EU AI Act ``.
37. **Undocumented Development Lifecycles:** Failing to log human design iterations makes it impossible to prove copyright originality during IP litigation ``.
38. **Unverified Third-Party Code Packages:** AI code generators routinely recommend hallucinated or outdated package dependencies, opening applications to supply-chain attacks ``.
39. **Inaccurate Software Representations:** Making broad claims in AI-generated copy about software capabilities ("100% secure", "error-free") creates strict warranty liability ``.
40. **Failure to Honor User Opt-Out Signals:** Ignoring automated browser opt-out signals (Global Privacy Control) violates state privacy laws like the CCPA and CTDPA ``.

---

### Pillar B: Mandatory Legalities All Websites Need (41–75)

41. **Compliant Privacy Policy:** Mandatory for any site collecting personal data (names, emails, IP addresses, cookies) explaining data collection, usage, and sharing ``.
42. **Annual Privacy Policy Updates:** State privacy regulations (such as CCPA) require privacy policies to be reviewed and updated at least once every 12 months ``.
43. **Terms of Service / Terms of Use:** Establishes rules of engagement, prohibited uses, IP rights, arbitration clauses, and liability limitations ``.
44. **Affirmative Cookie Consent Banner (GDPR):** Requires clear opt-in consent before setting non-essential cookies for European visitors ``.
45. **Opt-Out Cookie Mechanism (CCPA/CTDPA):** Requires providing U.S. visitors a clear mechanism to opt out of targeted advertising and data sales ``.
46. **"Do Not Sell or Share My Personal Information" Link:** Mandatory footer link for businesses subject to California privacy laws ``.
47. **Cookie Preference Center:** Enables users to modify or revoke their cookie preferences at any time ``.
48. **Data Subject Access Request (DSAR) Mechanisms:** Providing accessible methods for users to submit requests to access, correct, delete, or port their data ``.
49. **Prohibition of FTC Dark Patterns:** Banning deceptive UI designs, such as hidden reject buttons or pre-checked consent boxes ``.
50. **WCAG 2.1 / 2.2 Level AA Accessibility Conformance:** Meeting baseline digital accessibility standards for perceivability, operability, understandability, and robustness ``.
51. **Minimum Text Color Contrast (4.5:1 Ratio):** Maintaining at least 4.5:1 contrast for normal text and 3:1 for large text against backgrounds ``.
52. **Non-Text UI Contrast (3:1 Ratio):** Maintaining at least 3:1 contrast for icons, input borders, and interactive boundaries ``.
53. **Touch Target Size Requirements:** Ensuring interactive buttons and links have touch targets of at least 24x24 pixels ``.
54. **Keyboard Navigation Support:** Guaranteeing that all interactive controls are fully usable without a mouse ``.
55. **Visible Keyboard Focus Indicators:** Displaying distinct focus rings when navigating elements via keyboard ``.
56. **Layout Reflow at 320px Width & 200% Zoom:** Ensuring layouts adapt without horizontal scrolling or clipping when zoomed ``.
57. **Screen Reader Semantic HTML Markup:** Using proper HTML tags (`<header>`, `<main>`, `<footer>`, `<nav>`) and ARIA roles for assistive tech ``.
58. **Published Accessibility Statement:** Disclosing current accessibility efforts, standards met, known limitations, and contact info ``.
59. **PCI DSS Compliance for E-Commerce:** Adhering to Payment Card Industry standards when handling payment transactions ``.
60. **HTTPS / SSL/TLS Encryption:** Enforcing secure encryption protocols across all website routes ``.
61. **Prominently Posted Refund & Return Policy:** Disclosing return terms prior to purchase to satisfy FTC e-commerce rules ``.
62. **FTC Mail Order Rule Shipping Policy:** Publishing realistic shipping timeframes (defaulting to 30 days if unspecified) ``.
63. **Separate E-Commerce Terms of Sale:** Defining rules for payment processing, order acceptance, and commercial cancellation rights ``.
64. **Copyright Notice in Footer:** Displaying an updated copyright notice (e.g., "© 2026 Brand Name. All rights reserved") ``.
65. **DMCA Takedown Clause in Terms:** Outlining procedures for copyright owners to report infringing user content ``.
66. **Responsible Content Attribution (TASL Framework):** Crediting third-party creative content with Title, Author, Source, and Link ``.
67. **Disclosure of Content Modifications:** Explicitly stating if third-party licensed media has been cropped, resized, or altered ``.
68. **CAN-SPAM Unsubscribe Buttons:** Including a functioning, single-click opt-out link in every marketing email ``.
69. **Accurate Email Header Information:** Ensuring "To", "From", and "Reply-To" email headers are accurate and non-deceptive ``.
70. **Clear Advertising Disclosures:** Marking commercial emails, sponsored posts, or affiliate links as advertisements ``.
71. **Affiliate & Legal Disclaimers Page:** Publishing dedicated disclosures for third-party links, liability limits, and general information ``.
72. **Professional Advice Disclaimers:** Clarifying that website content does not constitute legal, medical, or financial advice ``.
73. **HIPAA Security & Privacy Disclosures (Health Sites):** Securing patient intake forms, booking engines, and health data with encryption and HIPAA compliance ``.
74. **ABA Model Rules Compliance (Legal Sites):** Avoiding false claims of specialization, subjective superiority, or guaranteed outcomes on law firm sites ``.
75. **Visible Contact & Ownership Information:** Displaying physical business addresses, company registration numbers, and contact forms ``.

---

### Pillar C: How to Avoid Legal Risks & Implement Best Practices (76–100)

76. **Establish an Internal AI Governance Policy:** Define approved AI coding tools, forbidden data inputs, and mandatory human code review thresholds ``.
77. **Use Enterprise AI Licenses:** Procure commercial AI licenses with Data Processing Agreements that explicitly exclude inputs from model training ``.
78. **Conduct Mandatory Human Code Reviews:** Require experienced developers to review all AI-generated code before pushing to production ``.
79. **Document Creative Human Contributions:** Maintain logs, prompt scaffolding, design specs, and commit tags to prove copyright originality ``.
80. **Substantially Modify AI Code:** Make meaningful human edits and architectural refinements to ensure code qualifies for copyright protection ``.
81. **Protect Specs as Trade Secrets:** Safeguard sensitive prompts, dataset curation, and architectural frameworks as proprietary trade secrets ``.
82. **Run Automated License & Duplicate Scanners:** Use tooling (such as Impeccable or License Checkers) to flag GPL copyleft code and missing open-source notices ``.
83. **Maintain a Data Inventory & Audit:** Audit all business data collection processes, third-party trackers, and vendor storage pipelines ``.
84. **Execute Data Processing Agreements (DPAs) with Vendors:** Ensure all third-party software and cloud tools sign binding DPAs ``.
85. **Self-Host Website Fonts:** Download and host font files locally on your own servers to prevent leaking visitor IP addresses ``.
86. **Enforce Row Level Security (RLS) Mandates:** Require database administrators to verify RLS policies on all database tables prior to deployment ``.
87. **Deploy Cloud DDoS & Rate-Limiting Protection:** Place applications behind CDNs (like Cloudflare) with automated rate limiting and hard spend caps ``.
88. **Perform Manual WCAG Accessibility Audits:** Conduct comprehensive manual audits with real assistive technology users rather than relying solely on automated scanners ``.
89. **Remediate Top 15 Accessibility Lawsuit Targets:** Prioritize immediate fixes for missing alt text, missing form labels, and broken keyboard navigation ``.
90. **Incorporate Contractual Accessibility Guarantees:** Add clauses to contracts with third-party web agencies holding them legally responsible for WCAG compliance ``.
91. **Implement Google Analytics Consent Mode:** Configure analytics tools to automatically restrict tracking based on user consent choices ``.
92. **Obtain Prior Express Written Consent for SMS:** Require explicit, documented opt-in signatures before sending marketing text messages ``.
93. **Place Subscription Disclosures Directly Next to CTAs:** Display billing frequency, renewal pricing, and cancellation links directly adjacent to payment buttons ``.
94. **Register a DMCA Agent Online:** Pay the \$6 fee to list an official DMCA designated agent with the U.S. Copyright Office ``.
95. **Incorporate AI Usage Disclosures:** Disclose customer-facing AI chatbots and AI-generated marketing assets clearly on the site ``.
96. **Incorporate as a Formal Legal Entity:** Form an LLC or corporation to insulate personal assets from business and web-related liabilities ``.
97. **Obtain Specialized Cyber & Tech Liability Insurance:** Secure insurance policies that explicitly cover data breaches, software flaws, and privacy lawsuits ``.
98. **Execute Clear IP Assignment Agreements:** Ensure all employees, freelancers, and contractors sign written agreements assigning software IP to the company ``.
99. **Implement Multi-Factor Authentication (MFA) & Access Control:** Restrict backend infrastructure access using strong passwords and MFA ``.
100. **Establish a Prompt Grievance & Complaint Resolution Process:** Actively investigate and resolve user privacy or accessibility complaints immediately to prevent escalation into lawsuits ``.
