document.getElementById('trackingForm').addEventListener('submit', function(event) {
    event.preventDefault();

    const trackingId = document.getElementById('trackingId').value.toUpperCase().trim();
    const resultDiv = document.getElementById('result');

    // Mapeo de ubicaciones a banderas
    const flags = {
        "Ecuador": "🇪🇨",
        "Estados Unidos": "🇺🇸",
        "En tránsito": "✈️"
    };

    // Mapeo de estados a clases CSS
    const statusClasses = {
        "RECOLECTADO": "status-recolectado",
        "EN CONSOLIDADO": "status-en-consolidado",
        "INGRESA A AEROLÍNEA": "status-ingresa-a-aerolinea",
        "EN ESPERA DE TURNO PARA REVISION POR LA ADUANA NACIONAL": "status-en-espera-de-turno",
        "PROCESO DE AFORO COMPLETADO": "status-proceso-de-aforo",
        "SALIDA DE ADUANA": "status-salida-de-aduana",
        "EN CLASIFICACIÓN": "status-en-clasificacion",
        "LISTA PARA ENVÍO A PROVINCIA": "status-lista-para-envio",
        "ENTREGADO": "status-entregado"
    };

    fetch('data.json')
        .then(response => response.json())
        .then(data => {
            if (data[trackingId]) {
                const packageInfo = data[trackingId];
                const statusClass = statusClasses[packageInfo.estado] || '';
                const flag = flags[packageInfo.ubicacion] || '';
                
                resultDiv.innerHTML = `
                    <div class="package-info">
                        <p><strong>Estado:</strong> <span class="${statusClass}">${packageInfo.estado}</span></p>
                        <p><strong>Ubicación:</strong> <span class="location-${packageInfo.ubicacion.toLowerCase().replace(/\s/g, '-')}">${flag} ${packageInfo.ubicacion}</span></p>
                        <p><strong>Última actualización:</strong> ${packageInfo.fecha}</p>
                    </div>
                `;
            } else {
                resultDiv.innerHTML = `<p class="error">Número de guía no encontrado. Por favor, verifica el número.</p>`;
            }
        })
        .catch(error => {
            console.error('Error al cargar los datos:', error);
            resultDiv.innerHTML = `<p class="error">Lo sentimos, ha ocurrido un error. Inténtalo de nuevo más tarde.</p>`;
        });
});