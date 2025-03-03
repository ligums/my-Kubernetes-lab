# Kubernetes CronJob and ConfigMap Explanation

## cronjob.yaml
This file defines a **Kubernetes CronJob** that schedules a job to run every **5 minutes**.

### Key Components:
- **apiVersion**: `batch/v1` (Defines it as a CronJob)
- **kind**: `CronJob`
- **metadata**: Sets the name to `my-cronjob`.
- **spec**:
  - **schedule**: `*/5 * * * *` (Runs every 5 minutes)
  - **jobTemplate**:
    - **template**:
      - **containers**:
        - Name: `my-container`
        - Image: `ubuntu:latest`
        - Command: `bash script.sh`
      - **volumeMounts**:
        - Mounts a **ConfigMap** (`my-script-config`) to `/script/script.sh`.
        - Mounts an **NFS volume** at `/mnt/nfs`.
      - **restartPolicy**: `OnFailure` (Restarts if it fails)
  - **volumes**:
    - **script-volume**: Uses the `my-script-config` ConfigMap.
    - **nfs-mount**: Connects to an **NFS share** (`10.0.1.11:/mnt/storage/k-data`).

---

## configmap.yaml
This file defines a **ConfigMap** named `my-script-config`, which stores a script (`script.sh`).

### script.sh Functionality:
- Uses **Bash** to create timestamped files in the **NFS mount**.
- Steps:
  1. Get the current date/time (`YYYY-MM-DD_HH-MM`).
  2. Create an empty file with the timestamp (`/mnt/nfs/YYYY-MM-DD_HH-MM.txt`).

```bash
#!/bin/bash
DATE=$(date +"%Y-%m-%d_%H-%M")
touch "/mnt/nfs/${DATE}.txt"
```

---

## Summary
- **The CronJob runs every 5 minutes**, executing the script.
- **The script creates a timestamped file** in `/mnt/nfs`.
- **Uses an NFS mount and a ConfigMap** to store the script and persist data.

