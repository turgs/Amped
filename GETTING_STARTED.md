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

**Option A: Quick Start with Modern English (RECOMMENDED)**
```bash
# Use World English Bible (WEB) - modern English, public domain
# Download from eBible.org or GitHub
# WEB has no "thee/thou" archaic language - perfect for development!

# Example: Get WEB SQLite from a public source
# Or use scrollmapper/bible_databases if WEB is available there
git clone https://github.com/scrollmapper/bible_databases.git
# Check for WEB or use KJV as fallback
```

**Option B: License Amplified Bible (For Production)**
```
1. Email: permissions@lockman.org
2. Subject: "Bible App License Request - Amplified Bible"
3. Describe your project (see BIBLE_DATA_GUIDE.md for template)
4. Budget: $500-$5,000/year
5. Wait for response (typically 1-2 weeks)
6. Architecture supports easy migration from WEB/KJV to Amplified
```

**Note:** WEB (World English Bible) is recommended over KJV for development because it uses modern English without archaic language. Transitioning from WEB to Amplified Bible later is straightforward.

### Step 2: Set Up Rails 8 Application

```bash
# Create new Rails 8 app
rails new amped \
  --css=tailwind \
  --javascript=importmap \
  --database=sqlite3

cd amped

# Add essential gems
bundle add devise
bundle add pundit
bundle add pagy

# Setup authentication
rails generate devise:install
rails generate devise User
```

### Step 3: Create Database Schema

```bash
# Generate models for Bible content
rails generate model Book name:string abbreviation:string testament:integer position:integer
rails generate model Chapter book:references number:integer
rails generate model Verse chapter:references number:integer text:text red_letter:boolean

# Generate models for user features
rails generate model Bookmark user:references verse:references color:string
rails generate model Highlight user:references start_verse:references end_verse:references start_offset:integer end_offset:integer selected_text:text color:string
rails generate model Note user:references context_chapter:references context_verse:references private:boolean
rails generate model NoteVerseReference note:references verse:references

# Note: Highlight has NO chapter constraint - can span any distance
# Note: Note context fields are OPTIONAL - notes are standalone content

# Setup ActionText for rich notes
rails action_text:install

# Run migrations
rails db:migrate
```

### Step 4: Import Bible Data

Create import rake task (see TECHNICAL_SPEC.md for complete code):

```bash
# Create the task file
touch lib/tasks/bible.rake

# Run import (once you have Bible data)
rails bible:import_kjv  # or import_amplified
rails bible:setup_fts   # Setup full-text search
```

### Step 5: Build Basic Reading Interface

```bash
# Generate controllers
rails generate controller Books index show
rails generate controller Chapters show
rails generate controller Bookmarks index create destroy
rails generate controller Notes index show new create edit update destroy

# Setup routes (see TECHNICAL_SPEC.md for complete routes)
```

## 📋 Development Checklist

### Week 1-2: Foundation
- [ ] Create Rails 8 app with correct options
- [ ] Setup Hotwire, Propshaft, Importmaps
- [ ] Configure Devise authentication
- [ ] Create database schema and run migrations
- [ ] Setup Git repository and version control
- [ ] Configure deployment (Fly.io or similar)

### Week 3-4: Bible Text
- [ ] Obtain Bible text (KJV for now)
- [ ] Create import scripts
- [ ] Import Bible data to database
- [ ] Setup SQLite FTS5 for search
- [ ] Validate data integrity (31,102 verses)
- [ ] Create seed data

### Week 5-6: Reading Interface
- [ ] Build book listing page
- [ ] Create chapter reading page
- [ ] Implement verse display
- [ ] Add red letter text styling
- [ ] Create navigation (prev/next chapter)
- [ ] Make responsive design
- [ ] Add dark mode support

### Week 7-8: User Features
- [ ] Implement bookmark toggle (Turbo Streams)
- [ ] Create bookmarks index page
- [ ] Build note creation form (ActionText)
- [ ] Create notes management interface
- [ ] Add user dashboard
- [ ] Implement user profiles

### Week 9-10: Search & Polish
- [ ] Implement full-text search
- [ ] Add search results page
- [ ] Setup caching strategy
- [ ] Performance optimization
- [ ] Add loading states
- [ ] Error handling

### Week 11-12: Testing & Launch
- [ ] Write RSpec tests
- [ ] Security audit
- [ ] Performance testing
- [ ] Beta user testing
- [ ] Bug fixes
- [ ] Documentation
- [ ] Deploy to production
- [ ] Soft launch

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
rails bible:import_kjv
rails bible:setup_fts

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

### Quick Reference: Toggle Bookmark

```ruby
# Controller
class BookmarksController < ApplicationController
  def toggle
    @verse = Verse.find(params[:verse_id])
    @bookmark = current_user.bookmarks.find_or_initialize_by(verse: @verse)
    
    if @bookmark.persisted?
      @bookmark.destroy
      @bookmarked = false
    else
      @bookmark.save
      @bookmarked = true
    end
    
    respond_to do |format|
      format.turbo_stream
    end
  end
end

# Turbo Stream View (app/views/bookmarks/toggle.turbo_stream.erb)
<turbo-stream action="replace" target="verse_<%= @verse.id %>_bookmark">
  <template>
    <%= render "bookmarks/button", verse: @verse, bookmarked: @bookmarked %>
  </template>
</turbo-stream>
```

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

### Security
1. **Authenticate users** - Use Devise
2. **Authorize actions** - Use Pundit
3. **Validate input** - Always validate user data
4. **Rate limit** - Use rack-attack
5. **HTTPS only** - In production

## 🚨 Common Pitfalls to Avoid

### ❌ Don't Do This:
- Scrape Bible websites (illegal)
- Use YAML for Bible storage (too slow)
- Skip licensing (legal risk)
- Ignore indexes (performance nightmare)
- Store Bible text in multiple places
- Forget to backup user data

### ✅ Do This Instead:
- License or use public domain Bible
- Use SQLite for everything
- Get proper licensing
- Index all foreign keys and lookups
- Single source of truth for Bible text
- Regular automated backups

## 🎯 Success Criteria

Your MVP is ready when:
- [ ] Users can read any chapter
- [ ] Navigation works (prev/next, book selector)
- [ ] Bookmarks save and display correctly
- [ ] Notes can be created and edited
- [ ] Search finds verses accurately
- [ ] Page loads in <200ms
- [ ] Mobile responsive
- [ ] Tests pass
- [ ] Deployed and accessible

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
