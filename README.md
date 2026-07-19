<!DOCTYPE html>
<html lang="nl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fitwandel Email Planner V12b</title>
    <style>
        body { font-family: sans-serif; line-height: 1.5; max-width: 900px; margin: 10px auto; padding: 15px; background-color: #f0f4f8; }
        .card { background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); margin-bottom: 20px; }
        .grid { display: flex; flex-wrap: wrap; gap: 15px; }
        .day-box { background: #f8f9fa; padding: 10px; border-radius: 8px; border: 1px solid #ddd; flex: 1; min-width: 250px; }
        label { display: block; font-weight: bold; margin-top: 10px; font-size: 0.9em; }
        select { 
            width: 100%; padding: 10px; margin-top: 5px; border: 1px solid #ccc; border-radius: 5px; 
            font-size: 16px; background-color: #fff; display: block;
        }
        .action-bar { margin: 15px 0; display: flex; gap: 10px; flex-wrap: wrap; }
        .btn { padding: 12px; border-radius: 5px; border: none; font-weight: bold; cursor: pointer; flex: 1; color: white; min-width: 150px; }
        .btn-subj { background: #3498db; }
        .btn-mail { background: #9b59b6; }
        #preview-html { background: #fff; border: 1px solid #ccc; padding: 15px; min-height: 100px; border-radius: 5px; }
        #copy-msg { color: #27ae60; font-weight: bold; display: none; margin-bottom: 10px; }
        a { color: #3498db; text-decoration: underline; }
    </style>
</head>
<body>

    <div class="card">
        <h2>Fitwandeltraining Planner</h2>
        <div style="background:#d1ecf1; padding:10px; border-radius:5px; margin-bottom:15px;">
            <label>Afzender:</label>
            <select id="sender-name" onchange="generateContent()"></select>
        </div>

        <div class="grid">
            <div class="day-box">
                <h3 id="date-mon">Maandag</h3>
                <label>Trainer:</label>
                <select id="trainer-mon" onchange="generateContent()"></select>
                <label>Locatie:</label>
                <select id="loc-mon" onchange="generateContent()"></select>
            </div>
            <div class="day-box">
                <h3 id="date-wed">Woensdag</h3>
                <label>Trainer:</label>
                <select id="trainer-wed" onchange="generateContent()"></select>
                <label>Locatie:</label>
                <select id="loc-wed" onchange="generateContent()"></select>
            </div>
            <div class="day-box">
                <h3 id="date-sat">Zaterdag</h3>
                <label>Trainers:</label>
                <div style="display:flex; gap:5px;">
                    <select id="trainer-sat-1" onchange="generateContent()"></select>
                    <select id="trainer-sat-2" onchange="generateContent()"></select>
                </div>
                <label>Locatie:</label>
                <select id="loc-sat" onchange="generateContent()"></select>
            </div>
        </div>
    </div>

    <div class="card">
        <div id="copy-msg">✔ Gekopieerd (inclusief linkjes)!</div>
        <div class="action-bar">
            <button class="btn btn-subj" onclick="copySubject()">1. Kopieer Onderwerp</button>
            <button class="btn btn-mail" onclick="copyBody()">2. Kopieer Inhoud</button>
        </div>
        <div id="preview-html"></div>
    </div>

<script>
    var TRAINERS = ["Ankie", "Clemens", "Marja", "Liesbeth"];
    var LOCATIES = [
        "Appelgaarde – Dental Clinics", "Bowling Westerpark", "Buytenparklaan t/o Adventure Valley",
        "CKC", "Dekker Sport – Scheglaan", "Happy Moose, Langeland", "Ilion", "Kinderboerderij Oosterheem, Weidemolen",
        "Kinderboerderij Rokkeveen, Balijhoeve", "Mandelabrug – Meerzicht", "Noord Aa, P2", "P Driemanspolder bij Camping de Drie Morgen",
        "Parkeerplaats Buytenhout, Oudeweg 100, Nootdorp", "Restaurant AA-Zicht", "Restaurant Roest, Golfbaan Bentwoud",
        "Snowworld Buytenpark, P rechts achterin", "Tennisvereniging Buytenwegh", "Vernedepark, bij de skatebaan",
        "Winkelcentrum De Leyens – Lidl", "Winkelcentrum Noordhove", "Winkelcentrum Rokkeveen – Quirinegang"
    ];

    var currentSubject = "";

    function fillSelect(id, list, defaultVal) {
        var sel = document.getElementById(id);
        if(!sel) return;
        var options = "";
        for(var i=0; i<list.length; i++) {
            var selected = (list[i] === defaultVal) ? " selected" : "";
            var label = (list[i] === "") ? "(geen)" : list[i];
            options += '<option value="' + list[i] + '"' + selected + '>' + label + '</option>';
        }
        sel.innerHTML = options;
    }

    function generateContent() {
        var sender = document.getElementById('sender-name').value;
        var tMon = document.getElementById('trainer-mon').value;
        var lMon = document.getElementById('loc-mon').value;
        var tWed = document.getElementById('trainer-wed').value;
        var lWed = document.getElementById('loc-wed').value;
        var tSat1 = document.getElementById('trainer-sat-1').value;
        var tSat2 = document.getElementById('trainer-sat-2').value;
        var lSat = document.getElementById('loc-sat').value;

        var zaterdagTrainer = (tSat2 === "" || tSat1 === tSat2) ? tSat1 : tSat1 + " en " + tSat2;

        var today = new Date();
        var dMon = new Date();
        dMon.setDate(today.getDate() + (1 - today.getDay() + 7) % 7 || 7);
        var dWed = new Date(dMon); dWed.setDate(dMon.getDate() + 2);
        var dSat = new Date(dMon); dSat.setDate(dMon.getDate() + 5);

        var opt = { weekday: 'long', day: 'numeric', month: 'long' };
        document.getElementById('date-mon').innerText = dMon.toLocaleDateString('nl-NL', opt);
        document.getElementById('date-wed').innerText = dWed.toLocaleDateString('nl-NL', opt);
        document.getElementById('date-sat').innerText = dSat.toLocaleDateString('nl-NL', opt);

        currentSubject = "Fitwandeltrainingen - week van " + dMon.toLocaleDateString('nl-NL', {day:'numeric', month:'long'});

        var targetEmail = "fitwandelen@ilion.nl";
        var html = "Beste wandelaars,<br><br>De planning voor de fitwandeltrainingen komende week:<br><br>";
        
        var days = [
            {d: dMon, t: tMon, l: lMon, time: "19.00 uur"},
            {d: dWed, t: tWed, l: lWed, time: "19.00 uur"},
            {d: dSat, t: zaterdagTrainer, l: lSat, time: "09.00 uur"}
        ];

        for(var j=0; j<days.length; j++) {
            var item = days[j];
            var daySimple = item.d.toLocaleDateString('nl-NL', {weekday:'long'});
            var dateSimple = item.d.toLocaleDateString('nl-NL', {day:'numeric', month:'long'}).replace(/ /g, "_");
            var mailLink = "mailto:" + targetEmail + "?subject=" + daySimple + "_" + dateSimple;
            
            html += "<strong>" + item.d.toLocaleDateString('nl-NL', opt) + ", " + item.time + "</strong><br>";
            html += "Trainer: " + item.t + "<br>";
            html += "Locatie: " + item.l + "<br>";
            html += "Aanmelden: <a href='" + mailLink + "'>Via deze Mail</a><br><br>";
        }

        html += "Fijn weekend en tot ziens bij de training(en)!<br><br>Namens de trainers,<br>" + sender;

        document.getElementById('preview-html').innerHTML = html;
    }

    function copySubject() {
        var textArea = document.createElement("textarea");
        textArea.value = currentSubject;
        document.body.appendChild(textArea);
        textArea.select();
        document.execCommand('copy');
        document.body.removeChild(textArea);
        showSuccess();
    }

    // DEZE FUNCTIE IS NU GEOPTIMALISEERD VOOR RICH TEXT
    function copyBody() {
        var preview = document.getElementById('preview-html');
        
        // Maak een selectie van de preview div (met alle linkjes en opmaak)
        var range = document.createRange();
        range.selectNodeContents(preview);
        var selection = window.getSelection();
        selection.removeAllRanges();
        selection.addRange(range);

        try {
            // Kopieer de geselecteerde HTML
            var success = document.execCommand('copy');
            if (success) {
                showSuccess();
            }
        } catch (err) {
            alert("Kopieerfout. Selecteer de tekst aub handmatig.");
        }
        
        // Haal de blauwe selectie weg na 1 seconde
        setTimeout(function() { selection.removeAllRanges(); }, 1000);
    }

    function showSuccess() {
        var m = document.getElementById('copy-msg');
        m.style.display = 'block';
        setTimeout(function(){ m.style.display='none'; }, 2500);
    }

    // Directe start voor iPad
    fillSelect("sender-name", TRAINERS, "Marja");
    fillSelect("trainer-mon", TRAINERS, "Marja");
    fillSelect("loc-mon", LOCATIES, "CKC");
    fillSelect("trainer-wed", TRAINERS, "Clemens");
    fillSelect("loc-wed", LOCATIES, "Kinderboerderij Oosterheem, Weidemolen");
    fillSelect("trainer-sat-1", TRAINERS, "Clemens");
    fillSelect("trainer-sat-2", ["", "Ankie", "Clemens", "Marja", "Liesbeth"], "Marja");
    fillSelect("loc-sat", LOCATIES, "Ilion");

    generateContent();
</script>

</body>
</html>
