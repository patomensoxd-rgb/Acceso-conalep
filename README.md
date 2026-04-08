<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Acceso Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; background: #000; color: white; margin: 0; text-align: center; }
        
        /* Banner superior */
        .banner { background: #fff; padding: 10px; display: flex; justify-content: space-around; align-items: center; border-bottom: 3px solid #b8860b; }
        .banner img { height: 35px; }

        /* Contenedor principal */
        .main-container { padding: 20px; max-width: 500px; margin: auto; }
        #reader { border-radius: 15px; overflow: hidden; border: 2px solid #333; margin-bottom: 20px; }
        
        #status-msg { padding: 15px; border-radius: 10px; font-weight: bold; margin-bottom: 15px; display: none; }
        .error-msg { background: #b71c1c; color: #ffcdd2; border: 1px solid #ff5252; display: block !important; }

        /* Tarjeta de Información */
        #info-card { 
            display: none; 
            padding: 20px; 
            background: #000; 
            position: fixed; 
            top: 0; left: 0; width: 100%; height: 100%; 
            z-index: 100;
            overflow-y: auto;
        }

        .title { color: #f48fb1; font-size: 1.4em; font-weight: bold; margin: 20px 0; }
        .conalep-logo { width: 180px; margin: 10px 0; }
        .label { font-weight: bold; color: #bbb; font-size: 0.9em; margin-top: 15px; display: block; }
        .value { font-size: 1.1em; color: #fff; margin-bottom: 5px; display: block; font-weight: bold; }
        
        .status-dot { color: #4caf50; display: flex; align-items: center; justify-content: center; gap: 10px; margin-top: 15px; font-weight: bold; }
        .dot { height: 12px; width: 12px; background-color: #00c853; border-radius: 50%; box-shadow: 0 0 10px #00c853; }

        .btn-reset { margin-top: 30px; padding: 12px 25px; background: #006a4e; color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; }
    </style>
</head>
<body>

    <div class="banner">
        <img src="https://upload.wikimedia.org/wikipedia/commons/b/b2/Logo_del_Gobierno_del_Estado_de_M%C3%A9xico.png" alt="EdoMex">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/c3/Logotipo_del_Estado_de_M%C3%A9xico.svg/1200px-Logotipo_del_Estado_de_M%C3%A9xico.svg.png" alt="Poder de Servir">
    </div>

    <div class="main-container" id="scanner-section">
        <h3 style="color: #00a8e8;">Escanear Credencial Conalep</h3>
        <div id="reader"></div>
        <div id="status-msg"></div>
        <p style="opacity: 0.6; font-size: 0.9em;">Apunta al código QR o Código de Barras</p>
    </div>

    <div id="info-card">
        <div class="title">Sistema de Credencialización</div>
        <img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Conalep_Logo.png" class="conalep-logo" alt="Conalep">
        
        <span class="label">Nombre:</span>
        <span class="value">ORLANDO MUÑIZ PICHARDO</span>

        <span class="label">Matrícula:</span>
        <span id="display-matricula" class="value">251090464-8</span>

        <span class="label">Curp:</span>
        <span class="value">MUPO080119HMCXCRA6</span>

        <span class="label">Plantel:</span>
        <span class="value">Ing. Bernardo Quintana Arrioja</span>

        <span class="label">Carrera:</span>
        <span class="value">PT-B en Informática</span>

        <span class="label">Estatus Credencial:</span>
        <div class="status-dot">
            <span class="dot"></span> 
            <span>Vigente</span>
        </div>

        <button class="btn-reset" onclick="resetApp()">Finalizar Acceso</button>
        
        <p style="margin-top: 40px; font-size: 0.7em; opacity: 0.5;">Conalep Estado de México @ 2026</p>
    </div>

    <script>
        let isProcessing = false;
        const statusMsg = document.getElementById('status-msg');

        function onScanSuccess(decodedText) {
            if (isProcessing) return;
            
            const cleanText = decodedText.trim();
            
            // VALIDACIÓN: Que contenga "109" (Plantel) y longitud razonable
            if (cleanText.includes("109") && cleanText.length >= 9) {
                isProcessing = true;
                showAccess(cleanText);
            } else {
                showError();
            }
        }

        function showAccess(matricula) {
            document.getElementById('display-matricula').innerText = matricula;
            // Sonido de éxito
            new Audio('https://actions.google.com/sounds/v1/alarms/beep_short.ogg').play().catch(()=>{});
            
            // Abrir tarjeta digital
            document.getElementById('info-card').style.display = 'block';
            document.getElementById('scanner-section').style.display = 'none';
        }

        function showError() {
            if (isProcessing) return;
            isProcessing = true;
            
            statusMsg.innerText = "❌ ACCESO DENEGADO - PLANTEL NO VÁLIDO";
            statusMsg.className = "error-msg";
            
            new Audio('https://actions.google.com/sounds/v1/alarms/beep_error_short.ogg').play().catch(()=>{});

            setTimeout(() => {
                statusMsg.className = "";
                statusMsg.innerText = "";
                isProcessing = false;
            }, 3000);
        }

        function resetApp() {
            document.getElementById('info-card').style.display = 'none';
            document.getElementById('scanner-section').style.display = 'block';
            isProcessing = false;
        }

        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { fps: 20, qrbox: 250 }
        );
        html5QrcodeScanner.render(onScanSuccess);
    </script>
</body>
</html>
