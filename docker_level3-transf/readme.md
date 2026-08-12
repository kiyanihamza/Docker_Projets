# Docker Image Transfer Exercise - Stratos DC

## 📋 Exercise Overview

This exercise involves transferring a custom Docker image (`media:devops`) from **App Server 1** to **App Server 3** in the Stratos DC environment. The image needs to be archived on the source server, transferred to the destination server, and then loaded with the same name and tag.

### 🎯 Objectives

- Save a Docker image to an archive file
- Transfer the archive between two servers
- Load the Docker image on the destination server
- Ensure the image maintains its original name and tag

### 📝 Context

- **Source Server:** App Server 1 (Stratos DC)
- **Destination Server:** App Server 3 (Stratos DC)
- **Image Name:** media:devops
- **Docker Status:** Already installed on both servers (but service may need to be started)

---

## 📋 Prerequisites

- Access to App Server 1 and App Server 3 in Stratos DC
- Docker installed on both servers
- SSH/Console access to both servers
- Sufficient disk space for the image archive
- Basic Docker knowledge

---

## 🔧 Step-by-Step Instructions

### Step 1: Prepare App Server 1

#### 1.1 Connect to App Server 1
```bash
# SSH into App Server 1
ssh <user>@<app-server-1-ip>
```

#### 1.2 Verify Docker Service is Running
```bash
# Check Docker daemon status
sudo systemctl status docker

# If Docker is not running, start it
sudo systemctl start docker

# Verify Docker is active
sudo systemctl status docker
```

#### 1.3 Verify the Image Exists
```bash
# List all Docker images
sudo docker images

# Look for the 'media:devops' image in the list
# Expected output format:
# REPOSITORY   TAG      IMAGE ID       CREATED        SIZE
# media        devops   <image-id>     <date>         <size>
```

---

### Step 2: Save the Image to an Archive

#### 2.1 Create Archive Directory (Optional but Recommended)
```bash
# Create a temporary directory for the archive
mkdir -p ~/docker-archives
cd ~/docker-archives
```

#### 2.2 Save the Image as a TAR Archive
```bash
# Export the Docker image to a TAR file
sudo docker save media:devops -o media-devops.tar

# Verify the archive was created
ls -lh media-devops.tar

# Expected: A file with size corresponding to the image size (usually several MB to GB)
```

#### 2.3 Check Archive Size (Important for Transfer Planning)
```bash
# Display file size in human-readable format
du -h media-devops.tar

# Example output: 500M (depends on image size)
```

---

### Step 3: Transfer the Archive to App Server 3

#### 3.1 Using SCP (Secure Copy Protocol)
```bash
# From App Server 1, transfer the archive to App Server 3
scp media-devops.tar <user>@<app-server-3-ip>:/tmp/

# Alternative: if you need to use a specific port
scp -P <port> media-devops.tar <user>@<app-server-3-ip>:/tmp/
```

#### 3.2 Verify Transfer Completion
```bash
# Check if file was transferred successfully
# On App Server 1:
md5sum media-devops.tar

# On App Server 3 (after transfer):
md5sum /tmp/media-devops.tar

# The MD5 hashes should match to confirm successful transfer
```

**Alternative Transfer Methods:**
- **Using SSH & Pipe:** Direct streaming without saving intermediate files
- **Using rsync:** For resume capability and better progress tracking
- **Using SFTP:** For GUI-based transfer

---

### Step 4: Load the Image on App Server 3

#### 4.1 Connect to App Server 3
```bash
# SSH into App Server 3
ssh <user>@<app-server-3-ip>
```

#### 4.2 Verify Docker Service is Running
```bash
# Check Docker daemon status
sudo systemctl status docker

# If Docker is not running, start it
sudo systemctl start docker
```

#### 4.3 Load the Image from the Archive
```bash
# Import the Docker image from the TAR archive
sudo docker load -i /tmp/media-devops.tar

# Expected output:
# Loaded image: media:devops
```

#### 4.4 Verify Image was Loaded Successfully
```bash
# List all Docker images
sudo docker images

# Look for the 'media:devops' image
# Expected output format:
# REPOSITORY   TAG      IMAGE ID       CREATED        SIZE
# media        devops   <image-id>     <date>         <size>
```

---

## ✅ Verification Checklist

- [ ] Docker service is running on App Server 1
- [ ] Docker image `media:devops` exists on App Server 1
- [ ] Image has been saved to TAR archive
- [ ] Archive file size matches expectations
- [ ] Archive transferred to App Server 3
- [ ] MD5 hash matches on both servers (confirming successful transfer)
- [ ] Docker service is running on App Server 3
- [ ] Archive has been loaded on App Server 3
- [ ] Image `media:devops` appears in `docker images` on App Server 3
- [ ] Image ID matches between both servers (optional but recommended)

