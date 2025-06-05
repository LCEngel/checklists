<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Checklist Offline - Matrícula Sempre Visível</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.4.0/css/all.min.css">
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #f5e9d9 0%, #e6d2b5 50%, #d9c09e 100%);
            min-height: 100vh;
        }
        .sidebar {
            background: linear-gradient(to bottom, #8d6e63 0%, #795548 100%);
            width: 280px;
            transition: transform 0.3s ease;
        }
        .sidebar-hidden {
            transform: translateX(-100%);
        }
        .main-content {
            transition: margin-left 0.3s ease;
        }
        .card {
            background: linear-gradient(to bottom, #f8f3ec 0%, #f1e7d9 100%);
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(121, 85, 72, 0.15);
            border: 1px solid rgba(121, 85, 72, 0.1);
        }
        .btn-primary {
            background: linear-gradient(to bottom, #a1887f 0%, #8d6e63 100%);
        }
        .btn-primary:hover {
            background: linear-gradient(to bottom, #8d6e63 0%, #795548 100%);
        }
        .btn-online {
            background: linear-gradient(to bottom, #10b981 0%, #059669 100%);
        }
        .btn-online:hover {
            background: linear-gradient(to bottom, #059669 0%, #047857 100%);
        }
        .btn-white {
            background: linear-gradient(to bottom, #f5f5f5 0%, #e0e0e0 100%);
            color: #5d4037;
            border: 1px solid #d7ccc8;
        }
        .btn-white:hover {
            background: linear-gradient(to bottom, #eeeeee 0%, #d6d6d6 100%);
        }
        .checklist-item {
            background: rgba(255, 255, 255, 0.5);
            border-bottom: 1px solid #e0d1c1;
        }
        .checklist-item:hover {
            background: rgba(255, 255, 255, 0.8);
        }
        .status-btn.active {
            background: #8d6e63;
            color: white;
            border-color: #6d4c41;
        }
        .status-btn.nc.active {
            background: #c62828;
            color: white;
            border-color: #b71c1c;
        }
        .status-btn.na.active {
            background: #757575;
            color: white;
            border-color: #616161;
        }
        .sidebar-item {
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            transition: background-color 0.2s;
        }
        .sidebar-item:hover {
            background-color: rgba(255, 255, 255, 0.1);
        }
        .sidebar-item.active {
            background-color: rgba(255, 255, 255, 0.2);
            border-left: 4px solid white;
        }
        .progress-bar {
            height: 8px;
            background: #efebe9;
            border-radius: 4px;
            overflow: hidden;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
        }
        .progress-value {
            height: 100%;
            background: linear-gradient(to right, #a1887f, #8d6e63);
            transition: width 0.3s ease;
        }
        @media (max-width: 768px) {
            .sidebar {
                position: fixed;
                z-index: 40;
                height: 100%;
            }
            .overlay {
                position: fixed;
                top: 0;
                left: 0;
                right: 0;
                bottom: 0;
                background-color: rgba(0, 0, 0, 0.5);
                z-index: 30;
            }
        }
        /* Para o contador de caracteres */
        .observation-container {
            position: relative;
        }
        .char-count {
            position: absolute;
            right: 8px;
            top: 50%;
            transform: translateY(-50%);
            font-size: 0.75rem;
            color: #8d6e63;
        }
        
        /* Estilos para pastas */
        .folder {
            position: relative;
            cursor: pointer;
        }
        .folder-content {
            margin-left: 15px;
            border-left: 1px dashed rgba(255, 255, 255, 0.2);
            padding-left: 10px;
            display: none;
        }
        .folder.open > .folder-content {
            display: block;
        }
        .folder-header {
            display: flex;
            align-items: center;
            padding: 8px 10px;
            border-radius: 4px;
            transition: background-color 0.2s;
        }
        .folder-header:hover {
            background-color: rgba(255, 255, 255, 0.1);
        }
        .folder-actions {
            display: none;
            margin-left: auto;
        }
        .folder-header:hover .folder-actions {
            display: flex;
        }
        .checklist-in-folder {
            margin-left: 5px;
        }
        /* Menu de contexto */
        .context-menu {
            position: absolute;
            background: white;
            border: 1px solid #d7ccc8;
            border-radius: 4px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            z-index: 100;
            min-width: 150px;
        }
        .context-menu-item {
            padding: 8px 15px;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        .context-menu-item:hover {
            background-color: #f1e7d9;
        }
        /* Drag and Drop */
        .drag-over {
            background-color: rgba(255, 255, 255, 0.2) !important;
        }
        .dragging {
            opacity: 0.5;
        }
        /* Badge para contador */
        .folder-badge {
            background-color: rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            padding: 0 6px;
            font-size: 0.7rem;
            margin-left: 5px;
        }
        
        /* Estilos para numeração */
        .item-number {
            background: #3b82f6;
            color: white;
            border-radius: 50%;
            width: 24px;
            height: 24px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.75rem;
            font-weight: bold;
            flex-shrink: 0;
        }
        
        /* Estilos para quebra de texto */
        .item-text {
            word-wrap: break-word;
            word-break: break-word;
            white-space: normal;
            overflow-wrap: break-word;
            hyphens: auto;
        }
        
        /* Responsividade para layout dos itens */
        @media (max-width: 640px) {
            .item-layout {
                display: block !important;
            }
            .item-equipment {
                margin-bottom: 0.5rem;
            }
        }
        
        /* Indicador de backup online */
        .backup-indicator {
            color: #10b981;
            font-size: 0.875rem;
            margin-left: 0.5rem;
            opacity: 0;
            transition: opacity 0.3s ease;
        }
        .backup-indicator.show {
            opacity: 1;
        }
        
        /* Loading spinner */
        .spinner {
            border: 2px solid #f3f3f3;
            border-top: 2px solid #10b981;
            border-radius: 50%;
            width: 16px;
            height: 16px;
            animation: spin 1s linear infinite;
            display: inline-block;
            margin-left: 0.5rem;
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="flex min-h-screen">
        <!-- Sidebar para dispositivos móveis -->
        <div id="overlay" class="overlay hidden md:hidden" onclick="toggleSidebar()"></div>
        
        <!-- Sidebar -->
        <div id="sidebar" class="sidebar fixed md:relative md:block text-white z-40 h-full overflow-y-auto">
            <div class="p-4 flex justify-between items-center border-b border-white border-opacity-20">
                <h1 class="text-xl font-bold">Pastas & Checklists</h1>
                <button class="md:hidden text-white" onclick="toggleSidebar()">
                    <i class="fas fa-times"></i>
                </button>
            </div>

            <!-- Botões de ação para pastas -->
            <div class="p-2 border-b border-white border-opacity-10">
                <button id="createFolderBtn" class="w-full py-2 px-4 mb-2 bg-white text-gray-800 rounded focus:outline-none hover:bg-gray-200 transition">
                    <i class="fas fa-folder-plus mr-2"></i> Nova Pasta
                </button>
                <button id="moveAllBtn" class="w-full py-2 px-4 mb-2 bg-white text-gray-800 rounded focus:outline-none hover:bg-gray-200 transition">
                    <i class="fas fa-exchange-alt mr-2"></i> Mover Para...
                </button>
                <button id="backupAllOnlineBtn" class="w-full py-2 px-4 bg-green-500 text-white rounded focus:outline-none hover:bg-green-600 transition">
                    <i class="fas fa-cloud-upload-alt mr-2"></i> Backup Online Completo
                </button>
            </div>
            
            <!-- Estrutura de pastas -->
            <div id="folderStructure" class="p-2">
                <!-- Pasta padrão (raiz) -->
                <div id="rootFolder" class="folder open" data-folder-id="root">
                    <div class="folder-header">
                        <i class="fas fa-folder-open mr-2"></i>
                        <span class="folder-name">Principal</span>
                        <span class="folder-badge" id="rootCount">0</span>
                    </div>
                    <div class="folder-content" id="rootContent">
                        <!-- Checklists da pasta raiz serão adicionados aqui -->
                        <div class="text-center text-white p-2 text-sm opacity-70">Nenhum checklist.</div>
                    </div>
                </div>
                <!-- Outras pastas serão adicionadas aqui -->
            </div>
            
            <!-- Botão para exportar todos os checklists -->
            <div class="p-3 border-t border-white border-opacity-20 mt-auto">
                <button id="exportAllBtn" class="w-full py-2 px-4 bg-white text-gray-800 rounded focus:outline-none hover:bg-gray-200 transition">
                    <i class="fas fa-file-export mr-2"></i> Exportar Todos
                </button>
            </div>
        </div>
        
        <!-- Conteúdo principal -->
        <div id="main-content" class="main-content flex-1 p-4">
            <!-- Botão de alternância para dispositivos móveis -->
            <div class="md:hidden mb-4">
                <button class="p-2 bg-white rounded shadow" onclick="toggleSidebar()">
                    <i class="fas fa-bars text-gray-800"></i>
                </button>
            </div>
            
            <!-- Cabeçalho -->
            <div class="card p-4 mb-4 text-center">
                <h1 class="text-2xl font-bold text-gray-800">Checklist Offline</h1>
                <p class="text-gray-600">Sistema que funciona sem internet 
                    <span class="inline-block bg-gray-700 text-white px-2 py-0.5 rounded-full text-xs">Offline</span>
                </p>
            </div>
            
            <!-- Opções de criar ou importar -->
            <div class="card p-4 mb-4">
                <h2 class="text-xl font-bold mb-4 text-gray-800 border-b pb-2">Criar ou Importar</h2>
                <div id="folderSelectContainer" class="bg-white bg-opacity-50 p-3 rounded mb-3">
                    <label for="folderSelect" class="block mb-2 text-gray-700">Salvar na pasta:</label>
                    <select id="folderSelect" class="w-full p-2 border rounded">
                        <option value="root">Principal</option>
                        <!-- Outras pastas serão adicionadas aqui -->
                    </select>
                </div>
                <button id="newBtn" class="w-full py-2 px-4 bg-brown-500 btn-primary text-white rounded mb-3 font-bold focus:outline-none">
                    Criar Novo Checklist
                </button>
                
                <div class="bg-white bg-opacity-50 p-3 rounded mb-3">
                    <label for="importEncoding" class="block mb-2 text-gray-700">Codificação para importar:</label>
                    <select id="importEncoding" class="w-full p-2 border rounded">
                        <option value="UTF-8">UTF-8</option>
                        <option value="ISO-8859-1" selected>ISO-8859-1 (Latin1)</option>
                        <option value="windows-1252">Windows-1252</option>
                    </select>
                </div>
                
                <button id="importBtn" class="w-full py-2 px-4 btn-white rounded mb-3 focus:outline-none">
                    <i class="fas fa-file-import mr-2"></i> Importar Checklist (.CSV)
                </button>
                <button id="exportModelBtn" class="w-full py-2 px-4 btn-white rounded focus:outline-none">
                    <i class="fas fa-download mr-2"></i> Baixar Modelo Excel
                </button>
                <input type="file" id="fileInput" accept=".csv,.xlsx,.xls" class="hidden">
            </div>
            
            <!-- Formulário de criação de checklist -->
            <div id="createForm" class="card p-4 mb-4 hidden">
                <h2 class="text-xl font-bold mb-4 text-gray-800 border-b pb-2">Criar Novo Checklist</h2>
                <input type="text" id="checklistName" placeholder="Nome do Checklist" class="w-full p-2 border rounded mb-4">
                <input type="text" id="checklistMatricula" placeholder="Matrícula do Responsável" class="w-full p-2 border rounded mb-4">
                
                <div id="formItems" class="mb-4">
                    <div class="checklist-item p-3 rounded flex flex-wrap mb-2">
                        <div class="grid grid-cols-3 gap-3 w-full">
                            <div class="col-span-1">
                                <input type="text" placeholder="Equipamento" class="w-full p-2 border rounded">
                            </div>
                            <div class="col-span-2">
                                <input type="text" placeholder="Item de Verificação" class="w-full p-2 border rounded">
                            </div>
                        </div>
                        <button class="ml-2 px-3 py-1 bg-red-500 text-white rounded text-sm" onclick="this.parentNode.remove()">✕</button>
                    </div>
                </div>
                
                <button id="addItemBtn" class="w-full py-2 px-4 btn-white rounded mb-4 focus:outline-none">
                    <i class="fas fa-plus mr-2"></i> Adicionar Item
                </button>
                
                <div class="flex gap-3">
                    <button id="cancelBtn" class="flex-1 py-2 px-4 btn-white rounded focus:outline-none">
                        Cancelar
                    </button>
                    <button id="saveNewBtn" class="flex-1 py-2 px-4 btn-primary text-white rounded font-bold focus:outline-none">
                        Criar Checklist
                    </button>
                </div>
            </div>
            
            <!-- Visualização do checklist -->
            <div id="checklistView" class="card p-4 mb-4 hidden">
                <h2 id="checklistTitle" class="text-xl font-bold mb-1 text-gray-800">Checklist</h2>
                <div class="flex flex-col sm:flex-row sm:items-center mb-4 gap-2">
                    <div class="flex items-center">
                        <span class="text-gray-600 mr-2">Data:</span>
                        <span id="checklistDateDisplay" class="text-gray-600">-</span>
                    </div>
                    <div class="flex items-center">
                        <span class="text-gray-600 mr-2">Matrícula:</span>
                        <input type="text" id="checklistMatriculaField" placeholder="Digite a matrícula" 
                               class="p-1 border rounded text-sm w-32" maxlength="20">
                    </div>
                </div>
                
                <div class="progress-bar mb-4">
                    <div id="progressBar" class="progress-value" style="width: 0%"></div>
                </div>
                
                <div class="grid grid-cols-5 gap-2 mb-4">
                    <div class="bg-white bg-opacity-60 p-2 rounded text-center">
                        <div id="totalCount" class="font-bold text-gray-800 text-lg">0</div>
                        <div class="text-sm text-gray-600">Total</div>
                    </div>
                    <div class="bg-white bg-opacity-60 p-2 rounded text-center">
                        <div id="okCount" class="font-bold text-gray-800 text-lg">0</div>
                        <div class="text-sm text-gray-600">OK</div>
                    </div>
                    <div class="bg-white bg-opacity-60 p-2 rounded text-center">
                        <div id="ncCount" class="font-bold text-gray-800 text-lg">0</div>
                        <div class="text-sm text-gray-600">NC</div>
                    </div>
                    <div class="bg-white bg-opacity-60 p-2 rounded text-center">
                        <div id="naCount" class="font-bold text-gray-800 text-lg">0</div>
                        <div class="text-sm text-gray-600">N/A</div>
                    </div>
                    <div class="bg-white bg-opacity-60 p-2 rounded text-center">
                        <div id="percentCount" class="font-bold text-gray-800 text-lg">0%</div>
                        <div class="text-sm text-gray-600">Concluído</div>
                    </div>
                </div>
                
                <div id="checklistItems" class="mb-4">
                    <!-- Itens serão adicionados dinamicamente -->
                </div>
                
                <div class="flex flex-col sm:flex-row gap-2 mb-3">
                    <div class="flex-grow">
                        <input type="text" id="saveName" placeholder="Nome para salvar" class="w-full p-2 border rounded">
                    </div>
                    <div class="flex gap-2">
                        <select id="saveToFolder" class="p-2 border rounded">
                            <option value="root">Pasta: Principal</option>
                            <!-- Outras pastas serão adicionadas aqui -->
                        </select>
                        <button id="saveLocalBtn" class="py-2 px-4 btn-primary text-white rounded font-bold focus:outline-none">
                            <i class="fas fa-save mr-1"></i> Salvar
                        </button>
                        <button id="saveOnlineBtn" class="py-2 px-4 btn-online text-white rounded font-bold focus:outline-none">
                            <i class="fas fa-cloud-upload-alt mr-1"></i> Salvar Online
                        </button>
                    </div>
                </div>
                
                <button id="exportBtn" class="w-full py-2 px-4 btn-primary text-white rounded font-bold focus:outline-none">
                    <i class="fas fa-file-export mr-2"></i> Exportar para Excel
                </button>
            </div>
            
            <!-- Formulário de criação de pasta -->
            <div id="folderForm" class="card p-4 mb-4 hidden">
                <h2 class="text-xl font-bold mb-4 text-gray-800 border-b pb-2">Criar Nova Pasta</h2>
                <input type="text" id="folderName" placeholder="Nome da Pasta" class="w-full p-2 border rounded mb-4">
                
                <div class="flex gap-3">
                    <button id="cancelFolderBtn" class="flex-1 py-2 px-4 btn-white rounded focus:outline-none">
                        Cancelar
                    </button>
                    <button id="saveFolderBtn" class="flex-1 py-2 px-4 btn-primary text-white rounded font-bold focus:outline-none">
                        Criar Pasta
                    </button>
                </div>
            </div>

            <!-- Formulário para mover checklists -->
            <div id="moveForm" class="card p-4 mb-4 hidden">
                <h2 class="text-xl font-bold mb-4 text-gray-800 border-b pb-2">Mover Checklists</h2>
                
                <div class="mb-4">
                    <label class="block mb-2 text-gray-700">Origem:</label>
                    <select id="moveSourceFolder" class="w-full p-2 border rounded mb-2">
                        <option value="root">Principal</option>
                        <!-- Outras pastas serão adicionadas aqui -->
                    </select>
                    <div id="moveChecklistsContainer" class="bg-white bg-opacity-50 p-2 rounded max-h-40 overflow-y-auto">
                        <!-- Checklists para mover serão adicionados aqui -->
                        <div class="text-center p-2">Selecione uma pasta de origem.</div>
                    </div>
                </div>
                
                <div class="mb-4">
                    <label class="block mb-2 text-gray-700">Destino:</label>
                    <select id="moveTargetFolder" class="w-full p-2 border rounded">
                        <option value="root">Principal</option>
                        <!-- Outras pastas serão adicionadas aqui -->
                    </select>
                </div>
                
                <div class="flex gap-3">
                    <button id="cancelMoveBtn" class="flex-1 py-2 px-4 btn-white rounded focus:outline-none">
                        Cancelar
                    </button>
                    <button id="confirmMoveBtn" class="flex-1 py-2 px-4 btn-primary text-white rounded font-bold focus:outline-none">
                        Mover Selecionados
                    </button>
                </div>
            </div>
        </div>
    </div>
    
    <!-- Menu de Contexto -->
    <div id="contextMenu" class="context-menu hidden">
        <div class="context-menu-item rename-folder">
            <i class="fas fa-edit mr-2"></i> Renomear
        </div>
        <div class="context-menu-item delete-folder">
            <i class="fas fa-trash mr-2"></i> Excluir
        </div>
    </div>
    
    <script>
        // Configuração do GitHub
        const GITHUB_CONFIG = {
            username: 'LCEngel',
            repo: 'checklists',
            token: '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'
        };

        // Modelos de checklist padrão
        const checklistModels = {
            'inspecao': {
                name: 'Inspeção de Equipamentos',
                date: new Date().toLocaleDateString('pt-BR'),
                items: [
                    ['Compressor', 'Verificar nível de óleo', '', '', '', ''],
                    ['Compressor', 'Verificar ruídos anormais', '', '', '', ''],
                    ['Empilhadeira', 'Verificar freios', '', '', '', ''],
                    ['Empilhadeira', 'Verificar nível de bateria', '', '', '', ''],
                    ['Gerador', 'Verificar nível de combustível', '', '', '', ''],
                    ['Gerador', 'Testar funcionamento', '', '', '', ''],
                    ['Máquina de Solda', 'Verificar cabos e conexões', '', '', '', ''],
                    ['Máquina de Solda', 'Testar operação', '', '', '', '']
                ]
            },
            'seguranca': {
                name: 'Checklist de Segurança',
                date: new Date().toLocaleDateString('pt-BR'),
                items: [
                    ['Escritório', 'Verificar extintores', '', '', '', ''],
                    ['Escritório', 'Verificar saídas de emergência', '', '', '', ''],
                    ['Depósito', 'Verificar EPIs disponíveis', '', '', '', ''],
                    ['Depósito', 'Verificar organização', '', '', '', ''],
                    ['Linha de Produção', 'Verificar proteções de máquinas', '', '', '', ''],
                    ['Linha de Produção', 'Verificar sinalizações', '', '', '', ''],
                    ['Área Externa', 'Verificar iluminação', '', '', '', ''],
                    ['Área Externa', 'Verificar controle de acesso', '', '', '', '']
                ]
            }
        };

        // Variáveis globais
        let currentChecklist = [];
        let checklistTitle = '';
        let checklistDate = '';
        let checklistMatricula = '';
        let currentChecklistId = null;
        let currentFolderId = 'root'; // Pasta atual para salvar

        // Cache dos elementos
        const createForm = document.getElementById('createForm');
        const checklistView = document.getElementById('checklistView');
        const formItems = document.getElementById('formItems');
        const checklistItems = document.getElementById('checklistItems');
        const sidebar = document.getElementById('sidebar');
        const overlay = document.getElementById('overlay');
        const folderForm = document.getElementById('folderForm');
        const moveForm = document.getElementById('moveForm');
        const contextMenu = document.getElementById('contextMenu');
        
        // Fechar menu de contexto ao clicar fora
        document.addEventListener('click', () => {
            contextMenu.classList.add('hidden');
        });
        
        // Impedir propagação de cliques dentro do menu
        contextMenu.addEventListener('click', (e) => {
            e.stopPropagation();
        });

        // Função para alternar a visibilidade da sidebar em dispositivos móveis
        function toggleSidebar() {
            sidebar.classList.toggle('sidebar-hidden');
            overlay.classList.toggle('hidden');
        }

        // Em telas menores, esconde a sidebar inicialmente
        if (window.innerWidth < 768) {
            sidebar.classList.add('sidebar-hidden');
        }
        
        // Função para alternar pasta aberta/fechada
        function toggleFolder(folderId) {
            const folder = document.querySelector(`.folder[data-folder-id="${folderId}"]`);
            if (folder) {
                folder.classList.toggle('open');
                const icon = folder.querySelector('.fa-folder, .fa-folder-open');
                if (icon) {
                    icon.classList.toggle('fa-folder');
                    icon.classList.toggle('fa-folder-open');
                }
            }
        }
        
        // Função para mostrar menu de contexto
        function showContextMenu(e, folderId) {
            e.preventDefault();
            e.stopPropagation();
            
            // Não mostrar menu para pasta raiz
            if (folderId === 'root') return;
            
            const folder = document.querySelector(`.folder[data-folder-id="${folderId}"]`);
            contextMenu.style.top = `${e.clientY}px`;
            contextMenu.style.left = `${e.clientX}px`;
            contextMenu.classList.remove('hidden');
            
            // Configurar ações do menu
            const renameAction = contextMenu.querySelector('.rename-folder');
            const deleteAction = contextMenu.querySelector('.delete-folder');
            
            renameAction.onclick = () => {
                const folderName = folder.querySelector('.folder-name').textContent;
                const newName = prompt('Renomear pasta:', folderName);
                if (newName && newName.trim()) {
                    renameFolder(folderId, newName.trim());
                }
                contextMenu.classList.add('hidden');
            };
            
            deleteAction.onclick = () => {
                if (confirm(`Tem certeza que deseja excluir a pasta "${folder.querySelector('.folder-name').textContent}"?`)) {
                    deleteFolder(folderId);
                }
                contextMenu.classList.add('hidden');
            };
        }
        
        // Criar pasta
        function createFolder(name) {
            const folders = JSON.parse(localStorage.getItem('folders') || '[]');
            const newFolder = {
                id: 'folder_' + Date.now(),
                name: name,
                createdAt: new Date().toISOString()
            };
            
            folders.push(newFolder);
            localStorage.setItem('folders', JSON.stringify(folders));
            renderFolderStructure();
            updateFolderSelects();
            return newFolder.id;
        }
        
        // Renomear pasta
        function renameFolder(folderId, newName) {
            const folders = JSON.parse(localStorage.getItem('folders') || '[]');
            const folderIndex = folders.findIndex(f => f.id === folderId);
            
            if (folderIndex !== -1) {
                folders[folderIndex].name = newName;
                localStorage.setItem('folders', JSON.stringify(folders));
                renderFolderStructure();
                updateFolderSelects();
            }
        }
        
        // Excluir pasta
        function deleteFolder(folderId) {
            // Mover checklists para pasta raiz antes de excluir
            moveChecklistsToFolder(folderId, 'root');
            
            // Excluir pasta
            const folders = JSON.parse(localStorage.getItem('folders') || '[]');
            const updatedFolders = folders.filter(f => f.id !== folderId);
            localStorage.setItem('folders', JSON.stringify(updatedFolders));
            
            // Atualizar interface
            renderFolderStructure();
            updateFolderSelects();
        }
        
        // Mover checklists de uma pasta para outra
        function moveChecklistsToFolder(sourceFolderId, targetFolderId) {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            
            // Atualizar pasta dos checklists
            savedChecklists.forEach(checklist => {
                if (checklist.folderId === sourceFolderId) {
                    checklist.folderId = targetFolderId;
                }
            });
            
            localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
            renderFolderStructure();
        }
        
        // Mover checklists selecionados
        function moveSelectedChecklists(selectedIds, targetFolderId) {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            
            // Atualizar pasta dos checklists selecionados
            savedChecklists.forEach(checklist => {
                if (selectedIds.includes(checklist.id)) {
                    checklist.folderId = targetFolderId;
                }
            });
            
            localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
            renderFolderStructure();
        }
        
        // Atualizar os selects de pasta
        function updateFolderSelects() {
            const folders = JSON.parse(localStorage.getItem('folders') || '[]');
            const selects = [
                document.getElementById('folderSelect'),
                document.getElementById('saveToFolder'),
                document.getElementById('moveSourceFolder'),
                document.getElementById('moveTargetFolder')
            ];
            
            selects.forEach(select => {
                if (!select) return;
                
                // Manter valor atual
                const currentValue = select.value;
                
                // Limpar todas as opções exceto "Principal"
                while (select.options.length > 1) {
                    select.remove(1);
                }
                
                // Adicionar pastas
                folders.forEach(folder => {
                    const option = document.createElement('option');
                    option.value = folder.id;
                    option.textContent = select.id === 'saveToFolder' ? 
                        `Pasta: ${folder.name}` : folder.name;
                    select.appendChild(option);
                });
                
                // Restaurar valor se ainda existir
                if (folders.find(f => f.id === currentValue) || currentValue === 'root') {
                    select.value = currentValue;
                }
            });
        }
        
        // Carregar checklists de uma pasta específica no formulário de movimentação
        function loadChecklistsForMove(folderId) {
            const container = document.getElementById('moveChecklistsContainer');
            container.innerHTML = '';
            
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            const checklistsInFolder = savedChecklists.filter(c => c.folderId === folderId);
            
            if (checklistsInFolder.length === 0) {
                container.innerHTML = '<div class="text-center p-2">Nenhum checklist nesta pasta.</div>';
                return;
            }
            
            checklistsInFolder.forEach(checklist => {
                const item = document.createElement('div');
                item.className = 'flex items-center py-1';
                item.innerHTML = `
                    <input type="checkbox" id="move_${checklist.id}" value="${checklist.id}" class="mr-2">
                    <label for="move_${checklist.id}" class="cursor-pointer">${checklist.name}</label>`;
                container.appendChild(item);
            });
            
            const selectAllContainer = document.createElement('div');
            selectAllContainer.className = 'border-t mt-2 pt-2';
            selectAllContainer.innerHTML = `
                <div class="flex items-center">
                    <input type="checkbox" id="selectAllChecklists" class="mr-2">
                    <label for="selectAllChecklists" class="cursor-pointer">Selecionar todos</label>
                </div>`;
            container.appendChild(selectAllContainer);
            
            document.getElementById('selectAllChecklists').addEventListener('change', (e) => {
                const checkboxes = container.querySelectorAll('input[type="checkbox"]');
                checkboxes.forEach(checkbox => {
                    if (checkbox.id !== 'selectAllChecklists') {
                        checkbox.checked = e.target.checked;
                    }
                });
            });
        }

        // Renderizar estrutura de pastas
        function renderFolderStructure() {
            const folderStructure = document.getElementById('folderStructure');
            const rootFolder = document.getElementById('rootFolder');
            const rootContent = document.getElementById('rootContent');
            
            // Manter a pasta raiz
            folderStructure.innerHTML = '';
            folderStructure.appendChild(rootFolder);
            
            // Limpar conteúdo da pasta raiz
            rootContent.innerHTML = '';
            
            // Carregar pastas e checklists
            const folders = JSON.parse(localStorage.getItem('folders') || '[]');
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            
            // Checklists na pasta raiz
            const rootChecklists = savedChecklists.filter(c => !c.folderId || c.folderId === 'root');
            document.getElementById('rootCount').textContent = rootChecklists.length;
            
            if (rootChecklists.length === 0) {
                rootContent.innerHTML = '<div class="text-center text-white p-2 text-sm opacity-70">Nenhum checklist.</div>';
            } else {
                rootChecklists.forEach(checklist => {
                    const checklistEl = createChecklistElement(checklist);
                    rootContent.appendChild(checklistEl);
                });
            }
            
            // Criar pastas e adicionar checklists
            folders.forEach(folder => {
                const folderEl = document.createElement('div');
                folderEl.className = 'folder';
                folderEl.dataset.folderId = folder.id;
                
                // Checklists nesta pasta
                const folderChecklists = savedChecklists.filter(c => c.folderId === folder.id);
                
                folderEl.innerHTML = `
                    <div class="folder-header" onclick="toggleFolder('${folder.id}')" oncontextmenu="showContextMenu(event, '${folder.id}')">
                        <i class="fas fa-folder mr-2"></i>
                        <span class="folder-name">${folder.name}</span>
                        <span class="folder-badge">${folderChecklists.length}</span>
                        <div class="folder-actions">
                            <button class="text-white opacity-70 hover:opacity-100 p-1" onclick="event.stopPropagation(); showContextMenu(event, '${folder.id}')">
                                <i class="fas fa-ellipsis-v"></i>
                            </button>
                        </div>
                    </div>
                    <div class="folder-content"></div>`;
                
                folderStructure.appendChild(folderEl);
                
                // Adicionar checklists à pasta
                const folderContent = folderEl.querySelector('.folder-content');
                
                if (folderChecklists.length === 0) {
                    folderContent.innerHTML = '<div class="text-center text-white p-2 text-sm opacity-70">Pasta vazia.</div>';
                } else {
                    folderChecklists.forEach(checklist => {
                        const checklistEl = createChecklistElement(checklist);
                        folderContent.appendChild(checklistEl);
                    });
                }
            });
        }
        
        // Criar elemento HTML para um checklist
        function createChecklistElement(checklist) {
            const item = document.createElement('div');
            item.className = 'sidebar-item checklist-in-folder p-2 hover:bg-opacity-10 hover:bg-white cursor-pointer';
            item.setAttribute('draggable', 'true');
            item.dataset.checklistId = checklist.id;
            
            // Calcular estatísticas do checklist
            const items = checklist.data.items;
            const total = items.length;
            const completed = items.filter(item => item.status).length;
            const percent = total > 0 ? Math.round((completed / total) * 100) : 0;
            
            // Verificar se tem backup online
            const backupIndicator = checklist.backupOnline ? 
                '<i class="fas fa-thumbs-up backup-indicator show" title="Salvo online"></i>' : '';
            
            item.innerHTML = `
                <div class="font-medium mb-1">${checklist.name}</div>
                <div class="flex justify-between text-xs text-gray-300">
                    <span>${checklist.data.saveDateTime || ''}</span>
                    <span>${percent}% concluído</span>
                </div>
                <div class="flex mt-2 items-center">
                    <button class="px-2 py-1 text-xs bg-white text-gray-800 rounded mr-1 hover:bg-gray-200" 
                        onclick="loadChecklist('${checklist.id}')">Abrir</button>
                    <button class="px-2 py-1 text-xs bg-red-500 text-white rounded hover:bg-red-600" 
                        onclick="deleteChecklist('${checklist.id}')">Remover</button>
                    ${backupIndicator}
                </div>`;
                
            // Adicionar eventos de drag and drop
            item.addEventListener('dragstart', (e) => {
                e.dataTransfer.setData('text/plain', checklist.id);
                item.classList.add('dragging');
            });
            
            item.addEventListener('dragend', () => {
                item.classList.remove('dragging');
                document.querySelectorAll('.drag-over').forEach(el => {
                    el.classList.remove('drag-over');
                });
            });
            
            return item;
        }
        
        // Configurar eventos de drag and drop para pastas
        function setupDragAndDropForFolders() {
            const folders = document.querySelectorAll('.folder');
            
            folders.forEach(folder => {
                const folderId = folder.dataset.folderId;
                const folderHeader = folder.querySelector('.folder-header');
                
                folderHeader.addEventListener('dragover', (e) => {
                    e.preventDefault();
                    folderHeader.classList.add('drag-over');
                });
                
                folderHeader.addEventListener('dragleave', () => {
                    folderHeader.classList.remove('drag-over');
                });
                
                folderHeader.addEventListener('drop', (e) => {
                    e.preventDefault();
                    folderHeader.classList.remove('drag-over');
                    const checklistId = e.dataTransfer.getData('text/plain');
                    moveChecklistToFolder(checklistId, folderId);
                });
            });
        }
        
        // Mover um checklist para uma pasta
        function moveChecklistToFolder(checklistId, folderId) {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            const checklistIndex = savedChecklists.findIndex(c => c.id === checklistId);
            
            if (checklistIndex !== -1) {
                savedChecklists[checklistIndex].folderId = folderId;
                localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
                renderFolderStructure();
                setupDragAndDropForFolders();
            }
        }
        
        // Função para salvar checklist online no GitHub
        async function saveChecklistOnline(checklistData) {
            try {
                const now = new Date();
                const year = now.getFullYear();
                const month = String(now.getMonth() + 1).padStart(2, '0');
                const monthName = now.toLocaleDateString('pt-BR', { month: 'long' });
                const timestamp = now.toISOString().replace(/[:.]/g, '-').slice(0, -5);
                const matricula = checklistData.matricula || 'SemMatricula';
                
                const filename = `${year}/${month}-${monthName}/checklist_${matricula}_${timestamp}.json`;
                const content = btoa(JSON.stringify(checklistData, null, 2));
                
                const response = await fetch(`https://api.github.com/repos/${GITHUB_CONFIG.username}/${GITHUB_CONFIG.repo}/contents/${filename}`, {
                    method: 'PUT',
                    headers: {
                        'Authorization': `token ${GITHUB_CONFIG.token}`,
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        message: `Checklist: ${checklistData.title} - ${checklistData.matricula}`,
                        content: content
                    })
                });
                
                if (response.ok) {
                    return true;
                } else {
                    console.error('Erro no GitHub:', await response.text());
                    return false;
                }
            } catch (error) {
                console.error('Erro ao salvar online:', error);
                return false;
            }
        }
        
        // Marcar checklist como salvo online
        function markChecklistAsBackedUp(checklistId) {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            const checklistIndex = savedChecklists.findIndex(c => c.id === checklistId);
            
            if (checklistIndex !== -1) {
                savedChecklists[checklistIndex].backupOnline = true;
                localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
                renderFolderStructure();
                setupDragAndDropForFolders();
            }
        }

        // Event listeners
        document.getElementById('newBtn').addEventListener('click', () => {
            createForm.classList.remove('hidden');
            checklistView.classList.add('hidden');
            folderForm.classList.add('hidden');
            moveForm.classList.add('hidden');
            if (window.innerWidth < 768) toggleSidebar();
        });

        document.getElementById('importBtn').addEventListener('click', () => {
            document.getElementById('fileInput').click();
            if (window.innerWidth < 768) toggleSidebar();
        });

        document.getElementById('fileInput').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;

            const encoding = document.getElementById('importEncoding').value;
            
            const reader = new FileReader();
            reader.onload = function(evt) {
                try {
                    const text = evt.target.result;
                    
                    // Detectar separador
                    let separator;
                    if (text.indexOf('\t') !== -1) separator = '\t';
                    else if (text.indexOf(';') !== -1) separator = ';';
                    else separator = ',';
                    
                    // Dividir em linhas, tratando diferentes terminações
                    const lines = text.split(/\r\n|\n|\r/).filter(line => line.trim());
                    if (lines.length < 2) {
                        alert('Arquivo inválido. São necessárias pelo menos duas linhas.');
                        return;
                    }

                    // Processar primeira linha (título, data, matrícula)
                    const titleLine = lines[0];
                    const titleParts = titleLine.split(separator);
                    checklistTitle = titleParts[0].replace(/^"/, '').replace(/"$/, '').trim();
                    checklistDate = titleParts.length > 1 ? 
                        titleParts[1].replace(/^"/, '').replace(/"$/, '').trim() : 
                        new Date().toLocaleString('pt-BR');
                    checklistMatricula = titleParts.length > 2 ? 
                        titleParts[2].replace(/^"/, '').replace(/"$/, '').trim() : '';

                    // Procurar linha de cabeçalho
                    let headerLine = 1;
                    const headerKeywords = ['EQUIPAMENTO', 'ITEM DE VERIFICAÇÃO', 'ITEM DE VERIFICACAO', 'NÚMERO'];
                    
                    if (lines[headerLine]) {
                        const upperLine = lines[headerLine].toUpperCase();
                        if (headerKeywords.some(keyword => upperLine.includes(keyword))) {
                            headerLine = 2;  // Pular para a próxima linha após cabeçalho
                        }
                    }

                    // Processar itens
                    currentChecklist = [];
                    for (let i = headerLine; i < lines.length; i++) {
                        const line = lines[i];
                        if (!line.trim() || line.toUpperCase().includes('ESTATÍSTICAS') || line.toUpperCase().includes('ESTATISTICAS')) {
                            continue;
                        }

                        const parts = line.split(separator);
                        if (parts.length >= 3) {
                            // Detectar se tem numeração (primeiro campo é número)
                            let startIndex = 0;
                            if (!isNaN(parts[0].replace(/^"/, '').replace(/"$/, '').trim())) {
                                startIndex = 1; // Pular o número
                            }
                            
                            const equipment = parts[startIndex].replace(/^"/, '').replace(/"$/, '').trim();
                            const text = parts[startIndex + 1].replace(/^"/, '').replace(/"$/, '').trim();
                            const status = (parts[startIndex + 2] || '').replace(/^"/, '').replace(/"$/, '').trim();
                            const observation = (parts[startIndex + 3] || '').replace(/^"/, '').replace(/"$/, '').trim();
                            const sapNumber = (parts[startIndex + 4] || '').replace(/^"/, '').replace(/"$/, '').trim();
                            const sapStatus = (parts[startIndex + 5] || '').replace(/^"/, '').replace(/"$/, '').trim();
                            
                            currentChecklist.push({
                                equipment,
                                text,
                                status,
                                observation,
                                sapNumber,
                                sapStatus
                            });
                        }
                    }

                    renderChecklist();
                    currentFolderId = document.getElementById('folderSelect').value;
                } catch (error) {
                    alert('Erro ao processar arquivo. Verifique a codificação e formato.');
                    console.error('Erro ao processar arquivo:', error);
                }
            };

            reader.onerror = function() {
                alert('Ocorreu um erro ao ler o arquivo.');
            };

            reader.readAsText(file, encoding);
            e.target.value = '';
        });

        document.getElementById('exportModelBtn').addEventListener('click', () => {
            const modelType = prompt('Escolha o tipo de modelo:\n1. Inspeção de Equipamentos\n2. Checklist de Segurança');
            
            let model = modelType === '2' ? checklistModels.seguranca : checklistModels.inspecao;
            
            const rows = [];
            rows.push(['NOME DO CHECKLIST', 'DATA E HORA DO CHECKLIST', 'MATRÍCULA RESPONSÁVEL']);
            rows.push(['NÚMERO', 'EQUIPAMENTO', 'ITEM DE VERIFICAÇÃO', 'STATUS', 'COMENTÁRIO', 'NÚMERO DA NOTA SAP', 'STATUS DA NOTA']);
            
            // Inserir valores correspondentes
            rows.push([model.name, new Date().toLocaleString('pt-BR'), '']);
            
            model.items.forEach((item, index) => {
                rows.push([index + 1, item[0], item[1], item[2] || '', item[3] || '', '', '']);
            });
            
            let csvContent = '';
            rows.forEach(row => {
                csvContent += row.map(cell => `"${cell}"`).join(';') + '\n';
            });
            
            const BOM = new Uint8Array([0xEF, 0xBB, 0xBF]);
            const blob = new Blob([BOM, csvContent], { type: 'text/csv;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = `modelo_checklist_${new Date().toLocaleDateString('pt-BR').replace(/\//g, '-')}.csv`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            setTimeout(() => {
                alert('Modelo CSV baixado com sucesso!');
            }, 500);
        });

        document.getElementById('addItemBtn').addEventListener('click', () => {
            const item = document.createElement('div');
            item.className = 'checklist-item p-3 rounded flex flex-wrap mb-2';
            item.innerHTML = `
                <div class="grid grid-cols-3 gap-3 w-full">
                    <div class="col-span-1">
                        <input type="text" placeholder="Equipamento" class="w-full p-2 border rounded">
                    </div>
                    <div class="col-span-2">
                        <input type="text" placeholder="Item de Verificação" class="w-full p-2 border rounded">
                    </div>
                </div>
                <button class="ml-2 px-3 py-1 bg-red-500 text-white rounded text-sm" onclick="this.parentNode.remove()">✕</button>`;
            formItems.appendChild(item);
        });

        document.getElementById('cancelBtn').addEventListener('click', () => {
            createForm.classList.add('hidden');
            resetCreateForm();
        });

        document.getElementById('saveNewBtn').addEventListener('click', () => {
            const title = document.getElementById('checklistName').value.trim();
            const matricula = document.getElementById('checklistMatricula').value.trim();
            
            if (!title) {
                alert('Por favor, informe um nome para o checklist.');
                return;
            }
            
            if (!matricula) {
                alert('Por favor, informe a matrícula do responsável.');
                return;
            }
            
            const items = formItems.querySelectorAll('.checklist-item');
            if (items.length === 0) {
                alert('Adicione pelo menos um item ao checklist.');
                return;
            }
            
            currentChecklist = [];
            checklistTitle = title;
            checklistDate = new Date().toLocaleString('pt-BR');
            checklistMatricula = matricula;
            currentFolderId = document.getElementById('folderSelect').value;
            
            items.forEach(item => {
                const inputs = item.querySelectorAll('input[type="text"]');
                if (inputs.length >= 2 && inputs[1].value.trim()) {
                    currentChecklist.push({
                        equipment: inputs[0].value.trim(),
                        text: inputs[1].value.trim(),
                        status: '',
                        observation: '',
                        sapNumber: '',
                        sapStatus: ''
                    });
                }
            });
            
            renderChecklist();
            createForm.classList.add('hidden');
            document.getElementById('saveToFolder').value = currentFolderId;
        });

        document.getElementById('saveLocalBtn').addEventListener('click', () => {
            const name = document.getElementById('saveName').value.trim() || checklistTitle;
            const matricula = document.getElementById('checklistMatriculaField').value.trim();
            
            if (!name) {
                alert('Digite um nome para salvar o checklist.');
                return;
            }
            
            if (!matricula) {
                alert('Por favor, informe a matrícula do responsável.');
                document.getElementById('checklistMatriculaField').focus();
                return;
            }
            
            const folderId = document.getElementById('saveToFolder').value;
            
            const savedData = {
                title: checklistTitle,
                checklistDate: new Date().toLocaleString('pt-BR'),
                matricula: matricula,
                items: currentChecklist,
                date: new Date().toISOString(),
                saveDateTime: new Date().toLocaleString('pt-BR')
            };
            
            let savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            
            if (currentChecklistId) {
                const index = savedChecklists.findIndex(c => c.id === currentChecklistId);
                if (index !== -1) {
                    savedChecklists[index] = {
                        id: currentChecklistId,
                        name: name,
                        data: savedData,
                        folderId: folderId,
                        backupOnline: savedChecklists[index].backupOnline || false
                    };
                } else {
                    savedChecklists.push({
                        id: currentChecklistId,
                        name: name,
                        data: savedData,
                        folderId: folderId,
                        backupOnline: false
                    });
                }
            } else {
                savedChecklists.push({
                    id: Date.now().toString(),
                    name: name,
                    data: savedData,
                    folderId: folderId,
                    backupOnline: false
                });
            }
            
            localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
            document.getElementById('saveName').value = '';
            
            alert(`Checklist "${name}" salvo com sucesso!`);
            renderFolderStructure();
            setupDragAndDropForFolders();
            clearMainView();
        });
        
        document.getElementById('saveOnlineBtn').addEventListener('click', async () => {
            const name = document.getElementById('saveName').value.trim() || checklistTitle;
            const matricula = document.getElementById('checklistMatriculaField').value.trim();
            
            if (!name) {
                alert('Digite um nome para salvar o checklist.');
                return;
            }
            
            if (!matricula) {
                alert('Por favor, informe a matrícula do responsável.');
                document.getElementById('checklistMatriculaField').focus();
                return;
            }
            
            const folderId = document.getElementById('saveToFolder').value;
            
            const savedData = {
                title: checklistTitle,
                checklistDate: new Date().toLocaleString('pt-BR'),
                matricula: matricula,
                items: currentChecklist,
                date: new Date().toISOString(),
                saveDateTime: new Date().toLocaleString('pt-BR')
            };
            
            // Mostrar loading
            const btn = document.getElementById('saveOnlineBtn');
            const originalText = btn.innerHTML;
            btn.innerHTML = '<div class="spinner"></div> Salvando...';
            btn.disabled = true;
            
            try {
                const success = await saveChecklistOnline(savedData);
                
                if (success) {
                    // Salvar localmente também
                    let savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
                    const checklistId = currentChecklistId || Date.now().toString();
                    
                    const checklistToSave = {
                        id: checklistId,
                        name: name,
                        data: savedData,
                        folderId: folderId,
                        backupOnline: true
                    };
                    
                    if (currentChecklistId) {
                        const index = savedChecklists.findIndex(c => c.id === currentChecklistId);
                        if (index !== -1) {
                            savedChecklists[index] = checklistToSave;
                        } else {
                            savedChecklists.push(checklistToSave);
                        }
                    } else {
                        savedChecklists.push(checklistToSave);
                    }
                    
                    localStorage.setItem('savedChecklists', JSON.stringify(savedChecklists));
                    
                    alert(`Checklist "${name}" salvo online com sucesso!`);
                    renderFolderStructure();
                    setupDragAndDropForFolders();
                    clearMainView();
                } else {
                    alert('Erro ao salvar online. Verifique sua conexão e tente novamente.');
                }
            } catch (error) {
                console.error('Erro ao salvar online:', error);
                alert('Erro ao salvar online. Verifique sua conexão e tente novamente.');
            } finally {
                btn.innerHTML = originalText;
                btn.disabled = false;
            }
        });
        
        document.getElementById('backupAllOnlineBtn').addEventListener('click', async () => {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            const checklistsToBackup = savedChecklists.filter(c => !c.backupOnline);
            
            if (checklistsToBackup.length === 0) {
                alert('Todos os checklists já estão salvos online!');
                return;
            }
            
            if (!confirm(`Fazer backup online de ${checklistsToBackup.length} checklist(s)?`)) {
                return;
            }
            
            const btn = document.getElementById('backupAllOnlineBtn');
            const originalText = btn.innerHTML;
            let completed = 0;
            
            btn.disabled = true;
            
            for (const checklist of checklistsToBackup) {
                btn.innerHTML = `<div class="spinner"></div> ${completed + 1}/${checklistsToBackup.length}`;
                
                try {
                    const success = await saveChecklistOnline(checklist.data);
                    if (success) {
                        markChecklistAsBackedUp(checklist.id);
                        completed++;
                    }
                } catch (error) {
                    console.error('Erro no backup:', error);
                }
                
                // Pequena pausa entre uploads
                await new Promise(resolve => setTimeout(resolve, 500));
            }
            
            btn.innerHTML = originalText;
            btn.disabled = false;
            
            alert(`Backup online concluído! ${completed}/${checklistsToBackup.length} checklists enviados.`);
        });
        
        // Limpar área principal
        function clearMainView() {
            checklistView.classList.add('hidden');
            createForm.classList.add('hidden');
            folderForm.classList.add('hidden');
            moveForm.classList.add('hidden');
            currentChecklist = [];
            checklistTitle = '';
            checklistDate = '';
            checklistMatricula = '';
            currentChecklistId = null;
        }

        document.getElementById('exportBtn').addEventListener('click', () => {
            const matricula = document.getElementById('checklistMatriculaField').value.trim();
            
            if (!matricula) {
                alert('Por favor, informe a matrícula do responsável antes de exportar.');
                document.getElementById('checklistMatriculaField').focus();
                return;
            }
            
            const rows = [];
            rows.push(['NOME DO CHECKLIST', 'DATA E HORA DO CHECKLIST', 'MATRÍCULA RESPONSÁVEL']);
            rows.push([checklistTitle, new Date().toLocaleString('pt-BR'), matricula]);
            rows.push(['NÚMERO', 'EQUIPAMENTO', 'ITEM DE VERIFICAÇÃO', 'STATUS', 'COMENTÁRIO', 'NÚMERO DA NOTA SAP', 'STATUS DA NOTA']);
            
            currentChecklist.forEach((item, index) => {
                rows.push([
                    index + 1,
                    item.equipment || '', 
                    item.text, 
                    item.status || '', 
                    item.observation || '', 
                    item.sapNumber || '', 
                    item.sapStatus || ''
                ]);
            });
            
            let csvContent = '';
            rows.forEach(row => {
                csvContent += row.map(cell => `"${cell}"`).join(';') + '\n';
            });
            
            const BOM = new Uint8Array([0xEF, 0xBB, 0xBF]);
            const blob = new Blob([BOM, csvContent], { type: 'text/csv;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = `${checklistTitle.replace(/\s+/g, '_')}_${matricula}_${new Date().toLocaleDateString('pt-BR').replace(/\//g, '-')}.csv`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            alert('Exportação concluída!');
        });

        document.getElementById('exportAllBtn').addEventListener('click', () => {
            const savedChecklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            
            if (savedChecklists.length === 0) {
                alert('Não há checklists salvos para exportar.');
                return;
            }
            
            let csvContent = '';
            let isFirstChecklist = true;
            
            savedChecklists.forEach(saved => {
                const checklist = saved.data;
                
                if (!isFirstChecklist) {
                    csvContent += '\n\n';
                } else {
                    isFirstChecklist = false;
                }
                
                csvContent += `"NOME DO CHECKLIST";"DATA E HORA DO CHECKLIST";"MATRÍCULA RESPONSÁVEL"\n`;
                csvContent += `"${checklist.title} (${saved.name})";"${checklist.checklistDate}";"${checklist.matricula || 'Não informado'}"\n`;
                csvContent += '"NÚMERO";"EQUIPAMENTO";"ITEM DE VERIFICAÇÃO";"STATUS";"COMENTÁRIO";"NÚMERO DA NOTA SAP";"STATUS DA NOTA"\n';
                
                checklist.items.forEach((item, index) => {
                    csvContent += `"${index + 1}";"${item.equipment || ''}";"${item.text}";"${item.status || ''}";"${item.observation || ''}";`;
                    csvContent += `"${item.sapNumber || ''}";"${item.sapStatus || ''}"\n`;
                });
                
                const total = checklist.items.length;
                const ok = checklist.items.filter(item => item.status === 'OK').length;
                const nc = checklist.items.filter(item => item.status === 'NC' || item.status === 'NOK').length;
                const na = checklist.items.filter(item => item.status === 'NA').length;
                const completed = ok + nc + na;
                const percent = total > 0 ? Math.round((completed / total) * 100) : 0;
                
                csvContent += '"ESTATÍSTICAS";"";"";"";"";"";"";\n';
                csvContent += `"Total de itens";"${total}";"";"";"";"";"";\n`;
                csvContent += `"Itens OK";"${ok}";"";"";"";"";"";\n`;
                csvContent += `"Itens NC";"${nc}";"";"";"";"";"";\n`;
                csvContent += `"Itens N/A";"${na}";"";"";"";"";"";\n`;
                csvContent += `"Concluído";"${percent}%";"";"";"";"";"";\n`;
            });
            
            const BOM = new Uint8Array([0xEF, 0xBB, 0xBF]);
            const blob = new Blob([BOM, csvContent], { type: 'text/csv;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.href = url;
            link.download = `todos_checklists_${new Date().toLocaleDateString('pt-BR').replace(/\//g, '-')}.csv`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            alert('Exportação de todos os checklists concluída!');
        });
        
        // Botões para gerenciamento de pastas
        document.getElementById('createFolderBtn').addEventListener('click', () => {
            folderForm.classList.remove('hidden');
            checklistView.classList.add('hidden');
            createForm.classList.add('hidden');
            moveForm.classList.add('hidden');
            document.getElementById('folderName').value = '';
            if (window.innerWidth < 768) toggleSidebar();
        });
        
        document.getElementById('cancelFolderBtn').addEventListener('click', () => {
            folderForm.classList.add('hidden');
        });
        
        document.getElementById('saveFolderBtn').addEventListener('click', () => {
            const folderName = document.getElementById('folderName').value.trim();
            if (folderName) {
                createFolder(folderName);
                folderForm.classList.add('hidden');
                alert(`Pasta "${folderName}" criada com sucesso!`);
            } else {
                alert('Por favor, digite um nome para a pasta.');
            }
        });
        
        // Botões para movimentação de checklists
        document.getElementById('moveAllBtn').addEventListener('click', () => {
            moveForm.classList.remove('hidden');
            checklistView.classList.add('hidden');
            createForm.classList.add('hidden');
            folderForm.classList.add('hidden');
            
            const sourceFolder = document.getElementById('moveSourceFolder').value;
            loadChecklistsForMove(sourceFolder);
            
            if (window.innerWidth < 768) toggleSidebar();
        });
        
        document.getElementById('moveSourceFolder').addEventListener('change', (e) => {
            loadChecklistsForMove(e.target.value);
        });
        
        document.getElementById('cancelMoveBtn').addEventListener('click', () => {
            moveForm.classList.add('hidden');
        });
        
        document.getElementById('confirmMoveBtn').addEventListener('click', () => {
            const checkboxes = document.querySelectorAll('#moveChecklistsContainer input[type="checkbox"]:checked');
            const selectedIds = Array.from(checkboxes)
                .filter(cb => cb.id !== 'selectAllChecklists')
                .map(cb => cb.value);
                
            if (selectedIds.length === 0) {
                alert('Nenhum checklist selecionado para mover.');
                return;
            }
            
            const targetFolder = document.getElementById('moveTargetFolder').value;
            moveSelectedChecklists(selectedIds, targetFolder);
            moveForm.classList.add('hidden');
            alert(`${selectedIds.length} checklist(s) movido(s) com sucesso!`);
        });

        function renderChecklist() {
            document.getElementById('checklistTitle').textContent = checklistTitle;
            document.getElementById('checklistDateDisplay').textContent = checklistDate;
            document.getElementById('checklistMatriculaField').value = checklistMatricula;
            
            checklistView.classList.remove('hidden');
            checklistItems.innerHTML = '';
            
            document.getElementById('saveName').value = checklistTitle;
            
            currentChecklist.forEach((item, index) => {
                const itemEl = document.createElement('div');
                itemEl.className = 'checklist-item p-3 rounded flex flex-wrap mb-2';
                
                itemEl.innerHTML = `
                    <div class="w-full flex items-start gap-3 mb-3 item-layout">
                        <div class="item-number">${index + 1}</div>
                        <div class="flex-1 min-w-0">
                            <div class="font-medium text-gray-700 mb-1 item-equipment">${item.equipment || ''}</div>
                            <div class="text-gray-800 item-text">${item.text}</div>
                        </div>
                    </div>
                    <div class="w-full flex flex-wrap items-center mb-2">
                        <div class="space-x-1 mb-2 sm:mb-0">
                            <button class="px-3 py-1 border rounded status-btn ${item.status === 'OK' ? 'active' : ''}" 
                                onclick="setStatus(${index}, 'OK')">OK</button>
                            <button class="px-3 py-1 border rounded status-btn nc ${item.status === 'NC' ? 'active' : ''}" 
                                onclick="setStatus(${index}, 'NC')">NC</button>
                            <button class="px-3 py-1 border rounded status-btn na ${item.status === 'NA' ? 'active' : ''}" 
                                onclick="setStatus(${index}, 'NA')">N/A</button>
                        </div>
                        <div class="observation-container flex-grow ml-2">
                            <input type="text" class="w-full p-2 border rounded" 
                                placeholder="Observação" maxlength="100" 
                                value="${item.observation || ''}" oninput="setObservation(${index}, this.value)">
                            <span class="char-count">${(item.observation || '').length}/100</span>
                        </div>
                    </div>
                    <div class="w-full sap-fields ${item.status === 'NC' ? '' : 'hidden'}">
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <input type="text" class="w-full p-2 border rounded" 
                                    placeholder="NÚMERO DA NOTA SAP" maxlength="20" 
                                    value="${item.sapNumber || ''}" oninput="setSapNumber(${index}, this.value)">
                            </div>
                            <div>
                                <select class="w-full p-2 border rounded" onchange="setSapStatus(${index}, this.value)">
                                    <option value="" disabled ${!item.sapStatus ? 'selected' : ''}>STATUS DA NOTA</option>
                                    <option value="Pendente" ${item.sapStatus === 'Pendente' ? 'selected' : ''}>Pendente</option>
                                    <option value="Em andamento" ${item.sapStatus === 'Em andamento' ? 'selected' : ''}>Em andamento</option>
                                    <option value="Concluída" ${item.sapStatus === 'Concluída' ? 'selected' : ''}>Concluída</option>
                                    <option value="Cancelada" ${item.sapStatus === 'Cancelada' ? 'selected' : ''}>Cancelada</option>
                                </select>
                            </div>
                        </div>
                    </div>`;
                checklistItems.appendChild(itemEl);
            });
            
            updateStats();
        }

        function setStatus(index, status) {
            if (status === 'NOK') status = 'NC';
            
            const oldStatus = currentChecklist[index].status;
            currentChecklist[index].status = status;
            
            const item = document.querySelectorAll('#checklistItems .checklist-item')[index];
            const buttons = item.querySelectorAll('.status-btn');
            buttons.forEach(btn => {
                if ((btn.textContent === 'OK' && status === 'OK') || 
                    (btn.textContent === 'NC' && status === 'NC') || 
                    (btn.textContent === 'N/A' && status === 'NA')) {
                    btn.classList.add('active');
                } else {
                    btn.classList.remove('active');
                }
            });
            
            const sapFields = item.querySelector('.sap-fields');
            if (status === 'NC') {
                sapFields.classList.remove('hidden');
                if (oldStatus !== 'NC' && !currentChecklist[index].sapStatus) {
                    currentChecklist[index].sapStatus = 'Pendente';
                    const statusSelect = sapFields.querySelector('select');
                    statusSelect.value = 'Pendente';
                }
            } else {
                sapFields.classList.add('hidden');
            }
            
            updateStats();
        }

        function setObservation(index, text) {
            if (text.length <= 100) {
                currentChecklist[index].observation = text;
                const countElement = document.querySelectorAll('#checklistItems .char-count')[index];
                if (countElement) countElement.textContent = `${text.length}/100`;
            }
        }
        
        function setSapNumber(index, text) {
            currentChecklist[index].sapNumber = text;
        }
        
        function setSapStatus(index, status) {
            currentChecklist[index].sapStatus = status;
        }

        function updateStats() {
            const total = currentChecklist.length;
            const ok = currentChecklist.filter(item => item.status === 'OK').length;
            const nc = currentChecklist.filter(item => item.status === 'NC' || item.status === 'NOK').length;
            const na = currentChecklist.filter(item => item.status === 'NA').length;
            const completed = ok + nc + na;
            const percent = total > 0 ? Math.round((completed / total) * 100) : 0;
            
            document.getElementById('totalCount').textContent = total;
            document.getElementById('okCount').textContent = ok;
            document.getElementById('ncCount').textContent = nc;
            document.getElementById('naCount').textContent = na;
            document.getElementById('percentCount').textContent = percent + '%';
            document.getElementById('progressBar').style.width = percent + '%';
        }
        
        function loadChecklist(id) {
            const checklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            const checklist = checklists.find(c => c.id === id);
            
            if (checklist) {
                checklistTitle = checklist.data.title;
                checklistDate = checklist.data.checklistDate || new Date().toLocaleString('pt-BR');
                checklistMatricula = checklist.data.matricula || '';
                currentChecklist = checklist.data.items;
                currentChecklistId = checklist.id;
                
                if (checklist.folderId) {
                    document.getElementById('saveToFolder').value = checklist.folderId;
                }
                
                renderChecklist();
                
                if (window.innerWidth < 768) {
                    toggleSidebar();
                }
            }
        }

        function deleteChecklist(id) {
            if (!confirm('Tem certeza que deseja excluir este checklist?')) return;
            
            let checklists = JSON.parse(localStorage.getItem('savedChecklists') || '[]');
            checklists = checklists.filter(c => c.id !== id);
            localStorage.setItem('savedChecklists', JSON.stringify(checklists));
            renderFolderStructure();
            setupDragAndDropForFolders();
            
            if (currentChecklistId === id) {
                clearMainView();
            }
        }

        function resetCreateForm() {
            formItems.innerHTML = `
                <div class="checklist-item p-3 rounded flex flex-wrap mb-2">
                    <div class="grid grid-cols-3 gap-3 w-full">
                        <div class="col-span-1">
                            <input type="text" placeholder="Equipamento" class="w-full p-2 border rounded">
                        </div>
                        <div class="col-span-2">
                            <input type="text" placeholder="Item de Verificação" class="w-full p-2 border rounded">
                        </div>
                    </div>
                    <button class="ml-2 px-3 py-1 bg-red-500 text-white rounded text-sm" onclick="this.parentNode.remove()">✕</button>
                </div>`;
            document.getElementById('checklistName').value = '';
            document.getElementById('checklistMatricula').value = '';
        }

        // Inicialização
        document.addEventListener('DOMContentLoaded', () => {
            renderFolderStructure();
            updateFolderSelects();
            setupDragAndDropForFolders();
        });
    </script>
</body>
</html>
