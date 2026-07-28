# DeMI-SRO Consortium Digital Governance and Membership Rules

## 1. Elimination of Bureaucratic Friction
1.1. To optimize operational workflows, reduce administrative overhead, and ensure cross-border adaptability across South and Southeast Asian markets, the DeMI-SRO Consortium explicitly waives any requirements for physical paperwork, traditional corporate seals, or paper-based application forms regarding membership management.

## 2. Legal Nature of Git Operations as Expression of Intent
2.1. The act of pushing an internal branch and creating a **Pull Request (PR)** within the master `demi-sro` repository is recognized by all parties as a direct, explicit, and legally binding expression of intent (action by conduct) to alter the Participant's legal standing within the Consortium.

2.2. **Linear Audit Trail Requirement:** To preserve the integrity, immutability, and chronological continuity of the membership ledger, the use of external repository forks is strictly prohibited. All corporate governance actions MUST occur sequentially within the single, master repository ledger.

2.3. **Joining the Consortium:** The creation of a PR by a prospective Participant containing edits to the `PARTICIPANTS.json` file (specifically appending its verified corporate credentials to the registry) constitutes a formal execution and acceptance of the Consortium Adhesion Agreement. The intent to join is established upon PR submission. The official legal inclusion of the Participant into the SRO is finalized upon the approval and merging (Merge) of said PR by the Consortium Founders (**Sole Proprietor Shubralov E.A.** and **"AI Cybersecurity" LLC**).

2.4. **Exiting the Consortium:** The creation of a PR by a Participant that removes its credentials from the `PARTICIPANTS.json` file (or updates its operational status to `Withdrawn`) constitutes an official, necessary, and sufficient notification of unilateral termination of its Consortium membership. This PR shall be unconditionally merged by the Founders within 24 hours of submission and requires no additional legal justifications or paper notices.

2.5. **Contributor Status as a Legal Fact:** The merging of a Participant's Pull Request automatically upgrades their standing to an official Repository Contributor in the master interface. This status is public, immutable, and recognized by all parties as continuous proof of active participation within the SRO, as well as binding consent to the joint ownership and development of technical specifications under the IETF Trust guidelines.

## 3. Cryptographic Verification
3.1. Every commit within a submitted branch for membership modification **MUST** be cryptographically signed using a valid PGP or SSH key authorized by the Participant. 

3.2. A verified commit signature is legally equivalent to an electronic signature under applicable international trade standards and confirms the authenticity, origin, and non-repudiation of the entity's intent. Anonymous or unsigned commits modifying the registry shall be automatically rejected by the pre-commit CI/CD protocols.

## 4. Institution of Sponsorship and Open-Source Customs
4.1. In accordance with established international customs of open-source and financial ecosystems, the DeMI-SRO Consortium supports voluntary sponsorship. 

4.2. Financial or infrastructural sponsorship does not grant the right to unilaterally alter the protocol's core architecture against the SRO engineering consensus, but serves as a recognized mechanism for sustaining infrastructural costs (e.g., bare-metal server allocations for Embassy Nodes, funding satellite uplinks, smart contract security audits).

4.3. Participants providing continuous support shall receive the official status of Sponsor / Partner (Core Infrastructure, Platinum, or Gold), with mandatory public recognition within the `PARTICIPANTS.json` registry file and the repository's main interface.

## 5. Liability and Extraterritoriality
5.1. The Consortium does not constitute a separate legal entity under any singular national jurisdiction and operates strictly as an extraterritorial, decentralized alliance governed by mathematical algorithms and smart contracts deployed on the Ethereum Layer 1 blockchain.

5.2. No Participant shall be liable for the financial, regulatory, or legal obligations of another Participant. Any disputes arising from the technical execution of on-chain operations shall be resolved exclusively by the automated code logic ("Code is Law") of the compensation pool smart contract.
