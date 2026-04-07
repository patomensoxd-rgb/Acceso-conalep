<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Conalep 109 - Oficial</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #004a99; border-radius: 15px; overflow: hidden; background: #000; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 10px; font-size: 1.3em; transition: 0.3s; }
        .success { background: #1b5e20; color: #ccff90; border: 2px solid #b2ff59; }
        .error { background: #b71c1c; color: #ffcdd2; border: 2px solid #ff5252; }
        .waiting { background: #333; color: #bbb; border: 2px solid #555; }
    </style>
</head>
<body>

    <h2 style="color: #00a8e8;">Control de Acceso Peatonal</h2>
    <p>Plantel Conalep 109</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando credencial...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // Limpiamos espacios para evitar errores de lectura
            const codigoLimpio = decodedText.trim();
            
            // --- EL ALGORITMO QUE FUNCIONÓ ---
            // 1. Debe tener entre 10 y 13 caracteres (Cubre con guion y sin guion)
            // 2. Debe contener el número de tu plantel (109)
            const longitudCorrecta = (codigoLimpio.length >= 10 && codigoLimpio.length <= 13);
            const esDelPlantel = codigoLimpio.includes("109");

            if (longitudCorrecta && esDelPlantel) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO AUTORIZADO / PLANTEL EXTERNO");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br><small>Matrícula: " + data + "</small>";
            // Espera 3 segundos antes de permitir otro escaneo
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

        // Configuración de la cámara
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
