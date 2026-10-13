# Planet Prestige — Email Analysis Report
This lab was from Blue Team Labs Online as part of MYDFIR's First Win Investigation.

## Background

CoCanDa, a planet known as 'The Heaven of the Universe' has been having a bad year. A series of riots have taken place across the planet due to the **frequent abduction of citizens**, known as CoCanDians, by a mysterious force. CoCanDa’s Planetary President arranged a war-room with the best brains and military leaders to work on a solution. After the meeting concluded **the President was informed his daughter had disappeared.** CoCanDa agents spread across multiple planets were working day and night to locate her. Two days later and there’s no update on the situation, no demand for ransom, not even a single clue regarding the whereabouts of the missing people. On the third day a CoCanDa representative, an Army Major on Earth, received an email.

## What We Know So Far...

- Ctizens were abducted
- President's daughter is missing
- Three days later, a ransom email was received

## Findings

- **Date and time:** Tuesday, 26 January 2021, 01:41:18 -0500 (EST)
- **Recipient:** `themajoronearth@gmail.com`
- **Claimed sender:** `billjobs@microapple.com`
- **Reply-To:** `negeja3921@pashter.com`
- **Email-related domain:** `emkei.cz`
- **Associated IP address:** `93.99.104.210` (`emkei.cz`)
- **Additional domain of interest:** `pashter.com`
- **Email file:** `A Hope To CoCandDa.eml`
- **Attachment / extracted content:** `PuzzleToCoCanDa.pdf` (identified as a ZIP archive rather than a genuine PDF), containing:
    - `DaughtersCrown.jpeg`
    - `GoodJobMajor.pdf`
    - `Money.xlsx`

The email and its attachments contain a ransom note, a crown image, instructions in a PDF, and an Excel file that reveals a declaration of war and the purported attacker location: **“The Martian Colony, Beside Interplanetary Spaceport.”**

## Investigation

The email was addressed to `themajoronearth@gmail.com`. Its displayed sender was `billjobs@microapple.com`, while the Reply-To address was `negeja3921@pashter.com`. This mismatch is suspicious because replies would be directed to a different domain than the one shown as the sender.

The raw email showed failed ARC authentication checks. These results add context to the assessment, but failed ARC checks alone do not prove that an email was spoofed or malicious. The sender/Reply-To discrepancy, message content, and attachments should be considered together. The investigation associates `emkei.cz` and IP address `93.99.104.210` with the use of an email-sending or spoofing service.

The email content was Base64-encoded. After decoding, the message included a ransom note addressed to **TheMajorOnEarth**. The note demanded **1 billion CoCanDs** for the return of abducted CoCanDians, including the Planetary President's daughter.

### Attachment analysis

The attachment named `PuzzleToCoCanDa.pdf` was identified as a ZIP archive rather than a genuine PDF. The archive was extracted into a folder containing `DaughtersCrown.jpeg`, `GoodJobMajor.pdf`, and `Money.xlsx`.

The file types were checked using tools such as `file` and `hexeditor` to check the magic-bytes against a file-signature identification service online. These checks helped validate the files' actual formats rather than relying on their names or extensions.

- **`DaughtersCrown.jpeg`:** Contains an image of a crown. In the context of the scenario—where citizens and the President's daughter have been abducted—the image can reasonably be interpreted as a symbolic indication that the attacker possesses something of exceptional value. It is analogous to a ransomware actor providing a sample or other proof-of-possession artifact.
- **`GoodJobMajor.pdf`:** Provides instructions directing the reader to the Excel file to find the payment/location information.
- **`Money.xlsx`:** Reveals a declaration of war and the purported attacker location: “The Martian Colony, Beside Interplanetary Spaceport.”

Using `exiftool` on `GoodJobMajor.pdf` revealed the author's name as **Pestero Negeja**. The surname resembles the name used in the Reply-To address (`negeja3921@pashter.com`). This is a potentially relevant correlation.

## WHO / WHAT / WHEN / WHERE / WHY / HOW

- **Who:** The email was addressed to `themajoronearth@gmail.com`. It displayed `billjobs@microapple.com` as the sender and used `negeja3921@pashter.com` as the Reply-To address. File metadata named the author as Pestero Negeja.
- **What:** The email contained a ransom note demanding 1 billion CoCanDs for the return of abducted CoCanDians, including the President's daughter. Its attachments included a crown image, a PDF with instructions, and an Excel file containing a declaration of war and a purported location.
- **When:** The email's reported date and time was Tuesday, 26 January 2021, at 01:41:18 -0500 (EST).
- **Where:** The email was addressed to `themajoronearth@gmail.com`. The investigation associated `emkei.cz` with IP address `93.99.104.210`, while `pashter.com` appeared in the Reply-To address. The Excel file stated the location “The Martian Colony, Beside Interplanetary Spaceport.”
- **Why:** The ransom note indicates an intent to demand payment in exchange for the return of abducted citizens. The declaration of war suggests a broader hostile or coercive objective in the story.
- **How:** The message used a different Reply-To address from its displayed sender, included Base64-encoded content, and carried an archive disguised with a `.pdf` extension. The extracted files led the recipient through a sequence of clues to the demand, declaration of war, and purported location.

