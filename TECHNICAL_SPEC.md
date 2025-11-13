# Amped - Technical Specification

## Database Schema (SQLite)

### Complete Schema Definition

```ruby
# db/schema.rb

ActiveRecord::Schema[7.1].define(version: 2024_XX_XX_XXXXXX) do
  
  # Bible Content Tables
  create_table "books", force: :cascade do |t|
    t.string "name", null: false
    t.string "abbreviation", limit: 10
    t.integer "testament", null: false  # 1=OT, 2=NT
    t.integer "position", null: false   # Order in Bible
    t.integer "chapter_count"           # Cached count
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["name"], name: "index_books_on_name", unique: true
    t.index ["position"], name: "index_books_on_position", unique: true
    t.index ["testament"], name: "index_books_on_testament"
  end

  create_table "chapters", force: :cascade do |t|
    t.integer "book_id", null: false
    t.integer "number", null: false
    t.integer "verse_count"             # Cached count
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["book_id", "number"], name: "index_chapters_on_book_and_number", unique: true
    t.index ["book_id"], name: "index_chapters_on_book_id"
  end

  create_table "verses", force: :cascade do |t|
    t.integer "chapter_id", null: false
    t.integer "number", null: false
    t.text "text", null: false
    t.boolean "red_letter", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["chapter_id", "number"], name: "index_verses_on_chapter_and_number", unique: true
    t.index ["chapter_id"], name: "index_verses_on_chapter_id"
    t.index ["red_letter"], name: "index_verses_on_red_letter"
  end

  # User Tables
  create_table "users", force: :cascade do |t|
    t.string "email", default: "", null: false
    t.string "encrypted_password", default: "", null: false
    t.string "reset_password_token"
    t.datetime "reset_password_sent_at"
    t.datetime "remember_created_at"
    t.integer "sign_in_count", default: 0, null: false
    t.datetime "current_sign_in_at"
    t.datetime "last_sign_in_at"
    t.string "current_sign_in_ip"
    t.string "last_sign_in_ip"
    t.string "confirmation_token"
    t.datetime "confirmed_at"
    t.datetime "confirmation_sent_at"
    t.string "unconfirmed_email"
    t.string "name"
    t.string "username"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["confirmation_token"], name: "index_users_on_confirmation_token", unique: true
    t.index ["email"], name: "index_users_on_email", unique: true
    t.index ["reset_password_token"], name: "index_users_on_reset_password_token", unique: true
    t.index ["username"], name: "index_users_on_username", unique: true
  end

  # User Content Tables
  
  # Simple bookmarks - quick verse markers
  create_table "bookmarks", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "verse_id", null: false
    t.string "color"                    # hex color code
    t.string "tags", array: true        # For categorization
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id", "verse_id"], name: "index_bookmarks_on_user_and_verse", unique: true
    t.index ["user_id"], name: "index_bookmarks_on_user_id"
    t.index ["verse_id"], name: "index_bookmarks_on_verse_id"
    t.index ["created_at"], name: "index_bookmarks_on_created_at"
  end

  # Highlights - completely flexible character-level text selections
  # Can span ANY length: partial word, multiple verses, even multiple chapters
  create_table "highlights", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "start_verse_id", null: false   # Starting verse (any verse in Bible)
    t.integer "start_offset", null: false     # Character offset in start verse
    t.integer "end_verse_id", null: false     # Ending verse (can span chapters/books)
    t.integer "end_offset", null: false       # Character offset in end verse
    t.text "selected_text"                    # Denormalized for quick display
    t.string "color", default: "#FFEB3B"      # Highlight color
    t.string "tags", array: true
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id"], name: "index_highlights_on_user_id"
    t.index ["start_verse_id"], name: "index_highlights_on_start_verse"
    t.index ["end_verse_id"], name: "index_highlights_on_end_verse"
    t.index ["created_at"], name: "index_highlights_on_created_at"
  end

  # Notes - completely flexible, optional location context
  # Not tied to chapters or verses - pure content with optional location tracking
  create_table "notes", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "context_chapter_id"            # OPTIONAL - where user was when note created
    t.integer "context_verse_id"              # OPTIONAL - specific verse context if relevant
    t.boolean "private", default: true
    t.string "tags", array: true
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id"], name: "index_notes_on_user_id"
    t.index ["context_chapter_id"], name: "index_notes_on_context_chapter"
    t.index ["context_verse_id"], name: "index_notes_on_context_verse"
    t.index ["private"], name: "index_notes_on_private"
    t.index ["created_at"], name: "index_notes_on_created_at"
  end
  
  # Note verse references - optional verse tags within notes
  create_table "note_verse_references", force: :cascade do |t|
    t.integer "note_id", null: false
    t.integer "verse_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["note_id"], name: "index_note_verse_refs_on_note"
    t.index ["verse_id"], name: "index_note_verse_refs_on_verse"
  end

  # Action Text (for rich text notes)
  create_table "action_text_rich_texts", force: :cascade do |t|
    t.string "name", null: false
    t.text "body"
    t.string "record_type", null: false
    t.bigint "record_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["record_type", "record_id", "name"], name: "index_action_text_rich_texts_uniqueness", unique: true
  end

  # Active Storage (for note attachments)
  create_table "active_storage_blobs", force: :cascade do |t|
    t.string "key", null: false
    t.string "filename", null: false
    t.string "content_type"
    t.text "metadata"
    t.string "service_name", null: false
    t.bigint "byte_size", null: false
    t.string "checksum"
    t.datetime "created_at", null: false
    
    t.index ["key"], name: "index_active_storage_blobs_on_key", unique: true
  end

  create_table "active_storage_attachments", force: :cascade do |t|
    t.string "name", null: false
    t.string "record_type", null: false
    t.bigint "record_id", null: false
    t.bigint "blob_id", null: false
    t.datetime "created_at", null: false
    
    t.index ["record_type", "record_id", "name", "blob_id"], name: "index_active_storage_attachments_uniqueness", unique: true
    t.index ["blob_id"], name: "index_active_storage_attachments_on_blob_id"
  end

  create_table "active_storage_variant_records", force: :cascade do |t|
    t.bigint "blob_id", null: false
    t.string "variation_digest", null: false
    
    t.index ["blob_id", "variation_digest"], name: "index_active_storage_variant_records_uniqueness", unique: true
  end

  # Collections (for organizing bookmarks)
  create_table "collections", force: :cascade do |t|
    t.integer "user_id", null: false
    t.string "name", null: false
    t.text "description"
    t.boolean "public", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id"], name: "index_collections_on_user_id"
  end

  create_table "collection_bookmarks", force: :cascade do |t|
    t.integer "collection_id", null: false
    t.integer "bookmark_id", null: false
    t.integer "position"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["collection_id", "bookmark_id"], name: "index_collection_bookmarks", unique: true
    t.index ["collection_id"], name: "index_collection_bookmarks_on_collection_id"
    t.index ["bookmark_id"], name: "index_collection_bookmarks_on_bookmark_id"
  end

  # Reading Plans (future feature)
  create_table "reading_plans", force: :cascade do |t|
    t.string "name", null: false
    t.text "description"
    t.integer "duration_days"
    t.boolean "public", default: true
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

  create_table "reading_plan_days", force: :cascade do |t|
    t.integer "reading_plan_id", null: false
    t.integer "day_number", null: false
    t.text "description"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["reading_plan_id", "day_number"], name: "index_plan_days", unique: true
  end

  create_table "reading_plan_passages", force: :cascade do |t|
    t.integer "reading_plan_day_id", null: false
    t.integer "start_verse_id", null: false
    t.integer "end_verse_id", null: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["reading_plan_day_id"], name: "index_plan_passages_on_day"
  end

  create_table "user_reading_plans", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "reading_plan_id", null: false
    t.date "start_date"
    t.integer "current_day", default: 1
    t.boolean "completed", default: false
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id", "reading_plan_id"], name: "index_user_plans"
  end

  # Foreign Keys
  add_foreign_key "chapters", "books"
  add_foreign_key "verses", "chapters"
  add_foreign_key "bookmarks", "users"
  add_foreign_key "bookmarks", "verses"
  add_foreign_key "highlights", "users"
  add_foreign_key "highlights", "verses", column: "start_verse_id"
  add_foreign_key "highlights", "verses", column: "end_verse_id"
  add_foreign_key "notes", "users"
  add_foreign_key "note_verse_references", "notes"
  add_foreign_key "note_verse_references", "verses"
  add_foreign_key "collections", "users"
  add_foreign_key "collection_bookmarks", "collections"
  add_foreign_key "collection_bookmarks", "bookmarks"
  add_foreign_key "reading_plan_days", "reading_plans"
  add_foreign_key "reading_plan_passages", "reading_plan_days"
  add_foreign_key "user_reading_plans", "users"
  add_foreign_key "user_reading_plans", "reading_plans"
  add_foreign_key "active_storage_attachments", "active_storage_blobs", column: "blob_id"
  add_foreign_key "active_storage_variant_records", "active_storage_blobs", column: "blob_id"
end
```

