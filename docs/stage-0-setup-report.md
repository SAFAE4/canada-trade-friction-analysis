# Stage 0 Report: Environment Setup and Connectivity Testing

**Project:** North American Trade Friction & Sector Exposure Analysis (EN/FR)
**Stage:** 0 of 6, Setup
**Tools:** Python, MySQL, Power BI Desktop, VS Code, Git/GitHub

---

## 1. Overview

Before touching any real data, I built the project environment and tested that every tool in the pipeline can communicate with the next one. This report records what was set up, the problems I hit, how I diagnosed them, and what I learned.

**Pipeline verified in this stage:**

```
Python (pandas, SQLAlchemy)  -->  MySQL (trade_friction_db)  -->  Power BI Desktop
        write data                      store data                    read data
```

**Outcome:** all three connections work. A test table was written from Python, stored in MySQL, and read in Power BI.

---

## 2. What Was Set Up

| Component | Purpose | Status |
|---|---|---|
| GitHub repository | Version control and portfolio hosting | Done |
| Folder structure | `data/`, `scripts/`, `sql/`, `dashboard/`, `notebooks/`, `docs/` | Done |
| Python virtual environment | Isolated dependencies (pandas, SQLAlchemy, PyMySQL, python-dotenv, ipykernel) | Done |
| VS Code interpreter and notebook kernel | Run notebooks inside the project's environment | Done |
| MySQL database `trade_friction_db` | Storage for the star schema | Done |
| MySQL user `trade_user` | Least-privilege access limited to this one database | Done |
| `.env` file | Keeps credentials out of the code and out of Git | Done |
| Python to MySQL test | Write a table, read it back | **Passed** |
| Power BI to MySQL test | Connect and see the test table | **Passed** |

---

## 3. Verification Results

**Python to MySQL.** A small DataFrame was written to `trade_friction_db.setup_test` with `DataFrame.to_sql()` and read back with `pd.read_sql()`.

| province | test_value |
|---|---|
| Quebec | 1 |
| Ontario | 2 |

The rows written matched the rows read back, and the same table was visible in MySQL Workbench.

**Power BI to MySQL.** Power BI Desktop connected to `localhost` / `trade_friction_db` as `trade_user` and listed `setup_test` in the Navigator.

---

## 4. Problems Solved

### 4.1 Main issue: MySQL error 1044 (access denied to database)

**Symptom**

```
OperationalError: (1044, "Access denied for user 'trade_user'@'localhost'
to database 'trade_friction_db'")
```

**How I diagnosed it**

| Step | What I checked | What it told me |
|---|---|---|
| 1 | Read the error code | 1044 means login worked but the account lacks rights on the database. A wrong password would give 1045. |
| 2 | Confirmed the notebook used the project's virtual environment and that `.env` values loaded | The notebook setup was not the cause. |
| 3 | Queried `mysql.user` | `trade_user@localhost` existed and used the default `caching_sha2_password` plugin, so the account and host were fine. |
| 4 | Tried `SHOW GRANTS` for a `127.0.0.1` account | Error 1141 (no such grant): that account never existed and was not needed, since Python connects via `localhost`. |
| 5 | Compared everything | The account, database and credentials were all valid. The only failing piece was the privilege assignment made through SQL statements. |

The exact reason the SQL `GRANT` statements did not take effect was not conclusively identified. A likely contributor is that Workbench's `Ctrl+Enter` runs only the statement under the cursor, so multi-line scripts may have run only in part.

**Resolution.** I assigned the privileges through Workbench's graphical interface:

1. Connected to the server as `root`.
2. Opened **Server > Users and Privileges**.
3. Selected `trade_user`, then the **Schema Privileges** tab.
4. Clicked **Add Entry...**, chose the schema `trade_friction_db`, and ticked all privileges.
5. Clicked **Apply**.

After restarting the notebook kernel, the write and read-back test succeeded.

### 4.2 Power BI: "couldn't authenticate"

**Likely cause.** Power BI's MySQL connector can have trouble with MySQL's default `caching_sha2_password` login method.

**Resolution**

1. Cleared saved credentials in Power BI (**File > Options and settings > Data source settings > Clear Permissions**).
2. Switched the user to the older login method in Workbench as `root`:
   ```sql
   ALTER USER 'trade_user'@'localhost' IDENTIFIED WITH mysql_native_password BY '<password>';
   FLUSH PRIVILEGES;
   ```
3. Reconnected using the **Database** credentials tab.

The connection then worked, and Python continued to work because PyMySQL supports both methods.

### 4.3 Smaller issues

| Issue | Cause | Fix |
|---|---|---|
| `mkdir data data\raw ...` failed | The VS Code terminal uses PowerShell, which does not accept space-separated paths for `mkdir` | Use commas: `mkdir data, data\raw, data\clean` |
| `venv\Scripts\Activate.ps1` raised "module could not be loaded" | PowerShell read the bare path as a module name | Prefix with `.\` |
| `pip` not recognized | Virtual environment was not active | Activate the venv, or run `python -m pip install ...` |
| `venv` missing from the notebook kernel list | `ipykernel` was not installed in the venv | Install it, then pick the venv under **Select Kernel > Python Environments** |
| VS Code notice about `python.terminal.useEnvFile` | Informational only, since `load_dotenv()` already reads `.env` | Ignored |

---

## 5. Lessons Learned

1. **Read the error code before changing anything.** 1044, 1045 and 2003 point to three different problems, and identifying the right one avoided wasted effort.
2. **Verify, don't assume.** A `GRANT` that appears to run is not proof the privilege exists. Confirm with `SHOW GRANTS` or by testing as the target user.
3. **Isolate the layers.** Testing the database separately from Python, and Python separately from Power BI, shows which layer is failing.
4. **Know your shell.** PowerShell, Command Prompt and Bash use different syntax, and many "broken tool" problems are really syntax problems.
5. **Restart the kernel after configuration changes.** A running kernel keeps old values and old connection state.
6. **Use a dedicated least-privilege user.** Scripts and Power BI connect as `trade_user`, limited to one database, instead of `root`.
7. **Keep secrets out of Git.** Credentials live in `.env` (ignored by Git). Only `.env.example`, with blank values, is committed.

---

## 6. Final Checklist

- [x] GitHub repository created and cloned
- [x] Folder structure created
- [x] Virtual environment created and selected in VS Code
- [x] MySQL database and user created, privileges granted
- [x] Python to MySQL write and read test passed
- [x] Power BI to MySQL connection passed
- [x] `.env` excluded from Git, `.env.example` created
- [ ] Stage 0 commit pushed to GitHub

---

## 7. Next Step

**Stage 1: Data discovery.** Identify the Statistics Canada trade table, download the English and French versions, and profile the data in pandas (date range, units, dimensions, suppressed values, and how the two language files align).
