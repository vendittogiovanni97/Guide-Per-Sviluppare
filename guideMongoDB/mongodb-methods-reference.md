# MongoDB Methods Reference Guide

## Operazioni di lettura
- `find()` - Cerca documenti in una collezione secondo criteri specificati
- `findOne()` - Trova un singolo documento che soddisfa i criteri
- `findById()` - Trova un documento tramite il suo ID
- `countDocuments()` - Conta i documenti che soddisfano un criterio
- `distinct()` - Trova valori unici per un campo specifico

## Operazioni di creazione
- `insertOne()` - Inserisce un singolo documento
- `insertMany()` - Inserisce più documenti in una volta
- `create()` - Crea un nuovo documento (metodo di Mongoose)
- `createIndex()` - Crea un indice sulla collezione

## Operazioni di aggiornamento
- `updateOne()` - Aggiorna un singolo documento
- `updateMany()` - Aggiorna più documenti
- `replaceOne()` - Sostituisce completamente un documento
- `findOneAndUpdate()` - Trova un documento e lo aggiorna
- `findByIdAndUpdate()` - Trova un documento tramite ID e lo aggiorna

## Operazioni di eliminazione
- `deleteOne()` - Elimina un singolo documento
- `deleteMany()` - Elimina più documenti
- `findOneAndDelete()` - Trova un documento e lo elimina
- `findByIdAndDelete()` - Trova un documento tramite ID e lo elimina
- `drop()` - Elimina un'intera collezione

## Operazioni di aggregazione
- `aggregate()` - Esegue operazioni di aggregazione
- `mapReduce()` - Esegue operazioni map-reduce
- `bulkWrite()` - Esegue operazioni multiple in un'unica richiesta

## Operazioni di indice
- `createIndex()` - Crea un indice
- `dropIndex()` - Elimina un indice
- `listIndexes()` - Elenca gli indici su una collezione

## Operazioni di cursore
- `sort()` - Ordina i risultati
- `limit()` - Limita il numero di risultati
- `skip()` - Salta un numero di risultati
- `project()` - Seleziona solo determinati campi

## Operatori di confronto
- `$eq` - Uguale a
- `$gt` - Maggiore di
- `$gte` - Maggiore o uguale a
- `$lt` - Minore di
- `$lte` - Minore o uguale a
- `$ne` - Non uguale a
- `$in` - Corrisponde a qualsiasi valore in un array
- `$nin` - Non corrisponde a nessun valore in un array

## Operatori logici
- `$and` - Corrisponde a tutte le condizioni specificate
- `$or` - Corrisponde ad almeno una delle condizioni specificate
- `$not` - Inverte il risultato della query
- `$nor` - Non corrisponde a nessuna delle condizioni specificate

## Operatori di elemento
- `$exists` - Corrisponde a documenti che hanno un campo specificato
- `$type` - Seleziona documenti se un campo è di un tipo specifico

## Operatori di aggiornamento
- `$set` - Imposta il valore di un campo
- `$unset` - Rimuove un campo
- `$inc` - Incrementa il valore di un campo
- `$push` - Aggiunge un elemento a un array
- `$pull` - Rimuove elementi da un array
- `$addToSet` - Aggiunge elementi a un array solo se non esistono già

## Metodi di Mongoose
- `save()` - Salva un documento
- `populate()` - Popola i riferimenti con documenti da altre collezioni
- `lean()` - Restituisce documenti come oggetti JavaScript normali
- `exec()` - Esegue una query e restituisce una Promise
- `select()` - Seleziona i campi da restituire

## Esempi di query comuni

### Trovare tutti i documenti in una collezione
```javascript
db.users.find({})
```

### Trovare documenti con criteri specifici
```javascript
db.users.find({ age: { $gt: 18 } })
```

### Aggiornare un documento
```javascript
db.users.updateOne(
  { _id: ObjectId("123") },
  { $set: { name: "Nuovo Nome" } }
)
```

### Eliminare documenti
```javascript
db.users.deleteMany({ status: "inattivo" })
```

### Aggregazione
```javascript
db.orders.aggregate([
  { $match: { status: "completato" } },
  { $group: { _id: "$customer", totalSpent: { $sum: "$amount" } } }
])
```
