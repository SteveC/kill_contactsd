# kill_contactsd

Kill MacOS contactsd when it's using insane amounts of CPU.

`contactsd` will regularly use large amounts of CPU and battery doing something weird. This is apparently related to if you have Google accounts set to sync on your Mac. This has apparently been going on for years: https://discussions.apple.com/thread/254322136?sortBy=rank

This script will kill `contactsd` if it's using a lot of CPU while while on battery power.

## Description

This script monitors the CPU usage of the `contactsd` process on macOS. It's designed to run primarily when the system is operating on battery power. If `contactsd`'s CPU usage exceeds a predefined threshold (default 50%), the script will terminate the process.

The script can be run in two modes:

1.  **Continuous Monitoring:** Runs indefinitely, checking the `contactsd` process at regular intervals (default 60 seconds) while on battery power.
2.  **Run Once:** Performs a single check of the `contactsd` process (if on battery) and then exits. This is useful for scheduled tasks (e.g., cron jobs).

## Installation

1.  Make the script executable:
    ```bash
    chmod +x kill_contactsd.sh
    ```
2.  Move the script to a directory in your PATH. A common choice is `~/bin`:
    ```bash
    mkdir -p ~/bin
    mv kill_contactsd.sh ~/bin/
    ```

## Adding ~/bin to your Zsh PATH

If `~/bin` is not already in your PATH, you need to add it. Edit your Zsh configuration file (`~/.zshrc`) and add the following line:

```bash
export PATH="$HOME/bin:$PATH"
```

Save the file and reload your Zsh configuration:

```bash
source ~/.zshrc
```

You should now be able to run the script directly by typing `kill_contactsd.sh` in your terminal.

## Usage

*   **Continuous Monitoring (only checks when on battery):**
    ```bash
    kill_contactsd.sh
    ```
*   **Single Run (only checks when on battery):**
    ```bash
    kill_contactsd.sh --run-once
    ```

## Scheduling with Cron (Run Once per Minute)

To run the script automatically every minute using cron, you can add the following line to your crontab.

1.  Open your crontab for editing:
    ```bash
    crontab -e
    ```
2.  Add the following line. Make sure to replace `/Users/your_username/bin/kill_contactsd.sh` with the actual path to the script on your system:
    ```cron
    * * * * * /Users/your_username/bin/kill_contactsd.sh --run-once
    ```
    *(Note: You might need to use the full path to `kill_contactsd.sh` depending on how cron's environment is configured)*

3.  Save and close the editor. Cron will now execute the script once every minute using the `--run-once` flag.

## Sample Output

Here is an example of the script running in continuous monitoring mode:

```bash
% ./kill_contactsd.sh  
Starting contactsd monitor. Checking every 60 seconds while on battery.
Fri Apr 11 11:04:57 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:04:57 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:05:57 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:05:57 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:06:58 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:06:58 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:07:58 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:07:58 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:08:58 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:08:58 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:09:58 MDT 2025: Process contactsd (PID: 742) is using 0.0% CPU; within limits.
Fri Apr 11 11:09:58 MDT 2025: Process contactsd (PID: 851) is using 0.0% CPU; within limits.
Fri Apr 11 11:10:58 MDT 2025: Process contactsd (PID: 742) is using 79.9% CPU. Killing it...
Fri Apr 11 11:10:58 MDT 2025: Process contactsd (PID: 851) is using 7.3% CPU; within limits.
```

**Explanation:**

*   The script starts in monitoring mode, checking every 60 seconds.
*   It identifies two `contactsd` processes (PIDs 742 and 851).
*   For several minutes, both processes are using minimal CPU, remaining within the limits.
*   At 11:10:58, process 742's CPU usage jumps to 79.9%, exceeding the 50% threshold.
*   The script kills process 742.
*   Process 851 remains running as its CPU usage (7.3%) is within limits.
