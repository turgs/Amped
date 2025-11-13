# Amped - Bible Reading Website Plan & Analysis

## Project Overview

**Amped** is a Bible reading website focused on the Amplified Bible with red letter formatting. The platform will support personal bookmarks, note-taking, and eventually mobile applications.

**Target Tech Stack:** Rails 8, Hotwire, Propshaft, Importmaps, SQLite, Hotwire Native

---

## 1. Acquiring Amplified Bible Text with Red Letter

### Option 1: Official Licensing (RECOMMENDED)
**Source:** Lockman Foundation (copyright holder of Amplified Bible)

- **Pros:**
  - Legally compliant
  - High-quality, accurate text
  - Support from publisher
  - Peace of mind for public deployment
  
- **Cons:**
  - Licensing fees required
  - Approval process needed
  - Restrictions on usage

**Contact:** https://www.lockman.org/

### Option 2: Bible APIs

#### A. API.Bible (Bible.com)
- **URL:** https://scripture.api.bible
- **Pros:**
  - Free tier available
  - Multiple Bible versions
  - RESTful API
  - JSON format
  
- **Cons:**
  - May not have Amplified Bible in all editions
  - Rate limiting on free tier
  - Requires attribution
  - Red letter formatting may not be available

#### B. Biblia API (Faithlife)
- **URL:** https://bibliaapi.com/
- **Pros:**
  - Professional grade
  - Multiple versions including Amplified
  - Good documentation
  
- **Cons:**
  - Paid service
  - API dependency

### Option 3: Web Scraping (NOT RECOMMENDED)

**Sources to scrape:** BibleGateway, Bible.com, YouVersion

- **Pros:**
  - Immediate access to text
  - No licensing fees upfront
  
- **Cons:**
  - **ILLEGAL** - Copyright violation
  - **Terms of Service violation** - Will result in IP bans
  - **Legal liability** - Risk of lawsuits
  - Unstable (sites change structure)
  - Ethical concerns
  - Not suitable for production

**STRONG RECOMMENDATION:** Do NOT scrape. The legal and ethical risks far outweigh benefits.

### Option 4: Public Domain Bibles as Alternative

- **Versions:** KJV, ASV, WEB (World English Bible)
- **Pros:**
  - Free and legal
  - Can be freely distributed
  - Many sources available
  
- **Cons:**
  - Not Amplified Bible
  - May need to compromise on initial vision

### Option 5: SWORD Project

- **URL:** https://crosswire.org/sword/
- **Pros:**
  - Open source Bible software library
  - Multiple Bible modules
  - Established community
  
- **Cons:**
  - Module availability varies
  - May not include Amplified Bible
  - Integration complexity

### Red Letter Text Considerations

Red letter text (words of Jesus) requires:
1. Marked-up source data with Jesus' words tagged
2. HTML/CSS rendering: `<span class="red-letter">text</span>`
3. Storage flag in database indicating red letter verses

Most APIs and sources will have this data marked if available.

---

## 2. Storage Options Analysis

### Option A: SQLite (STRONGLY RECOMMENDED)

#### Pros:
- **Perfect for Rails 8:** Native support, production-ready
- **Structured queries:** Fast lookups by book, chapter, verse
- **Relationships:** Easy to link bookmarks/notes to verses
- **Full-text search:** Built-in FTS5 for searching Bible text
- **Indexing:** Fast retrieval with proper indexes
- **Concurrent reads:** Multiple users can read simultaneously
- **ACID compliance:** Data integrity for user bookmarks/notes
- **Backup/export:** Simple file-based backup
- **Size efficient:** Compressed storage, ~10-30MB for full Bible
- **Rails integration:** ActiveRecord support out of the box

#### Schema Design:

