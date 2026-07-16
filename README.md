# 📰 Personalized Digest System

A smart content curation system that uses AI to generate personalized article summaries based on member interests.

## 🌟 Features

1. **Member Onboarding with Interest Selection**
   - Register new members
   - Select interest tags during onboarding
   - Login to existing accounts

2. **Document Processing with AI (LLM Task #1)**
   - Upload articles, forum posts, or documents manually
   - **NEW: Fetch posts automatically from WordPress sites via REST API**
   - **NEW: Web scraping to extract clean content from post URLs**
   - Automatically extract relevant interest tags using Groq's LLM API
   - Analyze content and identify primary topics

3. **Personalized Digest Generation (LLM Task #2)**
   - Generate customized summaries for each member
   - Weight content based on matching interest tags
   - Store digests as drafts for review

4. **Interest Management**
   - Update interest preferences anytime
   - View digest statistics
   - Track published vs draft digests

5. **WordPress Integration**
   - Fetch posts from any WordPress site using REST API
   - Automatic web scraping of post content
   - Bulk processing of multiple posts
   - One-click integration with AI analysis

6. **🆕 Automated Digest Generation & Email Delivery**
   - Schedule automatic digest generation on a daily/weekly basis
   - Fetch WordPress posts automatically
   - Send branded HTML emails to members
   - Email queue with retry logic for large memberships
   - Comprehensive logging for monitoring and debugging

## 🚀 Setup Instructions

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```
### 3. Run the Application

```bash
streamlit run src/app.py
```

The application will open in your browser at `http://localhost:8501`

## 📖 How to Use

### Step 1: Member Onboarding
1. Go to **Member Onboarding** page
2. Register with your name and email
3. Select your interest tags (Technology, AI/ML, Health, Business, etc.)
4. Click Register

### Step 2: Upload Documents (Manual or WordPress)

#### Option A: Manual Upload
1. Go to **Upload Document** page
2. Enter document title and content
3. Select source type (Article/Forum/Document)
4. Click "Upload & Process"
5. The system will use **LLM Task #1** to extract interest tags
6. Generate personalized digests for all members

#### Option B: Fetch from WordPress
1. Go to **Fetch WordPress Posts** page
2. Enter WordPress site URL (e.g., https://domain1.badev.tools)
3. Set number of posts to fetch
4. Click "🔍 Fetch Posts"
5. The system will:
   - Fetch posts via WordPress REST API
   - Scrape clean content from each post URL
   - Display all posts with previews
6. Process individual posts or all at once
7. AI automatically extracts tags and generates digests

### Step 3: View Your Digests
1. Go to **View Digests** page
2. See all your personalized summaries
3. Digests are sorted by relevance to your interests
4. View matching interest tags
5. Publish or read full documents

### Step 4: Manage Interests
1. Go to **Manage Interests** page
2. Update your interest selections anytime
3. Future digests will reflect your new preferences

### Step 5: 🆕 Set Up Automated Digests (Optional)
1. Go to **🔧 Admin: Automation** page
2. Configure schedule settings:
   - Enable automated digests
   - Set daily run time (e.g., 9:00 AM)
   - Select days to run (weekdays, weekends, etc.)
   - Enter WordPress site URL
   - Set number of posts to fetch per run
   - Enable "Send Emails Immediately" if configured
3. Click **Save Configuration**
4. Test manually using **"🚀 Run Digest Generation Now"** button
5. Monitor logs and email queue status

## 🤖 LLM Integration

The system uses Groq's LLM API (llama-3.3-70b-versatile) for two main tasks:

### LLM Task #1: Interest Tag Extraction
- **Input**: Raw document content + available interest tags
- **Process**: LLM analyzes content and identifies relevant categories
- **Output**: JSON with extracted tags, primary topic, and confidence level

### LLM Task #2: Personalized Digest Generation
- **Input**: Document content + member interests + extracted tags
- **Process**: LLM creates customized summary weighted by matching interests
- **Output**: Personalized summary text emphasizing relevant aspects

## ⚙️ Automated Digest Generation

The system supports fully automated digest generation with scheduled email delivery.

### Features
- � **Scheduled Execution**: Configure daily/weekly schedules via cron or APScheduler
- 📡 **WordPress Integration**: Automatically fetch latest posts from WordPress sites
- 📧 **Email Delivery**: Send branded HTML emails to all members
- 🔄 **Retry Logic**: Email queue with automatic retry for failed deliveries
- 📝 **Comprehensive Logging**: Track all operations with timestamped logs

### Setup Options

#### Option 1: Run as Daemon (Development/Testing)
```bash
python run_digest_automation.py
```
Runs continuously using APScheduler. Press Ctrl+C to stop.

#### Option 2: Cron Job (Production - Recommended)
Add to your crontab (`crontab -e`):

```bash
# Run digest generation daily at 9 AM
0 9 * * * cd /path/to/tag_ai && python run_digest_automation.py --once >> logs/cron_digest.log 2>&1

# Process email queue every hour
0 * * * * cd /path/to/tag_ai && python run_digest_automation.py --once --process-queue-only >> logs/cron_email.log 2>&1
```

#### Option 3: Manual Trigger via UI
Use the **🔧 Admin: Automation** page in Streamlit to manually trigger digest generation and monitor the email queue.

### Configuration

Configure automation settings via the Admin UI:
- **Schedule Time**: When to run (24-hour format)
- **Schedule Days**: Which days to run (0=Monday, 6=Sunday)
- **WordPress URL**: Source for fetching posts
- **Posts Per Run**: Number of posts to fetch each time
- **Send Immediately**: Auto-send emails after generation

View recent logs in the Admin UI or check the log files directly.

### Email Queue

The email queue system prevents timeouts on large memberships:
- Emails are queued with priority levels
- Automatic retry up to 3 times for failed sends
- Rate limiting to avoid SMTP throttling
- Status tracking (pending, sent, failed, retry)

## �📊 Database Schema

- **Members**: User accounts with names and emails
- **Interests**: Predefined interest categories
- **Documents**: Uploaded content with extracted tags
- **Digests**: Personalized summaries for each member-document pair
- **EmailQueue**: 🆕 Queue for digest email delivery with retry logic
- **SchedulerConfig**: 🆕 Configuration for automated digest generation

## 🛠️ Technology Stack

- **Frontend**: Streamlit
- **Backend**: Python, SQLAlchemy
- **Database**: SQLite
- **LLM API**: Groq (llama-3.3-70b-versatile)
- **Automation**: APScheduler, Cron
- **Email**: SMTP (Gmail, SendGrid, etc.), Jinja2 templates
- **Libraries**: python-dotenv, pydantic, beautifulsoup4, requests

## 📂 Project Structure

```
tag_ai/
├── src/
│   ├── app.py                    # Main Streamlit application
│   ├── database.py               # Database models and setup
│   ├── llm_service.py            # Groq LLM integration
│   ├── wordpress_fetcher.py      # WordPress API & web scraping
│   ├── email_service.py          # 🆕 Email service with HTML templates
│   ├── email_queue_processor.py  # 🆕 Email queue & batch processing
│   ├── scheduler_service.py      # 🆕 Automated digest scheduler
│   ├── logger_utils.py           # 🆕 Logging utilities
│   └── __init__.py
├── logs/                         # 🆕 Log files (auto-created)
├── run_digest_automation.py      # 🆕 Standalone automation script
├── .env                          # Environment variables
├── .env.example                  # Example environment configuration
├── requirements.txt              # Python dependencies
└── README.md                     # This file
```

## 💡 Key Highlights

- ✅ **Two LLM Tasks**: Content analysis AND personalized summarization
- ✅ **Interest-based Weighting**: Summaries prioritize matching topics
- ✅ **Dynamic Preferences**: Update interests anytime
- ✅ **Draft System**: Review digests before publishing
- ✅ **Relevance Scoring**: Track how well content matches interests
- 🆕 **Automated Scheduling**: Set-it-and-forget-it digest generation
- 🆕 **Email Delivery**: Branded HTML emails with retry logic
- 🆕 **Comprehensive Logging**: Monitor all operations with timestamped logs
- 🆕 **Production Ready**: Cron integration for reliable automation

## 🎯 Use Cases

- Personal news aggregation with daily email digests
- Research paper summaries for academic teams
- Corporate knowledge sharing with automated distribution
- Educational content curation for students
- Community forum digests sent to members
- Newsletter generation from WordPress blogs

## 🐛 Troubleshooting

### Email Not Sending
1. Check SMTP credentials in `.env` file
2. For Gmail, use an [App Password](https://support.google.com/accounts/answer/185833)
3. Test connection in **Admin: Automation** → **Email Settings** tab
4. Check logs at `logs/digest_YYYYMMDD.log`

### Automation Not Running
1. Verify schedule is enabled in Admin UI
2. Check WordPress URL is correct
3. View logs for error messages
4. Test manually with **"🚀 Run Digest Generation Now"**

