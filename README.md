<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Conalep 109 - Dual Scan</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #f4f4f4; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #004a99; border-radius: 10px; overflow: hidden; background: white; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 5px; font-size: 1.2em; }
        .success { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .error { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
        .waiting { background: #e2e3e5; color: #383d41; }
    </style>
</head>
<body>

    <h2>Control de Acceso Peatonal</h2>
    <p>Escaneo de QR y Código de Barras</p>

    <div id="reader"></div>
    <div id="status" class="waiting">coloque credencial...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            const codigoLimpio = decodedText.trim();

            // --- ALGORITMO ORIGINAL ---
            // Verifica 11 caracteres y que pertenezca al plantel 109
            const esLongitudExacta = (codigoLimpio.length === 11);
            const esDelPlantel = codigoLimpio.includes("109");

            if (esLongitudExacta && esDelPlantel) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO AUTORIZADO");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br> Matrícula: " + data;
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO <br>" + motivo;
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando escaneo de credencial...";
            bloqueado = false;
        }

        // --- CONFIGURACIÓN PARA CÓDIGO DE BARRAS ---
        const config = { 
            fps: 20, 
            qrbox: { width: 300, height: 150 }, // Rectángulo más ancho para el código de barras
            formatsToSupport: [ 
                Html5QrcodeSupportedFormats.QR_CODE, 
                Html5QrcodeSupportedFormats.CODE_128, // Formato estándar de credenciales
                Html5QrcodeSupportedFormats.EAN_13 
            ]
        };

        const html5QrcodeScanner = new Html5QrcodeScanner("reader", config);
        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