```ruby
# Bible text tables
create_table :books do |t|
  t.string :name, null: false          # "Genesis", "Matthew"
  t.string :abbreviation               # "Gen", "Matt"
  t.integer :testament                 # 1 = Old, 2 = New
  t.integer :position, null: false     # Book order
  t.timestamps
end

create_table :chapters do |t|
  t.references :book, null: false, foreign_key: true
  t.integer :number, null: false       # Chapter number
  t.timestamps
  
  t.index [:book_id, :number], unique: true
end

create_table :verses do |t|
  t.references :chapter, null: false, foreign_key: true
  t.integer :number, null: false       # Verse number
  t.text :text, null: false            # Verse content
  t.boolean :red_letter, default: false
  t.timestamps
  
  t.index [:chapter_id, :number], unique: true
  t.index :red_letter
end

# User-generated content
create_table :users do |t|
  t.string :email, null: false
  t.string :encrypted_password
  # Devise or other authentication fields
  t.timestamps
  
  t.index :email, unique: true
end

create_table :bookmarks do |t|
  t.references :user, null: false, foreign_key: true
  t.references :verse, null: false, foreign_key: true
  t.string :color                      # Optional color coding
  t.timestamps
  
  t.index [:user_id, :verse_id], unique: true
  t.index :created_at
end

create_table :notes do |t|
  t.references :user, null: false, foreign_key: true
  t.references :verse, null: false, foreign_key: true
  t.text :content, null: false
  t.boolean :private, default: true
  t.timestamps
  
  t.index [:user_id, :verse_id]
  t.index :created_at
end

# Full-text search
# Use SQLite FTS5 virtual table for search
CREATE VIRTUAL TABLE verses_fts USING fts5(
  verse_id UNINDEXED,
  book_name,
  chapter_number UNINDEXED,
  verse_number UNINDEXED,
  text,
  content=verses
);
```

#### Performance Estimates:
- **Full Bible:** ~31,000 verses = ~10-30MB
- **Lookup time:** <1ms for verse retrieval with indexes
- **Search time:** <50ms for full-text search
- **Concurrent users:** 100+ concurrent readers easily

### Option B: YAML (NOT RECOMMENDED FOR PRODUCTION)

#### Pros:
- Human-readable
- Easy to edit manually
- Simple structure
- Good for initial prototyping

#### Cons:
- **Load entire file into memory:** 30MB+ per request
- **No indexing:** Must scan entire file for searches
- **Slow queries:** O(n) time complexity
- **No relationships:** Hard to link bookmarks/notes
- **Concurrency issues:** File locking problems
- **Not scalable:** Performance degrades with users
- **No transactions:** Data corruption risk

#### When to use YAML:
- Initial prototyping only
- Seed data storage
- Configuration files
- NOT for production Bible text storage

### Option C: JSON Files (NOT RECOMMENDED)

Similar pros/cons to YAML. Useful for:
- API response caching
- Export/import features
- NOT primary storage

### Option D: PostgreSQL (Overkill)

- More features than needed
- Adds deployment complexity
- Rails 8 optimizes for SQLite
- Use only if scaling beyond SQLite limits (unlikely)

### Recommended Approach:

**PRIMARY: SQLite for all data (Bible text, bookmarks, notes)**

**SECONDARY: JSON/YAML for:**
- Seed data to populate database initially
- Import/export features
- API responses

---

## 3. Bookmark & Note-Taking Implementation

### Bookmark Features

#### Basic Requirements:
- Single-click bookmark toggle
- Visual indicator on bookmarked verses
- List view of all bookmarks
- Sort by: date added, book order, custom
- Filter by: book, testament, date range

#### Advanced Features:
- **Color coding:** Different colors for different purposes
- **Tags:** Custom tags for organization
- **Collections:** Group bookmarks into reading lists
- **Sharing:** Share bookmark collections with others
- **Export:** Export to PDF, text, or markdown

#### UI/UX with Hotwire:
```ruby
# Turbo Frame for instant bookmark toggle
<%= turbo_frame_tag "verse_#{verse.id}_bookmark" do %>
  <%= render "bookmarks/toggle", verse: verse %>
<% end %>

# Controller action
def toggle
  @bookmark = current_user.bookmarks.find_or_initialize_by(verse: @verse)
  
  if @bookmark.persisted?
    @bookmark.destroy
  else
    @bookmark.save
  end
  
  # Turbo Stream updates the bookmark icon
  respond_to do |format|
    format.turbo_stream
  end
end
```

### Note-Taking Features

#### Basic Requirements:
- Rich text editor (ActionText)
- Attach notes to any verse
- Private by default
- Edit/delete capabilities
- Timestamp tracking

#### Advanced Features:
- **Markdown support:** For formatting
- **Cross-references:** Link to other verses
- **Search notes:** Full-text search across all notes
- **Tags:** Categorize notes
- **Sharing:** Optionally share notes publicly
- **Attachments:** Images, PDFs (ActionText + ActiveStorage)