### Practical Highlight Examples

The highlight system supports any selection length. Here are real-world examples:

```ruby
# Example 1: Partial word highlight
# User selects just "od" from the word "God" in Genesis 1:1
Highlight.create!(
  user: current_user,
  start_verse_id: 1,      # Genesis 1:1
  start_offset: 20,       # Character position of 'o'
  end_verse_id: 1,        # Same verse
  end_offset: 22,         # Character position after 'd'
  selected_text: "od",
  color: "#FFEB3B"
)

# Example 2: Multiple verses in same chapter
# User highlights Genesis 1:1-5 (all 5 verses)
Highlight.create!(
  user: current_user,
  start_verse_id: 1,      # Genesis 1:1
  start_offset: 0,        # Start of verse 1
  end_verse_id: 5,        # Genesis 1:5
  end_offset: 82,         # End of verse 5
  selected_text: "In the beginning God created...[full text]...and there was evening",
  color: "#90CAF9"
)

# Example 3: Cross-chapter highlight
# User highlights Genesis 1:26 through Genesis 2:3
Highlight.create!(
  user: current_user,
  start_verse_id: 26,     # Genesis 1:26
  start_offset: 0,
  end_verse_id: 34,       # Genesis 2:3 (assuming sequential IDs)
  end_offset: 95,
  selected_text: "Then God said, 'Let us make...[continues]...blessed the seventh day",
  color: "#A5D6A7"
)

# Example 4: Cross-book highlight (Old Testament to New Testament)
# User highlights Malachi 4:5-6 through Matthew 1:1
Highlight.create!(
  user: current_user,
  start_verse_id: 23143,  # Malachi 4:5
  start_offset: 0,
  end_verse_id: 23145,    # Matthew 1:1
  end_offset: 48,
  selected_text: "See, I will send the prophet...[gap]...The book of the genealogy",
  color: "#FFEB3B"
)

# Querying highlights
# Find all highlights spanning a specific verse
verse = Verse.find(15)
highlights = Highlight.where(
  '(start_verse_id <= ? AND end_verse_id >= ?)',
  verse.id, verse.id
)

# Find cross-chapter highlights
cross_chapter = Highlight.joins(:start_verse, :end_verse)
  .where('verses.chapter_id != end_verses_highlights.chapter_id')
```

