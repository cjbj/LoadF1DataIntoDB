## F1 Data Loader for database demos
This is a trivial data loader for the Oracle Database. It's currently designed to work against Oracle Database 23c but I'll ease this requirment shortly to support Oracle Database 19c as well.

It will end up creating 4 tables
```sql
CREATE TABLE IF NOT EXISTS team
(team_id    INTEGER GENERATED BY DEFAULT ON NULL AS IDENTITY,
 name       VARCHAR2(255) NOT NULL UNIQUE,
 points     INTEGER NOT NULL,
 CONSTRAINT team_pk PRIMARY KEY(team_id))

CREATE TABLE IF NOT EXISTS driver
  (driver_id  INTEGER GENERATED BY DEFAULT ON NULL AS IDENTITY,
   name       VARCHAR2(255) NOT NULL UNIQUE,
   points     INTEGER NOT NULL,
   team_id    INTEGER,
   CONSTRAINT driver_pk PRIMARY KEY(driver_id),
   CONSTRAINT driver_fk FOREIGN KEY(team_id) REFERENCES team(team_id))

CREATE TABLE IF NOT EXISTS race
  (race_id    INTEGER GENERATED BY DEFAULT ON NULL AS IDENTITY,
   name       VARCHAR2(255) NOT NULL UNIQUE,
   laps       INTEGER NOT NULL,
   race_date  DATE,
   podium     JSON,
   CONSTRAINT race_pk PRIMARY KEY(race_id))

CREATE TABLE IF NOT EXISTS DRIVER_RACE_MAP
    (DRIVER_RACE_MAP_ID NUMBER generated by default on null as identity constraint DRIVER_RACE_MAP_PK primary key,
    RACE_ID            NUMBER not null constraint DRIVER_RACE_MAP_FK1 references RACE,
    DRIVER_ID          NUMBER not null constraint DRIVER_RACE_MAP_FK2 references DRIVER,
    POSITION           NUMBER)
```
It then calls the public rest API ```http://ergast.com/api/f1``` to populate the data.

### Installation
You should have a later version of Python 3 installed (3.6 and above). You'll then need to install a couple of libraries (I recommend using virtual environments)
```python
pip install rich rich_argparse xmltodict oracledb
```

### Command Line Parameters
To run the command you need to specify the username, password and connectstring of a valid Oracle Database user/schema. You can also optionally specify a valid year for the season (it defaults to the current year).

```shell
 python LoadF1DataIntoDB.py -u f1 -p f1 -cs //localhost/f1
```
After running it you should get the following
```text
F1 Data Schema Setup
Started retrieving F1 data for 2022
Schema setup completed
Inserted constructors
Drivers inserted
Circuits inserted
Results inserted
F1 Data Loaded
Finished in 19.39 seconds
```
