<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Malla Curricular - Ing. en Recursos Naturales UC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@400;600;700&display=swap');
        
        * { box-sizing: border-box; }
        
        body { 
            font-family: 'Quicksand', sans-serif; 
            background-color: #fdf2f8; 
        }
        
        .planner-grid { 
            display: grid; 
            grid-template-columns: repeat(11, auto); 
            gap: 0.5rem; 
            align-items: start; 
        }
        
        .subject-box { 
            background: white; 
            border-left: 4px solid #db2777; 
            width: 135px; 
            padding: 0.5rem; 
            height: 175px; 
            display: flex; 
            flex-direction: column; 
            justify-content: space-between; 
            transition: all 0.3s ease; 
            border-radius: 0.75rem;
            border: 1px solid #f0bcd4;
            cursor: pointer;
        }
        
        .subject-box:hover:not(.bloqueado) { 
            transform: translateY(-2px); 
            box-shadow: 0 4px 12px rgba(0,0,0,0.1); 
        }
        
        .subject-box.aprobado { 
            border-left-color: #22c55e; 
            background-color: #dcfce7; 
        }
        
        .subject-box.reprobado { 
            border-left-color: #ef4444; 
            background-color: #fee2e2; 
        }
        
        .subject-box.bloqueado { 
            opacity: 0.4; 
            cursor: not-allowed; 
            border-left-color: #94a3b8; 
            background-color: #f1f5f9; 
        }
        
        .cell-input, 
        .cell-code { 
            border: none; 
            background: transparent; 
            width: 100%; 
            font-weight: 700; 
            color: black; 
            outline: none; 
            text-align: center;
        }
        
        .cell-input { 
            font-size: 0.70rem; 
        }
        
        .cell-code { 
            font-size: 0.65rem; 
        }
        
        .cell-input:not([readonly]):focus,
        .cell-code:not([readonly]):focus {
            background: rgba(219, 39, 119, 0.1);
            border-radius: 4px;
        }
        
        .nota-btn { 
            font-size: 10px; 
            background: #db2777; 
            color: white; 
            padding: 3px 6px; 
            border-radius: 4px; 
            cursor: pointer; 
            border: none; 
            z-index: 10; 
            font-weight: bold; 
            width: 100%; 
            margin-top: auto;
            transition: background-color 0.2s ease;
        }
        
        .nota-btn:hover {
            background: #c4206d;
        }
        
        .divider-col { 
            writing-mode: vertical-rl; 
            transform: rotate(180deg); 
            background: #db2777; 
            color: white; 
            font-weight: bold; 
            padding: 0.5rem 0; 
            font-size: 0.7rem; 
            border-radius: 4px; 
            height: 98%;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }
        
        .prereq-tooltip {
            position: relative;
            display: inline-block;
            cursor: help;
        }
        
        .prereq-tooltip .tooltiptext {
            visibility: hidden;
            width: 200px;
            background-color: #db2777;
            color: #fff;
            text-align: center;
            padding: 8px;
            border-radius: 6px;
            position: absolute;
            z-index: 100;
            bottom: 125%;
            left: 50%;
            margin-left: -100px;
            opacity: 0;
            transition: opacity 0.3s;
            font-size: 0.75rem;
            line-height: 1.3;
        }
        
        .prereq-tooltip .tooltiptext::after {
            content: "";
            position: absolute;
            top: 100%;
            left: 50%;
            margin-left: -5px;
            border-width: 5px;
            border-style: solid;
            border-color: #db2777 transparent transparent transparent;
        }
        
        .prereq-tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
        
        @media (max-width: 768px) {
            .planner-grid {
                grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            }
            
            .subject-box {
                width: 100%;
                min-width: 130px;
            }
        }
        
        .stats-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 1rem;
            margin-top: 1rem;
        }
        
        .stat-box {
            padding: 1rem;
            background: white;
            border-radius: 0.75rem;
            box-shadow: 0 2px 4px rgba(0,0,0,0.05);
            border: 2px solid #f0bcd4;
        }
        
        .stat-label {
            font-size: 0.875rem;
            color: #666;
            font-weight: 600;
        }
        
        .stat-value {
            font-size: 1.75rem;
            font-weight: 700;
            color: #db2777;
            margin-top: 0.5rem;
        }
        
        .search-container {
            margin-bottom: 1.5rem;
            display: flex;
            gap: 1rem;
            flex-wrap: wrap;
            align-items: center;
        }
        
        .search-input {
            padding: 0.5rem 1rem;
            border: 2px solid #db2777;
            border-radius: 0.5rem;
            font-size: 0.95rem;
            min-width: 200px;
        }
        
        .filter-btn {
            padding: 0.5rem 1rem;
            background: white;
            border: 2px solid #db2777;
            color: #db2777;
            border-radius: 0.5rem;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.2s ease;
        }
        
        .filter-btn.active {
            background: #db2777;
            color: white;
        }
        
        .filter-btn:hover {
            background: #db2777;
            color: white;
        }
    </style>