### Practical Note Examples

Notes are completely flexible with optional context:

```ruby
# Example 1: Note with no context (standalone thought)
Note.create!(
  user: current_user,
  context_chapter_id: nil,   # No context
  context_verse_id: nil,      # No context
  content: "Key themes I'm noticing: grace, mercy, redemption",
  private: true
)

# Example 2: Note with chapter context
# User was reading Matthew 5 and started typing
Note.create!(
  user: current_user,
  context_chapter_id: 929,    # Matthew 5
  context_verse_id: nil,      # No specific verse
  content: "The Sermon on the Mount is revolutionary...",
  private: true
)

# Example 3: Note with verse context
# User clicked on John 3:16 then started typing
Note.create!(
  user: current_user,
  context_chapter_id: 1100,   # John 3
  context_verse_id: 26087,    # John 3:16
  content: "This verse changed my life. @Romans5:8 is similar.",
  private: true
)
# After save, @Romans5:8 becomes a NoteVerseReference

# Example 4: User removes context later
note = Note.find(123)
note.update(
  context_chapter_id: nil,
  context_verse_id: nil
)
# Note is now context-free but still has @verse references in content
```

### SQLite FTS5 Setup

```sql
-- Full-text search virtual table
CREATE VIRTUAL TABLE verses_fts USING fts5(
  verse_id UNINDEXED,
  book_name,
  book_abbreviation,
  chapter_number UNINDEXED,
  verse_number UNINDEXED,
  reference,
  text,
  content='verses',
  content_rowid='id'
);

-- Triggers to keep FTS table in sync
CREATE TRIGGER verses_fts_insert AFTER INSERT ON verses BEGIN
  INSERT INTO verses_fts(
    verse_id,
    book_name,
    book_abbreviation,
    chapter_number,
    verse_number,
    reference,
    text
  )
  SELECT
    new.id,
    books.name,
    books.abbreviation,
    chapters.number,
    new.number,
    books.name || ' ' || chapters.number || ':' || new.number,
    new.text
  FROM chapters
  JOIN books ON chapters.book_id = books.id
  WHERE chapters.id = new.chapter_id;
END;

CREATE TRIGGER verses_fts_delete AFTER DELETE ON verses BEGIN
  DELETE FROM verses_fts WHERE verse_id = old.id;
END;

CREATE TRIGGER verses_fts_update AFTER UPDATE ON verses BEGIN
  DELETE FROM verses_fts WHERE verse_id = old.id;
  INSERT INTO verses_fts(
    verse_id,
    book_name,
    book_abbreviation,
    chapter_number,
    verse_number,
    reference,
    text
  )
  SELECT
    new.id,
    books.name,
    books.abbreviation,
    chapters.number,
    new.number,
    books.name || ' ' || chapters.number || ':' || new.number,
    new.text
  FROM chapters
  JOIN books ON chapters.book_id = books.id
  WHERE chapters.id = new.chapter_id;
END;
```

---

## API Design (Future Mobile Apps)

### RESTful API Endpoints

```ruby
# config/routes.rb
namespace :api do
  namespace :v1 do
    # Public endpoints (no auth required)
    resources :books, only: [:index, :show] do
      resources :chapters, only: [:index, :show]
    end
    
    resources :verses, only: [:index, :show]
    
    get 'search', to: 'search#index'
    
    # Authenticated endpoints
    authenticate :user do
      resources :bookmarks do
        collection do
          post :toggle  # Toggle bookmark on/off
          post :batch   # Batch create/delete
        end
      end
      
      resources :notes
      
      resources :collections do
        member do
          post :add_bookmark
          delete :remove_bookmark
        end
      end
      
      get 'user/profile', to: 'users#show'
      put 'user/profile', to: 'users#update'
      
      get 'user/sync', to: 'sync#index'  # Sync data for offline
    end
  end
end
```

