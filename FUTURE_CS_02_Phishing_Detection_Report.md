**Phishing Email Detection & Awareness Report**  
**Future Interns — Cyber Security Track | Task 2**  
   
 **Repository:** FUTURE_CS_02  
| | |  
|-|-|  
| **Field** | **Detail** |   
| Prepared by | [Shivansh Yadav] — Cyber Security Intern, Future Interns |   
| CIN ID | [FIT/SEP26/CS10303] |   
| Date | [18/09/26] |   
| Sample source | PhishTank / Kaggle phishing datasets / Enron corpus / provided samples |   
| Emails analysed | [N] |   
| Version | 1.0 |   
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSPBCUZfE2IYmVDBhAU2QtIq6DIzW7UHAMBfnGt1V8fXEwAAXrse/xcF7U7sx4wAAAAASUVORK5CYII=)  
**1. Executive Summary**  
Phishing remains the single most common way attackers get inside an organisation. It works because it targets people rather than software — no firewall blocks a convincing email.  
This report analyses [N] sample emails, classifies each as **Safe**,  **Suspicious**, or  **Phishing**, explains the techniques attackers used, and sets out practical guidance staff can follow without needing technical training.  
**Results of this analysis**  
| | | |  
|-|-|-|  
| **Classification** | **Count** | **Share** |   
| ✅ Safe | [ ] | [ ]% |   
| ⚠️ Suspicious | [ ] | [ ]% |   
| 🔴 Phishing | [ ] | [ ]% |   
   
**Key takeaway for the business:** the most convincing samples did not contain spelling errors or obvious red flags. They relied on  **authority, urgency, and a lookalike domain** — which means "spot the typo" training is no longer sufficient. Technical controls (SPF, DKIM, DMARC) plus a simple, blame-free reporting process are what actually reduce risk.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhYMMAKlD4OzrxgQU2QtIq6DIzR3UFAMBf3Gu1VefXEwAAXtsfSqADWz4G/HUAAAAASUVORK5CYII=)  
**2. Where to Get Legitimate Sample Emails**  
Use public, research-approved sources. Never forward real phishing to colleagues as a "test" without authorisation.  
| | | |  
|-|-|-|  
| **Source** | **What it gives you** | **Link** |   
| **PhishTank** | Community-verified phishing URLs and submissions | phishtank.org |   
| **Kaggle — Phishing Email Datasets** | Labelled CSV corpora of phishing vs. legitimate email | kaggle.com/datasets |   
| **Enron Email Dataset** | Large corpus of genuine business email (for "Safe" samples) | cs.cmu.edu/~enron/ |   
| **APWG eCrime Exchange** | Phishing trend reports and statistics | apwg.org |   
| **Your own spam folder** | Real, current samples — redact personal information before committing | — |   
| **Gophish (self-hosted)** | Build your own simulated phishing email in a lab you control | getgophish.com |   
   
**Safety rules while handling samples**  
1. Analyse inside a **virtual machine** or a disposable environment — never your main system.  
2. **Do not click links.** Copy the URL as text and inspect it. If you must resolve it, use urlscan.io or VirusTotal, which visit it on your behalf.  
3. **Do not open attachments.** Inspect them with static tools (oletools, exiftool, file) or upload the hash to VirusTotal.  
4. **Redact** real names, addresses, and account numbers before committing anything to a public GitHub repository.  
5. Defang URLs in documentation so no one clicks them by accident: hxxp://evil[.]com/login.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OQQmAABRAsSdYxKY/jMFMIZ7ECt5E2BJsmZmt2gMA4C+Otbqr8+sJAACvXQ85QgYXd/O+eQAAAABJRU5ErkJggg==)  
**3. Detection Methodology**  
**3.1 The four-layer check**  
| | | |  
|-|-|-|  
| **Layer** | **Question** | **Tools** |   
| **1. Header** | Did this really come from where it claims? | Google Admin Toolbox Message Header, MXToolbox Header Analyzer, mail-parser |   
| **2. Sender** | Is the display name consistent with the actual address and domain? | WHOIS, dig, manual inspection |   
| **3. Content** | What psychological lever is being pulled? | Manual reading against the indicator list |   
| **4. Payload** | Where do links and attachments actually lead? | urlscan.io, VirusTotal, hover/inspect in plain-text view |   
   
