### Document: How to Delete Docker Images by Tag Name and Create a Daily Cron Job

#### Objective:
This document outlines the steps to delete Docker images with specific tags and create a cron job to execute this operation daily. We will specifically delete Docker images with tags matching the pattern `:dev-` (e.g., `gcr.io/my-registry/app1:dev-v1`).

---

### Step 1: Command to Delete Docker Images by Tag Name

The following command will delete all Docker images that have a tag starting with `dev-`:

```bash
docker image rm $(docker image ls --format '{{ .Repository }}:{{ .Tag }}' | grep ':dev-')
```

#### Explanation:
- **`docker image ls --format '{{ .Repository }}:{{ .Tag }}'`**: Lists all Docker images with their repository name and tag.
- **`grep ':dev-'`**: Filters the images to only include those with tags starting with `dev-`.
- **`docker image rm $(...)`**: Removes all the images whose tags were filtered by the `grep` command.

#### Example:
Given the following images:
```
gcr.io/my-registry/app1:dev-v1
gcr.io/my-registry/app1:dev-v2
gcr.io/my-registry/app2:dev-v3
gcr.io/my-registry/app2:dev-v1
gcr.io/my-registry/app2:dev-v2
```
This command will delete all of them because their tags start with `dev-`.

---

### Step 2: Creating a Bash Script to Automate the Deletion

1. Create a script called `image-cleanup.sh` that automates the deletion process.
   
   ```bash
   #!/bin/bash
   # Script to delete Docker images with tags starting with 'dev-'
   
   docker image rm $(docker image ls --format '{{ .Repository }}:{{ .Tag }}' | grep ':dev-')
   ```

2. Make the script executable by running:

   ```bash
   chmod +x image-cleanup.sh
   ```

---

### Step 3: Creating a Cron Job to Run the Cleanup Daily

1. Open the crontab file for editing:

   ```bash
   crontab -e
   ```

2. Add the following cron job to schedule the image cleanup script to run daily at 2:00 AM. The cron job is named `image-cleanup-job`.

   ```bash
   0 2 * * * /path/to/image-cleanup.sh > /path/to/image-cleanup.log 2>&1
   ```

   #### Explanation:
   - `0 2 * * *`: This cron expression schedules the job to run daily at 2:00 AM.
   - `/path/to/image-cleanup.sh`: The path to the cleanup script.
   - `> /path/to/image-cleanup.log 2>&1`: Redirects output and error messages to a log file.

---

### Step 4: Verifying the Cron Job

To ensure the cron job is set correctly:
1. List all cron jobs using:

   ```bash
   crontab -l
   ```

2. You should see the `image-cleanup-job` scheduled in the output.

---

### Final Notes:
- The `image-cleanup.sh` script will be executed daily at 2:00 AM by the cron job, and all images with tags starting with `dev-` will be deleted.
- Check the `/path/to/image-cleanup.log` file to see the logs of the cron job's execution and any potential errors.

