# Getting Started with Amped Development

## Quick Start Guide

This guide helps you get started implementing the Amped Bible reading website based on the comprehensive planning documents.

## 📚 Read This First

Before starting development, read the documentation in this order:

1. **README.md** - Project overview and context
2. **PLAN.md** - Full project plan and architecture (start here for big picture)
3. **BIBLE_DATA_GUIDE.md** - How to get Bible text data
4. **TECHNICAL_SPEC.md** - Implementation details and code examples

## 🎯 Immediate Next Steps

### Step 1: Acquire Bible Data (Do This First!)

**Option A: WEB for Development (RECOMMENDED - 100% FREE)**
```bash
# World English Bible (WEB) - modern English, 100% public domain
# Download from https://ebible.org/web/
# Available in multiple formats: XML, SQL, JSON
# No licensing needed - completely free forever

# Download WEB in SQLite format for easy import
wget https://ebible.org/web/web_sqlite.zip
unzip web_sqlite.zip
```

**Option B: Request Amplified Bible Evaluation License**
```
For private testing to assess suitability:

1. Email: permissions@lockman.org
2. Subject: "Evaluation License Request - Amplified Bible Assessment"
3. Explain:
   - Building Bible reading web application
   - Need to assess Amplified Bible's suitability before licensing
   - Request short-term evaluation license or sample chapters
   - Commit to full license if proceeding to production
4. Budget for full license: $500-$5,000/year
5. Wait for response (typically 1-2 weeks)

See BIBLE_DATA_GUIDE.md for detailed options and legal analysis.
```

**Note:** WEB is 100% free with no licensing concerns. Use it for all development. For Amplified Bible testing, request evaluation license from Lockman Foundation. See BIBLE_DATA_GUIDE.md section "ALL Options for Obtaining Amplified Bible" for comprehensive legal analysis.

### Step 2: Set Up Rails 8 Application

```bash
# Create new Rails 8 app
rails new amped \
  --css=tailwind \
  --javascript=importmap \
  --database=sqlite3

cd amped

# Add essential gems (Phase 1 - minimal)
bundle add pagy  # For pagination if needed

# Note: Authentication deferred to Phase 2
# Phase 2 will add: authorization with Pundit
# No authentication gems needed for Phase 1 static pages
```

### Step 3: Create Database Schema (Bible Content Only - Phase 1)

```bash
# Generate models for Bible content (start simple)
rails generate model Book name:string abbreviation:string testament:integer position:integer
rails generate model Chapter book:references number:integer
rails generate model Verse chapter:references number:integer text:text red_letter:boolean

# Run migrations
rails db:migrate

# Note: User features (bookmarks, highlights, notes) will be added later
# Focus on static reading pages first
```

### Step 4: Import Bible Data

Create import rake task (see TECHNICAL_SPEC.md for complete code):

```bash
# Create the task file
touch lib/tasks/bible.rake

# Run import (once you have Bible data)
rails bible:import_web  # Import World English Bible (100% free)
rails bible:setup_fts   # Setup full-text search (optional for phase 1)
```

### Step 5: Build Static Reading Pages

```bash
# Generate controllers for reading only (no user features yet)
rails generate controller Books index show
rails generate controller Chapters show

# Setup basic routes for reading
# config/routes.rb
resources :books, only: [:index, :show] do
  resources :chapters, only: [:show]
end

# Note: Bookmarks, notes, highlights will be added in later phases
# Focus on clean, simple reading experience first
```

## 📋 Development Checklist

### Week 1-2: Foundation
- [ ] Create Rails 8 app with correct options
- [ ] Setup Hotwire, Propshaft, Importmaps
- [ ] Create database schema for Bible content only (no user features yet)
- [ ] Run migrations
- [ ] Setup Git repository and version control
- [ ] Configure deployment (Fly.io or similar)

### Week 3-4: Bible Text
- [ ] Download WEB Bible text (100% free from ebible.org)
- [ ] Create import scripts
- [ ] Import Bible data to database
- [ ] Validate data integrity (31,102 verses)
- [ ] Create seed data

### Week 5-6: Static Reading Interface (Phase 1 - No User Features)
- [ ] Build book listing page (simple, static)
- [ ] Create chapter reading page (static, no bookmarks/notes)
- [ ] Implement verse display with proper formatting
- [ ] Add red letter text styling
- [ ] Create navigation (prev/next chapter)
- [ ] Make responsive design (multi-column layout)
- [ ] Add dark mode support
- [ ] Test reading experience across devices

### Week 7-8: Polish & Refinement (Still Static)
- [ ] Optimize page load performance
- [ ] Refine typography and layout
- [ ] Test multi-column and side-by-side layouts
- [ ] Improve mobile reading experience
- [ ] Add keyboard shortcuts for navigation
- [ ] User testing and feedback

### Week 9-10: Optional Enhancements (Still Static)
- [ ] Implement basic search (optional)
- [ ] Setup caching strategy
- [ ] Performance optimization
- [ ] Add loading states
- [ ] Error handling
- [ ] Cross-referencing between passages

### Week 11-12: Testing & Launch (Phase 1 - Static Reading)
- [ ] Write basic tests
- [ ] Performance testing
- [ ] Beta user testing (reading experience only)
- [ ] Bug fixes
- [ ] Documentation
- [ ] Deploy to production
- [ ] Soft launch

**Note:** User features (authentication, bookmarks, notes, highlights) will be Phase 2
**Focus:** Build excellent static reading experience first

## 🛠 Essential Commands

