# Content Idea Generator - Automated Content Strategy Workflow

> An intelligent n8n workflow that automatically generates, categorizes, and organizes content ideas using AI-powered analysis, keyword research, and trend monitoring.

<!-- First image: centered -->
<p align="center">
  <img width="600" height="400" alt="Screenshot 1" src="https://github.com/user-attachments/assets/db4d717b-77e3-43eb-a0f0-16161ac7e119" />
</p>

<!-- Next two images side by side: centered -->
<p align="center">
  <img width="290" height="200" alt="Screenshot 2" src="https://github.com/user-attachments/assets/97dfd845-b45c-4f21-8372-b32eadffb9b1" />
  <img width="290" height="200" alt="Screenshot 3" src="https://github.com/user-attachments/assets/a9b03c5a-0ab0-4c1c-976e-de58bdab8a92" />
</p>


## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Workflow Components](#workflow-components)
- [Data Flow](#data-flow)
- [Code Nodes Documentation](#code-nodes-documentation)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

The Content Idea Generator is an automated workflow built on n8n that helps content creators, marketers, and SEO professionals generate high-quality content ideas based on keyword research, competitor analysis, and trending topics. The system uses Google's Gemini AI to intelligently generate and categorize content with comprehensive SEO insights.

### What It Does

1. Accepts niche and target audience input via form submission, webhook, or manual trigger
2. Fetches trending topics from Google Trends via SerpAPI
3. Analyzes Reddit discussions for engagement insights
4. Uses Google Gemini 2.0 Flash with AI Agent to generate detailed content ideas
5. Performs keyword research using integrated search tools
6. Parses and structures AI-generated ideas with quality scoring
7. Uses Gemini API for intelligent content type categorization
8. Validates and formats data for Airtable storage
9. Saves keywords and content ideas to separate Airtable tables
10. Sends a beautifully formatted HTML email summary to stakeholders

## ✨ Features

- **Multiple Trigger Options**: Form submission, webhook, scheduled runs, or manual execution
- **Flexible Niche Configuration**: Easy-to-configure niche and target audience parameters
- **Real-Time Trend Analysis**: Integrates Google Trends and Reddit for current topics
- **AI-Powered Content Generation**: Leverages Gemini 2.0 Flash for intelligent content creation
- **Autonomous Keyword Research**: AI Agent can perform independent keyword research when needed
- **Intelligent Categorization**: Uses Gemini API to classify content types (Blog Post, Text, Video, Carousel)
- **Quality Scoring**: Automatically calculates quality scores based on completeness (0-100)
- **Dual Airtable Storage**: Separate tables for keywords database and content ideas
- **Email Notifications**: Professional HTML email summaries with all generated ideas
- **Error Handling**: Robust error handling with retry mechanisms and fallback values
- **Date Management**: Proper date formatting with publish target dates

## 🏗️ Architecture

```
┌─────────────────┐
│  Form/Webhook   │ (User Input)
│  - Niche        │
│  - Audience     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ Niche Config    │ (Parameters)
│  - Settings     │
│  - Constraints  │
└────────┬────────┘
         │
         ├──────────────────┬──────────────────┐
         ▼                  ▼                  ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│  SerpAPI        │  │  Reddit API     │  │  Google Trends  │
│  (Trends)       │  │  (Discussions)  │  │  (Volume)       │
└────────┬────────┘  └────────┬────────┘  └────────┬────────┘
         │                    │                    │
         └────────────────────┴────────────────────┘
                              │
                              ▼
                    ┌─────────────────┐
                    │  Merge Data     │
                    │  (Aggregate)    │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  AI Prompt      │ (Build Context)
                    │  - Keywords     │
                    │  - Trends       │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │  AI Agent       │ (Gemini 2.0)
                    │  - Generate     │
                    │  - Research     │
                    │  + Search Tools │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Post-Processing │ (Parse & Score)
                    │  - Extract      │
                    │  - Quality Score│
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Prep Data       │ (Format for API)
                    │  - Build Prompt │
                    │  - Structure    │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Gemini API      │ (Categorization)
                    │  - Content Type │
                    │  - Confidence   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │ Merge & Validate│ (Final Processing)
                    │  - Combine Data │
                    │  - Format Dates │
                    │  - Validate     │
                    └────────┬────────┘
                             │
                             ├─────────────────┬─────────────────┐
                             ▼                 ▼                 ▼
                    ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
                    │  Save Keywords  │  │  Save Ideas     │  │  Merge & Email  │
                    │  (Airtable)     │  │  (Airtable)     │  │  (Gmail)        │
                    └─────────────────┘  └─────────────────┘  └─────────────────┘
```

## 📦 Prerequisites

### Required Services

- **n8n** (v1.97.1 or higher)
  - Self-hosted or cloud instance
  - Node.js v20.18.1 or higher

- **Airtable Account**
  - Base with Keywords Database table
  - Base with Content Ideas table
  - Personal Access Token with read/write permissions

- **Google Gemini API**
  - API key for Gemini 2.0 Flash Exp model
  - Access to generativelanguage.googleapis.com

- **SerpAPI Account**
  - API key for Google Trends data
  - Access to serpapi.com/search

- **Reddit OAuth2**
  - OAuth2 credentials for Reddit API
  - App created at reddit.com/prefs/apps

- **Gmail Account** (for email notifications)
  - OAuth2 credentials or app-specific password
  - SMTP access enabled

### Airtable Schema

#### Keywords Database Table
```
- Keyword (Single line text) - Primary field
- Search Volume (Number)
- Difficulty (Number)
- CPC (Number)
- Trend Direction (Single select: Rising/Stable/Declining)
- Related Keywords (Long text)
- Last Updated (Date)
```

#### Content Ideas Table
```
- Idea Title (Single line text) - Primary field
- Niche/Category (Single select: Photography/Content Creation/etc.)
- Keyword (Single line text)
- Search Volume (Number)
- Keyword Difficulty (Number)
- Competition Level (Single select: Low/Medium/High)
- Content Type (Single select: Blog Post/Text/Video/Carousel)
- Outline (Long text)
- SEO Tips (Long text)
- Suggested Images (Long text)
- Trending Score (Number)
- Source URLs (Long text)
- Reddit Mentions (Number)
- Status (Single select: New/In Progress/Published/Archived)
- Assigned To (Single line text)
- Publish Target Date (Date)
```

## 🚀 Installation

### Step 1: Clone or Import Workflow

1. Open your n8n instance
2. Click "Add Workflow" → "Import from File" or "Import from URL"
3. Import the workflow JSON file provided
4. The workflow ID should be: `TAUIcNT99SjCHL3d`

### Step 2: Configure Credentials

#### Airtable OAuth2 Credentials
```
Name: Airtable Personal Access Token account
Type: OAuth2
Follow Airtable's OAuth2 flow or use Personal Access Token
```

#### Google Gemini API (PaLM)
```
Name: Google Gemini(PaLM) Api account
API Key: [Your Gemini API Key]
```

#### SerpAPI
```
Name: SerpAPI Account
API Key: [Your SerpAPI Key]
Add to HTTP Request nodes for Google Trends
```

#### Reddit OAuth2
```
Name: Reddit account
Type: OAuth2
Client ID: [From Reddit App]
Client Secret: [From Reddit App]
Redirect URL: [Your n8n OAuth redirect URL]
```

#### Gmail OAuth2
```
Name: Gmail account
Type: OAuth2
Follow Google OAuth2 flow for Gmail API access
```

### Step 3: Update Configuration

Replace these placeholders in the workflow:

1. **SerpAPI Key**: In "Serp API Search" and "Search Volume" nodes
   - Replace `"your api key"` with your actual SerpAPI key

2. **Gemini API Key**: In "HTTP Request" node URL
   - Replace `your api key` with your key

3. **Airtable Base ID**: In all Airtable nodes
   - Current: `app666rrIm8om9ecs`
   - Replace with your base ID

4. **Airtable Table IDs**: 
   - Keywords Database: `tblqcIDR4Yl3GMzsR`
   - Content Ideas: `tblwhM74z8Qxwyw0h`

5. **Email Recipient**: In "Send Content Mail" node`
   - Replace with your email

6. **Niche Presets**: In "Video Editing" and "Content Creation" nodes
   - Update with your preferred niches

## ⚙️ Configuration

### Workflow Triggers

The workflow supports four trigger methods:

1. **Form Trigger** (Primary)
   - Web form for user input
   - Fields: Niche, Target Audience
   - Webhook ID: `777bc09e-c65c-4a62-85ba-34d066ac7b1f`

2. **Webhook Trigger**
   - Direct API integration
   - Path: `0c49b066-13f5-4411-9750-3291c91a3019`
   - POST JSON: `{"Niche": "...", "Target Audience": "..."}`

3. **Schedule Trigger** (Currently disabled)
   - Weekly execution (every 7 days at 9 AM)
   - Enable in node settings for automation

4. **Manual Trigger**
   - Click "Execute workflow" button
   - Uses preset niche configurations

### Niche & Parameters Configuration

Edit the "Niche & Parameters" node to customize:

```javascript
const config = {
  niche: $input.first().json.Niche,
  targetAudience: $input.first().json['Target Audience'],
  numberOfIdeas: 10, // Number of ideas to generate
  competitorUrls: [
    "https://example.com/blog",
    // Add your competitor URLs
  ],
  excludeKeywords: ["free", "cheap"], // Keywords to avoid
  minSearchVolume: 500, // Minimum search volume
  maxKeywordDifficulty: 60, // Maximum difficulty (0-100)
  contentTypes: ["blog post", "video script", "carousel"]
};
```

### AI Agent Configuration

The AI Agent uses Google Gemini 2.0 Flash with:

- **Model**: `models/gemini-2.0-flash`
- **Memory**: Buffer window with session ID `cnt101`
- **Tools**: Google Search, Reddit Keyword Search
- **Context**: Keywords, trending topics, competitor content

### Gemini API Settings (Content Categorization)

```json
{
  "model": "gemini-2.0-flash-exp",
  "temperature": 0.2,
  "topK": 1,
  "topP": 1,
  "maxOutputTokens": 2048,
  "responseMimeType": "application/json"
}
```

## 🔧 Workflow Components

### 1. Form Trigger / Webhook / Manual Trigger
**Purpose**: Accept user input for niche and target audience

**Form Configuration**:
- Title: "Content Idea Generator + SEO Optimizer"
- Fields: Niche (required), Target Audience (required)
- Completion message: Custom wait message

**Webhook Path**: `/webhook/0c49b066-13f5-4411-9750-3291c91a3019`

### 2. Niche & Parameters (Code Node)
**Purpose**: Configure workflow parameters and constraints

**Key Configurations**:
- Number of ideas to generate
- Competitor URLs for analysis
- Excluded keywords
- Search volume and difficulty thresholds
- Content type preferences

**Output**: Configuration object for downstream nodes

### 3. SerpAPI Search (HTTP Request)
**Purpose**: Fetch Google Trends data for related queries

**Configuration**:
- Engine: google_trends
- Data Type: RELATED_QUERIES
- Error handling: Continue on error
- Retry: Enabled with 1000ms wait

### 4. Search Volume (HTTP Request)
**Purpose**: Get search volume data from Google Trends

**Configuration**:
- Engine: google_trends
- Number of results: 20
- Retry enabled with 1600ms delay

### 5. Reddit API Request (HTTP Request)
**Purpose**: Fetch trending discussions and topics

**Configuration**:
- Endpoint: oauth.reddit.com/search
- Sort: top
- Time period: week
- Limit: 50 posts
- User-Agent: n8n-workflow-bot/1.0

### 6. AI Prompt (Code Node)
**Purpose**: Aggregate all data and prepare context for AI

**Key Operations**:
- Processes SerpAPI results for keywords
- Extracts Reddit topics with engagement scores
- Calculates trending scores
- Identifies content gaps
- Flags if keyword research needed

**Output**: Comprehensive context object with keywords, trends, and config

### 7. AI Agent (Gemini 2.0 Flash)
**Purpose**: Generate detailed content ideas with AI

**Features**:
- Autonomous keyword research capability
- Uses Google Search and Reddit tools
- Generates comprehensive content outlines
- Includes SEO optimization tips
- Creates engagement hooks
- Suggests visual content

**Expected Output Format**:
```
**IDEA #1: Title Here**
**Target Keyword:** keyword
**Secondary Keywords:** keyword1, keyword2
**Search Volume:** 1000
**Keyword Difficulty:** 50
**Trending Score:** 75
**Competition Level:** Medium
**Content Type:** Blog Post
**Estimated Word Count:** 2500
**Target Audience Segment:** Specific segment

**Why This Will Work:**
[Explanation of opportunity and strategy]

**Detailed Outline:**
1. Introduction - [Details]
2. Section Title - [Details]
...

**SEO Optimization Tips:**
- H1 tag guidelines
- Keyword placement
- Internal linking
- Meta description

**Visual Content Suggestions:**
- Custom graphics
- Charts and data
- Screenshots

**Content Gap Exploited:**
[Unique angle explanation]

**Engagement Hooks:**
- Opening hook
- Social media teaser
- Email subject line

**Related Content Ideas:**
1. Follow-up topic
2. Complementary topic
3. Advanced/beginner version

---
```

### 8. Post-Processing & Scoring (Code Node)
**Purpose**: Parse AI output and structure data with quality scoring

**Key Functions**:

```javascript
// Split ideas
const ideaBlocks = agentOutput.split(/\*\*IDEA #/i);

// Extract fields with regex
const titleMatch = block.match(/^(\d+):\s*(.+?)(?:\n|\*\*)/);
const keywordMatch = block.match(/\*\*Target Keyword:\*\*\s*(.+?)(?:\n|$)/i);

// Calculate quality score (0-100)
let qualityScore = 0;
if (idea.keyword) qualityScore += 15;
if (idea.searchVolume > 0) qualityScore += 15;
if (idea.outline) qualityScore += 20;
// ... more scoring logic
```

**Output**: Array of structured idea objects with quality scores

### 9. Prep Data (Code Node)
**Purpose**: Format data for Gemini API categorization

**Operations**:
- Maps ideas to simplified format
- Builds categorization prompt
- Truncates long fields (400 chars for outline)
- Returns JSON object (not string)

**Prompt Structure**:
```
You are a content strategist. Categorize each content idea...

AVAILABLE FORMATS:
- Blog Post: 1500+ words, comprehensive guides
- Text: <1500 words, quick tips, FAQs
- Video: Visual demonstrations, tutorials
- Carousel: 5-10 slides, step-by-step visuals

CONTENT IDEAS TO CATEGORIZE:
[JSON array of ideas]

Respond with ONLY a JSON array:
[{
  "id": 1,
  "contentType": "Blog Post",
  "confidence": 95,
  "reasoning": "..."
}]
```

### 10. HTTP Request (Gemini API)
**Purpose**: Call Gemini API for content type categorization

**Request**:
```javascript
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash-exp:generateContent?key=API_KEY

Headers:
- Content-Type: application/json

Body:
{
  "contents": [...],
  "generationConfig": {
    "temperature": 0.2,
    "topK": 1,
    "topP": 1,
    "maxOutputTokens": 2048,
    "responseMimeType": "application/json"
  }
}
```

**Response Format**:
```json
[
  {
    "id": 1,
    "contentType": "Blog Post",
    "confidence": 95,
    "reasoning": "High word count and comprehensive structure"
  }
]
```

### 11. Merge & Validate (Code Node)
**Purpose**: Combine all data and validate for Airtable storage

**Key Helper Functions**:

```javascript
// Normalize content types
function normalizeContentType(type, wordCount) {
  if (!type) return wordCount >= 1500 ? 'Blog Post' : 'Text';
  const normalized = TYPE_NORMALIZER[type.toLowerCase()] || type;
  return VALID_TYPES.includes(normalized) ? normalized : 'Blog Post';
}

// Format dates for Airtable (YYYY-MM-DD)
function formatDateForAirtable(date) {
  const d = new Date(date);
  if (isNaN(d.getTime())) return null;
  return d.toISOString().split('T')[0];
}

// Truncate long fields
function truncateField(text, maxLength = 99000) {
  if (!text || text.length <= maxLength) return text;
  return text.substring(0, maxLength - 15) + '...[truncated]';
}

// Safe integer parsing
function safeInt(value, defaultValue = 0) {
  const parsed = parseInt(value);
  return isNaN(parsed) ? defaultValue : parsed;
}
```

**Validation Rules**:
- Content types: Blog Post, Text, Video, or Carousel
- Competition levels: Low, Medium, or High
- All numeric fields validated and parsed
- Dates formatted as YYYY-MM-DD
- Long fields truncated to prevent errors
- Publish target date set to 7 days from creation

**Metadata Added**:
- `aiCategorized`: Boolean flag
- `aiModel`: "gemini-2.0-flash-exp"
- `contentTypeConfidence`: 0-100
- `contentTypeReasoning`: Explanation
- `airtableReady`: true
- `processingVersion`: "2.0-gemini"

### 12. Save Keywords To DB (Airtable)
**Purpose**: Upsert keywords to Keywords Database table

**Configuration**:
- Operation: Upsert
- Matching: By ID
- Fields: Keyword, Search Volume, Difficulty, CPC

**Note**: Uses upsert to avoid duplicates

### 13. Create Content Idea (Airtable)
**Purpose**: Save content ideas to Content Ideas table

**Configuration**:
- Operation: Create
- Maps all validated fields
- Status: New (default)
- Includes publish target date

**Critical Fields Mapped**:
- Idea Title, Keyword, Search Volume, Difficulty
- Content Type, Competition Level
- Outline, SEO Tips, Suggested Images
- Trending Score, Publish Target Date

### 14. Merge (Node)
**Purpose**: Combine outputs from keywords and ideas saves

**Configuration**:
- Waits for both Airtable operations
- Passes combined data to email node

### 15. Merge Content To Email (Code Node)
**Purpose**: Build HTML email with all content ideas

**Features**:
- Keyword overview table
- Detailed breakdown per idea
- Color-coded styling
- Responsive design
- Next scheduled run date

**Template Sections**:
- Subject line with date
- Keyword overview table
- Detailed content breakdown with:
  - Outline
  - SEO tips
  - Suggested images
- Footer with next run info

### 16. Send Content Mail (Gmail)
**Purpose**: Send formatted email to stakeholders

**Configuration**:
- OAuth2 authentication
- HTML content type
- Uses dynamically generated subject and body
- Recipient configurable in node

## 📊 Data Flow

### Input Data Structure (Form/Webhook)
```json
{
  "Niche": "video editing",
  "Target Audience": "Video editors and social media content creators"
}
```

### After Niche & Parameters
```json
{
  "config": {
    "niche": "video editing",
    "targetAudience": "Video editors and social media content creators",
    "numberOfIdeas": 10,
    "competitorUrls": ["..."],
    "excludeKeywords": ["free", "cheap"],
    "minSearchVolume": 500,
    "maxKeywordDifficulty": 60
  },
  "runDate": "2025-11-12T10:30:00.000Z",
  "runId": "run_1731408600000"
}
```

### After AI Prompt Aggregation
```json
{
  "keywords": [
    {
      "keyword": "video editing tutorial",
      "url": "...",
      "snippet": "..."
    }
  ],
  "keywordCount": 45,
  "trendingTopics": [
    {
      "topic": "Best video editing software 2025",
      "engagement": 1250,
      "subreddit": "VideoEditing"
    }
  ],
  "competitorContent": [...],
  "config": {...},
  "needsKeywordResearch": false
}
```

### After Post-Processing & Scoring
```json
{
  "ideaNumber": 1,
  "title": "Complete Guide to Video Editing for Beginners",
  "keyword": "video editing tutorial",
  "searchVolume": 8500,
  "difficulty": 45,
  "trendingScore": 82,
  "qualityScore": 95,
  "competition": "Medium",
  "wordCount": 2800,
  "outline": "1. Introduction...",
  "seoTips": "- H1 tag should be...",
  "visualSuggestions": "- Screenshot of...",
  "createdAt": "2025-11-12T00:00:00.000Z"
}
```

### After Merge & Validate (Airtable Ready)
```json
{
  "Idea Title": "Complete Guide to Video Editing for Beginners",
  "Keyword": "video editing tutorial",
  "Search Volume": 8500,
  "Keyword Difficulty": 45,
  "Trending Score": 82,
  "Competition Level": "Medium",
  "Content Type": "Blog Post",
  "Outline": "1. Introduction...",
  "SEO Tips": "- H1 tag should be...",
  "Suggested Images": "- Screenshot of...",
  "Status": "New",
  "Publish Target Date": "2025-11-19",
  "aiCategorized": true,
  "aiModel": "gemini-2.0-flash-exp",
  "contentTypeConfidence": 95,
  "processingVersion": "2.0-gemini"
}
```

## 💻 Code Nodes Documentation

### Post-Processing & Scoring Code

**Location**: "Post-Processing & Scoring" node

**Purpose**: Parse AI-generated content into structured data with quality scoring

**Key Operations**:

```javascript
// 1. Validate agent output
if (!agentOutput || agentOutput.trim() === '') {
  throw new Error('Agent returned empty output');
}

// 2. Split into individual ideas
const ideaBlocks = agentOutput.split(/\*\*IDEA #/i);

// 3. Parse each field using regex
const titleMatch = block.match(/^(\d+):\s*(.+?)(?:\n|\*\*)/);
const keywordMatch = block.match(/\*\*Target Keyword:\*\*\s*(.+?)(?:\n|$)/i);
// ... more field extractions

// 4. Calculate quality score
let qualityScore = 0;
if (idea.keyword) qualityScore += 15;
if (idea.searchVolume > 0) qualityScore += 15;
if (idea.outline) qualityScore += 20;
if (idea.seoTips) qualityScore += 15;
if (idea.visualSuggestions) qualityScore += 10;
if (idea.contentGap) qualityScore += 10;
if (idea.engagementHooks) qualityScore += 10;
if (idea.trendingScore > 50) qualityScore += 5;

// 5. Sort by combined score
ideas.sort((a, b) => {
  const scoreA = (a.trendingScore || 0) + (a.qualityScore || 0);
  const scoreB = (b.trendingScore || 0) + (b.qualityScore || 0);
  return scoreB - scoreA;
});
```

**Error Handling**:
- Validates non-empty output
- Try-catch per idea to prevent total failure
- Returns partial data on parsing errors
- Logs detailed error messages

**Output**: Array of structured idea objects sorted by quality

### Prep Data Code

**Location**: "Prep Data" node

**Purpose**: Format data for Gemini API categorization request

**Key Operations**:

```javascript
// 1. Map to simplified structure
const contentIdeas = items.map(item => ({
  id: item.json.ideaNumber,
  title: item.json.title,
  wordCount: item.json.wordCount || 0,
  keyword: item.json.keyword || '',
  outline: item.json.outline ? item.json.outline.substring(0, 400) : ''
}));

// 2. Build categorization prompt
const prompt = `You are a content strategist. Categorize each...
AVAILABLE FORMATS:
- Blog Post: 1500+ words...
- Text: <1500 words...
...`;

// 3. Return as JSON object (NOT stringified)
return [{
  json: {
    contents: [{
      parts: [{ text: prompt }]
    }],
    generationConfig: {
      temperature: 0.2,
      topK: 1,
      topP: 1,
      maxOutputTokens: 2048,
      responseMimeType: "application/json"
    }
  }
}];
```

**Important Notes**:
- Returns JSON object, not stringified JSON
- Truncates outline to 400 chars to save tokens
- Includes clear categorization rules in prompt
- Specifies exact response format

### Merge & Validate Code

**Location**: "Merge & Validate" node

**Purpose**: Final data processing and validation for Airtable

**Key Helper Functions**:

```javascript
// Type normalization
const TYPE_NORMALIZER = {
  'blogpost': 'Blog Post',
  'blog post': 'Blog Post',
  'blog': 'Blog Post',
  'text': 'Text',
  'article': 'Text',
  'video': 'Video',
  'carousel': 'Carousel',
  'slides': 'Carousel'
};

function normalizeContentType(type, wordCount) {
  if (!type) return wordCount >= 1500 ? 'Blog Post' : 'Text';
  const normalized = TYPE_NORMALIZER[type.toLowerCase()] || type;
  return VALID_TYPES.includes(normalized) ? normalized : 'Blog Post';
}

// Date formatting for Airtable
function formatDateForAirtable(date) {
  if (!date) return null;
  try {
    const d = new Date(date);
    if (isNaN(d.getTime())) return null;
    return d.toISOString().split('T')[0]; // YYYY-MM-DD
  } catch (error) {
    return null;
  }
}

// Field truncation
function truncateField(text, maxLength = 99000) {
  if (!text || text.length <= maxLength) return text;
  return text.substring(0, maxLength - 15) + '...[truncated]';
}

// Safe parsing
function safeInt(value, defaultValue = 0) {
  const parsed = parseInt(value);
  return isNaN(parsed) ? defaultValue : parsed;
}
```

**Processing Steps**:

1. Parse Gemini categorization response
2. Create lookup map for O(1) access
3. Merge AI categorization with original data
4. Normalize content types and competition levels
5. Validate and parse numeric fields
6. Truncate long text fields
7. Format dates to YYYY-MM-DD
8. Set publish target date (7 days ahead if not specified)
9. Add metadata fields
10. Return Airtable-ready objects

**Validation Rules**:
- Content types must be valid enum values
- Competition levels must be Low/Medium/High
- All numbers parsed and validated
- Dates properly formatted
- Long fields truncated
- Required fields have fallbacks

## 🐛 Troubleshooting

### Common Issues and Solutions

#### 1. "No ideas were successfully parsed"

**Cause**: AI Agent output doesn't match expected format

**Solution**:
1. Check AI Agent output in execution logs
2. Verify format starts with `**IDEA #1: Title**`
3. Ensure all required fields are present
4. Check for extra markdown formatting

**Debug Steps**:
```javascript
// Add to Post-Processing node
console.log('Agent output length:', agentOutput.length);
console.log('First 500 chars:', agentOutput.substring(0, 500));
console.log('Idea blocks found:', ideaBlocks.length);
```

#### 2. "Gemini response parsing failed"

**Cause**: Gemini returned markdown code blocks or invalid JSON

**Solution**:
```javascript
// Already handled in Merge & Validate node
const cleanedText = responseText.replace(/```json\n?|\n?```/g, '').trim();
aiResults = JSON.parse(cleanedText);
```

**Prevention**:
- Ensure `responseMimeType: "application/json"` is set
- Prompt clearly states "no markdown, no code blocks"

#### 3. "Field cannot accept the provided value" (Airtable)

**Cause**: Data type mismatch or invalid enum value

**Solutions**:
- **Dates**: Already formatted as YYYY-MM-DD in Merge & Validate
- **Content Type**: Normalized to valid enum values
- **Competition**: Validated against allowed values
- **Numbers**: Parsed with safeInt()

**Verification**:
```javascript
// Check in Merge & Validate output
console.log('Content Type:', idea.contentType);
console.log('Competition:', idea.competition);
console.log('Publish Date:', idea.publishTargetDate);
console.log('Is Airtable Ready:', idea.airtableReady);
```

#### 4. "No data from SerpAPI or Reddit"

**Cause**: API credentials invalid or rate limits exceeded

**Solutions**:
- **SerpAPI**: Verify API key in HTTP Request nodes
- **Reddit**: Check OAuth2 credentials and token expiry
- **Rate Limits**: Nodes have retry enabled with delays
- **Error Handling**: Set to "Continue on Error Output"

**Testing**:
1. Test API calls individually
2. Check API dashboard for usage and errors
3. Verify endpoint URLs are correct
4. Ensure authentication headers are present

#### 5. "AI Agent not performing keyword research"

**Cause**: Agent thinks sufficient keywords exist in context

**Solution**:
The AI Agent will automatically perform keyword research when:
- `needsKeywordResearch: true` in AI Prompt output
- `keywordCount` is 0 or less than 10

**Force Keyword Research**:
Modify AI Prompt node to set:
```javascript
needsKeywordResearch: true
```

**Verify Tools**:
- Ensure "Google Search" tool is connected to AI Agent
- Check "Reddit Keyword Search" tool is enabled
- Verify tool credentials are valid

#### 6. "Email not sending"

**Cause**: Gmail authentication or template errors

**Solutions**:
- **Authentication**: Verify Gmail OAuth2 credentials
- **Permissions**: Ensure Gmail API access granted
- **Template**: Check HTML syntax in Merge Content To Email
- **Node Reference**: Verify `$('Create Content Idea')` matches exact node name

**Testing**:
```javascript
// Test with simple email first
return [{
  json: {
    subject: "Test Email",
    html: "<h1>Test</h1><p>If you see this, email works!</p>"
  }
}];
```

#### 7. "Webhook/Form not triggering workflow"

**Cause**: Webhook URL incorrect or credentials issue

**Solutions**:
- **Form**: Access form at `https://your-n8n-url/form/777bc09e-c65c-4a62-85ba-34d066ac7b1f`
- **Webhook**: POST to `https://your-n8n-url/webhook/0c49b066-13f5-4411-9750-3291c91a3019`
- **Test Mode**: Ensure workflow is activated (not in test mode)
- **CORS**: Check n8n CORS settings if calling from external site

**Webhook Test**:
```bash
curl -X POST https://your-n8n-url/webhook/0c49b066-13f5-4411-9750-3291c91a3019 \
  -H "Content-Type: application/json" \
  -d '{"Niche":"test","Target Audience":"testers"}'
```

#### 8. "Quality scores all zero or incorrect"

**Cause**: Field extraction failing in Post-Processing

**Solution**:
Check that these fields are being extracted:
- keyword (15 points)
- searchVolume > 0 (15 points)
- outline (20 points)
- seoTips (15 points)
- visualSuggestions (10 points)
- contentGap (10 points)
- engagementHooks (10 points)
- trendingScore > 50 (5 points)

**Debug**:
```javascript
// Add after quality score calculation
console.log('Quality Score Breakdown:');
console.log('- Has keyword:', idea.keyword ? '✓' : '✗');
console.log('- Has volume:', idea.searchVolume > 0 ? '✓' : '✗');
console.log('- Has outline:', idea.outline ? '✓' : '✗');
console.log('Total Score:', qualityScore);
```

### Debug Mode

Enable detailed logging throughout the workflow:

```javascript
// Add to any code node
console.log('=== NODE DEBUG START ===');
console.log('Input items:', $input.all().length);
console.log('First item:', JSON.stringify($input.first().json, null, 2));
console.log('Processing...');
// ... your code ...
console.log('Output items:', output.length);
console.log('=== NODE DEBUG END ===');
```

**View Logs**:
1. Click on executed node
2. Select "View Details" tab
3. Check "Input" and "Output" sections
4. Review console logs in browser developer tools (if using n8n UI)

## 🎯 Best Practices

### Performance Optimization

1. **Batch Processing**
   - Current limit: 10 ideas per run (configurable in Niche & Parameters)
   - For large volumes: Split into multiple workflow runs
   - Use Schedule Trigger for regular automated runs

2. **API Rate Limits**
   - SerpAPI: Retry enabled with 1000-1600ms delays
   - Reddit: 50 posts limit per request
   - Gemini: 2048 token limit for categorization
   - Monitor API quotas in respective dashboards

3. **Error Recovery**
   - All API nodes set to "Continue on Error"
   - Retry mechanisms enabled on critical nodes
   - Fallback values in Merge & Validate
   - Partial data saved even if some steps fail

4. **Memory Management**
   - AI Agent uses buffer window memory (session: cnt101)
   - Clears after each workflow execution
   - Long fields truncated before storage

### Data Quality

1. **Validation Layers**
   - Post-Processing: Extracts and validates AI output
   - Prep Data: Sanitizes before API call
   - Merge & Validate: Final validation before storage
   - Each layer adds quality checks

2. **Quality Scoring**
   - Comprehensive 0-100 scale
   - Based on completeness of fields
   - Combined with trending score for prioritization
   - Helps identify best opportunities

3. **Date Management**
   - All dates formatted as YYYY-MM-DD for Airtable
   - Publish target auto-set to 7 days ahead
   - Timezone handling with UTC noon
   - Validation prevents invalid dates

4. **Field Truncation**
   - Outlines: 400 chars for API calls
   - Long text: 99,000 chars for Airtable
   - Prevents API token limits
   - Adds "[truncated]" indicator

### Content Strategy

1. **Keyword Selection**
   - Prioritize long-tail keywords (3-5 words)
   - Focus on question-based keywords (high intent)
   - Balance volume with difficulty
   - Avoid overly competitive terms

2. **Content Type Distribution**
   - AI automatically categorizes based on:
     - Word count (Blog Post: 1500+, Text: <1500)
     - Visual requirements (Video, Carousel)
     - Content structure and format
   - Diversify content types for audience variety

3. **Trend Monitoring**
   - Reddit engagement scores guide trending topics
   - Google Trends data shows search interest
   - Competitor gap analysis identifies opportunities
   - Weekly runs keep content fresh

4. **SEO Optimization**
   - Each idea includes detailed SEO tips
   - Keyword placement guidelines
   - Internal linking suggestions
   - Meta description recommendations

### Workflow Maintenance

1. **Regular Updates**
   - Review AI prompts monthly for quality
   - Update regex patterns if format changes
   - Refresh competitor URLs quarterly
   - Monitor API deprecations

2. **Monitoring**
   - Check execution logs weekly
   - Track success/failure rates
   - Monitor Airtable storage usage
   - Review email delivery rates

3. **Credential Management**
   - Rotate API keys quarterly
   - Update OAuth tokens before expiry
   - Document credential locations
   - Use environment variables when possible

4. **Version Control**
   - Export workflow JSON regularly
   - Document major changes in README
   - Tag versions in git
   - Keep change log updated

## 📈 Scaling Considerations

### Handling Larger Volumes

**Current Capacity**: 10 ideas per run

**To Scale to 50+ Ideas**:

1. **Increase numberOfIdeas in Niche & Parameters**
   ```javascript
   numberOfIdeas: 50 // Increase from 10
   ```

2. **Adjust AI Agent Token Limits**
   - May need to split into multiple agent calls
   - Consider using loop nodes for batching

3. **Optimize API Calls**
   ```javascript
   // Process in batches
   const BATCH_SIZE = 10;
   const batches = _.chunk(allIdeas, BATCH_SIZE);
   
   for (const batch of batches) {
     // Process batch
     await new Promise(resolve => setTimeout(resolve, 2000));
   }
   ```

4. **Use Parallel Processing**
   - Split workflow into parallel branches
   - Process different niches simultaneously
   - Aggregate results with final Merge node

### Multi-Niche Support

**Current**: Single niche per workflow execution

**Enhancement Options**:

1. **Multiple Preset Nodes**
   - Already has "Video Editing" and "Content Creation" presets
   - Add more preset nodes for different niches
   - Connect all to Merge2 node

2. **Dynamic Niche Iteration**
   ```javascript
   // In Niche & Parameters node
   const niches = [
     { niche: "Photography", audience: "Professional photographers" },
     { niche: "Video Editing", audience: "Content creators" },
     { niche: "Graphic Design", audience: "Designers" }
   ];
   
   // Return multiple items for downstream processing
   return niches.map(n => ({ json: { config: { ...config, ...n } } }));
   ```

3. **Separate Airtable Views**
   - Create views per niche in Airtable
   - Filter by Niche/Category field
   - Easier content organization

### Database Optimization

1. **Airtable Performance**
   - Index frequently queried fields
   - Use linked records for relationships
   - Archive old ideas (Status: Archived)
   - Regular cleanup of duplicates

2. **Keyword Database**
   - Upsert prevents duplicates
   - Track last updated dates
   - Monitor trend direction changes
   - Build historical data over time

3. **Content Ideas Table**
   - Use Status field for workflow management
   - Assign To field for team collaboration
   - Track Publish Target Date for scheduling
   - Link to Keywords Database for relationships

## 🔐 Security Best Practices

1. **API Key Management**
   - Store all keys in n8n credentials
   - Never hardcode keys in workflow
   - Use separate keys for dev/prod
   - Monitor API key usage dashboards

2. **Credential Rotation**
   - Rotate API keys quarterly
   - Update OAuth tokens before expiry
   - Document rotation schedule
   - Test after rotation

3. **Access Control**
   - Limit workflow edit permissions
   - Use separate Airtable bases for different teams
   - Restrict webhook access with authentication
   - Enable n8n basic auth for form trigger

4. **Data Privacy**
   - Don't log sensitive data in console
   - Sanitize outputs before email
   - Use Airtable field-level permissions
   - GDPR compliance for user data

5. **Webhook Security**
   - Use unique, unpredictable webhook IDs
   - Consider adding authentication headers
   - Rate limit webhook calls
   - Monitor for abuse

## 📝 Customization Guide

### Adding New Content Types

1. **Update Valid Types Constant**
   ```javascript
   // In Merge & Validate node
   const VALID_TYPES = ['Blog Post', 'Text', 'Video', 'Carousel', 'Podcast', 'Infographic'];
   ```

2. **Update Airtable Field Options**
   - Go to Content Ideas table
   - Edit Content Type field
   - Add new options

3. **Update Gemini Prompt**
   ```javascript
   // In Prep Data node
   const prompt = `...
   AVAILABLE FORMATS:
   - Podcast: 30-60 min audio, interviews, discussions
   - Infographic: Data visualization, statistics, comparisons
   ...`;
   ```

4. **Update Type Normalizer**
   ```javascript
   // In Merge & Validate node
   const TYPE_NORMALIZER = {
     // ... existing types
     'podcast': 'Podcast',
     'audio': 'Podcast',
     'infographic': 'Infographic',
     'graphic': 'Infographic'
   };
   ```

### Custom Quality Scoring Algorithm

Modify in Post-Processing & Scoring node:

```javascript
function calculateQualityScore(idea) {
  let score = 0;
  
  // Customize weights based on your priorities
  if (idea.keyword) score += 20; // Increased weight
  if (idea.searchVolume > 5000) score += 30; // Bonus for high volume
  if (idea.searchVolume > 1000) score += 15;
  if (idea.outline && idea.outline.length > 500) score += 25;
  if (idea.seoTips && idea.seoTips.length > 200) score += 15;
  if (idea.visualSuggestions) score += 10;
  if (idea.trendingScore > 80) score += 15; // High trending bonus
  if (idea.trendingScore > 50) score += 5;
  if (idea.difficulty < 30) score += 10; // Low competition bonus
  
  return Math.min(score, 100);
}
```

### Email Template Customization

Modify in Merge Content To Email node:

```javascript
// Add custom styling
const customStyles = `
<style>
  .priority-high { background-color: #fef3c7; border-left: 4px solid #f59e0b; }
  .priority-medium { background-color: #dbeafe; border-left: 4px solid #3b82f6; }
  .priority-low { background-color: #f3f4f6; border-left: 4px solid #9ca3af; }
</style>
`;

// Add custom sections per idea
const customSection = `
<div class="priority-${getPriority(f['Trending Score'])}">
  <h4>🎯 Priority Metrics</h4>
  <p><strong>Estimated Traffic:</strong> ${estimateTraffic(f['Search Volume'])}</p>
  <p><strong>Competition:</strong> ${f['Competition Level']}</p>
  <p><strong>Time to Rank:</strong> ${estimateRankingTime(f['Keyword Difficulty'])}</p>
</div>
`;

// Add charts or visualizations
const chartSection = `
<div>
  <h4>📊 Opportunity Score</h4>
  <div style="width: 100%; background: #e5e7eb; border-radius: 4px;">
    <div style="width: ${f['Trending Score']}%; background: #10b981; height: 20px; border-radius: 4px;"></div>
  </div>
</div>
`;
```

### Adding Custom API Integrations

Example: Add Ahrefs keyword data

1. **Add HTTP Request Node**
   ```javascript
   // New node: "Ahrefs Keyword Data"
   {
     method: "GET",
     url: "https://api.ahrefs.com/v3/keywords-overview",
     authentication: "headerAuth",
     sendQuery: true,
     queryParameters: {
       keywords: "={{$json.keyword}}",
       country: "us"
     }
   }
   ```

2. **Merge with Existing Data**
   - Add to main Merge node
   - Extract relevant metrics in AI Prompt node

3. **Update AI Prompt**
   ```javascript
   // Include Ahrefs data in context
   const ahrefsData = items.find(item => item.json.keywords_overview);
   // Add to prompt context
   ```

### Custom Niche Templates

Create preset configurations for different niches:

```javascript
// In new "Niche Templates" node
const NICHE_TEMPLATES = {
  photography: {
    niche: "Photography",
    targetAudience: "Professional photographers and enthusiasts",
    competitorUrls: [
      "https://www.format.com/magazine",
      "https://photographylife.com/articles"
    ],
    excludeKeywords: ["free", "cheap", "amateur"],
    minSearchVolume: 1000,
    maxKeywordDifficulty: 50,
    contentTypes: ["blog post", "video tutorial", "gear review"]
  },
  videoEditing: {
    niche: "Video Editing",
    targetAudience: "Video editors and content creators",
    competitorUrls: [
      "https://www.videomaker.com",
      "https://premiumbeat.com/blog"
    ],
    excludeKeywords: ["free", "pirated"],
    minSearchVolume: 500,
    maxKeywordDifficulty: 60,
    contentTypes: ["tutorial", "software comparison", "effects guide"]
  },
  // Add more templates...
};

// Use in workflow
const template = NICHE_TEMPLATES[$input.first().json.nicheType];
return [{ json: { config: template } }];
```

## 🤝 Contributing

We welcome contributions! Here's how to get involved:

### Ways to Contribute

1. **🐛 Bug Reports**
   - Use GitHub Issues
   - Include workflow execution logs
   - Provide steps to reproduce
   - Mention n8n version

2. **✨ Feature Requests**
   - Describe the use case
   - Explain expected behavior
   - Provide examples if possible

3. **📚 Documentation**
   - Fix typos or unclear instructions
   - Add examples and use cases
   - Improve troubleshooting guides
   - Translate to other languages

4. **💻 Code Contributions**
   - Fork the repository
   - Create a feature branch
   - Follow existing code style
   - Test thoroughly
   - Submit pull request

### Development Workflow

1. **Fork and Clone**
   ```bash
   git clone https://github.com/coderbenny/Content-Idea-Generator-SEO-Optimizer---n8n.git
   cd Content-Idea-Generator-SEO-Optimizer---n8n
   ```

2. **Create Feature Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make Changes**
   - Update workflow JSON
   - Modify code nodes
   - Update documentation
   - Add tests if applicable

4. **Test Thoroughly**
   - Test with multiple niches
   - Verify all nodes execute
   - Check Airtable storage
   - Validate email output
   - Test error scenarios

5. **Commit and Push**
   ```bash
   git add .
   git commit -m "feat: Add new feature description"
   git push origin feature/your-feature-name
   ```

6. **Submit Pull Request**
   - Describe changes in detail
   - Include screenshots/videos
   - Reference related issues
   - Request review

### Code Style Guidelines

1. **Code Nodes**
   - Use clear variable names
   - Add comments for complex logic
   - Handle errors gracefully
   - Log important steps

2. **Node Naming**
   - Use descriptive names
   - Follow existing patterns
   - Avoid special characters
   - Keep it concise

3. **Documentation**
   - Update README for major changes
   - Keep examples current
   - Document all configuration options
   - Include troubleshooting tips

## 📄 License

MIT License - Feel free to use and modify for your projects.

```
MIT License

Copyright (c) 2025 Content Idea Generator Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## 🙏 Acknowledgments

- **n8n Team** - For the amazing automation platform and continuous improvements
- **Google Gemini** - For powerful AI generation and categorization capabilities
- **Airtable** - For flexible data storage and excellent API
- **SerpAPI** - For reliable Google Trends and search data
- **Reddit** - For community engagement insights and trending topics
- **Community Contributors** - For feedback, bug reports, and feature suggestions

## 📞 Support and Resources

### Documentation
- **n8n Docs**: https://docs.n8n.io
- **Airtable API**: https://airtable.com/developers/web/api
- **Gemini API**: https://ai.google.dev/docs
- **SerpAPI**: https://serpapi.com/docs

### Community
- **GitHub Issues**: Report bugs and request features
- **GitHub Discussions**: Ask questions and share ideas
- **n8n Community**: https://community.n8n.io

### Contact
- **Email**: bhinnexclusive@gmail.com
- **Twitter**: @coderbenny
- **Website**: https://benny-eta.vercel.app

## 🔄 Changelog

### Version 2.0-gemini (2025-11-12)
- Added Gemini 2.0 Flash AI Agent for content generation
- Implemented autonomous keyword research capability
- Added intelligent content type categorization with Gemini API
- Improved date formatting for Airtable compatibility
- Enhanced error handling and retry mechanisms
- Added quality scoring system (0-100)
- Implemented dual Airtable storage (keywords + ideas)
- Added multiple trigger options (form, webhook, schedule, manual)
- Enhanced email template with detailed breakdowns
- Added comprehensive validation in Merge & Validate node
- Improved Reddit integration with OAuth2
- Added SerpAPI integration for Google Trends

### Version 1.0 (Previous)
- Initial release with basic content idea generation
- Single AI model for generation
- Basic Airtable integration
- Simple email notifications

## 🗺️ Roadmap

### Planned Features

**Q1 2025**
- [ ] Multi-language support for content ideas
- [ ] Integration with more keyword research tools
- [ ] Advanced competitor analysis features
- [ ] Content calendar integration

**Q2 2025**
- [ ] AI-powered content writing assistance
- [ ] Image generation for suggested visuals
- [ ] SEO score predictions
- [ ] A/B testing recommendations

**Q3 2025**
- [ ] Team collaboration features
- [ ] Performance analytics dashboard
- [ ] Automated content publishing
- [ ] Social media integration

**Future Considerations**
- WordPress integration for direct publishing
- Custom AI model fine-tuning
- Mobile app for content review
- Advanced reporting and insights

---

**Last Updated**: November 12, 2025  
**Version**: 2.0-gemini  
**Workflow ID**: TAUIcNT99SjCHL3d  
**Maintained by**: Benny Hinn

---

## 🚀 Quick Start Guide

New to this workflow? Follow these steps:

1. **Set up accounts**: Airtable, Gemini API, SerpAPI, Reddit, Gmail
2. **Import workflow**: Load the JSON into your n8n instance
3. **Configure credentials**: Add all API keys and OAuth tokens
4. **Update configuration**: Replace placeholder values (API keys, base IDs, email)
5. **Test with preset**: Use "Video Editing" or "Content Creation" preset
6. **Review output**: Check Airtable tables and email
7. **Customize**: Adjust parameters for your niche
8. **Schedule**: Enable Schedule Trigger for weekly automation

**Need help?** Check the [Troubleshooting](#troubleshooting) section or open an issue!
