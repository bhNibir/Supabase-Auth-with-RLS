# Secure Notes

This is a simple demo app with Supabase Auth, forget password and save note with RLS. It is a minimal, secure notes app with user authentication and row-level security where each user can only see and manage their own notes.

## Tech Stack

- **Next.js 16** (App Router) — React framework
- **TypeScript** — type safety
- **Tailwind CSS** — styling (styled in Supabase green theme)
- **Supabase** — auth + Postgres database + row-level security (RLS)

## Features

- Email + password sign up / sign in / sign out
- Forgot password request & password reset flow
- Create, view, and delete notes
- Row-level security: users can only access their own notes
- Loading states and error messages throughout
- Dark-themed modern UI

## Getting Started

### 1. Clone and install

```bash
git clone https://github.com/bhNibir/Supabase-Auth-with-RLS.git
cd Supabase-Auth-with-RLS
npm install
```

### 2. Set up Supabase

1. Create a project at [supabase.com](https://supabase.com)
2. In the SQL Editor, create the `notes` table and RLS policies:

```sql
CREATE TABLE notes (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  user_id UUID REFERENCES auth.users(id) NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT now()
);

ALTER TABLE notes ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users can view their own notes"
  ON notes FOR SELECT
  USING (auth.uid() = user_id);

CREATE POLICY "Users can create their own notes"
  ON notes FOR INSERT
  WITH CHECK (auth.uid() = user_id);

CREATE POLICY "Users can delete their own notes"
  ON notes FOR DELETE
  USING (auth.uid() = user_id);
```

### 3. Configure environment variables

Create a `.env.local` file in the project root:

```
NEXT_PUBLIC_SUPABASE_URL=your_project_url
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=publishable-key
```

You can find these values in your Supabase dashboard under **Settings → API**.

### 4. Run locally

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Project Structure

```
.
├── src/
│   ├── app/
│   │   ├── favicon.ico
│   │   ├── globals.css
│   │   ├── layout.tsx              # Root layout
│   │   ├── page.tsx                # Redirects to /login
│   │   ├── login/
│   │   │   └── page.tsx            # Auth page (with Demo login)
│   │   ├── forgot-password/
│   │   │   └── page.tsx            # Forgot password request page
│   │   ├── reset-password/
│   │   │   └── page.tsx            # Reset password confirmation page
│   │   └── notes/
│   │       └── page.tsx            # Notes CRUD page
│   ├── lib/
│   │   └── supabaseClient.ts      # Supabase client initialization
│   └── services/
│       ├── auth.ts                 # Auth service layer (signIn, signUp, signOut, etc.)
│       └── notes.ts                # Notes service layer (listNotes, createNote, deleteNote)
├── public/
│   └── (static assets)
├── .env.example                # Example environment variables
├── eslint.config.mjs           # ESLint configuration
├── next.config.ts              # Next.js configuration
├── package.json                # Project dependencies and scripts
├── postcss.config.mjs          # PostCSS configuration
├── tsconfig.json               # TypeScript configuration
└── README.md                   # Documentation
```

> **Note:** The UI never calls Supabase directly — all data access goes through the service layer in `src/services/`.

## Deploy to Vercel

1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → **New Project** → import the repo
3. Add the same env vars (`NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY`) in the Vercel project settings
4. Deploy

