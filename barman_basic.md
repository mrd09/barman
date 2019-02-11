# Intro
- __Barman (Backup and Recovery Manager)__ is an open-source administration __tool for disaster recovery of PostgreSQL servers written in Python__. 
- It allows your organisation to __perform remote backups of multiple servers in business critical environments to reduce risk and help DBAs during the recovery phase__.
- In a perfect world, there would be no need for a backup. However, it is important, especially in business environments, to be __prepared for when the “unexpected”__ happens. In a __database scenario, the unexpected could__ take any of the following forms:
```
  - data corruption
  - system failure (including hardware failure)
  - human error
  - natural disaster
```
In such cases, any ICT manager or DBA should be able to fix the incident and recover the database in the shortest time possible. We normally refer to this discipline as disaster recovery, and more broadly business continuity.

Within business continuity, it is important to familiarise with two fundamental metrics, as defined by Wikipedia:
```
    Recovery Point Objective (RPO): “maximum targeted period in which data might be lost from an IT service due to a major incident”
    Recovery Time Objective (RTO): “the targeted duration of time and a service level within which a business process must be restored after a disaster (or disruption) in order to avoid unacceptable consequences associated with a break in business continuity”
```

- In a few words, __RPO represents the maximum amount of data you can afford to lose__, while __RTO represents the maximum down-time you can afford for your service__.
- Understandably, __we all want RPO=0 (“zero data loss”) and RTO=0 (zero down-time, utopia)__
- Fortunately, with an __open source stack composed of Barman and PostgreSQL__, you can __achieve RPO=0 thanks to _synchronous streaming replication___. 
- RTO is more the focus of a High Availability solution, like repmgr. Therefore, by __integrating Barman and repmgr__, you can dramatically reduce RTO to nearly zero.

# Barman and Postgre:
- [Barman](http://docs.pgbarman.org/release/2.6/#_basic_configuration)
- __Streaming backup vs rsync/SSH__
    - _Traditionally, Barman has always operated remotely via SSH, taking advantage of __rsync__ for physical backup operations_. 
    - _Version 2.0 introduces native support for PostgreSQL’s streaming replication protocol for backup operations, via __pg_basebackup___. 4

- Choosing one of these two methods is a decision you will need to make.
- On a general basis, starting from Barman 2.0, backup over streaming replication is the recommended setup for PostgreSQL 9.4 or higher. 
- Moreover, if you do not make use of tablespaces, backup over streaming can be used starting from PostgreSQL 9.2.

- __IMPORTANT__: Because __Barman transparently makes use of pg_basebackup__,  
    - _features such as incremental backup, parallel backup, deduplication, and network compression __are currently not available___. 
    - In this case, __bandwidth limitation has some restrictions__ - compared to the traditional method via rsync.

- __Traditional backup via rsync/SSH__ is available for all versions of PostgreSQL starting from 8.3, and it is __recommended in all cases where pg_basebackup limitations occur__ (for example, a very large database that can benefit from incremental backup and deduplication).

- __The reason why we recommend streaming backup__ is that, based on our experience, it is __easier to setup than the traditional one__. Also, streaming backup allows you to backup a PostgreSQL server on Windows5, and __makes life easier when working with Docker__.