**3.2 Reading email headers**  
Get the raw source first:  
- **Gmail:** ⋮ → Show original  
- **Outlook:** File → Properties → Internet headers  
- **Thunderbird:** Ctrl+U  
Then look for these fields:  
Return-Path: <bounce@random-domain.ru>          ← rarely matches a real sender  
 From: "Microsoft Account Team" <no-reply@micros0ft-security.com>  
 Reply-To: accounts-recovery@gmail.com           ← replies go somewhere else entirely  
 Received: from unknown-host (203.0.113.45)      ← trace the true origin IP  
 Authentication-Results: mx.google.com;  
     spf=fail (domain does not designate 203.0.113.45 as permitted sender)  
     dkim=none  
     dmarc=fail (p=NONE)  
   
**How to read ** **Received:** ** headers:** they stack bottom-to-top. The  **bottom-most** entry is the origin. If the bottom hop is a residential IP, a consumer VPS provider, or a country unrelated to the claimed sender, treat that as a strong indicator.  
**The authentication trio, explained simply**  
| | | |  
|-|-|-|  
| **Mechanism** | **What it proves** | **Failure means** |   
| **SPF** | The sending server is on the domain owner's approved list | The mail was sent from an unauthorised server |   
| **DKIM** | The message was cryptographically signed by the domain and not altered in transit | No signature, or the content was modified |   
| **DMARC** | What to do when SPF/DKIM fail, and that the visible From: matches the authenticated domain | Display-name spoofing is possible |   
   
*A * *dmarc=fail* * on a message claiming to be from a major brand is close to conclusive. Genuine banks, Microsoft, and Google all publish strict DMARC policies.*  
**3.3 Inspecting URLs safely**  
# Expand a shortened link without visiting it  
 curl -sI https://bit.ly/EXAMPLE | grep -i location  
   
 # Check who registered a suspicious domain and when  
 whois micros0ft-security.com | grep -Ei 'creation|registrar|country'  
   
A domain **registered within the last 30 days** is one of the strongest single phishing signals there is. Legitimate brands do not send account notices from week-old domains.  
Paste URLs into **urlscan.io** or  **VirusTotal** for a sandboxed screenshot and reputation check.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OQQmAABRAsSd4NIGRTPXNaQBrWMGbCFuCLTOzV2cAAPzFvVZbdXw9AQDgtesBhZQEOYZGgUEAAAAASUVORK5CYII=)  
**4. Phishing Indicators Reference**  
**4.1 Sender indicators**  
| | | |  
|-|-|-|  
| **Indicator** | **Example** | **Why it works** |   
| **Lookalike domain (typosquatting)** | paypaI.com (capital i), rnicrosoft.com (rn≈m) | The eye reads shape, not characters |   
| **Homograph / IDN attack** | аpple.com with a Cyrillic а | Visually identical in many fonts |   
| **Subdomain deception** | paypal.com.secure-login.xyz | Users read left to right and stop at "paypal.com" |   
| **Display-name spoofing** | "HR Department" <randomuser@gmail.com> | Mobile clients often show only the display name |   
| **Free-mail for corporate business** | hr-payroll-team@gmail.com | Real HR does not email from Gmail |   
| **Reply-To mismatch** | From a bank, Reply-To a Gmail address | Replies are harvested by the attacker |   
   
**4.2 Content and psychological indicators**  
| | | |  
|-|-|-|  
| **Lever** | **Typical phrasing** | **Countermeasure** |   
| **Urgency** | "Your account will be closed in 24 hours" | Attackers need you to act before you think. Any deadline is a reason to slow down. |   
| **Authority** | Sent as the CEO, IT admin, or a tax authority | Verify through a channel you chose, not one the email gave you |   
| **Fear** | "Suspicious login from Russia detected" | Go to the site directly and check the real security log |   
| **Greed / reward** | "You have received a refund of ₹14,850" | Unexpected money is always worth suspicion |   
| **Curiosity** | "Shared document: Q3_Salaries.xlsx" | Confirm with the supposed sender first |   
| **Generic greeting** | "Dear Valued Customer" | Real providers know your name |   
| **Confidentiality pressure** | "Keep this between us, I'm in a meeting" | This is the signature of Business Email Compromise |   
   
