<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Outil de devis/bon de commande (Autonome)</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.8.2/jspdf.plugin.autotable.min.js"></script>
    <style>
        /* A little bit of custom CSS for better UX */
        body {
            font-family: 'Inter', sans-serif;
        }
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap');
        .modal {
            display: none; /* Hidden by default */
        }
        .modal.is-open {
            display: flex; /* Display when open */
        }
        /* Style for more visible input fields */
        input[type="text"], input[type="number"], input[type="date"], select, textarea {
            background-color: #f3f4f6; /* bg-gray-100 */
            border-color: #9ca3af; /* border-gray-400 */
        }
        input[type="text"]:focus, input[type="number"]:focus, input[type="date"]:focus, select:focus, textarea:focus {
            --tw-ring-color: #4f46e5; /* ring-indigo-500 */
            border-color: #4f46e5; /* border-indigo-500 */
        }
        .main-tab-content {
            display: none;
        }
        .main-tab-content.active {
            display: block;
        }
    </style>
</head>
<body class="bg-gray-50">

    <div id="app-container">
        <div id="main-modal" class="modal fixed inset-0 bg-black bg-opacity-50 z-50 justify-center items-center p-4">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-md">
                <h3 id="main-modal-title" class="text-xl font-bold text-gray-800 mb-4">Titre du Modal</h3>
                <div id="main-modal-body" class="text-gray-600 mb-6">Contenu du modal.</div>
                <div id="main-modal-actions" class="flex justify-end space-x-3">
                    <button id="main-modal-close-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-lg">Fermer</button>
                </div>
            </div>
        </div>

        <div id="excel-modal" class="modal fixed inset-0 bg-black bg-opacity-50 z-50 justify-center items-center p-4">
            <div class="bg-white rounded-lg shadow-xl p-6 w-full max-w-md">
                <h3 class="text-xl font-bold text-gray-800 mb-4">Personnaliser les colonnes de l'export Excel</h3>
                <div id="excel-columns-container" class="grid grid-cols-2 gap-4">
                    <!-- Column toggles will be inserted here by JS -->
                </div>
                <div class="flex justify-end space-x-3 mt-6">
                     <button id="excel-modal-validate-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-lg">Valider</button>
                </div>
            </div>
        </div>

        <div class="container mx-auto p-4 sm:p-6 lg:p-8">
            <header class="mb-8">
                <h1 class="text-4xl font-extrabold text-gray-800 tracking-tight">Outil de devis/bon de commande (Autonome)</h1>
                <p class="mt-2 text-lg text-gray-500">Créez, modifiez et gérez les commandes de vos clients en local.</p>
            </header>

            <main class="space-y-8">

                 <div class="bg-white p-6 rounded-xl shadow-lg">
                     <div id="main-tabs-container" class="flex border-b border-gray-200">
                         <button data-tab-id="order-creation" class="main-tab-btn py-2 px-4 -mb-px font-medium text-sm border-b-2 border-indigo-500 text-indigo-600">Création de Commande</button>
                         <button data-tab-id="saved-orders" class="main-tab-btn py-2 px-4 -mb-px font-medium text-sm text-gray-500 hover:text-gray-700">Commandes Sauvegardées</button>
                     </div>

                     <div id="order-creation" class="main-tab-content active mt-6 space-y-8">
                         <section>
                             <h2 class="text-2xl font-bold text-gray-800 border-b pb-3 mb-6">Informations sur la commande</h2>
                             <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                                 <div>
                                     <label class="block text-sm font-medium text-gray-700">Type de Document</label>
                                     <div class="mt-2 flex items-center space-x-4">
                                         <div class="flex items-center">
                                             <input id="doc-type-order" name="doc-type" type="radio" value="order" checked>
                                             <label for="doc-type-order" class="ml-2 block text-sm text-gray-900">Commande</label>
                                         </div>
                                         <div class="flex items-center">
                                             <input id="doc-type-quote" name="doc-type" type="radio" value="quote">
                                             <label for="doc-type-quote" class="ml-2 block text-sm text-gray-900">Devis</label>
                                         </div>
                                     </div>
                                 </div>
                                 <div id="order-scope-container">
                                     <label class="block text-sm font-medium text-gray-700">Type de saisie</label>
                                     <div class="mt-2 flex items-center space-x-4">
                                         <div class="flex items-center">
                                             <input id="scope-global" name="scope" type="radio" value="global" checked>
                                             <label for="scope-global" class="ml-2 block text-sm text-gray-900">Globale</label>
                                         </div>
                                         <div class="flex items-center">
                                             <input id="scope-licensee" name="scope" type="radio" value="licensee">
                                             <label for="scope-licensee" class="ml-2 block text-sm text-gray-900">Par licencié</label>
                                         </div>
                                     </div>
                                 </div>
                                  <div>
                                     <label class="block text-sm font-medium text-gray-700">Options de Commande</label>
                                      <div class="mt-2 space-y-2">
                                         <div class="flex items-center">
                                             <input id="doc-type-reassort" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                             <label for="doc-type-reassort" class="ml-2 block text-sm text-gray-900">Réassort 2 mois</label>
                                         </div>
                                         <div class="flex items-center">
                                             <input id="custom-design-order-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                             <label for="custom-design-order-check" class="ml-2 block text-sm text-gray-900">Commande création personnalisée</label>
                                         </div>
                                         <div class="flex items-center">
                                             <input id="store-order-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                             <label for="store-order-check" class="ml-2 block text-sm text-gray-900">Commande Magasin</label>
                                         </div>
                                     </div>
                                 </div>
                                 <div>
                                     <label for="clubName" class="block text-sm font-medium text-gray-700">Nom du Club <span class="text-red-500">*</span></label>
                                     <input type="text" id="clubName" list="club-list" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                     <datalist id="club-list"></datalist>
                                 </div>
                                 <div>
                                     <label for="departement" class="block text-sm font-medium text-gray-700">Département <span class="text-red-500">*</span></label>
                                     <input type="text" id="departement" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                 </div>
                                 <div>
                                     <label for="clientNumber" class="block text-sm font-medium text-gray-700">N° Client <span class="text-red-500">*</span></label>
                                     <input type="text" id="clientNumber" list="client-list" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                      <datalist id="client-list"></datalist>
                                 </div>
                                 <div id="pre-order-number-container">
                                     <label for="preOrderNumber" class="block text-sm font-medium text-gray-700">N° Pré-commande</label>
                                     <input type="text" id="preOrderNumber" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                 </div>
                                 <div id="quote-number-container" class="hidden">
                                     <label for="quoteNumber" class="block text-sm font-medium text-gray-700">N° Devis</label>
                                     <input type="text" id="quoteNumber" readonly class="mt-1 block w-full rounded-md border-gray-300 shadow-sm bg-gray-100">
                                 </div>
                                 <div>
                                     <label for="orderDate" class="block text-sm font-medium text-gray-700">Date</label>
                                     <input type="date" id="orderDate" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                 </div>
                                  <div id="licencieName-container" class="hidden">
                                     <label for="licencieName" class="block text-sm font-medium text-gray-700">Nom du licencié</label>
                                     <input type="text" id="licencieName" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                 </div>
                                 <div>
                                     <label class="block text-sm font-medium text-gray-700">Remise</label>
                                     <div class="mt-2 flex items-center">
                                         <input id="apply-discount-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500">
                                         <label for="apply-discount-check" class="ml-2 block text-sm text-gray-900">Application d'une remise</label>
                                     </div>
                                 </div>
                                 <div class="md:col-span-2">
                                     <label for="orderSpecificity" class="block text-sm font-medium text-gray-700">Spécificité Commande</label>
                                      <textarea id="orderSpecificity" rows="1" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="Notes générales sur la commande..."></textarea>
                                 </div>
                                 <div class="lg:col-span-3">
                                     <div class="flex items-center">
                                         <input id="imperatif-check" type="checkbox" class="h-4 w-4 rounded border-gray-300 text-red-600 focus:ring-red-500">
                                         <label for="imperatif-check" class="ml-2 block text-sm font-bold text-red-600">Impératif</label>
                                     </div>
                                     <div id="imperatif-note-container" class="hidden mt-2">
                                         <label for="imperatif-note" class="block text-sm font-medium text-gray-700">Préciser l'impératif</label>
                                         <textarea id="imperatif-note" rows="2" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="Ex: Doit être livré avant le JJ/MM/AAAA pour l'événement X..."></textarea>
                                     </div>
                                 </div>
                             </div>
                         </section>
                        
                         <section>
                             <h2 class="text-2xl font-bold text-gray-800 border-b pb-3 mb-6">Ajouter un Article</h2>
                             <div id="product-tabs-container" class="flex border-b border-gray-200">
                                 <button data-tab="CYCLISME/RUNNING" class="product-tab-btn py-2 px-4 -mb-px font-medium text-sm border-b-2 border-indigo-500 text-indigo-600">CYCLISME/RUNNING</button>
                                 <button data-tab="Accessoires" class="product-tab-btn py-2 px-4 -mb-px font-medium text-sm text-gray-500 hover:text-gray-700">Accessoires</button>
                                 <button data-tab="GAMME ENFANTS" class="product-tab-btn py-2 px-4 -mb-px font-medium text-sm text-gray-500 hover:text-gray-700">GAMME ENFANTS</button>
                             </div>
                             <div id="product-form-container" class="pt-6">
                                 <!-- Product form will be rendered here by JS -->
                             </div>
                         </section>

                         <section>
                             <div class="flex justify-between items-center border-b pb-3 mb-6">
                                 <h2 class="text-2xl font-bold text-gray-800">Détails du devis/bon de commande</h2>
                                 <span id="total-articles-display" class="text-lg font-semibold text-gray-600">Total des articles : 0</span>
                             </div>
                             <div class="overflow-x-auto">
                                 <table class="min-w-full divide-y divide-gray-200">
                                     <thead id="order-table-head" class="bg-gray-50">
                                         <!-- Table head will be rendered here by JS -->
                                         </thead>
                                     <tbody id="order-table-body" class="bg-white divide-y divide-gray-200">
                                         <tr><td colSpan="8" class="px-6 py-12 text-center text-gray-500">Aucun article dans le devis.</td></tr>
                                     </tbody>
                                 </table>
                             </div>
                         </section>
                        
                         <section>
                              <div class="grid grid-cols-1 md:grid-cols-2 gap-x-8 gap-y-6">
                                   <div id="discount-controls-container" class="hidden">
                                     <label class="block text-sm font-medium text-gray-700">Type de remise</label>
                                     <div class="mt-2 flex items-center space-x-4">
                                          <div class="flex items-center">
                                             <input id="discount-global" name="discount-type" type="radio" value="global" checked>
                                             <label for="discount-global" class="ml-2 block text-sm text-gray-900">Globale</label>
                                          </div>
                                          <div class="flex items-center">
                                             <input id="discount-item" name="discount-type" type="radio" value="item">
                                             <label for="discount-item" class="ml-2 block text-sm text-gray-900">Par article</label>
                                          </div>
                                     </div>
                                     <label for="clubDiscount" class="block text-sm font-medium text-gray-700 mt-4">Remise Club (%)</label>
                                     <input type="number" id="clubDiscount" value="0" class="mt-1 block w-full md:w-1/2 rounded-md border-gray-300 shadow-sm" placeholder="0">
                                   </div>
                                 <div class="space-y-2 text-right md:col-start-2">
                                     <div class="text-md"><span class="font-medium text-gray-600">Sous-total HT: </span><span id="subtotal-ht" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-md"><span class="font-medium text-gray-600">Sous-total TTC: </span><span id="subtotal-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-md text-red-600"><span class="font-medium">Remise Club HT (Information): </span><span id="discount-amount-ht" class="font-semibold">-0.00€</span></div>
                                     <div class="text-md text-red-600"><span class="font-medium">Remise Club TTC (Information): </span><span id="discount-amount-ttc" class="font-semibold">-0.00€</span></div>
                                     <div class="text-md hidden" id="graphic-fee-ht-row"><span class="font-medium text-gray-600">Frais création graphique HT: </span><span id="graphic-design-fee-ht" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-md hidden" id="graphic-fee-ttc-row"><span class="font-medium text-gray-600">Frais création graphique TTC: </span><span id="graphic-design-fee-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-md border-t pt-2 mt-2"><span class="font-medium text-gray-600">Frais de port HT: </span><span id="shipping-ht" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-md"><span class="font-medium text-gray-600">Frais de port TTC: </span><span id="shipping-ttc" class="font-semibold text-gray-800">0.00€</span></div>
                                     <div class="text-xl mt-4"><span class="font-bold text-gray-700">Total Général HT: </span><span id="grand-total-ht" class="font-extrabold text-indigo-600">0.00€</span></div>
                                     <div class="text-2xl"><span class="font-bold text-gray-700">Total Général TTC: </span><span id="grand-total-ttc" class="font-extrabold text-indigo-600">0.00€</span></div>
                                     <div class="text-xl mt-2 font-bold text-green-600"><span id="down-payment-label" class="font-bold text-gray-700">Acompte à verser (30%): </span><span id="down-payment">0.00€</span></div>
                                 </div>
                                 </div>
                              <div class="mt-8 pt-6 border-t flex flex-col sm:flex-row justify-between items-center gap-4">
                                   <button id="new-order-btn" class="w-full sm:w-auto px-6 py-3 border border-red-500 text-base font-medium rounded-md text-red-500 bg-white hover:bg-red-50">Nouvelle Commande</button>
                                   <div class="flex flex-col sm:flex-row gap-4 w-full sm:w-auto">
                                       <button id="export-pdf-btn" class="w-full sm:w-auto px-6 py-3 border border-red-600 text-base font-medium rounded-md text-red-700 bg-white hover:bg-red-50 disabled:bg-gray-200 disabled:cursor-not-allowed">Exporter en PDF</button>
                                       <button id="export-excel-btn" class="w-full sm:w-auto px-6 py-3 border border-green-600 text-base font-medium rounded-md text-green-700 bg-white hover:bg-green-50 disabled:bg-gray-200 disabled:cursor-not-allowed">Exporter en Excel</button>
                                       <button id="send-email-btn" class="w-full sm:w-auto inline-flex justify-center px-6 py-3 border border-blue-600 text-base font-medium rounded-md text-blue-700 bg-white hover:bg-blue-50 disabled:bg-gray-200 disabled:cursor-not-allowed">Envoyer par Mail</button>
                                       <button id="save-order-btn" class="w-full sm:w-auto inline-flex justify-center items-center px-8 py-3 border border-transparent text-base font-bold rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300">Sauvegarder la Commande</button>
                                   </div>
                                 </div>
                         </section>
                     </div>
                      <div id="saved-orders" class="main-tab-content mt-6">
                          <div class="flex justify-between items-center border-b pb-3 mb-6">
                              <h2 class="text-2xl font-bold text-gray-800">Commandes Sauvegardées</h2>
                              <div class="flex gap-2">
                                  <button id="import-orders-json-btn" class="px-4 py-2 text-sm font-medium text-white bg-green-600 rounded-lg hover:bg-green-700">Charger .json</button>
                                  <button id="export-orders-json-btn" class="px-4 py-2 text-sm font-medium text-white bg-purple-600 rounded-lg hover:bg-purple-700">Exporter .json</button>
                                  <input type="file" id="json-orders-file-input" class="hidden" accept=".json">
                              </div>
                          </div>
                          <div id="saved-orders-list" class="space-y-4">
                              <!-- Saved orders will be rendered here by JS -->
                          </div>
                      </div>
                 </div>

                
            </main>
        </div>
    </div>

    <script type="module">
        // --- DATA ---
        const allAvailableProducts = [
            // This is a very large array, so it's truncated for brevity in this comment.
            // In the final code, the full array from the React component is used.
             // =========== CYCLISME ===========
            { name: 'MAILLOT CLASSIQUE HOMME CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
            { name: 'MAILLOT CLASSIQUE FEMME CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingGroup: 'maillotClassiqueMC', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
            { name: 'MAILLOT MIXTE CONFORT SANS MANCHE', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 86.10 }, { quantity: 2, price: 73.80 }, { quantity: 3, price: 61.50 }, { quantity: 5, price: 49.20 }, { quantity: 15, price: 46.74 }, { quantity: 25, price: 45.26 }, { quantity: 50, price: 44.28 }, { quantity: 80, price: 42.80 }, { quantity: 150, price: 41.82 } ] },
            { name: 'MAILLOT MIXTE PERFORMANCE MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 89.25 }, { quantity: 2, price: 76.50 }, { quantity: 3, price: 63.75 }, { quantity: 5, price: 51.00 }, { quantity: 15, price: 48.45 }, { quantity: 25, price: 46.92 }, { quantity: 50, price: 45.90 }, { quantity: 80, price: 44.37 }, { quantity: 150, price: 43.35 } ] },
            { name: 'MAILLOT MIXTE VTT CONFORT MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', pricingTiers: [ { quantity: 1, price: 89.25 }, { quantity: 2, price: 76.50 }, { quantity: 3, price: 63.75 }, { quantity: 5, price: 51.00 }, { quantity: 15, price: 48.45 }, { quantity: 25, price: 46.92 }, { quantity: 50, price: 45.90 }, { quantity: 80, price: 44.37 }, { quantity: 150, price: 43.35 } ] },
            { name: 'MAILLOT VTT/DESCENTE MIXTE CONFORT MC (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'largeHaut', hasOptions: false, pricingTiers: [ { quantity: 1, price: 77.70 }, { quantity: 2, price: 66.60 }, { quantity: 3, price: 55.50 }, { quantity: 5, price: 44.40 }, { quantity: 15, price: 42.18 }, { quantity: 25, price: 40.85 }, { quantity: 50, price: 39.96 }, { quantity: 80, price: 38.63 }, { quantity: 150, price: 37.74 } ] },
            { name: 'MAILLOT MIXTE AERO MC', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Courtes', sizeType: 'aero', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
            { name: 'MAILLOT MI-SAISON HOMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', pricingTiers: [ { quantity: 1, price: 101.85 }, { quantity: 2, price: 87.30 }, { quantity: 3, price: 72.75 }, { quantity: 5, price: 58.20 }, { quantity: 15, price: 55.29 }, { quantity: 25, price: 53.54 }, { quantity: 50, price: 52.38 }, { quantity: 80, price: 50.63 }, { quantity: 150, price: 49.47 } ] },
            { name: 'MAILLOT MI-SAISON FEMME CONFORT ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', pricingGroup: 'maillotMiSaisonML', pricingTiers: [ { quantity: 1, price: 101.85 }, { quantity: 2, price: 87.30 }, { quantity: 3, price: 72.75 }, { quantity: 5, price: 58.20 }, { quantity: 15, price: 55.29 }, { quantity: 25, price: 53.54 }, { quantity: 50, price: 52.38 }, { quantity: 80, price: 50.63 }, { quantity: 150, price: 49.47 } ] },
            { name: 'MAILLOT BMX MIXTE CONFORT ML (Très ample)', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'largeHaut', hasOptions: false, pricingTiers: [ { quantity: 1, price: 88.20 }, { quantity: 2, price: 75.60 }, { quantity: 3, price: 63.00 }, { quantity: 5, price: 50.40 }, { quantity: 15, price: 47.88 }, { quantity: 25, price: 46.37 }, { quantity: 50, price: 45.36 }, { quantity: 80, price: 43.85 }, { quantity: 150, price: 42.84 } ] },
            { name: 'MAILLOT MI-SAISON MIXTE AERO ML', category: 'CYCLISME', type: 'haut', subtype: 'Maillots Manches Longues', sizeType: 'aero', pricingTiers: [ { quantity: 1, price: 107.10 }, { quantity: 2, price: 91.80 }, { quantity: 3, price: 76.50 }, { quantity: 5, price: 61.20 }, { quantity: 15, price: 58.14 }, { quantity: 25, price: 56.30 }, { quantity: 50, price: 55.08 }, { quantity: 80, price: 53.24 }, { quantity: 150, price: 52.02 } ] },
            
            { name: 'MAILLOT PLUIE MIXTE AERO MC (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingTiers: [ { quantity: 1, price: 149.10 }, { quantity: 2, price: 127.80 }, { quantity: 3, price: 106.50 }, { quantity: 5, price: 85.20 }, { quantity: 15, price: 80.94 }, { quantity: 25, price: 78.38 }, { quantity: 50, price: 76.68 }, { quantity: 80, price: 74.12 }, { quantity: 150, price: 72.42 } ] },
            { name: 'MAILLOT PLUIE MIXTE AERO ML (non sublimé, marquage DTF)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingTiers: [ { quantity: 1, price: 178.50 }, { quantity: 2, price: 153.00 }, { quantity: 3, price: 127.50 }, { quantity: 5, price: 102.00 }, { quantity: 15, price: 96.90 }, { quantity: 25, price: 93.84 }, { quantity: 50, price: 91.80 }, { quantity: 80, price: 88.74 }, { quantity: 150, price: 86.70 } ] },
            { name: 'GILET COUPE-VENT LEGER MIXTE (vent et pluie fine, sans poche dos)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
            { name: 'GILET COUPE-VENT MI-SAISON MIXTE (dos ajouré)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 98.70 }, { quantity: 2, price: 84.60 }, { quantity: 3, price: 70.50 }, { quantity: 5, price: 56.40 }, { quantity: 15, price: 53.58 }, { quantity: 25, price: 51.89 }, { quantity: 50, price: 50.76 }, { quantity: 80, price: 49.07 }, { quantity: 150, price: 47.94 } ] },
            { name: 'GILET COUPE-VENT HIVER MIXTE (tout membranné)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'giletCoupeVent', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 105.00 }, { quantity: 2, price: 90.00 }, { quantity: 3, price: 75.00 }, { quantity: 5, price: 60.00 }, { quantity: 15, price: 57.00 }, { quantity: 25, price: 55.20 }, { quantity: 50, price: 54.00 }, { quantity: 80, price: 52.20 }, { quantity: 150, price: 51.00 } ] },
            { name: 'COUPE-VENT LEGER MIXTE CONFORT (vent et pluie fine)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
            { name: 'COUPE-VENT LEGER DEPERLANT MIXTE CONFORT (avec membranne)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'coupeVent', pricingTiers: [ { quantity: 1, price: 128.10 }, { quantity: 2, price: 109.80 }, { quantity: 3, price: 91.50 }, { quantity: 5, price: 73.20 }, { quantity: 15, price: 69.54 }, { quantity: 25, price: 67.34 }, { quantity: 50, price: 65.88 }, { quantity: 80, price: 63.68 }, { quantity: 150, price: 62.22 } ] },
            { name: 'VESTE MI-SAISON MIXTE CONFORT (membranne coupe-vent + mi-saison)', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },

            { name: 'VESTE MI-SAISON MIXTE CONFORT avec -6cm aux ML', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteMiSaison', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
            { name: 'VESTE HIVER HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiverConfort', pricingTiers: [ { quantity: 1, price: 163.80 }, { quantity: 2, price: 140.40 }, { quantity: 3, price: 117.00 }, { quantity: 5, price: 93.60 }, { quantity: 15, price: 88.92 }, { quantity: 25, price: 86.11 }, { quantity: 50, price: 84.24 }, { quantity: 80, price: 81.43 }, { quantity: 150, price: 79.56 } ] },
            { name: 'VESTE HIVER FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiverConfort', pricingTiers: [ { quantity: 1, price: 163.80 }, { quantity: 2, price: 140.40 }, { quantity: 3, price: 117.00 }, { quantity: 5, price: 93.60 }, { quantity: 15, price: 88.92 }, { quantity: 25, price: 86.11 }, { quantity: 50, price: 84.24 }, { quantity: 80, price: 81.43 }, { quantity: 150, price: 79.56 } ] },
            { name: 'VESTE HIVER THERMIQUE HOMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiverThermique', pricingTiers: [ { quantity: 1, price: 168.00 }, { quantity: 2, price: 144.00 }, { quantity: 3, price: 120.00 }, { quantity: 5, price: 96.00 }, { quantity: 15, price: 91.20 }, { quantity: 25, price: 88.32 }, { quantity: 50, price: 86.40 }, { quantity: 80, price: 83.52 }, { quantity: 150, price: 81.60 } ] },
            { name: 'VESTE HIVER THERMIQUE FEMME CONFORT', category: 'CYCLISME', type: 'haut', subtype: 'Essentiels et Vestes', pricingGroup: 'vesteHiverThermique', pricingTiers: [ { quantity: 1, price: 168.00 }, { quantity: 2, price: 144.00 }, { quantity: 3, price: 120.00 }, { quantity: 5, price: 96.00 }, { quantity: 15, price: 91.20 }, { quantity: 25, price: 88.32 }, { quantity: 50, price: 86.40 }, { quantity: 80, price: 83.52 }, { quantity: 150, price: 81.60 } ] },
            // --- Cuissards Courts ---
            { name: 'CUISSARD A BRETELLES HOMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ] },
            { name: 'CUISSARD A BRETELLES FEMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 121.80 }, { quantity: 2, price: 104.40 }, { quantity: 3, price: 87.00 }, { quantity: 5, price: 69.60 }, { quantity: 15, price: 66.12 }, { quantity: 25, price: 64.03 }, { quantity: 50, price: 62.64 }, { quantity: 80, price: 60.55 }, { quantity: 150, price: 59.16 }, ] },
            { name: 'CUISSARD FEMME SANS BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingGroup: 'cuissardConfortLandscape', pricingTiers: [ { quantity: 1, price: 117.60 }, { quantity: 2, price: 100.80 }, { quantity: 3, price: 84.00 }, { quantity: 5, price: 67.20 }, { quantity: 15, price: 63.84 }, { quantity: 25, price: 61.82 }, { quantity: 50, price: 60.48 }, { quantity: 80, price: 58.46 }, { quantity: 150, price: 57.12 }, ] },
            { name: 'CUISSARD HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ] },
            { name: 'CUISSARD FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', sizeType: 'aero', isCuissardOrCollant: true, pricingGroup: 'cuissardAeroCervino', pricingTiers: [ { quantity: 1, price: 142.80 }, { quantity: 2, price: 122.40 }, { quantity: 3, price: 102.00 }, { quantity: 5, price: 81.60 }, { quantity: 15, price: 77.52 }, { quantity: 25, price: 75.07 }, { quantity: 50, price: 73.44 }, { quantity: 80, price: 70.99 }, { quantity: 150, price: 69.36 }, ] },
            { name: 'SHORT VTT FOND Peau ENDURANCE 2.5', category: 'CYCLISME', type: 'haut', subtype: 'Cuissards Courts', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 75.00 } ] },
            // --- Corsaires/Collants ---
            { name: 'CORSAIRE HOMME A BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', pricingTiers: [ { quantity: 1, price: 98.70 } ] },
            { name: 'CORSAIRE FEMME SANS BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'corsaireConfortLandscape', pricingTiers: [ { quantity: 1, price: 95.70 } ] },
            { name: 'COLLANT HIVER A BRETELLES HOMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
            { name: 'COLLANT HIVER A BRETELLES FEMME CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 138.60 }, { quantity: 2, price: 118.80 }, { quantity: 3, price: 99.00 }, { quantity: 5, price: 79.20 }, { quantity: 15, price: 75.24 }, { quantity: 25, price: 72.86 }, { quantity: 50, price: 71.28 }, { quantity: 80, price: 68.90 }, { quantity: 150, price: 67.32 }, ] },
            { name: 'COLLANT HIVER FEMME SANS BRETELLES CONFORT Peau LANDSCAPE', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingGroup: 'collantHiverConfortLandscape', pricingTiers: [ { quantity: 1, price: 134.40 }, { quantity: 2, price: 115.20 }, { quantity: 3, price: 96.00 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 }, ] },
            { name: 'COLLANT HIVER HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ] },
            { name: 'COLLANT HIVER FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', pricingGroup: 'collantHiverAeroCervino', sizeType: 'aero', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 165.90 }, { quantity: 2, price: 142.20 }, { quantity: 3, price: 118.50 }, { quantity: 5, price: 94.80 }, { quantity: 15, price: 90.06 }, { quantity: 25, price: 87.22 }, { quantity: 50, price: 85.32 }, { quantity: 80, price: 82.48 }, { quantity: 150, price: 80.58 }, ] },
            { name: 'COLLANT MIXTE ECHAUFFEMENT', category: 'CYCLISME', type: 'haut', subtype: 'Corsaires/Collants', sizeType: 'ample', isCuissardOrCollant: true, pricingTiers: [ { quantity: 1, price: 98.70 }, { quantity: 2, price: 84.60 }, { quantity: 3, price: 70.50 }, { quantity: 5, price: 56.40 }, { quantity: 15, price: 53.58 }, { quantity: 25, price: 51.89 }, { quantity: 50, price: 50.76 }, { quantity: 80, price: 49.07 }, { quantity: 150, price: 47.94 }, ] },
            // --- Combinaisons ---
            { name: 'COMBINAISON ROUTE MANCHES COURTES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
            { name: 'COMBINAISON ROUTE MANCHES COURTES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
            { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
            { name: 'COMBINAISON CHRONO ROUTE MANCHES COURTES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 115.20 }] },
            { name: 'COMBINAISON CHRONO ROUTE MANCHES LONGUES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
            { name: 'COMBINAISON CHRONO ROUTE MANCHES LONGUES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
            { name: 'COMBINAISON CHRONO PISTE MANCHES COURTES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 111.60 }] },
            { name: 'COMBINAISON CHRONO PISTE MANCHES COURTES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 111.60 }] },
            { name: 'COMBINAISON CHRONO PISTE MANCHES LONGUES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 116.40 }] },
            { name: 'COMBINAISON CHRONO PISTE MANCHES LONGUES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 111.60 }] },
            { name: 'COMBINAISON CYCLO-CROSS MANCHES LONGUES HOMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
            { name: 'COMBINAISON CYCLO-CROSSMANCHES LONGUES FEMME AERO Peau CERVINO', category: 'CYCLISME', type: 'haut', subtype: 'Combinaisons', sizeType: 'aero', pricingTiers: [{ quantity: 1, price: 120.00 }] },
            
            // =========== RUNNING ===========
            // --- Hauts ---
            { name: 'MAILLOT RUNNING HOMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 63.00 }, { quantity: 2, price: 54.00 }, { quantity: 3, price: 45.00 }, { quantity: 5, price: 36.00 }, { quantity: 15, price: 34.20 }, { quantity: 25, price: 33.12 }, { quantity: 50, price: 32.40 }, { quantity: 80, price: 31.32 }, { quantity: 150, price: 30.60 } ] },
            { name: 'MAILLOT RUNNING FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 63.00 }, { quantity: 2, price: 54.00 }, { quantity: 3, price: 45.00 }, { quantity: 5, price: 36.00 }, { quantity: 15, price: 34.20 }, { quantity: 25, price: 33.12 }, { quantity: 50, price: 32.40 }, { quantity: 80, price: 31.32 }, { quantity: 150, price: 30.60 } ] },
            { name: 'MAILLOT TRAIL HOMME MANCHES COURTES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotTrail', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 87.15 }, { quantity: 2, price: 74.70 }, { quantity: 3, price: 62.25 }, { quantity: 5, price: 49.80 }, { quantity: 15, price: 47.31 }, { quantity: 25, price: 45.82 }, { quantity: 50, price: 44.82 }, { quantity: 80, price: 43.33 }, { quantity: 150, price: 42.33 } ] },
            { name: 'MAILLOT TRAIL FEMME MANCHES COURTES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotTrail', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 87.15 }, { quantity: 2, price: 74.70 }, { quantity: 3, price: 62.25 }, { quantity: 5, price: 49.80 }, { quantity: 15, price: 47.31 }, { quantity: 25, price: 45.82 }, { quantity: 50, price: 44.82 }, { quantity: 80, price: 43.33 }, { quantity: 150, price: 42.33 } ] },
            { name: 'DEBARDEUR ATHLETISME HOMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'debardeurAthletisme', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 56.70 }, { quantity: 2, price: 48.60 }, { quantity: 3, price: 40.50 }, { quantity: 5, price: 32.40 }, { quantity: 15, price: 30.78 }, { quantity: 25, price: 29.81 }, { quantity: 50, price: 29.16 }, { quantity: 80, price: 28.19 }, { quantity: 150, price: 27.54 } ] },
            { name: 'DEBARDEUR ATHLETISME FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'debardeurAthletisme', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 56.70 }, { quantity: 2, price: 48.60 }, { quantity: 3, price: 40.50 }, { quantity: 5, price: 32.40 }, { quantity: 15, price: 30.78 }, { quantity: 25, price: 29.81 }, { quantity: 50, price: 29.16 }, { quantity: 80, price: 28.19 }, { quantity: 150, price: 27.54 } ] },
            { name: 'BRASSIERE RUNNING FEMME', category: 'RUNNING', type: 'haut', subtype: 'Hauts', hasOptions: false, pricingTiers: [ { quantity: 1, price: 68.25 }, { quantity: 2, price: 58.50 }, { quantity: 3, price: 48.75 }, { quantity: 5, price: 39.00 }, { quantity: 15, price: 37.05 }, { quantity: 25, price: 35.88 }, { quantity: 50, price: 35.10 }, { quantity: 80, price: 33.93 }, { quantity: 150, price: 33.15 } ] },
            { name: 'MAILLOT RUNNING HIVER HOMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
            { name: 'MAILLOT RUNNING HIVER FEMME MANCHES LONGUES', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingGroup: 'maillotRunningHiver', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 94.50 }, { quantity: 2, price: 81.00 }, { quantity: 3, price: 67.50 }, { quantity: 5, price: 54.00 }, { quantity: 15, price: 51.30 }, { quantity: 25, price: 49.68 }, { quantity: 50, price: 48.60 }, { quantity: 80, price: 46.98 }, { quantity: 150, price: 45.90 } ] },
            { name: 'GILET COUPE-VENT LEGER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
            { name: 'GILET COUPE-VENT MI-SAISON MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 98.70 }, { quantity: 2, price: 84.60 }, { quantity: 3, price: 70.50 }, { quantity: 5, price: 56.40 }, { quantity: 15, price: 53.58 }, { quantity: 25, price: 51.89 }, { quantity: 50, price: 50.76 }, { quantity: 80, price: 49.07 }, { quantity: 150, price: 47.94 } ] },
            { name: 'GILET COUPE-VENT HIVER MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 105.00 }, { quantity: 2, price: 90.00 }, { quantity: 3, price: 75.00 }, { quantity: 5, price: 60.00 }, { quantity: 15, price: 57.00 }, { quantity: 25, price: 55.20 }, { quantity: 50, price: 54.00 }, { quantity: 80, price: 52.20 }, { quantity: 150, price: 51.00 } ] },
            { name: 'COUPE-VENT LEGER MIXTE CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 96.60 }, { quantity: 2, price: 82.80 }, { quantity: 3, price: 69.00 }, { quantity: 5, price: 55.20 }, { quantity: 15, price: 52.44 }, { quantity: 25, price: 50.78 }, { quantity: 50, price: 49.68 }, { quantity: 80, price: 48.02 }, { quantity: 150, price: 46.92 } ] },
            { name: 'VESTE MI-SAISON HOMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
            { name: 'VESTE MI-SAISON FEMME CONFORT', category: 'RUNNING', type: 'haut', subtype: 'Hauts', pricingTiers: [ { quantity: 1, price: 136.50 }, { quantity: 2, price: 117.00 }, { quantity: 3, price: 97.50 }, { quantity: 5, price: 78.00 }, { quantity: 15, price: 74.10 }, { quantity: 25, price: 71.76 }, { quantity: 50, price: 70.20 }, { quantity: 80, price: 67.86 }, { quantity: 150, price: 66.30 } ] },
            // --- Bas ---
            { name: 'SHORT RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 79.80 }, { quantity: 2, price: 68.40 }, { quantity: 3, price: 57.00 }, { quantity: 5, price: 45.60 }, { quantity: 15, price: 43.32 }, { quantity: 25, price: 41.95 }, { quantity: 50, price: 41.04 }, { quantity: 80, price: 39.67 }, { quantity: 150, price: 38.76 } ] },
            { name: 'SHORTY FEMME RUNNING', category: 'RUNNING', type: 'haut', subtype: 'Bas', pricingGroup: 'cuissardShortyRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 84.00 }, { quantity: 2, price: 72.00 }, { quantity: 3, price: 60.00 }, { quantity: 5, price: 48.00 }, { quantity: 15, price: 45.60 }, { quantity: 25, price: 44.16 }, { quantity: 50, price: 43.20 }, { quantity: 80, price: 41.76 }, { quantity: 150, price: 40.80 } ] },
            { name: 'CUISSARD RUNNING HOMME', category: 'RUNNING', type: 'haut', subtype: 'Bas', pricingGroup: 'cuissardShortyRunning', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 84.00 }, { quantity: 2, price: 72.00 }, { quantity: 3, price: 60.00 }, { quantity: 5, price: 48.00 }, { quantity: 15, price: 45.60 }, { quantity: 25, price: 44.16 }, { quantity: 50, price: 43.20 }, { quantity: 80, price: 41.76 }, { quantity: 150, price: 40.80 } ] },
            { name: 'COLLANT RUNNING MIXTE', category: 'RUNNING', type: 'haut', subtype: 'Bas', excludedOptions: ['POCHE DOS ZIPPEE'], pricingTiers: [ { quantity: 1, price: 105.00 }, { quantity: 2, price: 90.00 }, { quantity: 3, price: 75.00 }, { quantity: 5, price: 60.00 }, { quantity: 15, price: 57.00 }, { quantity: 25, price: 55.20 }, { quantity: 50, price: 54.00 }, { quantity: 80, price: 52.20 }, { quantity: 150, price: 51.00 } ] },
            // --- Trifactions ---
            { name: 'TRIFONCTION HOMME COURTE DISTANCE Peau TRI GEL', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionCourte', pricingTiers: [ { quantity: 1, price: 134.40 }, { quantity: 2, price: 115.20 }, { quantity: 3, price: 96.00 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 } ] },
            { name: 'TRIFONCTION FEMME COURTE DISTANCE Peau TRI GEL', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionCourte', pricingTiers: [ { quantity: 1, price: 134.40 }, { quantity: 2, price: 115.20 }, { quantity: 3, price: 96.00 }, { quantity: 5, price: 76.80 }, { quantity: 15, price: 72.96 }, { quantity: 25, price: 70.66 }, { quantity: 50, price: 69.12 }, { quantity: 80, price: 66.82 }, { quantity: 150, price: 65.28 } ] },
            { name: 'TRIFONCTION HOMME HALF Peau TRI GEL, ZIP DEVANT OU DOS', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionHalf', pricingTiers: [ { quantity: 1, price: 184.80 }, { quantity: 2, price: 158.40 }, { quantity: 3, price: 132.00 }, { quantity: 5, price: 105.60 }, { quantity: 15, price: 100.32 }, { quantity: 25, price: 97.15 }, { quantity: 50, price: 95.04 }, { quantity: 80, price: 91.87 }, { quantity: 150, price: 89.76 } ] },
            { name: 'TRIFONCTION FEMME HALF Peau TRI GEL, ZIP DEVANT OU DOS', category: 'RUNNING', type: 'haut', subtype: 'Trifonctions', hasOptions: false, pricingGroup: 'trifonctionHalf', pricingTiers: [ { quantity: 1, price: 184.80 }, { quantity: 2, price: 158.40 }, { quantity: 3, price: 132.00 }, { quantity: 5, price: 105.60 }, { quantity: 15, price: 100.32 }, { quantity: 25, price: 97.15 }, { quantity: 50, price: 95.04 }, { quantity: 80, price: 91.87 }, { quantity: 150, price: 89.76 } ] },
            
            // =========== ACCESSOIRES ===========
            { name: 'BANDANA ÉTÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 12.00 }, { quantity: 20, price: 10.44 }, { quantity: 50, price: 10.20 } ] },
            { name: 'BANDEAU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 9.00 },  { quantity: 20, price: 8.40 }, { quantity: 50, price: 7.20 } ] },
            { name: 'TOUR DE COU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 10.20 }, { quantity: 20, price: 8.70 }, { quantity: 50, price: 8.40 } ] },
            { name: 'PASSE MONTAGNE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 18.00 }, { quantity: 20, price: 15.60 }, { quantity: 50, price: 14.40 } ] },
            { name: 'MANCHETTES HIVER VELO/RUNNING', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'manchettes', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 19.20 }, { quantity: 20, price: 16.80 }, { quantity: 50, price: 15.60 } ] },
            { name: 'JAMBIERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'jambieres', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 26.40 }, { quantity: 20, price: 24.00 }, { quantity: 50, price: 22.80 } ] },
            { name: 'GANTS ÉTÉ', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'gants', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 21.00 }, { quantity: 20, price: 18.00 }, { quantity: 50, price: 16.80 } ] },
            { name: 'TAPIS DE TRANSITION MULTISPORTS', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 10.80 }, { quantity: 20, price: 9.18 }, { quantity: 50, price: 8.40 } ] },
            { name: 'CHAUSSETTES AERO MIXTE 18cm', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'chaussettes', minQuantity: 10, pricingTiers: [ { quantity: 10, price: 21.00 }, { quantity: 20, price: 20.40 }, { quantity: 50, price: 19.20 } ] },
            { name: 'CHAUSSETTES VELO/COURSE A PIED Mixte Tige 13 ou 17cm', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'chaussettes', minQuantity: 50, pricingTiers: [ { quantity: 50, price: 12.60 }, { quantity: 100, price: 12.00 }, { quantity: 200, price: 12.00 } ] },
            { name: 'GAPETTE VELO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', minQuantity: 50, pricingTiers: [ { quantity: 50, price: 15.00 }, { quantity: 100, price: 13.20 }, { quantity: 200, price: 13.20 } ] },
            { name: 'DOSSARDS JEU DE 1 à 100', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 68.80}] },
            { name: 'DOSSARDS JEU DE 1 à 150', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 91.20}] },
            { name: 'DOSSARDS JEU DE 1 à 200', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 115.20}] },
            { name: 'DOSSARDS JEU DE 1 à 250', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 136.00}] },
            { name: 'DOSSARDS JEU DE 1 à 300', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERSONNALISÉS', sizeType:'unique', pricingTiers: [{quantity: 1, price: 158.40}] },
            { name: 'SOUS-MAILLOT SANS MANCHES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillot', price: 40, colors: ["blanc"]},
            { name: 'SOUS-MAILLOT MI-SAISON MANCHES COURTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillot', price: 45, colors: ["blanc"]},
            { name: 'SOUS-MAILLOT HIVER MANCHES LONGUES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'sousMaillotHiver', price: 55, colors: ["blanc"]},
            { name: 'SOUS CASQUE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 18, colors: ["NOIR"]},
            { name: 'CAGOULE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 20, colors: ["NOIR"]},
            { name: 'GANTS HIVER', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 55, colors: ["NOIR"]},
            { name: 'GANTS ETE CONFORT', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 30, colors: ["NOIR", "BLANC", "MARINE", "BRETON PUR BEURRE"]},
            { name: 'GANTS ETE SLIM', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gants', price: 40, colors: ["NOIR"]},
            { name: 'GANTS MI-SAISON', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'gantsMiSaison', price: 30, colors: ["NOIR"]},
            { name: 'COUVRE-CHAUSSURES AÉRO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'couvreChaussuresAero', price: 40, colors: ["NOIR"]},
            { name: 'COUVRE-CHAUSSURES HIVER/PLUIE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'couvreChaussuresHiver', price: 65, colors: ["NOIR"]},
            { name: 'BANDEAU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'bandeau', price: 12, colors: ["ARDENT", "FLUO", "HYPNOTIC", "BRETON PUR BEURRE"]},
            { name: 'TOUR DE COU', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'tourDeCou', price: 15, colors: ["ARDENT", "FLUO", "HYPNOTIC", "BRETON PUR BEURRE"]},
            { name: 'GAPETTE', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'unique', price: 20, colors: ["ARDENT", "MAGICREME", "HYPNOTIC", "NOIR"]},
            { name: 'MANCHETTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'manchettes', price: 33, colors: ["NOIR"]},
            { name: 'GENOUILLERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'manchettes', price: 33, colors: ["NOIR"]},
            { name: 'JAMBIERES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'jambieres', price: 40, colors: ["NOIR"]},
            { name: 'CHAUSSETTES AÉRO', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'chaussettes', price: 30, colors: ["NOIR", "BLANC", "ARDENT", "HYPNOTIC"]},
            { name: 'CHAUSSETTES MI-HAUTES', category: 'ACCESSOIRES', type: 'accessoire', subtype: 'ACCESSOIRES PERMANENTS', sizeType: 'chaussettes', price: 17, colors: ["NOIR", "BLANC", "BEIGE", "BRETON PUR BEURRE"]},
            
            // =========== ENFANTS ===========
            { name: 'MAILLOT ENFANT PERFORMANCE MC', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 42.00 }] },
            { name: 'MAILLOT VTT/DESCENTE ENFANT CONFORT MC', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 44.40 }] },
            { name: 'MAILLOT BMX ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 50.40 }] },
            { name: 'MAILLOT MI-SAISON ENFANT CONFORT ML', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 49.20 }] },
            { name: 'GILET COUPE-VENT LEGER ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 42.00 }] },
            { name: 'VESTE HIVER ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 90.00 }] },
            { name: 'CUISSARD ENFANT CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 42.00 }] },
            { name: 'COLLANT HIVER ENFANT SUBLIME CONFORT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 60.00 }] },
            { name: 'COLLANT ENFANT VELOURS ECHAUFFEMENT', category: 'ENFANTS', type: 'enfant', subtype: 'Cyclisme Enfant', hasOptions: false, pricingTiers: [{ quantity: 1, price: 48.00 }] },
            { name: 'MAILLOT RUNNING ENFANT MANCHES COURTES', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 30.00 }] },
            { name: 'DEBARDEUR ATHLETISME ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 27.00 }] },
            { name: 'CUISSARD RUNNING ENFANT', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 36.00 }] },
            { name: 'TRIFONCTION ENFANT COURTE DISTANCE', category: 'ENFANTS', type: 'enfant', subtype: 'Running Enfants', hasOptions: false, pricingTiers: [{ quantity: 1, price: 55.20 }] },

            // =========== OPTIONS ===========
            { name: 'POCHE DOS ZIPPEE', category: 'option', type: 'option', pricingTiers: [ { quantity: 1, price: 11.72 }, { quantity: 2, price: 9.38 }, { quantity: 3, price: 7.50 }, { quantity: 5, price: 6.00 }, { quantity: 15, price: 5.70 }, { quantity: 25, price: 5.52 }, { quantity: 50, price: 5.40 }, { quantity: 80, price: 5.22 }, { quantity: 150, price: 5.10 } ] },
            { name: 'BANDE REFLECTIVE', category: 'option', type: 'option', pricingTiers: [ { quantity: 1, price: 7.08 }, { quantity: 2, price: 5.40 }, { quantity: 3, price: 4.50 }, { quantity: 5, price: 3.60 }, { quantity: 15, price: 3.42 }, { quantity: 25, price: 3.31 }, { quantity: 50, price: 3.24 }, { quantity: 80, price: 3.13 }, { quantity: 150, price: 3.06 }, ] },
            { name: 'Ajustement Longueur +3cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
            { name: 'Ajustement Longueur +6cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
            { name: 'Ajustement Longueur +9cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
            { name: 'Ajustement Longueur -3cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
            { name: 'Ajustement Longueur -6cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
            { name: 'Ajustement Longueur -9cm', category: 'option', type: 'option', optionGroup: 'length', fixedPriceTTC: 7.20 },
        ];

        const productSizes = {
            haut: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
            enfant: ['6A', '8A', '10A', '12A', '14A', '16A'],
            aero: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL'],
            ample: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
            largeHaut: ['XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL', '3XL', '4XL', '5XL', '6XL'],
            manchettes: ["P (Biceps 27/31cm)", "G (Biceps 31/34cm)"],
            jambieres: ["P (Cuisses 39/44cm)", "G (Cuisses 44/50cm)"],
            unique: ["U"],
            gants: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],
            chaussettes: ["S/M (35/40)", "L/XL (41/46)"],
            sousMaillot: ["2XS/XS", "S/M", "L/XL", "2XL/3XL"],
            sousMaillotHiver: ["S", "M", "L", "XL"],
            gantsMiSaison: ["S", "M", "L", "XL"],
            couvreChaussuresAero: ["36/38", "39/41", "42/44", "45/47"],
            couvreChaussuresHiver: ["38/39", "40/42", "43/44", "45/46", "47/48"],
            bandeau: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],
            tourDeCou: ["XXS", "XS", "S", "M", "L", "XL", "XXL"],
        };

        const TVA_RATE = 0.20;
        const DOWN_PAYMENT_RATE = 0.30;
        const GRAPHIC_FEE_TTC = 150;
        
        // --- NOUVEAU: Logique et assistants de tri ---
        // Crée une map pour obtenir l'index de tri d'un produit en fonction de sa position dans la liste principale
        const productSortOrderMap = new Map(allAvailableProducts.map((p, i) => [p.name, i]));

        // Crée un ordre de tri maître pour toutes les tailles possibles afin d'assurer la cohérence
        const masterSizeOrder = [
            // Enfants
            '6A', '8A', '10A', '12A', '14A', '16A',
            // Sous-vêtements
            '2XS/XS',
            // Apparel principal
            'XXS', 'XS', 'S', 'M', 'L', 'XL', 'XXL',
            // Sous-vêtements
            'S/M', 'L/XL', '2XL/3XL',
            // Apparel principal (suite)
            '3XL', '4XL', '5XL', '6XL',
            // Chaussettes & Couvre-chaussures (par taille numérique)
            'S/M (35/40)', '36/38', '38/39', '39/41', 'L/XL (41/46)', '40/42', '42/44', '43/44', '44/45', '45/46', '45/47', '47/48',
            // Spécifiques (Manchettes, Jambières)
            "P (Biceps 27/31cm)", "G (Biceps 31/34cm)",
            "P (Cuisses 39/44cm)", "G (Cuisses 44/50cm)",
            // Unique
            'U'
        ];
        // Crée une map pour obtenir rapidement l'index de tri d'une taille
        const sizeSortOrderMap = new Map(masterSizeOrder.map((s, i) => [s, i]));

        // Fonction pour trier un tableau de lignes de commande
        const sortLineItems = (items) => {
            return [...items].sort((a, b) => {
                const productAOrder = productSortOrderMap.get(a.productName) ?? 9999;
                const productBOrder = productSortOrderMap.get(b.productName) ?? 9999;
                return productAOrder - productBOrder;
            });
        };

        // Fonction pour trier un tableau de clés de taille (ex: ['M', 'S', 'L'] -> ['S', 'M', 'L'])
        const sortSizeKeys = (sizeKeys) => {
            return [...sizeKeys].sort((a, b) => {
                const sizeAOrder = sizeSortOrderMap.get(a) ?? 999;
                const sizeBOrder = sizeSortOrderMap.get(b) ?? 999;
                return sizeAOrder - sizeBOrder;
            });
        };


        // --- APPLICATION STATE ---
        let state = {
            // UI State
            isGeneratingSpecificity: false,
            isSaving: false,
            activeTab: 'order-creation',
            isSavedOrdersTabLocked: true,
            savedOrders: [],
            excelColumns: {
                licencie: true, produit: true, specificite: true, options: true,
                tailles: true, qte: true, prixUHT: true, prixUTTC: true,
                totalHT: true, totalTTC: true, remise: true, geste: true
            },
            
            // Form State
            orderId: null, 
            isOrderValidated: false, 
            isReassort: false,
            isCustomDesignOrder: false,
            isStoreOrder: false,
            applyDiscount: false,
            clubName: '',
            departement: '',
            clientNumber: '',
            preOrderNumber: '',
            quoteNumber: '',
            orderDate: new Date().toISOString().split('T')[0],
            licencieName: '',
            clubDiscount: 0,
            currentOrderLineItems: [],
            discountType: 'global',
            orderScope: 'global',
            documentType: 'order',
            orderSpecificity: '',
            isImperatif: false,
            imperatifNote: '',

            // Item-specific State
            currentProduct: '',
            applyCommercialGesture: false,
            currentQuantities: {},
            currentCalculatedUnitPrice: 0,
            manualUnitPrice: '',
            currentSelectedOptions: [],
            currentSpecificity: '',
            currentAccessoryQuantity: '',
            currentColor: '',
        };

        // --- DOM Element References ---
        const dom = {
            // Modals
            mainModal: document.getElementById('main-modal'),
            mainModalTitle: document.getElementById('main-modal-title'),
            mainModalBody: document.getElementById('main-modal-body'),
            mainModalActions: document.getElementById('main-modal-actions'),
            excelModal: document.getElementById('excel-modal'),
            excelColumnsContainer: document.getElementById('excel-columns-container'),
            
            // Order Info
            docTypeOrderRadio: document.getElementById('doc-type-order'),
            docTypeQuoteRadio: document.getElementById('doc-type-quote'),
            docTypeReassortCheck: document.getElementById('doc-type-reassort'),
            customDesignOrderCheck: document.getElementById('custom-design-order-check'),
            orderScopeContainer: document.getElementById('order-scope-container'),
            scopeGlobalRadio: document.getElementById('scope-global'),
            scopeLicenseeRadio: document.getElementById('scope-licensee'),
            storeOrderCheck: document.getElementById('store-order-check'),
            applyDiscountCheck: document.getElementById('apply-discount-check'),
            clubName: document.getElementById('clubName'),
            departement: document.getElementById('departement'),
            clientNumber: document.getElementById('clientNumber'),
            preOrderNumberContainer: document.getElementById('pre-order-number-container'),
            preOrderNumber: document.getElementById('preOrderNumber'),
            quoteNumberContainer: document.getElementById('quote-number-container'),
            quoteNumber: document.getElementById('quoteNumber'),
            orderDate: document.getElementById('orderDate'),
            orderSpecificity: document.getElementById('orderSpecificity'),
            licencieNameContainer: document.getElementById('licencieName-container'),
            licencieName: document.getElementById('licencieName'),
            imperatifCheck: document.getElementById('imperatif-check'),
            imperatifNoteContainer: document.getElementById('imperatif-note-container'),
            imperatifNote: document.getElementById('imperatif-note'),
            
            // Product Form
            productTabsContainer: document.getElementById('product-tabs-container'),
            productFormContainer: document.getElementById('product-form-container'),
            
            // Main Tabs
            mainTabsContainer: document.getElementById('main-tabs-container'),
            orderCreationTabContent: document.getElementById('order-creation'),
            savedOrdersTabContent: document.getElementById('saved-orders'),
            savedOrdersList: document.getElementById('saved-orders-list'),
            importOrdersJsonBtn: document.getElementById('import-orders-json-btn'),
            exportOrdersJsonBtn: document.getElementById('export-orders-json-btn'),
            jsonOrdersFileInput: document.getElementById('json-orders-file-input'),

            // Order Table
            orderTableBody: document.getElementById('order-table-body'),
            orderTableHead: document.getElementById('order-table-head'),
            totalArticlesDisplay: document.getElementById('total-articles-display'),

            // Totals & Actions
            discountControlsContainer: document.getElementById('discount-controls-container'),
            discountGlobalRadio: document.getElementById('discount-global'),
            discountItemRadio: document.getElementById('discount-item'),
            clubDiscount: document.getElementById('clubDiscount'),
            subtotalHT: document.getElementById('subtotal-ht'),
            subtotalTTC: document.getElementById('subtotal-ttc'),
            discountAmountHT: document.getElementById('discount-amount-ht'),
            discountAmountTTC: document.getElementById('discount-amount-ttc'),
            graphicFeeHtRow: document.getElementById('graphic-fee-ht-row'),
            graphicFeeTtcRow: document.getElementById('graphic-fee-ttc-row'),
            graphicDesignFeeHT: document.getElementById('graphic-design-fee-ht'),
            graphicDesignFeeTTC: document.getElementById('graphic-design-fee-ttc'),
            shippingHT: document.getElementById('shipping-ht'),
            shippingTTC: document.getElementById('shipping-ttc'),
            grandTotalHT: document.getElementById('grand-total-ht'),
            grandTotalTTC: document.getElementById('grand-total-ttc'),
            downPaymentLabel: document.getElementById('down-payment-label'),
            downPayment: document.getElementById('down-payment'),
            newOrderBtn: document.getElementById('new-order-btn'),
            customizeExportBtn: document.getElementById('customize-export-btn'),
            exportExcelBtn: document.getElementById('export-excel-btn'),
            exportPdfBtn: document.getElementById('export-pdf-btn'),
            sendEmailBtn: document.getElementById('send-email-btn'),
            saveOrderBtn: document.getElementById('save-order-btn'),
        };
        
        // --- DATABASE & AUTOCOMPLETE FUNCTIONS ---
        let clientDatabase = [];

        const saveClientInfo = () => {
            const clubName = dom.clubName.value.trim();
            const clientNumber = dom.clientNumber.value.trim();
            const departement = dom.departement.value.trim();

            if (clubName && clientNumber) {
                let clientFound = false;
                // Check for existing club and update its details
                clientDatabase.forEach(client => {
                    if (client.clubName === clubName) {
                        client.clientNumber = clientNumber;
                        client.departement = departement;
                        clientFound = true;
                    }
                });

                // If club is not found, add it as a new entry
                if (!clientFound) {
                    clientDatabase.push({ clubName, clientNumber, departement });
                }

                localStorage.setItem('clientDatabase', JSON.stringify(clientDatabase));
                updateDatalists();
            }
        };

        const updateDatalists = () => {
            const clubList = document.getElementById('club-list');
            const clientList = document.getElementById('client-list');
            if(clubList) {
                clubList.innerHTML = clientDatabase.map(c => `<option value="${c.clubName}"></option>`).join('');
            }
            if(clientList){
                clientList.innerHTML = clientDatabase.map(c => `<option value="${c.clientNumber}"></option>`).join('');
            }
        };


        // --- HELPER FUNCTIONS ---
        const getPriceForQuantity = (pricingTiers, totalQuantity, applyGesture = false) => {
            if (!pricingTiers || pricingTiers.length === 0) return 0;
            let currentTierIndex = -1;
            for (let i = pricingTiers.length - 1; i >= 0; i--) {
                if (totalQuantity >= pricingTiers[i].quantity) {
                    currentTierIndex = i;
                    break;
                }
            }
            if(currentTierIndex === -1) currentTierIndex = 0;

            if (applyGesture) {
                const currentTierQuantity = pricingTiers[currentTierIndex].quantity;
                let nextTierIndex = -1;
                for(let i = 0; i < pricingTiers.length; i++){
                    if(pricingTiers[i].quantity > currentTierQuantity){
                        nextTierIndex = i;
                        break;
                    }
                }
                if (nextTierIndex !== -1) {
                    return pricingTiers[nextTierIndex].price;
                }
            }
            return pricingTiers[currentTierIndex].price;
        };

        const showModal = (modalElement, title, content, actions = []) => {
            dom.mainModalTitle.textContent = title;
            dom.mainModalBody.innerHTML = '';
            dom.mainModalBody.appendChild(content);

            dom.mainModalActions.innerHTML = '';
            if (actions.length > 0) {
                 actions.forEach(action => {
                     const button = document.createElement('button');
                     button.textContent = action.label;
                     button.className = `${action.className || 'bg-indigo-600 hover:bg-indigo-700 text-white'} font-bold py-2 px-4 rounded-lg transition duration-300 ease-in-out transform hover:scale-105`;
                     button.onclick = action.onClick;
                     dom.mainModalActions.appendChild(button);
                 });
            } else {
                 const closeButton = document.createElement('button');
                 closeButton.textContent = 'Fermer';
                 closeButton.className = 'bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-lg';
                 closeButton.onclick = () => modalElement.classList.remove('is-open');
                 dom.mainModalActions.appendChild(closeButton);
            }
            modalElement.classList.add('is-open');
        };

        const hideModal = (modalElement) => {
            modalElement.classList.remove('is-open');
        };

        const createSpinner = () => {
             const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
             svg.setAttribute('class', 'animate-spin -ml-1 mr-3 h-5 w-5 text-white');
             svg.setAttribute('fill', 'none');
             svg.setAttribute('viewBox', '0 0 24 24');
             svg.innerHTML = `
                 <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                 <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
             `;
             return svg;
        }

        // --- RENDER FUNCTIONS ---
        const renderAll = () => {
            renderTotals();
            renderOrderTable();
            renderUIState();
            renderProductForm();
            renderSavedOrders();
        };

        const renderUIState = () => {
            // Update input fields with state
            dom.clubName.value = state.clubName;
            dom.departement.value = state.departement;
            dom.clientNumber.value = state.clientNumber;
            dom.preOrderNumber.value = state.preOrderNumber;
            dom.quoteNumber.value = state.quoteNumber;
            dom.orderDate.value = state.orderDate;
            dom.orderSpecificity.value = state.orderSpecificity;
            dom.licencieName.value = state.licencieName;
            dom.clubDiscount.value = state.clubDiscount;
            dom.imperatifNote.value = state.imperatifNote;

            // Update radio/checkboxes
            dom.docTypeOrderRadio.checked = state.documentType === 'order';
            dom.docTypeQuoteRadio.checked = state.documentType === 'quote';
            dom.docTypeReassortCheck.checked = state.isReassort;
            dom.customDesignOrderCheck.checked = state.isCustomDesignOrder;
            dom.storeOrderCheck.checked = state.isStoreOrder;
            dom.applyDiscountCheck.checked = state.applyDiscount;
            dom.imperatifCheck.checked = state.isImperatif;
            dom.scopeGlobalRadio.checked = state.orderScope === 'global';
            dom.scopeLicenseeRadio.checked = state.orderScope === 'licensee';
            dom.discountGlobalRadio.checked = state.discountType === 'global';
            dom.discountItemRadio.checked = state.discountType === 'item';

            // Show/hide elements based on state
            dom.licencieNameContainer.classList.toggle('hidden', !(state.orderScope === 'licensee' && state.documentType === 'order'));
            dom.orderScopeContainer.classList.toggle('hidden', state.documentType !== 'order');
            dom.preOrderNumberContainer.classList.toggle('hidden', state.documentType !== 'order');
            dom.quoteNumberContainer.classList.toggle('hidden', state.documentType !== 'quote');
            dom.discountControlsContainer.classList.toggle('hidden', !state.applyDiscount);
            dom.imperatifNoteContainer.classList.toggle('hidden', !state.isImperatif);
            
            // Update save button text
            const buttonText = state.orderId ? 'Mettre à jour la Commande' : 'Sauvegarder la Commande';
            dom.saveOrderBtn.disabled = state.isSaving;
            if (state.isSaving) {
                 dom.saveOrderBtn.innerHTML = '';
                 dom.saveOrderBtn.appendChild(createSpinner());
                 dom.saveOrderBtn.append('Sauvegarde...');
            } else {
                 dom.saveOrderBtn.textContent = buttonText;
            }

            // MODIFIÉ: Les boutons d'export sont activés seulement si la commande est validée
            dom.exportPdfBtn.disabled = !state.isOrderValidated;
            dom.exportExcelBtn.disabled = !state.isOrderValidated;
            dom.sendEmailBtn.disabled = !state.isOrderValidated;

            renderOrderTableHead();
        };
        
        const renderOrderTableHead = () => {
            let headers = '';
            
            if (state.orderScope === 'licensee' && state.documentType === 'order') {
                // No extra header for licensee name, it's a row.
            }
             if (state.applyDiscount && state.discountType === 'item') {
                  headers += `<th class="px-2 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Remise</th>`;
             }
            
            headers += `
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Produit</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Qté</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Prix U. HT</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Prix U. TTC</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total HT</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Total TTC</th>
                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">Actions</th>
            `;
            dom.orderTableHead.innerHTML = `<tr>${headers}</tr>`;
        };
        
        const renderOrderTable = () => {
            if (state.currentOrderLineItems.length === 0) {
                dom.orderTableBody.innerHTML = `<tr><td colspan="9" class="px-6 py-12 text-center text-gray-500">Aucun article.</td></tr>`;
                return;
            }

            let tableHtml = '';
            const sortedItems = sortLineItems(state.currentOrderLineItems);

            if (state.orderScope === 'licensee' && state.documentType === 'order') {
                const groupedItems = sortedItems.reduce((acc, item) => {
                    const key = item.licencieName || 'Commande Globale';
                    if (!acc[key]) acc[key] = [];
                    acc[key].push(item);
                    return acc;
                }, {});

                for (const licensee in groupedItems) {
                    let licenseeSubtotal = 0;
                    tableHtml += `
                        <tr class="bg-indigo-50">
                            <td colspan="8" class="px-6 py-2 text-left text-sm font-bold text-indigo-800">${licensee}</td>
                        </tr>
                    `;
                    tableHtml += groupedItems[licensee].map(item => {
                         let itemTotal = item.totalPriceTTC;
                         if(state.applyDiscount && (state.discountType === 'global' || (state.discountType === 'item' && item.applyDiscount))){
                              itemTotal -= (item.totalPriceTTC * (state.clubDiscount / 100));
                         }
                         licenseeSubtotal += itemTotal;
                        return renderSingleItemRow(item); 
                    }).join('');
                     tableHtml += `
                        <tr class="bg-indigo-100 font-bold">
                            <td colspan="8" class="px-6 py-2 text-right text-sm text-indigo-800">Total à régler pour ${licensee}: ${licenseeSubtotal.toFixed(2)}€</td>
                        </tr>
                    `;
                }

            } else {
                 tableHtml = sortedItems.map(item => renderSingleItemRow(item)).join('');
            }


            dom.orderTableBody.innerHTML = tableHtml;
        };
        
        const renderSingleItemRow = (item) => {
            const gestureIndicator = item.applyGesture ? `<span class="text-green-500 ml-1" title="Geste commercial appliqué">✳</span>` : '';
            const sortedSizeKeys = sortSizeKeys(Object.keys(item.quantitiesPerSize));
            const sizesText = sortedSizeKeys.map(size => `${size}: ${item.quantitiesPerSize[size]}`).join(', ');
            
            const optionsText = item.options.length > 0 ? `<div class="text-xs text-blue-500">Options: ${item.options.join(', ')}</div>` : '';
            const specificityText = item.specificity ? `<div class="text-xs text-gray-600 italic mt-1">Note: ${item.specificity}</div>` : '';
            
            let rowHtml = `<tr class="hover:bg-gray-50">`;
            
            if (state.applyDiscount && state.discountType === 'item') {
                rowHtml += `<td class="px-2 py-4 whitespace-nowrap"><input type="checkbox" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" data-item-id="${item.id}" data-action="toggle-discount" ${item.applyDiscount ? 'checked' : ''}></td>`;
            }
            
            let totalTTCDisplay = `${item.totalPriceTTC.toFixed(2)}€`;
            if (state.applyDiscount && ((state.discountType === 'global') || (state.discountType === 'item' && item.applyDiscount))) {
                const discountedPrice = item.totalPriceTTC * (1 - (state.clubDiscount / 100));
                totalTTCDisplay = `
                    <div class="flex flex-col items-end">
                        <span class="line-through text-gray-500 text-xs">${item.totalPriceTTC.toFixed(2)}€</span>
                        <span class="text-red-600 font-bold">${discountedPrice.toFixed(2)}€</span>
                    </div>
                `;
            }

            rowHtml += `
                <td class="px-6 py-4 whitespace-nowrap">
                    <div class="text-sm font-medium text-gray-900">${item.productName}${gestureIndicator}</div>
                    <div class="text-xs text-gray-500">${sizesText}</div>
                    ${optionsText}
                    ${specificityText}
                </td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.totalQuantity}</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.unitPriceHT.toFixed(2)}€</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">${item.unitPriceTTC.toFixed(2)}€</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-bold text-gray-900">${item.totalPriceHT.toFixed(2)}€</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-bold text-gray-900">${totalTTCDisplay}</td>
                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                    <button data-action="edit-item" data-item-id="${item.id}" class="text-blue-600 hover:text-blue-900 mr-4">Modifier</button>
                    <button data-action="remove-item" data-item-id="${item.id}" class="text-red-600 hover:text-red-900">Supprimer</button>
                </td>
            </tr>`;
            return rowHtml;
        };

        const renderTotals = () => {
            const totals = calculateAllTotals();
            dom.subtotalHT.textContent = `${totals.subtotalHT.toFixed(2)}€`;
            dom.subtotalTTC.textContent = `${totals.subtotalTTC.toFixed(2)}€`;
            dom.discountAmountHT.textContent = `-${totals.discountAmountHT.toFixed(2)}€`;
            dom.discountAmountTTC.textContent = `-${totals.discountAmountTTC.toFixed(2)}€`;
            
            dom.graphicDesignFeeHT.textContent = `${totals.appliedGraphicFeeHT.toFixed(2)}€`;
            dom.graphicDesignFeeTTC.textContent = `${totals.appliedGraphicFeeTTC.toFixed(2)}€`;
            dom.graphicFeeHtRow.classList.toggle('hidden', totals.appliedGraphicFeeTTC <= 0);
            dom.graphicFeeTtcRow.classList.toggle('hidden', totals.appliedGraphicFeeTTC <= 0);

            dom.shippingHT.textContent = `${totals.shippingHT.toFixed(2)}€`;
            dom.shippingTTC.textContent = `${totals.shippingTTC.toFixed(2)}€`;
            dom.grandTotalHT.textContent = `${totals.grandTotalHT.toFixed(2)}€`;
            dom.grandTotalTTC.textContent = `${totals.grandTotalTTC.toFixed(2)}€`;
            dom.downPaymentLabel.textContent = totals.downPaymentLabel;
            dom.downPayment.textContent = `${totals.downPayment.toFixed(2)}€`;
            dom.totalArticlesDisplay.textContent = `Total des articles : ${state.currentOrderLineItems.reduce((acc, item) => acc + item.totalQuantity, 0)}`;
        };
        
        const renderProductForm = () => {
            const productsToShow = allAvailableProducts.filter(p => {
                if (p.category === 'option') return false; // Always exclude options from the main list
                const activeProductTab = document.querySelector('.product-tab-btn.border-indigo-500')?.dataset.tab;
                if (activeProductTab === 'CYCLISME/RUNNING') return p.category === 'CYCLISME' || p.category === 'RUNNING';
                if (activeProductTab === 'Accessoires') return p.category === 'ACCESSOIRES';
                if (activeProductTab === 'GAMME ENFANTS') return p.category === 'ENFANTS';
                return false;
            });

            const grouped = productsToShow.reduce((acc, p) => {
                const groupKey = `${p.category} - ${p.subtype}`;
                acc[groupKey] = [...(acc[groupKey] || []), p];
                return acc;
            }, {});

            const productSelectorOptions = Object.entries(grouped).map(([groupLabel, productList]) => 
                `<optgroup label="${groupLabel}">
                    ${productList.map(p => `<option value="${p.name}" ${state.currentProduct === p.name ? 'selected' : ''}>${p.name}</option>`).join('')}
                </optgroup>`
            ).join('');

            let formHtml = `
                <div class="space-y-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700">Produit</label>
                        <select id="current-product-select" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                            <option value="">-- Sélectionner un produit --</option>
                            ${productSelectorOptions}
                        </select>
                    </div>
            `;

            const productData = allAvailableProducts.find(p => p.name === state.currentProduct);
            if(productData){
                formHtml += `<div id="product-details-form" class="space-y-6">`;
                const isQuote = state.documentType === 'quote';
                const showSizeGrid = !isQuote && productData && (productData.type === 'haut' || productData.type === 'enfant' || (productData.type === 'accessoire' && productData.sizeType && productData.sizeType !== 'unique'));
                const showSingleQuantityForOrder = !isQuote && (productData?.type === 'accessoire' && (!productData.sizeType || productData.sizeType === 'unique'));

                if(isQuote){
                    formHtml += `
                     <div>
                         <label for="quote-qty" class="block text-sm font-medium text-gray-700">Quantité</label>
                         <input type="number" id="quote-qty" value="${state.currentAccessoryQuantity}" min="1" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="0">
                    </div>`;
                }
                
                if(showSizeGrid){
                    const sizes = productSizes[productData.sizeType || productData.type] || [];
                    const sizeInputs = sizes.map(size => `
                        <div key="${size}">
                            <label for="size-${size}" class="block text-sm font-medium text-gray-700">${size}</label>
                            <input type="number" id="size-${size}" data-size="${size}" class="size-input mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="0" value="${state.currentQuantities[size] || ''}">
                        </div>
                    `).join('');
                    formHtml += `<div class="grid grid-cols-3 sm:grid-cols-4 md:grid-cols-5 gap-3">${sizeInputs}</div>`;
                }

                if(showSingleQuantityForOrder){
                    formHtml += `
                        <div>
                            <label for="accessory-qty" class="block text-sm font-medium text-gray-700">Quantité</label>
                            <input type="number" id="accessory-qty" value="${state.currentAccessoryQuantity}" min="1" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                            ${productData.minQuantity && state.orderScope === 'global' ? `<p class="text-xs text-gray-500 mt-1">Quantité minimale : ${productData.minQuantity}</p>` : ''}
                        </div>
                    `;
                }
                
               if (productData.colors) {
                    const colorOptions = productData.colors.map(c => `<option value="${c}" ${state.currentColor === c ? 'selected' : ''}>${c}</option>`).join('');
                     formHtml += `
                        <div>
                            <label class="block text-sm font-medium text-gray-700">Coloris</label>
                            <select id="current-color-select" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">
                                <option value="">-- Sélectionner un coloris --</option>
                                ${colorOptions}
                            </select>
                        </div>
                    `;
               }

                formHtml += `<div class="flex justify-between items-center gap-4">`;
                if(state.isReassort && productData.type !== 'accessoire'){
                     formHtml += `
                        <div class="flex-grow">
                            <label for="manual-price" class="block text-sm font-medium text-gray-700">Prix U. TTC Manuel</label>
                            <input type="number" id="manual-price" value="${state.manualUnitPrice}" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm" placeholder="0.00">
                        </div>
                    `;
                } else if(state.currentCalculatedUnitPrice > 0) {
                     formHtml += `
                        <div class="bg-indigo-50 p-4 rounded-lg flex-grow">
                            <p class="text-indigo-800 font-semibold">Prix unitaire TTC : <span class="text-xl">${state.currentCalculatedUnitPrice.toFixed(2)}€</span></p>
                        </div>
                    `;
                }
                formHtml += `<button id="add-product-btn" class="inline-flex items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 disabled:bg-indigo-300">Ajouter Article</button></div>`;


                if(productData.pricingTiers && productData.pricingTiers.length > 1 && !state.isReassort && state.documentType !== 'quote'){
                    formHtml += `
                        <div class="mt-4">
                            <label class="flex items-center">
                                <input type="checkbox" id="commercial-gesture-check" class="h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" ${state.applyCommercialGesture ? 'checked' : ''}>
                                <span class="ml-2 text-sm text-gray-800">Geste commercial (passer au palier tarifaire suivant)</span>
                            </label>
                        </div>
                    `;
                }
                
                let normalOptions = [];
                let lengthOptions = [];

                const isLongSleeved = productData.type === 'haut' && (productData.name.includes('ML') || productData.name.includes('MANCHES LONGUES'));
                
                if (productData.isCuissardOrCollant || isLongSleeved) {
                    lengthOptions = allAvailableProducts.filter(p => p.optionGroup === 'length');
                }
                
                if (productData.hasOptions !== false && productData.type !== 'accessoire' && !productData.isCuissardOrCollant) {
                    normalOptions = allAvailableProducts.filter(p =>
                        p.category === 'option' && !p.optionGroup && !productData.excludedOptions?.includes(p.name)
                    );
                }

                if (lengthOptions.length > 0) {
                    const optionCheckboxes = lengthOptions.map(opt => `
                        <div class="flex items-center">
                           <input id="option-${opt.name}" type="checkbox" data-option-name="${opt.name}" data-option-group="length" class="option-checkbox h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" ${state.currentSelectedOptions.includes(opt.name) ? 'checked' : ''}>
                           <label for="option-${opt.name}" class="ml-3 block text-sm text-gray-900">${opt.name.replace('Ajustement Longueur ', '')} (+${opt.fixedPriceTTC.toFixed(2)}€)</label>
                        </div>
                    `).join('');
                    formHtml += `<div><label class="block text-sm font-medium text-gray-700 mb-2">Ajustement Longueur</label><div class="grid grid-cols-2 md:grid-cols-3 gap-x-4 gap-y-2">${optionCheckboxes}</div></div>`;
                }

                 if (normalOptions.length > 0) {
                    const optionCheckboxes = normalOptions.map(opt => `
                         <div class="flex items-center">
                              <input id="option-${opt.name}" type="checkbox" data-option-name="${opt.name}" class="option-checkbox h-4 w-4 rounded border-gray-300 text-indigo-600 focus:ring-indigo-500" ${state.currentSelectedOptions.includes(opt.name) ? 'checked' : ''}>
                              <label for="option-${opt.name}" class="ml-3 block text-sm text-gray-900">${opt.name}</label>
                         </div>
                    `).join('');
                    formHtml += `<div><label class="block text-sm font-medium text-gray-700 mb-2">Options</label><div class="space-y-2">${optionCheckboxes}</div></div>`;
                 }
                
                formHtml += `
                    <div>
                        <label for="specificity" class="block text-sm font-medium text-gray-700">Spécificité / Notes</label>
                        <textarea id="specificity" rows="3" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm">${state.currentSpecificity}</textarea>
                        ${ productData?.type !== 'accessoire' && !state.isReassort ? 
                            `<button id="generate-specificity-btn" class="mt-2 inline-flex items-center px-4 py-2 border border-transparent text-sm font-medium rounded-md shadow-sm text-white bg-green-600 hover:bg-green-700 disabled:bg-green-300" ${state.isGeneratingSpecificity ? 'disabled' : ''}>
                                ✨ Générer avec IA
                            </button>` : ''
                        }
                    </div>
                `;
                formHtml += `</div>`; // End of product-details-form
            }
            formHtml += `</div>`; // End of space-y-6 wrapper

            dom.productFormContainer.innerHTML = formHtml;
        };

        const renderSavedOrders = () => {
            const listContainer = dom.savedOrdersList;
            if (!listContainer) return;
            listContainer.innerHTML = ''; 

            if (state.savedOrders.length === 0) {
                listContainer.innerHTML = '<p class="text-gray-500">Aucune commande sauvegardée.</p>';
                return;
            }

            const sortedOrders = [...state.savedOrders].sort((a, b) => new Date(b.savedAt) - new Date(a.savedAt));

            sortedOrders.forEach(order => {
                const orderCard = document.createElement('div');
                orderCard.className = 'p-4 border rounded-lg flex justify-between items-center bg-white shadow-sm';
                orderCard.innerHTML = `
                    <div>
                        <p class="font-bold text-indigo-700">${order.clubName}</p>
                        <p class="text-sm text-gray-600">N° Commande: ${order.preOrderNumber || order.quoteNumber || 'N/A'} - Date: ${order.orderDate}</p>
                        <p class="text-xs text-gray-500">Sauvegardé le: ${new Date(order.savedAt).toLocaleString('fr-FR')}</p>
                    </div>
                    <div class="flex gap-2">
                        <button data-action="load-order" data-order-id="${order.orderId}" class="px-4 py-2 text-sm font-medium text-white bg-blue-600 rounded-lg hover:bg-blue-700">Charger</button>
                        <button data-action="delete-order" data-order-id="${order.orderId}" class="px-4 py-2 text-sm font-medium text-white bg-red-600 rounded-lg hover:bg-red-700">Supprimer</button>
                    </div>
                `;
                listContainer.appendChild(orderCard);
            });
        };


        // --- CALCULATION LOGIC ---

        const calculateCurrentFormPrice = () => {
            const product = allAvailableProducts.find(p => p.name === state.currentProduct);
            if (!product || (state.isReassort && product.type !== 'accessoire')) {
                state.currentCalculatedUnitPrice = 0;
                return;
            }

            let currentFormQuantity = 0;
            if (state.documentType === 'quote') {
                 currentFormQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
            } else {
                 if (product.type === 'accessoire') {
                     if(product.sizeType && product.sizeType !== 'unique') {
                         currentFormQuantity = Object.values(state.currentQuantities).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
                     } else {
                         currentFormQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
                     }
                 } else if (product.type === 'haut' || product.type === 'enfant') {
                    currentFormQuantity = Object.values(state.currentQuantities).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
                 }
            }
        
            let groupQuantityInCart = 0;
            if (product.name === 'CHAUSSETTES AERO MIXTE 18cm') {
                groupQuantityInCart = state.currentOrderLineItems
                    .filter(li => li.productName === product.name && li.specificity === state.currentSpecificity)
                    .reduce((sum, li) => sum + li.totalQuantity, 0);
            } else if (product.pricingGroup || (product.subtype === 'ACCESSOIRES PERSONNALISÉS' && product.pricingTiers)) {
                groupQuantityInCart = state.currentOrderLineItems
                    .filter(li => {
                        const liProduct = allAvailableProducts.find(p => p.name === li.productName);
                        const isSameGroup = product.pricingGroup ? (liProduct && liProduct.pricingGroup === product.pricingGroup) : (li.productName === product.name);
                        return isSameGroup;
                    })
                    .reduce((sum, li) => sum + li.totalQuantity, 0);
            }
        
            const totalPricingQuantity = currentFormQuantity + groupQuantityInCart;
        
            const basePrice = product.price ? product.price : getPriceForQuantity(product.pricingTiers, totalPricingQuantity, state.applyCommercialGesture);
        
            const optionsPrice = state.currentSelectedOptions.reduce((total, optionName) => {
                const optionProduct = allAvailableProducts.find(p => p.name === optionName);
                if (!optionProduct) return total;
            
                if (optionProduct.fixedPriceTTC) {
                    return total + optionProduct.fixedPriceTTC;
                }
                if (optionProduct.pricingTiers) {
                    return total + getPriceForQuantity(optionProduct.pricingTiers, totalPricingQuantity, state.applyCommercialGesture);
                }
                return total;
            }, 0);
        
            state.currentCalculatedUnitPrice = basePrice + optionsPrice;
        };

        const calculateAllTotals = () => {
            const updatedLineItems = state.currentOrderLineItems.map(item => {
                const product = allAvailableProducts.find(p => p.name === item.productName);
                if (!product) return item;

                let pricingQuantity = item.totalQuantity;
                
                let baseUnitPriceTTC;
                if (item.isManualPrice) {
                    baseUnitPriceTTC = item.initialManualPrice;
                } else {
                    if (product.name === 'CHAUSSETTES AERO MIXTE 18cm') {
                        pricingQuantity = state.currentOrderLineItems
                            .filter(li => li.productName === product.name && li.specificity === item.specificity)
                            .reduce((sum, li) => sum + li.totalQuantity, 0);
                    } else if (product.pricingGroup || (product.subtype === 'ACCESSOIRES PERSONNALISÉS' && product.pricingTiers)) {
                        pricingQuantity = state.currentOrderLineItems
                        .filter(li => {
                            const liProduct = allAvailableProducts.find(p => p.name === li.productName);
                            const isSameGroup = product.pricingGroup ? (liProduct && liProduct.pricingGroup === product.pricingGroup) : (li.productName === item.productName);
                            return isSameGroup;
                        })
                        .reduce((sum, li) => sum + li.totalQuantity, 0);
                    }
                   baseUnitPriceTTC = product.price ? product.price : getPriceForQuantity(product.pricingTiers, pricingQuantity, item.applyGesture);
                }
                
                const totalOptionsPriceTTC = item.options.reduce((total, optionName) => {
                    const optionProduct = allAvailableProducts.find(p => p.name === optionName);
                    if (!optionProduct) return total;
                    if (optionProduct.fixedPriceTTC) return total + optionProduct.fixedPriceTTC;
                    if (optionProduct.pricingTiers) return total + getPriceForQuantity(optionProduct.pricingTiers, pricingQuantity, item.applyGesture);
                    return total;
                }, 0);

                const finalUnitPriceTTC = baseUnitPriceTTC + totalOptionsPriceTTC;
                const totalPriceTTC = finalUnitPriceTTC * item.totalQuantity;
                const finalUnitPriceHT = finalUnitPriceTTC / (1 + TVA_RATE);
                const totalPriceHT = totalPriceTTC / (1 + TVA_RATE);

                return { ...item, unitPriceTTC: finalUnitPriceTTC, unitPriceHT: finalUnitPriceHT, totalPriceTTC, totalPriceHT };
            });

            state.currentOrderLineItems = updatedLineItems;

            const originalSubtotalHT = updatedLineItems.reduce((acc, item) => acc + item.totalPriceHT, 0);
            const originalSubtotalTTC = updatedLineItems.reduce((acc, item) => acc + item.totalPriceTTC, 0);

            let discountBaseHT = 0;
            if (state.applyDiscount && state.discountType === 'global') {
                discountBaseHT = originalSubtotalHT;
            } else if (state.applyDiscount && state.discountType === 'item') {
                discountBaseHT = updatedLineItems
                    .filter(item => item.applyDiscount)
                    .reduce((acc, item) => acc + item.totalPriceHT, 0);
            }
            const discountAmountHT = discountBaseHT * (state.clubDiscount / 100);
            const discountAmountTTC = discountAmountHT * (1 + TVA_RATE);

            let shippingHT = 0;
            if (originalSubtotalHT > 2000) shippingHT = 0;
            else if (originalSubtotalHT > 1000) shippingHT = 14;
            else if (originalSubtotalHT > 500) shippingHT = 12;
            else if (originalSubtotalHT > 0) shippingHT = 9.50;
            
            const shippingTTC = shippingHT * (1 + TVA_RATE);
            
            const appliedGraphicFeeTTC = state.isCustomDesignOrder ? GRAPHIC_FEE_TTC : 0;
            const appliedGraphicFeeHT = appliedGraphicFeeTTC / (1 + TVA_RATE);

            const grandTotalHT = originalSubtotalHT + shippingHT + appliedGraphicFeeHT;
            const grandTotalTTC = originalSubtotalTTC + shippingTTC + appliedGraphicFeeTTC;
            
            let downPayment = 0;
            let downPaymentLabel = 'Acompte à verser (30%):';
            if (state.isCustomDesignOrder) {
                downPayment = grandTotalTTC;
                downPaymentLabel = 'Solde à régler avant expédition:';
            } else {
                downPayment = grandTotalTTC * DOWN_PAYMENT_RATE;
            }
            
            return {
                subtotalHT: originalSubtotalHT, subtotalTTC: originalSubtotalTTC,
                discountAmountHT, discountAmountTTC, shippingHT, shippingTTC,
                appliedGraphicFeeHT, appliedGraphicFeeTTC,
                grandTotalHT, grandTotalTTC, downPayment, downPaymentLabel
            };
        };


        // --- EVENT HANDLERS & LOGIC ---
        
        const resetProductFormFields = () => {
            state.applyCommercialGesture = false;
            state.currentQuantities = {};
            state.currentCalculatedUnitPrice = 0;
            state.manualUnitPrice = '';
            state.currentSelectedOptions = [];
            state.currentSpecificity = '';
            state.currentAccessoryQuantity = '';
            state.currentColor = '';
        };

        const handleProductAdd = () => {
             const product = allAvailableProducts.find(p => p.name === state.currentProduct);
            if (!product) return;

            if (state.orderScope === 'licensee' && state.documentType === 'order' && !state.licencieName) {
                const p = document.createElement('p');
                p.textContent = `Veuillez saisir un nom de licencié avant d'ajouter un article.`;
                showModal(dom.mainModal, 'Erreur de validation', p);
                return;
            }

            let totalQuantity = 0;
            let quantitiesPerSize = {};
        
            if(state.documentType === 'quote') {
                totalQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
                quantitiesPerSize = { 'Total': totalQuantity };
            } else {
                if (product.type === 'accessoire') {
                     if (product.sizeType && product.sizeType !== 'unique') {
                         totalQuantity = Object.values(state.currentQuantities).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
                         quantitiesPerSize = { ...state.currentQuantities };
                     } else {
                         totalQuantity = parseInt(state.currentAccessoryQuantity, 10) || 0;
                         quantitiesPerSize = { 'U': totalQuantity }; 
                     }
                } else {
                    totalQuantity = Object.values(state.currentQuantities).reduce((sum, q) => sum + (parseInt(q, 10) || 0), 0);
                    quantitiesPerSize = { ...state.currentQuantities };
                }
            }


            if (totalQuantity === 0) {
                 const p = document.createElement('p');
                 p.textContent = `Veuillez entrer une quantité valide.`;
                 showModal(dom.mainModal, 'Erreur', p);
                return;
            }

            const isManualPriceItem = state.isReassort && product.type !== 'accessoire';
            if (isManualPriceItem && (!state.manualUnitPrice || parseFloat(state.manualUnitPrice) <= 0)) {
                const p = document.createElement('p');
                p.textContent = `Veuillez saisir un prix manuel valide pour le réassort.`;
                showModal(dom.mainModal, 'Erreur de Prix', p);
                return;
            }
        
            if(state.orderScope === 'global' && state.documentType === 'order' && product.subtype === 'ACCESSOIRES PERSONNALISÉS' && totalQuantity < product.minQuantity){
                const p = document.createElement('p');
                p.textContent = `La quantité minimale pour cet accessoire est de ${product.minQuantity}.`;
                showModal(dom.mainModal, 'Erreur', p);
                return;
            }

            const newLineItem = {
                id: Date.now(), productName: product.name, type: product.type,
                quantitiesPerSize, totalQuantity, 
                unitPriceTTC: 0, unitPriceHT: 0, totalPriceTTC: 0, totalPriceHT: 0,
                options: state.currentSelectedOptions, specificity: state.currentSpecificity,
                applyDiscount: true,
                applyGesture: !state.isReassort && state.orderScope === 'global' && state.documentType === 'order' ? state.applyCommercialGesture : false,
                licencieName: (state.documentType === 'order' && state.orderScope === 'licensee') ? state.licencieName : 'Commande Globale',
                color: state.currentColor,
                isManualPrice: isManualPriceItem,
                initialManualPrice: isManualPriceItem ? parseFloat(state.manualUnitPrice) : 0,
            };
        
            state.currentOrderLineItems.push(newLineItem);
            state.isOrderValidated = false;
            state.currentProduct = '';
            resetProductFormFields();
            renderAll();
        };
        
        const resetForm = () => {
            Object.assign(state, {
                isGeneratingSpecificity: false, isSaving: false,
                isReassort: false, isCustomDesignOrder: false, isStoreOrder: false, applyDiscount: false, clubName: '',
                departement: '', clientNumber: '', preOrderNumber: '', quoteNumber: '',
                orderDate: new Date().toISOString().split('T')[0], licencieName: '',
                clubDiscount: 0, currentOrderLineItems: [], discountType: 'global',
                orderScope: 'global', documentType: 'order', orderSpecificity: '',
                isImperatif: false, imperatifNote: '',
                orderId: null,
                isOrderValidated: false
            });
            state.currentProduct = '';
            resetProductFormFields();
            renderAll();
        };

        const handleNewOrder = () => {
            const p = document.createElement('p');
            p.textContent = `Êtes-vous sûr de vouloir commencer une nouvelle commande ? Toutes les modifications non sauvegardées seront perdues.`;
            const actions = [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300 hover:bg-gray-400 text-gray-800' },
                { label: 'Commencer une nouvelle commande', onClick: () => {
                    resetForm();
                    hideModal(dom.mainModal);
                }, className: 'bg-red-600 hover:bg-red-700 text-white' }
            ];
            showModal(dom.mainModal, 'Confirmation', p, actions);
        };

        const promptForForceCode = (callbackOnSuccess) => {
            hideModal(dom.mainModal); 

            const container = document.createElement('div');
            container.className = 'space-y-4';
            const p = document.createElement('p');
            p.textContent = 'Pour forcer cette action, veuillez saisir le code d\'autorisation.';
            const input = document.createElement('input');
            input.type = 'password';
            input.id = 'force-code-input';
            input.className = 'mt-1 block w-full rounded-md border-gray-300 shadow-sm';
            const errorMsg = document.createElement('p');
            errorMsg.id = 'force-code-error';
            errorMsg.className = 'text-red-500 text-sm hidden';
            errorMsg.textContent = 'Code incorrect.';
            container.appendChild(p);
            container.appendChild(input);
            container.appendChild(errorMsg);

            const actions = [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                { 
                    label: 'Confirmer', 
                    onClick: () => {
                        const enteredCode = document.getElementById('force-code-input').value;
                        if (enteredCode === '582069') {
                            hideModal(dom.mainModal);
                            callbackOnSuccess();
                        } else {
                            document.getElementById('force-code-error').classList.remove('hidden');
                            document.getElementById('force-code-input').classList.add('border-red-500');
                            input.focus();
                        }
                    }, 
                    className: 'bg-green-600 hover:bg-green-700' 
                }
            ];
            showModal(dom.mainModal, 'Code d\'Autorisation Requis', container, actions);
        };
        
        const handleSaveOrderClick = () => {
            if (!state.clubName || !state.departement || !state.clientNumber) {
                const p = document.createElement('p');
                p.textContent = 'Le nom du club, le département et le N° client sont obligatoires.';
                showModal(dom.mainModal, 'Erreur de validation', p);
                return;
            }
            if (state.currentOrderLineItems.length === 0) {
                const p = document.createElement('p');
                p.textContent = 'Impossible de sauvegarder un document vide.';
                showModal(dom.mainModal, 'Erreur de validation', p);
                return;
            }
        
            if (state.isReassort) {
                 const nonAccessoryItems = state.currentOrderLineItems.filter(item => {
                     const product = allAvailableProducts.find(p => p.name === item.productName);
                     return product && product.type !== 'accessoire';
                 });
                 const totalNonAccessoryQuantity = nonAccessoryItems.reduce((sum, item) => sum + item.totalQuantity, 0);
                 
                 if (totalNonAccessoryQuantity < 10) {
                     const p = document.createElement('p');
                     p.innerHTML = `<b>Validation Réassort :</b> La commande doit contenir un minimum de 10 articles (hors accessoires).<br>Quantité actuelle : <b>${totalNonAccessoryQuantity}</b>.`;
                     showModal(dom.mainModal, 'Validation Impossible (Réassort)', p, [
                         { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                         { label: 'Forcer la sauvegarde', onClick: () => promptForForceCode(() => saveOrder()), className: 'bg-orange-500 hover:bg-orange-600' }
                     ]);
                     return;
                 }

                 const itemsWithLessThanTwo = nonAccessoryItems.filter(item => item.totalQuantity < 2);
                 if (itemsWithLessThanTwo.length > 0) {
                    const p = document.createElement('p');
                    const itemNames = itemsWithLessThanTwo.map(item => `<li>${item.productName} (Qté: ${item.totalQuantity})</li>`).join('');
                    p.innerHTML = `
                        <b>Validation Réassort :</b> Chaque article d'une commande de réassort doit avoir une quantité minimale de 2.
                        <br><br>Les articles suivants ne respectent pas cette règle :
                        <ul class="list-disc list-inside mt-2">${itemNames}</ul>
                    `;
                    showModal(dom.mainModal, 'Validation Impossible (Réassort)', p, [
                        { label: 'Fermer', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                        { label: 'Forcer la sauvegarde', onClick: () => promptForForceCode(() => saveOrder()), className: 'bg-orange-500 hover:bg-orange-600' }
                    ]);
                    return;
                }

            } else if (state.documentType === 'order') {
                 const totalQuantity = state.currentOrderLineItems.reduce((sum, item) => sum + item.totalQuantity, 0);
                 if (totalQuantity < 10 && !state.isCustomDesignOrder) { // Don't check min quantity for custom design orders as they have a fee
                      const p = document.createElement('p');
                     p.innerHTML = `<b>Validation Commande :</b> La commande doit contenir un minimum de 10 articles au total.<br>Quantité actuelle : <b>${totalQuantity}</b>.`;
                     showModal(dom.mainModal, 'Validation Impossible', p, [
                         { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                         { label: 'Forcer la sauvegarde', onClick: () => promptForForceCode(() => saveOrder()), className: 'bg-orange-500 hover:bg-orange-600' }
                     ]);
                     return;
                 }
            }
            saveOrder();
        };

        const createOrderSnapshot = () => {
            const orderData = {};
            const orderKeys = [
                'orderId', 'isReassort', 'isCustomDesignOrder', 'isStoreOrder', 'applyDiscount', 'clubName', 
                'departement', 'clientNumber', 'preOrderNumber', 'quoteNumber', 'orderDate', 
                'licencieName', 'clubDiscount', 'currentOrderLineItems', 'discountType', 
                'orderScope', 'documentType', 'orderSpecificity', 'isImperatif', 'imperatifNote'
            ];
            orderKeys.forEach(key => { orderData[key] = state[key]; });
            return orderData;
        };
        
        const saveOrder = () => {
            state.isSaving = true;
            renderAll();
            const orderSnapshot = createOrderSnapshot();
            
            setTimeout(() => {
                if (state.orderId) {
                    const index = state.savedOrders.findIndex(o => o.orderId === state.orderId);
                    if (index !== -1) {
                        state.savedOrders[index] = { ...orderSnapshot, savedAt: new Date().toISOString() };
                    } else { 
                        const newOrderId = Date.now();
                        orderSnapshot.orderId = newOrderId;
                        state.orderId = newOrderId;
                        state.savedOrders.push({ ...orderSnapshot, savedAt: new Date().toISOString() });
                    }
                } else {
                    const newOrderId = Date.now();
                    orderSnapshot.orderId = newOrderId;
                    state.orderId = newOrderId;
                    state.savedOrders.push({ ...orderSnapshot, savedAt: new Date().toISOString() });
                }
                localStorage.setItem('savedOrdersDB', JSON.stringify(state.savedOrders));
                state.isSaving = false;
                state.isOrderValidated = true; // MODIFIÉ: Valider la commande après sauvegarde réussie
                const p = document.createElement('p');
                p.textContent = "La commande a été sauvegardée et validée avec succès. Les exports sont maintenant disponibles.";
                showModal(dom.mainModal, 'Succès', p);
                renderAll();
            }, 500);
        };

        // --- PDF EXPORT ---
        const handleExportPdf = () => {
            if (!state.isOrderValidated) {
                 const p = document.createElement('p');
                 p.textContent = "Veuillez d'abord valider la commande en cliquant sur 'Sauvegarder la Commande' avant de l'exporter.";
                 showModal(dom.mainModal, 'Exportation impossible', p);
                 return;
            }

            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const totals = calculateAllTotals();
            let finalY = 0;

            // --- HEADER ---
            doc.setFontSize(18);
            const docTitle = `${state.documentType === 'quote' ? 'Devis' : 'Bon de commande'} ${state.isReassort ? '(Réassort 2 mois)' : ''}`;
            doc.text(docTitle, 14, 22);

            doc.setFontSize(11);
            doc.setTextColor(100);

            doc.text(`Nom du Club: ${state.clubName}`, 14, 32);
            doc.text(`Département: ${state.departement}`, 14, 38);
            doc.text(`N° Client: ${state.clientNumber}`, 14, 44);
            
            const orderRefLabel = state.documentType === 'quote' ? 'N° Devis:' : 'N° Pré-commande:';
            const orderRefValue = state.documentType === 'quote' ? state.quoteNumber : state.preOrderNumber;
            doc.text(`${orderRefLabel} ${orderRefValue}`, 140, 32);
            doc.text(`Date: ${state.orderDate}`, 140, 38);
            if (state.documentType === 'quote') {
                doc.text("Validité de l'offre: 1 mois", 140, 44);
            }

            finalY = 50; 

            // --- IMPERATIF NOTE ---
            if (state.isImperatif && state.imperatifNote) {
                doc.setFillColor(255, 26, 26); // Red background
                doc.setTextColor(255, 255, 255); // White text
                doc.setFont(undefined, 'bold');
                
                const noteLines = doc.splitTextToSize(`IMPÉRATIF : ${state.imperatifNote}`, 180);
                const noteHeight = (noteLines.length * 5) + 8; // Calculate height needed
                
                doc.rect(14, finalY, 182, noteHeight, 'F');
                doc.text(noteLines, 16, finalY + 6);
                
                finalY += noteHeight + 5; // Add some padding below
                
                // Reset colors and font style
                doc.setTextColor(0);
                doc.setFont(undefined, 'normal');
            }

            // --- ITEMS TABLE ---
            const head = [['Produit', 'Tailles', 'Qté', 'Prix U. HT', 'Prix U. TTC', 'Total HT', 'Total TTC']];
            const sortedItems = sortLineItems(state.currentOrderLineItems);
            
            const body = sortedItems.map(item => {
                let productName = item.productName;
                if (item.options.length > 0) productName += `\nOptions: ${item.options.join(', ')}`;
                if (item.specificity) productName += `\nNote: ${item.specificity}`;

                const sortedSizeKeys = sortSizeKeys(Object.keys(item.quantitiesPerSize));
                const sizesText = sortedSizeKeys.map(size => `${size}: ${item.quantitiesPerSize[size]}`).join('\n');

                return [
                    productName, sizesText, item.totalQuantity,
                    `${item.unitPriceHT.toFixed(2)} €`, `${item.unitPriceTTC.toFixed(2)} €`,
                    `${item.totalPriceHT.toFixed(2)} €`, `${item.totalPriceTTC.toFixed(2)} €`
                ];
            });

            doc.autoTable({
                startY: finalY, head: head, body: body, theme: 'striped',
                headStyles: { fillColor: [41, 128, 185], textColor: 255 },
                styles: { cellPadding: 2, fontSize: 8 },
                didDrawPage: (data) => { finalY = data.cursor.y; }
            });

            // --- TOTALS ---
            const totalsLabelX = 120;
            const totalsValueX = 200;
            let totalsY = finalY + 10;

            const neededHeight = 70;
            if (totalsY + neededHeight > doc.internal.pageSize.height) {
                doc.addPage();
                totalsY = 20;
            }
            
            doc.setFontSize(10);
            doc.text(`Sous-total HT:`, totalsLabelX, totalsY);
            doc.text(`${totals.subtotalHT.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            totalsY += 6;
            doc.text(`Sous-total TTC:`, totalsLabelX, totalsY);
            doc.text(`${totals.subtotalTTC.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            totalsY += 6;
            
            if (state.applyDiscount && totals.discountAmountTTC > 0) {
                 doc.setTextColor(255, 0, 0);
                 doc.text(`Remise Club (${state.clubDiscount}%) TTC (Info):`, totalsLabelX, totalsY);
                 doc.text(`-${totals.discountAmountTTC.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
                 totalsY += 6;
                 doc.setTextColor(0);
            }

            if (totals.appliedGraphicFeeTTC > 0) {
                doc.text(`Frais création graphique TTC:`, totalsLabelX, totalsY);
                doc.text(`${totals.appliedGraphicFeeTTC.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
                totalsY += 6;
            }

            doc.text(`Frais de port HT:`, totalsLabelX, totalsY);
            doc.text(`${totals.shippingHT.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            totalsY += 6;
            doc.text(`Frais de port TTC:`, totalsLabelX, totalsY);
            doc.text(`${totals.shippingTTC.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            totalsY += 8;

            doc.setFontSize(12);
            doc.setFont(undefined, 'bold');
            doc.text(`Total Général TTC:`, totalsLabelX, totalsY);
            doc.text(`${totals.grandTotalTTC.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            totalsY += 8;
            doc.setTextColor(28, 175, 28);
            doc.text(totals.downPaymentLabel, totalsLabelX, totalsY);
            doc.text(`${totals.downPayment.toFixed(2)} €`, totalsValueX, totalsY, { align: 'right' });
            doc.setTextColor(0);
            doc.setFont(undefined, 'normal');

            // --- OBSERVATIONS ---
            finalY = Math.max(totalsY, doc.internal.pageSize.height - 60);
            const observations = [
                'OBSERVATIONS GENERALES',
                'Prix indiqués en HT et TTC.',
                'Documents à contrôler.',
                'Nous informer si modifications à effectuer.',
                'Ce document est une précommande et non le document final, NORET se garde le droit d\'apporter des modifications et seul l\'accusé de réception envoyé pour validation et signature sera pris en compte.'
            ];
            doc.setFillColor(255, 255, 0);
            doc.rect(14, finalY, 182, 35, 'F');
            doc.setTextColor(0);
            doc.setFontSize(9);
            doc.text(observations[0], 16, finalY + 5, {maxWidth: 178});
            doc.setFontSize(8);
            doc.text(observations[1], 16, finalY + 10, {maxWidth: 178});
            doc.text(observations[2], 16, finalY + 14, {maxWidth: 178});
            doc.text(observations[3], 16, finalY + 18, {maxWidth: 178});
            doc.text(observations[4], 16, finalY + 22, {maxWidth: 178});

            // --- SAVE ---
            doc.save(`devis-bon-de-commande_${state.clubName.replace(/ /g, '_') || 'commande'}_${state.orderDate}.pdf`);
        };
        
        const handleExportExcel = () => {
            if (!state.isOrderValidated) {
                const p = document.createElement('p');
                p.textContent = "Veuillez d'abord valider la commande en cliquant sur 'Sauvegarder la Commande' avant de l'exporter.";
                showModal(dom.mainModal, 'Exportation impossible', p);
                return;
            }
            if (typeof XLSX === 'undefined') {
                 const p = document.createElement('p');
                 p.textContent = "La librairie d'exportation Excel (SheetJS/xlsx) n'a pas pu être chargée.";
                 showModal(dom.mainModal, 'Erreur de librairie', p);
                console.error("XLSX library is not loaded."); 
                return;
            }
            
            const excelData = [];
            const columnDefinitions = [
                {id: 'produit', label: 'Produit'}, {id: 'licencie', label: 'Licencié'},
                {id: 'specificite', label: 'Spécificité'}, {id: 'options', label: 'Options'},
                {id: 'tailles', label: 'Tailles & Quantités'}, {id: 'qte', label: 'Qté Totale'},
                {id: 'prixUHT', label: 'Prix U. HT'}, {id: 'prixUTTC', label: 'Prix U. TTC'},
                {id: 'totalHT', label: 'Total HT'}, {id: 'totalTTC', label: 'Total TTC'},
                {id: 'remise', label: 'Remise Appliquée'}, {id: 'geste', label: 'Geste Comm.'},
            ]
        
            const activeHeaders = columnDefinitions.filter(col => state.excelColumns[col.id] && !(col.id === 'licencie' && state.documentType === 'quote'));
            excelData.push(activeHeaders.map(h => h.label));

            const sortedItems = sortLineItems(state.currentOrderLineItems);

            sortedItems.forEach(item => {
                const sortedSizeKeys = sortSizeKeys(Object.keys(item.quantitiesPerSize));
                const sizes = sortedSizeKeys.map(size => `${size}: ${item.quantitiesPerSize[size]}`).join(' | ');
                
                const discountAppliedText = state.discountType === 'item' ? (item.applyDiscount ? 'Oui' : 'Non') : 'Globale';
                const rowData = {
                    produit: item.productName,
                    licencie: item.licencieName,
                    specificite: item.specificity,
                    options: item.options.join(', '),
                    tailles: sizes,
                    qte: item.totalQuantity,
                    prixUHT: { t: 'n', v: item.unitPriceHT, z: '#,##0.00 €' },
                    prixUTTC: { t: 'n', v: item.unitPriceTTC, z: '#,##0.00 €' },
                    totalHT: { t: 'n', v: item.totalPriceHT, z: '#,##0.00 €' },
                    totalTTC: { t: 'n', v: item.totalPriceTTC, z: '#,##0.00 €' },
                    remise: discountAppliedText,
                    geste: item.applyGesture ? 'Oui' : 'Non',
                };
                if(state.documentType !== 'order') delete rowData.licencie;

                const row = activeHeaders.map(h => rowData[h.id]);
                excelData.push(row);
            });
            
             const totals = calculateAllTotals();
             const totalArticles = state.currentOrderLineItems.reduce((acc, item) => acc + item.totalQuantity, 0);
             const dataForSheet = [
                  [`${state.documentType === 'quote' ? 'Devis' : 'Bon de commande'} ${state.isReassort ? '(Réassort 2 mois)' : ''}`], 
                  [], 
                  ['Nom du Club:', state.clubName], ['Département:', state.departement], ['N° Client:', state.clientNumber], 
                  [state.documentType === 'quote' ? 'N° Devis:' : 'N° Pré-commande:', state.documentType === 'quote' ? state.quoteNumber : state.preOrderNumber], 
                  ['Date:', state.orderDate],
                  ['Note Commande:', state.orderSpecificity],
                  ['Commande Magasin:', state.isStoreOrder ? 'Oui' : 'Non'],
                  ['Total Articles:', totalArticles],
             ];
             if(state.documentType === 'quote') { dataForSheet.push(['Validité de l\'offre:', '1 mois']); }
             dataForSheet.push([], ...excelData);
        
             const totalsStartColumn = excelData[0].length - 3;
             dataForSheet.push([],
                 Array(totalsStartColumn).fill(null).concat(['Sous-total HT:', { t: 'n', v: totals.subtotalHT, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat(['Sous-total TTC:', { t: 'n', v: totals.subtotalTTC, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat([`Remise Club (${state.clubDiscount}%) HT (Info):`, { t: 'n', v: -totals.discountAmountHT, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat([`Remise Club (${state.clubDiscount}%) TTC (Info):`, { t: 'n', v: -totals.discountAmountTTC, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat(['Frais création graphique HT:', { t: 'n', v: totals.appliedGraphicFeeHT, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat(['Frais création graphique TTC:', { t: 'n', v: totals.appliedGraphicFeeTTC, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat(['Frais de port HT:', { t: 'n', v: totals.shippingHT, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat(['Frais de port TTC:', { t: 'n', v: totals.shippingTTC, z: '#,##0.00 €' }]),
                 Array(totalsStartColumn).fill(null).concat([{v: "Total Général HT:", s:{font:{bold:true}}}, { t: 'n', v: totals.grandTotalHT, z: '#,##0.00 €', s:{font:{bold:true}}}]),
                 Array(totalsStartColumn).fill(null).concat([{v: "Total Général TTC:", s:{font:{bold:true}}}, { t: 'n', v: totals.grandTotalTTC, z: '#,##0.00 €', s:{font:{bold:true}}}]),
                 Array(totalsStartColumn).fill(null).concat([{v: totals.downPaymentLabel, s:{font:{bold:true}}}, { t: 'n', v: totals.downPayment, z: '#,##0.00 €', s:{font:{bold:true}}}])
             );

             const yellowFill = { fgColor: { rgb: "FFFF00" } };
             const observationsSection = [
                  [], [],
                  [{ v: 'OBSERVATIONS GENERALES', s: { font: { bold: true, sz: 12 }, fill: yellowFill } }],
                  [{ v: 'Prix indiqués en HT et TTC.', s: { fill: yellowFill } }],
                  [{ v: 'Documents à contrôler.', s: { fill: yellowFill } }],
                  [{ v: 'Nous informer si modifications à effectuer.', s: { fill: yellowFill } }],
                  [{ v: 'Ce document est une précommande et non le document final, NORET se garde le droit d\'apporter des modifications et seul l\'accusé de réception envoyé pour validation et signature sera pris en compte.', s: { fill: yellowFill, alignment: { wrapText: true } } }]
             ];

             observationsSection.forEach(row => {
                  row[0] = row[0] || {v: ""};
                  for(let i=1; i < 6; i++){ row.push({v: "", s: {fill: yellowFill}}); }
             });
             dataForSheet.push(...observationsSection);

              const wb = XLSX.utils.book_new();
              const ws = XLSX.utils.aoa_to_sheet(dataForSheet);
              ws['!cols'] = activeHeaders.map(h => ({wch: h.label.length > 20 ? 50 : 20}));
            
             const mergeStartRow = dataForSheet.length - observationsSection.length + 2;
             if (!ws['!merges']) ws['!merges'] = [];
             ws['!merges'].push({ s: { r: mergeStartRow, c: 0 }, e: { r: mergeStartRow, c: 5 } }); 
             ws['!merges'].push({ s: { r: mergeStartRow + 1, c: 0 }, e: { r: mergeStartRow + 1, c: 5 } });
             ws['!merges'].push({ s: { r: mergeStartRow + 2, c: 0 }, e: { r: mergeStartRow + 2, c: 5 } });
             ws['!merges'].push({ s: { r: mergeStartRow + 3, c: 0 }, e: { r: mergeStartRow + 3, c: 5 } });
             ws['!merges'].push({ s: { r: mergeStartRow + 4, c: 0 }, e: { r: mergeStartRow + 4, c: 5 } });


              XLSX.utils.book_append_sheet(wb, ws, 'Devis');
              XLSX.writeFile(wb, `devis-bon-de-commande_${state.clubName.replace(/ /g, '_') || 'commande'}_${state.orderDate}.xlsx`);
        };
        
        const handleEmail = () => {
             if (!state.isOrderValidated) {
                  const p = document.createElement('p');
                  p.textContent = "Veuillez d'abord valider la commande en cliquant sur 'Sauvegarder la Commande' avant de l'envoyer.";
                  showModal(dom.mainModal, 'Envoi impossible', p);
                  return;
             }
             if (!state.clubName || !state.clientNumber) {
                 const p = document.createElement('p');
                 p.textContent = "Veuillez renseigner le nom du club et le N° client avant d'envoyer l'e-mail.";
                 showModal(dom.mainModal, 'Information manquante', p);
                 return;
             }
        
             const to = "aude@noret.com";
             const subject = `Précommande - ${state.clubName} - ${state.clientNumber}`;
             
             const bodyParts = [
                 "Bonjour,",
                 "",
                 "Veuillez trouver le bon de commande en pièce jointe.",
                 "",
                 `Rappel du N° de Précommande : ${state.preOrderNumber || 'Non spécifié'}`,
                 `Rappel des spécificités de la commande : ${state.orderSpecificity || 'Aucune'}`,
                 "",
                 "----------------------------------------------------------------",
                 "IMPORTANT : N'oubliez pas de joindre le fichier PDF du bon de commande que vous avez généré.",
                 "----------------------------------------------------------------",
                 "",
                 "Cordialement,"
             ];
         
             const body = bodyParts.join('\n');
             const mailtoLink = `mailto:${to}?subject=${encodeURIComponent(subject)}&body=${encodeURIComponent(body)}`;
        
             const p = document.createElement('p');
             p.innerHTML = `
                 Vous allez être redirigé vers votre client de messagerie par défaut pour envoyer un e-mail à <b>aude@noret.com</b>.<br><br>
                 <b class="text-red-600">N'oubliez pas de joindre manuellement le fichier PDF</b> que vous avez généré avec le bouton "Exporter en PDF".<br><br>
                 Cliquez sur "Continuer" pour ouvrir votre client de messagerie.
             `;
             const actions = [
                 { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                 { 
                     label: 'Continuer', 
                     onClick: () => {
                         window.location.href = mailtoLink;
                         hideModal(dom.mainModal);
                     }, 
                     className: 'bg-blue-600 hover:bg-blue-700' 
                 }
             ];
             showModal(dom.mainModal, "Préparer l'envoi par e-mail", p, actions);
        };
        
        const switchToTab = (tabId) => {
            document.querySelectorAll('.main-tab-btn').forEach(btn => {
                btn.classList.remove('border-indigo-500', 'text-indigo-600');
                btn.classList.add('text-gray-500', 'hover:text-gray-700');
            });
            const tabButton = document.querySelector(`[data-tab-id="${tabId}"]`);
            tabButton.classList.add('border-indigo-500', 'text-indigo-600');
            tabButton.classList.remove('text-gray-500', 'hover:text-gray-700');

            document.querySelectorAll('.main-tab-content').forEach(content => content.classList.remove('active'));
            document.getElementById(tabId).classList.add('active');
        };

        const promptForSavedOrdersAccess = () => {
            const container = document.createElement('div');
            container.className = 'space-y-4';
            const p = document.createElement('p');
            p.textContent = "Pour accéder aux commandes sauvegardées, veuillez saisir le code d'autorisation.";
            const input = document.createElement('input');
            input.type = 'password';
            input.id = 'access-code-input';
            input.className = 'mt-1 block w-full rounded-md border-gray-300 shadow-sm';
            const errorMsg = document.createElement('p');
            errorMsg.id = 'access-code-error';
            errorMsg.className = 'text-red-500 text-sm hidden';
            errorMsg.textContent = 'Code incorrect.';
            container.appendChild(p);
            container.appendChild(input);
            container.appendChild(errorMsg);

            const actions = [
                { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                { 
                    label: 'Confirmer', 
                    onClick: () => {
                        const enteredCode = document.getElementById('access-code-input').value;
                        if (enteredCode === '582069') {
                            state.isSavedOrdersTabLocked = false;
                            hideModal(dom.mainModal);
                            switchToTab('saved-orders');
                        } else {
                            document.getElementById('access-code-error').classList.remove('hidden');
                            document.getElementById('access-code-input').classList.add('border-red-500');
                            input.focus();
                        }
                    }, 
                    className: 'bg-green-600 hover:bg-green-700' 
                }
            ];
            showModal(dom.mainModal, 'Accès Protégé', container, actions);
        };

        // --- INITIALIZATION ---
        const init = () => {
            dom.orderDate.value = new Date().toISOString().split('T')[0];
            const savedClients = localStorage.getItem('clientDatabase');
            if (savedClients) { clientDatabase = JSON.parse(savedClients); }
            updateDatalists();

            const savedOrdersData = localStorage.getItem('savedOrdersDB');
            if (savedOrdersData) { state.savedOrders = JSON.parse(savedOrdersData); }

            document.body.addEventListener('input', (e) => {
                 if (!e.target) return;
                 state.isOrderValidated = false;
                 const { id, value } = e.target;

                 if (id === 'clubName') {
                    state.clubName = value;
                    const found = clientDatabase.find(c => c.clubName === value);
                    if (found) {
                        state.clientNumber = found.clientNumber;
                        dom.clientNumber.value = found.clientNumber;
                        state.departement = found.departement || '';
                        dom.departement.value = state.departement;
                    }
                 } else if (id === 'clientNumber') {
                    state.clientNumber = value;
                    const found = clientDatabase.find(c => c.clientNumber === value);
                    if (found) {
                        state.clubName = found.clubName;
                        dom.clubName.value = found.clubName;
                        state.departement = found.departement || '';
                        dom.departement.value = state.departement;
                    }
                 } else if (id === 'departement') {
                     state.departement = value;
                 }
            });

            document.body.addEventListener('focusout', (e) => {
                if (e.target.id === 'clubName' || e.target.id === 'clientNumber' || e.target.id === 'departement') {
                    saveClientInfo();
                }
            });

            document.body.addEventListener('change', (e) => {
                if (!e.target) return;
                state.isOrderValidated = false;
                
                if (e.target.id === 'preOrderNumber') state.preOrderNumber = e.target.value;
                if (e.target.id === 'orderDate') {
                     state.orderDate = e.target.value;
                     if(state.documentType === 'quote') {
                          const date = new Date(state.orderDate);
                          state.quoteNumber = `${String(date.getDate()).padStart(2, '0')}${String(date.getMonth() + 1).padStart(2, '0')}${date.getFullYear()}`;
                     }
                }
                if (e.target.id === 'orderSpecificity') state.orderSpecificity = e.target.value;
                if (e.target.id === 'licencieName') state.licencieName = e.target.value;
                if (e.target.id === 'clubDiscount') {
                    state.clubDiscount = parseFloat(e.target.value) || 0;
                }
                if (e.target.name === 'doc-type') {
                    state.documentType = e.target.value;
                    if(state.documentType === 'quote') {
                         state.isReassort = false;
                         state.orderScope = 'global';
                         const date = new Date(state.orderDate);
                         state.quoteNumber = `${String(date.getDate()).padStart(2, '0')}${String(date.getMonth() + 1).padStart(2, '0')}${date.getFullYear()}`;
                     }
                }
                 if (e.target.id === 'doc-type-reassort') {
                      const checked = e.target.checked;
                      const action = () => {
                          state.isReassort = checked;
                          state.currentOrderLineItems = [];
                          if (checked) state.documentType = 'order';
                          hideModal(dom.mainModal);
                          renderAll();
                      };
                      if (state.currentOrderLineItems.length > 0) {
                          const p = document.createElement('p');
                          p.textContent = `Ce changement videra le panier actuel. Êtes-vous sûr de vouloir continuer ?`;
                          showModal(dom.mainModal, 'Changement de mode', p, [
                              {label: 'Annuler', onClick: () => {e.target.checked = !checked; hideModal(dom.mainModal)}, className: 'bg-gray-300'},
                              {label: 'Continuer', onClick: action, className: 'bg-red-600'}
                          ]);
                      } else { action(); }
                 }
                if (e.target.id === 'custom-design-order-check') { state.isCustomDesignOrder = e.target.checked; }
                if (e.target.id === 'imperatif-check') {
                    state.isImperatif = e.target.checked;
                    if (!state.isImperatif) { state.imperatifNote = ''; }
                }
                if (e.target.id === 'imperatif-note') { state.imperatifNote = e.target.value; }
                if (e.target.name === 'scope') { state.orderScope = e.target.value; }
                if (e.target.id === 'store-order-check') { state.isStoreOrder = e.target.checked; }
                if (e.target.id === 'apply-discount-check') {
                    state.applyDiscount = e.target.checked;
                    if (!state.applyDiscount) { state.clubDiscount = 0; }
                }
                if (e.target.name === 'discount-type') { state.discountType = e.target.value; }

                if (e.target.id === 'current-product-select') {
                    state.currentProduct = e.target.value;
                    resetProductFormFields();
                    calculateCurrentFormPrice();
                }
                if (e.target.classList.contains('size-input')) {
                    state.currentQuantities[e.target.dataset.size] = e.target.value;
                    calculateCurrentFormPrice();
                }
                if (e.target.id === 'accessory-qty' || e.target.id === 'quote-qty') {
                    state.currentAccessoryQuantity = e.target.value;
                    calculateCurrentFormPrice();
                }
                 if (e.target.id === 'manual-price') state.manualUnitPrice = e.target.value;
                 if (e.target.id === 'current-color-select') state.currentColor = e.target.value;
                 if (e.target.id === 'specificity') state.currentSpecificity = e.target.value;
                 if (e.target.id === 'commercial-gesture-check') {
                    if (e.target.checked) {
                        promptForForceCode(() => {
                            state.applyCommercialGesture = true;
                            calculateCurrentFormPrice();
                            renderProductForm();
                        });
                        e.target.checked = false;
                    } else {
                        state.applyCommercialGesture = false;
                        calculateCurrentFormPrice();
                    }
                 }
                 if (e.target.classList.contains('option-checkbox')) {
                    const optionName = e.target.dataset.optionName;
                    const optionGroup = e.target.dataset.optionGroup;
                    if (optionGroup === 'length') {
                        const allLengthOptions = allAvailableProducts.filter(p => p.optionGroup === 'length').map(p => p.name);
                        const nonLengthOptions = state.currentSelectedOptions.filter(opt => !allLengthOptions.includes(opt));
                         state.currentSelectedOptions = e.target.checked ? [...nonLengthOptions, optionName] : nonLengthOptions;
                    } else {
                         state.currentSelectedOptions = e.target.checked ? [...state.currentSelectedOptions, optionName] : state.currentSelectedOptions.filter(o => o !== optionName);
                    }
                    calculateCurrentFormPrice();
                 }
                 renderAll();
            });

            document.body.addEventListener('click', (e) => {
                 if (!e.target) return;
                 // Product Form
                if(e.target.id === 'add-product-btn' || e.target.parentElement?.id === 'add-product-btn') handleProductAdd();
                if(e.target.id === 'generate-specificity-btn' || e.target.parentElement?.id === 'generate-specificity-btn') { /* AI function placeholder */ }

                 // Table Actions
                 const actionTarget = e.target.closest('[data-action]');
                 if(actionTarget){
                     const { action, itemId } = actionTarget.dataset;
                     if(action === 'remove-item'){
                         state.currentOrderLineItems = state.currentOrderLineItems.filter(item => item.id != itemId);
                         state.isOrderValidated = false;
                         renderAll();
                     }
                     if(action === 'toggle-discount'){
                         state.currentOrderLineItems = state.currentOrderLineItems.map(item => item.id == itemId ? { ...item, applyDiscount: !item.applyDiscount } : item);
                         state.isOrderValidated = false;
                         renderAll();
                     }
                    if (action === 'edit-item') {
                         const itemToEdit = state.currentOrderLineItems.find(i => i.id == itemId);
                         if (itemToEdit) {
                             const product = allAvailableProducts.find(p => p.name === itemToEdit.productName);
                             state.currentProduct = itemToEdit.productName;
                             state.currentQuantities = itemToEdit.quantitiesPerSize;
                             if(product?.type === 'accessoire' && (!product.sizeType || product.sizeType === 'unique')){
                                 state.currentAccessoryQuantity = itemToEdit.totalQuantity;
                             } else { state.currentAccessoryQuantity = ''; }
                             state.currentSelectedOptions = itemToEdit.options;
                             state.currentSpecificity = itemToEdit.specificity;
                             state.currentColor = itemToEdit.color;
                             state.manualUnitPrice = itemToEdit.initialManualPrice || '';
                             state.currentOrderLineItems = state.currentOrderLineItems.filter(i => i.id != itemId);
                             state.isOrderValidated = false;
                             renderAll();
                         }
                    }
                 }

                 // Main Tabs
                 const mainTabTarget = e.target.closest('.main-tab-btn');
                 if (mainTabTarget) {
                    const tabId = mainTabTarget.dataset.tabId;
                    if (tabId === 'saved-orders' && state.isSavedOrdersTabLocked) {
                        promptForSavedOrdersAccess();
                    } else { switchToTab(tabId); }
                 }

                 // Product Tabs
                 const productTabTarget = e.target.closest('.product-tab-btn');
                 if(productTabTarget){
                       document.querySelectorAll('.product-tab-btn').forEach(btn => {
                            btn.classList.remove('border-indigo-500', 'text-indigo-600');
                            btn.classList.add('text-gray-500', 'hover:text-gray-700');
                       });
                       productTabTarget.classList.add('border-indigo-500', 'text-indigo-600');
                       productTabTarget.classList.remove('text-gray-500', 'hover:text-gray-700');
                       state.activeTab = productTabTarget.dataset.tab;
                       state.currentProduct = '';
                       resetProductFormFields();
                       renderProductForm();
                 }
                
                 // Main Action Buttons
                if (e.target.id === 'new-order-btn') handleNewOrder();
                if (e.target.id === 'save-order-btn') handleSaveOrderClick();
                if (e.target.id === 'export-excel-btn') handleExportExcel();
                if (e.target.id === 'export-pdf-btn') handleExportPdf();
                if (e.target.id === 'send-email-btn') handleEmail();
                if (e.target.id === 'customize-export-btn') dom.excelModal.classList.add('is-open');

                // Modal Buttons
                if (e.target.id === 'main-modal-close-btn') hideModal(dom.mainModal);
                if (e.target.id === 'excel-modal-validate-btn') hideModal(dom.excelModal);
            });
            
            // Excel Modal Logic
            dom.excelColumnsContainer.innerHTML = Object.keys(state.excelColumns).map(key => `
                <label class="flex items-center space-x-2">
                    <input type="checkbox" data-column-key="${key}" class="excel-column-check" ${state.excelColumns[key] ? 'checked' : ''}/>
                    <span>${key.charAt(0).toUpperCase() + key.slice(1)}</span>
                </label>
            `).join('');
            dom.excelColumnsContainer.addEventListener('change', e => {
                if(e.target.classList.contains('excel-column-check')){
                    state.excelColumns[e.target.dataset.columnKey] = e.target.checked;
                }
            });

            // Saved Orders List Actions
            dom.savedOrdersList.addEventListener('click', (e) => {
                const target = e.target.closest('[data-action]');
                if (!target) return;
                const orderId = parseInt(target.dataset.orderId, 10);
                if (target.dataset.action === 'load-order') {
                    const orderToLoad = state.savedOrders.find(o => o.orderId === orderId);
                    if (orderToLoad) {
                        resetForm();
                        const loadedState = JSON.parse(JSON.stringify(orderToLoad));
                        const orderKeys = [
                            'orderId', 'isReassort', 'isCustomDesignOrder', 'isStoreOrder', 'applyDiscount', 'clubName', 
                            'departement', 'clientNumber', 'preOrderNumber', 'quoteNumber', 'orderDate', 
                            'licencieName', 'clubDiscount', 'currentOrderLineItems', 'discountType', 
                            'orderScope', 'documentType', 'orderSpecificity', 'isImperatif', 'imperatifNote'
                        ];
                        orderKeys.forEach(key => {
                            if (loadedState[key] !== undefined) { state[key] = loadedState[key]; }
                        });
                        state.currentOrderLineItems = state.currentOrderLineItems || [];
                        state.isOrderValidated = true;
                        switchToTab('order-creation');
                        renderAll();
                    }
                } else if (target.dataset.action === 'delete-order') {
                    const p = document.createElement('p');
                    p.textContent = 'Êtes-vous sûr de vouloir supprimer cette commande ? Cette action est irréversible.';
                    showModal(dom.mainModal, 'Confirmer la suppression', p, [
                        { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                        { 
                            label: 'Supprimer', 
                            onClick: () => {
                                state.savedOrders = state.savedOrders.filter(o => o.orderId !== orderId);
                                localStorage.setItem('savedOrdersDB', JSON.stringify(state.savedOrders));
                                renderSavedOrders();
                                hideModal(dom.mainModal);
                            }, 
                            className: 'bg-red-600'
                        }
                    ]);
                }
            });

            // JSON Import/Export
            dom.exportOrdersJsonBtn.addEventListener('click', () => {
                if (state.savedOrders.length === 0) {
                    showModal(dom.mainModal, 'Exportation impossible', document.createTextNode("Il n'y a aucune commande à exporter."));
                    return;
                }
                const dataUri = 'data:application/json;charset=utf-8,'+ encodeURIComponent(JSON.stringify(state.savedOrders, null, 2));
                let linkElement = document.createElement('a');
                linkElement.setAttribute('href', dataUri);
                linkElement.setAttribute('download', 'commandes_sauvegardees.json');
                linkElement.click();
            });
            dom.importOrdersJsonBtn.addEventListener('click', () => dom.jsonOrdersFileInput.click());
            dom.jsonOrdersFileInput.addEventListener('change', (event) => {
                const file = event.target.files[0];
                if (!file) return;
                const reader = new FileReader();
                reader.onload = (e) => {
                    try {
                        const loadedOrders = JSON.parse(e.target.result);
                        if (!Array.isArray(loadedOrders)) throw new Error("Fichier JSON invalide.");
                        const p = document.createElement('p');
                        p.textContent = 'Fusionner les nouvelles commandes avec la liste existante, ou remplacer entièrement la liste ?';
                        showModal(dom.mainModal, 'Charger des commandes', p, [
                            { label: 'Annuler', onClick: () => hideModal(dom.mainModal), className: 'bg-gray-300' },
                            { 
                                label: 'Fusionner', 
                                onClick: () => {
                                    const existingIds = new Set(state.savedOrders.map(o => o.orderId));
                                    const newOrders = loadedOrders.filter(o => !existingIds.has(o.orderId));
                                    state.savedOrders.push(...newOrders);
                                    localStorage.setItem('savedOrdersDB', JSON.stringify(state.savedOrders));
                                    renderSavedOrders();
                                    hideModal(dom.mainModal);
                                }, 
                                className: 'bg-blue-600'
                            },
                            { 
                                label: 'Remplacer', 
                                onClick: () => {
                                    state.savedOrders = loadedOrders;
                                    localStorage.setItem('savedOrdersDB', JSON.stringify(state.savedOrders));
                                    renderSavedOrders();
                                    hideModal(dom.mainModal);
                                }, 
                                className: 'bg-red-600'
                            }
                        ]);
                    } catch (error) {
                        showModal(dom.mainModal, 'Erreur Fichier JSON', document.createTextNode(`Erreur de lecture : ${error.message}`));
                    } finally { event.target.value = ''; }
                };
                reader.readAsText(file);
            });

            // Initial render
            renderAll();
        };

        // Start the application
        document.addEventListener('DOMContentLoaded', init);

    </script>
</body>
</html>
