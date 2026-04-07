<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Conalep 109</title>
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
    <p>Plantel Conalep 109</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando escaneo de credencial...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // Limpiamos espacios que puedan venir de la imagen
            const codigoLimpio = decodedText.trim();

            // --- ALGORITMO DE PRECISIÓN (11 CARACTERES) ---
            // 1. Verifica que tenga exactamente 11 caracteres (ej: 251090635-3)
            // 2. Verifica que sea de tu plantel (contenga "109")
            const esLongitudExacta = codigoLimpio.length === 11;
            const esDelPlantel = codigoLimpio.includes("109");

            if (esLongitudExacta && esDelPlantel) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO AUTORIZADO (Revisar Plantel/Longitud)");
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

        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { fps: 15, qrbox: 250 }
        );

        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
