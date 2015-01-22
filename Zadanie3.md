## Zadanie 3  

Najpierw należy zaimportować dokument tekstowy to bazy mongo
### mongo version v2.6.6
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
 ```
 m = function() {  
 
  emit( Array.sum(this.word.split("").sort()), this.word );  
  
};
````
Funkcja reduce:
````
r = function(key, values) {  

  return values.toString();  
  
 
};
````
MapReduce:
````
db.wordList.mapReduce(m,  r,
  {
    out: "reduce"
  }
);
````
Wynik: 
````
{
        "result" : "reduce",
        "timeMillis" : 808,
        "counts" : {
                "input" : 16398,
                "emit" : 16398,
                "reduce" : 7853,
                "output" : 7011
        },
        "ok" : 1
}
````
## Zadanie 3 b  

Po kilku nie udanych próbach skorzystałam z XML2CSV-Generic-Converter
````
 time java -jar XML2CSVGenericConverter_V1.0.0.jar -v -i plwiki.xml -o /home/magdalena/Pobrane/nosql/
````
Czas:
````
real	10m18.912s
user	7m50.329s
sys	0m21.665s
````
Następnie import do bazy mongo:
````
time mongoimport -d wiki -c wiki --type tsv --file plwiki.csv --headerline --ignoreBlanks
````

Czas:

````
real	50m11.950s
user	3m34.252s
sys	0m43.029s
````
````
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
`````
