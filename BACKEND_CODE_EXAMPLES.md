# Journal Publication Platform - Backend Code Examples
## Node.js/Express API Implementation

---

## 📌 FILE STRUCTURE

```
journal-backend/
├── config/
│   └── database.js
├── controllers/
│   ├── articleController.js
│   ├── submissionController.js
│   ├── userController.js
│   └── reviewController.js
├── routes/
│   ├── articles.js
│   ├── submissions.js
│   ├── users.js
│   └── reviews.js
├── models/
│   ├── Article.js
│   ├── User.js
│   ├── Submission.js
│   └── Review.js
├── middleware/
│   ├── auth.js
│   └── validation.js
├── uploads/
│   ├── manuscripts/
│   └── pdfs/
├── .env
├── .gitignore
├── package.json
└── server.js
```

---

## 🔧 SETUP & CONFIGURATION

### package.json
```json
{
  "name": "journal-publication-platform",
  "version": "1.0.0",
  "description": "Peer-reviewed journal publication system",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0",
    "pg": "^8.8.0",
    "dotenv": "^16.0.0",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.0",
    "express-validator": "^7.0.0",
    "multer": "^1.4.5",
    "nodemailer": "^6.9.0",
    "cors": "^2.8.5",
    "helmet": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.0",
    "jest": "^29.0.0"
  }
}
```

### .env File
```
# Database
DATABASE_URL=postgresql://username:password@localhost:5432/journal_db
DATABASE_HOST=localhost
DATABASE_USER=journal_user
DATABASE_PASSWORD=strong_password_123
DATABASE_PORT=5432
DATABASE_NAME=journal_db

# JWT
JWT_SECRET=your_super_secret_jwt_key_change_this_in_production
JWT_EXPIRE=7d

# Email
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-journal@gmail.com
SMTP_PASSWORD=your_app_password
SMTP_FROM=noreply@yourjournal.com

# Server
NODE_ENV=development
PORT=3000
BASE_URL=http://localhost:3000

# File Upload
UPLOAD_PATH=./uploads
MAX_FILE_SIZE=10485760
ALLOWED_MIME_TYPES=application/pdf,application/msword,application/vnd.openxmlformats-officedocument.wordprocessingml.document
```

### server.js
```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const dotenv = require('dotenv');
const path = require('path');

dotenv.config();

const app = express();

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ limit: '10mb', extended: true }));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/uploads', express.static(path.join(__dirname, 'uploads')));

// Database connection test
const pool = require('./config/database');
pool.query('SELECT NOW()', (err, res) => {
  if (err) {
    console.error('Database connection failed:', err);
  } else {
    console.log('Database connected');
  }
});

// Routes
app.use('/api/articles', require('./routes/articles'));
app.use('/api/submissions', require('./routes/submissions'));
app.use('/api/users', require('./routes/users'));
app.use('/api/reviews', require('./routes/reviews'));

// Health check
app.get('/api/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date() });
});

// Error handling
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    success: false,
    message: 'Internal server error'
  });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({ success: false, message: 'Route not found' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});

module.exports = app;
```

---

## 🗄️ DATABASE CONFIGURATION

### config/database.js
```javascript
const { Pool } = require('pg');
const dotenv = require('dotenv');

dotenv.config();

const pool = new Pool({
  connectionString: process.env.DATABASE_URL || 
    `postgresql://${process.env.DATABASE_USER}:${process.env.DATABASE_PASSWORD}@${process.env.DATABASE_HOST}:${process.env.DATABASE_PORT}/${process.env.DATABASE_NAME}`
});

pool.on('error', (err) => {
  console.error('Unexpected error on idle client', err);
});

pool.query('SELECT NOW()', (err, result) => {
  if (err) {
    console.error('Database connection error:', err);
  } else {
    console.log('Database connection successful');
  }
});

module.exports = pool;
```

---

## 🔐 AUTHENTICATION MIDDLEWARE

### middleware/auth.js
```javascript
const jwt = require('jsonwebtoken');
const dotenv = require('dotenv');

dotenv.config();