#### Implementation with ActionText:
```ruby
# Note model
class Note < ApplicationRecord
  belongs_to :user
  belongs_to :verse
  has_rich_text :content
  
  validates :content, presence: true
  
  scope :recent, -> { order(created_at: :desc) }
  scope :for_verse, ->(verse) { where(verse: verse) }
end

# View with Trix editor
<%= form_with model: [@verse, @note], 
              data: { turbo_frame: "note_form" } do |f| %>
  <%= f.rich_text_area :content, 
                       placeholder: "Add your note..." %>
  <%= f.submit "Save Note" %>
<% end %>
```

#### Search Implementation:
```ruby
# Full-text search for notes
class Note < ApplicationRecord
  include PgSearch::Model if using Postgres
  # OR use SQLite FTS5
  
  def self.search(query)
    where("content LIKE ?", "%#{sanitize_sql_like(query)}%")
  end
end
```

---

## 4. Technical Architecture

### Rails 8 Stack Details

#### 1. Hotwire (Turbo + Stimulus)

**Turbo Drive:** Page navigation without full reloads
- Fast navigation between chapters
- Preserve scroll position
- Progress bar for navigation

**Turbo Frames:** Independent page sections
```erb
<!-- Each verse can be an independent frame -->
<turbo-frame id="verse_<%= verse.id %>">
  <%= render verse %>
</turbo-frame>
```

**Turbo Streams:** Real-time updates
- Live bookmark updates
- Real-time note saving
- Multi-device sync (future)

**Stimulus Controllers:** Lightweight JavaScript
```javascript
// Verse highlight controller
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["verse"]
  
  highlight() {
    this.verseTarget.classList.add("highlighted")
  }
  
  removeHighlight() {
    this.verseTarget.classList.remove("highlighted")
  }
}
```

#### 2. Propshaft (Asset Pipeline)

- Simpler than Sprockets
- Faster compilation
- Direct file serving
- Perfect for Hotwire apps

**Setup:**
```ruby
# config/application.rb
config.assets.paths << Rails.root.join("app/assets/stylesheets")
```

#### 3. Importmaps (JavaScript)

No build step needed for JavaScript:
```ruby
# config/importmap.rb
pin "application"
pin "@hotwired/turbo-rails"
pin "@hotwired/stimulus"
pin "@hotwired/stimulus-loading"
```

#### 4. SQLite for Production

Rails 8 makes SQLite production-ready:
```yaml
# config/database.yml
production:
  adapter: sqlite3
  database: storage/production.sqlite3
  pool: 5
  timeout: 5000
  # Rails 8 production enhancements
```

**Optimization:**
```ruby
# config/initializers/sqlite3.rb
Rails.application.config.after_initialize do
  ActiveRecord::Base.connection.execute("PRAGMA journal_mode = WAL")
  ActiveRecord::Base.connection.execute("PRAGMA synchronous = NORMAL")
  ActiveRecord::Base.connection.execute("PRAGMA cache_size = -64000")
end
```

#### 5. Hotwire Native (Mobile Apps)

Future iOS/Android apps using web views:
- Reuse same Rails views
- Native navigation wrappers
- Access device features
- Offline support with SQLite sync

---

## 5. Application Structure

### Core Models

```ruby
# app/models/book.rb
class Book < ApplicationRecord
  has_many :chapters, dependent: :destroy
  has_many :verses, through: :chapters
  
  validates :name, presence: true, uniqueness: true
  validates :position, presence: true, uniqueness: true
  
  scope :old_testament, -> { where(testament: 1) }
  scope :new_testament, -> { where(testament: 2) }
  scope :ordered, -> { order(:position) }
end

# app/models/chapter.rb
class Chapter < ApplicationRecord
  belongs_to :book
  has_many :verses, dependent: :destroy
  
  validates :number, presence: true, 
            uniqueness: { scope: :book_id }
  
  def reference
    "#{book.name} #{number}"
  end
end

# app/models/verse.rb
class Verse < ApplicationRecord
  belongs_to :chapter
  has_many :bookmarks, dependent: :destroy
  has_many :notes, dependent: :destroy
  has_many :users, through: :bookmarks
  
  validates :number, presence: true,
            uniqueness: { scope: :chapter_id }
  validates :text, presence: true
  
  delegate :book, to: :chapter
  
  def reference
    "#{chapter.book.name} #{chapter.number}:#{number}"
  end
  
  def bookmarked_by?(user)
    user && bookmarks.exists?(user: user)
  end
  
  def notes_by(user)
    notes.where(user: user)
  end
end

# app/models/bookmark.rb
class Bookmark < ApplicationRecord
  belongs_to :user
  belongs_to :verse
  
  validates :verse_id, uniqueness: { scope: :user_id }
  
  scope :recent, -> { order(created_at: :desc) }
  scope :by_book_order, -> do
    joins(verse: { chapter: :book })
      .order("books.position, chapters.number, verses.number")
  end
end

# app/models/note.rb
class Note < ApplicationRecord
  belongs_to :user
  belongs_to :verse
  has_rich_text :content
  
  validates :content, presence: true
  
  scope :recent, -> { order(created_at: :desc) }
  scope :public_notes, -> { where(private: false) }
end
```