## Cyber Kill Chain — Actions on Objectives

For purposes of this challenge, the fictional scenario can reasonably be mapped to **Actions on Objectives**.

The scenario explicitly establishes that CoCanDian citizens, including the Planetary President's daughter, have been abducted. In the challenge's analogy, these abductions represent the successful removal or acquisition of high-value targets, paralleling data exfiltration in a ransomware/extortion scenario. The crown image functions as a symbolic proof-of-possession artifact, while the ransom note communicates the demand.

The artifacts provided for the exercise are primarily email and attachment evidence, and no independent endpoint or network telemetry is available in this investigation to demonstrate digital data exfiltration.

## Recommendations for CoCanDa

1.  **Notify the President and establish incident leadership.** Provide a concise briefing on the ransom demand, the missing citizens, the email artifacts, and the information that remains unverified.
2.  **Coordinate the search and protect potential targets.** Raise readiness as appropriate, coordinate agencies responsible for locating the missing citizens, and preserve relevant communications and intelligence.
3.  **Investigate the abductions and the attacker's access.** Determine how the "mysterious force" gained entry to the planet without raising an alarm. Find  out what is known about the victims' disappearance, identify any common circumstances, and seek corroborating evidence for the attacker's claims.
4.  **Preserve and share relevant evidence securely.** Retain the original email, complete headers, attachments, decoded content, file hashes, and investigative notes so that other teams can independently examine them.

## Recommendations for a Real-World Ransomware / Extortion Scenario

The fictional story resembles a successful ransomware/extortion scenario, but in a real incident the presence of a ransom note would not, by itself, prove malware execution, data exfiltration, or the full scope of compromise without thorough investigation of prior events leading to delivery of the ransom note. The following actions would be appropriate as follow-up recommendations, subject to the evidence available.

1.  **Preserve evidence.** Retain the original email and complete headers, encoded content, attachments, file hashes, relevant mail-security records, and any available endpoint or network logs. Avoid modifying the original evidence.
2.  **Determine prior activity leading to the ransomware note.** Review available endpoint, email, and user-activity telemetry for evidence of file opening, process execution, malware activity, unauthorized access, encryption, persistence, or data transfer.
3.  **Conduct a targeted threat hunt if warranted.** If compromise is suspected or confirmed and relevant telemetry is available, search email, endpoint, identity, DNS, proxy, firewall, and file-server data for the listed indicators and related behaviors. Look for other recipients, suspicious processes, credential abuse, lateral movement, and possible data staging or outbound transfer. 
4.  **Perform forensic investigation where evidence is available.** If a host may have been compromised, preserve relevant logs and artifacts and examine them to reconstruct what happened, determine scope, and assess whether data was accessed, staged, encrypted, or transferred. The email alone cannot answer these questions.
5.  **Contain based on risk and evidence.** If compromise is reasonably suspected or confirmed, follow the organization's incident-response procedures to isolate affected systems where appropriate, disable or secure compromised accounts, revoke sessions, and prevent further spread. Coordinate containment with responders so that evidence is preserved.
6.  **Review and block confirmed indicators carefully.** Quarantine or remove malicious emails where possible and consider blocking confirmed malicious domains, addresses, hashes, or other indicators. Validate indicators before applying broad blocks, particularly where infrastructure may be shared; avoid treating every associated IP or domain as definitively malicious without context.
7.  **Escalate through the appropriate response channels.** Notify incident response, security leadership, legal/privacy teams, and relevant authorities as required. Any decision about ransom payment should be made by authorized leadership with appropriate legal and incident-response advice.
8.  **Eradicate and recover if compromise is confirmed.** Remove identified malware and persistence, remediate the root cause, reset affected credentials as appropriate, and restore systems from verified clean backups. Validate system integrity and monitor for recurrence before returning systems to normal operation.
9.  **Conduct a post-incident review.** Document the timeline, scope, root cause, response gaps, and lessons learned. Improve relevant controls such as email authentication and filtering, attachment analysis, endpoint detection, access controls, backup resilience, and user awareness.

## Conclusion

The provided artifacts support classifying the message as a malicious extortion-themed email within the Planet Prestige challenge. The strongest evidence is the combined context of the ransom note, the sender/Reply-To discrepancy, the encoded content, the archive disguised as a PDF, and the extracted files containing the crown image, payment-related instructions, and declaration of war.

The scenario supports interpreting the abductions as the fictional equivalent of successful exfiltration and the ransom note as the extortion communication. 
