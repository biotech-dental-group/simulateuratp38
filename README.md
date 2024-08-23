<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulateur de Financement ATP38</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-image: url('https://zupimages.net/up/24/34/b392.png');
            background-size: cover;
            background-position: center;
            color: #fff;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 20px auto;
            background: rgba(0, 0, 0, 0.8);
            padding: 20px;
            box-shadow: 0 0 20px rgba(255, 255, 255, 0.5); /* Ombre blanche en mode halo */
            border-radius: 10px;
            text-align: center;
        }
        h1 {
            color: #fff;
            font-family: Arial, sans-serif;
            font-weight: bold;
            margin-bottom: 5px;
        }
        h2 {
            color: #fff;
            font-family: Arial, sans-serif;
            font-weight: lighter;
            font-size: 14px;
            margin-top: 0;
            margin-bottom: 20px; /* Ajout d'espace en dessous */
        }
        label {
            margin-bottom: 10px;
            display: block;
            text-align: left;
        }
        select, input[type="number"] {
            width: calc(50% - 10px);
            padding: 10px;
            margin-bottom: 20px;
            border-radius: 5px;
            border: 1px solid #ddd;
            background-color: #333;
            color: #fff;
        }
        .form-group {
            display: flex;
            justify-content: space-between;
        }
        input[type="radio"] {
            margin-right: 10px;
        }
        .result {
            background: rgba(0, 0, 0, 0.9);
            padding: 15px;
            border-radius: 5px;
            text-align: center;
            margin-top: 20px;
            font-size: 16px;
        }
        .result p {
            margin: 10px 0;
        }
        button {
            background-color: #9B3189;
            color: #fff;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
        }
        button:hover {
            background-color: #6A0080;
        }
        .logo {
            margin-bottom: 30px; /* Ajout d'espace en dessous */
            margin-top: 40px; /* Plus d'espace au-dessus */
            text-align: center;
        }
        .logo img {
            width: 80%; /* Réduction de la taille du logo */
            max-width: 200px;
        }
        .note {
            font-size: 12px;
            color: #ccc;
            margin-top: 20px;
            text-align: left;
        }
    </style>
</head>
<body>

<div class="logo">
    <img src="https://zupimages.net/up/24/34/6fra.png" alt="ATP38">
</div>

<div class="container">
    <h1>Simulateur financement ATP38</h1>
    <h2>TARIF 20 900 €TTC SOIT 17 416,66 €HT</h2>
    <form id="simulatorForm">
        <div class="form-group">
            <label for="months">Nombre de mois:</label>
            <select id="months" onchange="toggleInsurance()">
                <option value="12">12 mois</option>
                <option value="36">36 mois</option>
                <option value="48">48 mois</option>
                <option value="60">60 mois</option>
            </select>
        </div>

        <div class="form-group">
            <label for="sessionPrice">Prix moyen séance ATP38 (€):</label>
            <input type="number" id="sessionPrice" placeholder="xx €" min="0">
        </div>

        <label>Avec ou sans assurance:</label>
        <div class="form-group">
            <label><input type="radio" name="insurance" value="with" id="insuranceWith"> Avec assurance</label>
            <label><input type="radio" name="insurance" value="without" checked> Sans assurance</label>
        </div>

        <button type="button" onclick="calculate()">Calculer</button>
    </form>

    <div class="result" id="result">
        <!-- Les résultats du calcul apparaîtront ici -->
    </div>
    
    <p class="note" id="note">
        <!-- Note apparaîtra ici -->
    </p>
</div>

<script>
    // Données fixes pour les paiements mensuels
    const data = {
        12: { with: 1742, without: 1742 },
        36: { with: 654, without: 634 },
        48: { with: 511, without: 495 },
        60: { with: 426, without: 410 }
    };

    function toggleInsurance() {
        const months = document.getElementById('months').value;
        const insuranceWith = document.getElementById('insuranceWith');
        
        // Désactiver l'assurance si 12 mois est sélectionné
        if (months == "12") {
            insuranceWith.disabled = true;
            insuranceWith.checked = false; // Décocher l'option "Avec assurance"
            document.querySelector('input[name="insurance"][value="without"]').checked = true;
        } else {
            insuranceWith.disabled = false;
        }
    }

    function calculate() {
        // Récupérer les valeurs du formulaire
        const months = document.getElementById('months').value;
        const sessionPrice = parseFloat(document.getElementById('sessionPrice').value);
        const insurance = document.querySelector('input[name="insurance"]:checked').value;

        // Validation du prix de la séance
        if (isNaN(sessionPrice) || sessionPrice <= 0) {
            document.getElementById('result').innerHTML = `<p style="color: red;">Veuillez entrer un prix de séance valide.</p>`;
            return;
        }

        // Récupérer les données basées sur les options choisies
        const monthlyPayment = data[months][insurance];
        const sessionsNeededPerMonth = Math.ceil(monthlyPayment / sessionPrice); // Arrondir au nombre entier le plus proche supérieur
        const totalSessions = sessionsNeededPerMonth * months;

        // Afficher les résultats
        document.getElementById('result').innerHTML = `
            <p>Montant échéance: ${monthlyPayment} €</p>
            <p>Nombre de séances nécessaires par mois: ${sessionsNeededPerMonth}</p>
            <p>Nombre total de séances nécessaires: ${totalSessions}</p>
        `;

        // Ajouter la note explicative en bas
        document.getElementById('note').innerHTML = `
            * Tous les rdv patient de la journée peuvent donner lieu à la facturation d'une séance d'ATP38, avec une tarification pouvant différer en fonction de l'acte (implant, paro, orthodontie, extraction, etc.).
        `;
    }

    // Appel de la fonction lors du chargement de la page pour désactiver l'assurance si nécessaire
    document.addEventListener('DOMContentLoaded', toggleInsurance);
</script>

</body>
</html>