const auth = (req, res, next) => {
  try {
    const token = req.headers.authorization?.split(' ')[1];

    if (!token) {
      return res.status(401).json({
        success: false,
        message: 'No token provided'
      });
    }

    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.userId = decoded.id;
    req.userRole = decoded.role;
    next();
  } catch (error) {
    return res.status(401).json({
      success: false,
      message: 'Invalid token'
    });
  }
};

const authorize = (roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.userRole)) {
      return res.status(403).json({
        success: false,
        message: 'Unauthorized access'
      });
    }
    next();
  };
};

module.exports = { auth, authorize };
```

---

## 👥 USER MANAGEMENT

### controllers/userController.js
```javascript
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const pool = require('../config/database');
const { validationResult } = require('express-validator');

exports.register = async (req, res) => {
  try {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

    const { email, password, fullName, affiliation, role } = req.body;

    const userCheck = await pool.query(
      'SELECT id FROM users WHERE email = $1',
      [email]
    );

    if (userCheck.rows.length > 0) {
      return res.status(400).json({
        success: false,
        message: 'Email already registered'
      });
    }

    const salt = await bcrypt.genSalt(10);
    const passwordHash = await bcrypt.hash(password, salt);

    const result = await pool.query(
      `INSERT INTO users (email, password_hash, full_name, affiliation, role)
       VALUES ($1, $2, $3, $4, $5)
       RETURNING id, email, full_name, role`,
      [email, passwordHash, fullName, affiliation, role || 'author']
    );

    const token = jwt.sign(
      { id: result.rows[0].id, role: result.rows[0].role },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRE }
    );

    res.status(201).json({
      success: true,
      message: 'User registered successfully',
      token,
      user: result.rows[0]
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Registration failed',
      error: error.message
    });
  }
};

exports.login = async (req, res) => {
  try {
    const { email, password } = req.body;

    const userResult = await pool.query(
      'SELECT * FROM users WHERE email = $1',
      [email]
    );

    if (userResult.rows.length === 0) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }

    const user = userResult.rows[0];
    const validPassword = await bcrypt.compare(password, user.password_hash);

    if (!validPassword) {
      return res.status(401).json({
        success: false,
        message: 'Invalid credentials'
      });
    }

    const token = jwt.sign(
      { id: user.id, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRE }
    );

    res.json({
      success: true,
      message: 'Login successful',
      token,
      user: {
        id: user.id,
        email: user.email,
        fullName: user.full_name,
        role: user.role
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Login failed',
      error: error.message
    });
  }
};

exports.getProfile = async (req, res) => {
  try {
    const result = await pool.query(
      'SELECT id, email, full_name, affiliation, role, created_at FROM users WHERE id = $1',
      [req.userId]
    );

    if (result.rows.length === 0) {
      return res.status(404).json({
        success: false,
        message: 'User not found'
      });
    }

    res.json({
      success: true,
      user: result.rows[0]
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Failed to fetch profile'
    });
  }
};
```

---

## 📄 ARTICLE MANAGEMENT

### controllers/articleController.js
```javascript
const pool = require('../config/database');

exports.getAllArticles = async (req, res) => {
  try {
    const { type, year, search, limit = 20, offset = 0 } = req.query;

    let query = `
      SELECT a.*, 
             array_agg(u.full_name) as authors
      FROM articles a
      LEFT JOIN article_authors aa ON a.id = aa.article_id
      LEFT JOIN users u ON aa.author_id = u.id
      WHERE a.publication_date IS NOT NULL
    `;

    const params = [];
    let paramIndex = 1;

    if (type) {
      query += ` AND a.article_type = $${paramIndex}`;
      params.push(type);
      paramIndex++;
    }

    if (year) {
      query += ` AND EXTRACT(YEAR FROM a.publication_date) = $${paramIndex}`;
      params.push(year);
      paramIndex++;
    }

    if (search) {
      query += ` AND (a.title ILIKE $${paramIndex} OR a.abstract ILIKE $${paramIndex})`;
      params.push(`%${search}%`);
      paramIndex++;
    }

    query += ` GROUP BY a.id ORDER BY a.publication_date DESC LIMIT $${paramIndex} OFFSET $${paramIndex + 1}`;
    params.push(limit, offset);

    const result = await pool.query(query, params);

    res.json({
      success: true,
      data: result.rows
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Failed to fetch articles',
      error: error.message
    });
  }
};

