# Managed and Scalable Industrial Digital Footprint Monitoring System

## 1. Problem and Goal

**Problem:**  
There is no unified, automated, and intelligent tool for collecting, filtering, and structuring information flows from key players across five target sectors of Russian industry. As a result, analysts waste time on manual data collection and cannot efficiently track or interpret emerging trends.  

**Goal:**  
Develop a software system that automatically collects, classifies, and systematizes publications from relevant sources, providing users with structured data within customizable timeframes for further analysis.

---

## 2. Project Tasks

1. Conduct research and build a registry of data sources.  
2. Develop a data collection module (parser/crawler).  
3. Develop a data processing and classification module using LLMs.  
4. Implement a data systematization and presentation module.  
5. Ensure scalability and reliability of the system.

---

## 3. Detailed Description

### 3.1 Phase 1: Source Ecosystem Analysis and Design

**Goal:**  
Not just to “find sources,” but to create a hierarchical, manageable database of sources with metadata.

#### 3.1.1 Research and Cataloging
- Identify and list corporate websites (~50–100 key companies across five industries such as Lukoil, Rosatom, Kamaz, Severstal, Gazprom, etc.).  
- Identify and list industry media outlets and aggregators (e.g., RIA Novosti, Kommersant, Vedomosti, MetalTorg.ru, RusAutoProm).  
- Explore official publication platforms (e.g., state procurement portals, disclosure portals for public companies).  
- Determine update frequency and typical content structures for each source type.

#### 3.1.2 Source Classification Schema
Each source is assigned attributes:
- **Type:** Corporate site / Industry media / Aggregator / Government platform  
- **Industry:** Energy, Engineering, etc. (multiple possible)  
- **Reliability/Priority:** High (official), Medium (major media), Low (regional media)  
- **Parsing structure:** Template for parser configuration  
- **Output:** Database or JSON registry with source metadata

---

### 3.2 Phase 2: Data Parser/Crawler Development

**Goal:**  
Build a flexible, configurable parser adaptable to common source structures defined in Phase 1.

#### 3.2.1 Functional Requirements
- Protocol support: HTTP/HTTPS, cookie and session handling  
- Respect robots.txt, configurable request delays  
- Configurability: Parsing templates via config files (XPath/CSS selectors for title, date, text, tags)  
- Dynamic content support: Integration with headless browsers (e.g., Puppeteer, Playwright)  
- Reliability: Retry mechanisms, error handling (404/403/500), logging  
- Scalability: Easily extendable without modifying core logic  

#### 3.2.2 Parser Output Format
```json
{
  "source_url": "https://example.com/news/123",
  "source_name": "Example-Energo",
  "crawled_at": "2023-10-25T12:00:00Z",
  "raw_data": {
    "title": "New CHP Plant Launched",
    "publication_date": "2023-10-25",
    "full_text": "...",
    "keywords": ["CHP", "energy"]
  }
}
```

---

### 3.3 Phase 3: LLM-Based Structuring and Classification Module

**Goal:**  
Transform unstructured text into structured, analyzable data.

#### 3.3.1 Taxonomy and Ontology
- **Category:** Company news, Press release, Report, Job post, Other  
- **Theme:** Investments, Technology, Personnel changes, Modernization, Ecology, Government support, Import substitution, Financial results  
- **Sentiment (optional):** Positive, Negative, Neutral  
- **Extracted Entities:**
  - Companies  
  - Persons (executives, officials)  
  - Locations  
  - Contract amounts, event dates  

#### 3.3.2 LLM Integration
- **Approach:** Use LLM APIs (e.g., GPT-4, YaGPT, domestic analogs) for zero/few-shot classification and NER.  
- **Prompt engineering:** Design prompts for consistent JSON output.

**Example prompt:**
> Classify the following news text by category, theme, and sentiment. Extract all mentioned companies. Return the result in JSON:  
> `{category: '', theme: '', sentiment: '', companies: []}`  
> Text: {insert_text_here}

- **Logic:** The parser accumulates raw data, sends it in batches to the LLM module, and receives enriched structured outputs.

#### 3.3.3 LLM Output Format
```json
{
  "...": "...",
  "llm_processed_at": "2023-10-25T12:05:00Z",
  "classification": {
    "category": "Company News",
    "theme": "Modernization",
    "sentiment": "Positive"
  },
  "entities": {
    "companies": ["Example-Energo", "Sibur"],
    "persons": ["Ivanov I.I."],
    "locations": ["Moscow Region"]
  }
}
```

---

### 3.4 Phase 4: Data Systematization and Presentation Module

**Goal:**  
Provide users with a robust interface and API for interacting with structured results.

#### 3.4.1 Storage
- Use a database (e.g., PostgreSQL) to store both raw and processed data.  
- Index fields by publication date, industry, theme, and company.

#### 3.4.2 API / Aggregation Interface
- Data retrieval by custom date ranges (`/api/news?from=2023-10-20&to=2023-10-25`).  
- Filters by industry, company, or theme.  
- Grouping support (e.g., “show all engineering news from the last week grouped by theme”).  
- Optional visualization: dashboards showing trends, topic distributions, tag clouds.

---

## 4. Acceptance Criteria

1. The system successfully collects data from at least **80%** of registered sources.  
2. The LLM module classifies and extracts entities with **≥85% accuracy** on the test sample.  
3. Users can retrieve structured news data for any of the five industries over any day within the past month via API.  
4. The system processes **≥95%** of sources without crashes (errors are logged but don’t halt execution).  
5. Adding a new source takes **≤15 minutes** (via config adjustment).

---

## 5. Non-Functional Requirements

- **Performance:** Parsing all registered sources takes ≤4 hours.  
- **Reliability:** System uptime ≥99%.  
- **Security:** Respects robots.txt and rate-limiting for each domain.  
- **Scalability:** Can increase the number of sources by 50% without rewriting core code.

---

## 6. Useful links

[Google Drive](https://drive.google.com/drive/folders/1eJBesfYyL8sr5AWAN03ozqeEggworfUF?usp=drive_link)
