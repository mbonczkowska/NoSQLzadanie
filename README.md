
## Zadanie 1a  
Najpierw należy pozbyć się znaków nowej linii.
```
cat Train.csv | tr "\n" " " | tr "\r" "\n" | head -n 6034196 > Train_prepared.csv
```
Następnie wykonać import.
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


![GitHub Logo](/images/arrayTrain.png)   


##Zadanie 1d

Dane to lista kodów pocztowych z lokalizacją i dzielnicą w Australii.  
Import:

````
time mongoimport -d dataBase -c geoaus --type csv --file AustralianPostCodes.csv --headerline
```
Czas:  

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
		"postcode" : 221,
		"coordinates" : [
			149.095065,
			-35.201372
		]
	}
}
````
W danych znajdowało się kilkadziesiąt pustych lokacji
````
db.geoaus.remove({"location.coordinates":[undefined,undefined]})
WriteResult({ "nRemoved" : 70 })
````
I dzielnic
````
db.geoaus.remove({suburb:""})
WriteResult({ "nRemoved" : 1 })
````
Po ich usunięciu ilość rekordów wynosi: 16009  

### Zapytanie 1
Dzielnice w promieniu 20 km od Ti Tree
````
db.geoaus.find(    
	{ location: 
		{$nearSphere:                      
			{$geometry:
				{ "type" : "Point","coordinates" : [ 131.068332, -12.477758 ] },$maxDistance:20000                         }                   
		}             
	},{_id:0,postcode:0}
)
````
Wynik:
````
{ "suburb" : "TI TREE", "location" : { "type" : "Point", "coordinates" : [ 131.068332, -12.477758 ] } }
{ "suburb" : "ULURU", "location" : { "type" : "Point", "coordinates" : [ 130.992541, -12.486152 ] } }
{ "suburb" : "KINTORE", "location" : { "type" : "Point", "coordinates" : [ 130.991347, -12.486851 ] } }
{ "suburb" : "KARAMA", "location" : { "type" : "Point", "coordinates" : [ 130.913672, -12.400091 ] } }
````
[Mapka](/geojson/1.geojson)
### Zapytanie 2  

Dzielnice w zasięgu wschodniego i zachodniego New Castle oraz The Junction
````
db.geoaus.find(
	{location:	
		{$geoWithin:
			{$polygon:[ [ 151.788039, -32.927881 ] , [ 151.761141, -32.924908 ], 
			[ 151.759625, -32.937528 ], [ 151.788039, -32.927881 ]  ]}
		} 
	},{_id:0,postcode:0}
)
````
Wynik:
````
{ "suburb" : "THE JUNCTION", "location" : { "type" : "Point", "coordinates" : [ 151.759625, -32.937528 ] } }
{ "suburb" : "COOKS HILL", "location" : { "type" : "Point", "coordinates" : [ 151.769244, -32.93413 ] } }
{ "suburb" : "NEWCASTLE EAST", "location" : { "type" : "Point", "coordinates" : [ 151.788039, -32.927881 ] } }
{ "suburb" : "THE HILL", "location" : { "type" : "Point", "coordinates" : [ 151.777382, -32.929808 ] } }
{ "suburb" : "NEWCASTLE WEST", "location" : { "type" : "Point", "coordinates" : [ 151.761141, -32.924908 ] } }


````
[Mapka](/geojson/3.geojson)

