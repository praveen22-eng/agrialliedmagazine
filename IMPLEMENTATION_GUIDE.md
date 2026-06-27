# Journal of Food & Agricultural Systems - Implementation Guide
## Complete Deployment Instructions

---

## 📋 TABLE OF CONTENTS
1. System Overview
2. Quick Deployment Options
3. GitHub Pages Setup
4. Custom Domain Setup
5. Backend Implementation
6. Database Setup
7. Troubleshooting

---

## 🎯 SYSTEM OVERVIEW

This is a complete, professional journal publication platform with:
- **Frontend**: Single HTML file (no build required)
- **Backend**: Node.js/Express (optional)
- **Database**: PostgreSQL (optional)
- **Hosting**: GitHub Pages, Netlify, or traditional web host

---

## 🚀 QUICK DEPLOYMENT OPTIONS

### FASTEST: Local Testing (2 minutes)
```bash
1. Download journal_publication_system.html
2. Double-click to open in browser
3. View your website locally
```

### FAST: GitHub Pages Hosting (10 minutes)
```bash
1. Create GitHub repository (yourname/yourname.github.io)
2. Upload journal_publication_system.html as index.html
3. Website live at yourname.github.io
```

### MEDIUM: Custom Domain (30 minutes)
```bash
1. Buy domain from GoDaddy, Namecheap, etc.
2. Point domain DNS to GitHub Pages or web host
3. Wait 24 hours for propagation
4. Enable HTTPS
```

### FULL: With Backend & Database (1-2 weeks)
```bash
1. Set up hosting (Heroku, AWS, DigitalOcean, etc.)
2. Install Node.js and PostgreSQL
3. Deploy backend code
4. Connect frontend to API
```

---

## 🐙 GITHUB PAGES SETUP (FREE HOSTING)

### Step-by-Step:

**1. Create GitHub Repository**
```
Go to github.com/new
- Repository name: yourusername.github.io
- Make it PUBLIC
- Click "Create repository"
```

**2. Upload Your Website**
```
Option A: Via GitHub Web Interface
- Go to your new repository
- Click "Add file" → "Upload files"
- Drag & drop journal_publication_system.html
- Rename to index.html
- Commit

Option B: Via Command Line
git clone https://github.com/yourusername/yourusername.github.io
cd yourusername.github.io
cp /path/to/journal_publication_system.html index.html
git add index.html
git commit -m "Add journal website"
git push origin main
```

**3. View Your Website**
```
Visit: https://yourusername.github.io
```

**4. Enable Custom Domain (Optional)**
- Go to Repository Settings → Pages
- Add your custom domain
- Update DNS records at your domain registrar

---

## 🌐 CUSTOM DOMAIN SETUP

### Using Namecheap (or similar registrar):

**1. Point Domain to GitHub Pages**
```
Go to Domain DNS Settings:
- Type: A Record
- Host: @
- Value: 185.199.108.153 (or latest GitHub IP)

Or for subdomain:
- Type: CNAME
- Host: www
- Value: yourusername.github.io
```

**2. Enable HTTPS**
```
GitHub Pages:
- Settings → Pages
- Enforce HTTPS (automatic with Let's Encrypt)

Traditional Hosting:
- Use free Let's Encrypt certificate
- Or purchase from SSL provider
```

**3. Verify Setup**
```bash
# Check DNS propagation
nslookup yourdomain.com

# Should show your hosting provider's IP
```

---

## 🗂️ CUSTOMIZING YOUR WEBSITE

### Edit with Any Text Editor:
```
- VS Code
- Notepad++
- Sublime Text
- Vim
```

### Key Sections to Customize:

**1. Journal Name** (Find & Replace)
```
Find: "Journal of Food & Agricultural Systems"
Replace with: "Your Journal Name"
```

**2. Colors** (Find & Replace)
```
Find: #1976d2
Replace with: Your color code (e.g., #2e7d32)
```

**3. Contact Email**
```
Find: email@example.com
Replace with: your-email@yourjournal.com
```

**4. Editorial Board**
```
Find: Editorial Board section
Update: Names and affiliations
```

---

## 🗄️ DATABASE SETUP (PostgreSQL)

### Option 1: Local Development

**Install PostgreSQL:**
```bash
# macOS
brew install postgresql@14

# Ubuntu/Debian
sudo apt-get install postgresql postgresql-contrib

# Windows
Download from postgresql.org
```

**Create Database:**
```bash
sudo -u postgres createdb journal_db
sudo -u postgres createuser journal_user
sudo -u postgres psql
ALTER USER journal_user WITH PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE journal_db TO journal_user;
```

