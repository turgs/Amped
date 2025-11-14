# Bible Data Acquisition Guide

## Overview

This guide provides detailed information on how to obtain Bible text data, specifically focusing on the Amplified Bible with red letter formatting. It includes legal considerations, technical approaches, and practical steps.

---

## ⚖️ Legal & Copyright Considerations

### Copyright Status of Bible Translations

**CRITICAL:** Most modern Bible translations are copyrighted and require licensing.

| Translation | Copyright Status | License Required |
|-------------|------------------|------------------|
| **Amplified Bible (AMP)** | © Lockman Foundation | **YES** |
| Modern English Version (MEV) | © Charisma House | **YES** (verify) |
| World English Bible (WEB) | Public Domain | NO |
| American Standard Version (ASV) | Public Domain | NO |
| King James Version (KJV) | Public Domain | NO |
| New International Version (NIV) | © Biblica | **YES** |
| English Standard Version (ESV) | © Crossway | **YES** |
| New Living Translation (NLT) | © Tyndale | **YES** |

### Amplified Bible Licensing

**Copyright Holder:** The Lockman Foundation  
**Website:** https://www.lockman.org/  
**Contact:** permissions@lockman.org

#### Licensing Options:

1. **Print Rights Only**
   - Books, study guides, devotionals
   - Price: Varies based on print run

2. **Electronic/Digital Rights**
   - **Websites and web applications**
   - **Mobile applications**
   - Bible software
   - Price: Typically $500-$5,000+ annually

3. **Limited Use Quotations**
   - Up to 500 verses without permission
   - Up to 50% of a single book
   - Up to 25% of entire work
   - **NOT sufficient for a full Bible reading app**

#### Application Process:

```
1. Contact Lockman Foundation
   Email: permissions@lockman.org
   Phone: (714) 879-3055

2. Describe Your Project:
   - Website/mobile app
   - Target audience size
   - Free or paid service
   - Features (reading, search, notes)

3. Negotiate Terms:
   - Usage fees
   - Attribution requirements
   - Term length
   - Renewal options

4. Sign License Agreement

5. Receive Text Files:
   - Usually XML or JSON format
   - With red letter markup
   - Includes verse numbers, chapter/book structure
```

#### Expected Costs:

- **Small website (<1,000 users):** $500-$1,500/year
- **Medium app (1,000-10,000 users):** $1,500-$5,000/year
- **Large app (10,000+ users):** $5,000+/year + revenue share

---

## 🔍 ALL Options for Obtaining Amplified Bible (For Legal Assessment)

**Context:** This section documents ALL possible ways to obtain Amplified Bible text for private testing and legal assessment, including legal implications of each approach.

### Option 1: Official Licensing (RECOMMENDED - ONLY LEGAL OPTION FOR PRODUCTION)

**How:**
- Contact Lockman Foundation directly
- Request evaluation/development license
- May offer reduced-rate testing license

**Legal Status:** ✅ Fully legal and proper  
**Cost:** $500-$5,000/year (may negotiate evaluation period)  
**Data Quality:** ✅ Highest - official, verified text with red letter markup  
**Risk:** None  
**Timeline:** 1-4 weeks for response and approval

**Recommendation for Your Situation:**
- Request a "development and evaluation license" from Lockman Foundation
- Explain you need to assess suitability before committing to full license
- They may offer short-term or reduced-cost evaluation license
- This is the ONLY legally sound approach for any use

### Option 2: Purchase Physical/Digital Copy for Manual Data Entry

**How:**
- Purchase Amplified Bible (print or digital)
- Manually type out sample passages for testing
- Limited to small samples for evaluation

**Legal Status:** ⚠️ Legal for small samples under "personal use"  
**Cost:** $15-50 for book/ebook  
**Data Quality:** ⚠️ Manual entry prone to errors  
**Risk:** Low if kept truly private; cannot be used in production  
**Limitations:** 
- Extremely time-consuming
- Only feasible for small samples (few chapters)
- Cannot be used for full Bible
- Cannot be distributed or used publicly

**Recommendation:** Only viable for testing 1-2 sample chapters to evaluate format/layout.

### Option 3: Third-Party Bible Software APIs

