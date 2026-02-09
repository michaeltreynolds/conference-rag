# Conference Q&A - RAG Application

A production-ready Retrieval Augmented Generation (RAG) application that allows users to ask questions about conference talks using semantic search and AI-generated answers.

## 🚀 Features

- ✅ **Supabase Authentication** - Secure magic link login
- ✅ **Vector Search** - Semantic search using pgvector
- ✅ **OpenAI Integration** - Embeddings and GPT-powered answers
- ✅ **Row Level Security** - Protect user data with RLS policies
- ✅ **Modern UI** - Clean, responsive interface
- ✅ **Zero Backend** - Fully client-side application

## 📋 Prerequisites

- Supabase account (free tier works)
- GitHub account (for deployment)
- Basic knowledge of HTML/CSS/JavaScript

## 🛠️ Setup

See [SETUP.md](SETUP.md) for detailed step-by-step instructions.

**Quick Start:**
1. Create a Supabase project
2. Update `config.js` with your credentials
3. Deploy Edge Functions (via Google Colab)
4. Deploy to GitHub Pages

## 📚 Learning Objectives

This project teaches:
- Production-ready application development
- Row Level Security (RLS) implementation
- Retrieval Augmented Generation (RAG)
- Vector embeddings and semantic search
- Supabase as a backend-as-a-service
- Client-side authentication flows

## 🏗️ Architecture

```
┌─────────────┐
│   Browser   │
│  (GitHub    │
│   Pages)    │
└──────┬──────┘
       │
       ├─────────────┐
       │             │
       ▼             ▼
┌─────────────┐ ┌──────────┐
│  Supabase   │ │  OpenAI  │
│  - Auth     │ │  - GPT   │
│  - Database │ │  - Embed │
│  - RLS      │ └──────────┘
└─────────────┘
```

## 🔒 Security

- Supabase anon key is safe to expose (protected by RLS)
- OpenAI keys managed server-side via Edge Functions
- Row Level Security policies control data access
- HTTPS enforced by GitHub Pages

## 📖 Documentation

- [SETUP.md](SETUP.md) - Complete setup guide
- [FEASIBILITY.md](FEASIBILITY.md) - Technical feasibility analysis

## 🤝 Contributing

This is a student project template. Feel free to customize and extend!

## 📄 License

Educational use only. Do not publicly share applications built with this template due to copyright considerations on conference talk content.

## ⚠️ Important Notes

- Never commit your `config.js` with real credentials
- Deploy Edge Functions before testing the app
- Implement RLS policies before loading sensitive data
- Test thoroughly before sharing your deployment

## 🆘 Troubleshooting

See the [SETUP.md](SETUP.md) troubleshooting section for common issues and solutions.

## 🎓 Assignment Deliverables

Students should submit:
1. GitHub repository URL
2. Live deployment URL
3. Documentation of RLS policies implemented
4. Example queries and responses
5. Reflection on embedding strategies tested

---

Built with ❤️ for CS452
