<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Oficial - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        body { font-family: sans-serif; text-align: center; background: #121212; color: white; margin: 0; padding: 20px; }
        #reader { width: 100%; max-width: 500px; margin: auto; border: 5px solid #004a99; border-radius: 15px; overflow: hidden; background: #000; }
        #status { margin-top: 20px; padding: 20px; font-weight: bold; border-radius: 10px; font-size: 1.3em; transition: 0.3s; }
        .success { background: #1b5e20; color: #ccff90; border: 2px solid #b2ff59; }
        .error { background: #b71c1c; color: #ffcdd2; border: 2px solid #ff5252; }
        .waiting { background: #333; color: #bbb; border: 2px solid #555; }
        h2 { color: #00a8e8; }
    </style>
</head>
<body>

    <h2>Control de Acceso Estudiantil</h2>
    <p>Plantel Ingeniero Bernardo Quintana Arrioja</p>

    <div id="reader"></div>
    <div id="status" class="waiting">Esperando escaneo ...</div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // Limpiamos espacios para evitar fallos de lectura
            const codigoLimpio = decodedText.trim();
            
            // --- EL ALGORITMO QUE FUNCIONÓ (11 CARACTERES) ---
            // 1. DEBE tener exactamente 11 caracteres (ej: 251090464-8)
            // 2. DEBE incluir el número de plantel 109
            const esLongitudExacta = (codigoLimpio.length === 11);
            const esDelPlantel = codigoLimpio.includes("109");

            if (esLongitudExacta && esDelPlantel) {
                accesoPermitido("BIENVENIDO");
            } else {
                accesoDenegado("CÓDIGO NO AUTORIZADO / PLANTEL EXTERNO");
            }
        }

        function accesoPermitido(data) {
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO <br><small>Matrícula: " + data + "</small>";
            
            // Vibración de éxito (si el cel lo permite)
            if (navigator.vibrate) navigator.vibrate(100);

            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO <br><small>" + motivo + "</small>";
            
            // Vibración de error
            if (navigator.vibrate) navigator.vibrate([100, 50, 100]);

            setTimeout(() => reiniciarEscaneo(), 3000);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando escaneo oficial...";
            bloqueado = false;
        }

        // Configuración de cámara optimizada para QR
        const html5QrcodeScanner = new Html5QrcodeScanner(