**How:**
- Use Bible Gateway API, YouVersion API, or similar
- Access through their API for development/testing
- Subject to their terms of service

**Examples:**
- Bible Gateway API (discontinued for new signups)
- YouVersion Bible API (requires partnership)
- Biblia API (check if Amplified is available)

**Legal Status:** ⚠️ Depends on API provider's terms  
**Cost:** Varies (some have free tiers)  
**Data Quality:** ✅ Good - official text  
**Risk:** Medium - must comply with API terms, may not allow full export  
**Limitations:**
- Often rate-limited
- May not allow downloading/caching full text
- May not have red letter formatting
- Not suitable for production use (dependent on third party)

**Current Reality:** Most APIs don't provide Amplified Bible or severely restrict usage.

### Option 4: Purchase Bible Software with Export Capability

**How:**
- Buy software like Logos, Accordance, or BibleWorks
- Some versions allow text export for personal use
- Check license carefully

**Legal Status:** ⚠️ Check End User License Agreement (EULA)  
**Cost:** $50-500+ for software  
**Data Quality:** ✅ Good - official text  
**Risk:** Medium-High - likely violates EULA to use commercially  
**Limitations:**
- Most EULAs prohibit commercial redistribution
- Export features often restricted or removed
- Cannot be used for production website/app

**Reality:** This approach is NOT appropriate for commercial use and likely violates the software's EULA.

### Option 5: Obtain from Existing Digital Sources (NOT RECOMMENDED)

**Examples:**
- Downloaded files from file-sharing sites
- Bible databases on GitHub without proper licensing
- Scraped from websites

**Legal Status:** ❌ ILLEGAL - Copyright infringement  
**Cost:** Free but illegal  
**Data Quality:** Varies (often has errors)  
**Risk:** ⚠️ **VERY HIGH** - Criminal and civil liability

**Legal Implications:**
1. **Copyright Infringement** - Violation of 17 U.S.C. § 501
2. **DMCA Violations** - If circumventing protection measures
3. **Computer Fraud and Abuse Act (CFAA)** - If accessing without authorization
4. **Terms of Service Violations** - If scraping websites
5. **Potential Damages:**
   - Statutory damages: $750-$30,000 per work (up to $150,000 for willful infringement)
   - Actual damages plus infringer's profits
   - Attorney's fees
   - Injunction preventing use of your app

**IMPORTANT:** 
- Using unauthorized copies, even for "private testing," creates legal exposure
- Digital evidence is easily discoverable in litigation
- "We were just testing" is not a legal defense
- Once code is written against pirated text, it's harder to switch later

### Option 6: Request Sample Data from Lockman Foundation

**How:**
- Contact Lockman Foundation
- Request sample chapters/books for evaluation
- Explain need for technical and suitability assessment

**Legal Status:** ✅ Legal if granted permission  
**Cost:** Possibly free or nominal fee  
**Data Quality:** ✅ Highest - official sample  
**Risk:** None if permission granted  
**Likelihood:** Good - publishers often provide samples for legitimate evaluation

**Recommendation:** This is a reasonable middle ground:
- Shows good faith to copyright holder
- Provides enough data for technical testing
- Establishes relationship for future licensing
- Legally protected

---

## 🎯 Recommended Approach for Your Legal Team

### For Private Testing & Suitability Assessment:

**Path 1: Sample/Evaluation License (BEST)**
```
1. Contact Lockman Foundation immediately
   - Email: permissions@lockman.org
   - Subject: "Evaluation License Request - Web Application Development"
   
2. In your email, explain:
   - Building Bible reading web application
   - Need to assess Amplified Bible's suitability
   - Want to test technical implementation
   - Seek short-term evaluation license before full commitment
   - Target audience and app features
   
3. Offer to sign NDA or restricted evaluation agreement
   
4. Request sample data or evaluation license
   - Suggest: 3-6 month evaluation period
   - Limited to development/testing environment
   - Commit to full license if proceeding to production
```

**Path 2: Limited Sample for Initial Testing**
```
1. Request 2-3 sample books from Lockman Foundation
   - Enough to test layout, red letter formatting, search
   - Build and test core features
   - Assess user experience
   
2. Use free WEB Bible for remainder of development
   
3. Apply for full license once commitment is made
```