### URL Structure

```ruby
# config/routes.rb
Rails.application.routes.draw do
  root "home#index"
  
  # Bible reading routes
  resources :books, only: [:index, :show] do
    resources :chapters, only: [:show]
  end
  
  # Alternative reading route
  get "read/:book/:chapter", to: "chapters#show", as: :read
  get "read/:book/:chapter/:verse", to: "verses#show", as: :read_verse
  
  # User features
  authenticate :user do
    resources :bookmarks, only: [:index, :create, :destroy]
    resources :notes
    
    # Quick toggle routes
    post "verses/:verse_id/bookmark", to: "bookmarks#toggle"
    get "verses/:verse_id/notes/new", to: "notes#new"
  end
  
  # Search
  get "search", to: "search#index"
  
  # API for mobile apps (future)
  namespace :api do
    namespace :v1 do
      resources :verses, only: [:index, :show]
      resources :bookmarks, only: [:index, :create, :destroy]
      resources :notes
    end
  end
end
```

---

## 6. UI/UX Considerations

### Reading Experience

1. **Clean, distraction-free interface**
   - Centered text column
   - Comfortable line height (1.6-1.8)
   - Readable font size (16-18px base)
   - Font: Georgia, serif for readability

2. **Red Letter Text Styling**
   ```css
   .verse-text .red-letter {
     color: #c41e3a; /* Traditional red letter color */
     font-weight: 500;
   }
   
   /* Dark mode support */
   @media (prefers-color-scheme: dark) {
     .verse-text .red-letter {
       color: #ff6b6b;
     }
   }
   ```

3. **Navigation**
   - Sticky header with book/chapter selector
   - Previous/Next chapter buttons
   - Quick jump dropdown
   - Breadcrumb trail

4. **Interaction**
   - Click verse number to bookmark
   - Hover shows note icon
   - Click note icon to add/view notes
   - Highlight on selection

### Mobile Considerations

- Touch-friendly tap targets (44x44px minimum)
- Swipe between chapters
- Pull-to-refresh
- Bottom navigation for thumbs
- Offline reading capability

---

## 7. Data Import Strategy

### Step 1: Obtain Source Data

**Recommended:** Purchase licensed Amplified Bible data in structured format (XML, JSON, or CSV)

### Step 2: Create Seed Data Structure

```yaml
# db/seeds/amplified_bible.yml
- book:
    name: "Genesis"
    abbreviation: "Gen"
    testament: 1
    position: 1
    chapters:
      - number: 1
        verses:
          - number: 1
            text: "In the beginning God created the heavens and the earth."
            red_letter: false
          - number: 2
            text: "The earth was formless and void..."
            red_letter: false
# ... more verses
```

### Step 3: Import Script

