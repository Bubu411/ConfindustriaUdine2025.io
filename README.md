<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calcolatore di Energia Compatto</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --secondary-color: #3498db;
            --accent-color: #e74c3c;
            --light-bg: #ecf0f1;
            --highlight: #f1c40f;
            --border-radius: 4px;
            --box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 10px;
            background-color: #f8f9fa;
            color: var(--primary-color);
            line-height: 1.4;
            font-size: 14px;
        }

        h2, h3 {
            color: var(--primary-color);
            border-bottom: 1px solid var(--secondary-color);
            padding-bottom: 5px;
            margin-top: 15px;
            margin-bottom: 10px;
            font-size: 16px;
        }

        .container {
            max-width: 800px;
            margin: 0 auto;
            padding: 10px;
            background-color: white;
            border-radius: var(--border-radius);
            box-shadow: var(--box-shadow);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin: 10px 0;
            box-shadow: var(--box-shadow);
            overflow: hidden;
            border-radius: var(--border-radius);
            font-size: 13px;
        }

        th, td {
            padding: 6px;
            text-align: center;
            border: 1px solid #ddd;
        }

        th {
            background-color: var(--secondary-color);
            color: white;
            font-weight: 600;
            font-size: 12px;
        }

        tr:nth-child(even) {
            background-color: #f2f2f2;
        }

        input, select {
            width: 100%;
            padding: 4px 6px;
            border: 1px solid #ddd;
            border-radius: 3px;
            font-size: 13px;
            transition: border-color 0.2s;
            box-sizing: border-box;
        }

        input:focus, select:focus {
            border-color: var(--secondary-color);
            outline: none;
            box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.2);
        }

        input[readonly] {
            background-color: #f9f9f9;
        }

        .highlight {
            background-color: var(--highlight);
            border-color: #e67e22;
        }

        .note {
            display: none;
            margin: 8px 0;
            padding: 8px;
            border-left: 3px solid var(--secondary-color);
            background-color: #f8f9fa;
            border-radius: 3px;
            box-shadow: var(--box-shadow);
            position: relative;
            max-width: 100%;
            font-size: 12px;
        }

        .kwh {
            color: var(--accent-color);
            font-weight: bold;
            background-color: #f9f9f9;
        }

        .informazioni {
            margin: 10px 0;
            padding: 8px;
            border-left: 3px solid var(--accent-color);
            background-color: #f8f9f9;
            border-radius: 3px;
            box-shadow: var(--box-shadow);
            display: none;
            font-size: 12px;
        }

        .evidenziato {
            color: var(--accent-color);
            font-weight: bold;
        }

        .calculator-row {
            background-color: white;
        }

        .calculator-row td {
            vertical-align: middle;
        }

        .reference-table th[rowspan], .reference-table th[colspan] {
            background-color: #2980b9;
            font-size: 11px;
        }

        .reference-table td {
            font-size: 12px;
        }
        
        /* Rimossi stili tab non più necessari */
    </style>
</head>