### Legal Analysis Summary:

| Option | Legality | Practicality | Risk | Recommended |
|--------|----------|--------------|------|-------------|
| Official License | ✅ Legal | ✅ High | None | ✅ YES |
| Sample/Eval License | ✅ Legal | ✅ High | None | ✅ YES |
| Manual Entry (small samples) | ⚠️ Grey Area | ❌ Low | Low | ⚠️ Only tiny samples |
| API Access | ⚠️ Depends on Terms | ⚠️ Medium | Medium | ⚠️ Check terms carefully |
| Bible Software Export | ❌ Likely violates EULA | ⚠️ Medium | High | ❌ NO |
| Unauthorized Download | ❌ ILLEGAL | ❌ High | ⚠️ VERY HIGH | ❌ NEVER |

---

## 📖 Alternative: Free Public Domain Bible for Development

If Amplified Bible licensing is not feasible initially, consider these free alternatives:

### 1. World English Bible (WEB) - RECOMMENDED (100% FREE)

**Pros:**
- **100% Public domain** - Completely free, no licensing needed
- **Modern English** - No archaic language
- Actively maintained
- Similar structure to commercial translations
- Good readability
- **Guaranteed free forever** - No ambiguity

**Cons:**
- Less well-known than MEV or other modern translations
- Not as widely used as NIV or ESV

**Source:**
- https://ebible.org/web/ (Multiple formats: XML, SQL, etc.)
- https://github.com/thiagobodruk/bible (JSON format)

**Recommendation:** 
- **Use WEB for all development and testing**
- 100% free with no licensing concerns
- Easy transition to Amplified Bible for production
- No risk, no cost, immediate availability

### 2. Modern English Version (MEV) - Check Licensing

**Note:** MEV is NOT public domain.

**Pros:**
- **Modern, readable English** - Contemporary language
- Good translation quality
- No archaic "thee/thou" language

**Cons:**
- **Copyrighted by Charisma House** - Requires licensing verification
- May require commercial license fees
- Licensing terms unclear for web applications
- Not recommended without confirmed free license

**Recommendation:** 
- Do NOT use MEV unless you confirm it's free for your use case
- Contact Charisma House to verify licensing requirements
- Use WEB instead - guaranteed free

### 3. King James Version (KJV) - LEGACY OPTION

**Note:** Not recommended for this project due to archaic language.

**Pros:**
- Most well-known translation
- Public domain - no licensing needed

**Cons:**
- Archaic English (thee, thou, etc.)
- Less accessible to modern readers

**Sources:**
- https://github.com/scrollmapper/bible_databases (KJV in SQLite)

### 4. American Standard Version (ASV)

**Pros:**
- Public domain
- More literal translation
- Good for study

**Cons:**
- Slightly archaic (1901 translation)

---

## 🔌 Bible APIs (Third-Party Services)

### 1. API.Bible

**Website:** https://scripture.api.bible  
**Provider:** American Bible Society

#### Features:
- RESTful API
- Multiple translations (1,600+)
- JSON responses
- Audio Bible support
- Search functionality

#### Bible Availability:
- ❌ Amplified Bible NOT available (as of 2024)
- ❌ MEV NOT available (as of 2024)
- ✅ WEB, ASV available
- Check their catalog for updates

#### Pricing:
- **Free Tier:** 10,000 requests/month
- **Paid Plans:** $99+/month for higher limits

#### Example Usage:

```bash
# Get books
curl -X GET "https://api.scripture.api.bible/v1/bibles/de4e12af7f28f599-02/books" \
  -H "api-key: YOUR_API_KEY"

# Get chapter
curl -X GET "https://api.scripture.api.bible/v1/bibles/de4e12af7f28f599-02/chapters/JHN.3" \
  -H "api-key: YOUR_API_KEY"

# Search
curl -X GET "https://api.scripture.api.bible/v1/bibles/de4e12af7f28f599-02/search?query=love" \
  -H "api-key: YOUR_API_KEY"
```