### API Response Formats

#### GET /api/v1/books
```json
{
  "books": [
    {
      "id": 1,
      "name": "Genesis",
      "abbreviation": "Gen",
      "testament": 1,
      "position": 1,
      "chapter_count": 50,
      "url": "/api/v1/books/1"
    },
    {
      "id": 2,
      "name": "Exodus",
      "abbreviation": "Exod",
      "testament": 1,
      "position": 2,
      "chapter_count": 40,
      "url": "/api/v1/books/2"
    }
  ],
  "meta": {
    "total": 66
  }
}
```

#### GET /api/v1/books/40/chapters/3
```json
{
  "chapter": {
    "id": 929,
    "number": 3,
    "verse_count": 18,
    "book": {
      "id": 40,
      "name": "Matthew",
      "abbreviation": "Matt"
    }
  },
  "verses": [
    {
      "id": 23145,
      "number": 1,
      "text": "In those days John the Baptist came, preaching in the wilderness of Judea",
      "red_letter": false,
      "reference": "Matthew 3:1"
    },
    {
      "id": 23146,
      "number": 2,
      "text": "and saying, "Repent, for the kingdom of heaven is at hand."",
      "red_letter": true,
      "reference": "Matthew 3:2"
    }
  ],
  "navigation": {
    "prev_chapter": "/api/v1/books/40/chapters/2",
    "next_chapter": "/api/v1/books/40/chapters/4"
  }
}
```

#### GET /api/v1/search?q=love
```json
{
  "results": [
    {
      "verse": {
        "id": 26087,
        "number": 8,
        "text": "But God demonstrates His own love toward us...",
        "red_letter": false,
        "reference": "Romans 5:8"
      },
      "book": {
        "name": "Romans",
        "abbreviation": "Rom"
      },
      "chapter": 5,
      "score": 0.95
    }
  ],
  "meta": {
    "query": "love",
    "total": 538,
    "page": 1,
    "per_page": 20
  }
}
```

#### POST /api/v1/bookmarks
```json
// Request
{
  "bookmark": {
    "verse_id": 23146,
    "color": "#FFD700",
    "tags": ["favorite", "memory"]
  }
}

// Response
{
  "bookmark": {
    "id": 42,
    "verse_id": 23146,
    "color": "#FFD700",
    "tags": ["favorite", "memory"],
    "created_at": "2024-11-13T21:30:00Z",
    "verse": {
      "reference": "Matthew 3:2",
      "text": "and saying, "Repent, for the kingdom of heaven is at hand.""
    }
  }
}
```

#### GET /api/v1/bookmarks
```json
{
  "bookmarks": [
    {
      "id": 42,
      "verse_id": 23146,
      "color": "#FFD700",
      "tags": ["favorite", "memory"],
      "created_at": "2024-11-13T21:30:00Z",
      "verse": {
        "reference": "Matthew 3:2",
        "text": "and saying, "Repent, for the kingdom of heaven is at hand."",
        "red_letter": true
      }
    }
  ],
  "meta": {
    "total": 87,
    "page": 1,
    "per_page": 20
  }
}
```

---

## Performance Benchmarks

### Expected Performance Metrics

| Operation | Target | Notes |
|-----------|--------|-------|
| Page load (chapter) | <200ms | With caching |
| Verse lookup | <5ms | With indexes |
| Full-text search | <50ms | FTS5 with ~30k verses |
| Bookmark toggle | <100ms | With Turbo Stream |
| Note save | <150ms | With ActionText |
| User dashboard | <300ms | With eager loading |

### SQLite Optimization Settings

```ruby
# config/initializers/sqlite3.rb
Rails.application.config.after_initialize do
  ActiveRecord::Base.connection_pool.with_connection do |conn|
    # Enable Write-Ahead Logging (WAL) mode
    conn.execute("PRAGMA journal_mode = WAL")
    
    # Normal synchronous mode (faster, still safe)
    conn.execute("PRAGMA synchronous = NORMAL")
    
    # Increase cache size (64MB)
    conn.execute("PRAGMA cache_size = -64000")
    
    # Memory-mapped I/O (128MB)
    conn.execute("PRAGMA mmap_size = 134217728")
    
    # Optimize for performance
    conn.execute("PRAGMA temp_store = MEMORY")
    
    # Set busy timeout (5 seconds)
    conn.execute("PRAGMA busy_timeout = 5000")
  end
end
```

### Caching Strategy

