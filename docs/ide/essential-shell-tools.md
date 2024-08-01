---
title: Essential shell tolls 
author: shubham
---

# Essential shell tolls

## System resource monitoring

### top
- **Description:** Provides a dynamic real-time view of the system’s resource usage, including CPU, memory, and process information.
- **Installation:** `top` is usually pre-installed on most Linux distributions.
- **Usage:** Run `top` in the terminal. Press `q` to quit. You can press `1` to view CPU usage per core.

### htop
- **Description:** An enhanced version of `top` with a more user-friendly, ncurses-based interface that includes a visual representation of CPU and memory usage.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install htop`
- **Usage:** Run `htop` in the terminal. Use the arrow keys to navigate, and press `F10` to exit.

### vmstat
- **Description:** Reports information about processes, memory, paging, block IO, traps, and CPU activity.
- **Installation:** `vmstat` is usually pre-installed.
- **Usage:** Run `vmstat 1` to get a continuous report every second. Press `Ctrl+C` to stop.

### iostat
- **Description:** Reports CPU and I/O statistics, which helps in understanding the load on the system's disks.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install sysstat`
- **Usage:** Run `iostat 1` to get continuous reports every second. Press `Ctrl+C` to stop.

### sar
- **Description:** Collects, reports, and saves system activity information. Useful for historical data analysis.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install sysstat`
- **Usage:** To collect data: `sar -u 1 3` (CPU usage every second for 3 iterations). Use `sar -o file` to save to a file and `sar -f file` to read from it.

### dstat
- **Description:** Versatile resource statistics tool that can replace vmstat, iostat, netstat, and ifstat.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install dstat`
- **Usage:** Run `dstat` to see a live report of various system metrics. Use `dstat -cnd` to get CPU, network, and disk stats.

### nload*
- **Description:** Provides a visual representation of incoming and outgoing traffic separately.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install nload`
- **Usage:** Run `nload` to see real-time network traffic. Use arrow keys to switch between network interfaces.

### glances
- **Description:** A cross-platform monitoring tool that provides a comprehensive overview of system resources, including CPU, memory, network, and disk usage.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install glances`
- **Usage:** Run `glances` in the terminal. Use `q` to quit.

### free
- **Description:** Displays information about total, used, free, shared, and buffered memory.
- **Installation:** `free` is usually pre-installed.
- **Usage:** Run `free -h` for human-readable format. Use `free -m` for memory in megabytes.

### iftop
- **Description:** Displays bandwidth usage on an interface in real-time.
- **Installation:** 
  - For Debian/Ubuntu: `sudo apt-get install iftop`
- **Usage:** Run `sudo iftop` to monitor network traffic. Press `q` to quit.

