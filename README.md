# Amped - Bible Reading Platform

A modern Bible reading website featuring the Amplified Bible with red letter text, personal bookmarks, and note-taking capabilities.

## 📖 Project Overview

Amped is designed to provide an exceptional Bible reading experience with:

- **Amplified Bible text** with red letter formatting (words of Jesus)
- **Personal bookmarks** with color coding and organization
- **Rich note-taking** with full-text search
- **Fast, responsive interface** using modern web technologies
- **Mobile apps** (planned) using Hotwire Native

## 🛠 Technology Stack

- **Backend:** Rails 8
- **Frontend:** Hotwire (Turbo + Stimulus)
- **Assets:** Propshaft
- **JavaScript:** Importmaps (no build step)
- **Database:** SQLite (production-ready with Rails 8)
- **Mobile:** Hotwire Native (future)

## 📚 Documentation

This repository contains comprehensive planning and technical documentation:

- **[PLAN.md](PLAN.md)** - Complete project plan, architecture, and implementation roadmap
- **[TECHNICAL_SPEC.md](TECHNICAL_SPEC.md)** - Detailed database schema, API design, and code examples
- **[BIBLE_DATA_GUIDE.md](BIBLE_DATA_GUIDE.md)** - Guide to acquiring Bible text data legally and implementing it

## 🚀 Key Features (Planned)

### Core Features
- [ ] Bible text reading interface
- [ ] Chapter and verse navigation
- [ ] Red letter text highlighting
- [ ] Full-text search
- [ ] User authentication

### Personal Study Tools
- [ ] One-click bookmarking
- [ ] Rich text notes with ActionText
- [ ] Bookmark collections
- [ ] Note organization and search
- [ ] Export capabilities

### Advanced Features
- [ ] Reading plans
- [ ] Multiple translations
- [ ] Cross-references
- [ ] Study resources
- [ ] Social sharing

### Mobile
- [ ] iOS app (Hotwire Native)
- [ ] Android app (Hotwire Native)
- [ ] Offline reading
- [ ] Push notifications

## 💡 Why Rails 8 + SQLite?

Rails 8 makes SQLite production-ready for applications like this:

- **Performance:** Fast reads with proper indexes (>1000 req/sec)
- **Simplicity:** Single file database, easy backups
- **Cost:** Minimal hosting costs
- **Scalability:** Handles 100+ concurrent users easily
- **Development:** Excellent developer experience

For a Bible reading app with mostly read operations, SQLite is perfect.

## 🔐 Bible Text Licensing

The Amplified Bible is copyrighted by the Lockman Foundation. This project requires:

1. **Licensing** for Amplified Bible text
2. **Alternative:** Start with public domain Bible (KJV, WEB) then add Amplified later

See [BIBLE_DATA_GUIDE.md](BIBLE_DATA_GUIDE.md) for complete details on:
- How to obtain licenses
- Expected costs ($500-$5,000/year)
- Legal considerations
- Public domain alternatives
- Data format options

## 📦 Getting Started (Future)

Once the application is built:

```bash
# Clone the repository
git clone https://github.com/turgs/Amped.git
cd Amped

# Install dependencies
bundle install

# Setup database
rails db:setup

# Import Bible data
rails bible:import

# Start server
rails server
```

## 🗂 Project Structure (Planned)

```
amped/
├── app/
│   ├── models/
│   │   ├── book.rb
│   │   ├── chapter.rb
│   │   ├── verse.rb
│   │   ├── bookmark.rb
│   │   └── note.rb
│   ├── controllers/
│   │   ├── books_controller.rb
│   │   ├── chapters_controller.rb
│   │   ├── bookmarks_controller.rb
│   │   └── notes_controller.rb
│   ├── views/
│   │   └── [Hotwire-powered views]
│   └── javascript/
│       └── controllers/
│           ├── bookmark_controller.js
│           ├── search_controller.js
│           └── verse_controller.js
├── db/
│   ├── migrate/
│   └── seeds/
│       └── [Bible text data]
└── lib/
    └── tasks/
        └── bible.rake
```

## 🎯 Development Phases

### Phase 1: Foundation (Weeks 1-2)
- Rails 8 setup with Hotwire
- Database schema
- Authentication
- Deployment pipeline

### Phase 2: Bible Text (Weeks 3-4)
- Obtain and import Bible data
- Reading interface
- Navigation

### Phase 3: User Features (Weeks 5-8)
- Bookmarks
- Notes
- Search
- User dashboard

### Phase 4: Polish & Launch (Weeks 9-12)
- Performance optimization
- Testing
- Documentation
- Beta launch

## 📊 Database Schema (Overview)

```ruby
books           # 66 books of the Bible
├── chapters    # ~1,189 chapters
    └── verses  # ~31,000 verses

users           # User accounts
├── bookmarks   # Verse bookmarks
├── notes       # Study notes
└── collections # Organized bookmark collections
```

See [TECHNICAL_SPEC.md](TECHNICAL_SPEC.md) for complete schema.

## 🤝 Contributing

This is currently in the planning phase. Contributions welcome once development begins!

## 📄 License

Application code: [Choose license - MIT recommended]

Bible text: Licensed separately from copyright holders (Lockman Foundation for Amplified Bible)

## 📞 Contact

For questions or suggestions, please open an issue in this repository.

---

**Status:** Planning & Documentation Phase  
**Next Steps:** See [PLAN.md](PLAN.md) for the complete roadmap

Built with ❤️ for Bible readers everywhere