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
````
db.wordList.mapReduce(m,  r,
  {
    out: "reduce"
  }
);
````