---

## 🐛 Troubleshooting

### Issue: Docker Service Not Running

**Problem:** `Docker daemon is not running`

**Solution:**
```bash
# Start Docker service
sudo systemctl start docker

# Enable Docker to start on boot
sudo systemctl enable docker

# Verify status
sudo systemctl status docker
```

### Issue: Permission Denied When Running Docker Commands

**Problem:** `Got permission denied while trying to connect to the Docker daemon`

**Solution:**
```bash
# Option 1: Use sudo with each command
sudo docker <command>

# Option 2: Add user to docker group (permanent)
sudo usermod -aG docker $USER
newgrp docker
```

### Issue: Transfer Failed or Incomplete

**Problem:** File transfer interrupted or checksum mismatch

**Solution:**
```bash
# Verify file integrity on source
md5sum media-devops.tar

# Verify on destination
md5sum /tmp/media-devops.tar

# If checksums don't match, re-transfer the file
# Use rsync for resume capability:
rsync -avz media-devops.tar <user>@<app-server-3-ip>:/tmp/
```

### Issue: Image Failed to Load

**Problem:** `Error loading image: unable to decode json`

**Possible Solutions:**
```bash
# Verify TAR archive integrity
tar -tzf media-devops.tar > /dev/null

# Check if archive is corrupted
file media-devops.tar

# Try loading with verbose output
sudo docker load -i /tmp/media-devops.tar -v

# If archive is corrupted, re-transfer from App Server 1
```

### Issue: Different Image IDs on Both Servers

**Note:** This is normal after loading. Image IDs should match, but if they don't:
```bash
# Inspect image details
sudo docker inspect media:devops

# Check if image functionality is correct
sudo docker run media:devops <test-command>
```

---

## 📊 Command Reference

### On App Server 1 (Source)

| Task | Command |
|------|---------|
| Check Docker status | `sudo systemctl status docker` |
| Start Docker | `sudo systemctl start docker` |
| List images | `sudo docker images` |
| Save image | `sudo docker save media:devops -o media-devops.tar` |
| Check file size | `ls -lh media-devops.tar` |
| Get checksum | `md5sum media-devops.tar` |
| Transfer to App Server 3 | `scp media-devops.tar <user>@<app-server-3-ip>:/tmp/` |

### On App Server 3 (Destination)

| Task | Command |
|------|---------|
| Check Docker status | `sudo systemctl status docker` |
| Start Docker | `sudo systemctl start docker` |
| Verify transfer | `ls -lh /tmp/media-devops.tar` |
| Check checksum | `md5sum /tmp/media-devops.tar` |
| Load image | `sudo docker load -i /tmp/media-devops.tar` |
| List images | `sudo docker images` |
| Test image | `sudo docker run media:devops <test-command>` |

---

## 💡 Best Practices

1. **Always verify checksums** before and after transfer to ensure data integrity
2. **Use compression** for large images to reduce transfer time:
   ```bash
   # On App Server 1
   sudo docker save media:devops | gzip > media-devops.tar.gz
   
   # On App Server 3
   sudo docker load < <(gunzip -c /tmp/media-devops.tar.gz)
   ```

3. **Clean up temporary files** after verification:
   ```bash
   # On App Server 3
   rm /tmp/media-devops.tar
   ```

4. **Document the transfer** with timestamps and checksums for audit purposes

5. **Test the loaded image** by running a simple container:
   ```bash
   sudo docker run --rm media:devops echo "Image loaded successfully!"
   ```

---

## 📚 Additional Resources

- [Docker Official Documentation - docker save](https://docs.docker.com/engine/reference/commandline/save/)
- [Docker Official Documentation - docker load](https://docs.docker.com/engine/reference/commandline/load/)
- [Docker Image Transfer Best Practices](https://docs.docker.com/engine/reference/commandline/export/)
- [Linux SCP Command Guide](https://linux.die.net/man/1/scp)

---

## ⏱️ Expected Duration

- **Preparation & Verification:** 5-10 minutes
- **Image Archive Creation:** 2-5 minutes (depending on image size)
- **Image Transfer:** 5-30 minutes (depending on network speed and image size)
- **Loading Image:** 2-5 minutes
- **Total:** 15-50 minutes

---

## 📝 Notes

- The image size can vary significantly. Larger images may take longer to process and transfer.
- Network bandwidth will be the limiting factor during the transfer step.
- Ensure you have write permissions to the `/tmp` directory on App Server 3.
- For production environments, consider using Docker Registry or a container image repository instead of manual transfer.

---

**Exercise Status:** Ready for execution

**Last Updated:** 2026-08-12
