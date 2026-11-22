# Schritt-für-Schritt-Einführung in die mobile Programmierung mit Kotlin

📖 **Das Tutorial lesen: [https://stahe.github.io/de-kotlin-etape-par-etape-nestjs-oct-2026/](https://stahe.github.io/de-kotlin-etape-par-etape-nestjs-oct-2026/)**

Dieser Kurs zeigt, wie man mit [Kotlin](https://kotlinlang.org) 2.2 und [Jetpack Compose](https://developer.android.com/compose), der deklarativen UI-Bibliothek von Android, eine **native mobile Android-App** schreibt: eine App, deren Bildschirme **auf dem Smartphone** anhand von JSON-Daten eines Servers gerendert werden.

Er folgt der Progression des Kurses [Schritt-für-Schritt-Einführung in das Mobile-Framework Flutter](https://stahe.github.io/de-flutter-etape-par-etape-sept-2026/) (der seinerseits den Kursen zu [React](https://stahe.github.io/de-react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/de-vuejs-etape-par-etape-sept-2026/) und [Angular](https://stahe.github.io/de-angular-etape-par-etape-sept-2026/) nachempfunden ist): dieselbe Struktur, dieselben 25 Beispiele, derselbe Server, dieselbe Fallstudie, im Android-Stil geschrieben.

| Flutter-Kurs | Kotlin-Kurs |
|---|---|
| Dart, Widgets, eine `build()`-Methode | Kotlin, `@Composable`-Funktionen |
| `setState`, `StatefulWidget` | `remember`, `rememberSaveable`, `mutableStateOf` |
| die Flutter-Engine rendert jedes Pixel | Compose, das eigene Toolkit von Android (Material 3) |
| `initState`, `dispose` | Effekte: `LaunchedEffect`, `DisposableEffect` |
| `InheritedWidget`, provider | `CompositionLocal`, `ViewModel`, `StateFlow` |
| go_router | Compose Navigation (`@Serializable`-Routen, Deep Links) |
| `Future`, `Stream` | Coroutinen, `Flow` |
| JSON-Wörterbücher, `intl` | `strings.xml`, die Sprache der App (`setApplicationLocales`) – und dieselben JSON-Wörterbücher |
| das Paket `http` | OkHttp und sein `CookieJar` für das Token-Cookie |
| `shared_preferences` | DataStore, `SharedPreferences` |
| `flutter_test` | instrumentierte Tests von Compose (auf dem Emulator) |

Der Server bleibt jedoch unverändert: Es ist der JSON-Server der Anwendung **RdvMedecins**, der bereits von den Clients in React, Vue.js, Angular und Flutter verwendet wird.

## Der Ansatz: viele kurze Beispiele, dann eine Fallstudie

Der Kurs ist um **25 kurze Beispiele** aufgebaut, die sich jeweils auf ein einziges Konzept konzentrieren. Sie bilden ein einziges Android-Studio-Projekt – eine Activity pro Beispiel –, das über ein Menü zugänglich ist.

| Kapitel | Inhalt | Beispiele |
|---|---|---|
| Erste Schritte | ein Android-Projekt (Gradle, Manifest, Activity), `@Composable`-Funktionen, Zustand und Recomposition, Ereignisse (Gesten, Scrollen, Fokus), alle Feldtypen, ein Reducer (`sealed interface`, `when`), Validierung, Felder, die sich selbst validieren können, Formatierung (`java.time`, `NumberFormat`, Extensions) | 01–09 |
| Komponenten | Parameter, Funktionen als Parameter, Komposition (Platzierungen, generische Komponenten), Lebenszyklus (Effekte), `CompositionLocal`, wiederverwendbare Verhaltensweisen und Animationen, Bestätigungsdialog (suspendierende Funktion) und `Snackbar` | 10–16 |
| Navigation | Compose Navigation: typisierte Routen, Parameter, Deep Links, Tab-Leiste, Weiterleitung, Guards | 17–18 |
| Asynchrone Operationen und gemeinsamer Zustand | Coroutinen, `Flow`, Entprellen, veraltete Antworten (`mapLatest`), `produceState`, `ViewModel`, `StateFlow`, DataStore, dunkles Design, `SaveableStateHolder` | 19–20 |
| Internationalisierung | `strings.xml`-Ressourcen, Parameter, Pluralformen, Datumsangaben, Beträge, Sprache der App, übersetzter Kalender | 21 |
| Der Server: eine Blackbox | Einrichtung des JSON-Servers, seine API, 48 `curl`-Beispiele, was sich für einen mobilen Client ändert | – |
| Kommunikation mit dem Server | OkHttp, die Serveradresse (Emulator, Smartphone, `adb reverse`), das Token-Cookie auf dem Smartphone, `kotlinx.serialization`, eine API-Zugriffsschicht, ein `ViewModel`, den Feldern zugeordnete Serverfehler | 22–25 |

Jedes Beispiel wird mit seinem vollständigen, kommentierten Code und Screenshots seiner Ausführung vorgestellt.

## Der Server: eine Blackbox

Der Server ist der NestJS-Server der vorherigen Lektionen, dessen Controller **JSON** zurückgeben. Diese Lektion behandelt ihn als **Blackbox**: Wir richten ihn ein, untersuchen seine API und fragen ihn mit `curl` ab, müssen aber seinen Code nicht lesen (er wird für Neugierige mitgeliefert und kommentiert).

- Alle Fehler haben dasselbe Format: `{ "statusCode": 409, "key": "ERRORS.LOGIN_TAKEN", "params": {...}, "fields": {...} }` – **Schlüssel** für die Übersetzung, niemals Klartext;
- Authentifizierung über ein JWT-Token in einem `httpOnly`-Cookie: Auf dem Smartphone speichert und sendet der HTTP-Client (OkHttp und sein `CookieJar`) es zurück;
- Ein „Testmodus“ für das CAPTCHA, damit die API mit `curl` abgefragt werden kann (und Screenshots über automatisierte Tests erzeugt werden können).

## Die Fallstudie: der Android-Client von RdvMedecins

Eine vollständige Anwendung zur **Terminbuchung in einer Arztpraxis**, bei der **alle** Dateien aufgeführt und kommentiert werden.

- **Modernes Android**: Kotlin 2.2, Jetpack Compose und Material 3; eine einzige Activity; Compose Navigation; `ViewModel`; Coroutinen; OkHttp; kotlinx.serialization; eine Schichtenarchitektur (Daten, Zustand, UI).
- **Drei Rollen**: `ADMIN` (verwaltet Ärzte und Kunden), `DOCTOR` (plant und storniert Termine), `USER` (der Patient: bucht Termine für sich selbst und verwaltet sein Konto).
- **Datenschutz**: Ein Patient erhält nie die Namen anderer Patienten – der Server sendet sie nicht.
- **Der gesamte Zustand eines Bildschirms steckt in seiner Route**: `Ecrans.Agenda(idMedecin = 1, jour = "2026-10-05")`; das Buchungsfenster ist ein Ziel vom Typ „Dialog“, das die Zurück-Taste schließt.
- **Serverseitige Validierung**: Formulare zeigen die von der API zurückgegebenen Fehler unter jedem Feld an; optimistisches Sperren, doppelte Namen, bereits vergebener Login usw.
- **Sitzung**: bleibt zwischen Sitzungen erhalten (das Cookie wird auf dem Gerät gespeichert), wird beim Start wiederhergestellt (`GET /api/auth/moi`), der Ablauf wird an einer einzigen Stelle behandelt (Antwort 401), nach erneuter Anmeldung geht es zum angeforderten Bildschirm zurück.
- **Für Mobilgeräte geeignet**: Drawer-Menü (oder `NavigationRail` auf großen Bildschirmen und im Querformat), Listen statt Tabellen, schwebende Schaltfläche, Tastatur, die Felder nicht verdeckt, und Funktionen, die Drehungen und Sprachwechsel überstehen.
- **Französisch / Englisch**, mit den JSON-Wörterbüchern der anderen Clients, einschließlich des Kalenders und der eigenen Textzeichenfolgen von Android.
- **Automatische Screenshots**: Ein instrumentierter Test navigiert wie ein Benutzer durch die App.
- **Bereitstellung**: die signierte Produktionsversion (APK, Android App Bundle).

## Inhalt des Repositorys

```
exemples_kotlin/           die 25 kurzen Beispiele (ein einziges Android-Studio-Projekt)
rdvmedecins-nestjs-json/   der JSON-Server von RdvMedecins (die „Blackbox“)
rdvmedecins_kotlin/        der Android-Client der Fallstudie
tools/                     die Windows-Skripte, die Screenshots erzeugen
```

Jedes Projekt kann in Android Studio geöffnet (`File / Open`) oder über die Kommandozeile kompiliert werden: `gradlew installDebug`. Die Apps verbinden sich mit dem Server unter `http://localhost:8080`, nachdem `adb reverse tcp:8080 tcp:8080` ausgeführt wurde (oder mit einer anderen Adresse: `gradlew installDebug -Pserver=http://10.0.2.2:8080`).

## Technologien

Kotlin 2.2 · Jetpack Compose (BOM 2026.02) · Material 3 · Navigation Compose 2.9 · Lifecycle / ViewModel 2.9 · DataStore 1.1 · AppCompat 1.7 · OkHttp 4.12 · kotlinx.serialization 1.9 · AndroidSVG 1.4 · Compose UI Test · AGP 9.4 · Gradle 9.6 · Android 8.0 (API 26) bis Android 17 (API 37) · serverseitig: NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Voraussetzungen

- Grundlagen der Sprache Kotlin: Der Kurs [Kotlin](https://stahe.github.io/de-kotlin-oct-2026/) ist eine gute Vorbereitung, die verwendeten Konzepte werden aber in den Beispielen jeweils erklärt. Grundlagen des HTTP-Protokolls.
- Android Studio (das JDK, Gradle, das Android SDK und den Emulator mitbringt) oder ein Android-Smartphone; Node.js und ein MySQL-Server (z. B. Laragon unter Windows) für den JSON-Server. Anleitungen zur Installation finden sich im Kurs.

## Autor

Dieser Kurs, seine Beispiele und der Android-Client wurden von **Claude**, der KI von [Anthropic](https://www.anthropic.com) (Oktober 2026), im Auftrag von **Serge Tahé** geschrieben.
