<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Malla Curricular - IRN UC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@400;700&display=swap');
        body { font-family: 'Quicksand', sans-serif; background-color: #fdf2f8; }
        .planner-grid { display: grid; grid-template-columns: repeat(11, auto); gap: 0.5rem; align-items: start; }
        .subject-box { background: white; border-left: 4px solid #d81b60; width: 130px; padding: 0.5rem; height: 160px; display: flex; flex-direction: column; justify-content: space-between; transition: all 0.2s; cursor: pointer; }
        .subject-box.aprobado { border-left-color: #22c55e; background-color: #dcfce7; }
        .subject-box.reprobado { border-left-color: #ef4444; background-color: #fee2e2; }
        .subject-box.bloqueado { opacity: 0.4; cursor: not-allowed; border-left-color: #94a3b8; }
        .cell-input { border: none; background: transparent; width: 100%; font-size: 0.70rem; font-weight: 700; color: black; outline: none; }
        .cell-code { font-size: 0.60rem; color: black; background: transparent; border: none; width: 100%; outline: none; }
        .nota-btn { font-size: 9px; background: #d81b60; color: white; padding: 2px 4px; border-radius: 4px; cursor: pointer; border: none; z-index: 10; }
        .divider-col { writing-mode: vertical-rl; transform: rotate(180deg); background: #d81b60; color: white; font-weight: bold; padding: 0.5rem 0; font-size: 0.6rem; border-radius: 4px; height: 95%; width: 25px; }
    </style>
</head>
<body class="p-4">
    <header class="text-center mb-6">
        <h1 class="text-3xl font-bold text-[#d81b60] uppercase tracking-tight">Ingeniería en Recursos Naturales</h1>
        <div class="mt-4 flex gap-4 justify-center">
            <div class="p-3 bg-white rounded-xl shadow-md border-2 border-[#d81b60]">
                <span class="font-bold text-gray-700">PPA: </span>
                <span id="ppa-value" class="text-2xl font-bold text-[#d81b60]">0.00</span>
            </div>
            <button onclick="saveProgress()" class="bg-green-600 text-white px-4 py-2 rounded-lg font-bold">Guardar</button>
            <button onclick="loadProgress()" class="bg-blue-600 text-white px-4 py-2 rounded-lg font-bold">Cargar</button>
        </div>
    </header>

    <div class="max-w-[98vw] mx-auto overflow-x-auto pb-10">
        <div class="planner-grid" id="planner"></div>
    </div>

    <!-- Modal de Notas -->
    <div id="nota-modal" class="hidden fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-white p-6 rounded-xl w-80">
            <h2 id="modal-title" class="font-bold mb-4">Gestionar Intentos</h2>
            <div id="intentos-list" class="max-h-40 overflow-y-auto mb-4"></div>
            <input type="number" id="new-nota" step="0.1" placeholder="Nueva nota" class="w-full p-2 border rounded mb-2">
            <button onclick="addIntento()" class="w-full bg-pink-600 text-white py-2 rounded mb-2">Añadir Intento</button>
            <button onclick="closeModal()" class="w-full bg-gray-400 text-white py-2 rounded">Cerrar</button>
        </div>
    </div>

    <script>
        const data = {
            "I": [["Precálculo", "MAT1000", 10, []], ["Química", "QIM201G", 15, []], ["Biología y Div. Animal", "BIO227E", 10, []], ["Intro. Geografía F.", "GEO625", 10, []], ["Desafíos IRN", "REN101", 5, []], ["Integridad Acad.", "VRA4000", 0, []], ["Español", "VRA100C", 0, []], ["English Test", "VRA2000", 0, []]],
            "II": [["Cálculo I", "MAT1100", 10, ["MAT1000"]], ["Física para Ciencias", "FIS109C", 10, []], ["Botánica", "AGL101", 10, []], ["Sustentabilidad", "SUS1000", 10, []], ["Filosofía para qué?", "FIL2001", 10, []]],
            "III": [["Cálculo II", "MAT1220", 10, ["MAT1100"]], ["Estadística", "AGL201", 10, ["MAT1100"]], ["Ecol. y Evolución", "BIO108A", 10, []], ["Intro. Economía", "EAE105A", 10, []], ["Form. Teológica", "FTL1000", 10, []]],
            "IV": [["Climatología", "REN201", 10, ["MAT1000", "FIS109C"]], ["Intro. a Progra", "IIC1103", 10, []], ["Sist. Socioambient.", "REN202", 10, ["BIO108A"]], ["A. Sustentabilidad", "AGE206", 10, ["EAE105A"]], ["Form. General", "FG1001", 10, []]],
            "V": [["Geomática", "AGR205", 10, ["MAT1000"]], ["Geología y Suelo", "REN203", 10, ["QIM201G", "GEO625"]], ["Met. Investigación", "REN225", 10, ["AGL201", "MAT1220", "IIC1103"]], ["Form. General", "FG1002", 10, []], ["Form. General", "FG1003", 10, []]],
            "VI": [["Der. Ambiental", "DER201E", 10, ["REN202"]], ["CMD", "AGL251", 10, []], ["Eva. Impacto Amb.", "AGR307", 10, ["AGE206", "GEO625"]], ["Política Ambiental", "REN220", 10, ["AGE206"]], ["Form. General", "FG1004", 10, []]],
            "VII": [["MINOR", "M1", 10, []], ["MINOR", "M2", 10, []], ["MINOR", "M3", 10, []], ["Form. General", "FG1005", 10, []], ["Form. General", "FG1006", 10, []]],
            "VIII": [["Mod. Capstone", "REN235", 10, ["AGL251", "AGR205", "REN203", "IIC1103"]], ["MINOR", "M4", 10, []], ["MINOR", "M5", 10, []], ["A. Sustentabilidad", "REN305", 10, ["DER201E"]], ["Ética para IRN", "ETI1002", 5, ["DER201E"]], ["Taller de Práctica", "REN290", 5, ["REN235"]]],
            "IX": [["Análisis de Riesgo", "ICS2023", 10, []], ["Conservación Amb.", "GEO2916", 10, []], ["Eva. Proyectos RRNN", "REN310", 10, []], ["Liderazgo e Innov.", "REN320", 10, []], ["Admin. para IRN", "REN325", 10, []]],
            "X": [["Gestión Ambiental", "REN308", 10, []], ["Tec. Digitales", "REN335", 10, []], ["Proyecto de Título", "REN390", 30, []]]
        };

        const state = {}; 
        const attempts = {}; 
        let activeSigla = null;

        function getCredits(sigla) {
            for (let sem in data) {
                let m = data[sem].find(i => i[1] === sigla);
                if (m) return m[2];
            }
            return 0;
        }

        function updateAll() {
            let totalWeighted = 0;
            let totalCredits = 0;

            document.querySelectorAll('.subject-box').forEach(box => {
                const sigla = box.dataset.sigla;
                const prereqs = JSON.parse(box.dataset.prereqs);
                let isBlocked = prereqs.some(p => state[p] !== 'aprobado');
                
                if (parseInt(box.dataset.sem) >= 9) {
                    const baseReqs = ["VRA4000", "VRA100C", "VRA2000", "FTL1000", "FIL2001"];
                    const fgs = ["FG1001", "FG1002", "FG1003", "FG1004", "FG1005", "FG1006"];
                    if(baseReqs.some(r => state[r] !== 'aprobado') || fgs.some(r => state[r] !== 'aprobado')) isBlocked = true;
                }

                if (isBlocked) box.classList.add('bloqueado');
                else box.classList.remove('bloqueado');
                
                box.className = `subject-box rounded-lg border border-pink-100 ${state[sigla] || ''} ${isBlocked ? 'bloqueado' : ''}`;
            });

            Object.entries(attempts).forEach(([sigla, notas]) => {
                const creds = getCredits(sigla);
                notas.forEach(nota => {
                    totalWeighted += (nota * creds);
                    totalCredits += creds;
                });
            });
            document.getElementById('ppa-value').innerText = totalCredits > 0 ? (totalWeighted / totalCredits).toFixed(2) : "0.00";
        }

        function openModal(sigla) {
            activeSigla = sigla;
            const list = document.getElementById('intentos-list');
            list.innerHTML = '';
            (attempts[sigla] || []).forEach((n, i) => {
                list.innerHTML += `<div class="flex justify-between p-1 bg-gray-100 mb-1 rounded">Nota: ${n} <button onclick="deleteIntento(${i})" class="text-red-500 font-bold">X</button></div>`;
            });
            document.getElementById('nota-modal').classList.remove('hidden');
        }

        function closeModal() { document.getElementById('nota-modal').classList.add('hidden'); }
        function addIntento() {
            const nota = parseFloat(document.getElementById('new-nota').value);
            if (!isNaN(nota)) {
                if (!attempts[activeSigla]) attempts[activeSigla] = [];
                attempts[activeSigla].push(nota);
                updateAll();
                openModal(activeSigla);
            }
        }
        function deleteIntento(idx) {
            attempts[activeSigla].splice(idx, 1);
            updateAll();
            openModal(activeSigla);
        }

        function saveProgress() {
            const saveObj = { state, attempts, texts: {} };
            document.querySelectorAll('input').forEach(el => saveObj.texts[el.dataset.id] = el.value);
            localStorage.setItem('irn_save', JSON.stringify(saveObj));
            alert("Guardado");
        }

        function loadProgress() {
            const data = JSON.parse(localStorage.getItem('irn_save'));
            Object.assign(state, data.state);
            Object.assign(attempts, data.attempts);
            Object.entries(data.texts).forEach(([id, val]) => { const el = document.querySelector(`[data-id="${id}"]`); if(el) el.value = val; });
            updateAll();
        }

        const planner = document.getElementById('planner');
        Object.keys(data).forEach((sem, idx) => {
            const col = document.createElement('div');
            col.className = "flex flex-col gap-2";
            col.innerHTML = `<div class="bg-[#d81b60] text-white text-center py-2 px-1 font-bold rounded-lg text-xs">Sem. ${sem}</div>`;
            data[sem].forEach(m => {
                const [nombre, sigla, creds, prereqs] = m;
                const editable = nombre.includes("MINOR") || nombre.includes("Form.") || nombre.includes("Filosofía");
                const box = document.createElement('div');
                box.className = "subject-box";
                box.dataset.sigla = sigla;
                box.dataset.sem = idx + 1;
                box.dataset.prereqs = JSON.stringify(prereqs);
                box.onclick = (e) => {
                    if (e.target.tagName !== 'INPUT' && e.target.tagName !== 'BUTTON') {
                        if (box.classList.contains('bloqueado')) return;
                        if (state[sigla] === 'aprobado') state[sigla] = 'reprobado';
                        else if (state[sigla] === 'reprobado') state[sigla] = 'pendiente';
                        else state[sigla] = 'aprobado';
                        updateAll();
                    }
                };
                box.innerHTML = `<input type="text" value="${nombre}" class="cell-input" data-id="n-${sigla}" ${editable?"":"readonly"}>
                                 <div class="text-xs">
                                    <input type="text" value="${sigla}" class="cell-code" data-id="c-${sigla}" ${editable?"":"readonly"}>
                                    <div class="text-black font-bold mt-1">cr: ${creds}</div>
                                 </div>
                                 <button class="nota-btn" onclick="openModal('${sigla}')">Nota</button>`;
                col.appendChild(box);
            });
            planner.appendChild(col);
            if (idx === 7) {
                const div = document.createElement('div');
                div.className = "divider-col flex items-center justify-center";
                div.innerText = "LICENCIATURA EN RRNN";
                planner.appendChild(div);
            }
        });
        updateAll();
    </script>
</body>
</html>