<body>
    <div class="container">
        <h2>Calcolatore di Energia</h2>
        <div id="notaCodice" class="note"></div>

        <table class="calculator-table">
            <tr>
                <th>Provenienza</th>
                <th>Codice</th>
                <th>Prodotto</th>
                <th>Quantità</th>
                <th>UdM</th>
                <th>kWh</th>
            </tr>
            <tr class="calculator-row">
                <td><input type="text" value="A - Nazionale" readonly></td>
                <td><input type="text" id="codiceFornitore" placeholder="Codice" oninput="gestisciEvidenzia(); calcolaKWh();"></td>
                <td>
                    <select id="prodotto" onchange="gestisciEvidenzia(); calcolaKWh();">
                        <option value="Gasolio">Gasolio</option>
                        <option value="GPL">GPL</option>
                        <option value="Olio Combustibile">Olio Combustibile</option>
                        <option value="Gas Naturale">Gas Naturale</option>
                        <option value="Carbone">Carbone</option>
                    </select>
                </td>
                <td><input type="number" id="quantita" oninput="gestisciEvidenzia(); calcolaKWh();"></td>
                <td>
                    <select id="unita" onchange="gestisciEvidenzia(); calcolaKWh();">
                        <option value="kg">Kg</option>
                        <option value="litro">L</option>
                        <option value="smc">Smc</option>
                    </select>
                </td>
                <td><input type="text" id="kwh" class="kwh" readonly></td>
            </tr>
        </table>
        
        <div id="informazioni" class="informazioni">
            <p id="informazioniContent" style="margin: 0;"></p>
        </div>

        <h3>Tabella dei calcoli</h3>
            <table class="reference-table">
                <tr>
                    <th rowspan="2">Prodotto</th>
                    <th colspan="2">Densità</th>
                    <th colspan="4">Potere Calorifico Inferiore</th>
                </tr>
                <tr>
                    <th>[kg/L]</th>
                    <th>[kg/smc]</th>
                    <th>[MJ/L-Nmc]</th>
                    <th>[kWh/kg]</th>
                    <th>[kWh/L]</th>
                    <th>[kWh/smc]</th>
                </tr>
                <tr>
                    <td>Gasolio</td>
                    <td>0,8325</td>
                    <td>-</td>
                    <td>36</td>
                    <td>12,01</td>
                    <td>10,00</td>
                    <td>-</td>
                </tr>
                <tr>
                    <td>GPL</td>
                    <td>0,530</td>
                    <td>-</td>
                    <td>24</td>
                    <td>12,58</td>
                    <td>6,67</td>
                    <td>-</td>
                </tr>
                <tr>
                    <td>Olio Combustibile</td>
                    <td>-</td>
                    <td>-</td>
                    <td>-</td>
                    <td>11,38</td>
                    <td>-</td>
                    <td>-</td>
                </tr>
                <tr>
                    <td>Gas Naturale</td>
                    <td>-</td>
                    <td>0,730</td>
                    <td>35,5</td>
                    <td>13,51</td>
                    <td>-</td>
                    <td>9,86</td>
                </tr>
            </table>
    </div>

    <script>
        let highlightColor = '#f1c40f';
        const informazioniText = {
            "Gasolio": "La quantità di prodotti energetici per <span class='evidenziato'>Gasolio</span> è ricavata dallo scarico del registro fiscale di c/s del deposito a servizio dell'impianto di produzione per la conversione in unità energetiche.",
            "GPL": "La quantità di prodotti energetici per <span class='evidenziato'>GPL</span> è ricavata dallo scarico del registro fiscale di c/s del deposito a servizio dell'impianto di produzione per la conversione in unità energetiche.",
            "Olio Combustibile": "La quantità di prodotti energetici per <span class='evidenziato'>Olio Combustibile</span> è ricavata dallo scarico del registro fiscale di c/s del deposito a servizio dell'impianto di produzione per la conversione in unità energetiche.",
            "Gas Naturale": "La quantità di prodotti energetici per <span class='evidenziato'>Gas Naturale</span> è ricavata dalla lettura dei misuratori ubicati nei POD a servizio della centrale e dal potere calorifico inferiore desunto dalla bolletta emessa dal venditore.",
            "Carbone": "La quantità di prodotti energetici per <span class='evidenziato'>Carbone</span> è ricavata dal peso netto riportato sulle bolle di consegna."
        };

        const notaCondizioni = {
            "Gasolio": "Indicare il codice di accisa o il codice ditta di ciascun deposito dal quale è effettuato approvvigionamento.",
            "GPL": "Indicare il codice di accisa o il codice ditta di ciascun deposito dal quale è effettuato approvvigionamento.",
            "Olio Combustibile": "Indicare il codice di accisa o il codice ditta di ciascun deposito dal quale è effettuato approvvigionamento.",
            "Gas Naturale": "In caso di generazione con gas naturale, indicare il codice accisa del venditore che ha proceduto alla fatturazione del gas naturale presso i PDR dell'officina.",
            "Carbone": "Indicare il codice ditta di ciascuna società fornitrice registrata ai sensi dell'art. 21, comma 6, del TUA. Qualora tale codice non sia noto al dichiarante, in alternativa può essere indicata la partita IVA della predetta società."
        };

        function calcolaKWh() {
            rimuoviEvidenziazioneCelle();

            let prodotto = document.getElementById("prodotto").value;
            let quantita = parseFloat(document.getElementById("quantita").value) || 0;
            let unita = document.getElementById("unita").value;
            let kWh = 0;

            const conversione = {
                "Gasolio": { "litro": 10.00, "kg": 12.01 },
                "GPL": { "litro": 6.67, "kg": 12.58 },
                "Olio Combustibile": { "kg": 11.38 },
                "Gas Naturale": { "smc": 9.86, "kg": 13.51 },
                "Carbone": { "kg": 24.00 }
            };

            // Calcolo kWh basato sull'unità scelta
            if (conversione[prodotto]) {
                if (unita === "kg" && conversione[prodotto]["kg"]) {
                    kWh = quantita * conversione[prodotto]["kg"];
                } else if (unita === "litro" && conversione[prodotto]["litro"]) {
                    kWh = quantita * conversione[prodotto]["litro"];
                } else if (unita === "smc" && conversione[prodotto]["smc"]) {
                    kWh = quantita * conversione[prodotto]["smc"];
                }
            }

            document.getElementById("kwh").value = kWh.toFixed(2);
            evidenziaCelleCalcolo(prodotto);
            mostraInformazioniAggiuntive(prodotto);
        }

        function rimuoviEvidenziazioneCelle() {
            const rows = document.querySelectorAll('.reference-table tr');
            rows.forEach(row => {
                for (let i = 1; i < row.cells.length; i++) {
                    row.cells[i].style.backgroundColor = '';
                }
            });
        }

        function mostraNota() {
            let prodotto = document.getElementById("prodotto").value;
            let notaSpecifica = `<strong>${prodotto}</strong>: ${notaCondizioni[prodotto]}`;

            let notaDiv = document.getElementById("notaCodice");
            notaDiv.innerHTML = notaSpecifica;
            notaDiv.style.display = "block";
        }

        function gestisciEvidenzia() {
            let codiceFornitore = document.getElementById("codiceFornitore").value;
            let quantita = parseFloat(document.getElementById("quantita").value);

            if (codiceFornitore) {
                document.getElementById("codiceFornitore").classList.add("highlight");
                mostraNota();
            } else {
                document.getElementById("codiceFornitore").classList.remove("highlight");
                document.getElementById("notaCodice").style.display = "none";
            }

            if (quantita > 0) {
                document.getElementById("quantita").classList.add("highlight");
                document.getElementById("unita").classList.add("highlight");
            } else {
                document.getElementById("quantita").classList.remove("highlight");
                document.getElementById("unita").classList.remove("highlight");
            }
        }

        function evidenziaCelleCalcolo(prodotto) {
            const rows = document.querySelectorAll('.reference-table tr');
            rows.forEach(row => {
                if (row.cells[0] && (row.cells[0].textContent === prodotto || row.cells[0].textContent === prodotto.substring(0, 7))) {
                    for (let i = 1; i < row.cells.length; i++) {
                        if (row.cells[i].textContent !== '-') {
                            row.cells[i].style.backgroundColor = highlightColor;
                        }
                    }
                }
            });
        }

        function mostraInformazioniAggiuntive(prodotto) {
            let informazioniDiv = document.getElementById("informazioni");
            let informazioniContent = document.getElementById("informazioniContent");

            let testoInformazioni = informazioniText[prodotto];
            informazioniContent.innerHTML = testoInformazioni;
            informazioniDiv.style.display = "block";
        }
        
        // Rimosso il codice di gestione dei tab perché non più necessario
    </script>
</body>
</html>
