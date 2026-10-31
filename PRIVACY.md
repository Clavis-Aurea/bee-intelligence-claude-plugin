# Privacy Policy — Bee Intelligence plugin


Last updated: 2026-10-31

## 1. Who we are
The Bee Intelligence plugin ("the Plugin") is provided by **Clavis Aurea** ("we", "us"), contact: cuong.nguyen@clavis-aurea.io. The Plugin connects Claude Code to the Bee Intelligence / Beetrack service ("the Service") operated at `https://<site>.beetrack.vn`, where `<site>` is the site name entered by the user. Each official Beetrack / Bee Intelligence customer has its own dedicated site (its own subdomain and its own data), and only that customer's authorized users can sign in and use the MCP server of that site. The Plugin gives no access to the data of any other customer.

## 2. What the Plugin does and does not do
- The Plugin consists of agents, skills and scripts that run **locally** in the user's Claude Code environment.
- The Plugin itself does **not** send data to us or to any third party other than the user's own Beetrack site and the Claude service the user is already using.
- The Plugin does **not** store credentials. Authentication uses OAuth: the user signs in with their Beetrack account in the browser, and the tools available depend on that user's permissions.

## 3. Data processed
| Category | Examples | Where it goes |
|---|---|---|
| Account data | Beetrack account identity and OAuth session | Beetrack site of the user (handled by the Service) |
| Business data | Hotel revenue, P&L, forecasts, reports and other data the user queries | Read from the user's Beetrack site via MCP; shown to the user in Claude Code |
| Uploaded files | Files the user asks to upload with the `bee-upload-file` skill | Sent to the user's Beetrack site (organization folder) |
| Local preferences | `SOUL.md`, `USER.md`, `.bee/bee_config.json`, saved decisions/norms | Stored on the user's machine (or in Claude's memory, if the user chooses) |

Content returned by the Service is also processed by the AI model of the user's Claude subscription, according to the terms and privacy policy of that provider ([Anthropic](https://www.anthropic.com/legal/privacy)).

## 4. How data is used
Data is used only to perform the tasks the user requests (answering questions, producing analyses, uploading files). We do not sell personal data and do not use it for advertising.

## 5. Data controller and processor roles
For business data held in the Service, the customer organization that owns the Beetrack site is the data controller and CTY TNHH CLAVIS AUREA acts as data processor, under the customer agreement or data processing agreement in force. Details of retention, hosting location and security of the Service are governed by that agreement and the Service's own policy.

## 6. Retention
The Plugin keeps no data of its own. Data in the Service is retained per the customer's agreement and settings. Local files can be deleted by the user at any time (plugin data folder `~/.claude/plugins/data/<id>/` and project `.bee/` folder).

## 7. Security
Connections to the Service use HTTPS. Access is controlled by OAuth and by the permissions of the signed-in user.

## 8. Sharing
We do not share data with any third party. Data is accessible only to the customer organization that owns it and its authorized users. The only exception is disclosure to competent authorities where the law requires it.

## 9. Your rights
Depending on applicable law (e.g. Vietnam Decree 13/2023/ND-CP on personal data protection, GDPR), you may request access, correction, deletion, restriction or portability of your personal data, and withdraw consent. Contact cuong.nguyen@clavis-aurea.io. Requests about business data should go to your organization's administrator first.

## 10. Children
The Plugin is intended for business use and is not directed at children.

## 11. Changes
We may update this policy; the "Last updated" date shows the latest version.

## 12. Contact
CTY TNHH CLAVIS AUREA, Số 2, đường Phùng Khắc Khoan, Phường Sài Gòn, TP Hồ Chí Minh, Việt Nam - cuong.nguyen@clavis-aurea.io
