## Zadanie 3  


Specyfikacja techniczna komputera:  

|                       |                                  |
|-----------------------|---------------------------------|
| System operacyjny     | Linux Ubuntu 14.04 (64-bitowy) |
| Procesor              | Intel i5-3230M                  |
| Ilość Ram             | 8 GB                            |


Najpierw należy zaimportować dokument tekstowy to bazy mongo
### mongo version 3.0.0-rc8

```
 time mongoimport  -db mapReduce -c wordList --file word_text.txt -f "word"
 ```

 Czas przesyłania: 
 ```
real    0m0.796s
user    0m0.000s
sys     0m0.031s
```
 Funkcja map:
 ```js

 m = function() {    
     emit( Array.sum(this.word.split("").sort()), 1 );    
};

```
Funkcja reduce:
```js
 r = function(key, values) {   
    return Array.sum(values); 
};

```
MapReduce:
```js
db.wordList.mapReduce(m,  r,
  {
    out: "reduce"
  }
);
```
Wynik: 
```
{
        "result" : "resp",
        "timeMillis" : 423,
        "counts" : {
                "input" : 8199,
                "emit" : 8199,
                "reduce" : 914,
                "output" : 7011
        },
        "ok" : 1
}
```
Przykładowe wyniki:
```
{ "_id" : "aabcrs", "value" : 1 }
{ "_id" : "aabcsu", "value" : 1 }
{ "_id" : "aabder", "value" : 1 }
{ "_id" : "aabdes", "value" : 1 }
{ "_id" : "aabdet", "value" : 1 }
{ "_id" : "aabdll", "value" : 1 }
{ "_id" : "aabdlm", "value" : 1 }
{ "_id" : "aabdmn", "value" : 1 }
{ "_id" : "aabdor", "value" : 2 }
{ "_id" : "aabegt", "value" : 1 }
{ "_id" : "aabelr", "value" : 1 }
{ "_id" : "aabelt", "value" : 1 }
{ "_id" : "aabelz", "value" : 1 }
{ "_id" : "aabemo", "value" : 1 }
{ "_id" : "aabess", "value" : 1 }
{ "_id" : "aabest", "value" : 1 }
{ "_id" : "aabggr", "value" : 1 }
{ "_id" : "aabggs", "value" : 1 }
{ "_id" : "aabgin", "value" : 1 }
{ "_id" : "aabgir", "value" : 1 }
```
##Zapytania:  
###Słowa, które mają po jednym anagramie
 ```
db.reduce.find({value:1}).count()
 ```
 Wynik:
 ```
 6097
 ```
###Słowa, które mają najwięcej anagramów  
```
 db.reduce.find().sort({value: -1})
 ```

 ![GitHub Logo](/images/pbar1.png)  
  
###Słowa, w których pierwsza posortowana litera to "o"
````
db.reduce.find({_id: /^o/}).sort({value: -1})
````
![GitHub Logo](/images/pbar2.png)      

###Ilość słów kończących się na dane litery  
```
db.reduce.find({_id: /a$/}).count()
db.reduce.find({_id: /b$/}).count()
db.reduce.find({_id: /c$/}).count()
itd.
```
![GitHub Logo](/images/pbar3.png)   

## Zadanie 3  
Korzystając ze skryptu link[http://wbzyl.inf.ug.edu.pl/nosql/db/mongodb/dostojewski.rb] przeniosłam książkę 
"Rozważna i romantyczna" Jane Austen do bazy mongo.



## Zadanie 3 b  

Po kilku nie udanych próbach skorzystałam z XML2CSV-Generic-Converter
```
 time java -jar XML2CSVGenericConverter_V1.0.0.jar -v -i plwiki.xml -o /home/magdalena/Pobrane/nosql/
```
Czas:
```
real	10m18.912s
user	7m50.329s
sys	0m21.665s
```
Następnie import do bazy mongo:
```
time mongoimport -d wiki -c wiki --type tsv --file plwiki.csv --headerline --ignoreBlanks
```

Czas:

```
real	50m11.950s
user	3m34.252s
sys	0m43.029s
```
```js
var map = function(){     
      var words = this.toString().split(" ");             
      words.forEach(function(key){                 
           emit(key, key);             
      });           
 };

var reduce = function(key, value){                         
       return value.length;              
 };


db.wiki.mapReduce( map, reduce,  {out: 'word_count'});
```