exports.getArticle = async (req, res) => {
  try {
    const { id } = req.params;

    const result = await pool.query(
      `SELECT * FROM articles WHERE id = $1`,
      [id]
    );

    if (result.rows.length === 0) {
      return res.status(404).json({
        success: false,
        message: 'Article not found'
      });
    }

    await pool.query(
      'UPDATE articles SET views = views + 1 WHERE id = $1',
      [id]
    );

    res.json({
      success: true,
      article: result.rows[0]
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Failed to fetch article'
    });
  }
};

exports.createArticle = async (req, res) => {
  try {
    const { title, abstract, keywords, articleType, volume, issue, pages, doi } = req.body;

    const result = await pool.query(
      `INSERT INTO articles (title, abstract, keywords, article_type, volume, issue, pages, doi, publication_date)
       VALUES ($1, $2, $3, $4, $5, $6, $7, $8, NOW())
       RETURNING *`,
      [title, abstract, keywords, articleType, volume, issue, pages, doi]
    );

    res.status(201).json({
      success: true,
      message: 'Article created successfully',
      article: result.rows[0]
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Failed to create article'
    });
  }
};
```

---

## 📝 ARTICLE SUBMISSION

### controllers/submissionController.js
```javascript
const pool = require('../config/database');
const nodemailer = require('nodemailer');

const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: process.env.SMTP_PORT,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASSWORD
  }
});

exports.submitArticle = async (req, res) => {
  try {
    const {
      articleTitle,
      articleType,
      abstract,
      keywords,
      authorName,
      authorEmail,
      institution
    } = req.body;

    if (!req.file) {
      return res.status(400).json({
        success: false,
        message: 'Manuscript file is required'
      });
    }

    const result = await pool.query(
      `INSERT INTO submissions (
        user_id, article_title, article_type, abstract, 
        keywords, manuscript_file, status
       )
       VALUES ($1, $2, $3, $4, $5, $6, 'pending')
       RETURNING *`,
      [
        req.userId,
        articleTitle,
        articleType,
        abstract,
        keywords,
        req.file.filename
      ]
    );

    const submission = result.rows[0];

    await transporter.sendMail({
      from: process.env.SMTP_FROM,
      to: authorEmail,
      subject: 'Article Submission Received',
      html: `
        <h2>Thank you for your submission!</h2>
        <p>We have received your article: <strong>${articleTitle}</strong></p>
        <p><strong>Submission ID:</strong> ${submission.id}</p>
        <p>You will receive updates at each stage of the review process.</p>
      `
    });

    res.status(201).json({
      success: true,
      message: 'Article submitted successfully',
      submissionId: submission.id
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Submission failed',
      error: error.message
    });
  }
};

exports.getMySubmissions = async (req, res) => {
  try {
    const result = await pool.query(
      `SELECT * FROM submissions 
       WHERE user_id = $1 
       ORDER BY submitted_date DESC`,
      [req.userId]
    );

    res.json({
      success: true,
      submissions: result.rows
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      message: 'Failed to fetch submissions'
    });
  }
};
```

---

## 📋 API ENDPOINTS SUMMARY

### Users
```
POST   /api/users/register
POST   /api/users/login
GET    /api/users/profile
PUT    /api/users/profile
```

### Articles
```
GET    /api/articles
GET    /api/articles/:id
POST   /api/articles (admin)
PUT    /api/articles/:id (admin)
DELETE /api/articles/:id (admin)
```

### Submissions
```
POST   /api/submissions
GET    /api/submissions (my submissions)
GET    /api/submissions/:id
PUT    /api/submissions/:id (change status)
```

---

## 🚀 DEPLOYMENT CHECKLIST

- [ ] Environment variables configured
- [ ] Database tables created
- [ ] Email service configured
- [ ] File upload directory created
- [ ] SSL certificate configured
- [ ] API tested with Postman
- [ ] Rate limiting added
- [ ] Logging configured
- [ ] Backups scheduled
- [ ] Monitoring set up

---

This code provides a solid foundation for your journal platform backend. Customize as needed for your specific requirements.
