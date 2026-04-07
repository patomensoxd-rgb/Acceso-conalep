<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Control de Acceso - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: 'Segoe UI', sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 450px; margin: auto; border: 4px solid #004a99; border-radius: 15px; overflow: hidden; background: #000; }
        #status { margin-top: 25px; padding: 25px; font-weight: bold; border-radius: 10px; font-size: 1.4em; transition: 0.3s; }
        .success { background: #1b5e20; color: #ccff90; border: 2px solid #b2ff59; }
        .error { background: #b71c1c; color: #ffcdd2; border: 2px solid #ff5252; }
        .waiting { background: #333; color: #bbb; border: 2px solid #555; }
        h2 { color: #00a8e8; margin-bottom: 5px; }
        p { color: #888; margin-top: 0; }
    </style>
</head>
<body>

    <h2>Control de Acceso Peatonal</h2>
    <p>Plantel Conalep 109 - Oficial</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando credencial...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // Limpiamos el texto de espacios o saltos de línea
            const codigoLimpio = decodedText.trim();
            console.log("Escaneado:", codigoLimpio);

            // --- ALGORITMO FLEXIBLE CORREGIDO ---
            // 1. Verifica si tiene longitud de matrícula (9 a 16 caracteres)
            // 2. O si contiene el código del plantel "109"
            const esLongitudCorrecta = codigoLimpio.length >= 9 && codigoLimpio.length <= 16;
            const esDelPlantel = codigoLimpio.includes("109");

            if (esLongitudCorrecta || esDelPlantel) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO RECONOCIDO");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br><span style='font-size:0.7em;'>ID: " + data + "</span>";
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO <br><span style='font-size:0.6em;'>" + motivo + "</span>";
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando credencial...";
            bloqueado = false;
        }

        // Configuración óptima para escaneo móvil
        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { 
                fps: 20, 
                qrbox: { width: 250, height: 250 },
                aspectRatio: 1.0 
            }
        );

        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
