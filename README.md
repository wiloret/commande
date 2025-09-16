<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Outil Devis Interactif Noret</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .collapsible-container .collapsible-content { display: none; }
        .collapsible-container.is-open > .collapsible-content { display: block; }
        .collapsible-container.is-open > .collapsible-trigger .chevron { transform: rotate(180deg); }
        .product-card { transition: all 0.3s ease-in-out; }
    </style>
</head>
<body class="bg-slate-100">

    <div class="flex h-screen">
        <!-- Sidebar -->
        <aside class="w-80 bg-white shadow-lg p-6 overflow-y-auto flex flex-col h-full">
            <div>
                <img src="https://www.noret.com/img/noret-logo-1599577348.jpg" alt="Noret Logo" class="h-12 w-auto mx-auto mb-6">
                <div class="space-y-4">
                    <div>
                        <h3 class="text-md font-bold text-gray-800 pb-2">1. Paramètres du document</h3>
                        <div class="space-y-3 bg-slate-50 p-4 rounded-lg">
                            <div class="flex items-center justify-between">
                                <label for="club-name-input" class="block text-sm font-medium text-gray-700">Nom du Club / Client</label>
                            </div>
                            <input type="text" id="club-name-input" class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm placeholder-gray-400 focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm" placeholder="Ex: Vélo Club Anjou">
                            
                             <div class="relative flex items-start">
                                <div class="flex h-5 items-center">
                                    <input id="custom-creation-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                </div>
                                <div class="ml-3 text-sm">
                                    <label for="custom-creation-check" class="font-medium text-gray-700">Création Personnalisée (TEAM)</label>
                                    <p class="text-gray-500 text-xs">Cochez pour un nouveau visuel (forfait maquette applicable).</p>
                                </div>
                            </div>
                            <div class="border-t border-gray-200 pt-3">
                                 <p class="text-xs text-center font-semibold text-gray-600 mb-2">Choisissez un mode :</p>
                                <div class="flex items-center justify-around gap-2">
                                     <div class="relative flex items-start">
                                        <div class="flex h-5 items-center">
                                            <input id="gamme-club-mode-check" name="mode-selection" type="radio" class="h-4 w-4 rounded-full border-gray-300 text-green-600 focus:ring-green-500">
                                        </div>
                                        <div class="ml-2 text-sm">
                                            <label for="gamme-club-mode-check" class="font-medium text-gray-700">Gamme Club</label>
                                        </div>
                                    </div>
                                    <div class="relative flex items-start">
                                        <div class="flex h-5 items-center">
                                            <input id="devis-mode-check" name="mode-selection" type="radio" class="h-4 w-4 rounded-full border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                        </div>
                                        <div class="ml-2 text-sm">
                                            <label for="devis-mode-check" class="font-medium text-gray-700">Devis</label>
                                        </div>
                                    </div>
                                </div>
                            </div>
                             <div id="maquette-link-container" class="hidden">
                                <label for="maquette-link-input" class="block text-sm font-medium text-gray-700 mt-2">Lien Maquette (Optionnel)</label>
                                <input type="text" id="maquette-link-input" class="mt-1 block w-full px-3 py-2 bg-white border border-gray-300 rounded-md shadow-sm" placeholder="https://...">
                            </div>
                        </div>
                    </div>
                    <div id="category-filters" class="border-t border-gray-200 pt-4">
                        <!-- Navigation will be injected here by JS -->
                    </div>
                </div>
            </div>
            <div class="mt-auto pt-6">
                <button id="general-tariff-btn" class="w-full bg-slate-700 hover:bg-slate-800 text-white font-bold py-2 px-4 rounded-lg text-sm">Voir le Tarif Général 2025/26</button>
            </div>
        </aside>

        <!-- Main Content -->
        <main class="flex-1 p-8 overflow-y-auto bg-slate-50">
            <div id="product-grid" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                 <div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500">
                    <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                    </svg>
                    <h3 class="mt-2 text-sm font-medium text-gray-900">Bienvenue sur l'outil interactif Noret</h3>
                    <p class="mt-1 text-sm text-gray-500">Utilisez le menu de gauche pour commencer à construire votre document.</p>
                  </div>
            </div>
        </main>
    </div>

    <!-- Summary Bar -->
    <div id="summary-bar" class="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200 p-4 shadow-2xl" style="display: none;">
        <!-- Summary will be injected here -->
    </div>
    
    <!-- Product Card Template -->
    <template id="product-card-template">
        <div class="product-card bg-white rounded-xl shadow-md hover:shadow-xl p-5 flex flex-col relative overflow-hidden">
            <span class="group-color-indicator absolute top-0 left-0 h-full w-1.5" style="display: none;"></span>
             <button class="remove-product-btn absolute top-2 right-2 text-gray-400 hover:text-red-500 rounded-full p-1 transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2"><path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12" /></svg>
            </button>
            <p class="product-category text-xs font-semibold text-indigo-500 uppercase tracking-wide"></p>
            <h4 class="product-name text-md font-bold text-gray-800 mt-1"></h4>
            <div class="product-links flex flex-col items-start mt-1"></div>
            <div class="product-group-info text-xs text-gray-500 mt-2 p-2 bg-slate-50 rounded-md" style="display: none;"></div>

            <div class="mt-auto pt-4">
                 <!-- Price Display Box (Container for different views) -->
                <div class="product-display-box bg-slate-50 p-4 rounded-lg">
                    <!-- View for DEVIS mode -->
                    <div class="single-price-view" style="display:none;">
                         <div class="flex items-center justify-between">
                            <div>
                                <label class="quantity-label text-sm font-medium text-gray-700 block">Quantité:</label>
                                <input type="number" min="0" class="quantity-input mt-1 w-24 rounded-md border-gray-300 shadow-sm focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50">
                            </div>
                            <div class="text-right">
                                <p class="text-xs text-gray-500">Prix U. TTC</p>
                                <p class="price-display text-2xl font-bold text-indigo-700">- €</p>
                            </div>
                        </div>
                        <div class="quantity-warning text-xs text-red-600 font-semibold mt-1" style="display: none;"></div>
                    </div>
                    <!-- View for PRESENTATION mode -->
                    <div class="tiers-price-view" style="display:none;">
                        <p class="text-sm font-semibold text-gray-700 mb-2 text-center">Grille Tarifaire TTC</p>
                        <div class="tiers-content text-xs"></div>
                    </div>
                    <!-- View for TEAM mode -->
                    <div class="team-price-view" style="display:none;">
                         <p class="text-sm font-semibold text-gray-700 mb-2 text-center">Tarifs Spécifiques TEAM</p>
                        <div class="team-tiers-content text-xs"></div>
                    </div>
                </div>

                <div class="product-options-container"></div>
            </div>
        </div>
    </template>

    <!-- Modals -->
    <div id="alert-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full items-center justify-center hidden">
      <div class="relative mx-auto p-5 border w-96 shadow-lg rounded-md bg-white">
        <div class="mt-3 text-center">
          <h3 id="alert-modal-title" class="text-lg leading-6 font-medium text-gray-900"></h3>
          <div class="mt-2 px-7 py-3"><p id="alert-modal-body" class="text-sm text-gray-500"></p></div>
          <div class="items-center px-4 py-3">
            <button id="alert-modal-close-btn" class="px-4 py-2 bg-indigo-500 text-white text-base font-medium rounded-md w-full shadow-sm hover:bg-indigo-600 focus:outline-none focus:ring-2 focus:ring-indigo-300">OK</button>
          </div>
        </div>
      </div>
    </div>
    
    <div id="conseil-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full items-center justify-center hidden">
      <div class="relative mx-auto p-5 border w-full max-w-lg shadow-lg rounded-md bg-white">
         <button id="conseil-modal-close-btn" class="absolute top-2 right-2 text-gray-400 hover:text-gray-600">&times;</button>
        <div class="mt-3">
          <h3 id="conseil-modal-title" class="text-lg leading-6 font-medium text-gray-900"></h3>
          <div id="conseil-modal-body" class="mt-4 prose prose-sm max-w-none"></div>
        </div>
      </div>
    </div>
    
    <div id="tariff-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full items-center justify-center hidden p-4">
      <div class="relative w-full max-w-7xl shadow-lg rounded-md bg-white flex flex-col h-full max-h-[90vh]">
         <div class="flex justify-between items-center p-4 border-b">
             <h3 class="text-xl leading-6 font-bold text-gray-900">Tarif Général 2025/2026 - Prix Publics TTC</h3>
             <button id="tariff-modal-close-btn" class="text-gray-400 hover:text-gray-600 text-3xl font-bold">&times;</button>
         </div>
         <div id="tariff-modal-body" class="p-5 overflow-auto"></div>
      </div>
    </div>
    
     <div id="pdf-selection-modal" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full items-center justify-center hidden p-4">
      <div class="relative w-full max-w-lg shadow-lg rounded-md bg-white flex flex-col">
         <div class="flex justify-between items-center p-4 border-b">
             <h3 class="text-lg leading-6 font-bold text-gray-900">Confirmer la sélection pour le PDF</h3>
         </div>
         <div class="p-5 overflow-auto max-h-96">
            <div class="flex justify-between mb-3">
                <button id="pdf-select-all-btn" class="text-xs text-indigo-600 hover:underline">Tout cocher</button>
                <button id="pdf-deselect-all-btn" class="text-xs text-indigo-600 hover:underline">Tout décocher</button>
            </div>
            <div id="pdf-selection-list" class="space-y-2"></div>
         </div>
         <div class="flex justify-end gap-3 p-4 border-t bg-gray-50">
             <button id="pdf-selection-cancel-btn" class="px-4 py-2 bg-gray-200 text-gray-800 text-sm font-medium rounded-md hover:bg-gray-300">Annuler</button>
             <button id="pdf-selection-confirm-btn" class="px-4 py-2 bg-green-600 text-white text-sm font-medium rounded-md hover:bg-green-700">Confirmer et Générer</button>
         </div>
      </div>
    </div>