#### Integration Strategy:
```ruby
# Import data from API once, store locally
namespace :bible do
  task import_from_api: :environment do
    api_key = ENV['BIBLE_API_KEY']
    bible_id = 'de4e12af7f28f599-02' # WEB or other available translation
    
    books = fetch_books(api_key, bible_id)
    
    books.each do |book_data|
      book = Book.create!(
        name: book_data['name'],
        abbreviation: book_data['abbreviation']
      )
      
      chapters = fetch_chapters(api_key, bible_id, book_data['id'])
      
      chapters.each do |chapter_data|
        chapter = book.chapters.create!(number: chapter_data['number'])
        
        verses_data = fetch_verses(api_key, bible_id, chapter_data['id'])
        
        verses_data.each do |verse_data|
          chapter.verses.create!(
            number: verse_data['number'],
            text: strip_html(verse_data['content'])
          )
        end
      end
    end
  end
end
```

### 2. Bible.org API

**Website:** https://bible.org/  
**Provider:** Bible.org

#### Features:
- NET Bible (free, copyrighted with permission for non-commercial use)
- Study notes
- Greek/Hebrew tools

#### Limitations:
- Not Amplified Bible
- Primarily NET Bible

### 3. Biblia API (Faithlife)

**Website:** https://bibliaapi.com/  
**Provider:** Faithlife (Logos Bible Software)

#### Features:
- Multiple translations
- **Amplified Bible available** (with proper subscription)
- Professional-grade API
- Rich metadata

#### Pricing:
- **Paid service:** Contact for pricing
- Likely $100-$500+/month

#### Amplified Bible:
- ✅ Available with appropriate license
- Requires both API subscription AND Amplified Bible license

---

## 📦 Downloadable Bible Databases

### 1. Bible Databases on GitHub

#### Repository: scrollmapper/bible_databases
**URL:** https://github.com/scrollmapper/bible_databases

**Contents:**
- SQLite databases
- Multiple translations (WEB, ASV available)
- Pre-structured tables
- Ready to use

**How to Use:**
```bash
# Clone repository
git clone https://github.com/scrollmapper/bible_databases.git

# Copy SQLite file
cp bible_databases/sqlite/kjv.db db/bibles/

# Import into your database
rake bible:import_from_sqlite
```

```ruby
# lib/tasks/import_sqlite.rake
namespace :bible do
  task import_from_sqlite: :environment do
    source_db = SQLite3::Database.new('db/bibles/kjv.db')
    
    books = source_db.execute('SELECT * FROM books')
    books.each do |book_row|
      book = Book.create!(
        name: book_row[1],
        testament: book_row[2]
      )
      
      verses = source_db.execute(
        'SELECT * FROM verses WHERE book_id = ?', 
        book_row[0]
      )
      
      # Import verses...
    end
  end
end
```

### 2. Zefania XML Bible Format

**Format:** XML
**Sources:** Multiple websites provide Zefania XML Bibles

**Example Structure:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<XMLBIBLE biblename="King James Version" type="x-bible">
  <BOOK bnumber="1" bname="Genesis">
    <CHAPTER cnumber="1">
      <VERS vnumber="1">
        In the beginning God created the heaven and the earth.
      </VERS>
      <VERS vnumber="2">
        And the earth was without form, and void...
      </VERS>
    </CHAPTER>
  </BOOK>
</XMLBIBLE>
```

**Parser Example:**
```ruby
require 'nokogiri'

namespace :bible do
  task import_zefania: :environment do
    xml = File.read('db/bibles/kjv.xml')
    doc = Nokogiri::XML(xml)
    
    doc.xpath('//BOOK').each do |book_node|
      book = Book.create!(
        name: book_node['bname'],
        position: book_node['bnumber'].to_i
      )
      
      book_node.xpath('CHAPTER').each do |chapter_node|
        chapter = book.chapters.create!(
          number: chapter_node['cnumber'].to_i
        )
        
        chapter_node.xpath('VERS').each do |verse_node|
          chapter.verses.create!(
            number: verse_node['vnumber'].to_i,
            text: verse_node.text.strip
          )
        end
      end
    end
  end
