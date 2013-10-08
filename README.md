dasset
======

```
usage: dasset [-h] [--json] [--csv] [--txt] [--update]
              [--updateall {eol,non-eol,all}] [--diffupdate] [--skip]
              [--remove] [--eol {false,true}] [--exp EXP] [--printall]
              [--hostname HOSTNAME] [--model MODEL]
              [stags [stags ...]]

Lightweight Asset Management for Dell Hardware

positional arguments:
  stags

optional arguments:
  -h, --help            show this help message and exit
  --json                Output results as JSON.
  --csv                 Output results as CSV.
  --txt                 Output results as TXT (default).
  --update              Update DB for given STAGs with results from Dell.
  --updateall {eol,non-eol,all}
                        Refresh all, eol or non-eol entries in DB from Dell.
  --diffupdate          Update DB with results from Dell if there's no
                        existing entry for given STAGs.
  --skip                Skip local DB check and always poll Dell for data.
  --remove              Remove STAG from local DB.
  --eol {false,true}    Set given EOL state for STAG.
  --exp EXP             Show hardware which has <= N remaining days of
                        warranty.
  --printall            Print everything we have in the DB.
  --hostname HOSTNAME   Set hostname for the STAG entry
  --model MODEL         Set model for the STAG entry.
```

### Requirements
------
*    This project requires the python [SUDS](https://pypi.python.org/pypi/suds) SOAP module, on most systems, you can run:
```
    $ pip install suds
```
*    For debian/ubuntu, you can install it from the mirror:
```
    $ sudo apt-get install python-suds
```

### Some Examples
------

* Query from dell site skipping DB check.

```
$ ./dasset --skip H8B544J H8B591J
Service Tag:      H8B544J
 Model:           Latitude E6400
 Shipped:         22.12.2008
 End Warranty:    21.12.2011
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
Service Tag:      H8B591J
 Model:           Inspiron 5160
 Shipped:         03.10.2004
 End Warranty:    02.10.2005
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
```

* Query from dell site and update results to db.

```
$ ./dasset --update H8B544J H8B591J
[H8B544J] fetching updated data from Dell.
[H8B591J] fetching updated data from Dell.
Service Tag:      H8B544J
 Model:           Latitude E6400
 Shipped:         22.12.2008
 End Warranty:    21.12.2011
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
Service Tag:      H8B591J
 Model:           Inspiron 5160
 Shipped:         03.10.2004
 End Warranty:    02.10.2005
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
2 entries updated.
```

* Query expiring hardware

```
$ ./dasset --exp 365
Service Tag:      H8B544J
 Model:           Latitude E6400
 Shipped:         22.12.2008
 End Warranty:    21.12.2011
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
Service Tag:      H8B591J
 Model:           Inspiron 5160
 Shipped:         03.10.2004
 End Warranty:    02.10.2005
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
```

* Set EOL for entry as 'false' so it doesn't show in expiring hardware list.

```
$ ./dasset --eol false H8B544J
[H8B544J] EOL status set in DB as "False"!
```

* And run the check again.

```
$ ./dasset --exp 365
Service Tag:      H8B591J
 Model:           Inspiron 5160
 Shipped:         03.10.2004
 End Warranty:    02.10.2005
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
```

* Set hostname for server.

```
$ ./dasset --hostname foobar.nyaa.com H8B591J
[H8B591J] hostname set as [foobar.nyaa.com]
$ ./dasset H8B591J
Service Tag:      H8B591J
 Model:           Inspiron 5160
 Shipped:         03.10.2004
 End Warranty:    02.10.2005
 Days Remaining:  0
 EOL Status:      None
 Hostname:        foobar.nyaa.com
```

* Print all the entries from DB as JSON

```
$ ./dasset --printall --json
{
    "H8B544J": {
        "days_remaining": 0,
        "endwarranty": "21.12.2011",
        "eol_status": "True",
        "hostname": "None",
        "model": "Latitude E6400",
        "shipped": "22.12.2008"
    },
    "H8B591J": {
        "days_remaining": 0,
        "endwarranty": "02.10.2005",
        "eol_status": "None",
        "hostname": "foobar.nyaa.com",
        "model": "Inspiron 5160",
        "shipped": "03.10.2004"
    }
}
```

* And as CSV

```
$ ./dasset --printall --csv
servicetag,model,shipped,endwarranty,daysremaining,eolstatus,hostname
H8B544J,Latitude E6400,22.12.2008,21.12.2011,0,True,None
H8B591J,Inspiron 5160,03.10.2004,02.10.2005,0,None,foobar.nyaa.com
```

* Update missing entries to DB

```
$ ./dasset --diffupdate H8B544J H8B291J
[H8B291J] doesn't exist in DB, fetching from Dell.
Service Tag:      H8B291J
 Model:           OptiPlex GX270
 Shipped:         30.09.2004
 End Warranty:    29.09.2007
 Days Remaining:  0
 EOL Status:      None
 Hostname:        None
1 entry updated.
```

* Update all EOL entries in the DB

```
$ ./dasset --updateall eol
[H8B544J] Fetching updated data from Dell.
Service Tag:      H8B544J
 Model:           Latitude E6400
 Shipped:         22.12.2008
 End Warranty:    21.12.2011
 Days Remaining:  0
 EOL Status:      True
 Hostname:        None
1 entry updated.
```
