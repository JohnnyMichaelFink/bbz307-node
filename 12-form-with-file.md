# Formulare (mit Dateiupload)

Um über ein Formular Daten erfassen zu können brauchen wir zuerst eine Seite, in der das Formular angezeigt wird. Das kann entweder in einer bestehenden Handlebars-Datei oder in einer neuen sein.
Wir gehen davon aus, dass wir eine neue Datei brauchen.

Registriere zuerst in der `config.js`-Datei eine neue Seite für das Formular:

```js
app.get('/post_formular', function(req, res) {
  res.render('post_formular');
});
```

Erstelle dann im Ordner `views` die Datei `post_formular.handlebars` und füge das Formular ein:

```html
<form action="/post" enctype="multipart/form-data" method="post">
  Titel: <input type="text" name="title"><br>
  Anleitung: <input type="text" name="description"><br>
  Foto: <input type="file" name="image"><br>
  <input type="submit">
</form>
```

Passe die Formularfelder so an, dass die Eingabefelder, insbesondere die `name`-Attribute deiner Datenbankstruktur entsprechen. Das obenstehende Beispiel erfasst den Titel, die Anleitung und das Foto eines Posts. Passe es auf deine Applikation an.

Wenn das Formular abgesendet wird, wird es an die Adresse `/post` gesendet. Diese Adresse müssen wir noch registrieren und dann die Formulardaten in die Datenbank speichern.

Füge in der Datei `config.js` den folgenden Code ein:

```js
app.post('/post', upload.single('image'), async function (req, res) {
  await pool.query('INSERT INTO posts (title, description, image) VALUES ($1, $2, $3)', [req.body.title, req.body.description, req.file.filename]);
  res.redirect('/');
});
```

Damit werden die Formularfelder `title` und `description` in die Datenbank gespeichert. Das Headerfoto wird automatisch im Ordner `uploads` gespeichert und nur der Dateiname in der Datenbank gespeichert. Passe auch hier die folgenden Angaben an, damit sie deiner Applikation entsprechen:

* Den Namen des Datei-Formularfeldes (hier: `upload.single('image')`)
* Den Tabellennamen im SQL-Befehl (hier: `posts`)
* Die Spalten, die gefüllt werden sollen (hier: `title, anleitung, headerfoto`)
* Die Formularfelder, die gespeichert werden sollen (hier: `req.body.titel, req.body.anleitung, req.file.filename`). **Beachte, dass der letzte Teil (`req.file.filename`) immer gleich bleibt.**

**Hinweis:** Wenn ebenfalls erfasst werden soll, welcher User den neuen Post erfasst hat müsst ihr die `user_id`ebenfalls in die Datenbank schreiben. Füge zu diesem Zweck stattdessen den folgenden Code in der Datei `config.js` ein: 

```js
app.post('/post', upload.single('headerfoto'), async function (req, res) {
  const user = await login.loggedInUser(req);
  await pool.query('INSERT INTO posts (title, description, image, user_id) VALUES ($1, $2, $3, $4)', [req.body.title, req.body.description, req.file.filename, user.id]);
  res.redirect('/');
});
```

Passe auch hier die folgenden Angaben an, damit sie deiner Applikation entsprechen:

* Den Namen des Datei-Formularfeldes (hier: `upload.single('image')`)
* Den Tabellennamen im SQL-Befehl (hier: `posts`)
* Die Spalten, die gefüllt werden sollen (hier: `title, description, image`)
* Die Formularfelder, die gespeichert werden sollen (hier: `req.body.title, req.body.description, req.file.filename`). Beachte, dass der letzte Teil (`req.file.filename`) immer gleich bleibt.

