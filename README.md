<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Lobos - Conalep 109</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    
    <style>
        :root {
            /* NUEVA PALETA CONALEP VERDE */
            --conalep-green: #007934; /* Verde oficial Conalep */
            --conalep-green-dark: #005a26;
            --wolf-gray: #f4f4f4; /* Fondo gris muy claro para contraste */
            --text-main: #333;
            --white: #ffffff;
            --danger-red: #dc3545;
            --success-green: #28a745;
        }

        body { 
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif; 
            text-align: center; 
            background: var(--wolf-gray); 
            color: var(--text-main);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            width: 100%;
            max-width: 500px;
            background: var(--white);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
        }

        /* --- Estilos Interfaz Previa (Welcome) --- */
        #welcome-screen {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .logo-placeholder {
            width: 150px;
            height: 150px;
            background: #e0e0e0; /* Color temporal */
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            color: #666;
            border: 4px solid var(--conalep-green);
            /* Descomenta la línea de abajo y pon la URL real del logo */
            /* background-image: url('URL_DE_TU_LOGO_CONALEP.png'); background-size: cover; */
        }

        .welcome-title {
            font-size: 1.8rem;
            color: var(--conalep-green);
            margin: 0;
            font-weight: 800;
        }

        .welcome-subtitle {
            font-size: 1.1rem;
            color: #666;
            margin: 0 0 20px 0;
        }

        .btn-start {
            background-color: var(--conalep-green);
            color: white;
            border: none;
            padding: 15px 40px;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            box-shadow: 0 4px 15px rgba(0,121,52,0.3);
        }

        .btn-start:hover {
            background-color: var(--conalep-green-dark);
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(0,121,52,0.4);
        }

        /* --- Estilos Interfaz Escáner (Oculta por defecto) --- */
        #scanner-screen {
            display: none; /* Se activa con JS */
        }

        h2.scan-title { 
            margin: 0 0 5px 0; 
            color: var(--conalep-green); 
            text-transform: uppercase; 
            font-size: 1.2rem;
        }
        p.scan-subtitle { margin: 0 0 25px 0; opacity: 0.7; font-size: 0.9rem; }

        /* Contenedor de la Mandíbula */
        .wolf-jaw-container {
            position: relative;
            background: #333; /* Fondo oscuro para la cámara */
            border-radius: 15px;
            padding: 35px 5px;
            border: 6px solid var(--conalep-green);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
            overflow: hidden;
        }

        /* Dientes (CSS shapes) */
        .teeth-top, .teeth-bottom {
            position: absolute;
            left: 0; width: 100%;
            height: 35px;
            background-image: linear-gradient(135deg, transparent 50%, var(--white) 50%), 
                              linear-gradient(-135deg, transparent 50%, var(--white) 50%);
            background-size: 35px 35px;
            background-repeat: repeat-x;
            z-index: 10;
        }
        .teeth-top { top: 0; }
        .teeth-bottom { 
            bottom: 0; 
            transform: scaleY(-1); /* Invierte los dientes para abajo */
        }

        /* Estilo del Lector QR */
        #reader { 
            width: 100%; 
            background: #000;
            border: none !important; 
        }

        /* Ajustes botones y textos internos de la librería */
        #reader button {
            background-color: var(--conalep-green);
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 5px;
            cursor: pointer;
            margin: 10px 0;
        }
        #reader__status_span { color: white !important; }

        /* Estado de Acceso (Barra inferior) */
        #status { 
            margin-top: 20px; 
            padding: 15px; 
            font-size: 1.1rem;
            font-weight: bold; 
            border-radius: 10px;
            transition: all 0.3s ease;
            background: #eee;
            border: 2px solid #ddd;
        }

        .success { 
            background: var(--success-green) !important; 
            color: white; 
            border-color: var(--success-green) !important;
        }

        .error { 
            background: var(--danger-red) !important; 
            color: white; 
            border-color: var(--danger-red) !important;
        }
    </style>
</head>
<body>

    <div class="container">
        
        <div id="welcome-screen">
            <div class="logo-placeholder">
                LOGO<br>CONALEP
            </div>
            <h1 class="welcome-title">Sistema de Acceso</h1>
            <p class="welcome-subtitle">Plantel Conalep 109 <br>Bienvenido, Lobo.</p>
            <button class="btn-start" onclick="iniciarApp()">Iniciar Escaneo</button>
        </div>

        <div id="scanner-screen">
            <h2 class="scan-title">Escanea tu Código</h2>
            <p class="scan-subtitle">Coloca el QR frente a la cámara</p>

            <div class="wolf-jaw-container">
                <div class="teeth-top"></div>
                
                <div id="reader"></div>
                
                <div class="teeth-bottom"></div>
            </div>

            <div id="status">Esperando lectura...</div>
        </div>

    </div>

    <script>
        const welcomeScreen = document.getElementById('welcome-screen');
        const scannerScreen = document.getElementById('scanner-screen');
        const statusDiv = document.getElementById('status');
        let html5QrCodeScanner; // Variable global para el scanner

        // Función que hace el cambio de interfaz
        function iniciarApp() {
            // 1. Ocultar bienvenida, mostrar escáner
            welcomeScreen.style.display = 'none';
            scannerScreen.style.display = 'block';

            // 2. Inicializar la cámara (solo cuando se pulsa el botón)
            inicializarEscaner();
        }

        // Lógica del Escáner QR
        function alDetectarCodigo(decodedText) {
            console.log("Datos: " + decodedText);

            // Algoritmo de validación (ejemplo básico)
            if (decodedText.length >= 8) {
                accesoPermitido(decodedText);
            } else {
                accesoDenegado("Código no válido");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO CONFIRMADO<br>Matrícula: " + data;
            
            // Pausar escaneo para evitar lecturas múltiples instantáneas
            html5QrCodeScanner.clear(); 
            
            setTimeout(() => reiniciarEscaneo(), 4000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO<br>" + motivo;
            
            html5QrCodeScanner.clear();

            setTimeout(() => reiniciarEscaneo(), 4000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "";
            statusDiv.innerHTML = "Esperando lectura...";
            // Reiniciar la cámara
            inicializarEscaner();
        }

        function inicializarEscaner() {
            // Configuración óptima para móviles
            html5QrCodeScanner = new Html5QrcodeScanner(
                "reader", { 
                    fps: 15, 
                    qrbox: { width: 250, height: 250 },
                    aspectRatio: 1.0,
                    rememberLastUsedCamera: true,
                    supportedScanTypes: [Html5QrcodeScanType.SCAN_TYPE_CAMERA]
                }
            );
            html5QrCodeScanner.render(alDetectarCodigo);
        }
    </script>
</body>
</html>