**4.3 Technical indicators**  
- Link text and link destination differ — displayed https://hdfcbank.com, actual http://45.32.x.x/hdfc/  
- HTTP rather than HTTPS on a login page  
- Attachments: .html, .htm, .iso, .img, .lnk, .js, .vbs, macro-enabled Office files (.docm, .xlsm), password-protected ZIPs (used to defeat scanning)  
- Double extensions: invoice.pdf.exe  
- Body rendered as a single image to evade text-based filters  
- Tracking pixels loading from unrelated domains  
- Mismatched or missing message-ID formatting  
- An email thread that appears to be a reply, but with no genuine prior message in your sent items  
**4.4 Modern indicators that break old training**  
Attackers now use LLMs and stolen templates, so grammar and formatting are frequently **perfect**. Train people on these instead:  
- **Thread hijacking** — a reply inserted into a real conversation from a compromised partner mailbox  
- **QR-code phishing (quishing)** — the payload is an image, invisible to URL scanners, opened on an unmanaged phone  
- **MFA fatigue** — repeated push notifications until the user approves one to make them stop  
- **Callback phishing (TOAD)** — no link at all, just a phone number and a fake invoice  
- **Trusted-platform abuse** — payloads hosted on legitimate SharePoint, Google Drive, Dropbox, or Notion links  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OMQ2AABAAsSPBCj7fFRYQwYwEZiywEZJWQZeZ2ao9AAD+4lyruzq+ngAA8Nr1AMTJBeJDClAyAAAAAElFTkSuQmCC)  
**5. Classification Framework**  
**5.1 Scoring**  
Score each email. Multiple weak signals together are as meaningful as one strong signal.  
| | |  
|-|-|  
| **Signal** | **Points** |   
| SPF, DKIM, or DMARC failure | 3 |   
| Sender domain registered < 30 days ago | 3 |   
| Link destination ≠ link text | 3 |   
| Credential-harvesting page requested | 3 |   
| Executable or macro-enabled attachment | 3 |   
| Lookalike / homograph domain | 2 |   
| Reply-To differs from From | 2 |   
| Urgency or threat language | 2 |   
| Unexpected financial or payment request | 2 |   
| Generic greeting | 1 |   
| Free-mail address for business matter | 1 |   
| Grammar/spelling errors | 1 |   
| Message is a single image | 1 |   
   
| | | |  
|-|-|-|  
| **Total** | **Classification** | **Action** |   
| **0–2** | ✅ **Safe** | Normal handling |   
| **3–5** | ⚠️ **Suspicious** | Do not interact. Verify sender through an independent channel. Report. |   
| **6+** | 🔴 **Phishing** | Report immediately, delete, block sender domain, check whether anyone interacted |   
   
**5.2 Analysis log**  
Use this table in your repo, one row per sample analysed.  
| | | | | | | | |  
|-|-|-|-|-|-|-|-|  
| **#** | **Subject** | **Claimed sender** | **Actual sender** | **Auth result** | **Key indicators** | **Score** | **Classification** |   
| 01 |   |   |   |   |   |   |   |   
| 02 |   |   |   |   |   |   |   |   
| 03 |   |   |   |   |   |   |   |   
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OQQmAABRAsScYxpg/h5VMYARvRrCCNxG2BFtmZquOAAD4i3Ot7mr/egIAwGvXA224BcUMk6pDAAAAAElFTkSuQmCC)  
**6. Worked Examples**  
*These are * ***illustrative constructions*** * written for this report, not real captured emails. Replace or supplement them with your own analysed samples and screenshots. All URLs are defanged.*  
**Sample 1 — Credential harvesting 🔴 Phishing**  
From: "Microsoft 365 Security" <security-alert@micros0ft-verify[.]com>  
 Reply-To: ms-recovery-team@gmail[.]com  
 Subject: Unusual sign-in activity — action required within 24 hours  
 Authentication-Results: spf=fail dkim=none dmarc=fail  
   
 Dear User,  
   
 We detected a sign-in to your account from Moscow, Russia.  
 If this was not you, verify your identity immediately or your  
 account will be permanently suspended.  
   
 [ Verify My Account ]   → hxxps://micros0ft-verify[.]com/login/auth.php  
   
