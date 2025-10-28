# Arbetsprov
Tidrapport  (PHP, HTML, JS, MySQL)

-----------------------------------------------------------------------------------------

PHP 8+
HTML/Bootstrap 5
MySQL (via PDO)
JavaScript (Fetch/AJAX)
REST API-integration
XAMPP (lokal Apache + MySQL-server) 

-----------------------------------------------------------------------------------------

projektmapp/
│
├── index.php
├── create.php
├── upload.php
├── api_client.php
├── db.php
├── config.php
├── Uploads/                ← här sparas uppladdade bilder
└── Bilder/                 ← bilder som används för logga och uppladdning

-----------------------------------------------------------------------------------------

**index.php** – Lista tidrapporter

Denna sida hämtar alla befintliga tidrapporter från API:et och presenterar dem i en tabell.
Användaren kan filtrera resultatet efter arbetsplats och datumintervall.

Fält för filtrering:
Arbetsplats (hämtas från API)
Från-datum
Till-datum

Huvudfunktioner:
Hämtar arbetsplatser från API och fyller dropdown-menyn.
Filtrerar tidrapporter med kombinationer av arbetsplats och datum.
Sorterar resultat efter rapportdatum (nyast först som standard).
Inkluderar en “Sök” och “Återställ”-knapp.
Visar tydliga felmeddelanden om API-anropet misslyckas.
Enkel, responsiv design med hjälp av Bootstrap.

Tekniker:
PHP för databehandling, HTML/Bootstrap för layout, GET-parametrar för filtrering.

-----------------------------------------------------------------------------------------

**create.php** – Skapa ny tidrapport

Denna sida innehåller ett formulär för att skapa nya tidrapporter.
Formuläret skickas asynkront via JavaScript (AJAX) till upload.php, som skapar upp tidrapporten i API:t och eventuella bilduppladdningar.
Det innebär att formuläret skickas till servern i bakgrunden, utan att ladda om sidan.

Fält i formuläret:
Datum
Antal timmar
Arbetsplats (hämtas från API)
Övrig information (valfritt textfält)
Bild (valfri filuppladdning)

Huvudfunktioner:
Hämtar arbetsplatser från API för dropdown-menyn.
Skickar formulärdata via fetch() utan att ladda om sidan (AJAX).
Visar tydligt feedback-meddelande (“Tidrapport skapad!” eller fel).
Tillåter valfri bilduppladdning (JPG, PNG, GIF, WEBP, SVG, max 5 MB).
Innehåller också responsiv design via Bootstrap.

Tekniker:
HTML, CSS/Bootstrap, JavaScript (fetch + FormData), PHP för API-anrop.

-----------------------------------------------------------------------------------------

**upload.php** – Serverdel (hanterar AJAX-anrop)

upload.php är kärnan i backend-logiken.
Den tar emot datan som skickas från create.php och hanterar hela uppladdningen mot API och databas.

Steg-för-steg:
Validerar fält – ser till att datum, timmar och arbetsplats finns och har rätt format.
Hanterar bilduppladdning (valfritt)
Sanerar filnamn (tar bort mellanslag, å/ä/ö m.m.)
Lägger till tidsstämpel för att undvika dubbletter.
Sparar bilden i mappen /Uploads.
Skapar tidrapport i API:et genom ett POST-anrop till /timereport.
Sparar referens i MySQL – om en bild laddats upp, lagras (report_id, filename) i tabellen images.
Svarar i JSON-format till webbläsaren med status (success/error).

Tekniker:
PHP (cURL, PDO, Filhantering), JSON, REST API-integration.

-----------------------------------------------------------------------------------------

**api_client.php** – Anslutning till REST API

Innehåller två funktioner (api_get och api_post) för att kommunicera med det externa REST-API:t.
All kommunikation sker via cURL och JSON.

Funktioner:
api_request() – hanterar både GET och POST, sätter korrekta headers (inkl. API-nyckel).
api_get() – används i index.php och create.php för att hämta data.
api_post() – används i upload.php för att skapa nya tidrapporter.
Returnerar svar som PHP-arrays och kastar tydliga felmeddelanden om något går fel.

Tekniker:
PHP cURL, JSON, Authorization headers.

-----------------------------------------------------------------------------------------

**db.php** – Databasanslutning 

Hantera anslutningen till den lokala MySQL-databasen via PDO (PHP Data Objects).
Används för att spara kopplingen mellan API:ets report_id och bildfilens namn.

Funktioner:
Konstanter (DB_HOST, DB_NAME, DB_USER, DB_PASS) för anslutningsinformation.
db() – återanvänder en och samma PDO-instans (“singleton”-mönster).
Aktiverar felhantering via PDO::ERRMODE_EXCEPTION.
Använder utf8mb4 för full Unicode-stöd.

Databastabell (för tabellen 'images'):

CREATE TABLE images (
  id INT AUTO_INCREMENT PRIMARY KEY,
  report_id INT NOT NULL,
  filename VARCHAR(255) NOT NULL,
  uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

Tekniker:
PHP PDO, MySQL, utf8mb4.

-----------------------------------------------------------------------------------------

**config.php** – Grundkonfiguration

Innehåller globala konstanter för API-adressen och API-nyckel.
Används av api_client.php för alla API-anrop.

-----------------------------------------------------------------------------------------

