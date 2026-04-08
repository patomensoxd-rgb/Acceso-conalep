<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Acceso Inteligente - Conalep 109</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <style>
        :root { 
            --conalep-green: #006a4e; 
            --conalep-dark: #0f2027; 
            --accent-glow: #00e676;
            --error-glow: #ff1744;
        }
        body { font-family: 'Segoe UI', Arial, sans-serif; background: var(--conalep-dark); color: white; margin: 0; padding: 0; display: flex; flex-direction: column; align-items: center; min-height: 100vh; }
        
        .header { background: rgba(255,255,255,0.95); width: 100%; padding: 20px 0; border-bottom: 5px solid var(--conalep-green); text-align: center; box-shadow: 0 4px 15px rgba(0,0,0,0.5); }
        .header h2 { color: var(--conalep-green); margin: 0; font-size: 1.8em; text-transform: uppercase; }
        .header p { color: #555; margin: 5px 0 0; }

        .scan-container { width: 90%; max-width: 450px; margin: 30px auto; padding: 20px; background: rgba(255,255,255,0.05); border-radius: 25px; border: 1px solid rgba(255,255,255,0.1); backdrop-filter: blur(5px); }
        
        #reader { width: 100%; border-radius: 20px; overflow: hidden; border: 4px solid #fff; box-shadow: 0 15px 35px rgba(0,0,0,0.7); }
        
        #status { margin-top: 30px; padding: 25px; border-radius: 15px; font-size: 1.5em; font-weight: bold; text-transform: uppercase; text-align: center; transition: all 0.4s ease; border: 2px solid transparent; }
        
        .waiting { background: #34495e; border-color: #7f8c8d; color: #ecf0f1; }
        .success { background: #1b5e20; border-color: var(--accent-glow); color: var(--accent-glow); box-shadow: 0 0 25px rgba(0, 230, 118, 0.4); }
        .error { background: #b71c1c; border-color: var(--error-glow); color: var(--error-glow); box-shadow: 0 0 25px rgba(255, 23, 68, 0.4); }

        .footer { margin-top: auto; padding: 20px; font-size: 0.85em; opacity: 0.7; color: white; text-align: center; }
    </style>
</head>
<body>

    <div class="header">
        <h2>CONALEP 109</h2>
        <p>Control de Acceso de Estudiantes</p>
    </div>

    <div class="scan-container">
        <div id="reader"></div>
        <div id="status" class="waiting">Esperando Credencial...</div>
    </div>

    <div class="footer">
        Desarrollado por Muñiz Pichardo Orlando<br>
        Grupo 210 - Plantel Bernardo Quintana Arrioja
    </div>

    <script>
        const statusDiv = document.getElementById('status');
        let bloqueado = false;

        function alDetectarCodigo(decodedText) {
            if (bloqueado) return;
            bloqueado = true;

            // Limpiamos espacios y caracteres extraños
            const codigoLimpio = decodedText.trim();
            
            // --- ALGORITMO INTELIGENTE CONALEP ---
            // 1. Debe contener el número de plantel (109)
            // 2. Longitud entre 10 y 13 (para cubrir variaciones de lectura del guion)
            const esDelPlantel = codigoLimpio.includes("109");
            const longitudCoherente = (codigoLimpio.length >= 10 && codigoLimpio.length <= 13);

            if (esDelPlantel && longitudCoherente) {
                accesoPermitido(codigoLimpio);
            } else {
                accesoDenegado("CÓDIGO NO VÁLIDO / PLANTEL EXTERNO");
            }
        }

        function accesoPermitido(data) {
            // OMISIÓN DE DATOS PERSONALES:
            // Solo mostramos los primeros 5 dígitos (que incluyen el año y el plantel)
            const dataSegura = data.substring(0, 5) + "****" + data.substring(data.length - 2);
            
            statusDiv.className = "success";
            statusDiv.innerHTML = "✅ ACCESO PERMITIDO<br><span style='font-size:0.65em; opacity:0.8;'>Ingreso Registrado: " + dataSegura + "</span>";
            
            // Sonido de éxito (opcional, requiere permisos del navegador)
            new Audio('https://actions.google.com/sounds/v1/alarms/beep_short.ogg').play().catch(()=>{});

            setTimeout(() => reiniciarEscaneo(), 3500); // 5.0 segundos para la animación
        }

        function accesoDenegado(motivo) {
            statusDiv.className = "error";
            statusDiv.innerHTML = "❌ ACCESO DENEGADO<br><span style='font-size:0.65em; opacity:0.8;'>" + motivo + "</span>";
            
            // Sonido de error (opcional)
            new Audio('https://actions.google.com/sounds/v1/alarms/beep_error_short.ogg').play().catch(()=>{});

            setTimeout(() => reiniciarEscaneo(), 3500);
        }

        function reiniciarEscaneo() {
            statusDiv.className = "waiting";
            statusDiv.innerHTML = "Esperando Credencial...";
            bloqueado = false;
        }

        // Configuración óptima del escáner
        const scanner = new Html5QrcodeScanner(
            "reader", { 
                fps: 20, // Suficientemente rápido para no haber filas
                qrbox: { width: 250, height: 250 },
                aspectRatio: 1.0,
                experimentalFeatures: {
                    useBarCodeDetectorIfSupported: true // Intenta usar la cámara para el código de barras también
                }
            }
        );

        scanner.render(alDetectarCodigo);
    </script>
</body>
</html>
