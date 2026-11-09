# Myron Johnson — Azure Engineering Portfolio

A Next.js App Router / TypeScript portfolio with project case studies, a searchable learning dashboard, credential history, and a Supabase-backed private coursework editor.

## Current implementation

- Responsive homepage, navigation, projects listing, four detailed project blueprints, experience/contact page, learning dashboard and admin route.
- Nine **screenshot-confirmed** historical Microsoft Learn module entries in `lib/content.ts` (2022), and an accurate historical AZ-104 record: passed September 3, 2022; Azure Administrator Associate certification expired September 3, 2023.
- Project labs are marked **Planned**. Do not change their status to Completed until they have been performed, validated and documented.
- Learning search and category filters work without a database.
- When Supabase is connected, publicly **published** coursework appears in the dashboard; a permitted administrator can add, publish/unpublish and delete database entries.
- The secure database controls access. There is intentionally no hard-coded administrator password, automatic public signup, or service-role key in the application.

## Local setup

Requires Node.js 20+ and npm. From this folder:

```bash
npm install
cp .env.example .env.local
npm run dev
```

Open http://localhost:3000. With no environment configuration the public pages work with seed records, while `/admin` displays setup instructions.

## Supabase setup for the editor

1. Create a Supabase project you own. In its SQL editor, execute `supabase/schema.sql` once on a fresh project. **Review schema changes before using it in an existing production database.**
2. In Authentication > Users, manually create/invite your **own** administrative user and set a strong password. In Authentication settings, disable open user signup if a public signup flow is not required. Enable MFA for your admin account where available.
3. Locate that user's UUID. Execute (with your actual UUID):

   ```sql
   insert into public.portfolio_admins (user_id)
   values ('YOUR-AUTH-USER-UUID');
   ```

4. Copy the project URL and **anon/publishable public key** into `.env.local`:

   ```dotenv
   NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
   NEXT_PUBLIC_SUPABASE_ANON_KEY=your-public-key
   ```

5. Restart the app. Sign into `/admin` using the invited admin account. Add coursework. Drafts remain private; only published records appear in the public dashboard.
6. Verify policies for anonymous and non-admin accounts, plus insert/update/delete for the admin, before production. See the current Supabase RLS guide at https://supabase.com/docs/guides/database/postgres/row-level-security.

**Never put a Supabase service-role/secret key into `NEXT_PUBLIC_*` variables.** RLS, not hiding the admin route, is the access boundary. Authentication is used only by the administrator; visitors do not need accounts.

## Public profile configuration

Set only verified destinations:

```dotenv
NEXT_PUBLIC_CONTACT_EMAIL=
NEXT_PUBLIC_LINKEDIN_URL=
NEXT_PUBLIC_GITHUB_URL=
NEXT_PUBLIC_RESUME_URL=
```

Unconfigured links are hidden to avoid dead ends. Add a public resume PDF hosted on a verified URL (or place it in `/public` and set `/resume.pdf`) after review. Do not publish personal phone numbers, confidential employer tickets, tenant IDs, credentials or sensitive customer details.

## Deployment

Deploy the GitHub repository to Vercel using the Next.js framework preset. Configure the same environment variables in Vercel Project Settings, deploy, then review all pages on desktop/mobile. The portfolio and coursework history are public; the admin route requires an authenticated account on the `portfolio_admins` allowlist.

For GitHub integration, create a repository only after reviewing contents. No deployment, repo, DNS or account configuration has been carried out by this source package.

## Editing / extending

- `lib/content.ts`: public profile skill chips, nine historical seed records, and project lab blueprints.
- `app/`: pages and metadata.
- `components/learning-dashboard.tsx`: search/filter, published database records.
- `components/admin-panel.tsx`: authenticated coursework management.
- `supabase/schema.sql`: schema, database grants, RLS policies.
- `app/globals.css`: theme and responsive styling.

Next improvements: admin editing for existing coursework, project CRUD, screenshots and architecture diagrams, GitHub repository links per project, proof links, JSON-LD SEO, automated tests, accessibility audit, and a user-owned contact mechanism. Seed records currently coexist with live entries using a title-based de-duplication rule.

## Verification status

Source files were created in this workspace. `npm install` could not be completed in the authoring environment because npm registry access timed out; therefore **a successful runtime/build test has not been claimed**. Run `npm install`, `npm run typecheck`, and `npm run build` in your local/CI environment before deployment.
