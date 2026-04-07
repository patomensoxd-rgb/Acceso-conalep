<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 450px; margin: auto; border: 4px solid #004a99; border-radius: 15px; background: #000; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 10px; font-size: 1.3em; }
        .success { background: #1b5e20; color: #ccff90; border: 2px solid #b2ff59; }
        .error { background: #b71c1c; color: #ffcdd2; border: 2px solid #ff5252; }
        .waiting { background: #333; color: #bbb; border: 2px solid #555; }
        h2 { color: #00a8e8; }
    </style>
</head>
<body>

    <h2>Control de Acceso</h2>
    <p>Plantel 109</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando credencial...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // LIMPIEZA TOTAL: Quitamos espacios, guiones y saltos de línea 
            // que suelen aparecer al subir IMÁGENES
            const codigoLimpio = decodedText.replace(/[^a-zA-Z0-9]/g, "").trim();
            
            console.log("Original:", decodedText);
            console.log("Limpio:", codigoLimpio);

            // --- ALGORITMO PERMISIVO ---
            // Acepta si contiene el número del plantel (109)
            // O si tiene la longitud típica de tus matrículas (entre 8 y 12 números)
            const esDeLaEscuela = codigoLimpio.includes("109");
            const tieneLongitudMatricula = codigoLimpio.length >= 8 && codigoLimpio.length <= 15;

            if (esDeLaEscuela || tieneLongitudMatricula) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO RECONOCIDO");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br><small>" + data + "</small>";
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO <br><small>" + motivo + "</small>";
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando credencial...";
            bloqueado = false;
        }

        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { 
                fps: 20, 
                qrbox: { width: 250, height: 250 },
                // Esto ayuda a que el botón de subir imagen funcione mejor
                showTorchButtonIfSupported: true 
            }
        );

        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