**Analysis**  
| | | |  
|-|-|-|  
| **Check** | **Finding** | **Points** |   
| Domain | micros0ft-verify.com — zero substituted for "o", not a Microsoft domain | 2 |   
| Registration | Created 6 days before the email | 3 |   
| Authentication | SPF fail, DKIM none, DMARC fail | 3 |   
| Reply-To | Gmail address, unrelated to sender | 2 |   
| Greeting | "Dear User" — Microsoft uses your name | 1 |   
| Urgency | 24-hour suspension threat | 2 |   
| Destination | Credential form imitating the Microsoft login page | 3 |   
| **Total** |   | **16 — Phishing** |   
   
**Technique:** brand impersonation combined with fear and a deadline. The landing page is a pixel-accurate clone of login.microsoftonline.com, often proxying credentials in real time so it can also capture the MFA code.  
**Impact if successful:** full mailbox access, which enables thread hijacking against the victim's contacts, mailbox rules that hide the attacker's activity, and password resets on every service tied to that address.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhZscVjnidKEAGFtgISaugy8zs1RkAAH9xr9VWHV9PAAB47XoAor8EPg1yCpUAAAAASUVORK5CYII=)  
**Sample 2 — Business Email Compromise / CEO fraud 🔴 Phishing**  
From: "Rajesh Kumar (CEO)" <rajesh.kumar.ceo@outlook[.]com>  
 Subject: Re: Urgent — confidential  
   
 Hi,  
   
 Are you at your desk? I need you to process a payment to a new  
 supplier today. It's for an acquisition we haven't announced yet,  
 so please don't discuss it with anyone in the office.  
   
 Send me the bank form and I'll share the details.  
   
 Sent from my iPhone  
   
**Analysis**  
| | | |  
|-|-|-|  
| **Check** | **Finding** | **Points** |   
| Sender | CEO writing from a personal Outlook address | 1 |   
| Domain | Not the corporate domain | 2 |   
| Content | Unexpected payment request | 2 |   
| Pressure | Urgency plus explicit secrecy instruction | 2 |   
| Subject | "Re:" with no prior thread | 1 |   
| Style | No link, no attachment — evades every scanner | — |   
| **Total** |   | **8 — Phishing** |   
   
**Technique:** pure social engineering. There is nothing technically malicious for a filter to detect. The secrecy instruction is the giveaway — it exists to stop the victim walking over and asking.  
**Why this is the costliest category:** BEC consistently accounts for the largest financial losses of any email attack type, because payments are large, authorised, and difficult to reverse.  
**Control that actually stops it:** a mandatory out-of-band verification rule for any payment or banking-detail change, using a phone number from your own records. No exceptions for senior staff — seniority is precisely what the attack exploits.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANElEQVR4nO3OQQmAABRAsaeILbwZ9Fewo0Gs4E2ELcGWmTmqKwAA/uLeqr06v54AAPDa+gAthwNEfGhnhAAAAABJRU5ErkJggg==)  
**Sample 3 — Invoice with malicious attachment ⚠️ Suspicious → 🔴 Phishing**  
From: "Accounts — Sharma Traders" <billing@sharma-traders-invoices[.]net>  
 Subject: Invoice #INV-2024-8871 overdue  
 Attachment: Invoice_8871.pdf.htm  (48 KB)  
   
**Analysis**  
| | | |  
|-|-|-|  
| **Check** | **Finding** | **Points** |   
| Attachment | Double extension — it is an HTML file, not a PDF | 3 |   
| Type | .htm attachments commonly contain a local credential-harvesting form | 3 |   
| Domain | Lookalike of a known supplier, different TLD | 2 |   
| Pressure | "Overdue" implies existing liability | 2 |   
| **Total** |   | **10 — Phishing** |   
   
