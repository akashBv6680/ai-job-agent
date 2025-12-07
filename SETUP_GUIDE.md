# AI Job Application Automation Agent - Complete Setup Guide

## 🎯 Overview

This is a production-ready AI-powered job application automation system designed for data science professionals. It:

- **Scrapes** job postings from LinkedIn, GUVI, and HyreNet
- **Analyzes** job descriptions using NLP to extract key skills and requirements
- **Generates** personalized cover letters using Google Gemini API
- **Creates** interview preparation materials (questions + STAR answers)
- **Tracks** your application funnel (applied → interviewed → offered)
- **Sends** automated follow-up emails to recruiters after 5 days
- **Visualizes** metrics with a real-time Streamlit dashboard

## 📋 Quick Start (5 minutes)

```bash
# 1. Clone the repository
git clone https://github.com/akashBv6680/ai-job-agent.git
cd ai-job-agent

# 2. Install dependencies
pip install -r requirements.txt

# 3. Set up environment variables
cp .env.example .env
# Edit .env and add your API keys (see below)

# 4. Create Airtable base
# Create table named 'Applications' with columns:
# - job_id (text)
# - company_name (text)
# - job_title (text)
# - job_url (text)
# - required_skills (text)
# - cover_letter (long_text)
# - interview_prep (long_text)
# - status (select: applied/interviewed/offered/rejected)
# - applied_date (date)
# - follow_up_date (date)
# - notes (long_text)

# 5. Launch the app
streamlit run src/main.py
```

## 🔑 API Keys Setup

### 1. **Gemini API Key** (Free)
- Go to: https://ai.google.dev
- Click "Get API Key"
- Create new API key for free (50 requests/minute)
- Copy to `.env` as `GEMINI_API_KEY`

### 2. **Airtable API Key** (Free)
- Go to: https://airtable.com/account/api
- Create new API token
- Copy to `.env` as `AIRTABLE_API_KEY`
- Also get your base ID and table name

### 3. **Gmail for Email Automation** (Gmail App Password)
- Enable 2-factor authentication on your Gmail account
- Go to: https://myaccount.google.com/apppasswords
- Generate app password for Gmail
- Use this password in `.env` as `EMAIL_PASS`

## 📁 Project Structure

```
ai-job-agent/
├── src/
│   ├── main.py                 # Streamlit dashboard
│   ├── scraper.py              # Job scraping module
│   ├── llm_module.py           # Gemini API integration
│   ├── airtable_client.py      # Database operations
│   ├── application_tracker.py  # Funnel tracking
│   ├── followup.py             # Email automation
│   └── utils.py                # Helper functions
├── data/
│   └── applications.db         # Local SQLite backup
├── logs/
│   └── job_agent.log           # Application logs
├── .env.example                # Environment template
├── requirements.txt            # Python dependencies
├── SETUP_GUIDE.md             # This file
└── README.md                   # Project overview
```

## 🚀 Core Features

### 1. Job Scraping (scraper.py)
- Scrapes LinkedIn job search results
- Extracts: company, title, description, requirements
- Filters by location (Bangalore), roles (DS/ML/AI), experience
- Stores raw job data in Airtable

```python
from src.scraper import JobScraper

scraper = JobScraper()
jobs = scraper.scrape_linkedin(
    roles=['Data Scientist', 'ML Engineer'],
    location='Bangalore',
    remote_preferred=True
)
```

### 2. LLM Integration (llm_module.py)
- Analyzes job descriptions with NLP
- Generates personalized cover letters
- Creates interview preparation questions
- Provides STAR method answers based on your experience

```python
from src.llm_module import JobAnalyzer

analyzer = JobAnalyzer(api_key=GEMINI_API_KEY)
cover_letter = analyzer.generate_cover_letter(
    job_description=job['description'],
    company_name=job['company'],
    your_resume=your_resume_text
)
```

### 3. Application Tracking (application_tracker.py)
- Real-time dashboard showing:
  - Total applications sent
  - Conversion rates (applied → interviewed → offered)
  - Days in funnel
  - Success metrics by company/role

