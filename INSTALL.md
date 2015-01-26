#### CxTracker INSTALL

# Basic installation 

libpcap 0.8 or higher with headers (dev package) is required.

```shell
$ wget https://github.com/shadowbq/cxtracker/archive/master.zip
$ unzip master.zip
$ cd cxtracker
$ cd src && make && make test
```

## Install w/Contribs from git

### On a Ubuntu system:

```shell
$ sudo apt-get install git-core libpcap0.8-dev
$ git clone git://github.com/shadowbq/cxtracker.git
$ cd cxtracker
$ cd src &&  make && make test
$ sudo bin/cxtracker -h
```

### Copying the bin

You can copy cxtracker to `/usr/local/sbin/` or `/opt/sbin/`

```shell
$ sudo mkdir -p /opt/sbin/
$ sudo cp bin/cxtracker /opt/sbin/
```

## Contrib

To use the perl Contrib files you may need:

```shell
$ sudo apt-get install libnet-pcap-perl libgetopt-long-descriptive-perl libdatetime-perl ```

### init.d Examples

There are mulitple examples of running cxtracker as a daemon process, with correct init.d files located in `etc\init.d\..`

### Integrating with SGUIL:

`$ /opt/sbin/cxtracker -i eth1 -d /nsm_data/sensor1/session/ -D`

IF YOU USE cxtracker WITH SGUIL - you can stop here!
Just make sure that the sguil sancp agent is picking up session files from the right location.

### Installing with a Database

Maybe for your own project or for openfpc etc.

`contrib/cxtracker2db.pl` - for storing session data to a DB

Edit `cxtracker2db.pl` to match db user and password if needed.

Some files and dirs that should be writable:

```shell
$ cp bin/cxtracker2db.pl /opt/sbin/
$ mkdir /var/run/
$ touch /var/log/cxtracker2db.log
```

Prepare the mysql database

```sql
GRANT USAGE ON *.* TO 'cxtracker'@'localhost' identified by 'cxtracker';
GRANT ALL ON cxtracker.* TO 'cxtracker'@'localhost' IDENTIFIED BY 'cxtracker';
FLUSH PRIVILEGES;

CREATE DATABASE cxtracker;
\u cxtracker

```

You need to add two function to mysql to handle IPv6 
INET_ATON6 and INET_NTOA6:


```sql
DELIMITER //
CREATE FUNCTION INET_ATON6(n CHAR(39))
RETURNS DECIMAL(39) UNSIGNED
DETERMINISTIC
BEGIN
    RETURN CAST(CONV(SUBSTRING(n FROM  1 FOR 4), 16, 10) AS DECIMAL(39))
                       * 5192296858534827628530496329220096 -- 65536 ^ 7
         + CAST(CONV(SUBSTRING(n FROM  6 FOR 4), 16, 10) AS DECIMAL(39))
                       *      79228162514264337593543950336 -- 65536 ^ 6
         + CAST(CONV(SUBSTRING(n FROM 11 FOR 4), 16, 10) AS DECIMAL(39))
                       *          1208925819614629174706176 -- 65536 ^ 5
         + CAST(CONV(SUBSTRING(n FROM 16 FOR 4), 16, 10) AS DECIMAL(39))
                       *               18446744073709551616 -- 65536 ^ 4
         + CAST(CONV(SUBSTRING(n FROM 21 FOR 4), 16, 10) AS DECIMAL(39))
                       *                    281474976710656 -- 65536 ^ 3
         + CAST(CONV(SUBSTRING(n FROM 26 FOR 4), 16, 10) AS DECIMAL(39))
                       *                         4294967296 -- 65536 ^ 2
         + CAST(CONV(SUBSTRING(n FROM 31 FOR 4), 16, 10) AS DECIMAL(39))
                       *                              65536 -- 65536 ^ 1
         + CAST(CONV(SUBSTRING(n FROM 36 FOR 4), 16, 10) AS DECIMAL(39))
         ;
END;
//

CREATE FUNCTION INET_NTOA6(n DECIMAL(39) UNSIGNED)
RETURNS CHAR(39)
DETERMINISTIC
BEGIN
  DECLARE a CHAR(39)             DEFAULT '';
  DECLARE i INT                  DEFAULT 7;
  DECLARE q DECIMAL(39) UNSIGNED DEFAULT 0;
  DECLARE r INT                  DEFAULT 0;
  WHILE i DO
    -- DIV doesn't work with numbers > bigint
    SET q := FLOOR(n / 65536);
    SET r := n MOD 65536;
    SET n := q;
    SET a := CONCAT_WS(':', LPAD(CONV(r, 10, 16), 4, '0'), a);

    SET i := i - 1;
  END WHILE;

  SET a := TRIM(TRAILING ':' FROM CONCAT_WS(':',
                                            LPAD(CONV(n, 10, 16), 4, '0'),
                                            a));

  RETURN a;

END;
//
DELIMITER ;
-----8<-----
```

Then:
```shell
$ /opt/sbin/cxtracker2db.pl --hostname sensor1 --dir /nsm_data/sensor1/session/ --daemon
```