end
```

### 3. OSIS XML Format

**Format:** Open Scripture Information Standard (OSIS)
**Used by:** SWORD Project, Crosswire

**More complex but comprehensive format with rich metadata**

---

## 🛡️ Red Letter Text

### What is Red Letter Text?

"Red letter" refers to the words of Jesus Christ being printed in red ink, a tradition dating back to 1899.

### Obtaining Red Letter Data

#### Option 1: In Licensed Bible Data
If you license the Amplified Bible, request:
- XML or JSON format
- With `<span class="jesus">` or similar markup
- Or separate flag indicating Jesus' words

#### Option 2: Separate Red Letter Indexes
Some databases provide red letter information separately:

```json
{
  "red_letter_verses": [
    {
      "reference": "Matthew 5:3",
      "portions": [
        { "start": 0, "end": 45 }
      ]
    },
    {
      "reference": "Matthew 5:4",
      "portions": [
        { "start": 0, "end": 32 }
      ]
    }
  ]
}
```

#### Option 3: Manual Curation
For public domain Bibles, you may need to manually mark or use existing red letter editions.

#### Implementation in Database:

```ruby
# Option A: Boolean flag for entire verse
create_table :verses do |t|
  t.text :text
  t.boolean :red_letter, default: false
end

# Option B: Character ranges for partial verses
create_table :verses do |t|
  t.text :text
  t.json :red_letter_ranges  # [{"start": 10, "end": 50}]
end

# Option C: HTML with embedded markup
create_table :verses do |t|
  t.text :text  # "And Jesus said, <span class='red-letter'>I am the way</span>"