```ruby
# lib/tasks/import_bible.rake
namespace :bible do
  desc "Import Bible text from YAML seed files"
  task import: :environment do
    require 'yaml'
    
    data = YAML.load_file(Rails.root.join('db/seeds/amplified_bible.yml'))
    
    data.each do |book_data|
      book = Book.find_or_create_by!(
        name: book_data['book']['name'],
        abbreviation: book_data['book']['abbreviation'],
        testament: book_data['book']['testament'],
        position: book_data['book']['position']
      )
      
      book_data['book']['chapters'].each do |chapter_data|
        chapter = book.chapters.find_or_create_by!(
          number: chapter_data['number']
        )
        
        chapter_data['verses'].each do |verse_data|
          chapter.verses.find_or_create_by!(
            number: verse_data['number']
          ) do |verse|
            verse.text = verse_data['text']
            verse.red_letter = verse_data['red_letter']
          end
        end
      end
      
      puts "Imported #{book.name}"
    end
    
    puts "Bible import complete!"
  end
  
  desc "Setup full-text search"
  task setup_fts: :environment do
    ActiveRecord::Base.connection.execute(<<~SQL)
      CREATE VIRTUAL TABLE IF NOT EXISTS verses_fts 
      USING fts5(
        verse_id UNINDEXED,
        book_name,
        reference,
        text,
        content=verses
      );
    SQL
    
    # Populate FTS table
    ActiveRecord::Base.connection.execute(<<~SQL)
      INSERT INTO verses_fts(verse_id, book_name, reference, text)
      SELECT 
        verses.id,
        books.name,
        books.name || ' ' || chapters.number || ':' || verses.number,
        verses.text
      FROM verses
      JOIN chapters ON verses.chapter_id = chapters.id
      JOIN books ON chapters.book_id = books.id;
    SQL
    
    puts "Full-text search setup complete!"
  end
end
```

### Step 4: Run Import

```bash
rails bible:import
rails bible:setup_fts
```

---

## 8. Search Implementation

### Full-Text Search with SQLite FTS5

```ruby
# app/models/verse.rb
class Verse < ApplicationRecord
  def self.search(query)
    return none if query.blank?
    
    # Use FTS5 virtual table
    sql = <<~SQL
      SELECT verses.*
      FROM verses
      JOIN verses_fts ON verses.id = verses_fts.verse_id
      WHERE verses_fts MATCH ?
      ORDER BY rank
      LIMIT 100
    SQL
    
    find_by_sql([sql, query])
  end
  
  def self.search_with_context(query)
    search(query).includes(chapter: :book)
  end
end

# app/controllers/search_controller.rb
class SearchController < ApplicationController
  def index
    @query = params[:q]
    @verses = Verse.search_with_context(@query)
    
    respond_to do |format|
      format.html
      format.turbo_stream
    end
  end
end
```

### Search UI

```erb
<!-- app/views/search/index.html.erb -->
<%= form_with url: search_path, method: :get, 
              data: { turbo_frame: "search_results" } do |f| %>
  <%= f.text_field :q, 
                   placeholder: "Search the Bible...",
                   autofocus: true,
                   data: { 
                     controller: "search",
                     action: "input->search#perform"
                   } %>
  <%= f.submit "Search" %>
<% end %>

<%= turbo_frame_tag "search_results" do %>
  <% if @verses.any? %>
    <div class="search-results">
      <p><%= @verses.count %> results found</p>
      
      <% @verses.each do |verse| %>
        <div class="search-result">
          <h4><%= verse.reference %></h4>
          <p><%= verse.text %></p>
        </div>
      <% end %>
    </div>
  <% end %>
<% end %>
```

---

## 9. Performance Optimization

### Database Indexes

```ruby
# db/migrate/xxx_add_performance_indexes.rb
class AddPerformanceIndexes < ActiveRecord::Migration[7.1]
  def change
    # Verse lookups
    add_index :verses, [:chapter_id, :number]
    add_index :verses, :red_letter
    
    # Chapter lookups
    add_index :chapters, [:book_id, :number]
    
    # Book lookups
    add_index :books, :position
    add_index :books, :testament
    
    # User features
    add_index :bookmarks, [:user_id, :verse_id], unique: true
    add_index :bookmarks, :created_at
    add_index :notes, [:user_id, :verse_id]
    add_index :notes, :created_at
  end
end
```

### Caching Strategy

```ruby
# app/controllers/chapters_controller.rb
class ChaptersController < ApplicationController
  def show
    @book = Book.find(params[:book_id])
    @chapter = @book.chapters.find_by!(number: params[:id])
    
    # Cache the chapter content
    @verses = Rails.cache.fetch(
      "chapter_#{@chapter.id}_verses",
      expires_in: 1.week
    ) do
      @chapter.verses.order(:number).to_a
    end
  end
end

# app/models/verse.rb
class Verse < ApplicationRecord
  after_save :clear_chapter_cache
  
  private
  
  def clear_chapter_cache
    Rails.cache.delete("chapter_#{chapter_id}_verses")
  end
end
```

### Eager Loading

```ruby
# Avoid N+1 queries
@bookmarks = current_user.bookmarks
  .includes(verse: { chapter: :book })
  .recent
```

---

## 10. Future Enhancements

