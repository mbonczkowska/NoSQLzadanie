
## Zadanie 1a

```
cat Train.csv | tr "\n" " " | tr "\r" "\n" | head -n 6034196 > Train_prepared.csv
```
### mongo version 2.4.1
```
time mongoimport -db train -c train -type csv --headerline -file Train_prepared.csv
```
real	7m50.867s  

user	1m20.793s  

sys	0m8.582s  
### mongo version 2.8.0-rc0

```
time mongoimport -d dataBase -c train --type csv --file Train_prepared.csv --headerline
```
real	9m32.918s  

user	6m30.474s  

sys	0m38.940s  
### postgreSQL  
Najpierw należy stworzyć tabelę
````
 CREATE TABLE train (Id integer PRIMARY KEY,Title varchar, Body varchar,Tags varchar);
````
Następnie wpisać
````
COPY train FROM '/home/magdalena/Pobrane/Train_prepared.csv' DELIMITER ',' CSV;  
````
czas: ok. 40 min

Dalsze podpunkty były zrobione w wersji mongo  2.8.0-rc0
## Zadanie 1b
### mongo
```
db.train.count()
```
### postgreSQL
````
SELECT COUNT(*) FROM train;
````

6034195
##Zadanie 1c
```
var conn = new Mongo();
var db = conn.getDB('dataBase');
var dataBase = db.train.find();
var count = 0;

dataBase.forEach(function (record) {
	if (typeof record.tags === 'string') {
		var tableOfTags = record.tags.split(' ');

		db.save.update(
			{_id: record._id},
			{$set: {tags: tableOfTags}}
		)
	}
});
````
real	3m25.169s  

user	0m46.436s  

sys	0m3.719s  



#Zadanie 1d
time mongoimport -d dataBase -c geoaus --type csv --file AustralianPostCodes.csv --headerline
real	0m1.332s
user	0m0.458s
sys	0m0.085s
