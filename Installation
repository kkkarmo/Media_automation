Step-by-step guide to install and configure the media stack using the refined Docker Compose script on your DietPi system.

Phase 1: Preparation on DietPi Host

SSH into DietPi: Connect to your DietPi device via SSH or open a local terminal.

Install Docker & Docker Compose: If you haven't already, install them using DietPi's optimized software tool:

sudo dietpi-software


Navigate to Software Optimized -> Select Docker and Docker Compose (using the spacebar) -> Select Install -> Ok. Follow the prompts.

Choose Installation Directory: Decide where you want to store the configuration files and Docker Compose file. A good place might be in your home directory or a dedicated Docker directory. Let's use ~/docker_media for this example.

mkdir ~/docker_media
cd ~/docker_media
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

(All subsequent commands assume you are in this ~/docker_media directory)

Create Directory Structure: Create the necessary folders for configuration, media data, downloads, and secrets.

mkdir -p config/{prowlarr,sonarr,radarr,transmission} media/{tv,movies} downloads secrets
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Identify User/Group IDs (PUID/PGID): Find the User ID (uid) and Group ID (gid) of the user that should own the files (usually your main DietPi user).

id
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Note down the uid and gid values (e.g., uid=1000(dietpi) gid=1000(dietpi)). The default in the script is 1000. If yours are different, you'll need to edit the docker-compose.yml file later.

Set Directory Ownership & Permissions: Ensure the user identified above owns these directories and has appropriate permissions. Replace 1000:1000 if your uid:gid is different.

