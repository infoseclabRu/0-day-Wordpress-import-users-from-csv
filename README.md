# Import Users from CSV <= 1.3.1 - Privilege Escalation (0-day)

**Status:** 0-day, no patch available  
**Type:** Missing Authorization / Improper Privilege Management  
**CWE:** CWE-862, CWE-269  
**CVSS v3.1:** 7.2 (High)  
**Vector:** `CVSS:3.1/AV:N/AC:L/PR:H/UI:N/S:U/C:H/I:H/A:H`

## Credits

Research by **infoseclab.ru** — a team of Russian cybersecurity practitioners

**Contact:**
- Website: https://infoseclab.ru
- Telegram: https://t.me/infoseclab.ru
---

## Summary
<img width="1600" height="766" alt="image" src="https://github.com/user-attachments/assets/2e05d907-5045-4298-a452-5f901719b05a" />

A privilege escalation vulnerability has been discovered in the WordPress plugin **Import Users from CSV** (versions **≤ 1.3.1**).

A user with a role that only has the `create_users` capability (not an administrator) can escalate privileges to WordPress site administrator.

The vendor was notified 3 months ago, no response was received.  
A CVE request was submitted to **Wordfence Threat Intelligence**.

---

## Description

The plugin allows importing users from a CSV file.  
During import, it assigns roles via the `role` column and arbitrary user meta via any CSV columns — including the sensitive `wp_capabilities` meta key.

Due to missing authorization checks, a user with a role that only has `create_users` can:
<img width="1113" height="597" alt="image" src="https://github.com/user-attachments/assets/9f8c605a-6726-44fa-96c2-975a28b96915" />

1. Create a new administrator by specifying `role=administrator` in the CSV.
2. Overwrite the capabilities of an existing user, including user ID 1, by injecting a `wp_capabilities` meta value.

This leads to full site compromise, including arbitrary PHP code execution via the theme/plugin editor, if it is not disabled.

---

## Affected Versions

- Import Users from CSV **≤ 1.3.1**

## Patched Versions

- None. No patch available.

---

## Impact

- Privilege escalation to administrator.
- Full compromise of the WordPress site.
- Possible arbitrary PHP code execution via the theme/plugin editor.
- Compromise of users, data, and site configuration.

---

## CVSS

**CVSS v3.1:** 7.2 (High)  
**Vector:** `CVSS:3.1/AV:N/AC:L/PR:H/UI:N/S:U/C:H/I:H/A:H`

---

## CWE

- **CWE-862:** Missing Authorization
- **CWE-269:** Improper Privilege Management

---

## Workarounds

Until a patch is available:

- Remove or disable the **Import Users from CSV** plugin.
- Restrict the `create_users` capability to trusted administrators only.
- Do not allow CSV imports from untrusted users.
- Audit users with the `administrator` role.
- Check user meta for suspicious `wp_capabilities` entries.
- Enable logging of role and capability changes.

---

## Recommendations for Developers

To fix the issue:

- Check `current_user_can('promote_users')` and `current_user_can('edit_user', $user_id)` during import.
- Prevent assigning a role higher than the current user's role.
- Use a whitelist of allowed CSV columns.
- Block importing protected meta keys:
  - `wp_capabilities`
  - `wp_user_level`
  - `session_tokens`
  - and other internal WordPress keys.
- Validate every user change through capability checks.

---

## Detection

Look for:

- New users with the `administrator` role.
- Changes to the `wp_capabilities` meta key on existing users.
- CSV imports performed by users without administrator rights.
- Unusual activity around user import pages.
---

## References

- Wordfence Threat Intelligence — CVE request submitted.
- NVD — CVE request submitted.

---

## Disclaimer

This advisory is published for defensive and informational purposes.  
No PoC is provided. The information is intended for administrators, developers, and security professionals.