### Phase 1: MVP (Months 1-2)
- [x] Plan and analysis document
- [ ] Rails 8 app setup
- [ ] Bible text import
- [ ] Basic reading interface
- [ ] User authentication
- [ ] Bookmarks feature
- [ ] Notes feature

### Phase 2: Enhanced Features (Months 3-4)
- [ ] Advanced search
- [ ] Reading plans
- [ ] Verse sharing
- [ ] Dark mode
- [ ] Multiple translations
- [ ] Cross-references
- [ ] Study resources

### Phase 3: Social Features (Months 5-6)
- [ ] User profiles
- [ ] Follow other readers
- [ ] Share notes publicly
- [ ] Discussion groups
- [ ] Study groups
- [ ] Verse of the day

### Phase 4: Mobile Apps (Months 7-9)
- [ ] Hotwire Native iOS app
- [ ] Hotwire Native Android app
- [ ] Offline reading
- [ ] Push notifications
- [ ] Audio Bible integration

### Phase 5: Advanced Features (Months 10-12)
- [ ] Bible study tools
- [ ] Concordance
- [ ] Commentaries
- [ ] Original language tools
- [ ] Passage comparison
- [ ] Analytics dashboard

---

## 11. Security Considerations

### Authentication & Authorization

```ruby
# Use Devise for authentication
gem 'devise'

# Authorization with Pundit
gem 'pundit'

# app/policies/note_policy.rb
class NotePolicy < ApplicationPolicy
  def show?
    record.public? || record.user == user
  end
  
  def update?
    record.user == user
  end
  
  def destroy?
    record.user == user
  end
end
```

### Data Protection

- Encrypt user passwords (Devise default)
- Use secure sessions
- HTTPS in production
- CSRF protection (Rails default)
- SQL injection prevention (ActiveRecord parameterization)
- XSS prevention (Rails ERB escaping)

### Privacy

- Private notes by default
- User data export capability (GDPR)
- Account deletion option
- Clear privacy policy

---

## 12. Deployment Considerations

### Hosting Options

1. **Fly.io** (Recommended for Rails + SQLite)
   - Built for Rails 8
   - SQLite support
   - Global distribution
   - Free tier available

2. **Heroku**
   - Easy deployment
   - Add-ons available
   - Note: Requires configuration for SQLite persistence

3. **Hatchbox.io**
   - Rails-focused
   - Simple setup
   - Good for small projects

4. **Self-hosted VPS**
   - Full control
   - DigitalOcean, Linode, etc.
   - Requires more DevOps

### Deployment Checklist

```bash
# 1. Precompile assets
rails assets:precompile

# 2. Run migrations
rails db:migrate

# 3. Import Bible data
rails bible:import
rails bible:setup_fts

# 4. Set environment variables
RAILS_ENV=production
SECRET_KEY_BASE=...
DATABASE_URL=...

# 5. Start server
rails server -e production
```

---

## 13. Cost Estimates

