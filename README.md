# Assignment 2 — Oracle Pluggable Database (PDB) Administration

This assignment demonstrates the basic lifecycle of an Oracle pluggable database (PDB). It shows how to create a PDB, open it, connect to it, test its status, and finally remove a temporary PDB after verification.

> This README uses all six relevant screenshots from the assignment. They are included in the same order as the Oracle PDB workflow shown in the task sequence.

---

## Objective

The purpose of the task is to show how to:

- create a new PDB,
- open it and confirm its state,
- connect to it using a service name,
- verify container restrictions,
- create a second PDB for testing,
- check PDB status with `SHOW PDBS`,
- remove the temporary PDB,
- and review the database status in Oracle Enterprise Manager.

The assignment is divided into the following tasks, matching the six images in the folder:

- Task 1: Create and open the PDB
- Task 2: Create a connection to the PDB
- Task 3: Attempt root-container change and create another PDB
- Task 4: Verify the PDB list
- Task 5: Drop the temporary PDB
- Task 6: Review Oracle Enterprise Manager status

---

## Relevant files in the folder

- `screenshots/1 pdb creation.png` — creation of the main PDB
- `screenshots/2 user conn.png` — database connection settings for the PDB
- `screenshots/3 create another pdb.png` — creation of a second PDB for testing
- `screenshots/4 pdb verification.png` — validation of the PDB list after creation
- `screenshots/5 pdb deletion.png` — removal of the temporary PDB
- `screenshots/6 OEM.png` — Oracle Enterprise Manager view of the database state
- `bnb.pdf` — assignment/reference document

The README below includes all six images in the assignment sequence.

---

## Task 1: Create and open the PDB

The first screenshot shows the SQL script used to create a new pluggable database and open it immediately:

```sql
CREATE PLUGGABLE DATABASE BL_PDB_29359
ADMIN USER blaise_plsqlauca_29359 IDENTIFIED BY 13045
FILE_NAME_CONVERT = ('C:\Oracle21c\oradata\ORCL\pdbseed', 'C:\Oracle21c\oradata\ORCL\BL_PDB_29359');
SHOW PDBS;
ALTER PLUGGABLE DATABASE BL_PDB_29359 OPEN;
SHOW PDBS;
```

The output confirms that the new PDB was created and appears with `READ WRITE NO` status, meaning it was opened successfully.

![Task 1: PDB creation and opening](screenshots/1%20pdb%20creation.png)

---

## Task 2: Create a database connection

This screenshot shows the Oracle connection setup for the new database:

- Connection name: `BNB`
- Username: `blaise_plsqlauca_29359`
- Host: `localhost`
- Port: `1521`
- Service name: `BL_PDB_29359`

The connection is created so the user can connect to the specific pluggable database instead of the CDB root.

![Task 2: database connection](screenshots/2%20user%20conn.png)

---

## Task 3: Attempt root-container change and create another PDB

This screenshot shows an important Oracle restriction. The command:

```sql
ALTER SESSION SET CONTAINER = CDB$ROOT;
```

fails with `ORA-65040` because the operation is not allowed inside a pluggable database. The same screenshot then creates a second PDB for testing and removal:

```sql
CREATE PLUGGABLE DATABASE BL_TO_DELETE_PDB_29359
ADMIN USER blaise_plsqlauca_29359 IDENTIFIED BY 13045
FILE_NAME_CONVERT = ('C:\Oracle21c\oradata\ORCL\pdbseed', 'C:\Oracle21c\oradata\ORCL\BL_TO_DELETE_PDB_29359');
```

This demonstrates that root-level operations must be done from the root or application root container.

![Task 3: root restriction and second PDB](screenshots/3%20create%20another%20pdb.png)

---

## Task 4: Verify the PDB list

The next screenshot runs `SHOW PDBS;` after the second PDB creation. The output shows the PDB list, including the newly created temporary database in `MOUNTED` mode.

This confirms the PDB exists and is present inside the container database.

![Task 4: PDB verification](screenshots/4%20pdb%20verification.png)

---

## Task 5: Drop the temporary PDB

The next command removes the temporary PDB:

```sql
DROP PLUGGABLE DATABASE BL_TO_DELETE_PDB_29359 INCLUDING DATAFILES;
SHOW PDBS;
```

The output confirms the database was dropped successfully and no longer appears in the list.

![Task 5: PDB deletion](screenshots/5%20pdb%20deletion.png)

---

## Task 6: Review Oracle Enterprise Manager status

The final image shows the Oracle Enterprise Manager dashboard for the database. It lists active sessions, SQL activity, and no incidents in the last 24 hours.

This confirms the database system is active and healthy after the PDB operations.

![Task 6: OEM dashboard](screenshots/6%20OEM.png)

---

## Learning outcome

This assignment teaches the core Oracle multitenant workflow:

- create a PDB,
- open it,
- connect to it,
- understand the role of the root container,
- validate status using `SHOW PDBS`,
- and remove a temporary PDB after testing.

The main lesson is that Oracle administration tasks must be performed in the correct container. The `SHOW PDBS` command is essential for checking the current status of each PDB and confirming whether the database is mounted, open, or removed.
