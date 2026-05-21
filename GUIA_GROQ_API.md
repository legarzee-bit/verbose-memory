# 🤖 Guía: Integración Groq API (Completamente Gratis)

## ¿Qué es Groq?

**Groq** es una plataforma de IA que ofrece **API gratuita** para generar texto con modelos de lenguaje de última generación.

**Ventajas:**
- ✅ 100% GRATIS
- ✅ Muy rápido (LLM inference)
- ✅ Sin tarjeta de crédito (para el tier free)
- ✅ Rate limits generosos (~500-1000 requests/día)
- ✅ Soporta múltiples modelos

---

## 🚀 Cómo Obtener tu API Key (5 minutos)

### Paso 1: Crear Cuenta en Groq
1. Abre: https://console.groq.com/login
2. Regístrate con email o GitHub
3. Verifica tu email

### Paso 2: Obtener tu API Key
1. Ve a: https://console.groq.com/keys
2. Click en **"Create API Key"**
3. Dale un nombre (ej: "NOIR Scripts")
4. **Copia la key** (aparece solo una vez)
5. ¡Listo! Ya la tienes

---

## 📝 Cómo Usar en NOIR Scripts

### Opción 1: Usar el Generador Web (Recomendado)

```html
Abre: /generators/scriptwriter-groq.html

1. Pega tu API key
2. Selecciona género y duración
3. Click "Generar Guión"
4. ¡Descarga o copia tu guión!
```

**Ventaja:** No necesitas instalar nada. Funciona desde el navegador.

---

## 💻 Opción 2: Integrar en tu Propio Proyecto

### JavaScript/Node.js

```javascript
const generateScript = async (genre, duration) => {
    const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
        method: 'POST',
        headers: {
            'Authorization': `Bearer YOUR_API_KEY`,
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({
            model: 'mixtral-8x7b-32768', // O cualquier otro modelo
            messages: [{
                role: 'user',
                content: `Genera un guión de ${duration} minutos en género ${genre}...`
            }],
            max_tokens: 2000,
            temperature: 0.7
        })
    });

    const data = await response.json();
    return data.choices[0].message.content;
};
```

### Python

```python
import requests

def generate_script(genre, duration, api_key):
    url = 'https://api.groq.com/openai/v1/chat/completions'
    
    headers = {
        'Authorization': f'Bearer {api_key}',
        'Content-Type': 'application/json'
    }
    
    payload = {
        'model': 'mixtral-8x7b-32768',
        'messages': [{
            'role': 'user',
            'content': f'Genera un guión de {duration} minutos en género {genre}...'
        }],
        'max_tokens': 2000,
        'temperature': 0.7
    }
    
    response = requests.post(url, headers=headers, json=payload)
    return response.json()['choices'][0]['message']['content']
```

---

## 🎯 Modelos Disponibles en Groq

```
mixtral-8x7b-32768        ⚡ Rápido, versátil
llama-3-70b-8192          ⚡ Potente
llama-3-8b-8192           ⚡ Ligero, rápido
gemma-7b-it               ⚡ Compact
```

Para generación de guiones, recomendamos: **mixtral-8x7b-32768** (equilibrio perfecto)

---

## 📊 Rate Limits & Costos

### Plan Gratuito (Free Tier)
- **Requests/día**: ~500-1000 (generoso)
- **Tokens/minuto**: ~6000
- **Costo**: $0
- **Perfecto para**: Testing, pequeños proyectos, uso personal

### Plan Pro ($5/mes)
- **Requests/día**: Ilimitados
- **Tokens/minuto**: ~20000
- **Costo**: $5/mes
- **Perfecto para**: Producción, apps en uso

### Plan Enterprise (Custom)
- **Requests/día**: Ilimitados
- **Tokens/minuto**: Ilimitado
- **Costo**: Contactar
- **Perfecto para**: SaaS, startups

---

## 🔒 Seguridad: Cómo Proteger tu API Key

