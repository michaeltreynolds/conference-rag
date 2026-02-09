# Supabase RAG Starter Template - Setup Guide

## Overview
This is a starter template for building a Retrieval Augmented Generation (RAG) application using Supabase, OpenAI, and vanilla JavaScript. The app allows users to ask questions about conference talks using semantic search and AI-generated answers.

## Prerequisites
- A Supabase account (free tier is sufficient)
- Basic knowledge of HTML/CSS/JavaScript
- Git and GitHub account (for deployment)

## Quick Start: Using the Template Repository

### Option A: Use the GitHub Template (Recommended - Fastest!)

1. **Create your repo from the template**:
   - Go to the template repository (provided by your instructor)
   - Click the green **"Use this template"** button
   - Select **"Create a new repository"**
   - Name it: `conference-rag` (or your choice)
   - Make it **Public**
   - Click **"Create repository"**

2. **Clone your new repo**:
   ```bash
   git clone https://github.com/YOUR-USERNAME/conference-rag.git
   cd conference-rag
   ```

3. **You're ready!** Skip to [Part 1: Supabase Setup](#part-1-supabase-setup-15-20-minutes)

### Option B: Manual Setup (If not using template)

If you're not using the template repository, you'll need to manually create your repo and copy the files. Follow all steps below.

---

## Setup Instructions

### Part 1: Supabase Setup (15-20 minutes)

