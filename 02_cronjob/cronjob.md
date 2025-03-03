# Kubernetes CronJob: Timestamp File Creation

This Kubernetes **CronJob** configures a job to create timestamped files in an NFS-mounted directory every 5 minutes.

- **CronJob**: Schedules a job to run at a specified interval, defined by the `schedule` parameter.
- **JobTemplate**: Defines the job’s specification, including the container and volumes.
- **Volumes**: The job uses a **ConfigMap** for the script and an **NFS volume** for persistent storage.

The **CronJob** runs a **Bash script** that creates a timestamped file in the mounted NFS directory (`/mnt/nfs`).

### Key Components:
1. **apiVersion**: `batch/v1` (indicates the resource type is CronJob)
2. **kind**: `CronJob`
3. **metadata**:
   - **name**: `my-cronjob`
4. **spec**:
   - **schedule**: `*/5 * * * *` (runs every 5 minutes)
   - **jobTemplate**:
     - **template**:
       - **containers**:
         - **name**: `my-container`
         - **image**: `ubuntu:latest`
         - **command**: `bash script.sh`
       - **volumeMounts**:
         - **ConfigMap Volume**: Mounts `my-script-config` to `/script/script.sh`.
         - **NFS Volume**: Mounts an NFS volume at `/mnt/nfs`.
       - **restartPolicy**: `OnFailure` (restarts only if it fails)
   - **volumes**:
     - **script-volume**: ConfigMap (`my-script-config`)
     - **nfs-mount**: NFS server (`10.0.1.11:/mnt/storage/k-data`)

### ConfigMap:
The `ConfigMap` stores the `script.sh` file that is executed by the CronJob. The script generates timestamped files at a specified path in the NFS volume.

**script.sh**:

```bash
#!/bin/bash
DATE=$(date +"%Y-%m-%d_%H-%M")
touch "/mnt/nfs/${DATE}.txt"
