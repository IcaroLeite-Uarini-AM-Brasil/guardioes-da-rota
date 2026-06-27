# guardioes-da-rota
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Guardiões da Rota - Solução de Rastreamento Urbanos</title>
    <!-- Tailwind CSS para Estilização Ultraleve e Responsiva -->
    <script src="https://tailwindcss.com"></script>
    <!-- Leaflet API: Mapas Gratuitos de Alta Performance (Sem travar com 10k acessos) -->
    <link rel="stylesheet" href="https://unpkg.com" />
    <script src="https://unpkg.com"></script>
    <!-- Firebase SDK Core para Sincronização em Tempo Real -->
    <script src="https://gstatic.com"></script>
    <script src="https://gstatic.com"></script>
</head>
<body class="bg-gray-900 text-white font-sans antialiased selection:bg-emerald-500 selection:text-white">

    <!-- 1. TELA DE SEGURANÇA E LOGIN -->
    <div id="login-screen" class="min-h-screen flex items-center justify-center p-4 bg-gradient-to-br from-gray-950 to-gray-900">
        <div class="bg-gray-800 p-8 rounded-2xl shadow-2xl max-w-md w-full border border-gray-700 transition-all transform hover:scale-[1.01]">
            <div class="text-center mb-6">
                <span class="text-4xl">📍</span>
                <h1 class="text-3xl font-extrabold text-emerald-400 mt-2">Guardiões da Rota</h1>
                <p class="text-gray-400 text-xs mt-1">Soluções de Localização em Tempo Real</p>
            </div>
            <div class="space-y-4">
                <div>
                    <label class="block text-xs font-semibold uppercase tracking-wider text-gray-400 mb-1">Chave de Homologação (PIN)</label>
                    <input type="password" id="pin-input" placeholder="Digite 1234 para testar" class="w-full bg-gray-900 border border-gray-700 rounded-xl px-4 py-3 text-white focus:outline-none focus:border-emerald-500 transition-all text-center tracking-widest text-lg font-mono">
                </div>
                <button onclick="autenticar()" class="w-full bg-emerald-500 hover:bg-emerald-600 text-gray-900 font-bold py-3.5 rounded-xl transition-all shadow-lg shadow-emerald-500/10 font-bold active:scale-[0.98]">Entrar no Painel de Controle</button>
            </div>
            <p id="login-erro" class="text-red-400 text-xs text-center mt-4 hidden animate-shake">⚠️ PIN inválido. Digite 1234 para o ambiente de testes.</p>
        </div>
    </div>

    <!-- 2. APLICATIVO PRINCIPAL E GUIA PEDAGÓGICO -->
    <div id="main-app" class="hidden min-h-screen flex flex-col">
        <!-- Barra de Navegação Superior -->
        <header class="bg-gray-800 border-b border-gray-700 p-4 sticky top-0 z-50 shadow-md">
            <div class="max-w-7xl mx-auto flex flex-col sm:flex-row justify-between items-center gap-4">
                <div class="flex items-center gap-3">
                    <span class="text-2xl">📍</span>
                    <div>
                        <h1 class="font-extrabold text-lg text-emerald-400 leading-tight">Guardiões da Rota</h1>
                        <p class="text-[10px] text-gray-400">Repositório: IcaroLeite-Uarini-AM-Brasil</p>
                    </div>
                </div>
                <div class="flex items-center gap-2">
                    <button onclick="alternarModalPedagogico()" class="bg-blue-600 hover:bg-blue-700 text-white text-xs font-bold px-4 py-2.5 rounded-xl transition-all shadow-md">📖 Ver Manual do Projeto (Documentação)</button>
                    <button onclick="logout()" class="bg-gray-700 hover:bg-red-500/20 hover:text-red-400 text-xs px-3 py-2.5 rounded-xl transition-all border border-gray-600">Sair</button>
                </div>
            </div>
        </header>

        <!-- Grid de Operação -->
        <main class="flex-1 max-w-7xl w-full mx-auto p-4 grid grid-cols-1 lg:grid-cols-3 gap-6">
            
            <!-- Painel Esquerdo: Entrada de Dados e Alertas -->
            <div class="space-y-6 lg:col-span-1">
                <!-- Controle de Transmissão de Coordenadas -->
                <div class="bg-gray-800 p-6 rounded-2xl border border-gray-700 shadow-xl">
                    <h2 class="text-md font-bold text-emerald-400 mb-4 flex items-center gap-2">📡 Transmitir Coordenadas (GPS)</h2>
                    <div class="space-y-4">
                        <div>
                            <label class="block text-xs font-semibold text-gray-400 mb-1">Identificador do Alvo (Ex: VAN-01, PET-BOB, IDOSO-JOAO)</label>
                            <input type="text" id="dev-id" value="TESTER-01" class="w-full bg-gray-900 border border-gray-700 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500 font-mono">
                        </div>
                        <div class="grid grid-cols-2 gap-3">
                            <div>
                                <label class="block text-xs font-semibold text-gray-400 mb-1">Latitude</label>
                                <input type="number" step="any" id="dev-lat" value="-2.9912" class="w-full bg-gray-900 border border-gray-700 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500 font-mono">
                            </div>
                            <div>
                                <label class="block text-xs font-semibold text-gray-400 mb-1">Longitude</label>
                                <input type="number" step="any" id="dev-lng" value="-65.1082" class="w-full bg-gray-900 border border-gray-700 rounded-xl px-4 py-2.5 text-sm text-white focus:outline-none focus:border-emerald-500 font-mono">
                            </div>
                        </div>
                        <button onclick="capturarGPSReal()" class="w-full bg-gray-700 hover:bg-gray-600 text-white font-medium py-2 rounded-xl text-xs transition-all border border-gray-600 flex items-center justify-center gap-1">📍 Detectar Meu GPS do Aparelho</button>
                        <button onclick="enviarDadosBanco('Ativo')" class="w-full bg-emerald-500 hover:bg-emerald-600 text-gray-900 font-extrabold py-3 rounded-xl transition-all shadow-md active:scale-[0.98]">Atualizar Localização no Mapa</button>
                    </div>
                </div>

                <!-- Módulo de Segurança Avançado (Passo 5) -->
                <div class="bg-gray-800 p-6 rounded-2xl border border-red-900/50 bg-gradient-to-b from-gray-800 to-red-950/20 shadow-xl">
                    <h2 class="text-md font-bold text-red-400 mb-2 flex items-center gap-2">🚨 Botão de Pânico / Emergência</h2>
                    <p class="text-xs text-gray-400 mb-4">Muda imediatamente o estado do pino para vermelho visível para todos os testadores na rede.</p>
                    <button onclick="enviarDadosBanco('ALERTA CRÍTICO')" class="w-full bg-red-600 hover:bg-red-700 text-white font-extrabold py-4 rounded-xl transition-all shadow-lg uppercase tracking-wider animate-pulse active:scale-[0.98]">⚠️ Disparar Alerta Geral</button>
                </div>
            </div>

            <!-- Painel Direito: Mapa Dinâmico em Tempo Real -->
            <div class="lg:col-span-2 flex flex-col space-y-4">
                <div class="bg-gray-800 p-6 rounded-2xl border border-gray-700 shadow-xl flex-1 flex flex-col min-h-[450px]">
                    <div class="flex justify-between items-center mb-4">
                        <h2 class="text-md font-bold text-emerald-400 flex items-center gap-2">🗺️ Tela de Monitoramento Georreferenciado</h2>
                        <span class="flex h-2 w-2 relative">
                            <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span>
                            <span class="relative inline-flex rounded-full h-2 w-2 bg-emerald-500"></span>
                        </span>
                    </div>
                    <!-- Área de renderização do mapa Leaflet -->
                    <div id="map" class="w-full flex-1 rounded-xl bg-gray-900 border border-gray-700 overflow-hidden z-10 shadow-inner"></div>
                </div>
            </div>
        </main>
    </div>

    <!-- 3. MODAL PEDAGÓGICO DE DOCUMENTAÇÃO (Guia completo para o Professor e Alunos) -->
    <div id="pedagogic-modal" class="hidden fixed inset-0 bg-black/80 flex items-center justify-center p-4 z-50 backdrop-blur-sm">
        <div class="bg-gray-800 border border-gray-700 rounded-2xl max-w-4xl w-full max-h-[85vh] overflow-y-auto p-6 shadow-2xl space-y-6">
            <div class="flex justify-between items-center border-b border-gray-700 pb-4">
                <h2 class="text-xl font-extrabold text-emerald-400">📖 Planejamento Pedagógico do Projeto</h2>
                <button onclick="alternarModalPedagogico()" class="text-gray-400 hover:text-white font-bold text-lg">✕</button>
            </div>
            
            <div class="space-y-6 text-sm text-gray-300 leading-relaxed">
                <div>
                    <h3 class="font-bold text-white text-base mb-2">🗓️ Cronograma Didático (6 Dias)</h3>
                    <ul class="list-disc pl-5 space-y-1 text-gray-400">
                        <li><b>Dia 1:</b> Escolha do nicho (vans, pets, idosos) e desenho do Wireframe no papel.</li>
                        <li><b>Dia 2:</b> Definição de UI/UX (Cores, botões intuitivos e caminhos).</li>
                        <li><b>Dia 3:</b> Programação de botões e integração com sensores de localização.</li>
                        <li><b>Dia 4:</b> Conexão ao banco de dados e testes de persistência na nuvem.</li>
                        <li><b>Dia 5:</b> Implementação de segurança (login corporativo e botão de pânico).</li>
