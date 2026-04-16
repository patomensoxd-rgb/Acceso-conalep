<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Lobos - Conalep 109</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    
    <style>
        :root {
            --conalep-green: #007934;
            --conalep-dark: #005a26;
            --white: #ffffff;
            --bg-gray: #f0f2f5;
            --danger: #dc3545;
            --success: #28a745;
        }

        body { 
            font-family: 'Segoe UI', Arial, sans-serif; 
            background-color: var(--conalep-green);
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .main-card {
            width: 90%;
            max-width: 450px;
            background: var(--white);
            border-radius: 30px;
            padding: 40px 20px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.4);
            text-align: center;
            position: relative;
        }

        /* --- Pantalla de Inicio --- */
        #welcome-screen {
            display: block;
        }

        .logo-container {
            width: 200px;
            height: 200px;
            margin: 0 auto 25px;
            border: 6px solid var(--conalep-green);
            border-radius: 50%;
            overflow: hidden;
            background: white;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .logo-container img {
            width: 85%;
            height: auto;
            object-fit: contain;
        }

        .title {
            color: var(--conalep-green);
            font-size: 2rem;
            margin: 0;
            font-weight: 800;
            text-transform: uppercase;
        }

        .subtitle {
            color: #555;
            font-size: 1.1rem;
            margin: 10px 0 30px;
        }

        .btn-ingresar {
            background: var(--conalep-green);
            color: white;
            border: none;
            padding: 18px 50px;
            font-size: 1.2rem;
            font-weight: bold;
            border-radius: 50px;
            cursor: pointer;
            transition: 0.3s all;
            box-shadow: 0 8px 15px rgba(0,121,52,0.3);
        }

        .btn-ingresar:hover {
            background: var(--conalep-dark);
            transform: translateY(-3px);
            box-shadow: 0 12px 20px rgba(0,121,52,0.4);
        }

        /* --- Pantalla de Escaneo --- */
        #scanner-screen {
            display: none;
        }

        .jaw-frame {
            position: relative;
            background: #111;
            border-radius: 20px;
            padding: 45px 10px;
            border: 6px solid var(--conalep-green);
            margin: 20px 0;
            overflow: hidden;
        }

        /* Dientes de Lobo (Mandíbula) */
        .wolf-teeth {
            position: absolute;
            left: 0; width: 100%; height: 45px;
            background-image: linear-gradient(135deg, transparent 50%, var(--white) 50%), 
                              linear-gradient(-135deg, transparent 50%, var(--white) 50%);
            background-size: 40px 45px;
            z-index: 10;
        }
        .teeth-top { top: 0; }
        .teeth-bottom { bottom: 0; transform: scaleY(-1); }

        #reader { 
            width: 100%; 
            border: none !important; 
        }

        /* Barra de Estado */
        #status-bar {
            margin-top: 20px;
            padding: 20px;
            border-radius: 15px;
            font-weight: bold;
            font-size: 1.1rem;
            background: #f8f9fa;
            border: 2px solid #ddd;
            color: #333;
        }

        .access-granted { background: var(--success) !important; color: white !important; border-color: var(--success) !important; }
        .access-denied { background: var(--danger) !important; color: white !important; border-color: var(--danger) !important; }

    </style>
</head>
<body>

    <div class="main-card">
        
        <div id="welcome-screen">
            <div class="logo-container">
                <img src="0ea957ca6e616a0ff0c8f6730541aa70.png" alt="Logo Lobo Conalep">
            </div>
            <h1 class="title">Conalep 109</h1>
            <p class="subtitle">Control de Acceso Peatonal</p>
            <button class="btn-ingresar" onclick="iniciarEscaneo()">ESCANEAR QR</button>
        </div>

        <div id="scanner-screen">
            <h2 style="color: var(--conalep-green); margin: 0 0 15px;">IDENTIFÍCATE</h2>
            
            <div class="jaw-frame">
                <div class="wolf-teeth teeth-top"></div>
                <div id="reader"></div>
                <div class="wolf-teeth teeth-bottom"></div>
            </div>

            <div id="status-bar">Esperando código...</div>
        </div>

    </div>

    <script>
        const welcomeView = document.getElementById('welcome-screen');
        const scannerView = document.getElementById('scanner-screen');
        const statusMsg = document.getElementById('status-bar');
        let html5Scanner;

        function iniciarEscaneo() {
            // Cambio de pantalla
            welcomeView.style.display = 'none';
            scannerView.style.display = 'block';

            // Iniciar cámara
            html5Scanner = new Html5QrcodeScanner("reader", { 
                fps: 15, 
                qrbox: { width: 250, height: 250 },
                aspectRatio: 1.0
            });
            html5Scanner.render(onScanSuccess);
        }

        function onScanSuccess(decodedText) {
            // Algoritmo de validación simple
            // Aquí puedes agregar lógica: ej. if(decodedText.startsWith("222"))
            if (decodedText.length >= 8) {
                mostrarResultado("✅ ACCESO PERMITIDO<br>Matrícula: " + decodedText, "access-granted");
            } else {
                mostrarResultado("❌ CÓDIGO NO RECONOCIDO", "access-denied");
            }
        }

        function mostrarResultado(mensaje, cssClass) {
            statusMsg.innerHTML = mensaje;
            statusMsg.className = cssClass;
            
            // Detenemos el scanner momentáneamente
            html5Scanner.clear();

            // Reiniciar después de 3 segundos para el siguiente alumno
            setTimeout(() => {
                statusMsg.innerHTML = "Esperando código...";
                statusMsg.className = "";
                iniciarEscaneo();
            }, 3500);
        }
    </script>
</body>
</html>
