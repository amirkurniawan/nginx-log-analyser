# Nginx Log Analyser

Tool CLI untuk menganalisis nginx access log dan menampilkan statistik yang berguna. Sangat membantu untuk monitoring traffic server dan identifikasi pattern.

Project ini bagian dari [roadmap.sh/projects/nginx-log-analyser](https://roadmap.sh/projects/nginx-log-analyser).

---

## Features

- Top 5 IP addresses dengan request terbanyak
- Top 5 path yang paling sering diakses
- Top 5 HTTP status codes
- Top 5 user agents
- Summary statistics (total requests, unique IPs, response breakdown)

---

## Installation

```bash
# Clone repository
git clone https://github.com/amirkurniawan/nginx-log-analyser.git

# Masuk ke directory
cd nginx-log-analyser

# Beri execute permission
chmod +x nginx-log-analyser

# (Optional) Install global
sudo cp nginx-log-analyser /usr/local/bin/
```

---

## Usage

```bash
# Basic usage
nginx-log-analyser <log-file>

# Contoh: analyze nginx access log
nginx-log-analyser /var/log/nginx/access.log

# Contoh: analyze custom log file
nginx-log-analyser ./nginx-access.log

# Dengan sudo jika perlu
sudo nginx-log-analyser /var/log/nginx/access.log

# Tampilkan help
nginx-log-analyser --help
```

---

## Sample Log File

Download sample log file untuk testing:

```bash
curl -O https://gist.githubusercontent.com/kamranahmedse/e66c3b9ea89a1a030d3b739eeeef22d0/raw/77fb3ac837a73c4f0206e78a236d885590b7ae35/nginx-access.log
```

---

## Sample Output

```
═══════════════════════════════════════════════════════
              NGINX LOG ANALYSER                       
═══════════════════════════════════════════════════════
Analyzing: /var/log/nginx/access.log

Log Summary
==================================================
Total Requests    : 5000
Unique IPs        : 234
Unique Paths      : 89

Response Breakdown:
  2xx (Success)   : 4200
  3xx (Redirect)  : 150
  4xx (Client Err): 580
  5xx (Server Err): 70

Top 5 IP Addresses with Most Requests
==================================================
178.128.94.113 - 1087 requests
142.93.136.176 - 1087 requests
138.68.248.85 - 1087 requests
159.89.185.30 - 1086 requests
86.134.118.70 - 277 requests

Top 5 Most Requested Paths
==================================================
/api/v1/users - 1500 requests
/api/v1/products - 890 requests
/api/v1/orders - 450 requests
/ - 320 requests
/api/v1/payments - 210 requests

Top 5 Response Status Codes
==================================================
200 - 3800 requests
304 - 400 requests
404 - 500 requests
500 - 70 requests
401 - 80 requests

Top 5 User Agents
==================================================
Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/91.0 - 2100 requests
curl/7.68.0 - 890 requests
DigitalOcean Uptime Probe 0.22.0 - 650 requests
Mozilla/5.0 (iPhone; CPU iPhone OS 14_0) Safari/604.1 - 420 requests
PostmanRuntime/7.28.0 - 310 requests

═══════════════════════════════════════════════════════
Analysis complete!
```

---

## Log Format

Script ini menganalisis nginx access log dengan format **combined** (default nginx):

```
$remote_addr - $remote_user [$time_local] "$request" $status $body_bytes_sent "$http_referer" "$http_user_agent"
```

Contoh entry:
```
178.128.94.113 - - [17/May/2024:10:00:01 +0000] "GET /api/v1/users HTTP/1.1" 200 1234 "-" "Mozilla/5.0 Chrome/91.0"
```

| Field | Position | Contoh |
|-------|----------|--------|
| IP Address | $1 | 178.128.94.113 |
| Date/Time | $4 | [17/May/2024:10:00:01 +0000] |
| Request Method | $6 | "GET |
| Path | $7 | /api/v1/users |
| Status Code | $9 | 200 |
| User Agent | $12+ | Mozilla/5.0... |

---

## How It Works

Script menggunakan kombinasi command Linux standar:

| Command | Fungsi |
|---------|--------|
| `awk` | Extract specific fields dari log |
| `sort` | Sorting data |
| `uniq -c` | Count unique entries |
| `head -5` | Ambil top 5 results |
| `grep` | Filter specific patterns |

### Contoh: Extract Top IPs

```bash
# Step by step
awk '{print $1}' access.log    # Extract IP (field 1)
sort                            # Sort alphabetically
uniq -c                         # Count occurrences
sort -rn                        # Sort by count (descending)
head -5                         # Take top 5
```

---

## Alternative Solutions

### Using grep + sed instead of awk

```bash
# Top IPs using grep and sed
grep -oE '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' access.log | \
    sort | uniq -c | sort -rn | head -5
```

### Using cut instead of awk

```bash
# Extract status codes using cut
cut -d' ' -f9 access.log | sort | uniq -c | sort -rn | head -5
```

---

## Requirements

- Bash 4.0+
- Standard Linux utilities: `awk`, `sort`, `uniq`, `head`, `grep`

---

## License

MIT License
