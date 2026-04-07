<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Universal - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #00a8e8; border-radius: 15px; overflow: hidden; background: #000; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 10px; font-size: 1.3em; transition: 0.3s; }
        .success { background: #1b5e20; color: #ccff90; border: 2px solid #b2ff59; }
        .waiting { background: #333; color: #bbb; border: 2px solid #555; }
    </style>
</head>
<body>

    <h2 style="color: #00a8e8;">Control de Acceso Peatonal</h2>
    <p>Escaneo de Credenciales (Modo Libre)</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando cualquier código...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // --- ALGORITMO LIBRE ---
            // No importa qué diga el código, ni cuántos caracteres tenga.
            // Si el QR se puede leer, ¡PASA!
            accesoPermitido(decodedText);
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br><small>Código: " + data + "</small>";
            
            // Espera 2 segundos y se reinicia para el siguiente alumno
            setTimeout(() => reiniciarEscaneo(), 2000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando cualquier código...";
            bloqueado = false;
        }

        const html5QrcodeScanner = new Html5QrcodeScanner(
            "reader", { 
                fps: 25, // Más rápido para que no haya filas
                qrbox: { width: 250, height: 250 },
                aspectRatio: 1.0 
            }
        );

        html5QrcodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
