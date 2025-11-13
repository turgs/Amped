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

  create_table "notes", force: :cascade do |t|
    t.integer "user_id", null: false
    t.integer "verse_id", null: false
    t.boolean "private", default: true
    t.string "tags", array: true
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    
    t.index ["user_id", "verse_id"], name: "index_notes_on_user_and_verse"
    t.index ["user_id"], name: "index_notes_on_user_id"
    t.index ["verse_id"], name: "index_notes_on_verse_id"
    t.index ["private"], name: "index_notes_on_private"
    t.index ["created_at"], name: "index_notes_on_created_at"
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
  add_foreign_key "notes", "users"
  add_foreign_key "notes", "verses"
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
      },
      fontFamily: {
        'bible': ['Georgia', 'serif'],
        'sans': ['Inter', 'sans-serif'],
      },
      fontSize: {
        'verse': ['18px', { lineHeight: '1.8' }],
      }
    },
  },
  plugins: [
    require('@tailwindcss/forms'),
    require('@tailwindcss/typography'),
  ],
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
