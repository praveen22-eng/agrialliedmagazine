# Journal Publication Website - Quick Start Guide
## Get Your Journal Online in 30 Minutes

---

## ⏱️ 30-MINUTE QUICK SETUP

### Step 1: Download Files (2 minutes)
- [ ] Download `journal_publication_system.html`
- [ ] Save to your computer
- [ ] Keep in a dedicated folder

### Step 2: Customize Journal Details (5 minutes)

Open the HTML file with a text editor (Notepad++, VS Code, Sublime Text)

**Find and Replace (CTRL+H):**

1. **Journal Title**
   - Find: `Journal of Food & Agricultural Systems`
   - Replace with: `Your Journal Name`

2. **Journal Subtitle**
   - Find: `A peer-reviewed open access publication on sustainable food systems and agriculture`
   - Replace with: `Your journal description`

3. **ISSN Placeholder**
   - Find: `ISSN: [Applied]`
   - Replace with: `ISSN: XXXX-XXXX` (once you receive it)

4. **Contact Email** (search for "email@example.com")
   - Replace with: `your-email@yourjournal.com`

### Step 3: Update Sample Articles (5 minutes)

**For Home Page:**
Search for "Analyzing Value Chain Development in TOP Crops" and replace with:
- Your article title
- Your author names
- Your abstract
- Your publication date

**Do this for 2-3 of your articles**

### Step 4: Customize Article Types (3 minutes)

Find this section:
```html
<option>Research Article</option>
<option>Case Studies</option>
<option>Success Stories</option>
<option>Reviews</option>
```

Keep or modify based on your journal scope.

### Step 5: Set Your Colors (5 minutes)

Find the color codes:
- `#1976d2` = Primary Blue (change this for your brand color)
- `#d4edda` = Success Green
- `#fff3cd` = Warning Yellow
- `#f8d7da` = Danger Red

**Common brand colors:**
```
Professional Blue:     #1565c0
Research Green:       #2e7d32
Academic Purple:      #6a1b9a
Publication Red:      #c62828
Tech Orange:          #f57c00
```

Example: Change blue to green
```
Find:    #1976d2
Replace: #2e7d32
```

### Step 6: Upload to Web (10 minutes)

**Option A: Free Hosting (GitHub Pages)**
1. Create account at github.com
2. Create new repository named: `yourname.github.io`
3. Upload the HTML file
4. Access at: `yourname.github.io`

**Option B: Paid Hosting**
1. Buy hosting from: Bluehost, GoDaddy, SiteGround
2. Upload via FTP/File Manager
3. Set as homepage (index.html)
4. Access via your domain

**Option C: Local Server (Testing)**
1. Just double-click the HTML file
2. It opens in your browser
3. Share the file with others via email/cloud

---

## 🔧 QUICK CUSTOMIZATION SNIPPETS

### Change Journal Name in 3 Places:
```
1. <div class="journal-title">YOUR NAME</div>
2. <title>YOUR NAME - Publication Platform</title>
3. Header logo text
```

### Update Footer:
Find:
```html
<p>© 2026 Journal of Food & Agricultural Systems...</p>
```
Replace year and name.

### Add Your Editorial Board:
Find "Editorial Board" section, add:
```html
<h3>Chief Editor</h3>
<p>Dr. Your Name<br/>Institution Name<br/>email@institution.com</p>

<h3>Associate Editors</h3>
<p>Dr. Editor 1 | Dr. Editor 2 | Dr. Editor 3</p>
```

### Customize Author Guidelines:
Find "Author Guidelines" section and update submission requirements:
- Manuscript length
- Citation style
- File formats
- Review process timeline

### Add Your Research Areas:
Find article type cards and modify:
```html
<div class="type-card">
  <div class="type-icon">📊</div>
  <div class="type-title">Data Analysis</div>
  <div class="type-desc">Original research with statistical analysis</div>
</div>
```

---

## 📱 MOBILE PREVIEW

To test on mobile before uploading:

**Using Developer Tools:**
1. Open HTML file in Chrome
2. Press F12 (Developer Tools)
3. Click mobile device icon (top left)
4. Choose iPhone or Android to preview

---

## 📧 SETUP EMAIL NOTIFICATIONS

When you add a backend (Phase 2), set up notifications:

```javascript
// Example: Notify author of submission
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  service: 'gmail',
  auth: {
    user: 'your-journal@gmail.com',
    pass: 'your-password'
  }
});

const mailOptions = {
  from: 'your-journal@gmail.com',
  to: 'author@email.com',
  subject: 'Article Submission Received',
  html: `
    <h2>Thank You for Your Submission</h2>
    <p>We have received your article: <strong>${articleTitle}</strong></p>
    <p>Submission ID: ${submissionId}</p>
    <p>You will receive updates at each stage of the review process.</p>
  `
};

transporter.sendMail(mailOptions, (error, info) => {
  if (error) console.log(error);
  else console.log('Email sent: ' + info.response);
});
```

