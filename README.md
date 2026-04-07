<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Control de Acceso - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #f4f4f4; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #004a99; border-radius: 10px; overflow: hidden; }
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
    <div id="status" class="waiting">Esperando escaneo...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            console.log("Código detectado: " + decodedText);

            // --- ALGORITMO DE SEGURIDAD (CORREGIDO) ---
            // Solo permite si empieza con 25109 (Tu plantel)
            if (decodedText.startsWith("25109")) {
                accesoPermitido(decodedText);
            } else {
                // Si es un QR externo, de sabritas o de otra escuela, lo rechaza
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
            statusDiv.innerHTML = "Esperando escaneo...";
            bloqueado = false;
        }

        // Configuración de la cámara
        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { fps: 15, qrbox: 250 }
        );

        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
