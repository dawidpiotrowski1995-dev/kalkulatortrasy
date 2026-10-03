<!DOCTYPE html>
<html lang="pl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kalkulator Trasy</title>
    <!-- Tailwind CSS for modern, responsive UI styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
        }
    </script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body class="bg-slate-50 dark:bg-slate-900 text-slate-800 dark:text-slate-200 min-h-screen flex flex-col transition-colors duration-200">

    <header class="bg-white dark:bg-slate-800 border-b border-slate-200 dark:border-slate-700 sticky top-0 z-10 transition-colors duration-200">
        <div class="max-w-5xl mx-auto px-4 py-4 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="bg-blue-600 text-white p-2 rounded-xl shadow-md">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"></path>
                    </svg>
                </div>
                <div>
                    <h1 class="text-xl font-bold text-slate-900 dark:text-white">Kalkulator Trasy</h1>
                    <p class="text-xs text-slate-500 dark:text-slate-400">Wyodrębnianie kodów pocztowych, obsługa zrzutów ekranu i trasy Google Maps</p>
                </div>
            </div>
            
            <!-- Dark Mode Toggle Button -->
            <button id="themeToggleBtn" class="p-2 rounded-lg bg-slate-100 dark:bg-slate-700 text-slate-600 dark:text-slate-300 hover:bg-slate-200 dark:hover:bg-slate-600 transition" title="Zmień motyw">
                <svg id="themeIconLight" class="w-5 h-5 hidden dark:block" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"></path>
                </svg>
                <svg id="themeIconDark" class="w-5 h-5 block dark:hidden" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"></path>
                </svg>
            </button>
        </div>
    </header>

    <main class="flex-grow max-w-5xl w-full mx-auto px-4 py-8 flex flex-col space-y-6">
        
        <!-- Sekcja 1: Wprowadzanie tekstu i przyciski (Szeroka na górze) -->
        <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 transition-colors duration-200">
            <div class="flex items-center justify-between mb-2">
                <label for="inputText" class="block text-sm font-semibold text-slate-700 dark:text-slate-200">
                    Wklej tekst ze zlecenia lub wklej zrzut ekranu (Ctrl+V):
                </label>
                <span id="imageStatus" class="text-xs text-blue-600 dark:text-blue-400 font-medium hidden">Załączono zrzut ekranu</span>
            </div>

            <div class="relative flex-grow flex flex-col">
                <textarea 
                    id="inputText" 
                    rows="3" 
                    class="w-full flex-grow p-4 rounded-xl border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-700 text-slate-900 dark:text-slate-100 focus:ring-2 focus:ring-blue-500 focus:border-blue-500 outline-none transition text-sm resize-y"
                    placeholder="Wklej tutaj tekst lub użyj Ctrl+V, aby wkleić screenshot z giełdy/maile zlecenia..."
                ></textarea>
                <!-- Ukryty podgląd wklejonego obrazu -->
                <div id="imagePreviewContainer" class="mt-3 hidden items-center justify-between p-2 bg-slate-50 dark:bg-slate-700/60 rounded-xl border border-slate-200 dark:border-slate-600">
                    <div class="flex items-center space-x-3">
                        <img id="pastedImageThumb" src="" alt="Screenshot" class="w-12 h-12 object-cover rounded-lg border border-slate-300 dark:border-slate-500">
                        <div>
                            <span class="text-xs font-semibold text-slate-700 dark:text-slate-200 block">Zrzut ekranu gotowy do analizy OCR</span>
                            <span class="text-[10px] text-slate-400">Kliknij Analizę AI, aby wyczytać adresy z grafiki</span>
                        </div>
                    </div>
                    <button type="button" id="removeImageBtn" class="text-slate-400 hover:text-red-500 p-2 rounded-lg transition" title="Usuń obraz">
                        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
                    </button>
                </div>
            </div>
            
            <div class="mt-4 flex flex-wrap gap-3 items-center justify-between">
                <div class="flex flex-wrap gap-2">
                    <button id="extractBtn" class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-5 py-2.5 rounded-xl shadow-sm hover:shadow transition flex items-center space-x-2 text-sm cursor-pointer">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
                        <span>Zwykłe wyodrębnianie</span>
                    </button>
                    
                    <button id="smartExtractBtn" class="bg-purple-600 hover:bg-purple-700 text-white font-medium px-5 py-2.5 rounded-xl shadow-sm hover:shadow transition flex items-center space-x-2 text-sm cursor-pointer" title="Wymaga podania klucza API w kodzie">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"></path></svg>
                        <span>Analiza AI / OCR z obrazu</span>
                    </button>
                </div>
                
                <button id="clearBtn" class="text-slate-500 dark:text-slate-400 hover:text-slate-700 dark:hover:text-slate-200 text-sm font-medium px-4 py-2 rounded-xl transition cursor-pointer">
                    Wyczyść
                </button>
            </div>
        </div>

        <!-- Sekcja 2: Wyodrębnione kody i Opcje Trasy -->
        <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 transition-colors duration-200">
            <h2 class="text-sm font-semibold text-slate-700 dark:text-slate-200 mb-3 flex items-center justify-between">
                <span>Znalezione Kody Pocztowe (Przeciągnij, aby zmienić kolejność)</span>
                <span id="badgeCount" class="bg-blue-100 dark:bg-blue-900 text-blue-800 dark:text-blue-200 text-xs font-semibold px-2.5 py-0.5 rounded-full">0</span>
            </h2>

            <!-- Extracted tags container -->
            <div id="tagsContainer" class="flex flex-wrap gap-2 min-h-[80px] p-3 bg-slate-50 dark:bg-slate-700/50 rounded-xl border border-slate-200 dark:border-slate-700 items-start overflow-y-auto max-h-48 transition-colors duration-200">
                <p class="text-xs text-slate-400 dark:text-slate-500 italic w-full text-center py-4" id="emptyMessage">Brak wyodrębnionych kodów. Wklej tekst/screenshot i kliknij przycisk.</p>
            </div>

            <!-- Route Options -->
            <div class="mt-4 pt-4 border-t border-slate-100 dark:border-slate-700 flex flex-col md:flex-row gap-6">
                <div class="flex-1">
                    <span class="block text-xs text-slate-600 dark:text-slate-400 font-medium mb-2">Kolejność punktów:</span>
                    <div class="flex space-x-4 text-xs dark:text-slate-300">
                        <label class="flex items-center space-x-1 cursor-pointer">
                            <input type="radio" name="orderMode" value="appearance" checked class="text-blue-600 focus:ring-blue-500">
                            <span>Kolejność w tekście</span>
                        </label>
                        <label class="flex items-center space-x-1 cursor-pointer">
                            <input type="radio" name="orderMode" value="unique" class="text-blue-600 focus:ring-blue-500">
                            <span>Tylko unikalne</span>
                        </label>
                    </div>
                </div>
                <div class="flex-1 border-t md:border-t-0 md:border-l border-slate-100 dark:border-slate-700 pt-4 md:pt-0 md:pl-6">
                    <span class="block text-xs text-slate-600 dark:text-slate-400 font-medium mb-2">Opcje trasy:</span>
                    <div class="flex flex-col space-y-2 dark:text-slate-300">
                        <label class="flex items-center space-x-2 cursor-pointer text-xs">
                            <input type="checkbox" id="viaKufstein" class="text-blue-600 focus:ring-blue-500 rounded border-slate-300">
                            <span>Trasa przez Kufstein (tranzyt na osi DE-IT)</span>
                        </label>
                        <label class="flex items-center space-x-2 cursor-pointer text-xs">
                            <input type="checkbox" id="avoidFerries" class="text-blue-600 focus:ring-blue-500 rounded border-slate-300">
                            <span>Unikaj promów (np. trasy przez Danię)</span>
                        </label>
                    </div>
                </div>
            </div>
        </div>

        <!-- Sekcja 3: Mapa na całą szerokość i Akcje Generowania Linków -->
        <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 transition-colors duration-200">
            <h2 class="text-sm font-semibold text-slate-700 dark:text-slate-200 mb-4 flex items-center space-x-2">
                <svg class="w-5 h-5 text-blue-600 dark:text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"></path></svg>
                <span>Podgląd i Linki</span>
            </h2>

            <!-- Wbudowane okno Mapy na całą szerokość (wysokie 480px) -->
            <div id="embeddedMapContainer" class="relative w-full h-[480px] bg-slate-100 dark:bg-slate-700 rounded-xl overflow-hidden border border-slate-200 dark:border-slate-600 hidden group transition-colors duration-200">
                <div class="absolute inset-0 flex items-center justify-center text-slate-400 flex-col space-y-2 z-0">
                    <svg class="animate-spin w-6 h-6 text-blue-500" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    <span class="text-xs font-medium">Ładowanie mapy...</span>
                </div>
                <iframe id="embeddedMapIframe" class="w-full h-full border-0 relative z-10" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
                
                <!-- Przycisk odświeżenia mapy -->
                <button id="refreshMapBtn" class="absolute top-3 right-14 z-20 bg-white/90 dark:bg-slate-800/90 hover:bg-white dark:hover:bg-slate-800 text-slate-800 dark:text-slate-200 p-2.5 rounded-lg shadow-sm transition backdrop-blur-sm opacity-0 group-hover:opacity-100 focus:opacity-100 cursor-pointer" title="Odśwież mapę">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path></svg>
                </button>

                <!-- Przycisk powiększenia -->
                <button id="expandMapBtn" class="absolute top-3 right-3 z-20 bg-white/90 dark:bg-slate-800/90 hover:bg-white dark:hover:bg-slate-800 text-slate-800 dark:text-slate-200 p-2.5 rounded-lg shadow-sm transition backdrop-blur-sm opacity-0 group-hover:opacity-100 focus:opacity-100 cursor-pointer" title="Powiększ mapę na cały ekran">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8V4m0 0h4M4 4l5 5m11-1V4m0 0h-4m4 0l5-5M4 16v4m0 0h4m-4 0l5-5m11 5l-5-5m5 5v-4m0 4h-4"></path></svg>
                </button>
            </div>

            <!-- Przyciski akcji z linkiem -->
            <div id="routeActions" class="grid grid-cols-1 sm:grid-cols-2 gap-4 opacity-50 pointer-events-none transition-opacity mt-5">
                <a 
                    id="mapsLink" 
                    href="#" 
                    target="_blank"
                    class="bg-emerald-600 hover:bg-emerald-700 text-white font-semibold py-3.5 px-4 rounded-xl shadow-sm hover:shadow transition flex items-center justify-center space-x-2 text-sm text-center"
                >
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14"></path>
                    </svg>
                    <span>Otwórz mapę w nowej karcie</span>
                </a>

                <button 
                    id="copyLinkBtn"
                    class="bg-slate-100 dark:bg-slate-700 hover:bg-slate-200 dark:hover:bg-slate-600 text-slate-700 dark:text-slate-200 font-medium py-3.5 px-4 rounded-xl transition text-sm flex items-center justify-center space-x-1.5 cursor-pointer"
                >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3"></path>
                    </svg>
                    <span>Kopiuj link do schowka</span>
                </button>
            </div>
        </div>

        <!-- Sekcja 4: Kalkulator Tacho (Pełna szerokość na dole) -->
        <div class="bg-white dark:bg-slate-800 p-6 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700 transition-colors duration-200">
            <div class="flex items-center space-x-3 mb-4">
                <div class="bg-indigo-100 dark:bg-indigo-900/50 text-indigo-600 dark:text-indigo-400 p-2 rounded-lg">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                    </svg>
                </div>
                <div>
                    <h3 class="font-semibold text-slate-800 dark:text-slate-200">Symulator Tacho (Szacunkowy czas tranzytu)</h3>
                    <p class="text-xs text-slate-500 dark:text-slate-400">Wpisz dystans odczytany z mapy, aby wyliczyć czas pracy kierowcy.</p>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-4 gap-4 items-end bg-slate-50 dark:bg-slate-700/50 p-4 rounded-xl border border-slate-100 dark:border-slate-600/50">
                <div class="w-full flex flex-col">
                    <label for="tachoDistance" class="text-xs font-semibold text-slate-600 dark:text-slate-300 mb-1">Dystans z mapy (km)</label>
                    <input type="number" id="tachoDistance" placeholder="np. 850" class="px-4 py-2.5 rounded-lg border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-700 text-slate-900 dark:text-white focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none text-sm w-full transition">
                </div>
                <div class="w-full flex flex-col">
                    <label for="tachoSpeed" class="text-xs font-semibold text-slate-600 dark:text-slate-300 mb-1">Średnia prędkość (km/h)</label>
                    <input type="number" id="tachoSpeed" value="70" class="px-4 py-2.5 rounded-lg border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-700 text-slate-900 dark:text-white focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none text-sm w-full transition">
                </div>
                <div class="w-full flex flex-col">
                    <label for="tachoStartTime" class="text-xs font-semibold text-slate-600 dark:text-slate-300 mb-1">Czas startu (załadunek)</label>
                    <input type="datetime-local" id="tachoStartTime" class="px-4 py-2.5 rounded-lg border border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-700 text-slate-900 dark:text-white focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none text-sm w-full transition" style="color-scheme: dark light;">
                </div>
                <div class="w-full">
                    <button id="calculateTachoBtn" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-medium py-2.5 px-4 rounded-lg shadow-sm hover:shadow transition flex items-center justify-center space-x-2 text-sm cursor-pointer">
                        <span>Przelicz Czas</span>
                    </button>
                </div>
            </div>

            <!-- Opcje dla tacho (magazyn) -->
            <div class="mt-4 flex items-center justify-between">
                <label class="flex items-center space-x-2 cursor-pointer text-sm">
                    <input type="checkbox" id="warehouseHours" checked class="text-indigo-600 focus:ring-indigo-500 rounded border-slate-300 dark:border-slate-600 bg-white dark:bg-slate-700">
                    <span class="text-slate-600 dark:text-slate-400 font-medium">Standardowe godziny pracy magazynu (8:00 - 16:00, pomija weekendy)</span>
                </label>
            </div>

            <!-- Pojemnik na wyniki Tacho -->
            <div id="tachoResult" class="mt-4 min-h-[4rem] flex items-center">
                <span class="text-sm text-slate-400 dark:text-slate-500 italic">Wpisz dystans powyżej, aby zobaczyć wyliczenia...</span>
            </div>
        </div>
    </main>

    <!-- Custom Notification Toast -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white text-xs px-4 py-3 rounded-xl shadow-xl transform translate-y-20 opacity-0 transition-all duration-300 z-50 flex items-center space-x-2">
        <svg class="w-4 h-4 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"></path>
        </svg>
        <span id="toastMessage">Skopiowano do schowka!</span>
    </div>

    <!-- Map Modal Overlay -->
    <div id="mapModal" class="fixed inset-0 z-50 hidden bg-slate-900 bg-opacity-75 flex items-center justify-center p-4 backdrop-blur-sm transition-opacity duration-300">
        <div class="bg-white dark:bg-slate-800 w-full max-w-5xl h-[85vh] rounded-2xl shadow-2xl flex flex-col overflow-hidden transform transition-transform duration-300 scale-95" id="mapModalContent">
            <div class="p-4 border-b border-slate-200 dark:border-slate-700 flex items-center justify-between bg-slate-50 dark:bg-slate-800">
                <h3 class="text-lg font-semibold text-slate-800 dark:text-slate-200 flex items-center space-x-2">
                    <svg class="w-5 h-5 text-blue-600 dark:text-blue-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 20l-5.447-2.724A1 1 0 013 16.382V5.618a1 1 0 011.447-.894L9 7m0 13l6-3m-6 3V7m6 10l4.553 2.276A1 1 0 0021 18.382V7.618a1 1 0 00-.553-.894L15 4m0 13V4m0 0L9 7"></path></svg>
                    <span>Podgląd Trasy</span>
                </h3>
                <button id="closeModalBtn" class="text-slate-400 hover:text-slate-700 dark:hover:text-slate-200 hover:bg-slate-200 dark:hover:bg-slate-700 p-2 rounded-lg transition cursor-pointer" title="Zamknij okno">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
                </button>
            </div>
            <div class="flex-grow w-full bg-slate-100 dark:bg-slate-700 relative">
                <div class="absolute inset-0 flex items-center justify-center text-slate-500 flex-col space-y-3 z-0">
                    <svg class="animate-spin w-8 h-8 text-blue-500" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    <span class="text-sm font-medium">Ładowanie mapy...</span>
                </div>
                <iframe id="mapIframe" class="w-full h-full border-0 relative z-10" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade"></iframe>
            </div>
        </div>
    </div>

    <script>
        // DOM Elements
        const inputText = document.getElementById('inputText');
        const extractBtn = document.getElementById('extractBtn');
        const smartExtractBtn = document.getElementById('smartExtractBtn');
        const clearBtn = document.getElementById('clearBtn');
        const tagsContainer = document.getElementById('tagsContainer');
        const badgeCount = document.getElementById('badgeCount');
        const emptyMessage = document.getElementById('emptyMessage');
        const routeActions = document.getElementById('routeActions');
        const mapsLink = document.getElementById('mapsLink');
        const copyLinkBtn = document.getElementById('copyLinkBtn');
        const orderModeRadios = document.getElementsByName('orderMode');
        const viaKufstein = document.getElementById('viaKufstein');
        const avoidFerries = document.getElementById('avoidFerries');
        const toast = document.getElementById('toast');
        const toastMessage = document.getElementById('toastMessage');
        const embeddedMapContainer = document.getElementById('embeddedMapContainer');
        const embeddedMapIframe = document.getElementById('embeddedMapIframe');
        const expandMapBtn = document.getElementById('expandMapBtn');
        const refreshMapBtn = document.getElementById('refreshMapBtn');

        // Image Paste Elements
        const imagePreviewContainer = document.getElementById('imagePreviewContainer');
        const pastedImageThumb = document.getElementById('pastedImageThumb');
        const removeImageBtn = document.getElementById('removeImageBtn');
        const imageStatus = document.getElementById('imageStatus');
        let attachedImageBase64 = null;
        let attachedImageMimeType = null;

        // Map Modal Elements
        const mapModal = document.getElementById('mapModal');
        const mapModalContent = document.getElementById('mapModalContent');
        const mapIframe = document.getElementById('mapIframe');
        const closeModalBtn = document.getElementById('closeModalBtn');

        // Tacho Simulator Elements
        const tachoDistance = document.getElementById('tachoDistance');
        const tachoSpeed = document.getElementById('tachoSpeed');
        const tachoStartTime = document.getElementById('tachoStartTime');
        const calculateTachoBtn = document.getElementById('calculateTachoBtn');
        const warehouseHours = document.getElementById('warehouseHours');
        const tachoResult = document.getElementById('tachoResult');

        // Theme Elements
        const themeToggleBtn = document.getElementById('themeToggleBtn');
        const htmlElement = document.documentElement;

        // Initialize Theme from localStorage or system preference
        if (localStorage.getItem('theme') === 'dark' || (!('theme' in localStorage) && window.matchMedia('(prefers-color-scheme: dark)').matches)) {
            htmlElement.classList.add('dark');
        } else {
            htmlElement.classList.remove('dark');
        }

        // Toggle Theme
        themeToggleBtn.addEventListener('click', () => {
            htmlElement.classList.toggle('dark');
            if (htmlElement.classList.contains('dark')) {
                localStorage.setItem('theme', 'dark');
            } else {
                localStorage.setItem('theme', 'light');
            }
        });

        let currentCodes = [];

        function showToast(msg) {
            toastMessage.textContent = msg;
            toast.classList.remove('translate-y-20', 'opacity-0');
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }

        // Obsługa wklejania obrazu ze schowka (Screenshot OCR)
        inputText.addEventListener('paste', (e) => {
            const items = (e.clipboardData || e.originalEvent.clipboardData).items;
            for (let i = 0; i < items.length; i++) {
                if (items[i].type.indexOf('image') === 0) {
                    const blob = items[i].getAsFile();
                    const reader = new FileReader();
                    reader.onload = function(event) {
                        const base64DataFull = event.target.result;
                        attachedImageMimeType = blob.type || 'image/png';
                        // Wyciągnięcie czystego base64 bez nagłówka data url
                        attachedImageBase64 = base64DataFull.split(',')[1];
                        
                        pastedImageThumb.src = base64DataFull;
                        imagePreviewContainer.classList.remove('hidden');
                        imagePreviewContainer.classList.add('flex');
                        imageStatus.classList.remove('hidden');
                        showToast('Zrzut ekranu został załączony do analizy!');
                    };
                    reader.readAsDataURL(blob);
                    e.preventDefault();
                    break;
                }
            }
        });

        removeImageBtn.addEventListener('click', () => {
            attachedImageBase64 = null;
            attachedImageMimeType = null;
            imagePreviewContainer.classList.add('hidden');
            imagePreviewContainer.classList.remove('flex');
            imageStatus.classList.add('hidden');
            showToast('Usunięto zrzut ekranu');
        });

        // Wyrażenie regularne dla zagranicznych i polskich kodów logistycznych
        const postalCodeRegex = /\b(?:[a-zA-Z]{1,3}[-\s]+)?(?:\d{2}-\d{3}|\d{4,5})\b|\b[a-zA-Z]{1,2}\d[a-zA-Z\d]?\s*\d[a-zA-Z]{2}\b/g;

        function processExtraction() {
            const text = inputText.value;
            let matches = [];
            let match;
            
            const regex = new RegExp(postalCodeRegex.source, postalCodeRegex.flags);
            
            while ((match = regex.exec(text)) !== null) {
                let code = match[0];
                let index = match.index;
                let endOfCode = index + code.length;
                
                let followingText = text.substring(endOfCode);
                let separatorRegex = /[\n,;|]|\d|\s-|-\s/g;
                let sepMatch = separatorRegex.exec(followingText);
                
                let cityPart = "";
                if (sepMatch) {
                    cityPart = followingText.substring(0, sepMatch.index);
                } else {
                    cityPart = followingText;
                }
                
                cityPart = cityPart.replace(/[^\p{L}\p{M}\-.' \t]/gu, '').trim();
                
                let words = cityPart.split(/\s+/).filter(w => w.length > 0);
                if (words.length > 3) {
                    words = words.slice(0, 3);
                }
                cityPart = words.join(' ');
                
                let combined = code;
                if (cityPart) {
                    combined += " " + cityPart;
                }
                
                matches.push(combined.trim().replace(/\s+/g, ' ').toUpperCase());
            }
            
            const mode = document.querySelector('input[name="orderMode"]:checked').value;
            if (mode === 'unique') {
                currentCodes = [...new Set(matches)];
            } else {
                currentCodes = matches;
            }

            renderTags();
            updateGoogleMapsLink();
        }

        // Moduł Inteligentny: Smart Extraction using AI API (Text + Image OCR)
        async function processSmartExtraction() {
            const text = inputText.value.trim();
            if (!text && !attachedImageBase64) {
                showToast('Wklej tekst lub zrzut ekranu do analizy!');
                return;
            }

            const originalHtml = smartExtractBtn.innerHTML;
            smartExtractBtn.innerHTML = `
                <svg class="animate-spin w-4 h-4 mr-2 text-white" fill="none" viewBox="0 0 24 24">
                    <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                    <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <span>Analizuję AI...</span>
            `;
            smartExtractBtn.disabled = true;
            smartExtractBtn.classList.add('opacity-75', 'cursor-not-allowed');
            extractBtn.disabled = true;

            try {
                const apiKey = "AQ.Ab8RN6KPpPok7aYbK3s02ZuIBkEqXk78G79163DfJJHxge8bJQ"; 
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-3-flash-preview:generateContent?key=${apiKey}`;
                
                let contentParts = [];
                let promptText = `Przeanalizuj dołączony tekst lub obraz ze zlecenia transportowego. Znajdź wszystkie punkty trasy (kody pocztowe i miejscowości). 
                Zwróć na nie uwagę w kontekście logistycznym. Jeśli jest załadunek (loading), powinien być na początku. Jeśli są rozładunki (unloading), uporządkuj je po załadunku chronologicznie.
                Zwróć wyodrębnione miejsca jako tablicę ciągów znaków. Ignoruj całkowicie wagę, towary, numery kont, numery naczep czy imiona spedytorów.`;
                
                if (text) {
                    promptText += `\n\nTekst zlecenia:\n${text}`;
                }
                
                contentParts.push({ text: promptText });

                if (attachedImageBase64) {
                    contentParts.push({
                        inlineData: {
                            mimeType: attachedImageMimeType,
                            data: attachedImageBase64
                        }
                    });
                }

                const payload = {
                    contents: [{
                        role: "user",
                        parts: contentParts
                    }],
                    generationConfig: {
                        responseMimeType: "application/json",
                        responseSchema: {
                            type: "OBJECT",
                            properties: {
                                locations: {
                                    type: "ARRAY",
                                    items: { type: "STRING" },
                                    description: "Uporządkowana lista punktów trasy z kodami krajów"
                                }
                            },
                            required: ["locations"]
                        }
                    },
                    systemInstruction: {
                        parts: [{ text: "Jesteś ekspertem ds. logistyki i OCR. Z chaotycznego tekstu lub zrzutu ekranu wydobywasz czyste, uporządkowane punkty adresowe. TWOIM ABSOLUTNYM OBOWIĄZKIEM JEST WYGENEROWANIE DLA KAŻDEGO PUNKTU DWULITEROWEGO KODU KRAJU ISO (np. DE, FR, PL, HU, IT). Jeśli w tekście/obrazie występuje kod pocztowy bez kraju, odgadnij go z kontekstu lub nazwy kraju/miasta w sąsiedztwie. ZABRONIONE jest zwracanie samego kodu bez przedrostka państwa. Format finalny: KOD_KRAJU KOD_POCZTOWY MIEJSCOWOŚĆ (np. DE 99428 GRAMMETAL). Używaj wyłącznie wielkich liter." }]
                    }
                };

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) throw new Error("API responded with status " + response.status);

                const result = await response.json();
                
                if (result.candidates && result.candidates.length > 0 && 
                    result.candidates[0].content && result.candidates[0].content.parts.length > 0) {
                    
                    const jsonResponse = result.candidates[0].content.parts[0].text;
                    const parsedData = JSON.parse(jsonResponse);
                    
                    if (parsedData.locations && parsedData.locations.length > 0) {
                        currentCodes = parsedData.locations.map(loc => loc.toUpperCase());
                        
                        const mode = document.querySelector('input[name="orderMode"]:checked').value;
                        if (mode === 'unique') {
                            currentCodes = [...new Set(currentCodes)];
                        }
                        
                        renderTags();
                        updateGoogleMapsLink();
                        showToast('Analiza AI / OCR zakończona sukcesem!');
                    } else {
                        showToast('AI nie znalazło żadnych adresów w podanych danych.');
                    }
                } else {
                    showToast('Nieoczekiwana odpowiedź od AI.');
                }
                
            } catch (error) {
                console.error("Błąd analizy AI:", error);
                showToast('Wystąpił błąd podczas analizy AI. Sprawdź konsolę.');
            } finally {
                smartExtractBtn.innerHTML = originalHtml;
                smartExtractBtn.disabled = false;
                smartExtractBtn.classList.remove('opacity-75', 'cursor-not-allowed');
                extractBtn.disabled = false;
            }
        }

        // Render tags into the UI
        function renderTags() {
            tagsContainer.innerHTML = '';
            badgeCount.textContent = currentCodes.length;

            if (currentCodes.length === 0) {
                emptyMessage.style.display = 'block';
                routeActions.classList.add('opacity-50', 'pointer-events-none');
                return;
            }

            emptyMessage.style.display = 'none';
            routeActions.classList.remove('opacity-50', 'pointer-events-none');

            currentCodes.forEach((code, index) => {
                const tag = document.createElement('div');
                tag.className = 'draggable-tag inline-flex items-center bg-blue-50 dark:bg-slate-700 border border-blue-200 dark:border-slate-600 text-blue-700 dark:text-blue-300 text-xs px-2.5 py-1.5 rounded-lg font-medium space-x-1.5 shadow-xs cursor-move transition-all hover:bg-blue-100 dark:hover:bg-slate-600';
                tag.draggable = true;
                tag.dataset.index = index;
                tag.innerHTML = `
                    <span class="text-blue-400 font-bold select-none cursor-move" title="Przeciągnij, aby zmienić kolejność">⋮⋮ ${index + 1}.</span>
                    <span>${code}</span>
                    <button type="button" onclick="removeCode(${index})" class="text-blue-400 hover:text-red-600 ml-1 focus:outline-none cursor-pointer" title="Usuń ten punkt">
                        &times;
                    </button>
                `;
                
                tag.addEventListener('dragstart', handleDragStart);
                tag.addEventListener('dragover', handleDragOver);
                tag.addEventListener('dragenter', handleDragEnter);
                tag.addEventListener('dragleave', handleDragLeave);
                tag.addEventListener('drop', handleDrop);
                tag.addEventListener('dragend', handleDragEnd);

                tagsContainer.appendChild(tag);
            });
        }

        // Drag & Drop
        let dragSrcEl = null;

        function handleDragStart(e) {
            dragSrcEl = this;
            e.dataTransfer.effectAllowed = 'move';
            e.dataTransfer.setData('text/plain', this.dataset.index);
            this.classList.add('opacity-40', 'scale-95');
        }

        function handleDragOver(e) {
            if (e.preventDefault) e.preventDefault();
            e.dataTransfer.dropEffect = 'move';
            return false;
        }

        function handleDragEnter(e) {
            if (this !== dragSrcEl) {
                this.classList.add('border-blue-500', 'bg-blue-200', 'dark:bg-blue-900', 'shadow-md');
            }
        }

        function handleDragLeave(e) {
            this.classList.remove('border-blue-500', 'bg-blue-200', 'dark:bg-blue-900', 'shadow-md');
        }

        function handleDrop(e) {
            if (e.stopPropagation) e.stopPropagation();

            if (dragSrcEl !== this) {
                const fromIndex = parseInt(dragSrcEl.dataset.index);
                const toIndex = parseInt(this.dataset.index);
                
                const movedItem = currentCodes.splice(fromIndex, 1)[0];
                currentCodes.splice(toIndex, 0, movedItem);
                
                renderTags();
                updateGoogleMapsLink();
            }
            return false;
        }

        function handleDragEnd(e) {
            this.classList.remove('opacity-40', 'scale-95');
            const tags = document.querySelectorAll('.draggable-tag');
            tags.forEach(tag => {
                tag.classList.remove('border-blue-500', 'bg-blue-200', 'dark:bg-blue-900', 'shadow-md');
            });
        }

        window.removeCode = function(index) {
            currentCodes.splice(index, 1);
            renderTags();
            updateGoogleMapsLink();
        };

        function getFinalRoutePoints() {
            if (currentCodes.length === 0) return [];
            let points = [...currentCodes];
            
            if (viaKufstein.checked && points.length >= 2) {
                let insertIndex = -1;
                
                for (let i = 0; i < points.length - 1; i++) {
                    const p1 = points[i].trim().toUpperCase();
                    const p2 = points[i+1].trim().toUpperCase();
                    
                    const p1IsIT = p1.startsWith('IT');
                    const p2IsIT = p2.startsWith('IT');
                    const p1IsDE = p1.startsWith('DE');
                    const p2IsDE = p2.startsWith('DE');
                    
                    if ((p1IsDE && p2IsIT) || (p1IsIT && p2IsDE)) {
                        insertIndex = i + 1;
                        break;
                    }
                    
                    if (p1IsIT !== p2IsIT) {
                        insertIndex = i + 1;
                    }
                }
                
                if (insertIndex === -1) {
                    for (let i = 0; i < points.length - 1; i++) {
                        const p1 = points[i].trim().toUpperCase();
                        const p2 = points[i+1].trim().toUpperCase();
                        const p1IsDE = p1.startsWith('DE');
                        const p2IsDE = p2.startsWith('DE');
                        
                        const isNorth = (code) => code.startsWith('NL') || code.startsWith('BE') || code.startsWith('PL') || code.startsWith('DK') || code.startsWith('SE') || code.startsWith('GB');
                        
                        if (p1IsDE && !p2IsDE && !isNorth(p2)) {
                            insertIndex = i + 1;
                            break;
                        } else if (!p1IsDE && p2IsDE && !isNorth(p1)) {
                            insertIndex = i + 1;
                            break;
                        }
                    }
                }

                if (insertIndex === -1) {
                    insertIndex = Math.floor(points.length / 2);
                }
                
                points.splice(insertIndex, 0, "6330 KUFSTEIN, AUSTRIA");
            }
            
            return points;
        }

        function updateGoogleMapsLink() {
            const points = getFinalRoutePoints();
            
            if (points.length < 2) {
                mapsLink.href = '#';
                embeddedMapContainer.classList.add('hidden');
                embeddedMapIframe.src = '';
                return;
            }

            embeddedMapContainer.classList.remove('hidden');

            const origin = encodeURIComponent(points[0]);
            const destination = encodeURIComponent(points[points.length - 1]);
            
            let url = `https://www.google.com/maps/dir/?api=1&origin=${origin}&destination=${destination}&travelmode=driving`;

            if (points.length > 2) {
                const intermediate = points.slice(1, points.length - 1);
                const waypointsStr = intermediate.map(c => encodeURIComponent(c)).join('|');
                url += `&waypoints=${waypointsStr}`;
            }

            if (avoidFerries.checked) {
                url += `&avoid=ferries`;
            }
            mapsLink.href = url;

            let daddr = '';
            if (points.length > 2) {
                const waypointsEmbed = points.slice(1).map(c => encodeURIComponent(c));
                daddr = waypointsEmbed.join('+to:');
            } else {
                daddr = encodeURIComponent(points[1]);
            }
            
            let dirFlags = 'd';
            if (avoidFerries.checked) {
                dirFlags += 'f';
            }
            
            let iframeUrl = `https://maps.google.com/maps?saddr=${origin}&daddr=${daddr}&output=embed&dirflg=${dirFlags}`;
            embeddedMapIframe.src = iframeUrl;
        }

        // Tacho Simulator
        function calculateTacho() {
            const distance = parseFloat(tachoDistance.value);
            const speed = parseFloat(tachoSpeed.value);

            if (isNaN(distance) || distance <= 0 || isNaN(speed) || speed <= 0) {
                tachoResult.innerHTML = '<span class="text-sm text-slate-400 italic">Wpisz poprawny dystans i prędkość powyżej, aby zobaczyć wyliczenia...</span>';
                return;
            }

            const driveTimeHours = distance / speed;
            let remainingDrive = driveTimeHours;
            let totalTimeHours = 0;
            let pauses45 = 0;
            let pauses11 = 0;
            let chunkIndex = 0;

            while (remainingDrive > 0) {
                if (remainingDrive > 4.5) {
                    totalTimeHours += 4.5;
                    remainingDrive -= 4.5;

                    if (chunkIndex % 2 === 1) {
                        totalTimeHours += 11;
                        pauses11++;
                    } else {
                        totalTimeHours += 0.75;
                        pauses45++;
                    }
                    chunkIndex++;
                } else {
                    totalTimeHours += remainingDrive;
                    remainingDrive = 0;
                }
            }

            const formatTime = (totalHours) => {
                const days = Math.floor(totalHours / 24);
                const hours = Math.floor((totalHours % 24));
                const minutes = Math.round((totalHours % 1) * 60);

                let timeStr = '';
                if (days > 0) timeStr += `<span class="text-2xl">${days}</span><span class="text-sm font-normal mx-1">dni</span>`;
                if (hours > 0) timeStr += `<span class="text-2xl">${hours}</span><span class="text-sm font-normal mx-1">godz</span>`;
                if (minutes > 0 || timeStr === '') timeStr += `<span class="text-2xl">${minutes}</span><span class="text-sm font-normal ml-1">min</span>`;
                return timeStr;
            };

            const formatSimpleTime = (totalHours) => {
                const h = Math.floor(totalHours);
                const m = Math.round((totalHours % 1) * 60);
                return `${h}h ${m}m`;
            }

            let etaHtml = '';
            let isDelayedByWarehouse = false;

            if (tachoStartTime.value) {
                const startDate = new Date(tachoStartTime.value);
                let etaDate = new Date(startDate.getTime() + totalTimeHours * 60 * 60 * 1000);
                
                if (warehouseHours.checked) {
                    let originalTime = etaDate.getTime();
                    let h = etaDate.getHours();
                    
                    if (h >= 16) {
                        etaDate.setDate(etaDate.getDate() + 1);
                        etaDate.setHours(8, 0, 0, 0);
                    } else if (h < 8) {
                        etaDate.setHours(8, 0, 0, 0);
                    }

                    let day = etaDate.getDay();
                    if (day === 6) { 
                        etaDate.setDate(etaDate.getDate() + 2);
                        etaDate.setHours(8, 0, 0, 0);
                    } else if (day === 0) { 
                        etaDate.setDate(etaDate.getDate() + 1);
                        etaDate.setHours(8, 0, 0, 0);
                    }

                    if (etaDate.getTime() !== originalTime) {
                        isDelayedByWarehouse = true;
                    }
                }
                
                const formatter = new Intl.DateTimeFormat('pl-PL', { 
                    weekday: 'long', 
                    day: 'numeric', 
                    month: 'long', 
                    hour: '2-digit', 
                    minute: '2-digit' 
                });
                
                let formattedEta = formatter.format(etaDate);
                formattedEta = formattedEta.charAt(0).toUpperCase() + formattedEta.slice(1);

                let warehouseNotice = isDelayedByWarehouse 
                    ? `<span class="block text-xs font-semibold text-orange-200 mt-1">Awizo zostało przesunięte na godziny otwarcia magazynu.</span>` 
                    : '';

                etaHtml = `
                    <div class="col-span-1 md:col-span-4 mt-2 bg-gradient-to-r from-indigo-600 to-blue-600 dark:from-indigo-800 dark:to-blue-800 p-4 rounded-xl shadow-md flex items-center justify-between text-white">
                        <div class="flex items-center space-x-4">
                            <div class="bg-white/20 p-2 rounded-lg">
                                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
                            </div>
                            <div>
                                <span class="block text-[10px] font-medium text-indigo-100 uppercase tracking-wider mb-0.5">Szacowany czas dostawy (ETA)</span>
                                <span class="text-xl font-bold leading-none">${formattedEta}</span>
                                ${warehouseNotice}
                            </div>
                        </div>
                    </div>
                `;
            }

            tachoResult.innerHTML = `
                <div class="w-full">
                    <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mt-2">
                        <div class="bg-blue-50 dark:bg-blue-900/20 p-4 rounded-xl border border-blue-100 dark:border-blue-800/50 flex flex-col justify-center">
                            <span class="block text-[10px] text-blue-500 dark:text-blue-400 font-bold uppercase tracking-wider mb-1">Sama jazda</span>
                            <span class="text-lg font-bold text-slate-800 dark:text-slate-200">${formatSimpleTime(driveTimeHours)}</span>
                        </div>
                        <div class="bg-orange-50 dark:bg-orange-900/20 p-4 rounded-xl border border-orange-100 dark:border-orange-800/50 flex flex-col justify-center">
                            <span class="block text-[10px] text-orange-500 dark:text-orange-400 font-bold uppercase tracking-wider mb-1">Pauzy 45 min</span>
                            <span class="text-2xl font-bold text-slate-800 dark:text-slate-200">${pauses45}</span>
                        </div>
                        <div class="bg-indigo-50 dark:bg-indigo-900/20 p-4 rounded-xl border border-indigo-100 dark:border-indigo-800/50 flex flex-col justify-center">
                            <span class="block text-[10px] text-indigo-500 dark:text-indigo-400 font-bold uppercase tracking-wider mb-1">Pauzy dobowe (11h)</span>
                            <span class="text-2xl font-bold text-slate-800 dark:text-slate-200">${pauses11}</span>
                        </div>
                        <div class="bg-emerald-50 dark:bg-emerald-900/20 p-4 rounded-xl border border-emerald-300 dark:border-emerald-700/50 shadow-sm flex flex-col justify-center">
                            <span class="block text-[10px] text-emerald-700 dark:text-emerald-400 font-bold uppercase tracking-wider mb-1">Czas na dojazd</span>
                            <span class="font-bold text-emerald-800 dark:text-emerald-300 flex items-baseline">${formatTime(totalTimeHours)}</span>
                        </div>
                        ${etaHtml}
                    </div>
                    <p class="text-[10px] text-slate-400 dark:text-slate-500 mt-3">* Standardowe reguły (9h jazdy, pauza 45m co 4.5h, pauza dobowa 11h). Nie uwzględnia weekendów, korków ani wydłużeń do 10h.</p>
                </div>
            `;
        }

        // Event Listeners
        extractBtn.addEventListener('click', processExtraction);
        smartExtractBtn.addEventListener('click', processSmartExtraction);
        
        tachoDistance.addEventListener('input', calculateTacho);
        tachoSpeed.addEventListener('input', calculateTacho);
        tachoStartTime.addEventListener('input', calculateTacho);
        calculateTachoBtn.addEventListener('click', calculateTacho);
        warehouseHours.addEventListener('change', calculateTacho);

        orderModeRadios.forEach(radio => {
            radio.addEventListener('change', processExtraction);
        });

        viaKufstein.addEventListener('change', updateGoogleMapsLink);
        avoidFerries.addEventListener('change', updateGoogleMapsLink);

        clearBtn.addEventListener('click', () => {
            inputText.value = '';
            attachedImageBase64 = null;
            attachedImageMimeType = null;
            imagePreviewContainer.classList.add('hidden');
            imagePreviewContainer.classList.remove('flex');
            imageStatus.classList.add('hidden');
            currentCodes = [];
            renderTags();
        });

        copyLinkBtn.addEventListener('click', () => {
            const url = mapsLink.href;
            if (!url || url === '#') return;

            const dummy = document.createElement('textarea');
            document.body.appendChild(dummy);
            dummy.value = url;
            dummy.select();
            try {
                document.execCommand('copy');
                showToast('Link do trasy skopiowany do schowka!');
            } catch (err) {
                showToast('Nie udało się skopiować linku.');
            }
            document.body.removeChild(dummy);
        });

        refreshMapBtn.addEventListener('click', () => {
            embeddedMapIframe.src = '';
            setTimeout(() => {
                updateGoogleMapsLink();
                showToast('Podgląd mapy został zaktualizowany');
            }, 150);
        });

        expandMapBtn.addEventListener('click', () => {
            if (!embeddedMapIframe.src) return;
            mapIframe.src = embeddedMapIframe.src;
            mapModal.classList.remove('hidden');
            setTimeout(() => {
                mapModalContent.classList.remove('scale-95');
                mapModalContent.classList.add('scale-100');
            }, 10);
        });

        closeModalBtn.addEventListener('click', () => {
            mapModalContent.classList.remove('scale-100');
            mapModalContent.classList.add('scale-95');
            setTimeout(() => {
                mapModal.classList.add('hidden');
                mapIframe.src = '';
            }, 300);
        });
        
        mapModal.addEventListener('click', (e) => {
            if (e.target === mapModal) {
                closeModalBtn.click();
            }
        });
    </script>
</body>
</html>