### Initial Setup (One-time)
- Bible text licensing: $500-$5,000 (one-time)
- Domain name: $10-15/year
- SSL certificate: Free (Let's Encrypt)
- Development time: Self-built

### Monthly Operating Costs
- Hosting (Fly.io/Heroku): $0-25/month
- Email service (for auth): $0-10/month
- Monitoring: $0-20/month
- **Total: $0-55/month** for MVP

### Scaling Costs
- Additional storage: ~$5/100GB
- More dynos/instances: $25-100/month each
- CDN (if needed): $10-50/month

---

## 14. Legal & Licensing

### Critical Legal Considerations

1. **Bible Text Copyright**
   - Amplified Bible is copyrighted by Lockman Foundation
   - Requires licensing for any distribution
   - Contact: https://www.lockman.org/
   - Budget: $500-$5,000+ depending on usage

2. **Fair Use (Does NOT Apply)**
   - Fair use is extremely limited for Bible texts
   - Do NOT assume fair use protects you
   - Get proper licensing

3. **Alternative: Public Domain**
   - If licensing is too expensive initially
   - Start with KJV or WEB (public domain)
   - Build user base first
   - Add Amplified later when revenue supports licensing

4. **Terms of Service**
   - Create clear ToS
   - User-generated content ownership
   - Acceptable use policy

5. **Privacy Policy**
   - GDPR compliance if EU users
   - CCPA compliance if California users
   - Data collection disclosure

---

## 15. Recommended Implementation Timeline

### Week 1-2: Foundation
- Set up Rails 8 app
- Configure Hotwire, Propshaft, Importmaps
- Set up authentication (Devise)
- Create database schema
- Set up deployment pipeline

### Week 3-4: Bible Text
- Obtain Bible text (licensed or public domain)
- Create import scripts
- Import Bible data
- Set up full-text search
- Test data integrity

### Week 5-6: Reading Interface
- Build book/chapter navigation
- Create verse display
- Implement red letter styling
- Add responsive design
- Test reading experience

### Week 7-8: User Features
- Implement bookmarks
- Add notes with ActionText
- Create user dashboard
- Build bookmark/note management
- Test with real users

### Week 9-10: Polish & Deploy
- Add search functionality
- Implement caching
- Performance optimization
- Security audit
- Deploy to production
- Beta testing

### Week 11-12: Launch
- Final testing
- Documentation
- Marketing materials
- Soft launch
- Gather feedback
- Iterate based on feedback

---

## 16. Key Takeaways & Recommendations

### ✅ DO:
1. **Get proper licensing** for Amplified Bible text
2. **Use SQLite** for all data storage - it's perfect for this use case
3. **Leverage Rails 8** features - they're built for this stack
4. **Start simple** with MVP, add features iteratively
5. **Focus on reading experience** - make it delightful
6. **Test with real users** early and often
7. **Plan for mobile** from day one (Hotwire Native later)

### ❌ DON'T:
1. **Don't scrape** Bible text - legal nightmare
2. **Don't use YAML** for primary storage - too slow
3. **Don't over-engineer** - Rails 8 + SQLite is enough
4. **Don't skip licensing** - protect yourself legally
5. **Don't ignore performance** - optimize from the start
6. **Don't forget backups** - user data is precious

### 🎯 Success Metrics:
- Fast page loads (<200ms)
- High user engagement (daily active users)
- Low bounce rate
- Positive user feedback
- Growing bookmark/note creation
- Mobile-friendly (responsive design)

---

## 17. Quick Start Commands

```bash
# Create new Rails 8 app
rails new amped --css=tailwind --javascript=importmap --database=sqlite3

# Add essential gems
bundle add devise
bundle add pundit
bundle add pagy

# Setup authentication
rails generate devise:install
rails generate devise User

# Create Bible models
rails generate model Book name:string abbreviation:string testament:integer position:integer
rails generate model Chapter book:references number:integer
rails generate model Verse chapter:references number:integer text:text red_letter:boolean

# Create user features
rails generate model Bookmark user:references verse:references color:string
rails generate model Note user:references verse:references private:boolean
rails active_text:install

# Run migrations
rails db:migrate

# Import Bible data
rails bible:import
rails bible:setup_fts

# Start server
rails server
```

---

## 18. Resources & References

### Documentation
- Rails Guides: https://guides.rubyonrails.org/
- Hotwire: https://hotwired.dev/
- Stimulus: https://stimulus.hotwired.dev/
- Turbo: https://turbo.hotwired.dev/
- Tailwind CSS: https://tailwindcss.com/

### Bible Resources
- Lockman Foundation: https://www.lockman.org/
- API.Bible: https://scripture.api.bible/
- SWORD Project: https://crosswire.org/
- Bible Gateway: https://www.biblegateway.com/

### Community
- Rails Forum: https://discuss.rubyonrails.org/
- Hotwire Discussion: https://discuss.hotwired.dev/
- Stack Overflow: https://stackoverflow.com/questions/tagged/ruby-on-rails

### Tutorials
- Hotrails.dev: https://www.hotrails.dev/
- GoRails: https://gorails.com/
- Drifting Ruby: https://www.driftingruby.com/

---

## Conclusion

This plan provides a comprehensive roadmap for building **Amped**, a Bible reading website with bookmarks and notes using Rails 8, Hotwire, and SQLite. The architecture is modern, performant, and scalable while remaining simple to develop and deploy.

**Next Steps:**
1. Secure Bible text licensing
2. Set up Rails 8 application
3. Implement MVP features
4. Deploy and test with users
5. Iterate based on feedback

The stack (Rails 8 + Hotwire + SQLite) is perfect for this project, providing excellent performance, developer experience, and a clear path to mobile apps with Hotwire Native.

**Questions or need clarification?** Open an issue or discussion in the repository.

---

**Document Version:** 1.0  
**Last Updated:** November 2025  
**Author:** Amped Development Team