#### 1.1 Create a Supabase Project
1. Go to [supabase.com](https://supabase.com) and sign up/login
2. Click "New Project"
3. Fill in:
   - Project name: `conference-rag` (or your choice)
   - Database password: (save this somewhere safe)
   - Region: Choose closest to you
4. Click "Create new project" and wait 2-3 minutes

#### 1.2 Get Your API Credentials
1. In your Supabase dashboard, click "Settings" (gear icon)
2. Click "API" in the sidebar
3. Copy these two values:
   - **Project URL** (e.g., `https://xxxxx.supabase.co`)
   - **anon public** key (the long string under "Project API keys")
4. Save these for later

#### 1.3 Create the Database Schema
1. In Supabase dashboard, click "SQL Editor"
2. Click "New Query"
3. Copy and paste the following SQL:

```sql
-- Enable the pgvector extension
CREATE EXTENSION IF NOT EXISTS vector;

-- Create the documents table
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,
    content TEXT NOT NULL,
    metadata JSONB,
    embedding vector(1536),  -- OpenAI text-embedding-3-small dimension
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Create an index for faster similarity search
CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops)
WITH (lists = 100);

-- Create the search function
CREATE OR REPLACE FUNCTION search_documents(
    query_embedding vector(1536),
    match_count INT DEFAULT 5
)
RETURNS TABLE (
    id BIGINT,
    content TEXT,
    metadata JSONB,
    similarity FLOAT
)
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN QUERY
    SELECT
        documents.id,
        documents.content,
        documents.metadata,
        1 - (documents.embedding <=> query_embedding) AS similarity
    FROM documents
    ORDER BY documents.embedding <=> query_embedding
    LIMIT match_count;
END;
$$;
```

4. Click "Run" to execute the query

#### 1.4 Set Up Row Level Security (RLS)

**IMPORTANT**: This is a key learning objective! Students will need to implement RLS policies.

1. In SQL Editor, create a new query with:

```sql
-- Enable RLS on the documents table
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- TODO: Students need to create policies here
-- Policy 1: Allow authenticated users to read all documents
-- Policy 2: (Optional) Allow specific users to insert documents

-- Example policy (students should implement this):
-- CREATE POLICY "Allow authenticated users to read documents"
--     ON documents FOR SELECT
--     TO authenticated
--     USING (true);
```

#### 1.5 Configure Authentication
1. In Supabase dashboard, click "Authentication" → "Providers"
2. Ensure "Email" is enabled
3. Scroll down to "Email Templates"
4. Customize the magic link email if desired

#### 1.6 Configure Redirect URLs (CRITICAL!)
**This step is required for magic link authentication to work!**

1. In Supabase dashboard, click "Authentication" → "URL Configuration"
2. Find the "Redirect URLs" section
3. Add the following URLs (one per line):
   ```
   http://localhost:3000
   http://localhost:5500
   http://127.0.0.1:5500
   https://YOUR-USERNAME.github.io/conference-rag
   ```
   
   **Important**: 
   - Replace `YOUR-USERNAME` with your actual GitHub username
   - Replace `conference-rag` with your actual repository name
   - Add any other URLs where you'll host the app (custom domains, etc.)
   
4. Click "Save"

**Why this matters**: When users click the magic link in their email, Supabase redirects them back to your app. For security, Supabase only allows redirects to URLs in this whitelist. If your deployment URL isn't listed, the magic link will fail!

**Testing locally?** Make sure to add your local development server URL (e.g., `http://localhost:5500` if using Live Server in VS Code).

### Part 2: Configure the Application (5 minutes)

#### 2.1 Update config.js
1. Open `config.js` in your code editor
2. Replace the placeholder values:

```javascript
const SUPABASE_CONFIG = {
    url: 'https://YOUR-PROJECT.supabase.co',  // Your Project URL
    anonKey: 'your-anon-key-here'              // Your anon public key
};
```

### Part 3: Deploy to GitHub Pages (5-10 minutes)

**Note**: If you used the template repository (Option A), you already have a repo! Skip to step 3.2.

#### 3.1 Create a GitHub Repository (Skip if using template)
1. Go to [github.com](https://github.com) and login
2. Click "New repository"
3. Name it: `conference-rag` (or your choice)
4. Make it **Public**
5. Don't initialize with README
6. Click "Create repository"

#### 3.2 Push Your Code
```bash
# In your project directory
git add config.js  # Add your configured file
git commit -m "Configure Supabase credentials"
git push
```

#### 3.3 Enable GitHub Pages

**Choose one of these methods:**

##### Method A: GitHub Actions (Recommended - Automatic Deployment)

If your template includes `.github/workflows/deploy.yml`:

1. In your GitHub repository, click **"Settings"**
2. Click **"Pages"** in the sidebar
3. Under "Build and deployment":
   - **Source**: Select **"GitHub Actions"**
4. That's it! The workflow will automatically deploy on every push

**To verify deployment**:
- Go to the **"Actions"** tab in your repo
- **If you see a banner saying "Workflows aren't being run"**, click **"I understand my workflows, go ahead and enable them"**
- You should see a "Deploy to GitHub Pages" workflow running (if not, push a commit to trigger it)
- When it completes (green checkmark), your site is live!

##### Method B: Manual Deployment (If no workflow file)

1. In your GitHub repository, click "Settings"
2. Click "Pages" in the sidebar
3. Under "Source", select "Deploy from a branch"
4. Branch: **main**, Folder: **/ (root)**
5. Click "Save"
6. Wait 1-2 minutes for deployment

#### 3.4 Visit Your Site

Your site will be available at:
```
https://YOUR-USERNAME.github.io/conference-rag/
```

**Note**: First deployment may take 2-3 minutes. Subsequent deployments (with GitHub Actions) take ~1 minute.

### Part 4: Load Sample Data (Students implement this)

Students will need to:
1. Run the provided scraper to get conference talk data
2. Generate embeddings using OpenAI
3. Insert the data into Supabase

See `DATA_LOADING.md` for detailed instructions.

### Part 5: Test the Application

1. Visit your deployed site
2. Enter your email and click "Sign In with Magic Link"
3. Check your email and click the magic link
4. Once logged in, try asking a question about conference talks
5. Verify the RAG system returns relevant answers

**Note**: Edge Functions must be deployed for the app to work. See the Google Colab notebook for deployment.

## Troubleshooting

### "Please configure your Supabase credentials"
- Make sure you updated `config.js` with your actual Supabase URL and key

### Magic link not working
**Most common cause**: Redirect URL not configured in Supabase!

1. **Check your email spam folder** - Magic links sometimes end up there
2. **Verify redirect URLs are configured**:
   - Go to Supabase Dashboard → Authentication → URL Configuration
   - Ensure your deployment URL is in the "Redirect URLs" list
   - Example: `https://yourusername.github.io/conference-rag`
   - Don't forget `http://localhost:5500` for local testing
3. **Check the magic link URL**: Click the link and look at the error message
   - If it says "redirect URL not allowed", add your URL to the whitelist
4. **Verify email provider is enabled**: 
   - Supabase Dashboard → Authentication → Providers
   - Email should be toggled ON
5. **Check browser console** for error messages

### "Database search failed"
- Verify you created the `search_documents` function in Supabase
- Check that you have data in the `documents` table
- Ensure RLS policies allow authenticated users to read documents

### GitHub Actions deployment failed
If you see: *"Branch 'main' is not allowed to deploy to github-pages due to environment protection rules"*

1. Go to your repo on GitHub
2. Click **Settings** → **Environments** → **github-pages**
3. Under "Deployment branches", click **Add deployment branch rule**
4. Add `main` (or your default branch name)
5. Save and re-run the workflow from the **Actions** tab

## Next Steps

Students should:
1. ✅ Implement RLS policies to protect user data
2. ✅ Load conference talk data and embeddings
3. ✅ Customize the UI and prompts
4. ✅ Test different embedding strategies
5. ✅ Add features like conversation history
6. ✅ Implement proper error handling

## Security Notes

- ✅ Never commit your `config.js` with real credentials to a public repo
- ✅ The anon key is safe to expose (protected by RLS)
- ✅ OpenAI keys are managed server-side via Edge Functions
- ✅ Always use HTTPS in production
- ✅ RLS is your primary security mechanism

## Resources

- [Supabase Documentation](https://supabase.com/docs)
- [OpenAI API Documentation](https://platform.openai.com/docs)
- [pgvector Documentation](https://github.com/pgvector/pgvector)