</head>
<body class="p-4">
    <header class="text-center mb-6">
        <h1 class="text-3xl font-bold text-[#db2777] uppercase tracking-tight">ING. EN RECURSOS NATURALES</h1>
        
        <div class="stats-container">
            <div class="stat-box">
                <div class="stat-label">PPA (Promedio Ponderado)</div>
                <div class="stat-value" id="ppa-value">0.00</div>
            </div>
            <div class="stat-box">
                <div class="stat-label">Créditos Aprobados</div>
                <div class="stat-value" id="credits-aprobados">0</div>
            </div>
            <div class="stat-box">
                <div class="stat-label">Créditos Totales</div>
                <div class="stat-value" id="credits-totales">0</div>
            </div>
            <div class="stat-box">
                <div class="stat-label">Progreso</div>
                <div class="stat-value" id="progress-pct">0%</div>
            </div>
        </div>
        
        <div class="mt-6 flex flex-wrap gap-3 justify-center">
            <button id="save-btn" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg font-bold shadow transition">Guardar Progreso</button>
            <button id="load-btn" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg font-bold shadow transition">Cargar Progreso</button>
            <button id="reset-btn" class="bg-red-500 hover:bg-red-600 text-white px-4 py-2 rounded-lg font-bold shadow transition">Reiniciar Malla</button>
            <button id="print-btn" class="bg-purple-600 hover:bg-purple-700 text-white px-4 py-2 rounded-lg font-bold shadow transition">Exportar PDF</button>
        </div>
    </header>

    <div class="search-container">
        <input type="text" id="search-input" class="search-input" placeholder="Buscar por nombre o código...">
        <button id="filter-all" class="filter-btn active">Todo</button>
        <button id="filter-aprobado" class="filter-btn">✓ Aprobado</button>
        <button id="filter-reprobado" class="filter-btn">✗ Reprobado</button>
        <button id="filter-pendiente" class="filter-btn">⊘ Pendiente</button>
    </div>

    <div class="max-w-[99vw] mx-auto overflow-x-auto pb-10">
        <div class="planner-grid" id="planner"></div>
    </div>

    <div id="nota-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white p-6 rounded-2xl w-80 shadow-2xl">
            <h2 id="modal-title" class="font-bold text-lg mb-3 text-gray-800 text-center">Gestionar Notas</h2>
            <div id="intentos-list" class="max-h-48 overflow-y-auto mb-4 space-y-2 pr-1"></div>
            <div class="flex gap-2 mb-3">
                <input type="number" id="new-nota" step="0.1" min="1.0" max="7.0" placeholder="Ej: 5.5" class="w-full p-2 border rounded-lg text-center font-bold" aria-label="Nueva nota">
                <button id="add-nota-btn" class="bg-pink-600 hover:bg-pink-700 text-white px-4 py-2 rounded-lg font-bold transition">Añadir</button>
            </div>
            <button id="close-modal-btn" class="w-full bg-gray-500 hover:bg-gray-600 text-white py-2 rounded-lg font-bold transition">Cerrar</button>
        </div>
    </div>

    <script>
        // Data Structure
        const data = {
            "I": [
                ["Precálculo", "MAT1000", 10, []],
                ["Química", "QIM201G", 15, []],
                ["Biología y Diversidad Animal", "BIO227E", 10, []],
                ["Intro. a la Geografía Física General", "GEO625", 10, []],
                ["Desafíos de la IRN", "REN101", 5, []],
                ["Integridad Académica en la UC", "VRA4000", 0, []],
                ["Español", "VRA100C", 0, []],
                ["English Test", "VRA2000", 0, []]
            ],
            "II": [
                ["Cálculo I", "MAT1100", 10, ["MAT1000"]],
                ["Física para Ciencias", "FIS109C", 10, []],
                ["Botánica", "AGL101", 10, []],
                ["Sustentabilidad", "SUS1000", 10, []],
                ["Filosofía para qué?", "FIL2001", 10, []]
            ],
            "III": [
                ["Cálculo II", "MAT1220", 10, ["MAT1100"]],
                ["Estadística", "AGL201", 10, ["MAT1100"]],
                ["Fundamentos de la Ecología y Evolución", "BIO108A", 10, []],
                ["Introducción a la Economía", "EAE105A", 10, []],
                ["Formación Teológica", "FTL1000", 10, []]
            ],
            "IV": [
                ["Climatología", "REN201", 10, ["MAT1000", "FIS109C"]],
                ["Introducción a la Programación", "IIC1103", 10, []],
                ["Sistemas Socioambientales", "REN202", 10, ["BIO108A"]],
                ["Introducción a la Economía Ambiental", "AGE206", 10, ["EAE105A"]],
                ["Formación General", "FG1001", 10, []]
            ],
            "V": [
                ["Geomática", "AGR205", 10, ["MAT1000"]],
                ["Geología y Suelo", "REN203", 10, ["QIM201G", "GEO625"]],
                ["Metodología de la Investigación", "REN225", 10, ["AGL201", "MAT1220", "IIC1103"]],
                ["Formación General", "FG1002", 10, []],
                ["Formación General", "FG1003", 10, []]
            ],
            "VI": [
                ["Derecho Ambiental", "DER201E", 10, ["REN202"]],
                ["CMD", "AGL251", 10, []],
                ["Evaluación de Impacto Ambiental", "AGR307", 10, ["AGE206", "GEO625"]],
                ["Política Ambiental de los Recursos Naturales", "REN220", 10, ["AGE206"]],
                ["Formación General", "FG1004", 10, []]
            ],
            "VII": [
                ["MINOR", "M1", 10, []],
                ["MINOR", "M2", 10, []],
                ["MINOR", "M3", 10, []],
                ["Formación General", "FG1005", 10, []],
                ["Formación General", "FG1006", 10, []]
            ],
            "VIII": [
                ["Mod. Capstone", "REN235", 10, ["AGL251", "AGR205", "REN203", "IIC1103"]],
                ["MINOR", "M4", 10, []],
                ["MINOR", "M5", 10, []],
                ["A. Sustentabilidad", "REN305", 10, ["DER201E"]],
                ["Ética para IRN", "ETI1002", 5, ["DER201E"]],
                ["Taller de Práctica", "REN290", 5, ["REN235"]]
            ],
            "IX": [
                ["Análisis de Riesgo Ambiental", "ICS2023", 10, ["AGR307"]],
                ["Conservación Ambiental y Participación Comunitaria", "GEO2916", 10, []],
                ["Evaluación de Proyectos en RRNN", "REN310", 10, ["AGR307"]],
                ["Liderazgo, Innovación y Emprendimiento en RRNN", "REN320", 10, ["REN235", "AGE206"]],
                ["Administración para Ingenieros de RRNN", "REN325", 10, ["REN235"]]
            ],
            "X": [
                ["Gestión Ambiental Empresarial", "REN308", 10, ["REN235"]],
                ["Tecnologías Digitales en RRNN", "REN335", 10, []],
                ["Proyecto de Título", "REN390", 30, ["ETI1002"]]
            ]
        };

        // State Management
        const state = {};       
        const attempts = {};    
        let activeSigla = null;
        let filterMode = 'all';
        let searchTerm = '';

        // Requirements per semester
        const requirementsPerSemester = {
            8: ['VRA4000', 'VRA100C', 'VRA2000', 'FIL2001', 'FTL1000', 'FG1001', 'FG1002', 'FG1003', 'FG1004', 'FG1005', 'FG1006'],
            9: [],
            10: []
        };

        // Utility Functions
        function getCredits(sigla) {
            for (let sem in data) {
                let m = data[sem].find(i => i[1] === sigla);
                if (m) return m[2];
            }
            return 10;
        }

        function getTotalCredits() {
            let total = 0;
            for (let sem in data) {
                data[sem].forEach(item => {
                    total += item[2];
                });
            }
            return total;
        }

        function getCourseInfo(sigla) {
            for (let sem in data) {
                let course = data[sem].find(i => i[1] === sigla);
                if (course) {
                    return { name: course[0], credits: course[2], prereqs: course[3] };
                }
            }
            return null;
        }

        function checkBlocked(sigla, semIndex, prereqs) {
            // Check prerequisite requirements
            if (prereqs && prereqs.length > 0) {
                if (prereqs.some(p => state[p] !== 'aprobado')) return true;
            }

            // Check semester-specific requirements
            if (semIndex >= 8) {
                const semRequirements = requirementsPerSemester[semIndex] || [];
                if (semRequirements.some(r => state[r] !== 'aprobado')) return true;

                // All prior semesters must be passed
                const maxSem = semIndex;
                for (let s = 0; s < maxSem; s++) {
                    for (let item of data[Object.keys(data)[s]]) {
                        if (state[item[1]] !== 'aprobado') return true;
                    }
                }
            }

            return false;
        }

        function updateAll() {
            let totalWeighted = 0;
            let totalCredits = 0;
            let creditsAprobados = 0;
            const totalCoursesCredits = getTotalCredits();

            document.querySelectorAll('.subject-box').forEach(box => {
                const sigla = box.dataset.sigla;
                const semIndex = parseInt(box.dataset.semIndex);
                const prereqs = JSON.parse(box.dataset.prereqs);
                const courseName = box.dataset.nombre || '';
                
                let isBlocked = checkBlocked(sigla, semIndex, prereqs);
                
                if (isBlocked && state[sigla] === 'aprobado') {
                    state[sigla] = '';
                }

                const statusClass = state[sigla] || '';
                box.className = `subject-box rounded-xl border border-pink-200 ${statusClass} ${isBlocked ? 'bloqueado' : ''}`;
                
                // Apply filter
                applyFilter(box, courseName, sigla);
            });

            // Calculate PPA
            Object.entries(attempts).forEach(([sigla, notas]) => {
                const creds = getCredits(sigla);
                notas.forEach(nota => {
                    totalWeighted += (nota * creds);
                    totalCredits += creds;
                });
            });

            // Count approved credits
            for (let sem in data) {
                data[sem].forEach(item => {
                    if (state[item[1]] === 'aprobado') {
                        creditsAprobados += item[2];
                    }
                });
            }

            // Update stats
            document.getElementById('ppa-value').innerText = totalCredits > 0 ? (totalWeighted / totalCredits).toFixed(2) : "0.00";
            document.getElementById('credits-aprobados').innerText = creditsAprobados;
            document.getElementById('credits-totales').innerText = totalCoursesCredits;
            document.getElementById('progress-pct').innerText = ((creditsAprobados / totalCoursesCredits) * 100).toFixed(0) + '%';
        }

        function applyFilter(box, courseName, sigla) {
            let show = true;
            const status = state[sigla] || '';

            // Apply filter mode
            if (filterMode === 'aprobado' && status !== 'aprobado') show = false;
            if (filterMode === 'reprobado' && status !== 'reprobado') show = false;
            if (filterMode === 'pendiente' && status !== '') show = false;

            // Apply search
            if (searchTerm) {
                const term = searchTerm.toLowerCase();
                if (!courseName.toLowerCase().includes(term) && !sigla.toLowerCase().includes(term)) {
                    show = false;
                }
            }

            box.style.display = show ? '' : 'none';
        }

        // Modal Functions
        function openModal(sigla, event) {
            if(event) event.stopPropagation();
            activeSigla = sigla;
            const courseInfo = getCourseInfo(sigla);
            const modalTitle = document.getElementById('modal-title');
            if (courseInfo) {
                modalTitle.innerText = `${courseInfo.name} (${sigla})`;
                if (courseInfo.prereqs.length > 0) {
                    modalTitle.innerHTML += `<div style="font-size: 0.75rem; font-weight: normal; margin-top: 0.5rem;">Requisitos: ${courseInfo.prereqs.join(', ')}</div>`;
                }
            }
            renderIntentosList();
            document.getElementById('nota-modal').classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('nota-modal').classList.add('hidden');
            document.getElementById('new-nota').value = '';
        }

        function renderIntentosList() {
            const list = document.getElementById('intentos-list');
            list.innerHTML = '';
            const notas = attempts[activeSigla] || [];
            
            if(notas.length === 0) {
                list.innerHTML = `<p class="text-xs text-gray-500 text-center py-2">No hay notas registradas para este ramo.</p>`;
                return;
            }

            notas.forEach((n, i) => {
                const deleteBtn = document.createElement('button');
                deleteBtn.className = "text-red-500 hover:text-red-700 font-bold px-2 py-1 text-xs bg-red-50 rounded";
                deleteBtn.innerText = "Eliminar";
                deleteBtn.onclick = () => deleteIntento(i);

                const item = document.createElement('div');
                item.className = "flex justify-between items-center p-2 bg-gray-50 rounded-lg border border-gray-200";
                item.innerHTML = `<span class="text-sm font-bold text-gray-700">Intento ${i + 1}: <span class="text-pink-600">${n.toFixed(1)}</span></span>`;
                item.appendChild(deleteBtn);
                list.appendChild(item);
            });
        }

        function addIntento() {
            const val = parseFloat(document.getElementById('new-nota').value);
            if (!isNaN(val) && val >= 1.0 && val <= 7.0) {
                if (!attempts[activeSigla]) attempts[activeSigla] = [];
                attempts[activeSigla].push(val);
                document.getElementById('new-nota').value = '';
                renderIntentosList();
                updateAll();
            } else {
                alert("Por favor ingresa una nota válida entre 1.0 y 7.0");
            }
        }

        function deleteIntento(idx) {
            if (attempts[activeSigla]) {
                attempts[activeSigla].splice(idx, 1);
                if (attempts[activeSigla].length === 0) delete attempts[activeSigla];
                renderIntentosList();
                updateAll();
            }
        }

        // Storage Functions with error handling
        function saveProgress() {
            try {
                const customTexts = {};
                document.querySelectorAll('.cell-input, .cell-code').forEach(el => {
                    customTexts[el.dataset.id] = el.value;
                });
                const saveObj = { state, attempts, customTexts };
                localStorage.setItem('irn_planner_save', JSON.stringify(saveObj));
                alert("✓ ¡Progreso guardado exitosamente en tu navegador!");
            } catch (e) {
                alert("✗ Error al guardar: " + (e.message || "almacenamiento lleno"));
            }
        }

        function loadProgress() {
            try {
                const saved = localStorage.getItem('irn_planner_save');
                if (saved) {
                    const dataObj = JSON.parse(saved);
                    Object.keys(state).forEach(k => delete state[k]);
                    Object.assign(state, dataObj.state || {});
                    Object.keys(attempts).forEach(k => delete attempts[k]);
                    Object.assign(attempts, dataObj.attempts || {});

                    if (dataObj.customTexts) {
                        Object.entries(dataObj.customTexts).forEach(([id, val]) => {
                            const el = document.querySelector(`[data-id="${id}"]`);
                            if (el) el.value = val;
                        });
                    }
                    updateAll();
                    alert("✓ ¡Progreso cargado con éxito!");
                } else {
                    alert("⊘ No se encontró ningún progreso guardado.");
                }
            } catch(e) {
                alert("✗ Error al cargar los datos: " + (e.message || "datos corruptos"));
            }
        }

        function resetProgress() {
            if (confirm("¿Estás seguro de que deseas reiniciar toda la malla? Se borrarán tus notas y estados.")) {
                Object.keys(state).forEach(k => delete state[k]);
                Object.keys(attempts).forEach(k => delete attempts[k]);
                localStorage.removeItem('irn_planner_save');
                location.reload();
            }
        }

        function printToPDF() {
            alert("Función de exportación PDF en desarrollo. Por ahora, usa Ctrl+P para imprimir.");
        }

        // Initialize Grid
        const planner = document.getElementById('planner');
        const semestresKeys = Object.keys(data);

        semestresKeys.forEach((sem, idx) => {
            const col = document.createElement('div');
            col.className = "flex flex-col gap-2";
            col.innerHTML = `<div class="bg-[#db2777] text-white text-center py-2 px-1 font-bold rounded-lg text-xs shadow-sm">Sem. ${sem}</div>`;
            
            data[sem].forEach(m => {
                const [nombre, sigla, creds, prereqs] = m;
                const isEditable = nombre.includes("MINOR") || nombre.includes("Form.") || nombre.includes("General");
                const box = document.createElement('div');
                
                box.className = "subject-box";
                box.dataset.sigla = sigla;
                box.dataset.semIndex = idx;
                box.dataset.prereqs = JSON.stringify(prereqs);
                box.dataset.nombre = nombre;

                box.onclick = (e) => {
                    if (e.target.tagName === 'INPUT' || e.target.tagName === 'BUTTON') return;
                    if (box.classList.contains('bloqueado')) return;

                    if (!state[sigla] || state[sigla] === '') {
                        state[sigla] = 'aprobado';
                    } else if (state[sigla] === 'aprobado') {
                        state[sigla] = 'reprobado';
                    } else {
                        state[sigla] = '';
                    }
                    updateAll();
                };

                const inputIdName = `n-${sigla}-${idx}`;
                const inputIdCode = `c-${sigla}-${idx}`;

                // Create elements individually for better control
                const nameInput = document.createElement('input');
                nameInput.type = 'text';
                nameInput.value = nombre;
                nameInput.className = 'cell-input';
                nameInput.dataset.id = inputIdName;
                if (!isEditable) nameInput.readOnly = true;

                const codeDiv = document.createElement('div');
                codeDiv.className = 'my-1';
                
                const codeInput = document.createElement('input');
                codeInput.type = 'text';
                codeInput.value = sigla;
                codeInput.className = 'cell-code font-bold text-gray-800';
                codeInput.dataset.id = inputIdCode;
                if (!isEditable) codeInput.readOnly = true;

                const creditsDiv = document.createElement('div');
                creditsDiv.className = 'text-[10px] text-gray-600 font-bold text-center mt-0.5';
                creditsDiv.innerText = `cr: ${creds}`;

                codeDiv.appendChild(codeInput);
                codeDiv.appendChild(creditsDiv);

                const notaBtn = document.createElement('button');
                notaBtn.className = 'nota-btn';
                notaBtn.innerText = 'Nota';
                notaBtn.onclick = (e) => openModal(sigla, e);

                box.appendChild(nameInput);
                box.appendChild(codeDiv);
                box.appendChild(notaBtn);
                
                col.appendChild(box);
            });

            planner.appendChild(col);

            if (idx === 7) {
                const div = document.createElement('div');
                div.className = "divider-col shadow-inner";
                div.innerText = "LICENCIATURA EN RRNN";
                planner.appendChild(div);
            }
        });

        // Event Listeners
        document.getElementById('save-btn').addEventListener('click', saveProgress);
        document.getElementById('load-btn').addEventListener('click', loadProgress);
        document.getElementById('reset-btn').addEventListener('click', resetProgress);
        document.getElementById('print-btn').addEventListener('click', printToPDF);
        document.getElementById('add-nota-btn').addEventListener('click', addIntento);
        document.getElementById('close-modal-btn').addEventListener('click', closeModal);

        // Filter buttons
        document.getElementById('filter-all').addEventListener('click', () => {
            filterMode = 'all';
            document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');
            updateAll();
        });

        document.getElementById('filter-aprobado').addEventListener('click', () => {
            filterMode = 'aprobado';
            document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');
            updateAll();
        });

        document.getElementById('filter-reprobado').addEventListener('click', () => {
            filterMode = 'reprobado';
            document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');
            updateAll();
        });

        document.getElementById('filter-pendiente').addEventListener('click', () => {
            filterMode = 'pendiente';
            document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
            event.target.classList.add('active');
            updateAll();
        });

        // Search
        document.getElementById('search-input').addEventListener('keyup', (e) => {
            searchTerm = e.target.value;
            updateAll();
        });

        // Keyboard support for modal
        document.getElementById('new-nota').addEventListener('keypress', (e) => {
            if (e.key === 'Enter') addIntento();
        });

        // Close modal on Escape
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape') closeModal();
        });

        // Initialize
        updateAll();
    </script>
</body>
</html>
