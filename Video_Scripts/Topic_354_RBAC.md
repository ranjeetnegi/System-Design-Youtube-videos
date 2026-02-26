# RBAC: Role-Based Access Control

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Imagine a hospital. Hundreds of staff. Doctors, nurses, janitors, admins. You don't give every person a custom list of "you can do X, Y, Z." That would be chaos. Instead: doctors get doctor permissions. Nurses get nurse permissions. Janitors get janitor permissions. One role, one set of rules. Add a new doctor? Assign the doctor role. Done. **Role-Based Access Control**—RBAC—is exactly that. Users get roles. Roles get permissions. You manage roles, not thousands of individual permission lists.

---

## The Story

In any system, you need to control who can do what. Read this file? Delete that record? Deploy to production? You could assign **permissions** directly to each user. User A: read, write. User B: read only. User C: admin. Works for 5 users. Nightmare for 500. Every hire, every role change—you're editing dozens of permission assignments. One typo, one missed update, and you've got a security hole or a locked-out employee.

**RBAC** flips it. You define **roles**: admin, editor, viewer. Each role has a set of **permissions**. Users get assigned roles. That's it. User joins? Assign "editor." User leaves? Remove the role. Change what editors can do? Update the role once. Every editor gets the new permissions. **Principle of least privilege** fits naturally: give users the minimum role they need. Viewer for read-only. Editor for content changes. Admin only when they actually need it.

**Common roles:** **Admin**—full access, user management, config changes. **Editor**—create, update, delete content. **Viewer**—read only. **Guest**—limited read, maybe nothing. The exact names vary. The idea doesn't: roles are reusable permission bundles. Users → roles → permissions. Three layers. Clean.

---

## Another Way to See It

A library. You don't give each patron a custom list of "you can check out 3 books, use the computer for 1 hour, access the archives." You have membership tiers: child, adult, researcher. Each tier has rules. Child: 5 books, no archives. Adult: 10 books, 2 hours computer. Researcher: unlimited, full archives. New patron? Assign a tier. Change the rules for researchers? Update the tier. Everyone with that tier gets the change. Roles = membership tiers. Permissions = the rules. Same model.

---

## Connecting to Software

- **RBAC model:** Users → Roles → Permissions. You assign roles to users. Roles define what actions are allowed. **Middleware** or **authorization checks** run on every request: "Does this user's role include permission X?" Yes → proceed. No → 403.
- **Permission tables:** Often stored as `user_roles` (user_id, role_id) and `role_permissions` (role_id, permission). Or in code: `if (user.hasRole('admin') || user.hasPermission('delete_post'))`.
- **RBAC vs ABAC:** **Attribute-Based Access Control** uses attributes—user department, resource owner, time of day, IP. "Allow if user.department == resource.department AND time < 5pm." More flexible, more complex. RBAC is simpler. ABAC for fine-grained, policy-heavy systems. RBAC for most apps.
- **Real tools:** **AWS IAM**—roles and policies. **Google Workspace**—admin, editor, viewer. **GitHub**—repo permissions: read, write, admin. **Kubernetes**—RBAC for API access. **Auth0**, **Okta**—role management built in.

---

## Let's Walk Through the Diagram

```
    RBAC: Users → Roles → Permissions

    ┌─────────┐     ┌─────────┐     ┌──────────────────┐
    │  Users  │     │  Roles  │     │   Permissions     │
    ├─────────┤     ├─────────┤     ├──────────────────┤
    │ Alice   │────▶│ Admin   │────▶│ create, read,    │
    │ Bob     │────▶│ Editor  │     │ update, delete,  │
    │ Carol   │────▶│ Viewer  │     │ manage_users     │
    └─────────┘     └─────────┘     └──────────────────┘

    Request: Bob tries to DELETE /posts/123
         │
         ▼
    Middleware: Does Bob's role (Editor) include "delete"?
         │
    ┌────┴────┐
    │         │
   YES       NO
    │         │
    ▼         ▼
  Allow    403 Forbidden
```

**Flow:** User makes a request. Middleware checks: what roles does this user have? What permissions do those roles grant? If the action is permitted, allow. Otherwise, deny. One check. One place. Roles are the bridge.

---

## Real-World Examples (2-3)

**Example 1 — AWS IAM:** You don't give a developer 47 individual permissions. You create a role: `DeveloperRole`. Attach policies: read S3, write to Lambda, no delete on RDS. Assign the role to the developer. New developer? Same role. Change what developers can do? Edit the role. Millions of AWS accounts use this. Roles scale.

**Example 2 — Google Workspace:** Shared drive. Owner, Content manager, Commenter, Viewer. Four roles. Each has clear permissions. Add someone? Pick a role. No custom "you can do X but not Y" for 200 people. Simple. Predictable.

**Example 3 — GitHub:** Repo permissions. Read: pull, view. Write: push, create branches. Admin: settings, delete repo. Organization-level roles: Member, Owner. Repo-level: per-repo role. Combine them. One user, multiple roles across repos. RBAC with hierarchy.

---

## Let's Think Together

Your app has 100 users. You need to add a new permission: "export data." Only 30 users should have it. Do you add it to 30 user profiles, or create a role?

Pause and think.

Create a role. "Data Exporter." Assign it to those 30 users. One role, one place to manage. Tomorrow you need to revoke it for 5 people? Remove the role from 5 users. The other 25 unchanged. If you'd assigned the permission to 30 users directly, you'd be hunting through 30 profiles. Roles win. Every time.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup gave everyone the "admin" role. "Easier to debug." "We're a small team." Six months later, a junior dev accidentally ran a script that deleted the production database. They had admin. No one had thought to restrict it. Recovery took 12 hours. Lost a day of data. The CEO asked: "Why did a junior dev have delete-all access?" No good answer. Lesson: principle of least privilege isn't bureaucracy. It's survival. RBAC only works if you use it. Admin for admins. Viewer for most. Audit your roles. Before disaster does it for you.

---

## Surprising Truth / Fun Fact

RBAC was formalized in 1992 by NIST. Thirty years later, it's still the dominant model. ABAC exists. Policy engines exist. But for most systems, roles are enough. Simple. Auditable. "Who has admin?"—one query. "What can editors do?"—one lookup. Complexity has its place. But RBAC proves: sometimes the old, simple idea wins.

---

## Quick Recap (5 bullets)

- **RBAC** = Users → Roles → Permissions. Assign roles, not individual permissions.
- **Common roles:** Admin (full), Editor (create/update/delete), Viewer (read only). Names vary; the pattern doesn't.
- **Principle of least privilege:** Give users the minimum role they need. Admin only when necessary.
- **RBAC vs ABAC:** RBAC = role-based. ABAC = attribute-based (department, time, resource). RBAC simpler; ABAC more flexible.
- **Middleware checks** permissions on every request. Store roles and permissions in DB or config. Tools: AWS IAM, GitHub, Kubernetes RBAC.

---

## One-Liner to Remember

> RBAC: Users get roles. Roles get permissions. You manage roles—not thousands of individual permission lists.

---

## Next Video

Your messages are flowing through the queue. But what happens when a message fails? And fails again? And keeps failing? Where does it go? That's the **dead letter queue**. That's next.
