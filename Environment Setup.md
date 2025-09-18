# Environment Setup

---

## 1. ✅ System Requirements

Before installing Odoo, ensure your system meets the following requirements:

### 📦 General Requirements

- **OS:** Ubuntu 20.04 or newer (preferred for Linux), Windows 10/11, macOS
- **Python:** 3.8 – 3.11 (depends on Odoo version)
- **PostgreSQL:** 12 or newer
- **Node.js:** 14.x or newer (for assets compilation)
- **npm / yarn:** For frontend assets
- **Git:** For source code management
- **wkhtmltopdf:** For PDF report generation

### 🛠 Required Libraries (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install git python3-pip build-essential wget python3-dev \
  libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev \
  libjpeg-dev libpq-dev libffi-dev libtiff-dev libopenjp2-7-dev \
  liblcms2-dev libwebp-dev libharfbuzz-dev libfribidi-dev \
  libxcb1-dev libpq-dev libssl-dev

# Install PostgreSQL
sudo apt install postgresql postgresql-client

# Install Node.js and npm
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
sudo npm install -g yarn

# Install wkhtmltopdf (for reports)
sudo apt install wkhtmltopdf
```

---

## 2. 🔧 Odoo Installation

Odoo can be installed in two ways:

### Option A: 📁 Source Installation (Recommended for Developers)

#### 1. Clone Odoo

```bash
git clone https://github.com/odoo/odoo.git -b 16.0  # or 14.0+ depending on your target version
cd odoo
```

#### 2. Create Python Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
```

#### 3. Install Python Dependencies

```bash
pip install wheel setuptools
pip install -r requirements.txt
```

#### 4. Create PostgreSQL User

```bash
sudo -u postgres createuser -s odoo
```

#### 5. Run Odoo

```bash
./odoo-bin -c odoo.conf
```

---

### Option B: 🐳 Docker Installation

Create a `docker-compose.yml`:

```yaml
version: '3'
services:
  web:
    image: odoo:16
    depends_on:
      - db
    ports:
      - "8069:8069"
    volumes:
      - ./addons:/mnt/extra-addons
      - ./config:/etc/odoo
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=odoo
  db:
    image: postgres:13
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

Run:

```bash
docker-compose up -d
```

---

## 3. 🧠 IDE Setup (VS Code / PyCharm)

### A. 💻 Visual Studio Code

**Recommended Extensions:**

- Python
- pylint
- Odoo Snippets
- XML Tools (for Odoo views)

---

### B. 🧠 PyCharm (Professional Preferred for Odoo)

1. Set Project Interpreter → Point to your virtual environment
2. Enable Python support
3. Install `pylint` & `pylint-odoo` in your interpreter
4. Code Style → Configure for Python and XML
5. Create Run Configuration for `odoo-bin` with `-c odoo.conf`

---

## 4. ⚙️ Configuration Files & Log Levels

### 📝 `odoo.conf` Sample

```ini
[options]
addons_path = addons,custom_addons
admin_passwd = admin123
db_host = False
db_port = False
db_user = odoo
db_password = False
logfile = /var/log/odoo/odoo.log
log_level = debug
xmlrpc_port = 8069
dev_mode = True
```

### 🔍 Log Levels in Odoo

Set the log level in the config file or via CLI:

```ini
log_level = info
```

**Possible values:**

- `critical`
- `error`
- `warning`
- `info` *(default)*
- `debug`
- `debug_rpc`
- `debug_sql`
- `debug_rpc_answer`
- `debug_tests`

To run with a specific log level via command line:

```bash
./odoo-bin --log-level=debug
```

---

## ✅ Final Checklist

| Task                         | Status |
| ---------------------------- | ------ |
| System libraries installed   | ✅      |
| PostgreSQL running           | ✅      |
| Odoo source cloned/setup     | ✅      |
| Virtualenv and dependencies  | ✅      |
| IDE configured               | ✅      |
| `odoo.conf` created          | ✅      |
| Run Odoo on `localhost:8069` | ✅      |

---