### Development
```bash
# Start Rails server
rails server

# Run console
rails console

# Run tests (once written)
rspec

# Check routes
rails routes | grep chapters

# Database console
rails dbconsole
```

### Database
```bash
# Create database
rails db:create

# Run migrations
rails db:migrate

# Rollback migration
rails db:rollback

# Reset database
rails db:reset

# Import Bible data
rails bible:import_web  # World English Bible (100% free)
rails bible:setup_fts   # optional for phase 1

# Validate data
rails bible:validate
```

### Deployment
```bash
# Deploy to Fly.io
fly launch
fly deploy

# View logs
fly logs

# SSH into production
fly ssh console

# Run migrations in production
fly ssh console -C "rails db:migrate"
```

## 📖 Code Examples

### Quick Reference: Read a Chapter

```ruby
# Controller
class ChaptersController < ApplicationController
  def show
    @book = Book.find_by!(abbreviation: params[:book])
    @chapter = @book.chapters.find_by!(number: params[:id])
    @verses = @chapter.verses.order(:number)
  end
end

# View (app/views/chapters/show.html.erb)
<div class="chapter">
  <h1><%= @chapter.reference %></h1>
  
  <% @verses.each do |verse| %>
    <div class="verse" data-verse-id="<%= verse.id %>">
      <span class="verse-number"><%= verse.number %></span>
      <span class="verse-text <%= 'red-letter' if verse.red_letter %>">
        <%= verse.text %>
      </span>
    </div>
  <% end %>
</div>
```

**Note:** Bookmarks, notes, and interactive features will be added in Phase 2.
Phase 1 focuses on static reading pages only.

## 🔧 Configuration Files

### Important Config Files to Customize

1. **config/database.yml** - SQLite configuration
2. **config/routes.rb** - URL structure
3. **config/importmap.rb** - JavaScript dependencies
4. **tailwind.config.js** - Styling configuration
5. **config/initializers/sqlite3.rb** - SQLite optimizations

See TECHNICAL_SPEC.md for complete configuration examples.

## 📚 Learning Resources

### Rails 8 & Hotwire
- Rails Guides: https://guides.rubyonrails.org/
- Hotwire: https://hotwired.dev/
- Turbo Handbook: https://turbo.hotwired.dev/handbook/introduction
- Stimulus Handbook: https://stimulus.hotwired.dev/handbook/introduction

### Tutorials
- Hotrails.dev: https://www.hotrails.dev/
- GoRails: https://gorails.com/
- Rails 8 SQLite Setup: https://fly.io/ruby-dispatch/sqlite-in-rails/

### Community
- Rails Forum: https://discuss.rubyonrails.org/
- Hotwire Discussion: https://discuss.hotwired.dev/
- Stack Overflow: https://stackoverflow.com/questions/tagged/ruby-on-rails

## 💡 Tips & Best Practices

### Development
1. **Start simple** - Get basic reading working first
2. **Use SQLite in development** - Same as production
3. **Test early** - Write tests as you go
4. **Commit often** - Small, focused commits
5. **Use Rails conventions** - Don't fight the framework

### Bible Data
1. **Start with free data** - Use KJV to build and test
2. **Validate thoroughly** - Check verse counts (31,102 for KJV)
3. **Backup often** - Bible data is precious
4. **Cache reads** - Bible text doesn't change
5. **Index properly** - Fast lookups are critical

### Performance
1. **Use indexes** - On foreign keys and lookup columns
2. **Eager load** - Avoid N+1 queries
3. **Cache views** - Chapter content rarely changes
4. **Enable WAL mode** - SQLite performance boost
5. **Monitor queries** - Use Bullet gem

### Security (For Phase 2 - User Features)
1. **HTTPS only** - In production
2. **Rate limiting** - Prevent abuse
3. Authentication and authorization deferred to Phase 2

## 🚨 Common Pitfalls to Avoid

### ❌ Don't Do This:
- Scrape Bible websites (illegal)
- Use YAML for Bible storage (too slow)
- Skip licensing (legal risk)
- Ignore indexes (performance nightmare)
- Store Bible text in multiple places


### ✅ Do This Instead:
- Use MEV or WEB for development
- Use SQLite for Bible text
- Get proper licensing for production
- Index all foreign keys and lookups
- Single source of truth for Bible text
- Focus on reading experience first

## 🎯 Success Criteria (Phase 1 - Static Reading)

Your Phase 1 MVP is ready when:
- [ ] Users can browse all books
- [ ] Users can read any chapter
- [ ] Navigation works smoothly (prev/next, book selector)
- [ ] Red letter text displays correctly
- [ ] Multi-column layout works on desktop
- [ ] Side-by-side pages work on tablet landscape
- [ ] Page loads in <200ms
- [ ] Mobile responsive (single column)
- [ ] Deployed and accessible

**Phase 2 will add:** User accounts, bookmarks, notes, highlights, search

## 📞 Getting Help

If you get stuck:

1. **Check the docs** - PLAN.md, TECHNICAL_SPEC.md, BIBLE_DATA_GUIDE.md
2. **Search issues** - Someone may have had the same problem
3. **Rails Guides** - Comprehensive Rails documentation
4. **Community forums** - Rails and Hotwire communities are helpful
5. **Open an issue** - Describe your problem clearly

## 🎉 You're Ready!

You now have everything you need to start building Amped. The documentation is comprehensive, the architecture is sound, and the path is clear.

**Start with Step 1** (acquire Bible data) and work through the checklist.

Good luck, and enjoy building! 🚀

---

**Questions?** Refer back to the main planning documents or open an issue.
