# Creación de una Extensión de Navegador para Detectar Sitios de Phishing utilizando un Modelo de Aprendizaje Automático

En este proyecto final, exploraremos cómo crear una extensión de navegador que utilice un modelo de aprendizaje automático para detectar sitios web de phishing. A través de este trabajo, aprenderás a combinar tus habilidades en tecnologías de la información y aprendizaje automático para construir una herramienta de seguridad en línea.

## Paso 1: Convertir el Modelo de Python a JavaScript

Para que tu modelo de aprendizaje automático entrenado en Python pueda ser utilizado en JavaScript dentro de la extensión de navegador, necesitas convertirlo a un formato compatible con JavaScript. En este ejemplo, vamos a asumir que estás utilizando una librería de Python llamada ONNX (Open Neural Network Exchange) para realizar esta conversión. ONNX es una herramienta que te permite exportar modelos entrenados en diferentes frameworks de aprendizaje automático, como scikit-learn, a un formato que puede ser utilizado en diferentes plataformas, incluyendo JavaScript.

# Pasos Detallados:

Abre tu entorno de desarrollo de Python y asegúrate de tener la librería ONNX instalada. Si no la tienes instalada, puedes hacerlo con el siguiente comando en tu terminal:

``` bash
pip install onnx
```

Carga tu modelo de aprendizaje automático entrenado utilizando las librerías y métodos correspondientes. Luego, exporta el modelo a un formato ONNX y guárdalo en un archivo:

``` python
import onnx
from onnx import numpy_helper

# Supongamos que 'modelo' es tu modelo entrenado en scikit-learn
# Exporta el modelo a formato ONNX
onnx_model = onnx.convert_sklearn(modelo, ['entrada'], 'salida')

# Guarda el modelo ONNX en un archivo
with open('model.onnx', 'wb') as f:
    f.write(onnx_model.SerializeToString())
```
Instala la herramienta onnxjs utilizando el siguiente comando:
``` bash
npm install onnxjs
```
Utiliza la herramienta onnxjs para convertir el modelo ONNX a JavaScript:

``` bash
onnxjs convert model.onnx model.js
```
## Paso 2: Crear la Infraestructura de la Extensión de Navegador

2.1. Archivo manifest.json

Crea un archivo llamado manifest.json en el directorio raíz de tu extensión. Define las propiedades básicas de la extensión:

``` json
{
  "manifest_version": 3,
  "name": "Detector de Phishing",
  "version": "1.0",
  "permissions": ["activeTab"],
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["<all_urls>"],
      "js": ["content.js"]
    }
  ]
}
``````
2.2. Archivo background.js

Crea un archivo llamado background.js en el mismo directorio. Este archivo contendrá el código para el script de fondo:

``` javascript
// background.js

// Función para manejar el clic en el ícono de la extensión
chrome.action.onClicked.addListener(tab => {
  chrome.scripting.executeScript({
    target: {tabId: tab.id},
    function: detectarPhishing
  });
});

// Función para detectar phishing
function detectarPhishing() {
  // Obtenemos el contenido de la página web actual
  chrome.scripting.executeScript({
    target: {tabId: tab.id},
    function: obtenerContenidoWeb
  });
}

// Función para obtener el contenido de la página web
function obtenerContenidoWeb() {
  const contenido = document.documentElement.outerHTML;
  // Llamamos a la función de detección de phishing en el modelo JavaScript
  const esPhishing = model.detectarPhishing(contenido);
  // Enviamos el resultado de la detección al script de contenido
  chrome.scripting.executeScript({
    target: {tabId: tab.id},
    function: mostrarResultado,
    args: [esPhishing]
  });
}
```
2.3. Archivo content.js

Crea un archivo llamado content.js en el mismo directorio. Este archivo contendrá el código para el script de contenido:

``` javascript
// content.js

// Escuchamos los mensajes del script de fondo
chrome.runtime.onMessage.addListener((mensaje, sender, enviarRespuesta) => {
  if (mensaje.accion === "mostrarResultado") {
    if (mensaje.esPhishing) {
      alert("¡Advertencia! Este sitio web podría ser de phishing.");
    } else {
      alert("Sitio web legítimo.");
    }
  }
});

// Notificamos al script de fondo para iniciar la detección de phishing
chrome.runtime.sendMessage({accion: "detectarPhishing"});
```
## Paso 3: Integrar el Modelo de JavaScript en la Extensión de Navegador

En el archivo background.js, importa el modelo JavaScript que convertiste en el Paso 1:

``` javascript
// background.js

// Importamos el modelo de JavaScript
import * as model from './model.js';

// ... (código restante)
```
## Paso 4: Pruebas y Depuración

Abre Google Chrome y accede a chrome://extensions/.
Habilita el "Modo de desarrollador" en la parte superior derecha.
Haz clic en "Cargar desempaquetada" y selecciona la carpeta que contiene tus archivos de extensión.
Aparecerá el ícono de tu extensión en la barra de herramientas del navegador.
Abre diferentes sitios web para probar la extensión y verificar si muestra alertas adecuadas según la detección de phishing.

## Paso 5: Mejoras y Personalización

Personaliza el mensaje de alerta o considera agregar una interfaz de usuario más elaborada para mostrar los resultados de detección de phishing.
Explora opciones para mejorar la precisión de la detección, como ajustar el umbral de confianza del modelo.

## Conclusión

Prueba estos pasos para el desarrollo de su proyecto