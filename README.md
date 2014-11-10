
# Zadanie 1a

```
cat Train.csv | tr "\n" " " | tr "\r" "\n" | head -n 6034196 > Train_prepared.csv
```

```
time mongoimport -db train -c train -type csv --headerline -file Train_prepared.csv
```
real	7m50.867s\n
user	1m20.793s\n
sys	0m8.582s\n

# Zadanie 1b
```
db.train.count()
```
6034195