end
```

#### CSS Styling:

```css
/* styles/application.css */
.red-letter,
.jesus-words {
  color: #c41e3a;
  font-weight: 500;
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  .red-letter,
  .jesus-words {
    color: #ff6b6b;
  }
}
```

---

## 🚫 Why NOT to Scrape

### Legal Issues:
1. **Copyright Infringement**
   - Bible text is copyrighted
   - Scraping = unauthorized copying
   - Potential lawsuit

2. **Terms of Service Violation**
   - Most sites explicitly forbid scraping
   - Can result in IP bans
   - Legal action possible

3. **Computer Fraud and Abuse Act (CFAA)**
   - In USA, scraping can violate CFAA
   - Criminal penalties possible

### Technical Issues:
1. **Unstable**
   - Websites change structure
   - Your scraper breaks
   - Maintenance nightmare

2. **Rate Limiting / Bans**
   - Sites detect and block scrapers
   - IP bans
   - CAPTCHA challenges

3. **Incomplete Data**
   - May miss metadata
   - Red letter markup might be CSS-only
   - Footnotes, cross-references lost

4. **Quality Issues**
   - HTML parsing errors
   - Encoding problems
   - Missing verses

### Ethical Issues:
1. **Respect for copyright**
2. **Harm to source websites**
3. **Dishonest practice**

### Recommendation:
**NEVER scrape Bible websites. Always use licensed or public domain sources.**

---

## 📋 Recommended Approach: Step-by-Step

### Phase 1: Start with WEB for Development (Week 1-2)

**Objective:** Get app working with modern English Bible (100% free)

1. **Obtain WEB text**
   - Download from https://ebible.org/web/
   - 100% public domain, no licensing needed
   - Available in multiple formats: XML, SQL, JSON
   - SQLite format for easy import

2. **Import Data**
   ```bash
   rails bible:import_web
   ```

3. **Build Core Features**
   - Reading interface
   - Bookmarks
   - Notes
   - Search

4. **Test with Users**
   - Beta testing
   - Gather feedback
   - Validate concept

### Phase 2: Apply for Amplified Bible License (Week 3-4)

**While Phase 1 is running:**

1. **Contact Lockman Foundation**
   ```
   Email: permissions@lockman.org
   Subject: Bible App License Request - Amplified Bible
   ```

2. **Describe Project**
   - Purpose: Personal Bible reading and study
   - Platform: Web and mobile (future)
   - Audience: General public
   - Monetization: [Free/Freemium/Paid]
   - Features: Reading, bookmarks, notes, search

3. **Provide Demo**
   - Show working WEB version
   - Demonstrate reading interface
   - Professional presentation

4. **Negotiate Terms**
   - Review their pricing
   - Propose budget
   - Agree on terms

### Phase 3: Integrate Licensed Bible (Week 5-6)

**Once license obtained:**

1. **Receive Data Files**
   - XML, JSON, or SQL format
   - With red letter markup
   - Documentation

2. **Create Import Script**
   ```ruby
   rails bible:import_amplified
   ```

3. **Update Database**
   - Keep database structure same
   - Add new translation
   - Support multiple versions (future)

4. **Migration Strategy**
   ```ruby
   # Support multiple translations
   create_table :translations do |t|
     t.string :name
     t.string :abbreviation
   end
   
   add_column :verses, :translation_id, :integer
   
   # Or separate tables per translation
   ```

5. **Update UI**
   - Add translation selector
   - Migrate user bookmarks
   - Test thoroughly

### Phase 4: Launch (Week 7-8)

1. **Announcement**
2. **User migration guide**
3. **Marketing**

---

## 📊 Data Format Examples

### JSON Format (Recommended for Import)

```json
{
  "translation": "AMP",
  "books": [
    {
      "id": 1,
      "name": "Genesis",
      "abbreviation": "Gen",
      "testament": "OT",
      "chapters": [
        {
          "number": 1,
          "verses": [
            {
              "number": 1,
              "text": "In the beginning God (Elohim) created [by forming from nothing] the heavens and the earth.",
              "red_letter": false
            },
            {
              "number": 2,
              "text": "The earth was formless and void or a waste and emptiness, and darkness was upon the face of the deep [primeval ocean that covered the unformed earth]. The Spirit of God was moving (hovering, brooding) over the face of the waters.",
              "red_letter": false
            }
          ]
        }
      ]
    },
    {
      "id": 40,
      "name": "Matthew",
      "abbreviation": "Matt",
      "testament": "NT",
      "chapters": [
        {
          "number": 3,
          "verses": [
            {
              "number": 1,
              "text": "Now in those days John the Baptist appeared, preaching in the Wilderness (Desert) of Judea [west of the Jordan River and north of the Dead Sea],",
              "red_letter": false
            },
            {
              "number": 2,
              "text": "saying, "Repent [change your inner self—your old way of thinking, regret past sins, live your life in a way that proves repentance; seek God's purpose for your life], for the kingdom of heaven is at hand."",
              "red_letter": true
            }
          ]
        }
      ]
    }
  ]
}
```

### CSV Format (Simple but Less Structure)

```csv
book,chapter,verse,text,red_letter
Genesis,1,1,"In the beginning God created the heavens and the earth.",false
Genesis,1,2,"The earth was formless and void...",false
Matthew,3,1,"Now in those days John the Baptist appeared...",false
Matthew,3,2,"saying, Repent...",true
```

### SQL Dump (Direct Import)

```sql
-- books
INSERT INTO books (id, name, abbreviation, testament, position) VALUES
(1, 'Genesis', 'Gen', 1, 1),
(2, 'Exodus', 'Exod', 1, 2),
-- ... more books

-- chapters
INSERT INTO chapters (id, book_id, number) VALUES
(1, 1, 1),
(2, 1, 2),
-- ... more chapters

-- verses
INSERT INTO verses (chapter_id, number, text, red_letter) VALUES
(1, 1, 'In the beginning God created the heavens and the earth.', 0),
(1, 2, 'The earth was formless and void...', 0),
-- ... more verses
```

---

## 🧪 Testing Your Bible Data

### Validation Checklist

```ruby
# lib/tasks/validate_bible.rake
namespace :bible do
  task validate: :environment do
    errors = []
    
    # Check book count
    book_count = Book.count
    expected_books = 66
    errors << "Book count mismatch: #{book_count} vs #{expected_books}" if book_count != expected_books
    
    # Check verse count (approximate)
    verse_count = Verse.count
    expected_verses = 31102 # Standard Bible verse count
    if (verse_count - expected_verses).abs > 100
      errors << "Verse count suspicious: #{verse_count} vs ~#{expected_verses}"
    end
    
    # Check for missing chapters
    Book.find_each do |book|
      chapters = book.chapters.pluck(:number).sort
      expected = (1..chapters.max).to_a
      missing = expected - chapters
      errors << "#{book.name} missing chapters: #{missing}" if missing.any?
    end
    
    # Check for missing verses
    Chapter.find_each do |chapter|
      verses = chapter.verses.pluck(:number).sort
      expected = (1..verses.max).to_a
      missing = expected - verses
      errors << "#{chapter.reference} missing verses: #{missing}" if missing.any?
    end
    
    # Check for blank text
    blank_verses = Verse.where("text = '' OR text IS NULL").count
    errors << "#{blank_verses} verses with blank text" if blank_verses > 0
    
    # Report results
    if errors.any?
      puts "Validation FAILED:"
      errors.each { |e| puts "  - #{e}" }
      exit 1
    else
      puts "Validation PASSED ✓"
      puts "  #{book_count} books"
      puts "  #{Chapter.count} chapters"
      puts "  #{verse_count} verses"
    end
  end
end
```

---

## 📚 Resources

### Official Sources
- **Lockman Foundation:** https://www.lockman.org/
- **American Bible Society:** https://www.americanbible.org/

### Technical Resources
- **API.Bible Documentation:** https://scripture.api.bible/livedocs
- **OSIS Format:** http://www.bibletechnologies.net/
- **Zefania XML:** http://www.zefania.de/index.php/en/

### Community Projects
- **Bible Databases (GitHub):** https://github.com/scrollmapper/bible_databases
- **eBible.org:** https://ebible.org/
- **SWORD Project:** https://crosswire.org/sword/

### Legal Resources
- **Copyright Law:** https://www.copyright.gov/
- **Fair Use Guidelines:** https://www.copyright.gov/fair-use/

---

## ⚡ Quick Reference

### Decision Matrix

| Requirement | WEB (100% Free) | API.Bible (Free Tier) | Amplified (Licensed) |
|-------------|-------------------------|-----------------------|----------------------|
| Cost | **FREE** | FREE-$99/mo | $500-$5,000/year |
| Legal | ✅ 100% Safe | ✅ Safe | ✅ Safe (with license) |
| Modern English | ✅ Yes | Varies | ✅ Yes |
| Red Letter | ✅ Available | ❌ Limited | ✅ Yes |
| Offline Use | ✅ Yes | ❌ API Only | ✅ Yes |
| Full Control | ✅ Yes | ⚠️ Limited | ✅ Yes |
| Maintenance | Low | Medium | Low |

### Recommended Path

```
START HERE → Use WEB (100% free, modern English)
    ↓
Build core features
    ↓
Test with users
    ↓
Apply for Amplified Bible license
    ↓
Integrate licensed Bible
    ↓
LAUNCH with Amplified Bible
```

---

## 🎯 Action Items

### Immediate (Today)
- [ ] Download WEB Bible text (100% free from ebible.org)
- [ ] Create import script
- [ ] Test data integrity
- [ ] Build basic static reading pages

### Short-term (This Week)
- [ ] Draft email to Lockman Foundation requesting evaluation/sample license for Amplified Bible
- [ ] Prepare project description for legal team review
- [ ] Create budget projection
- [ ] Set up demo environment with WEB

### Medium-term (This Month)
- [ ] Submit license application for Amplified Bible (or evaluation license request)
- [ ] Continue building with WEB (100% legal, no concerns)
- [ ] Focus on static reading interface first
- [ ] Test with users
- [ ] Request Amplified Bible sample chapters for evaluation if full license not yet approved

### Long-term (Next 3 Months)
- [ ] Finalize license agreement
- [ ] Receive Amplified Bible data
- [ ] Migrate to Amplified Bible
- [ ] Launch publicly

---

## 📞 Contact Information

### Licensing Inquiries
**Lockman Foundation**
- Email: permissions@lockman.org
- Phone: (714) 879-3055
- Address: 2100 W. Orangethorpe Ave., Suite 100, La Habra, CA 90631

### Technical Support
For questions about this guide or implementation:
- Open an issue in the GitHub repository
- Refer to PLAN.md for overall project planning
- Refer to TECHNICAL_SPEC.md for implementation details

---

**Document Version:** 1.0  
**Last Updated:** November 2025  
**Author:** Amped Development Team