```ruby
# config/environments/production.rb
config.cache_store = :solid_cache_store

# Cache keys
CACHE_KEYS = {
  book_list: 'books:all',
  chapter: ->(chapter_id) { "chapter:#{chapter_id}:verses" },
  verse: ->(verse_id) { "verse:#{verse_id}" },
  user_bookmarks: ->(user_id) { "user:#{user_id}:bookmarks" },
  search: ->(query) { "search:#{Digest::MD5.hexdigest(query)}" }
}

# Cache TTL
CACHE_TTL = {
  bible_text: 1.week,      # Bible text rarely changes
  user_data: 5.minutes,     # User data changes more often
  search_results: 1.hour    # Search results
}
```

---

## Security Implementation

### Authentication with Devise

```ruby
# app/models/user.rb
class User < ApplicationRecord
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable,
         :confirmable, :trackable

  has_many :bookmarks, dependent: :destroy
  has_many :notes, dependent: :destroy
  has_many :collections, dependent: :destroy
  has_many :bookmarked_verses, through: :bookmarks, source: :verse
  
  validates :username, 
            presence: true, 
            uniqueness: { case_sensitive: false },
            format: { with: /\A[a-zA-Z0-9_]+\z/ },
            length: { minimum: 3, maximum: 20 }
  
  validates :email, presence: true, uniqueness: { case_sensitive: false }
  
  def bookmark_count
    bookmarks.count
  end
  
  def note_count
    notes.count
  end
end
```

### Authorization with Pundit

```ruby
# app/policies/application_policy.rb
class ApplicationPolicy
  attr_reader :user, :record

  def initialize(user, record)
    @user = user
    @record = record
  end

  def index?
    false
  end

  def show?
    false
  end

  def create?
    false
  end

  def new?
    create?
  end

  def update?
    false
  end

  def edit?
    update?
  end

  def destroy?
    false
  end

  class Scope
    def initialize(user, scope)
      @user = user
      @scope = scope
    end

    def resolve
      raise NotImplementedError
    end

    private

    attr_reader :user, :scope
  end
end

# app/policies/note_policy.rb
class NotePolicy < ApplicationPolicy
  def show?
    record.user == user || !record.private?
  end

  def create?
    user.present?
  end

  def update?
    record.user == user
  end

  def destroy?
    record.user == user
  end

  class Scope < Scope
    def resolve
      if user
        scope.where(user: user).or(scope.where(private: false))
      else
        scope.where(private: false)
      end
    end
  end
end

# app/policies/bookmark_policy.rb
class BookmarkPolicy < ApplicationPolicy
  def create?
    user.present?
  end

  def destroy?
    record.user == user
  end

  class Scope < Scope
    def resolve
      scope.where(user: user)
    end
  end
end
```

### Rate Limiting

```ruby
# Gemfile
gem 'rack-attack'

# config/initializers/rack_attack.rb
class Rack::Attack
  # Throttle all requests by IP
  throttle('req/ip', limit: 300, period: 5.minutes) do |req|
    req.ip
  end

  # Throttle login attempts
  throttle('logins/email', limit: 5, period: 20.seconds) do |req|
    if req.path == '/users/sign_in' && req.post?
      req.params['email'].to_s.downcase.gsub(/\s+/, '')
    end
  end

  # Throttle API requests
  throttle('api/ip', limit: 100, period: 1.minute) do |req|
    req.ip if req.path.start_with?('/api/')
  end
end
```

---

## Frontend Implementation

### Stimulus Controllers

```javascript
// app/javascript/controllers/bookmark_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["button", "icon"]
  static values = {
    verseId: Number,
    bookmarked: Boolean
  }

  toggle() {
    const url = `/verses/${this.verseIdValue}/bookmark`
    const method = this.bookmarkedValue ? "DELETE" : "POST"
    
    fetch(url, {
      method: method,
      headers: {
        "X-CSRF-Token": this.csrfToken,
        "Accept": "text/vnd.turbo-stream.html"
      }
    })
    .then(response => response.text())
    .then(html => Turbo.renderStreamMessage(html))
  }

  get csrfToken() {
    return document.querySelector("[name='csrf-token']").content
  }
}
```

```javascript
// app/javascript/controllers/search_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["input", "results"]
  static values = { url: String }

  connect() {
    this.timeout = null
  }

  perform() {
    clearTimeout(this.timeout)
    
    this.timeout = setTimeout(() => {
      const query = this.inputTarget.value
      
      if (query.length < 3) {
        this.resultsTarget.innerHTML = ""
        return
      }
      
      const url = `${this.urlValue}?q=${encodeURIComponent(query)}`
      
      fetch(url, {
        headers: { "Accept": "text/vnd.turbo-stream.html" }
      })
      .then(response => response.text())
      .then(html => Turbo.renderStreamMessage(html))
    }, 300) // Debounce 300ms
  }
}
```

