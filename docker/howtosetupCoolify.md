Phase 1: Environment & Secret Management
Step 1: Define the .env: Create your environment variables in the Coolify UI. Ensure your JWT_SECRET and POSTGRES_PASSWORD match exactly what you intend to use in the SQL scripts.

Step 2: Hardcode Secrets in SQL: Since the database cannot read shell variables during initialization, ensure roles.sql and jwt.sql have your passwords and secrets hardcoded directly in the text.

Phase 2: Coolify UI Configuration (Persistent Storage)
Step 3: Create Persistent Files: Navigate to the Storage -> Files tab in the Coolify UI and add the following three files:

kong.yml → Destination: /home/kong/temp.yml

roles.sql → Destination: /docker-entrypoint-initdb.d/init-scripts/99-roles.sql

jwt.sql → Destination: /docker-entrypoint-initdb.d/init-scripts/99-jwt.sql

Phase 3: Networking & Proxy (The Missing Step)
Step 4: Set the FQDN for Kong: In the Kong service settings within Coolify, set the FQDN to https://userdbagrimaze.vlifecycle.com.

Step 5: Define the Destination Port: In the same settings, set the Destination Port to 8000. This tells the Coolify Proxy (Traefik) to send web traffic to Kong's internal port.

Phase 4: Deployment & Initialization
Step 6: Update Docker Compose: Paste your finalized docker-compose.yml (the one with host ports removed) into the Compose tab.

Step 7: Wipe Old Data (Critical): If the database container has already run once, it created a /var/lib/postgresql/data folder. Postgres only runs initialization scripts when that folder is empty. If you are doing a fresh setup, you must delete that folder's contents on the host or in the Volumes tab before deploying to ensure your roles.sql and jwt.sql are processed.

Step 8: Deploy: Hit the Deploy button in Coolify.

Phase 5: Verification
Step 9: Monitor Logs: Once deployed, check the logs for supabase-db. You are looking for a message saying: /docker-entrypoint-initdb.d/init-scripts/99-roles.sql: running....

Step 10: Browser Test: Visit your domain. You should be prompted for Basic Auth (use the credentials from your logs) to access the Supabase Studio.
