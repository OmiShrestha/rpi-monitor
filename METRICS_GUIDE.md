# system metrics tracking guide

## what it tracks

- **cpu**: usage percentage, core count, frequency
- **memory**: total, used, available, swap
- **disk**: total space, used space, free space, i/o operations
- **network**: bytes sent/received, packets sent/received

---

## quick start

```python
from scripts.system_metrics import SystemMetrics

# create tracker
tracker = SystemMetrics("data/system_metrics.json")

# log metrics
metrics = tracker.log_metrics(format='json')

# get summary
print(tracker.get_summary())
```

---

## json vs txt

**use json when:**
- you want structured, queryable data
- analyzing trends over time
- building dashboards or graphs

**use txt when:**
- you just want quick human readability
- doing simple manual inspection

**recommendation:** json (already set up in this project)

---

## examples

### check resources
```python
from scripts.system_metrics import SystemMetrics

tracker = SystemMetrics()
metrics = tracker.collect_metrics()

if metrics['memory']['percent'] > 80:
    print("warning: high memory usage")

if metrics['disk']['percent'] > 90:
    print("warning: low disk space")
```

### access individual metrics
```python
metrics = tracker.collect_metrics()

print(f"cpu: {metrics['cpu']['usage_percent']}%")
print(f"memory: {metrics['memory']['used_gb']} gb used")
print(f"disk: {metrics['disk']['free_gb']} gb free")
```

### analyze historical data
```python
import json

with open('data/system_metrics.json', 'r') as f:
    history = json.load(f)

# average cpu usage
cpu_values = [entry['cpu']['usage_percent'] for entry in history]
avg_cpu = sum(cpu_values) / len(cpu_values)
print(f"average cpu: {avg_cpu:.2f}%")
```

---

## how it works in your project

1. runs at 9 am, 2 pm, 8 pm daily
2. collects system metrics automatically
3. saves to `data/system_metrics.json` (local only)
4. updates `data/daily_log.txt` (pushed to github)
5. displays summary in console

---

## configuration

### change schedule
edit `scripts/daily_commit.py`:
```python
schedule.every().day.at("09:00").do(make_daily_commit)
schedule.every().day.at("14:00").do(make_daily_commit)
schedule.every().day.at("20:00").do(make_daily_commit)
```

### change format
```python
metrics_tracker.log_metrics(format='txt')  # or 'json'
```

---

## testing

```bash
python scripts/test_metrics.py
```

---

## metrics reference

**cpu metrics:**
- `usage_percent` - current cpu usage
- `count` - number of cores
- `frequency_mhz` - cpu frequency (may be null)

**memory metrics:**
- `total_gb` - total ram
- `used_gb` - used ram
- `available_gb` - available ram
- `percent` - usage percentage

**disk metrics:**
- `total_gb` - total space
- `used_gb` - used space
- `free_gb` - free space
- `percent` - usage percentage

**network metrics:**
- `bytes_sent_mb` - total mb sent
- `bytes_recv_mb` - total mb received
- `packets_sent` - total packets sent
- `packets_recv` - total packets received

---

## troubleshooting

**import error?**
```bash
source .venv/bin/activate
pip install -r requirements.txt
```

**clear history?**
```bash
echo "[]" > data/system_metrics.json
```
---