**Create Tables:**
```sql
-- Users Table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(255),
    affiliation VARCHAR(255),
    role VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Articles Table
CREATE TABLE articles (
    id SERIAL PRIMARY KEY,
    title VARCHAR(500) NOT NULL,
    abstract TEXT NOT NULL,
    keywords TEXT,
    article_type VARCHAR(100),
    volume INT,
    issue INT,
    pages VARCHAR(50),
    doi VARCHAR(255) UNIQUE,
    publication_date TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Submissions Table
CREATE TABLE submissions (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    article_title VARCHAR(500),
    article_type VARCHAR(100),
    abstract TEXT,
    manuscript_file VARCHAR(255),
    status VARCHAR(50),
    submitted_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Reviews Table
CREATE TABLE reviews (
    id SERIAL PRIMARY KEY,
    submission_id INT REFERENCES submissions(id),
    reviewer_id INT REFERENCES users(id),
    review_text TEXT,
    recommendation VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### Option 2: Cloud Database

**Using Amazon RDS:**
```
1. Go to AWS Console
2. Create RDS instance (PostgreSQL)
3. Get connection string
4. Add to .env file
```

**Using Heroku Postgres:**
```
1. Create Heroku app
2. Add PostgreSQL addon
3. Get DATABASE_URL
4. Use in backend
```

---

## 🚀 BACKEND DEPLOYMENT

### Using Heroku (Free tier available):

**1. Install Heroku CLI**
```bash
npm install -g heroku
heroku login
```

**2. Create Heroku App**
```bash
heroku create yourapp
heroku addons:create heroku-postgresql:hobby-dev
```

**3. Deploy Code**
```bash
git init
git add .
git commit -m "Initial commit"
git push heroku main
```

**4. Set Environment Variables**
```bash
heroku config:set JWT_SECRET=your_secret
heroku config:set SMTP_USER=your-email@gmail.com
heroku config:set SMTP_PASSWORD=your_password
```

### Using DigitalOcean:

**1. Create Droplet**
- Choose Ubuntu 20.04
- Select $5/month plan
- Create

**2. SSH into Droplet**
```bash
ssh root@your_server_ip
```

**3. Install Dependencies**
```bash
apt update && apt upgrade
apt install nodejs npm postgresql postgresql-contrib
```

**4. Clone Repository & Deploy**
```bash
cd /var/www
git clone your-repo-url
cd journal-backend
npm install
npm start
```

---

## 📝 FRONTEND CUSTOMIZATION CHECKLIST

Before launching, update these sections:

**Content to Customize:**
- [ ] Journal title
- [ ] Journal subtitle
- [ ] ISSN (once received)
- [ ] Contact email
- [ ] Editor names and affiliations
- [ ] Author guidelines
- [ ] Sample articles
- [ ] Colors/branding
- [ ] Footer information

**Testing Before Launch:**
- [ ] Test in Chrome, Firefox, Safari
- [ ] Test on mobile (F12 → device toolbar)
- [ ] Check all links work
- [ ] Verify form submissions work
- [ ] Test search functionality
- [ ] Check PDF download links

---

## 🆘 TROUBLESHOOTING

### Website Won't Load
```
Solution:
1. Check file name is index.html
2. Verify GitHub Pages is enabled
3. Clear browser cache (Ctrl+Shift+Delete)
4. Try different browser
5. Check repository is PUBLIC
```

### Layout Looks Broken
```
Solution:
1. Clear browser cache
2. Try zooming out (Ctrl+-)
3. Resize browser window
4. Check CSS isn't corrupted
5. Re-upload HTML file
```

### Emails Not Sending (Backend)
```
Solution:
1. Check SMTP credentials
2. Enable "Less secure app access" (Gmail)
3. Use app-specific password
4. Verify email service is running
5. Check logs for errors
```

### Database Connection Error
```
Solution:
1. Verify DATABASE_URL is correct
2. Check database is running
3. Ensure user has correct permissions
4. Test connection locally first
5. Check firewall rules
```

---

## 📊 MONITORING & MAINTENANCE

### Regular Tasks:

**Weekly:**
- Check website is accessible
- Monitor error logs
- Review submissions

**Monthly:**
- Update article content
- Check for broken links
- Review analytics
- Backup database

**Quarterly:**
- Update dependencies
- Security audit
- Performance optimization
- Content review

### Backup Strategy:
```bash
# Automated daily backups
0 2 * * * pg_dump -U journal_user journal_db > /backups/journal_$(date +%Y%m%d).sql

# Weekly website backups
0 3 * * 0 tar -czf /backups/website_$(date +%Y%m%d).tar.gz /var/www/journal/
```

---

## 🎓 ISSN REGISTRATION

### Steps:

1. **Gather Information:**
   - Journal title
   - Editor details
   - Publication frequency
   - Publisher information

2. **Submit Application:**
   - Visit: www.issn.org (international)
   - Or national ISSN center in your country
   - Fill out online form
   - Upload sample issues (3-5 articles)

3. **Processing:**
   - Usually takes 2-4 weeks
   - You'll receive unique ISSN number
   - Can use immediately

4. **Display on Website:**
```html
<div class="issn-badge">ISSN: 2024-1234</div>
```

---

## 💡 NEXT STEPS

### Immediately:
1. ✅ Customize HTML file
2. ✅ Test locally
3. ✅ Deploy to GitHub Pages

### This Week:
4. Apply for domain
5. Set up custom email
6. Finalize author guidelines

### This Month:
7. Apply for ISSN
8. Set up backend (if needed)
9. Start accepting submissions

### Next 3 Months:
10. Build author network
11. Recruit reviewers
12. Set up peer review process

---

## 📞 SUPPORT RESOURCES

- GitHub Pages Help: https://pages.github.com
- PostgreSQL Docs: https://www.postgresql.org/docs
- Node.js Docs: https://nodejs.org/docs
- Heroku Docs: https://devcenter.heroku.com

---

**Version**: 1.0
**Last Updated**: June 2026
**License**: MIT

Questions? Check QUICK_START_GUIDE.md for additional help.
