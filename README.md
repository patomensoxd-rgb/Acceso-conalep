<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Control de Acceso - Conalep 109</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    
    <style>
        body { font-family: sans-serif; text-align: center; background: #f4f4f4; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #004a99; border-radius: 10px; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 5px; }
        .success { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .error { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
    </style>
</head>
<body>

    <h2>Control de Acceso Peatonal</h2>
    <p>Plantel Conalep 109</p>

    <div id="reader"></div>
    <div id="status">Esperando escaneo...</div>

    <script>
        const statusDiv = document.getElementById('status');

        // Esta función procesa el dato del QR
        function alDetectarCodigo(decodedText) {
            console.log("Código detectado: " + decodedText);

            // Lógica de Validación (Algoritmo de acceso)
            // Aquí puedes filtrar por prefijo de matrícula o consultar una base de datos
            if (decodedText.length >= 10) {
                accesoPermitido(decodedText);
            } else {
                accesoDenegado("Código no reconocido");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br> Matrícula: " + data;
            // Aquí se enviaría el log al servidor (opcional)
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO <br>" + motivo;
            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "";
            statusDiv.innerHTML = "Esperando escaneo...";
        }

        // Configuración de la cámara
        const html5QrCodeScanner = new Html5QrcodeScanner(
            "reader", { fps: 15, qrbox: 250 }
        );

        html5QrCodeScanner.render(alDetectarCodigo);
    </script>
</body>
</html>
