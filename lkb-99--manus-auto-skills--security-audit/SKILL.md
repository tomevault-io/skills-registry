---
name: security-audit
description: Performs a comprehensive security audit on applications based on OWASP ASVS. Use this skill when users want to perform a security check, audit, or review of their application. Triggers: security audit, security check, vulnerability scan, penetration test, OWASP, ASVS, application security, code review, secure coding, pentest, auditoria de segurança, verificação de segurança, análise de vulnerabilidade, teste de invasão. Use when this capability is needed.
metadata:
  author: lkb-99
---

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: security audit, security check, vulnerability scan, penetration test, OWASP, ASVS, application security, code review, secure coding, pentest, auditoria de segurança, verificação de segurança, análise de vulnerabilidade, teste de invasão, teste de penetração.
- Phrases: "perform a security audit", "check my app for vulnerabilities", "run a penetration test", "review my code for security issues", "fazer uma auditoria de segurança", "verificar vulnerabilidades no meu aplicativo", "realizar um pentest".
- Context: Any discussion about assessing or testing the security of an application, codebase, or system.

**Example user queries that trigger this skill:**
- "I need to run a security audit on my web application before deployment."
- "Can you help me perform a penetration test on my API?"
- "Preciso de uma análise de segurança do meu código-fonte."

## When to Use This Skill

This skill is particularly useful in the following scenarios:

- **Pre-deployment Security Review:** Before deploying a new application or a major update, use this skill to perform a final security check.
- **Regular Security Audits:** For applications in production, this skill can be used to conduct periodic security audits (e.g., quarterly or annually) to ensure ongoing security.
- **Third-party Application Assessment:** When evaluating a third-party application or library, this skill can help you assess its security posture.
- **Compliance Requirements:** If your organization needs to comply with security standards like PCI DSS, HIPAA, or GDPR, this skill can be a valuable tool to verify compliance.
- **Developer Training:** This skill can be used as a training tool to educate developers about common security vulnerabilities and best practices.

## Core Capabilities

This skill provides a structured approach to application security auditing, covering the following core capabilities:

### V1: Architecture, Design and Threat Modeling

*   **Threat Modeling:** Identify and analyze potential threats to the application.
*   **Secure Design Patterns:** Ensure the use of secure design patterns to prevent common vulnerabilities.
*   **Component and Data Flow Analysis:** Review the application's components and data flows for security risks.

### V2: Authentication

*   **Password Strength and Storage:** Verify that passwords are strong and stored securely.
*   **Multi-Factor Authentication (MFA):** Assess the implementation of MFA.
*   **Authentication Feedback:** Check for secure authentication feedback mechanisms.

### V3: Session Management

*   **Session Token Generation and Handling:** Verify that session tokens are generated and handled securely.
*   **Session Timeout:** Assess the implementation of session timeout controls.
*   **Session Termination:** Check for secure session termination mechanisms.

### V4: Access Control

*   **Principle of Least Privilege:** Ensure that users have only the permissions they need to perform their tasks.
*   **Role-Based Access Control (RBAC):** Verify the implementation of RBAC.
*   **Insecure Direct Object References (IDOR):** Test for IDOR vulnerabilities.

### V5: Malicious Input Handling

*   **Input Validation:** Verify that all input is validated to prevent injection attacks.
*   **Output Encoding:** Ensure that all output is properly encoded to prevent XSS.
*   **File Uploads:** Assess the security of file upload functionality.

### V6: Cryptography at Rest

*   **Data Encryption:** Verify that sensitive data is encrypted at rest.
*   **Key Management:** Assess the security of cryptographic key management.

### V7: Error Handling and Logging

*   **Error Messages:** Check for informative but not overly descriptive error messages.
*   **Security Logging:** Verify that security-related events are logged.

### V8: Data Protection

*   **Data Classification:** Ensure that data is classified according to its sensitivity.
*   **Data Privacy:** Assess compliance with data privacy regulations.

### V9: Communication Security

*   **TLS/SSL:** Verify the use of TLS/SSL for all communications.
*   **Certificate Management:** Assess the security of certificate management.

### V10: HTTP Security Configuration

*   **Security Headers:** Check for the use of security headers such as Content Security Policy (CSP), HTTP Strict Transport Security (HSTS), and X-Frame-Options.
*   **Cookie Security:** Verify that cookies are configured securely.

## Step-by-Step Workflow

This workflow provides a structured approach to conducting a security audit using the OWASP ASVS. It is divided into three main phases: Planning, Execution, and Reporting.

### 1. Planning Phase

1.  **Define the Scope:** Clearly define the application or system to be audited. Identify all components, including web servers, application servers, databases, and any third-party services.
2.  **Select the ASVS Level:** Choose the appropriate ASVS level (1, 2, or 3) based on the application's risk profile. Level 1 is for low-assurance applications, Level 2 is for applications that contain sensitive data, and Level 3 is for the most critical applications.
3.  **Gather Documentation:** Collect all relevant documentation, including architecture diagrams, data flow diagrams, and any existing security policies or procedures.
4.  **Assemble the Audit Team:** Put together a team with the necessary skills and expertise to conduct the audit. This may include security analysts, developers, and system administrators.

### 2. Execution Phase

1.  **Review Architecture and Design:** Analyze the application's architecture and design for potential security flaws. This includes reviewing the threat model and data flow diagrams.
2.  **Verify Security Controls:** Systematically go through the ASVS checklist for the selected level. For each control, verify its implementation and effectiveness. This will involve a combination of manual testing, automated scanning, and code review.
3.  **Document Findings:** For each control, document whether it is in place, not in place, or not applicable. For any controls that are not in place, document the finding with a clear description of the vulnerability, its potential impact, and the steps to reproduce it.