### ❌ NUNCA HAGAS ESTO:
```javascript
// ❌ MALO - Expone tu key en el cliente
const apiKey = "gsk_xxxxx...";
```

### ✅ HAZ ESTO INSTEAD:

**Opción 1: Backend Proxy (Recomendado)**
```javascript
// Frontend
fetch('tudominio.com/api/generate-script', {
    method: 'POST',
    body: JSON.stringify({ genre, duration })
});

// Backend (Node.js/Express)
app.post('/api/generate-script', async (req, res) => {
    const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
        headers: {
            'Authorization': `Bearer ${process.env.GROQ_API_KEY}` // ← Guardada en servidor
        }
    });
    res.json(response);
});
```

**Opción 2: Variables de Entorno**
```bash
# .env
GROQ_API_KEY=gsk_xxxxx...
```

```javascript
// JavaScript
const apiKey = process.env.GROQ_API_KEY;
```

---

## 🛠️ Troubleshooting

### Error: "401 Unauthorized"
**Solución:** Verifica que tu API key es correcta
```javascript
// Check en console
console.log(apiKey); // Debe empezar con "gsk_"
```

### Error: "429 Too Many Requests"
**Solución:** Has excedido el rate limit. Espera o upgrade a Plan Pro
```javascript
// Implementar retry con backoff
async function generateWithRetry(genre, duration, retries = 3) {
    try {
        return await generateScript(genre, duration);
    } catch (error) {
        if (error.status === 429 && retries > 0) {
            await new Promise(r => setTimeout(r, 5000)); // Espera 5s
            return generateWithRetry(genre, duration, retries - 1);
        }
        throw error;
    }
}
```

### Error: "Invalid model"
**Solución:** Usa uno de los modelos disponibles listados arriba

---

## 💡 Tips para Maximizar Groq Gratis

### 1. **Usar Temperatura Baja** (Para Scripts Profesionales)
```javascript
temperature: 0.3  // Más consistente
```

### 2. **Limitar Tokens**
```javascript
max_tokens: 2000  // Suficiente para un guión, ahorra requests
```

### 3. **Cachear Resultados**
```javascript
// Almacenar guiones generados en localStorage
const cacheKey = `${genre}_${duration}`;
if (localStorage[cacheKey]) {
    return JSON.parse(localStorage[cacheKey]);
}
```

### 4. **Batch Processing**
```javascript
// Si necesitas múltiples guiones, hazlo en paralelo
const scripts = await Promise.all([
    generateScript('cyberpunk', 5),
    generateScript('space_opera', 5),
    generateScript('time_travel', 5)
]);
```

---

## 📈 Próximos Pasos: Monetización

Si quieres monetizar NOIR Scripts mientras usas Groq gratis:

### Plan 1: Tier Freemium
```
GRATIS:
- Generación con Groq
- 1 guión/día
- 3 géneros

PAGO ($9.99/mes):
- Ilimitado
- Todos los géneros
- Descarga PDF
```

### Plan 2: Affiliate Groq
```
Promueve Groq Pro → Gana comisión
Link: https://groq.com/pricing
```

### Plan 3: Backend Custom
```
Tu servidor + Groq API
- Cobras por API calls
- Tú pagas Groq Pro
- Margen: 70% profit
```

---

## 📚 Recursos Útiles

- **Docs Groq**: https://console.groq.com/docs
- **API Reference**: https://api.groq.com/openai/v1/
- **Community Discord**: https://discord.gg/groq
- **Status**: https://status.groq.com

---

## 🎯 Checklist para Lanzar

- [ ] Crear cuenta en Groq
- [ ] Obtener API key
- [ ] Probar en `/generators/scriptwriter-groq.html`
- [ ] Probar generación de 3 guiones
- [ ] Compartir con amigos
- [ ] Publicar en redes
- [ ] Recopilar feedback
- [ ] Mejorar prompts basado en feedback

---

**¡Ya estás listo para generar guiones con IA de forma GRATIS! 🚀**