### 4. Email Follow-ups (followup.py)
- Automatically sends follow-up emails after 5 days
- Uses email templates with personalization
- Tracks delivery and opens
- Can be scheduled with cron jobs

```python
from src.followup import EmailFollowup

followup = EmailFollowup()
followup.send_followups()  # Runs on schedule
```

## ⚙️ Configuration (.env)

```bash
# Gemini API
GEMINI_API_KEY=your_api_key_here

# Airtable
AIRTABLE_API_KEY=your_api_key_here
AIRTABLE_BASE_ID=appXxxxxxYyyyyZzzzzz
AIRTABLE_TABLE_NAME=Applications

# Email
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password

# LinkedIn (optional, for direct scraping)
LINKEDIN_EMAIL=your_email@example.com
LINKEDIN_PASS=your_password

# Job Preferences
JOB_ROLES=Data Scientist,ML Engineer,AI Engineer
BANGALORE_LOCATION=Bangalore
REMOTE_PREFERRED=true
MIN_EXPERIENCE_YEARS=0
MAX_EXPERIENCE_YEARS=3

# Automation
SCRAPE_INTERVAL_HOURS=24
FOLLOWUP_DELAY_DAYS=5
```

## 📊 Streamlit Dashboard Usage

```bash
streamlit run src/main.py
```

The dashboard includes:

1. **Overview** - Key metrics
   - Total applications
   - Conversion rate
   - Average days to response

2. **Application Tracker** - See all applications
   - Filter by status, company, date
   - View cover letter & interview prep
   - Update status

3. **Analytics** - Funnel visualization
   - Applied → Interviewed → Offered
   - Success rate by company/role
   - Trend analysis

4. **Settings** - Configuration management
   - Update job preferences
   - Manage email templates
   - View logs

## 🔄 Automation with Cron Jobs

### Linux/Mac
```bash
# Edit crontab
crontab -e

# Add this line to run scraper daily at 9 AM
0 9 * * * cd /path/to/ai-job-agent && python -m src.scraper > logs/scraper.log 2>&1

# Add this to send follow-ups at 10 AM
0 10 * * * cd /path/to/ai-job-agent && python -m src.followup > logs/followup.log 2>&1
```

### Windows (Task Scheduler)
1. Open Task Scheduler
2. Create Basic Task
3. Set trigger (daily 9 AM)
4. Set action: `python -m src.scraper`
5. Repeat for followup.py

## 📱 Deployment Options

### Option 1: Streamlit Cloud (Free)
```bash
# Push to GitHub and connect to Streamlit Cloud
# Automatic deployment on each push
```

### Option 2: Heroku (Free tier ended, but low cost)
```bash
heroku create your-app-name
git push heroku main
```

### Option 3: Railway/Render (Free tier available)
- Connect GitHub repository
- Set environment variables
- Auto-deploys on push

## 🐛 Troubleshooting

**Issue**: Gemini API returning rate limit errors
- **Solution**: Reduce scraping frequency, add delays between API calls

**Issue**: Airtable connection fails
- **Solution**: Verify API key, base ID, and table name in `.env`

**Issue**: Email not sending
- **Solution**: Enable Gmail "Less secure app access" or use app password

## 📚 Next Steps

1. **Customize cover letter templates** - Edit templates in `llm_module.py`
2. **Add more job sources** - Extend scraper for GUVI, HyreNet
3. **Integrate LinkedIn messaging** - Auto-message recruiters
4. **Voice-enabled resume** - Add audio interface for interviews
5. **Multi-language support** - Generate cover letters in multiple languages

## 🎓 Learning Resources

- [Streamlit Docs](https://docs.streamlit.io)
- [LangChain Docs](https://python.langchain.com)
- [Google Gemini API](https://ai.google.dev/docs)
- [Airtable API](https://airtable.com/api)
- [Selenium Web Scraping](https://selenium.dev/)

## 📞 Support

For issues, questions, or contributions:
1. Open an issue on GitHub
2. Check existing discussions
3. Submit a pull request

## 📄 License

MIT License - Feel free to use for personal projects

---

**Ready to automate your job search? Start with the Quick Start above!** 🚀