**Technique:** HTML smuggling. Because the form is inside the attachment rather than at a URL, no link scanner has anything to check. Opening it renders a local login page in the browser and posts the credentials to the attacker's server.  
**Safe inspection (in a VM):**  
file Invoice_8871.pdf.htm          # confirm real type  
 sha256sum Invoice_8871.pdf.htm     # hash → check on VirusTotal  
 grep -oE 'action="[^"]+"' Invoice_8871.pdf.htm   # find where the form posts  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNBCkJfE1pYGfHAiAU2QtIq6DIzW7UHAMBfnGt1V8fXEwAAXrse4dwF6o2O55YAAAAASUVORK5CYII=)  
**Sample 4 — Legitimate email ✅ Safe**  
From: "GitHub" <noreply@github.com>  
 Subject: [GitHub] A new SSH key was added to your account  
 Authentication-Results: spf=pass dkim=pass dmarc=pass (p=REJECT)  
   
 Hey [your-actual-username]!  
   
 An SSH key with fingerprint SHA256:... was added to your account.  
 If you didn't do this, visit https://github.com/settings/keys  
   
**Why this scores 0**  
- SPF, DKIM, and DMARC all pass on the genuine github.com domain  
- Addresses the user by their real username  
- Describes a specific, verifiable action the user just performed  
- Directs the user to a top-level path on the real domain rather than a deep custom link  
- No urgency, no threat, no credential request  
**Teaching point:** the correct response is still to navigate to github.com/settings/keys by typing it, not by clicking. The habit matters more than the verdict on any single email.  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhwgJuUPYDMpnRgQU2QtIq6DIze3UGAMBf3Gu1VcfXEwAAXrseaHEEM+cJoFcAAAAASUVORK5CYII=)  
**7. Prevention & Awareness Guidelines**  
**7.1 For every employee — the 4-step check**  
Pin this somewhere visible.  
1. **PAUSE** — does this email want me to act fast? Urgency is the attacker's main tool. Nothing legitimate breaks if you take ten minutes.  
2. **INSPECT** — hover over the sender and every link. Read the domain from  **right to left**: secure-login.xyz is the real domain in paypal.com.secure-login.xyz.  
3. **VERIFY** — for any request involving money, credentials, or data, confirm through a channel *you* choose. Call the number in your own contacts, not the one in the email.  
4. **REPORT** — forward to security@[company] or use the Report Phishing button.  **Report even when you are unsure, and especially if you already clicked.** Speed of reporting is what limits the damage.  
**7.2 Golden rules**  
| | |  
|-|-|  
| **Do** | **Don't** |   
| Type the website address yourself | Click links in unexpected emails |   
| Use a password manager (it won't autofill on a fake domain — a built-in warning) | Reuse passwords across sites |   
| Turn on phishing-resistant MFA (passkeys, FIDO2 security keys) | Approve MFA prompts you didn't trigger |   
| Verify payment changes by phone | Act on secrecy requests from "executives" |   
| Report and move on | Feel embarrassed — reporting fast is the win |   
   
**7.3 Technical controls for the organisation**  
| | | |  
|-|-|-|  
| **Control** | **What it does** | **Priority** |   
| **SPF, DKIM, DMARC at ** **p=reject** | Stops attackers spoofing your own domain | Critical |   
| **Phishing-resistant MFA** (FIDO2/passkeys) | Defeats credential theft and real-time proxy phishing | Critical |   
| **External-sender email banner** | Visually flags anything from outside the organisation | High |   
| **Attachment sandboxing** | Detonates files before delivery | High |   
| **Link rewriting / time-of-click protection** | Re-checks URLs when clicked, not just on delivery | High |   
| **One-click "Report Phishing" button** | Makes reporting the path of least resistance | High |   
| **Block risky attachment types** at the gateway | .exe .js .vbs .lnk .iso .htm | High |   
| **Payment verification policy** | Out-of-band confirmation for bank-detail changes | Critical |   
| **DNS filtering** | Blocks known phishing and newly-registered domains | Medium |   
| **Quarterly simulations + training** | Measures and maintains awareness | Medium |   
   
**7.4 Incident response — if someone clicks**  
| | |  
|-|-|  
| **Time** | **Action** |   
| **0–15 min** | Disconnect the device from the network. Do not power it off (preserves memory evidence). |   
| **15–30 min** | Reset the user's password from a clean device. Revoke all active sessions and OAuth tokens. |   
| **30–60 min** | Check the mailbox for attacker-created forwarding rules and filters — this is the most commonly missed step. |   
| **1–4 hrs** | Search mail logs for other recipients of the same campaign. Block the sender domain and URL. |   
| **4–24 hrs** | Review authentication logs for suspicious sign-ins. Scan the device. Check for data access or exfiltration. |   
| **24–72 hrs** | Assess breach-notification obligations (DPDP Act / GDPR). Document. Run a blameless post-incident review. |   
   
***Culture note:*** * an organisation that punishes people for clicking gets slower reporting and worse outcomes. The measurable goal is * *time to report* *, not * *click rate* *.*  
**7.5 Awareness one-pager (for distribution)**  
┌───────────────────────────────────────────────┐  
 │  BEFORE YOU CLICK — 10 SECONDS                │  
 ├───────────────────────────────────────────────┤  
 │  ⏱  Is it urgent?          → Slow down        │  
 │  👤 Do I know the sender?  → Check the domain │   
 │  🔗 Where does it go?      → Hover first      │   
 │  💰 Money or password?     → Call to confirm  │   
 │  🤔 Not sure?              → Report it        │   
 ├───────────────────────────────────────────────┤  
 │  Report: security@company.com                 │  
 │  No one is ever in trouble for reporting.     │  
 └───────────────────────────────────────────────┘  
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OYQ1AABSAwY8JoIGqr4Z6Eoiggn9mu0twy8wc1RkAAH9xbdVa7V9PAAB47X4A9C4EIsmYmgsAAAAASUVORK5CYII=)  
**8. Metrics to Track**  
| | | |  
|-|-|-|  
| **Metric** | **Why it matters** | **Target** |   
| **Median time to report** | Determines how much damage is preventable | < 5 minutes |   
| Report rate on simulations | Measures active participation, not just avoidance | > 70% |   
| Click rate on simulations | Useful only alongside report rate | < 5% |   
| Credential-submission rate | The genuinely dangerous outcome | ~0% |   
| Repeat clickers | Identifies who needs targeted support | Declining |   
| DMARC enforcement coverage | Protects your brand from being spoofed | 100% at p=reject |   
   
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANUlEQVR4nO3OMQ2AABAAsSNhwgJuUPYDMpnRgQU2QtIq6DIze3UGAMBf3Gu1VcfXEwAAXrseaHEEM+cJoFcAAAAASUVORK5CYII=)  
**9. References**  
- APWG Phishing Activity Trends Reports — apwg.org  
- FBI IC3 Annual Report (BEC loss figures) — ic3.gov  
- Verizon Data Breach Investigations Report — verizon.com/dbir  
- CISA Phishing Guidance — cisa.gov  
- MITRE ATT&CK: Phishing (T1566) — attack.mitre.org/techniques/T1566/  
- NIST SP 800-177 (Trustworthy Email) — nist.gov  
- DMARC deployment guidance — dmarc.org  
![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAnEAAAACCAYAAAA3pIp+AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAANklEQVR4nO3OQQmAABRAsSfYxZo/khWsYQLPJrCCNxG2BFtmZquOAAD4i3Ot7mr/egIAwGvXA4qjBdKlX6OKAAAAAElFTkSuQmCC)  
**Appendix — Repository structure**  
FUTURE_CS_02/  
 ├── README.md  
 ├── Phishing_Detection_Report.md  
 ├── Phishing_Detection_Report.pdf  
 ├── samples/  
 │   ├── sample_01_headers.txt      # redacted, URLs defanged  
 │   ├── sample_02_headers.txt  
 │   └── ANALYSIS_LOG.md  
 ├── evidence/  
 │   ├── header_analysis_01.png  
 │   ├── urlscan_result_01.png  
 │   └── virustotal_01.png  
 └── awareness/  
     ├── employee_one_pager.pdf  
     └── incident_response_checklist.md  
   
