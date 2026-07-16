# Security Policy

## Reporting

If you find a security issue in this repository (for example accidental secret
commits, RCE in tooling, or unsafe defaults), open a GitHub issue privately if
possible or contact the repo owner on GitHub ([binx-ux](https://github.com/binx-ux)).

Discord (community, not a formal security channel): https://discord.gg/3WsFqS9hTF

## Expectations

- Do not use this project to attack systems you do not own
- Do not submit PRs that embed live webhook URLs, API keys, mint secrets, or license servers
- Do not commit `.env` files or personal tokens
- Treat Roblox ToS risk as a user responsibility; document high-risk features clearly
- Offset data from Theo is third-party; report API abuse / credential leaks in *this* repo only if we hard-coded secrets (we should not)