---

## 🌐 DOMAIN SETUP

### If you bought a domain:

1. **Point domain to hosting:**
   - Go to domain registrar (GoDaddy, Namecheap, etc.)
   - Find DNS settings
   - Add hosting provider's nameservers
   - Wait 24 hours for propagation

2. **Enable HTTPS:**
   - Most hosts offer free SSL (Let's Encrypt)
   - Check hosting control panel
   - Enable in 1 click

3. **Verify with WHOIS:**
   ```
   whois yourdomain.com
   ```
   Check that domain points to your hosting IP

---

## ✅ LAUNCH CHECKLIST

Before announcing your journal:

**Content:**
- [ ] Journal name and description finalized
- [ ] Editor and board information complete
- [ ] Author guidelines written
- [ ] Privacy policy created
- [ ] Ethical guidelines documented

**Technical:**
- [ ] Website tested in Chrome, Firefox, Safari
- [ ] Mobile responsive verified
- [ ] HTTPS/SSL enabled
- [ ] Email notifications configured
- [ ] Backup system set up

**Legal:**
- [ ] ISSN applied for (takes 2-4 weeks)
- [ ] Copyright notice added
- [ ] Terms of use written
- [ ] Data privacy policy compliant

**Marketing:**
- [ ] Social media accounts created
- [ ] Email list started
- [ ] Authors contacted
- [ ] Journal listed on DOAJ (doaj.org)
- [ ] Listed on Google Scholar

---

## 🚀 LAUNCH SEQUENCE

### Week 1: Soft Launch
- Publish website
- Add 3-5 sample articles
- Invite friends/colleagues to test
- Gather feedback

### Week 2: Refinement
- Fix any bugs found
- Update based on feedback
- Prepare first official issue

### Week 3: Hard Launch
- Apply for ISSN
- List on academic databases
- Announce on social media
- Send invitation to authors

### Week 4: Growth
- Start accepting submissions
- Build author network
- Reach out to reviewers
- Plan next issue

---

## 📊 TRACKING SUCCESS

Monitor these metrics after launch:

**Traffic:**
- Unique visitors per month
- Articles read
- Countries accessing journal

**Engagement:**
- Article downloads
- Citations
- Social media shares
- Email subscriber growth

**Content:**
- Submissions received per month
- Acceptance rate
- Time to publication
- Author retention

---

## 🆘 TROUBLESHOOTING

### "Website won't load"
- Check file name is `index.html` or `journal_publication_system.html`
- Try opening in different browser
- Clear browser cache (Ctrl+Shift+Delete)

### "Layout looks broken"
- Zoom out browser (Ctrl+-)
- Check window size
- Try different browser
- Update browser to latest version

### "Colors look wrong"
- Check file wasn't corrupted during download
- Re-download and try again
- Make sure you saved changes

### "Articles don't display"
- Check HTML syntax isn't broken
- Make sure closing tags are present
- Validate HTML at w3.org

---

## 📚 NEXT LEARNING STEPS

Once basic site is live:

1. **Learn About Peer Review**
   - Read: "A Guide to Academic Peer Review" (Elsevier)
   - Set up review process

2. **Master ISSN & DOI**
   - Register with CrossRef for DOIs
   - Each article gets unique identifier

3. **Grow Your Community**
   - Build author/reviewer network
   - Start social media presence
   - Write blog posts

4. **Optimize for Discovery**
   - Google Scholar indexing
   - DOAJ listing
   - ResearchGate promotion

5. **Plan Features**
   - Email alerts for new issues
   - Archive organization
   - Author profiles
   - Citation tracking

---

## 💬 QUICK SUPPORT TIPS

**When contacting hosting support:**
```
Subject: Help with journal website
Please help me:
- Upload my website to my domain
- Enable HTTPS/SSL certificate
- Set up email on my domain
- Configure backups
```

**When contacting web developer:**
```
I need help setting up a journal platform with:
- User registration (authors, reviewers)
- Article submission system
- Peer review workflow
- Database for articles and submissions
Budget: [Your budget]
Timeline: [Your timeline]
```

---

## 🎉 READY TO LAUNCH!

You now have:
✅ Professional website template
✅ All features needed for peer-reviewed publication
✅ Customization guide
✅ Implementation roadmap
✅ Backup and recovery plan

### Your next action:
1. Customize the HTML file with YOUR information
2. Test on different devices
3. Upload to web hosting
4. Apply for ISSN
5. Start building your author network

---

## 📞 CONTACT & SUPPORT

For questions about the platform:
- Email: your-email@yourjournal.com
- Website: yourjournal.com
- Twitter: @YourJournal
- LinkedIn: Your Journal Name

---

**Version**: 1.0
**Last Updated**: June 2026
**License**: Free to use and modify

Congratulations on launching your journal! 🎓📚