### 3. Reporting Phase

1.  **Compile the Audit Report:** Create a comprehensive audit report that summarizes the findings. The report should include an executive summary, a detailed list of vulnerabilities, and recommendations for remediation.
2.  **Prioritize Vulnerabilities:** Prioritize the identified vulnerabilities based on their risk level. This will help the development team focus on the most critical issues first.
3.  **Present the Findings:** Present the audit findings to the relevant stakeholders, including the development team, management, and any other affected parties.
4.  **Track Remediation:** Track the remediation of the identified vulnerabilities to ensure that they are addressed in a timely manner.

## Best Practices

- **Automate Where Possible:** Use a combination of Static Application Security Testing (SAST) and Dynamic Application Security Testing (DAST) tools to automate the discovery of common vulnerabilities. SAST tools analyze the source code for potential security flaws, while DAST tools test the running application for vulnerabilities. This automated approach can significantly speed up the audit process.
- **Combine with Manual Testing:** Automated tools are not a silver bullet. Manual penetration testing is crucial for uncovering complex vulnerabilities, business logic flaws, and issues that require a deep understanding of the application's context. A skilled security professional can simulate real-world attack scenarios and identify weaknesses that automated scanners might miss.
- **Involve Developers in the Process:** Foster a collaborative environment by involving the development team throughout the audit process. This not only helps in quicker remediation but also serves as a valuable learning experience for the developers, enabling them to write more secure code in the future. Share the findings with them and provide clear guidance on how to fix the identified vulnerabilities.
- **Stay Up-to-Date with the ASVS:** The OWASP ASVS is a living document that is regularly updated to reflect the latest threats and security best practices. Make sure you are using the latest version of the standard for your audits. Subscribe to the OWASP mailing lists and follow the project on social media to stay informed about new releases.
- **Risk-Based Approach:** Prioritize your audit efforts based on the risk profile of the application. Not all applications require the same level of scrutiny. Use the ASVS levels to tailor the audit to the specific needs of the application. For high-risk applications, a comprehensive Level 3 audit is recommended, while for lower-risk applications, a Level 1 or Level 2 audit may be sufficient.
- **Continuous Auditing:** Security is not a one-time effort. Integrate security auditing into your CI/CD pipeline to enable continuous security testing. This will help you to identify and fix vulnerabilities early in the development lifecycle, reducing the cost and effort of remediation.

## Examples

### Example 1: SQL Injection

**ASVS Control:** V5.3.1 - Verify that the application protects against SQL injection.

**Finding:** The application is vulnerable to SQL injection in the product search form. An attacker can manipulate the query to retrieve all products from the database by entering `\' OR 1=1; --` in the search field.

**Recommendation:** Use parameterized queries (also known as prepared statements) to ensure that user input is treated as data and not as executable code.

**Code Example (Node.js with `mysql` package):**

```javascript
// Vulnerable code
const productId = req.query.id;
const query = `SELECT * FROM products WHERE id = ${productId}`;
db.query(query, (err, results) => {
  // ...
});

// Secure code
const productId = req.query.id;
const query = 'SELECT * FROM products WHERE id = ?';
db.query(query, [productId], (err, results) => {
  // ...
});
```

### Example 2: Cross-Site Scripting (XSS)

**ASVS Control:** V5.2.1 - Verify that the application protects against reflected XSS.

**Finding:** The application is vulnerable to reflected XSS in the user profile page. A user's name is displayed without proper encoding, allowing an attacker to inject a script. For example, a name could be `<script>alert('XSS')</script>`.

**Recommendation:** Implement context-sensitive output encoding for all user-supplied data before it is rendered in the browser. Use a library like `DOMPurify` for client-side sanitization or server-side encoding functions.

**Code Example (JavaScript/React):**

```jsx
// Vulnerable code
function UserProfile({ name }) {
  return <div dangerouslySetInnerHTML={{ __html: `<h1>${name}</h1>` }} />;
}

// Secure code
function UserProfile({ name }) {
  return <h1>{name}</h1>; // React automatically escapes the content
}
```

### Example 3: Insecure Direct Object Reference (IDOR)

**ASVS Control:** V4.2.1 - Verify that the application enforces authorization rules for every request.

**Finding:** A user can view another user's private information by changing the `id` parameter in the URL (e.g., `/profile/123` to `/profile/124`). The application does not verify if the logged-in user is authorized to view the requested profile.

**Recommendation:** Implement access control checks on the server for every request that accesses a private resource. The server should verify that the logged-in user has the necessary permissions to access the requested resource.

**Code Example (Ruby on Rails):**

```ruby
# Vulnerable code
class ProfilesController < ApplicationController
  def show
    @profile = Profile.find(params[:id])
  end
end

# Secure code
class ProfilesController < ApplicationController
  before_action :authenticate_user!

  def show
    @profile = current_user.profiles.find(params[:id])
    # This will raise an exception if the profile doesn't belong to the current user
  end
end
```

## References

- [OWASP Application Security Verification Standard (ASVS) Project](https://owasp.org/www-project-application-security-verification-standard/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
### Example 4: XML External Entity (XXE)

**ASVS Control:** V5.4.1 - Verify that the application is not vulnerable to XML External Entity (XXE) injection.

**Finding:** The XML parser of the application is configured to process external entities, which allows an attacker to read local files on the server.

**Recommendation:** Disable XXE processing in the XML parser.

**Code Example (Java):**

```java
// Vulnerable code
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
DocumentBuilder db = dbf.newDocumentBuilder();
Document doc = db.parse(new File("input.xml"));

// Secure code
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
DocumentBuilder db = dbf.newDocumentBuilder();
Document doc = db.parse(new File("input.xml"));
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