sudo chown -R 1000:1000 ./config ./media ./downloads ./secrets
sudo chmod -R 775 ./config ./media ./downloads # Owner/Group RWX, Others RX
sudo chmod 700 ./secrets # Restrict secrets folder access
sudo chmod 600 ./secrets/* # Restrict secrets file access (run after creating files)
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Phase 2: Create Configuration Files

Create docker-compose.yml: Create the main compose file using a text editor like nano.

nano docker-compose.yml
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Paste the entire refined script (provided in the previous answer) into the editor.

Verify/Adjust: Double-check the PUID, PGID, and TZ (Timezone, e.g., America/New_York) values in the file and adjust them if necessary to match your system/location.

Save and Exit (Ctrl+X, then Y, then Enter).

Create Transmission Secrets Files: These files will hold the username and password for Transmission's Web UI.

Username:

nano ./secrets/transmission_user.txt
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Enter only your desired username (e.g., admin) on the first line. Save and Exit.

Password:

nano ./secrets/transmission_pass.txt
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Enter only your desired strong password on the first line. Use a strong, unique password! Save and Exit.

Secure Secrets Files: Apply restrictive permissions now that the files exist.

sudo chmod 600 ./secrets/*
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Phase 3: Start the Containers

Launch the Stack: Make sure you are still in the ~/docker_media directory (where your docker-compose.yml file is). Run:

docker-compose up -d
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

Docker will download all the specified container images (this can take several minutes depending on your internet speed).

It will then create and start the containers in the background (-d). Healthchecks will run, and dependent services will wait for others to become healthy before starting.

Check Container Status: Verify that all containers are running.

docker-compose ps
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

All services should show State as Up (it might take a minute or two for healthchecks to pass initially).

Phase 4: Application Configuration (via Web UIs)

This is the most critical phase to make everything work together.

Configure Transmission (CRUCIAL for Tor):

Access: Open a web browser on the DietPi device itself (or use SSH port forwarding) and navigate to http://localhost:9091.

Login: Use the username and password you put in the secrets files.

Proxy Settings:

Click the Spanner/Wrench icon (Settings) bottom-left.

Go to the Network tab.

Scroll to Proxy.

Check ✅ Use a proxy server.

Host: tor-proxy

Port: 9050

Type: Select SOCKS5.

Leave Authentication unchecked.

Check ✅ Use proxy for peer connections (VERY IMPORTANT!).

Privacy Settings:

Still in the Network tab, scroll to Privacy.

Disable (Uncheck): Enable DHT, Enable LPD, Enable PEX. (Essential for privacy over Tor).

Download Paths:

Go to the Downloading tab.

Download to: /downloads/complete

Check ✅ Keep incomplete torrents in and set path to /downloads/incomplete.

Save: Click Close.

Configure Prowlarr (Indexers):

Access: http://<Your-DietPi-IP>:9696 (Replace <Your-DietPi-IP> with the actual IP address of your DietPi).

Initial Setup: Create a username/password for the Prowlarr Web UI when prompted.

Add Indexers:

Go to Indexers -> Add Indexer (+).

Search for your indexer sites (you need accounts on these sites).

Select one, fill in the required details (API Keys, site login, etc., obtained from the indexer site).

Test and Save. Repeat for all your indexers.

Get API Key: Go to Settings -> General. Copy the API Key shown under the Security section - you'll need this for Sonarr/Radarr.

Configure Sonarr (TV Shows):

Access: http://<Your-DietPi-IP>:8989

Download Client:

Settings -> Download Clients -> Add (+).

Select Transmission.

Name: Transmission (or similar).

Host: transmission

Port: 9091

Username: (The username from secrets/transmission_user.txt).

Password: (The password from secrets/transmission_pass.txt).

(Optional) Category: tv (helps organize downloads if Transmission is set up for it).

(Optional) Directory: /downloads/complete/tv (if using category subfolders).

Test and Save.

Indexers (via Prowlarr):

Settings -> Indexers -> Add (+).

Select Prowlarr.

Name: Prowlarr.

Sync Level: Add and Remove Indexers.

Prowlarr Server: http://prowlarr:9696

API Key: Paste the API key you copied from Prowlarr.

Test and Save.

Root Folder (Media Library Path):

Settings -> Media Management.

Under Root Folders, click Add Root Folder (+).

Path: Enter /tv (This is the path inside the container).

Click Ok. Save Changes if prompted.

Configure Radarr (Movies):

Access: http://<Your-DietPi-IP>:7878

Configuration: Follow the same logic as Sonarr:

Add Transmission as a Download Client (host: transmission, port 9091, secret credentials, optional category movies, optional directory /downloads/complete/movies).

Add Prowlarr as an Indexer (http://prowlarr:9696, Prowlarr API Key).

Add a Root Folder with the path /movies (path inside the container).

Phase 5: Verification & Use

Verify Tor (Optional but Recommended):

Add a safe, legal test torrent (like a Linux distribution ISO) directly via the Transmission Web UI.

Check the IP address reported by peers or use a "check my torrent IP" service (search online for one). The IP shown should be a Tor exit node IP, not your home IP address. If it shows your home IP, the proxy configuration is incorrect - revisit Transmission settings immediately.

Check logs: docker-compose logs tor-proxy and docker-compose logs transmission.

Start Adding Media:

Use the Sonarr (http://<Your-DietPi-IP>:8989) and Radarr (http://<Your-DietPi-IP>:7878) Web UIs to search for and add the TV shows and movies you want.

They will search using Prowlarr, send the chosen download to Transmission, monitor Transmission (via its API), and then import the completed file from the /downloads/complete folder into your /tv or /movies library folders.

Maintenance

Updating: To update the container images to the versions specified in the docker-compose.yml (or newer versions if you change the tags):

cd ~/docker_media # Ensure you are in the right directory
docker-compose pull       # Pull newer images specified
docker-compose up -d      # Stop, remove, and recreate containers with new images
docker image prune -f     # Optional: Remove old, unused images
IGNORE_WHEN_COPYING_START
content_copy
download
Use code with caution.
Bash
IGNORE_WHEN_COPYING_END

You should now have a fully functional media automation stack running on your DietPi, with downloads routed through the Tor network for enhanced privacy. Remember the performance trade-offs of using Tor.
