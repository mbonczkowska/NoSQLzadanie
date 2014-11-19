
## Zadanie 1a  
Najpierw należy pozbyć się znaków nowej linii.
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
real	17m46.795s  

user	6m31.315s  

sys	0m40.601s

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

![GitHub Logo](/images/importTrain.png)

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
Skrypt
```
var conn = new Mongo();
var db = conn.getDB('dataBase');
var dataBase = db.train.find();

dataBase.forEach(function (record) {
	if (typeof record.tags === 'string') {
		var table = record.tags.split(' ');
		db.save.update(
			{_id: record._id},
			{$set: {tags: table}}
		)
	}
});
````
real	3m25.169s  

user	0m46.436s  

sys	0m3.719s  



#Zadanie 1d
````
time mongoimport -d dataBase -c geoaus --type csv --file AustralianPostCodes.csv --headerline
```
real	0m1.332s  

user	0m0.458s  

sys	0m0.085s
    
    
Przykładowy rekord:
```
{
	"_id" : ObjectId("546ba7cac71c8488c51e285a"),
	"suburb" : "BARTON",
	"location" : {
		"type" : "Point",
		"coordinates" : [
			149.095065,
			-35.201372
		]
	}
}
````
![GitHub Logo](/images/importAus.png)