```javascript
// app/javascript/controllers/text_highlighter_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static values = {
    chapterId: Number,
    userId: Number
  }

  connect() {
    // Add selection event listener
    this.element.addEventListener('mouseup', this.handleSelection.bind(this))
    this.element.addEventListener('touchend', this.handleSelection.bind(this))
  }

  handleSelection(event) {
    const selection = window.getSelection()
    const selectedText = selection.toString().trim()
    
    if (selectedText.length === 0) return
    
    // Get selection range details
    const range = selection.getRangeAt(0)
    const startContainer = range.startContainer
    const endContainer = range.endContainer
    
    // Find verse elements
    const startVerse = this.findVerseElement(startContainer)
    const endVerse = this.findVerseElement(endContainer)
    
    if (!startVerse || !endVerse) return
    
    // Calculate character offsets within verses
    const startOffset = this.getCharacterOffset(startVerse, startContainer, range.startOffset)
    const endOffset = this.getCharacterOffset(endVerse, endContainer, range.endOffset)
    
    // Show highlight menu
    this.showHighlightMenu(event, {
      startVerseId: startVerse.dataset.verseId,
      endVerseId: endVerse.dataset.verseId,
      startOffset: startOffset,
      endOffset: endOffset,
      selectedText: selectedText
    })
  }

  findVerseElement(node) {
    while (node && node !== this.element) {
      if (node.classList && node.classList.contains('verse')) {
        return node
      }
      node = node.parentNode
    }
    return null
  }

  getCharacterOffset(verseElement, node, offset) {
    // Calculate character offset from start of verse text
    const range = document.createRange()
    range.setStart(verseElement, 0)
    range.setEnd(node, offset)
    return range.toString().length
  }

  showHighlightMenu(event, selectionData) {
    // Create and position floating menu
    const menu = document.createElement('div')
    menu.className = 'highlight-menu'
    menu.innerHTML = `
      <button data-action="click->text-highlighter#createHighlight" 
              data-color="#FFEB3B">Yellow</button>
      <button data-action="click->text-highlighter#createHighlight" 
              data-color="#A5D6A7">Green</button>
      <button data-action="click->text-highlighter#createHighlight" 
              data-color="#90CAF9">Blue</button>
      <button data-action="click->text-highlighter#createNote">Note</button>
    `
    
    // Store selection data
    this.selectionData = selectionData
    
    // Position menu near selection
    menu.style.position = 'absolute'
    menu.style.top = `${event.pageY + 10}px`
    menu.style.left = `${event.pageX}px`
    
    document.body.appendChild(menu)
    this.currentMenu = menu
  }

  createHighlight(event) {
    const color = event.target.dataset.color
    
    fetch('/highlights', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'X-CSRF-Token': this.csrfToken
      },
      body: JSON.stringify({
        highlight: {
          chapter_id: this.chapterIdValue,
          start_verse_id: this.selectionData.startVerseId,
          start_offset: this.selectionData.startOffset,
          end_verse_id: this.selectionData.endVerseId,
          end_offset: this.selectionData.endOffset,
          selected_text: this.selectionData.selectedText,
          color: color
        }
      })
    })
    .then(response => response.json())
    .then(data => {
      this.applyHighlight(data.highlight)
      this.closeMenu()
    })
  }

  applyHighlight(highlight) {
    // Apply visual highlight to the selected text
    // This would wrap the selected text in a <mark> element
    // Implementation depends on specific DOM structure
  }

  closeMenu() {
    if (this.currentMenu) {
      this.currentMenu.remove()
      this.currentMenu = null
    }
    window.getSelection().removeAllRanges()
  }

  get csrfToken() {
    return document.querySelector("[name='csrf-token']").content
  }
}
```

```javascript
// app/javascript/controllers/verse_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["text"]
  
  highlight() {
    this.textTarget.classList.add("bg-yellow-200", "transition-colors")
    
    setTimeout(() => {
      this.textTarget.classList.remove("bg-yellow-200")
    }, 2000)
  }
  
  copy() {
    const text = this.textTarget.textContent
    navigator.clipboard.writeText(text)
    
    // Show notification
    this.dispatch("copied", { detail: { text } })
  }
}
```

### Tailwind CSS Configuration

```javascript
// tailwind.config.js
module.exports = {
  content: [
    './app/views/**/*.html.erb',
    './app/helpers/**/*.rb',
    './app/javascript/**/*.js',
    './app/components/**/*.{rb,erb}'
  ],
  theme: {
    extend: {
      colors: {
        'red-letter': '#c41e3a',
        'red-letter-dark': '#ff6b6b',
        'page-bg': '#FFFEF7',
        'page-bg-dark': '#1a1a1a',
      },
      fontFamily: {
        'bible': ['Georgia', 'Baskerville', 'serif'],
        'sans': ['Inter', 'sans-serif'],
      },
      fontSize: {
        'verse': ['18px', { lineHeight: '1.8' }],
      },
      gridTemplateColumns: {
        'bible-pages': 'repeat(2, minmax(0, 600px))',
      },
      columnCount: {
        'bible': '2',
      },
      columnGap: {
        'bible': '3rem',
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
}
```

### CSS for Multi-Column and Side-by-Side Layout

