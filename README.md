---

# 📘 Apache Airflow Full Setup & Operations Guide (Ubuntu + Python 3.12)

---

# 🧠 0. Bash vs Zsh (IMPORTANT)

## Check your shell

```bash id="sh1"
echo $SHELL
```

### Bash users:

* config file: `~/.bashrc`

### Zsh users (your case):

* config file: `~/.zshrc`

---

## Add environment variable (Zsh example)

```bash id="sh2"
echo 'export AIRFLOW_HOME=~/airflow' >> ~/.zshrc
source ~/.zshrc
```

---

# Prerequisites

Install the required system packages:
```
sudo apt update
sudo apt install -y python3 python3-pip python3-venv build-essential libssl-dev libffi-dev
```

Check your Python version:
```
python3 --version
```


# 🚀 1. Project Setup

```bash id="s1"
mkdir -p ~/airflow-project
cd ~/airflow-project
```

---

# 🧪 2. Virtual Environment (ALWAYS USE THIS)

```bash id="s2"
python3 -m venv airflow-venv
source airflow-venv/bin/activate
```

✔ You must see:

```text id="s2o"
(airflow-venv)
```

---

# 📦 3. Install Airflow (Python 3.12 safe)

```bash id="s3"
pip install --upgrade pip setuptools wheel
```

```bash id="s4"
export AIRFLOW_VERSION=2.10.5
export PYTHON_VERSION=3.12

pip install "apache-airflow==${AIRFLOW_VERSION}" \
  --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
```
Replace 3.12 with your installed Python version if different. 

---

# 🌍 4. Environment Variables

## Airflow home

```bash id="s5"
export AIRFLOW_HOME=~/airflow
```

### Make permanent:

### ZSH:

```bash id="s6"
echo 'export AIRFLOW_HOME=~/airflow' >> ~/.zshrc
source ~/.zshrc
```

### BASH:

```bash id="s6b"
echo 'export AIRFLOW_HOME=~/airflow' >> ~/.bashrc
source ~/.bashrc
```

---

## Change port (IMPORTANT if Jenkins uses 8080)

```bash id="s7"
export AIRFLOW__WEBSERVER__WEB_SERVER_PORT=8081
```

### Permanent:

```bash id="s7p"
echo 'export AIRFLOW__WEBSERVER__WEB_SERVER_PORT=8081' >> ~/.zshrc
source ~/.zshrc
```

---

# 🛠️ 5. Initialize Airflow

```bash id="s8"
airflow db migrate
```

---

# 👤 6. Create Admin User

⚠️ IMPORTANT: no blank lines between \

```bash id="s9"
airflow users create \
  --username admin \
  --firstname Ritam \
  --lastname Bhadra \
  --role Admin \
  --email you@example.com
```

---

# ▶️ 7. START AIRFLOW

## ✅ Recommended (SIMPLEST)

```bash id="s10"
airflow standalone
```

✔ starts:

* scheduler
* webserver
* triggerer

---

## 🌐 Open UI

```text id="s11"
http://localhost:8081
```

---

## ⚙️ Manual mode (advanced)

### Terminal 1:

```bash id="s12"
airflow scheduler
```

### Terminal 2:

```bash id="s13"
airflow webserver --port 8081
```

---

# 🔁 8. RESTART AIRFLOW

## Safe restart

```bash id="s14"
pkill -f airflow
pkill -f gunicorn
airflow standalone
```

---

# ❌ 9. KILL AIRFLOW PROCESSES

## Kill everything

```bash id="s15"
pkill -f airflow
pkill -f gunicorn
```

---

## Check ports

```bash id="s16"
lsof -i :8081
lsof -i :8793
```

---

## Kill specific PID

```bash id="s17"
kill -9 <PID>
```

---

# 🧹 10. FIX PID ISSUES

If you see:

```text id="s18"
webserver already running / stale pid file
```

Fix:

```bash id="s19"
rm -f ~/airflow/airflow-webserver.pid
rm -f ~/airflow/airflow-scheduler.pid
```

---

# 🚨 11. COMMON ERRORS

---

## ❌ airflow: command not found

```bash id="e1"
source airflow-venv/bin/activate
```

---

## ❌ Port already in use (8080 / 8081 / 8793)

```bash id="e2"
lsof -i :8081
pkill -f airflow
```

---

## ❌ Webserver stuck

```bash id="e3"
pkill -f gunicorn
```

---

## ❌ Missing pandas warning

```bash id="e4"
pip install pandas
```

---

## ❌ SQLite warning

✔ Ignore (dev only)

---

## ❌ Zsh plugin errors

Fix `.zshrc` or remove missing plugins.

---

# 🧠 12. KEY RULES (VERY IMPORTANT)

### ✔ Always:

```bash id="r1"
cd ~/airflow-project
source airflow-venv/bin/activate
```

### ✔ Use ONLY ONE mode:

* `airflow standalone` (recommended)

### ❌ Never mix:

* standalone + manual scheduler
* multiple terminals without control

---

# ⚡ 13. PORT MANAGEMENT (JENKINS CASE)

| Service | Port |
| ------- | ---- |
| Jenkins | 8080 |
| Airflow | 8081 |

Set:

```bash id="r2"
export AIRFLOW__WEBSERVER__WEB_SERVER_PORT=8081
```

---

# 🏁 14. QUICK CHEATSHEET

## Start

```bash id="r3"
source airflow-venv/bin/activate
airflow standalone
```

---

## Stop

```bash id="r4"
pkill -f airflow
pkill -f gunicorn
```

---

## Restart

```bash id="r5"
pkill -f airflow
airflow standalone
```

---

## Fix stuck ports

```bash id="r6"
lsof -i :8081
kill -9 <PID>
```

---
