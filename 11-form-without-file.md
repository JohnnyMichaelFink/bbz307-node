# Formulare (ohne Dateiupload)

Um über ein Formular Daten erfassen zu können brauchen wir zuerst eine Seite, in der das Formular angezeigt wird. Das kann entweder in einer bestehenden Handlebars-Datei oder in einer neuen sein.
Wir gehen davon aus, dass wir eine neue Datei brauchen.

Registriere zuerst in der `config.js`-Datei eine neue Seite für das Formular:

```js
app.get('/event_formular', function(req, res) {
  res.render('event_formular');
});
```

Erstelle dann im Ordner `views` die Datei `event_formular.handlebars` und füge das Formular ein:

```html
<form action="/event" enctype="multipart/form-data" method="post">
  Event Name: <input type="text" name="event_name"><br>
  Beschreibung: <input type="text" name="description"><br>
  <input type="submit">
</form>
```

Passe die Formularfelder so an, dass die Eingabefelder, insbesondere die `name`-Attribute deiner Datenbankstruktur entsprechen. Das obenstehende Beispiel erfasst den Namen und die Beschreibung eines Events. Passe es auf deine Applikation an.

Wenn das Formular abgesendet wird, wird es an die Adresse `/event` gesendet. Diese Adresse müssen wir noch registrieren und dann die Formulardaten in die Datenbank speichern.

Füge in der Datei `config.js` den folgenden Code ein:

```js
app.post('/event', upload.none(), async function (req, res) {
  await pool.query('INSERT INTO events (event_name, description) VALUES ($1, $2)', [req.body.event_name, req.body.description]);
  res.redirect('/');
});
```

Damit werden die Formularfelder `event_name` und `description` in die Datenbank gespeichert. Passe auch hier die folgenden Angaben an, damit sie deiner Applikation entsprechen:

* Den Tabellennamen im SQL-Befehl (hier: `events`)
* Die Spalten, die gefüllt werden sollen (hier: `event_name, description`)
* Die Formularfelder, die gespeichert werden sollen (hier: `req.body.event_name, req.body.description`)

**Hinweis:** Wenn ebenfalls erfasst werden soll, welcher User das neue Event erfasst hat müsst ihr die `user_id`ebenfalls in die Datenbank schreiben. Füge zu diesem Zweck stattdessen den folgenden Code in der Datei `config.js` ein: 

```js
app.post('/event', upload.none(), async function (req, res) {
  const user = await login.loggedInUser(req);
  await pool.query('INSERT INTO event (event_name, description) VALUES ($1, $2, $3)', [req.body.event_name, req.body.description, user.id]);
  res.redirect('/');
});
```
Passe auch hier die folgenden Angaben an, damit sie deiner Applikation entsprechen:

* Den Tabellennamen im SQL-Befehl (hier: `events`)
* Die Spalten, die gefüllt werden sollen (hier: `event_name, description`)
* Die Formularfelder, die gespeichert werden sollen (hier: `req.body.event_name, req.body.description`)