```css
/* app/assets/stylesheets/bible_layout.css */

/* Base reading container */
.bible-container {
  max-width: 100%;
  margin: 0 auto;
  padding: 1rem;
}

/* Single column - mobile portrait */
@media (max-width: 768px) {
  .bible-text {
    column-count: 1;
    padding: 1rem;
  }
}

/* Two columns - tablet portrait / small desktop */
@media (min-width: 769px) and (max-width: 1200px) {
  .bible-text {
    column-count: 2;
    column-gap: 3rem;
    column-rule: 1px solid rgba(0, 0, 0, 0.1);
    padding: 2rem 3rem;
    max-width: 900px;
    margin: 0 auto;
  }
  
  /* Prevent verses from breaking across columns */
  .verse {
    break-inside: avoid;
    page-break-inside: avoid;
  }
}

/* Side-by-side pages - tablet landscape / large desktop */
@media (min-width: 1201px) {
  .reading-view {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 600px));
    gap: 2rem;
    justify-content: center;
    padding: 2rem;
  }
  
  .bible-page {
    background: var(--page-bg, #FFFEF7);
    padding: 3rem;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    border-radius: 4px;
    max-height: 90vh;
    overflow-y: auto;
  }
  
  /* Smooth scrolling */
  .bible-page {
    scroll-behavior: smooth;
  }
}

/* Physical Bible styling */
.verse {
  margin-bottom: 0.5rem;
  line-height: 1.8;
}

.verse-number {
  font-size: 0.75em;
  vertical-align: super;
  color: rgba(0, 0, 0, 0.5);
  margin-right: 0.25rem;
  user-select: none;
}

.verse-text {
  font-family: Georgia, Baskerville, serif;
  font-size: 18px;
}

/* Red letter text */
.red-letter {
  color: #c41e3a;
  font-weight: 500;
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  .bible-page {
    background: var(--page-bg-dark, #1a1a1a);
    color: #e0e0e0;
  }
  
  .red-letter {
    color: #ff6b6b;
  }
  
  .verse-number {
    color: rgba(255, 255, 255, 0.5);
  }
  
  @media (min-width: 769px) and (max-width: 1200px) {
    .bible-text {
      column-rule-color: rgba(255, 255, 255, 0.1);
    }
  }
}

/* Highlight styles */
.highlight {
  padding: 2px 0;
  border-radius: 2px;
  cursor: pointer;
}

.highlight[data-color="#FFEB3B"] { background-color: rgba(255, 235, 59, 0.4); }
.highlight[data-color="#A5D6A7"] { background-color: rgba(165, 214, 167, 0.4); }
.highlight[data-color="#90CAF9"] { background-color: rgba(144, 202, 249, 0.4); }

/* Highlight menu */
.highlight-menu {
  background: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  padding: 0.5rem;
  display: flex;
  gap: 0.5rem;
  z-index: 1000;
}

.highlight-menu button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: transform 0.1s;
}

.highlight-menu button:hover {
  transform: scale(1.05);
}

/* Text selection styling */
::selection {
  background-color: rgba(255, 235, 59, 0.3);
}
```

---

## Testing Strategy

### RSpec Setup

```ruby
# Gemfile (test group)
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
  gem 'faker'
  gem 'shoulda-matchers'
end

group :test do
  gem 'capybara'
  gem 'selenium-webdriver'
  gem 'database_cleaner-active_record'
  gem 'simplecov', require: false
end
```

### Model Tests

```ruby
# spec/models/verse_spec.rb
require 'rails_helper'

RSpec.describe Verse, type: :model do
  describe 'associations' do
    it { should belong_to(:chapter) }
    it { should have_many(:bookmarks) }
    it { should have_many(:notes) }
  end

  describe 'validations' do
    it { should validate_presence_of(:text) }
    it { should validate_presence_of(:number) }
    it { should validate_uniqueness_of(:number).scoped_to(:chapter_id) }
  end

  describe '#reference' do
    it 'returns full verse reference' do
      book = create(:book, name: 'John')
      chapter = create(:chapter, book: book, number: 3)
      verse = create(:verse, chapter: chapter, number: 16)
      
      expect(verse.reference).to eq('John 3:16')
    end
  end

  describe '#bookmarked_by?' do
    let(:user) { create(:user) }
    let(:verse) { create(:verse) }

    it 'returns true when user has bookmarked verse' do
      create(:bookmark, user: user, verse: verse)
      expect(verse.bookmarked_by?(user)).to be true
    end

    it 'returns false when user has not bookmarked verse' do
      expect(verse.bookmarked_by?(user)).to be false
    end
  end

  describe '.search' do
    it 'finds verses containing search term' do
      verse = create(:verse, text: 'For God so loved the world')
      create(:verse, text: 'Different text')
      
      results = Verse.search('loved')
      expect(results).to include(verse)
    end
  end
end
```

### Controller Tests