<script>
    document.addEventListener('DOMContentLoaded', function () {
        const TVA_RATE = 0.20;
        const GRAPHIC_FEE_TTC = 150;
        const sizeData = {
            default: 'XS, S, M, L, XL, XXL, 3XL, 4XL, 5XL',
            ample: 'XS, S, M, L, XL, XXL, 3XL, 4XL, 5XL',
            aero: '3XS, XXS, XS, S, M, L, XL, XXL',
            enfant: '6 ans, 8 ans, 10 ans, 12 ans, 14 ans',
            largeHaut: 'S, M, L, XL, XXL, 3XL',
            chaussettes: '35-38, 39-42, 43-46',
            cuissardConfort: 'XXS, XS, S, M, L, XL, XXL, 3XL, 4XL, 5XL, 6XL'
        };
        const friendlyGroupNames = {
            maillotClassiqueMC: 'Maillot Classique MC',
            maillotMiSaisonML: 'Maillot Mi-saison ML',
            giletCoupeVent: 'Gilet Coupe-Vent',
            coupeVent: 'Coupe-Vent',
            vesteMiSaison: 'Veste Mi-Saison',
            vesteHiver: 'Veste Hiver',
            cuissardConfortLandscape: 'Cuissard Confort',
            cuissardAeroCervino: 'Cuissard AERO',
            corsaireConfortLandscape: 'Corsaire Confort',
            collantHiverConfortLandscape: 'Collant Hiver Confort',
            collantHiverAeroCervino: 'Collant Hiver AERO',
            maillotRunning: 'Maillot Running',
            maillotRunningHiver: 'Maillot Running Hiver',
            debardeurRunning: 'Débardeur Running',
        };

        const conseilCuissard = `<h4 class="font-bold text-gray-800">Comment choisir sa peau ?</h4><p class="mt-2 text-sm text-gray-600">Le choix de la peau est essentiel pour votre confort. Chaque type de peau est conçu pour une durée et une intensité d'effort spécifiques.</p><ul class="mt-2 list-disc list-inside text-sm space-y-1"><li><strong>Peau ENDURANCE 2.5 :</strong> Idéale pour les longues distances et les entraînements intensifs.</li><li><strong>Peau LANDSCAPE :</strong> Polyvalente, parfaite pour les sorties régulières et les compétitions.</li><li><strong>Peau CERVINO :</strong> Conçue pour la performance, offrant un excellent soutien pour les courses et les efforts courts et intenses.</li></ul><p class="mt-3 text-xs italic text-gray-500">Consultez les fiches techniques des peaux pour plus de détails sur leurs spécificités.</p>`;
        const confortWinterJacketTiers = [
            { quantity: 1, price: 153.00 }, { quantity: 2, price: 131.10 }, { quantity: 3, price: 109.25 },
            { quantity: 5, price: 87.40 }, { quantity: 15, price: 83.03 }, { quantity: 25, price: 80.41 },
            { quantity: 50, price: 78.66 }, { quantity: 80, price: 76.04 }, { quantity: 150, price: 74.30 }
        ];
    const thermiqueWinterJacketTiers = [
        { quantity: 1, price: 159.60 }, { quantity: 2, price: 136.80 }, { quantity: 3, price: 114.00 },
        { quantity: 5, price: 91.20 }, { quantity: 15, price: 86.64 }, { quantity: 25, price: 83.90 },
        { quantity: 50, price: 82.08 }, { quantity: 80, price: 79.34 }, { quantity: 150, price: 77.52 }
    ];
    const allAvailableProducts = [
        { name: 'MAILLOT CLASSIQUE HOMME CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:3206a4eb-2bde-4045-93de-eeef78bf61ff', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
        { name: 'MAILLOT CLASSIQUE FEMME CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
        { name: 'MAILLOT MIXTE CONFORT SANS MANCHE', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
        { name: 'MAILLOT MIXTE PERFORMANCE MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 89.25 },{ quantity: 2, price: 76.50 }, { quantity: 3, price: 63.75 }, { quantity: 5, price: 51.00 }, { quantity: 15, price: 48.45 }, { quantity: 25, price: 46.92 }, { quantity: 50, price: 45.90 }, { quantity: 80, price: 44.37 }, { quantity: 150, price: 43.35 } ] },
        { name: 'MAILLOT VTT/DESCENTE MIXTE CONFORT MC (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'largeHaut', hasOptions: false, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:fe1475d4-25df-4dc8-acf0-58e700b92792', pricingTiers: [ { quantity: 1, price: 77.70 }, { quantity: 2, price: 66.60 }, { quantity: 3, price: 55.50 }, { quantity: 5, price: 44.40 }, { quantity: 15, price: 42.18 }, { quantity: 25, price: 40.85 }, { quantity: 50, price: 39.96 }, { quantity: 80, price: 38.63 }, { quantity: 150, price: 37.74 } ] },
        { name: 'MAILLOT MIXTE AERO MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'aero', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:05f14843-0c71-4630-8791-ec044cf3d106', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 45.00 }, { quantity: 50, price: 44.10 }, { quantity: 80, price: 42.75 }, { quantity: 150, price: 41.88 } ] },
        { name: 'MAILLOT MI-SAISON HOMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:325cbd11-bb7f-4b21-92a0-beb5b76a4626', pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
        { name: 'MAILLOT MI-SAISON FEMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:bfb0ee58-6844-4ee3-a811-432beaea7517', pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
        { name: 'MAILLOT BMX MIXTE CONFORT ML (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'largeHaut', hasOptions: false, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:117f4f03-d6d5-4818-a87e-2491c1bdf8b0', pricingTiers: [ { quantity: 1, price: 88.20 }, { quantity: 2, price: 75.60 }, { quantity: 3, price: 63.00 }, { quantity: 5, price: 50.40 }, { quantity: 15, price: 47.88 }, { quantity: 25, price: 46.37 }, { quantity: 50, price: 45.36 }, { quantity: 80, price: 43.85 }, { quantity: 150, price: 42.84 } ] },
        { name: 'MAILLOT MI-SAISON MIXTE AERO ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'aero', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:b09fd90e-1362-4517-ba8f-952d4e81158b', pricingTiers: [ { quantity: 1, price: 107.10 }, { quantity: 2, price: 91.80 }, { quantity: 3, price: 76.50 }, { quantity: 5, price: 61.20 }, { quantity: 15, price: 58.14 }, { quantity: 25, price: 56.30 }, { quantity: 50, price: 55.08 }, { quantity: 80, price: 53.24 }, { quantity: 150, price: 52.02 } ] },
        { name: 'MAILLOT PLUIE MIXTE AERO MC (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', price: 85.20, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:05461d8f-709e-4ec2-9e7f-07cd60e10d74' },
        { name: 'MAILLOT PLUIE MIXTE AERO ML (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', price: 102.00, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:05461d8f-709e-4ec2-9e7f-07cd60e10d74' },
        { name: 'GILET COUPE-VENT LEGER MIXTE (vent et pluie fine, sans poche dos)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:e33e2526-748d-45b9-9caa-ade12d3a0059', pricingTiers: [ { quantity: 1, price: 72.45 }, { quantity: 2, price: 62.10 }, { quantity: 3, price: 51.75 }, { quantity: 5, price: 41.40 }, { quantity: 15, price: 39.33 }, { quantity: 25, price: 38.09 }, { quantity: 50, price: 37.26 }, { quantity: 80, price: 36.02 }, { quantity: 150, price: 35.19 } ] },
        { name: 'GILET COUPE-VENT MI-SAISON MIXTE (dos ajouré)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f155520b-2268-404b-9491-37b228a72ae7', pricingTiers: [ { quantity: 1, price: 91.35 }, { quantity: 2, price: 78.30 }, { quantity: 3, price: 65.25 }, { quantity: 5, price: 52.20 }, { quantity: 15, price: 49.59 }, { quantity: 25, price: 48.02 }, { quantity: 50, price: 46.98 }, { quantity: 80, price: 45.41 }, { quantity: 150, price: 44.37 } ] },
        { name: 'GILET COUPE-VENT HIVER MIXTE (tout membranné)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:d4ac0624-a655-4a27-b02d-2a00460fcd0b', pricingTiers: [ { quantity: 1, price: 97.65 }, { quantity: 2, price: 83.70 }, { quantity: 3, price: 69.75 }, { quantity: 5, price: 55.80 }, { quantity: 15, price: 53.01 }, { quantity: 25, price: 51.34 }, { quantity: 50, price: 50.22 }, { quantity: 80, price: 48.55 }, { quantity: 150, price: 47.43 } ] },
        { name: 'COUPE-VENT LEGER MIXTE CONFORT (vent et pluie fine)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:1c71ad46-5363-43b0-8051-ba153ccf7a53', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
        { name: 'COUPE-VENT LEGER DEPERLANT MIXTE CONFORT (avec membranne)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:a588b2ef-7669-4eb9-8b3d-b6fef039383e', pricingTiers: [ { quantity: 1, price: 128.10 }, { quantity: 2, price: 109.80 }, { quantity: 3, price: 91.50 }, { quantity: 5, price: 73.20 }, { quantity: 15, price: 69.54 }, { quantity: 25, price: 67.34 }, { quantity: 50, price: 65.88 }, { quantity: 80, price: 63.68 }, { quantity: 150, price: 62.22 } ] },
        { name: 'VESTE MI-SAISON HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:264e626b-2bc5-4781-8d39-57084c462a4c', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'VESTE MI-SAISON FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:689e6b68-d63d-415d-a691-10c602df3c82', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'VESTE MI-SAISON MIXTE CONFORT (membranne coupe-vent + mi-saison)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'VESTE MI-SAISON MIXTE CONFORT avec -6cm aux ML', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'VESTE HIVER HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:768a412f-2182-4ef4-927b-b3805ffe8761', pricingTiers: confortWinterJacketTiers },
        { name: 'VESTE HIVER FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:93317f52-8305-460f-bfc3-b2ac0ecb9a45', pricingTiers: confortWinterJacketTiers },
        { name: 'VESTE HIVER THERMIQUE HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:56ab2378-75b1-4760-86df-76c7ad3b66d8', pricingTiers: thermiqueWinterJacketTiers },
        { name: 'VESTE HIVER THERMIQUE FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiver', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:5326db9b-b63e-4920-8c55-4e8861d9e8b0', pricingTiers: thermiqueWinterJacketTiers },
        { name: 'CUISSARD A BRETELLES HOMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', sizeType: 'cuissardConfort', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:b7868a8f-5c52-498d-81dd-d26bb55c0aaa', chamoisInfo: { name: 'Peau LANDSCAPE', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:2d406e66-798f-44aa-bf83-5861224b93a3' }, pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ], conseil: conseilCuissard },
        { name: 'CUISSARD A BRETELLES FEMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', sizeType: 'cuissardConfort', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:b7868a8f-5c52-498d-81dd-d26bb55c0aaa', chamoisInfo: { name: 'Peau LANDSCAPE', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:8a00d7f0-ab38-4656-a786-77fd645606c0' }, pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ], conseil: conseilCuissard },
        { name: 'CUISSARD FEMME SANS BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', sizeType: 'cuissardConfort', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f2e878e2-fdb6-4536-b837-396b3489f99d', chamoisInfo: { name: 'Peau LANDSCAPE', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:8a00d7f0-ab38-4656-a786-77fd645606c0' }, pricingTiers: [ { quantity: 1, price: 117.60 }, { quantity: 2, price: 100.80 }, { quantity: 3, price: 84.00 }, { quantity: 5, price: 67.20 }, { quantity: 15, price: 63.84 }, { quantity: 25, price: 61.82 }, { quantity: 50, price: 60.48 }, { quantity: 80, price: 58.46 }, { quantity: 150, price: 57.12 }, ], conseil: conseilCuissard },
        { name: 'CUISSARD HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f1fc53c5-9a3f-4c0f-8b92-5bd05f764f48', chamoisInfo: { name: 'Peau CERVINO', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:9b6f6d83-7726-43ed-8c1b-d3c67c9e3c42' }, pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ], conseil: conseilCuissard },
        { name: 'CUISSARD FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f1fc53c5-9a3f-4c0f-8b92-5bd05f764f48', chamoisInfo: { name: 'Peau CERVINO', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:3cad08d0-732f-465a-8f34-8c61898e0579' }, pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ], conseil: conseilCuissard },
        { name: 'SHORT VTT FOND Peau ENDURANCE 2.5', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:3502afda-fc68-4fdf-ae4c-1847ef3c4c87', pricingTiers: [ { quantity: 1, price: 75.00 } ] },
        { name: 'CORSAIRE HOMME A BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:2fecf5d7-61eb-41af-938e-4e3fc7d72a25', pricingTiers: [ { quantity: 1, price: 79.20 }, { quantity: 2, price: 79.20 }, { quantity: 3, price: 79.20 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 } ] },
        { name: 'CORSAIRE FEMME SANS BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:876b9e39-98de-4726-8fc4-afd0bbf4f973', pricingTiers: [ { quantity: 1, price: 76.80 }, { quantity: 2, price: 76.80 }, { quantity: 3, price: 76.80 }, { quantity: 5, price: 73.00 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 } ] },
        { name: 'COLLANT HIVER A BRETELLES HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
        { name: 'COLLANT HIVER A BRETELLES FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
        { name: 'COLLANT HIVER FEMME SANS BRETELLES CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 134.40 }, { quantity: 2, price: 115.20 }, { quantity: 3, price: 96.00 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 }, ] },
        { name: 'COLLANT HIVER HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:1c051cec-30e2-45c9-84c5-6aaaf8b0a344', chamoisInfo: { name: 'Peau CERVINO', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:9b6f6d83-7726-43ed-8c1b-d3c67c9e3c42' }, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ], conseil: conseilCuissard },
        { name: 'COLLANT HIVER FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:1c051cec-30e2-45c9-84c5-6aaaf8b0a344', chamoisInfo: { name: 'Peau CERVINO', link: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:3cad08d0-732f-465a-8f34-8c61898e0579' }, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ], conseil: conseilCuissard },
        { name: 'COLLANT MIXTE ECHAUFFEMENT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:7b607522-6b8e-45ea-b4ca-ca4f404bfffd', pricingTiers: [ { quantity: 1, price: 98.70 }, { quantity: 2, price: 84.60 }, { quantity: 3, price: 70.50 }, { quantity: 5, price: 56.40 }, { quantity: 15, price: 53.58 }, { quantity: 25, price: 51.89 }, { quantity: 50, price: 50.76 }, { quantity: 80, price: 49.07 }, { quantity: 150, price: 47.94 }, ] },
        { name: 'COMBINAISON ROUTE MANCHES COURTES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:232568c6-45d3-47ce-9843-c17ac546d87e', pricingTiers: [{ quantity: 1, price: 115.20 }] },
        { name: 'COMBINAISON ROUTE MANCHES COURTES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:232568c6-45d3-47ce-9843-c17ac546d87e', pricingTiers: [{ quantity: 1, price: 115.20 }] },
        { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES HOMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
        { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES FEMME AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
        { name: 'COMBINAISON CYCLOCROSS/VTT MANCHES LONGUES MIXTE AERO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:81a79859-bb47-49d7-8491-031f08e80556', pricingTiers: [{ quantity: 1, price: 115.20 }] },
        { name: 'MAILLOT RUNNING MIXTE MANCHES COURTES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
        { name: 'DEBARDEUR RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'debardeurRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
        { name: 'MAILLOT RUNNING HIVER HOMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
        { name: 'MAILLOT RUNNING HIVER FEMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
        { name: 'COUPE-VENT LEGER MIXTE CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:1c71ad46-5363-43b0-8051-ba153ccf7a53', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
        { name: 'VESTE MI-SAISON HOMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:264e626b-2bc5-4781-8d39-57084c462a4c', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'VESTE MI-SAISON FEMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:689e6b68-d63d-415d-a691-10c602df3c82', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
        { name: 'GILET COUPE-VENT LEGER MIXTE (vent et pluie fine, sans poche dos)', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:e33e2526-748d-45b9-9caa-ade12d3a0059', pricingTiers: [ { quantity: 1, price: 72.45 }, { quantity: 2, price: 62.10 }, { quantity: 3, price: 51.75 }, { quantity: 5, price: 41.40 }, { quantity: 15, price: 39.33 }, { quantity: 25, price: 38.09 }, { quantity: 50, price: 37.26 }, { quantity: 80, price: 36.02 }, { quantity: 150, price: 35.19 } ] },
        { name: 'GILET COUPE-VENT MI-SAISON MIXTE (dos ajouré)', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f155520b-2268-404b-9491-37b228a72ae7', pricingTiers: [ { quantity: 1, price: 91.35 }, { quantity: 2, price: 78.30 }, { quantity: 3, price: 65.25 }, { quantity: 5, price: 52.20 }, { quantity: 15, price: 49.59 }, { quantity: 25, price: 48.02 }, { quantity: 50, price: 46.98 }, { quantity: 80, price: 45.41 }, { quantity: 150, price: 44.37 } ] },
        { name: 'GILET COUPE-VENT HIVER MIXTE (tout membranné)', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:d4ac0624-a655-4a27-b02d-2a00460fcd0b', pricingTiers: [ { quantity: 1, price: 97.65 }, { quantity: 2, price: 83.70 }, { quantity: 3, price: 69.75 }, { quantity: 5, price: 55.80 }, { quantity: 15, price: 53.01 }, { quantity: 25, price: 51.34 }, { quantity: 50, price: 50.22 }, { quantity: 80, price: 48.55 }, { quantity: 150, price: 47.43 } ] },
        { name: 'GILET RUNNING COUPE-VENT LEGER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 72.45 }, { quantity: 2, price: 62.10 }, { quantity: 3, price: 51.75 }, { quantity: 5, price: 41.40 }, { quantity: 15, price: 39.33 }, { quantity: 25, price: 38.09 }, { quantity: 50, price: 37.26 }, { quantity: 80, price: 36.02 }, { quantity: 150, price: 35.19 } ] },
        { name: 'GILET RUNNING COUPE-VENT MI-SAISON MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 91.35 }, { quantity: 2, price: 78.30 }, { quantity: 3, price: 65.25 }, { quantity: 5, price: 52.20 }, { quantity: 15, price: 49.59 }, { quantity: 25, price: 48.02 }, { quantity: 50, price: 46.98 }, { quantity: 80, price: 45.41 }, { quantity: 150, price: 44.37 } ] },
        { name: 'GILET RUNNING COUPE-VENT HIVER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 97.65 }, { quantity: 2, price: 83.70 }, { quantity: 3, price: 69.75 }, { quantity: 5, price: 55.80 }, { quantity: 15, price: 53.01 }, { quantity: 25, price: 51.34 }, { quantity: 50, price: 50.22 }, { quantity: 80, price: 48.55 }, { quantity: 150, price: 47.43 } ] },
        { name: 'SHORT RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
        { name: 'SPRINTER RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 85.80 }, { quantity: 2, price: 73.50 }, { quantity: 3, price: 61.25 }, { quantity: 5, price: 49.00 }, { quantity: 15, price: 46.55 }, { quantity: 25, price: 45.08 }, { quantity: 50, price: 44.10 }, { quantity: 80, price: 42.64 }, { quantity: 150, price: 41.65 } ] },
        { name: 'COLLANT RUNNING HIVER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 } ] },
        { name: 'TRIFONCTION AERO HOMME', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', sizeType: 'aero', price: 172.80 },
        { name: 'TRIFONCTION AERO FEMME', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', sizeType: 'aero', price: 172.80 },
        { name: 'option', name: 'POCHE DOS ZIPPEE', category: 'option', fixedPriceTTC: 7.20 },
        { name: 'option', name: 'FLOCAGE NOM PRENOM', category: 'option', fixedPriceTTC: 6.00 },
        { name: 'option', name: 'Ajustement Longueur +4cm', category: 'option', optionGroup: 'length', fixedPriceTTC: 6.00 },
        { name: 'option', name: 'Ajustement Longueur -4cm', category: 'option', optionGroup: 'length', fixedPriceTTC: 6.00 },
        { name: 'option', name: 'Ajustement Longueur -6cm', category: 'option', optionGroup: 'length', fixedPriceTTC: 6.00 },
        { name: 'GANTS ETE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 50, pricingTiers: [{ quantity: 50, price: 28.80 }] },
        { name: 'GANTS MI-SAISON', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 50, pricingTiers: [{ quantity: 50, price: 33.60 }] },
        { name: 'GANTS HIVER', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 50, pricingTiers: [{ quantity: 50, price: 36.00 }] },
        { name: 'MANCHETTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 20, pricingTiers: [{ quantity: 20, price: 27.60 }, { quantity: 50, price: 24.00 }] },
        { name: 'JAMBIERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 20, pricingTiers: [{ quantity: 20, price: 36.00 }, { quantity: 50, price: 30.00 }] },
        { name: 'GENOUILLERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 20, pricingTiers: [{ quantity: 20, price: 30.00 }, { quantity: 50, price: 27.60 }] },
        { name: 'CHAUSSETTES AERO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType: 'chaussettes', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 26.40 }, { quantity: 50, price: 22.80 }] },
        { name: 'CHAUSSETTES SUBLIMEES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType: 'chaussettes', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 18.00 }, { quantity: 50, price: 15.60 }] },
        { name: 'SUR-CHAUSSURES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 20, pricingTiers: [{ quantity: 20, price: 33.60 }, { quantity: 50, price: 28.80 }] },
        { name: 'CASQUETTE CYCLISTE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 21.60 }, { quantity: 50, price: 18.00 }] },
        { name: 'BONNET SOUS CASQUE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 21.60 }, { quantity: 50, price: 18.00 }] },
        { name: 'TOUR DE COU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 18.00 }, { quantity: 50, price: 14.40 }] },
        { name: 'MUSETE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', minQuantity: 30, pricingTiers: [{ quantity: 30, price: 18.00 }, { quantity: 50, price: 15.60 }] },
        { name: 'BIDONS 500ML', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', price: 6.60, colors: ["TRANSPARENT", "BLANC", "NOIR", "JAUNE FLUO", "ORANGE", "ROUGE", "VERT", "BLEU"]},
        { name: 'BIDONS 750ML', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', price: 7.80, colors: ["TRANSPARENT", "BLANC", "NOIR", "JAUNE FLUO", "ORANGE", "ROUGE", "VERT", "BLEU"]},
        { name: 'CHAUSSETTES MI-HAUTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'chaussettes', price: 17, colors: ["NOIR", "BLANC", "BEIGE", "BRETON PUR BEURRE"]},
        { name: 'MAILLOT ENFANT PERFORMANCE MC', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 42.00, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:9a9217a7-81e3-4ec1-a717-a066b06219b2' },
        { name: 'MAILLOT BMX ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 45.60, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:c12f4446-f720-4088-ac99-2cd6686dafd5' },
        { name: 'MAILLOT MI-SAISON ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 45.60, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:ec6bb336-9d52-46a9-bb70-745d41f2d996' },
        { name: 'GILET COUPE-VENT LEGER ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 38.40, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:b0f1b1ce-aac5-49b3-9609-ba0c705f723e' },
        { name: 'VESTE HIVER ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 84.00, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:f906cd17-b2d7-42da-b938-11a95a5e1cae' },
        { name: 'CUISSARD ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 42.00, techSheetLink: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:3279f8a5-33cf-4b91-a778-a6036f037ff8' },
        { name: 'COLLANT HIVER ENFANT SUBLIME CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 60.00 },
        { name: 'COLLANT ENFANT VELOURS ECHAUFFEMENT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', sizeType: 'enfant', hasOptions: false, price: 49.80 },
        { name: 'MAILLOT RUNNING ENFANT MANCHES COURTES', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', sizeType: 'enfant', hasOptions: false, price: 30.00 },
        { name: 'DEBARDEUR RUNNING ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', sizeType: 'enfant', hasOptions: false, price: 30.00 },
    ];
        const productMap = new Map(allAvailableProducts.map(p => [p.name, p]));
        
        // --- DOM ELEMENTS ---
        const clubNameInput = document.getElementById('club-name-input');
        const customCreationCheck = document.getElementById('custom-creation-check');
        const devisModeCheck = document.getElementById('devis-mode-check');
        const gammeClubModeCheck = document.getElementById('gamme-club-mode-check');
        const maquetteLinkContainer = document.getElementById('maquette-link-container');
        const maquetteLinkInput = document.getElementById('maquette-link-input');
        const categoryFiltersContainer = document.getElementById('category-filters');
        const productGrid = document.getElementById('product-grid');
        const summaryBar = document.getElementById('summary-bar');
        const alertModal = document.getElementById('alert-modal');
        let alertModalCloseBtn = document.getElementById('alert-modal-close-btn');
        const alertModalTitle = document.getElementById('alert-modal-title');
        const alertModalBody = document.getElementById('alert-modal-body');
        const conseilModal = document.getElementById('conseil-modal');
        const conseilModalCloseBtn = document.getElementById('conseil-modal-close-btn');
        const conseilModalTitle = document.getElementById('conseil-modal-title');
        const conseilModalBody = document.getElementById('conseil-modal-body');
        const tariffModal = document.getElementById('tariff-modal');
        const tariffModalCloseBtn = document.getElementById('tariff-modal-close-btn');
        const tariffModalBody = document.getElementById('tariff-modal-body');
        const generalTariffBtn = document.getElementById('general-tariff-btn');
        
        const pdfSelectionModal = document.getElementById('pdf-selection-modal');
        const pdfSelectionList = document.getElementById('pdf-selection-list');
        const pdfSelectionCancelBtn = document.getElementById('pdf-selection-cancel-btn');
        const pdfSelectAllBtn = document.getElementById('pdf-select-all-btn');
        const pdfDeselectAllBtn = document.getElementById('pdf-deselect-all-btn');
        const pdfSelectionConfirmBtn = document.getElementById('pdf-selection-confirm-btn');

        // =================================================================================
        // --- APPLICATION STATE ---
        // =================================================================================
        let quoteState = {
            items: {}, 
            clubName: '',
            customCreation: false,
            devisMode: false,
            gammeClubMode: false,
            maquetteLink: ''
        };
        let pdfConfirmCallback = null;
        
        const groupColors = {};
        (() => {
            const colors = ['#fecaca', '#fed7aa', '#fef08a', '#d9f99d', '#bfdbfe', '#e9d5ff', '#fbcfe8', '#fce7f3', '#ccfbf1', '#dcfce7'];
            let colorIndex = 0;
            allAvailableProducts.forEach(product => {
                if (product.pricingGroup && !groupColors[product.pricingGroup]) {
                    groupColors[product.pricingGroup] = colors[colorIndex % colors.length];
                    colorIndex++;
                }
            });
        })();

        // =================================================================================
        // --- UTILITY & HELPER FUNCTIONS ---
        // =================================================================================
        const sanitizeForId = (str) => str.replace(/[^a-zA-Z0-9-_]/g, '_');

        const getPriceForQuantity = (pricingTiers, totalQuantity) => {
            if (!pricingTiers || pricingTiers.length === 0) return 0;
            return pricingTiers.slice().reverse().find(tier => totalQuantity >= tier.quantity)?.price || pricingTiers[0].price;
        };
        
        const getTierRangeString = (pricingTiers, totalQuantity) => {
            if (!pricingTiers || pricingTiers.length === 0 || totalQuantity === 0) return '';
            const applicableTier = pricingTiers.slice().reverse().find(tier => totalQuantity >= tier.quantity) || pricingTiers[0];
            const tierIndex = pricingTiers.findIndex(tier => tier.quantity === applicableTier.quantity);
            if (tierIndex === -1) return '';
            const lowerBound = applicableTier.quantity;
            if (tierIndex === pricingTiers.length - 1) return `(Tarif pour ${lowerBound}+ pièces)`;
            const upperBound = pricingTiers[tierIndex + 1].quantity - 1;
            return lowerBound === upperBound ? `(Tarif pour ${lowerBound} pièces)` : `(Tarif pour ${lowerBound}-${upperBound} pièces)`;
        };

        const formatTiersForDisplay = (tiers) => {
             if (!tiers) return '<p>Prix unique</p>';
             let html = '<div class="space-y-1">';
             tiers.forEach((tier, index) => {
                 const nextTier = tiers[index + 1];
                 let range;
                 if (!nextTier) {
                     range = `${tier.quantity}+`;
                 } else {
                     range = tier.quantity === nextTier.quantity - 1 ? `${tier.quantity}` : `${tier.quantity}-${nextTier.quantity - 1}`;
                 }
                 html += `<div class="flex justify-between"><span class="font-medium text-gray-600">${range} p:</span><span class="font-bold text-indigo-700">${tier.price.toFixed(2)} €</span></div>`;
             });
             html += '</div>';
             return html;
        };

        const formatTiersForPdf = (tiers) => {
            if (!tiers) return 'Prix unique';
            let text = '';
            tiers.forEach((tier, index) => {
                const nextTier = tiers[index + 1];
                let range;
                 if (!nextTier) {
                     range = `${tier.quantity}+`;
                 } else {
                     range = tier.quantity === nextTier.quantity - 1 ? `${tier.quantity}` : `${tier.quantity}-${nextTier.quantity - 1}`;
                 }
                text += `${range} pièces: ${tier.price.toFixed(2)} €\n`;
            });
            return text.trim();
        };

        const summarizeSizes = (sizeString) => {
            if (!sizeString || typeof sizeString !== 'string') return '';
            const sizes = sizeString.split(', ');
            if (sizes.length <= 2) {
                return `Taille(s) : ${sizeString}`;
            }
            return `Tailles : de ${sizes[0]} à ${sizes[sizes.length - 1]}`;
        };

        const formatTeamTiersForDisplay = (product) => {
            const price5 = getPriceForQuantity(product.pricingTiers, 5);
            const price15 = getPriceForQuantity(product.pricingTiers, 15);
            let html = '<div class="space-y-1">';
            html += `<div class="flex justify-between"><span class="font-medium text-gray-600">5-14 p:</span><span class="font-bold text-green-700">${price5.toFixed(2)} €</span></div>`;
            html += `<div class="flex justify-between"><span class="font-medium text-gray-600">15-19 p:</span><span class="font-bold text-green-700">${price15.toFixed(2)} €</span></div>`;
            html += '</div>';
            return html;
        };

        // --- MODAL MANAGEMENT ---
        function showAlert(message, title = 'Information', onOk = null) {
            alertModalTitle.textContent = title;
            alertModalBody.textContent = message;
            alertModal.classList.remove('hidden');
            alertModal.classList.add('flex');
            const newOkBtn = alertModalCloseBtn.cloneNode(true);
            alertModalCloseBtn.parentNode.replaceChild(newOkBtn, alertModalCloseBtn);
            alertModalCloseBtn = newOkBtn;
            alertModalCloseBtn.onclick = () => {
                hideAlert();
                if (onOk) onOk();
            };
        }

        const hideAlert = () => alertModal.classList.add('hidden');
        const showConseilModal = (title, content) => {
            conseilModalTitle.textContent = title;
            conseilModalBody.innerHTML = content;
            conseilModal.classList.remove('hidden');
            conseilModal.classList.add('flex');
        };
        const hideConseilModal = () => conseilModal.classList.add('hidden');
        const showTariffModal = () => {
            renderGeneralTariffTable();
            tariffModal.classList.remove('hidden');
            tariffModal.classList.add('flex');
        };
        const hideTariffModal = () => tariffModal.classList.add('hidden');
        
        // =================================================================================
        // --- CORE LOGIC & UI UPDATES ---
        // =================================================================================
        
        function updateUI() {
            const { devisMode, gammeClubMode, customCreation } = quoteState;
            
            document.querySelectorAll('#product-grid .product-card').forEach(card => {
                const displayBox = card.querySelector('.product-display-box');
                const singlePriceView = card.querySelector('.single-price-view');
                const tiersPriceView = card.querySelector('.tiers-price-view');
                const teamPriceView = card.querySelector('.team-price-view');
                const productName = card.querySelector('.remove-product-btn').dataset.productName;
                const product = productMap.get(productName);
                if (!product) return;
                
                // Hide all views first
                displayBox.style.display = 'block';
                singlePriceView.style.display = 'none';
                tiersPriceView.style.display = 'none';
                teamPriceView.style.display = 'none';
                
                const quantityElements = [card.querySelector('.quantity-label'), card.querySelector('.quantity-input'), card.querySelector('.quantity-warning')];
                quantityElements.forEach(el => el.style.display = 'none');


                if (gammeClubMode && customCreation) {
                    teamPriceView.style.display = 'block';
                    teamPriceView.querySelector('.team-tiers-content').innerHTML = formatTeamTiersForDisplay(product);
                } else if (gammeClubMode) {
                    displayBox.style.display = 'none';
                } else if (devisMode) {
                    quantityElements.forEach(el => el.style.display = 'block');
                    singlePriceView.style.display = 'block';
                } else { // Presentation Mode
                    tiersPriceView.style.display = 'block';
                    tiersPriceView.querySelector('.tiers-content').innerHTML = formatTiersForDisplay(product.pricingTiers || [{ quantity: 1, price: product.price }]);
                }
            });

            if (devisMode) {
                updateAllCalculationsAndRenderDetailedSummary();
            } else if (gammeClubMode) {
                renderGammeClubSummary();
            } else {
                renderPresentationSummary();
            }
        }

        function updateAllCalculationsAndRenderDetailedSummary() {
            if (!quoteState.devisMode) return;

            const groupQuantities = {};
            let totalNonAccessoryQty = 0;

            for (const itemName in quoteState.items) {
                const product = productMap.get(itemName);
                const item = quoteState.items[itemName];
                if (product && item.quantity > 0) {
                    if (product.type !== 'accessoire') {
                        totalNonAccessoryQty += item.quantity;
                    }
                    if (product.pricingGroup) {
                        groupQuantities[product.pricingGroup] = (groupQuantities[product.pricingGroup] || 0) + item.quantity;
                    }
                }
            }

            for (const itemName in quoteState.items) {
                const product = productMap.get(itemName);
                const item = quoteState.items[itemName];
                if (!product) continue;
                
                // Normal pricing logic always applies for calculation
                const pricingQty = product.pricingGroup ? groupQuantities[product.pricingGroup] : item.quantity;

                let unitPrice = product.price || getPriceForQuantity(product.pricingTiers, pricingQty) || 0;
                
                item.options.forEach(optionName => {
                    const optionProduct = productMap.get(optionName);
                    if (optionProduct) {
                        unitPrice += optionProduct.fixedPriceTTC || getPriceForQuantity(optionProduct.pricingTiers, pricingQty) || 0;
                    }
                });
                
                const priceDisplay = document.querySelector(`#product-card-${sanitizeForId(itemName)} .price-display`);
                if (priceDisplay) {
                     priceDisplay.textContent = item.quantity > 0 ? `${unitPrice.toFixed(2)} €` : '- €';
                }

                const warningDiv = document.querySelector(`#product-card-${sanitizeForId(itemName)} .quantity-warning`);
                if (warningDiv) {
                    warningDiv.style.display = (product.minQuantity && item.quantity > 0 && item.quantity < product.minQuantity) ? 'block' : 'none';
                    if (warningDiv.style.display === 'block') warningDiv.textContent = `Min. ${product.minQuantity} pièces requis.`;
                }
            }
            
            renderDetailedSummaryBar(groupQuantities);
        }

        function renderDetailedSummaryBar(groupQuantities) {
            summaryBar.style.display = 'block';
            if (Object.values(quoteState.items).every(item => item.quantity === 0)) {
                 summaryBar.innerHTML = `<div class="max-w-6xl mx-auto text-center text-gray-500"><p class="font-semibold text-indigo-700">Mode Devis Actif</p><p>Saisissez des quantités pour commencer le calcul.</p></div>`;
                 return;
            }

            let subtotalTTC = 0, totalNonAccessoryQty = 0, totalItems = 0;
            for (const itemName in quoteState.items) {
                const item = quoteState.items[itemName];
                const product = productMap.get(itemName);
                if (!product || item.quantity === 0) continue;

                totalItems += item.quantity;
                if (product.type !== 'accessoire') totalNonAccessoryQty += item.quantity;
                
                const pricingQty = product.pricingGroup ? groupQuantities[product.pricingGroup] : item.quantity;
                let unitPrice = product.price || getPriceForQuantity(product.pricingTiers, pricingQty) || 0;
                item.options.forEach(optName => {
                    const optProduct = productMap.get(optName);
                    if (optProduct) unitPrice += optProduct.fixedPriceTTC || getPriceForQuantity(optProduct.pricingTiers, pricingQty) || 0;
                });
                subtotalTTC += unitPrice * item.quantity;
            }
            
            const subtotalHT = subtotalTTC / (1 + TVA_RATE);
            let shippingHT = 0;
            if (subtotalHT > 2000) shippingHT = 0; else if (subtotalHT > 1000) shippingHT = 14; else if (subtotalHT > 500) shippingHT = 12; else if (subtotalHT > 0) shippingHT = 9.50;
            const shippingTTC = shippingHT * (1 + TVA_RATE);
            const graphicFeeTTC = (quoteState.customCreation && totalNonAccessoryQty > 0 && totalNonAccessoryQty < 20) ? GRAPHIC_FEE_TTC : 0;
            const graphicFeeHT = graphicFeeTTC / (1 + TVA_RATE);
            const grandTotalHT = subtotalHT + shippingHT + graphicFeeHT;
            const grandTotalTTC = subtotalTTC + shippingTTC + graphicFeeTTC;

            summaryBar.innerHTML = `
                <div class="max-w-6xl mx-auto grid grid-cols-1 md:grid-cols-3 gap-4 items-center">
                    <div class="md:col-span-2 space-y-2">
                        <div class="grid grid-cols-2 gap-x-4 text-sm">
                            <div>
                                <div class="flex justify-between"><span>Sous-total HT</span><span class="font-semibold">${subtotalHT.toFixed(2)} €</span></div>
                                <div class="flex justify-between"><span>Frais de port HT</span><span class="font-semibold">${shippingHT.toFixed(2)} €</span></div>
                                ${graphicFeeHT > 0 ? `<div class="flex justify-between text-red-600"><span>Forfait Création HT</span><span class="font-semibold">${graphicFeeHT.toFixed(2)} €</span></div>` : ''}
                                <div class="flex justify-between font-bold border-t pt-1 mt-1"><span>Total HT</span><span>${grandTotalHT.toFixed(2)} €</span></div>
                            </div>
                            <div>
                                <div class="flex justify-between"><span>Sous-total TTC</span><span class="font-semibold">${subtotalTTC.toFixed(2)} €</span></div>
                                <div class="flex justify-between"><span>Frais de port TTC</span><span class="font-semibold">${shippingTTC.toFixed(2)} €</span></div>
                                ${graphicFeeTTC > 0 ? `<div class="flex justify-between text-red-600"><span>Forfait Création TTC</span><span class="font-semibold">${graphicFeeTTC.toFixed(2)} €</span></div>` : ''}
                                <div class="flex justify-between font-bold border-t pt-1 mt-1 text-indigo-600"><span>Total TTC</span><span>${grandTotalTTC.toFixed(2)} €</span></div>
                            </div>
                        </div>
                    </div>
                    <div class="flex items-center justify-around md:justify-end gap-4">
                        <div class="text-center">
                             <div class="text-3xl font-extrabold text-indigo-600">${totalItems}</div>
                             <div class="text-xs text-gray-500 uppercase">Articles</div>
                        </div>
                        <div class="flex flex-col gap-2">
                            <button id="email-devis-btn" class="bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-lg text-sm">Envoyer par Mail</button>
                            <button id="export-devis-pdf-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded-lg text-sm">Exporter PDF</button>
                            <button id="reset-summary-btn" class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-lg text-sm">Réinitialiser</button>
                        </div>
                    </div>
                </div>`;
        }
        
        function renderPresentationSummary() {
            const itemCount = Object.keys(quoteState.items).length;
            if (itemCount === 0) {
                summaryBar.style.display = 'none';
                return;
            }
            summaryBar.style.display = 'block';
            summaryBar.innerHTML = `
                 <div class="max-w-6xl mx-auto flex justify-between items-center">
                    <span class="text-gray-700 font-semibold">Mode Présentation : ${itemCount} article${itemCount > 1 ? 's' : ''} sélectionné${itemCount > 1 ? 's' : ''}</span>
                    <div class="flex items-center gap-2">
                        <button id="export-devis-pdf-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded-lg text-sm">Exporter la Sélection en PDF</button>
                        <button id="reset-summary-btn" class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-lg text-sm">Réinitialiser</button>
                    </div>
                </div>`;
        }

        function renderGammeClubSummary() {
            const itemCount = Object.keys(quoteState.items).length;
            if (itemCount === 0) {
                summaryBar.style.display = 'none';
                return;
            }
            summaryBar.style.display = 'block';
            summaryBar.innerHTML = `
                 <div class="max-w-6xl mx-auto flex justify-between items-center">
                    <span class="text-gray-700 font-semibold">Mode Gamme Club : ${itemCount} article${itemCount > 1 ? 's' : ''} sélectionné${itemCount > 1 ? 's' : ''}</span>
                    <div class="flex items-center gap-2">
                        <button id="export-devis-pdf-btn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded-lg text-sm">Exporter la Gamme en PDF</button>
                        <button id="reset-summary-btn" class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-lg text-sm">Réinitialiser</button>
                    </div>
                </div>`;
        }
        
        // =================================================================================
        // --- DOM MANIPULATION & RENDERING ---
        // =================================================================================

        const addItemToQuote = (productName) => {
            if (quoteState.items[productName]) {
                return false; // Not added, already exists
            }
            quoteState.items[productName] = { quantity: 0, options: new Set() };
            return true; // Added successfully
        };

        const removeItemFromQuote = (productName) => {
            delete quoteState.items[productName];
        };

        const createAndAppendProductCard = (productName) => {
            const sanitizedId = sanitizeForId(productName);
            document.getElementById('product-placeholder')?.remove();

            const product = productMap.get(productName);
            if (!product) return;

            const template = document.getElementById('product-card-template');
            const cardClone = template.content.cloneNode(true);
            const cardElement = cardClone.querySelector('.product-card');
            
            cardElement.id = `product-card-${sanitizedId}`;
            cardElement.querySelector('.product-category').textContent = `${product.category} / ${product.subtype}`;
            cardElement.querySelector('.product-name').textContent = product.name;
            
            cardElement.querySelector('.remove-product-btn').dataset.productName = product.name;
            const quantityInput = cardElement.querySelector('.quantity-input');
            quantityInput.id = `qty-${sanitizedId}`;
            quantityInput.dataset.productName = product.name;
            cardElement.querySelector('.quantity-label').htmlFor = `qty-${sanitizedId}`;

            const linksContainer = cardElement.querySelector('.product-links');
            let linksHtml = `<a href="${product.techSheetLink || '#'}" ${product.techSheetLink ? 'target="_blank"' : ''} class="tech-sheet-link inline-flex items-center gap-1 text-xs text-indigo-600 hover:text-indigo-800 hover:underline mt-1">Voir la fiche technique <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" /></svg></a>`;
            if (product.chamoisInfo) {
                linksHtml += `<a href="${product.chamoisInfo.link}" target="_blank" class="chamois-link inline-flex items-center gap-1 text-xs text-green-600 hover:text-green-800 hover:underline mt-1">Fiche technique ${product.chamoisInfo.name} <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5"><path stroke-linecap="round" stroke-linejoin="round" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" /></svg></a>`;
            }
            linksContainer.innerHTML = linksHtml;
            
            if (product.pricingGroup) {
                const groupName = friendlyGroupNames[product.pricingGroup] || product.pricingGroup;
                const groupColor = groupColors[product.pricingGroup];
                cardElement.querySelector('.product-group-info').innerHTML = `<span>Prix groupé : <strong>${groupName}</strong></span>`;
                cardElement.querySelector('.product-group-info').style.display = 'block';
                const indicator = cardElement.querySelector('.group-color-indicator');
                indicator.style.backgroundColor = groupColor;
                indicator.title = `Article groupable : ${groupName}`;
                indicator.style.display = 'block';
            }

            let optionsHtml = '';
            const isLongSleeved = product.type === 'haut' && (product.name.includes('ML') || product.name.includes('MANCHES LONGUES'));
            const normalOptions = (product.hasOptions !== false && product.type !== 'accessoire' && !product.isCuissardOrCollant) ? allAvailableProducts.filter(p => p.category === 'option' && !p.optionGroup && !(product.excludedOptions || []).includes(p.name)) : [];
            const lengthOptions = (product.isCuissardOrCollant || isLongSleeved) ? allAvailableProducts.filter(p => p.optionGroup === 'length') : [];

            if (normalOptions.length > 0 || lengthOptions.length > 0) {
                optionsHtml = '<div class="mt-4 pt-4 border-t border-slate-200"><h5 class="text-sm font-semibold text-gray-600 mb-2">Options disponibles :</h5><div class="space-y-2">';
                optionsHtml += normalOptions.map(opt => `<div class="flex items-center"><input id="option-${sanitizedId}-${sanitizeForId(opt.name)}" type="checkbox" data-product-name="${product.name}" data-option-name="${opt.name}" class="option-checkbox h-4 w-4 rounded border-gray-300 text-indigo-600"><label for="option-${sanitizedId}-${sanitizeForId(opt.name)}" class="ml-2 block text-sm text-gray-700">${opt.name}</label></div>`).join('');
                if (lengthOptions.length > 0) {
                    optionsHtml += '<div class="pt-2"><p class="text-xs font-medium text-gray-500 mb-1">Ajustement Longueur :</p><div class="flex flex-wrap gap-2">';
                    optionsHtml += lengthOptions.map(opt => `<div class="flex items-center"><input id="option-${sanitizedId}-${sanitizeForId(opt.name)}" type="radio" name="length-option-${sanitizedId}" data-product-name="${product.name}" data-option-name="${opt.name}" class="option-radio h-4 w-4 border-gray-300 text-indigo-600"><label for="option-${sanitizedId}-${sanitizeForId(opt.name)}" class="ml-2 block text-sm text-gray-700">${opt.name.replace('Ajustement Longueur ', '')}</label></div>`).join('');
                    optionsHtml += '</div></div>';
                }
                optionsHtml += '</div></div>';
                cardElement.querySelector('.product-options-container').innerHTML = optionsHtml;
            }

            productGrid.appendChild(cardClone);
            cardElement.scrollIntoView({ behavior: 'smooth', block: 'center' });
        };

        const addProductToGrid = (productName) => {
            const sanitizedId = sanitizeForId(productName);
            const wasAdded = addItemToQuote(productName);
            
            if (wasAdded) {
                createAndAppendProductCard(productName);
                updateUI();
            } else {
                const existingCard = document.querySelector(`#product-card-${sanitizedId}`);
                if (existingCard) {
                    existingCard.scrollIntoView({ behavior: 'smooth', block: 'center' });
                    existingCard.classList.add('scale-105', 'shadow-2xl');
                    setTimeout(() => existingCard.classList.remove('scale-105', 'shadow-2xl'), 1000);
                }
            }
        };

        const renderSidebarNavigation = () => {
            const container = document.getElementById('category-filters');
            const isSelectionMode = quoteState.gammeClubMode || quoteState.devisMode;
            const categoryOrder = ['CYCLISME', 'RUNNING', 'ENFANTS', 'ACCESSOIRES_PERSONNALISES', 'ACCESSOIRES_PERMANENTS'];
            const categoryTitles = { CYCLISME: 'Cyclisme', RUNNING: 'Running', ENFANTS: 'Gamme Enfants', ACCESSOIRES_PERSONNALISES: 'Accessoires Personnalisés', ACCESSOIRES_PERMANENTS: 'Accessoires Permanents' };
            
            let html = `<h3 class="text-md font-bold text-gray-800 pb-2">2. Choisissez les articles</h3><div class="collapsible-container w-full text-left">`;
            
            const triggerContent = isSelectionMode
            ? `
                <div class="flex items-center gap-3">
                    <input type="checkbox" id="select-all-gamme-check" class="h-5 w-5 rounded border-gray-400 text-green-600 focus:ring-green-500">
                    <label for="select-all-gamme-check" class="text-base font-semibold text-gray-800 cursor-pointer">Toutes les gammes</label>
                </div>
            `
            : `<span>Toutes les gammes</span>`;

            html += `
                <div class="collapsible-trigger cursor-pointer text-base font-semibold text-gray-800 flex justify-between items-center hover:text-indigo-600 p-2.5 rounded-md bg-white shadow-sm">
                    ${triggerContent}
                    <svg class="chevron h-4 w-4 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 8.25l-7.5 7.5-7.5-7.5" /></svg>
                </div>
                <div class="collapsible-content pl-2 pt-2">`;

            categoryOrder.forEach(catKey => {
                let productsToRender, hasSubtypes = true;
                if (catKey === 'ACCESSOIRES_PERSONNALISES') {
                    productsToRender = allAvailableProducts.filter(p => p.subtype === 'ACCESSOIRES PERSONNALISÉS');
                    hasSubtypes = false;
                } else if (catKey === 'ACCESSOIRES_PERMANENTS') {
                    productsToRender = allAvailableProducts.filter(p => p.subtype === 'ACCESSOIRES PERMANENTS');
                    hasSubtypes = false;
                } else {
                    productsToRender = allAvailableProducts.filter(p => p.category === catKey);
                }

                const groupedBySubtype = productsToRender.reduce((acc, p) => {
                    const subtype = p.subtype || 'default';
                    if (!acc[subtype]) acc[subtype] = [];
                    acc[subtype].push(p);
                    return acc;
                }, {});

                html += `<div class="collapsible-container w-full text-left"><h4 class="collapsible-trigger cursor-pointer text-md font-bold text-gray-600 mb-1 flex justify-between items-center hover:text-indigo-600 p-2 rounded-md"><span>${categoryTitles[catKey]}</span><svg class="chevron h-4 w-4 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 8.25l-7.5 7.5-7.5-7.5" /></svg></h4><div class="collapsible-content pl-3">`;
                
                const renderProductButton = (product) => {
                    const colorDot = groupColors[product.pricingGroup] ? `<span class="inline-block w-2 h-2 rounded-full mr-2" style="background-color: ${groupColors[product.pricingGroup]};"></span>` : '<span class="inline-block w-2 h-2 mr-2"></span>';
                    if (isSelectionMode) {
                        const isChecked = quoteState.items[product.name] ? 'checked' : '';
                        return `<div class="flex items-center p-1.5"><input type="checkbox" id="select-check-${sanitizeForId(product.name)}" ${isChecked} data-product-name="${product.name}" class="product-select-check h-4 w-4 rounded border-gray-400 text-green-600 focus:ring-green-500"><label for="select-check-${sanitizeForId(product.name)}" class="ml-2 text-xs font-medium text-gray-700 hover:text-green-800 cursor-pointer">${product.name}</label></div>`;
                    } else {
                        return `<button class="add-product-btn w-full text-left px-2 py-1.5 text-xs font-medium rounded-md transition text-gray-700 hover:bg-indigo-100 hover:text-indigo-800 flex items-center" data-product-name="${product.name}">${colorDot}<span>${product.name}</span></button>`;
                    }
                };

                if (hasSubtypes) {
                    Object.keys(groupedBySubtype).sort().forEach(subtypeKey => {
                        html += `<div class="collapsible-container w-full text-left"><h5 class="collapsible-trigger cursor-pointer text-sm font-semibold text-gray-500 flex justify-between items-center hover:text-indigo-600 p-1.5 rounded-md"><span>${subtypeKey}</span><svg class="chevron h-4 w-4 transition-transform" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="2.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M19.5 8.25l-7.5 7.5-7.5-7.5" /></svg></h5><div class="collapsible-content pl-4 py-1">`;
                        html += groupedBySubtype[subtypeKey].map(renderProductButton).join('');
                        html += `</div></div>`;
                    });
                } else {
                    html += productsToRender.map(renderProductButton).join('');
                }
                html += `</div></div>`;
            });
            html += `</div></div>`;
            container.innerHTML = html;
        };

        const renderGeneralTariffTable = () => {
             const quantityHeaders = ['1', '2', '3-4', '5-14', '15-24', '25-49', '50-79', '80-149', '150+'];
             const quantitiesForCalc = [1, 2, 3, 5, 15, 25, 50, 80, 150];
             let headerHtml = quantityHeaders.map(q => `<th scope="col" class="px-4 py-3 text-center">${q}</th>`).join('');

             let html = `<table class="w-full text-sm text-left text-gray-500"><thead class="text-xs text-gray-700 uppercase bg-gray-100 sticky top-0"><tr><th scope="col" class="px-6 py-3">Article</th>${headerHtml}</tr></thead><tbody>`;

             const products = allAvailableProducts.filter(p => p.category !== 'option');
             const options = allAvailableProducts.filter(p => p.category === 'option');
             
             const renderProductRow = (product) => {
                 const color = groupColors[product.pricingGroup];
                 const rowStyle = color ? `style="background-color: ${color}80"` : '';
                 const rowClass = color ? '' : 'bg-white';
                 let rowHtml = `<tr class="border-b hover:brightness-95 transition-all ${rowClass}" ${rowStyle}><td class="px-6 py-4 font-medium text-gray-900">${product.name}</td>`;
                 
                 if (product.subtype === 'ACCESSOIRES PERSONNALISÉS' && product.minQuantity >= 50) {
                     const prices = product.pricingTiers.map(t => `<strong>${t.quantity}+:</strong> ${t.price.toFixed(2)}€`).join('<span class="mx-2 text-gray-300">|</span>');
                     rowHtml += `<td class="px-6 py-4 text-center text-xs" colspan="${quantitiesForCalc.length}">${prices}</td>`;
                 } else if (product.subtype === 'ACCESSOIRES PERSONNALISÉS' && product.pricingTiers) {
                     const prices = product.pricingTiers.map(t => `<strong>${t.quantity}+:</strong> ${t.price.toFixed(2)}€`).join('<span class="mx-2 text-gray-300">|</span>');
                     rowHtml += `<td class="px-6 py-4 text-center text-xs" colspan="${quantitiesForCalc.length}">${prices}</td>`;
                 } else if (product.price || product.fixedPriceTTC) {
                     const priceStr = `${(product.price || product.fixedPriceTTC).toFixed(2)} €`;
                     rowHtml += `<td class="px-6 py-4 text-center" colspan="${quantitiesForCalc.length}">${priceStr}</td>`;
                 } else if (product.pricingTiers) {
                     quantitiesForCalc.forEach(qty => {
                         const price = getPriceForQuantity(product.pricingTiers, qty);
                         rowHtml += `<td class="px-4 py-4 text-center">${price.toFixed(2)} €</td>`;
                     });
                 } else {
                      rowHtml += `<td class="px-6 py-4 text-center" colspan="${quantitiesForCalc.length}">N/A</td>`;
                 }
                 rowHtml += `</tr>`;
                 return rowHtml;
             };

             const cyclingSubtypeOrder = ['Maillots Manches Courtes', 'Maillots Manches Longues', 'Essentiels et Vestes', 'Cuissards Courts', 'Corsaires/Collants', 'Combinaisons'];
             const runningSubtypeOrder = ['Hauts', 'Bas', 'Trifonctions'];

             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">Cyclisme</td></tr>`;
             const cyclingProducts = products.filter(p => p.category === 'CYCLISME');
             const cyclingGrouped = cyclingProducts.reduce((acc, p) => { if (!acc[p.subtype]) acc[p.subtype] = []; acc[p.subtype].push(p); return acc; }, {});
             cyclingSubtypeOrder.forEach(subtypeKey => {
                 if (cyclingGrouped[subtypeKey]) {
                     html += `<tr class="bg-gray-100"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-semibold text-gray-700">${subtypeKey}</td></tr>`;
                     cyclingGrouped[subtypeKey].forEach(product => { html += renderProductRow(product); });
                 }
             });

             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">Running</td></tr>`;
             const runningProducts = products.filter(p => p.category === 'RUNNING');
             const runningGrouped = runningProducts.reduce((acc, p) => { if (!acc[p.subtype]) acc[p.subtype] = []; acc[p.subtype].push(p); return acc; }, {});
              runningSubtypeOrder.forEach(subtypeKey => {
                 if(runningGrouped[subtypeKey]) {
                     html += `<tr class="bg-gray-100"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-semibold text-gray-700">${subtypeKey}</td></tr>`;
                     runningGrouped[subtypeKey].forEach(product => { html += renderProductRow(product); });
                 }
             });

             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">Gamme Enfants</td></tr>`;
             products.filter(p => p.category === 'ENFANTS').forEach(product => { html += renderProductRow(product); });
             
             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">Accessoires Personnalisés</td></tr>`;
             products.filter(p => p.subtype === 'ACCESSOIRES PERSONNALISÉS').forEach(product => { html += renderProductRow(product); });

             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">Accessoires Permanents</td></tr>`;
             products.filter(p => p.subtype === 'ACCESSOIRES PERMANENTS').forEach(product => { html += renderProductRow(product); });

             html += `<tr class="bg-gray-200"><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-2 font-bold text-gray-800">OPTIONS</td></tr>`;
             html += `<tr><td colspan="${quantitiesForCalc.length + 1}" class="px-6 py-1 text-xs text-center italic text-gray-600 bg-gray-50">Applicable sur les articles d'une même catégorie et à ajouter au coût de l'article.</td></tr>`;
             options.forEach(option => { html += renderProductRow(option); });

             html += `</tbody></table>`;
             tariffModalBody.innerHTML = html;
        };
        
        // =================================================================================
        // --- EVENT HANDLERS & INITIALIZATION ---
        // =================================================================================
        
        categoryFiltersContainer.addEventListener('click', (e) => {
            if (e.target.id === 'select-all-gamme-check' || (e.target.tagName === 'LABEL' && e.target.htmlFor === 'select-all-gamme-check')) {
                return;
            }

            const trigger = e.target.closest('.collapsible-trigger');
            if (trigger) {
                const parentContainer = trigger.parentElement;
                const siblings = [...parentContainer.parentElement.children].filter(child => child !== parentContainer && child.classList.contains('collapsible-container'));
                siblings.forEach(sibling => sibling.classList.remove('is-open'));
                parentContainer.classList.toggle('is-open');
            }
            const productBtn = e.target.closest('.add-product-btn');
            if (productBtn) {
                addProductToGrid(productBtn.dataset.productName);
            }
        });

        categoryFiltersContainer.addEventListener('change', e => {
            if (e.target.id === 'select-all-gamme-check') {
                const isChecked = e.target.checked;
                const allCheckboxes = categoryFiltersContainer.querySelectorAll('.product-select-check');
                allCheckboxes.forEach(checkbox => {
                    if (checkbox.checked !== isChecked) {
                        checkbox.checked = isChecked;
                        checkbox.dispatchEvent(new Event('change', { bubbles: true }));
                    }
                });
            } else if (e.target.classList.contains('product-select-check')) {
                const productName = e.target.dataset.productName;
                if (e.target.checked) {
                    if (quoteState.gammeClubMode) {
                        addItemToQuote(productName);
                        updateUI();
                    } else { // Devis mode
                        addProductToGrid(productName);
                    }
                } else {
                    removeItemFromQuote(productName);
                    const sanitizedId = sanitizeForId(productName);
                    document.getElementById(`product-card-${sanitizedId}`)?.remove();
                    if (Object.keys(quoteState.items).length === 0) {
                         productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500"><svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1"><path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg><h3 class="mt-2 text-sm font-medium text-gray-900">La sélection est vide</h3><p class="mt-1 text-sm text-gray-500">Utilisez le menu de gauche pour ajouter des articles.</p></div>`;
                    }
                    updateUI();
                }
            }
        });

        productGrid.addEventListener('click', (e) => {
            const removeBtn = e.target.closest('.remove-product-btn');
            if (removeBtn) {
                const productName = removeBtn.dataset.productName;
                removeItemFromQuote(productName);
                document.getElementById(`product-card-${sanitizeForId(productName)}`)?.remove();
                
                const checkbox = document.getElementById(`select-check-${sanitizeForId(productName)}`);
                if(checkbox) checkbox.checked = false;

                if (Object.keys(quoteState.items).length === 0) {
                    productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500"><svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1"><path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg><h3 class="mt-2 text-sm font-medium text-gray-900">La sélection est vide</h3><p class="mt-1 text-sm text-gray-500">Utilisez le menu de gauche pour ajouter des articles.</p></div>`;
                }
                updateUI();
            }
            const techLink = e.target.closest('.tech-sheet-link');
            if (techLink && techLink.getAttribute('href') === '#') {
                e.preventDefault();
                showAlert('Lien vers la fiche technique non disponible pour cet article.');
            }
        });

        productGrid.addEventListener('input', (e) => {
            if (e.target.classList.contains('quantity-input')) {
                const productName = e.target.dataset.productName;
                const quantity = parseInt(e.target.value, 10) || 0;
                if (quoteState.items[productName]) {
                    quoteState.items[productName].quantity = quantity;
                    updateAllCalculationsAndRenderDetailedSummary();
                }
            }
        });

        productGrid.addEventListener('change', (e) => {
            if (e.target.classList.contains('option-checkbox') || e.target.classList.contains('option-radio')) {
                const { productName, optionName } = e.target.dataset;
                if (quoteState.items[productName]) {
                    if (e.target.type === 'radio') {
                         const group = e.target.name;
                         document.querySelectorAll(`input[name="${group}"]`).forEach(radio => {
                             if(radio !== e.target) quoteState.items[productName].options.delete(radio.dataset.optionName);
                         });
                    }
                    if (e.target.checked) quoteState.items[productName].options.add(optionName);
                    else quoteState.items[productName].options.delete(optionName);
                    
                    if (quoteState.devisMode) updateAllCalculationsAndRenderDetailedSummary();
                }
            }
        });

        gammeClubModeCheck.addEventListener('change', (e) => {
            quoteState.gammeClubMode = e.target.checked;
            if (quoteState.gammeClubMode) {
                quoteState.devisMode = false;
                devisModeCheck.checked = false;
                maquetteLinkContainer.style.display = 'block';
                productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500">
                     <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1">
                       <path stroke-linecap="round" stroke-linejoin="round" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                     </svg>
                     <h3 class="mt-2 text-sm font-medium text-gray-900">Mode Gamme Club</h3>
                     <p class="mt-1 text-sm text-gray-500">Cochez les articles dans le menu de gauche pour composer votre gamme. La sélection apparaîtra dans le PDF.</p>
                   </div>`;
            } else {
                maquetteLinkContainer.style.display = 'none';
                productGrid.innerHTML = '';
                const itemsToRender = Object.keys(quoteState.items);
                if (itemsToRender.length > 0) {
                    itemsToRender.forEach(createAndAppendProductCard);
                } else {
                     productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500"><svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1"><path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg><h3 class="mt-2 text-sm font-medium text-gray-900">La sélection est vide</h3><p class="mt-1 text-sm text-gray-500">Utilisez le menu de gauche pour ajouter des articles.</p></div>`;
                }
            }
            renderSidebarNavigation();
            updateUI();
        });
        
        devisModeCheck.addEventListener('change', (e) => {
            quoteState.devisMode = e.target.checked;
            if (quoteState.devisMode) {
                quoteState.gammeClubMode = false;
                gammeClubModeCheck.checked = false;
                maquetteLinkContainer.style.display = 'none';
                 productGrid.innerHTML = ''; // Clear grid
                const itemsToRender = Object.keys(quoteState.items);
                if (itemsToRender.length > 0) {
                    itemsToRender.forEach(createAndAppendProductCard);
                } else {
                     productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500"><svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1"><path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg><h3 class="mt-2 text-sm font-medium text-gray-900">Mode Devis</h3><p class="mt-1 text-sm text-gray-500">Cochez des articles à gauche pour les ajouter au devis.</p></div>`;
                }
            }
            renderSidebarNavigation();
            updateUI();
        });


        const modeRadios = document.querySelectorAll('input[name="mode-selection"]');
        modeRadios.forEach(radio => {
            radio.addEventListener('change', (e) => {
                const isGamme = e.target.id === 'gamme-club-mode-check' && e.target.checked;
                const isDevis = e.target.id === 'devis-mode-check' && e.target.checked;

                quoteState.gammeClubMode = isGamme;
                quoteState.devisMode = isDevis;
                maquetteLinkContainer.style.display = isGamme ? 'block' : 'none';

                productGrid.innerHTML = ''; // Always clear the grid on mode change

                if (isGamme) {
                     productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500">
                         <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1">
                           <path stroke-linecap="round" stroke-linejoin="round" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                         </svg>
                         <h3 class="mt-2 text-sm font-medium text-gray-900">Mode Gamme Club</h3>
                         <p class="mt-1 text-sm text-gray-500">Cochez les articles dans le menu de gauche pour composer votre gamme.</p>
                       </div>`;
                } else { // Devis or Presentation mode
                    const itemsToRender = Object.keys(quoteState.items);
                    if (itemsToRender.length > 0) {
                        itemsToRender.forEach(createAndAppendProductCard);
                    } else {
                        const placeholderTitle = isDevis ? "Mode Devis" : "Mode Présentation";
                        const placeholderText = isDevis ? "Cochez des articles à gauche pour les ajouter au devis." : "Utilisez le menu de gauche pour commencer à construire votre document.";
                         productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500">
                             <svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1">
                               <path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                             </svg>
                             <h3 class="mt-2 text-sm font-medium text-gray-900">${placeholderTitle}</h3>
                             <p class="mt-1 text-sm text-gray-500">${placeholderText}</p>
                           </div>`;
                    }
                }
                
                renderSidebarNavigation();
                updateUI();
            });
        });


        customCreationCheck.addEventListener('change', (e) => {
            quoteState.customCreation = e.target.checked;
             updateUI();
        });
        clubNameInput.addEventListener('input', (e) => quoteState.clubName = e.target.value);
        maquetteLinkInput.addEventListener('input', (e) => quoteState.maquetteLink = e.target.value);
        
        summaryBar.addEventListener('click', (e) => {
            const targetId = e.target.id;
            if (targetId === 'reset-summary-btn') {
                quoteState.items = {};
                productGrid.innerHTML = `<div id="product-placeholder" class="col-span-full text-center py-24 text-gray-500"><svg xmlns="http://www.w3.org/2000/svg" class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1"><path stroke-linecap="round" stroke-linejoin="round" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" /></svg><h3 class="mt-2 text-sm font-medium text-gray-900">La sélection est vide</h3><p class="mt-1 text-sm text-gray-500">Utilisez le menu de gauche pour ajouter des articles.</p></div>`;
                renderSidebarNavigation();
                updateUI();
            } else if (targetId === 'export-devis-pdf-btn') {
                showPdfSelectionModal((selectedItems) => {
                    generatePdf(selectedItems);
                });
            } else if (targetId === 'email-devis-btn') {
                if (!quoteState.clubName) return showAlert("Veuillez saisir un nom de club avant d'envoyer un e-mail.", "Action Requise");
                showPdfSelectionModal((selectedItems) => {
                    const success = generatePdf(selectedItems);
                    if (success) {
                        let docType = 'présentation tarifaire';
                        if (quoteState.devisMode) docType = 'devis';
                        if (quoteState.gammeClubMode) docType = 'présentation de la gamme club';
                        const subject = `Votre ${docType} Noret pour ${quoteState.clubName}`;
                        const body = `Bonjour,\n\nJe vous envoie ci-joint le document suite à votre demande.\nN’hésitez pas à me dire si vous avez des questions ou si vous souhaitez que l’on ajuste certains points, je suis à votre disposition pour en discuter.\nEn espérant que cette proposition corresponde à vos attentes,\n\nje vous souhaite une belle journée et à très bientôt.\nSportivement,\n\nWilfrid`;
                        showAlert(
                            "Le document PDF a été généré et téléchargé. Veuillez le joindre manuellement à l'e-mail qui va s'ouvrir.", "PDF Généré",
                            () => { window.location.href = `mailto:?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`; }
                        );
                    }
                });
            }
        });

        [conseilModal, alertModal, tariffModal].forEach(modal => {
            modal.addEventListener('click', (e) => { if (e.target === modal) modal.classList.add('hidden'); });
        });
        conseilModalCloseBtn.addEventListener('click', hideConseilModal);
        tariffModalCloseBtn.addEventListener('click', hideTariffModal);
        generalTariffBtn.addEventListener('click', showTariffModal);
        alertModalCloseBtn.addEventListener('click', hideAlert);
        
        pdfSelectionCancelBtn.addEventListener('click', () => pdfSelectionModal.classList.add('hidden'));
        pdfSelectAllBtn.addEventListener('click', () => pdfSelectionList.querySelectorAll('.pdf-item-check').forEach(cb => cb.checked = true));
        pdfDeselectAllBtn.addEventListener('click', () => pdfSelectionList.querySelectorAll('.pdf-item-check').forEach(cb => cb.checked = false));
        pdfSelectionConfirmBtn.addEventListener('click', () => {
            const selectedItems = Array.from(pdfSelectionList.querySelectorAll('.pdf-item-check:checked')).map(cb => cb.dataset.itemName);
            if (selectedItems.length === 0) {
                showAlert("Veuillez sélectionner au moins un article pour le PDF.", "Aucune sélection");
                return;
            }
            if (pdfConfirmCallback) {
                pdfConfirmCallback(selectedItems);
            }
            pdfSelectionModal.classList.add('hidden');
            pdfConfirmCallback = null;
        });
        
        // =================================================================================
        // --- EXPORT & EMAIL LOGIC ---
        // =================================================================================

        const showPdfSelectionModal = (onConfirm) => {
            if (Object.keys(quoteState.items).length === 0) {
                showAlert("Veuillez ajouter des articles avant d'exporter.", "Exportation Impossible");
                return;
            }
            pdfConfirmCallback = onConfirm;
            pdfSelectionList.innerHTML = Object.keys(quoteState.items).sort().map(itemName => `
                <div class="flex items-center p-1">
                    <input type="checkbox" id="pdf-check-${sanitizeForId(itemName)}" data-item-name="${itemName}" class="pdf-item-check h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" checked>
                    <label for="pdf-check-${sanitizeForId(itemName)}" class="ml-3 block text-sm text-gray-800 cursor-pointer">${itemName}</label>
                </div>
            `).join('');
            pdfSelectionModal.classList.remove('hidden');
            pdfSelectionModal.classList.add('flex');
        };

        const generatePdf = (selectedItems) => {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const { devisMode, gammeClubMode, maquetteLink, customCreation } = quoteState;
            
            let docTitle = 'PRESENTATION TARIFAIRE';
            if (devisMode && customCreation) docTitle = 'DEVIS - CRÉATION PERSONNALISÉE';
            else if (devisMode) docTitle = 'DEVIS - RÉASSORT';
            else if (gammeClubMode && customCreation) docTitle = 'GAMME CLUB - PROPOSITION TEAM';
            else if (gammeClubMode) docTitle = 'GAMME CLUB';

            // --- PDF Header ---
            doc.setFontSize(18);
            doc.text(`${docTitle} - ${quoteState.clubName || 'Interactif'}`, 14, 22);
            doc.setFontSize(12);
            doc.setFont(undefined, 'bold');
            doc.setTextColor(0);
            doc.text('Tarif TTC saison 2025/2026', 14, 29);
            doc.setFontSize(11);
            doc.setFont(undefined, 'normal');
            doc.setTextColor(100);
            doc.text(`Date: ${new Date().toLocaleDateString('fr-FR')}`, 14, 35);
            let startY = 45;

            if (gammeClubMode && maquetteLink) {
                doc.setFontSize(12);
                doc.setFont(undefined, 'bold');
                doc.setTextColor(0, 0, 238);
                doc.textWithLink('Cliquez ici pour voir la maquette du club', 14, startY, { url: maquetteLink });
                doc.setTextColor(100);
                startY += 10;
            }

            const tableBody = [];
            const techLinks = new Map();
            let finalYAfterTable;

            if (devisMode) {
                let subtotalTTC = 0;
                const groupQuantities = {};
                let totalNonAccessoryQty = 0; // For pricing logic AND fee calculation
                for (const itemName in quoteState.items) {
                    const product = productMap.get(itemName);
                    const item = quoteState.items[itemName];
                    if (product && item.quantity > 0) {
                        if (product.type !== 'accessoire') {
                            totalNonAccessoryQty += item.quantity;
                        }
                        if (product.pricingGroup) {
                            groupQuantities[product.pricingGroup] = (groupQuantities[product.pricingGroup] || 0) + item.quantity;
                        }
                    }
                }

                if (customCreation) {
                    let finalY = startY;
                    doc.setFontSize(10);
                    doc.setFont(undefined, 'bold');
                    doc.text("Conditions spécifiques pour la création d'un nouveau visuel :", 14, finalY);
                    finalY += 6;
                    doc.setFont(undefined, 'normal');
                    const noteText = [
                        '- Minimum de commande pour une création : 10 pièces (hors accessoires).',
                        '- Pour une commande de 10 à 19 pièces, un forfait maquette de 150€ TTC sera appliqué.',
                        "- À partir de 20 pièces, le forfait maquette est offert."
                    ].join('\n');
                    doc.text(noteText, 14, finalY, { maxWidth: 180 });
                    startY = finalY + 18;
                }

                const isSpecialCreationCase = quoteState.customCreation && totalNonAccessoryQty >= 10 && totalNonAccessoryQty < 20;

                for (const itemName of selectedItems) {
                     const product = productMap.get(itemName);
                     const item = quoteState.items[itemName];
                     if (!product || item.quantity === 0) continue;

                     if (product.chamoisInfo?.link) techLinks.set(product.chamoisInfo.link, `FICHE TECHNIQUE ${product.chamoisInfo.name.toUpperCase()}${product.name.toUpperCase().includes('HOMME') ? ' HOMME' : (product.name.toUpperCase().includes('FEMME') ? ' FEMME' : '')}`);
                     if (product.techSheetLink) techLinks.set(product.techSheetLink, `FICHE TECHNIQUE ${product.name}`);

                    const pricingQty = product.pricingGroup ? groupQuantities[product.pricingGroup] : item.quantity;
                     
                     let unitPrice = product.price || getPriceForQuantity(product.pricingTiers, pricingQty) || 0;
                     
                     let optionsDesc = [];
                     item.options.forEach(optName => {
                         const optProduct = productMap.get(optName);
                         if (optProduct) {
                             optionsDesc.push(optName);
                             unitPrice += optProduct.fixedPriceTTC || getPriceForQuantity(optProduct.pricingTiers, pricingQty) || 0;
                         }
                     });
                     const totalLinePrice = unitPrice * item.quantity;
                     subtotalTTC += totalLinePrice;
                     
                     let productTitle = itemName;

                     let details = [];
                     details.push(summarizeSizes(sizeData[product.type === 'enfant' ? 'enfant' : (product.sizeType || 'default')] || sizeData.default));
                     if (product.pricingGroup) {
                         const otherItemsInGroup = allAvailableProducts.filter(p => p.pricingGroup === product.pricingGroup && p.name !== itemName).map(p => p.name);
                         if (otherItemsInGroup.length > 0) {
                             let groupText = `Prix groupé avec : ${otherItemsInGroup.join(', ')}`;
                             if (product.pricingGroup === 'giletCoupeVent') {
                                 groupText += " (uniquement si visuel identique)";
                             }
                             details.push(groupText);
                         }
                     }
                     productTitle += `\n(${details.join('\n')})`;

                     if (optionsDesc.length > 0) {
                        productTitle += `\n(Prix inclut option(s): ${optionsDesc.join(', ')})`;
                     }

                     if (product.pricingTiers) {
                        let tierString;
                        // Special display text for custom creation between 10 & 19 items
                        if (isSpecialCreationCase && pricingQty >= 5) {
                            if (pricingQty >= 15) { // The price is calculated on 15+, display as 15-19
                                tierString = '(Tarif pour 15-19 pièces)';
                            } else { // The price is calculated on 5+, display as 5-14
                                tierString = '(Tarif pour 5-14 pièces)';
                            }
                        } else {
                            // Use normal display logic for all other cases
                            tierString = getTierRangeString(product.pricingTiers, pricingQty);
                        }
                        productTitle += `\n${tierString}`;
                     }

                     tableBody.push([productTitle, item.quantity, `${unitPrice.toFixed(2)} €`, `${totalLinePrice.toFixed(2)} €`]);
                }

                doc.autoTable({
                    startY: startY,
                    head: [['Article', 'Quantité', 'Prix U. TTC', 'Total Ligne TTC']],
                    body: tableBody,
                    theme: 'striped', headStyles: { fillColor: [79, 70, 229] }, styles: { fontSize: 9 }, columnStyles: { 0: { cellWidth: 100 } },
                   didParseCell: data => { 
                        if (data.column.index > 0) data.cell.styles.halign = 'center';
                        if (data.column.index === 0) data.cell.styles.fontStyle = 'bold';
                   }
                });

                finalYAfterTable = doc.autoTable.previous.finalY;
                const subtotalHT = subtotalTTC / (1 + TVA_RATE);
                let shippingHT = 0;
                if (subtotalHT > 2000) shippingHT = 0; else if (subtotalHT > 1000) shippingHT = 14; else if (subtotalHT > 500) shippingHT = 12; else if (subtotalHT > 0) shippingHT = 9.50;
                const shippingTTC = shippingHT * (1 + TVA_RATE);
                const graphicFeeTTC = (quoteState.customCreation && totalNonAccessoryQty > 0 && totalNonAccessoryQty < 20) ? GRAPHIC_FEE_TTC : 0;
                const graphicFeeHT = graphicFeeTTC / (1 + TVA_RATE);
                const grandTotalHT = subtotalHT + shippingHT + graphicFeeHT;
                const grandTotalTTC = subtotalTTC + shippingTTC + graphicFeeTTC;

                const totalsData = [
                    ['Sous-total HT:', `${subtotalHT.toFixed(2)} €`, 'Sous-total TTC:', `${subtotalTTC.toFixed(2)} €`],
                    ['Frais de port HT:', `${shippingHT.toFixed(2)} €`, 'Frais de port TTC:', `${shippingTTC.toFixed(2)} €`],
                ];
                if (graphicFeeTTC > 0) {
                    totalsData.push(['Forfait Création HT:', `${graphicFeeHT.toFixed(2)} €`, 'Forfait Création TTC:', `${graphicFeeTTC.toFixed(2)} €`]);
                }
                 totalsData.push(['Total HT:', `${grandTotalHT.toFixed(2)} €`, 'Total TTC:', `${grandTotalTTC.toFixed(2)} €`]);

                doc.autoTable({
                    startY: finalYAfterTable + 10,
                    body: totalsData,
                    theme: 'plain',
                    tableWidth: 'wrap',
                    margin: { left: 105 },
                    styles: { fontSize: 9 },
                    columnStyles: {
                        0: { halign: 'right', fontStyle: 'bold' }, 1: { halign: 'right' },
                        2: { halign: 'right', fontStyle: 'bold' }, 3: { halign: 'right' }
                    },
                    didParseCell: function (data) {
                        if (data.row.index === totalsData.length - 1) {
                            data.cell.styles.fontStyle = 'bold';
                            if (data.column.index === 3) data.cell.styles.textColor = [79, 70, 229];
                        }
                        if (graphicFeeTTC > 0 && data.row.index === 2) {
                             data.cell.styles.textColor = [220, 38, 38];
                        }
                    }
                });

                finalYAfterTable = doc.autoTable.previous.finalY;

                doc.setTextColor(0);
                doc.setFontSize(8); 
                doc.setFont(undefined, 'italic');
                doc.text("Ce devis est valable 1 mois à compter de la date d'émission.", 14, finalYAfterTable + 5);

            } else {
                const head = (gammeClubMode && customCreation) 
                    ? [['Article', 'Prix TTC (5-14p)', 'Prix TTC (15-19p)']]
                    : (gammeClubMode ? [['Article']] : [['Article', 'Grille Tarifaire TTC']]);
                
                if (gammeClubMode && customCreation) {
                    let finalY = startY;
                    doc.setFontSize(10);
                    doc.setFont(undefined, 'bold');
                    doc.text("Conditions spécifiques pour la création d'un nouveau visuel (TEAM) :", 14, finalY);
                    finalY += 6;
                    doc.setFont(undefined, 'normal');
                    const noteText = [
                        '- Minimum de commande : 10 pièces (hors accessoires).',
                        '- Pour une commande de 10 à 19 pièces, un forfait maquette de 150€ TTC sera appliqué.',
                        '- Les tarifs ci-dessous sont basés sur les tranches tarifaires "5-14 pièces" et "15-19 pièces".',
                        "- À partir de 20 pièces, le forfait maquette est offert et les tarifs dégressifs normaux s'appliquent."
                    ].join('\n');
                    doc.text(noteText, 14, finalY, { maxWidth: 180 });
                    startY = finalY + 25;
                }

                for (const itemName of selectedItems) {
                    const product = productMap.get(itemName);
                    if (!product) continue;
                    
                    if (product.chamoisInfo?.link) techLinks.set(product.chamoisInfo.link, `FICHE TECHNIQUE ${product.chamoisInfo.name.toUpperCase()}${product.name.toUpperCase().includes('HOMME') ? ' HOMME' : (product.name.toUpperCase().includes('FEMME') ? ' FEMME' : '')}`);
                    if (product.techSheetLink) techLinks.set(product.techSheetLink, `FICHE TECHNIQUE ${product.name}`);
                    
                    let productNameWithDetails = product.name;
                    let details = [];
                    details.push(summarizeSizes(sizeData[product.type === 'enfant' ? 'enfant' : (product.sizeType || 'default')] || sizeData.default));
                    if (product.pricingGroup) {
                        const otherItemsInGroup = allAvailableProducts.filter(p => p.pricingGroup === product.pricingGroup && p.name !== itemName).map(p => p.name);
                        if (otherItemsInGroup.length > 0) {
                            let groupText = `Prix groupé avec : ${otherItemsInGroup.join(', ')}`;
                            if (product.pricingGroup === 'giletCoupeVent') {
                                groupText += " (uniquement si visuel identique)";
                            }
                            details.push(groupText);
                        }
                    }
                    productNameWithDetails += `\n(${details.join('\n')})`;
                    const rowData = [productNameWithDetails];

                    if (gammeClubMode && customCreation) {
                        const price5 = getPriceForQuantity(product.pricingTiers, 5) || product.price || 0;
                        const price15 = getPriceForQuantity(product.pricingTiers, 15) || product.price || 0;
                        rowData.push(`${price5.toFixed(2)} €`, `${price15.toFixed(2)} €`);
                    } else if (!gammeClubMode) {
                        let priceInfo = formatTiersForPdf(product.pricingTiers || [{ quantity: 1, price: product.price }]);
                        
                        const isLongSleeved = product.type === 'haut' && (product.name.includes('ML') || product.name.includes('MANCHES LONGUES'));
                        const normalOptions = (product.hasOptions !== false && product.type !== 'accessoire' && !product.isCuissardOrCollant) ? allAvailableProducts.filter(p => p.category === 'option' && !p.optionGroup && !(product.excludedOptions || []).includes(p.name)) : [];
                        const lengthOptions = (product.isCuissardOrCollant || isLongSleeved) ? allAvailableProducts.filter(p => p.optionGroup === 'length') : [];
                        const allOptions = [...normalOptions, ...lengthOptions];

                        if (allOptions.length > 0) {
                            priceInfo += '\n\nOptions disponibles pour la gamme (prix TTC à ajouter) :';
                            allOptions.forEach(opt => {
                                priceInfo += `\n- ${opt.name}: ${opt.fixedPriceTTC.toFixed(2)} €`;
                            });
                        }
                        
                        rowData.push(priceInfo);
                    }
                    tableBody.push(rowData);
                }

                doc.autoTable({
                    startY: startY,
                    head: head,
                    body: tableBody,
                    theme: 'striped', 
                    headStyles: { fillColor: (gammeClubMode && customCreation) ? [22, 163, 74] : (gammeClubMode ? [22, 163, 74] : [79, 70, 229]) }, 
                    styles: { fontSize: 9 }, 
                    columnStyles: { 
                        0: { cellWidth: (gammeClubMode && customCreation) ? 100 : (gammeClubMode ? 'auto' : 100) }, 
                        1: { cellWidth: 'auto', halign: 'center' },
                        2: { cellWidth: 'auto', halign: 'center' }
                    },
                   didParseCell: data => {
                       if (data.column.index === 0) data.cell.styles.fontStyle = 'bold';
                   }
                });
                finalYAfterTable = doc.autoTable.previous.finalY;
            }
            
            if (techLinks.size > 0) {
                 if (finalYAfterTable > 240) { doc.addPage(); finalYAfterTable = 20; }
                finalYAfterTable += 15;
                doc.setFontSize(12); doc.setFont(undefined, 'bold');
                doc.text("Fiches Techniques", 14, finalYAfterTable);
                finalYAfterTable += 7;
                doc.setFontSize(10); doc.setFont(undefined, 'normal');
                for (const [url, title] of techLinks.entries()) {
                    doc.setTextColor(0, 0, 238);
                    doc.textWithLink(title, 14, finalYAfterTable, { url: url });
                    finalYAfterTable += 6;
                }
                doc.setTextColor(0);
            }

            if (finalYAfterTable > 260) { doc.addPage(); finalYAfterTable = 20; }
            finalYAfterTable += 15;
            
            const boxY = finalYAfterTable > 240 ? 20 : finalYAfterTable;
            if (finalYAfterTable > 240) doc.addPage();
            doc.setFillColor(220, 38, 38);
            doc.rect(14, boxY, doc.internal.pageSize.getWidth() - 28, 28, 'F');
            doc.setTextColor(255);
            doc.setFont(undefined, 'bold');
            doc.setFontSize(12);
            doc.text("COMPLÉTEZ VOTRE GAMME CLUB", doc.internal.pageSize.getWidth() / 2, boxY + 8, { align: 'center' });
            doc.setFont(undefined, 'normal');
            doc.setFontSize(9);
            const centerX = doc.internal.pageSize.getWidth() / 2;
            doc.textWithLink("Cliquez pour découvrir notre catalogue sportswear et équiper vos membres.", centerX, boxY + 15, { url: 'https://acrobat.adobe.com/id/urn:aaid:sc:EU:9e2ca643-28a7-4bff-b46d-cc55f73c033c', align: 'center' });
            doc.textWithLink("Cliquez pour nous contacter et obtenir un devis personnalisé.", centerX, boxY + 22, { url: 'mailto:wilfrid@noret.com', align: 'center' });
            finalYAfterTable = boxY + 35;


            doc.save(`${docTitle}_${quoteState.clubName.replace(/ /g, '_') || 'Interactif'}.pdf`);
            return true;
        };
        
        // --- APP INITIALIZATION ---
        renderSidebarNavigation();
        updateUI();
    });
    </script>
</body>
</html>











