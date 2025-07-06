# 📦 Automated Backup and Rotation Script with Google Drive Integration

A reliable, automated solution to back up your project directory, store the backup locally and in Google Drive, apply a rotational backup strategy, and notify via webhook.

---

## ✅ Overview of the Project

This script automates backups for any specified project directory. It:

- Creates `.zip` archives with timestamped names.
- Saves backups in structured folders (`YYYY/MM/DD`).
- Uploads to Google Drive using `rclone`.
- Implements a rotational backup strategy (daily, weekly, monthly).
- Sends a webhook notification on successful backup.
- Logs all actions to a local log file.

---

## ▶️ Instructions to Run the Script with Arguments

1. **Install Python and dependencies**
2. **Create a `.env` file** for configuration
3. **Run the script manually or via cron**

### Basic Usage:

```bash
python3 backup_script.py
```

Disable webhook notification:
```bash
python3 backup_script.py --no-notify
```
🔧 How to Install and Configure rclone (Google Drive CLI)
1. Install rclone:
```bash
curl https://rclone.org/install.sh | sudo bash
```
3. Configure rclone with Google Drive:
```bash
rclone config
```
- Choose n for a new remote

- Name it (e.g., gdrive)

- Select 13 for Google Drive

- Choose auto config

- Authorize in browser

- Go to your local pc command prompt

3. command prompt:
```bash
ssh -i <.pem key> -L 53682:localhost:53682 ubuntu@<public-ip> -N
```
⚙️ How to Configure Retention Settings
In your .env file:

```env

RETENTION_DAYS=7          # Keep last 7 daily backups
RETENTION_WEEKS=4         # Keep last 4 Sundays
RETENTION_MONTHS=3        # Keep backups from the 1st of the last 3 months
The script will automatically delete older backups beyond these settings.
```
💡 Example Usage and Expected Output
Example .env File:

```bash
PROJECT_NAME=MyGitHubProject
PROJECT_DIR=/home/ubuntu/projects/MyGitHubProject
BACKUP_DIR=/home/ubuntu/backups
RCLONE_REMOTE=gdrive
RCLONE_FOLDER=MyProjectBackups
LOG_FILE=/home/ubuntu/project_backup/backup.log
NOTIFY_URL=https://webhook.site/your-unique-url
ENABLE_NOTIFY=true
RETENTION_DAYS=7
RETENTION_WEEKS=4
RETENTION_MONTHS=3
```
Sample Log Output (backup.log):
```bash
[2025-07-06 02:00:01] === Backup Started for MyGitHubProject ===
[2025-07-06 02:00:02] Backup created at 2025-07-06 02:00:02 — File: /home/ubuntu/backups/MyGitHubProject/2025/07/06/MyGitHubProject_20250706_020002.zip
[2025-07-06 02:00:03] Uploaded to Google Drive: MyGitHubProject_20250706_020002.zip
[2025-07-06 02:00:04] Deleted 2 old backups:
 - /home/ubuntu/backups/MyGitHubProject/2025/06/28
 - /home/ubuntu/backups/MyGitHubProject/2025/06/29
[2025-07-06 02:00:05] Webhook sent successfully.
[2025-07-06 02:00:05] === Backup Completed for MyGitHubProject ===
```
📡 Sample cURL Webhook with Payload
If enabled, a webhook request is sent as:

Sample JSON Payload:
```bash
json

{
  "project": "MyGitHubProject",
  "date": "2025-07-06T02:00:04Z",
  "status": "BackupSuccessful",
  "file": "MyGitHubProject_20250706_020002.zip"
}
```
Manually test with:
```bash
curl -X POST -H "Content-Type: application/json" \
-d '{"project": "MyGitHubProject", "date": "2025-07-06T02:00:04", "status": "BackupSuccessful", "file": "MyGitHubProject_20250706_020002.zip"}' \
https://webhook.site/your-unique-url
```
🔐 Security Considerations
🔒 Never push .env, backup.log, or .zip files to GitHub

Use .gitignore to exclude sensitive files:
```bash
gitignore

.env
*.log
*.zip
backups/
```
- Use OAuth scopes in rclone that restrict access only to the required folder.

- Regularly audit your Google Drive access permissions.

- Secure the webhook URL (avoid sharing publicly).

⏱️ Automate with Crontab (Optional)
Schedule a daily backup at 2:00 AM:
```bash
bash

crontab -e
```
Add this line:
```bash
bash

0 1 * * * /usr/bin/python3 /home/ubuntu/project_backup/backup_script.py >> /home/ubuntu/project_backup/cron.log 2>&1
```
📄 License
MIT License © 2025

👨‍💻 Author
Built by [Shubham Sarkar] — Cloud Engineer & DevOps Enthusiast 🚀