```ruby
# spec/requests/bookmarks_spec.rb
require 'rails_helper'

RSpec.describe 'Bookmarks', type: :request do
  let(:user) { create(:user) }
  let(:verse) { create(:verse) }

  before { sign_in user }

  describe 'POST /verses/:verse_id/bookmark' do
    it 'creates a bookmark' do
      expect {
        post "/verses/#{verse.id}/bookmark"
      }.to change(Bookmark, :count).by(1)
      
      expect(response).to have_http_status(:success)
    end

    it 'returns turbo stream' do
      post "/verses/#{verse.id}/bookmark"
      expect(response.media_type).to eq('text/vnd.turbo-stream.html')
    end
  end

  describe 'DELETE /verses/:verse_id/bookmark' do
    let!(:bookmark) { create(:bookmark, user: user, verse: verse) }

    it 'removes bookmark' do
      expect {
        delete "/verses/#{verse.id}/bookmark"
      }.to change(Bookmark, :count).by(-1)
    end
  end
end
```

### System Tests

```ruby
# spec/system/reading_spec.rb
require 'rails_helper'

RSpec.describe 'Reading', type: :system do
  let(:user) { create(:user) }
  
  before do
    sign_in user
    create_bible_content
  end

  it 'allows reading a chapter' do
    visit root_path
    click_link 'John'
    click_link 'Chapter 3'
    
    expect(page).to have_content('John 3')
    expect(page).to have_css('.verse', minimum: 36)
  end

  it 'allows bookmarking a verse' do
    visit read_path(book: 'john', chapter: 3)
    
    within('.verse[data-number="16"]') do
      click_button 'Bookmark'
      expect(page).to have_css('.bookmarked')
    end
    
    visit bookmarks_path
    expect(page).to have_content('John 3:16')
  end

  it 'allows adding a note' do
    visit read_path(book: 'john', chapter: 3)
    
    within('.verse[data-number="16"]') do
      click_button 'Add Note'
    end
    
    fill_in 'note_content', with: 'This is my favorite verse'
    click_button 'Save Note'
    
    expect(page).to have_content('Note saved')
  end
end
```

---

## Deployment Guide

### Fly.io Deployment

```toml
# fly.toml
app = "amped-bible"
primary_region = "iad"

[build]
  [build.args]
    RUBY_VERSION = "3.2.2"
    BUNDLER_VERSION = "2.4.19"

[env]
  RAILS_ENV = "production"
  RAILS_LOG_TO_STDOUT = "true"
  RAILS_SERVE_STATIC_FILES = "true"

[http_service]
  internal_port = 3000
  force_https = true
  auto_stop_machines = true
  auto_start_machines = true
  min_machines_running = 1
  processes = ["app"]

[[statics]]
  guest_path = "/app/public"
  url_prefix = "/"

[[vm]]
  cpu_kind = "shared"
  cpus = 1
  memory_mb = 512

[mounts]
  source = "amped_data"
  destination = "/app/storage"
```

```bash
# Deploy to Fly.io
fly launch
fly volumes create amped_data --size 1
fly deploy
fly ssh console -C "rails db:migrate"
fly ssh console -C "rails bible:import"
```

### Environment Variables

```bash
# .env.production
RAILS_ENV=production
SECRET_KEY_BASE=your_secret_key_base_here
DATABASE_URL=sqlite3:storage/production.sqlite3
RAILS_LOG_TO_STDOUT=enabled
RAILS_SERVE_STATIC_FILES=enabled

# Email (for Devise)
SMTP_ADDRESS=smtp.sendgrid.net
SMTP_PORT=587
SMTP_USERNAME=apikey
SMTP_PASSWORD=your_sendgrid_api_key
SMTP_DOMAIN=yourdomain.com
EMAIL_FROM=noreply@yourdomain.com

# AWS S3 (for ActiveStorage in production)
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_REGION=us-east-1
AWS_BUCKET=amped-bible-storage
```

---

## Monitoring & Analytics

### Error Tracking

```ruby
# Gemfile
gem 'sentry-ruby'
gem 'sentry-rails'

# config/initializers/sentry.rb
Sentry.init do |config|
  config.dsn = ENV['SENTRY_DSN']
  config.breadcrumbs_logger = [:active_support_logger, :http_logger]
  config.traces_sample_rate = 0.5
  config.environment = Rails.env
end
```

### Application Metrics

```ruby
# Gemfile
gem 'prometheus_exporter'

# config/initializers/prometheus.rb
unless Rails.env.test?
  require 'prometheus_exporter/middleware'
  
  Rails.application.middleware.unshift PrometheusExporter::Middleware
end
```

### User Analytics

```erb
<!-- app/views/layouts/application.html.erb -->
<!-- Simple, privacy-focused analytics -->
<% if Rails.env.production? %>
  <script defer data-domain="yourdomain.com" 
          src="https://plausible.io/js/script.js"></script>
<% end %>
```

---

## Conclusion

This technical specification provides the complete database schema, API design, performance optimizations, security implementation, and deployment guide for the Amped Bible reading platform. The architecture is production-ready and optimized for the Rails 8 + Hotwire + SQLite stack.

**Next steps:**
1. Implement database migrations
2. Create models with associations
3. Build API endpoints
4. Develop frontend with Hotwire
5. Deploy to production

For questions or contributions, see the main PLAN.md document.
