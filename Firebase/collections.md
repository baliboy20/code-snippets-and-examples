#Firebase Collections

#### Referencing a collection
````
 const a: AngularFirestoreCollection = this.fs.collection('companies');
````

#### Referencing a Document

```typescript
  getDocument() {
    const a: AngularFirestoreDocument = this.fs.doc('/companies/1L4BBOKDgp0ExXu4LcKI');
    a.get().subscribe(b => console.log('DOC', b.data()));
  }

 getDocumentasWell() {
    const a: AngularFirestoreDocument = this.fs.collection('companies').doc('1L4BBOKDgp0ExXu4LcKI');
    a.get().subscribe(b => console.log('DOC', b.data()));
  }
```

 
#### Referencing a SubDocument

```typescript
   getSubCollection() {
      const a: AngularFirestoreCollection = this.fs.collection('companies')
        .doc('1L4BBOKDgp0ExXu4LcKI')
        .collection('employee');

      a.get().subscribe(b => {
        b.docs.forEach(c => console.log('c', c.data()));
        b.forEach(a1 => console.log(a1.data()));
      });
// Also
    const a: AngularFirestoreCollection = this.fs.collection('companies')
      .doc('1L4BBOKDgp0ExXu4LcKI').collection('employee');
        a.snapshotChanges().subscribe(b => b.forEach(c => console.log('d', c.payload.doc.data())));
  }

    }
```
