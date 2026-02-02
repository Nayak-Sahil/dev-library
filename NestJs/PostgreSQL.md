# PostgreSQL — Notes & Quick Setup

## Need for a database
- Persistent storage for application data
- Separation of data from code
- Security and access control
- Scalability and backups

---

## Installation (Windows)
Two simple options are shown below: unattended installer (PowerShell) or manual download.

1) Unattended installer (PowerShell)

- Download and run the installer from EnterpriseDB (example for PostgreSQL 15):

```powershell
# Download the installer
curl -Lo postgresql-installer.exe "https://get.enterprisedb.com/postgresql/postgresql-15.4-1-windows-x64.exe"

# Run unattended install (example)
.\\postgresql-installer.exe --mode unattended --superpassword "1234" --servicename "postgresql-x64-15" --datadir "C:\Program Files\PostgreSQL\15\data"

# Add bin to PATH (system-wide)
setx /M Path "%Path%;C:\Program Files\PostgreSQL\15\bin"
```

Notes:
- If you change the download URL or the version number, update the `--servicename` and `--datadir` accordingly.
- Running the installer requires Administrator privileges.

2) Manual download (GUI)

- Download the Windows installer or .msi from EnterpriseDB: https://www.enterprisedb.com/downloads/postgres-postgresql-downloads
- Follow the GUI steps in the installer.

Reference guide (example walkthrough): https://medium.com/@Abhisek_Sahu/installing-postgresql-on-windows-for-sql-practices-671212203e9b

---

## ORMs and why to use one
Benefits:
- Developer-friendly abstractions
- Code-first (model-first) workflows
- Separation of concerns and safer migrations
- Fewer SQL mistakes and better type-safety (when supported)

Common options:
- TypeORM (`@nestjs/typeorm`) + `typeorm` + `pg`
- Prisma (`@prisma/client`) + Prisma CLI
- Sequelize (`@nestjs/sequelize`) + `pg`
- Mongoose for MongoDB (if you prefer document DB)

---

## Quick testing & connection checks
Use these quick checks after installing PostgreSQL to ensure the server is running and reachable.

1) Using psql (command-line)

```powershell
# Connect to the DB (prompts for password)
psql -U <your_username> -h localhost -p 5432 -d <your_dbname>

# Or run a single command (no interactive session)
psql "postgresql://<your_username>:<your_password>@localhost:5432/<your_dbname>" -c "SELECT version();"
```

If `psql` is not on your PATH, either add it (see Installation) or run it from "C:\Program Files\PostgreSQL\15\bin\psql.exe".


Run the script (PowerShell):

```powershell
$env:DATABASE_URL = "postgresql://<your_username>:<your_password>@localhost:5432/<Your_dbname>"
node test-conn.js
```

3) Simple SQL to create a test DB and user (run in psql as a superuser)

```sql
CREATE USER test_user WITH PASSWORD 'testpass';
CREATE DATABASE test_db OWNER test_user;
GRANT ALL PRIVILEGES ON DATABASE test_db TO test_user;
```

---

## References
- PostgreSQL: https://www.postgresql.org/
- Prisma docs (DATABASE_URL format): https://www.prisma.io/
- pg (node-postgres): https://node-postgres.com/


<!-- End of formatted PostgreSQL notes -->
