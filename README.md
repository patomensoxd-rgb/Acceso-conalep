<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Credencialización - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; background: #000; color: white; margin: 0; text-align: center; }
        .banner { background: #fff; padding: 10px; display: flex; justify-content: space-around; align-items: center; border-bottom: 3px solid #b8860b; }
        .banner img { height: 35px; }
        
        /* Contenedor Escáner */
        #scanner-section { padding: 20px; }
        #reader { border-radius: 15px; overflow: hidden; border: 2px solid #333; max-width: 450px; margin: auto; }
        #status-msg { margin-top: 15px; padding: 10px; border-radius: 8px; font-weight: bold; display: none; }
        .error { background: #b71c1c; color: white; display: block !important; }

        /* Tarjeta de Datos (Estilo Imagen 35138.jpg) */
        #info-card { display: none; padding: 20px; background: #000; position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: 100; overflow-y: auto; }
        .pink-title { color: #f48fb1; font-size: 1.5em; font-weight: bold; margin: 20px 0; }
        .conalep-logo { width: 200px; margin: 10px 0; }
        .label { font-weight: bold; color: #fff; font-size: 1.1em; margin-top: 15px; display: block; }
        .value { font-size: 1.1em; color: #fff; margin-bottom: 5px; display: block; text-transform: uppercase; }
        .status-dot { color: #4caf50; display: flex; align-items: center; justify-content: center; gap: 10px; margin-top: 15px; }
        .dot { height: 12px; width: 12px; background-color: #00c853; border-radius: 50%; box-shadow: 0 0 10px #00c853; }
        .btn-reset { margin-top: 30px; padding: 12px 25px; background: #333; color: white; border: 1px solid #555; border-radius: 8px; cursor: pointer; }
    </style>
</head>
<body>

    <div class="banner">
        <img src="https://upload.wikimedia.org/wikipedia/commons/b/b2/Logo_del_Gobierno_del_Estado_de_M%C3%A9xico.png" alt="EdoMex">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Logotipo_del_Estado_de_M%C3%A9xico.svg/1200px-Logotipo_del_Estado_de_M%C3%A9xico.svg.png" alt="Poder de Servir">
    </div>

    <div id="scanner-section">
        <h3 style="color: #f48fb1;">Escanear QR Estudiantil</h3>
        <div id="reader"></div>
        <div id="status-msg"></div>
    </div>

    <div id="info-card">
        <div class="pink-title">Sistema de Credencialización</div>
        <img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Conalep_Logo.png" class="conalep-logo">
        
        <span class="label">Nombre:</span>
        <span id="res-nombre" class="value">---</span>

        <span class="label">Matrícula:</span>
        <span id="res-matricula" class="value">---</span>

        <span class="label">Curp:</span>
        <span id="res-curp" class="value">---</span>

        <span class="label">Plantel:</span>
        <span class="value">Ing. Bernardo Quintana Arrioja</span>

        <span class="label">Carrera:</span>
        <span id="res-carrera" class="value">---</span>

        <span class="label">Estatus Credencial:</span>
        <div class="status-dot">
            <span class="dot"></span> 
            <span>Vigente</span>
        </div>

        <button class="btn-reset" onclick="reiniciar()">Escanear otro</button>
        <p style="margin-top: 40px; font-size: 0.8em; opacity: 0.5;">Conalep Estado de México @ 2026</p>
    </div>

    <script>
        // --- BASE DE DATOS LOCAL ---
        // Aquí agregas las matrículas de tus compañeros
        const alumnos = {
            "251090464-8": {
                nombre: "ORLANDO MUÑIZ PICHARDO",
                curp: "MUPO080119HMCXCRA6",
                carrera: "PT-B EN INFORMÁTICA"
            },
            "251090000-1": {
                nombre: "JUAN PEREZ LOPEZ",
                curp: "PELJ050505HMCXYZ01",
                carrera: "PT-B EN MANTENIMIENTO"
            }
        };

        let isScanning = true;

        function alEscanear(texto) {
            if (!isScanning) return;
            const matricula = texto.trim();

            // Filtro de seguridad (11 caracteres y que incluya 109)
            if (matricula.length === 11 && matricula.includes("109")) {
                const datos = alumnos[matricula];
                
                if (datos) {
                    mostrarDatos(matricula, datos);
                } else {
                    mostrarError("MATRÍCULA NO REGISTRADA EN SISTEMA");
                }
            } else {
                mostrarError("CÓDIGO NO VÁLIDO / PLANTEL EXTERNO");
            }
        }

        function mostrarDatos(matricula, datos) {
            isScanning = false;
            document.getElementById('res-nombre').innerText = datos.nombre;
            document.getElementById('res-matricula').innerText = matricula;
            document.getElementById('res-curp').innerText = datos.curp;
            document.getElementById('res-carrera').innerText = datos.carrera;

            document.getElementById('info-card').style.display = 'block';
            document.getElementById('scanner-section').style.display = 'none';
        }

        function mostrarError(msg) {
            const status = document.getElementById('status-msg');
            status.innerText = "❌ " + msg;
            status.className = "error";
            setTimeout(() => { status.className = ""; status.innerText = ""; }, 3000);
        }

        function reiniciar() {
            document.getElementById('info-card').style.display = 'none';
            document.getElementById('scanner-section').style.display = 'block';
            isScanning = true;
        }

        const scanner = new Html5QrcodeScanner("reader", { fps: 20, qrbox: 250 });
        scanner.render(alEscanear);
    </script>
</body>
</html>
