**ClientHub - cloud-based CRM platform**

CRM platforms are highly attractive targets for cyberattacks due to the sensitive data they store. When designing such applications, developers must balance Security, Functionality, and Usability. These attributes form an interdependent triangle: increasing one often reduces the others. Testing components individually may show strong security, but when tested as an integrated system, flaws may emerge so there might be an hole in the whole.
Example: API endpoints may be secure when tested individually, and the web dashboard may enforce role-based access. However, when combined, a poorly validated ID in a URL could allow a logged-in Sales Rep to access another client’s data, resulting in a Cross-Tenant Broken Object Level Authorization (BOLA) vulnerability.

**Top 5 Critical Attack Surfaces**

The following attack surfaces are identified using STRIDE, OWASP Top 10, and MITRE ATT&CK TTPs. Likelihood and impact are calculated using the OWASP Risk Rating Methodology.

**1. API Authorization (Cross-Tenant BOLA)**

The Threat: An authenticated user modifies resource IDs (e.g., customer_id) in API requests to access or edit data belonging to other business clients.

Impact: Massive data breach of all 200 business clients; total loss of platform integrity and multi-tenant isolation, reputational damage as well

Likelihood: High – Extremely easy to discover (ID in URL) and exploit via simple script.

Compliance & Framework: Relates to MITRE T1567 (Exfiltration to Cloud Service) and violates GDPR Art. 32 (Security of Processing) by failing to ensure the ongoing confidentiality of multi-tenant data.

**2. Insecure Mobile Data Storage**

The Threat: The mobile app stores customer PII (names, phone numbers) in unencrypted local databases or caches on field staff devices.

Impact: If a device is lost or stolen, all customer records previously accessed on that device are fully compromised.

Likelihood: High – Mobile devices are frequently lost.

Compliance & Framework: Relates to MITRE T1401 (Stored Device Data) and violates GDPR Art. 25 (Privacy by Design) which requires technical measures to minimize data exposure on end-user hardware.

**3. Third-Party Integration (OAuth Token Theft)**

The Threat: Attackers intercept or steal OAuth tokens used for email/calendar sync to impersonate the business and access private schedules.

Impact: High-efficacy phishing (sending mail from a real business account) and unauthorized access to sensitive executive data.

Likelihood: Medium – Requires technical skill to intercept tokens, but they are high-value targets for advanced persistent threats.

Compliance & Framework: Relates to MITRE T1550.001 (Application Access Token) and violates GDPR Art. 33 (Breach Notification) as unauthorized token use constitutes a "personal data breach" requiring a 72-hour report.

**4. Admin Session Management**

The Threat: Attackers use session hijacking (stealing active cookies) to bypass Multi-Factor Authentication (MFA) and take over high-privilege accounts.

Impact: Full account takeover; attackers can delete entire databases, lock out legitimate users, or change payment routing.

Likelihood: Medium – Mitigated by HTTPS, but still vulnerable to malicious browser extensions or public Wi-Fi "Man-in-the-Middle" attacks.

Compliance & Framework: Relates to MITRE T1539 (Steal Web Session Cookie) and violates GDPR Art. 32, as failing to secure sessions constitutes a failure to implement "appropriate technical and organisational measures."

**5. Cloud Storage Misconfiguration (S3 Buckets)**

The Threat: Private files like contract PDFs or payment records are stored in cloud buckets with "Public Read" permissions or guessable URLs.

Impact: Direct public exposure of sensitive customer documentation to anyone with the link or a scanning tool.

Likelihood: Medium – Automated scanners find these daily, though discovery depends on whether bucket names follow a predictable pattern.

Compliance & Framework: Relates to MITRE T1530 (Data from Cloud Storage) and violates GDPR Art. 5 (Integrity and Confidentiality) by failing to protect data against unauthorized processing.

Formula:

Likelihood =Average Threat Factors + Average Vulnerability Factors/2

Mapping:

0–<3 → LOW
3–<6 → MEDIUM
6–9 → HIGH

