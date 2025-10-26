---
layout: post
title: Museum Collection Manager App
image: "https://commons.wikimedia.org/wiki/File:Pavillon_de_Flore,_Louvre_Museum,_Paris_5_November_2019.jpg"
tags: [flask, nginx, aws-ec2, ubuntu]
---

# Museum Collection Manager 

A full-stack web application for managing museum collections, artworks, artists, and exhibitions built with Flask and MySQL.

##  Live Demo

**Live Application:** [https://museums.khilldata.com](https://museums.khilldata.com)

## Features

- **Artist Management**: Track artist biographies, birth/death dates, art movements, and portfolios
- **Artwork Cataloging**: Detailed artwork records with dimensions, valuations, signatures, and provenance
- **Museum Registry**: Comprehensive museum information including locations, visitor statistics, and contact details
- **Collection Management**: Link artworks to museums with acquisition details, accession numbers, and display status
- **SQL Playground**: Interactive query builder for exploring collection data
- **Data Export**: Export collection data to CSV format
- **Professional UI**: Responsive design with beautiful artwork backgrounds and modern styling

## Technology Stack

**Backend:**
- Python 3.12
- Flask (web framework)
- Flask-WTF (form handling & validation)
- MySQL (database)
- Gunicorn (WSGI server)

**Frontend:**
- HTML5/CSS3
- JavaScript (ES6+)
- Font Awesome icons
- Responsive design

**Deployment:**
- AWS EC2 (Ubuntu 24.04)
- Nginx (reverse proxy)
- Let's Encrypt (SSL/TLS)
- Systemd (service management)

## Prerequisites

- Python 3.8+
- MySQL 8.0+
- pip (Python package manager)

## Installation

### 1. Clone the repository
```bash
git clone https://github.com/kd365/museum-collection-manager.git
cd museum-collection-manager
```

### 2. Create virtual environment
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Setup MySQL Database
```bash
sudo mysql

# Create database and user
CREATE DATABASE museumapp;
CREATE USER 'museum_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON museumapp.* TO 'museum_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

# Import schema
mysql -u museum_user -p museumapp < schema.sql
```

### 5. Configure Environment Variables

Create a `.env` file:
```bash
SECRET_KEY=your-secret-key-here
DB_HOST=localhost
DB_USER=museum_user
DB_PASSWORD=your_secure_password
DB_NAME=museumapp
```

### 6. Run the Application

**Development:**
```bash
python3 museums_app.py
```

**Production:**
```bash
gunicorn --bind 127.0.0.1:8000 --workers 3 museums_app:app
```

Access at: `http://localhost:5000` (development) or `http://localhost:8000` (production)

##  Database Schema

The application uses four main tables:

- **artists**: Artist biographical information and metadata
- **artworks**: Detailed artwork records with dimensions and valuations
- **museums**: Museum information including location and visitor data
- **collections**: Junction table linking artworks to museums with acquisition details

See `schema.sql` for complete database structure.

##  Key Features Explained

### Form Validation
- Server-side validation using Flask-WTF
- CSRF protection on all forms
- Data type validation (dates, URLs, decimals)
- Required field enforcement

### Security
- Environment-based configuration
- SQL injection prevention (parameterized queries)
- HTTPS encryption (Let's Encrypt)
- Secure headers (HSTS, X-Frame-Options, etc.)

### User Experience
- Flash messages for user feedback
- Responsive design for mobile/tablet/desktop
- Professional artwork imagery
- Auto-closing notifications

## Project Structure
```
museum-collection-manager/
├── museums_app.py          # Main Flask application
├── config.py               # Configuration management
├── forms.py                # WTForms form definitions
├── requirements.txt        # Python dependencies
├── .env                    # Environment variables (not in repo)
├── templates/              # Jinja2 HTML templates
│   ├── base.html
│   ├── index.html
│   ├── artist_form.html
│   ├── artists.html
│   └── ...
├── static/                 # Static assets
│   ├── css/
│   │   └── style.css
│   └── js/
│       └── main.js
└── schema.sql              # Database schema
```

## Production Deployment

The live application is deployed on AWS EC2 with:
- Nginx as reverse proxy
- Gunicorn as WSGI server
- Systemd for service management
- Let's Encrypt for SSL/TLS

See deployment documentation for details.

##  API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/` | GET | Homepage with statistics |
| `/artists` | GET | List all artists |
| `/artists/create` | GET/POST | Create new artist |
| `/artists/<id>/edit` | GET/POST | Edit artist |
| `/artists/<id>/delete` | POST | Delete artist |
| `/artworks` | GET | List all artworks |
| `/artworks/create` | GET/POST | Create new artwork |
| `/museums` | GET | List all museums |
| `/collections` | GET | List collection items |
| `/sql-playground` | GET/POST | Interactive SQL query builder |
| `/export/<table>` | GET | Export table data to CSV |

## Contributing

This is a portfolio project, but suggestions and feedback are welcome!

## Author

**Kathleen Hill**
- Portfolio: [khilldata.com](https://khilldata.com)
- GitHub: [@kd365](https://github.com/kd365)
- LinkedIn: [kathleen-hill322](https://www.linkedin.com/in/kathleen-hill322/)

##  License

This project is open source and available under the MIT License.

## Acknowledgments

- Artwork images courtesy of Wikimedia Commons
- Icons by Font Awesome
- Fonts: Playfair Display & Lato (Google Fonts)
