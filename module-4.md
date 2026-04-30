# Module 4 Process Management — Processes & Scheduling Tasks
**Environment:** Oracle Linux 9 on VirtualBox
---

## Process Management

A process is any running program on a Linux system. Every command you run creates a process.

### Viewing Processes

ps — Shows processes in current terminal  
ps aux — Shows all processes with detailed info (user, PID, CPU%, MEM%, command)  
ps -l — Shows processes with nice (priority) values  
top — Real-time interactive process viewer (press q to quit)

### Background & Foreground Jobs

& — Run a command in background. Example: sleep 300 &  
jobs — List all background and suspended jobs  
fg — Bring a background job to foreground  
bg — Resume a suspended job in background  
Ctrl+Z — Suspend the current foreground process  
Ctrl+C — Terminate the current foreground process

Examples:
```
sleep 300 &          # Run in background
jobs                 # See running jobs
fg                   # Bring to foreground
Ctrl+Z               # Suspend
bg                   # Resume in background
```
### Killing Processes

kill PID — Terminate process by process ID  
kill %1 — Terminate by job number  
kill -9 PID — Force kill (SIGKILL)  
pkill name — Kill by process name  
killall name — Kill all processes with that name

Examples:
```
sleep 500 &
kill %1              # Kill job 1
ps aux | grep sleep  # Find PID
kill 2814            # Kill by PID
```
### Process Priority (Nice Values)

Nice value determines process priority. Higher nice = lower priority.

Range: -20 (highest) to +19 (lowest)  
Default: 0  
Only root can set negative (higher priority) values

Commands:

nice -n value command — Start process with specific priority  
renice -n value -p PID — Change priority of running process  
sudo renice -n -5 PID — Increase priority (needs sudo)

Examples:
```
nice -n 5 sleep 300 &        # Lower priority (NI=5)
sudo nice -n -5 sleep 300 &  # Higher priority (NI=-5)
sudo renice -n 10 -p 3177    # Change running process priority

Check priority with: ps -l (look at NI column)
```
---

## Scheduling Tasks

Linux has two main tools for scheduling: cron (recurring) and at (one-time).

### Cron — Recurring Tasks

crontab -e — Edit cron jobs for current user  
crontab -l — List cron jobs  
crontab -r — Remove all cron jobs

### Cron Time Syntax (5 fields)
```
Field          Values
Minute         0-59
Hour           0-23
Day of month   1-31
Month          1-12
Day of week    0-7 (0 and 7 = Sunday)
```

Examples:
```
* * * * * command                    # Every minute
*/5 * * * * command                  # Every 5 minutes
0 * * * * command                    # Every hour
0 0 * * * command                    # Every day at midnight
30 2 * * * command                   # 2:30 AM daily
0 9 * * 1 command                    # Monday at 9 AM
0 0 1 * * command                    # 1st day of every month
```
### Cron Practice

Create a cron job that runs every minute:

crontab -e
- Add line: * * * * * echo "Test at $(date)" >> /tmp/cron.log

Save and exit

Verify: crontab -l

Check after 2-3 minutes: cat /tmp/cron.log

### At — One-Time Tasks

at now + 2 minutes — Schedule task 2 minutes from now  
at 15:30 — Schedule for specific time  
at 5:00 PM tomorrow — Schedule for specific time  
atq — List pending at jobs  
atrm job_number — Remove an at job

To schedule: at now + 2 minutes, then type the command, then Ctrl+D

Example:
```
at now + 3 minutes
echo "One-time task" >> /tmp/at-test.log
Ctrl+D

Verify: atq
Check after time passes: cat /tmp/at-test.log
```
---

## Commands Summary
```
ps, ps aux, ps -l — View processes
top — Realtime process monitor
& — Run in background
jobs — List background jobs
fg — Foreground
bg — Background
Ctrl+Z — Suspend
kill, pkill, killall — Terminate processes
nice, renice — Manage priorities
crontab -e, -l, -r — Schedule recurring tasks
at, atq, atrm — Schedule one-time tasks
```