Impact = Technical Impact Avg + Business Impact Avg/2

Example – API Authorization (BOLA):

Threat Factors: Skill 6, Motive 9, Opportunity 9, Size 9 → Avg = 8.25

Vulnerability Factors: Ease of Discovery 9, Ease of Exploit 9, Awareness 9, Detection 3 → Avg = 7.5

Likelihood: (8.25 + 7.5)/2 = 7.88 → High

Technical Impact: Loss of Confidentiality 9, Integrity 7, Availability 6, Accountability 8 → Avg = 7.5

Business Impact: Financial 9, Reputation 7, Non-compliance 7, Privacy Violation 9 → Avg = 8.0

Impact: (7.5 + 8.0)/2 = 7.75 → High

Overall Risk: Likelihood High + Impact High → High Risk

Similar calculations are done for other attack surfaces.

**Top 3 Prioritized Risks**

API Authorization (BOLA): Highest systemic risk; one user can access all clients’ data catastrophic GDPR and business impact.

Insecure Mobile Data Storage: High likelihood due to device loss; PII exposure can trigger compliance fines.

Third-Party Integration (OAuth Token Theft): Compromises identity; attackers can impersonate business, high legal and reputational risk.

**Prioritization Logic:**

Likelihood alone is insufficient; even if a vulnerability is easy to exploit but low impact, it is less critical. Impact alone ignores ease of attack.Combining high likelihood and high impact identifies the attack surfaces.
-> API Authorization (Cross-Tenant BOLA) – Highest Priority, this risk is prioritised first because it represents a Broken Object Level Authorization (BOLA) vulnerability, which is a direct subset of OWASP Top 10 (2025) A01: Broken Access Control, currently ranked as the most critical web application risk.
And also the CVSS V3.1 Base Score is 9.4 (AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:L) (CVSS V3.1 CALCULATOR: https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) and also because the attack is easy to discover and requires minimal technical skill, it poses an immediate and catastrophic risk. 
Reputation and customer trust is the foundation of a business and hence this attack surface leads to the great business impact.
-> Insecure Mobile Data Storage – Second Priority, it represents a failure to protect sensitive data at rest on end-user devices. The mobile application stores customer personally identifiable information (PII) such as names, emails, and phone numbers in unencrypted local storage, making the data immediately accessible if a device is lost or stolen.
Although the attack vector is physical, mobile device loss is a highly realistic and frequent occurrence in operational environments. The exploit requires no technical skill once physical access is obtained, and detection is effectively impossible. From a business perspective, this leads to direct exposure of customer data, triggering mandatory breach notifications under GDPR and erosion of customer trust. 
The predictability and inevitability of this scenario make it a high-priority risk despite its lower technical exploit complexity.
-> Third-Party Integration (OAuth Token Theft) – Third Priority, it compromises identity and trust, rather than just data, and aligns with OWASP Top 10 (2025) A07: Authentication Failures. Stolen or intercepted OAuth tokens used for email and calendar integrations allow attackers to impersonate legitimate businesses and access sensitive communications.
While exploitation requires higher technical skill compared to BOLA or mobile data exposure, OAuth tokens are high-value assets and are frequently targeted by advanced attackers. Once compromised, attackers can send emails, manipulate calendars, and interact with customers as a trusted business entity. This significantly amplifies phishing success rates and fraud potential. 
The resulting reputational damage and legal exposure elevate this risk above traditional data leakage scenarios, justifying its inclusion in the top three.

**Future Direction – AI Attack Surface**

While ClientHub currently does not use AI, a futuristic AI attack surface could include:

AI-Powered Recommendation Manipulation: AI models suggest sales actions or customer prioritization. An attacker poisoning training data could bias recommendations, leading to poor business decisions or leaks of PII via model inference attacks (Model Inversion).
And also there is a threat which is sensitive data leakage through AI prompts.

Mitigation Consideration: Ensure AI models are trained with validated, isolated data, and monitor for anomalous outputs and Data leakage prevention should be installed as well.

